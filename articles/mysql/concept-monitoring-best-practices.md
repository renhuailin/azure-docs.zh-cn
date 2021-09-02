---
title: 监视最佳做法 - Azure Database for MySQL
description: 本文介绍监视 Azure Database for MySQL 的最佳做法。
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.custom: ''
ms.date: 11/23/2020
ms.openlocfilehash: 351f4d0c6e2c920e22f1f21c2ef3f44add3cb145
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "122651937"
---
# <a name="best-practices-for-monitoring-azure-database-for-mysql---single-server"></a>监视 Azure Database for MySQL - 单一服务器的最佳做法

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

了解可用于监视数据库操作并确保性能不会随着数据大小的增长而降低的最佳做法。 在向平台添加新功能时，我们将继续优化本部分中详述的最佳做法。

## <a name="layout-of-the-current-monitoring-toolkit"></a>当前监视工具包的布局

Azure Database for MySQL 提供所需的工具和方法，用于轻松监视使用情况、添加或删除资源（例如 CPU、内存或 I/O）、排查潜在问题并提高数据库的性能。 可以定期[监视性能指标](concepts-monitoring.md#metrics)，以查看各种时间范围的平均值、最大值和最小值。

可以为指标阈值[设置警报](howto-alert-on-metric.md#create-an-alert-rule-on-a-metric-from-the-azure-portal)，以便你了解服务器是否已达到这些限制并采取适当措施。

监视数据库服务器，以确保分配给数据库的资源能够处理应用程序工作负载。 如果数据库即将达到资源限制，请考虑：

* 识别资源消耗量最大的查询并对其进行优化。
* 通过升级服务层级来添加更多的资源。

### <a name="cpu-utilization"></a>CPU 使用率

监视 CPU 使用率以及数据库是否耗尽 CPU 资源。 如果 CPU 使用率为 90% 或更多，则应该通过增加 vCore 的数量或缩放到下一个定价层来纵向扩展计算。  在纵向扩展或纵向缩减 CPU 时，请确保吞吐量或并发符合预期。 

### <a name="memory"></a>内存

数据库服务器的可用内存量与 [vCore 数目](concepts-pricing-tiers.md)成正比。 确保内存对于工作负荷而言足够。 对应用程序进行负载测试以验证内存是否足以支持读写操作。 如果数据库内存消耗经常超过定义的阈值，则表示你应该通过增加 vCore 数目或扩展到更高的性能层来升级实例。 使用[查询存储](concepts-query-store.md)、[查询性能建议](concepts-performance-recommendations.md)来确定持续时间最长、执行次数最多的查询。 探索优化机会。 

### <a name="storage"></a>存储

为 MySQL 服务器预配的[存储量](howto-create-manage-server-portal.md#scale-compute-and-storage)决定服务器的 IOP。 服务使用的存储包括数据库文件、事务日志、服务器日志和备份快照。 确保消耗的磁盘空间不会经常超过预配磁盘空间总数的 85%。 如果是这种情况，则需要从数据库服务器中删除或存档数据以释放一些空间。 

### <a name="network-traffic"></a>流量

**网络接收吞吐量、网络传输吞吐量** - 与 MySQL 实例之间的网络流量速率（以兆字节/秒为单位）。 你需要评估服务器的吞吐量要求，如果吞吐量低于预期，则需要持续监视流量。 

### <a name="database-connections"></a>数据库连接

**数据库连接** - 连接到 Azure Database for MySQL 的客户端会话数应与 [所选 SKU 大小的连接限制](concepts-server-parameters.md#max_connections)对齐。

## <a name="next-steps"></a>后续步骤

* [Azure Database for MySQL 性能的最佳做法](concept-performance-best-practices.md)
* [使用 Azure Database for MySQL 的服务器操作的最佳做法](concept-operation-excellence-best-practices.md)
