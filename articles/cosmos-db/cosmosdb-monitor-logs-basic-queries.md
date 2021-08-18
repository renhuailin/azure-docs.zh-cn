---
title: 使用诊断查询排查问题
titleSuffix: Azure Cosmos DB
description: 了解如何查询诊断日志以对存储在 Azure Cosmos DB 中的数据进行故障排除
author: StefArroyo
services: cosmos-db
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/12/2021
ms.author: esarroyo
ms.openlocfilehash: 769db1b447c6f5ce31a1ed23ddf5d7ef973bcad5
ms.sourcegitcommit: f0168d80eb396ce27032aa02fe9da5a0c10b5af3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2021
ms.locfileid: "112553137"
---
# <a name="troubleshoot-issues-with-diagnostics-queries"></a>使用诊断查询排查问题

本文介绍如何使用发送到“AzureDiagnostics (legacy)”和“Resource-specific (preview)”表的诊断日志编写简单查询，以帮助排查 Azure Cosmos DB 帐户的问题。

就 Azure 诊断表来说，所有数据都写入到一个表中，用户需要指定要查询的类别。

对于特定于资源的表，数据将写入每个资源类别的各个表中（不适用于表 API）。 建议使用此模式，因为它可以大幅简化数据的处理、更好地发现架构、改善引入延迟和查询时间方面的性能。

## <a name="azurediagnostics-queries"></a><a id="azure-diagnostics-queries"></a> AzureDiagnostics 查询

- 如何查询运行时间超过 3 毫秒的操作：

   ```Kusto
   AzureDiagnostics 
   | where toint(duration_s) > 3 and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
   | summarize count() by clientIpAddress_s, TimeGenerated
   ```

- 如何查询正在运行操作的用户代理：

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
   | summarize count() by OperationName, userAgent_s
   ```

- 如何查询长时间运行的操作：

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
   | project TimeGenerated , duration_s 
   | summarize count() by bin(TimeGenerated, 5s)
   | render timechart
   ```
    
- 如何获取分区键统计信息，以评估数据库帐户最大的三个分区之间的偏差：

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics" 
   | project SubscriptionId, regionName_s, databaseName_s, collectionName_s, partitionKey_s, sizeKb_d, ResourceId 
   ```

- 如何获取高开销查询的请求开销？

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" and todouble(requestCharge_s) > 10.0
   | project activityId_g, requestCharge_s
   | join kind= inner (
   AzureDiagnostics
   | where ResourceProvider =="MICROSOFT.DOCUMENTDB" and Category == "QueryRuntimeStatistics"
   | project activityId_g, querytext_s
   ) on $left.activityId_g == $right.activityId_g
   | order by requestCharge_s desc
   | limit 100
   ```

- 如何确定哪些操作消耗的每秒 RU 数最多？

    ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize max(responseLength_s), max(requestLength_s), max(requestCharge_s), count = count() by OperationName, requestResourceType_s, userAgent_s, collectionRid_s, bin(TimeGenerated, 1h)
   ```

- 如何获取与 DataPlaneRequests 和 QueryRunTimeStatistics 中的数据联接时消耗超过 100 RU/秒的所有查询 。

   ```kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" and todouble(requestCharge_s) > 100.0
   | project activityId_g, requestCharge_s
   | join kind= inner (
           AzureDiagnostics
           | where ResourceProvider =="MICROSOFT.DOCUMENTDB" and Category == "QueryRuntimeStatistics"
           | project activityId_g, querytext_s
   ) on $left.activityId_g == $right.activityId_g
   | order by requestCharge_s desc
   | limit 100
   ```

- 如何获取查询的请求费用和执行持续时间？

   ```kusto
   AzureDiagnostics
   | where TimeGenerated >= ago(24hr)
   | where Category == "QueryRuntimeStatistics"
   | join (
   AzureDiagnostics
   | where TimeGenerated >= ago(24hr)
   | where Category == "DataPlaneRequests"
   ) on $left.activityId_g == $right.activityId_g
   | project databasename_s, collectionname_s, OperationName1 , querytext_s,requestCharge_s1, duration_s1, bin(TimeGenerated, 1min)
   ```

- 如何获取不同操作的分布情况？

   ```kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize count = count()  by OperationName, requestResourceType_s, bin(TimeGenerated, 1h) 
   ```

- 分区已使用的最大吞吐量是多少？
   
   ```kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize max(requestCharge_s) by bin(TimeGenerated, 1h), partitionId_g
   ```

- 如何获取分区键的每秒 RU 消耗量信息？

   ```kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | summarize total = sum(todouble(requestCharge_s)) by databaseName_s, collectionName_s, partitionKey_s, TimeGenerated 
   | order by TimeGenerated asc 
   ```

- 如何获取特定分区键的请求开销

   ```kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | where parse_json(partitionKey_s)[0] == "2" 
   ```

- 如何获取在特定时间段每秒消耗了最多 RU 的分区键？

   ```kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | where TimeGenerated >= datetime("11/26/2019, 11:20:00.000 PM") and TimeGenerated <= datetime("11/26/2019, 11:30:00.000 PM") 
   | summarize total = sum(todouble(requestCharge_s)) by databaseName_s, collectionName_s, partitionKey_s 
   | order by total desc
   ```

- 如何获取存储大小大于 8 GB 的分区键的日志？

   ```kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics"
   | where todouble(sizeKb_d) > 800000
   ```

- 如何获取操作的 P99 或 P50 复制延迟、请求费用或响应时间？

   ```kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2d)
   | summarize percentile(todouble(responseLength_s), 50), percentile(todouble(responseLength_s), 99), max(responseLength_s), percentile(todouble(requestCharge_s), 50), percentile(todouble(requestCharge_s), 99), max(requestCharge_s), percentile(todouble(duration_s), 50), percentile(todouble(duration_s), 99), max(duration_s), count() by OperationName, requestResourceType_s, userAgent_s, collectionRid_s, bin(TimeGenerated, 1h)
   ```
 
- 如何获取 ControlPlane 日志？
 
   切记按照[禁用基于键的元数据写访问权限](audit-control-plane-logs.md#disable-key-based-metadata-write-access)一文中所述打开标志，并通过使用 Azure PowerShell、Azure CLI 或 Azure 资源管理器执行操作。
 
   ```kusto  
   AzureDiagnostics 
   | where Category =="ControlPlaneRequests"
   | summarize by OperationName 
   ```


## <a name="resource-specific-queries"></a><a id="resource-specific-queries"></a> 特定于资源的查询

- 如何查询运行时间超过 3 毫秒的操作：

    ```kusto
    CDBDataPlaneRequests 
    | where toint(DurationMs) > 3
    | summarize count() by ClientIpAddress, TimeGenerated
    ```

- 如何查询正在运行操作的用户代理：

   ```kusto
    CDBDataPlaneRequests
    | summarize count() by OperationName, UserAgent
   ```

- 如何查询长时间运行的操作：

   ```kusto
    CDBDataPlaneRequests
    | project TimeGenerated , DurationMs 
    | summarize count() by bin(TimeGenerated, 5s)
    | render timechart
   ```
    
- 如何获取分区键统计信息，以评估数据库帐户最大的三个分区之间的偏差：

   ```kusto
    CDBPartitionKeyStatistics
    | project RegionName, DatabaseName, CollectionName, PartitionKey, SizeKb
   ```

- 如何获取高开销查询的请求开销？

   ```kusto
    CDBDataPlaneRequests
    | where todouble(RequestCharge) > 10.0
    | project ActivityId, RequestCharge
    | join kind= inner (
    CDBQueryRuntimeStatistics
    | project ActivityId, QueryText
    ) on $left.ActivityId == $right.ActivityId
    | order by RequestCharge desc
    | limit 100
   ```

- 如何确定哪些操作消耗的每秒 RU 数最多？

    ```kusto
    CDBDataPlaneRequests
    | where TimeGenerated >= ago(2h) 
    | summarize max(ResponseLength), max(RequestLength), max(RequestCharge), count = count() by OperationName, RequestResourceType, UserAgent, CollectionName, bin(TimeGenerated, 1h)
   ```

- 如何获取与 DataPlaneRequests 和 QueryRunTimeStatistics 中的数据联接时消耗超过 100 RU/秒的所有查询 。

   ```kusto
    CDBDataPlaneRequests
    | where todouble(RequestCharge) > 100.0
    | project ActivityId, RequestCharge
    | join kind= inner (
    CDBQueryRuntimeStatistics
    | project ActivityId, QueryText
    ) on $left.ActivityId == $right.ActivityId
    | order by RequestCharge desc
    | limit 100
   ```

- 如何获取查询的请求费用和执行持续时间？

   ```kusto
    CDBQueryRuntimeStatistics
    | join kind= inner (
    CDBDataPlaneRequests
    ) on $left.ActivityId == $right.ActivityId
    | project DatabaseName, CollectionName, OperationName , QueryText, RequestCharge, DurationMs, bin(TimeGenerated, 1min)
   ```

- 如何获取不同操作的分布情况？

   ```kusto
    CDBDataPlaneRequests
    | where TimeGenerated >= ago(2h) 
    | summarize count = count()  by OperationName, RequestResourceType, bin(TimeGenerated, 1h)
   ```

- 分区已使用的最大吞吐量是多少？

   ```kusto
   CDBDataPlaneRequests
   | where TimeGenerated >= ago(2h) 
   | summarize max(RequestCharge) by bin(TimeGenerated, 1h), PartitionId
   ```

- 如何获取分区键的每秒 RU 消耗量信息？

   ```kusto
   CDBPartitionKeyRUConsumption 
   | summarize total = sum(todouble(RequestCharge)) by DatabaseName, CollectionName, PartitionKey, TimeGenerated 
   | order by TimeGenerated asc 
   ```

- 如何获取特定分区键的请求开销？

   ```kusto
   CDBPartitionKeyRUConsumption  
   | where parse_json(PartitionKey)[0] == "2" 
   ```

- 如何获取在特定时间段每秒消耗了最多 RU 的分区键？ 

   ```kusto
   CDBPartitionKeyRUConsumption
   | where TimeGenerated >= datetime("02/12/2021, 11:20:00.000 PM") and TimeGenerated <= datetime("05/12/2021, 11:30:00.000 PM") 
   | summarize total = sum(todouble(RequestCharge)) by DatabaseName, CollectionName, PartitionKey 
   | order by total desc
   ```

- 如何获取存储大小大于 8 GB 的分区键的日志？

   ```kusto
   CDBPartitionKeyStatistics
   | where todouble(SizeKb) > 800000
   ``` 

- 如何获取操作的 P99 或 P50 复制延迟、请求费用或响应时间？

   ```kusto
   CDBDataPlaneRequests
   | where TimeGenerated >= ago(2d)
   | summarize percentile(todouble(ResponseLength), 50), percentile(todouble(ResponseLength), 99), max(ResponseLength), percentile(todouble(RequestCharge), 50), percentile(todouble(RequestCharge), 99), max(RequestCharge), percentile(todouble(DurationMs), 50), percentile(todouble(DurationMs), 99), max(DurationMs),count() by OperationName, RequestResourceType, UserAgent, CollectionName, bin(TimeGenerated, 1h)
   ```
 
- 如何获取 ControlPlane 日志？
 
   切记按照[禁用基于键的元数据写访问权限](audit-control-plane-logs.md#disable-key-based-metadata-write-access)一文中所述打开标志，并通过使用 Azure PowerShell、Azure CLI 或 Azure 资源管理器执行操作。
 
   ```kusto  
   CDBControlPlaneRequests
   | summarize by OperationName 
   ```

## <a name="next-steps"></a>后续步骤 
* 若要详细了解如何为 Cosmos DB 创建诊断设置，请参阅[创建诊断设置](cosmosdb-monitor-resource-logs.md)一文。

* 若要详细了解如何使用 Azure 门户、CLI 或 PowerShell 创建诊断设置，请参阅[创建诊断设置以在 Azure 中收集平台日志和指标](../azure-monitor/essentials/diagnostic-settings.md)一文。