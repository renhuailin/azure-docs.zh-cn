---
title: 从本地 MySQL 迁移到 Azure Database for MySQL 的性能基线指南
description: 了解现有 MySQL 工作负荷是确保迁移成功可做的最佳投资之一。
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 05/26/2021
ms.openlocfilehash: d04a02eb23bb386a6a751d5fab4a3b33c9673ac0
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111971000"
---
# <a name="mysql-on-premises-to-azure-database-for-mysql-migration-guide-performance-baselines"></a>从本地 MySQL 迁移到 Azure Database for MySQL 的性能基线指南

了解现有 MySQL 工作负荷是确保迁移成功可做的最佳投资之一。 卓越的系统性能取决于充足的硬件和优秀的应用程序设计。 CPU、内存、磁盘和网络等项目都需要适当调整大小并进行配置，以满足预期负荷。 硬件和配置是系统性能公式的一部分。 开发人员必须了解数据库查询负荷以及要执行的成本最高的查询。 重点关注成本最高的查询有可能会对总体性能指标产生显著差异。

创建查询性能的基线对于迁移项目至关重要。 性能基线可用于验证迁移的数据工作负荷的 Azure 登陆区域配置。 大多数系统将 24 小时运行，并具有不同的峰值负荷时间。 捕获峰值工作负荷以确定基线非常重要。 而且，应当多次捕获相关指标。 我们将在本文后面探讨源服务器参数，以及这些参数对于总体性能基线的必要性。 在开展迁移项目的过程中，服务器参数不应被忽略。

### <a name="tools"></a>工具

下面介绍用于收集服务器指标和数据库工作负荷信息的工具。 你可以使用捕获的指标来确定适当的 Azure Database for MySQL 层级和相关缩放方案。

  - [MySQL Enterprise Monitor：](https://www.mysql.com/products/enterprise/monitor.html)此企业版付费工具可针对成本最高的查询、服务器指标、文件 I/O 和拓扑信息提供排序列表

  - [Percona Monitoring and Management (PMM)](https://www.percona.com/software/database-tools/percona-monitoring-and-management)：一种开源数据库监视解决方案，可提供同类最佳服务。 无论部署位置为何，该解决方案都有助于降低复杂性、优化性能以及提高业务关键数据库环境的安全性。

### <a name="server-parameters"></a>服务器参数

MySQL 服务器的默认配置可能无法充分支持工作负荷。 MySQL 中有很多服务器参数，但迁移团队在大多数情况下应当重点关注一小部分参数。 以下参数应当在源环境和目标环境中进行评估。 不正确的配置可能会影响迁移速度。 我们将会在执行迁移步骤时再次重新访问这些参数。

  - innodb\_buffer\_pool\_size：该值较高时，可确保在使用磁盘 I/O 之前，先使用内存中的资源。 典型值的范围为可用内存的 80 - 90%。 例如，系统内存为 8GB 时，分配的池大小应为 5 - 6GB。

  - innodb\_log\_file\_size：重做日志可用于确保写入操作快速、持久。 此类事务备份在系统出现故障时很有用。 先从 innodb\_log\_file\_size = 512M（提供 1 GB 重做日志）开始，应可提供大量写入空间。 对于使用 MySQL 5.6 或更高版本的写入密集型应用程序，则应从 innodb\_log\_file\_size = 4G 开始。

  - max\_connections：此参数可帮助抑制 `Too many connections` 错误。 默认值为 151 个连接。 首选在应用程序级别使用连接池，但服务器连接配置可能也需要增加。

  - innodb\_file\_per\_table：此设置将告知 InnoDB 是否应将数据和索引存储在共享表空间中或存储在每个表的 separate.ibd 文件中。 若配置每个表都有一个 innodb 文件，则服务器便可在系统删除、截断或重新生成表时回收空间。 包含大量表的数据库不应使用“每个表一个 innodb 文件”这一配置。 从 MySQL 5.6 开始，该默认值为 ON。 对于更早的数据库版本，应当在加载数据前，先将配置设置为 ON。 不过，此设置仅会影响新创建的表。

  - innodb\_flush\_log\_at\_trx\_commit：默认设置为 1，表示 InnoDB 完全符合 ACID 规范。 对于磁盘速度较慢的系统，这种风险较低的事务配置可能会产生很大的开销，因为你需要额外使用 fsync 来刷新对重做日志所做的每一个更改。 将该参数设置为 2 会导致可靠性有所降低，因为系统将每秒只执行一次将已提交事务刷新到重做日志的操作。 此风险在一些关键情况下是可接受的，对于副本来说也具有相当可观的价值。 若该值为 0，系统性能将有所提升，但数据库服务器在发生故障期间丢失部分数据的可能性也会增加。 底线是，只能将副本参数设置为 0。

  - innodb\_flush\_method：此设置可控制如何将数据和日志刷新到磁盘。 若使用硬件 RAID 控制器且其中具有受电池保护的写回缓存，则应使用 `O_DIRECT`。 其他方案可使用 `fdatasync`（默认值）。

  - innodb\_log\_buffer\_size：此设置可用于设置尚未提交的事务的缓冲区大小。 默认值 1MB 通常情况下非常合适。 包含大型 blob/文本字段的事务可快速填充缓冲区，并触发额外的 I/O 负荷。 查看 `Innodb_log_waits` 状态变量，如果值不为 0，应增加 `innodb_log_buffer_size`。

  - query\_cache\_size：众所周知，查询缓存是可在中等并发期间看到的瓶颈。 初始值应设置为 0，以禁用缓存。 例如，`query_cache_size = 0` 此为 MySQL 5.6 及更高版本上的默认设置。

  - log\_bin：此设置将启用二进制日志记录。 若将服务器用作复制主机，则必须启用二进制日志记录。

  - server\_id：在复制拓扑中，此设置将是标识服务器的唯一值。

  - expire\_logs\_days：此设置将控制自动清除二进制日志的天数。

  - skip\_name\_resolve：让用户执行客户端主机名解析。 如果 DNS 速度较慢，则连接速度也会很慢。 禁用名称解析时，GRANT 语句必须仅使用 IP 地址。 要使用 IP，之前创建的任何 GRANT 语句都需要重做。

运行以下命令，以将服务器参数导出到文件以供查看。 使用一些简单分析时，若输出适用于 Azure Database for MySQL 服务器，则可在迁移后使用该输出重新应用相同的服务器参数。 参考[使用 Azure 门户在 Azure Database for MySQL 中配置服务器参数](../howto-server-parameters.md)。

`mysql -u root -p -A -e "SHOW GLOBAL VARIABLES;" > settings.txt`

你可以在附录 C 中找到 MySQL 5.5.60 默认安装的服务器参数。

请在开始迁移前导出源 MySQL 配置设置， 并在迁移完成后，将这些值与 Azure 登陆区域实例设置进行比较。 如果目标 Azure 登陆区域实例中的默认设置发生了任何更改，请确保在迁移后恢复这些设置。 此外，迁移用户应当验证是否可以在迁移之前设置服务器参数。

有关无法配置的服务器参数列表，请参考[不可配置的服务器参数](../concepts-server-parameters.md#non-configurable-server-parameters)。

#### <a name="egress-and-ingress"></a>流出量和流入量

对于每个单独的数据迁移工具和路径，你需要修改源和目标 MySQL 服务器参数，以最大限度提高流出量和流入量的传输速度。 参数可能因不同工具而存在差异。 例如，与单线程工具相比，并行执行迁移的工具在源环境和目标环境中需要的连接数可能更多。

请查看可能受数据集影响的任何超时参数。 其中包括:

  - [connect\_timeout ](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_connect_timeout)

  - [wait\_timeout ](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_wait_timeout)

此外，请查看将影响最大值的任何参数：

  - [max\_allowed\_packet ](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_max_allowed_packet)

> [!NOTE]
> 常见的迁移错误为 `MySQL server has gone away`。 要解决该错误，本部分提到的参数就是典型的根源所在。

### <a name="wwi-scenario"></a>WWI 方案

WWI 在查看会议数据库工作负荷后，确定其负荷非常小。 虽然基本层服务器适用于他们的工作负荷，但其不希望以后再花精力迁移到另一层。 正在部署的服务器最终要托管其他 MySQL 数据工作负荷，因此他们选择了 `General Performance` 层。

在评审 MySQL 数据库时，MySQL 5.5 服务器正在使用初次安装服务器时设置的默认服务器参数进行运行。  

> [!div class="nextstepaction"]
> [数据迁移](./data-migration.md)