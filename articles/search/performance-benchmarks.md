---
title: 性能基准测试
titleSuffix: Azure Cognitive Search
description: 通过各种性能基准了解 Azure 认知搜索的性能
author: dereklegenzoff
ms.author: delegenz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: 03276e72224c8ddefa80358c4214893fd3363c96
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122178134"
---
# <a name="azure-cognitive-search-performance-benchmarks"></a>Azure 认知搜索性能基准

Azure 认知搜索的性能取决于[多种因素](search-performance-tips.md)，包括搜索服务的大小以及要发送的查询的类型。 为帮助估计工作负载所需的搜索服务大小，我们运行了多种基准来记录不同搜索服务和配置的性能。 这些基准并不保证服务的特定性能级别，但能够让你了解可期望何种性能。

为涵盖一系列不同用例，我们针对两种主要场景运行了基准：

* **电子商务搜索** - 此基准模拟真实的电子商务场景，并基于北欧电子商务公司 [CDON](https://cdon.com)。
* **文档搜索** - 此场景由对 [Semantic Scholar](https://www.aclweb.org/anthology/2020.acl-main.447/) 中的全文文档的关键字搜索组成。 这模拟的是常见文档搜索解决方案。

尽管这些场景反映了不同的用例，但每种场景都不同，因此我们始终建议对单个工作负载进行性能测试。 我们[使用 JMeter 发布了性能测试解决方案](https://github.com/Azure-Samples/azure-search-performance-testing)，因此你可以针对自己的服务运行类似测试。

## <a name="testing-methodology"></a>测试方法

为了对 Azure 认知搜索的性能进行基准测试，我们在不同层和副本/分区组合针对两种不同场景运行了测试。

创建这些基准时，使用了以下方法：

1. 测试以每秒 `X` 个查询 (QPS) 的速度开始，持续 180 秒。 这通常为 5 或 10 QPS。
2. 然后，QPS 增加 `X` 并再次运行 180 秒
3. 每 180 秒，测试就会增加 `X` QPS，直到平均延迟增加到 1000 毫秒以上或查询成功率小于 99% 为止。

下图提供了测试查询负载情况的直观示例：

![示例测试](./media/performance-benchmarks/example-test.png)

每种场景均使用了至少 10,000 个唯一的查询，以免测试因缓存而过度扭曲。

> [!IMPORTANT]
> 这些测试仅包含查询工作负载。 如果希望进行大量索引操作，请务必将其纳入估算和性能测试中。 在本[教程](tutorial-optimize-indexing-push-api.md)中，可以找到模拟索引编制的示例代码。

### <a name="definitions"></a>定义

- **最大 QPS** - 下面的最大 QPS 数基于测试中实现的最高 QPS，而该测试中 99% 的查询成功完成且未达到限制，平均延迟低于 1000 毫秒。

- **最大 QPS 的百分比** - 针对特定测试实现的最大 QPS 的百分比。 例如，如果在特定测试中达到了最大值 100 QPS，则最大 QPS 的 20% 为 20 QPS。

- **延迟** - 查询的服务器延迟；这些数字不包括 [往返延迟 (RTT)](https://en.wikipedia.org/wiki/Round-trip_delay)。 以下值以毫秒 (ms) 为单位。

### <a name="disclaimer"></a>免责声明

[此处](https://github.com/Azure-Samples/azure-search-performance-testing/tree/main/other_tools)提供了用于运行这些基准的代码。 值得注意的是，与以下基准相比，[JMeter 性能测试解决方案](https://github.com/Azure-Samples/azure-search-performance-testing)的 QPS 水平略低一些。 差异可能是由于测试样式不同。 这说明了尽量让性能测试与生产工作负载保持相似的重要性。

这些基准并不保证服务的特定性能级别，但能够让你了解可对自己的情况期望何种性能。

如果有任何疑问或疑虑，请通过 azuresearch_contact@microsoft.com 与我们联系。

## <a name="benchmark-1-e-commerce-search"></a>基准 1：电子商务搜索

:::row:::
   :::column span="1":::
      ![CDON 徽标](./media/performance-benchmarks/cdon-logo-160px2.png)
   :::column-end:::
   :::column span="3":::
      此基准与电子商务公司 [CDON](https://cdon.com) 合作创建，该公司是北欧地区最大的在线市场，在瑞典、芬兰、挪威和丹麦均开展业务。 CDON 通过其 1,500 位商家提供各式各样的产品类别，包含的产品超过 8 百万种。 2020 年，CDON 拥有的访客超过 1.2 亿，活跃客户达 200 万。 请参阅[本文](https://pulse.microsoft.com/transform/na/fa1-how-cdon-has-been-using-technology-to-become-the-leading-marketplace-in-the-nordics/)，了解有关 CDON 的 Azure 认知搜索使用情况的详细信息。
   :::column-end:::
:::row-end:::

为了运行这些测试，我们使用了 CDON 的生产搜索索引的快照以及对其[网站](https://cdon.com)的数千次唯一查询。

### <a name="scenario-details"></a>场景详细信息

- **文档计数**：6,000,000 
- **索引大小**：20 GB
- **索引架构**：一种宽索引，其中共包含 250 个字段、25 个可搜索字段以及 200 个可查找/可筛选字段
- **查询类型**：全文搜索查询，包括 facet、筛选器、排序和计分概要文件

### <a name="s1-performance"></a>S1 性能

#### <a name="queries-per-second"></a>每秒查询次数

下图显示了较长时间内服务可以处理的最高查询负载，单位为每秒查询次数 (QPS)。

![可维护性最高的 QPS 电子商务 s1](./media/performance-benchmarks/s1-ecom-qps.png)

#### <a name="query-latency"></a>查询延迟

查询延迟因服务负载而异，服务负载较高时，平均查询延迟较高。 下表显示了三种不同利用率级别在查询延迟时间占比为 25%、50%、75%、90%、95% 和 99% 时的情况

| 最大 QPS 的百分比  | 平均延迟 | 25% | 75% | 90% | 95% | 99%|
|---|---|---|---| --- | --- | --- | 
| 20%  | 104 毫秒  | 35 毫秒  | 115 毫秒   | 177 毫秒 | 257 毫秒 | 738 毫秒 |
| 50%  | 140 毫秒  | 47 毫秒  | 144 毫秒   | 241 毫秒 | 400 毫秒 | 1175 毫秒 |
| 80%  | 239 毫秒  | 77 毫秒  | 248 毫秒   | 466 毫秒 | 763 毫秒 | 1752 毫秒 | 


### <a name="s2-performance"></a>S2 性能

#### <a name="queries-per-second"></a>每秒查询次数

下图显示了较长时间内服务可以处理的最高查询负载，单位为每秒查询次数 (QPS)。

![可维护性最高的 QPS 电子商务 s2](./media/performance-benchmarks/s2-ecom-qps.png)

#### <a name="query-latency"></a>查询延迟

查询延迟因服务负载而异，服务负载较高时，平均查询延迟较高。 下表显示了三种不同利用率级别在查询延迟时间占比为 25%、50%、75%、90%、95% 和 99% 时的情况

| 最大 QPS 的百分比  | 平均延迟 | 25% | 75% | 90% | 95% | 99%|
|---|---|---|---| --- | --- | --- | 
| 20%  | 56 ms | 21 ms | 68 毫秒  | 106 ms  | 132 毫秒 | 210 毫秒 | 
| 50%  | 71 毫秒  | 26 毫秒  | 83 毫秒   | 132 毫秒 | 177 毫秒 | 329 毫秒 |
| 80%  | 140 毫秒  | 47 毫秒  | 153 ms   | 293 毫秒 | 452 毫秒 | 924 毫秒 | 

### <a name="s3-performance"></a>S3 性能

#### <a name="queries-per-second"></a>每秒查询次数

下图显示了较长时间内服务可以处理的最高查询负载，单位为每秒查询次数 (QPS)。

![可维护性最高的 QPS 电子商务 s3](./media/performance-benchmarks/s3-ecom-qps.png)

在本例中，我们看到，添加第二个分区会大幅增加最大 QPS，但添加第三个分区会导致边际收益递减。 收益减少可能是因为所有数据都已提取到仅包含两个分区的 S3 的有效内存。

#### <a name="query-latency"></a>查询延迟

查询延迟因服务负载而异，服务负载较高时，平均查询延迟较高。 下表显示了三种不同利用率级别在查询延迟时间占比为 25%、50%、75%、90%、95% 和 99% 时的情况

| 最大 QPS 的百分比  | 平均延迟 | 25% | 75% | 90% | 95% | 99%|
|---|---|---|---| --- | --- | --- |
| 20%  | 50 毫秒  | 20 ms  | 64 毫秒   | 83 毫秒 | 98 毫秒 | 160 ms |
| 50%  | 62 毫秒  | 24 毫秒  | 80 ms   | 107 毫秒 | 130 毫秒 | 253 毫秒 |
| 80%  | 115 毫秒  | 38 毫秒  | 121 毫秒   | 218 毫秒 | 352 毫秒 | 828 毫秒 |

## <a name="benchmark-2-document-search"></a>基准 2：文档搜索

### <a name="scenario-details"></a>场景详细信息

- **文档计数**：750 万
- **索引大小**：22 GB
- **索引架构**：23 个字段；8 个可搜索，10 个可筛选/可查找
- **查询类型**：关键字搜索，facet 和命中词突出显示

### <a name="s1-performance"></a>S1 性能

#### <a name="queries-per-second"></a>每秒查询次数

下图显示了较长时间内服务可以处理的最高查询负载，单位为每秒查询次数 (QPS)。

![可维护性最高的 QPS 文档搜索 s1](./media/performance-benchmarks/s1-docsearch-qps.png)

#### <a name="query-latency"></a>查询延迟

查询延迟因服务负载而异，服务负载较高时，平均查询延迟较高。 下表显示了三种不同利用率级别在查询延迟时间占比为 25%、50%、75%、90%、95% 和 99% 时的情况

| 最大 QPS 的百分比  | 平均延迟 | 25% | 75% | 90% | 95% | 99%|
|---|---|---|---| --- | --- | --- |
| 20%  | 67 毫秒  | 44 毫秒  | 77 毫秒   | 103 毫秒 | 126 毫秒 | 216 毫秒 |
| 50%  | 93 毫秒  | 59 毫秒  | 110 毫秒   | 150 毫秒 | 184 毫秒 | 304 毫秒 |
| 80%  | 150 毫秒  | 96 毫秒  | 184 毫秒   | 248 毫秒 | 297 毫秒 | 424 毫秒 |

### <a name="s2-performance"></a>S2 性能

#### <a name="queries-per-second"></a>每秒查询次数

下图显示了较长时间内服务可以处理的最高查询负载，单位为每秒查询次数 (QPS)。

![可维护性最高的 QPS 文档搜索 s2](./media/performance-benchmarks/s2-docsearch-qps.png)

#### <a name="query-latency"></a>查询延迟

查询延迟因服务负载而异，服务负载较高时，平均查询延迟较高。 下表显示了三种不同利用率级别在查询延迟时间占比为 25%、50%、75%、90%、95% 和 99% 时的情况

| 最大 QPS 的百分比  | 平均延迟 | 25% | 75% | 90% | 95% | 99%|
|---|---|---|---| --- | --- | --- |
| 20%  | 45 毫秒  | 31 毫秒  | 55 ms   | 73 ms | 84 毫秒 | 109 毫秒 |
| 50%  | 63 毫秒  | 39 毫秒  | 81 毫秒   | 106 ms | 123 毫秒 | 163 毫秒 |
| 80%  | 115 毫秒  | 73 ms  | 145 毫秒   | 191 毫秒 | 224 毫秒 | 291 毫秒 |

### <a name="s3-performance"></a>S3 性能

#### <a name="queries-per-second"></a>每秒查询次数

下图显示了较长时间内服务可以处理的最高查询负载，单位为每秒查询次数 (QPS)。

![可维护性最高的 QPS 文档搜索 s3](./media/performance-benchmarks/s3-docsearch-qps.png)

#### <a name="query-latency"></a>查询延迟

查询延迟因服务负载而异，服务负载较高时，平均查询延迟较高。 下表显示了三种不同利用率级别在查询延迟时间占比为 25%、50%、75%、90%、95% 和 99% 时的情况

| 最大 QPS 的百分比  | 平均延迟 | 25% | 75% | 90% | 95% | 99%|
|---|---|---|---| --- | --- | --- |
| 20%  | 43 毫秒  | 29 毫秒  | 53 毫秒   | 74 ms | 86 毫秒 | 111 毫秒 |
| 50%  | 65 毫秒  | 37 毫秒  | 85 毫秒   | 111 毫秒 | 128 毫秒 | 164 毫秒 |
| 80%  | 126 毫秒  | 83 毫秒  | 162 毫秒   | 205 毫秒 | 233 毫秒 | 281 毫秒 |

## <a name="takeaways"></a>要点

通过这些基准，你可以大致了解 Azure 认知搜索提供的性能。 还可以了解不同层上的服务之间的差异。

以下是这些基准的关键要点：

* 通常，S2 可处理的查询量至少是 S1 的四倍
* 在可比较的查询量下，S2 的延迟通常比 S1 更低
* 添加副本时，服务可处理的 QPS 通常呈线性缩放（例如，如果一个副本可处理 10 QPS，则 5 个副本通常可处理 50 QPS）
* 服务负载越高，平均延迟就越高

你还了解到，不同场景之间的性能可能存在很大差异。 如果没有获得预期的性能，请查看[提高性能的提示](search-performance-tips.md)。

## <a name="next-steps"></a>后续步骤

现在，你已了解性能基准，接下来可进一步了解如何分析认知搜索的性能和影响性能的关键因素。

> [!div class="nextstepaction"]
> [分析性能](search-performance-analysis.md)
> [提高性能的提示](search-performance-tips.md)
