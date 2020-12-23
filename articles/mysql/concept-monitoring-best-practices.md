---
title: 监视最佳做法-Azure Database for MySQL
description: 本文介绍监视 Azure Database for MySQL 的最佳实践。
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 11/23/2020
ms.openlocfilehash: 59301e26f4d42056322ba5a7cdaff1555c531096
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2020
ms.locfileid: "96354939"
---
# <a name="best-practices-for-monitoring-azure-database-for-mysql--single-server"></a>监视 Azure Database for MySQL 的最佳实践-单服务器

了解可用于监视数据库操作的最佳实践，并确保在数据大小增长时性能不会受到影响。 随着我们向平台添加新功能，我们将继续改进本部分中详细介绍的最佳实践。

## <a name="layout-of-the-current-monitoring-toolkit"></a>当前监视工具包的布局

Azure Database for MySQL 提供可用于监视使用情况的工具和方法，这些工具和方法可用于监视、添加或删除资源 (如 CPU、内存或 i/o) 、排查潜在问题，并帮助提高数据库的性能。 可以定期 [监视性能指标](concepts-monitoring.md#metrics) ，以查看各种时间范围的平均值、最大值和最小值。

你可以为指标阈值 [设置警报](howto-alert-on-metric.md#create-an-alert-rule-on-a-metric-from-the-azure-portal) ，以便通知服务器是否达到了这些限制并采取适当的措施。  

监视数据库服务器，以确保分配给数据库的资源可以处理应用程序工作负荷。 如果数据库即将达到资源限制，请考虑：
    * 识别资源消耗量最大的查询并对其进行优化。 
    * 通过升级服务层级来添加更多的资源。

### <a name="cpu-utilization"></a>CPU 使用率
监视 CPU 使用情况，如果数据库是耗尽 CPU 资源，则为。 如果 CPU 使用率为90% 或以上，则应增加计算，方法是将 Vcore 数或扩展到下一个定价层。  请确保在 CPU 中增加/降低吞吐量或并发性。 

### <a name="memory"></a>内存 
数据库服务器可用的内存量与 [vcore 数](concepts-pricing-tiers.md)成正比。 确保内存对于工作负荷而言足够。 对应用程序进行负载测试，验证内存是否足以用于读取和写入操作。 如果数据库内存消耗经常超出定义的阈值，则表示你应通过提高 Vcore 或更高性能层来升级实例。 使用 [查询存储](concepts-query-store.md)， [查询性能建议](concepts-performance-recommendations.md) 来确定最长持续时间最长的查询。 探索优化机会。 

### <a name="storage"></a>存储 
为 MySQL server 预配的 [存储量](howto-create-manage-server-portal.md#scale-compute-and-storage) 决定了服务器的 IOPs。 服务使用的存储包括数据库文件、事务日志、服务器日志和备份快照。 确保消耗的磁盘空间不会持续超过预配磁盘空间总量的85% 以上。 如果是这种情况，则需要从数据库服务器中删除或存档数据以释放一些空间。 

### <a name="network-traffic"></a>流量 

**网络接收吞吐量，网络传输吞吐量** –进出 MySQL 实例的网络流量速率（mb/秒）。 如果吞吐量低于预期，则需要评估服务器的吞吐量要求，并持续监视流量。 

### <a name="database-connections"></a>数据库连接 
**数据库连接** -连接到 Azure Database for MySQL 的客户端会话数应与 [选定 SKU 大小的连接限制](concepts-server-parameters.md#max_connections) 一致。 


## <a name="next-steps"></a>后续步骤

- [Azure Database for MySQL 性能的最佳做法](concept-performance-best-practices.md)
- [使用 Azure Database for MySQL 执行服务器操作的最佳做法](concept-operation-excellence-best-practices.md)
