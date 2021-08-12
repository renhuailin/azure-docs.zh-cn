---
title: 容器映像存储
description: 详述如何在 Azure 容器注册表中存储容器映像和其他项目，包括安全性、冗余和容量。
ms.topic: conceptual
ms.date: 03/24/2021
ms.custom: references_regions
ms.openlocfilehash: a9c8ec877ddb17603e82b763223278a2e5e36714
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105047739"
---
# <a name="container-image-storage-in-azure-container-registry"></a>Azure 容器注册表中的容器映像存储

每个[基本、标准和高级](container-registry-skus.md) Azure 容器注册表均受益于包括静态加密在内的高级 Azure 存储功能。 以下部分介绍 Azure 容器注册表 (ACR) 中的映像存储的功能和限制。

## <a name="encryption-at-rest"></a>静态加密

注册表中的所有容器映像和其他项目都已静态加密。 Azure 在存储映像之前自动对其进行加密，当应用程序和服务请求映像时即时对其进行解密。 可选地使用[客户管理的密钥](container-registry-customer-managed-keys.md)应用额外的加密层。

## <a name="regional-storage"></a>区域存储

Azure 容器注册表将数据存储在创建注册表的区域内，以帮助客户满足数据驻留和合规性要求。 在除巴西南部和东南亚以外的所有区域中，Azure 还可将注册表数据存储在同一地理区域的配对区域中。 在巴西南部和东南亚地区，根据相应的数据驻留要求，注册表数据一律受限于该区域。

如果发生区域性服务中断，注册表数据可能会变得不可用，并且不会自动恢复。 如果客户希望将其注册表数据存储在多个区域，以便在不同的地理位置获得更好的性能，或在发生区域性服务中断时希望具有复原能力，则应启用[异地复制](container-registry-geo-replication.md)。

## <a name="geo-replication"></a>异地复制

对于需要高可用性保证的场景，请考虑使用高级注册表的[异地复制](container-registry-geo-replication.md)功能。 异地复制可帮助在发生区域性故障时，防止丢失对注册表的访问权限。 异地复制还提供了其他好处，如临近网络映像存储，以便在分布式开发或部署方案中实现更快地推送和拉取。

## <a name="zone-redundancy"></a>区域冗余

若要创建可复原的高可用性 Azure 容器注册表，可以选择在所选的 Azure 区域中启用[区域冗余](zone-redundancy.md)。 区域冗余是高级服务层级的一项功能，它使用 Azure [可用性区域](../availability-zones/az-overview.md)将注册表复制到每个启用的地区中至少三个单独的区域。 将异地复制和区域冗余结合起来，提高注册表的可靠性和性能。 

## <a name="scalable-storage"></a>可缩放存储

Azure 容器注册表允许你根据需要创建任意数量的存储库、映像、层或标记，直到达到[注册表存储限制](container-registry-skus.md#service-tier-features-and-limits)为止。 

大量的存储库和标记可能会影响注册表的性能。 按照注册表维护例程定期删除不使用的存储库、标记和映像，并有选择地为未标记的清单设置[保留策略](container-registry-retention-policy.md)。 已删除的注册表资源（如存储库、映像和标记）在删除后无法恢复。 有关删除注册表资源的详细信息，请参阅[删除 Azure 容器注册表中的容器映像](container-registry-delete.md)。

## <a name="storage-cost"></a>存储成本

有关定价的完整详细信息，请参阅 [Azure 容器注册表定价][pricing]。

## <a name="next-steps"></a>后续步骤

有关基本、标准和高级容器注册表的详细信息，请参阅 [Azure 容器注册表服务层级](container-registry-skus.md)。

<!-- IMAGES -->

<!-- LINKS - External -->
[portal]: https://portal.azure.com
[pricing]: https://aka.ms/acr/pricing

<!-- LINKS - Internal -->
