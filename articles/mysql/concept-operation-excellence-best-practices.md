---
title: MySQL 服务器操作最佳做法 - Azure Database for MySQL
description: 本文介绍了在 Azure 上操作 MySQL 数据库的最佳做法。
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 11/23/2020
ms.openlocfilehash: 85048e9a5fccc520cab5bb319ee0b6f81b06f4fd
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "122652647"
---
# <a name="best-practices-for-server-operations-on-azure-database-for-mysql--single-server"></a>Azure Database for MySQL 单一服务器上的服务器操作的最佳做法

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

了解使用 Azure Database for MySQL 的最佳做法。 在向平台添加新功能时，我们将继续重点关注优化本部分中详述的最佳做法。

## <a name="azure-database-for-mysql-operational-guidelines"></a>Azure Database for MySQL 操作指南 

下面是在使用 Azure Database for MySQL 提高数据库性能时应遵循的操作指南： 

* **归置**：若要减少网络延迟，请将客户端和数据库服务器置于同一 Azure 区域。

* **监视内存、CPU 和存储使用情况**：可以 [设置警报](howto-alert-on-metric.md)，以在使用模式发生更改或在接近部署容量时通知你，以便你可以保持系统性能和可用性。 

* **纵向扩展 DB 实例**：可在接近存储容量限额时 [纵向扩展](howto-create-manage-server-portal.md)。 存储和内存中应具有一些缓冲区，以适应应用程序中不可预见的需求增长。 还可以[启用存储自动增长](howto-auto-grow-storage-portal.md)功能，以确保服务在存储接近存储限额时自动缩放存储。 

* **配置备份**：根据业务需求启用 [本地或异地冗余备份](howto-restore-server-portal.md#set-backup-configuration)。 此外，还可以修改保持期，即备份可用于业务连续性的时长。 

* **增加 I/O 容量**：如果数据库工作负载所需的 I/O 容量大于预配的容量，恢复或数据库的其他事务性操作将变慢。 若要增加服务器实例的 I/O 容量，请执行以下任一或全部操作： 

    * Azure database for MySQL 按 3 个 IOPS/预配的每 GB 存储这一速率提供 IOPS 缩放。 [增加预配的存储](howto-create-manage-server-portal.md#scale-storage-up)以缩放 IOPS，从而提高性能。 

    * 如果已在使用预配的 IOPS 存储，请预配[额外的吞吐量容量](howto-create-manage-server-portal.md#scale-storage-up)。 

* **缩放计算**：数据库工作负载还会因 CPU 或内存而受到限制，这可能会对事务处理产生严重影响。 请注意，计算（定价层）可在[常规用途或内存优化](concepts-pricing-tiers.md)层之间进行纵向扩展或缩减。 

* **测试故障转移**：手动测试服务器实例的故障转移，以了解该过程在你的用例中需要多长时间，并确保访问服务器实例的应用程序可以在故障转移后自动连接到新的服务器实例。

* **使用主键**：确保在操作 Azure Database for MySQL 时，表具有主键或唯一键。 这有助于进行备份、复制等，并可提高性能。

* **配置 TTL 值**：如果客户端应用程序要缓存服务器实例的域名服务 (DNS) 数据，请设置一个小于 30 秒的生存时间 (TTL) 值。 由于服务器实例的基础 IP 地址在故障转移后可能会更改，因此，如果应用程序尝试连接到不再提供服务的 IP 地址，长时间缓存 DNS 数据就可能导致连接失败。

* 使用连接池来避免达到[最大连接限制](concepts-server-parameters.md#max_connections)，并使用重试逻辑来避免间歇性连接问题。 

* 如果使用副本，请使用 [ProxySQL 在主服务器和可读辅助副本服务器之间进行负载均衡](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/scaling-an-azure-database-for-mysql-workload-running-on/ba-p/1105847)。 请参阅此处的设置步骤。 </br> 

* 预配资源时，请务必为 Azure Database for MySQL [启用自动增长](howto-auto-grow-storage-portal.md)。 这不会增加额外的费用，并将保护数据库免受可能遇到的任何存储瓶颈的影响。 </br> 


### <a name="using-innodb-with-azure-database-for-mysql"></a>将 InnoDB 与 Azure Database for MySQL 配合使用

*   如果使用 `ibdata1` 功能（系统表空间），则无法通过从表中删除数据或将表移至 file-per-table `tablespaces` 来收缩或清除数据文件。

* 对于大小超过 1 TB 的数据库，应在 innodb_file_per_table `tablespace` 中创建表。 对于大小超过 1 TB 的单个表，应使用[分区](https://dev.mysql.com/doc/refman/5.7/en/partitioning.html)表。

*   对于具有大量 `tablespace` 的服务器，由于 MySQL 启动或故障转移期间的顺序表空间扫描，引擎启动将非常缓慢。 

* 如果表的总数小于 500，则在创建表之前设置 innodb_file_per_table = ON。

* 如果数据库中包含 500 多个表，请查看每个表的表大小。 对于大型表，仍应考虑使用 file-per-table 表空间来避免系统表空间文件达到最大存储限制。

> [!NOTE]
> 对于大小小于 5 GB 的表，请考虑使用系统表空间 
> ```sql
>    CREATE TABLE tbl_name ... *TABLESPACE* = *innodb_system*;
> ```

* 如果你有一个非常大的表，并且该表可能会增长到超过 1 TB，请在创建表时对表进行[分区](https://dev.mysql.com/doc/refman/5.7/en/partitioning.html)。

* 使用多个 MySQL 服务器，并将表分散到这些服务器上。 如果有大约 10000 个表或更多的表，请避免在单个服务器上放置过多的表。 

## <a name="next-steps"></a>后续步骤
- [Azure Database for MySQL 性能的最佳做法](concept-performance-best-practices.md)
- [监视 Azure Database for MySQL 的最佳做法](concept-monitoring-best-practices.md)
