---
title: 选择一个定价层
titleSuffix: Azure Cognitive Search
description: 了解 Azure 认知搜索 (或 Sku) 的定价层。 可以在以下级别预配搜索服务：免费版、基本版和标准版。 标准在各种资源配置和容量级别中可用。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/15/2021
ms.custom: contperf-fy21q2
ms.openlocfilehash: 38ddfc2d3940bb9267edd6c5c683918c1fb5dc58
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2021
ms.locfileid: "98251688"
---
# <a name="choose-a-pricing-tier-for-azure-cognitive-search"></a>选择 Azure 认知搜索的定价层

当你 [创建搜索服务](search-create-service-portal.md)时，你可以选择定价层 (或 SKU) ，该定价层在服务的生存期内是固定的。 估计的每月费用显示在门户的 " **选择定价层** " 页中。 如果要通过 PowerShell 或 Azure CLI 来创建服务，则通过参数指定层 **`-Sku`** 。

选择的层决定：

+ 可以在服务上创建的索引和其他对象的最大数目
+ 分区（物理存储）的大小和速度
+ 按月固定费率收费，同时增加容量

在少数情况下，你选择的层决定了 [高级功能](#premium-features)的可用性。

## <a name="tier-descriptions"></a>层说明

层包括 **免费**、 **基本**、 **标准** 和 **优化存储**。 “标准”和“存储优化”提供多种配置和容量。 以下 Azure 门户屏幕截图显示了可用的层，其中不包括定价层（可在门户中和[定价页](https://azure.microsoft.com/pricing/details/search/)上找到该层）。 

:::image type="content" source="media/search-sku-tier/tiers.png" alt-text="定价层图表" border="true":::

**免费** 为较小的项目创建受限的搜索服务，如运行教程和代码示例。 在内部，系统资源在多个订阅服务器之间共享。 不能缩放免费服务，也不能运行繁重的工作负荷。

"**基本**" 和 "**标准**" 是最常用的可计费层，其默认 **值为默认** 值，因为它可让你更灵活地缩放工作负荷。 通过控制下的专用资源，你可以部署更大的项目、优化性能和增加容量。

一些层专为某些类型的工作而设计。 例如，“标准 3 高密度(S3 HD)”是 S3 的托管模式，其中的底层硬件已针对大量的较小索引进行优化，适用于多租户方案。 S3 HD 的每单位费用与 S3 相同，但硬件经过优化，可基于大量的小型索引快速读取文件。

与“标准”层相比，“存储优化”层以更低的每 TB 价格提供更大的存储容量。 主要弊端是查询延迟更高，应根据具体的应用程序要求确认这种延迟。 若要详细了解此层的性能注意事项，请参阅[性能和优化注意事项](search-performance-optimization.md)。

预配服务时，可以在[定价页](https://azure.microsoft.com/pricing/details/search/)、[Azure 认知搜索中的服务限制](search-limits-quotas-capacity.md)以及门户页上找到有关各个层的详细信息。

<a name="premium-features"></a>

## <a name="feature-availability-by-tier"></a>按层划分的功能可用性

大多数功能都适用于所有层，包括免费层。 在少数情况下，你选择的层会影响你实现功能的能力。 下表描述了与服务层相关的功能约束。

| 功能 | 限制 |
|---------|-------------|
| [索引器](search-indexer-overview.md) | 索引器在 S3 HD 上不可用。  |
| [AI 扩充](search-security-manage-encryption-keys.md) | 在免费层上运行，但不建议这样做。 |
| [) 访问的出站 (索引器的托管或可信标识](search-howto-managed-identities-data-sources.md) | 在免费层上不可用。|
| [客户托管的加密密钥](search-security-manage-encryption-keys.md) | 在免费层上不可用。 |
| [IP 防火墙访问](service-configure-firewall.md) | 在免费层上不可用。 |
| [专用终结点 (与 Azure Private Link 集成) ](service-create-private-endpoint.md) | 对于到搜索服务的入站连接，不能用于免费层。 对于通过索引器连接到其他 Azure 资源的出站连接，在免费或 S3 HD 上不可用。 对于使用技能集的索引器，不适用于免费、基本、S1 或 S3 HD 的索引器。|

资源密集型功能可能无法正常工作，除非您给予其足够的容量。 例如，[AI 扩充](cognitive-search-concept-intro.md)包含长时间运行的技能，除非数据集较小，否则这些技能在免费服务中会超时。

## <a name="upper-limits"></a>上限

层确定服务本身的最大存储量，以及可创建的最大索引数、索引器、数据源、技能集和同义词映射。 有关所有限制的完全中断，请参阅 [Azure 认知搜索中的服务限制](search-limits-quotas-capacity.md)。 

## <a name="partition-size-and-speed"></a>分区大小和速度

层定价包括每分区存储的详细信息，范围为从 2 GB 到基本，最高可达 2 TB 的存储优化 (L2) 层。 其他硬件特征（如操作速度、延迟和传输率）未发布，但设计用于特定解决方案体系结构的层构建在具有支持这些方案的功能的硬件上。

## <a name="billing-rates"></a>计费费率

层具有不同的计费费率，在更昂贵的硬件上运行更高的层，或者提供更昂贵的功能。 计费率是 Azure 认知搜索每个服务层的 [azure 定价页](https://azure.microsoft.com/pricing/details/search/) 中所显示的内容。

一旦你创建了服务，计费费率就会成为在时钟附近运行服务的 *固定成本* ，如果你选择添加更多容量，则会产生 *增量成本* 。

搜索服务以存储) 的 *分区* (的形式分配计算资源，并在查询引擎) *实例 (的* 实例。 最初，使用每个服务创建一个服务，计费费率包含在这两个资源中。 但是，如果你缩放容量，则成本会按计费费率的增量增加或减少。

下面的示例进行了这方面的演示。 假设每月的假设计费率为 $100。 如果将搜索服务保持在一个分区和一个副本的初始容量范围内，则 $100 将是你在月底需要支付的费用。 但是，如果你再添加两个副本以实现高可用性，则每月费用将增加到 $300 ($100 （对于第一个副本分区对），后跟 $200 两个副本) 。

此定价模型基于搜索服务使用的 (SU) 的编号 *搜索单位* 的计费费率。 所有服务最初预配在一个 SU 上，但你可以通过添加分区或副本来处理更大的工作负荷，从而增加 SUs。 有关详细信息，请参阅 [如何估算搜索服务的成本](search-sku-manage-costs.md)。

## <a name="next-steps"></a>后续步骤

选择定价层的最佳方式是从最小成本层开始，然后允许经验和测试通知您决定保留服务还是在更高的层创建新的服务。 在接下来的步骤中，我们建议你在可容纳你建议的测试级别的层上创建一个搜索服务，然后查看以下指南以获取有关评估成本和容量的建议。

+ [创建搜索服务](search-create-service-portal.md)
+ [估算成本](search-sku-manage-costs.md)
+ [估计容量](search-sku-manage-costs.md)