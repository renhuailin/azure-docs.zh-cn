---
title: 使用重新分区优化 Azure 流分析作业
description: 本文介绍如何使用重新分区优化无法并行化的 Azure 流分析作业。
ms.service: stream-analytics
author: sidramadoss
ms.author: sidram
ms.date: 03/04/2021
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 95749f2acea6b605cfdba5a4f3d4f5526e751c5a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102182530"
---
# <a name="use-repartitioning-to-optimize-processing-with-azure-stream-analytics"></a>通过重新分区优化使用 Azure 流分析进行的处理

本文介绍如何使用重新分区针对无法完全[并行化](stream-analytics-scale-jobs.md)的方案缩放 Azure 流分析查询。

如果出现以下情况，则可能无法使用[并行化](stream-analytics-parallelization.md)：

* 你无法控制输入流的分区键。
* 你的源“散布”的输入跨多个以后需要合并的分区。

当你处理的数据所在的流不是按照自然的输入方案（例如事件中心的 **PartitionId**）分片时，需要重新分区或重新组织。 如果重新分区，则每个分片都可以独立处理，这样就可以通过线性方式横向扩展流式处理管道。 

## <a name="how-to-repartition"></a>如何重新分区
可以通过两种方式对输入进行重新分区：
1. 使用执行重新分区的单独流分析作业
2. 使用单个作业，但在进行重新分区后执行自定义分析逻辑

### <a name="creating-a-separate-stream-analytics-job-to-repartition-input"></a>创建单独的流分析作业对输入进行重新分区
可以创建使用分区键读取输入并将其写入到事件中心输出的作业。 然后，此事件中心可以作为另一个流分析作业的输入，你可以在其中实现分析逻辑。 在作业中配置此事件中心输出时，必须指定分区键，流分析将使用该分区键对数据重新分区。 
```sql
-- For compat level 1.2 or higher
SELECT * 
INTO output
FROM input

--For compat level 1.1 or lower
SELECT *
INTO output
FROM input PARTITION BY PartitionId
```

### <a name="repartition-input-within-a-single-stream-analytics-job"></a>在单个流分析作业中对输入进行重新分区
你还可以在查询中引入一个步骤，该步骤先对输入重新分区，供查询中的其他步骤使用。 例如，如果想要基于 DeviceId 对输入重新分区，则查询将为：
```sql
WITH RepartitionedInput AS 
( 
SELECT * 
FROM input PARTITION BY DeviceID
)

SELECT DeviceID, AVG(Reading) as AvgNormalReading  
INTO output
FROM RepartitionedInput  
GROUP BY DeviceId, TumblingWindow(minute, 1)  
```

以下示例查询联接两个其数据已重新分区的流。 联接两个其数据已重新分区的流时，这两个流必须有相同的分区键和计数。 结果就是一个具有相同分区方案的流。

```sql
WITH step1 AS (SELECT * FROM input1 PARTITION BY DeviceID),
step2 AS (SELECT * FROM input2 PARTITION BY DeviceID)

SELECT * INTO output FROM step1 PARTITION BY DeviceID UNION step2 PARTITION BY DeviceID
```

输出方案应该与流方案键和计数匹配，这样就可以单独刷新每个子流。 流也可以在刷新之前通过另一方案再次进行合并和重新分区，但是应避免该方法，因为它会导致常规处理延迟增加，以及资源使用率增加。

## <a name="streaming-units-for-repartitions"></a>重新分区的流单元

试验并观察作业的资源使用情况，确定所需的具体分区数。 [流单元 (SU)](stream-analytics-streaming-unit-consumption.md) 的数目必须按照每个分区所需的物理资源进行调整。 通常情况下，每个分区需要六个 SU。 如果分配给作业的资源不足，系统只会在有益于作业的情况下应用重新分区。

## <a name="repartitions-for-sql-output"></a>针对 SQL 输出的重新分区

当作业使用 SQL 数据库进行输出时，请使用显式重新分区来匹配优化的分区计数，以便将吞吐量最大化。 由于 SQL 在使用八个写入器时效果最好，因此在刷新之前将流重新分区为八个（或在上游的某个位置进一步进行分区）可能会有益于作业性能。 

如果输入分区超过 8 个，则继承输入分区方案可能不是适当的选择。 请考虑在查询中使用 [INTO](/stream-analytics-query/into-azure-stream-analytics#into-shard-count) 来显式指定输出写入器的数量。 

以下示例从输入中读取数据（而不考虑其自然分区情况如何），然后根据 DeviceID 维度将流重新划分为十份，并将数据刷新到输出。 

```sql
SELECT * INTO [output] FROM [input] PARTITION BY DeviceID INTO 10
```

有关详细信息，请参阅从 [Azure 流分析输出到 Azure SQL 数据库](stream-analytics-sql-output-perf.md)。


## <a name="next-steps"></a>后续步骤

* [Azure 流分析入门](stream-analytics-introduction.md)
* [利用 Azure 流分析中的查询并行化](stream-analytics-parallelization.md)
