---
title: 使用针对 Mongo API 的高级诊断查询排查问题
titleSuffix: Azure Cosmos DB
description: 了解如何查询诊断日志，以排查 Azure Cosmos DB for MongoDB API 中所存储数据的问题。
services: cosmos-db
ms.service: cosmos-db
ms.topic: how-to
ms.date: 06/12/2021
ms.author: esarroyo
author: StefArroyo
ms.openlocfilehash: 07d0942be40cf2834399db198d0c56e9485161c0
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2021
ms.locfileid: "122605115"
---
# <a name="troubleshoot-issues-with-advanced-diagnostics-queries-for-the-mongodb-api"></a>使用适用于 MongoDB API 的高级诊断查询来排查问题

[!INCLUDE[appliesto-all-apis-except-table](../includes/appliesto-all-apis-except-table.md)]

> [!div class="op_single_selector"]
> * [SQL（核心）API](../cosmos-db-advanced-queries.md)
> * [MongoDB API](diagnostic-queries-mongodb.md)
> * [Cassandra API](../cassandra/diagnostic-queries-cassandra.md)
> * [Gremlin API](../queries-gremlin.md)
>

本文介绍如何使用发送到 Azure 诊断（旧版）和资源专用（预览版）表的诊断日志编写更高级查询，以帮助排查 Azure Cosmos DB 帐户的问题。 

对于 Azure 诊断表，所有数据都写入一个表中。 用户指定要查询的类别。 若要查看请求的全文查询，请参阅[使用 Azure 中的诊断设置监视 Azure Cosmos DB 数据](../cosmosdb-monitor-resource-logs.md#full-text-query)，了解如何启用此功能。

对于[特定于资源的表](../cosmosdb-monitor-resource-logs.md#create-setting-portal)，数据将写入每个资源类别的各个表中。 出于以下目的，我们建议采用此模式：

- 简化数据处理程序。 
- 可提高架构的可发现性。
- 改善了引入延迟和查询时间的性能。

## <a name="common-queries"></a>常见查询
常见查询显示在资源专用表和 Azure 诊断表中。

### <a name="top-n10-request-unit-ru-consuming-requests-or-queries-in-a-specific-time-frame"></a>在特定期限内消耗请求或查询的前 N(10) 个请求单位 (RU)

# <a name="resource-specific"></a>[特定于资源](#tab/resource-specific)
   ```Kusto
   let topRequestsByRUcharge = CDBDataPlaneRequests 
   | where TimeGenerated > ago(24h)
   | project  RequestCharge , TimeGenerated, ActivityId;
   CDBMongoRequests
   | project PIICommandText, ActivityId, DatabaseName , CollectionName
   | join kind=inner topRequestsByRUcharge on ActivityId
   | project DatabaseName , CollectionName , PIICommandText , RequestCharge, TimeGenerated
   | order by RequestCharge desc
   | take 10
   ```

# <a name="azure-diagnostics"></a>[Azure 诊断](#tab/azure-diagnostics)
   ```Kusto
   let topRequestsByRUcharge = AzureDiagnostics
   | where Category == "DataPlaneRequests" and TimeGenerated > ago(1h)
   | project  requestCharge_s , TimeGenerated, activityId_g;
   AzureDiagnostics
   | where Category == "MongoRequests"
   | project piiCommandText_s, activityId_g, databaseName_s , collectionName_s
   | join kind=inner topRequestsByRUcharge on activityId_g
   | project databaseName_s , collectionName_s , piiCommandText_s , requestCharge_s, TimeGenerated
   | order by requestCharge_s desc
   | take 10
   ```    
---

### <a name="requests-throttled-statuscode--429-or-16500-in-a-specific-time-window"></a>给定时间范围内限制的请求数（statusCode = 429 或 16500） 

# <a name="resource-specific"></a>[特定于资源](#tab/resource-specific)
   ```Kusto
   let throttledRequests = CDBDataPlaneRequests
   | where StatusCode == "429" or StatusCode == "16500"
    | project  OperationName , TimeGenerated, ActivityId;
   CDBMongoRequests
   | project PIICommandText, ActivityId, DatabaseName , CollectionName
   | join kind=inner throttledRequests on ActivityId
   | project DatabaseName , CollectionName , PIICommandText , OperationName, TimeGenerated
   ```

# <a name="azure-diagnostics"></a>[Azure 诊断](#tab/azure-diagnostics)
   ```Kusto
   let throttledRequests = AzureDiagnostics
   | where Category == "DataPlaneRequests"
   | where statusCode_s == "429" or statusCode_s == "16500" 
   | project  OperationName , TimeGenerated, activityId_g;
   AzureDiagnostics
   | where Category == "MongoRequests"
   | project piiCommandText_s, activityId_g, databaseName_s , collectionName_s
   | join kind=inner throttledRequests on activityId_g
   | project databaseName_s , collectionName_s , piiCommandText_s , OperationName, TimeGenerated
   ```    
---

### <a name="timed-out-requests-statuscode--50-in-a-specific-time-window"></a>给定时间范围内的超时请求数 (statusCode = 50) 

# <a name="resource-specific"></a>[特定于资源](#tab/resource-specific)
   ```Kusto
   let throttledRequests = CDBDataPlaneRequests
   | where StatusCode == "50"
   | project  OperationName , TimeGenerated, ActivityId;
   CDBMongoRequests
   | project PIICommandText, ActivityId, DatabaseName , CollectionName
   | join kind=inner throttledRequests on ActivityId
   | project DatabaseName , CollectionName , PIICommandText , OperationName, TimeGenerated
   ```
# <a name="azure-diagnostics"></a>[Azure 诊断](#tab/azure-diagnostics)
   ```Kusto
   let throttledRequests = AzureDiagnostics
   | where Category == "DataPlaneRequests"
   | where statusCode_s == "50"
   | project  OperationName , TimeGenerated, activityId_g;
   AzureDiagnostics
   | where Category == "MongoRequests"
   | project piiCommandText_s, activityId_g, databaseName_s , collectionName_s
   | join kind=inner throttledRequests on activityId_g
   | project databaseName_s , collectionName_s , piiCommandText_s , OperationName, TimeGenerated
   ```    
---

### <a name="queries-with-large-response-lengths-payload-size-of-the-server-response"></a>响应长度较大的查询（服务器响应的有效负载大小）

# <a name="resource-specific"></a>[特定于资源](#tab/resource-specific)
   ```Kusto
   let operationsbyUserAgent = CDBDataPlaneRequests
   | project OperationName, DurationMs, RequestCharge, ResponseLength, ActivityId;
   CDBMongoRequests
   //specify collection and database
   //| where DatabaseName == "DBNAME" and CollectionName == "COLLECTIONNAME"
   | join kind=inner operationsbyUserAgent on ActivityId
   | summarize max(ResponseLength) by PIICommandText
   | order by max_ResponseLength desc
   ```
# <a name="azure-diagnostics"></a>[Azure 诊断](#tab/azure-diagnostics)
   ```Kusto
   let operationsbyUserAgent = AzureDiagnostics
   | where Category=="DataPlaneRequests"
   | project OperationName, duration_s, requestCharge_s, responseLength_s, activityId_g;
   AzureDiagnostics
   | where Category == "MongoRequests"
   //specify collection and database
   //| where databaseName_s == "DBNAME" and collectionName_s == "COLLECTIONNAME"
   | join kind=inner operationsbyUserAgent on activityId_g
   | summarize max(responseLength_s1) by piiCommandText_s
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
   //| where databaseName_s == "DBNAME" and collectionName_s == "COLLECTIONNAME"
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
   //| where databaseName_s == "DBNAME" and collectionName_s == "COLLECTIONNAME"
   // filter by operation type
   //| where operationType_s == 'Create'
   | summarize sum(todouble(requestCharge_s)) by partitionKey_s, partitionKeyRangeId_s
   | render columnchart  
   ```
---

## <a name="next-steps"></a>后续步骤 
* 有关如何为 Cosmos DB 创建诊断设置的详细信息，请参阅[创建诊断设置](../cosmosdb-monitor-resource-logs.md)一文。
* 若要详细了解如何通过使用 Azure 门户、Azure CLI 或 PowerShell 来创建诊断设置，请参阅[创建诊断设置以在 Azure 中收集平台日志和指标](../../azure-monitor/essentials/diagnostic-settings.md)一文。
