---
title: 使用转储和还原进行迁移 - Azure Database for MySQL
description: 本文介绍使用 mysqldump、MySQL Workbench 和 PHPMyAdmin 等工具在 Azure Database for MySQL 中备份和还原数据库的两种常见方式。
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 10/30/2020
ms.openlocfilehash: 70858b1ff5251c55d4f09f24e4ba6e418c86b1ed
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "113089460"
---
# <a name="migrate-your-mysql-database-to-azure-database-for-mysql-using-dump-and-restore"></a>使用转储和还原将 MySQL 数据库迁移到 Azure Database for MySQL

[!INCLUDE[applies-to-mysql-single-flexible-server](includes/applies-to-mysql-single-flexible-server.md)]

本文介绍了在 Azure Database for MySQL 中备份和还原数据库的两种常见方式
- 从命令行转储和还原（使用 mysqldump）
- 使用 PHPMyAdmin 转储和还原

还可参阅[数据库迁移指南](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide)，获取有关将数据库迁移到 Azure Database for MySQL 的详细信息和用例。 遵循本指南提供的指导，就可以成功地进行规划和执行到 Azure 的 MySQL 迁移。

## <a name="before-you-begin"></a>开始之前
若要逐步执行本操作方法指南，需要具备以下条件：
- [创建 Azure Database for MySQL 服务器 - Azure 门户](quickstart-create-mysql-server-database-using-azure-portal.md)
- 已在计算机上安装 [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html) 命令行实用程序。
- [MySQL Workbench](https://dev.mysql.com/downloads/workbench/) 或其他用于执行转储和还原命令的第三方 MySQL 工具。

> [!TIP]
> 如果希望迁移数据库大小超过 1 TB 的大型数据库，则可能需要考虑使用支持并行导出和导入的社区工具（如 mydumper/myloader）。 了解[如何迁移大型 MySQL 数据库](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/best-practices-for-migrating-large-databases-to-azure-database/ba-p/1362699)。


## <a name="common-use-cases-for-dump-and-restore"></a>转储和还原的常见用例

最常见的用例包括：

- **从其他托管服务提供商转移** - 由于安全原因，大多数托管服务提供商可能不提供对物理存储文件的访问，因此逻辑备份和还原是迁移的唯一选项。
- **从本地环境或虚拟机迁移** - Azure Database for MySQL 不支持还原物理备份，这使得逻辑备份和还原成为唯一方法。
- **将备份存储从本地冗余存储迁移到异地冗余存储** - Azure Database for MySQL 允许为备份配置本地冗余存储或异地冗余存储，但只有在服务器创建期间才能这样做。 预配服务器以后，不能更改备份存储冗余选项。 若要将备份存储从本地冗余存储移到异地冗余存储，只能选择“转储和还原”选项。 
-  **从备选存储引擎迁移到 InnoDB** - Azure Database for MySQL 仅支持 InnoDB 存储引擎，因此不支持备选存储引擎。 如果表配置了其他存储引擎，请确保先将它们转换为 InnoDB 引擎格式，再迁移到 Azure Database for MySQL。

    例如，如果有使用 MyISAM 表的 WordPress 或 WebApp，在将这些表还原到 Azure Database for MySQL 之前，首先通过将这些表迁移到 InnoDB 格式的方式转换格式。 使用子句 `ENGINE=InnoDB` 设置创建新表时所用的引擎，然后在还原之前将数据传输到兼容表中。

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```
> [!Important]
> - 若要避免任何兼容性问题，请确保转储数据库时，源和目标系统上所使用的 MySQL 版本相同。 例如，如果现有 MySQL 服务器版本是 5.7，那么应迁移到配置为运行版本 5.7 的 Azure Database for MySQL 中。 在 Azure Database for MySQL 中，`mysql_upgrade` 命令不起作用，也不受支持。 
> - 如果需要跨 MySQL 版本进行升级，应先将低版本数据库转储或导出到自己环境中更高版本的 MySQL 中。 然后运行 `mysql_upgrade`再尝试迁移到 Azure Database for MySQL 中。

## <a name="performance-considerations"></a>性能注意事项
若要优化性能，请在转储大型数据库时留意这些注意事项：
-   转储数据库时，请使用 mysqldump 中的 `exclude-triggers` 选项。 从转储文件中排除触发器，避免在还原数据期间触发触发器命令。
-   使用 `single-transaction` 选项，将事务隔离模式设置为 REPEATABLE READ 并在转储数据之前将 START TRANSACTION SQL 语句发送到服务器。 在单个事务中转储多个表会在还原过程中占用一些额外的存储空间。 选项 `single-transaction` 和 `lock-tables` 互斥，因为 LOCK TABLES 导致所有挂起的事务均为隐式提交。 若要转储大型表，请结合使用选项 `single-transaction` 和 `quick`。
-   使用其中包含多个 VALUE 列表的 `extended-insert` 多行语法。 这可使转储文件较小并在重新加载文件时加快插入。
-  转储数据库时，使用 mysqldump 中的 `order-by-primary` 选项，以便按主键顺序编写数据脚本。
-   转储数据时，使用 mysqldump 中的 `disable-keys` 选项，以便在加载前，禁用外键约束。 禁用外键检查可提高性能。 启用约束并在加载后验证数据，确保引用完整性。
-   适当时使用已分区表。
-   并行加载数据。 避免太多将导致达到资源限制的并行度，并通过使用 Azure 门户中提供的指标监视资源。
-   转储数据库时，使用 mysqlpump 中的 `defer-table-indexes` 选项，以便在加载表数据后创建索引。
-   使用 mysqlump 中的 `skip-definer` 选项可以省略视图和存储过程的 CREATE 语句中的 definer 和 SQL SECURITY 子句。  重新加载转储文件时，它会创建使用默认 DEFINER 和 SQL SECURITY 值的对象。
-   将备份文件复制到 Azure blob/存储，并在其中执行还原，这应该比通过 Internet 执行还原要快得多。

## <a name="create-a-database-on-the-target-azure-database-for-mysql-server"></a>在 Azure Database for MySQL 目标服务器上创建数据库
在要迁移数据的 Azure Database for MySQL 目标服务器上创建一个空数据库。 使用 MySQL Workbench 或 mysql.exe 等工具创建数据库。 数据库名称可与包含转储数据的数据库名称相同，或可以创建一个不同名称的数据库。

若要获取连接，请在 Azure Database for MySQL 的“概述”中找到连接信息。

:::image type="content" source="./media/concepts-migrate-dump-restore/1_server-overview-name-login.png" alt-text="在 Azure 门户中找到连接信息":::

将连接信息添加到 MySQL Workbench。

:::image type="content" source="./media/concepts-migrate-dump-restore/2_setup-new-connection.png" alt-text="MySQL Workbench 连接字符串":::

## <a name="preparing-the-target-azure-database-for-mysql-server-for-fast-data-loads"></a>准备目标 Azure Database for MySQL 服务器以实现快速数据加载
若要准备目标 Azure Database for MySQL 服务器以实现快速数据加载，需要更改以下服务器参数和配置。
- max_allowed_packet – 设置为 1073741824（即 1 GB），以防止由于长行而引起的溢出问题。
- slow_query_log – 设置为“关闭”以关闭慢速查询日志。 这将消除数据加载过程中由慢速查询日志记录导致的开销。
- query_store_capture_mode - 设置为“无”以关闭查询存储。 这将消除由查询存储的采样活动导致的开销。
- innodb_buffer_pool_size – 在迁移期间从门户的定价层纵向扩展服务器到 32 vCore 内存优化 SKU，以增大 innodb_buffer_pool_size。 只能通过纵向扩展 Azure Database for MySQL 服务器的计算来增大 innodb_buffer_pool_size。
- innodb_io_capacity 和 innodb_io_capacity_max - 从 Azure 门户中的服务器参数更改为 9000，以提高 IO 利用率，从而优化迁移速度。
- innodb_write_io_threads 和 innodb_write_io_threads - 从 Azure 门户中的服务器参数更改为 4 以加快迁移速度。
- 纵向扩展存储层 – 随着存储层的增加，Azure Database for MySQL 服务器的 IOP 会逐渐增加。 为了更快地加载，可能需要增加存储层以增加预配的 IOP。 请记住，存储只能纵向扩展，而不能横向扩展。

迁移完成后，可以将服务器参数和计算层配置还原为以前的值。

## <a name="dump-and-restore-using-mysqldump-utility"></a>使用 mysqldump 实用工具进行转储和还原

### <a name="create-a-backup-file-from-the-command-line-using-mysqldump"></a>使用 mysqldump 从命令行创建备份文件
若要备份本地服务器或虚拟机中的现有 MySQL 数据库，请运行以下命令：
```bash
$ mysqldump --opt -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

需要提供的参数包括：
- [uname] 数据库用户名
- [pass] 数据库密码（请注意，-p 和密码之间没有空格）
- [dbname] 数据库名称
- [backupfile.sql] 数据库备份的文件名
- [--opt] mysqldump 选项

例如，若要将 MySQL 服务器上名为“testdb”的数据库（用户名为“testuser”且无密码）备份到文件 testdb_backup.sql，请使用以下命令。 该命令将 `testdb` 数据库备份到名为 `testdb_backup.sql` 的文件中，该文件包含重新创建数据库所需的所有 SQL 语句。 确保用户名“testuser”至少对转储表具有 SELECT 特权，对转储视图具 有 SHOW VIEW 权限，对转储触发器至少具有 TRIGGER 权限，如果未使用 --single transaction 选项，则至少具有 LOCK TABLES 权限。

```bash
GRANT SELECT, LOCK TABLES, SHOW VIEW ON *.* TO 'testuser'@'hostname' IDENTIFIED BY 'password';
```
现在，运行 mysqldump 以创建 `testdb` 数据库的备份

```bash
$ mysqldump -u root -p testdb > testdb_backup.sql
```
若要在数据库中选择特定的表进行备份，列出表名，用空格隔开。 例如，若仅从“testdb”备份 table1 和 table2 两个表，请遵循此示例：

```bash
$ mysqldump -u root -p testdb table1 table2 > testdb_tables_backup.sql
```
若要一次性备份多个数据库，请使用 --database 切换并列出数据库名（用空格隔开）。
```bash
$ mysqldump -u root -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql
```

### <a name="restore-your-mysql-database-using-command-line-or-mysql-workbench"></a>使用命令行或 MySQL Workbench 还原 MySQL 数据库
创建目标数据库后，可以使用 mysql 命令或 MySQL Workbench 将数据从转储文件还原到新创建的特定数据库。
```bash
mysql -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
在此示例中，将数据还原到在 Azure Database for MySQL 目标服务器上新创建的数据库中。

下面是一个示例，说明如何将此 mysql 用于单一服务器：

```bash
$ mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p testdb < testdb_backup.sql
```
下面是一个示例，说明如何将此 mysql 用于灵活服务器  ：

```bash
$ mysql -h mydemoserver.mysql.database.azure.com -u myadmin -p testdb < testdb_backup.sql
```
---

## <a name="dump-and-restore-using-phpmyadmin"></a>使用 PHPMyAdmin 转储和还原
按照以下步骤使用 PHPMyadmin 转储并还原数据库。

> [!NOTE]
> 对于单一服务器，用户名必须采用格式“username@servername”，但对于灵活服务器，只需使用“用户名”，如果灵活服务器使用“username@servername”，则连接将失败。

### <a name="export-with-phpmyadmin"></a>使用 PHPMyadmin 进行导出
若要导出，可以使用可能已安装在本地环境中的常用工具 phpMyAdmin。 使用 PHPMyAdmin 导出 MySQL 数据库：
1. 打开 phpMyAdmin。
2. 选择数据库。 单击左侧列表中的数据库名称。
3. 单击“导出”链接。 这将显示一个新页面，可查看数据库转储情况。
4. 在“导出”区域中，单击“全选”链接，选择数据库中的表。
5. 在 SQL 选项区域中，单击适当的选项。
6. 单击“另存为文件”选项及相应的压缩选项，然后单击“执行”按钮。 将出现一个对话框，提示在本地保存该文件。

### <a name="import-using-phpmyadmin"></a>使用 PHPMyAdmin 进行导入
导入数据库的方法与导出类似。 执行以下操作：
1. 打开 phpMyAdmin。
2. 在 phpMyAdmin 设置页中，单击“添加”可添加 Azure Database for MySQL 服务器。 提供连接详细信息和登录信息。
3. 创建适当命名的数据库，并在屏幕左侧选中该数据库。 若要重写现有数据库，请单击数据库名称，选中所有表名称旁边的复选框，再选择“删除”以删除现有表。
4. 单击“SQL”链接，显示可在其中键入 SQL 命令或上传 SQL 文件的页面。
5. 使用“浏览”按钮查找数据库文件。
6. 单击“执行”按钮，导出备份、执行 SQL 命令并重新创建数据库。

## <a name="known-issues"></a>已知问题
有关已知问题、提示和技巧，我们建议你查看[技术社区博客](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/tips-and-tricks-in-using-mysqldump-and-mysql-restore-to-azure/ba-p/916912)。

## <a name="next-steps"></a>后续步骤
- [将应用程序连接到 Azure Database for MySQL](./howto-connection-string.md)。
- 若要详细了解如何将数据库迁移到 Azure Database for MySQL，请参阅[数据库迁移指南](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide)。
- 如果希望迁移数据库大小超过 1 TB 的大型数据库，则可能需要考虑使用支持并行导出和导入的社区工具（如 mydumper/myloader）。 了解[如何迁移大型 MySQL 数据库](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/best-practices-for-migrating-large-databases-to-azure-database/ba-p/1362699)。
