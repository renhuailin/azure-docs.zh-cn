---
title: MySQL server 操作最佳做法-Azure Database for MySQL
description: 本文介绍了在 Azure 上操作 MySQL 数据库的最佳实践。
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 11/23/2020
ms.openlocfilehash: e1d84483a701bf7fb4e6c50b9dc4f4f89993c64d
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2020
ms.locfileid: "96354951"
---
# <a name="best-practices-for-server-operations-on-azure-database-for-mysql--single-server"></a>Azure Database for MySQL 上的服务器操作的最佳实践-单服务器

了解使用 Azure Database for MySQL 的最佳实践。 随着我们向平台中添加新功能，我们将继续致力于改进本部分中详细介绍的最佳实践。

## <a name="azure-database-for-mysql-operational-guidelines"></a>Azure Database for MySQL 操作指南 

下面是在使用 Azure Database for MySQL 提高数据库性能时应遵循的操作指导原则： 

* **归置**：若要减少网络延迟，请将客户端和数据库服务器放在同一 Azure 区域中。

* **监视你的内存、CPU 和存储使用量**：你可以 [设置警报](howto-alert-on-metric.md) ，以便在使用模式发生更改时通知你，或在你接近部署的容量时通知你，以便你可以保持系统性能和可用性。 

* **纵向扩展数据库实例**：当接近存储容量限制时，可以 [纵向扩展](howto-create-manage-server-portal.md) 。 你应该在存储和内存中有一些缓冲区，以适应应用程序的意外增加需求。 你还可以 [启用存储自动增长](howto-auto-grow-storage-portal.md) 功能 "打开"，以确保服务在接近存储限制时自动缩放存储。 

* **配置备份**：根据业务需求启用 [本地或异地冗余备份](howto-restore-server-portal.md#set-backup-configuration) 。 此外，还可以修改备份可用于业务连续性的时间长度的保留期。 

* **增加 i/o 容量**：如果你的数据库工作负荷所需的 i/o 数量大于你预配的数量，则恢复或数据库的其他事务操作将会缓慢。 若要增加服务器实例的 i/o 容量，请执行以下任一或全部操作： 

    * Azure database for MySQL 按预配的每 GB 存储空间的速率提供 IOPS 缩放。 [增加预配的存储](howto-create-manage-server-portal.md#scale-storage-up) 以缩放 IOPS 以提高性能。 

    * 如果已在使用预配的 IOPS 存储，请预配 [额外的吞吐量容量](howto-create-manage-server-portal.md#scale-storage-up)。 

* **缩放计算**：由于 CPU 或内存，数据库工作负荷也会受到限制，这可能会对事务处理产生严重影响。 请注意，计算 (定价层) 只能在 [常规用途或内存优化](concepts-pricing-tiers.md) 层之间进行扩展或缩减。 

* **测试故障转移**：手动测试服务器实例的故障转移，以了解该过程需要多长时间才能处理用例，并确保访问服务器实例的应用程序可以在故障转移后自动连接到新的服务器实例。

* **使用主键**：在 Azure Database for MySQL 操作时，请确保表具有主键或唯一键。 这有助于执行备份、复制等工作，并提高性能。

* **配置 TTL 值**：如果客户端应用程序正在缓存 (DNS) 服务器实例的数据的域名服务，请将时间间隔设置)  (为小于30秒。 由于服务器实例的基础 IP 地址在故障转移后可能会更改，因此，如果应用程序尝试连接到不再处于服务中的 IP 地址，则将 DNS 数据缓存更长的时间可能会导致连接失败。

* 使用连接池来避免 [达到最大连接限制](concepts-server-parameters.md#max_connections)并使用重试逻辑，以避免间歇性连接问题。 

* 如果使用的是副本，请使用 [ProxySQL 来平衡](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/scaling-an-azure-database-for-mysql-workload-running-on/ba-p/1105847) 主服务器与可读辅助副本服务器之间的负载。 请参阅此处的设置步骤。 </br> 

* 设置资源时，请确保已为 Azure Database for MySQL [启用自动增长](howto-auto-grow-storage-portal.md) 。 这不会增加额外的费用，并将保护数据库免受你可能遇到的任何存储瓶颈的问题。 </br> 


### <a name="using-innodb-with-azure-database-for-mysql"></a>在 Azure Database for MySQL 中使用 InnoDB

*   如果使用的 `ibdata1` 功能是系统表空间数据文件，则不能通过从表中删除数据来收缩或清除数据，也不能将表移至每表文件 `tablespaces` 。

* 对于大小超过 1 TB 的数据库，应在 **innodb_file_per_table** 中创建该表 `tablespace` 。 对于大小超过 1 TB 的单个表，应为 [分区](https://dev.mysql.com/doc/refman/5.7/en/partitioning.html) 表。

*   对于具有大量的的服务器 `tablespace` ，由于在 MySQL 启动或故障转移过程中顺序表空间扫描，引擎启动会非常缓慢。 

* 如果表的总数小于500，则在创建表之前将 innodb_file_per_table 设置为 ON。

* 如果数据库中包含500个以上的表，请查看每个表的表大小。 对于大型表，还应考虑使用 "每个表一个文件" 表空间来避免系统表空间文件达到最大存储限制。

> [!NOTE]
> 对于大小小于5GB 的表，请考虑使用系统表空间 
> ```sql
>    CREATE TABLE tbl_name ... *TABLESPACE* = *innodb_system*;
> ```

* 如果你有一个非常大的表，则在表创建过程中对表进行[分区](https://dev.mysql.com/doc/refman/5.7/en/partitioning.html)可能会增长到 1 TB 以上。

* 使用多个 MySQL 服务器，并将表分散到这些服务器中。 如果有大约10000的表或更多表，请避免在单个服务器上放置过多的表。 

## <a name="next-steps"></a>后续步骤
- [Azure Database for MySQL 性能的最佳做法](concept-performance-best-practices.md)
- [监视 Azure Database for MySQL 的最佳实践](concept-monitoring-best-practices.md)
