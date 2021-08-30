---
title: 将本地 MySQL 迁移到 Azure Database for MySQL：迁移方法
description: 从源获取数据并发送到目标需要使用 MySQL 的工具或功能来完成迁移。
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 06/21/2021
ms.openlocfilehash: 6a4b0e2dc084168e1bffa539924ad897ac93961e
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "113084995"
---
# <a name="migrate-mysql-on-premises-to-azure-database-for-mysql-migration-methods"></a>将本地 MySQL 迁移到 Azure Database for MySQL：迁移方法

[!INCLUDE[applies-to-mysql-single-flexible-server](../../includes/applies-to-mysql-single-flexible-server.md)]

## <a name="prerequisites"></a>先决条件

[规划](04-planning.md)

## <a name="overview"></a>概述

从源获取数据并发送到目标需要使用 MySQL 的工具或功能来完成迁移。

在开始下一阶段之前，必须完成整个评估和计划阶段。 在选择迁移路径和工具时，依赖于收集的决策和数据。

本部分将探讨以下常用工具：

  - MySQL Workbench

  - mysqldump

  - mydumper 和 myloader

  - 数据传入复制 (binlog)

### <a name="mysql-workbench"></a>MySQL Workbench

[MySQL Workbench](https://www.mysql.com/products/workbench/) 提供丰富的 GUI 体验，使开发人员和管理员能够设计、开发和管理其 MySQL 实例。

将数据库从源移动到目标时，最新版本的 MySQL Workbench 提供复杂的[对象迁移功能](https://www.mysql.com/products/workbench/migrate/)。

#### <a name="data-import-and-export"></a>数据导入和导出

MySQL Workbench 提供基于向导的 UI，用于完全或部分导出和导入表和数据库对象。 有关如何使用 MySQL Workbench 的示例，请参阅[使用导入和导出迁移 MySQL 数据库](../../concepts-migrate-import-export.md)。

### <a name="dump-and-restore-mysqldump"></a>转储和还原 (mysqldump)

`mysqldump` 通常在 MySQL 安装过程中提供。 它是一个[客户端实用工具](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html)，可以运行此工具来创建逻辑备份，这些备份相当于一组 SQL 语句，可以重新运行这些语句以重构建某一时间点的数据库。 在备份或迁移大量数据时，`mysqldump` 不适合作为快速或可缩放的解决方案。 由于更新索引需要磁盘 I/O，因此在执行大量 SQL 插入语句可能会性能不佳。 但是，与其他需要原始架构的工具相结合时，`mysqldump` 非常适合于生成数据库和表架构。 架构可以创建目标登陆区域环境。

`mysqldump`   实用工具提供了一些在数据迁移阶段实用的功能。 在运行此实用工具之前，需要评估性能注意事项。 请参阅[性能注意事项](../../concepts-migrate-dump-restore.md#performance-considerations)。

### <a name="mydumper-and-myloader"></a>mydumper 和 myloader

如果环境中包含大型数据，需要快速迁移，则应该使用 [mydumper 和 myloader](https://github.com/maxbube/mydumper)。 这些工具以 C++ 编写，并利用多线程技术尽快将数据发送到目标 MySQL 实例。 `mydumper` 和 `myloader` 利用并行性，可以将迁移速度提高 10 倍以上。

此工具的二进制版本可以公开下载，并且已针对 Linux 进行了编译。 要在 Windows 中运行这些工具，需要重新编译开源项目。 对于大多数用户来说，编译源代码和创建发布并不是一项轻松的任务。

### <a name="data-in-replication-binlog"></a>数据传入复制 (binlog)

与其他数据库管理系统类似，MySQL 提供了一个名为 [binlog 复制](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html)的日志复制功能。 `binlog` 复制功能有助于数据迁移和创建只读副本。

在联机方案中，利用 binlog 复制[将数据迁移到 Azure Database for MySQL](../../concepts-data-in-replication.md)。 数据复制有助于减少进行最终目标数据更改所需的停机时间。

要使用复制 `binlog` 复制功能，有一些设置方面的[要求](../../howto-data-in-replication.md#link-source-and-replica-servers-to-start-data-in-replication)：

  - 然后，建议主服务器使用 MySQL InnoDB 引擎。 如果使用的是非 InnoDB 存储引擎，则需要将这些表迁移到 InnoDB。

  - 迁移用户必须具有权限才能在主服务器上配置二进制日志记录和创建新用户。

  - 如果主服务器启用了 SSL，请确保“mysql.az\_replication\_change\_master”存储过程中包含为域提供的 SSL CA 证书。 请参阅以下[示例](../../howto-data-in-replication.md#link-source-and-replica-servers-to-start-data-in-replication)和“master\_ssl\_ca”参数。

  - 确保主服务器的 IP 地址已添加到 Azure Database for MySQL 副本服务器的防火墙规则中。 使用 Azure 门户或 Azure CLI 更新防火墙规则。

  - 确保托管主服务器的计算机在端口 3306 上允许入站和出站流量。

  - 确保主服务器具有从源到目标的可访问 IP 地址（公共或私有）。

要使用复制执行迁移，请参阅[如何配置 Azure Database for MySQL 数据传入复制](../../howto-data-in-replication.md#link-source-and-replica-servers-to-start-data-in-replication)以了解详细信息。

`binlog` 复制方法对于 CPU 和额外存储的要求较高。 迁移用户应该在联机迁移期间在源系统上测试负载，并确定负载是否可以接受。

### <a name="azure-database-migration-service-dms"></a>Azure 数据库迁移服务 (DMS)

[Azure 数据库迁移服务 (DMS)](https://azure.microsoft.com/services/database-migration/) 是一种 Azure 基于云的工具，允许管理员跟踪各种迁移设置，并在必要时重复使用这些设置。 DMS 的工作原理是使用指向各种源和目标的设置创建迁移项目。 支持[脱机迁移](../../../dms/tutorial-mysql-azure-mysql-offline-portal.md)。 此外，它还支持本地数据负载和基于云的负载，如 Amazon 关系数据库服务 (RDS) MySQL。

尽管 DMS 服务是一个联机工具，但它依赖于 MySQL 的 `binlog` 复制功能来完成其任务。 目前，DMS 在一定程度上自动执行脱机迁移过程。 DMS 要求在目标 Azure Database for MySQL 实例中生成和复制匹配的架构。 可以使用 `mysqldump` 客户端实用工具导出架构。

### <a name="fastestminimum-downtime-migration"></a>最快/最短停机时间迁移

有许多途径来迁移数据。 迁移团队的技能组合包括决定采用哪种路径，以及数据库和应用程序所有者愿意接受的停机时间。 某些工具支持多线程并行数据迁移方法，而其他工具仅针对简单的表数据迁移而设计。

最快速且最完整的路径是使用 `binlog` 复制（直接使用 MySQL 或通过 DMS)，但带来的成本是需要添加主键。

### <a name="decision-table"></a>决策表

WWI 可通过多种路径迁移其 MySQL 工作负载。 我们提供了一个表，其中列出了每种潜在路径及其优缺点：

| 目标 | 说明 | 工具 | 先决条件 | 优点 | 缺点 |
|-----------|-------------|------|---------------|------------|---------------|
| **可能最快的迁移速度** | 并行方法| mydumper 和 myloader | Linux | 高度并行化 | 目标限制 |
| **联机迁移** | 尽可能长时间地保持源的正常状态 | binlog | 无 | 无缝 | 额外处理和存储 |
| **脱机迁移** | 尽可能长时间地保持源的正常状态 | 数据库迁移服务 (DMS)| 无 | 可重复进程 | 仅限于数据，支持所有 MySQL 版本 |
| **高度自定义的脱机迁移** | 有选择地导出对象 | mysqldump | 无 | 高度可自定义 | 手动 |
| **脱机迁移半自动化** | 基于 UI 的导出和导入 | MySQL Workbench | 下载并安装 | 半自动 | 仅支持通用开关集 |

### <a name="wwi-scenario"></a>WWI 方案

WWI 已选择其会议数据库作为首次迁移工作负载。 之所以选择工作量，是因为它的风险最小，而且由于年度会议计划的空档，可利用的停机时间最长。 此外，根据迁移团队的评估，他们确定他们尝试使用 MySQL Workbench 执行脱机迁移。

### <a name="migration-methods-checklist"></a>迁移方法清单

  - 确保为目标环境和源环境选择了正确的方法。

  - 确保该方法可以满足业务要求。

  - 请始终验证数据工作负载是否支持方法。  


## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [测试计划](./06-test-plans.md)