---
title: 使用高级诊断查询排查问题 (SQL API)
titleSuffix: Azure Cosmos DB
description: 了解如何查询诊断日志以对存储在 Azure Cosmos DB 中的数据进行故障排除 (SQL API)。
author: StefArroyo
services: cosmos-db
ms.service: cosmos-db
ms.topic: how-to
ms.date: 06/12/2021
ms.author: esarroyo
ms.openlocfilehash: 16c4fe809fc411a9d6eec89ed7ceeb04dce317d2
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123097768"
---
# <a name="troubleshoot-issues-with-advanced-diagnostics-queries-for-the-sql-core-api"></a>使用针对 SQL（核心）API 的高级诊断查询排查问题

[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [SQL（核心）API](cosmos-db-advanced-queries.md)
> * [MongoDB API](mongodb/diagnostic-queries-mongodb.md)
> * [Cassandra API](cassandra/diagnostic-queries-cassandra.md)
> * [Gremlin API](graph/diagnostic-queries-gremlin.md)
>

本文介绍如何使用发送到 Azure Diagnostics（旧版）和特定于资源（预览版）的表的诊断日志编写更高级的查询，以便排查 Azure Cosmos DB 帐户的问题 。

对于 Azure 诊断表，所有数据都写入一个表中。 用户指定要查询的类别。 若要查看请求的全文查询，请参阅[使用 Azure 中的诊断设置监视 Azure Cosmos DB 数据](cosmosdb-monitor-resource-logs.md#full-text-query)，了解如何启用此功能。

对于[特定于资源的表](cosmosdb-monitor-resource-logs.md#create-setting-portal)，数据将写入每个资源类别的各个表中。 建议采用此模式，因为它：

- 使数据的处理容易得多。 
- 提供更好的架构可发现性。
- 改善了引入延迟和查询时间方面的性能。

## <a name="common-queries"></a>常见查询
常见查询显示在特定于资源的表和 Azure 诊断表中。

### <a name="top-n10-queries-ordered-by-request-unit-ru-consumption-in-a-specific-time-frame"></a>在特定期限内按请求单位 (RU) 使用量排序的前 N (10) 个查询

# <a name="resource-specific"></a>[特定于资源](#tab/resource-specific)

   ```Kusto
   let topRequestsByRUcharge = CDBDataPlaneRequests 
   | where TimeGenerated > ago(24h)
   | project  RequestCharge , TimeGenerated, ActivityId;
   CDBQueryRuntimeStatistics
   | project QueryText, ActivityId, DatabaseName , CollectionName
   | join kind=inner topRequestsByRUcharge on ActivityId
   | project DatabaseName , CollectionName , QueryText , RequestCharge, TimeGenerated
   | order by RequestCharge desc
   | take 10
   ```
# <a name="azure-diagnostics"></a>[Azure 诊断](#tab/azure-diagnostics)

   ```Kusto
   let topRequestsByRUcharge = AzureDiagnostics
   | where Category == "DataPlaneRequests" and TimeGenerated > ago(24h)
   | project  requestCharge_s , TimeGenerated, activityId_g;
   AzureDiagnostics
   | where Category == "QueryRuntimeStatistics"
   | project querytext_s, activityId_g, databasename_s , collectionname_s
   | join kind=inner topRequestsByRUcharge on activityId_g
   | project databasename_s , collectionname_s , querytext_s , requestCharge_s, TimeGenerated
   | order by requestCharge_s desc
   | take 10
   ```    
---

### <a name="requests-throttled-statuscode--429-in-a-specific-time-window"></a>在特定时间范围内受限制的请求数 (statusCode = 429) 

# <a name="resource-specific"></a>[特定于资源](#tab/resource-specific)

   ```Kusto
   let throttledRequests = CDBDataPlaneRequests
   | where StatusCode == "429"
   | project  OperationName , TimeGenerated, ActivityId;
   CDBQueryRuntimeStatistics
   | project QueryText, ActivityId, DatabaseName , CollectionName
   | join kind=inner throttledRequests on ActivityId
   | project DatabaseName , CollectionName , QueryText , OperationName, TimeGenerated
   ```
# <a name="azure-diagnostics"></a>[Azure 诊断](#tab/azure-diagnostics)

   ```Kusto
   let throttledRequests = AzureDiagnostics
   | where Category == "DataPlaneRequests" and statusCode_s == "429"
   | project  OperationName , TimeGenerated, activityId_g;
   AzureDiagnostics
   | where Category == "QueryRuntimeStatistics"
   | project querytext_s, activityId_g, databasename_s , collectionname_s
   | join kind=inner throttledRequests on activityId_g
   | project databasename_s , collectionname_s , querytext_s , OperationName, TimeGenerated
   ```    
---

### <a name="queries-with-the-largest-response-lengths-payload-size-of-the-server-response"></a>响应长度最大的查询（服务器响应的有效负载大小）

# <a name="resource-specific"></a>[特定于资源](#tab/resource-specific)

   ```Kusto
   let operationsbyUserAgent = CDBDataPlaneRequests
   | project OperationName, DurationMs, RequestCharge, ResponseLength, ActivityId;
   CDBQueryRuntimeStatistics
   //specify collection and database
   //| where DatabaseName == "DBNAME" and CollectionName == "COLLECTIONNAME"
   | join kind=inner operationsbyUserAgent on ActivityId
   | summarize max(ResponseLength) by QueryText
   | order by max_ResponseLength desc
   ```
# <a name="azure-diagnostics"></a>[Azure 诊断](#tab/azure-diagnostics)

   ```Kusto
   let operationsbyUserAgent = AzureDiagnostics
   | where Category=="DataPlaneRequests"
   | project OperationName, duration_s, requestCharge_s, responseLength_s, activityId_g;
   AzureDiagnostics
   | where Category == "QueryRuntimeStatistics"
   //specify collection and database
   //| where databasename_s == "DBNAME" and collectioname_s == "COLLECTIONNAME"
   | join kind=inner operationsbyUserAgent on activityId_g
   | summarize max(responseLength_s1) by querytext_s
   | order by max_responseLength_s1 desc
   ```    
---

### <a name="ru-consumption-by-physical-partition-across-all-replicas-in-the-replica-set"></a>按物理分区划分的 RU 使用量（跨副本集中的所有副本）

# <a name="resource-specific"></a>[特定于资源](#tab/resource-specific)

   ```Kusto
   CDBPartitionKeyRUConsumption
   | where TimeGenerated >= now(-1d)
   //specify collection and database
   //| where DatabaseName == "DBNAME" and CollectionName == "COLLECTIONNAME"
   // filter by operation type
   //| where operationType_s == 'Create'
   | summarize sum(todouble(RequestCharge)) by toint(PartitionKeyRangeId)
   | render columnchart
   ```
# <a name="azure-diagnostics"></a>[Azure 诊断](#tab/azure-diagnostics)

   ```Kusto
   AzureDiagnostics
   | where TimeGenerated >= now(-1d)
   | where Category == 'PartitionKeyRUConsumption'
   //specify collection and database
   //| where databasename_s == "DBNAME" and collectioname_s == "COLLECTIONNAME"
   // filter by operation type
   //| where operationType_s == 'Create'
   | summarize sum(todouble(requestCharge_s)) by toint(partitionKeyRangeId_s)
   | render columnchart  
   ```    
---

### <a name="ru-consumption-by-logical-partition-across-all-replicas-in-the-replica-set"></a>按逻辑分区划分的 RU 使用量（跨副本集中的所有副本）

# <a name="resource-specific"></a>[特定于资源](#tab/resource-specific)

   ```Kusto
   CDBPartitionKeyRUConsumption
   | where TimeGenerated >= now(-1d)
   //specify collection and database
   //| where DatabaseName == "DBNAME" and CollectionName == "COLLECTIONNAME"
   // filter by operation type
   //| where operationType_s == 'Create'
   | summarize sum(todouble(RequestCharge)) by PartitionKey, PartitionKeyRangeId
   | render columnchart  
   ```
# <a name="azure-diagnostics"></a>[Azure 诊断](#tab/azure-diagnostics)

   ```Kusto
   AzureDiagnostics
   | where TimeGenerated >= now(-1d)
   | where Category == 'PartitionKeyRUConsumption'
   //specify collection and database
   //| where databasename_s == "DBNAME" and collectioname_s == "COLLECTIONNAME"
   // filter by operation type
   //| where operationType_s == 'Create'
   | summarize sum(todouble(requestCharge_s)) by partitionKey_s, partitionKeyRangeId_s
   | render columnchart  
   ```
---

## <a name="next-steps"></a>后续步骤
* 若要详细了解如何为 Azure Cosmos DB 创建诊断设置，请参阅[创建诊断设置](cosmosdb-monitor-resource-logs.md)。
* 若要详细了解如何通过 Azure 门户、Azure CLI 或 PowerShell 创建诊断设置，请参阅[创建诊断设置以在 Azure 中收集平台日志和指标](../azure-monitor/essentials/diagnostic-settings.md)。
