---
title: 内存和并发限制
description: 查看分配给 Azure Synapse Analytics 中专用 SQL 池的各性能级别和资源类的内存和并发限制。
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/04/2021
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 8fd628c649d379e60ddf8ec772e1cf708f3e50d1
ms.sourcegitcommit: bd65925eb409d0c516c48494c5b97960949aee05
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/06/2021
ms.locfileid: "111540295"
---
# <a name="memory-and-concurrency-limits-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Azure Synapse Analytics 中专用 SQL 池的内存和并发限制

查看分配给 Azure Synapse Analytics 中的各个性能级别和资源类的内存和并发限制。  

> [!NOTE]
> 与动态或静态资源类相比，工作负载管理工作负荷组为每个请求和并发性配置资源提供了更大的灵活性。  了解更多详细信息，请参阅[工作负荷组](sql-data-warehouse-workload-isolation.md)和[创建工作负荷组](/sql/t-sql/statements/create-workload-group-transact-sql)语法。

## <a name="data-warehouse-capacity-settings"></a>数据仓库容量设置

以下各表显示了不同性能级别的数据仓库的最大容量。 若要更改性能级别，请参阅[缩放计算 - 门户](quickstart-scale-compute-portal.md)。

### <a name="service-levels"></a>服务级别

服务级别范围为 DW100c 到 DW30000c。

| 性能级别 | 计算节点 | 每个计算节点的分布区数 | 每个数据仓库的内存 (GB) |
|:-----------------:|:-------------:|:------------------------------:|:------------------------------:|
| DW100c            | 1             | 60                             |    60                          |
| DW200c            | 1             | 60                             |   120                          |
| DW300c            | 1             | 60                             |   180                          |
| DW400c            | 1             | 60                             |   240                          |
| DW500c            | 1             | 60                             |   300                          |
| DW1000c           | 2             | 30                             |   600                          |
| DW1500c           | 3             | 20                             |   900                          |
| DW2000c           | 4             | 15                             |  1200                          |
| DW2500c           | 5             | 12                             |  1500                          |
| DW3000c           | 6             | 10                             |  1800                          |
| DW5000c           | 10            | 6                              |  3000                          |
| DW6000c           | 12            | 5                              |  3600                          |
| DW7500c           | 15            | 4                              |  4500                          |
| DW10000c          | 20            | 3                              |  6000                          |
| DW15000c          | 30            | 2                              |  9000                          |
| DW30000c          | 60            | 1                              | 18000                          |

最大服务级别为 DW30000c，包含 60 个计算节点，每个计算节点有一个分布区。 例如，DW30000c 级别的 600 TB 数据仓库的每个计算节点可以处理大约 10 TB 数据。

## <a name="concurrency-maximums-for-workload-groups"></a>工作负载组的并发最大值

随着[工作负载组](sql-data-warehouse-workload-isolation.md)的引入，并发槽位的概念不再适用。  每个请求的资源按百分比分配，并在工作负载组定义中指定。  但是，即使删除了并发槽位，每个查询也需要基于服务级别的最小资源量。  下表定义了各服务级别协议中，每个查询所需的最小资源量以及可以实现的关联并发。

|服务级别|并发查询数上限|REQUEST_MIN_RESOURCE_GRANT_PERCENT 支持的最小百分比|
|---|---|---|
|DW100c|4|25%|
|DW200c|8|12.5%|
|DW300c|12|8%|
|DW400c|16|6.25%|
|DW500c|20|5%|
|DW1000c|32|3%|
|DW1500c|32|3%|
|DW2000c|48|2%|
|DW2500c|48|2%|
|DW3000c|64|1.5%|
|DW5000c|64|1.5%|
|DW6000c|128|0.75%|
|DW7500c|128|0.75%|
|DW10000c|128|0.75%|
|DW15000c|128|0.75%|
|DW30000c|128|0.75%|
||||

## <a name="concurrency-maximums-for-resource-classes"></a>资源类的并发最大值

为了确保每个查询都有足够的资源来有效执行，Synapse SQL 会通过向每个查询分配并发槽位来跟踪资源利用率。 系统根据重要性和并发槽位将查询放入某个队列。 查询在队列中等待，直到有足够的并发槽位可用。 [重要性](sql-data-warehouse-workload-importance.md)和并发槽位确定了 CPU 优先级。 有关详细信息，请参阅[分析工作负荷](analyze-your-workload.md)。

**静态资源类**

下表显示了每个[静态资源类](resource-classes-for-workload-management.md)的最大并发查询数和并发槽位数。  

| 服务级别 | 并发查询数上限 | 可用的并发槽位数 | staticrc10 使用的槽位数 | staticrc20 使用的槽位数 | staticrc30 使用的槽位数 | staticrc40 使用的槽位数 | staticrc50 使用的槽位数 | staticrc60 使用的槽位数 | staticrc70 使用的槽位数 | staticrc80 使用的槽位数 |
|:-------------:|:--------------------------:|:---------------------------:|:---------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|
| DW100c        |  4                         |    4                        | 1         | 2          | 4          | 4          | 4         |  4         |  4         |  4         |
| DW200c        |  8                         |    8                        | 1         | 2          | 4          | 8          |  8         |  8         |  8         |  8        |
| DW300c        | 12                         |   12                        | 1         | 2          | 4          | 8          |  8         |  8         |  8         |   8        |
| DW400c        | 16                         |   16                        | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW500c        | 20                         |   20                        | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW1000c       | 32                         |   40                        | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW1500c       | 32                         |   60                        | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW2000c       | 48                         |   80                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW2500c       | 48                         |  100                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW3000c       | 64                         |  120                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW5000c       | 64                         |  200                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW6000c       | 128                        |  240                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW7500c       | 128                        |  300                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW10000c      | 128                        |  400                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW15000c      | 128                        |  600                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW30000c      | 128                        | 1200                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |

**动态资源类**

下表显示了每个[动态资源类](resource-classes-for-workload-management.md)的最大并发查询数和并发槽位数。 动态资源类对所有服务级别的 small-medium-large-xlarge 资源类使用 3-10-22-70 内存百分比分配。

| 服务级别 | 并发查询数上限 | 可用的并发槽位数 | smallrc 使用的槽数 | mediumrc 使用的槽数 | largerc 使用的槽数 | xlargerc 使用的槽数 |
|:-------------:|:--------------------------:|:---------------------------:|:---------------------:|:----------------------:|:---------------------:|:----------------------:|
| DW100c        |  4                         |    4                        | 1                     |  1                     |  1                    |   2                    |
| DW200c        |  8                         |    8                        | 1                     |  1                     |  1                    |   5                    |
| DW300c        | 12                         |   12                        | 1                     |  1                     |  2                    |   8                    |
| DW400c        | 16                         |   16                        | 1                     |  1                     |  3                    |  11                    |
| DW500c        | 20                         |   20                        | 1                     |  2                     |  4                    |  14                    |
| DW1000c       | 32                         |   40                        | 1                     |  4                     |  8                    |  28                    |
| DW1500c       | 32                         |   60                        | 1                     |  6                     |  13                   |  42                    |
| DW2000c       | 32                         |   80                        | 2                     |  8                     |  17                   |  56                    |
| DW2500c       | 32                         |  100                        | 3                     | 10                     |  22                   |  70                    |
| DW3000c       | 32                         |  120                        | 3                     | 12                     |  26                   |  84                    |
| DW5000c       | 32                         |  200                        | 6                     | 20                     |  44                   | 140                    |
| DW6000c       | 32                         |  240                        | 7                     | 24                     |  52                   | 168                    |
| DW7500c       | 32                         |  300                        | 9                     | 30                     |  66                   | 210                    |
| DW10000c      | 32                         |  400                        | 12                    | 40                     |  88                   | 280                    |
| DW15000c      | 32                         |  600                        | 18                    | 60                     | 132                   | 420                    |
| DW30000c      | 32                         | 1200                        | 36                    | 120                    | 264                   | 840                    |

如果没有足够的并发槽位来启动查询执行，查询将根据重要性进行排队和执行。  如果重要性相同，查询将以先进先出的方式执行。  如果查询已完成并且查询数和槽数低于限制，则 Azure Synapse Analytics 会释放排队的查询。

## <a name="next-steps"></a>后续步骤

若要详细了解如何利用资源类来进一步优化工作负荷，请查看以下文章：

* [工作负荷管理工作负荷组](sql-data-warehouse-workload-isolation.md)
* [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql)
* [用于工作负荷管理的资源类](resource-classes-for-workload-management.md)
* [分析工作负荷](analyze-your-workload.md)
