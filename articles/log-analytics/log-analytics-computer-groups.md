---
title: Grupos de computadores em pesquisas de log do Log Analytics | Microsoft Docs
description: "Os grupos de computadores no Log Analytics permitem analisar pesquisas de log para um conjunto específico de computadores.  Este artigo descreve os diferentes métodos que você pode usar para criar grupos de computadores e como usá-los em uma pesquisa de log."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: 
ms.assetid: a28b9e8a-6761-4ead-aa61-c8451ca90125
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/09/2016
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 6c0affd0f5ea600f979cfcc87e2435658c8dab14


---
# <a name="computer-groups-in-log-analytics-log-searches"></a>Grupos de computadores em pesquisas de log do Log Analytics
Os grupos de computadores no Log Analytics permitem analisar [pesquisas de log](log-analytics-log-searches.md) para um conjunto específico de computadores.  Cada grupo é preenchido com computadores usando uma consulta que você define ou importando grupos de fontes diferentes.  Quando o grupo é incluído em uma pesquisa de log, os resultados são limitados aos registros que correspondem os computadores do grupo.

## <a name="creating-a-computer-group"></a>Criando um grupo de computadores
Você pode criar um grupo de computadores no Log Analytics usando qualquer um dos métodos na tabela a seguir.  Detalhes sobre cada método são fornecidos nas seções a seguir. 

| Método | Descrição |
|:--- |:--- |
| Pesquisa de log |Crie uma pesquisa de log que retorna uma lista de computadores e salva os resultados como um grupo de computadores. |
| API da Pesquisa de Log |Use a API da Pesquisa de Log para criar um grupo de computadores programaticamente com base nos resultados de uma pesquisa de log. |
| Active Directory |Verifique automaticamente a associação de grupo dos computadores de agente que são membros de um domínio do Active Directory e crie um grupo no Log Analytics para cada grupo de segurança. |
| WSUS |Verifique servidores ou clientes de WSUS para direcionar grupos e criar um grupo no Log Analytics para cada um. |

### <a name="log-search"></a>Pesquisa de log
Grupos de computadores criados por meio de uma Pesquisa de Log conterá todos os computadores retornados por uma consulta de pesquisa que você definir.  Essa consulta é executada sempre que o grupo de computadores é usado para que todas as alterações desde que o grupo foi criado sejam refletidas.

Use o procedimento a seguir para criar um grupo de computadores de uma pesquisa de log.

1. [Crie uma pesquisa de log](log-analytics-log-searches.md) que retorna uma lista de computadores.  A pesquisa deve retornar um conjunto distinto de computadores usando algo como **Computador distinto** ou **medir contagem() por Computador** na consulta.  
2. Clique no botão **Salvar** na parte superior da tela.
3. Selecione **Sim** para **Salvar esta consulta como um grupo de computadores:**.
4. Digite um **Nome** e uma **Categoria** para o grupo.  Se já existir uma pesquisa com o mesmo nome e categoria, você será solicitado a substituí-la.  Você pode ter várias pesquisas com o mesmo nome em categorias diferentes. 

A seguir, temos pesquisas de exemplo que você pode salvar como um grupo de computadores.

    Computer="Computer1" OR Computer="Computer2" | distinct Computer 
    Computer=*srv* | measure count() by Computer

### <a name="log-search-api"></a>API da Pesquisa de Log
Grupos de computadores criados com a API da Pesquisa de Log são iguais a pesquisas criadas com uma Pesquisa de Log.

Para obter detalhes sobre como criar um grupo de computadores usando a API da Pesquisa de Log, consulte [Grupos de computadores na API REST de pesquisa de log do Log Analytics](log-analytics-log-search-api.md#computer-groups).

### <a name="active-directory"></a>Active Directory
Quando você configura o Log Analytics para importar associações de grupo do Active Directory, ele analisará a associação de grupo de todos os computadores associados ao domínio com o agente do OMS.  Um grupo de computadores é criado no Log Analytics para cada grupo de segurança no Active Directory, e cada computador é adicionado aos grupos de computadores que correspondem aos grupos de segurança de que são membros.  Essa associação é atualizada continuamente a cada 4 horas.  

Configure o Log Analytics para importar grupos de segurança do Active Directory do menu **Grupos de Computadores** nas **Configurações** do Log Analytics.  Selecione **Automação** e **Importe as associações de grupo do Active Directory dos computadores**.  Não é necessária nenhuma configuração.

![Grupos de computadores do Active Directory](media/log-analytics-computer-groups/configure-activedirectory.png)

Quando os grupos forem importados, o menu listará o número de computadores com a associação de grupo detectada e o número de grupos importados.  Você pode clicar em qualquer um desses links para retornar os registros de **ComputerGroup** com essas informações.

### <a name="windows-server-update-service"></a>Serviços de Atualização do Windows Server
Quando você configura o Log Analytics para importar associações de grupo do WSUS, ele analisará a associação de grupo de destino de todos os computadores com o agente do OMS.  Se você estiver usando o direcionamento do lado do cliente, qualquer computador que estiver conectado ao OMS e fizer parte de qualquer grupo de direcionamento do WSUS terá sua associação de grupo importada para o Log Analytics. Se você estiver usando o direcionamento do lado do servidor, o agente do OMS deverá ser instalado no servidor do WSUS para que as informações de associação do grupo sejam importadas ao OMS.  Essa associação é atualizada continuamente a cada 4 horas. 

Configure o Log Analytics para importar grupos de segurança do Active Directory do menu **Grupos de Computadores** nas **Configurações** do Log Analytics.  Selecione **Active Directory** e **Importe as associações de grupo do Active Directory dos computadores**.  Não é necessária nenhuma configuração.

![Grupos de computadores do Active Directory](media/log-analytics-computer-groups/configure-wsus.png)

Quando os grupos forem importados, o menu listará o número de computadores com a associação de grupo detectada e o número de grupos importados.  Você pode clicar em qualquer um desses links para retornar os registros de **ComputerGroup** com essas informações.

## <a name="managing-computer-groups"></a>Gerenciando grupos de computadores
Você pode exibir grupos de computadores que foram criados por meio de uma pesquisa de log ou da API da Pesquisa de Log do menu **Grupos de Computadores** nas **Configurações** do Log Analytics.  Clique no **x** na coluna **Remover** para excluir o grupo de computadores.  Clique no ícone **Exibir membros** para que um grupo execute a pesquisa de log do grupo que retorna seus membros. 

![Grupos de computadores salvados](media/log-analytics-computer-groups/configure-saved.png)

Para modificar o grupo, crie um novo grupo com a mesma **Categoria** e o **Nome** para substituir o grupo original.

## <a name="using-a-computer-group-in-a-log-search"></a>Usando um grupo de computadores em uma pesquisa de log
Use a seguinte sintaxe para se referir a um grupo de computadores em uma pesquisa de log.  Especificar a **Categoria** será opcional e necessário somente se você tiver grupos de computadores com o mesmo nome em diferentes categorias. 

    $ComputerGroups[Category: Name]

Quando uma pesquisa é executada, os membros dos grupos de computadores incluídos na pesquisa são resolvidos primeiro.  Se o grupo for baseado em uma pesquisa de log, a pesquisa será executada para retornar apenas os membros do grupo antes de executar a pesquisa de log de nível superior.

Grupos de computadores normalmente são usados com a cláusula **IN** na pesquisa de log, como no exemplo a seguir.

    Type=UpdateSummary Computer IN $ComputerGroups[My Computer Group]

## <a name="computer-group-records"></a>Registros de grupo de computadores
Um registro é criado no repositório do OMS para cada associação do grupo do computadores criada no Active Directory ou no WSUS.  Esses registros de desempenho têm um tipo de **ComputerGroup** e têm as propriedades na tabela a seguir.  Registros não são criados para grupos de computadores com base em pesquisas de log.

| Propriedade | Descrição |
|:--- |:--- |
| Tipo |*ComputerGroup* |
| SourceSystem |*SourceSystem* |
| Computador |Nome do computador membro. |
| Agrupar |Nome do grupo. |
| GroupFullName |Caminho completo para o grupo, incluindo a fonte e o nome da fonte. |
| GroupSource |Fonte da qual o grupo foi coletado. <br><br>Active Directory<br>WSUS<br>WSUSClientTargeting |
| GroupSourceName |Nome da fonte da qual os grupos foram coletados.  Para o Active Directory, este é o nome de domínio. |
| ManagementGroupName |Nome do grupo de gerenciamento de agentes do SCOM.  Para outros agentes, ele é AOI-\<ID do espaço de trabalho\> |
| TimeGenerated |Data e hora em que o grupo de computadores foi criado ou atualizado. |

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre [pesquisas de log](log-analytics-log-searches.md) para analisar os dados coletados de fontes de dados e soluções.  




<!--HONumber=Nov16_HO3-->


