---
title: 设置 Azure Cache for Redis 的 Redis 版本（预览）
description: 了解如何配置 Redis 版本
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 09/30/2020
ms.openlocfilehash: d1dda6c4c414c79daf8223794c7d89846fad418f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121739855"
---
# <a name="set-redis-version-for-azure-cache-for-redis-preview"></a>设置 Azure Cache for Redis 的 Redis 版本（预览）
本文介绍如何配置要与缓存实例一起使用的 Redis 软件版本。 Azure Cache for Redis 提供 Redis 的最新主版本和至少一个早期版本。 由于会发布新的 Redis 软件，它将定期更新这些版本。 可在两个可用版本之间进行选择。 请记住，如果缓存当前使用的版本不再受支持，缓存会自动升级到下一版本。

> [!NOTE]
> Redis 6 目前为预览版。 目前，Redis 6 不支持群集、区域冗余、ACL、PowerShell、Azure CLI、Terraform，以及在 Redis 4.0 与 6.0 缓存之间进行的异地复制。 创建缓存后，也无法更改 Redis 版本。 
>

> [!IMPORTANT]
> Redis 6.0 在正式发布 (GA) 后将成为新缓存的默认 Redis 版本。 你仍可选择创建 Redis 4.0 缓存。 
>

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
    | **缓存类型** | 选择[缓存层和大小](https://azure.microsoft.com/pricing/details/cache/)。 |  定价层决定可用于缓存的大小、性能和功能。 有关详细信息，请参阅[用于 Redis 的 Azure 缓存概述](cache-overview.md)。 |
   
1. 在“高级”页面上，选择要使用的 Redis 版本。
   
    :::image type="content" source="media/cache-how-to-version/select-redis-version.png" alt-text="Redis 版本。":::

1. 选择“创建”。 
   
    创建缓存需要花费片刻时间。 可以在 Azure Cache for Redis 的“概述”页上监视进度。  如果“状态”显示为“正在运行”，则表示该缓存可供使用。 

    > [!NOTE]
    > 目前，一旦创建缓存，就无法更改 Redis 版本。
    >

## <a name="faq"></a>常见问题解答

### <a name="what-features-arent-supported-with-redis-6"></a>Redis 6 不支持哪些功能？

目前，Redis 6 不支持群集、区域冗余、ACL、PowerShell、Azure CLI 和 Terraform，也不支持在 Redis 4.0 与 6.0 缓存之间进行异地复制。 

### <a name="can-i-change-the-version-of-my-cache-after-its-created"></a>创建缓存后，能否更改缓存的版本？

目前，创建缓存后无法更改缓存的版本。

## <a name="next-steps"></a>后续步骤
了解有关 Azure Cache for Redis 功能的详细信息。

> [!div class="nextstepaction"]
> [Azure Cache for Redis 高级服务层](cache-overview.md#service-tiers)
