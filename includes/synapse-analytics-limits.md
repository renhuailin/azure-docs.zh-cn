---
title: include 文件
description: include 文件
services: synapse-analytics
author: jonburchel
ms.service: synapse-analytics
ms.topic: include
ms.date: 09/22/2021
ms.author: jburchel
ms.custom: include file
ms.openlocfilehash: d97a314a5928de17ad878aee5dfc14b5519d0f80
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2021
ms.locfileid: "129095873"
---
Azure Synapse Analytics 具有以下默认限制，可确保客户的订阅受到保护，使其免受其他工作负载的限制。 若要将限制提高到订阅的最大值，请联系支持人员。

### <a name="synapse-workspace-limits"></a>Synapse 工作区限制

| 资源 | 默认限制 | 最大限制 | 
| -------- | ------------- | ------------- |
| Azure 订阅中的 Synapse 工作区 | 20 | 20 |

### <a name="synapse-pipeline-limits"></a>Synapse 管道限制

| 资源 | 默认限制 | 最大限制 |
| -------- | ------------- | ------------- |
| Synapse 工作区中的 Synapse 管道 | 800 | 800 |
| 工作区中的实体（例如管道、数据集、触发器、链接服务、专用终结点和集成运行时）总数 | 5,000 | 请[联系支持人员](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 一个工作区中 Azure-SSIS Integration Runtime 的 CPU 核心总数 | 256 | 请[联系支持人员](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 每个工作区的并行管道运行数，这些运行在该工作区中的所有管道之间共享 | 10,000  | 10,000 |
| 每个 [Azure Integration Runtime 区域](../articles/data-factory/concepts-integration-runtime.md#azure-ir-location)每个工作区的并发外部活动运行数<br><small>外部活动在集成运行时上进行管理，但在 Databricks、存储过程、HDInsight 和 Web 等链接服务上执行。此限制不适用于自承载 IR。</small> | 3,000 | 3,000 |
| 每个 [Azure Integration Runtime 区域](../articles/data-factory/concepts-integration-runtime.md#azure-ir-location)每个工作区的并发管道活动运行数 <br><small>Lookup、GetMetadata 和 Delete 等管道活动在集成运行时上执行。此限制不适用于自承载 IR。</small> | 1,000 | 1,000                                                        |
| 每个 [Azure Integration Runtime 区域](../articles/data-factory/concepts-integration-runtime.md#azure-ir-location)每个工作区的并发创作操作数<br><small>包括测试连接、浏览文件夹列表和表列表，以及预览数据。此限制不适用于自承载 IR。</small> | 200 | 200                                                          |
| 每个 [Azure Integration Runtime 区域](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)每个工作区的并发数据集成单位<sup>1</sup>消耗量| 区域组 1<sup>2</sup>：6,000<br>区域组 2<sup>2</sup>：3,000<br>区域组 3<sup>2</sup>：1,500<br>托管虚拟网络<sup>2</sup>：2,400 | 区域组 1<sup>2</sup>：6,000<br/>区域组 2<sup>2</sup>：3,000<br/>区域组 3<sup>2</sup>：1,500<br>托管虚拟网络：[联系客户支持](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 每个管道的最大活动数，包括容器的内部活动 | 40 | 40 |
| 可以针对单个自承载集成运行时创建的最大链接集成运行时数 | 100 | 请[联系支持人员](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 每个管道的最大参数个数 | 50 | 50 |
| ForEach 项 | 100,000 | 100,000 |
| ForEach 并行度 | 20 | 50 |
| 每个管道的最大排队运行数 | 100 | 100 |
| 每个表达式的字符数 | 8,192 | 8,192 |
| 最小翻转窗口触发间隔 | 5 分钟 | 15 分钟 |
| 管道活动运行的最大超时时间 | 7 天 | 7 天 |
| 管道对象的每对象字节数<sup>3</sup> | 200 KB | 200 KB |
| 数据集和链接服务对象的每对象字节数<sup>3</sup> | 100 KB | 2,000 KB |
| 每个活动运行的每有效负载字节数<sup>4</sup> | 896 KB | 896 KB |
| 每个复制活动运行的数据集成单元数<sup>1</sup> | 256 | 256 |
| 编写 API 调用 | 1,200/小时 | 1,200/小时<br/><br/> 此限制是由 Azure 资源管理器而不是 Azure Synapse Analytics 所强加的。 |
| 读取 API 调用 | 12,500/小时 | 12,500/小时<br/><br/> 此限制是由 Azure 资源管理器而不是 Azure Synapse Analytics 所强加的。 |
| 每分钟监视的查询数 | 1,000 | 1,000 |
| 数据流调试会话最长时间 | 8 小时 | 8 小时 |
| 每个集成运行时的数据流的并发数量 | 50 | 请[联系支持人员](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 托管 vNet 中每个集成运行时的并发数据流数| 20 | 请[联系支持人员](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 每个工作区每个用户的数据流调试会话的并发数量 | 3 | 3 |
| 数据流 Azure IR TTL 限制 | 4 小时 |  4 小时 |
| 工作区的元数据实体大小限制 | 2 GB | 请[联系支持人员](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |

<sup>1</sup> 数据集成单元 (DIU) 用于云到云复制操作，详见[数据集成单元（版本 2）](../articles/data-factory/copy-activity-performance.md#data-integration-units)。 有关计费的信息，请参阅 [Azure Synapse Analytics 定价](https://azure.microsoft.com/pricing/details/synapse-analytics/)。

<sup>2</sup> [Azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#azure-integration-runtime) 已[在全球提供](https://azure.microsoft.com/global-infrastructure/services/)，以确保数据的符合性、有效性并减少网络流出成本。 

| 区域组 | 区域 |
| -------- | ------ |
| 区域组 1 | 美国中部、美国东部、美国东部 2、北欧、西欧、美国西部、美国西部 2 |
| 区域组 2 | 澳大利亚东部、澳大利亚东南部、巴西南部、印度中部、日本东部、美国中北部、美国中南部、东南亚、美国中西部 |
| 区域组 3 | 其他区域 |
如果启用了托管虚拟网络，则所有区域组中的数据集成单元 (DIU) 数为 2,400。

<sup>3</sup> 管道、数据集和链接服务对象代表工作负荷的逻辑组。 对这些对象的限制与可以使用 Azure Synapse Analytics 移动或处理的数据量无关。 Synapse Analytics 已设计为可以扩展，以便处理数千万亿字节的数据。

<sup>4</sup> 每个活动运行的有效负载包括活动配置、关联的数据集和链接服务配置（如果有），以及为每个活动类型生成的系统属性的一小部分。 对此有效负载大小的限制与可以使用 Azure Synapse Analytics 移动或处理的数据量无关。 如果达到此限制，请了解[症状和建议](../articles/data-factory/data-factory-troubleshoot-guide.md#payload-is-too-large)。

### <a name="dedicated-sql-pool-limits"></a>专用 SQL 池限制
有关 Azure Synapse Analytics 中专用 SQL 池的容量限制的详细信息，请参阅[专用 SQL 池资源限制](../articles/synapse-analytics/sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md)。

### <a name="web-service-call-limits"></a>Web 服务调用限制
Azure 资源管理器限制 API 调用。 可以根据 [Azure 资源管理器 API 限制](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits)中规定的频率执行 API 调用。
