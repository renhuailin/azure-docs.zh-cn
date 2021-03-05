---
title: 容器映像存储
description: 有关容器映像和其他项目在 Azure 容器注册表中的存储方式的详细信息，包括安全、冗余和容量。
ms.topic: article
ms.date: 03/03/2021
ms.custom: references_regions
ms.openlocfilehash: ec4328b44d5493b8d765fa30c548adc3d747d446
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2021
ms.locfileid: "102183261"
---
# <a name="container-image-storage-in-azure-container-registry"></a>Azure 容器注册表中的容器映像存储

每个 [基本、标准和高级](container-registry-skus.md) azure 容器注册表都受益于高级 azure 存储功能，包括静态加密。 以下部分介绍 Azure 容器注册表 (ACR) 中的映像存储的功能和限制。

## <a name="encryption-at-rest"></a>静态加密

注册表中的所有容器映像和其他项目都静态加密。 Azure 在存储映像之前自动对其进行加密，当应用程序和服务请求映像时即时对其进行解密。 （可选）使用 [客户管理的密钥](container-registry-customer-managed-keys.md)应用额外的加密层。

## <a name="regional-storage"></a>区域存储

Azure 容器注册表将数据存储在创建注册表的区域，以帮助客户满足数据驻留和符合性要求。

为了帮助防止数据中心中断，某些区域提供了 [区域冗余](zone-redundancy.md)，其中的数据在特定区域的多个数据中心之间进行复制。

如果客户想要将其数据存储在多个区域，以便在不同的地理位置获得更好的性能，或者在发生区域性服务中断时希望具有复原能力的客户，则应启用 [异地复制](container-registry-geo-replication.md)。

## <a name="geo-replication"></a>异地复制

对于需要高可用性保证的方案，请考虑使用高级注册表的 [异地复制](container-registry-geo-replication.md) 功能。 异地复制有助于防止在发生区域性故障时对注册表的访问权限。 异地复制还提供了其他好处，如临近网络映像存储，以便在分布式开发或部署方案中实现更快地推送和拉取。

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
