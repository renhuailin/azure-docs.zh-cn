---
title: 教程：使用 DMS 将 MySQL 脱机迁移到 Azure Database for MySQL
titleSuffix: Azure Database Migration Service
description: 了解如何使用 Azure 数据库迁移服务执行从本地 MySQL 到 Azure Database for MySQL 的脱机迁移。
services: dms
author: sumitgaurin
ms.author: sgaur
manager: arthiaga
ms.reviewer: arthiaga
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 04/11/2021
ms.openlocfilehash: cafe928ad8baed2a597bfef9bbedca8ca72e1d76
ms.sourcegitcommit: 47491ce44b91e546b608de58e6fa5bbd67315119
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2021
ms.locfileid: "122201969"
---
# <a name="tutorial-migrate-mysql-to-azure-database-for-mysql-offline-using-dms"></a>教程：使用 DMS 将 MySQL 脱机迁移到 Azure Database for MySQL

可以使用 Azure 数据库迁移服务执行从本地 MySQL 实例到具有高速数据迁移功能的 [Azure Database for MySQL](../mysql/index.yml) 的一次性完整数据库迁移。 本教程介绍如何在 Azure 数据库迁移服务中使用脱机迁移活动将示例数据库从 MySQL 5.7 的本地实例迁移到 Azure Database for MySQL (v5.7)。 虽然本文假设源为 MySQL 数据库实例，目标为 Azure Database for MySQL，但只需更改源服务器名称和凭据，本文即可用于在 Azure Database for MySQL 之间迁移。 此外，还支持从低版本的 MySQL 服务器（5.6 和更高版本）迁移到高版本。

> [!IMPORTANT]
> 对于联机迁移，可以将 [MyDumper/MyLoader](https://centminmod.com/mydumper.html) 之类的开源工具与[数据传入复制](../mysql/concepts-data-in-replication.md)配合使用。 

> [!NOTE]
> 有关此迁移体验的基于 PowerShell 的可编写脚本版本，请参阅[以可编写脚本的方式脱机迁移到 Azure Database for MySQL](./migrate-mysql-to-azure-mysql-powershell.md)。

> [!NOTE]
> 另外，支持 Amazon Relational Database Service (RDS) for MySQL 和 Amazon Aurora（基于 MySQL）作为源进行迁移。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
>
> * 使用 mysqldump 实用程序迁移数据库架构。
> * 创建 Azure 数据库迁移服务的实例。
> * 使用 Azure 数据库迁移服务创建迁移项目。
> * 运行迁移。
> * 监视迁移。

## <a name="prerequisites"></a>先决条件

要完成本教程，需要：

* 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free)。
* 具有 5.7 版本的本地 MySQL 数据库。 如果没有，请下载并安装 [MySQL 社区版](https://dev.mysql.com/downloads/mysql/) 5.7。
* MySQL 脱机迁移仅在高级 DMS SKU 上受支持。
* [在 Azure Database for MySQL 中创建一个实例](../mysql/quickstart-create-mysql-server-database-using-azure-portal.md)。 有关如何使用 Workbench 应用程序连接和创建数据库的详细信息，请参阅[使用 MySQL Workbench 进行连接并查询数据](../mysql/connect-workbench.md)一文。 Azure Database for MySQL 的版本应不低于本地 MySQL 版本。 例如，MySQL 5.7 可以迁移到 Azure Database for MySQL 5.7，或升级到 8。 
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
* 打开 Windows 防火墙，使 Azure 数据库迁移服务的虚拟网络发出的连接能够访问源 MySQL 服务器（默认情况下为 TCP 端口 3306）。
* 在源数据库的前面使用了防火墙设备时，可能需要添加防火墙规则以允许 Azure 数据库迁移服务的虚拟网络发出的连接访问要迁移的源数据库。
* 为目标 Azure Database for MySQL 创建服务器级别的[防火墙规则](../azure-sql/database/firewall-configure.md)或[配置 VNET 服务器终结点](../mysql/howto-manage-vnet-using-portal.md)，以允许 Azure 数据迁移服务的虚拟机网络访问目标数据库。
* 源 MySQL 必须使用支持的 MySQL 社区版。 若要确定 MySQL 实例的版本，请在 MySQL 实用程序或 MySQL Workbench 中运行以下命令：

    ```
    SELECT @@version;
    ```

* Azure Database for MySQL 仅支持 InnoDB 表。 若要将 MyISAM 表转换为 InnoDB，请参阅 [Converting Tables from MyISAM to InnoDB](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html)（将表从 MyISAM 转换为 InnoDB）一文
* 用户必须具有读取源数据库上数据的权限。

## <a name="sizing-the-target-azure-database-for-mysql-instance"></a>调整目标 Azure Database for MySQL 实例的大小

若要准备目标 Azure Database for MySQL 服务器以便使用 Azure 数据库迁移服务更快地加载数据，建议进行以下服务器参数和配置更改。 

* max_allowed_packet – 设置为 1073741824（即 1 GB），以防止由于大型行而引起的连接问题。 
* slow_query_log – 设置为“关闭”以关闭慢速查询日志。 这将消除数据加载过程中由慢速查询日志记录导致的开销。
* query_store_capture_mode - 设置为“无”以关闭查询存储。 这将消除由查询存储的采样活动导致的开销。
* innodb_buffer_pool_size – 只能通过纵向扩展 Azure Database for MySQL 服务器的计算来增大 innodb_buffer_pool_size。 在迁移期间从门户的定价层纵向扩展服务器到 64 vCore 常规用途 SKU，以增大 innodb_buffer_pool_size。 
* innodb_io_capacity 和 innodb_io_capacity_max - 从 Azure 门户中的服务器参数更改为 9000，以提高 IO 利用率，从而优化迁移速度。
* innodb_write_io_threads 和 innodb_write_io_threads - 从 Azure 门户中的服务器参数更改为 4 以加快迁移速度。
* 纵向扩展存储层 – 随着存储层的增加，Azure Database for MySQL 服务器的 IOP 会逐渐增加。 
    * 在单服务器部署选项中，为了更快地加载，建议增加存储层以增加预配的 IOP。 
    * 在灵活服务器部署选项中，建议可以缩放（增加或减少）IOPS，而不考虑存储大小。 
    * 请注意，存储大小只能纵向扩展，不能纵向缩减。

迁移完成后，可以将服务器参数和配置还原为工作负载所需的值。 


## <a name="migrate-database-schema"></a>迁移数据库架构

要传输所有数据库对象（例如表架构、索引和存储过程），需从源数据库提取架构并将其应用到目标数据库。 若要提取架构，可以将 mysqldump 与 `--no-data` 参数配合使用。 为此，需要一台可以连接到源 MySQL 数据库和目标 Azure Database for MySQL 的计算机。

要使用 mysqldump 导出架构，请运行以下命令：

```
mysqldump -h [servername] -u [username] -p[password] --databases [db name] --no-data > [schema file path]
```

例如：

```
mysqldump -h 10.10.123.123 -u root -p --databases migtestdb --no-data > d:\migtestdb.sql
```

要将架构导入到目标 Azure Database for MySQL，请运行以下命令：

```
mysql.exe -h [servername] -u [username] -p[password] [database]< [schema file path]
 ```

例如：

```
mysql.exe -h mysqlsstrgt.mysql.database.azure.com -u docadmin@mysqlsstrgt -p migtestdb < d:\migtestdb.sql
 ```

如果在架构中有外键或触发器，则迁移任务将处理迁移过程中的并行数据加载。 在架构迁移期间不需要删除外键或触发器。

[!INCLUDE [resource-provider-register](../../includes/database-migration-service-resource-provider-register.md)]

## <a name="create-a-database-migration-service-instance"></a>创建数据库迁移服务实例

1. 在 Azure 门户中，选择 **+ 创建资源**，搜索 Azure 数据库迁移服务，然后从下拉列表选择 **Azure 数据库迁移服务**。

    ![Azure 市场](media/tutorial-mysql-to-azure-mysql-offline-portal/03-dms-portal-marketplace.png)

2. 在“Azure 数据库迁移服务”屏幕上，选择“创建”   。

    ![创建 Azure 数据库迁移服务实例](media/tutorial-mysql-to-azure-mysql-offline-portal/04-dms-portal-marketplace-create.png)
  
3. 在“创建迁移服务”屏幕上，为服务、订阅以及新的或现有资源组指定名称。

4. 选择定价层并移动到“网络”屏幕。 脱机迁移功能仅在高级定价层上可用。

    有关成本和定价层的详细信息，请参阅[价格页](https://aka.ms/dms-pricing)。

    ![配置 Azure 数据库迁移服务基础设置](media/tutorial-mysql-to-azure-mysql-offline-portal/05-dms-portal-create-basic.png)

5. 从列表中选择一个现有的虚拟网络，或提供要创建的新虚拟网络的名称。 转到“查看 + 创建”屏幕。 可选择使用“标记”屏幕向服务添加标记。

    虚拟网络为 Azure 数据库迁移服务提供了对源 SQL Server 和目标 Azure SQL 数据库实例的访问权限。

    ![配置 Azure 数据库迁移服务网络设置](media/tutorial-mysql-to-azure-mysql-offline-portal/06-dms-portal-create-networking.png)

    有关如何在 Azure 门户中创建虚拟网络的详细信息，请参阅[使用 Azure 门户创建虚拟网络](../virtual-network/quick-create-portal.md)一文。

6. 查看配置，然后选择“创建”以创建服务。
    
    ![Azure 数据库迁移服务 - 创建](media/tutorial-mysql-to-azure-mysql-offline-portal/07-dms-portal-create-submit.png)

## <a name="create-a-migration-project"></a>创建迁移项目

创建服务后，在 Azure 门户中找到并打开它，然后创建一个新的迁移项目。  

1. 在 Azure 门户中，选择“所有服务”，搜索 Azure 数据库迁移服务，然后选择“Azure 数据库迁移服务”。

    ![查找 Azure 数据库迁移服务的所有实例](media/tutorial-mysql-to-azure-mysql-offline-portal/08-01-dms-portal-search-service.png)

2. 从搜索结果中选择你的迁移服务实例，然后选择“+ 新建迁移项目”。
    
    ![创建新迁移项目](media/tutorial-mysql-to-azure-mysql-offline-portal/08-02-dms-portal-new-project.png)

3. 在“新建迁移项目”屏幕上指定项目名称，在“源服务器类型”选择框中选择“MySQL”，在“目标服务器类型”选择框中选择“Azure Database For MySQL”，然后在“迁移活动类型”选择框中选择“数据迁移”      。 选择“创建并运行活动”。

    ![创建数据库迁移服务项目](media/tutorial-mysql-to-azure-mysql-offline-portal/09-dms-portal-project-mysql-create.png)

    > [!NOTE]
    > 也可以现在就选择“仅创建项目”来创建迁移项目，在以后再执行迁移。

## <a name="configure-migration-project"></a>配置迁移项目

1. 在“选择源”屏幕上，指定源 MySQL 实例的连接详细信息，然后选择“下一步: 选择目标>>” 

    ![“添加源详细信息”屏幕](media/tutorial-mysql-to-azure-mysql-offline-portal/10-dms-portal-project-mysql-source.png)

2. 在“选择目标”屏幕上，指定目标 zure Database for MySQL 实例的连接详细信息，然后选择“下一步: 选择数据库>>” 

    ![“添加目标详细信息”屏幕](media/tutorial-mysql-to-azure-mysql-offline-portal/11-dms-portal-project-mysql-target.png)

3. 在“选择数据库”屏幕上，映射源数据库和目标数据库以进行迁移，然后选择“下一步: 配置迁移设置>>” 。 可选择“使源服务器只读”选项，使源变为只读，但要注意，这是一项服务器级别设置。 如果选择了此选项，它会将整个服务器设置为只读，而不只是选定的数据库。
    
    如果目标数据库包含的数据库名称与源数据库的相同，则 Azure 数据库迁移服务默认会选择目标数据库。
    ![“选择数据库详细信息”屏幕](media/tutorial-mysql-to-azure-mysql-offline-portal/12-dms-portal-project-mysql-select-db.png)
    
    > [!NOTE] 
    > 虽然在此步骤中可以选择多个数据库，但由于每个数据库都将共享计算，因此可以通过这种方式迁移数据库的数量和速度有限。 利用高级 SKU 的默认配置，每个迁移任务都将尝试并行迁移两个表。 这些表可以来自任何选定的数据库。 如果这不够快，则可以将数据库迁移活动拆分为不同的迁移任务，并跨多个服务进行缩放。 此外，每个区域的每个订阅的 Azure 数据库迁移服务实例数限制为 10 个。
    > 若要对迁移吞吐量和并行化进行更精细控制，请参阅 [PowerShell：使用 DMS 运行从 MySQL 数据库到 Azure Database for MySQL 的脱机迁移](./migrate-mysql-to-azure-mysql-powershell.md)

4. 在“配置迁移设置”屏幕上，选择要作为迁移的一部分的表，然后选择“下一步: 摘要>>” 。 如果目标表有任何数据，则默认不会选择这些表，但你可以显式选择它们，它们会在开始迁移之前被截断。

    ![“选择表”屏幕](media/tutorial-mysql-to-azure-mysql-offline-portal/13-dms-portal-project-mysql-select-tbl.png)

5. 在“摘要”屏幕上的“活动名称”文本框中，指定迁移活动的名称，然后查看摘要，确保源和目标详细信息与此前指定的信息相符 。

    ![迁移项目摘要](media/tutorial-mysql-to-azure-mysql-offline-portal/14-dms-portal-project-mysql-activity-summary.png)

6. 选择“开始迁移”。 迁移活动窗口随即出现，活动的“状态”为“正在初始化”   。 表迁移开始时，“状态”将更改为“正在运行” 。
 
     ![运行迁移](media/tutorial-mysql-to-azure-mysql-offline-portal/15-dms-portal-project-mysql-running.png)

## <a name="monitor-the-migration"></a>监视迁移

1. 在“迁移活动”屏幕上，选择“刷新”以更新显示并查看有关已完成的表数的进度。 

2. 可以单击活动屏幕上的数据库名称，以查看每个表迁移时的状态。 选择“刷新”可更新显示。 

     ![监视迁移](media/tutorial-mysql-to-azure-mysql-offline-portal/16-dms-portal-project-mysql-monitor.png)

## <a name="complete-the-migration"></a>完成迁移

1. 在迁移活动屏幕上选择“刷新”，以便更新显示，直到迁移的“状态”显示为“完成”。

    ![完成迁移](media/tutorial-mysql-to-azure-mysql-offline-portal/17-dms-portal-project-mysql-complete.png)

## <a name="post-migration-activities"></a>迁移后活动

脱机迁移中的直接转换迁移是一个依赖于应用程序的过程，虽然本文档不会进行相关介绍，但规定了以下迁移后活动：

1. 按照应用程序要求创建登录名、角色和权限。
2. 在迁移前的步骤中，根据提取的内容，在目标数据库上重新创建所有触发器。
3. 针对目标数据库执行应用程序的性能测试，以验证迁移。 

## <a name="clean-up-resources"></a>清理资源

如果不打算继续使用数据库迁移服务，则可以使用以下步骤删除该服务：

1. 在 Azure 门户中，选择“所有服务”，搜索 Azure 数据库迁移服务，然后选择“Azure 数据库迁移服务”。

    ![查找 DMS 的所有实例](media/tutorial-mysql-to-azure-mysql-offline-portal/08-01-dms-portal-search-service.png)

2. 从搜索结果中选择你的迁移服务实例，然后选择“删除服务”。
    
    ![删除迁移服务](media/tutorial-mysql-to-azure-mysql-offline-portal/18-dms-portal-delete.png)

3. 在确认对话框中，在“输入数据库迁移服务名称”文本框中输入服务的名称，然后选择“删除” 

    ![确认迁移服务删除](media/tutorial-mysql-to-azure-mysql-offline-portal/19-dms-portal-deleteconfirm.png)

## <a name="next-steps"></a>后续步骤

* 有关使用 DMS 执行迁移时的已知问题和限制，请参阅[常见问题 - Azure 数据库迁移服务](./known-issues-troubleshooting-dms.md)一文。
* 要在使用 DMS 时排查源数据库连接问题，请参阅[连接源数据库时的问题](./known-issues-troubleshooting-dms-source-connectivity.md)一文。
* 若要了解 Azure 数据库迁移服务，请参阅[什么是 Azure 数据库迁移服务？](./dms-overview.md)一文。
* 若要了解 Azure Database for MySQL，请参阅[什么是 Azure Database for MySQL？](../mysql/overview.md)一文。
* 有关通过 PowerShell 使用 DMS 的指南，请参阅 [PowerShell：使用 DMS 运行从 MySQL 数据库到 Azure Database for MySQL 的脱机迁移](./migrate-mysql-to-azure-mysql-powershell.md)
