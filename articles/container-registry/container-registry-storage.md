---
title: 容器映像存储
description: 有关容器映像和其他项目在 Azure 容器注册表中的存储方式的详细信息，包括安全、冗余和容量。
ms.topic: article
ms.date: 03/02/2021
ms.custom: references_regions
ms.openlocfilehash: 4bdffd111273e00b796e45f4e09bfac9ba6713e4
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102036004"
---
# <a name="container-image-storage-in-azure-container-registry"></a>Azure 容器注册表中的容器映像存储

每个[基本、标准和高级](container-registry-skus.md) Azure 容器注册表均受益于高级 Azure 存储功能，如静态加密（以确保映像数据安全）和地域冗余（以实现映像数据保护）。 以下部分介绍 Azure 容器注册表 (ACR) 中映射存储的功能和限制。

## <a name="encryption-at-rest"></a>静态加密

注册表中的所有容器映像和其他项目都静态加密。 Azure 在存储映像之前自动对其进行加密，当应用程序和服务请求映像时即时对其进行解密。 （可选）使用 [客户管理的密钥](container-registry-customer-managed-keys.md)应用额外的加密层。

## <a name="geo-redundant-storage"></a>异地冗余存储

对于在大多数区域中部署的容器注册表，Azure 使用异地冗余存储方案来帮助防止丢失容器映像和其他项目。 Azure 容器注册表会自动将容器映像复制到多个地理位置较远的数据中心，以防在出现区域存储故障时丢失它们。

> [!IMPORTANT]
> * 如果发生区域存储故障，只能通过联系 Azure 支持人员恢复注册表数据。 
> * 由于巴西南部和东南亚的数据派驻要求，这些区域中的 Azure 容器注册表数据仅存储在 [本地地域](https://azure.microsoft.com/global-infrastructure/geographies/)。 对于东南亚，所有数据都存储在新加坡。 对于巴西南部，所有数据都存储在巴西。 当区域由于严重的灾难而丢失时，Microsoft 将无法恢复 Azure 容器注册表数据。

## <a name="geo-replication"></a>异地复制

对于需要更高可用性保证的方案，请考虑使用高级注册表的[异地复制](container-registry-geo-replication.md)功能。 异地复制可帮助在全部区域失败（而不仅仅是一个存储失败）时，防止丢失对注册表的访问权限。 异地复制还提供了其他好处，如临近网络映像存储，以便在分布式开发或部署方案中实现更快地推送和拉取。

## <a name="zone-redundancy"></a>区域冗余

若要创建弹性和高可用性 Azure 容器注册表，可以选择在选择的 Azure 区域中启用 [区域冗余](zone-redundancy.md) 。 "高级" 服务层的一项功能，区域冗余使用 Azure [可用性区域](../availability-zones/az-overview.md) 将注册表复制到每个已启用区域中至少三个单独的区域。 将异地复制和区域冗余结合起来，提高注册表的可靠性和性能。 

## <a name="scalable-storage"></a>可缩放存储

Azure 容器注册表允许你根据需要创建任意数量的存储库、映像、层或标记，直到达到[注册表存储限制](container-registry-skus.md#service-tier-features-and-limits)为止。 

大数量的存储库和标记会影响注册表性能。 按照注册表维护例程定期删除不使用的存储库、标记和映像，并有选择地为未标记的清单设置[保留策略](container-registry-retention-policy.md)。 已删除的注册表资源（如存储库、映像和标记）在删除后无法恢复。 有关删除注册表资源的详细信息，请参阅[删除 Azure 容器注册表中的容器映像](container-registry-delete.md)。

## <a name="storage-cost"></a>存储成本

有关定价的完整详细信息，请参阅 [Azure 容器注册表定价][pricing]。

## <a name="next-steps"></a>后续步骤

有关基本、标准和高级容器注册表的详细信息，请参阅 [Azure 容器注册表服务层级](container-registry-skus.md)。

<!-- IMAGES -->

<!-- LINKS - External -->
[portal]: https://portal.azure.com
[pricing]: https://aka.ms/acr/pricing

<!-- LINKS - Internal -->
