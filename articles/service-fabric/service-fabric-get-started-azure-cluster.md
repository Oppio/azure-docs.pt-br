---
title: Configurar um cluster do Azure Service Fabric | Microsoft Docs
description: "Guia de início rápido - criar um cluster do Service Fabric do Windows ou Linux no Azure."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/05/2017
ms.author: ryanwi
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: ad473a2c9006e2593a84364f03e3954a569adcab
ms.contentlocale: pt-br
ms.lasthandoff: 05/09/2017


---

# <a name="create-your-first-service-fabric-cluster-on-azure"></a>Criar seu primeiro cluster do Service Fabric no Azure
Um [cluster do Service Fabric](service-fabric-deploy-anywhere.md) é um conjunto de máquinas físicas ou virtuais conectadas em rede, no qual os microsserviços são implantados e gerenciados. Este guia de início rápido ajuda a criar um cluster de cinco nós, em execução no Windows ou Linux, por meio do [Azure PowerShell](https://msdn.microsoft.com/library/dn135248) ou do [Portal do Azure](http://portal.azure.com) em poucos minutos.  

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.


## <a name="use-the-azure-portal"></a>Use o Portal do Azure

Faça logon no Portal do Azure em [http://portal.azure.com](http://portal.azure.com).

### <a name="create-the-cluster"></a>Criar o cluster

1. Clique no botão **Novo** no canto superior esquerdo do Portal do Azure.
2. Selecione **Computação** na folha **Novo**, em seguida, selecione **Cluster do Service Fabric** na folha **Computação**.
3. Preencha o formulário **Básico** do Service Fabric. Para o **Sistema operacional**, selecione a versão do Windows ou Linux com a qual você deseja que os nós do cluster sejam executados. O nome do usuário e a senha inseridos aqui são usados para fazer logon na máquina virtual. Para **Grupo de Recursos**, crie um novo. Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são criados e gerenciados coletivamente. Ao concluir, clique em **OK**.

    ![Saída da instalação do cluster][cluster-setup-basics]

4. Preencha o formulário **Configuração do cluster**.  Para a **contagem do tipo de nó**, insira "1" e defina o [Nível de durabilidade](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) para "Bronze".

5. Selecione **Configurar cada tipo de nó** e preencha o formulário **Configuração do tipo de nó**. Os tipos de nó definem o tamanho da VM, número de VMs, pontos de extremidade personalizados e outras configurações para as VMs desse tipo. Cada tipo de nó definido é configurado como um conjunto de dimensionamento de máquinas virtuais, que é usado para implantar e gerenciar as máquinas virtuais como um conjunto. Cada tipo de nó pode ser dimensionado para cima ou para baixo de forma independente, tem conjuntos diferentes de portas abertas e pode ter métricas de capacidade diferentes.  O primeiro tipo de nó, ou primário, é onde os serviços do sistema do Service Fabric são hospedados e devem ter cinco ou mais VMs.

    Para qualquer implantação de produção, o [planejamento da capacidade](service-fabric-cluster-capacity.md) é uma etapa importante.  Para esse início rápido, no entanto, você não está executando aplicativos, portanto, escolha um tamanho de VM *DS1_v2 Padrão*.  Selecione "Prata" para o [nível de confiabilidade](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) e uma capacidade 5 do conjunto de dimensionamento de máquinas virtuais.  

    Os pontos de extremidade personalizados abrem portas no Azure Load Balancer para que você possa conectar os aplicativos executados no cluster.  Insira "80, 8172" para abrir as portas 80 e 8172.

    Não marque a caixa **Definir configurações avançadas**, que é usada para personalizar os pontos de extremidade de gerenciamento do TCP/HTTP, intervalos de portas do aplicativo, [restrições de posicionamento](service-fabric-cluster-resource-manager-configure-services.md#placement-constraints), e [propriedades da capacidade](service-fabric-cluster-resource-manager-metrics.md).    

    Selecione **OK**.

6. No formulário **Configuração do cluster**, defina **Diagnóstico** para **Ativar**.  Para este início rápido, você não precisa inserir nenhuma propriedade de [configuração do fabric](service-fabric-cluster-fabric-settings.md).  Na **Versão do fabric**, selecione o modo de atualização **Automático** para que a Microsoft atualize automaticamente a versão do código do fabric em execução no cluster.  Defina o modo para **Manual** se quiser [escolher uma versão com suporte](service-fabric-cluster-upgrade.md) para a qual atualizar. 

    ![Configuração do tipo de nó][node-type-config]

    Selecione **OK**.

7. Preencha o formulário **Segurança**.  Para esse início rápido, selecione **Não seguro**.  Porém, é altamente recomendável criar um cluster seguro para as cargas de trabalho de produção, uma vez que qualquer pessoa pode conectar um cluster inseguro anonimamente e realizar operações de gerenciamento.  

    Os certificados são usados no Service Fabric para fornecer autenticação e criptografia para proteger vários aspectos de um cluster e de seus aplicativos. Para obter mais informações sobre como os certificados são usados no Service Fabric, consulte [Cenários de segurança do cluster do Service Fabric](service-fabric-cluster-security.md).  Para habilitar a autenticação do usuário usando o Azure Active Directory ou configurar os certificados para a segurança do aplicativo, [crie um cluster a partir de um modelo do Resource Manager](service-fabric-cluster-creation-via-arm.md).

    Selecione **OK**.

8. Examine o resumo.  Se você quiser baixar um modelo do Resource Manager criado a partir das configurações inseridas, selecione **Baixar modelo e parâmetros**.  Selecione **Criar** para criar o cluster.

    Você pode ver o progresso da criação nas notificações. (Clique no ícone de "Sino" próximo à barra de status no canto superior direito da tela). Se você clicou em **Fixar no Quadro Inicial** durante a criação do cluster, verá **Implantando o Cluster do Service Fabric** fixado na tela **Inicial**.

### <a name="view-cluster-status"></a>Exibir o status do cluster
Depois de criar o cluster, você pode inspecionar o cluster na folha **Visão geral** no portal. Agora você pode ver os detalhes do cluster no painel, inclusive o ponto de extremidade público do cluster e um link para o Service Fabric Explorer.

![Status do cluster][cluster-status]

### <a name="visualize-the-cluster-using-service-fabric-explorer"></a>Visualizar o cluster usando o Service Fabric Explorer
O [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) é uma boa ferramenta para visualizar o cluster e gerenciar os aplicativos.  O Service Fabric Explorer é um serviço executado no cluster.  Acesse-o usando um navegador da Web clicando no link **Service Fabric Explorer** da página **Visão geral** do cluster no portal.  Você também pode digitar o endereço diretamente no navegador: [http://quickstartcluster.westus.cloudapp.azure.com:19080/Explorer](http://quickstartcluster.westus.cloudapp.azure.com:19080/Explorer)

O painel do cluster fornece uma visão geral do cluster, incluindo um resumo do aplicativo e a integridade do nó. A exibição de nós mostra o layout físico do cluster. Para um nó específico, você pode inspecionar quais aplicativos têm código implantado naquele nó.

![Service Fabric Explorer][service-fabric-explorer]

### <a name="connect-to-the-cluster-using-powershell"></a>Conectar o cluster usando o PowerShell
Verifique se o cluster está em execução conectando e usando o PowerShell.  O módulo ServiceFabric PowerShell é instalado com o [SDK do Service Fabric](service-fabric-get-started.md).  O cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) estabelece uma conexão com o cluster.   

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint localhost:19000
```
Confira [Conectar-se a um cluster seguro](service-fabric-connect-to-secure-cluster.md) para obter outros exemplos de como se conectar a um cluster. Depois de se conectar ao cluster, use o script [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) para exibir uma lista de nós de cluster e informações de status para cada nó. **HealthState** deve ser *OK* para cada nó.

```powershell
PS C:\> Get-ServiceFabricNode |Format-Table

NodeDeactivationInfo NodeName     IpAddressOrFQDN NodeType  CodeVersion ConfigVersion NodeStatus NodeUpTime NodeDownTime HealthState
-------------------- --------     --------------- --------  ----------- ------------- ---------- ---------- ------------ -----------
                     _nodetype1_2 10.0.0.6        nodetype1 5.5.216.0   1                     Up 00:59:04   00:00:00              Ok
                     _nodetype1_1 10.0.0.5        nodetype1 5.5.216.0   1                     Up 00:59:04   00:00:00              Ok
                     _nodetype1_0 10.0.0.4        nodetype1 5.5.216.0   1                     Up 00:59:04   00:00:00              Ok
                     _nodetype1_4 10.0.0.8        nodetype1 5.5.216.0   1                     Up 00:59:04   00:00:00              Ok
                     _nodetype1_3 10.0.0.7        nodetype1 5.5.216.0   1                     Up 00:59:04   00:00:00              Ok
```

### <a name="remove-the-cluster"></a>Remover o cluster
Um cluster do Service Fabric é composto por outros recursos do Azure, além do próprio recurso do cluster. Portanto, para excluir por completo um cluster do Service Fabric, também é necessário excluir todos os recursos que o compõem. A maneira mais simples de excluir o cluster e todos os recursos que ele consume é excluir o grupo de recursos. Para ter outras maneiras de excluir um cluster ou excluir alguns (mas não todos) recursos em um grupo de recursos, consulte [Excluir um cluster](service-fabric-cluster-delete.md)

Exclua um grupo de recursos no portal do Azure:
1. Navegue até o cluster do Service Fabric que você deseja excluir.
2. Clique no nome do **Grupo de Recursos** na página de conceitos básicos do cluster.
3. Na página **Conceitos Básicos do Grupo de Recursos**, clique em **Excluir** e siga as instruções na página para concluir a exclusão do grupo de recursos.
    ![Excluir o grupo de recursos][cluster-delete]


## <a name="use-azure-powershell-to-deploy-a-secure-cluster"></a>Usar o Azure Powershell para implantar um cluster seguro


1) Baixe o [módulo Azure PowerShell, versão 4.0 ou mais recente](https://docs.microsoft.com/powershell/azure/install-azurerm-ps), em seu computador.

2) Abra uma janela do Windows PowerShell, execute os seguintes comandos. 
    
```powershell

Get-Command -Module AzureRM.ServiceFabric 
```

Você deverá ver um resultado semelhante ao seguinte.

![ps-list][ps-list]

3) Faça logon no Azure e selecione a assinatura para a qual você deseja criar o cluster

```powershell

Login-AzureRmAccount

Select-AzureRmSubscription -SubscriptionId "Subcription ID" 

```

4) Execute o seguinte comando para agora criar um cluster seguro. Não se esqueça de personalizar os parâmetros. 


````powershell

$certpwd="Password#1234" | ConvertTo-SecureString -AsPlainText -Force
$RDPpwd="Password#1234" | ConvertTo-SecureString -AsPlainText -Force 
$RDPuser="vmadmin"
$RGname="mycluster" # this is also the name of your cluster
$clusterloc="SouthCentralUS"
$subname="$RGname.$clusterloc.cloudapp.azure.com"
$certfolder="c:\mycertificates\"
$clustersize=1 # can take values 1, 3-99

New-AzureRmServiceFabricCluster -ResourceGroupName $RGname -Location $clusterloc -ClusterSize $clustersize -VmUserName $RDPuser -VmPassword $RDPpwd -CertificateSubjectName $subname -CertificatePassword $certpwd -CertificateOutputFolder $certfolder

````

O comando pode levar de 10 a 30 minutos para ser concluído, no final dela, você verá uma saída semelhante à seguinte. A saída tem informações sobre o certificado, o KeyVault onde ele foi carregado e a pasta local para a qual o certificado é copiado. 

![ps-out][ps-out]

5) Copie toda a saída e salve um arquivo de texto, pois precisamos fazer referência a ele. Anote as informações a seguir da saída.
 

- **CertificateSavedLocalPath** : c:\mycertificates\mycluster20170504141137.pfx
- **CertificateThumbprint** : C4C1E541AD512B8065280292A8BA6079C3F26F10
- **ManagementEndpoint** : https://mycluster.southcentralus.cloudapp.azure.com:19080
- **ClientConnectionEndpointPort** : 19000

### <a name="install-the-certificate-on-your-local-machine"></a>Instalar o certificado no seu computador local
  
Para se conectar ao cluster, você precisa instalar o certificado no repositório pessoal (Meu) do usuário atual. 

Execute o seguinte PowerShell

```powershell
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\mycertificates\the name of the cert.pfx `
        -Password (ConvertTo-SecureString -String certpwd -AsPlainText -Force)
```

Agora você está pronto para se conectar ao seu cluster seguro.

### <a name="connect-to-a-secure-cluster"></a>Conectar a um cluster seguro 

Execute o seguinte comando do PowerShell para se conectar a um cluster seguro. Os detalhes do certificado devem corresponder a um certificado que foi usado para configurar o cluster. 

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint <Certificate Thumbprint> `
          -FindType FindByThumbprint -FindValue <Certificate Thumbprint> `
          -StoreLocation CurrentUser -StoreName My
```


O exemplo a seguir mostra os parâmetros concluídos: 

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mycluster.southcentralus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -StoreLocation CurrentUser -StoreName My
```

Execute o seguinte comando para verificar se você está conectado e o cluster está íntegro.

```powershell

Get-ServiceFabricClusterHealth

```
### <a name="publish-your-apps-to-your-cluster-from-visual-studio"></a>Publicar seus aplicativos para o cluster a partir do Visual Studio

Agora que você configurou um cluster do Azure, você pode publicar seus aplicativos a partir do Visual Studio, seguindo o documento [Publicar para um cluster](service-fabric-publish-app-remote-cluster.md). 

### <a name="remove-the-cluster"></a>Remover o cluster
Um cluster é composto por outros recursos do Azure, além do próprio recurso do cluster. A maneira mais simples de excluir o cluster e todos os recursos que ele consume é excluir o grupo de recursos. 

```powershell

Remove-AzureRmResourceGroup -Name $RGname -Force

```

## <a name="next-steps"></a>Próximas etapas
Agora que você configurou um cluster de desenvolvimento, tente o seguinte:
* [Criar um cluster seguro no portal](service-fabric-cluster-creation-via-portal.md)
* [Criar um cluster a partir de um modelo](service-fabric-cluster-creation-via-arm.md) 
* [Implantar aplicativos usando o PowerShell](service-fabric-deploy-remove-applications.md)


[cluster-setup-basics]: ./media/service-fabric-get-started-azure-cluster/basics.png
[node-type-config]: ./media/service-fabric-get-started-azure-cluster/nodetypeconfig.png
[cluster-status]: ./media/service-fabric-get-started-azure-cluster/clusterstatus.png
[service-fabric-explorer]: ./media/service-fabric-get-started-azure-cluster/sfx.png
[cluster-delete]: ./media/service-fabric-get-started-azure-cluster/delete.png
[ps-list]: ./media/service-fabric-get-started-azure-cluster/pslist.PNG
[ps-out]: ./media/service-fabric-get-started-azure-cluster/psout.PNG

