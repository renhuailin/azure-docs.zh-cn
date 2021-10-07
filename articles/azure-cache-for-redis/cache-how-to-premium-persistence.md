---
title: 配置数据暂留 - 高级 Azure Cache for Redis
description: 了解如何为高级层的 Azure Redis 缓存实例配置和管理数据暂留
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: 472f06a10e76cb557bd5e9350c94a352ebad17b3
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129538119"
---
# <a name="configure-data-persistence-for-a-premium-azure-cache-for-redis-instance"></a>为高级 Azure Cache for Redis 实例配置数据暂留

[Redis 暂留](https://redis.io/topics/persistence)可让你保留存储在 Redis 中的数据。 你还可以创建快照和备份数据。 发生硬件故障时，可以加载这些数据。 与将所有数据存储在内存中的基本或标准层相比，能够持久保存数据是一项巨大的优势。 如果在缓存节点关闭的情况下发生故障，则可能会丢失数据。

Azure Cache for Redis 使用 Redis 数据库 (RDB) 和仅追加文件 (AOF) 提供 Redis 持久性：

* RDB 持久性 - 配置 RDB 持久性后，Azure Cache for Redis 会将 Redis 中的 Azure Cache for Redis 快照以二进制格式持久保存在磁盘上。 该快照保存在 Azure 存储帐户中。 可配置的备份频率决定了持久保存快照的频率。 如果发生了灾难性事件，导致主缓存和副缓存都无法使用，则会使用最新快照重新构造缓存。 详细了解 RDB 暂留的[优点](https://redis.io/topics/persistence#rdb-advantages)和[缺点](https://redis.io/topics/persistence#rdb-disadvantages)。
* AOF 持久性 - 使用 AOF 持久性时，Azure Cache for Redis 会将每个写入操作保存到日志。 日志每秒至少保存到 Azure 存储帐户中一次。 如果发生了灾难性事件，导致主缓存和副缓存都无法使用，则会使用存储的写入操作重新构造缓存。 详细了解 AOF 暂留的[优点](https://redis.io/topics/persistence#aof-advantages)和[缺点](https://redis.io/topics/persistence#aof-disadvantages)。

暂留将 Redis 数据写入你拥有和管理的 Azure 存储帐户。 在创建缓存期间，在左侧配置“新建 Azure Cache for Redis”。 对于现有的高级缓存，请使用“资源”菜单。

> [!NOTE]
>
> 保存数据时，Azure 存储会自动加密数据。 可以使用自己的密钥进行加密。 有关详细信息，请参阅 [Azure Key Vault 中的客户托管密钥](../storage/common/storage-service-encryption.md)。
>
>

## <a name="set-up-data-persistence"></a>设置数据持久性

1. 若要创建高级缓存，请登录到 [Azure 门户](https://portal.azure.com)并选择“创建资源”。 可以在 Azure 门户中创建缓存。 也可以使用资源管理器模板、PowerShell 或 Azure CLI 创建缓存。 有关创建 Azure Redis 缓存的详细信息，请参阅[创建缓存](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache)。

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="创建资源。":::
  
2. 在“新建”页上选择“数据库”，然后选择“Azure Cache for Redis”。

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="选择 Azure Cache for Redis。":::

3. 在“新建 Redis 缓存”页上配置新高级缓存的设置。
  
   | 设置      | 建议的值  | 说明 |
   | ------------ |  ------- | -------------------------------------------------- |
   | **DNS 名称** | 输入任何全局唯一的名称。 | 缓存名称必须是包含 1 到 63 个字符的字符串，只能包含数字、字母或连字符。 该名称必须以数字或字母开头和结尾，且不能包含连续的连字符。 缓存实例的主机名是 *\<DNS name> .redis.cache.windows.net*。 |
   | **订阅** | 单击下拉箭头并选择你的订阅。 | 要在其下创建此新的 Azure Cache for Redis 实例的订阅。 |
   | **资源组** | 单击下拉箭头并选择一个资源组，或者选择“新建”并输入新的资源组名称。 | 要在其中创建缓存和其他资源的资源组的名称。 将所有应用资源放入一个资源组可以轻松地统一管理或删除这些资源。 |
   | **位置** | 单击下拉箭头并选择一个位置。 | 选择与要使用该缓存的其他服务靠近的[区域](https://azure.microsoft.com/regions/)。 |
   | **缓存类型** | 单击下拉箭头并选择高级缓存来配置高级功能。 有关详细信息，请参阅 [Azure Cache for Redis 定价](https://azure.microsoft.com/pricing/details/cache/)。 |  定价层决定可用于缓存的大小、性能和功能。 有关详细信息，请参阅[用于 Redis 的 Azure 缓存概述](cache-overview.md)。 |

4. 选择“网络”选项卡，或选择“网络”按钮（位于页面底部） 。

5. 在“网络”选项卡中，选择你的连接方法。 对于高级缓存实例，可以通过公共 IP 地址或服务终结点公开进行连接。 可以使用专用终结点以私密方式进行连接。

6. 选择“下一步:高级”选项卡，或者选择页面底部的“下一步:高级”按钮 。

7. 在高级缓存实例的“高级”选项卡中，配置非 TLS 端口、群集和数据暂留的设置。 对于数据暂留，可以选择 RDB 或 AOF 暂留。

8. 若要启用 RDB 持久性，请选择“RDB”并配置设置。
  
   | 设置      | 建议的值  | 说明 |
   | ------------ |  ------- | -------------------------------------------------- |
   | **备份频率** | 单击下拉箭头并选择一个备份间隔。 选项包括“15 分钟”、“30 分钟”、“60 分钟”、“6 小时”、“12 小时”和“24 小时”。 | 在上一个备份操作成功完成后，此间隔就会开始倒计时，同时会启动新的备份。 |
   | **存储帐户** | 单击下拉箭头，选择你的存储帐户。 | 选择与缓存位于同一区域和订阅的存储帐户。建议选择“高级存储”帐户，因为高级存储的吞吐量较高。  |
   | **存储密钥** | 单击下拉箭头，选择要使用的“主密钥”或“辅助密钥”。 | 如果重新生成了持久性帐户的存储密钥，必须从“存储密钥”下拉列表中重新配置密钥。 |

    备份频率间隔的时间过后，将启动第一次备份。
  
   > [!NOTE]
   > 当 RDB 文件备份到存储时，它们以页 blob 的形式存储。
  
9. 若要启用 AOF 持久性，请选择“AOF”并配置设置。

   | 设置      | 建议的值  | 说明 |
   | ------------ |  ------- | -------------------------------------------------- |
   | **第一个存储帐户** | 单击下拉箭头，选择你的存储帐户。 | 此存储帐户必须与缓存处于同一区域和订阅，建议选择“高级存储”帐户，因为高级存储的吞吐量较高。 |
   | **第一个存储密钥** | 单击下拉箭头，选择要使用的“主密钥”或“辅助密钥”。 | 如果重新生成了持久性帐户的存储密钥，必须从“存储密钥”下拉列表中重新配置密钥。 |
   | **第二个存储帐户** | （可选）单击下拉箭头，选择你的辅助存储帐户。 | 可以选择性地配置另一个存储帐户。 如果配置第二个存储帐户，写入副本缓存操作会写入到第二个存储帐户。 |
   | **第二个存储密钥** | （可选）单击下拉箭头，选择要使用的“主密钥”或“辅助密钥”。 | 如果重新生成了持久性帐户的存储密钥，必须从“存储密钥”下拉列表中重新配置密钥。 |

    启用 AOF 持久性后，写入缓存操作会保存到指定的存储帐户（如果配置了另一个存储帐户，则会保存到这两个帐户）。 如果灾难性故障导致主缓存和副本缓存均无法使用，则会使用存储的 AOF 日志重新生成缓存。

10. 选择“下一步: 标记”选项卡，或者选择页面底部的“下一步: 标记”按钮 。

11. 或者，在“标记”选项卡中，如果希望对资源分类，请输入名称或值。

12. 选择“查看 + 创建”  。 随后你会转到“查看 + 创建”选项卡，Azure 将在此处验证配置。

13. 显示绿色的“已通过验证”消息后，选择“创建”。

创建缓存需要花费片刻时间。 可以在 Azure Cache for Redis 的“概述”页上监视进度。  如果“状态”显示为“正在运行”，则表示该缓存可供使用。 

## <a name="persistence-faq"></a>保留常见问题

以下列表包含对 Azure Redis 缓存暂留相关常见问题的解答。

* [能否在此前已创建的缓存的基础上启用保留？](#can-i-enable-persistence-on-a-previously-created-cache)
* [是否可同时启用 AOF 暂留和 RDB 暂留？](#can-i-enable-aof-and-rdb-persistence-at-the-same-time)
* [应该选择哪个暂留模型？](#which-persistence-model-should-i-choose)
* [如果我缩放到不同大小并还原了缩放操作之前生成的备份，会发生什么情况？](#what-happens-if-ive-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)
* [能否在两个不同的缓存中使用同一存储帐户进行保留？](#can-i-use-the-same-storage-account-for-persistence-across-two-different-caches)
* [是否需要为数据暂留中使用存储付费](#will-i-be-charged-for-the-storage-being-used-in-data-persistence)

### <a name="rdb-persistence"></a>RDB 暂留

* [创建缓存后是否可更改 RDB 备份频率？](#can-i-change-the-rdb-backup-frequency-after-i-create-the-cache)
* [为何我的 RDB 备份频率为 60 分钟，而两次备份的间隔却超过 60 分钟？](#why-is-there-more-than-60-minutes-between-backups-when-i-have-an-rdb-backup-frequency-of-60-minutes)
* [进行新备份以后，旧的 RDB 备份会发生什么情况？](#what-happens-to-the-old-rdb-backups-when-a-new-backup-is-made)

### <a name="aof-persistence"></a>AOF 暂留

* [是否应使用第二个存储帐户？](#when-should-i-use-a-second-storage-account)
* [AOF 暂留是否影响缓存吞吐量、延迟或性能？](#does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache)
* [如何删除第二个存储帐户？](#how-can-i-remove-the-second-storage-account)
* [什么是重写？重写对缓存有何影响？](#what-is-a-rewrite-and-how-does-it-affect-my-cache)
* [缩放启用 AOF 的缓存时会出现什么情况？](#what-should-i-expect-when-scaling-a-cache-with-aof-enabled)
* [AOF 数据如何在存储中进行整理？](#how-is-my-aof-data-organized-in-storage)

### <a name="can-i-enable-persistence-on-a-previously-created-cache"></a>能否在此前已创建的缓存的基础上启用保留？

是的，可以在创建缓存时或者在现有高级缓存上配置 Redis 持久性。

### <a name="can-i-enable-aof-and-rdb-persistence-at-the-same-time"></a>是否可以同时启用 AOF 暂留和 RDB 暂留？

不可以，只能分别启用 RDB 或 AOF，但不能同时启用二者。

### <a name="which-persistence-model-should-i-choose"></a>应该选择哪个暂留模型？

AOF 持久性会将每次写入保存到日志，这会对吞吐量产生重大影响。 相较于 AOF，RDB 持久性基于配置的备份间隔保存备份，因此对性能的影响极小。 如果主要目标是最大程度地减少数据损失，并且你可以应对缓存吞吐量下降，请选择 AOF 持久性。 如果希望在缓存上保持最优吞吐量，但仍希望使用一种数据恢复机制，请选择 RDB 暂留。

* 详细了解 RDB 暂留的[优点](https://redis.io/topics/persistence#rdb-advantages)和[缺点](https://redis.io/topics/persistence#rdb-disadvantages)。
* 详细了解 AOF 暂留的[优点](https://redis.io/topics/persistence#aof-advantages)和[缺点](https://redis.io/topics/persistence#aof-disadvantages)。

有关使用 AOF 暂留时的性能详细信息，请参阅 [AOF 暂留是否影响缓存吞吐量、延迟或性能？](#does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache)

### <a name="what-happens-if-ive-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation"></a>如果我缩放到不同大小并还原了缩放操作之前生成的备份，会发生什么情况？

对于 RDB 暂留和 AOF 暂留：

* 扩展到更大的大小不会产生任何影响。
* 如果缩减到更小的大小，并且自定义[数据库](cache-configure.md#databases)设置大于新大小的[数据库限制](cache-configure.md#databases)，则不会还原这些数据库中的数据。 有关详细信息，请参阅[在缩放过程中，自定义数据库设置是否会受影响？](cache-how-to-scale.md#is-my-custom-databases-setting-affected-during-scaling)
* 如果缩减到更小的大小，并且更小的大小空间不足，无法容纳上次备份的所有数据，则在还原过程中会逐出密钥。  通常使用 [allkeys-lru](https://redis.io/topics/lru-cache) 逐出策略来逐出密钥。

### <a name="can-i-use-the-same-storage-account-for-persistence-across-two-different-caches"></a>能否在两个不同的缓存中使用同一存储帐户进行保留？

可以，你可以使用同一存储帐户在两个不同的缓存中进行保留

### <a name="can-i-change-the-rdb-backup-frequency-after-i-create-the-cache"></a>创建缓存后是否可更改 RDB 备份频率？

是，可以在左侧的“数据持久性”中更改 RDB 持久性备份频率。 有关说明，请参阅“配置 Redis 暂留”。

### <a name="why-is-there-more-than-60-minutes-between-backups-when-i-have-an-rdb-backup-frequency-of-60-minutes"></a>为何我的 RDB 备份频率为 60 分钟，而两次备份的间隔却超过 60 分钟？

RDB 持久性备份频率间隔在先前备份过程已成功完成后才会开始。 如果备份频率为 60 分钟，而备份过程需要 15 分钟才能完成，则在上一次备份开始以后，要再过 75 分钟才会开始下一次备份。

### <a name="what-happens-to-the-old-rdb-backups-when-a-new-backup-is-made"></a>进行新备份以后，旧的 RDB 备份会发生什么情况？

除最新备份外的所有 RDB 持久性备份会被自动删除。 这种删除可能不会即刻发生，但旧备份是不会无限期保存。 请注意，如果为存储帐户启用软删除，则将应用软删除设置，并且现有备份将继续处于软删除状态。

### <a name="when-should-i-use-a-second-storage-account"></a>是否应使用第二个存储帐户？

如果你认为缓存上的设置操作高于预期，请将第二个存储帐户用于 AOF 持久性。  设置辅助存储帐户有助于确保缓存不会达到存储带宽限制。

### <a name="does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache"></a>AOF 暂留是否影响缓存吞吐量、延迟或性能？

缓存低于最大负载（CPU 和服务器负载皆低于 90%）时，AOF 暂留对性能产生约 15% - 20% 的影响。 缓存处于这些限制以内时不存在延迟问题。 但是，启用 AOF 时缓存将更快达到这些限制。

### <a name="how-can-i-remove-the-second-storage-account"></a>如何删除第二个存储帐户？

可通过将第二个存储帐户设置为与第一个存储帐户相同的方式来删除 AOF 暂留辅助存储帐户。 对于现有缓存，可从缓存的“资源菜单”访问左侧的“数据持久性” 。 若要禁用 AOF 持久性，请选择“禁用”。

### <a name="what-is-a-rewrite-and-how-does-it-affect-my-cache"></a>什么是重写？重写对缓存有何影响？

在 AOF 文件足够大时，重写会自动处于缓存上的队列中。 重写使用创建当前数据集所需的最小操作集来重设 AOF 文件大小。 重写期间预期会很快达到性能限制，尤其是在处理大型数据集时。 AOF 文件越大，重写发生频率越低，但是如果发生则会需要较长时间。

### <a name="what-should-i-expect-when-scaling-a-cache-with-aof-enabled"></a>缩放启用 AOF 的缓存时会出现什么情况？

如果缩放时的 AOF 文件非常大，则缩放操作所花时间应长于预期，因为在缩放结束后才重载文件。

有关缩放的详细信息，请参阅[如果我缩放到不同大小并还原了缩放操作之前生成的备份，会发生什么情况？](#what-happens-if-ive-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)

### <a name="how-is-my-aof-data-organized-in-storage"></a>AOF 数据如何在存储中进行整理？

AOF 文件中存储的数据在每个节点分为多个页 Blob，以便提升将数据保存至存储的性能。 下表介绍每个定价层使用的页 Blob 数量：

| 高级层 | Blob |
|--------------|-------|
| P1           | 4 个/分片    |
| P2           | 8 个/分片    |
| P3           | 16 个/分片   |
| P4           | 20 个/分片   |

启用群集时，缓存中的每个分片具有自己的页 Blob 集，如上表所示。 例如，具有 3 个分片的 P2 缓存在 24 个页 Blob 之间（每个分片 8 个页 Blob，共 3 个分片）分配其 AOF 文件。

重写后，存储中存在 2 个 AOF 文件集。 重写在后台进行，并追加到第一个文件集。 在重写期间发送到缓存的设置操作会追加到第二个集。 重写期间如果发生故障，会暂时存储备份。 重写完成后，会立即删除该备份。 请注意，如果为存储帐户启用软删除，则将应用软删除设置，并且现有备份将继续处于软删除状态。

### <a name="will-i-be-charged-for-the-storage-being-used-in-data-persistence"></a>是否需要为数据暂留中使用的存储付费？

是，你将按照正在使用的存储帐户的定价模型，为使用的存储付费。

## <a name="next-steps"></a>后续步骤

了解有关 Azure Cache for Redis 功能的详细信息。

* [Azure Cache for Redis 高级服务层](cache-overview.md#service-tiers)

<!-- IMAGES -->

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-persistence/redis-cache-premium-pricing-tier.png

[redis-cache-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-persistence.png

[redis-cache-rdb-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-rdb-persistence.png

[redis-cache-aof-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-aof-persistence.png

[redis-cache-settings]: ./media/cache-how-to-premium-persistence/redis-cache-settings.png
