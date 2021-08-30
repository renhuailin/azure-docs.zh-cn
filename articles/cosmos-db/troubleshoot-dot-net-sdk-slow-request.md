---
title: 使用 .NET SDK 对 Azure Cosmos DB 请求缓慢进行故障排除
description: 了解如何在使用 Azure Cosmos DB .NET SDK 时诊断和修复请求缓慢问题。
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 06/15/2021
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: abee066553c4ee338000efd39ecd3b0bfd11d171
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121745448"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-net-sdk-slow-requests"></a>诊断和排查 Azure Cosmos DB .NET SDK 请求缓慢问题
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB 请求缓慢的原因有多种，例如请求限制或应用程序的设计方式。 本文介绍此问题的不同根本原因。 

## <a name="request-rate-too-large-429-throttles"></a>请求速率过大（限制 429）

请求限制是请求缓慢的最常见原因。 如果请求超过为数据库或容器分配的 RU，Azure Cosmos DB 将限制请求。 SDK 具有重试这些请求的内置逻辑。 [请求速率过大](troubleshoot-request-rate-too-large.md#how-to-investigate)故障排除一文介绍了如何检查请求是否受到限制，以及如何缩放帐户，避免将来出现这些问题。

## <a name="application-design"></a>应用程序设计

如果应用程序不遵循 SDK 最佳做法，则可能会导致不同的问题，从而使请求缓慢或失败。 请遵循 [.NET SDK 最佳做法](performance-tips-dotnet-sdk-v3-sql.md)获得最佳性能。

开发应用程序时，请考虑以下事项：
* 应用程序应与 Azure Cosmos DB 帐户在同一区域。
* SDK 实例的单一实例。 SDK 具有多个必须初始化的缓存，这可能会减慢前几个请求的速度。 
* 使用 Direct + TCP 连接模式
* 避免 CPU 使用率过高。 请务必查看“最大 CPU 使用率”而不是“平均值”，这是大多数日志系统的默认值。 任何超过 40% 以上 CPU 占用的内容都可能会增加延迟。


## <a name="capture-the-diagnostics"></a><a name="capture-diagnostics"></a>捕获诊断

SDK 中的所有响应（包括 `CosmosException`），都具有诊断属性。 此属性记录与单一请求相关的所有信息，包括是否发生重试或任何瞬时故障。 

诊断以字符串形式返回。 字符串会随每个版本而不断变化并进行改进，以更好地对不同场景进行故障排除。 对于 SDK 的每个版本，字符串对格式设置都有中断性变更。 请勿分析字符串以避免发生中断性变更。 以下代码示例展示了如何使用 .NET SDK 读取诊断日志：

```c#
try
{
    ItemResponse<Book> response = await this.Container.CreateItemAsync<Book>(item: testItem);
    if (response.Diagnostics.GetClientElapsedTime() > ConfigurableSlowRequestTimeSpan)
    {
        // Log the diagnostics and add any additional info necessary to correlate to other logs 
        Console.Write(response.Diagnostics.ToString());
    }
}catch(CosmosException cosmosException){
    // Log the full exception including the stack trace 
    Console.Write(cosmosException.ToString());
    // The Diagnostics can be logged separately if required.
    Console.Write(cosmosException.Diagnostics.ToString());
}

ResponseMessage response = await this.Container.CreateItemStreamAsync(partitionKey, stream);
if (response.Diagnostics.GetClientElapsedTime() > ConfigurableSlowRequestTimeSpan || IsFailureStatusCode(response.StatusCode))
{
    // Log the diagnostics and add any additional info necessary to correlate to other logs 
    Console.Write(response.Diagnostics.ToString());
}
```


## <a name="diagnostics-in-version-319-and-higher"></a>3\.19 及更高版本的诊断
JSON 结构在每个版本的 SDK 中都有中断性变更。 这使得分析不安全。 JSON 表示通过 SDK 的请求的树结构。 这涵盖一些需要关注的关键内容：

### <a name="cpu-history"></a><a name="cpu-history"></a>CPU 历史记录
CPU 使用率过高是请求缓慢的最常见原因。 为实现最佳延迟，CPU 利用率应大约为 40%。 将时间间隔设为 10 秒来监视最大 CPU 利用率（而不是平均利用率）。 CPU 峰值在跨分区查询中更常见，其中请求可能为单一查询执行多个连接。

如果错误包含 `TransportException` 信息，它可能还包含 `CPU History`：

```
CPU history: 
(2020-08-28T00:40:09.1769900Z 0.114), 
(2020-08-28T00:40:19.1763818Z 1.732), 
(2020-08-28T00:40:29.1759235Z 0.000), 
(2020-08-28T00:40:39.1763208Z 0.063), 
(2020-08-28T00:40:49.1767057Z 0.648), 
(2020-08-28T00:40:59.1689401Z 0.137), 
CPU count: 8)
```

* 如果 CPU 使用率超过 70%，则超时可能是由于 CPU 耗尽导致的。 在这种情况下，解决方法是调查 CPU 使用率较高的源并使其减少，或将计算机扩展到更大的资源大小。
* 如果 CPU 度量不是每 10 秒进行一次，则间隔或测量时间指示两次度量之间的时间更长。 在这种情况下，原因是线程不足。 解决方法是调查线程不足（可能锁定的线程）的源，或将计算机扩展到更大的资源大小。

#### <a name="solution"></a>解决方案：
应纵向扩展或横向扩展使用 SDK 的客户端应用程序。


### <a name="httpresponsestats"></a><a name="httpResponseStats"></a>HttpResponseStats
HttpResponseStats 是发送到[网关](sql-sdk-connection-modes.md)的请求。 即使在直接模式下，SDK 也会从网关获取所有元数据信息。

如果请求速度缓慢，请首先验证上述所有建议是否都没有产生结果。

如果仍然较慢，则不同模式会指向不同的问题：

单一请求的单一存储结果

| 请求数量 | 方案 | 说明 | 
|----------|-------------|-------------|
| 单一到全部 | 请求超时或 HttpRequestExceptions | 指向 [SNAT 端口耗尽](troubleshoot-dot-net-sdk.md#snat)或计算机上缺少资源，无法实时处理请求。 |
| 单一或一小部分（不违反 SLA） | 全部 | 单一或一小部分慢速请求可能是由几个不同的瞬时问题引起的，并且是可以预料到的。 | 
| 全部 | 全部 | 指向基础结构或网络的问题。 |
| 违反 SLA | 没有删除对应用程序和 SLA 的更改 | 指向 Azure Cosmos DB 服务的问题。 |

```json
"HttpResponseStats": [
    {
        "StartTimeUTC": "2021-06-15T13:53:09.7961124Z",
        "EndTimeUTC": "2021-06-15T13:53:09.7961127Z",
        "RequestUri": "https://127.0.0.1:8081/dbs/347a8e44-a550-493e-88ee-29a19c070ecc/colls/4f72e752-fa91-455a-82c1-bf253a5a3c4e",
        "ResourceType": "Collection",
        "HttpMethod": "GET",
        "ActivityId": "e16e98ec-f2e3-430c-b9e9-7d99e58a4f72",
        "StatusCode": "OK"
    }
]
```

### <a name="storeresult"></a><a name="storeResult"></a>StoreResult
StoreResult 表示使用 TCP 协议的直接模式对 Azure Cosmos DB 的单一请求。

如果仍然较慢，则不同模式会指向不同的问题：

单一请求的单一存储结果

| 请求数量 | 方案 | 说明 | 
|----------|-------------|-------------|
| 单一到全部 | StoreResult 包含 TransportException | 指向 [SNAT 端口耗尽](troubleshoot-dot-net-sdk.md#snat)或计算机上缺少资源，无法实时处理请求。 |
| 单一或一小部分（不违反 SLA） | 全部 | 单一或一小部分慢速请求可能是由几个不同的瞬时问题引起的，并且是可以预料到的。 | 
| 全部 | 全部 | 基础结构或网络存在问题。 |
| 违反 SLA | 请求包含多个失败错误代码，如 410 和 IsValid 为 true | 指向 Cosmos DB 服务的问题 |
| 违反 SLA | 请求包含多个失败错误代码，如 410 和 IsValid 为 false | 指向计算机的问题 |
| 违反 SLA | StorePhysicalAddress 相同，没有失败状态代码 | 可能是 Cosmos DB 服务的问题 |
| 违反 SLA | StorePhysicalAddress 具有相同的分区 ID，但副本 ID 不同，没有失败状态代码 | 可能是 Cosmos DB 服务的问题 |
| 违反 SLA | StorePhysicalAddress 是随机的，没有失败状态代码 | 指向计算机的问题 |

单一请求的多个 StoreResult：

* 强过期一致性和有限过期一致性将始终具有至少两个存储结果
* 检查每个 StoreResult 的状态代码。 SDK 会自动重试多个不同的[瞬时故障](troubleshoot-dot-net-sdk-request-timeout.md)。 SDK 会不断改进，以应对更多场景。 

### <a name="rntbdrequeststats"></a><a name="rntbdRequestStats"></a>RntbdRequestStats 
显示传输层中发送和接收请求在不同阶段消耗的时间。

* ChannelAcquisitionStarted：获取或创建新连接的时间。 可以为许多不同的区域创建新连接。 例如，连接意外关闭，或者通过现有连接发送的请求过多，因此有必要创建新连接。 
* 管道时间很长，可能指向大型请求。
* 传输时间很长，这会导致出现网络问题。 将此数字与 `BELatencyInMs` 进行比较。 如果 BELatencyInMs 较小，则时间消耗在网络上，而不是在 Azure Cosmos DB 服务上。
* 接收时间很长，这表明存在线程饥饿问题。 这是获得响应和返回结果之间的时间。

```json
"StoreResult": {
    "ActivityId": "a3d325c1-f4e9-405b-820c-bab4d329ee4c",
    "StatusCode": "Created",
    "SubStatusCode": "Unknown",
    "LSN": 1766,
    "PartitionKeyRangeId": "0",
    "GlobalCommittedLSN": -1,
    "ItemLSN": -1,
    "UsingLocalLSN": false,
    "QuorumAckedLSN": 1765,
    "SessionToken": "-1#1766",
    "CurrentWriteQuorum": 1,
    "CurrentReplicaSetSize": 1,
    "NumberOfReadRegions": 0,
    "IsClientCpuOverloaded": false,
    "IsValid": true,
    "StorePhysicalAddress": "rntbd://127.0.0.1:10253/apps/DocDbApp/services/DocDbServer92/partitions/a4cb49a8-38c8-11e6-8106-8cdcd42c33be/replicas/1p/",
    "RequestCharge": 11.05,
    "BELatencyInMs": "7.954",
    "RntbdRequestStats": [
        {
            "EventName": "Created",
            "StartTime": "2021-06-15T13:53:10.1302477Z",
            "DurationInMicroSec": "6383"
        },
        {
            "EventName": "ChannelAcquisitionStarted",
            "StartTime": "2021-06-15T13:53:10.1366314Z",
            "DurationInMicroSec": "96511"
        },
        {
            "EventName": "Pipelined",
            "StartTime": "2021-06-15T13:53:10.2331431Z",
            "DurationInMicroSec": "50834"
        },
        {
            "EventName": "Transit Time",
            "StartTime": "2021-06-15T13:53:10.2839774Z",
            "DurationInMicroSec": "17677"
        },
        {
            "EventName": "Received",
            "StartTime": "2021-06-15T13:53:10.3016546Z",
            "DurationInMicroSec": "7079"
        },
        {
            "EventName": "Completed",
            "StartTime": "2021-06-15T13:53:10.3087338Z",
            "DurationInMicroSec": "0"
        }
    ],
    "TransportException": null
}
```

### <a name="failure-rate-violates-the-azure-cosmos-db-sla"></a>失败率与 Azure Cosmos DB SLA 不符
请联系 [Azure 支持部门](https://aka.ms/azure-support)。

## <a name="next-steps"></a>后续步骤
* [诊断和排查](troubleshoot-dot-net-sdk.md)在使用 Azure Cosmos DB .NET SDK 时遇到的问题。
* 了解 [.NET v3](performance-tips-dotnet-sdk-v3-sql.md) 和 [.NET v2](performance-tips.md) 的性能准则。
