---
title: 为 Azure Cache for Redis 企业实例配置活动异地复制
description: 了解如何跨 Azure 区域复制 Azure Cache for Redis 企业实例
author: curib
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.author: cauribeg
ms.openlocfilehash: 99169aaacf0ddd39fb4213ee6c1d83c05a71d532
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129538480"
---
# <a name="configure-active-geo-replication-for-enterprise-azure-cache-for-redis-instances-preview"></a>为 Azure Cache for Redis 企业实例配置活动异地复制（预览）

本文介绍了如何使用 Azure 门户配置活动异地复制的 Azure 缓存。

活动异地复制将最多 5 个 Enterprise Azure Cache for Redis 实例分组到一个跨 Azure 区域的缓存中。 所有实例都充当本地主缓存。 应用程序决定将哪个或哪些实例用于读取和写入请求。

> [!NOTE]
> Azure 区域之间的数据传输将按标准[带宽费率](https://azure.microsoft.com/pricing/details/bandwidth/)收费。

## <a name="create-or-join-an-active-geo-replication-group"></a>创建或加入活动异地复制组

> [!IMPORTANT]
> 必须在创建 Azure Cache for Redis 时启用活动异地复制。
>
>

1. 在“新建 Redis 缓存”创建 UI 的“高级”选项卡中，为“群集策略”选择“企业”   。

    ![配置活动异地复制](./media/cache-how-to-active-geo-replication/cache-active-geo-replication-not-configured.png)

1. 选择“配置”以设置“活动异地复制” 。

1. 为第一个缓存实例新建复制组，或从列表中选择一个现有复制组。

    ![链接缓存](./media/cache-how-to-active-geo-replication/cache-active-geo-replication-new-group.png)

1. 选择“配置”以完成。

    ![已配置活动异地复制](./media/cache-how-to-active-geo-replication/cache-active-geo-replication-configured.png)

1. 等待第一个缓存创建成功。 对异地复制组中的每个附加缓存实例重复执行上述步骤。

## <a name="remove-from-an-active-geo-replication-group"></a>从活动异地复制组中删除

若要从活动异地复制组中删除缓存实例，只需删除相应实例即可。 其余实例将会自动重新配置自身。

## <a name="next-steps"></a>后续步骤

了解有关 Azure Cache for Redis 功能的详细信息。

* [Azure Cache for Redis 服务层](cache-overview.md#service-tiers)
* [Azure Cache for Redis 的高可用性](cache-high-availability.md)
