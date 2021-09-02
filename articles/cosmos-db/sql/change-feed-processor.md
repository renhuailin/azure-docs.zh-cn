---
title: Azure Cosmos DB 更改源处理器
description: 了解如何使用 Azure Cosmos DB 更改源处理器读取更改源（更改源处理器的组件）
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 07/20/2021
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: c2c22b6de6c4c1bdf5cd8856c8d9405f19d56509
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123113252"
---
# <a name="change-feed-processor-in-azure-cosmos-db"></a>Azure Cosmos DB 更改源处理器
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

更改源处理器是 [Azure Cosmos DB SDK V3](https://github.com/Azure/azure-cosmos-dotnet-v3) 的一部分。 它简化了读取更改源的过程，可有效地在多个使用者之间分布事件处理。

更改源处理器库的主要优点是其容错行为，可保证更改源中的所有事件交付“至少一次”。

## <a name="components-of-the-change-feed-processor"></a>更改源处理器的组件

实现更改源处理器需要四个主要组件：

1. **监视的容器：** 监视的容器是用于生成更改源的数据。 对受监视的容器的任何插入和更新都会反映在容器的更改源中。

1. **租约容器：** 租用容器充当状态存储并协调处理跨多个辅助角色的更改源。 租用容器可以与受监视的容器存储在同一帐户中，也可以存储在单独的帐户中。

1. 主机：主机是使用更改源处理器侦听更改的应用程序实例。 具有相同租用配置的多个实例可以并行运行，但是每个实例应具有不同的实例名称。

1. 委托：委托是用于定义开发人员要对更改源处理器读取的每一批更改执行何种操作的代码。 

若要进一步了解更改源处理器的四个元素是如何协同工作的，请看下图中的一个示例。 受监视的容器会存储文档，并将“City”用作分区键。 我们发现分区键值分布在包含项的范围内。 有两个主机实例，更改源处理器向每个实例分配不同范围的分区键值，以最大程度地提高计算分布率。 每个范围都是并行读取的，其进程的维护独立于租用容器中的其他范围。

:::image type="content" source="./media/change-feed-processor/changefeedprocessor.png" alt-text="更改源处理器示例" border="false":::

## <a name="implementing-the-change-feed-processor"></a>实现更改源处理器

入口点始终是被监视的容器，来自你调用 `GetChangeFeedProcessorBuilder` 的 `Container` 实例：

[!code-csharp[Main](~/samples-cosmosdb-dotnet-change-feed-processor/src/Program.cs?name=DefineProcessor)]

其中，第一个参数是描述此处理器的目标的唯一名称，第二个参数是要处理更改的委托实现。 

委托示例如下：


[!code-csharp[Main](~/samples-cosmosdb-dotnet-change-feed-processor/src/Program.cs?name=Delegate)]

最后，使用 `WithInstanceName` 定义此处理器实例的名称，它是用于维护具有 `WithLeaseContainer` 的租用状态的容器。

调用 `Build` 可让你获得可通过调用 `StartAsync` 启动的处理器实例。

## <a name="processing-life-cycle"></a>处理生命周期

主机实例的正常生命周期为：

1. 读取更改源。
1. 如果没有发生更改，请在一段预定义的时间内保持睡眠状态（可在 Builder 中使用 `WithPollInterval` 进行自定义），然后转到 #1。
1. 如果发生了更改，请将其发送给委托。
1. 委托成功地处理更改后，以最新的处理时间点更新租用存储，然后转到 #1。

## <a name="error-handling"></a>错误处理。

更改源处理器可在发生用户代码错误后复原。 这意味着，如果委托实现具有未经处理的异常（步骤 #4），则将停止处理特定更改批次的线程，并将创建一个新线程。 新线程将检查租赁存储在该分区键值范围内的最新时间点，并从该时间点重启，从而有效地向委托发送同一批更改。 此行为一直持续到委托能正确处理更改为止，这也是更改源处理器能够提供“至少一次”保证的原因，因为如果委托代码引发异常，它将重试该批次。

> [!NOTE]
> 只有一种情况不会重试一批更改。 如果第一次执行委托时发生故障，则租用存储没有以前保存的状态可用于重试。 在这种情况下，重试将使用[初始启动配置](#starting-time)，该配置可能包含也可能不包含最后一批次。

若要防止更改源处理器不断地重试同一批更改，应在委托代码中添加逻辑，以便在出现异常时将文档写入死信队列。 此设计可确保你可以跟踪未处理的更改，同时仍然能够继续处理将来的更改。 死信队列可能是另一个 Cosmos 容器。 确切的数据存储并不重要，只是未处理的更改会被保留。

此外，还可以使用[更改源估算器](how-to-use-change-feed-estimator.md)在更改源处理器实例读取更改源时监视其进度。 可以使用此估计来了解更改源处理器是否由于可用资源（如 CPU、内存和网络带宽）而“停滞”或滞后。

## <a name="deployment-unit"></a>部署单元

单个更改源处理器部署单元由一个或多个具有相同 `processorName` 和租用容器配置的实例组成。 可以有多个部署单元，其中每个单元可以具有不同的更改业务流，且每个部署单元可由一个或多个实例组成。 

例如，你可能有一个部署单元，只要容器发生更改，该部署单元就会触发外部 API。 另一个部署单元可能会在每次发生更改时实时移动数据。 当被监视的容器中发生更改时，所有部署单元都会收到通知。

## <a name="dynamic-scaling"></a>动态缩放

如前所述，部署单元中可以有一个或多个实例。 若要充分利用部署单元内的计算分布，只需满足以下关键要求：

1. 所有实例应具有相同的租用容器配置。
1. 所有实例都应具有相同的 `processorName`。
1. 每个实例都需要具有不同的实例名称 (`WithInstanceName`)。

如果符合这三个条件，更改源处理器将使用均等分布算法，将租用容器中的所有租约分布到所有正在运行的实例，并将计算并行化。 在给定的时间，一个租约只能归一个实例所有，因此，最大实例数等于租约数。

实例数可以增加和减少，更改源处理器会通过相应地重新分配来动态调整负载。

而且，由于吞吐量或存储增加，更改源处理器还可以动态调整到容器规模。 当容器增多时，更改源处理器会以透明方式应对这种情况，方法是动态增加租约并将新的租约分布到现有实例。

## <a name="change-feed-and-provisioned-throughput"></a>更改源和预配吞吐量

针对受监视容器的更改源读取操作将消耗 RU。 

针对租用容器的操作会消耗 RU。 使用相同租用容器的实例数越多，潜在的 RU 消耗量就越高。 如果决定扩展并递增实例数，请记住监视租用容器上的 RU 消耗量。

## <a name="starting-time"></a>开始时间

默认情况下，首次启动更改源处理器时，它将初始化租用容器，并开始其[处理生命周期](#processing-life-cycle)。 不会检测到首次初始化更改源处理器之前在受监视容器中发生的任何更改。

### <a name="reading-from-a-previous-date-and-time"></a>从以前的某个日期和时间读取

将 `DateTime` 的实例传递给 `WithStartTime` 生成器扩展，可将更改源处理器初始化为从 **特定的日期和时间** 开始读取更改：

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=TimeInitialization)]

更改源处理器将根据该特定日期和时间初始化，并开始读取此日期和时间之后发生的更改。

> [!NOTE]
> 多区域写入帐户不支持在特定的日期和时间启动更改源处理器。

### <a name="reading-from-the-beginning"></a>从头开始读取

在其他方案（例如数据迁移，或分析容器的整个历史记录）中，需要从 **该容器的生存期开始时间** 读取更改源。 为此，可以在生成器扩展中使用 `WithStartTime`，但需要传递 `DateTime.MinValue.ToUniversalTime()`，以便生成最小 `DateTime` 值的 UTC 表示形式，如下所示：

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartFromBeginningInitialization)]

更改源处理器将会初始化，并从容器生存期的开始时间读取更改。

> [!NOTE]
> 这些自定义选项仅用于设置更改源处理器的起始时间点。 首次初始化租约容器后，更改这些选项不起作用。

## <a name="where-to-host-the-change-feed-processor"></a>托管更改源处理器的位置

更改源处理器可以托管在任何支持长时间运行的进程或任务的平台中：

* 连续运行的 [Azure WebJob](/learn/modules/run-web-app-background-task-with-webjobs/)。
* [Azure 虚拟机](/azure/architecture/best-practices/background-jobs#azure-virtual-machines)中的进程。
* [Azure Kubernetes 服务](/azure/architecture/best-practices/background-jobs#azure-kubernetes-service)中的后台作业。
* [ASP.NET 托管服务](/aspnet/core/fundamentals/host/hosted-services)。

虽然更改源处理器可以在生存期较短的环境中运行，但由于租用容器会对状态进行维护，这些环境的启动周期会导致接收通知的延迟增加（因为每次启动环境时存在启动处理器的开销）。

## <a name="additional-resources"></a>其他资源

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [GitHub 上的完整示例应用程序](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)
* [GitHub 上的其他使用情况示例](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [针对更改源处理器的 Cosmos DB 研讨会实验室](https://azurecosmosdb.github.io/labs/dotnet/labs/08-change_feed_with_azure_functions.html#consume-cosmos-db-change-feed-via-the-change-feed-processor)

## <a name="next-steps"></a>后续步骤

现在，可以通过以下文章继续详细了解更改源处理器：

* [更改源概述](../change-feed.md)
* [更改源请求模型](change-feed-pull-model.md)
* [如何从更改源处理器库迁移](how-to-migrate-from-change-feed-library.md)
* [使用更改源估算器](how-to-use-change-feed-estimator.md)
* [更改源处理器开始时间](#starting-time)