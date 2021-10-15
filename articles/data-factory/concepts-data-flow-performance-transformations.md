---
title: 优化映射数据流中的转换性能
titleSuffix: Azure Data Factory & Azure Synapse
description: 了解如何优化 Azure 数据工厂和 Azure Synapse Analytics 管道的映射数据流中的转换性能。
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.custom: synapse
ms.date: 09/29/2021
ms.openlocfilehash: 28b7847c4341751bd7a2beacdf61e138551f991c
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129293499"
---
# <a name="optimizing-transformations"></a>优化转换

使用以下策略优化 Azure 数据工厂和 Azure Synapse Analytics 管道的映射数据流中的转换性能。

## <a name="optimizing-joins-exists-and-lookups"></a>优化联接、存在和查找

### <a name="broadcasting"></a>广播

在联接、查找和存在转换中，如果数据流足够小，工作器节点内存可容纳一个数据流或同时容纳两个数据流，则可以通过启用“广播”来优化性能。 广播是指将较小的数据帧发送到群集中的所有节点。 这样，Spark 引擎即可在不重新组合大型流中的数据的情况下执行联接。 默认情况下，Spark 引擎将自动决定是否广播联接的一侧。 如果你熟悉传入的数据并且知道一个流将显著小于另一个流，则可以选择固定广播。 固定广播会强制 Spark 广播选定的流。 

如果广播数据的大小对于 Spark 节点而言太大，则可能会发生内存不足错误。 为避免内存不足错误，请使用内存优化群集。 如果在数据流执行期间遇到广播超时，可以关闭广播优化。 但是，这会导致数据流执行速度变慢。

当处理可能需要较长时间才能进行查询的数据源时（如大型数据库查询），建议关闭联接的广播。 当群集尝试广播到计算节点时，查询时间较长的源可能会导致 Spark 超时。 关闭广播的另一种不错的选择是：当数据流中有一个流时，该流正在聚合要在以后的查找转换中使用的值。 此模式可能会迷惑 Spark 优化器并导致超时。

:::image type="content" source="media/data-flow/joinoptimize.png" alt-text="联接转换优化":::

### <a name="cross-joins"></a>交叉联接

如果在联接条件中使用文本值，或者在联接的两侧具有多个匹配，Spark 会将联接作为交叉联接运行。 交叉联接是完全笛卡尔积，可筛选掉联接的值。 它比其他联接类型要慢得多。 确保在联接条件的两侧都具有列引用，以避免影响性能。

### <a name="sorting-before-joins"></a>联接前排序

与 SSIS 等工具中的合并联接不同，联接转换不是强制性的合并联接操作。 联接键在转换前无需排序。 不建议在映射数据流中使用排序转换。

## <a name="window-transformation-performance"></a>窗口转换性能

[映射数据流中的窗口转换](data-flow-window.md)根据你在转换设置的 ```over()``` 子句中选择的列值对数据进行分区。 窗口转换中公开了大量非常常用的聚合和分析函数。 但如果要为排名 ```rank()``` 或行号 ```rowNumber()``` 在整个数据集上生成窗口，则建议改用[排名转换](data-flow-rank.md)和[代理键转换](data-flow-surrogate-key.md)。 这些转换将再次使用这些函数更好地执行完整数据集操作。

## <a name="repartitioning-skewed-data"></a>对倾斜的数据重新分区

某些转换（例如联接和聚合）会重新组合数据分区，有时可能会导致数据倾斜。 数据倾斜意味着数据未均匀地分布于各个分区。 数据严重倾斜会导致下游转换和接收器写入变慢。 可以通过单击监视显示器中的转换在数据流运行的任何时间点检查数据的倾斜度。

:::image type="content" source="media/data-flow/skewness-kurtosis.png" alt-text="倾斜度和峰度":::

监视显示器将显示数据如何分布到每个分区，以及倾斜度和峰度两个指标。 倾斜度是数据不对称程度的度量，可以是正、零、负或未定义的值。 负倾斜表示左尾比右尾长。 峰度是表示数据是重尾分布还是轻尾分布的度量。 高峰度值表示不理想。 理想的倾斜度范围在 -3 到 3 之间，而理想的峰度范围则是小于 10。 查看分区图并查看 1 bar 是否明显大于其余部分可轻松理解这些数字。

如果转换后数据未均匀分区，可以使用[优化选项卡](concepts-data-flow-performance.md#optimize-tab)进行重新分区。 重新组合数据需要时间，并且可能无法优化数据流性能。

> [!TIP]
> 如果对数据进行了重新分区，但下游转换重新组合了数据，请在用作联接键的列上使用哈希分区。

## <a name="next-steps"></a>后续步骤

- [数据流性能概述](concepts-data-flow-performance.md)
- [优化源](concepts-data-flow-performance-sources.md)
- [优化接收器](concepts-data-flow-performance-sinks.md)
- [在管道中使用数据流](concepts-data-flow-performance-pipelines.md)

请参阅与性能相关的其他数据流文章：

- [数据流活动](control-flow-execute-data-flow-activity.md)
- [监视数据流性能](concepts-data-flow-monitoring.md)
- [Integration Runtime 性能](concepts-integration-runtime-performance.md)
