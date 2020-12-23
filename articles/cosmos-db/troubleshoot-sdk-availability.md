---
title: 诊断多区域环境中 Azure Cosmos SDK 的可用性并对其进行故障排除
description: 了解有关在多区域环境中运行时 Azure Cosmos SDK 可用性行为的所有信息。
author: ealsur
ms.service: cosmos-db
ms.date: 10/20/2020
ms.author: maquaran
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: b1c2377ba26b4ca64f5028fb1a51ca4e64f6a67c
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93097883"
---
# <a name="diagnose-and-troubleshoot-the-availability-of-azure-cosmos-sdks-in-multiregional-environments"></a>诊断多区域环境中 Azure Cosmos SDK 的可用性并对其进行故障排除
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

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

如果 **未设置首选区域** ，SDK 客户端默认为主要区域：

|帐户类型 |读取 |写入 |
|------------------------|--|--|
| 单个写入区域 | 主要区域 | 主要区域 |
| 多个写入区域 | 主要区域  | 主要区域  |

> [!NOTE]
> 主要区域是指 [Azure Cosmos 帐户区域列表](distribute-data-globally.md)中的第一个区域

在正常情况下，SDK 客户端将连接到首选区域 (如果将区域首选项设置) 或主要区域 (如果未) 首选项设置，则操作将限制为该区域，除非发生以下任何情况。

在这些情况下，使用 Azure Cosmos SDK 的客户端将公开日志，并在 **操作诊断信息** 中包含重试信息：

* .NET V2 SDK 中有关响应的 RequestDiagnosticsString 属性。
* .NET V3 SDK 中有关响应和异常的 Diagnostics 属性。
* Java V4 SDK 中有关响应和异常的 getDiagnostics() 方法。

在按优先顺序确定下一个区域时，SDK 客户端将使用帐户区域列表，并确定首选区域的优先级 (如果有任何) 。

有关这些事件中的 SLA 保证的详细信息，请参阅[可用性 SLA](high-availability.md#slas-for-availability)。

## <a name="removing-a-region-from-the-account"></a><a id="remove-region"></a>从帐户中删除区域

从 Azure Cosmos 帐户删除区域时，任何主动使用该帐户的 SDK 客户端都将通过后端响应代码检测到区域删除。 然后，客户端将区域终结点标记为不可用。 客户端会重试当前操作，所有将来的操作将按照优先顺序永久路由到下一个区域。

## <a name="adding-a-region-to-an-account"></a>将区域添加到帐户

Azure Cosmos SDK 客户端每隔 5 分钟读取一次帐户配置，并刷新其识别的区域。

如果删除某个区域，然后再将其重新添加到帐户中，并且在 SDK 配置中，添加的区域的区域首选项顺序高于当前连接的区域，则 SDK 将切换回永久使用该区域。 检测到添加的区域后，所有将来的请求都将定向到该区域。

如果将客户端配置为最好连接到 Azure Cosmos 帐户没有的区域，则将忽略首选区域。 如果以后添加该区域，则客户端会检测到该区域，并永久切换到该区域。

## <a name="fail-over-the-write-region-in-a-single-write-region-account"></a><a id="manual-failover-single-region"></a>在单个写入区域帐户中将写入区域故障转移

如果启动当前写入区域的故障转移，则下一个写入请求将失败，并返回一个已知的后端响应。 检测到此响应时，客户端将查询帐户以了解新的写入区域，然后继续重试当前操作，并将所有未来的写入操作永久路由到新区域。

## <a name="regional-outage"></a>区域中断

如果帐户是单个写入区域，并且在写入操作期间发生区域中断，则此行为类似于[手动故障转移](#manual-failover-single-region)。 对于读取请求或多个写入区域帐户，其行为类似于[删除区域](#remove-region)。

## <a name="session-consistency-guarantees"></a>会话一致性保证

使用[会话一致性](consistency-levels.md#guarantees-associated-with-consistency-levels)时，客户端需要保证其自身可以读取自己的写入内容。 在读取区域首选项与写入区域不同的单个写入区域帐户中，可能会出现以下情况：用户发出写入，当从本地区域进行读取时，本地区域尚未接收到数据复制（光速约束）。 在这种情况下，SDK 会检测读取操作的特定故障，并在主要区域重试读取操作，以确保会话一致性。

## <a name="transient-connectivity-issues-on-tcp-protocol"></a>TCP 协议上的暂时性连接问题

在 Azure Cosmos SDK 客户端配置为使用 TCP 协议的情况下，对于给定的请求，可能会出现网络状况暂时影响与特定终结点之间的通信的情况。 这些暂时的网络状况可能会显示为 TCP 超时。 在几秒钟内，客户端将在同一终结点上本地重试请求。

如果用户配置了具有多个区域的首选区域列表，Azure Cosmos 帐户是多个写入区域或单个写入区域，并且操作是一个读取请求，则客户端将在首选项列表的下一个区域中重试该单个操作。

## <a name="next-steps"></a>后续步骤

* 查看[可用性 SLA](high-availability.md#slas-for-availability)。
* 使用最新的 [.NET SDK](sql-api-sdk-dotnet-standard.md)
* 使用最新的 [Java SDK](sql-api-sdk-java-v4.md)
* 使用最新的 [Python SDK](sql-api-sdk-python.md)
* 使用最新的 [Node SDK](sql-api-sdk-node.md)
