---
title: 使用 MySQL Workbench 将 Amazon RDS for MySQL 迁移到 Azure Database for MySQL
description: 本文介绍如何使用 MySQL Workbench 迁移向导将 Amazon RDS for MySQL 迁移到 Azure Database for MySQL。
author: HJToland3
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 05/21/2021
ms.openlocfilehash: c744517dfab088fa4edb104b2c5aebda2818ab8f
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "122653076"
---
# <a name="migrate-amazon-rds-for-mysql-to-azure-database-for-mysql-using-mysql-workbench"></a>使用 MySQL Workbench 将 Amazon RDS for MySQL 迁移到 Azure Database for MySQL

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

可以使用 MySQL Workbench 导出/导入、Azure 数据库迁移服务 (DMS) 和 MySQL 转储和还原等各种实用工具，将 Amazon RDS for MySQL 迁移到 Azure Database for MySQL。 但是，使用 MySQL Workbench 迁移向导可以方便地将 Amazon RDS for MySQL 数据库移到 Azure Database for MySQL。

在迁移向导中可以方便地选择要迁移的架构和对象。 它还可让你查看服务器日志，以实时识别错误和瓶颈。 因此，可以在迁移过程中检测到错误时编辑和修改表或数据库结构与对象，然后继续迁移，而无需从头重新开始。

> [!NOTE]
> 还可以使用迁移向导来迁移 Microsoft SQL Server、Oracle、PostgreSQL、MariaDB 等其他源，但此操作超出了本文的范畴。

## <a name="prerequisites"></a>先决条件

在开始迁移过程之前，最好是确保正确配置并设置多个参数和功能，如下所述。

- 确保源数据库和目标数据库的字符集相同。
- 根据要导入或迁移的数据或工作负载数量，将等待超时设置为合理的时间。
- 根据要导入或迁移的数据库大小，将 `max_allowed_packet parameter` 设置为合理的数量。
- 验证所有表是否都使用 InnoDB，因为 Azure Database for MySQL Server 仅支持 InnoDB 存储引擎。
- 删除、替换或修改包含 root 用户或超级用户定义者的所有触发器、存储过程和其他函数（Azure Database for MySQL 不支持超级用户特权）。 若要将定义者替换为正在运行导入过程的管理员用户的姓名，请运行以下命令：

  ```
  DELIMITER; ;/*!50003 CREATE*/ /*!50017 DEFINER=`root`@`127.0.0.1`*/ /*!50003
  DELIMITER;
  /* Modified to */
  DELIMITER;
  /*!50003 CREATE*//*!50017 DEFINER=`AdminUserName`@`ServerName`*/ /*!50003
  DELIMITER;

  ```

- 如果用户定义的函数 (UDF) 在数据库服务器上运行，则你需要删除对 mysql 数据库的特权。 若要确定是否有任何 UDF 在服务器上运行，请使用以下查询：

  ```
  SELECT * FROM mysql.func;
  ```

  如果发现 UDF 正在运行，可使用以下查询来删除 UDF：

  ```
  DROP FUNCTION your_UDFunction;
  ```

- 确保运行该工具的服务器以及最终的导出位置可以提供足够的磁盘空间和计算能力（vCore、CPU 和内存）用于执行导出操作，尤其是导出极大型数据库时。
- 如果工作负载位于防火墙或其他网络安全层的后面，请在本地实例或 AWS 实例与 Azure Database for MySQL 之间创建路径。

## <a name="begin-the-migration-process"></a>开始迁移过程

1. 若要开始迁移过程，请登录到 MySQL Workbench，然后选择主页图标。
2. 如以下屏幕截图所示，在左侧导航栏中选择“迁移向导”图标。

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/begin-the-migration.png" alt-text="MySQL Workbench 开始屏幕":::

   此时将显示迁移向导的“概述”页，如下所示。

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/migration-wizard-welcome.png" alt-text="MySQL Workbench 迁移向导欢迎页":::

3. 选择“打开 ODBC 管理器”，确定是否安装了适用于 MySQL Server 的 ODBC 驱动程序。

   在本例中，在“驱动程序”选项卡上可以看到，已安装了两个 MySQL Server ODBC 驱动程序。

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/obdc-administrator-page.png" alt-text="“ODBC 数据源管理器”页":::

   如果未安装某个 MySQL ODBC 驱动程序，请使用用于安装 MySQL Workbench 的 MySQL 安装程序来安装该驱动程序。 有关 MySQL ODBC 驱动程序安装的详细信息，请参阅以下资源：

   - [MySQL :: MySQL 连接器/ODBC 开发人员指南 :: 4.1 在 Windows 上安装连接器/ODBC](https://dev.mysql.com/doc/connector-odbc/en/connector-odbc-installation-binary-windows.html)
   - [适用于 MySQL 的 ODBC 驱动程序：如何安装和设置连接（分步指南）– {coding}Sight (codingsight.com)](https://codingsight.com/install-and-configure-odbc-drivers-for-mysql/)

4. 关闭“ODBC 数据源管理器”对话框，然后继续执行迁移过程。

## <a name="configure-source-database-server-connection-parameters"></a>配置源数据库服务器连接参数

1. 在“概述”页上，选择“开始迁移” 。

   此时将显示“选择源”页。 使用此页提供有关要从中迁移的 RDBMS 以及连接参数的信息。

2. 在“数据库系统”字段中，选择“MySQL” 。
3. 在“存储的连接”字段中，选择为该 RDBMS 保存的连接设置之一。

   可以通过勾选该页底部的复选框并提供偏好的名称来保存连接。

4. 在“连接方法”字段中，选择“标准 TCP/IP” 。
5. 在“主机名”字段中，指定源数据库服务器的名称。
6. 在“端口”字段中指定 3306，然后输入用于连接到服务器的用户名和密码 。
7. 在“数据库”字段中，输入要迁移的数据库的名称（如果知道的话）；否则请将此字段留空。
8. 选择“测试连接”，检查与 MySQL 服务器实例的连接。

   如果输入的参数正确，则会显示一条消息，指出已成功尝试连接。

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/source-connection-parameters.png" alt-text="“源数据库连接参数”页":::

9. 选择“**下一页**”。

## <a name="configure-target-database-server-connection-parameters"></a>配置目标数据库服务器连接参数

1. 在“选择目标”页上，使用与设置源服务器连接类似的过程设置参数，以连接到目标 MySQL 服务器实例。
2. 若要验证是否可成功连接，请选择“测试连接”。

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/target-connection-parameters.png" alt-text="“目标数据库连接参数”页":::

3. 选择“**下一页**”。

## <a name="select-the-schemas-to-migrate"></a>选择要迁移的架构

迁移向导将与 MySQL Server 实例通信，并从源服务器提取架构列表。

1. 选择“显示日志”以查看此操作。

   以下屏幕截图显示了如何从源数据库服务器检索架构。

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/retrieve-schemas.png" alt-text="“提取架构列表”页":::

2. 选择“下一步”，验证是否成功提取了所有架构。

   以下屏幕截图显示了提取的架构列表。

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/schemas-selection.png" alt-text="架构选项页":::

   只能迁移此列表中显示的架构。

3. 选择要迁移的架构，然后选择“下一步”。

## <a name="object-migration"></a>对象迁移

接下来，指定要迁移的对象。

1. 选择“显示选定内容”，然后在“可用对象”下，选择并添加要迁移的对象 。

   添加对象后，它们将显示在“要迁移的对象”下，如以下屏幕截图所示。

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/source-objects.png" alt-text="源对象选项页":::

   在此方案中，我们选择了所有表对象。

2. 选择“**下一页**”。

## <a name="edit-data"></a>编辑数据

在本部分，你将通过相应的选项编辑要迁移的对象。

1. 在“手动编辑”页上，请注意右上角的“查看”下拉菜单 。

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/manual-editing.png" alt-text="“手动编辑”选项页":::

   “查看”下拉框包括三个项：

   - 所有对象 - 显示所有对象。 使用此选项可以先手动编辑生成的 SQL，然后将其应用到目标数据库服务器。 为此，请选择该对象，然后选择“显示代码和消息”。 可以查看（和编辑）生成的、与选定对象对应的 MySQL 代码。
   - 迁移问题 – 显示迁移过程中发生的任何问题，在此处可以查看和验证这些问题。
   - 列映射 – 显示列映射信息。 可以使用此视图来编辑目标对象的名称并更改其列。

2. 选择“**下一页**”。

## <a name="create-the-target-database"></a>创建目标数据库

1. 选中“在目标 RDBMS 中创建架构”复选框。

   还可以选择保留现有的架构，以避免对其进行修改或更新。

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/create-target-database.png" alt-text="“目标创建选项”页":::

   在本文中，我们已选择在目标 RDBMS 中创建架构，但你也可以选中“创建 SQL 脚本文件”复选框，以将文件保存在本地计算机上或实现其他目的。

2. 选择“**下一页**”。

## <a name="run-the-mysql-script-to-create-the-database-objects"></a>运行 MySQL 脚本以创建数据库对象

由于我们已选择在目标 RDBMS 中创建架构，因此将在目标 MySQL Server 中执行迁移的 SQL 脚本。 可以查看此操作的进度，如以下屏幕截图所示：

:::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/create-schemas.png" alt-text="“创建架构”页":::

1. 创建完架构及其对象后，选择“下一步”。

   在“创建目标结果”页上，显示了创建的对象列表，以及在创建这些对象时遇到的任何错误的通知，如以下屏幕截图所示。

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/create-target-results.png" alt-text="“创建目标结果”页":::

2. 查看此页上的详细信息，以验证所有操作是否已按预期方式完成。

   在本文中，我们未遇到任何错误。 如果不需要解决任何错误消息，可以编辑迁移脚本。

3. 在“对象”框中，选择要编辑的对象。
4. 在“选定对象的 SQL CREATE 脚本”下修改 SQL 脚本，然后选择“应用”以保存更改 。
5. 选择“重新创建对象”以运行更改后的脚本。

   如果该脚本失败，可能需要编辑生成的脚本。 然后，可以手动修复 SQL 脚本并再次运行所有操作。 在本文中，我们不会更改任何内容，因此将脚本保留原样。

6. 选择“**下一页**”。

## <a name="transfer-data"></a>传输数据

此步骤将数据从源 MySQL Server 数据库实例移入新建的目标 MySQL 数据库实例。 请使用“数据传输设置”页配置此过程。

:::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/data-transfer-setup.png" alt-text="“数据传输设置”页":::

此页提供用于设置数据传输的选项。 对于本文，我们将接受默认值。

1. 若要实际开始传输数据，请选择“下一步”。

   将显示数据传输过程的进度，如以下屏幕截图所示。

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/bulk-data-transfer.png" alt-text="“批量数据传输”页":::

   > [!NOTE]
   > 数据传输过程的持续时间与要迁移的数据库大小直接相关。 源数据库越大，该过程所需的时间就越长；对于较大的数据库，该过程可能需要几个小时。

2. 传输完成后，选择“下一步”。

   此时将显示“迁移报告”页，其中的报告汇总了整个过程，如以下屏幕截图所示：

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/migration-report.png" alt-text="“迁移进度报告”页":::

3. 选择“完成”关闭迁移向导。

   迁移现已成功完成。

## <a name="verify-consistency-of-the-migrated-schemas-and-tables"></a>验证迁移的架构和表的一致性

1. 接下来，登录到 MySQL 目标数据库实例，以验证迁移的架构和表是否与 MySQL 源数据库相一致。

   在本例中可以看到，Amazon RDS for MySQL 的 MyjolieDB 数据库中的所有架构（sakila、moda、items、customer、clothes、world 和 world_x）已成功迁移到 Azure Database for MySQL 的 azmysql 实例 。

2. 若要验证表和行计数，请对这两个实例运行以下查询：

      `SELECT COUNT (*) FROM sakila.actor;`

   在以下屏幕截图中可以看到，Amazon RDS MySQL 的行计数为 200，这与 Azure Database for MySQL 实例相匹配。

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/table-row-size-source.png" alt-text="表和行大小 - 源数据库":::

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/table-row-size-target.png" alt-text="表和行大小 - 目标数据库":::

   虽然可以针对每一个架构和表运行上述查询，但如果要处理数十万甚至数百万个表，则会相当繁琐。 可以改用以下查询来验证架构（数据库）和表大小。

3. 若要检查数据库大小，请运行以下查询：

    ```
   SELECT table_schema AS "Database", 
   ROUND(SUM(data_length + index_length) / 1024 / 1024, 2) AS "Size (MB)" 
   FROM information_schema.TABLES 
   GROUP BY table_schema;
   ```

4. 若要检查表大小，请运行以下查询：

   ```
   SELECT table_name AS "Table",
   ROUND(((data_length + index_length) / 1024 / 1024), 2) AS "Size (MB)"
   FROM information_schema.TABLES
   WHERE table_schema = "database_name"
   ORDER BY (data_length + index_length) DESC;
   ```

   在以下屏幕截图中可以看到，源 Amazon RDS MySQL 实例中的架构（数据库）大小与目标 Azure Database for MySQL 实例中的大小相同。

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/database-size-source.png" alt-text="数据库大小 - 源数据库":::

    :::image type="content" source="./media/how-to-migrate-rds-mysql-workbench/database-size-target.png" alt-text="数据库大小 - 目标数据库":::

   由于这两个实例中的架构（数据库）大小相同，因此实际上不需要检查各个表的大小。 在任何情况下，始终都可以根据需要使用上述查询来检查表大小。

   现已确认成功完成了迁移。

## <a name="next-steps"></a>后续步骤

- 若要详细了解如何将数据库迁移到 Azure Database for MySQL，请参阅[数据库迁移指南](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide)。
- 观看视频[轻松将 MySQL/PostgreSQL 应用迁移到 Azure 托管服务](https://medius.studios.ms/Embed/Video/THR2201?sid=THR2201)，其中包含一个演示，演示如何将 MySQL 应用迁移到 Azure Database for MySQL。
