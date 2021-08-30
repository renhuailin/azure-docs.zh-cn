---
title: 选择一个定价层
titleSuffix: Azure Cognitive Search
description: 了解 Azure 认知搜索的定价层（或 SKU）。 可在以下层预配搜索服务：免费层、基本层和标准层。 标准层在各种资源配置和容量级别中均可用。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/26/2021
ms.custom: contperf-fy21q2
ms.openlocfilehash: e62f2a07b4266671bb055e6a672e13f69e2a0bab
ms.sourcegitcommit: 7c44970b9caf9d26ab8174c75480f5b09ae7c3d7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2021
ms.locfileid: "112982809"
---
# <a name="choose-a-pricing-tier-for-azure-cognitive-search"></a>选择 Azure 认知搜索的定价层

[创建搜索服务](search-create-service-portal.md)的一部分意味着选择定价层（或 SKU），该定价层（或 SKU）在服务的生存期内是固定的。 在门户中创建服务时，在“选择定价层”页中指定层级。 如果要通过 PowerShell 或 Azure CLI 预配，则通过 `-Sku` 参数指定层级

你选择的层级决定了以下事项：

+ 服务上允许的最大索引数和其他对象数
+ 分区（物理存储）的大小和速度
+ 按计费费率计费，每月成本是固定的，但在添加容量的情况下成本会增加

在一些实例中，选择的层级决定了[高级版功能](#premium-features)的可用性。

定价（或运行服务的每月预估成本）在门户的“选择定价层”页中显示。 应查看[服务定价](https://azure.microsoft.com/pricing/details/search/)，了解估计成本。

> [!NOTE]
> 正在查找有关 Azure SKU 的详细信息？ 从 [Azure 定价](https://azure.microsoft.com/pricing/)开始，然后向下滚动查看每服务定价页面的链接。

## <a name="tier-descriptions"></a>层级说明

层级包括“免费”、“基本”、“标准”和“存储优化”。    “标准”和“存储优化”提供多种配置和容量。 以下 Azure 门户屏幕截图显示了可用的层，其中不包括定价层（可在门户中和[定价页](https://azure.microsoft.com/pricing/details/search/)上找到该层）。 

:::image type="content" source="media/search-sku-tier/tiers.png" alt-text="定价图" border="true":::

“免费”层可用于为较小的项目（例如，运行教程和代码示例）创建有限的搜索服务。 在内部，系统资源会在多个订阅服务器之间共享。 不能缩放免费服务，也不能运行繁重的工作负荷。

基本层和标准层是最常用的计费层级，其中默认为标准层，因为它使你能够更灵活地缩放工作负荷  。 使用受你控制的专用资源，你可以部署较大的项目，优化性能并增大容量。

有些层级是为某些类型的工作而设计的。 例如，“标准 3 高密度(S3 HD)”是 S3 的托管模式，其中的底层硬件已针对大量的较小索引进行优化，适用于多租户方案。 S3 HD 的每单位费用与 S3 相同，但硬件经过优化，可基于大量的小型索引快速读取文件。

与“标准”层相比，“存储优化”层以更低的每 TB 价格提供更大的存储容量。 主要弊端是查询延迟更高，应根据具体的应用程序要求确认这种延迟。 若要详细了解此层的性能注意事项，请参阅[性能和优化注意事项](search-performance-optimization.md)。

预配服务时，可以在[定价页](https://azure.microsoft.com/pricing/details/search/)、[Azure 认知搜索中的服务限制](search-limits-quotas-capacity.md)以及门户页上找到有关各个层的详细信息。

<a name="premium-features"></a>

## <a name="feature-availability-by-tier"></a>按层划分的功能可用性

大多数功能在所有层级（包括免费层）都可用。 在少数情况下，你选择的层级会影响你实现功能的能力。 下表介绍了与服务层级相关的功能约束。

| 功能 | 限制 |
|---------|-------------|
| [索引器](search-indexer-overview.md) | 索引器在 S3 HD 上不可用。  |
| [AI 扩充](cognitive-search-concept-intro.md) | 在免费层上运行，但不建议这样做。 |
| [用于出站（索引器）访问的托管或受信任标识](search-howto-managed-identities-data-sources.md) | 在免费层上不可用。|
| [客户托管的加密密钥](search-security-manage-encryption-keys.md) | 在免费层上不可用。 |
| [IP 防火墙访问](service-configure-firewall.md) | 在免费层上不可用。 |
| [专用终结点（与 Azure 专用链接集成）](service-create-private-endpoint.md) | 对于到搜索服务的入站连接，在免费层上不可用。 对于通过索引器连接到其他 Azure 资源的出站连接，在免费层或 S3 HD 上不可用。 对于使用技能组的索引器，在免费层、基本层、S1 或 S3 HD 上不可用。| 
| [可用性区域](search-performance-optimization.md) | 在免费层或基本层上不可用。 |
| [语义搜索（预览）] | 在免费层或基本层上不可用。 |

资源密集型功能可能无法正常运行，除非你为其提供足够的容量。 例如，[AI 扩充](cognitive-search-concept-intro.md)包含长时间运行的技能，除非数据集较小，否则这些技能在免费服务中会超时。

## <a name="upper-limits"></a>上限

层级决定服务本身的最大存储，以及可创建的索引、索引器、数据源、技能组和同义词映射的最大数量。 如需详细了解所有限制，请参阅 [Azure 认知搜索中的服务限制](search-limits-quotas-capacity.md) 

## <a name="partition-size-and-speed"></a>分区大小和速度

层定价包括关于每分区存储的详细信息，范围从基本层 2 GB 到存储优化 (L2) 层 2 TB 不等。 未发布其他硬件特性（如操作速度、延迟和传输率），但在具有支持这些方案的功能的硬件上构建了设计用于特定解决方案体系结构的层级。 有关分区的详细信息，请参阅[估计和管理容量](search-capacity-planning.md)和[缩放以提高性能](search-performance-optimization.md)。

## <a name="billing-rates"></a>计费费率

不同层级的计费费率也不同，层级所在的硬件越昂贵，或者提供的功能越昂贵，费率就越高。 可在 Azure 认知搜索的 [Azure 定价页面](https://azure.microsoft.com/pricing/details/search/)中查看每个层级的计费费率。

创建服务后，计费费率将成为 24 小时运行服务的固定成本，如果选择添加更多容量，则会产生增量成本 。

搜索服务以分区（用于存储）和副本（查询引擎实例）的形式分配计算资源 。 最初，使用其中一种资源创建服务，并且计费费率包含这两种资源。 但是，如果缩放容量，则成本会以计费费率的增量增加或减少。

下面的示例进行了这方面的演示。 假定每月的计费费率为 100 美元。 如果将搜索服务的初始容量保持在一个分区和一个副本，则月底你可能需要支付 100 美元的费用。 但是，如果添加两个副本来获得高可用性，则每月费用将增加到 300 美元（第一个副本分区对的费用为 100 美元，后两个副本的费用为 200 美元）。

此定价模型基于将计费费率应用到搜索服务使用的数字搜索单位 (SU) 的概念。 所有服务最初预配在一个 SU 上，但可通过添加分区或副本来处理更大的工作负荷，从而增加 SU。 有关详细信息，请参阅[如何估计搜索服务的成本](search-sku-manage-costs.md)。

## <a name="next-steps"></a>后续步骤

选择定价层的最佳方法是从最低成本层级开始，然后通过体验和测试来决定是保留服务还是在更高层级创建新服务。 在接下来的步骤中，建议你在一个层级上创建一个搜索服务（该层级可适应你打算进行的测试级别），然后查看以下指南，获得关于估计成本和容量的建议。

+ [创建搜索服务](search-create-service-portal.md)
+ [估算成本](search-sku-manage-costs.md)
+ [估计容量](search-sku-manage-costs.md)