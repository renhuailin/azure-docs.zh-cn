---
title: 估算成本
titleSuffix: Azure Cognitive Search
description: 了解可计费事件、定价模型以及用于管理运行认知搜索服务的成本的提示。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/12/2021
ms.openlocfilehash: 6a371d3a9edf537e78f5a889139a053cb925518f
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122770831"
---
# <a name="estimate-and-manage-costs-of-an-azure-cognitive-search-service"></a>估计和管理 Azure 认知搜索服务的成本

本文介绍定价模型、可计费事件以及用于管理运行 Azure 认知搜索服务的成本的提示。

## <a name="pricing-model"></a>定价模型

Azure 认知搜索中的可伸缩性体系结构基于副本和分区的灵活组合，以便你可根据是否需要更多的查询或索引功能来改变容量，并仅为所需内容付费。

搜索服务使用的资源量乘以服务层所建立的计费费率就是运行服务的成本。 成本和容量密切相关。 在估计成本时，了解运行索引和查询工作负荷所需的容量，可极大帮助你获得预计成本。

出于计费目的，有两个简单的公式需要注意：

| 公式 | 说明 |
|---------|-------------|
| **R x P = SU** | 使用的副本数乘以使用的分区数，等于服务所使用的服务单元 (SU) 的数量。 SU 是资源单位，可以是分区也可以是副本。 |
| **SU * 计费费率 = 每月支出** | SU 的数量乘以预配服务的层的计费费率，得出总体月度帐单的主要决定因素。 某些功能或工作负载依赖于其他 Azure 服务，这可能会在订阅级别增加解决方案的成本。 以下可计费事件部分确定了可添加到帐单中的功能。 |

每个服务至少从 1 个 SU（1 个分区乘以 1 个副本）开始。 任何服务的最大 SU 值为 36。 可通过多种方式来实现此最大值：例如，6 个分区 x 6 个副本，或 3 个分区 x 12 个副本。 通常使用小于总容量的值（例如，3 副本、3 分区的服务按 9 个 SU 计费）。 有关有效的组合，请参阅[分区和副本组合](search-capacity-planning.md#chart)图表。

费率按每个 SU、按小时计算。 每个层的费率渐进式提高。 层越高，分区越大且速度越快，因此，每小时的总费率更高。 可以在[定价详细信息](https://azure.microsoft.com/pricing/details/search/)页上查看每个层的费率。

大多数客户只是联机使用一部分总容量，将剩余的容器保持预留状态。 在计费方面，支付的每小时费用取决于联机的分区和副本数量（使用 SU 公式计算）。 

## <a name="billable-events"></a>计费事件

基于 Azure 认知搜索构建的解决方案可能会在以下方面产生成本：

+ [服务本身的成本](#service-costs)，在全天候运行且使用最低配置（一个分区和副本）的情况下，按基准费率计算。 可以将此视为运行服务的固定成本。

+ 增加容量（副本或分区）带来的成本，该成本随计费费率的递增而增加。 如果业务要求有高可用性，则你需要 3 个副本。

+ 带宽费用（出站数据传输）

+ 特定功能或高级特性所需的附加服务：

  + AI 扩充使用计费技能（需要[认知服务](https://azure.microsoft.com/pricing/details/cognitive-services/)）。 图像提取也可计费。
  + 知识存储（需要 [Azure 存储](https://azure.microsoft.com/pricing/details/storage/)）
  + 增量扩充（需要 [Azure 存储](https://azure.microsoft.com/pricing/details/storage/)，适用于 AI 扩充）
  + 客户管理的密钥和双重加密（需要 [Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/)）
  + 无 Internet 访问模型的专用终结点（需要 [Azure 专用链接](https://azure.microsoft.com/pricing/details/private-link/)）
  + 语义搜索是标准层上的一项高级功能（参阅[认知搜索定价页](https://azure.microsoft.com/pricing/details/search/)以了解成本）。 可以[禁用语义搜索](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update#searchsemanticsearch)以防止意外使用。

### <a name="service-costs"></a>服务成本

与可以“暂停”以避免产生费用的虚拟机或其他资源不同，Azure 认知搜索服务在专门供你使用的硬件上始终可用。 因此，创建服务是一个计费事件，该事件在创建该服务时开始，在删除该服务时结束。 

最低费用是采用计费费率的第一个搜索单位（1 个副本 x 1 个分区）。 在服务的整个生命周期内，此最低费用都是固定的，因为服务不能在低于此配置的组件上运行。 

超出最低费用时，可以单独添加副本和分区。 通过副本和分区递增容量会增大费用，其计算公式如下：（副本数 x 分区数 x 计费费率），其中，收费费率取决于所选的定价层。

估计搜索解决方案的成本时，请记住定价和容量不是线性的（容量翻倍不是支付两倍的费用，而是要在同一层上支付更高的费用）。 此外，在某些情况下，切换到更高的层可以在大致相同的价位上提供更好更快的性能。 有关详细信息和示例，请参阅[升级到标准 S2 层](search-performance-tips.md#tip-upgrade-to-a-standard-s2-tier)。

### <a name="bandwidth-charges"></a>带宽费用

如果 Azure 数据源与 Azure 认知搜索位于不同的区域中，则使用[索引器](search-indexer-overview.md)可能会影响计费。 在这种情况下，需要计算将出站数据从 Azure 数据源移动到 Azure 认知搜索的成本。 有关详细信息，请参阅相关的 Azure 数据平台的定价页。

如果在数据所在的同一区域中创建 Azure 认知搜索服务，则可以完全消除数据流出费用。 下面是摘自[带宽定价页](https://azure.microsoft.com/pricing/details/bandwidth/)中的一些信息：

+ 入站数据：Microsoft 不会对 Azure 上的任何服务的入站数据收费。 

+ 出站数据：出站数据是指查询结果。 认知搜索不会对出站数据收费，但如果服务在不同区域中，则可能会产生来自 Azure 的出站费用。

  这些费用实际上不是 Azure 认知搜索帐单的一部分。 之所以提到这些费用，是因为如果你要将结果发送到其他区域或非 Azure 应用，则可以看到这些成本反映在你的整体账单中。

### <a name="ai-enrichment-with-cognitive-services"></a>使用认知服务的 AI 扩充

对于使用计费技能的 [AI 扩充](cognitive-search-concept-intro.md)，应当计划[将可计费的 Azure 认知服务资源附加到](cognitive-search-attach-cognitive-services.md) Azure 认知搜索所在的同一区域，以在 S0 定价层实现即用即付处理。 附加认知服务不会产生固定的费用。 只需支付所需的处理费。

| 操作 | 计费影响 |
|-----------|----------------|
| 文档破解、文本提取 | 免费 |
| 文档破解、图像提取 | 根据从文档中提取的图像数计费。 在 [索引器配置](/rest/api/searchservice/create-indexer#indexer-parameters)中，**imageAction** 是触发图像提取的参数。 如果 **imageAction** 设置为“none”（默认值），则不收取图像提取费用。 参阅[定价页](https://azure.microsoft.com/pricing/details/search/)，了解图像提取费用。 |
| 基于认知服务的[内置技能](cognitive-search-predefined-skills.md) | 计费费率与直接使用认知服务执行任务的费率相同。 每个索引器每天可以免费处理 20 份文档。 更大或更频繁的工作负荷需要使用密钥。 |
| 不添加扩充的[内置技能](cognitive-search-predefined-skills.md) | 无。 非计费实用工具技能包括条件逻辑、整形程序、文本合并和文本拆分。 不会影响计费，无需认知服务密钥，也不会限制处理 20 份文档。 |
| 自定义技能 | 自定义技能是你提供的功能。 使用自定义技能的费用完全取决于自定义代码是否调用其他计量的服务。  无认知服务关键要求，也未针对自定义技能设定 20 份文档限制。|
| [自定义实体查找](cognitive-search-skill-custom-entity-lookup.md) | 按 Azure 认知搜索计量。 有关详细信息，请参阅[定价页](https://azure.microsoft.com/pricing/details/search/#pricing)页。 |

> [!TIP]
> [增量扩充（预览版）](cognitive-search-incremental-indexing-conceptual.md)通过缓存和重用不受对技能组合的更改影响的扩充，来降低技能组合处理成本。 缓存需要 Azure 存储（请参阅[定价](https://azure.microsoft.com/pricing/details/storage/blobs/)），但如果可以重复使用现有的扩充，则技能组执行的累积成本会降低。

## <a name="tips-for-managing-costs"></a>管理成本的提示

以下指南有助于降低成本或提高成本管理效率：

+ 在同一区域或者在尽可能少的区域中创建所有资源，以最大程度地减少甚至消除带宽费用。

+ 将所有服务（例如 Azure 认知搜索、认知服务，以及解决方案中使用的任何其他 Azure 服务）合并到一个资源组中。 在 Azure 门户中找到该资源组，并使用“成本管理”命令来洞察实际支出和预计支出。

+ 考虑对前端应用程序使用 Azure Web 应用，使请求和响应保留在数据中心边界范围内。

+ 针对索引编制等资源密集型操作纵向扩展，然后针对常规查询工作负荷向下重新调整。 首先对 Azure 认知搜索使用最低的配置（由一个分区和一个副本组成的一个 SU），然后监视用户活动，以识别指示需要更多容量的使用模式。 如果有可预测的模式，也许可以使用活动来同步规模（需要编写代码来自动化此过程）。

+ 成本管理内置于 Azure 基础结构中。 有关跟踪成本、工具和 API 的详细信息，请查看[计费和成本管理](../cost-management-billing/cost-management-billing-overview.md)。

不可能临时关闭搜索服务。 专用资源始终运行，是在服务的生存期内专门分配给你使用的。 删除服务这项操作是永久性的，也会删除其关联的数据。

就服务本身而言，降低费用的唯一方法是将副本和分区数减少到一个较低的水平，但仍能提供可接受的性能并[遵从 SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/)；或者在较低的层创建一个服务（S1 的每小时费率低于 S2 或 S3 的费率）。 假设你预配了一个面向低端负载预测的服务。若要扩充该服务，可以创建另一个具有较大层的服务，在该服务上重建索引，然后删除第一个服务。

## <a name="next-steps"></a>后续步骤

希望优化并节省云支出？

> [!div class="nextstepaction"]
> [使用成本管理开始分析成本](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)