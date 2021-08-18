---
title: 导入和导出 - Azure Database for MySQL
description: 本文介绍使用 MySQL Workbench 等工具在 Azure Database for MySQL 导入和导出数据库的常用方法。
author: savjani
ms.author: pariks
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
ms.date: 10/30/2020
ms.openlocfilehash: 954e764cfa454cdfa3175a614093eda6ea8d6173
ms.sourcegitcommit: 2cff2a795ff39f7f0f427b5412869c65ca3d8515
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2021
ms.locfileid: "113598371"
---
# <a name="migrate-your-mysql-database-by-using-import-and-export"></a>使用导入和导出迁移 MySQL 数据库

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

本文介绍通过使用 MySQL Workbench 将数据导入和导出到 Azure Database for MySQL 服务器的两种常用方法。

有关详细全面的迁移指导，请参阅[迁移指南资源](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide)。 

有关其他迁移方案，请参阅[数据库迁移指南](https://datamigration.microsoft.com/)。

## <a name="prerequisites"></a>先决条件

在开始迁移 MySQL 数据库之前，需要：

- [使用 Azure 门户创建 Azure Database for MySQL 服务器](quickstart-create-mysql-server-database-using-azure-portal.md)。
- 下载并安装 [MySQL Workbench](https://dev.mysql.com/downloads/workbench/) 或其他第三方 MySQL 工具以用于导入和导出。

## <a name="create-a-database-on-the-azure-database-for-mysql-server"></a>在 Azure Database for MySQL 服务器上创建数据库

使用 MySQL Workbench、Toad 或 Navicat 在 Azure Database for MySQL 服务器上创建一个空数据库。 数据库名称可与包含转储数据的数据库名称相同，或可以创建一个不同名称的数据库。

若要建立连接，请执行以下操作：

1. 在 Azure 门户中，查看 Azure Database for MySQL 的“概述”窗格中的连接信息。

   :::image type="content" source="./media/concepts-migrate-import-export/1_server-overview-name-login.png" alt-text="Azure 门户中 Azure Database for MySQL 服务器连接信息的屏幕截图。":::

1. 将连接信息添加到 MySQL Workbench。

   :::image type="content" source="./media/concepts-migrate-import-export/2_setup-new-connection.png" alt-text="MySQL Workbench 连接字符串的屏幕截图。":::

## <a name="determine-when-to-use-import-and-export-techniques"></a>确定何时使用导入和导出技术

> [!TIP]
> 如果你要转储和还原整个数据库，请改用[转储和还原](concepts-migrate-dump-restore.md)方法。

对于以下情况，请使用 MySQL 工具将数据库导入和导出到 Azure MySQL 数据库中。 有关其他工具，请参阅 [MySQL 到 Azure 数据库迁移指南](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide)的“迁移方法”部分（第 22 页）。 

- 需要有选择性地选择要从现有 MySQL 数据库导入到 Azure MySQL 数据库的少数几个表时，最好使用导入和导出方法。 这样做，可以在迁移过程中省略任何不需要的表，从而节省时间和资源。 例如，将 `--include-tables` 或 `--exclude-tables` 开关与 [mysqlpump](https://dev.mysql.com/doc/refman/5.7/en/mysqlpump.html#option_mysqlpump_include-tables) 配合使用，以及将 `--tables` 开关与 [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html#option_mysqldump_tables) 配合使用。
- 移动除表以外的数据库对象时，请显式创建这些对象。 包含约束（主键、外键和索引）、视图、函数、过程、触发器和想要迁移的任何其他数据库对象。
- 从 MySQL 数据库以外的外部数据源迁移数据时，使用 [mysqlimport](https://dev.mysql.com/doc/refman/5.7/en/mysqlimport.html) 创建平面文件并导入它们。

> [!Important]
> 单一服务器和灵活服务器仅支持 InnoDB 存储引擎。 将数据载入 Azure Database for MySQL 时，请确保数据库中的所有表都使用 InnoDB 存储引擎。
>
> 如果源数据库使用其他存储引擎，请在迁移数据库之前将其转换为 InnoDB 引擎。 例如，如果你有使用 MyISAM 引擎的 WordPress 或 Web 应用，需首先通过将数据迁移到 InnoDB 表中的方式来转换表。 使用子句 `ENGINE=INNODB` 设置用于创建表的引擎，然后在迁移之前将数据传输到兼容表中。

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```

## <a name="performance-recommendations-for-import-and-export"></a>导入和导出的性能建议

为了获得最佳的数据导入和导出性能，我们建议执行以下操作：

- 在加载数据之前创建聚集索引和主键。 按主键顺序加载数据。
- 延迟创建辅助索引，直到数据加载完毕之后。
- 在加载数据之前禁用外键约束。 禁用外键检查可以显著提高性能。 启用约束并在加载后验证数据，确保引用完整性。
- 并行加载数据。 避免太多将导致达到资源限制的并行度，并通过使用 Azure 门户中提供的指标监视资源。
- 适当时使用已分区表。

## <a name="import-and-export-data-by-using-mysql-workbench"></a>使用 MySQL Workbench 导入和导出数据

可以在 MySQL Workbench 中通过两种方法导出和导入数据：从对象浏览器上下文菜单，或者从“导航器”窗格。 每种方法的目的不同。

> [!NOTE]
> 如果要在 MySQL Workbench 上添加与 MySQL 单一服务器或灵活服务器（预览版）的连接，请执行以下操作：
>
> - 对于 MySQL 单一服务器，请确保用户名采用 *\<username@servername>* 格式。
> - 对于 MySQL 灵活服务器，请仅使用 *\<username>* 。 如果使用 *\<username@servername>* 进行连接，连接将会失败。

### <a name="run-the-table-data-export-and-import-wizards-from-the-object-browser-context-menu"></a>从对象浏览器上下文菜单运行表数据导出和导入向导

:::image type="content" source="./media/concepts-migrate-import-export/p1.png" alt-text="对象浏览器上下文菜单中 MySQL Workbench 导出和导入向导命令的屏幕截图。":::

表数据向导支持使用 CSV 和 JSON 文件执行导入和导出操作。 向导中包括多个配置选项，如分隔符、列选择和编码选择。 可以对本地或远程连接的 MySQL 服务器运行每个向导。 导入操作包括表、列和类型映射。

若要从对象浏览器上下文菜单访问这些向导，请右键单击某个表，然后选择“表数据导出向导”或“表数据导入向导” 。

#### <a name="the-table-data-export-wizard"></a>表数据导出向导

若要将表导出到 CSV 文件，请执行以下操作：

1. 右键单击要导出的数据库的表。
1. 选择“表数据导出向导”。 选择要导出的列、行偏移量（如果存在）、计数（如果存在）。
1. 在“选择要导出的数据”窗格中，选择“下一步” 。 选择文件路径、CSV 或 JSON 文件类型。 此外选择行分隔符、封闭字符串的方法和字段分隔符。
1. 在“选择输出文件位置”窗格中，选择“下一步” 。
1. 在“导出数据”窗格中，选择“下一步” 。

#### <a name="the-table-data-import-wizard"></a>表数据导入向导

若要从 CSV 文件中导入表，请执行以下操作：

1. 右键单击要导入的数据库的表。
1. 找到并选择要导入的 CSV 文件，然后选择“下一步”。
1. 选择目标表（新的或现有的），选中或清除“导入前截断表”复选框，然后选择“下一步” 。
1. 选择编码和要导入的列，然后选择“下一步”。
1. 在“导入数据”窗格中，选择“下一步” 。 向导将导入数据。

### <a name="run-the-sql-data-export-and-import-wizards-from-the-navigator-pane"></a>从“导航器”窗格运行 SQL 数据导出和导入向导

使用向导导出或导入从 MySQL Workbench 或从 mysqldump 命令生成的 SQL 数据。 可以从“导航器”窗格访问向导，也可以从主菜单中选择“服务器” 。

#### <a name="export-data"></a>导出数据

:::image type="content" source="./media/concepts-migrate-import-export/p2.png" alt-text="在 MySQL Workbench 中使用“导航器”窗格显示“数据导出”窗格的屏幕截图。":::

可以使用“数据导出”窗格导出 MySQL 数据。

1. 在 MySQL Workbench 的“导航器”窗格中，选择“数据导出” 。

1. 在“数据导出”窗格中，选择要导出的每个架构。
 
   对于每个架构，可以选择要导出的特定架构对象或表。 配置选项包括导出到项目文件夹或自包含的 SQL 文件、转储存储的例程和事件，或跳过表数据。

   或者，使用“导出结果集”将 SQL 编辑器中的特定结果集导出为其他格式，例如 CSV、JSON、HTML 和 XML。

1. 选择要导出的数据库对象，并配置相关选项。
1. 选择“刷新”加载当前对象。
1. （可选）选择右上角的“高级选项”以完善导出操作。 例如，添加表锁、使用 `replace` 而不是 `insert` 语句、使用反撇号字符将标识符括起来。
1. 选择“开始导出”开始导出过程。


#### <a name="import-data"></a>导入数据

:::image type="content" source="./media/concepts-migrate-import-export/p3.png" alt-text="在 MySQL Workbench 中使用“导航器”窗格显示“数据导入”窗格的屏幕截图。":::

可以使用“数据导入”窗格来导入或者还原通过数据导出操作或 mysqldump 命令导出的数据。

1. 在 MySQL Workbench 的“导航器”窗格中，选择“数据导出/还原” 。
1. 选择项目文件夹或自包含的 SQL 文件、选择要导入到的架构，或选择“新建”按钮定义新架构。
1. 选择“开始导入”开始导入过程。

## <a name="next-steps"></a>后续步骤

- 有关其他迁移方法，请参阅[使用转储和还原将 MySQL 数据库迁移到 Azure Database for MySQL](concepts-migrate-dump-restore.md)。
- 有关将数据库迁移到 Azure Database for MySQL 的详细信息，请参阅[数据库迁移指南](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide)。
