---
title: 使用针对 Gremlin API 的高级诊断查询排查问题
titleSuffix: Azure Cosmos DB
description: 了解如何查询诊断日志，以排除 Azure Cosmos DB for Gremlin API 中存储的数据的问题
author: StefArroyo
services: cosmos-db
ms.service: cosmos-db
ms.topic: how-to
ms.date: 06/12/2021
ms.author: esarroyo
ms.openlocfilehash: fe7739e338b83a249172fe68a4c8125dfad6bbc5
ms.sourcegitcommit: f0168d80eb396ce27032aa02fe9da5a0c10b5af3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2021
ms.locfileid: "112560897"
---
# <a name="troubleshoot-issues-with-advanced-diagnostics-queries-for-gremlin-api"></a>使用针对 Gremlin API 的高级诊断查询排查问题

[!INCLUDE[appliesto-all-apis-except-table](includes/appliesto-all-apis-except-table.md)]

> [!div class="op_single_selector"]
> * [SQL（核心）API](cosmos-db-advanced-queries.md)
> * [MongoDB API](queries-mongo.md)
> * [Cassandra API](queries-cassandra.md)
> * [Gremlin API](queries-gremlin.md)
>

本文介绍如何使用发送到“AzureDiagnostics (legacy)”和“Resource-specific (preview)”表的诊断日志编写更高级的查询，以帮助排查 Azure Cosmos DB 帐户的问题。

就 Azure 诊断表来说，所有数据都写入到一个表中，用户需要指定要查询的类别。 若要查看请求的全文查询，请[按照本文](cosmosdb-monitor-resource-logs.md#full-text-query)进行操作，了解如何启用此功能。

对于[特定于资源的表](cosmosdb-monitor-resource-logs.md#create-setting-portal)，数据将写入每个资源类别的各个表中。 建议使用此模式，因为它可以大幅简化数据的处理、更好地发现架构、改善引入延迟和查询时间方面的性能。

## <a name="common-queries"></a>常见查询

- 在给定时间范围内使用请求/查询的前 N (10) 个查询

# <a name="resource-specific"></a>[特定于资源](#tab/resource-specific)

   ```Kusto
   let topRequestsByRUcharge = CDBDataPlaneRequests 
   | where TimeGenerated > ago(24h)
   | project  RequestCharge , TimeGenerated, ActivityId;
   CDBGremlinRequests
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
   | where Category == "GremlinRequests"
   | project piiCommandText_s, activityId_g, databasename_s , collectionname_s
   | join kind=inner topRequestsByRUcharge on activityId_g
   | project databasename_s , collectionname_s , piiCommandText_s , requestCharge_s, TimeGenerated
   | order by requestCharge_s desc
   | take 10
   ```    
---

- 在给定时间范围内限制的请求数 (statusCode = 429) 

# <a name="resource-specific"></a>[特定于资源](#tab/resource-specific)
   ```Kusto
   let throttledRequests = CDBDataPlaneRequests
   | where StatusCode == "429"
   | project  OperationName , TimeGenerated, ActivityId;
   CDBGremlinRequests
   | project PIICommandText, ActivityId, DatabaseName , CollectionName
   | join kind=inner throttledRequests on ActivityId
   | project DatabaseName , CollectionName , PIICommandText , OperationName, TimeGenerated
   ```
# <a name="azure-diagnostics"></a>[Azure 诊断](#tab/azure-diagnostics)
   ```Kusto
   let throttledRequests = AzureDiagnostics
   | where Category == "DataPlaneRequests"
   | where statusCode_s == "429"
   | project  OperationName , TimeGenerated, activityId_g;
   AzureDiagnostics
   | where Category == "GremlinRequests"
   | project piiCommandText_s, activityId_g, databasename_s , collectionname_s
   | join kind=inner throttledRequests on activityId_g
   | project databasename_s , collectionname_s , piiCommandText_s , OperationName, TimeGenerated
   ```    
---

- 响应长度较大的查询（服务器响应的有效负载大小）

# <a name="resource-specific"></a>[特定于资源](#tab/resource-specific)
   ```Kusto
   let operationsbyUserAgent = CDBDataPlaneRequests
   | project OperationName, DurationMs, RequestCharge, ResponseLength, ActivityId;
   CDBGremlinRequests
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
   | where Category == "GremlinRequests"
   //| where databasename_s == "DBNAME" and collectioname_s == "COLLECTIONNAME"
   | join kind=inner operationsbyUserAgent on activityId_g
   | summarize max(responseLength_s1) by piiCommandText_s
   | order by max_responseLength_s1 desc
   ```    
---

- 按物理分区划分的 RU 使用量（跨副本集中的所有副本）

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

- 按逻辑分区划分的 RU 使用量（跨副本集中的所有副本）

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
* 若要详细了解如何为 Cosmos DB 创建诊断设置，请参阅[创建诊断设置](cosmosdb-monitor-resource-logs.md)一文。

* 若要详细了解如何使用 Azure 门户、CLI 或 PowerShell 创建诊断设置，请参阅[创建诊断设置以在 Azure 中收集平台日志和指标](../azure-monitor/essentials/diagnostic-settings.md)一文。