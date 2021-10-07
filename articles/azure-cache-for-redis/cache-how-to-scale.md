---
title: 缩放 Azure Cache for Redis 实例
description: 了解如何使用 Azure 门户以及 Azure PowerShell 和 Azure CLI 等工具缩放 Azure Cache for Redis 实例
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 5c8bf3839c7bae9b1c93b201bb52a4e7be371904
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129538166"
---
# <a name="scale-an-azure-cache-for-redis-instance"></a>缩放 Azure Cache for Redis 实例

Azure Redis 缓存具有不同的缓存产品/服务，使缓存大小和功能的选择更加灵活。 对于基本、标准或高级缓存，可在创建后更改其大小和层级，以满足应用程序需求。 本文演示如何使用 Azure 门户以及 Azure PowerShell 和 Azure CLI 等工具来缩放缓存。

## <a name="when-to-scale"></a>何时缩放

可使用 Azure Cache for Redis 的[监视](cache-how-to-monitor.md)功能来监视缓存的运行状况和性能。 使用该信息确定何时缩放缓存。

可以监视以下指标以帮助确定是否需要进行缩放。

- Redis 服务器负载
  - Redis 服务器是单线程进程。 高 Redis 服务器负载意味着该服务器无法应对来自所有客户端连接的请求。 在这种情况下，它有助于启用聚类分析或增加分片计数，以便将开销函数分布在多个 Redis 进程中。 聚类分析和更大的分片计数分发 TLS 加密和解密，并分发 TLS 连接和断开连接。
  - 有关详细信息，请参阅[设置聚类分析](cache-how-to-premium-clustering.md#set-up-clustering)。
- 内存用量
  - 高内存用量指示数据大小对于当前的缓存大小来说太大。 请考虑缩放到具有更大内存的缓存大小。
- 客户端连接
  - 每个缓存大小都有其所能支持的客户端连接数的限制。 如果客户端连接接近缓存大小的限制，请考虑纵向扩展到更大的层，或进行横向扩展以启用聚类分析并增加分片计数。 你的选择取决于 Redis 服务器负载和内存用量。
  - 若要详细了解缓存大小的连接限制，请参阅 [Azure Cache for Redis 规划常见问题解答](./cache-planning-faq.yml)。
- 网络带宽
  - 如果 Redis 服务器超出可用带宽，则客户端请求可能会超时，因为服务器无法以足够快的速度将数据推送到客户端。 检查“缓存读取”和“缓存写入”指标，了解使用的服务器端带宽量。 如果 Redis 服务器超出了可用的网络带宽，则应考虑纵向扩展到具有更高网络带宽的更大缓存大小。
  - 若要详细了解缓存大小的网络可用带宽，请参阅 [Azure Cache for Redis 规划常见问题解答](./cache-planning-faq.yml)。

如果你确定缓存不再满足应用程序的要求，可针对应用程序缩放到适当的缓存定价层。 可选择一个更大或更小的缓存来满足你的需求。

若要详细了解如何确定要使用的缓存定价层，请参阅[选择正确的层](cache-overview.md#choosing-the-right-tier)和 [Azure Cache for Redis 规划常见问题解答](./cache-planning-faq.yml)。

## <a name="scale-a-cache"></a>缩放缓存

要缩放缓存，请在 [Azure 门户](https://portal.azure.com)中[找到“缓存”](cache-configure.md#configure-azure-cache-for-redis-settings)，并选择左侧的“缩放”。

:::image type="content" source="media/cache-how-to-scale/scale-a-cache.png" alt-text="资源菜单中的缩放":::

选择右侧的定价层，然后选择选择“选择”。

:::image type="content" source="media/cache-how-to-scale/select-a-tier.png" alt-text="Azure Cache for Redis 层":::

可以扩展到不同定价层，但有以下限制：

- 不能从较高的定价层缩放到较低的定价层。
  - 不能从 **高级** 缓存向下缩放到 **标准** 或 **基本** 缓存。
  - 不能从 **标准** 缓存向下缩放到 **基本** 缓存。
- 可从 **基本** 缓存缩放到 **标准** 缓存，但不能同时更改大小。 以后如果需要不同的大小，可以执行缩放操作以缩放为所需大小。
- 不能从 **基本** 缓存直接缩放到 **高级** 缓存。 首先在一个缩放操作中从“基本”缩放到“标准”，然后在后续的缩放操作中从“标准”缩放到“高级”   。
- 不能从较大的大小减小为 **C0 (250 MB)** 。 但是，可以纵向缩减为同一定价层中的任何其他大小。 例如，可以从“C5 标准”纵向缩减为“C1 标准”。

当缓存缩放到新层级时，会显示“缩放 Redis 缓存”通知。

:::image type="content" source="media/cache-how-to-scale/scaling-notification.png" alt-text="缩放通知":::

缩放完成后，状态将从 **正在缩放** 更改为 **正在运行**。

## <a name="how-to-automate-a-scaling-operation"></a>如何自动执行缩放操作

可以在 Azure 门户中缩放缓存实例。 此外，还可以使用 PowerShell cmdlet、Azure CLI 和 Microsoft Azure 管理库 (MAML) 进行缩放。

- [使用 PowerShell 进行缩放](#scale-using-powershell)
- [使用 Azure CLI 进行缩放](#scale-using-azure-cli)
- [使用 MAML 进行缩放](#scale-using-maml)

### <a name="scale-using-powershell"></a>使用 PowerShell 进行缩放

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

修改 `Size`、`Sku` 或 `ShardCount` 属性后，可以在 PowerShell 中使用 [Set-AzRedisCache](/powershell/module/az.rediscache/set-azrediscache) cmdlet 缩放 Azure Redis 缓存实例。 以下示例演示如何将名为 `myCache` 的缓存缩放为 2.5-GB 缓存。

```powershell
   Set-AzRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB
```

有关使用 PowerShell 进行缩放的详细信息，请参阅[使用 PowerShell 缩放 Azure Redis 缓存](cache-how-to-manage-redis-cache-powershell.md#scale)。

### <a name="scale-using-azure-cli"></a>使用 Azure CLI 进行缩放

若要使用 Azure CLI 缩放 Azure Cache for Redis 实例，请调用 `azure rediscache set` 命令，并根据所需的缩放操作传入所需的配置更改（包括新大小、SKU 或群集大小）。

有关使用 Azure CLI 进行缩放的详细信息，请参阅[更改现有 Azure Redis 缓存的设置](cache-manage-cli.md#scale)。

### <a name="scale-using-maml"></a>使用 MAML 进行缩放

若要使用 [Microsoft Azure 管理库 (MAML)](https://azure.microsoft.com/updates/management-libraries-for-net-release-announcement/) 缩放 Azure Redis 缓存实例，请调用 `IRedisOperations.CreateOrUpdate` 并传入 `RedisProperties.SKU.Capacity` 的新大小。

```csharp
    static void Main(string[] args)
    {
        // For instructions on getting the access token, see
        // https://azure.microsoft.com/documentation/articles/cache-configure/#access-keys
        string token = GetAuthorizationHeader();

        TokenCloudCredentials creds = new TokenCloudCredentials(subscriptionId,token);

        RedisManagementClient client = new RedisManagementClient(creds);
        var redisProperties = new RedisProperties();

        // To scale, set a new size for the redisSKUCapacity parameter.
        redisProperties.Sku = new Sku(redisSKUName,redisSKUFamily,redisSKUCapacity);
        redisProperties.RedisVersion = redisVersion;
        var redisParams = new RedisCreateOrUpdateParameters(redisProperties, redisCacheRegion);
        client.Redis.CreateOrUpdate(resourceGroupName,cacheName, redisParams);
    }
```

有关详细信息，请参阅[使用 MAML 管理 Azure Redis 缓存](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML)示例。

## <a name="scaling-faq"></a>关于缩放的常见问题

以下列表包含有关 Azure Redis 缓存缩放的常见问题的解答。

- [可以向上缩放到高级缓存，或在其中向下缩放吗？](#can-i-scale-to-from-or-within-a-premium-cache)
- [缩放后，我是否需要更改缓存名称或访问密钥？](#after-scaling-do-i-have-to-change-my-cache-name-or-access-keys)
- [缩放的工作原理？](#how-does-scaling-work)
- [在缩放过程中是否会丢失缓存中的数据？](#will-i-lose-data-from-my-cache-during-scaling)
- [在缩放过程中，自定义数据库设置是否会受影响？](#is-my-custom-databases-setting-affected-during-scaling)
- [在缩放过程中，缓存是否可用？](#will-my-cache-be-available-during-scaling)
- [异地复制是否存在缩放限制？](#are-there-scaling-limitations-with-geo-replication)
- [不支持的操作](#operations-that-arent-supported)
- [缩放需要多长时间？](#how-long-does-scaling-take)
- [如何判断缩放何时完成？](#how-can-i-tell-when-scaling-is-complete)

### <a name="can-i-scale-to-from-or-within-a-premium-cache"></a>可以向上缩放到高级缓存，或在其中向下缩放吗？

- 不能从 **高级** 缓存向下缩放到 **基本** 或 **标准** 定价层。
- 可以从一个 **高级** 缓存定价层缩放到另一个高级缓存定价层。
- 不能从 **基本** 缓存直接缩放到 **高级** 缓存。 首先在一个缩放操作中从“基本”缩放到“标准”，然后在后续的缩放操作中从“标准”缩放到“高级”   。
- 如果在创建 **高级** 缓存时启用了群集，则可以 [更改群集大小](cache-how-to-premium-clustering.md#cluster-size)。 如果创建缓存时未启用群集功能，可以稍后进行配置。

有关详细信息，请参阅[如何为高级 Azure Redis 缓存配置群集功能](cache-how-to-premium-clustering.md)。

### <a name="after-scaling-do-i-have-to-change-my-cache-name-or-access-keys"></a>缩放后，我是否需要更改缓存名称或访问密钥？

不需要，在缩放操作期间缓存名称和密钥不变。

### <a name="how-does-scaling-work"></a>缩放的工作原理？

- 将基本缓存缩放为不同大小时，将关闭该缓存，同时使用新的大小预配一个新缓存。 在此期间，缓存不可用，且缓存中的所有数据都将丢失。
- 将基本缓存缩放为标准缓存时，将预配副本缓存并将主缓存中的数据复制到副本缓存 。 在缩放过程中，缓存仍然可用。
- 将标准缓存缩放为不同大小或缩放到高级缓存时，将关闭其中一个副本，同时将其重新预配为新的大小，将数据转移，然后另一个副本在进行重新预配前执行一次故障转移，类似于一个缓存节点发生故障时所发生的过程 。
- 横向扩展群集缓存时，将预配新的分片并将其添加到 Redis 服务器群集中。 然后，数据跨所有分片重新进行切分。
- 在群集缓存中进行缩放时，首先对数据重新进行切分，然后针对所需的分片缩减群集大小。

### <a name="will-i-lose-data-from-my-cache-during-scaling"></a>在缩放过程中是否会丢失缓存中的数据？

- 将基本缓存缩放为新的大小时，所有数据都会丢失，且在缩放操作期间缓存将不可用。
- 将基本缓存缩放为标准缓存时，通常将保留缓存中的数据 。
- 将标准缓存扩展为更大的大小或更大的层，或者将高级缓存扩展为更大的大小时，通常将保留所有数据 。 将标准缓存或高级缓存缩放为更小的大小时，如果数据大小在缩减时超出了新的较小大小，数据可能会丢失。 如果缩小时数据丢失，则使用 [allkeys lru](https://redis.io/topics/lru-cache) 逐出策略逐出密钥。

### <a name="is-my-custom-databases-setting-affected-during-scaling"></a>在缩放过程中，自定义数据库设置是否会受影响？

如果在缓存创建过程中为 `databases` 设置配置了自定义值，请记住，某些定价层具有不同的[数据库限制](cache-configure.md#databases)。 以下是在这种情况下缩放时的一些注意事项：

- 缩放到的定价层的 `databases` 限制低于当前层：
  - 如果使用的是默认 `databases` 数（对于所有定价层来说为 16），则不会丢失数据。
  - 如果使用的是在要缩放到的层的限制内的自定义 `databases` 数，则将保留此 `databases` 设置并且不会丢失数据。
  - 如果使用的是超出新层限制的自定义 `databases` 数，则 `databases` 设置将降低到新层的限制，并且已删除数据库中的所有数据都将丢失。
- 所缩放到的定价层的 `databases` 限制等于或高于当前层时，会保留 `databases` 设置并且不会丢失数据。

虽然标准和高级缓存具有 99.9% 可用性 SLA，但没有数据丢失方面的 SLA。

### <a name="will-my-cache-be-available-during-scaling"></a>在缩放过程中，缓存是否可用？

- **标准** 和 **高级** 缓存在缩放操作期间保持可用。 但是，缩放标准和高级缓存时，以及从基本缓存缩放到标准缓存时，可能会发生连接故障。 这些连接故障预期为小故障，Redis 客户端通常可以立即重新建立连接。
- **基本** 缓存在缩放为不同大小的操作期间处于脱机状态。 基本缓存在从“基本”缩放到“标准”时仍然可用，但可能会出现较小的连接故障 。 如果发生连接故障，Redis 客户端通常可以立即重新建立连接。

### <a name="are-there-scaling-limitations-with-geo-replication"></a>异地复制是否存在缩放限制？

在配置了异地复制的情况下，你可能会注意到你无法缩放缓存或更改群集中的分片。 两个缓存之间的异地复制链接会阻止你执行缩放操作或更改群集中的分片数。 若要发布这些命令，必须取消链接缓存。 有关详细信息，请参阅[配置异地复制](cache-how-to-geo-replication.md)。

### <a name="operations-that-arent-supported"></a>不支持的操作

- 不能从较高的定价层缩放到较低的定价层。
  - 不能从 **高级** 缓存向下缩放到 **标准** 或 **基本** 缓存。
  - 不能从 **标准** 缓存向下缩放到 **基本** 缓存。
- 可从 **基本** 缓存缩放到 **标准** 缓存，但不能同时更改大小。 以后如果需要不同的大小，可以执行缩放操作以缩放为所需大小。
- 不能从 **基本** 缓存直接缩放到 **高级** 缓存。 首先在一个缩放操作中从“基本”缩放到“标准”，然后在之后的一个操作中从“标准”缩放到“高级”   。
- 不能从较大的大小减小为 **C0 (250 MB)** 。

如果缩放操作失败，该服务将尝试还原操作并且缓存将还原为原始大小。

### <a name="how-long-does-scaling-take"></a>缩放需要多长时间？

缩放时间取决于几个因素。 下面是一些可能会影响缩放时间的因素。

- 数据量：量较大的数据需要较长的时间进行复制
- 高写入请求：写入次数越多，意味着跨节点或分片的数据复制越多
- 高服务器负载：较高的服务器负载意味着 Redis 服务器繁忙，可用于完成数据重新分发的 CPU 周期有限

通常，在没有数据的情况下扩展缓存时，大约需要 20 分钟。 对于群集缓存，在数据最少的情况下，每个分片的缩放大约需要 20 分钟。

### <a name="how-can-i-tell-when-scaling-is-complete"></a>如何判断缩放何时完成？

在 Azure 门户中可以看到进行中的缩放操作。 缩放完成后，缓存状态将更改为 **正在运行**。
