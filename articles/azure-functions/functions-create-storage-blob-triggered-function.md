---
title: "Criar uma função no Azure disparada pelo Armazenamento de Blobs | Microsoft Docs"
description: "Use o Azure Functions para criar uma função sem servidor que é invocada por itens adicionados ao Armazenamento de Blobs do Azure."
services: azure-functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: d6bff41c-a624-40c1-bbc7-80590df29ded
ms.service: functions
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/02/2017
ms.author: glenga
ms.translationtype: Human Translation
ms.sourcegitcommit: fc4172b27b93a49c613eb915252895e845b96892
ms.openlocfilehash: c0d1271bc083688bbc72bd2556546c2f738e7345
ms.contentlocale: pt-br
ms.lasthandoff: 05/12/2017


---
# <a name="create-a-function-triggered-by-azure-blob-storage"></a>Criar uma função disparada pelo Armazenamento de Blobs do Azure

Saiba como criar uma função disparada quando arquivos são carregados ou atualizados no Armazenamento de Blobs do Azure.

![Exiba a mensagem nos logs.](./media/functions-create-storage-blob-triggered-function/function-app-in-portal-editor.png)

## <a name="prerequisites"></a>Pré-requisitos

Antes de executar este exemplo, você deve executar o seguinte:

- Baixe e instale o [Gerenciador de Armazenamento do Microsoft Azure](http://storageexplorer.com/).

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)]

## <a name="create-an-azure-function-app"></a>Criar um Aplicativo de funções do Azure

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

![Aplicativo de funções criado com êxito.](./media/functions-create-first-azure-function/function-app-create-success.png)

Em seguida, crie uma nova função no novo aplicativo de funções.

<a name="create-function"></a>

## <a name="create-a-blob-storage-triggered-function"></a>Criar uma função disparada pelo Armazenamento de Blobs

Expanda seu aplicativo de funções, clique no botão **+** ao lado de **Funções**, clique no modelo **BlobTrigger** para a linguagem de programação desejada. Use então as configurações especificadas na tabela e, em seguida, clique em **Criar**.

![Crie a função disparada pelo Armazenamento de Blobs.](./media/functions-create-storage-blob-triggered-function/functions-create-blob-storage-trigger-portal.png)

| Configuração | Valor sugerido | Descrição |
|---|---|---|
| **Caminho**   | mycontainer/{name}    | Local no Armazenamento de Blobs que está sendo monitorada. O nome do arquivo do blob é passado na associação como o parâmetro _name_.  |
| **Conexão da conta de armazenamento** | AzureWebJobStorage | Você pode usar a conexão da conta de armazenamento que já está sendo usada por seu aplicativo de funções ou criar uma nova.  |
| **Nomeie sua função** | Exclusivo no aplicativo de funções | O nome dessa função disparada por filas. |

Em seguida, você pode se conectar à sua conta de armazenamento do Azure e criar o contêiner **mycontainer**.

## <a name="create-the-container"></a>Criar o contêiner

1. Em sua função, clique em **Integrar**, expanda **Documentação**e copie **Nome da conta** e **Chave de conta**. Você usa essas credenciais para conectar-se à conta de armazenamento. Se você já tiver se conectado à conta de armazenamento, vá para a etapa 4.

    ![Obtenha as credenciais de conexão da conta de armazenamento.](./media/functions-create-storage-blob-triggered-function/functions-storage-account-connection.png)

1. Execute a ferramenta [Gerenciador de Armazenamento do Microsoft Azure](http://storageexplorer.com/), clique no ícone conectar-se à esquerda, escolha **Usar um nome e chave de conta de armazenamento** e clique em **Avançar**.

    ![Execute a ferramenta Gerenciador de Conta de Armazenamento.](./media/functions-create-storage-blob-triggered-function/functions-storage-manager-connect-1.png)

1. Insira o **Nome da conta** e **Chave de conta** da etapa 1, clique em **Avançar** e em **Conectar**. 

    ![Insira as credenciais de armazenamento e conecte-se.](./media/functions-create-storage-blob-triggered-function/functions-storage-manager-connect-2.png)

1. Expanda a conta de armazenamento anexada, clique com o botão direito do mouse em **Contêineres de blob**, clique em **Criar contêiner de blob**, digite `mycontainer` e pressione enter.

    ![Crie uma fila de armazenamento.](./media/functions-create-storage-blob-triggered-function/functions-storage-manager-create-blob-container.png)

Agora que você tem um contêiner de blob, você pode testar a função carregando um arquivo para o contêiner.

## <a name="test-the-function"></a>Testar a função

1. De volta ao Portal do Azure, navegue até sua função, expanda os **Logs** na parte inferior da página e verifique se o streaming de log não está em pausa.

1. No Gerenciador de Armazenamento, expanda sua conta de armazenamento, **Contêineres de blob** e **mycontainer**. Clique em **Carregar** e depois em **Carregar arquivos...**.

    ![Carregue um arquivo para o contêiner de blob.](./media/functions-create-storage-blob-triggered-function/functions-storage-manager-upload-file-blob.png)

1. Na caixa de diálogo **Carregar arquivos**, clique no campo **Arquivos**. Navegue até um arquivo em seu computador local, por exemplo, um arquivo de imagem, selecione-o e clique em **Abrir** e depois em **Carregar**.

1. Volte para os logs de função e verifique se o blob foi lido.

   ![Exiba a mensagem nos logs.](./media/functions-create-storage-blob-triggered-function/functions-blob-storage-trigger-view-logs.png)

    >[!NOTE]
    > Quando seu aplicativo de funções é executado no plano de consumo padrão, pode haver um atraso de até vários minutos entre o blob que está sendo adicionado ou atualizado e a função sendo disparada. Se você precisar de baixa latência em suas funções disparadas por blob, considere executar seu aplicativo de funções em um Plano do Serviço de Aplicativo.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Você criou uma função que é executada quando um blob é adicionado a um Armazenamento de Blobs ou atualizado nele. 

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Para obter mais informações sobre gatilhos de armazenamento de blobs, consulte [Associações de Armazenamento de Blobs do Azure Functions](functions-bindings-storage-blob.md).
