---
title: 排查只读访问的问题 - 超大规模 (Citus) - Azure Database for PostgreSQL
description: 了解超大规模 (Citus) 服务器组为何会变为只读，以及如何解决此问题
keywords: PostgreSQL 连接，只读
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 08/03/2021
ms.openlocfilehash: 48ea5fbeacf1769f73b9d505e6cc8fb55e8c3a44
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121741992"
---
# <a name="troubleshoot-read-only-access-to-azure-database-for-postgresql---hyperscale-citus"></a>排查对 Azure Database for PostgreSQL - 超大规模 (Citus) 只读访问的问题

PostgreSQL 无法在没有可用磁盘空间的计算机上运行。 为了保持对 PostgreSQL 服务器的访问，请务必防止磁盘空间消耗殆尽。

对于超大规模 (Citus) 而言，当磁盘几乎已满时，节点将设置为只读 (RO) 状态。 阻止写入会让磁盘不再继续填充，使节点可供读取。 在只读状态期间，可以采取措施来释放更多的磁盘空间。

具体而言，超大规模 (Citus) 节点在剩余可用存储空间少于 5 GiB 时变为只读。 当服务器变为只读时，所有现有会话都将断开连接，并且未提交的事务会回滚。 任何写入操作和事务提交都将失败，而读取查询将继续工作。

## <a name="ways-to-recover-write-access"></a>恢复写入访问的方法

### <a name="on-the-coordinator-node"></a>针对协调器节点

* 在协调器节点上[增加存储大小](howto-hyperscale-scale-grow.md#increase-storage-on-nodes)，和/或
* 将本地表分发到工作器节点，或删除数据。 在连接到数据库之后需要首先运行 `SET SESSION CHARACTERISTICS AS TRANSACTION READ WRITE`，然后再执行其他命令。

### <a name="on-a-worker-node"></a>针对工作器节点

* 在工作器节点上[增加存储大小](howto-hyperscale-scale-grow.md#increase-storage-on-nodes)，和/或
* 将数据[再平衡](howto-hyperscale-scale-rebalance.md)到其他节点，或删除一些数据。
    * 需要暂时将工作器节点设置为读/写。 可以直接连接到工作器节点并使用 `SET SESSION CHARACTERISTICS`，如上面针对协调器节点中所述。

## <a name="prevention"></a>预防

我们建议你设置警报，以便在服务器存储接近阈值时收到通知。 这使你可以尽早采取行动，避免进入只读状态。 有关详细信息，请参阅[建议的警报](howto-hyperscale-alert-on-metric.md#suggested-alerts)文档。

## <a name="next-steps"></a>后续步骤

* [设置 Azure 警报](howto-hyperscale-alert-on-metric.md#suggested-alerts)以提前收到通知，从而在达到只读状态之前采取措施。
* 在 PostgreSQL 文档中了解[磁盘使用情况](https://www.postgresql.org/docs/current/diskusage.html)。
* 在 PostgreSQL 文档中了解[会话特征](https://www.postgresql.org/docs/13/sql-set-transaction.html)。
