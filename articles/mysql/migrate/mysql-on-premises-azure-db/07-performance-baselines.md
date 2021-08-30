---
title: 将本地 MySQL 迁移到 Azure Database for MySQL：性能基线
description: 了解现有 MySQL 工作负荷是确保迁移成功的最佳投资之一。
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 06/21/2021
ms.openlocfilehash: 2077ef62ddabf7910d5a634c07262c9d29905cf4
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "113084959"
---
# <a name="migrate-mysql-on-premises-to-azure-database-for-mysql-performance-baselines"></a>将本地 MySQL 迁移到 Azure Database for MySQL：性能基线

[!INCLUDE[applies-to-mysql-single-flexible-server](../../includes/applies-to-mysql-single-flexible-server.md)]

## <a name="prerequisites"></a>先决条件

[测试计划](06-test-plans.md)

## <a name="overview"></a>概述

了解现有 MySQL 工作负荷是确保迁移成功的最佳投资之一。 优秀的系统性能取决于充足的硬件和优秀的应用程序设计。 需要调整 CPU、内存、磁盘和网络等项目的大小，并对预期负载进行适当的配置。 硬件和配置是影响系统性能的因素之一。 开发人员必须了解数据库查询负载和要执行的计算量最大的查询。 专注于计算量最大的查询可能会让整体性能指标产生显著的差异。

创建查询性能的基线对于迁移项目至关重要。 性能基线可用于验证迁移的数据工作负荷的 Azure 登陆区域配置。 大多数系统将全天候不间断运行，并具有不同的峰值负载时间。 为基线捕获峰值工作负荷很重要。 系统将多次捕获指标。 我们稍后将在本文档中探讨源服务器参数及其对于总体性能基线图的重要程度。 在迁移项目过程中，不应忽略服务器参数。

## <a name="tools"></a>工具

下面是用于收集服务器指标和数据库工作负荷信息的工具。 使用捕获的指标来确定适当的 Azure Database for MySQL 层和相关的缩放选项。

  - [MySQL Enterprise Monitor](https://www.mysql.com/products/enterprise/monitor.html)：此收费的企业版工具可提供计算量最大的查询、服务器指标、文件 I/O 和拓扑信息的排序列表

  - [Percona Monitoring and Management (PMM)](https://www.percona.com/software/database-tools/percona-monitoring-and-management)：卓越的开源数据库监视解决方案。 无论部署在何位置，其都有助于降低复杂性、优化性能和提高关键业务数据库环境的安全性。

## <a name="server-parameters"></a>服务器参数

MySQL 服务器的默认配置可能无法充分支持工作负荷。 MySQL 中存在大量服务器参数，但在多数情况下，迁移团队应重点关注其中的一小部分。 应在“源环境”和“目标环境”中评估以下参数。 配置不当会影响迁移速度。 在执行迁移步骤时将再次重新访问这些参数。

  - innodb\_buffer\_pool\_size：在使用磁盘 I/O 之前，较大的值将确保先使用内存中的资源。 典型值的范围为 80-90% 的可用内存。 例如，具有 8GB 内存的系统应为池分配 5-6GB 大小的空间。

  - innodb \_log\_file\_size：重做日志用于确保快速持久写入。 此事务备份在系统崩溃时会派上用场。 从 innodb\_log\_file\_size = 512M（提供 1GB 重做日志）开始应会为写入提供大量空间。 若在使用 MySQL 5.6 或更高版本的写入密集型应用程序，则应以 innodb\_log\_file\_size = 4G 开始。

  - max\_connections：此参数可帮助缓解 `Too many connections` 错误。 默认为 151 个连接。 首选在应用程序级别使用连接池，但服务器连接配置可能还需要增加。

  - innodb\_file\_per\_table：此设置将告知 InnoDB 应将数据和索引存储在共享表空间中，还是存储在每个表的单独 ibd 文件中。 每个表都拥有一个文件，能让服务器在表被删除、截断或重新生成时回收空间。 若数据库中包含大量表格，则不应使用“每个文件一个表”的配置。 对于 MySQL 5.6，默认值为“开启”。 如果是早期的数据库版本，应在加载数据之前将配置设置为“开启”。 此设置仅影响新创建的表。

  - innodb\_flush\_log\_at\_trx\_commit：默认设置为 1 表示 InnoDB 完全符合 ACID。 由于需要额外的 fsyncs 来刷新对重做日志所做的每个更改，因此，较低的风险事务配置可能会在具有慢速磁盘的系统上产生很大的计算量。 将参数设置为 2 没有那么可靠，因为系统仅仅会以每秒一次的频率将已提交的事务刷新到恢复日志。 在大多数情况下，这种风险是可以接受的，对于副本来说，这绝对具有良好的价值。 将参数设置为 0 可以提高系统性能，但在发生故障时，数据库服务器丢失某些数据的可能性更大。 仅在底部的行中为副本使用 0 值。

  - innodb\_flush\_method：此设置控制如何将数据和日志刷新到磁盘。 当硬件 RAID 控制器具有电池保护的回写缓存时，请使用 `O_DIRECT`。 其他情况下请使用 `fdatasync`（默认值）。

  - innodb\_log\_buffer\_size：此设置是尚未提交的事务的缓冲区大小。 默认值 (1MB) 通常能满足需求。 具有大型 blob 或文本字段的事务可以快速填充缓冲区，并触发额外的 I/O 负载。 查看 `Innodb_log_waits` 状态变量，如果不是 0，请增加 `innodb_log_buffer_size`。

  - query\_cache\_size：众所周知，查询缓存是审查并发时的常见瓶颈。 应将初始值设置为 0 以禁用缓存。 例如，`query_cache_size = 0` 这是 MySQL 5.6 和更高版本上的默认设置。

  - log\_bin：此设置将启用二进制日志记录。 如果将服务器作为复制主机，则必须启用二进制日志记录。

  - server\_id：在复制拓扑中，此设置将是标识服务器的唯一值。

  - expire\_logs\_days：此设置将控制自动清除二进制日志的天数。

  - skip\_name\_resolve：用户执行客户端主机名解析。 如果 DNS 速度慢，则连接速度也会慢。 禁用名称解析时，GRANT 语句必须仅使用 IP 地址。 如要能使用该 IP，则需重做之前所做的任何 GRANT 语句。

运行以下命令，将服务器参数导出到文件以供查看。 使用一些简单分析时，如果适合 Azure Database for MySQL 服务器，则可在迁移后，使用输出重新应用相同的服务器参数。 参阅[使用 Azure 门户配置 Azure Database for MySQL 中的服务器参数](../../howto-server-parameters.md)。

`mysql -u root -p -A -e "SHOW GLOBAL VARIABLES;" > settings.txt`

可以在[附录](15-appendix.md#default-server-parameters-mysql-55-and-azure-database-for-mysql)中找到 MySQL 5.5.60 默认安装的服务器参数。

迁移开始之前，请导出源 MySQL 配置设置。 迁移后，将这些值与 Azure 登陆区域实例设置进行比较。 如果在目标 Azure 登陆区域实例中修改了任何设置的默认值，请确保在迁移后重新调回这些设置。 此外，迁移用户在迁移之前应该验证是否可以设置服务器参数。

有关无法配置的服务器参数的列表，请参阅[不可配置的服务器参数](../../concepts-server-parameters.md#non-configurable-server-parameters)。

### <a name="egress-and-ingress"></a>流出量和流入量

对于每个单独的数据迁移工具和路径，将需要修改源和目标 MySQL 服务器参数，以支持可能实现的最快流出和流入。 参数因工具而异。 例如，并行执行迁移的工具可能需要在源和目标与单线程工具上进行更多连接。

查看可能受数据集影响的任何超时参数。 其中包括:

  - [connect\_timeout](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_connect_timeout)

  - [wait\_timeout](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_wait_timeout)

此外，请查看会影响最大值的任何参数：

  - [max\_allowed\_packet](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_max_allowed_packet)

> [!NOTE]
> 常见迁移错误是 `MySQL server has gone away`。 此处提到的参数是解决该错误的典型方法。

## <a name="wwi-scenario"></a>WWI 方案

WWI 查看了其会议数据库工作负荷，并确定其负载较小。 尽管基本层服务器即可运行这些工具，但他们不希望以后再迁移到另一层。 正在部署的服务器最终将托管其他 MySQL 数据工作负荷，因此他们选取了 `General Performance` 层。

在评审 MySQL 数据库时，MySQL 5.5 服务器以初始安装过程中设置的默认服务器参数运行。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [数据迁移](./08-data-migration.md)
