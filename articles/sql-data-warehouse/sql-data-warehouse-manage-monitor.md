---
title: Monitore sua carga de trabalho usando DMVs | Microsoft Docs
description: Aprenda a monitorar sua carga de trabalho usando DMVs.
services: sql-data-warehouse
documentationcenter: NA
author: sqlmojo
manager: jhubbard
editor: 
ms.assetid: 69ecd479-0941-48df-b3d0-cf54c79e6549
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: performance
ms.date: 10/31/2016
ms.author: joeyong;barbkess
ms.translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 3735d656429da1f1fe7569f640b272b099382032
ms.contentlocale: pt-br
ms.lasthandoff: 04/03/2017


---
# <a name="monitor-your-workload-using-dmvs"></a>Monitore sua carga de trabalho usando DMVs
Este artigo descreve como usar Visualizações de Gerenciamento Dinâmico (DMVs) para monitorar sua carga de trabalho e investigar a execução da consulta no SQL Data Warehouse do Azure.

## <a name="permissions"></a>Permissões
Para consultar as DMVs deste artigo, você precisa de permissão VIEW DATABASE STATE ou CONTROL. Geralmente, é preferível conceder a permissão VIEW DATABASE STATE, por ser muito mais restritiva.

```sql
GRANT VIEW DATABASE STATE TO myuser;
```

## <a name="monitor-connections"></a>Conexões do monitor
Todos os logons no SQL Data Warehouse são registrados em [sys.dm_pdw_exec_sessions][sys.dm_pdw_exec_sessions].  Essa DMV contém os últimos 10.000 logons.  A session_id é a chave primária e é atribuída em sequência para cada novo logon.

```sql
-- Other Active Connections
SELECT * FROM sys.dm_pdw_exec_sessions where status <> 'Closed' and session_id <> session_id();
```

## <a name="monitor-query-execution"></a>Monitorar a execução de consultas
Todas as consultas executadas no SQL Data Warehouse são registradas em [sys.dm_pdw_exec_requests][sys.dm_pdw_exec_requests].  Essa DMV contém as últimas 10.000 consultas executadas.  A request_id identifica cada consulta exclusivamente e é a chave primária para essa DMV.  A request_id é atribuída em sequência para cada nova consulta e é prefixada com QID, que representa a ID da consulta.  A consulta a esta DMV para uma determinada session_id mostra todas as consultas para um logon específico.

> [!NOTE]
> Os procedimentos armazenados usam vários request_ids.  As IDs de solicitação são atribuídas em ordem sequencial. 
> 
> 

Estas são as etapas para investigar os planos de execução da consulta e as horas para uma consulta específica.

### <a name="step-1-identify-the-query-you-wish-to-investigate"></a>ETAPA 1: Identificar a consulta que você deseja investigar
```sql
-- Monitor active queries
SELECT * 
FROM sys.dm_pdw_exec_requests 
WHERE status not in ('Completed','Failed','Cancelled')
  AND session_id <> session_id()
ORDER BY submit_time DESC;

-- Find top 10 queries longest running queries
SELECT TOP 10 * 
FROM sys.dm_pdw_exec_requests 
ORDER BY total_elapsed_time DESC;

-- Find a query with the Label 'My Query'
-- Use brackets when querying the label column, as it it a key word
SELECT  *
FROM    sys.dm_pdw_exec_requests
WHERE   [label] = 'My Query';
```

Nos resultados da consulta anterior, **observe a ID da Solicitação** da consulta que você deseja investigar.

As consultas no estado **Suspenso** estão sendo enfileiradas devido aos limites de simultaneidade. Essas consultas também aparecem na consulta de esperas sys.dm_pdw_waits com um tipo de UserConcurrencyResourceType. Confira [Concurrency and workload management][Concurrency and workload management] (Gerenciamento de simultaneidade e carga de trabalho) para obter mais detalhes sobre os limites de simultaneidade. As consultas também podem esperar por motivos, como bloqueios.  Se sua consulta estiver aguardando um recurso, confira [Investigar consultas aguardando recursos][Investigating queries waiting for resources] mais adiante neste artigo.

Para simplificar a pesquisa de uma consulta na tabela sys.dm_pdw_exec_requests, use [LABEL][LABEL] para atribuir um comentário à consulta que pode ser pesquisada no modo de exibição sys.dm_pdw_exec_requests.

```sql
-- Query with Label
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query')
;
```

### <a name="step-2-investigate-the-query-plan"></a>ETAPA 2: investigar o plano de consulta
Use a ID de solicitação para recuperar o DSQL (plano de SQL distribuído) da consulta de [sys.dm_pdw_request_steps][sys.dm_pdw_request_steps].

```sql
-- Find the distributed query plan steps for a specific query.
-- Replace request_id with value from Step 1.

SELECT * FROM sys.dm_pdw_request_steps
WHERE request_id = 'QID####'
ORDER BY step_index;
```

Quando um plano DSQL estiver demorando mais do que o esperado, a causa pode ser um plano complexo com muitas etapas de DSQL ou apenas uma etapa demorando muito tempo.  Se o plano tiver muitas etapas com várias operações de movimentação, considere otimizar suas distribuições de tabela para reduzir a movimentação de dados. O artigo [Distribuição da tabela][Table distribution] explica por que os dados devem ser movidos para resolver uma consulta e explica algumas estratégias de distribuição para minimizar a movimentação de dados.

Para investigar mais detalhes sobre uma única etapa, verifique a coluna *operation_type* da etapa de consulta de execução longa de consulta e observe o **Índice da etapa**:

* Continue com a Etapa 3a para **Operações SQL**: OnOperation, RemoteOperation, ReturnOperation.
* Continue com a Etapa 3b para **Operações de movimentação de dados**: ShuffleMoveOperation, BroadcastMoveOperation, TrimMoveOperation, PartitionMoveOperation, MoveOperation, CopyOperation.

### <a name="step-3a-investigate-sql-on-the-distributed-databases"></a>ETAPA 3a: investigar o SQL nos bancos de dados distribuídos
Use a ID da Solicitação e o Índice de Etapas para recuperar os detalhes de [sys.dm_pdw_sql_requests][sys.dm_pdw_sql_requests], que contém informações sobre a execução da consulta em todos os bancos de dados distribuídos.

```sql
-- Find the distribution run times for a SQL step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_sql_requests
WHERE request_id = 'QID####' AND step_index = 2;
```

Se a consulta estiver em execução, [DBCC PDW_SHOWEXECUTIONPLAN][DBCC PDW_SHOWEXECUTIONPLAN] poderá ser usado para recuperar o plano estimado do SQL Server do cache do plano do SQL Server para a etapa em execução em uma distribuição específica.

```sql
-- Find the SQL Server execution plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(1, 78);
```

### <a name="step-3b-investigate-data-movement-on-the-distributed-databases"></a>ETAPA 3b: investigar a movimentação de dados em bancos de dados distribuídos
Use a ID da Solicitação e o Índice da Etapa para recuperar as informações sobre a etapa de movimentação dos dados em execução em cada distribuição em [sys.dm_pdw_dms_workers][sys.dm_pdw_dms_workers].

```sql
-- Find the information about all the workers completing a Data Movement Step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_dms_workers
WHERE request_id = 'QID####' AND step_index = 2;
```

* Verifique a coluna *total_elapsed_time* para ver se uma distribuição específica está demorando muito mais do que outras para movimentar dados.
* Para a distribuição de longa execução, verifique a coluna *rows_processed* para verificar se o número de linhas sendo movidas dessa distribuição é significativamente maior do que outros. Se for o caso, isso poderá indicar distorção de dados subjacentes.

Se a consulta estiver em execução, [DBCC PDW_SHOWEXECUTIONPLAN][DBCC PDW_SHOWEXECUTIONPLAN] poderá ser usado para recuperar o plano estimado do SQL Server do cache do plano do SQL Server para a Etapa de SQL em execução no momento em uma distribuição específica.

```sql
-- Find the SQL Server estimated plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(55, 238);
```

<a name="waiting"></a>

## <a name="monitor-waiting-queries"></a>Monitorar as consultas em espera
Caso você descubra que sua consulta não está fazendo progresso porque está aguardando um recurso, veja a seguir uma consulta que mostra todos os recursos que uma consulta está aguardando.

```sql
-- Find queries 
-- Replace request_id with value from Step 1.

SELECT waits.session_id,
      waits.request_id,  
      requests.command,
      requests.status,
      requests.start_time,  
      waits.type,
      waits.state,
      waits.object_type,
      waits.object_name
FROM   sys.dm_pdw_waits waits
   JOIN  sys.dm_pdw_exec_requests requests
   ON waits.request_id=requests.request_id
WHERE waits.request_id = 'QID####'
ORDER BY waits.object_name, waits.object_type, waits.state;
```

Se a consulta estiver ativamente aguardando recursos de outra consulta, o estado será **AcquireResources**.  Se a consulta tiver todos os recursos necessários, o estado será **Concedido**.

## <a name="next-steps"></a>Próximas etapas
Confira [Exibições do sistema][System views] para obter mais informações sobre DMVs.
Consulte [Práticas Recomendadas do SQL Data Warehouse][SQL Data Warehouse best practices] para saber mais sobre as práticas recomendadas

<!--Image references-->

<!--Article references-->
[Manage overview]: ./sql-data-warehouse-overview-manage.md
[SQL Data Warehouse best practices]: ./sql-data-warehouse-best-practices.md
[System views]: ./sql-data-warehouse-reference-tsql-system-views.md
[Table distribution]: ./sql-data-warehouse-tables-distribute.md
[Concurrency and workload management]: ./sql-data-warehouse-develop-concurrency.md
[Investigating queries waiting for resources]: ./sql-data-warehouse-manage-monitor.md#waiting

<!--MSDN references-->
[sys.dm_pdw_dms_workers]: http://msdn.microsoft.com/library/mt203878.aspx
[sys.dm_pdw_exec_requests]: http://msdn.microsoft.com/library/mt203887.aspx
[sys.dm_pdw_exec_sessions]: http://msdn.microsoft.com/library/mt203883.aspx
[sys.dm_pdw_request_steps]: http://msdn.microsoft.com/library/mt203913.aspx
[sys.dm_pdw_sql_requests]: http://msdn.microsoft.com/library/mt203889.aspx
[DBCC PDW_SHOWEXECUTIONPLAN]: http://msdn.microsoft.com/library/mt204017.aspx
[DBCC PDW_SHOWSPACEUSED]: http://msdn.microsoft.com/library/mt204028.aspx
[LABEL]: https://msdn.microsoft.com/library/ms190322.aspx

