---
title: 将副本添加到 Azure Cache for Redis
description: 了解如何向 Azure Cache for Redis 高级层实例添加更多副本
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: 373928398595beb7e6564e3d290fa1ec9f702691
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124828926"
---
# <a name="add-replicas-to-azure-cache-for-redis"></a>将副本添加到 Azure Cache for Redis

本文介绍如何使用 Azure 门户设置包含附加副本的 Azure Cache for Redis 实例。

Azure Cache for Redis 标准和高级层通过将每个缓存托管在两个专用虚拟机 (VM) 上来提供冗余。 这些 VM 配置为主 VM 和副本。 当主 VM 变得不可用时，副本将检测到该情况并自动担当新的主 VM。 你现在可以将高级缓存中的副本数增加到三个，总共提供四个 VM 来支持缓存。 具有多个副本比单个副本能够提供更高的复原能力。

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [创建免费帐户](https://azure.microsoft.com/free/)

## <a name="create-a-cache"></a>创建缓存

若要创建缓存，请执行以下步骤：

1. 登录到 [Azure 门户](https://portal.azure.com)，然后选择“创建资源”。
  
1. 在“新建”页上选择“数据库”，然后选择“Azure Cache for Redis”。

    :::image type="content" source="media/cache-create/new-cache-menu.png" alt-text="选择 Azure Cache for Redis。":::

1. 在“基本信息”页面上，配置新缓存的设置。

    | 设置      | 建议的值  | 描述 |
    | ------------ |  ------- | -------------------------------------------------- |
    | **订阅** | 选择订阅。 | 要在其下创建此新的 Azure Cache for Redis 实例的订阅。 |
    | **资源组** | 选择一个资源组，或者选择“新建”并输入新的资源组名称。 | 要在其中创建缓存和其他资源的资源组的名称。 将所有应用资源放入一个资源组可以轻松地统一管理或删除这些资源。 |
    | **DNS 名称** | 输入任何全局唯一的名称。 | 缓存名称必须是包含 1 到 63 个字符的字符串，只能包含数字、字母或连字符。 该名称必须以数字或字母开头和结尾，且不能包含连续的连字符。 缓存实例的主机名是 *\<DNS name> .redis.cache.windows.net*。 |
    | **位置** | 选择一个位置。 | 选择与要使用该缓存的其他服务靠近的[区域](https://azure.microsoft.com/regions/)。 |
    | **缓存类型** | 选择[高级层](https://azure.microsoft.com/pricing/details/cache/)缓存。 |  定价层决定可用于缓存的大小、性能和功能。 有关详细信息，请参阅[用于 Redis 的 Azure 缓存概述](cache-overview.md)。 |

1. 在“高级”页面上，选择“副本计数” 。

    :::image type="content" source="media/cache-how-to-multi-replicas/create-multi-replicas.png" alt-text="副本计数。":::

    > [!NOTE]
    > 目前，不能对多个副本（多于 1 个副本）使用仅追加文件 (AOF) 持久化或异地复制。
    >

1. 将其他选项保留默认设置。

1. 选择“创建”。

    创建缓存需要花费片刻时间。 可以在 Azure Cache for Redis 的“概述”页上监视进度。  如果“状态”显示为“正在运行”，则表示该缓存可供使用。 

    > [!NOTE]
    > 创建后，缓存中的副本数无法更改。
    >

## <a name="next-steps"></a>后续步骤

了解有关 Azure Cache for Redis 功能的详细信息。

> [!div class="nextstepaction"]
> [Azure Cache for Redis 高级服务层](cache-overview.md#service-tiers)
