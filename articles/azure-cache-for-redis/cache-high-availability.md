---
title: Azure Cache for Redis 的高可用性
description: 了解 Azure Cache for Redis 高可用性功能和选项
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.author: yegu
ms.openlocfilehash: ad6696fc4fe2af7047c25a3a9c260d3b12588ee2
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2021
ms.locfileid: "102203294"
---
# <a name="high-availability-for-azure-cache-for-redis"></a>Azure Cache for Redis 的高可用性

Azure Cache for Redis 具有内置的高可用性。 其高可用性体系结构的目标是确保托管的 Redis 实例正常运行，即使其基础虚拟机 (VM) 受计划内或计划外中断的影响。 它提供的可用性百分比率要远高于将 Redis 承载在单个 VM 上的情况。

Azure Cache for Redis 使用多个称为“节点”的用于缓存的 VM 来实现高可用性。 它将这些节点配置为以协调的方式进行数据复制和故障转移。 它还会协调维护操作，例如 Redis 软件修补。 “标准”、“高级”和“企业”层级中提供了各种高可用性选项：

| 选项 | 说明 | 可用性 | 标准 | 高级 | Enterprise |
| ------------------- | ------- | ------- | :------: | :---: | :---: |
| [标准复制](#standard-replication)| 具有自动故障转移功能的单个数据中心内的双节点复制配置 | 99.9% |✔|✔|-|
| [区域冗余](#zone-redundancy) | 跨 AZs 的多节点复制配置，具有自动故障转移 | 99.95% (高级层) ，99.99% (企业级)  |-|预览|预览|
| [异地复制](#geo-replication) | 两个区域中的链接缓存实例，具有用户控制的故障转移 | 99.999% (企业层)  |-|✔|预览|

## <a name="standard-replication"></a>标准复制

默认情况下，在“标准”或“高级”层级中，Azure Cache for Redis 在一对 Redis 服务器上运行。 这两个服务器托管在专用 VM 上。 开源 Redis 只允许一台服务器处理数据写入请求。 此服务器是主要节点，而另一服务器是副本。 预配服务器节点后，Azure Cache for Redis 可向其分配主要角色和副本角色。 主要节点通常负责为来自 Redis 客户端的写入和读取请求提供服务。 在执行写入操作时，它会向其内部内存提交一个新密钥和密钥更新，并立即回复客户端。 它以异步方式将操作转发给副本。

:::image type="content" source="media/cache-high-availability/replication.png" alt-text="数据复制设置":::
   
>[!NOTE]
>通常，Redis 客户端会与 Redis 缓存中的主节点通信，以获取所有读取和写入请求。 某些 Redis 客户端可以配置为从副本节点进行读取。
>
>

如果 Redis 缓存中的主节点不可用，则副本会自动升级为新的主节点。 此过程称为故障转移。 副本会等待足够长的时间才去接管，以免出现主节点快速恢复的情况。 发生故障转移时，Azure Cache for Redis 会预配新的 VM，并将其作为副本节点加入到缓存中。 副本执行与主节点的完整数据同步，这样它就会有缓存数据的另一个副本。

主节点在计划内维护活动（例如 Redis 软件或操作系统更新）中可能会停止服务。 它还可能因为计划外事件（例如底层硬件、软件或网络故障）而停止工作。 [Azure Cache for Redis 的故障转移和修补](cache-failover.md)提供了有关 Redis 故障转移类型的详细说明。 Azure Cache for Redis 在其生存期内会经历许多故障转移。 高可用性体系结构旨在使缓存中的这些更改对其客户端尽可能透明。

>[!NOTE]
>以下内容以预览版提供。
>
>

此外，Azure Cache for Redis 在高级层中允许更多副本节点。 可以为[多副本缓存](cache-how-to-multi-replicas.md)配置最多三个副本节点。 具有更多副本通常会提高复原能力，因为附加的节点会为主节点提供备份。 即使有更多副本，Azure Cache for Redis 实例仍会受到数据中心或 AZ 级中断的严重影响。 通过将多个副本与[区域冗余](#zone-redundancy)结合使用，可以提高缓存可用性。

## <a name="zone-redundancy"></a>区域冗余

适用于 Redis 的 Azure 缓存支持高级和企业层中的区域冗余配置。 [区域冗余缓存](cache-how-to-zone-redundancy.md)可以将其节点置于同一区域中的不同[Azure 可用性区域](../availability-zones/az-overview.md)上。 它消除了数据中心或 AZ 停机作为单点故障，并提高了缓存的总体可用性。

### <a name="premium-tier"></a>高级层

>[!NOTE]
>此功能以预览版提供。
>
>

下图说明了高级层的区域冗余配置：

:::image type="content" source="media/cache-high-availability/zone-redundancy.png" alt-text="区域冗余设置":::
   
适用于 Redis 的 Azure 缓存以循环方式将区域中的节点分配给所选的 AZs。 它还确定哪个节点最初将用作主节点。

区域冗余缓存提供自动故障转移。 当当前主节点不可用时，其中一个副本将接管。 如果新的主节点位于不同的 AZ，则您的应用程序可能会遇到更高的缓存响应时间。 AZs 是地理上分隔的。 从 AZ 切换到另一 AZ 会改变应用程序和缓存的托管位置之间的物理距离。 此更改会影响应用程序到缓存的双程网络延迟。 对于大多数应用程序而言，额外滞后时间应在可接受的范围内。 建议你对应用程序进行测试，以确保它可以使用区域冗余缓存良好运行。

### <a name="enterprise-tiers"></a>企业层

任一企业层中的缓存在 Redis Enterprise 群集上运行。 它需要在任何时候都保持奇数个服务器节点以便形成仲裁。 默认情况下，它由三个节点组成，每个节点都托管在专用 VM 上。 Enterprise 缓存有两个大小相同的数据节点和一个较小的仲裁节点 。 Enterprise Flash 缓存有三个大小相同的数据节点。 Enterprise 群集在内部将 Redis 数据划分为多个分区。 每个分区有一个主分区和至少一个副本 。 每个数据节点都包含一个或多个分区。 Enterprise 群集可确保任何分区的主分区和副本永远不会并置在同一数据节点上。 分区将数据从主分区异步复制到其相应的副本。

当数据节点不可用或发生网络拆分时，会发生类似于[标准复制](#standard-replication)中描述的故障转移。 Enterprise 群集使用基于仲裁的模型来确定哪些未受影响的节点将参与新的仲裁。 它还根据需要将这些节点中的分区副本提升为主分区。

## <a name="geo-replication"></a>异地复制

[异地复制](cache-how-to-geo-replication.md) 是一种用于链接两个或更多 azure Cache for Redis 实例的机制，通常跨两个 azure 区域。 

### <a name="premium-tier"></a>高级层

>[!NOTE]
>高级层中的异地复制主要用于灾难恢复。
>
>

可以通过 [异地复制](cache-how-to-geo-replication.md) 连接两个高级层缓存实例，以便可以将缓存数据备份到其他区域。 链接在一起后，一个实例被指定为主链接缓存，另一个实例指定为辅助链接缓存。 只有主缓存接受读写请求。 写入主缓存的数据将复制到辅助缓存。 应用程序通过主缓存和辅助缓存的不同终结点来访问缓存。 当应用程序部署到多个 Azure 区域时，它必须将所有写入请求发送到主缓存。 它可以从主缓存或辅助缓存读取。 通常，你希望应用程序的计算实例从最近的缓存中读取，以减少延迟。 两个缓存实例之间的数据传输由 TLS 保护。

异地复制不提供自动故障转移，因为如果应用程序的其余部分保留在主要区域中，则在区域之间增加网络往返时间。 需要通过取消链接辅助缓存来管理和启动故障转移。 这将使它提升为新的主实例。

### <a name="enterprise-tiers"></a>企业层

>[!NOTE]
>此功能以预览版提供。
>
>

企业级支持更高级的异地复制形式，称为 [活动异地复制](cache-how-to-active-geo-replication.md)。 利用无冲突的复制数据类型，Redis 企业软件支持写入多个缓存实例，并在必要时处理更改并解决冲突。 可以加入不同 Azure 区域中的两个或更多个企业级缓存实例，形成活动异地复制缓存。 使用此类缓存的应用程序可以通过相应的终结点来读取和写入地理分布缓存实例。 它应使用最接近每个计算实例的内容，这可提供最低的延迟。 应用程序还需要监视缓存实例，并在其中一个实例变为不可用时切换到另一个区域。 有关活动异地复制的工作原理的详细信息，请参阅 [active-active Geo-Distriubtion (基于 CRDTs 的) ](https://redislabs.com/redis-enterprise/technology/active-active-geo-distribution/)。

## <a name="next-steps"></a>后续步骤

详细了解如何配置 Azure Cache for Redis 高可用性选项。

* [Azure Cache for Redis 高级服务层](cache-overview.md#service-tiers)
* [将副本添加到 Azure Cache for Redis](cache-how-to-multi-replicas.md)
* [为 Redis 的 Azure 缓存启用区域冗余](cache-how-to-zone-redundancy.md)
* [为 Azure Cache for Redis 设置异地复制](cache-how-to-geo-replication.md)
