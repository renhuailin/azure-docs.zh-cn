---
title: 为 Azure Cache for Redis 企业实例配置活动异地复制
description: 了解如何跨 Azure 区域复制 Azure Cache for Redis 企业实例
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.author: yegu
ms.openlocfilehash: fe777c3aa7b314dc56a42cc64712d18281a6ea7d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102121161"
---
# <a name="configure-active-geo-replication-for-enterprise-azure-cache-for-redis-instances-preview"></a>为 Azure Cache for Redis 企业实例配置活动异地复制（预览）

本文介绍了如何使用 Azure 门户配置活动异地复制的 Azure 缓存。

活动异地复制将两个或多个 Azure Cache for Redis 企业实例分组到一个跨 Azure 区域的缓存中。 所有实例都作为本地主缓存。 应用程序决定将哪些实例用于读取和写入请求。

## <a name="create-or-join-an-active-geo-replication-group"></a>创建或加入活动异地复制组

> [!IMPORTANT]
> 必须在创建 Azure Cache for Redis 时启用活动异地复制。
>
>

1. 在“新建 Redis 缓存”UI 中，单击“配置”以在“高级”选项卡中设置“活动异地复制”。

    ![配置活动异地复制](./media/cache-how-to-active-geo-replication/cache-active-geo-replication-not-configured.png)

1. 为第一个缓存实例新建复制组，或从列表中选择一个现有复制组。

    ![链接缓存](./media/cache-how-to-active-geo-replication/cache-active-geo-replication-new-group.png)

1. 单击“配置”来完成操作。

    ![已配置活动异地复制](./media/cache-how-to-active-geo-replication/cache-active-geo-replication-configured.png)

1. 对异地复制组中的每个附加缓存实例重复执行上述步骤。

## <a name="remove-from-an-active-geo-replication-group"></a>从活动异地复制组中删除

若要从活动异地复制组中删除缓存实例，只需删除相应实例即可。 其余实例将会自动重新配置自身。

## <a name="next-steps"></a>后续步骤

了解有关 Azure Cache for Redis 功能的详细信息。

* [Azure Cache for Redis 服务层](cache-overview.md#service-tiers)
* [Azure Cache for Redis 的高可用性](cache-high-availability.md)
