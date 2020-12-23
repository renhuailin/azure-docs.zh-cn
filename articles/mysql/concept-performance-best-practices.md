---
title: 性能最佳实践-Azure Database for MySQL
description: 本文介绍了用于监视和调整 Azure Database for MySQL 性能的最佳做法。
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 11/23/2020
ms.openlocfilehash: c29c043a3af46086751629b31ce68217e7226442
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2020
ms.locfileid: "96354940"
---
# <a name="best-practices-for-optimal-performance-of-your-azure-database-for-mysql---single-server"></a>Azure Database for MySQL 单服务器的最佳性能最佳做法

了解在使用 Azure Database for MySQL 单服务器时获得最佳性能的最佳做法。 随着我们向平台添加新功能，我们将继续改进本部分中详细介绍的最佳实践。

## <a name="physical-proximity"></a>物理邻近性

 请确保将应用程序和数据库部署在同一区域中。 启动任何性能基准测试之前的快速检查是使用简单的 SELECT 1 查询来确定客户端与数据库之间的网络延迟。 

## <a name="accelerated-networking"></a>加速网络

如果使用的是 Azure 虚拟机、Azure Kubernetes 或应用服务，请使用适用于应用程序服务器的加速网络。 使用加速网络可以实现对 VM 的单根 I/O 虚拟化 (SR-IOV)，大幅提升其网络性能。 这种高性能路径会绕过数据路径中的主机，降低延迟、抖动，以及受支持 VM 类型上的最苛刻网络工作负荷的 CPU 利用率。

## <a name="connection-efficiency"></a>连接效率

建立新连接始终是一项成本高昂且耗时的任务。 当应用程序请求数据库连接时，它会划分现有空闲数据库连接的分配，而不是创建一个新连接。  下面是一些用于实现良好连接实践的选项：

- **ProxySQL**：使用 [ProxySQL](https://proxysql.com/) 提供内置连接池，并根据需要对应用程序代码进行任何更改，按需对 [工作负荷进行负载均衡](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042) 。

- **Heimdall 数据代理**：此外，还可以利用 Heimdall 数据代理（一种特定于供应商的专用代理解决方案）。 它支持通过复制滞后检测来进行查询缓存和读/写拆分。 你还可以参阅如何 [通过 Heimdall 代理加速 MySQL 性能](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/accelerate-mysql-performance-with-the-heimdall-proxy/ba-p/1063349)。  

- **永久性连接或 Long-Lived 连接**：如果应用程序的短事务或查询的执行时间 < 为5-10 毫秒，则将短连接替换为永久性连接。 将短连接替换为永久性连接只需对代码进行少量更改，但在许多典型的应用程序方案中，它会影响性能。 请确保在事务完成时设置超时或关闭连接。

- **副本**：如果使用的是副本，请使用 [ProxySQL](https://proxysql.com/) 来平衡主服务器与可读辅助副本服务器之间的负载。 了解 [如何设置 ProxySQL](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/scaling-an-azure-database-for-mysql-workload-running-on/ba-p/1105847)。

## <a name="data-import-configurations"></a>数据导入配置

- 在开始数据导入操作之前，可以临时将实例扩展到更高的 SKU 大小，然后在导入成功时将其缩小。
- 使用 [Azure 数据库迁移服务 (dm) ](https://datamigration.microsoft.com/) 进行联机或脱机迁移时，可以使用最短的停机时间来导入数据。 

## <a name="azure-database-for-mysql-memory-recommendations"></a>Azure Database for MySQL 内存建议

Azure Database for MySQL 性能最佳做法是分配足够的 RAM，使工作集几乎完全在内存中。 

- 使用[MySQL 服务器的指标](https://docs.microsoft.com/azure/mysql/concepts-monitoring)检查是否正在使用的内存百分比达到[限制](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers)。 
- 设置此类号码的警报，确保服务器达到限制时，可以采取提示操作来修复此类问题。 根据定义的限制，检查是否扩展数据库 SKU，以提高计算规模，或提高定价层，从而提高性能。 
- 在缩放操作后，在性能编号不再大幅下降之前进行扩展。 有关监视 DB 实例的度量值的信息，请参阅 [MYSQL Db 度量值](https://docs.microsoft.com/azure/mysql/concepts-monitoring#metrics)。

## <a name="next-steps"></a>后续步骤

- [使用 Azure Database for MySQL 执行服务器操作的最佳做法](concept-operation-excellence-best-practices.md) <br/>
- [监视 Azure Database for MySQL 的最佳实践](concept-monitoring-best-practices.md)<br/>
- [Azure Database for MySQL 入门](quickstart-create-mysql-server-database-using-azure-portal.md)<br/>
