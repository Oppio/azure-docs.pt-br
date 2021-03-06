---
title: Integre os registros de recursos do Azure nos seus sistemas SIEM | Microsoft Docs
description: "Saiba mais sobre a integração de log do Azure, seus principais recursos e como ela funciona."
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: TerryLanfear
ms.assetid: 9c1346e1-baf8-4975-b2f2-42ae05b2dc0a
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/20/2017
ms.author: TomSh
ms.translationtype: Human Translation
ms.sourcegitcommit: d9ae8e8948d82b9695d7d144d458fe8180294084
ms.openlocfilehash: 616a29b3f7db817e63a3a3673b845cc86752af77
ms.contentlocale: pt-br
ms.lasthandoff: 05/23/2017


---
# <a name="introduction-to-microsoft-azure-log-integration"></a>Introdução à integração de log do Microsoft Azure
Saiba mais sobre a integração de log do Azure, seus principais recursos e como ela funciona.

## <a name="overview"></a>Visão geral

A integração do log do Azure é uma solução gratuita que permite integrar logs brutos dos recursos do Azure a sistemas SIEM (Gerenciamento de Informações e Eventos de Segurança) locais.

A Integração do Log do Azure coleta eventos do Windows de canais do Visualizador de Eventos do Windows, [Logs de Atividades do Azure](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md), [Alertas da Central de Segurança do Azure](../security-center/security-center-intro.md) e [Logs de Diagnóstico do Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) de recursos do Azure. Esta integração ajuda sua solução de SIEM a fornecer um painel unificado para todos os seus ativos, locais ou na nuvem, para que você possa agregar, correlacionar, analisar e emitir alertas de eventos de segurança.

>[!NOTE]
No momento, as únicas nuvens com suporte são o Azure comercial e Azure Governamental. Não há suporte para outras nuvens no momento.

![Integração de log do Azure][1]

## <a name="what-logs-can-i-integrate"></a>Quais logs posso integrar?
O Azure produz um log abrangente para cada um de seus serviço. Esses logs representam três tipos de logs:

* **Logs de controle/gerenciamento**, que fornecem visibilidade das operações CREATE, UPDATE e DELETE do [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). Os Logs de Atividades do Azure são um exemplo desse tipo de log.
* **Logs do plano de dados**, que fornecem visibilidade dos eventos gerados como parte do uso de um recurso do Azure. Um exemplo desse tipo de log são os canais **Sistema**, **Segurança** e **Aplicativo** do Visualizador de Eventos do Windows em uma máquina virtual Windows. Outro exemplo é o Log de Diagnóstico configurado por meio do Azure Monitor
* **Eventos processados** fornecem informações de eventos e alertas processados em seu nome. Um exemplo desse tipo de evento são os Alertas da Central de Segurança do Azure, em que a Central de Segurança do Azure processou e analisou sua assinatura para fornecer alertas relevantes para sua postura de segurança atual.

A Integração do Log do Azure dá suporte à integração dos Logs de Atividades do Azure, Logs de Eventos do Windows de máquinas virtuais do Windows em sua assinatura do Azure, alertas da Central de Segurança do Azure, Logs de Diagnóstico do Azure e logs de auditoria do Azure Active Directory no momento.

A tabela a seguir explica a categoria de log e os detalhes de integração do SIEM

| Tipo de log  |JSON de suporte ao Log Analytics (Splunk, ELK)| ArcSight  | QRadar  |   
|---|---|---|---|
|  Logs de auditoria do AAD |  Sim | Exige a criação de um arquivo analisador de JSON do FlexConnector. Consulte a documentação do ArcSight para obter mais informações  |  Você deve criar uma extensão de origem do log. Consulte a documentação do QRadar para obter mais informações. |  
| Logs de atividade  | Sim  |  O arquivo analisador de JSON do FlexConnector está disponível no Centro de Download junto com o download de integração de log do Azure |  [QRadar DSM](https://www.ibm.com/support/knowledgecenter/SSKMKU/com.ibm.dsm.doc/c_dsm_guide_microsoft_azure_overview.html) (enviar por Syslog) |  
| Alertas do ASC  | Sim  |  Exige a criação de um arquivo analisador de JSON do FlexConnector. Consulte a documentação do ArcSight para obter mais informações. | [QRadar DSM](https://www.ibm.com/support/knowledgecenter/SSKMKU/com.ibm.dsm.doc/c_dsm_guide_microsoft_azure_overview.html) (enviar pelo Syslog)   |   
| Logs de diagnóstico (logs de recurso) | Sim | Precisa que o usuário final crie o arquivo do analisador JSON FlexConnector. Consulte a documentação do ArcSight para saber como fazer isso. | Você deve criar uma extensão de origem do log. Consulte a documentação do QRadar para obter mais informações |
| Logs da VM | Sim, por meio de eventos encaminhados e não por meio do JSON | Sim, por meio de eventos encaminhados | Sim, por meio de eventos encaminhados |

>[!NOTE]
Embora a Integração do Log do Azure seja uma solução gratuita, haverá custos de armazenamento do Azure resultantes do armazenamento de informações do arquivo de log.

Ajuda da comunidade está disponível por meio de [Fórum do MSDN de integração de Log do Azure](https://social.msdn.microsoft.com/Forums/office/home?forum=AzureLogIntegration). O fórum fornece para a comunidade AzLog a capacidade de apoiar uns aos outros com perguntas, respostas, dicas e truques sobre como aproveitar ao máximo a integração de log do Azure. Além disso, a equipe de Integração do Log do Azure monitora esse fórum e ajuda sempre que possível.

Você também pode abrir uma [solicitação de suporte](../azure-supportability/how-to-create-azure-support-request.md). Para fazer isso, selecione **integração de log** como o serviço para o qual você está solicitando suporte.

## <a name="next-steps"></a>Próximas etapas
Neste documento, você foi apresentado à integração de log do Azure. Para saber mais sobre a integração de log do Azure e os tipos de logs com suporte, confira o seguinte:

* [Integração do Log do Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=53324) – Visite o Centro de Download para obter os detalhes, os requisitos de sistema e as instruções de instalação da integração do Log do Azure.
* [Introdução à integração do log do Azure](security-azure-log-integration-get-started.md) — este tutorial explica as etapas de instalação da integração do log do Azure, bem como a integração de logs do armazenamento WAD do Azure, Logs de Atividades do Azure, alertas da Central de Segurança do Azure e logs de auditoria do Azure Active Directory.
* [Etapas de configuração de parceiro](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) – esta postagem de blog mostra a você como configurar a integração de log do Azure para trabalhar com as soluções de parceiros Splunk, HP ArcSight e IBM QRadar.
* [Alertas de atividade e ASC pelo syslog para o QRadar](https://blogs.msdn.microsoft.com/azuresecurity/2016/09/24/integrate-azure-logs-to-qradar/) – Esta postagem de blog fornece as etapas para enviar alertas de atividade e da Central de Segurança do Azure pelo syslog para o QRadar
* [Perguntas frequentes sobre o log de integração do Azure](security-azure-log-integration-faq.md) – encontre as respostas para as perguntas frequentes sobre a integração de log do Azure.
* [Integrando alertas da Central de Segurança com a Integração do Log do Azure](../security-center/security-center-integrating-alerts-with-log-integration.md) – Este documento mostra como sincronizar os alertas da Central de Segurança do Azure com a Integração do Log do Azure.

<!--Image references-->
[1]: ./media/security-azure-log-integration-overview/azure-log-integration.png

