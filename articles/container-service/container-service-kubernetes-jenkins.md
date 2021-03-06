---
title: "CI/CD Jenkins com Kubernetes no Serviço de Contêiner do Azure | Microsoft Docs"
description: "Como automatizar um processo de CI/CD com Jenkins para implantar e atualizar um aplicativo em recipientes no Kubernetes no Serviço de Contêiner do Azure"
services: container-service
documentationcenter: 
author: chzbrgr71
manager: johny
editor: 
tags: acs, azure-container-service, jenkins
keywords: "Docker, Contêineres, Kubernetes, Azure, Jenkins"
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/23/2017
ms.author: briar
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 3d206ebb6deeaa40f8e792ec12304c99c0abe684
ms.lasthandoff: 03/29/2017


---

# <a name="jenkins-integration-with-azure-container-service-and-kubernetes"></a>Integração do Jenkins com o Serviço de Contêiner do Azure e o Kubernetes 
Neste tutorial, percorreremos o processo para configurar a integração contínua de um aplicativo de vários contêineres no Kubernetes do Serviço de Contêiner do Azure usando a plataforma Jenkins. O fluxo de trabalho atualiza a imagem de contêiner no Hub do Docker e atualiza os pods do Kubernetes usando uma distribuição de implantação. 

## <a name="high-level-process"></a>Processo de alto nível
As etapas básicas descritas neste artigo são: 
- Instalar um cluster Kubernetes no Serviço de Contêiner
- Configurar o Jenkins e configurar o acesso ao Serviço de Contêiner
- Criar um fluxo de trabalho do Jenkins
- Testar o processo de CI/CD de ponta a ponta

## <a name="install-a-kubernetes-cluster"></a>Instalar um cluster Kubernetes
    
Implante o cluster Kubernetes no Serviço de Contêiner do Azure usando as etapas a seguir. A documentação completa está [aqui](container-service-kubernetes-walkthrough.md).

### <a name="step-1-create-a-resource-group"></a>Etapa 1: criar um grupo de recursos
```azurecli
RESOURCE_GROUP=my-resource-group
LOCATION=westus

az group create --name=$RESOURCE_GROUP --location=$LOCATION
```

### <a name="step-2-deploy-the-cluster"></a>Etapa 2: implantar o cluster
> [!NOTE]
> As etapas a seguir exigem uma chave pública SSH local armazenada na pasta ~/.ssh.
>

```azurecli
RESOURCE_GROUP=my-resource-group
DNS_PREFIX=some-unique-value
CLUSTER_NAME=any-acs-cluster-name

az acs create \
--orchestrator-type=kubernetes \
--resource-group $RESOURCE_GROUP \ 
--name=$CLUSTER_NAME \
--dns-prefix=$DNS_PREFIX \ 
--ssh-key-value ~/.ssh/id_rsa.pub \
--admin-username=azureuser \
--master-count=1 \
--agent-count=5 \
--agent-vm-size=Standard_D1_v2
```

## <a name="set-up-jenkins-and-configure-access-to-container-service"></a>Configurar o Jenkins e configurar o acesso ao Serviço de Contêiner

### <a name="step-1-install-jenkins"></a>Etapa 1: instalar o Jenkins
1. Crie uma VM do Azure com o Ubuntu 16.04 LTS. 
2. Instale o Jenkins seguindo estas [instruções](https://wiki.jenkins-ci.org/display/JENKINS/Installing+Jenkins+on+Ubuntu).
3. Um tutorial mais detalhado está disponível em [howtoforge.com](https://www.howtoforge.com/tutorial/how-to-install-jenkins-with-apache-on-ubuntu-16-04).
4. Atualize o grupo de segurança de rede do Azure para permitir a porta 8080 e procure o IP público na porta 8080 para gerenciar Jenkins no seu navegador.
5. A senha de administrador inicial do Jenkins é armazenada em /var/lib/jenkins/secrets/initialAdminPassword.
6. Instale o Docker no computador do Jenkins seguindo estas [instruções](https://docs.docker.com/cs-engine/1.13/#install-on-ubuntu-1404-lts-or-1604-lts). Isso permite que os comandos do Docker sejam executados em trabalhos do Jenkins.
7. Configure permissões de Docker para permitir que o Jenkins acesse o ponto de extremidade.

    ```bash
    sudo chmod 777 /run/docker.sock
    ```
8. Instale a CLI `kubectl` no Jenkins. Mais detalhes podem ser encontrados em [Instalando e Configurando kubectl](https://kubernetes.io/docs/tasks/kubectl/install/).

    ```bash
    curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl

    chmod +x ./kubectl

    sudo mv ./kubectl /usr/local/bin/kubectl
    ```

### <a name="step-2-set-up-access-to-the-kubernetes-cluster"></a>Etapa 2: configurar o acesso ao cluster Kubernetes

> [!NOTE]
> Há várias abordagens para realizar as etapas a seguir. Use a abordagem que for mais fácil para você.
>

1. Copie o arquivo de configuração `kubectl` para o computador do Jenkins.

    ```bash
    export KUBE_MASTER=<your_cluster_master_fqdn>
        
    sudo scp -3 -i ~/.ssh/id_rsa azureuser@$KUBE_MASTER:.kube/config user@<your_jenkins_server>:~/.kube/config
        
    sudo ssh user@<your_jenkins_server> sudo chmod 777 /home/user/.kube/config

    sudo ssh -i ~/.ssh/id_rsa user@<your_jenkins_server> sudo chmod 777 /home/user/.kube/config
        
    sudo ssh -i ~/.ssh/id_rsa user@<your_jenkins_server> sudo cp /home/user/.kube/config /var/lib/jenkins/config
    ```
        
2. Do Jenkins, valide que o cluster Kubernetes está acessível.
    

## <a name="create-a-jenkins-workflow"></a>Criar um fluxo de trabalho do Jenkins

### <a name="prerequisites"></a>Pré-requisitos

- Conta do GitHub para o repositório de código.
- Conta de Hub do Docker para armazenar e atualizar imagens.
- Aplicativo em contêineres que pode ser recompilado e atualizado. Você pode usar este aplicativo de contêiner de exemplo escrito em Golang: https://github.com/chzbrgr71/go-web 

> [!NOTE]
> As etapas a seguir devem ser executadas em sua própria conta do GitHub. Fique à vontade para clonar o repositório acima, mas você deve usar sua própria conta para configurar os webhooks e o acesso ao Jenkins.
>

### <a name="step-1-deploy-initial-v1-of-application"></a>Etapa 1: implantar o v1 inicial do aplicativo
1. Compilar o aplicativo no computador do desenvolvedor usando os comandos a seguir. Substitua `myrepo` com o seu próprio.
    
    ```bash
    git clone https://github.com/chzbrgr71/go-web.git
    cd go-web
    docker build -t myrepo/go-web .
    ```

2. Envie a imagem por push ao Hub do Docker.

    ```bash
    docker login
    docker push myrepo/go-web
    ```

3. Implante no cluster do Kubernetes.
    
    > [!NOTE] 
    > Edite o arquivo `go-web.yaml` para atualizar o repositório e a imagem do contêiner.
    >
        
    ```bash
    kubectl create -f ./go-web.yaml --record
    ```
### <a name="step-2-configure-jenkins-system"></a>Etapa 2: configurar o sistema Jenkins
1. Clique em **Gerenciar Jenkins** > **Configurar Sistema**.
2. Em **GitHub**, selecione **Adicionar Servidor GitHub**.
3. Deixe **URL da API** como padrão.
4. Em **Credenciais**, adicione uma credencial do Jenkins usando **Texto secreto**. É recomendável usar os tokens de acesso pessoal do GitHub, que são definidos nas configurações de conta de usuário do GitHub. Mais detalhes sobre isso podem ser encontrados [aqui.](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/)
5. Clique em **Testar conexão** para assegurar que isso esteja configurado corretamente.
6. Em **Propriedades Globais**, adicione uma variável de ambiente `DOCKER_HUB` e forneça sua senha do Hub do Docker. (Isso é útil para esta demonstração, mas um cenário de produção exige uma abordagem mais segura.)
7. Salve.

![Acesso ao GitHub do Jenkins](media/container-service-kubernetes-jenkins/jenkins-github-access.png)

### <a name="step-3-create-the-jenkins-workflow"></a>Etapa 3: criar o fluxo de trabalho do Jenkins
1. Crie um item do Jenkins.
2. Forneça um nome (por exemplo, "go-web") e selecione **Projeto Estilo Livre**. 
3. Marque a opção **Projeto GitHub** e forneça a URL para seu repositório GitHub.
4. Em **Gerenciamento de Código-Fonte**, forneça a URL do repositório GitHub e as credenciais. 
5. Adicione uma **Etapa de Build** do tipo **Executar shell** e use o seguinte texto:

    ```bash
    WEB_IMAGE_NAME="myrepo/go-web:kube${BUILD_NUMBER}"
    docker build -t $WEB_IMAGE_NAME .
    docker login -u <your-dockerhub-username> -p ${DOCKER_HUB}
    docker push $WEB_IMAGE_NAME
    ```

6. Adicione outra **Etapa de Build** do tipo **Executar shell** e use o seguinte texto:

    ```bash
    WEB_IMAGE_NAME="myrepo/go-web:kube${BUILD_NUMBER}"
    kubectl set image deployment/go-web go-web=$WEB_IMAGE_NAME --kubeconfig /var/lib/jenkins/config
    ```

![Etapas de build do Jenkins](media/container-service-kubernetes-jenkins/jenkins-build-steps.png)
    
7. Salve o item do Jenkins e teste com **Compilar Agora**.

### <a name="step-4-connect-github-webhook"></a>Etapa 4: conectar o webhook do GitHub
1. No item do Jenkins criado, clique em **Configurar**.
2. Em **Gatilhos de Build**, selecione **Gatilho de webhook do GitHub para sondagem de GITScm** e **Salvar**. Isso configura automaticamente o webhook do GitHub.
3. No seu repositório GitHub para go-web, clique em **Configurações > Webhooks**.
4. Verifique se a URL do webhook Jenkins foi adicionada com êxito. A URL deve terminar em "github-webhook".

![Configuração de webhook Jenkins](media/container-service-kubernetes-jenkins/jenkins-webhook.png)

## <a name="test-the-cicd-process-end-to-end"></a>Testar o processo de CI/CD de ponta a ponta

1. Atualize o código para o repositório e envie por push/sincronize com o repositório do GitHub.
2. No console do Jenkins, verifique o **Histórico de Build** e valide que o trabalho foi executado. Exiba a saída do console para ver detalhes.
3. Do Kubernetes, exiba detalhes da implantação atualizada:

    ```bash
    kubectl rollout history deployment/go-web
    ```

## <a name="next-steps"></a>Próximas etapas

- Implante o Registro de Contêiner do Azure e armazene as imagens em um repositório seguro. Confira [Documentos do Registro de Contêiner do Azure](https://docs.microsoft.com/azure/container-registry).
- Crie um fluxo de trabalho mais complexo que inclua implantação lado a lado e testes automatizados no Jenkins.
- Para obter mais informações sobre CI/CD com Jenkins e Kubernetes, consulte o [blog do Jenkins](https://jenkins.io/blog/2015/07/24/integrating-kubernetes-and-jenkins/).

