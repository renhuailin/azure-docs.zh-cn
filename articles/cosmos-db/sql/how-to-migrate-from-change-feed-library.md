---
title: 从更改源处理器库迁移到 Azure Cosmos DB .NET V3 SDK
description: 了解如何使用更改源处理器库将应用程序迁移到 Azure Cosmos DB SDK V3
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 09/13/2021
ms.author: maquaran
ms.custom: devx-track-dotnet
ms.openlocfilehash: ad060c3fce28ef70137e0f25e09a1e4ea5fb9a09
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128561471"
---
# <a name="migrate-from-the-change-feed-processor-library-to-the-azure-cosmos-db-net-v3-sdk"></a>从更改源处理器库迁移到 Azure Cosmos DB .NET V3 SDK
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

本文介绍了将使用[更改源处理器库](https://github.com/Azure/azure-documentdb-changefeedprocessor-dotnet)的现有应用程序代码迁移到 .NET SDK 最新版本（也称为 .NET V3 SDK）中的[更改源](../change-feed.md)功能所需的步骤。

## <a name="required-code-changes"></a>所需的代码更改

.NET V3 SDK 有几项中断性变更，以下是迁移应用程序的关键步骤：

1. 将 `DocumentCollectionInfo` 实例转换为受监视容器和租约容器的 `Container` 引用。
1. 使用 `WithProcessorOptions` 的自定义应更新为对时间间隔使用 `WithLeaseConfiguration` 和 `WithPollInterval`、对 [开始时间](./change-feed-processor.md#starting-time)使用 `WithStartTime` 以及使用 `WithMaxItems` 来定义最大项计数。
1. 将 `GetChangeFeedProcessorBuilder` 上的 `processorName` 设置为与 `ChangeFeedProcessorOptions.LeasePrefix`上配置的值匹配，否则使用 `string.Empty`。
1. 更改将不再作为 `IReadOnlyList<Document>` 传递，而是作为 `IReadOnlyCollection<T>`，其中 `T` 是需要定义的类型，任何基项类都不再存在。
1. 若要处理更改，不再需要实现 `IChangeFeedObserver`，而是需要[定义委托](change-feed-processor.md#implementing-the-change-feed-processor)。 委托可以是静态函数，或者，如果需要跨执行维护状态，还可以创建自己的类并传递实例方法作为委托。

例如，如果用于生成更改源处理器的原始代码如下所示：

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=ChangeFeedProcessorLibrary)]

迁移的代码将如下所示：

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=ChangeFeedProcessorMigrated)]

对于委托，可以使用静态方法来接收事件。 如果使用的是 `IChangeFeedObserverContext` 中的信息，可以迁移以使用 `ChangeFeedProcessorContext`：

* 可以使用 `ChangeFeedProcessorContext.LeaseToken`，而不是使用 `IChangeFeedObserverContext.PartitionKeyRangeId`
* 可以使用 `ChangeFeedProcessorContext.Headers`，而不是使用 `IChangeFeedObserverContext.FeedResponse`
* `ChangeFeedProcessorContext.Diagnostics` 包含有关故障排除的请求延迟的详细信息

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=Delegate)]

## <a name="state-and-lease-container"></a>状态和租约容器

与更改源处理器库类似，.NET V3 SDK 中的更改源功能使用[租约容器](change-feed-processor.md#components-of-the-change-feed-processor)来存储状态。 但是，架构是不同的。

SDK V3 更改源处理器将在首次执行迁移的应用程序代码时自动检测任何旧的库状态并将其迁移到新的架构。 

可以使用旧代码安全地停止应用程序，将代码迁移到新版本，启动已迁移的应用程序，而在应用程序停止时所发生的任何更改都将由新版本选取并处理。

## <a name="additional-resources"></a>其他资源

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [GitHub 上的用法示例](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [GitHub 上的其他示例](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>后续步骤

现在，可以通过以下文章继续详细了解更改源处理器：

* [更改源处理器概述](change-feed-processor.md)
* [使用更改源估算器](how-to-use-change-feed-estimator.md)
* [更改源处理器开始时间](./change-feed-processor.md#starting-time)
* 正在尝试为迁移到 Azure Cosmos DB 进行容量计划？
    * 若只知道现有数据库群集中的 vcore 和服务器数量，请阅读[使用 vCore 或 vCPU 估算请求单位](../convert-vcore-to-request-unit.md) 
    * 若知道当前数据库工作负载的典型请求速率，请阅读[使用 Azure Cosmos DB 容量计划工具估算请求单位](estimate-ru-with-capacity-planner.md)