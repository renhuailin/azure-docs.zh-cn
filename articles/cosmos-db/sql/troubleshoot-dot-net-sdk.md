---
title: 诊断和排查在使用 Azure Cosmos DB .NET SDK 时出现的问题
description: 通过客户端日志记录等功能及其他第三方工具来识别、诊断和排查在使用 .NET SDK 时出现的 Azure Cosmos DB 问题。
author: anfeldma-ms
ms.service: cosmos-db
ms.date: 03/05/2021
ms.author: anfeldma
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.custom: devx-track-dotnet
ms.openlocfilehash: 26c16583dbbbc49d2f1be51d0cbf1a6e26e70299
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123113562"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-cosmos-db-net-sdk"></a>诊断和排查在使用 Azure Cosmos DB .NET SDK 时出现的问题
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [Java SDK v4](troubleshoot-java-sdk-v4-sql.md)
> * [Async Java SDK v2](troubleshoot-java-async-sdk.md)
> * [.NET](troubleshoot-dot-net-sdk.md)
> 

本文介绍将 [.NET SDK](sql-api-sdk-dotnet.md) 与 Azure Cosmos DB SQL API 帐户配合使用时的常见问题、解决方法、诊断步骤和工具。
.NET SDK 提供了客户端逻辑表示，以用于访问 Azure Cosmos DB SQL API。 本文介绍了在遇到任何问题时可以提供帮助的工具和方法。

## <a name="checklist-for-troubleshooting-issues"></a>问题排查清单

在将应用程序转移到生产环境之前，请考虑根据以下清单进行检查。 使用该清单有助于防止出现多个常见问题。 出现问题时可以快速诊断：

*    使用最新的 [SDK](sql-api-sdk-dotnet-standard.md)。 不要将预览版 SDK 用于生产。 这样就可以避免遇到已更正的已知问题。
*    查看[性能提示](performance-tips.md)并按照建议的做法进行操作。 这有助于避免缩放、延迟和其他性能问题。
*    启用 SDK 日志记录以帮助排查问题。 启用日志记录可能会影响性能，因此，最好是只在排查问题时才启用日志记录。 可以启用以下日志：
*    使用 Azure 门户[记录指标](../monitor-cosmos-db.md)。 门户指标显示 Azure Cosmos DB 遥测数据，这有助于确定问题是否与 Azure Cosmos DB 相关，或者是否由客户端造成。
*    记录点操作响应中的[诊断字符串](/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring)（在 V2 SDK 中）或[诊断](/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics)（在 V3 SDK 中）。
*    记录所有查询响应中的 [SQL 查询指标](sql-api-query-metrics.md) 
*    遵循针对 [SDK 日志记录]( https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/docs/documentdb-sdk_capture_etl.md)的设置步骤

请查看本文中的[常见问题和解决方法](#common-issues-workarounds)部分。

查看我们积极关注的 [GitHub 问题部分](https://github.com/Azure/azure-cosmos-dotnet-v2/issues)。 检查是否已提交包含解决方法的任何类似问题。 如果找不到解决方法，请提出 GitHub 问题。 对于紧急问题，可以开具支持票证。


## <a name="common-issues-and-workarounds"></a><a name="common-issues-workarounds"></a>常见问题和解决方法

### <a name="general-suggestions"></a>常规建议
* 尽可能在 Azure Cosmos DB 帐户所在的同一个 Azure 区域中运行应用。 
* 由于客户端计算机上的资源不足，你可能会遇到连接/可用性问题。 我们建议监视运行 Azure Cosmos DB 客户端的节点上的 CPU 利用率，如果这些节点的负载较大，请纵向/横向扩展节点。

### <a name="check-the-portal-metrics"></a>检查门户指标
检查[门户指标](../monitor-cosmos-db.md)有助于确定问题是否与客户端相关，或者服务是否有问题。 例如，如果指标中包含较高比率的速率受限请求（HTTP 状态代码 429，表示请求受到限制），请查看[请求速率过大](troubleshoot-request-rate-too-large.md)部分。 

## <a name="retry-logic"></a>重试逻辑<a id="retry-logics"></a>
如果可以在 SDK 中重试，则任何 IO 故障的 Cosmos DB SDK 都将尝试重试失败的操作。 重试任何故障是一种好习惯，特别是处理/重试写入故障必不可少。 由于重试逻辑不断改进，因此建议使用最新的 SDK。

1. SDK 会重试读取和查询 IO 故障，而不会将它们呈现给最终用户。
2. 写入（创建、更新、替换、删除）不是幂等的，因此，SDK 不能总是盲目地重试失败的写入操作。 用户的应用程序逻辑必须能够处理故障并重试。
3. [SDK 可用性疑难解答](troubleshoot-sdk-availability.md)说明了多区域 Cosmos DB 帐户的重试。

### <a name="retry-design"></a>重试设计

应用程序应设计为对任何异常进行重试，除非已经知道重试将无效。 例如，应用程序应重试 408 请求超时，此超时可能是暂时的，因此重试可能会成功。 应用程序不应在 400 秒后重试，这通常意味着必须首先解决请求存在的问题。 400 秒后重试不会解决问题，如果再次重试，将导致相同的失败。 下表显示了已知故障以及要重试的故障。

## <a name="common-error-status-codes"></a>常见错误状态代码 <a id="error-codes"></a>

| 状态代码 | 可重试 | 说明 | 
|----------|-------------|-------------|
| 400 | 否 | 错误的请求（即无效的 json、不正确的标头、标头中的分区键不正确）| 
| 401 | 否 | [未授权](troubleshoot-unauthorized.md) | 
| 403 | 否 | 已禁止 |
| 404 | 否 | [找不到资源](troubleshoot-not-found.md) |
| 408 | 是 | [请求已超时](troubleshoot-dot-net-sdk-request-timeout.md) |
| 409 | 否 | 冲突失败是指为写入操作中的资源提供的 ID 已被现有资源使用。 对资源使用另一个 ID 可解决此问题，因为 ID 在具有相同分区键值的所有文档中必须唯一。 |
| 410 | 是 | 消失异常（不应违反 SLA 的瞬间失败） |
| 412 | 否 | 前提条件失败是操作指定的 eTag 与服务器上提供的版本不同。 这是乐观并发错误。 在读取资源的最新版本并更新请求中的 eTag 后重试该请求。
| 413 | 否 | [请求实体太大](../concepts-limits.md#per-item-limits) |
| 429 | 是 | 可以安全地重试 429。 对于[请求过多](troubleshoot-request-rate-too-large.md)，可以按照此链接来避免这种情况。|
| 449 | 是 | 仅在进行写入操作时才发生的暂时性错误，可安全重试。 这可能指向一个设计问题，即有太多并发操作试图更新 Cosmos DB 中的同一对象。 |
| 500 | 是 | 操作由于意外服务错误而失败。 通过提交 [Azure 支持问题](https://aka.ms/azure-support)联系支持人员。 |
| 503 | 是 | [服务不可用](troubleshoot-service-unavailable.md) | 

### <a name="azure-snat-pat-port-exhaustion"></a><a name="snat"></a>Azure SNAT (PAT) 端口耗尽

如果应用部署在[没有公共 IP 地址的 Azure 虚拟机](../../load-balancer/load-balancer-outbound-connections.md)上，则默认情况下，[Azure SNAT 端口](../../load-balancer/load-balancer-outbound-connections.md#preallocatedports)将与 VM 外部的任何终结点建立连接。 从 VM 到 Azure Cosmos DB 终结点，允许的连接数受 [Azure SNAT 配置](../../load-balancer/load-balancer-outbound-connections.md#preallocatedports)的限制。 这种情况可能会导致连接限制、连接关闭或上述[请求超时](troubleshoot-dot-net-sdk-request-timeout.md)。

 仅当 VM 具有专用 IP 地址且连接到公共 IP 地址时，才会使用 Azure SNAT 端口。 有两种解决方法可以避免 Azure SNAT 限制（前提是已在整个应用程序中使用单个客户端实例）：

* 向 Azure 虚拟机虚拟网络的子网添加 Azure Cosmos DB 服务终结点。 有关详细信息，请参阅 [Azure 虚拟网络服务终结点](../../virtual-network/virtual-network-service-endpoints-overview.md)。 

    启用服务终结点后，不再从公共 IP 向 Azure Cosmos DB 发送请求， 而是发送虚拟网络和子网标识。 如果仅允许公共 IP，则此更改可能会导致防火墙丢失。 如果使用防火墙，则在启用服务终结点后，请使用[虚拟网络 ACL](/previous-versions/azure/virtual-network/virtual-networks-acl) 将子网添加到防火墙。
* [将公共 IP 分配给 Azure VM](../../load-balancer/troubleshoot-outbound-connection.md#assignilpip)。

### <a name="high-network-latency"></a><a name="high-network-latency"></a>高网络延迟
可以使用 V2 SDK 中的[诊断字符串](/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring)或 V3 SDK 中的[诊断](/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics#Microsoft_Azure_Cosmos_ResponseMessage_Diagnostics)来识别高网络延迟。

如果未发生[超时](troubleshoot-dot-net-sdk-request-timeout.md)，则诊断会显示延迟明显较高的单个请求。

# <a name="v3-sdk"></a>[V3 SDK](#tab/diagnostics-v3)

可以调用 `Diagnostics` 属性从任何 `ResponseMessage`、`ItemResponse`、`FeedResponse` 或 `CosmosException` 中获取诊断：

```csharp
ItemResponse<MyItem> response = await container.CreateItemAsync<MyItem>(item);
Console.WriteLine(response.Diagnostics.ToString());
```

例如，诊断中的网络交互是：

```json
{
    "name": "Microsoft.Azure.Documents.ServerStoreModel Transport Request",
    "id": "0e026cca-15d3-4cf6-bb07-48be02e1e82e",
    "component": "Transport",
    "start time": "12: 58: 20: 032",
    "duration in milliseconds": 1638.5957
}
```

其中 `duration in milliseconds` 显示延迟。

# <a name="v2-sdk"></a>[V2 SDK](#tab/diagnostics-v2)

当客户端以[直接模式](sql-sdk-connection-modes.md)配置时，可通过 `RequestDiagnosticsString` 属性获取诊断：

```csharp
ResourceResponse<Document> response = await client.ReadDocumentAsync(documentLink, new RequestOptions() { PartitionKey = new PartitionKey(partitionKey) });
Console.WriteLine(response.RequestDiagnosticsString);
```

延迟将是 `ResponseTime` 与 `RequestStartTime` 之间的差异：

```bash
RequestStartTime: 2020-03-09T22:44:49.5373624Z, RequestEndTime: 2020-03-09T22:44:49.9279906Z,  Number of regions attempted:1
ResponseTime: 2020-03-09T22:44:49.9279906Z, StoreResult: StorePhysicalAddress: rntbd://..., ...
```
--- 

这种延迟的可能原因有多种：

* 应用程序不是在 Azure Cosmos DB 帐户所在的同一区域中运行。
* [PreferredLocations](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations) 或 [ApplicationRegion](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationregion) 配置不正确，尝试连接到的区域不是当前正在运行应用程序的区域。
* 由于流量较大，网络接口可能出现了瓶颈。 如果应用程序在 Azure 虚拟机上运行，则有两种可能的解决方法：
    * 考虑使用[启用了加速网络的虚拟机](../../virtual-network/create-vm-accelerated-networking-powershell.md)。
    * 启用[现有虚拟机上的加速网络](../../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms)。
    * 考虑使用[更高端的虚拟机](../../virtual-machines/sizes.md)。

### <a name="common-query-issues"></a>常见查询问题

[查询指标](sql-api-query-metrics.md)有助于确定查询在何处花费的时间最多。 在查询指标中，可以查看查询在客户端与后端上花费的时间。 详细了解[如何排查查询性能问题](troubleshoot-query-performance.md)。

* 如果后端查询的返回速度较快，并将大量的时间花费在客户端上，请检查计算机上的负载。 可能的原因是资源不足，SDK 正在等待资源可用于处理响应。
* 如果后端查询速度较慢，请尝试[优化查询](troubleshoot-query-performance.md)，并查看当前的[索引策略](../index-overview.md)

    > [!NOTE]
    > 为获得提升的性能，建议使用 Windows 64 位主机处理。 SQL SDK 包含一个本机 ServiceInterop.dll，用于在本地分析和优化查询。 仅 Windows x64 平台支持 ServiceInterop.dll。 对于 ServiceInterop.dll 在其中不可用的 Linux 平台及其他不受支持的平台，将对网关进行额外的网络调用以获取优化的查询。

如果遇到“`Unable to load DLL 'Microsoft.Azure.Cosmos.ServiceInterop.dll' or one of its dependencies:`”错误并且正在使用 Windows，则应升级到最新的 Windows 版本。

## <a name="next-steps"></a>后续步骤

* 了解 [.NET V3](performance-tips-dotnet-sdk-v3-sql.md) 和 [.NET V2](performance-tips.md) 的性能准则
* 了解[基于 Reactor 的 Java SDK](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-pattern-guide.md)

 <!--Anchors-->
[Common issues and workarounds]: #common-issues-workarounds
[Enable client SDK logging]: #logging
[Azure SNAT (PAT) port exhaustion]: #snat
[Production check list]: #production-check-list
