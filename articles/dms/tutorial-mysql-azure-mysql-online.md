---
title: 教程：将 MySQL 联机迁移到 Azure Database for MySQL
titleSuffix: Azure Database Migration Service
description: 了解如何使用 Azure 数据库迁移服务执行从本地 MySQL 到 Azure Database for MySQL 的联机迁移。
services: dms
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 01/08/2020
ms.openlocfilehash: b7afc6c82f06e977caea90ac3e8d2355d56c920d
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2021
ms.locfileid: "108129758"
---
# <a name="tutorial-migrate-mysql-to-azure-database-for-mysql-online-using-dms"></a>教程：使用 DMS 以联机方式将 MySQL 迁移到 Azure Database for MySQL

可以使用 Azure 数据库迁移服务在尽量缩短停机时间的情况下，将数据库从本地 MySQL 实例迁移到 [Azure Database for MySQL](../mysql/index.yml)。 换句话说，可以在尽量减少应用程序故障时间的情况下进行迁移。 本教程介绍如何在 Azure 数据库迁移服务中使用联机迁移活动将 **Employees** 示例数据库从 MySQL 5.7 的本地实例迁移到 Azure Database for MySQL。

> [!IMPORTANT]
> “MySQL 到 Azure Database for MySQL”联机迁移方案将于 2021 年 6 月 1 日被并行化的高性能脱机迁移方案取代。 对于联机迁移，可以将此新产品/服务与[数据传入复制](../mysql/concepts-data-in-replication.md)配合使用。 也可将 [MyDumper/MyLoader](https://centminmod.com/mydumper.html) 之类的开源工具与用于联机迁移的数据传入复制配合使用。 

本教程介绍如何执行下列操作：
> [!div class="checklist"]
>
> * 使用 mysqldump 实用程序迁移示例架构。
> * 创建 Azure 数据库迁移服务的实例。
> * 使用 Azure 数据库迁移服务创建迁移项目。
> * 运行迁移。
> * 监视迁移。

> [!NOTE]
> 使用 Azure 数据库迁移服务执行联机迁移需要基于“高级”定价层创建实例。

> [!IMPORTANT]
> 为获得最佳迁移体验，Microsoft 建议在目标数据库所在的 Azure 区域中创建 Azure 数据库迁移服务的实例。 跨区域或地理位置移动数据可能会减慢迁移过程并引入错误。

> [!NOTE]
> 无偏差通信
>
> Microsoft 支持多样化的包容性环境。 本文包含对单词 slave 的引用。 Microsoft 的[无偏差通信风格指南](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md)将其视为排他性单词。 本文使用该单词旨在保持一致性，因为目前软件中使用的是该单词。 如果软件更新后删除了该单词，则本文也将更新以保持一致。
>


## <a name="prerequisites"></a>先决条件

要完成本教程，需要：

* 下载并安装 [MySQL 社区版](https://dev.mysql.com/downloads/mysql/) 5.6 或 5.7。 本地 MySQL 版本必须与 Azure Database for MySQL 版本相符。 例如，MySQL 5.6 只能迁移到 Azure Database for MySQL 5.6，不能升级到 5.7。 不支持向/从 MySQL 8.0 进行迁移。
* [在 Azure Database for MySQL 中创建一个实例](../mysql/quickstart-create-mysql-server-database-using-azure-portal.md)。 有关如何使用 Workbench 应用程序连接和创建数据库的详细信息，请参阅[使用 MySQL Workbench 进行连接并查询数据](../mysql/connect-workbench.md)一文。  
* 使用 Azure 资源管理器部署模型创建适合 Azure 数据库迁移服务的 Microsoft Azure 虚拟网络，它将使用 [ExpressRoute](../expressroute/expressroute-introduction.md) 或 [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) 为本地源服务器提供站点到站点连接。 有关创建虚拟网络的详细信息，请参阅[虚拟网络文档](../virtual-network/index.yml)，尤其是提供了分步详细信息的快速入门文章。

    > [!NOTE]
    > 在虚拟网络 (VNet) 设置期间，如果将 ExpressRoute 与 Microsoft 的网络对等互连一起使用，则请将以下服务[终结点](../virtual-network/virtual-network-service-endpoints-overview.md)添加到要在其中预配该服务的子网：
    >
    > * 目标数据库终结点（例如，SQL 终结点、Cosmos DB 终结点等）
    > * 存储终结点
    > * 服务总线终结点
    >
    > Azure 数据库迁移服务缺少 Internet 连接，因此必须提供此配置。

* 请确保虚拟网络的网络安全组规则不阻止 ServiceBus、存储服务和 AzureMonitor 的 ServiceTag 出站端口 443。 有关虚拟网络 NSG 流量筛选的更多详细信息，请参阅[使用网络安全组筛选网络流量](../virtual-network/virtual-network-vnet-plan-design-arm.md)一文。
* 配置[针对数据库引擎访问的 Windows 防火墙](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)。
* 打开 Windows 防火墙，使 Azure 数据库迁移服务能够访问源 MySQL 服务器（默认情况下为 TCP 端口 3306）。
* 在源数据库的前面使用了防火墙设备时，可能需要添加防火墙规则以允许 Azure 数据库迁移服务访问要迁移的源数据库。
* 为 Azure Database for MySQL 创建服务器级[防火墙规则](../azure-sql/database/firewall-configure.md)，以允许 Azure 数据库迁移服务访问目标数据库。 提供用于 Azure 数据库迁移服务的虚拟网络子网范围。
* 源 MySQL 必须使用支持的 MySQL 社区版。 若要确定 MySQL 实例的版本，请在 MySQL 实用程序或 MySQL Workbench 中运行以下命令：

    ```
    SELECT @@version;
    ```

* Azure Database for MySQL 仅支持 InnoDB 表。 若要将 MyISAM 表转换为 InnoDB，请参阅 [Converting Tables from MyISAM to InnoDB](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html)（将表从 MyISAM 转换为 InnoDB）一文

* 通过以下配置在源数据库的 my.ini (Windows) 或 my.cnf (Unix) 文件中启用二进制日志记录。

  * **server_id** = 1 或更高版本（仅适用于 MySQL 5.6）
  * **log-bin** =\<path>（仅适用于 MySQL 5.6）。例如：log-bin = E:\MySQL_logs\BinLog
  * **binlog_format** = row
  * **Expire_logs_days** = 5（建议不要使用零；仅适用于 MySQL 5.6）
  * **Binlog_row_image** = full（仅适用于 MySQL 5.6）
  * **log_slave_updates** = 1

* 用户必须具有 ReplicationAdmin 角色的以下权限：

  * **复制客户端** - 仅仅是“更改处理”任务所需的。 换句话说，“仅完整加载”任务不需要此权限。
  * **复制副本** - 仅仅是“更改处理”任务所需的。 换句话说，“仅完整加载”任务不需要此权限。
  * **超级** - 仅在 MySQL 5.6.6 之前的版本中需要。

## <a name="migrate-the-sample-schema"></a>迁移示例架构

若要完成所有数据库对象（例如表架构、索引和存储过程），需从源数据库提取架构并将其应用到此数据库。 若要提取架构，可以将 mysqldump 与 `--no-data` 参数配合使用。

假设本地系统中有 MySQL Employees 示例数据库，则使用 mysqldump 进行架构迁移时所需的命令如下  ：

```
mysqldump -h [servername] -u [username] -p[password] --databases [db name] --no-data > [schema file path]
```

例如：

```
mysqldump -h 10.10.123.123 -u root -p --databases employees --no-data > d:\employees.sql
```

若要将架构导入到 Azure Database for MySQL 目标，请运行以下命令：

```
mysql.exe -h [servername] -u [username] -p[password] [database]< [schema file path]
 ```

例如：

```
mysql.exe -h shausample.mysql.database.azure.com -u dms@shausample -p employees < d:\employees.sql
 ```

如果架构中有外键，则迁移的初始加载和连续同步会失败。  请在 MySQL Workbench 中执行以下脚本，以便提取 DROP FOREIGN KEY 脚本和 ADD FOREIGN KEY 脚本。

```sql
SET group_concat_max_len = 8192;
    SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery
    FROM
    (SELECT
    KCU.REFERENCED_TABLE_SCHEMA as SchemaName,
    KCU.TABLE_NAME,
    KCU.COLUMN_NAME,
    CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery,
    CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery
    FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC
    WHERE
      KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME
      AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA
  AND KCU.REFERENCED_TABLE_SCHEMA = 'SchemaName') Queries
  GROUP BY SchemaName;
 ```

运行查询结果中的 DROP FOREIGN KEY（第二列），以便删除外键。

> [!NOTE]
> Azure DMS 不支持 CASCADE 引用操作，这有助于在父表中删除或更新行时，自动删除或更新子表中的匹配行。 有关详细信息，请参见 MySQL 文档中的[外键约束](https://dev.mysql.com/doc/refman/8.0/en/create-table-foreign-keys.html)一文中的“引用操作”部分。
> Azure DMS 要求在初始数据加载过程中在目标数据库服务器中删除外键约束，并且不能使用引用操作。 如果你的工作负载依赖于通过此引用操作更新相关子表，我们建议你改为执行[转储并还原](../mysql/concepts-migrate-dump-restore.md)。 


> [!IMPORTANT]
> 如果使用备份导入数据，请在执行 mysqldump 时手动删除或使用 --skip-definer 命令删除 CREATE DEFINER 命令。 DEFINER 需要使用超级特权来创建，并在 Azure Database for MySQL 中受到限制。

如果数据库中有触发器，它将在从源进行完整数据迁移之前在目标中强制实施数据完整性。 建议在迁移期间禁用目标中所有表的触发器，然后在迁移完成后再启用这些触发器。

在 MySQL Workbench 中对目标数据库执行以下脚本，以提取“删除触发器”脚本和“添加触发器”脚本。

```sql
SELECT
    SchemaName,
    GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery,
    Concat('DELIMITER $$ \n\n', GROUP_CONCAT(AddQuery SEPARATOR '$$\n'), '$$\n\nDELIMITER ;') as AddQuery
FROM
(
SELECT 
    TRIGGER_SCHEMA as SchemaName,
    Concat('DROP TRIGGER `', TRIGGER_NAME, "`") as DropQuery,
    Concat('CREATE TRIGGER `', TRIGGER_NAME, '` ', ACTION_TIMING, ' ', EVENT_MANIPULATION, 
            '\nON `', EVENT_OBJECT_TABLE, '`\n' , 'FOR EACH ', ACTION_ORIENTATION, ' ',
            ACTION_STATEMENT) as AddQuery
FROM  
    INFORMATION_SCHEMA.TRIGGERS
ORDER BY EVENT_OBJECT_SCHEMA, EVENT_OBJECT_TABLE, ACTION_TIMING, EVENT_MANIPULATION, ACTION_ORDER ASC
) AS Queries
GROUP BY SchemaName
```

在结果中运行生成的“删除触发器”查询（DropQuery 列），以删除目标数据库中的触发器。 可保存“添加触发器”查询，以供数据迁移完成后使用。

## <a name="register-the-microsoftdatamigration-resource-provider"></a>注册 Microsoft.DataMigration 资源提供程序

1. 登录到 Azure 门户，选择“所有服务”，然后选择“订阅”。

   ![显示门户订阅](media/tutorial-mysql-to-azure-mysql-online/01-portal-select-subscriptions.png)

2. 选择要在其中创建 Azure 数据库迁移服务实例的订阅，再选择“资源提供程序”。

    ![显示资源提供程序](media/tutorial-mysql-to-azure-mysql-online/02-01-portal-select-resource-provider.png)

3. 搜索迁移服务，再选择“Microsoft.DataMigration”右侧的“注册” 。

    ![注册资源提供程序](media/tutorial-mysql-to-azure-mysql-online/02-02-portal-register-resource-provider.png)

## <a name="create-a-database-migration-service-instance"></a>创建数据库迁移服务实例

1. 在 Azure 门户中，选择 **+ 创建资源**，搜索 Azure 数据库迁移服务，然后从下拉列表选择 **Azure 数据库迁移服务**。

    ![Azure 市场](media/tutorial-mysql-to-azure-mysql-online/03-dms-portal-marketplace.png)

2. 在“Azure 数据库迁移服务”屏幕上，选择“创建”   。

    ![创建 Azure 数据库迁移服务实例](media/tutorial-mysql-to-azure-mysql-online/04-dms-portal-marketplace-create.png)
  
3. 在“创建迁移服务”屏幕上，为服务、订阅以及新的或现有资源组指定名称。

4. 选择定价层并移动到“网络”屏幕。 标准定价层和高级定价层均提供脱机迁移功能。

    有关成本和定价层的详细信息，请参阅[价格页](https://aka.ms/dms-pricing)。

    ![配置 Azure 数据库迁移服务基础设置](media/tutorial-mysql-to-azure-mysql-online/05-dms-portal-create-basic.png)

5. 从列表中选择一个现有的虚拟网络，或提供要创建的新虚拟网络的名称。 转到“查看 + 创建”屏幕。 可选择使用“标记”屏幕向服务添加标记。

    虚拟网络为 Azure 数据库迁移服务提供了对源 SQL Server 和目标 Azure SQL 数据库实例的访问权限。

    ![配置 Azure 数据库迁移服务网络设置](media/tutorial-mysql-to-azure-mysql-online/06-dms-portal-create-networking.png)

    有关如何在 Azure 门户中创建虚拟网络的详细信息，请参阅[使用 Azure 门户创建虚拟网络](../virtual-network/quick-create-portal.md)一文。

6. 查看配置，然后选择“创建”以创建服务。
    
    ![Azure 数据库迁移服务 - 创建](media/tutorial-mysql-to-azure-mysql-online/07-dms-portal-create-submit.png)

## <a name="create-a-migration-project"></a>创建迁移项目

创建服务后，在 Azure 门户中找到并打开它，然后创建一个新的迁移项目。  

1. 在 Azure 门户中，选择“所有服务”，搜索 Azure 数据库迁移服务，然后选择“Azure 数据库迁移服务”。

    ![查找 Azure 数据库迁移服务的所有实例](media/tutorial-mysql-to-azure-mysql-online/08-01-dms-portal-search-service.png)

2. 从搜索结果中选择你的迁移服务实例，然后选择“+ 新建迁移项目”。
    
    ![创建新迁移项目](media/tutorial-mysql-to-azure-mysql-online/08-02-dms-portal-new-project.png)

3. 在“新建迁移项目”屏幕上指定项目名称，在“源服务器类型”选择框中选择“MySQL”，在“目标服务器类型”选择框中选择“Azure Database For MySQL”，然后在“迁移活动类型”选择框中选择“联机数据迁移”。       选择“创建并运行活动”。

    ![创建数据库迁移服务项目](media/tutorial-mysql-to-azure-mysql-online/09-dms-portal-project-mysql-create.png)

    > [!NOTE]
    > 也可以现在就选择“仅创建项目”来创建迁移项目，在以后再执行迁移。

## <a name="configure-migration-project"></a>配置迁移项目

1. 在“选择源”屏幕上，指定源 MySQL 实例的连接详细信息，然后选择“下一步: 选择目标>>” 

    ![“添加源详细信息”屏幕](media/tutorial-mysql-to-azure-mysql-online/10-dms-portal-project-mysql-source.png)

2. 在“选择目标”屏幕上，指定目标 zure Database for MySQL 实例的连接详细信息，然后选择“下一步: 选择数据库>>” 

    ![“添加目标详细信息”屏幕](media/tutorial-mysql-to-azure-mysql-online/11-dms-portal-project-mysql-target.png)

3. 在“选择数据库”屏幕上，映射源数据库和目标数据库以进行迁移，然后选择“下一步: 配置迁移设置>>” 。 可选择“使源服务器只读”选项，使源变为只读，但要注意，这是一项服务器级别设置。 如果选择了此选项，它会将整个服务器设置为只读，而不只是选定的数据库。
    
    如果目标数据库包含的数据库名称与源数据库的相同，则 Azure 数据库迁移服务默认会选择目标数据库。
    ![“选择数据库详细信息”屏幕](media/tutorial-mysql-to-azure-mysql-online/12-dms-portal-project-mysql-select-db.png)
    
    > [!NOTE] 
   > 虽然在此步骤中可以选择多个数据库，但每个 Azure 数据库迁移服务实例最多支持 4 个数据库进行并发迁移。 此外，每个区域的每个订阅的 Azure 数据库迁移服务实例数限制为 10 个。 例如，如果要迁移 80 个数据库，则可以将其中的 40 个数据库同时迁移到同一区域，但前提是你已经创建了 10 个 Azure 数据库迁移服务实例。

4. 在“配置迁移设置”屏幕上，选择要作为迁移的一部分的表，然后选择“下一步: 摘要>>” 。 如果目标表有任何数据，则默认不会选择这些表，但你可以显式选择它们，它们会在开始迁移之前被截断。

    ![“选择表”屏幕](media/tutorial-mysql-to-azure-mysql-online/13-dms-portal-project-mysql-select-tbl.png)

5. 在“摘要”屏幕上的“活动名称”文本框中，指定迁移活动的名称，然后查看摘要，确保源和目标详细信息与此前指定的信息相符 。

    ![迁移项目摘要](media/tutorial-mysql-to-azure-mysql-online/14-dms-portal-project-mysql-activity-summary.png)

6. 选择“开始迁移”。 迁移活动窗口随即出现，活动的“状态”为“正在初始化”   。 表迁移开始时，“状态”将更改为“正在运行” 。

## <a name="monitor-the-migration"></a>监视迁移

1. 在迁移活动屏幕上选择“刷新”，以便更新显示，直到迁移的“状态”显示为“完成”。

     ![活动状态 - 完成](media/tutorial-mysql-to-azure-mysql-online/15-dms-activity-completed.png)

2. 在“数据库名称”下选择特定数据库即可转到“完整数据加载”和“增量数据同步”操作的迁移状态。

    完整数据加载会显示初始加载迁移状态，而增量数据同步则会显示变更数据捕获 (CDC) 状态。

     ![活动状态 - 完整加载已完成](media/tutorial-mysql-to-azure-mysql-online/16-dms-activity-full-load-completed.png)

     ![活动状态 - 增量数据同步](media/tutorial-mysql-to-azure-mysql-online/17-dms-activity-incremental-data-sync.png)

## <a name="perform-migration-cutover"></a>执行迁移直接转换

完成初始的完整加载以后，数据库会被标记为“直接转换可供执行”。

1. 如果准备完成数据库迁移，请选择“启动直接转换”。

    ![启动直接转换](media/tutorial-mysql-to-azure-mysql-online/18-dms-start-cutover.png)

2. 确保停止传入源数据库的所有事务；等到“挂起的更改”计数器显示 **0**。
3. 选择“确认”，然后选择“应用”。
4. 当数据库迁移状态显示“已完成”后，请将应用程序连接到新的目标 Azure SQL 数据库。

## <a name="next-steps"></a>后续步骤

* 若要了解联机迁移到 Azure Database for MySQL 时的已知问题和限制，请参阅 [Azure Database for MySQL 联机迁移的已知问题和解决方法](known-issues-azure-mysql-online.md)一文。
* 若要了解 Azure 数据库迁移服务，请参阅[什么是 Azure 数据库迁移服务？](./dms-overview.md)一文。
* 若要了解 Azure Database for MySQL，请参阅[什么是 Azure Database for MySQL？](../mysql/overview.md)一文。