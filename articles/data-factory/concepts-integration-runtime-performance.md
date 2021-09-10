---
title: Integration Runtime 性能
titleSuffix: Azure Data Factory & Azure Synapse
description: 了解如何优化和改进 Azure 数据工厂和 Azure Synapse Analytics 中的 Azure Integration Runtime 的性能。
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.custom: synapse
ms.date: 08/24/2021
ms.openlocfilehash: 6cc19a1c37071fc7a9af9c3474e0b706283cdf9d
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123113334"
---
# <a name="optimizing-performance-of-the-azure-integration-runtime"></a>优化 Azure Integration Runtime 的性能

数据流在运行时启动的 Spark 群集上运行。 活动的集成运行时 (IR) 中定义了所用群集的配置。 定义集成运行时时，需考虑三个性能注意事项：群集类型、群集大小和生存时间。

有关如何创建 Integration Runtime 的详细信息，请参阅 [Integration Runtime](concepts-integration-runtime.md)。

## <a name="cluster-type"></a>群集类型

对于启动的 Spark 群集类型，有三个可用选项：常规用途、内存优化和计算优化。

常规用途群集是默认选择，非常适合大多数数据流工作负载。 这些群集往往达到了性能与成本的最佳平衡。

如果数据流具有大量联接和查找，则建议使用内存优化群集。 内存优化群集可以在内存中存储更多数据，可最大程度地减少可能遇到的任何内存不足错误。 内存优化群集的每核心价格最高，但也往往可以生成更有效的管道。 如果在执行数据流时遇到任何内存不足错误，请切换到内存优化 Azure IR 配置。 

计算优化不适用于 ETL 工作流，不建议用于大多数生产工作负荷。 对于更简单的非内存密集型数据转换（例如，筛选数据或添加派生列），可以以更便宜的每核心价格使用计算优化群集。

## <a name="cluster-size"></a>群集大小

数据流将数据处理分发到 Spark 群集中的各个节点，以并行执行操作。 具有更多核心的 Spark 群集会增加计算环境中的节点数。 节点越多，对数据流的处理能力越高。 增加群集的大小通常可轻松缩短处理时间。

默认群集大小即四个驱动程序节点和四个工作器节点。  处理更多数据时，建议使用较大的群集。 以下是可能的大小选项：

| 工作器核心 | 驱动程序核心 | 核心总数 | 说明 |
| ------------ | ------------ | ----------- | ----- |
| 4 | 4 | 8 | 不可用于计算优化群集 |
| 8 | 8 | 16 | |
| 16 | 16 | 32 | |
| 32 | 16 | 48 | |
| 64 | 16 | 80 | |
| 128 | 16 | 144 | |
| 256 | 16 | 272 | |

数据流按 vcore-hrs 定价，这意味着群集大小和执行时间都会对价格产生影响。 纵向扩展时，每分钟的群集成本将增加，但总时间将缩短。

> [!TIP]
> 群集大小对数据流性能的影响程度有上限。 根据数据的大小，当群集大小增加到某一程度时，将不再能优化性能。 例如，如果节点多于数据分区，则添加更多节点将无济于事。 最佳做法是从小规模开始，然后纵向扩展以满足性能需求。 

## <a name="time-to-live"></a>生存时间

默认情况下，每个数据流活动都会根据 Azure IR 配置启动新的 Spark 群集。 冷群集启动需要几分钟时间，且完成启动后才能开始处理数据。 如果管道包含多个顺序数据流，则可以启用生存时间 (TTL) 值。 指定生存时间值可使群集在执行完成后保持一定时长的活跃度。 如果新作业在 TTL 时间内开始使用 IR，则它将重新使用现有群集，且启动时间将显著缩短。 第二个作业完成后，群集将再次保持 TTL 时长的活跃度。

通过在“数据流属性”下的“Azure 集成运行时”中设置“快速重用”选项，你还可以最大程度减少热群集的启动时间。 将其设置为“true”可以告诉该服务在每次执行作业后不销毁现有群集，而是重新使用现有群集，其实就是在 TTL 中指定的时间段内保持在 Azure IR 中设置的计算环境有效。 在管道中执行时，此选项可以使数据流活动的启动时间达到最短。

但是，如果大部分数据流并行执行，则建议为用于这些活动的 IR 启用 TTL。 一个群集一次只能运行一个作业。 如果有一个可用群集，但启动了两个数据流，则只有一个数据流可使用该活跃群集。 第二个作业将启动自己的独立群集。

> [!NOTE]
> 使用自动解析集成运行时时，生存时间不可用

## <a name="next-steps"></a>后续步骤

请参阅与性能相关的其他数据流文章：

- [数据流性能](concepts-data-flow-performance.md)
- [数据流活动](control-flow-execute-data-flow-activity.md)
- [监视数据流性能](concepts-data-flow-monitoring.md)
