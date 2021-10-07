---
title: 从 Oracle 到 Azure Database for PostgreSQL：迁移指南
titleSuffix: Azure Database for PostgreSQL
description: 本指南介绍如何将 Oracle 架构迁移到 Azure Database for PostgreSQL。
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.subservice: migration-guide
ms.topic: how-to
ms.date: 03/18/2021
ms.openlocfilehash: 758199dca165c301322631e0d29b1af2711dc56b
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129356948"
---
# <a name="migrate-oracle-to-azure-database-for-postgresql"></a>将 Oracle 迁移到 Azure Database for PostgreSQL

本指南介绍如何将 Oracle 架构迁移到 Azure Database for PostgreSQL。 

如需详细而全面的迁移指南，请参阅[迁移指南资源](https://github.com/microsoft/OrcasNinjaTeam/blob/master/Oracle%20to%20PostgreSQL%20Migration%20Guide/Oracle%20to%20Azure%20Database%20for%20PostgreSQL%20Migration%20Guide.pdf)。 

## <a name="prerequisites"></a>先决条件

若要将 Oracle 架构迁移到 Azure Database for PostgreSQL，需要： 

- 验证你的源环境是否受支持。 
- 下载最新版本的 [ora2pg](https://ora2pg.darold.net/)。 
- 具有最新版本的 [DBD 模块](https://www.cpan.org/modules/by-module/DBD/)。 


## <a name="overview"></a>概述

PostgreSQL 是全球最先进的开源数据库之一。 本文介绍如何使用免费的 ora2pg 工具将 Oracle 数据库迁移到 PostgreSQL。 可以使用 ora2pg 将 Oracle 数据库或 MySQL 数据库迁移到 PostgreSQL 兼容的架构。 

ora2pg 工具将连接 Oracle 数据库，自动对其进行扫描，并提取其结构或数据。 然后，ora2pg 将生成可加载到 PostgreSQL 数据库的 SQL 脚本。 可使用 ora2pg 完成如下所述的任务：对 Oracle 数据库进行反向工程、迁移大型企业数据库，或者只是将某些 Oracle 数据复制到 PostgreSQL 数据库中。 此工具简单易用，它不要求用户具备 Oracle 数据库方面的知识，只需能够提供用于连接到 Oracle 数据库的参数即可。

> [!NOTE]
> 有关使用最新版 ora2pg 的详细信息，请参阅 [ora2pg 文档](https://ora2pg.darold.net/documentation.html)。

### <a name="typical-ora2pg-migration-architecture"></a>典型的 ora2pg 迁移体系结构

![ora2pg 迁移体系结构的屏幕截图。](media/howto-migrate-from-oracle/ora2pg-migration-architecture.png)

在预配 VM 和 Azure Database for PostgreSQL 之后，需要进行两项配置以启用它们之间的连接：允许访问 Azure 服务和强制实施 SSL 连接 ： 

- “连接安全性”边栏选项卡 ->“允许访问 Azure 服务” > “打开”  

- “连接安全性”边栏选项卡 ->“SSL 设置” > “强制实施 SSL 连接” > “已禁用”   

### <a name="recommendations"></a>建议

- 若要提高 Oracle 服务器中的评估或导出操作的性能，请收集统计信息：

   ```
   BEGIN
   
     DBMS_STATS.GATHER_SCHEMA_STATS
     DBMS_STATS.GATHER_DATABASE_STATS
     DBMS_STATS.GATHER_DICTIONARY_STATS
     END;
   ```

- 使用 `COPY` 命令而不是 `INSERT` 导出数据。

- 避免将表连同其外键 (FK)、约束和索引一同导出。 这些元素会减缓将数据导入到 PostgreSQL 的过程。

- 使用 no data 子句创建具体化视图。 稍后刷新视图。

- 如果可能，请在具体化视图中使用唯一索引。 使用 `REFRESH MATERIALIZED VIEW CONCURRENTLY` 语法时，这些索引可以加快刷新速度。


## <a name="pre-migration"></a>迁移前 

在确认你的源环境受支持并且你满足了所有先决条件之后，即准备就绪，可开始预迁移阶段。 开始时，请执行以下操作： 

1. 发现：清点需要迁移的数据库。 
2. 评估：评估这些数据库是否存在潜在的迁移问题或障碍。
3. 转换：解决所发现的任何问题或障碍。 
 
对于异构迁移（例如，从 Oracle 迁移到 Azure Database for PostgreSQL），此阶段还涉及到使源数据库架构与目标环境兼容。

### <a name="discover"></a>发现

发现阶段的目标是识别现有数据源以及有关所用功能的详细信息。 此阶段有助于更好地了解和规划迁移。 此过程涉及到扫描网络，以确定组织的所有 Oracle 实例以及正在使用的版本和功能。

适用于 Oracle 的 Microsoft 预评估脚本是针对 Oracle 数据库运行的。 预评估脚本查询 Oracle 元数据。 脚本提供：

- 数据库盘存，包括按架构、类型和状态列出的对象计数。
- 对每个架构中原始数据的粗略估算（根据统计信息给出）。
- 每个架构中表的大小。
- 每个包、函数、过程等的代码行数。

从 [github](https://github.com/microsoft/DataMigrationTeam/tree/master/Whitepapers) 下载相关脚本。

### <a name="assess"></a>评估

清点 Oracle 数据库后，你将了解数据库大小以及可能的挑战。 下一步是运行评估。

估算从 Oracle 到 PostgreSQL 的迁移成本并不容易。 为了评估迁移成本，ora2pg 会检查所有数据库对象、函数和对象的存储过程，以及它不能自动转换的 PL/SQL 代码。

ora2pg 工具具有一种内容分析模式，该模式可检查 Oracle 数据库，以生成文本报表。 该报表会显示 Oracle 数据库包含的内容和无法导出的内容。

若要激活 *分析和报告* 模式，请按以下命令中所示使用导出的类型 `SHOW_REPORT`：

```
ora2pg -t SHOW_REPORT
```

ora2pg 工具可将 SQL 和 PL/SQL 代码从 Oracle 语法转换为 PostgreSQL。 因此，在对数据库进行分析后，ora2pg 可以估算代码难度和迁移完整数据库所需的时间。

为了估算迁移成本（以人工日表示），ora2pg 允许使用名为 `ESTIMATE_COST` 的配置指令。 还可以通过命令提示符启用此指令：

```
ora2pg -t SHOW_REPORT --estimate_cost
```

默认迁移单位表示大约 5 分钟（对于一名 PostgreSQL 专家所需花费的时间而言）。 如果这是你首次进行迁移，可以使用配置指令 `COST_UNIT_VALUE` 或 `--cost_unit_value` 命令行选项增大默认迁移单位。

报表的最后一行显示迁移代码估算总量（人工日）。 该估算值跟在为各对象估算的迁移单位数之后。

在下面的代码示例中，可以看到一些评估变体： 
* 表评估
* 列评估
* 使用 5 分钟默认成本单位的架构评估
* 使用 10 分钟默认成本单位的架构评估

```
ora2pg -t SHOW_TABLE -c c:\ora2pg\ora2pg_hr.conf > c:\ts303\hr_migration\reports\tables.txt 
ora2pg -t SHOW_COLUMN -c c:\ora2pg\ora2pg_hr.conf > c:\ts303\hr_migration\reports\columns.txt
ora2pg -t SHOW_REPORT -c c:\ora2pg\ora2pg_hr.conf --dump_as_html --estimate_cost > c:\ts303\hr_migration\reports\report.html
ora2pg -t SHOW_REPORT -c c:\ora2pg\ora2pg_hr.conf –-cost_unit_value 10 --dump_as_html --estimate_cost > c:\ts303\hr_migration\reports\report2.html
```

下面是架构评估迁移级别 B-5 的输出：

* 迁移级别：

  * A - 可自动运行的迁移
    
  * B - 使用代码重写和最长 5 天的人工日成本进行的迁移
    
  * C - 使用代码重写和 5 天以上的人工日成本进行的迁移
    
* 技术级别：

   * 1 = 极简：无存储函数和触发器

   * 2 = 轻松：无存储函数但有触发器；无手动重写

   * 3 = 简单：有存储函数和/或触发器；无手动重写

   * 4 = 手动：无存储函数，但有触发器或视图以及代码重写

   * 5 = 困难：有存储函数和/或触发器以及代码重写

评估由以下部分构成： 
* 字母（A 或 B）指定迁移是否需要手动重写。

* 一个介于 1 和 5 之间的数字，指示技术难度。 

另一个选项 `-human_days_limit` 指定人工日的限制。 此处，将迁移级别设置为 C，以指示迁移需要大量的工作、完整的项目管理和迁移支持。 默认值为 10 个人工日。 可以使用配置指令 `HUMAN_DAYS_LIMIT` 永久更改此默认值。

此架构评估旨在帮助用户确定要首先迁移的数据库以及需动员的团队。

### <a name="convert"></a>转换

在最短停机时间的迁移中，迁移源会发生更改。 在一次性迁移后，它就数据和架构而言会偏离目标。 在数据同步阶段，确保捕获源中的所有更改，并准实时地将其应用到目标。 在确认所有更改都已应用到目标后，可从源环境切换到目标环境。

在迁移的此步骤中，Oracle 代码和 DDL 脚本会转换或转译为 PostgreSQL。 ora2pg 工具会自动以 PostgreSQL 格式导出 Oracle 对象。 如果不进行手动更改，某些生成的对象无法在 PostgreSQL 数据库中进行编译。  

若要了解哪些元素需要手动干预，请先编译 ora2pg 针对 PostgreSQL 数据库生成的文件。 检查日志，并进行任何必要的更改，直到架构结构与 PostgreSQL 语法兼容。


#### <a name="create-a-migration-template"></a>创建迁移模板 

建议使用 ora2pg 提供的迁移模板。 使用 `--project_base` 和 `--init_project` 选项时，ora2pg 会创建包含一个工作树、一个配置文件以及一个用于从 Oracle 数据库导出所有对象的脚本的项目模板。 有关详细信息，请参阅 [ora2pg 文档](https://ora2pg.darold.net/documentation.html)。

请使用以下命令： 

```
ora2pg --project_base /app/migration/ --init_project test_project
```

示例输出如下所示： 
   
```
ora2pg --project_base /app/migration/ --init_project test_project
        Creating project test_project.
        /app/migration/test_project/
                schema/
                        dblinks/
                        directories/
                        functions/
                        grants/
                        mviews/
                        packages/
                        partitions/
                        procedures/
                        sequences/
                        synonyms/
                        tables/
                        tablespaces/
                        triggers/
                        types/
                        views/
                sources/
                        functions/
                        mviews/
                        packages/
                        partitions/
                        procedures/
                        triggers/
                        types/
                        views/
                data/
                config/
                reports/

        Generating generic configuration file
        Creating script export_schema.sh to automate all exports.
        Creating script import_all.sh to automate all imports.
```

`sources/` 目录包含 Oracle 代码。 `schema/` 目录包含移植到 PostgreSQL 的代码。 `reports/` 目录包含 HTML 报表和迁移成本评估。


创建项目结构后，将创建一个通用配置文件。 在配置文件中定义 Oracle 数据库连接和相关的配置参数。 有关配置文件的详细信息，请参阅 [ora2pg 文档](https://ora2pg.darold.net/documentation.html)。


#### <a name="export-oracle-objects"></a>导出 Oracle 对象

接下来，运行 export_schema.sh 文件将 Oracle 对象作为 PostgreSQL 对象导出。

```
cd /app/migration/mig_project
./export_schema.sh
```

手动运行以下命令。

```
SET namespace="/app/migration/mig_project"

ora2pg -p -t DBLINK -o dblink.sql -b %namespace%/schema/dblinks -c %namespace%/config/ora2pg.conf
ora2pg -p -t DIRECTORY -o directory.sql -b %namespace%/schema/directories -c %namespace%/config/ora2pg.conf
ora2pg -p -t FUNCTION -o functions2.sql -b %namespace%/schema/functions -c %namespace%/config/ora2pg.conf 
ora2pg -p -t GRANT -o grants.sql -b %namespace%/schema/grants -c %namespace%/config/ora2pg.conf 
ora2pg -p -t MVIEW -o mview.sql -b %namespace%/schema/mviews -c %namespace%/config/ora2pg.conf
ora2pg -p -t PACKAGE -o packages.sql -b %namespace%/schema/packages -c %namespace%/config/ora2pg.conf
ora2pg -p -t PARTITION -o partitions.sql -b %namespace%/schema/partitions -c %namespace%/config/ora2pg.conf
ora2pg -p -t PROCEDURE -o procs.sql -b %namespace%/schema/procedures -c %namespace%/config/ora2pg.conf
ora2pg -p -t SEQUENCE -o sequences.sql -b %namespace%/schema/sequences -c %namespace%/config/ora2pg.conf
ora2pg -p -t SYNONYM -o synonym.sql -b %namespace%/schema/synonyms -c %namespace%/config/ora2pg.conf
ora2pg -p -t TABLE -o table.sql -b %namespace%/schema/tables -c %namespace%/config/ora2pg.conf 
ora2pg -p -t TABLESPACE -o tablespaces.sql -b %namespace%/schema/tablespaces -c %namespace%/config/ora2pg.conf
ora2pg -p -t TRIGGER -o triggers.sql -b %namespace%/schema/triggers -c %namespace%/config/ora2pg.conf 
ora2pg -p -t TYPE -o types.sql -b %namespace%/schema/types -c %namespace%/config/ora2pg.conf 
ora2pg -p -t VIEW -o views.sql -b %namespace%/schema/views -c %namespace%/config/ora2pg.conf
```

若要提取数据，请使用以下命令。

```
ora2pg -t COPY -o data.sql -b %namespace/data -c %namespace/config/ora2pg.conf
```

#### <a name="compile-files"></a>编译文件

最后，针对 Azure Database for PostgreSQL 服务器编译所有文件。 可以选择加载手动生成的 DDL 文件，或使用第二个脚本 import_all.sh 以交互方式导入这些文件。

```
psql -f %namespace%\schema\sequences\sequence.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database -l %namespace%\ schema\sequences\create_sequences.log

psql -f %namespace%\schema\tables\table.sql -h server1-server.postgres.database.azure.com p 5432 -U username@server1-server -d database -l %namespace%\schema\tables\create_table.log
```

数据导入命令如下所示：

```
psql -f %namespace%\data\table1.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database -l %namespace%\data\table1.log

psql -f %namespace%\data\table2.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database -l %namespace%\data\table2.log
```

在编译文件时，请检查日志并更正 ora2pg 无法自行转换的任何语法。

有关详细信息，请参阅[Oracle 到 Azure Database for PostgreSQL 迁移问题的解决方法](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20Database%20for%20PostgreSQL%20Migration%20Workarounds.pdf)。

## <a name="migrate"></a>Migrate 

满足必要的先决条件并完成预迁移步骤后，便可开始架构和数据迁移。

### <a name="migrate-schema-and-data"></a>迁移架构和数据

进行必要的修复后，即可部署数据库的稳定版本。 运行 `psql` 导入命令，指向包含修改后的代码的文件。 此任务针对 PostgreSQL 数据库编译数据库对象，并导入数据。

在此步骤中，对于导入数据，可以实施某种程度的并行导入。

### <a name="sync-data-and-cut-over"></a>同步数据和切换

在联机（最短停机时间）迁移中，迁移源将继续更改。 在一次性迁移后，它就数据和架构而言会偏离目标。 

在数据同步阶段，确保捕获源中的所有更改，并准实时地将其应用到目标。 在确认所有更改都已应用后，可从源环境切换到目标环境。

若要进行联机迁移，可联系 AskAzureDBforPostgreSQL@service.microsoft.com 寻求支持。

在使用 ora2pg 的差异/增量迁移中，对于每个表，请使用按日期、时间或其他参数筛选（切入）的查询 。 然后使用第二个查询迁移剩余数据，以完成迁移。

在源数据表中，先迁移所有历史数据。 下面是一个示例：

```
select * from table1 where filter_data < 01/01/2019
```

可以运行如下命令查询自初始迁移以来的更改：

```
select * from table1 where filter_data >= 01/01/2019
```

在这种情况下，建议检查两端（源和目标）的数据奇偶性来增强验证。

## <a name="post-migration"></a>迁移后 

在“迁移”阶段之后，完成迁移后的任务，确保所有操作尽可能顺利高效地运作。

### <a name="remediate-applications"></a>修正应用程序

将数据迁移到目标环境后，以前使用源的所有应用程序都需要开始使用目标。 此设置有时需要对应用程序进行更改。

### <a name="test"></a>测试

将数据迁移到目标后，针对数据库运行测试，验证应用程序是否可很好地与目标配合运作。 对 Oracle 源和 PostgreSQL 目标数据库运行手动数据验证脚本，确保源和目标正确迁移。

理想情况下，如果源和目标数据库采用网络路径，则应使用 ora2pg 进行数据验证。 可以使用 `TEST` 操作确保已在 PostgreSQL 中创建 Oracle 数据库中的所有对象。 

运行以下命令：

```
ora2pg -t TEST -c config/ora2pg.conf > migration_diff.txt
```

### <a name="optimize"></a>优化

迁移后阶段对于协调任何数据准确性问题和验证完整性至关重要。 在此阶段中，你还需解决工作负载的性能问题。

## <a name="migration-assets"></a>迁移资产 

有关此迁移场景的详细信息，请参阅以下资源。 它们支持实际的迁移项目参与。

| 资源 | 说明    |
| -------------- | ------------------ |
| [Oracle 到 Azure PostgreSQL 迁移指南](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf) | 此文档可帮助架构师、咨询人员、数据库管理员及相关角色使用 ora2pg 快速将工作负载从 Oracle 迁移到 Azure Database for PostgreSQL。 |
| [Oracle 到 Azure PostgreSQL 迁移问题的解决方法](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20Database%20for%20PostgreSQL%20Migration%20Workarounds.pdf) | 此文档可帮助架构师、咨询人员、数据库管理员及相关角色在将工作负载从 Oracle 迁移到 Azure Database for PostgreSQL 时快速修复和解决问题。 |
| [在 Windows 或 Linux 上安装 ora2pg 的步骤](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows%20and%20Linux.pdf)                       | 本文档提供有关在 Windows 或 Linux 上使用 ora2pg 将架构和数据从 Oracle 迁移到 Azure Database for PostgreSQL 的快速安装指南。 有关详细信息，请参阅 [ora2pg 文档](http://ora2pg.darold.net/documentation.html)。 |

数据 SQL 工程团队开发了这些资源。 此团队的核心章程是解锁和加速到 Microsoft Azure 数据平台的数据平台迁移项目的复杂现代化进程。

## <a name="more-support"></a>更多支持

有关 ora2pg 工具范畴之外的迁移帮助，可联系 [@Ask Azure DB for PostgreSQL](mailto:AskAzureDBforPostgreSQL@service.microsoft.com)。

## <a name="next-steps"></a>后续步骤

如需一整套用于数据库和数据迁移以及用于特殊任务的服务和工具，请参阅[数据迁移服务和工具](../dms/dms-tools-matrix.md)。

文档： 
- [Azure Database for PostgreSQL 文档](./index.yml)
- [ora2pg 文档](https://ora2pg.darold.net/documentation.html)
- [PostgreSQL 网站](https://www.postgresql.org/)
- [PostgreSQL 中的自治事务支持](http://blog.dalibo.com/2016/08/19/Autonoumous_transactions_support_in_PostgreSQL.html) 
