---
title: 适用于 Azure 上的 Oracle 数据库的参考体系结构 | Microsoft Docs
description: 用于在 Microsoft Azure 虚拟机上运行 Oracle Database Enterprise Edition 数据库的参考体系结构。
author: dbakevlar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 12/13/2019
ms.author: kegorman
ms.openlocfilehash: a8171d29dfe88ea34d02d7f48567aeb2df46a8a7
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122694133"
---
# <a name="reference-architectures-for-oracle-database-enterprise-edition-on-azure"></a>适用于 Azure 上的 Oracle Database Enterprise Edition 的参考体系结构

**适用于：** :heavy_check_mark: Linux VM 

本指南详细介绍如何在 Azure 上部署高可用性 Oracle 数据库。 此外，本指南还深入探讨灾难恢复注意事项。 这些体系结构根据客户部署创建。 本指南仅适用于 Oracle Database Enterprise Edition。

如果有兴趣了解有关如何最大程度提高 Oracle 数据库性能的详细信息，请参阅[设计 Oracle DB](oracle-design.md)。

## <a name="assumptions"></a>假设

- 你了解 Azure 的不同概念，如[可用性区域](../../../availability-zones/az-overview.md)
- 你正在运行 Oracle Database Enterprise Edition 12c 或更高版本
- 你在使用本文中的解决方案时已了解并确认许可含义

## <a name="high-availability-for-oracle-databases"></a>Oracle 数据库的高可用性

在云中实现高可用性是每个组织规划和设计的重要组成部分。 Microsoft Azure 提供[可用性区域](../../../availability-zones/az-overview.md)和可用性集（用于在可用性区域不可用的区域中使用）。 详细了解如何[管理虚拟机的可用性](../../availability.md)，以便进行云方面的设计。

除了云原生工具和产品/服务，Oracle 还提供实现高可用性的解决方案，如 [Oracle Data Guard](https://docs.oracle.com/en/database/oracle/oracle-database/18/sbydb/introduction-to-oracle-data-guard-concepts.html#GUID-5E73667D-4A56-445E-911F-1E99092DD8D7)、[Data Guard with FSFO](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/index.html)、[Sharding](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/admin/sharding-overview.html) 和 [GoldenGate](https://www.oracle.com/middleware/technologies/goldengate.html)（可在 Azure 上设置）。 本指南介绍其中每种解决方案的参考体系结构。

最后，在为云迁移或创建应用程序时，请务必调整应用程序代码以添加云原生模式，如[重试模式](/azure/architecture/patterns/retry)和[断路器模式](/azure/architecture/patterns/circuit-breaker)。 [云设计模式指南](/azure/architecture/patterns/)中定义的其他模式可帮助应用程序更具复原能力。

### <a name="oracle-rac-in-the-cloud"></a>云中的 Oracle RAC

Oracle Real Application Cluster (RAC) 是 Oracle 的一种解决方案，可通过让多个实例访问一个数据库存储（共享 - 所有体系结构模式）来帮助客户实现高吞吐量。 虽然 Oracle RAC 还可以用于本地高可用性，但不能单独使用 Oracle RAC 在云中实现高可用性，因为它仅针对实例级故障提供保护，而不会针对机架级或数据中心级故障提供保护。 因此，Oracle 建议将 Oracle Data Guard 与数据库（无论是单实例还是 RAC）配合使用，以实现高可用性。 客户通常需要使用高 SLA 来运行其任务关键型应用程序。 Oracle RAC 当前未通过 Azure 上的 Oracle 认证，也不受其支持。 但是，Azure 提供了一些功能（例如 Azure 提供了可用性区域和计划内维护时段），帮助防止实例级故障。 除此之外，客户还可以使用 Oracle Data Guard、Oracle GoldenGate 和 Oracle Sharding 等技术来提高性能和复原能力，方法是保护其数据库不受机架级故障以及数据中心级故障和地理政治故障。

跨多个[可用性区域](../../../availability-zones/az-overview.md)与 Oracle Data Guard 或 GoldenGate 一起运行 Oracle Database 时，客户能够获得 99.99% 的运行时间 SLA。 在可用性区域尚不存在的 Azure 区域中，客户可使用[可用性集](../../availability-set-overview.md)并实现 99.95% 的运行时间 SLA。

>注意：运行时间目标可远远高于 Microsoft 提供的运行时间 SLA。

## <a name="disaster-recovery-for-oracle-databases"></a>Oracle 数据库的灾难恢复

在云中托管任务关键型应用程序时，在实现高可用性和灾难恢复方面进行设计十分重要。

对于 Oracle Database Enterprise Edition，Oracle Data Guard 是一项用于灾难恢复的有用功能。 可以在[配对的 Azure 区域](../../../best-practices-availability-paired-regions.md)中设置备用数据库实例，并设置 Data Guard 故障转移，以便实现灾难恢复。 对于零数据丢失，建议除 Active Data Guard 外，还部署 Oracle Data Guard Far Sync 实例。 

如果应用程序允许延迟（需要进行彻底测试），不妨考虑在不同于 Oracle 主数据库的可用性区域中设置 Data Guard Far Sync 实例。 使用最大可用性模式来设置恢复文件到 Far Sync 实例的同步传输。 然后，这些文件将异步传输到备用数据库。 

如果应用程序在最大可用性模式（同步）下在另一个可用性区域中设置 Far Sync 实例时不允许性能损失，则可以在与主数据库相同的可用性区域中设置 Far Sync 实例。 为了提高可用性，请考虑在主数据库附近设置多个 Far Sync 实例，并在备用数据库附近至少设置一个实例（如果角色转换）。 详细阅读此 [Oracle Active Data Guard Far Sync 白皮书](https://www.oracle.com/technetwork/database/availability/farsync-2267608.pdf)中有关 Oracle Data Guard Far Sync 的信息。

使用 Oracle Standard Edition 数据库时，可以使用 DBVisit 备用等 ISV 解决方案来设置高可用性和灾难恢复。

## <a name="reference-architectures"></a>参考体系结构

### <a name="oracle-data-guard"></a>Oracle 数据防护

Oracle Data Guard 可确保企业数据的高可用性、数据保护和灾难恢复。 Data Guard 将备用数据库作为主数据库的事务一致副本进行维护。 根据主数据库和辅助数据库之间的距离以及应用程序对延迟的容差，可以设置同步或异步复制。 然后，如果主数据库因计划内或计划外中断而不可用，Data Guard 可以将任何备用数据库切换为主要角色，从而最大程度地减少故障时间。 

使用 Oracle Data Guard 时，还能够以只读目的打开辅助数据库。 此配置称为 Active Data Guard。 Oracle Database 12c 引入了一项称为 Data Guard Far Sync 实例的功能。 借助此实例，你可以设置 Oracle 数据库的零数据丢失配置，且不会影响性能。

> [!NOTE]
> Active Data Guard 需要额外的许可。 使用 Far Sync 功能也需要此许可证。 请与 Oracle 代表联系以讨论许可影响。

#### <a name="oracle-data-guard-with-fsfo"></a>Oracle Data Guard Far Sync
Oracle Data Guard with Fast-Start Failover (FSFO) 可通过在单独的计算机上设置代理来提供额外的复原能力。 Data Guard Broker 和辅助数据库均运行观察程序，并观察主数据库的故障时间。 这也允许在 Data Guard 观察程序设置中实现冗余。 

在 Oracle Database 版本 12.2 及更高版本中，还可以使用单个 Oracle Data Guard Broker 配置来配置多个观察程序。 如果一个观察程序和辅助数据库遇到故障时间，那么此设置可提供额外的可用性。 Data Guard Broker 比较轻型，可托管在相对较小的虚拟机上。 若要详细了解 Data Guard Broker 及其优点，请访问关于本主题的 [Oracle 文档](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/oracle-data-guard-broker-concepts.html)。

下图是将 Azure 上的 Oracle Data Guard 与可用性区域配合使用的建议体系结构。 此体系结构使你可获得 99.99% 的 VM 运行时间 SLA。

![该图显示了将 Azure 上的 Oracle Data Guard 与可用性区域配合使用时的建议体系结构。](./media/oracle-reference-architecture/oracledb_dg_fsfo_az.png)

在上图中，客户端系统使用 Oracle 后端通过 Web 访问自定义应用程序。 Web 前端在负载均衡器中进行配置。 Web 前端对适当的应用程序服务器进行调用以处理工作。 应用程序服务器查询主 Oracle 数据库。 Oracle 数据库已使用超线程[内存优化虚拟机](../../sizes-memory.md)进行配置（该虚拟机具有[受约束的核心 vCPU](../../../virtual-machines/constrained-vcpu.md)），以节省许可成本并最大限度地提高性能。 多个高级或超级磁盘（托管磁盘）用于实现性能和高可用性。

Oracle 数据库放置在多个可用性区域中，以实现高可用性。 每个区域由一个或多个数据中心组成，这些数据中心配置了独立电源、冷却和网络。 为确保复原能力，所有已启用的区域中至少设置了三个单独的区域。 数据中心发生故障时，区域中的可用性区域的物理隔离可保护数据。 此外，在两个可用性区域之间设置两个 FSFO 观察程序，以便在发生故障时启动数据库并将数据库故障转移到辅助区域。 

可以在与先前体系结构所示区域不同的可用性区域（本例中为 AZ 1）中设置其他观察程序和/或备用数据库。 最后，Oracle Enterprise Manager (OEM) 会监视 Oracle 数据库的运行时间和性能。 OEM 还让你能够生成各种性能和使用情况报告。

在不支持可用性区域的区域中，可以使用可用性集以高度可用的方式部署 Oracle Database。 可用性集可让你实现 99.95% 的 VM 运行时间。 下图是此用法的参考体系结构：

![Oracle Database 将可用性集与 Data Guard Broker - FSFO 配合使用](./media/oracle-reference-architecture/oracledb_dg_fsfo_as.png)

> [!NOTE]
> * 不需要将 Oracle Enterprise Manager VM 放置在可用性集中，因为只会部署 OEM 的一个实例。
> * 当前不支持在可用性集配置中使用超级磁盘。

#### <a name="oracle-data-guard-far-sync"></a>Oracle Data Guard Far Sync

Oracle Data Guard Far Sync 为 Oracle Database 提供零数据丢失保护功能。 借助此功能，你可以在数据库计算机出现故障时防止数据丢失。 Oracle Data Guard Far Sync 需要安装在单独的 VM 上。 Far Sync 是一种轻型 Oracle 实例，仅包含控制文件、密码文件、spfile 和备用日志。 没有数据文件或 rego 日志文件。 

为实现零数据丢失保护，主数据库和 Far Sync 实例之间必须进行同步通信。 Far Sync 实例以同步方式从主副本接收恢复内容，并以异步方式将其立即转发到所有备用数据库。 此设置还将减少主数据库上的开销，因为它只需将恢复内容发送到 Far Sync 实例，而不是所有备用数据库。 如果 Far Sync 实例失败，Data Guard 会自动使用从主数据库到辅助数据库的异步传输，以维持近乎为零的数据丢失保护。 为提升复原能力，客户可以为每个数据库实例（主数据库和辅助数据库实例）部署多个 Far Sync 实例。

下图是一种使用 Oracle Data Guard Far Sync 的高可用性体系结构：

![Oracle Database 将可用性区域与 Data Guard Far Sync 和 Broker - FSFO 配合使用](./media/oracle-reference-architecture/oracledb_dg_fs_az.png)

在前面的体系结构中，在数据库实例所在的同一可用性区域中部署有 Far Sync 实例，以减少这两者之间的延迟。 如果应用程序对延迟敏感，请考虑将数据库和 Far Sync 实例部署在[邻近放置组](../../../virtual-machines/linux/proximity-placement-groups.md)中。

下图是一种利用 Oracle Data Guard FSFO 和 Far Sync 实现高可用性和灾难恢复的体系结构：

![Oracle Database 将可用性区域与 Data Guard Far Sync 和 Broker - FSFO 配合使用以进行灾难恢复](./media/oracle-reference-architecture/oracledb_dg_fs_az_dr.png)

### <a name="oracle-goldengate"></a>Oracle GoldenGate

GoldenGate 支持在整个企业的多种异构平台之间在事务级交换和操作数据。 它将提交的事务移至现有基础结构上，同时保持事务完整性，并将开销降至最低。 借助其模块化体系结构，你可以灵活地在各种拓扑中提取并复制所选数据记录、事务更改以及对 DDL（数据定义语言）的更改。

Oracle GoldenGate 通过提供双向复制，让你可配置数据库来实现高可用性。 这样便可以设置多主数据库或主动-主动配置 。 以下关系图是 Azure 上 Oracle GoldenGate 主动-主动设置的建议体系结构。 在以下体系结构中，Oracle 数据库已使用超线程[内存优化虚拟机](../../sizes-memory.md)进行配置（该虚拟机具有[受约束的核心 vCPU](../../../virtual-machines/constrained-vcpu.md)），以节省许可成本并最大限度地提高性能。 多个高级或超级磁盘（托管磁盘）用于实现性能和可用性。

![Oracle Database 将可用性区域与 Data Guard Broker - FSFO 配合使用](./media/oracle-reference-architecture/oracledb_gg_az.png)

> [!NOTE]
> 在可用性区域当前不可用的区域中，可以使用可用性集来设置类似的体系结构。

Oracle GoldenGate 的进程（如提取、抽取和 Replicat）有助于将数据从一个 Oracle 数据库服务器异步复制到另外一个。 借助这些进程，你可以设置双向复制，以确保数据库的高可用性，前提是存在可用性区域级故障时间。 在上面的关系图中，提取进程与 Oracle 数据库在同一服务器上运行，而数据抽取和 Replicat 进程各自在同一可用性区域的单独服务器上运行。 Replicat 进程用于接收另一个可用性区域中的数据库中的数据，并将数据提交到其可用性区域中的 Oracle 数据库。 同样，数据抽取进程会将提取进程提取的数据发送到另一个可用性区域中的 Replicat 进程。 

虽然前面的体系结构关系图显示了在单独服务器上配置的数据抽取和 Replicat 进程，但你可以根据服务器的容量和使用情况在同一服务器上设置所有 Oracle GoldenGate 进程。 请始终查阅 AWR 报告和 Azure 中的指标，了解服务器的使用模式。

在不同可用性区域或不同区域中设置 Oracle GoldenGate 双向复制时，务必确保应用程序可接受不同组件之间的延迟。 可用性区域和区域之间的延迟可能会有所不同，具体取决于多种因素。 建议在不同可用性区域和/或区域中的应用层和数据库层之间设置性能测试，以确认其符合应用程序性能要求。

应用层可在其自身的子网中设置，数据库层可分离到其自身的子网中。 如有可能，请考虑使用 [Azure 应用程序网关](../../../application-gateway/overview.md)对应用程序服务器之间的流量进行负载均衡。 Azure 应用程序网关是一种可靠的 Web 流量负载均衡器。 该网关提供了基于 cookie 的会话亲和性，可让用户会话保留在同一服务器上，从而最大程度减少数据库上的冲突。 应用程序网关的替代项为 [Azure 负载均衡器](../../../load-balancer/load-balancer-overview.md)和 [Azure 流量管理器](../../../traffic-manager/traffic-manager-overview.md)。

### <a name="oracle-sharding"></a>Oracle Sharding

Sharding 是 Oracle 12.2 中引入的一种数据层模式。 你可以借助它在独立数据库之间对数据进行横向分区和缩放。 这是一种无共享体系结构，其中每个数据库都托管在专用虚拟机上，这样一来，除实现复原能力和可用性提升外，还可以实现较高的读取和写入吞吐量。 此模式消除了单一故障点，提供故障隔离，并支持滚动升级，且没有故障时间。 一个分片或数据中心级故障的故障时间不会影响其他数据中心中其他分片的性能或可用性。 

Sharding 适用于不能承受故障时间的高吞吐量 OLTP 应用程序。 具有相同分片键的所有行都位于同一分片上这一点可始终得到保证，从而提升性能，并提供较高一致性。 使用分片的应用程序必须具有定义明确的数据模型和数据分发策略（一致的哈希、范围、列表或复合），该策略主要使用分片键（例如 customerId 或 accountNum）访问数据 。 借助 Sharding，你还可以将特定数据集存储在更接近最终客户的位置，从而帮助满足性能和合规性要求。

建议复制分片，以实现高可用性和灾难恢复。 可以使用 Oracle Data Guard 或 Oracle GoldenGate 等 Oracle 技术来完成此设置。 复制单元可以是分片或分片的一部分，也可以是一组分片。 分片数据库的可用性不受一个或多个分片故障或减速的影响。 为实现高可用性，可以将备用分片放置在主分片所在的同一可用性区域中。 对于灾难恢复，备用分片可以位于另一个区域。 还可以在多个区域中部署分片，以提供这些区域中的流量。 在 [Oracle Sharding 文档](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-high-availability.html)中了解有关配置分片数据库的高可用性和复制的详细信息。

Oracle Sharding 主要包含以下组件。 有关这些组件的详细信息，请参阅 [Oracle Sharding 文档](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-overview.html)：

- **分片目录** - 专用 Oracle 数据库，是所有分片数据库配置数据的永久存储。 所有配置更改（如添加或删除分片、数据映射和分片数据库中的 DDL）都在分片目录中启动。 分片目录还包含 SDB 中所有复制表的主副本。 

  分片目录使用具体化视图自动将更改复制到所有分片中的复制表。 分片目录数据库还充当查询协调器，用于处理多分片查询和未指定分片键的查询。 
  
  将 Oracle Data Guard 与可用性区域或可用性集结合使用来实现分片目录的高可用性是建议的最佳做法。 分片目录的可用性不会影响分片数据库的可用性。 分片目录中的故障时间仅会影响 Data Guard 故障转移完成这一短暂期间的维护操作和多分片查询。 联机事务继续由 SDB 进行路由和执行，不受目录故障的影响。

- **分片控制器** - 需在分片驻留的每个区域/可用性区域中进行部署的轻量级服务。 分片控制器是在 Oracle Sharding 上下文中部署的 Global Service Manager。 为实现高可用性，建议在分片所在的每个可用性区域中至少部署一个分片控制器。 

  一开始连接到数据库时，路由信息由分片控制器设置，并为后续请求缓存，同时会绕过分片控制器。 通过分片建立会话后，所有 SQL 查询和 DML 都受支持，并在给定分片范围内执行。 此路由速度快，并用于执行分片内事务的所有 OLTP 工作负载。 建议将直接路由用于需要最高性能和可用性的所有 OLTP 工作负载。 当分片变得不可用或对分片拓扑进行更改时，路由缓存将自动刷新。 
  
  对于依赖于数据的高性能路由，Oracle 建议在访问分片数据库中的数据时使用连接池。 Oracle 连接池、特定于语言的库和驱动程序支持 Oracle Sharding。 请参阅 [Oracle Sharding 文档](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-overview.html#GUID-3D41F762-BE04-486D-8018-C7A210D809F9)，了解更多详细信息。

- **全局服务** - 全局服务类似于常规数据库服务。 除了数据库服务的所有属性外，全局服务还具有分片数据库的属性，例如客户端之间的区域相关性以及分片和复制滞后容差。 只需创建一种全局服务即可从/向分片数据库中读取/写入数据。 使用 Active Data Guard 并设置分片的只读副本时，可以为只读工作负载创建另一种全局服务。 客户端可以使用这些全局服务连接到数据库。

- **分片数据库** - 分片数据库是你的 Oracle 数据库。 每个数据库都使用 Oracle Data Guard 在 Broker 配置中进行复制，并启用 Fast-Start Failover (FSFO)。 无需在每个分片上都设置 Data Guard 故障转移和复制。 创建共享数据库时便会对此自动配置和部署。 如果特定分片失败，则 Oracle Sharing 会自动从主数据库到备用数据库对数据库连接进行故障转移。

可以使用两个界面来部署和管理 Oracle 分片数据库：Oracle Enterprise Manager Cloud Control GUI 和/或 `GDSCTL` 命令行实用程序。 甚至可以使用云控制来监视不同分片的可用性和性能。 `GDSCTL DEPLOY` 命令会自动创建分片及其相应的侦听器。 此外，此命令会自动部署用于管理员指定的分片级高可用性的复制配置。

可通过不同方式对数据库分片：

* 系统托管分片 - 使用分区跨分片自动分发
* 用户定义的分片 - 允许指定数据到分片的映射，这在具有法规或数据本地化要求时效果很好
* 复合分片 - 不同分片空间的系统托管分片和用户定义的分片的组合
* 表子分区 - 类似于常规已分区表。

在 Oracle 的文档中了解有关不同[分片方法](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-methods.html)的详细信息。

尽管对于应用程序和开发人员而言，分片数据库看起来像是单一数据库，但从非分片数据库迁移到分片数据库时，需要进行仔细规划，以确定要复制与分片的表。 

复制表存储在所有分片上，而分片表分布在不同分片中。 建议复制小型表和维度表，并对事实数据表进行分配/分片。 数据可以加载到分片数据库中，方法是使用分片目录作为中心协调器或在每个分片上运行数据抽取。 在 Oracle 的文档中了解有关[将数据迁移到分片数据库](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-loading-data.html)的详细信息。

#### <a name="oracle-sharding-with-data-guard"></a>使用 Data Guard 的 Oracle Sharding

Oracle Data Guard 可用于通过系统托管分片、用户定义的分片和复合分片方法进行分片。

下图是将 Oracle Data Guard 用于实现每个分片的高可用性的 Oracle Sharding 的参考体系结构。 该体系结构关系图显示了复合分片方法。 对于对数据区域、负载均衡、高可用性、灾难恢复等具有不同要求的应用程序，该体系结构图可能会有所不同，并且可能会使用不同的分片方法。 Oracle Sharding 通过提供这些选项，可让你满足这些要求，并高效进行横向缩放。 甚至可以使用 Oracle GoldenGate 部署类似的体系结构。

![Oracle Database Sharding 将可用性区域与 Data Guard Broker - FSFO 配合使用](./media/oracle-reference-architecture/oracledb_dg_sh_az.png)

虽然系统托管分片最容易配置和管理，但用户定义的分片或复合分片非常适用于数据和应用程序是异地分布的场景（你需要控制每个分片的复制情况）。 

在前面的体系结构中，复合分片用于对数据进行异地分布，并横向扩展应用层。 复合分片是系统托管分片和用户定义的分片的组合，因此可提供这两种方法的优点。 在上述场景中，首先在由区域分隔的多个分片空间之间对数据分片。 然后，在分片空间中的多个分片上通过一致哈希对数据进行进一步分区。 每个分区空间包含多个分区组。 在这种情况下，每个分区组都有多个分片，是复制的“单元”。 每个分区组都包含分区空间中的所有数据。 分区组 A1 和 B1 是主分区组，而分区组 A2 和 B2 均为备用分区组。 可以选择将单个分片作为复制单元，而不是选择分区组。

在前面的体系结构中，每个可用性区域中都部署了 GSM/分片控制器，以实现高可用性。 建议为每个数据中心/区域至少部署一个 GSM/分片控制器。 此外，还会在包含分区组的每个可用性区域中部署应用程序服务器的实例。 此设置使应用程序可让应用程序服务器与数据库/分区组之间的延迟保持较短时间。 如果数据库发生故障，在发生数据库角色转换后，与备用数据库位于同一区域中的应用程序服务器便可以处理请求。 Azure 应用程序网关和分片控制器将跟踪请求和响应延迟，并相应地路由请求。

从应用程序的角度来看，客户端系统向 Azure 应用程序网关（或 Azure 中的其他负载平衡技术）发出请求，后者将请求重定向到离客户端最近的区域。 Azure 应用程序网关还支持粘滞会话，因此来自同一客户端的任何请求都将路由到同一应用程序服务器。 应用程序服务器在数据访问驱动程序中使用连接池。 JDBC、ODP.NET、OCI 等驱动程序中提供了此功能。驱动程序可以识别请求中指定的分片键。 用于 JDBC 客户端的 [Oracle Universal Connection Pool (UCP)](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/jjucp/ucp-database-sharding-overview.html) 使非 Oracle 应用程序客户端（如 Apache Tomcat 和 IIS）能够与 Oracle Sharding 配合使用。 

在初始请求期间，应用程序服务器将连接到其区域中的分片控制器，以获取需要将请求路由到的分片的路由信息。 根据传递的分片键，控制器会将应用程序服务器路由到各自的分片。 应用程序服务器通过构建映射来缓存此信息，而对于后续请求，则绕过分片控制器并直接将请求路由到分片。

#### <a name="oracle-sharding-with-goldengate"></a>使用 GoldenGate 的 Oracle Sharding

下图是将 Oracle GoldenGate 用于每个分片的区域内高可用性的 Oracle Sharding 的参考体系结构。 与上述体系结构不同，此体系结构仅描述单个 Azure 区域（多个可用性区域）中的高可用性。 可以使用 Oracle GoldenGate 部署多区域高可用性分片数据库（类似于前面的示例）。

![Oracle Database Sharding 将可用性区域与 GoldenGate 配合使用](./media/oracle-reference-architecture/oracledb_gg_sh_az.png)

上述参考体系结构使用系统托管分片方法来对数据进行分片。 由于 Oracle GoldenGate 复制在区块级完成，因此，复制到一个分片的数据可复制到另一个分片。 另一半可复制到其他分片。 

复制数据的方式取决于复制因子。 若复制因子为 2，在分片组中的三个分片之间，你将拥有每个数据块的两个副本。 同样，若复制因子为 3，分片组中有三个分片，那么每个分片中的所有数据都将复制到该分片组中的所有其他分片中。 分片组中的每个分片都有不同的复制因子。 此设置可帮助你在分片组中以及跨多个分片组有效地定义高可用性和灾难恢复设计。

在前面的体系结构中，分片组 A 和分片组 B 都包含相同数据，但驻留在不同的可用性区域中。 如果分片组 A 和分片组 B 的复制因子均为 3，则分片表的每个行/块将在两个分片组之间复制 6 次。 如果分片组 A 的复制因子为 3，分片组 B 的复制因子为 2，则每个行/块区将在两个分片组之间复制 5 次。

如果发生实例级或可用性区域级故障，此设置可防止数据丢失。 应用程序层能够对每个分片进行读取和写入。 为最大限度减少冲突，Oracle Sharding 为每个哈希值范围指定了一个“主块”。 此功能可确保将特定块区的写入请求定向到相应的区块。 此外，Oracle GoldenGate 提供自动冲突检测和解决方法来处理可能出现的所有冲突。 有关向 Oracle Sharding 实现 GoldenGate 的详细信息和限制，请参阅有关将 [Oracle GoldenGate 与分片数据库配合使用](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-high-availability.html#GUID-4FC0AC46-0B8B-4670-BBE4-052228492C72)的 Oracle 文档。

在前面的体系结构中，每个可用性区域中都部署了 GSM/分片控制器，以实现高可用性。 建议为每个数据中心或区域至少部署一个 GSM/分片控制器。 此外，还会在包含分区组的每个可用性区域中部署应用程序服务器的实例。 此设置使应用程序可让应用程序服务器与数据库/分区组之间的延迟保持较短时间。 如果数据库发生故障，在数据库角色转换后，与备用数据库位于同一区域中的应用程序服务器便可以处理请求。 Azure 应用程序网关和分片控制器将跟踪请求和响应延迟，并相应地路由请求。

从应用程序的角度来看，客户端系统向 Azure 应用程序网关（或 Azure 中的其他负载平衡技术）发出请求，后者将请求重定向到离客户端最近的区域。 Azure 应用程序网关还支持粘滞会话，因此来自同一客户端的任何请求都将路由到同一应用程序服务器。 应用程序服务器在数据访问驱动程序中使用连接池。 JDBC、ODP.NET、OCI 等驱动程序中提供了此功能。驱动程序可以识别请求中指定的分片键。 用于 JDBC 客户端的 [Oracle Universal Connection Pool (UCP)](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/jjucp/ucp-database-sharding-overview.html) 使非 Oracle 应用程序客户端（如 Apache Tomcat 和 IIS）能够与 Oracle Sharding 配合使用。 

在初始请求期间，应用程序服务器将连接到其区域中的分片控制器，以获取需要将请求路由到的分片的路由信息。 根据传递的分片键，控制器会将应用程序服务器路由到各自的分片。 应用程序服务器通过构建映射来缓存此信息，而对于后续请求，则绕过分片控制器并直接将请求路由到分片。

## <a name="patching-and-maintenance"></a>修补和维护

将 Oracle 工作负载部署到 Azure 时，Microsoft 会负责所有主机 OS 级修补。 任何计划 OS 级维护都将提前传达给客户，以便客户进行此计划内维护。 两个不同可用性区域的两个服务器绝不会同时修补。 请参阅[管理虚拟机的可用性](../../availability.md)，获取有关 VM 维护和修补的详细信息。 

可以使用 [Azure 自动化更新管理](../../../automation/update-management/overview.md)来自动修补虚拟机操作系统。 可以使用 [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines) 或 [Azure 自动化更新管理](../../../automation/update-management/overview.md)来自动执行和安排 Oracle 数据库的修补和维护，以最大程度减少故障时间。 请参阅[持续交付和蓝/绿部署](/devops/deliver/what-is-continuous-delivery)，了解其在 Oracle 数据库上下文中使用方法。

## <a name="architecture-and-design-considerations"></a>体系结构和设计注意事项

- 请考虑对 Oracle Database VM 使用超线程[内存优化虚拟机](../../sizes-memory.md)（具有[受约束的核心 vCPU](../../../virtual-machines/constrained-vcpu.md)），以节省许可成本并最大限度地提高性能。 使用多个高级或超级磁盘（托管磁盘）来实现性能和可用性。
- 使用托管磁盘时，磁盘/设备名可能会在重启时更改。 建议使用设备 UUID（而不是名称）来确保重启后可保留你的装载内容。 有关详细信息，请参阅[在 Linux VM 上配置软件 RAID](/previous-versions/azure/virtual-machines/linux/configure-raid#add-the-new-file-system-to-etcfstab)。
- 使用可用性区域实现区域内的高可用性。
- 考虑为 Oracle 数据库使用超级磁盘（如果可用）或高级磁盘。
- 请考虑使用 Oracle Data Guard 在另一个 Azure 区域中设置备用 Oracle 数据库。
- 请考虑使用[邻近的放置组](../../co-location.md#proximity-placement-groups)来降低应用层和数据库层之间的延迟。
- 设置 [Oracle Enterprise Manage](https://docs.oracle.com/en/enterprise-manager/) 以进行管理、监视和日志记录。
- 请考虑使用 Oracle Automatic Storage Management (ASM) 来简化数据库的存储管理。
- 使用 [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines) 以在无故障时间的情况下管理对数据库的修补和更新。
- 调整应用程序代码以添加云原生模式，如[重试模式](/azure/architecture/patterns/retry)、[断路器模式](/azure/architecture/patterns/circuit-breaker)，以及[云设计模式指南](/azure/architecture/patterns/)中定义的其他模式，这些模式有助于让应用程序更具复原能力。

## <a name="next-steps"></a>后续步骤

查看适用于自身场景的以下 Oracle 参考文章。

- [Oracle Data Guard 简介](https://docs.oracle.com/en/database/oracle/oracle-database/18/sbydb/introduction-to-oracle-data-guard-concepts.html#GUID-5E73667D-4A56-445E-911F-1E99092DD8D7)
- [Oracle Data Guard Broker 概念](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/oracle-data-guard-broker-concepts.html)
- [配置 Oracle GoldenGate 以实现主动-主动高可用性](https://docs.oracle.com/goldengate/1212/gg-winux/GWUAD/wu_bidirectional.htm#GWUAD282)
- [Oracle Sharding 概述](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-overview.html)
- [任何距离下的 Oracle Active Data Guard Far Sync 零数据丢失](https://www.oracle.com/technetwork/database/availability/farsync-2267608.pdf)
