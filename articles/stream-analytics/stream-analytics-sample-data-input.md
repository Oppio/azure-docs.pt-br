---
title: Entradas de amostragem no Stream Analytics do Azure | Microsoft Docs
description: Identificar problemas ao solucionar problemas em trabalhos do Stream Analytics.
keywords: "entrada de solução de problemas, amostragem de entrada"
documentationcenter: 
services: stream-analytics
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: jeffstok
ms.translationtype: Human Translation
ms.sourcegitcommit: 7f8b63c22a3f5a6916264acd22a80649ac7cd12f
ms.openlocfilehash: 8a8664cec88f2d04a3dff55df0e6ccea1ea79422
ms.contentlocale: pt-br
ms.lasthandoff: 05/01/2017


---
# <a name="azure-stream-analytics-input-stream-sampling"></a>Amostragem do fluxo de entrada do Stream Analytics do Azure

Usando o Stream Analytics do Azure, você pode criar uma amostra dos eventos de entrada que acompanham um arquivo e testar consultas no portal sem a necessidade de iniciar ou interromper um trabalho.

## <a name="testing-your-query"></a>Testando sua consulta

No painel de detalhes de trabalho do Stream Analytics, abra a folha **Editor de Consultas** clicando no nome da consulta em **Consulta**. (No nosso cenário de exemplo, como ainda não foi criada nenhuma consulta, clique no espaço reservado **< >**.)

![O editor de consultas do Stream Analytics](media/stream-analytics-sample-data-input/stream-analytics-query-editor.png)

A folha do editor avançado para criar sua consulta é exibida como era na versão anterior. Agora a folha foi atualizada com um novo painel à esquerda que mostra as entradas e saídas que são usadas pela consulta e definidas para esse trabalho.

![As listas de entradas e saídas do editor de consultas do Stream Analytics](media/stream-analytics-sample-data-input/stream-analytics-query-editor-highlight.png)

São mostradas também uma entrada adicional e uma saída, que não são definidas. Elas são provenientes do novo modelo de consulta com que você começa. Elas mudam, ou até mesmo desaparecem, conforme você edita a consulta. Você pode ignorá-las com segurança por ora.

Para testar com dados de entrada de exemplo, clique com o botão direito do mouse em qualquer uma de suas entradas e, em seguida, selecione **Carregar dados de exemplo do arquivo**.

![O comando Carregar dados de exemplo do arquivo do editor de consultas do Stream Analytics](media/stream-analytics-sample-data-input/stream-analytics-query-editor-upload.png)

Depois que o upload for concluído, clique em **Teste** para testar essa consulta em relação aos dados de exemplo que você forneceu.

![O botão Testar do editor de consultas do Stream Analytics](media/stream-analytics-sample-data-input/stream-analytics-query-editor-test.png)

Se você deseja salvar a saída do teste para uso posterior, a saída da consulta é exibida no navegador com um link para os resultados do download. Agora você pode modificar fácil e iterativamente sua consulta e testá-la repetidamente para ver como a saída muda.

![A saída do exemplo do editor de consultas do Stream Analytics](media/stream-analytics-sample-data-input/stream-analytics-query-editor-samples-output.png)

Na imagem anterior, uma segunda saída foi adicionada, chamada **HighAvgTempOutput**.

Ao usar várias saídas em uma consulta, você pode ver os resultados de cada saída separadamente e alterná-los facilmente.

Depois que estiver satisfeito com os resultados, você poderá salvar sua consulta, iniciar seu trabalho, sentar-se e assistir à mágica do Stream Analytics.

## <a name="get-help"></a>Obter ajuda

Para obter mais assistência, experimente nosso [fórum do Stream Analytics do Azure](https://social.msdn.microsoft.com/Forums/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Próximas etapas
* [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
* [Introdução ao uso do Stream Analytics do Azure](stream-analytics-get-started.md)
* [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
* [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência da API REST do Gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

