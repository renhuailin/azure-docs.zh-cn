---
title: 什么是 Azure Redis 缓存？
description: 了解 Azure Cache for Redis，以便启用缓存端、内容缓存、用户会话缓存、作业和消息队列以及分布式事务。
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: overview
ms.date: 02/08/2021
ms.openlocfilehash: 5debf0fdd82132ece890f29967e345ab5b134635
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114297577"
---
# <a name="about-azure-cache-for-redis"></a>关于用于 Redis 的 Azure 缓存

Azure Cache for Redis 基于 [Redis](https://redis.io/) 软件提供内存中数据存储。 Redis 改善了大量使用后端数据存储的应用程序的性能和可伸缩性。 它将经常访问的数据保留在可快速读写的服务器内存中，从而能够处理大量应用程序请求。 Redis 为新式应用程序带来了关键的低延迟、高吞吐量数据存储解决方案。

Azure Cache for Redis 提供 Redis 开放源代码 (OSS Redis) ，还提供 Redis 实验室的一款商业产品 (Redis Enterprise) 作为托管服务。 它提供安全的专用 Redis 服务器实例，且完全兼容 Redis API。 该服务由 Microsoft 管理并在 Azure 中托管，可由 Azure 内外的任何应用程序使用。

Azure Cache for Redis 可用作分布式数据或内容缓存、会话存储和消息中转站等。 它可以单独部署。 或者，它也可以与其他 Azure 数据库服务（如 Azure SQL 或 Cosmos DB）一起部署。

## <a name="key-scenarios"></a>关键方案

Azure Cache for Redis 通过支持常见的应用程序体系结构模式来提高应用程序性能。 部分最常用的模式如下所示：

| 模式      | 说明                                        |
| ------------ | -------------------------------------------------- |
| [数据缓存](cache-web-app-cache-aside-leaderboard.md) | 数据库通常很大，无法直接加载到缓存中。 通常是使用[缓存端](/azure/architecture/patterns/cache-aside)模式，只在需要时才将数据加载到缓存中。 系统对数据进行更改时，还会同时更新缓存，然后将缓存分发给其他客户端。 另外，系统还可以设置数据的过期时间，或者使用逐出策略触发对缓存的数据更新。|
| [内容缓存](cache-aspnet-output-cache-provider.md) | 许多网页都是基于使用静态内容（例如页眉、页脚、横幅）的模板生成的。 这些静态项不应经常更改。 与后端数据存储相比，使用内存中缓存可以快速地访问静态内容。 此模式降低了处理时间和服务器负载，使 Web 服务器能够更快地做出响应。 这可减少处理负载所需的服务器数量。 Azure Cache for Redis 提供 Redis 输出缓存提供程序，支持对 ASP.NET 使用此模式。|
| [会话存储](cache-aspnet-session-state-provider.md) | 此模式通常用于购物车和其他用户历史记录数据，Web 应用程序可能将此类信息与用户 Cookie 相关联。 在 Cookie 中存储过多内容可能会对性能造成负面影响，因为 Cookie 会变大，并且每次请求都需要传递和验证 Cookie。 常用解决方案是使用 Cookie 作为键来查询数据库中的数据。 使用内存中缓存（例如 Azure Redis 缓存）将信息与用户关联在速度上要比与整个关系数据库交互快得多。 |
| 作业和消息队列 | 当需要花费时间来执行与请求相关联的操作时，应用程序通常将任务添加到队列中。 长时间运行的操作将排队等待按顺序处理，通常由另一服务器进行处理。  这种将工作推迟的方法称为任务队列。 Azure Cache for Redis 提供了一个分布式队列，用以在应用程序中启用此模式。|
| 分布式事务 | 有时候，应用程序需要将针对后端数据存储的一系列命令作为单个原子操作来执行。 所有命令都必须成功，否则，所有命令都必须回退到初始状态。 Azure Cache for Redis 支持将一批命令作为单个[事务](https://redis.io/topics/transactions)来执行。 |

## <a name="redis-versions"></a>Redis 版本

Azure Cache for Redis 支持 OSS Redis 版本 4.x 和 6.0（预览版）。 我们已决定跳过 Redis 5.0，为你提供最新版本。 以前，Azure Cache for Redis 维护一个 Redis 版本。 以后，它将提供一个较新的主要版本升级，以及至少一个较旧的稳定版本。 你可以选择最适合你的应用程序的[版本](cache-how-to-version.md)。


## <a name="service-tiers"></a>服务层

Azure Cache for Redis 在下面各层中提供：

| 层 | 说明 |
|---|---|
| 基本 | 在单个 VM 上运行的 OSS Redis 缓存。 该层没有服务级别协议 (SLA)，非常适用于开发/测试和非关键工作负载。 |
| Standard | 在配置完全相同的两个 VM 上运行的 OSS Redis 缓存。 |
| 高级 | 高性能 OSS Redis 缓存。 该层提供更高的吞吐量、更低的延迟、更好的可用性和更多功能。 与基本或标准缓存相比，高级缓存部署在功能更强大的 VM 上。 |
| Enterprise | 高性能缓存由 Redis 实验室的 Redis Enterprise 软件提供支持。 该层支持 Redis 模块，包括 RediSearch、RedisBloom 和 RedisTimeSeries。 此外，它甚至提供比高级层更高的可用性。 |
| Enterprise Flash | 经济高效的大型缓存由 Redis 实验室的 Redis Enterprise 软件提供支持。 该层将 Redis 数据存储扩展到 VM 上的非易失存储器，该存储器的价格低于 DRAM 的价格。 它总体上降低了每 GB 内存的成本。 |

### <a name="feature-comparison"></a>功能比较

[Azure Cache for Redis 定价](https://azure.microsoft.com/pricing/details/cache/)提供了每个层的详细比较。 下表介绍了每个层支持的部分功能：

| 功能说明 | 基本 | Standard | 高级 | Enterprise | Enterprise Flash |
| ------------------- | :-----: | :------: | :---: | :---: | :---: |
| [服务级别协议 (SLA)](https://azure.microsoft.com/support/legal/sla/cache/v1_0/) |-|✔|✔|✔|✔|
| 数据加密 |✔|✔|✔|✔|✔|
| [网络隔离](cache-how-to-premium-vnet.md) |✔|✔|✔|✔|✔|
| [缩放](cache-how-to-scale.md) |✔|✔|✔|-|-|
| [OSS 群集](cache-how-to-premium-clustering.md) |-|-|✔|✔|✔|
| [数据持久性](cache-how-to-premium-persistence.md) |-|-|✔|预览|预览|
| [区域冗余](cache-how-to-zone-redundancy.md) |-|-|✔|✔|✔|
| [异地复制](cache-how-to-geo-replication.md) |-|-|✔|预览|预览|
| [Redis 模块](#choosing-the-right-tier) |-|-|-|✔|✔|
| [导入/导出](cache-how-to-import-export-data.md) |-|-|✔|✔|✔|
| [重新启动](cache-administration.md#reboot) |✔|✔|✔|-|-|
| [计划的更新](cache-administration.md#schedule-updates) |✔|✔|✔|-|-|

### <a name="choosing-the-right-tier"></a>选择正确的层

选择 Azure Cache for Redis 层时，请考虑以下选项：

* **内存**：基本层和标准层提供 250 MB - 53 GB；高级层提供 6 GB - 1.2 TB；企业层提供 12 GB - 14 TB。  若要创建大于 120 GB 的高级层缓存，可以使用 Redis OSS 群集。 有关详细信息，请参阅 [Azure Redis 缓存定价](https://azure.microsoft.com/pricing/details/cache/)。 有关详细信息，请参阅[如何为高级 Azure Redis 缓存配置群集功能](cache-how-to-premium-clustering.md)。
* **性能**：高级层和企业层中的缓存部署在处理器速度更快的硬件上，可提供比基本层或标准层更好的性能。 高级级别缓存的吞吐量更高，延迟更低。 有关详细信息，请参阅 [Azure Cache for Redis 性能](./cache-planning-faq.yml#azure-cache-for-redis-performance)。
* **针对 Redis 服务器的专用核心**：除 C0 之外的所有缓存均运行专用 VM 核心。 根据设计，Redis 只使用一个线程进行命令处理。 Azure Cache for Redis 使用其他核心进行 I/O 处理。 拥有更多的内核可能不会产生线性缩放，但可提高吞吐量性能。 而且，较大 VM 的带宽限制通常比较小 VM 的更高。 这有助于避免网络饱和，从而避免应用程序超时。
* **网络性能**：如果工作负载需要较高的吞吐量，则可使用高级层或企业层，这两层可提供比基本层或标准层更高的带宽。 另外，在每个层中，缓存大小越大，带宽越高，因为是由基础 VM 托管缓存。 有关详细信息，请参阅 [Azure Cache for Redis 性能](./cache-planning-faq.yml#azure-cache-for-redis-performance)。
* **客户端连接的最大数量**：高级层和企业层提供的可以连接到 Redis 的客户端数量是最大的，缓存大小越大，连接数量越大。 群集会增加可用于群集缓存的总网络带宽量。
* **高可用性**：Azure Cache for Redis 提供了多个 [高可用性](cache-high-availability.md)选项。 它保证标准、高级或企业缓存的可用性符合我们的 [SLA](https://azure.microsoft.com/support/legal/sla/cache/v1_0/)。 SLA 仅涉及与缓存终结点的连接。 SLA 不涉及对数据丢失的防护。 我们建议使用高级层和企业层中的 Redis 数据暂留功能来提高复原能力，防止数据丢失。
* **数据暂留**：高级层和企业层可用于分别在 Azure 存储账户和托管磁盘中暂留缓存数据。 底层基础结构问题可能会导致潜在的数据丢失。 我们建议使用这些层中的 Redis 数据暂留功能来提高复原能力，防止数据丢失。 Azure Cache for Redis 同时提供 RDB 和 AOF（预览版）选项。 可通过 Azure 门户和 CLI 启用数据持久性。 对于高级层，请参阅[如何为高级 Azure Cache for Redis 配置暂留](cache-how-to-premium-persistence.md)。
* **网络隔离**：Azure 专用链接和虚拟网络 (VNET) 部署为 Azure Cache for Redis 提供增强的安全性和流量隔离。 使用 VNET，可以进一步通过网络访问控制策略来限制访问。 有关详细信息，请参阅[具有 Azure 专用链接的 Azure Cache for Redis](cache-private-link.md) 和[如何为高级 Azure Cache for Redis 配置虚拟网络支持](cache-how-to-premium-vnet.md)。
* **Redis 模块**：企业层支持 [RediSearch](https://docs.redislabs.com/latest/modules/redisearch/)、[RedisBloom](https://docs.redislabs.com/latest/modules/redisbloom/) 和 [RedisTimeSeries](https://docs.redislabs.com/latest/modules/redistimeseries/)。 这些模块向 Redis 添加了新的数据类型和功能。

创建缓存后，可以将其从基本层升级到高级层。 目前不支持纵向缩放到更低的层。 有关缩放的分步说明，请参阅[如何缩放 Azure Redis 缓存](cache-how-to-scale.md)和[如何自动执行缩放操作](cache-how-to-scale.md#how-to-automate-a-scaling-operation)。

### <a name="special-considerations-for-enterprise-tiers"></a>企业层的特殊注意事项

企业层依赖于 Redis Enterprise，它是 Redis 实验室提供的商业版 Redis。 客户将通过 Azure 市场产品/服务获取此软件的许可证并为其付费。 Azure Cache for Redis 将管理许可证获取，使你无需单独执行此操作。 若要在 Azure 市场中购买，必须具备以下先决条件：
* Azure 订阅具有有效的付款方式。 不支持 Azure 额度或免费 MSDN 订阅。
* 你的组织允许进行 [Azure 市场购买](../cost-management-billing/manage/ea-azure-marketplace.md#enabling-azure-marketplace-purchases)。
* 如果你使用的是专用市场，它必须包含 Redis 实验室企业产品/服务。

> [!IMPORTANT]
> Azure Cache for Redis Enterprise 需要标准网络负载均衡器，这些均衡器与缓存实例分开收费。 有关详细信息，请参阅[负载均衡器定价](https://azure.microsoft.com/pricing/details/load-balancer/)。
> 如果为多个可用性区域配置 Enterprise 缓存，则从 2021 年 7 月 1 日起，将按[标准网络带宽费率](https://azure.microsoft.com/pricing/details/bandwidth/)对数据传输进行计费。
>
> 此外，数据持久性会添加托管磁盘。 在企业数据持久性功能的公共预览版期间，可免费使用这些资源。 该功能正式发布后，可能不再免费。
>
>

## <a name="next-steps"></a>后续步骤

* [创建开放源 Redis 缓存](quickstart-create-redis.md)
* [创建 Redis Enterprise 缓存](quickstart-create-redis-enterprise.md)
* [在 ASP.NET Web 应用中使用 Azure Cache for Redis](cache-web-app-howto.md)
* [在 .NET Core 中使用 Azure Cache for Redis](cache-dotnet-core-quickstart.md)
* [在 .NET Framework 中使用 Azure Cache for Redis](cache-dotnet-how-to-use-azure-redis-cache.md)
* [在 Node.js 中使用 Azure Cache for Redis](cache-nodejs-get-started.md)
* [在 Java 中使用 Azure Cache for Redis](cache-java-get-started.md)
* [在 Python 中使用 Azure Cache for Redis](cache-python-get-started.md)