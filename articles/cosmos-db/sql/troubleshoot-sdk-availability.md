---
title: 诊断多区域环境中 Azure Cosmos SDK 的可用性并对其进行故障排除
description: 了解有关在多区域环境中运行时 Azure Cosmos SDK 可用性行为的所有信息。
author: ealsur
ms.service: cosmos-db
ms.date: 02/18/2021
ms.author: maquaran
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 55912ee592ea04ec8771cf833dd92874d6d2c505
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123113541"
---
# <a name="diagnose-and-troubleshoot-the-availability-of-azure-cosmos-sdks-in-multiregional-environments"></a>诊断多区域环境中 Azure Cosmos SDK 的可用性并对其进行故障排除
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

本文介绍特定区域出现连接性问题或发生区域故障转移时，最新版本的 Azure Cosmos SDK 的行为。

所有 Azure Cosmos SDK 都提供了用于自定义区域首选项的选项。 在不同的 SDK 中使用以下属性：

* .NET V2 SDK 中的 [ConnectionPolicy.PreferredLocations](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations) 属性。
* .NET V3 SDK 中的 [CosmosClientOptions.ApplicationRegion](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationregion) 或 [CosmosClientOptions.ApplicationPreferredRegions](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationpreferredregions) 属性。
* Java V4 SDK 中的 [CosmosClientBuilder.preferredRegions](/java/api/com.azure.cosmos.cosmosclientbuilder.preferredregions) 方法。
* Python SDK 中的 [CosmosClient.preferred_locations](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient) 参数。
* JS SDK 中的 [CosmosClientOptions.ConnectionPolicy.preferredLocations](/javascript/api/@azure/cosmos/connectionpolicy#preferredlocations) 参数。

设置区域首选项时，客户端将连接到下表中提到的区域：

|帐户类型 |读取 |写入 |
|------------------------|--|--|
| 单个写入区域 | 首选区域 | 主要区域  |
| 多个写入区域 | 首选区域 | 首选区域  |

如果不设置首选区域，则 SDK 客户端将默认为主要区域：

|帐户类型 |读取 |写入 |
|------------------------|--|--|
| 单个写入区域 | 主要区域 | 主要区域 |
| 多个写入区域 | 主要区域  | 主要区域  |

> [!NOTE]
> 主要区域是指 [Azure Cosmos 帐户区域列表](../distribute-data-globally.md)中的第一个区域。
> 如果指定为区域首选项的值与任何现有 Azure 区域都不匹配，则将忽略这些值。 如果这些值与某个现有区域匹配，但未将帐户复制到该区域，则客户端将会连接到下一个匹配的首选区域或者将会连接到主区域。

> [!WARNING]
> 本文档中描述的故障转移和可用性逻辑可以在客户端配置上禁用，但不建议这样做，除非用户应用程序要自己处理可用性错误。 可以通过以下方式来实现此目的：
>
> * 将 .NET V2 SDK 中的 [ConnectionPolicy.EnableEndpointRediscovery](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.enableendpointdiscovery) 属性设置为 false。
> * 将 .NET V3 SDK 中的 [CosmosClientOptions.LimitToEndpoint](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.limittoendpoint) 属性设置为 true。
> * 将 Java V4 SDK 中的 [CosmosClientBuilder.endpointDiscoveryEnabled](/java/api/com.azure.cosmos.cosmosclientbuilder.endpointdiscoveryenabled) 方法设置为 false。
> * 将 Python SDK 中的 [CosmosClient.enable_endpoint_discovery](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient) 参数设置为 false。
> * 将 JS SDK 中的 [CosmosClientOptions.ConnectionPolicy.enableEndpointDiscovery](/javascript/api/@azure/cosmos/connectionpolicy#enableEndpointDiscovery) 参数设置为 false。

通常情况下，SDK 客户端将连接到首选区域（如果设置了区域首选项）或主要区域（如果未设置首选项），并且操作将限于该区域，除非出现以下任何情况。

在这些情况下，使用 Azure Cosmos SDK 的客户端都会公开日志，并会将重试信息作为操作诊断信息的一部分包括在内：

* .NET V2 SDK 中有关响应的 RequestDiagnosticsString 属性。
* .NET V3 SDK 中有关响应和异常的 Diagnostics 属性。
* Java V4 SDK 中有关响应和异常的 getDiagnostics() 方法。

按优先级顺序确定下一个区域时，SDK 客户端将使用帐户区域列表，对首选区域（如果有）进行优先级排序。

有关这些事件中的 SLA 保证的详细信息，请参阅[可用性 SLA](../high-availability.md#slas-for-availability)。

## <a name="removing-a-region-from-the-account"></a><a id="remove-region"></a>从帐户中删除区域

从 Azure Cosmos 帐户删除区域时，任何主动使用该帐户的 SDK 客户端都将通过后端响应代码检测到区域删除。 然后，客户端将区域终结点标记为不可用。 客户端会重试当前操作，所有将来的操作将按照优先顺序永久路由到下一个区域。 如果首选项列表仅有一个条目（或为空），但该帐户还有其他可用区域，则该帐户将会路由到帐户列表中的下一个区域。

## <a name="adding-a-region-to-an-account"></a>将区域添加到帐户

Azure Cosmos SDK 客户端每隔 5 分钟读取一次帐户配置，并刷新其识别的区域。

如果删除某个区域，然后再将其重新添加到帐户中，并且在 SDK 配置中，添加的区域的区域首选项顺序高于当前连接的区域，则 SDK 将切换回永久使用该区域。 检测到添加的区域后，所有将来的请求都将定向到该区域。

如果将客户端配置为最好连接到 Azure Cosmos 帐户没有的区域，则将忽略首选区域。 如果以后添加该区域，则客户端会检测到该区域，并永久切换到该区域。

## <a name="fail-over-the-write-region-in-a-single-write-region-account"></a><a id="manual-failover-single-region"></a>对单写入区域帐户中的写入区域进行故障转移

如果启动当前写入区域的故障转移，则下一个写入请求将失败，并返回一个已知的后端响应。 检测到此响应时，客户端将查询帐户以了解新的写入区域，然后继续重试当前操作，并将所有未来的写入操作永久路由到新区域。

## <a name="regional-outage"></a>区域中断

如果帐户是单个写入区域，并且在写入操作期间发生区域中断，则此行为类似于[手动故障转移](#manual-failover-single-region)。 对于读取请求或多个写入区域帐户，其行为类似于[删除区域](#remove-region)。

## <a name="session-consistency-guarantees"></a>会话一致性保证

使用[会话一致性](../consistency-levels.md#guarantees-associated-with-consistency-levels)时，客户端需要保证其自身可以读取自己的写入内容。 在读取区域首选项与写入区域不同的单个写入区域帐户中，可能会出现以下情况：用户发出写入，当从本地区域进行读取时，本地区域尚未接收到数据复制（光速约束）。 在这种情况下，SDK 会检测到读取操作中的特定故障，并在主要区域重试读取操作，以确保会话一致性。

## <a name="transient-connectivity-issues-on-tcp-protocol"></a>TCP 协议上的暂时性连接问题

在 Azure Cosmos SDK 客户端配置为使用 TCP 协议的情况下，对于给定的请求，可能会出现网络状况暂时影响与特定终结点之间的通信的情况。 这些临时网络条件可能会表现为 TCP 超时和服务不可用 (HTTP 503) 错误。 客户端将在同一终结点上以本地方式重试请求几秒钟，然后呈现错误。

如果用户配置了具有多个区域的首选区域列表，Azure Cosmos 帐户是多个写入区域或单个写入区域，并且操作是一个读取请求，则客户端将会在首选项列表的下一个区域中检测到本地失败，并重试该单个操作。

## <a name="next-steps"></a>后续步骤

* 查看[可用性 SLA](../high-availability.md#slas-for-availability)。
* 使用最新的 [.NET SDK](sql-api-sdk-dotnet-standard.md)
* 使用最新的 [Java SDK](sql-api-sdk-java-v4.md)
* 使用最新的 [Python SDK](sql-api-sdk-python.md)
* 使用最新的 [Node SDK](sql-api-sdk-node.md)
