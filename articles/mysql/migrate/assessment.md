---
title: 从本地 MySQL 到 Azure Database for MySQL 的迁移指南评估
description: 在开始迁移 MySQL 工作负荷之前，必须先执行大量尽职调查。
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 05/25/2021
ms.openlocfilehash: f5a02a9e4ec6b6c1e9961637d588a3838570c432
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111969908"
---
# <a name="mysql-on-premises-to-azure-database-for-mysql-migration-guide-assessment"></a>从本地 MySQL 到 Azure Database for MySQL 的迁移指南评估

在开始迁移 MySQL 工作负荷之前，必须先执行大量尽职调查。 这其中包括分析数据、托管环境和应用程序工作负荷，以验证 Azure 登陆区域是否已正确配置，并准备好托管即将迁移的工作负荷。

### <a name="limitations"></a>限制

Azure Database for MySQL 是以平台即服务运行的 MySQL 社区版，而且提供全方位支持。 但在执行初始评估时，你还需要熟知[一些限制](../concepts-limits.md)。

最重要的限制包括：

  - 存储引擎仅支持 `InnoDB` 和 `Memory`

  - 有限的 `Privilege` 支持（`DBA`、`SUPER`、`DEFINER`）

  - 禁用数据操作语句（`SELECT ... INTO OUTFILE`、`LOAD DATA INFILE`）

  - 自动迁移重要数据库（5.6 至 5.7、5.7 至 8.0）

  - 使用 [MySQL Server 用户定义函数 (UDF) 时，](https://dev.mysql.com/doc/refman/5.7/en/server-udfs.html) 唯一可行的托管选项是 Azure 托管 VM，因为无法将 `so` 或 `dll` 组件上传到 Azure Database for MySQL。

许多其他限制都与操作有关，而且管理员应当熟悉这些限制以便管理操作数据工作负荷生命周期。 对于这些操作方面，其中很多都会在本指南的“迁移后管理”部分探讨。

### <a name="mysql-versions"></a>MySQL 版本

MySQL 创建于 1995 年，拥有丰富的版本修改历史。 而且自那时起，其便已发展为广泛使用的数据库管理系统。 Azure Database for MySQL 最初支持 MySQL 5.6 版，现已扩展到 5.7 版及最新的 8.0 版。 有关 Azure Database for MySQL 最新支持的版本信息，请参阅 [Azure Database for MySQL 支持的服务器版本。](../concepts-supported-versions.md) 在“迁移后管理”部分，我们将查看如何在 Azure 中升级 MySQL 实例（例如从 5.7.20 升级到 5.7.21）。

> [!NOTE]
> 从 5.x 版跃升至 8.0 版很大程度上是因为 Oracle 收购了 MySQL。 若要阅读有关 MySQL 的更多发展信息，请导航到 [MySQL Wiki 介绍页](https://en.wikipedia.org/wiki/MySQL)。

必须要了解 MySQL 源版本。 使用系统的应用程序可能正在使用特定于该版本的数据库对象和功能。 因此，将数据库迁移到较低版本可能会引致兼容性问题并丢失功能。 此外，我们建议在迁移到较新版本之前，全面测试数据和应用程序实例，因为引入的功能可能会破坏应用程序。

可能影响迁移路径和版本的示例：

  - 5.6：时间戳列，用于正确存储毫秒时间以及全文搜索

  - 5.7：支持本机 JSON 数据类型

  - 8.0：支持 NoSQL 文档存储、Crash Safe DDL 原子化和 JSON 表函数

    > [!NOTE]
    > 2021 年 2 月将停止提供 MySQL 5.6 的常规支持。 MySQL 工作负载将需要迁移到 MySQL 5.7 或更高版本。

如要检查 MySQL 服务器版本：

```
SHOW VARIABLES LIKE "%version%";
```

#### <a name="database-storage-engines"></a>数据库存储引擎

Azure Database for MySQL 仅支持 [InnoDB](https://dev.mysql.com/doc/refman/8.0/en/innodb-storage-engine.html) 和[内存](https://dev.mysql.com/doc/refman/8.0/en/memory-storage-engine.html)数据库存储引擎。 [MyISAM](https://dev.mysql.com/doc/refman/8.0/en/myisam-storage-engine.html) 等其他存储引擎将需要迁移到受支持的存储引擎。 MyISAM 和 InnoDB 之间的区别在于操作功能和性能输出。 引擎之间的高级别表和架构结构通常不会发生变化，但索引列和表列类型可能出于存储和性能原因有所更改。 尽管已知 InnoDB 的数据文件大小较大，但这些存储详细信息由 Azure Database for MySQL 服务管理。

##### <a name="migrating-from-myisam-to-innodb"></a>从 MyISAM 迁移到 InnoDB

MyISAM 数据库和表将需要转换为 InnoDB 表。 完成转换后，应该测试应用程序的兼容性和性能。 在大多数情况下，测试需要重新创建表，并通过 DDL 语句更改目标存储引擎。 在迁移期间不太可能需要执行此更改，因为此更改将会在 Azure 目标中创建架构期间发生。 有关更多详细信息，请参阅 MySQL 网站上的[转换表开发人员文档](https://dev.mysql.com/doc/refman/5.6/en/converting-tables-to-innodb.html)。

若要查找有用的表信息，请使用以下查询：

```dotnetcli
    SELECT 
        tab.table_schema,   
        tab.table_name,   
        tab.engine as engine_type,   
        tab.auto_increment,   
        tab.table_rows,   
        tab.create_time,   
        tab.update_time,   
        tco.constraint_type 
    FROM information_schema.tables tab   
    LEFT JOIN information_schema.table_constraints tco   
        ON (tab.table_schema = tco.table_schema   
            AND tab.table_name = tco.table_name   
            )   
    WHERE  
        tab.table_schema NOT IN ('mysql', 'information_schema', 'performance_
schema', 'sys')  
        AND tab.table_type = 'BASE TABLE';
```

> [!NOTE]
> 跨多个数据库对 INFORMATION\_SCHEMA 运行查询可能会影响性能。 因此，请在使用率较低的时段运行。

若要将 MyISAM 表转换为 InnoDB 表，请运行以下内容：

```
ALTER TABLE {table\_name} ENGINE=InnoDB;
```

> [!NOTE]
> 此转换方法会导致表锁定，所有应用程序都需等待该操作完成。 表锁定会导致数据库在短期内显示为脱机状态。

你也可以改为使用相同结构但不同的存储引擎来创建该表。 完成创建后，将行复制到新表中：

```
INSERT INTO {table\_name} SELECT * FROM {myisam\_table} ORDER BY {primary\_key\_columns}
```

> [!NOTE]
> 要使此方法生效，需要删除原表，然后重命名新表。 此任务会导致短暂停机。

#### <a name="database-data-and-objects"></a>数据库数据和对象

数据是数据库迁移的一个组件。 因此，必须迁移并验证支持对象的数据库，确保应用程序能够继续可靠运行。

下面是应当在迁移之前和之后清点的各项列表：

  - 表（架构）

  - 主键、外键

  - 索引

  - 函数

  - 过程

  - 触发器

  - 视图

#### <a name="user-defined-functions"></a>用户定义函数

MySQL 允许使用调用外部代码的函数。 但遗憾的是，使用用户定义函数 (UDF) 的数据工作负荷无法迁移到 Azure Database for MySQL。 其原因在于，必需要在支持 MySQL 函数的前提下，才能将 dll 代码上传到 Azure 服务器。

运行以下查询，查找可能已安装的任何 UDF：

```
SELECT * FROM mysql.func;
```

### <a name="source-systems"></a>源系统

根据不同的源系统及其位置，迁移准备的工作量也会有所差异。 除数据库对象之外，还应考虑如何将数据从源系统迁移到目标系统。 如果源系统和目标系统之间设有防火墙和其他网络组件，迁移数据的难度将会增加。

此外，通过互联网将数据移至 Azure 的速度可能要比使用专用线路要慢。 因此，在移动多个 GB、TB 和 PB 的数据时，请考虑在源网络与 Azure 网络之间设置 [ExpressRoute](../../expressroute/expressroute-introduction.md) 连接。

如果 ExpressRoute 已经存在，则可能是有其他应用程序正在使用该连接。 通过现有路由执行迁移可能会导致网络吞吐量负担加重，并可能致使使用网络的迁移及其他应用程序的性能明显降低。

最后，必须评估磁盘空间。 导出大型数据库时，请考虑数据的大小。 确保有运行该工具的系统，并且最终的导出位置可提供充足的磁盘空间来执行导出操作。

#### <a name="cloud-providers"></a>云提供商

若要从 Amazon Web Services (AWS) 等云服务提供商迁移数据库，则可能需要执行额外的网络配置步骤，才能访问云托管的 MySQL 实例。 迁移工具（如数据迁移服务）需要从 IP 范围以外的区域进行访问，因此有可能遭到阻止。

#### <a name="on-premises"></a>本地

与云提供商一样，如果 MySQL 数据工作负载位于防火墙或其他网络安全层后面，将需要在本地实例与 Azure Database for MySQL 之间创建路径。

### <a name="tools"></a>工具

有许多工具和方法可用于评估 MySQL 数据工作负荷和环境。 每个工具都会提供一组不同的评估以及迁移特性和功能。 我们将在本指南中查看用于最常用于评估 MySQL 数据工作负荷的工具。

#### <a name="azure-migrate"></a>Azure Migrate

尽管 [Azure Migrate](../../migrate/migrate-services-overview.md) 不支持直接迁移 MySQL 数据库工作负荷，但若管理员并不确定正在使用数据（无论是托管在虚拟机还是基于硬件的计算机上）的用户和应用程序，便可使用此方法。 若要完成[依赖关系分析](../../migrate/concepts-dependency-visualization.md)，则可以通过在托管 MySQL 工作负荷的计算机上安装并运行监视代理来实现。 该代理会收集一段时间（例如一个月）内的信息。 可以分析依赖关系数据，以查找建立用来连接数据库的未知连接。 而连接数据可帮助标识需要接收待迁移通知的应用程序所有者。

除了对应用程序和用户连接数据进行依赖关系分析以外，你还可以使用 Azure Migrate 来分析 [Hyper-V、VMware 或物理服务器](../../migrate/migrate-appliance-architecture.md)以提供数据库工作负荷的使用模式，从而帮助推荐适当的目标环境。

#### <a name="telgraf-for-linux"></a>适用于 Linux 的 Telgraf

Linux 工作负荷可以利用 [Microsoft Monitoring Agent (MMA) ](../../azure-monitor/agents/agent-linux.md) 在虚拟机和物理计算机上收集数据。 此外，可以考虑使用 [Telegraf 代理](../../azure-monitor/essentials/collect-custom-metrics-linux-telegraf.md)及其各种插件来收集性能指标。

#### <a name="service-tiers"></a>服务层级

借助评估信息（CPU、内存、存储等），迁移用户要做的下一个选择便是确定要开始使用的 Azure Database for MySQL [定价层](../concepts-pricing-tiers.md)。

目前有三个定价层：

  - 基本：适用于需要少量计算及 I/O 性能的工作负荷。

  - 常规用途：适用于大多数需要均衡计算以及内存可缩放 I/O 吞吐量的业务工作负荷。

  - 内存优化：：适用于需要内存中性能来提高事务处理速度及提升并发性的高性能数据库工作负荷。

有关层的使用决定可能会受到数据工作负荷的 RTO 和 RPO 要求的影响。 如果数据工作负荷需要超过 4 TB 的存储空间，则需要执行额外步骤。 查看并选择[支持](../concepts-pricing-tiers.md#storage)高达 16 TB 存储空间的区域。

> [!NOTE]
> 如要了解不支持你的存储要求的区域，请联系 MySQL 团队 (AskAzureDBforMySQL@service.microsoft.com)。

通常，决策的重点将会是存储和 IOPS 需求，或是每秒需要执行的输入/输出操作。 因此，目标系统将始终至少需要与源系统相同的存储。 此外，尽管 IOPS 获分配 3 GB 存储空间，但一定要根据最终存储大小满足 IOPs 的存储空间需求。

| 因素          | 层                                                                                                                                                                                                            |
|------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **基本**            | 开发计算机，无需高性能，存储不到 1 TB                                                                                                                                    |
| **常规用途**  | 对 IOPS 的需求超过了基本层的提供上限，但存储需求量少于 16 TB，内存需求量少于 4 GB                                                                                               |
| **内存优化** | 利用高内存或高缓存以及与缓冲区相关的服务器配置（例如，高并发 innodb_buffer_pool_instances、大型 BLOB 大小、包含多个复制目标的系统）的数据工作负载 |

#### <a name="costs"></a>成本

在评估所有 WWI MySQL 数据工作负荷后，WWI 确定这些工作负荷需要至少 4 个 vCore、20 GB 内存以及至少 100 GB 存储空间（IOP 容量为 450 IOPS）。 由于使用 [Azure Database for MySQL IOPS 分配法](../concepts-pricing-tiers.md#storage)，因此需要分配至少 150 GB 存储空间才可满足 450 IOPS 要求。 此外，这些工作负荷至少还需要高达 100% 的预配服务器存储空间用作备份存储空间，以及一份只读副本。 出站流出量预计不会超过 5 GB。

由于使用 [Azure Database for MySQL 定价计算器](https://azure.microsoft.com/en-us/pricing/details/mysql/)，WWI 已经能够确定 Azure Database for MySQL 实例的使用成本。 截至 2020 年 9 月，WWI 会议数据库的总拥有成本 (TCO) 如下表所示：

| 资源                  | 说明                       | 数量                                                    | 开销          |
|---------------------------|-----------------------------------|-------------------------------------------------------------|---------------|
| 计算（常规用途） | 4 个 vCore，20 GB                    | 1 @ $0.351/小时                                               | $3074.76/年 |
| **存储**                   | 5 GB                               | 12 x 150 @ $0.115                                           | $207/年     |
| **备份**                    | 高达 100% 的预配存储空间 | 高达 100% 的预配服务器存储空间，不会产生额外成本 | $0.00/年    |
| 只读副本              | 1 份辅助区域副本           | 计算 + 存储                                           | $3281.76/年 |
| **Network**                   | < 5GB/月流出量                | 免费                                                        |               |
| **总计**                     |                                   |                                                             | $6563.52/年 |

查看初始成本后，WWI 的 CIO 确认其使用 Azure 的时间将超过 3 年。 因此，他们决定使用 3 年期[保留实例](../concept-reserved-pricing.md)来节省每年约需额外支出的 4,000 美元：

| 资源                  | 说明                       | 数量                                                     | 开销         |
|---------------------------|-----------------------------------|--------------------------------------------------------------|--------------|
| 计算（常规用途） | 4 个 vCore                          | 1 @ $0.1375/小时                                               | $1204.5/年 |
| **存储**                   | 5 GB                               | 12 x 150 @ $0.115                                            | $207/年    |
| **备份**                    | 高达 100% 的预配存储空间 | 高达 100% 的预配服务器存储空间，不会产生额外成本 | $0.00/年   |
| **Network**                   | < 5GB/月流出量                | 免费                                                         |              |
| 只读副本              | 1 份辅助区域副本           | 计算 + 存储                                            | $1411.5/年 |
| **总计**                     |                                   |                                                              | $2823/年   |

如上表所示，备份、网络流出量和任何只读副本都必须纳入总拥有成本 (TCO) 的考虑范畴。 随着添加更多数据库，存储空间和生成的网络流出量将成为唯一需要额外考虑的基于成本的因素。

> [!NOTE]
> 上述估算不包括任何适用于应用程序层的 [ExpressRoute](../../expressroute/expressroute-introduction.md)、[Azure 应用网关](../../application-gateway/overview.md)、[Azure 负载均衡器](../../load-balancer/load-balancer-overview.md)或[应用服务](../../app-service/overview.md)成本。
>
> 上述定价可能会随时变化，并且将会因地区而异。

#### <a name="application-implications"></a>应用程序影响

在迁移到 Azure Database for MySQL 的过程中，应用程序要作出的其中一项最为重大的更改可能便是——转为使用基于通信的安全套接字层 (SSL)。 Azure Database for MySQL 默认启用 SSL，这可能是因为本地应用程序和数据工作负荷未经专门设置以使用 SSL 连接 MySQL。 在启用时，使用 SSL 会产生一些额外的处理开销，应该受到监视。

> [!NOTE]
> 尽管 SSL 默认启用，但你也可以选择禁用。

按照[在应用程序中配置 SSL 连接性以安全连接到 Azure Database for MySQL](../howto-configure-ssl.md) 中的所述活动重新配置应用程序，以支持此强身份验证路径。

最后，修改应用程序连接字符串中的服务器名称，或者将 DNS 切换为指向新的 Azure Database for MySQL 服务器。

### <a name="wwi-scenario"></a>WWI 场景

WWI 通过收集有关其 MySQL 数据的信息来开始评估。 其可编译以下内容：

| 名称         | 源      | 数据库引擎 | 大小 | IOPS | 版本 | 所有者          | 故障时间 |
|--------------|-------------|-----------|------|------|---------|----------------|----------|
| WwwDB        | AWS (PaaS)  | InnoDB    | 1 GB  | 150  | 5.7     | 营销部门 | 1 小时     |
| BlogDB       | AWS (PaaS)  | InnoDB    | 1 GB  | 100  | 5.7     | 营销部门 | 4 小时    |
| ConferenceDB | 本地 | InnoDB    | 5 GB  | 50   | 5.5     | 销售部门     | 4 小时    |
| CustomerDB   | 本地 |           | 10 GB | 75   | 5.5     | 销售部门     | 2 小时    |
| SalesDB      | 本地 | InnoDB    | 20 GB | 75   | 5.5     | 销售部门     | 1 小时     |

已经联系每个数据库所有者，以确定可接受的停机时间。 所选的计划和迁移方法基于可接受的数据库停机时间。

在第一阶段，WWI 仅侧重于 ConferenceDB 数据库。 团队需要参与迁移，以帮助处理数据工作负荷迁移。 选择 ConferenceDB 数据库的原因在于，数据库结构简单且可接受的停机时间较长。 成功迁移数据库后，团队便侧重于将应用程序迁移到安全的 Azure 登陆区域。

### <a name="assessment-checklist"></a>评估清单

  - 测试工作负荷能否在目标系统上顺利运行。

  - 确保为迁移提供正确的网络组件。

  - 了解数据工作负荷资源要求。

  - 估算总成本。

  - 了解停机时间要求。

  - 做好更改应用程序的准备。


> [!div class="nextstepaction"]
> [规划](./planning.md)