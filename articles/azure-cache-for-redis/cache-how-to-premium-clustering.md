---
title: 配置 Redis 群集功能 - 高级 Azure Cache for Redis
description: 了解如何为高级层的 Azure Redis 缓存实例创建和管理 Redis 群集功能
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: c4812bb6b352ad57fa07ad766329644ef6af9c57
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122323575"
---
# <a name="configure-redis-clustering-for-a-premium-azure-cache-for-redis-instance"></a>为高级 Azure Cache for Redis 实例配置 Redis 群集功能

Azure Redis 缓存提供的 Redis 群集与 [在 Redis 中实施](https://redis.io/topics/cluster-tutorial)的一样。 Redis 群集具有以下优势：

* 能够在多个节点中自动拆分数据集。
* 能够在部分节点遇到故障或无法与群集其余部分通信的情况下继续运行。
* 更大的吞吐量：增加分片数时，吞吐量呈线性增加。
* 更大的内存大小：增加分片数时，内存大小呈线性增加。  

群集不会增加可用于群集缓存的连接数。 有关高级缓存大小、吞吐量和带宽的详细信息，请参阅[选择正确的层](cache-overview.md#choosing-the-right-tier)

在 Azure 中，Redis 群集以主/副模型提供。在该模型中，每个分片都有一个带副本的主/副对，副本由 Azure Redis 缓存服务管理。

## <a name="set-up-clustering"></a>设置群集功能

在创建缓存期间，在左侧的“新建 Azure Cache for Redis”中启用群集。

1. 若要创建高级缓存，请登录到 [Azure 门户](https://portal.azure.com)并选择“创建资源”。 除了在 Azure 门户中创建缓存以外，也可以使用 Resource Manager 模板、PowerShell 或 Azure CLI 创建。 有关创建 Azure Redis 缓存的详细信息，请参阅[创建缓存](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache)。

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="创建资源。":::

2. 在“新建”页上选择“数据库”，然后选择“Azure Cache for Redis”。

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="选择 Azure Cache for Redis。":::

3. 在“新建 Redis 缓存”页上配置新高级缓存的设置。

   | 设置      | 建议的值  | 说明 |
   | ------------ |  ------- | -------------------------------------------------- |
   | **DNS 名称** | 输入任何全局唯一的名称。 | 缓存名称必须是介于 1 到 63 个字符之间的字符串。 字符串只能包含数字、字母或连字符。 该名称必须以数字或字母开头和结尾，且不能包含连续的连字符。 缓存实例的主机名是 *\<DNS name> .redis.cache.windows.net*。 |
   | **订阅** | 单击下拉箭头并选择你的订阅。 | 要在其下创建此新的 Azure Cache for Redis 实例的订阅。 |
   | **资源组** | 单击下拉箭头并选择一个资源组，或者选择“新建”并输入新的资源组名称。 | 要在其中创建缓存和其他资源的资源组的名称。 将所有应用资源放入一个资源组可以轻松地统一管理或删除这些资源。 |
   | **位置** | 单击下拉箭头并选择一个位置。 | 选择与要使用该缓存的其他服务靠近的[区域](https://azure.microsoft.com/regions/)。 |
   | **缓存类型** | 单击下拉箭头并选择高级缓存来配置高级功能。 有关详细信息，请参阅 [Azure Cache for Redis 定价](https://azure.microsoft.com/pricing/details/cache/)。 |  定价层决定可用于缓存的大小、性能和功能。 有关详细信息，请参阅[用于 Redis 的 Azure 缓存概述](cache-overview.md)。 |

4. 选择“网络”选项卡，或选择页面底部的“网络”按钮 。

5. 在“网络”选项卡中，选择你的连接方法。 对于高级缓存实例，可以通过公共 IP 地址或服务终结点进行公开连接，也可以通过专用终结点进行私密连接。

6. 选择页面底部的“下一步:高级”选项卡，或者选择页面底部的“下一步:高级”按钮。

7. 在高级缓存实例的“高级”选项卡中，配置非 TLS 端口、群集和数据暂留的设置。 若要选择群集，请选择“启用”。

    :::image type="content" source="media/cache-how-to-premium-clustering/redis-cache-clustering.png" alt-text="群集切换。":::

    群集中最多可以有 10 个分片。 选择“启用”后，滑动滑块或者针对“分片计数”键入一个 1 到 10 之间的数字，并选择“确定”。

    每个分片都是一个由 Azure 管理的主/副缓存对，而缓存的总大小则通过将定价层中选择的缓存大小乘以分片数来计算。

    :::image type="content" source="media/cache-how-to-premium-clustering/redis-cache-clustering-selected.png" alt-text="已选择“群集切换”。":::

    创建缓存后，连接到缓存，就像使用非群集缓存一样使用缓存。 Redis 在整个缓存分片中分发数据。 如果[已启用](cache-how-to-monitor.md#enable-cache-diagnostics)诊断，则会为每个分片单独捕获度量值，这些度量值可在左侧的 Azure Cache for Redis 中[查看](cache-how-to-monitor.md)。

8. 选择“下一步:标记”选项卡，或者选择“下一步:标记”按钮（位于页面底部）。

9. 或者，在“标记”选项卡中，如果希望对资源分类，请输入名称或值。

10. 选择“查看 + 创建”  。 随后你会转到“查看 + 创建”选项卡，Azure 将在此处验证配置。

11. 显示绿色的“已通过验证”消息后，选择“创建”。

创建缓存需要花费片刻时间。 可以在 Azure Cache for Redis 的“概述”页上监视进度。  如果“状态”显示为“正在运行”，则表示该缓存可供使用。 

> [!NOTE]
>
> 配置群集时，客户端应用程序中需要有一些细微的差别。 有关详细信息，请参阅[使用群集功能时，是否需要对客户端应用程序进行更改？](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
>
>

有关在 StackExchange.Redis 客户端中使用群集功能的示例代码，请参阅 [Hello World](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) 示例的 [clustering.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/Clustering.cs) 部分。

<a name="cluster-size"></a>

## <a name="change-the-cluster-size-on-a-running-premium-cache"></a>更改正在运行的高级缓存上的群集大小

若要更改正在运行并且已启用群集功能的高级缓存上的群集大小，请在“资源菜单”中选择“群集大小”。

![Redis 群集大小][redis-cache-redis-cluster-size]

若要更改群集大小，请使用滑块，或在“分片计数”文本框中键入 1 到 10 之间的数字。 然后选择“确定”以保存。

增加群集大小会增加最大吞吐量和缓存大小。 增加群集大小不会增加用于客户端的最大连接数据。

> [!NOTE]
> 缩放群集会运行 [MIGRATE](https://redis.io/commands/migrate) 命令，此命令需耗费大量资源，因此为使其影响最小，请考虑在非高峰时段运行此操作。 在迁移过程中，服务器负载将达到峰值。 缩放群集的运行过程耗时较长，所花费的时间量取决于密钥数以及与这些密钥相关联的值的大小。
>
>

## <a name="clustering-faq"></a>群集功能常见问题

以下列表包含有关 Azure Redis 缓存群集功能的常见问题解答。

* [使用群集功能时，是否需要对客户端应用程序进行更改？](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
* [密钥在群集中是如何分布的？](#how-are-keys-distributed-in-a-cluster)
* [我可以创建的最大缓存大小是多大？](#what-is-the-largest-cache-size-i-can-create)
* [是否所有 Redis 客户端都支持群集功能？](#do-all-redis-clients-support-clustering)
* [启用群集功能后，如何连接到我的缓存？](#how-do-i-connect-to-my-cache-when-clustering-is-enabled)
* [我可以直接连接到缓存的各个分片吗？](#can-i-directly-connect-to-the-individual-shards-of-my-cache)
* [我可以为以前创建的缓存配置群集功能吗？](#can-i-configure-clustering-for-a-previously-created-cache)
* [我可以为基本缓存或标准缓存配置群集功能吗？](#can-i-configure-clustering-for-a-basic-or-standard-cache)
* [能否在 Redis ASP.NET 会话状态和输出缓存提供程序中使用群集功能？](#can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers)
* [我在使用 StackExchange.Redis 和群集功能时出现 MOVE 异常，应该怎么办？](#im-getting-move-exceptions-when-using-stackexchangeredis-and-clustering-what-should-i-do)

### <a name="do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering"></a>使用群集功能时，是否需要对客户端应用程序进行更改？

* 启用群集功能时，仅数据库 0 可用。 如果客户端应用程序使用多个数据库并尝试读取或写入数据库 0 之外的其他数据库，则会引发以下异常：`Unhandled Exception: StackExchange.Redis.RedisConnectionException: ProtocolFailure on GET --->` `StackExchange.Redis.RedisCommandException: Multiple databases are not supported on this server; cannot switch to database: 6`
  
  有关详细信息，请参阅 [Redis 群集规范 - 已实现子集](https://redis.io/topics/cluster-spec#implemented-subset)。
* 如果使用的是 [StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/)，则必须使用 1.0.481 或更高版本。 连接到该缓存时，可以使用的[终结点、端口和密钥](cache-configure.md#properties)与连接到禁用了群集功能的缓存时使用的相同。 唯一的区别是，所有读取和写入都必须在数据库 0 中进行。
  
  其他客户端可能有不同的要求。 请参阅 [是否所有 Redis 客户端都支持群集功能？](#do-all-redis-clients-support-clustering)
* 如果应用程序使用的多个密钥操作都在单个命令中成批执行，则所有密钥都必须位于同一分片。 若要查找同一分片中的密钥，请参阅[密钥在群集中是如何分布的？](#how-are-keys-distributed-in-a-cluster)
* 如果使用的是 Redis ASP.NET 会话状态提供程序，则必须使用 2.0.1 或更高版本。 请参阅 [能否在 Redis ASP.NET 会话状态和输出缓存提供程序中使用群集功能？](#can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers)

### <a name="how-are-keys-distributed-in-a-cluster"></a>密钥在群集中是如何分布的？

请参阅 Redis [密钥分布模型](https://redis.io/topics/cluster-spec#keys-distribution-model)文档：密钥空间拆分成 16384 个槽。 每个密钥都经过哈希处理并分配到其中一个槽，这些槽分布在群集的节点中。 对密钥的哪部分进行哈希处理是可以配置的，这样可确保多个使用哈希标记的密钥位于同一分片。

* 使用哈希标记的密钥 - 如果将密钥的任意部分括在 `{` 和 `}` 中，则只会对密钥的该部分进行哈希处理，以便确定密钥的哈希槽。 例如，以下 3 个密钥将位于同一分片中：`{key}1`、`{key}2` 和 `{key}3`，因为只对名称的 `key` 部分进行了哈希处理。 如需密钥哈希标记规范的完整列表，请参阅 [密钥哈希标记](https://redis.io/topics/cluster-spec#keys-hash-tags)。
* 不带哈希标记的密钥 - 将使用整个密钥名称进行哈希处理，从而让缓存在分片之间均匀分布（从统计角度）。

为了优化性能和吞吐量，建议将密钥平均分布。 如果使用带哈希标记的密钥，则应用程序会负责确保密钥平均分布。

有关详细信息，请参阅 [Keys distribution mode](https://redis.io/topics/cluster-spec#keys-distribution-model)（密钥分布模型）、[Redis Cluster data sharding](https://redis.io/topics/cluster-tutorial#redis-cluster-data-sharding)（Redis 群集数据分片）和 [Keys hash tags](https://redis.io/topics/cluster-spec#keys-hash-tags)（密钥哈希标记）。

有关在 StackExchange.Redis 客户端中使用群集和查找同一分片中的密钥的示例代码，请参阅 [Hello World](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) 示例的 [clustering.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/Clustering.cs) 部分。

### <a name="what-is-the-largest-cache-size-i-can-create"></a>可以创建的最大缓存大小是多大？

可以具有的最大缓存大小为 1.2 TB。 这会是包含 10 个分片的群集 P5 缓存。 有关详细信息，请参阅 [Azure Redis 缓存定价](https://azure.microsoft.com/pricing/details/cache/)。

### <a name="do-all-redis-clients-support-clustering"></a>是否所有 Redis 客户端都支持群集功能？

很多（但并非全部）客户端都支持 Redis 群集。 查看文档，找到你正在使用的库，确认你正在使用的库和版本是否支持群集。 StackExchange.Redis 是一个库，它在更新的版本中确实支持群集。 有关其他客户端的详细信息，请参阅 [Redis cluster tutorial](https://redis.io/topics/cluster-tutorial)（Redis 群集教程）的 [Playing with the cluster](https://redis.io/topics/cluster-tutorial#playing-with-the-cluster)（操作群集）部分。

Redis 群集协议要求每个客户端以群集模式直接连接到每个分片，并且还定义了新的错误响应，例如“MOVED”或“CROSSSLOTS”。 如果你要尝试使用的客户端不支持群集但包含群集模式缓存，则结果可能造成很多[ MOVED 重定向异常](https://redis.io/topics/cluster-spec#moved-redirection)，如果你正在执行横向槽多密钥请求，则会中断你的应用程序。

> [!NOTE]
> 如果使用 StackExchange.Redis 作为客户端，请确保使用最新版本的 [StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/)，即 1.0.481 或更高版本，以便群集功能能够正常使用。 要详细了解出现 move 异常的任何问题，请参阅 [move 异常](#move-exceptions)。
>

### <a name="how-do-i-connect-to-my-cache-when-clustering-is-enabled"></a>启用群集功能后，如何连接到缓存？

连接到缓存时，可以使用的[终结点](cache-configure.md#properties)、[端口](cache-configure.md#properties)和[密钥与](cache-configure.md#access-keys)你连接到未启用群集功能的缓存时使用的相同。 Redis 在后端管理群集功能，因此不需要你通过客户端来管理它。

### <a name="can-i-directly-connect-to-the-individual-shards-of-my-cache"></a>可以直接连接到缓存的各个分片吗？

群集协议要求客户端建立正确的分片连接，以便客户端应为你建立共享连接。 话虽如此，但每个分片都是由主/副缓存对组成的，该缓存对统称为缓存实例。 可以在 GitHub 上通过 Redis 存储库的 [不稳定](https://redis.io/download) 分支使用 redis-cli 实用程序连接到这些缓存实例。 使用 `-c` 开关启动后，此版本可实现基本的支持。 有关详细信息，请参阅 [https://redis.io](https://redis.io) 上 [Redis cluster tutorial](https://redis.io/topics/cluster-tutorial)（Redis 群集教程）中的[操作群集](https://redis.io/topics/cluster-tutorial#playing-with-the-cluster)。

对于非 TLS，请使用以下命令。

```bash
Redis-cli.exe –h <<cachename>> -p 13000 (to connect to instance 0)
Redis-cli.exe –h <<cachename>> -p 13001 (to connect to instance 1)
Redis-cli.exe –h <<cachename>> -p 13002 (to connect to instance 2)
...
Redis-cli.exe –h <<cachename>> -p 1300N (to connect to instance N)
```

对于 TLS，请将 `1300N` 替换为 `1500N`。

### <a name="can-i-configure-clustering-for-a-previously-created-cache"></a>可以为以前创建的缓存配置群集功能吗？

是的。 首先，请确保缓存为高级缓存（通过纵向扩展缓存）。 接下来，应该能够看到群集配置选项，包括用于启用群集的选项。 在创建缓存或首次启用群集功能后更改群集大小。

   >[!IMPORTANT]
   >无法撤消启用群集功能。 启用了群集功能且只有一个分片的缓存的行为与 *没有* 群集功能的相同大小缓存的行为 *不同*。

### <a name="can-i-configure-clustering-for-a-basic-or-standard-cache"></a>可以为基本缓存或标准缓存配置群集功能吗？

群集功能仅适用于高级缓存。

### <a name="can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers"></a>能否在 Redis ASP.NET 会话状态和输出缓存提供程序中使用群集功能？

* **Redis 输出缓存提供程序** - 无需进行更改。
* **Redis 会话状态提供程序** - 若要使用群集功能，必须使用 [RedisSessionStateProvider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.1 或更高版本，否则会引发异常，这是一项重大更改。 有关详细信息，请参阅 [v 2.0.0 重大更改详细信息](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details)。

<a name="move-exceptions"></a>

### <a name="im-getting-move-exceptions-when-using-stackexchangeredis-and-clustering-what-should-i-do"></a>我在使用 StackExchange.Redis 和群集功能时出现 MOVE 异常，应该怎么办？

如果使用的是 StackExchange.Redis 并在使用群集功能时收到 `MOVE` 异常，请确保使用的是 [StackExchange.Redis 1.1.603](https://www.nuget.org/packages/StackExchange.Redis/) 或更高版本。 有关如何配置 .NET 应用程序以使用 StackExchange.Redis 的说明，请参阅[配置缓存客户端](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients)。

## <a name="next-steps"></a>后续步骤

了解有关 Azure Cache for Redis 功能的详细信息。

* [Azure Cache for Redis 高级服务层](cache-overview.md#service-tiers)

<!-- IMAGES -->

[redis-cache-clustering]: ./media/cache-how-to-premium-clustering/redis-cache-clustering.png

[redis-cache-clustering-selected]: ./media/cache-how-to-premium-clustering/redis-cache-clustering-selected.png

[redis-cache-redis-cluster-size]: ./media/cache-how-to-premium-clustering/redis-cache-redis-cluster-size.png
