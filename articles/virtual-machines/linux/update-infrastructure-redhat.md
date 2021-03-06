---
title: "Infraestrutura de Atualização do Red Hat (RHUI) | Microsoft Docs"
description: "Saiba mais sobre a Infraestrutura de Atualização do Red Hat (RHUI) para as instâncias sob demanda do Red Hat Enterprise Linux no Microsoft Azure"
services: virtual-machines-linux
documentationcenter: 
author: BorisB2015
manager: timlt
editor: 
ms.assetid: f495f1b4-ae24-46b9-8d26-c617ce3daf3a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/13/2017
ms.author: borisb
ms.translationtype: Human Translation
ms.sourcegitcommit: e72275ffc91559a30720a2b125fbd3d7703484f0
ms.openlocfilehash: 07815d691ffe57f0349f7a90ced4a2fcc1ab834f
ms.contentlocale: pt-br
ms.lasthandoff: 05/05/2017


---
# <a name="red-hat-update-infrastructure-rhui-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>Infraestrutura de Atualização do Red Hat (RHUI) para as VMs do Red Hat Enterprise Linux sob demanda no Azure
As máquinas virtuais criadas a partir das imagens disponíveis do Red Hat Enterprise Linux (RHEL) sob demanda no Azure Marketplace são registradas para acessar a Infraestrutura de Atualização do Red Hat (RHUI) implantada no Azure.  As instâncias do RHEL sob demanda têm acesso a um repositório yum regional e são capazes de receber atualizações incrementais.

A lista de repositórios yum, que é gerenciada pelo RHUI, é configurada na sua instância RHEL durante o provisionamento. Você não precisa fazer nenhuma configuração adicional: execute `yum update` depois da instância do RHEL estar pronta para obter as atualizações mais recentes.

> [!NOTE]
> Em setembro de 2016, implantamos um RHUI do Azure atualizada e em janeiro de 2017 começamos o desligamento em fases dos RHUIs do Azure mais antigos. Se você tem usado imagens do RHEL (ou seus instantâneos) de setembro de 2016 ou posteriores, provavelmente nenhuma ação é necessária. Se, no entanto, você tiver instantâneos/VMs mais antigas, sua configuração precisará ser atualizada para acesso ininterrupto ao RHUI do Azure.
> 

## <a name="rhui-azure-infrastructure-update"></a>Atualização da infraestrutura RHUI do Azure
A partir de setembro de 2016, o Azure tem um novo conjunto de servidores do RHUI (Red Hat Update Infrastructure). Esses servidores são implantados com o [Gerenciador de Tráfego do Azure](https://azure.microsoft.com/services/traffic-manager/) para que um único ponto de extremidade (rhui-1.microsoft.com) possa ser usado por qualquer VM, independentemente da região. As novas imagens de RHEL PAYG (pré-pago) no Azure Marketplace (versões de setembro de 2016 ou posteriores) apontam para os novos servidores do RHUI do Azure e não exigem nenhuma ação adicional.

### <a name="determine-if-action-is-required"></a>Determinar se uma ação é necessária
Se você estiver tendo problemas para se conectar ao RHUI do Azure de sua VM PAYG do Azure RHEL, siga estas etapas
1. Inspecionar a configuração da VM para o ponto de extremidade do RHUI do Azure

    Verifique se o arquivo `/etc/yum.repos.d/rh-cloud.repo` contém a referência a `rhui-[1-3].microsoft.com` na baseurl da seção `[rhui-microsoft-azure-rhel*]` do arquivo. Se esse é o caso, significa que você está usando o novo RHUI do Azure.

    No entanto, será necessária uma atualização da configuração se ele estiver apontando para um local com o padrão a seguir: `mirrorlist.*cds[1-4].cloudapp.net`.

    Se você estiver usando a nova configuração e ainda não puder se conectar ao RHUI do Azure, registre um caso de suporte com a Microsoft ou a Red Hat.

    > [!NOTE]
    > O acesso ao RHUI hospedado pelo Azure é limitado às VMs nos [Intervalos de IP do Data Center do Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).
    > 

2. Se o antigo RHUI do Azure ainda estiver disponível quando você fizer essa verificação e você quiser de atualizar automaticamente a configuração, execute o seguinte comando:

    `sudo yum update RHEL6` ou `sudo yum update RHEL7` dependendo da versão de família RHEL.

3. Se você não puder se conectar ao antigo RHUI do Azure, siga as etapas manuais descritas na próxima seção.

4. Certifique-se de atualizar a configuração no instantâneo/imagem de origem da qual a VM afetada foi provisionada.

### <a name="phased-shutdown-of-the-old-azure-rhui"></a>Desligamento em fases do antigo RHUI do Azure
Durante o desligamento do antigo RHUI do Azure, restringimos o acesso a ele da seguinte maneira:

1. Restrição ainda maior do acesso (ACL) ao conjunto de endereços IP que já estão se conectando a ele. Possíveis efeitos colaterais: se você continuar a usar o antigo RHUI do Azure, suas novas VMs podem não ser capazes de se conectar a ela. VMs do RHEL com IPs dinâmicos que passam pela sequência de desligamento/desalocação/inicialização podem receber um novo IP e, portanto, também podem passar a falhar ao conectar-se ao antigo RHUI do Azure

2. Desligamento dos servidores de distribuição de conteúdo de espelho. Possíveis efeitos colaterais: conforme desligarmos mais CDSes, você poderá ver um tempo de manutenção de `yum update` mais longo e mais tempos limite atingidos até o ponto em que você não poderá mais conectar-se ao antigo RHUI do Azure.

### <a name="the-ips-for-the-new-rhui-content-delivery-servers-are"></a>Os IPs para os novos servidores de distribuição de conteúdo RHUI são
Se você estiver usando a configuração de rede para restringir o acesso de VMs PAYG do RHEL, verifique se os seguintes IPs são permitidos para `yum update` funcionar dependendo do ambiente em que você está. 

```
# Azure Global
13.91.47.76
40.85.190.91
52.187.75.218
52.174.163.213

# Azure US Government
13.72.186.193

# Azure Germany
51.5.243.77
51.4.228.145
```

### <a name="manual-update-procedure-to-use-the-new-azure-rhui-servers"></a>Procedimento de atualização manual para usar os novos servidores do RHUI do Azure
Baixar (via curl) a assinatura de chave pública

```bash
curl -o RPM-GPG-KEY-microsoft-azure-release https://download.microsoft.com/download/9/D/9/9d945f05-541d-494f-9977-289b3ce8e774/microsoft-sign-public.asc 
```

Verificar a chave baixada

```bash
gpg --list-packets --verbose < RPM-GPG-KEY-microsoft-azure-release
```

Verificar o arquivo de saída, verificar `keyid` e `user ID packet`:

```bash
Version: GnuPG v1.4.7 (GNU/Linux)
:public key packet:
        version 4, algo 1, created 1446074508, expires 0
        pkey[0]: [2048 bits]
        pkey[1]: [17 bits]
        keyid: EB3E94ADBE1229CF
:user ID packet: "Microsoft (Release signing) <gpgsecurity@microsoft.com>"
:signature packet: algo 1, keyid EB3E94ADBE1229CF
        version 4, created 1446074508, md5len 0, sigclass 0x13
        digest algo 2, begin of digest 1a 9b
        hashed subpkt 2 len 4 (sig created 2015-10-28)
        hashed subpkt 27 len 1 (key flags: 03)
        hashed subpkt 11 len 5 (pref-sym-algos: 9 8 7 3 2)
        hashed subpkt 21 len 3 (pref-hash-algos: 2 8 3)
        hashed subpkt 22 len 2 (pref-zip-algos: 2 1)
        hashed subpkt 30 len 1 (features: 01)
        hashed subpkt 23 len 1 (key server preferences: 80)
        subpkt 16 len 8 (issuer key ID EB3E94ADBE1229CF)
        data: [2047 bits]
```

Instalar a chave pública

```bash
sudo install -o root -g root -m 644 RPM-GPG-KEY-microsoft-azure-release /etc/pki/rpm-gpg
sudo rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-microsoft-azure-release
```

Baixar, verificar e instalar o cliente RPM

Download: para RHEL 6

```bash
curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel6/rhui-azure-rhel6-2.0-2.noarch.rpm 
```

Para RHEL 7

```bash
curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel7/rhui-azure-rhel7-2.0-2.noarch.rpm  
```

Verificar:

```bash
rpm -Kv azureclient.rpm
```

Verificar na saída se a assinatura do pacote está OK

```bash
azureclient.rpm:
    Header V3 RSA/SHA256 Signature, key ID be1229cf: OK
    Header SHA1 digest: OK (927a3b548146c95a3f6c1a5d5ae52258a8859ab3)
    V3 RSA/SHA256 Signature, key ID be1229cf: OK
    MD5 digest: OK (c04ff605f82f4be8c96020bf5c23b86c)
```

Instalar o RPM

```bash
sudo rpm -U azureclient.rpm
```

Após a conclusão, verifique se você pode acessar o RHUI do Azure por meio da VM

### <a name="all-in-one-script-for-automating-the-preceding-task"></a>Script tudo-em-um para automatizar a tarefa anterior
Use o script a seguir conforme necessário para automatizar a tarefa de atualização de VMs afetadas para os novos servidores RHUI do Azure.

```sh
# Download key
curl -o RPM-GPG-KEY-microsoft-azure-release https://download.microsoft.com/download/9/D/9/9d945f05-541d-494f-9977-289b3ce8e774/microsoft-sign-public.asc 

# Validate key
if ! gpg --list-packets --verbose < RPM-GPG-KEY-microsoft-azure-release | grep -q "keyid: EB3E94ADBE1229CF"; then
    echo "Keyfile azure.asc NOT valid. Exiting."
    exit 1
fi

# Install Key
sudo install -o root -g root -m 644 RPM-GPG-KEY-microsoft-azure-release /etc/pki/rpm-gpg
sudo rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-microsoft-azure-release

# Download RPM package
if grep -q "release 7" /etc/redhat-release; then
    ver=7
elif  grep -q "release 6" /etc/redhat-release; then
    ver=6
else
    echo "Version not supported, exiting"
    exit 1
fi

url=https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel$ver/rhui-azure-rhel$ver-2.0-2.noarch.rpm
curl -o azureclient.rpm "$url"

# Verify package
if ! rpm -Kv azureclient.rpm | grep -q "key ID be1229cf: OK"; then
    echo "RPM failed validation ($url)"
    exit 1
fi

# Install package
sudo rpm -U azureclient.rpm
```

## <a name="rhui-overview"></a>Visão geral de RHUI
[Red Hat Update Infrastructure](https://access.redhat.com/products/red-hat-update-infrastructure) oferece uma solução altamente escalonável para gerenciar o conteúdo do repositório yum para instâncias de nuvem do Red Hat Enterprise Linux hospedadas por provedores de nuvem certificados pela Red Hat. Com base no projeto Pulp upstream, o RHUI permite que provedores de nuvem espelhem o conteúdo do repositório hospedado pelo Red Hat localmente, criem repositórios personalizados com seu próprio conteúdo e disponibilizem esses repositórios para um grande grupo de usuários finais por meio de um sistema de entrega de conteúdo com balanceamento de carga.

## <a name="regions-where-rhui-is-available"></a>Regiões em que o RHUI está disponível
O RHUI está disponível em todas as regiões onde as imagens do RHEL sob demanda estão disponíveis. Atualmente, inclui todas as regiões públicas listadas na página [Painel de status do Azure](https://azure.microsoft.com/status/) e as regiões da Alemanha e do Governo dos EUA do Azure. O acesso do RHUI para as VMs provisionadas a partir das imagens do RHEL sob demanda está incluído em seu preço. A disponibilidade adicional de nuvem regional/nacional será atualizada quando expandirmos a disponibilidade do RHEL sob demanda no futuro.

> [!NOTE]
> O acesso ao RHUI hospedado pelo Azure é limitado às VMs nos [Intervalos de IP do Data Center do Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).
> 
> 

## <a name="get-updates-from-another-update-repository"></a>Obter atualizações de outro repositório de atualizações
Se você precisar obter atualizações de um repositório de atualização diferentes (em vez do RHUI hospedado no Azure), primeiro você precisará cancelar o registro de suas instâncias no RHUI. Em seguida, você precisa registrá-los novamente com a infraestrutura de atualização desejada (como CDN do Portal do Cliente da Red Hat ou Red Hat Satellite). Você precisará das devidas assinaturas do Red Hat para esses serviços e o registro para o [Acesso em Nuvem da Red Hat no Azure](https://access.redhat.com/ecosystem/partners/ccsp/microsoft-azure).

Para cancelar o registro do RHUI e registrar novamente na infraestrutura de atualização, siga as etapas a seguir:

1. Edite /etc/yum.repos.d/rh-cloud.repo e altere todos os `enabled=1` para `enabled=0`. Por exemplo:
   
   ```bash
   sed -i 's/enabled=1/enabled=0/g' /etc/yum.repos.d/rh-cloud.repo
   ```
   
2. Edite /etc/yum/pluginconf.d/rhnplugin.conf e altere `enabled=0` para `enabled=1`.
3. Então, registre-se na infraestrutura desejada, como o Portal do Cliente do Red Hat. Siga o guia de solução do Red Hat em [como registrar e assinar um sistema para o Portal do Cliente da Red Hat](https://access.redhat.com/solutions/253273).

> [!NOTE]
> O acesso ao RHUI hospedado pelo Azure é incluído no preço de imagem Pré-Pago (PAYG) do RHEL. Cancelar o registro de uma VM RHEL PAYG do RHUI hospedado no Azure não converte a máquina virtual em uma VM do tipo BYOL (traga sua própria licença). Se você registrar a mesma VM com outra fonte de atualizações, poderá incorrer em encargos duplos: a primeira vez para a taxa de software RHEL do Azure e a segunda vez para assinaturas do Red Hat. 
> 
> Se você precisar usar consistentemente uma infraestrutura de atualização diferente do RHUI hospedado pelo Azure, considere criar e implantar suas próprias imagens (do tipo BYOL), como descrito no artigo [Criar e carregar uma máquina virtual baseada no Red Hat para o Azure](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) .
> 

## <a name="next-steps"></a>Próximas etapas
Para criar uma VM do Red Hat Enterprise Linux por meio da imagem Pré-Paga do Azure Marketplace e aproveitar o RHUI hospedado pelo Azure, acesse o [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/redhat/). Você poderá usar `yum update` em sua instância do RHEL sem nenhuma configuração adicional.


