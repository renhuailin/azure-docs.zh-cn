---
title: 使用更改源估算器 - Azure Cosmos DB
description: 了解如何使用更改源估算器分析更改源处理器的进度
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 04/01/2021
ms.author: maquaran
ms.custom: devx-track-csharp
ms.openlocfilehash: a94ac4dd7b0006f3c1dc5a4fb7e8a95666780f1b
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123113378"
---
# <a name="use-the-change-feed-estimator"></a>使用更改源估算器
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

本文介绍如何监视[更改源处理器](./change-feed-processor.md)实例在读取更改源时的进度。

## <a name="why-is-monitoring-progress-important"></a>为什么监视进度很重要？

更改源处理器充当一个指针，它会在[更改源](../change-feed.md)中向前移动，并将更改传递给委托实现。

更改源处理器部署可以按特定速率处理更改，具体取决于可用的资源，例如 CPU、内存、网络，等等。

如果该速率低于更改在 Azure Cosmos 容器中的发生速率，则处理器就会开始出现滞后现象。

确定此情况后，我们就可以决定是否需要缩放更改源处理器部署。

## <a name="implement-the-change-feed-estimator"></a>实现更改源估算器

### <a name="as-a-push-model-for-automatic-notifications"></a>作为自动通知的推送模型

与[更改源处理器](./change-feed-processor.md)一样，更改源估算器可用作推送模型。 此估算器会度量上一个处理项（按租用容器的状态定义）和容器中的最新更改之间的差异，并将该值推送到某个委托。 进行度量的时间间隔也可以自定义，默认值为 5 秒。

例如，如果更改源处理器定义如下：

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartProcessorEstimator)]

那么，若要初始化某个估算器来度量该处理器，正确的方式是使用 `GetChangeFeedEstimatorBuilder`，如下所示：

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartEstimator)]

其中的处理器和估算器共享同一 `leaseContainer` 和同一名称。

另外有两个参数，一个是委托，其接收的数字表示 **有多少更改待处理器读取**，另一个是需要进行该度量的时间间隔。

例如，下面是用于接收估算的委托：

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=EstimationDelegate)]

可以将此估算发送给监视解决方案，并通过它了解进度随时间的变化情况。

### <a name="as-an-on-demand-detailed-estimation"></a>作为按需详细估算

有一种替代方法可让你按需获得估算值（与推送模型相比）。 此模型还提供了更多详细信息：

* 每次租用的估计滞后时间。
* 实例拥有并处理每次租用，因此你可以确定该实例是否存在问题。

如果更改源处理器定义如下：

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartProcessorEstimatorDetailed)]

你可以使用相同的租用配置创建估算器：

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartEstimatorDetailed)]

无论何时，只要你需要，就可以以所需的频率获得详细的估算值：

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=GetIteratorEstimatorDetailed)]

每个 `ChangeFeedProcessorState` 都会包含租用和滞后时间信息，以及当前哪个实例拥有它。 

> [!NOTE]
> 更改源估算器不需部署在更改源处理器中，也不需部署在同一项目中。 它可以是独立的，也可以运行在完全不同的实例中，这正是我们建议你使用的。 它只需使用同一名称和租用配置。

## <a name="additional-resources"></a>其他资源

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [GitHub 上的用法示例](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [GitHub 上的其他示例](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>后续步骤

现在，可以通过以下文章继续详细了解更改源处理器：

* [更改源处理器概述](change-feed-processor.md)
* [更改源处理器开始时间](./change-feed-processor.md#starting-time)