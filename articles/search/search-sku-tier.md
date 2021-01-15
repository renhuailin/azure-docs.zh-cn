---
title: 选择一个定价层
titleSuffix: Azure Cognitive Search
description: 可在以下层中预配 Azure 认知搜索：“免费”、“基本”和“标准”，其中“标准”在各种资源配置和容量级别中均可用。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/15/2020
ms.custom: contperf-fy21q2
ms.openlocfilehash: 4c58968cb6a38a10433915ec8fa00336ccad301e
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2021
ms.locfileid: "98216404"
---
# <a name="choose-a-pricing-tier-for-azure-cognitive-search"></a>选择 Azure 认知搜索的定价层

[创建搜索服务](search-create-service-portal.md)时，请选择在服务生存期内固定的定价层。 选择的层决定：

+ 可以创建 (最大限制的索引和其他对象数量) 
+ 分区（物理存储）的大小和速度
+ 可计费费率，每月固定费用，但如果添加分区或副本，则还会增加成本

此外，还提供了一些级别要求的 [高级功能](#premium-features) 。

## <a name="tier-descriptions"></a>层说明

层包括 **免费**、 **基本**、 **标准** 和 **优化存储**。 “标准”和“存储优化”提供多种配置和容量。

以下 Azure 门户屏幕截图显示了可用的层，其中不包括定价层（可在门户中和[定价页](https://azure.microsoft.com/pricing/details/search/)上找到该层）。 

![Azure 认知搜索的定价层](media/search-sku-tier/tiers.png "Azure 认知搜索的定价层")

**免费** 为较小的项目创建受限的搜索服务，如运行教程和代码示例。 在内部，副本和分区可被多个订阅者共享。 不能缩放免费服务，也不能运行繁重的工作负荷。

“基本”和“标准”层是最常用的计费层，其中“标准”层是默认的层。   通过控制下的专用资源，你可以部署更大的项目、优化性能和增加容量。

某些层已针对特定类型的工作进行优化。 例如，“标准 3 高密度(S3 HD)”是 S3 的托管模式，其中的底层硬件已针对大量的较小索引进行优化，适用于多租户方案。 S3 HD 的每单位费用与 S3 相同，但硬件经过优化，可基于大量的小型索引快速读取文件。

与“标准”层相比，“存储优化”层以更低的每 TB 价格提供更大的存储容量。 主要弊端是查询延迟更高，应根据具体的应用程序要求确认这种延迟。 若要详细了解此层的性能注意事项，请参阅[性能和优化注意事项](search-performance-optimization.md)。

预配服务时，可以在[定价页](https://azure.microsoft.com/pricing/details/search/)、[Azure 认知搜索中的服务限制](search-limits-quotas-capacity.md)以及门户页上找到有关各个层的详细信息。

<a name="premium-features"></a>

## <a name="feature-availability-by-tier"></a>按层划分的功能可用性

大多数功能都适用于所有层，包括免费层。 在少数情况下，你选择的层会影响你实现功能的能力。 下表描述了与服务层相关的功能约束。

| 功能 | 限制 |
|---------|-------------|
| [索引器](search-indexer-overview.md) | 索引器在 S3 HD 上不可用。  |
| [AI 扩充](search-security-manage-encryption-keys.md) | 在免费层上运行，但不建议这样做。 |
| [客户托管的加密密钥](search-security-manage-encryption-keys.md) | 在免费层上不可用。 |
| [IP 防火墙访问](service-configure-firewall.md) | 在免费层上不可用。 |
| [专用终结点 (与 Azure Private Link 集成) ](service-create-private-endpoint.md) | 对于到搜索服务的入站连接，不能用于免费层。 对于通过索引器连接到其他 Azure 资源的出站连接，在免费或 S3 HD 上不可用。 对于使用技能集的索引器，不适用于免费、基本、S1 或 S3 HD 的索引器。|

资源密集型功能可能无法正常工作，除非您给予其足够的容量。 例如，[AI 扩充](cognitive-search-concept-intro.md)包含长时间运行的技能，除非数据集较小，否则这些技能在免费服务中会超时。

## <a name="billable-events"></a>计费事件

基于 Azure 认知搜索构建的解决方案可能会在以下方面产生成本：

+ [服务](#service-costs) 本身的成本，以最低配置 (一个分区和副本) 以基本速率运行。 可以将此视为运行服务的固定成本。

+  (副本或分区) 添加容量，其中成本以计费费率的增量递增

+ 带宽费用（出站数据传输）

+ 特定功能或特性所需的附加服务：

  + AI 扩充（需要[认知服务](https://azure.microsoft.com/pricing/details/cognitive-services/)）
  + 知识存储（需要 [Azure 存储](https://azure.microsoft.com/pricing/details/storage/)）
  + 增量扩充（需要 [Azure 存储](https://azure.microsoft.com/pricing/details/storage/)，适用于 AI 扩充）
  + 客户管理的密钥和双加密（需要 [Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/)）
  + 无 internet 访问模型的专用终结点 (需要 [Azure 专用链接](https://azure.microsoft.com/pricing/details/private-link/)) 

### <a name="service-costs"></a>服务成本

与可以“暂停”以避免产生费用的虚拟机或其他资源不同，Azure 认知搜索服务在专门供你使用的硬件上始终可用。 因此，创建服务是一个计费事件，该事件在创建该服务时开始，在删除该服务时结束。 

最低费用是采用计费费率的第一个搜索单位（1 个副本 x 1 个分区）。 在服务的整个生命周期内，此最低费用都是固定的，因为服务不能在低于此配置的组件上运行。 超出最低费用时，可以单独添加副本和分区。 通过副本和分区递增容量会增大费用，其计算公式如下：[(副本数 x 分区数 x 费率)](#search-units)，其中，收费费率取决于所选的定价层。

在估算搜索解决方案的费用时，请记住，定价和容量不是线性的。 （容量翻倍不是支付两倍的费用，而是要支付更高的费用）有关该公式的工作方式示例，请参阅[如何分配副本和分区](search-capacity-planning.md#how-to-allocate-replicas-and-partitions)。

### <a name="bandwidth-charges"></a>带宽费用

如果 Azure 数据源位于 Azure 认知搜索的不同区域，则使用 [索引器](search-indexer-overview.md) 可能会影响计费。 在这种情况下，将出站数据从 Azure 数据源移到 Azure 认知搜索可能会产生费用。 有关详细信息，请参阅相关 Azure 数据平台的定价页。

如果在数据所在的同一区域中创建 Azure 认知搜索服务，则可以完全消除数据流出费用。 下面是摘自[带宽定价页](https://azure.microsoft.com/pricing/details/bandwidth/)中的一些信息：

+ 入站数据： Microsoft 不会向 Azure 上的任何服务收取任何入站数据的费用。 

+ 出站数据：出站数据是指查询结果。 认知搜索不会对出站数据收费，但如果服务在不同区域中，则可能会产生来自 Azure 的出站费用。 这些费用实际上不是 Azure 认知搜索帐单的一部分。 之所以提到，是因为如果你要将结果发送到其他区域或非 Azure 应用，则可以看到反映在你的整体帐单中的这些成本。

### <a name="ai-enrichment-with-cognitive-services"></a>使用认知服务的 AI 扩充

对于 [AI 扩充](cognitive-search-concept-intro.md)，应该计划在 S0 定价层上的 Azure 认知搜索所在的同一区域中[附加一个计费的认知服务资源](cognitive-search-attach-cognitive-services.md)，用于即用即付处理。 附加认知服务不会产生固定的费用。 只需支付所需的处理费。

| 操作 | 计费影响 |
|-----------|----------------|
| 文档破解、文本提取 | 免费 |
| 文档破解、图像提取 | 根据从文档中提取的图像数计费。 在 [索引器配置](/rest/api/searchservice/create-indexer#indexer-parameters)中，**imageAction** 是触发图像提取的参数。 如果 **imageAction** 设置为“none”（默认值），则不收取图像提取费用。 Azure 认知搜索的[定价详细信息](https://azure.microsoft.com/pricing/details/search/)页上阐述了图像提取费率。|
| [内置认知技能](cognitive-search-predefined-skills.md) | 计费费率与直接使用认知服务执行任务的费率相同。 |
| 自定义技能 | 自定义技能是你提供的功能。 使用自定义技能的费用完全取决于自定义代码是否调用其他计量的服务。 |

可以利用[增量扩充（预览版）](cognitive-search-incremental-indexing-conceptual.md)功能来提供缓存，使得在未来修改技能组后只需运行必要的认知技能，从而使索引器更高效，为你节省时间和金钱。

<a name="search-units"></a>

## <a name="billing-formula-r-x-p--su"></a>计费公式 (R x P = SU)

对于 Azure 认知搜索操作，要了解的最重要计费概念是搜索单位 (SU)。 由于 Azure 认知搜索必须同时使用副本和分区进行索引编制和查询，因此无法按其中的一个进行计费。 相反，应基于两者的组合来计费。

SU 是服务使用的副本数和分区数的乘积：  **(R x P = SU)** 。

每个服务至少从 1 个 SU（1 个分区乘以 1 个副本）开始。 任何服务的最大 SU 值为 36。 可通过多种方式来实现此最大值：例如，6 个分区 x 6 个副本，或 3 个分区 x 12 个副本。 通常使用小于总容量的值（例如，3 副本、3 分区的服务按 9 个 SU 计费）。 有关有效的组合，请参阅[分区和副本组合](search-capacity-planning.md#chart)图表。

费率按每个 SU、按小时计算。 每个层的费率渐进式提高。 层越高，分区越大且速度越快，因此，每小时的总费率更高。 可以在[定价详细信息](https://azure.microsoft.com/pricing/details/search/)页上查看每个层的费率。

大多数客户只是联机使用一部分总容量，将剩余的容器保持预留状态。 在计费方面，支付的每小时费用取决于联机的分区和副本数量（使用 SU 公式计算）。

## <a name="next-steps"></a>后续步骤

成本管理是容量规划的有机组成部分。 作为下一步，请继续阅读以下文章，了解有关如何估算容量和管理成本的指导。

> [!div class="nextstepaction"]
> [如何在 Azure 中管理成本和估计容量认知搜索](search-sku-manage-costs.md)