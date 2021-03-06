---
title: "Usar privilégios de raiz nas máquinas virtuais do Linux | Microsoft Docs"
description: "Saiba como usar privilégios de raiz em uma máquina virtual Linux do Azure."
services: virtual-machines-linux
documentationcenter: 
author: szarkos
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: a2c106a2-dceb-43a3-9dd1-50ed77685952
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/02/2017
ms.author: szark
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: dc39db1f5fecffb60499a5420bfe72850e2fffd9
ms.lasthandoff: 04/03/2017


---
# <a name="using-root-privileges-on-linux-virtual-machines-in-azure"></a>Utilizando privilégios de raiz nas máquinas virtuais Linux do Azure
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Por padrão, o usuário `root` encontra-se desabilitado nas máquinas virtuais Linux no Azure. Os usuários podem executar comandos com privilégios elevados usando o comando `sudo` . No entanto, a experiência pode variar dependendo de como o sistema foi fornecido.

1. **Chave SSH e senha OU senha apenas**: a máquina virtual foi provisionada com um certificado (arquivo `.CER`) ou chave SSH, bem como uma senha, ou apenas com um nome de usuário e uma senha. Neste caso, `sudo` solicitará a senha do usuário antes de executar o comando.
2. **Somente chave SSH**: a máquina virtual foi configurada com um certificado (arquivo `.cer`, `.pem` ou `.pub`) ou chave SSH, mas nenhuma senha.  Neste caso, `sudo` **não solicitará** a senha do usuário antes de executar o comando.

## <a name="ssh-key-and-password-or-password-only"></a>SSH chave e a senha ou a senha apenas
Faça logon na máquina virtual Linux usando autenticação de senha ou chave SSH, em seguida, execute comandos usando `sudo`, por exemplo:

    # sudo <command>
    [sudo] password for azureuser:

Nesse caso, o usuário será solicitado para uma senha. Depois de inserir a senha,  `sudo` executará o comando com privilégios de `root`.

Você também pode habilitar sudo sem senha editando o arquivo `/etc/sudoers.d/waagent` , por exemplo:

    #/etc/sudoers.d/waagent
    azureuser ALL = (ALL) NOPASSWD: ALL

Essa alteração permitirá sudo sem senha pelo usuário "azureuser".

## <a name="ssh-key-only"></a>SSH chave somente
Faça logon na máquina virtual Linux usando autenticação de chave SSH, em seguida, execute comandos usando `sudo`, por exemplo:

    # sudo <command>

Nesse caso, o usuário **não** será solicitado para uma senha. Depois de pressionar `<enter>`, `sudo` executará o comando com privilégios `root`.


