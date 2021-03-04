---
title: 为 Redis 实例配置企业 Azure 缓存的活动异地复制
description: 了解如何在 Azure 区域中为 Redis Enterprise 实例复制 Azure 缓存
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.author: yegu
ms.openlocfilehash: edf7a7cdbd24249205fedb4654aa092755700910
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102035647"
---
# <a name="configure-active-geo-replication-for-enterprise-azure-cache-for-redis-instances-preview"></a>为 Redis 实例配置适用于企业 Azure 缓存的活动异地复制 (预览) 

本文介绍如何使用 Azure 门户配置活动异地复制的 Azure 缓存。

活动异地复制将 Redis 实例的两个或更多企业 Azure 缓存合并到跨 Azure 区域的单个缓存中。 所有实例都作为本地主副本。 应用程序决定哪个实例 () 用于读取和写入请求。

## <a name="create-or-join-an-active-geo-replication-group"></a>创建或加入活动异地复制组

> [!IMPORTANT]
> 创建用于 Redis 的 Azure 缓存时，必须启用活动异地复制。
>
>

1. 在 **新的 Redis 缓存** 创建 UI 中，单击 "**配置**"，在 "**高级**" 选项卡中设置 **活动异地复制**。

    ![配置活动异地复制](./media/cache-how-to-active-geo-replication/cache-active-geo-replication-not-configured.png)

1. 为第一个缓存实例创建新的复制组，或从列表中选择一个现有复制组。

    ![链接缓存](./media/cache-how-to-active-geo-replication/cache-active-geo-replication-new-group.png)

1. 单击 " **配置** " 完成操作。

    ![已配置活动异地复制](./media/cache-how-to-active-geo-replication/cache-active-geo-replication-configured.png)

1. 针对异地复制组中的每个附加缓存实例重复上述步骤。

## <a name="remove-from-an-active-geo-replication-group"></a>从活动异地复制组中删除

若要从活动异地复制组中删除缓存实例，只需删除该实例。 其余实例将自动重新配置自身。

## <a name="next-steps"></a>后续步骤

了解有关 Azure Cache for Redis 功能的详细信息。

* [Azure Cache for Redis 服务层](cache-overview.md#service-tiers)
* [Azure Cache for Redis 的高可用性](cache-high-availability.md)
