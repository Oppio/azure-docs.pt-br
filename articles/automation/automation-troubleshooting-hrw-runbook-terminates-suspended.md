---
title: 'Hybrid Runbook Worker: um trabalho de runbook termina com o status Suspenso | Microsoft Docs'
description: "Sintomas, causas e resoluções para o erro de terminação de trabalho do Hybrid Runbook Worker."
services: automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: tysonn
ms.assetid: 02c6606e-8924-4328-a196-45630c2255e9
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/17/2016
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 7c6365b729d73f1c5b9bc57952b1723255d9e9f0


---
# <a name="hybrid-runbook-worker-a-runbook-job-terminates-with-a-status-of-suspended"></a>Hybrid Runbook Worker : um trabalho de runbook termina com o status Suspenso
## <a name="summary"></a>Resumo
Seu runbook foi suspenso logo depois de tentar executá-lo três vezes. Existem condições que podem impedir o runbook de ser concluído com êxito e a mensagem de erro relacionada não inclui informações adicionais indicando o motivo. Este artigo fornece etapas de solução de problemas relacionados a falhas de execução de runbook do Hybrid Runbook Worker.

Se o problema do Azure não for resolvido neste artigo, visite os fóruns do Azure no [MSDN e Stack Overflow](https://azure.microsoft.com/support/forums/). Você pode postar seu problema nesses fóruns ou usando [@AzureSupport no Twitter](https://twitter.com/AzureSupport). Além disso, você pode registrar uma solicitação de suporte do Azure selecionando **Obter suporte** no site de [suporte do Azure](https://azure.microsoft.com/support/options/) .

## <a name="symptom"></a>Sintoma
Falha na execução de runbook e o erro retornado é "a ação do trabalho 'Activate' não pode ser executada porque o processo foi interrompido inesperadamente. A ação do trabalho foi tentada 3 vezes."

## <a name="cause"></a>Causa
Há várias causas possíveis para o erro: 

1. O Hybrid Worker está usando um proxy ou firewall
2. O computador em que o Hybrid Worker está em execução tem menos que os [requisitos](automation-hybrid-runbook-worker.md#hybrid-runbook-worker-requirements) 
3. Os runbooks não podem ser autenticados com recursos locais

## <a name="cause-1-hybrid-runbook-worker-is-behind-proxy-or-firewall"></a>Causa 1: o Hybrid Runbook Worker está protegido por proxy ou firewall
O computador em que o Hybrid Runbook Worker está em execução está protegido por um servidor proxy ou firewall e o acesso de rede de saída não pode ser permitido ou configurado corretamente.

### <a name="solution"></a>Solução
Verifique se o computador tem acesso de saída para *.cloudapp.net portas 443, 9354 e 30199-30000. 

## <a name="cause-2-computer-has-less-than-minimum-hardware-requirements"></a>Causa 2: o computador tem requisitos de hardware inferiores aos mínimos
Os computadores que executam o Hybrid Runbook Worker devem atender aos requisitos mínimos de hardware antes de serem designados para hospedar esse recurso. Caso contrário, dependendo da utilização de recursos de outros processos em segundo plano e da contenção provocada por runbooks durante a execução, o computador ficará sobrecarregados e causará atrasos de trabalho de runbook ou tempos limite. 

### <a name="solution"></a>Solução
Confirme se o computador designado para executar o recurso Hybrid Runbook Worker atende aos requisitos mínimos de hardware.  Se isso acontecer, monitore a utilização de CPU e memória para determinar qualquer correlação entre o desempenho de processos do Hybrid Runbook Worker e o Windows.  Se não houver memória ou pressão da CPU, isso pode indicar a necessidade de atualizar ou adicionar mais processadores ou aumentar a memória para eliminar o afunilamento de recursos e resolver o erro. Como alternativa, selecione um recurso de computação diferente que consiga dar suporte aos requisitos mínimos e ajuste a escala quando a demanda da carga de trabalho indicar que um aumento é necessário.         

## <a name="cause-3-runbooks-cannot-authenticate-with-local-resources"></a>Causa 3: os runbooks não podem ser autenticados com recursos locais
### <a name="solution"></a>Solução
Verifique o log de eventos do **Microsoft SMA** para ver um evento correspondente com descrição *Processo Win32 Encerrado com o código [4294967295]*.  A causa desse erro é que você ainda não configurou a autenticação em seus runbooks ou especificou as credenciais Executar como para o grupo do Hybrid Worker.  Examine as [permissões de Runbook](automation-hybrid-runbook-worker.md#runbook-permissions) para confirmar que você configurou corretamente a autenticação para seus runbooks.  




<!--HONumber=Nov16_HO3-->


