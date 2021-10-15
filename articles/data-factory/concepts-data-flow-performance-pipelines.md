---
title: 优化映射数据流中的管道性能
titleSuffix: Azure Data Factory & Azure Synapse
description: 了解如何在 Azure 数据工厂和 Azure Synapse Analytics 管道的管道中优化数据流执行。
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.custom: synapse
ms.date: 09/29/2021
ms.openlocfilehash: 3545d7e8bbf8131c9fe454b39ea57a23cd233264
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129293495"
---
# <a name="using-data-flows-in-pipelines"></a>在管道中使用数据流 

生成具有多个数据流的复杂管道时，逻辑流可能会对时序和成本产生重大影响。 本部分介绍各体系结构策略的影响。

## <a name="executing-data-flows-in-parallel"></a>并行执行数据流

如果并行执行多个数据流，则服务会为每个活动启动单独的 Spark 群集。 这样，每个作业将是独立的，并且将并行运行，但这会导致多个群集同时运行。

如果数据流并行执行，建议不启用 Azure IR 生存时间属性，因为该属性会导致多个暖池处于空闲状态。

> [!TIP]
> 你无需在 for each 活动中多次运行同一数据流，而可将数据暂存到数据湖中并使用通配符路径处理单个数据流中的数据。

## <a name="execute-data-flows-sequentially"></a>按顺序执行数据流

如果按顺序执行数据流活动，则建议在 Azure IR 配置中设置 TTL。 服务将重新使用计算资源，从而缩短群集启动时间。 每个活动将仍处于独立状态，并将为每个执行接收新的 Spark 上下文。 若要进一步减少连续活动之间的时间，请在 Azure IR 上设置“快速重用”复选框，让服务重新使用现有群集。

## <a name="overloading-a-single-data-flow"></a>重载单一数据流

如果将所有逻辑放入单一数据流，则服务将在单个 Spark 实例上执行整个作业。 尽管这似乎可以降低成本，但它混合了各种逻辑流，可能难以监视和调试。 如果一个组件失败，则该作业的所有其他部分也会失败。 建议按独立的业务逻辑流来组织数据流。 如果数据流太大，将其拆分为单独的组件即可更轻松地进行监视和调试。 尽管对数据流中的转换数量没有硬性限制，但转换过多会导致作业变得复杂。

## <a name="execute-sinks-in-parallel"></a>并行执行接收器

数据流接收器默认以串行方式顺序执行每个接收器，并且会在接收器中遇到错误时使数据流失败。 此外，除非进入数据流属性并为接收器设置不同的优先级，否则所有接收器均默认为同一组。

数据流允许你在 UI 设计器的“数据流属性”选项卡中将接收器组合在一起。 可以设置接收器的执行顺序，也可以使用同一组号将接收器组合在一起。 为帮助管理组，可以要求服务在同一组中运行接收器，以并行运行。

在管道上，“接收器属性”部分下的“执行数据流”活动是一个开启并行接收器加载的选项。 启用“并行运行”即指示数据流同时（而不是按顺序）写入连接的接收器。 为了使用并行选项，接收器必须组合在一起，并且必须通过“新建分支”或“条件拆分”连接到同一流。

## <a name="next-steps"></a>后续步骤

- [数据流性能概述](concepts-data-flow-performance.md)
- [优化源](concepts-data-flow-performance-sources.md)
- [优化接收器](concepts-data-flow-performance-sinks.md)
- [优化转换](concepts-data-flow-performance-transformations.md)

请参阅与性能相关的其他数据流文章：

- [数据流活动](control-flow-execute-data-flow-activity.md)
- [监视数据流性能](concepts-data-flow-monitoring.md)
- [Integration Runtime 性能](concepts-integration-runtime-performance.md)
