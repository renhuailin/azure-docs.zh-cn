---
title: 映射数据流性能和优化指南
titleSuffix: Azure Data Factory & Azure Synapse
description: 了解影响 Azure 数据工厂和 Azure Synapse Analytics 管道中映射数据流性能的关键因素。
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.custom: synapse
ms.date: 09/29/2021
ms.openlocfilehash: 7f12eb06d514ddc7d001012b0e3111c7603bdca9
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129274038"
---
# <a name="mapping-data-flows-performance-and-tuning-guide"></a>映射数据流性能和优化指南

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure 数据工厂和 Synapse 管道中的映射数据流提供一个无代码界面，用于大规模设计和运行数据转换。 如果你不熟悉映射数据流，请参阅[映射数据流概述](concepts-data-flow-overview.md)。 本文重点介绍调整和优化数据流以使它们达到性能基准的各种方法。

观看下面的视频，看看显示了用数据流转换数据的一些示例计时。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4rNxM]

## <a name="monitoring-data-flow-performance"></a>监视数据流性能

使用调试模式验证转换逻辑后，以管道中活动的形式端到端地运行数据流。 数据流使用[执行数据流活动](control-flow-execute-data-flow-activity.md)在管道内进行操作化。 相较于其他活动，数据流活动具有独特的监视体验，其中显示了转换逻辑的详细执行计划和性能配置文件。 若要查看数据流的详细监视信息，请单击管道的活动运行输出中的眼镜图标。 有关详细信息，请参阅[监视映射数据流](concepts-data-flow-monitoring.md)。

:::image type="content" source="media/data-flow/monitoring-details.png" alt-text="数据流监视器":::

监视数据流性能时，需注意四个潜在瓶颈：

* 群集启动时间
* 从源中读取
* 转换时间
* 写入接收器 

:::image type="content" source="media/data-flow/monitoring-performance.png" alt-text="数据流监视":::

群集启动时间是指启动 Apache Spark 群集所需的时间。 该值位于监视屏幕的右上角。 数据流在实时模型上运行，其中每个作业都使用独立的群集。 该启动时间通常为 3-5 分钟。 对于顺序作业，可以通过启用生存时间值来缩短该时间。 有关详细信息，请参阅 [Integration Runtime 性能](concepts-integration-runtime-performance.md#time-to-live)中的“生存时间”部分。

数据流利用 Spark 优化器对“阶段”中的业务逻辑进行重新排序并运行，从而尽可能快地执行。 对于数据流写入的每个接收器，监视输出列出每个转换阶段的持续时间，以及将数据写入接收器所需的时间。 最长的持续时间可能就是数据流的瓶颈。 如果耗时最久的转换阶段包含源，则建议考虑进一步优化读取时间。 如果转换耗时久，则可能需要重新分区或增加集成运行时的大小。 如果接收器处理时间耗时久，则可能需要扩展数据库或验证是否未输出到单个文件。

确定数据流的瓶颈后，请使用以下优化策略来优化性能。

## <a name="testing-data-flow-logic"></a>测试数据流逻辑

从 UI 设计和测试数据流时，使用调试模式可以针对实时 Spark 群集进行交互式测试。 这样，无需等待群集预热即可预览数据和执行数据流。 有关详细信息，请参阅[调试模式](concepts-data-flow-debug-mode.md)。

## <a name="optimize-tab"></a>“优化”选项卡

“优化”选项卡包含用于配置 Spark 群集的分区方案的设置。 此选项卡存在于数据流的每个转换中，可指定是否要在转换完成后对数据进行重新分区。 调整分区可对跨计算节点的数据分布以及数据局部优化提供控制度，但会对数据流的总体性能同时造成正面影响和负面影响。

:::image type="content" source="media/data-flow/optimize.png" alt-text="屏幕截图显示“优化”选项卡，其中包含“分区选项”、“分区类型”和“分区数”。":::

默认选中“使用当前分区”，这指示服务保留转换的当前输出分区。 由于对数据进行重新分区需要时间，因此在大多数情况下建议选择“使用当前分区”。 建议对数据进行重新分区的情况包括：“进行会严重倾斜数据的聚合和联接后”或“在 SQL DB 上使用源分区时”。

若要更改任何转换的分区，请选择“优化”选项卡并选择“设置分区”单选按钮 。 你会看到一系列用于分区的选项。 最佳的分区方法根据数据量、候选键、null 值和基数而异。 

> [!IMPORTANT]
> 单一分区将所有分布式数据合并到一个分区。 此操作非常慢，还会严重影响所有下游转换和写入。 除非有明确的业务原因要使用此选项，否则极不建议使用它。

以下分区选项在每个转换中都可用：

### <a name="round-robin"></a>轮循机制 

轮循机制将数据平均分布到各个分区。 如果没有适当的候选键用于实施可靠、智能的分区策略，请使用轮循机制。 可以设置物理分区数目。

### <a name="hash"></a>哈希

服务将生成列的哈希来生成统一的分区，使包含类似值的行划归到同一分区。 使用“哈希”选项时，请测试是否存在分区倾斜。 可以设置物理分区数目。

### <a name="dynamic-range"></a>动态范围

动态范围基于提供的列或表达式使用 Spark 动态范围。 可以设置物理分区数目。 

### <a name="fixed-range"></a>固定范围

生成一个表达式用于在分区的数据列中提供值的固定范围。 使用此选项之前，应该充分了解你的数据，以避免分区倾斜。 为表达式输入的值将用作分区函数的一部分。 可以设置物理分区数目。

### <a name="key"></a>键

如果你已充分了解数据的基数，则键分区可能是不错的策略。 键分区为列中的每个唯一值创建分区。 无法设置分区数目，因为该数目基于数据中的唯一值。

> [!TIP]
> 手动设置分区方案会重新组合数据，可能会抵消 Spark 优化器的优势。 最佳做法是不要手动设置分区，除非有需要。

## <a name="logging-level"></a>日志记录级别

如果不需要数据流活动的每个管道执行完整地记录所有详细的遥测日志，则可根据需要将日志记录级别设置为“基本”或“无”。 在“详细”模式（默认）下执行数据流时，要求此服务在数据转换期间完整地记录每个分区级别的活动。 该操作成本昂贵，因此仅在进行故障排除时启用“详细”模式可优化整体数据流和管道性能。 “基本”模式仅记录转换持续时间，而“无”模式仅提供持续时间的摘要。

:::image type="content" source="media/data-flow/logging.png" alt-text="日志记录级别":::

## <a name="next-steps"></a>后续步骤

- [优化源](concepts-data-flow-performance-sources.md)
- [优化接收器](concepts-data-flow-performance-sinks.md)
- [优化转换](concepts-data-flow-performance-transformations.md)
- [在管道中使用数据流](concepts-data-flow-performance-pipelines.md)

请参阅与性能相关的其他数据流文章：

- [数据流活动](control-flow-execute-data-flow-activity.md)
- [监视数据流性能](concepts-data-flow-monitoring.md)
- [Integration Runtime 性能](concepts-integration-runtime-performance.md)
