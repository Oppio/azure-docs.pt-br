---
title: "Visualização de dados em tempo real de dados de sensor do Hub IoT do Azure – aplicativos Web | Microsoft Docs"
description: "Use aplicativos Web do Azure para visualizar dados de temperatura e umidade que são coletados do sensor e enviados para o Hub IoT do Azure."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "visualização de dados em tempo real, visualização de dados dinâmicos, visualização de dados de sensor"
ms.assetid: e42b07a8-ddd4-476e-9bfb-903d6b033e91
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/29/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: b0c27ca561567ff002bbb864846b7a3ea95d7fa3
ms.openlocfilehash: f3bb01da7764e467963a47d3d5485679411c9167
ms.lasthandoff: 04/25/2017


---
# <a name="visualize-real-time-sensor-data-from-azure-iot-hub-using-azure-web-apps"></a>Visualizar dados de sensor em tempo real do Hub IoT do Azure usando Aplicativos Web do Azure

![Diagrama de ponta a ponta](media/iot-hub-get-started-e2e-diagram/5.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>O que você aprenderá

Nesta lição, você aprenderá como visualizar dados de sensor em tempo real que o Hub IoT do Azure recebe por meio da execução de um aplicativo Web hospedado em um aplicativo Web do Azure. Se você quiser tentar visualizar os dados em seu Hub IoT com o Power BI, consulte [Usar o Power BI para visualizar dados de sensor em tempo real do Hub IoT do Azure](iot-hub-live-data-visualization-in-power-bi.md).

## <a name="what-you-do"></a>O que fazer

- Criar um aplicativo Web no Portal do Azure.
- Preparar seu Hub IoT para acesso a dados, adicionando um grupo de consumidores.
- Configurar o aplicativo Web para ler dados de sensor de seu Hub IoT.
- Carregar um aplicativo Web a ser hospedado pelo aplicativo Web.
- Abrir o aplicativo Web para ver os dados de temperatura e umidade em tempo real de seu Hub IoT.

## <a name="what-you-need"></a>O que você precisa

- Tutorial [Configurar seu dispositivo](iot-hub-raspberry-pi-kit-node-get-started.md) concluído que aborda os seguintes requisitos:
  - Uma assinatura ativa do Azure.
  - Um hub IoT do Azure em sua assinatura.
  - O aplicativo cliente que envia mensagens para o Hub IoT do Azure.
- Git. ([Baixar Git](https://www.git-scm.com/downloads)).

## <a name="create-an-azure-web-app"></a>Criar um aplicativo Web do Azure

1. No [Portal do Azure](https://ms.portal.azure.com/), clique em **Novo** > **Web + Móvel** > **Aplicativo Web**.
1. Insira um nome de trabalho exclusivo, verifique a assinatura, especifique um grupo de recursos e um local, selecione **Fixar no painel** e, em seguida, clique em **Criar**.

   Recomendamos que você selecione o mesmo local em que se encontra o grupo de recursos. Isso ajuda a aumentar a velocidade de processamento e reduzir os custos de transferência de dados.

   ![Criar um aplicativo Web do Azure](media/iot-hub-live-data-visualization-in-web-apps/2_create-web-app-azure.png)

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="configure-the-web-app-to-read-data-from-your-iot-hub"></a>Configurar o aplicativo Web para ler dados de sensor de seu Hub IoT

1. Abra o aplicativo Web que você acabou de provisionar.
1. Clique em **Configurações do aplicativo** e, em seguida, adicione os seguintes pares chave/valor em **Configurações do aplicativo**:

   | Chave                                   | Valor                                                        |
   |---------------------------------------|--------------------------------------------------------------|
   | Azure.IoT.IoTHub.ConnectionString     | Obtido de iothub-explorer                                |
   | Azure.IoT.IoTHub.DeviceId             | Obtido de iothub-explorer                                |
   | Azure.IoT.IoTHub.ConsumerGroup        | O nome do grupo de consumidores que você adiciona ao seu Hub IoT  |

   ![Adicionar configurações ao aplicativo Web do Azure com pares chave-valor](media/iot-hub-live-data-visualization-in-web-apps/4_web-app-settings-key-value-azure.png)

## <a name="upload-a-web-application-to-be-hosted-by-the-web-app"></a>Carregar um aplicativo Web a ser hospedado pelo aplicativo Web

No GitHub, foi disponibilizado um aplicativo Web que exibe dados de sensor em tempo real obtidos do seu Hub IoT. Tudo que você precisa fazer é configurar o aplicativo Web para trabalhar com um repositório Git, baixar o aplicativo Web do GitHub e carregá-lo no Azure para ser hospedado pelo aplicativo Web.

1. No aplicativo Web, clique em **Opções de Implantação** > **Escolher Origem** > **Repositório Git Local**.

   ![Configurar a implantação do aplicativo Web do Azure para usar o repositório Git local](media/iot-hub-live-data-visualization-in-web-apps/5_configure-web-app-deployment-local-git-repository-azure.png)

1. Clique em **Configurar conexão**, crie um nome de usuário e senha que serão usados para se conectar ao repositório Git no Azure e, em seguida, clique em **OK**.

   ![Definir o nome de usuário e senha para o repositório Git no Azure para seu aplicativo Web](media/iot-hub-live-data-visualization-in-web-apps/6_web-app-set-user-password-git-repo-azure.png)

1. Clique em **OK** para concluir a configuração.
1. Clique em **Visão geral** e anote o valor de **URL de clone do Git**.

   ![Obter a URL de clone do Git de seu aplicativo Web do Azure](media/iot-hub-live-data-visualization-in-web-apps/7_web-app-git-clone-url-azure.png)

1. Abra uma janela de terminal ou de comando no computador local.
1. Baixe o aplicativo Web do GitHub e carregue-o no Azure para ser hospedado pelo aplicativo Web. Para fazer isso, execute os seguintes comandos:

   ```bash
   git clone https://github.com/Azure-Samples/web-apps-node-iot-hub-data-visualization.git
   git remote add webapp <Git clone URL>
   git push webapp master:master
   ```

   > [!Note]
   > \<URL de clone do Git\> é a URL do repositório Git encontrado na página **Visão geral** do aplicativo Web.

## <a name="open-the-web-app-to-see-real-time-temperature-and-humidity-data-from-your-iot-hub"></a>Abrir o aplicativo Web para ver os dados de temperatura e umidade em tempo real de seu Hub IoT

Na página **Visão geral** do seu aplicativo Web, clique na URL para abri-lo.

![Obter a URL do seu aplicativo Web do Azure](media/iot-hub-live-data-visualization-in-web-apps/8_web-app-url-azure.png)

Você deve ver os dados de temperatura e umidade em tempo real de seu Hub IoT.

![Página de aplicativo Web do Azure mostrando a umidade e a temperatura em tempo real](media/iot-hub-live-data-visualization-in-web-apps/9_web-app-page-show-real-time-temperature-humidity-azure.png)

## <a name="next-steps"></a>Próximas etapas
Você usou com êxito um aplicativo Web para visualizar dados do sensor em tempo real do seu Hub IoT do Azure.

Há uma maneira alternativa para visualizar dados do Hub IoT do Azure. Confira [Usar o Power BI para visualizar dados de sensor em tempo real do Hub IoT do Azure](iot-hub-live-data-visualization-in-power-bi.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
