---
title: 性能最佳做法 - Azure Database for MySQL
description: 本文介绍了一些监视和调整 Azure Database for MySQL 性能的建议。
author: Bashar-MSFT
ms.author: bahusse
ms.service: mysql
ms.topic: conceptual
ms.date: 1/28/2021
ms.openlocfilehash: dc47e06643f3bb4ebf986382e149e13fd9bbc968
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779694"
---
# <a name="best-practices-for-optimal-performance-of-your-azure-database-for-mysql---single-server"></a>获得 Azure Database for MySQL 单一服务器的最佳性能的最佳做法

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

了解如何在使用 Azure Database for MySQL 单一服务器时获得最佳性能。 在向平台添加新功能时，我们将继续优化本部分中的建议。

## <a name="physical-proximity"></a>物理邻近性

 请确保在同一区域中部署应用程序和数据库。 在开始任何性能基准测试运行之前执行的快速检查是使用简单的 SELECT 1 查询确定客户端和数据库之间的网络延迟。 

## <a name="accelerated-networking"></a>加速网络

如果使用 Azure 虚拟机、Azure Kubernetes 或应用服务，请为应用程序服务器使用加速网络。 使用加速网络可以实现对 VM 的单根 I/O 虚拟化 (SR-IOV)，大幅提升其网络性能。 这种高性能路径会绕过数据路径中的主机，降低延迟、抖动，以及受支持 VM 类型上的最苛刻网络工作负荷的 CPU 利用率。

## <a name="connection-efficiency"></a>连接效率

建立新的连接始终是一项昂贵而耗时的任务。 当某个应用程序请求数据库连接时，它会优先分配现有的空闲数据库连接，而不是创建新的连接。  以下是一些用于良好连接做法的选项：

- **ProxySQL**：使用 [ProxySQL](https://proxysql.com/)（提供内置连接池）并 [将工作负载负载均衡](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042)到多个只读副本，如应用程序代码更改所需要的那样。

- **Heimdall 数据代理**：此外，还可以利用 Heimdall 数据代理，这是一个与供应商无关的专用代理解决方案。 它支持查询缓存和读/写拆分以及复制滞后检测。 还可以参考如何[使用 Heimdall 代理加速 MySQL 性能](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/accelerate-mysql-performance-with-the-heimdall-proxy/ba-p/1063349)。  

- **持久或长期连接**：如果应用程序具有短期事务或查询，执行时间通常 < 5-10 ms，则用持久连接替换短期连接。 将短期连接替换为持久性连接只需对代码进行轻微的更改，但在许多典型的应用方案中，这种做法可以大幅提高性能。 请确保设置事务完成时超时或关闭连接。

- **副本**：如果使用副本，请使用 [ProxySQL](https://proxysql.com/) 在主服务器和可读辅助副本服务器之间进行负载均衡。 了解[如何设置 ProxySQL](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/scaling-an-azure-database-for-mysql-workload-running-on/ba-p/1105847)。

## <a name="data-import-configurations"></a>数据导入配置

- 在开始数据导入操作之前，可以暂时将实例缩放到较高的 SKU 大小，然后在导入成功时将其纵向缩减。
- 通过使用 [Azure 数据库迁移服务 (DMS)](https://datamigration.microsoft.com/) 进行联机或脱机迁移，可以以最少的停机时间导入数据。 

## <a name="azure-database-for-mysql-memory-recommendations"></a>Azure Database for MySQL 内存建议

Azure Database for MySQL 性能最佳做法是分配足够的 RAM，使工作集几乎完全驻留在内存中。 

- 使用 [MySQL 服务器的指标](./concepts-monitoring.md)检查使用的内存百分比是否达到[限制](./concepts-pricing-tiers.md)。 
- 设置针对此类值的警报，确保当服务器达到限制时，你可以立即执行操作来修复它。 根据定义的限制，检查是否将数据库 SKU 纵向扩展为更高的计算大小或更好的定价层，从而显著提高性能。 
- 进行纵向扩展，直到缩放操作后性能值不再急剧下降。 有关监视 DB 实例的指标的信息，请参阅 [MySQL DB 指标](./concepts-monitoring.md#metrics)。
 
## <a name="use-innodb-buffer-pool-warmup"></a>使用 InnoDB 缓冲池预热

重启 Azure Database for MySQL 服务器后，驻留在存储中的数据页将在查询表时加载，这会导致第一次执行查询时延迟增加和性能下降。 对于延迟敏感型工作负载，这可能是不可接受的。 

利用 InnoDB 缓冲池预热，可以通过在重启之前重新加载缓冲池中的磁盘页来缩短预热时间，而不必等待 DML 或 SELECT 操作访问相应的行。

通过配置 [InnoDB 缓冲池服务器参数](https://dev.mysql.com/doc/refman/8.0/en/innodb-preload-buffer-pool.html)，可以减少重启 Azure Database for MySQL 服务器后的预热时间，这是一种性能优势。 InnoDB 会在服务器关闭时为每个缓冲池保存一定百分比的最近使用的页面，并在服务器启动时还原这些页面。

还需要注意的是，性能的提高是以服务器启动时间的延长为代价的。 启用此参数后，预计服务器启动和重启时间会增加，具体取决于服务器上预配的 IOPS。 

我们建议测试和监视重启时间，以确保启动/重启性能是可接受的，因为在此期间服务器不可用。 当预配的 IOPS 小于 1000（或换句话说，当预配置的存储小于 335GB）时，不建议使用此参数。

要在服务器关闭时保存缓冲池的状态，请将服务器参数 `innodb_buffer_pool_dump_at_shutdown` 设置为 `ON`。 同样，将服务器参数 `innodb_buffer_pool_load_at_startup` 设置为 `ON` 以在服务器启动时还原缓冲池状态。 可以通过降低和微调服务器参数 `innodb_buffer_pool_dump_pct` 的值来控制对启动/重启时间的影响。 默认情况下，此参数设置为 `25`。

> [!Note]
> 仅存储容量最大达 16 TB 的常规用途存储服务器才支持 InnoDB 缓冲池预热参数。 在此处详细了解 [Azure Database for MySQL 存储选项](./concepts-pricing-tiers.md#storage)。

## <a name="next-steps"></a>后续步骤

- [使用 Azure Database for MySQL 的服务器操作的最佳做法](concept-operation-excellence-best-practices.md) <br/>
- [监视 Azure Database for MySQL 的最佳做法](concept-monitoring-best-practices.md)<br/>
- [开始使用 Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)<br/>
