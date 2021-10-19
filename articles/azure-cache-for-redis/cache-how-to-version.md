---
title: 设置 Azure Cache for Redis 的 Redis 版本
description: 了解如何配置 Redis 版本
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 10/07/2021
ms.openlocfilehash: 41d61ccce3602b1d3e823eb6a4c46afe39159c46
ms.sourcegitcommit: 216b6c593baa354b36b6f20a67b87956d2231c4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2021
ms.locfileid: "129728173"
---
# <a name="set-redis-version-for-azure-cache-for-redis"></a>设置 Azure Cache for Redis 的 Redis 版本
本文介绍如何配置要与缓存实例一起使用的 Redis 软件版本。 Azure Cache for Redis 提供 Redis 的最新主版本和至少一个早期版本。 由于会发布新的 Redis 软件，它将定期更新这些版本。 可在两个可用版本之间进行选择。 请记住，如果缓存当前使用的版本不再受支持，缓存会自动升级到下一版本。

> [!NOTE]
> 此时，Redis 6 不支持 ACL，以及 Redis 4 和 6 缓存之间的异地复制。
>

## <a name="prerequisites"></a>先决条件
* Azure 订阅 - [创建免费帐户](https://azure.microsoft.com/free/)

## <a name="create-a-cache-using-the-azure-portal"></a>使用 Azure 门户创建缓存
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


## <a name="create-a-cache-using-azure-powershell"></a>使用 Azure PowerShell 创建缓存

```azurepowershell
    New-AzRedisCache -ResourceGroupName "ResourceGroupName" -Name "CacheName" -Location "West US 2" -Size 250MB -Sku "Standard" -RedisVersion "6"
```
有关如何使用 Azure PowerShell 管理 Azure Cache for Redis 的详细信息，请参阅[此处](cache-how-to-manage-redis-cache-powershell.md)

## <a name="create-a-cache-using-azure-cli"></a>使用 Azure CLI 创建缓存

```azurecli-interactive
az redis create --resource-group resourceGroupName --name cacheName --location westus2 --sku Standard --vm-size c0 --redisVersion="6"
```
有关如何使用 Azure CLI 管理 Azure Cache for Redis 的详细信息，请参阅[此处](cli-samples.md)

## <a name="upgrade-an-existing-redis-4-cache-to-redis-6"></a>将现有的 Redis 4 缓存升级到 Redis 6
Azure Cache for Redis 支持将 Redis 缓存服务器主版本从 Redis 4 升级到 Redis 6。 请注意，升级为永久性，可能会导致短暂的连接故障。 作为预防措施，我们建议在升级之前导出现有的 Redis 4 缓存中的数据，并在较低的环境中使用 Redis 6 缓存测试客户端应用程序。 有关如何导出的详细信息，请参阅[此处](cache-how-to-import-export-data.md)。

> [!NOTE]
> 请注意，使用异地复制链接的缓存不支持升级，因此在升级之前必须手动取消链接缓存实例。 
>

要升级缓存，请按照以下步骤操作：

1. 在 Azure 门户中，搜索“Azure Cache for Redis”。 然后，按 Enter 或者在搜索建议中选择该服务。

    :::image type="content" source="media/cache-private-link/4-search-for-cache.png" alt-text="搜索 Azure Cache for Redis。":::

1. 选择要从 Redis 4 升级到 Redis 6 的缓存实例。

1. 在屏幕左侧，选择“高级设置”。 

1. 如果缓存实例符合升级条件，应会看到以下蓝色横幅。 如果要继续，请选择横幅中的文本。

    :::image type="content" source="media/cache-how-to-version/blue-banner-upgrade-cache.png" alt-text="蓝色横幅，显示可使用其他功能和命令升级 Redis 6 缓存，以提高开发人员工作效率和易用性。无法反向升级缓存实例。":::
    
1. 然后会弹出一个对话框，通知你升级为永久性，可能会导致短暂的连接故障。 如果要升级缓存实例，请选择“是”。

    :::image type="content" source="media/cache-how-to-version/dialog-version-upgrade.png" alt-text="包含有关升级缓存的详细信息的对话框。":::

1. 要检查升级状态，请导航到“概览”。

    :::image type="content" source="media/cache-how-to-version/upgrade-status.png" alt-text="概览显示正在升级的缓存的状态。":::

## <a name="faq"></a>常见问题解答

### <a name="what-features-arent-supported-with-redis-6"></a>Redis 6 不支持哪些功能？

此时，Redis 6 不支持 ACL，以及 Redis 4 和 6 缓存之间的异地复制。

### <a name="can-i-change-the-version-of-my-cache-after-its-created"></a>创建缓存后，能否更改缓存的版本？

可将现有的 Redis 4 缓存升级到 Redis 6，有关详细信息，请参阅[此处](#upgrade-an-existing-redis-4-cache-to-redis-6)。 请注意，升级缓存实例为永久性，且无法将 Redis 6 缓存降级到 Redis 4 缓存。

## <a name="next-steps"></a>后续步骤

- 要详细了解 Redis 6 功能，请参阅[通过 Redis 深入了解 Redis 6.0](https://redis.com/blog/diving-into-redis-6/)
- 详细了解 Azure Cache for Redis 功能：

> [!div class="nextstepaction"]
> [Azure Cache for Redis 高级服务层](cache-overview.md#service-tiers)
