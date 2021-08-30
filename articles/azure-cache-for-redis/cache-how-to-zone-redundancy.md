---
title: 启用适用于 Azure Cache for Redis 的区域冗余
description: 了解如何为高级层和企业层 Azure Cache for Redis 实例设置区域冗余
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: 522cf0ad115e1149d7746ecadef96ae9494a1d8c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121744943"
---
# <a name="enable-zone-redundancy-for-azure-cache-for-redis"></a>启用适用于 Azure Cache for Redis 的区域冗余
本文将介绍如何使用 Azure 门户配置区域冗余 Azure 缓存实例。

Azure Cache for Redis 标准层、高级层和企业层通过将每个缓存托管在两个专用虚拟机 (VM) 上来提供内置冗余。 即使这些 VM 位于单独的 [Azure 容错域和更新域](../virtual-machines/availability.md)中且高度可用，它们也容易受到数据中心级别故障的影响。 Azure Cache for Redis 还支持其高级层和企业层中的区域冗余。 区域冗余缓存在分布于多个[可用性区域](../availability-zones/az-overview.md)间的 VM 上运行。 它提供更高的复原能力和可用性。

> [!NOTE]
> Azure 可用性区域之间的数据传输将按标准[带宽费率](https://azure.microsoft.com/pricing/details/bandwidth/)收费。

## <a name="prerequisites"></a>先决条件
* Azure 订阅 - [创建免费帐户](https://azure.microsoft.com/free/)

## <a name="create-a-cache"></a>创建缓存
若要创建缓存，请执行以下步骤：

1. 登录到 [Azure 门户](https://portal.azure.com)，然后选择“创建资源”。
  
1. 在“新建”页上选择“数据库”，然后选择“Azure Cache for Redis”。

    :::image type="content" source="media/cache-create/new-cache-menu.png" alt-text="选择 Azure Cache for Redis。":::
   
1. 在“基本信息”页面上，配置新缓存的设置。
   
    | 设置      | 建议的值  | 说明 |
    | ------------ |  ------- | -------------------------------------------------- |
    | **订阅** | 选择订阅。 | 要在其下创建此新的 Azure Cache for Redis 实例的订阅。 | 
    | **资源组** | 选择一个资源组，或者选择“新建”并输入新的资源组名称。 | 要在其中创建缓存和其他资源的资源组的名称。 将所有应用资源放入一个资源组可以轻松地统一管理或删除这些资源。 | 
    | **DNS 名称** | 输入任何全局唯一的名称。 | 缓存名称必须是包含 1 到 63 个字符的字符串，只能包含数字、字母或连字符。 该名称必须以数字或字母开头和结尾，且不能包含连续的连字符。 缓存实例的主机名是 *\<DNS name> .redis.cache.windows.net*。 | 
    | **位置** | 选择一个位置。 | 选择与要使用该缓存的其他服务靠近的[区域](https://azure.microsoft.com/regions/)。 |
    | **缓存类型** | 选择[高级层或企业层](https://azure.microsoft.com/pricing/details/cache/)缓存。 |  定价层决定可用于缓存的大小、性能和功能。 有关详细信息，请参阅[用于 Redis 的 Azure 缓存概述](cache-overview.md)。 |
   
1. 在“高级”页上，对于高级层缓存，请选择“副本计数” 。
   
    :::image type="content" source="media/cache-how-to-multi-replicas/create-multi-replicas.png" alt-text="副本计数":::

1. 选择“可用性区域”。 
   
    :::image type="content" source="media/cache-how-to-zone-redundancy/create-zones.png" alt-text="可用性区域":::

1. 配置群集和/或 RDB 持久性的设置。  

    > [!NOTE]
    > 目前，区域冗余不支持 AOF 暂留，也不兼容异地复制。
    >

1. 选择“创建”。 
   
    创建缓存需要花费片刻时间。 可以在 Azure Cache for Redis 的“概述”页上监视进度。  如果“状态”显示为“正在运行”，则表示该缓存可供使用。 
   
    > [!NOTE]
    > 创建缓存后，无法更改或启用可用性区域。 
    >

## <a name="zone-redundancy-faq"></a>区域冗余常见问题解答

- [为什么在创建高级缓存时无法启用区域冗余？](#why-cant-i-enable-zone-redundancy-when-creating-a-premium-cache)
- [为什么在缓存创建过程中无法选择所有三个区域？](#why-cant-i-select-all-three-zones-during-cache-create)
- [是否可以更新现有的高级缓存以使用区域冗余？](#can-i-update-my-existing-premium-cache-to-use-zone-redundancy)
- [跨 Azure 可用性区域复制数据的费用是多少？](#how-much-does-it-cost-to-replicate-my-data-across-azure-availability-zones)

### <a name="why-cant-i-enable-zone-redundancy-when-creating-a-premium-cache"></a>为什么在创建高级缓存时无法启用区域冗余？

区域冗余只在具有可用性区域的 Azure 区域中可用。 请参阅[具有可用性区域的 Azure 区域](../availability-zones/az-region.md#azure-services-supporting-availability-zones)，以获取最新列表。

### <a name="why-cant-i-select-all-three-zones-during-cache-create"></a>为什么在缓存创建过程中无法选择所有三个区域？

在默认情况下，高级缓存有一个主要节点和一个副本节点。 若要为两个以上可用性区域配置区域冗余，需要将[更多副本](cache-how-to-multi-replicas.md)添加到要创建的缓存。

### <a name="can-i-update-my-existing-premium-cache-to-use-zone-redundancy"></a>是否可以更新现有的高级缓存以使用区域冗余？

不可以，目前不支持此操作。

### <a name="how-much-does-it-cost-to-replicate-my-data-across-azure-availability-zones"></a>跨 Azure 可用性区域复制数据的费用是多少？

在配置了多个可用性区域的情况下使用区域冗余时，数据会从一个区域中的主缓存节点复制到另一个区域中的其他节点。 数据传输费用是在所选可用性区域间移动的数据的网络流出费用。 有关详细信息，请参阅[带宽定价详细信息](https://azure.microsoft.com/pricing/details/bandwidth/)。

## <a name="next-steps"></a>后续步骤
了解有关 Azure Cache for Redis 功能的详细信息。

> [!div class="nextstepaction"]
> [Azure Cache for Redis 高级服务层](cache-overview.md#service-tiers)
