---
title: Azure Cache for Redis 的高可用性
description: 了解 Azure Cache for Redis 高可用性功能和选项
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.author: yegu
ms.openlocfilehash: e06ced47829beeab46edbc98ddeb92fa4e959ec0
ms.sourcegitcommit: 6323442dbe8effb3cbfc76ffdd6db417eab0cef7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/28/2021
ms.locfileid: "110614755"
---
# <a name="high-availability-for-azure-cache-for-redis"></a>Azure Cache for Redis 的高可用性

Azure Cache for Redis 具有内置的高可用性。  高可用性体系结构用于确保托管 Redis 实例即使在中断影响基础虚拟机 (VM) 时也能正常运行（无论是计划内还是计划外中断）。 它提供的可用性百分比率要远高于将 Redis 承载在单个 VM 上的情况。

Azure Cache for Redis 使用多个称为“节点”的用于缓存的 VM 来实现高可用性。 对节点进行配置，目的是以协调的方式进行数据复制和故障转移。 高可用性还有助于维护操作，例如 Redis 软件修补。 “标准”、“高级”和“企业”层级中提供了各种高可用性选项：

| 选项 | 说明 | 可用性 | 标准 | 高级 | Enterprise |
| ------------------- | ------- | ------- | :------: | :---: | :---: |
| [标准复制](#standard-replication)| 单个数据中心中具有自动故障转移功能的双节点复制配置 | 99.9%（查看[详细信息](https://azure.microsoft.com/support/legal/sla/cache/v1_1/)） |✔|✔|-|
| [区域冗余](#zone-redundancy) | AZ 中具有自动故障转移功能的多节点复制配置 | 高达 99.99%（查看[详细信息](https://azure.microsoft.com/support/legal/sla/cache/v1_1/)） |-|✔|✔|
| [异地复制](#geo-replication) | 两个区域中的链接缓存实例，具有用户控制的故障转移 | 高达 99.999%（查看[详细信息](https://azure.microsoft.com/support/legal/sla/cache/v1_1/)） |-|✔|预览|

## <a name="standard-replication"></a>标准复制

默认情况下，在“标准”或“高级”层级中，Azure Cache for Redis 在一对 Redis 服务器上运行。 这两个服务器托管在专用 VM 上。 开源 Redis 只允许一台服务器处理数据写入请求。 此服务器是主要节点，而另一服务器是副本。 预配服务器节点后，Azure Cache for Redis 可向其分配主要角色和副本角色。 主节点通常负责为来自 Redis 客户端的写入和读取请求提供服务。 在执行写入操作时，它会向其内部内存提交一个新密钥和密钥更新，并立即回复客户端。 它以异步方式将操作转发给副本。

:::image type="content" source="media/cache-high-availability/replication.png" alt-text="数据复制设置":::

>[!NOTE]
>通常，Redis 客户端会与 Redis 缓存中的主节点通信，以获取所有读取和写入请求。 某些 Redis 客户端可以配置为从副本节点进行读取。
>
>

如果 Redis 缓存中的主节点不可用，则副本会自动升级为新的主节点。 此过程称为故障转移。 副本会等待足够长的时间才去接管，以免出现主节点快速恢复的情况。 发生故障转移时，Azure Cache for Redis 会预配新的 VM，并将其作为副本节点加入到缓存中。 副本执行与主节点的完整数据同步，这样它就会有缓存数据的另一个副本。

主节点在计划内维护活动（例如 Redis 软件或操作系统更新）期间可能会停止服务。 它还可能因为计划外事件（例如底层硬件、软件或网络故障）而停止工作。 [Azure Cache for Redis 的故障转移和修补](cache-failover.md)提供了有关 Redis 故障转移类型的详细说明。 Azure Cache for Redis 在其生存期内会经历许多故障转移。 高可用性体系结构的设计使缓存中的这些更改对其客户端尽可能透明。

此外，Azure Cache for Redis 在高级层中提供更多副本节点。 可以为[多副本缓存](cache-how-to-multi-replicas.md)配置最多三个副本节点。 具有更多副本通常可提高复原能力，因为你的节点会为主节点提供备份。 即使有更多副本，Azure Cache for Redis 实例仍可能会受到数据中心级别或 AZ 级别的服务中断的严重影响。 可使用多个具有[区域冗余](#zone-redundancy)的副本来提高缓存可用性。

## <a name="zone-redundancy"></a>区域冗余

Azure Cache for Redis 支持高级层和企业层中的区域冗余配置。 [区域冗余缓存](cache-how-to-zone-redundancy.md)可以将其节点置于同一区域中的不同[Azure 可用性区域](../availability-zones/az-overview.md)上。 它消除了数据中心或 AZ 中断造成的单点故障，并提高了缓存的总体可用性。

### <a name="premium-tier"></a>高级层

下图说明了“高级”层级的区域冗余配置：

:::image type="content" source="media/cache-high-availability/zone-redundancy.png" alt-text="区域冗余设置":::

Azure Cache for Redis 在你选择的 AZ 上将节点以循环方式分布在区域冗余缓存中。 它还确定最初将哪个节点用作主节点。

区域冗余缓存提供自动故障转移。 若当前主节点不可用，则由其中一个副本接管。 如果新的主节点位于不同的 AZ，则应用程序可能会遇到更长的缓存响应时间。 AZ 在地理位置上是分开的。 从一个 AZ 切换到另一 AZ 会改变应用程序和缓存的托管位置之间的物理距离。 此更改会影响应用程序到缓存的往返网络延迟。 额外的延迟应在大多数应用程序的可接受范围内。 建议对应用程序进行测试，确保它可以正常使用区域冗余缓存。

### <a name="enterprise-tiers"></a>企业层

任一企业层中的缓存在 Redis Enterprise 群集上运行。 它总是需要保持奇数个服务器节点才能形成仲裁。 默认情况下，它有三个节点，每个节点都托管在专用 VM 上。 Enterprise 缓存有两个大小相同的数据节点和一个较小的仲裁节点 。 Enterprise Flash 缓存有三个大小相同的数据节点。 Enterprise 群集在内部将 Redis 数据划分为多个分区。 每个分区有一个主分区和至少一个副本 。 每个数据节点都包含一个或多个分区。 Enterprise 群集可确保任何分区的主分区和副本永远不会并置在同一数据节点上。 分区将数据从主分区异步复制到其相应的副本。

当数据节点不可用或发生网络拆分时，会发生类似于[标准复制](#standard-replication)中描述的故障转移。 Enterprise 群集使用基于仲裁的模型来确定哪些未受影响的节点将参与新的仲裁。 它还根据需要将这些节点中的分区副本提升为主分区。

## <a name="geo-replication"></a>异地复制

[异地复制](cache-how-to-geo-replication.md)是用于链接两个或以上 Azure Cache for Redis 实例的机制，这两个实例通常跨越两个 Azure 区域。

### <a name="premium-tier"></a>高级层

>[!NOTE]
>高级层中的异地复制主要用于灾难恢复。
>
>

可以通过 [异地复制](cache-how-to-geo-replication.md) 连接两个高级层缓存实例，以便可以将缓存数据备份到其他区域。 这两个缓存实例链接在一起之后，一个实例命名为主链接缓存，另一个实例命名为辅助链接缓存。 只有主链接缓存才接受读写请求。 写入主缓存的数据将被复制到辅助缓存。

应用程序通过主缓存和辅助缓存的不同终结点来访问缓存。 当应用程序部署到多个 Azure 区域时，它必须将所有写入请求发送到主缓存。 它可以从主缓存或辅助缓存读取。 通常情况下，你希望应用程序的计算实例从最近的缓存中读取，以减少延迟。 两个缓存实例之间的数据传输受 TLS 保护。

如果应用程序的其余部分保留在主要区域中，则考虑到区域之间的网络延迟会增加，异地复制不会提供自动故障转移。 需要通过取消链接辅助缓存来管理和启动故障转移。 取消链接将使它提升为新的主实例。

### <a name="enterprise-tiers"></a>企业层

>[!NOTE]
>此功能以预览版提供。
>
>

企业层支持更高级的异地复制形式， 我们称之为[活动异地复制](cache-how-to-active-geo-replication.md)。 利用无冲突的复制数据类型，Redis Enterprise 软件支持写入多个缓存实例，并负责合并更改和解决冲突。 可加入不同 Azure 区域中的两个或更多个企业层缓存实例，形成活动异地复制缓存。 

使用此类缓存的应用程序可以通过相应的终结点来读取和写入地理分布缓存实例。 缓存应使用最接近每个计算实例的内容，以提供最低的延迟。 应用程序还需要监视缓存实例，并在其中一个实例变为不可用时切换到另一个区域。 有关活动异地复制的工作原理的详细信息，请参阅 [Active-Active Geo-Distriubtion（基于 CRDT）](https://redislabs.com/redis-enterprise/technology/active-active-geo-distribution/)。

## <a name="next-steps"></a>后续步骤

详细了解如何配置 Azure Cache for Redis 高可用性选项。

* [Azure Cache for Redis 高级服务层](cache-overview.md#service-tiers)
* [将副本添加到 Azure Cache for Redis](cache-how-to-multi-replicas.md)
* [启用适用于 Azure Cache for Redis 的区域冗余](cache-how-to-zone-redundancy.md)
* [为 Azure Cache for Redis 设置异地复制](cache-how-to-geo-replication.md)
