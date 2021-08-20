---
title: 教程：通过 Azure 门户将 PostgreSQL 迁移到 Azure DB for PostgreSQL
titleSuffix: Azure Database Migration Service
description: 了解如何通过 Azure 门户使用 Azure 数据库迁移服务执行从本地 PostgreSQL 到 Azure Database for PostgreSQL 的联机迁移。
services: dms
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 04/11/2020
ms.openlocfilehash: 16493cf44ba3666b900b34e18acd90f5ac73baca
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "112282756"
---
# <a name="tutorial-migrate-postgresql-to-azure-db-for-postgresql-online-using-dms-via-the-azure-portal"></a>教程：通过 Azure 门户使用 DMS 将 PostgreSQL 联机迁移到 Azure DB for PostgreSQL

可以使用 Azure 数据库迁移服务在尽量缩短应用程序停机时间的情况下，将数据库从本地 PostgreSQL 实例迁移到 [Azure Database for PostgreSQL](../postgresql/index.yml)。 本教程介绍如何在 Azure 数据库迁移服务中使用联机迁移活动将 **DVD Rental** 示例数据库从 PostgreSQL 9.6 的本地实例迁移到 Azure Database for PostgreSQL。

本教程介绍如何执行下列操作：
> [!div class="checklist"]
>
> * 使用 pg_dump 实用工具迁移示例架构。
> * 创建 Azure 数据库迁移服务的实例。
> * 在 Azure 数据库迁移服务中创建迁移项目。
> * 运行迁移。
> * 监视迁移。
> * 执行直接转换迁移。

> [!NOTE]
> 使用 Azure 数据库迁移服务执行联机迁移需要基于“高级”定价层创建实例。 我们对磁盘进行加密，以防止在迁移过程中数据被盗

> [!IMPORTANT]
> 为获得最佳迁移体验，Microsoft 建议在目标数据库所在的 Azure 区域中创建 Azure 数据库迁移服务的实例。 跨区域或地理位置移动数据可能会减慢迁移过程并引入错误。

## <a name="prerequisites"></a>先决条件

要完成本教程，需要：

* 下载并安装 [PostgreSQL 社区版](https://www.postgresql.org/download/) 9.4、9.5、9.6 或 10。 源 PostgreSQL 服务器版本必须是 9.4、9.5、9.6、10、11 或 12。 有关详细信息，请参阅[支持的 PostgreSQL 数据库版本](../postgresql/concepts-supported-versions.md)一文。

    另请注意，目标 Azure Database for PostgreSQL 版本必须等于或晚于本地 PostgreSQL 版本。 例如，PostgreSQL 9.6 可以迁移到 Azure Database for PostgreSQL 9.6、10 或 11，而不能迁移到 Azure Database for PostgreSQL 9.5。 目前不支持迁移到 PostgreSQL 13+。 

* [创建 Azure Database for PostgreSQL 服务器](../postgresql/quickstart-create-server-database-portal.md)或[创建 Azure Database for PostgreSQL - 超大规模 (Citus) 服务器](../postgresql/quickstart-create-hyperscale-portal.md)。
* 使用 Azure 资源管理器部署模型创建适合 Azure 数据库迁移服务的 Microsoft Azure 虚拟网络，它将使用 [ExpressRoute](../expressroute/expressroute-introduction.md) 或 [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) 为本地源服务器提供站点到站点连接。 有关创建虚拟网络的详细信息，请参阅[虚拟网络文档](../virtual-network/index.yml)，尤其是提供了分步详细信息的快速入门文章。

    > [!NOTE]
    > 在虚拟网络设置期间，如果将 ExpressRoute 与 Microsoft 的网络对等互连一起使用，则请将以下服务[终结点](../virtual-network/virtual-network-service-endpoints-overview.md)添加到要在其中预配该服务的子网：
    >
    > * 目标数据库终结点（例如，SQL 终结点、Cosmos DB 终结点等）
    > * 存储终结点
    > * 服务总线终结点
    >
    > Azure 数据库迁移服务缺少 Internet 连接，因此必须提供此配置。

* 请确保虚拟网络的网络安全组 (NSG) 规则不阻止 ServiceBus、存储服务和 AzureMonitor 的 ServiceTag 出站端口 443。 有关虚拟网络 NSG 流量筛选的更多详细信息，请参阅[使用网络安全组筛选网络流量](../virtual-network/virtual-network-vnet-plan-design-arm.md)一文。
* 配置[针对数据库引擎访问的 Windows 防火墙](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)。
* 打开 Windows 防火墙，使 Azure 数据库迁移服务能够访问源 PostgreSQL 服务器（默认情况下为 TCP 端口 5432）。
* 在源数据库的前面使用了防火墙设备时，可能需要添加防火墙规则以允许 Azure 数据库迁移服务访问要迁移的源数据库。
* 为 Azure Database for PostgreSQL 创建服务器级[防火墙规则](../postgresql/concepts-firewall-rules.md)，以允许 Azure 数据库迁移服务访问目标数据库。 提供用于 Azure 数据库迁移服务的虚拟网络子网范围。
* 在 postgresql.config 文件中启用逻辑复制，并设置以下参数：

  * wal_level = **logical**
  * max_replication_slots = [槽数]，建议设置为“5 个槽” 
  * max_wal_senders =[并发任务数] - max_wal_senders 参数设置可以运行的并发任务数，建议设置为“10 个任务” 

> [!IMPORTANT]
> 现有数据库中的所有表都需要主键，以确保可以将更改同步到目标数据库。

## <a name="migrate-the-sample-schema"></a>迁移示例架构

若要完成所有数据库对象（例如表架构、索引和存储过程），需从源数据库提取架构并将其应用到此数据库。

1. 使用 pg_dump -s 命令为数据库创建架构转储文件。

    ```
    pg_dump -o -h hostname -U db_username -d db_name -s > your_schema.sql
    ```

    例如，若要为 dvdrental 数据库创建架构转储文件：

    ```
    pg_dump -o -h localhost -U postgres -d dvdrental -s -O -x > dvdrentalSchema.sql
    ```

    若要详细了解如何使用 pg_dump 实用程序，请参阅 [pg-dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html#PG-DUMP-EXAMPLES) 教程中的示例。

2. 在目标环境中创建一个空数据库，即 Azure Database for PostgreSQL。

    有关如何连接和创建数据库的详细信息，请参阅文章[在 Azure 门户中创建 Azure Database for PostgreSQL 服务器](../postgresql/quickstart-create-server-database-portal.md)或[在 Azure 门户中创建 Azure Database for PostgreSQL - 超大规模 (Citus) 服务器](../postgresql/quickstart-create-hyperscale-portal.md)。

    > [!NOTE]
    > Azure Database for PostgreSQL - 超大规模 (Citus) 的实例只有单个数据库：citus。

3. 通过还原架构转储文件，将架构导入已创建的目标数据库。

    ```
    psql -h hostname -U db_username -d db_name < your_schema.sql
    ```

    例如：

    ```
    psql -h mypgserver-20170401.postgres.database.azure.com  -U postgres -d dvdrental citus < dvdrentalSchema.sql
    ```

   > [!NOTE]
   > 迁移服务在内部处理外键和触发器的启用/禁用，以确保可靠且稳定的数据迁移。 因此，你不需要担忧对目标数据库架构所做的任何修改。


## <a name="register-the-microsoftdatamigration-resource-provider"></a>注册 Microsoft.DataMigration 资源提供程序

1. 登录到 Azure 门户，选择“所有服务”，然后选择“订阅”。

   ![显示门户订阅](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-select-subscriptions.png)

2. 选择要在其中创建 Azure 数据库迁移服务实例的订阅，再选择“资源提供程序”。

    ![显示资源提供程序](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-select-resource-provider.png)

3. 搜索迁移服务，再选择“Microsoft.DataMigration”右侧的“注册” 。

    ![注册资源提供程序](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>创建 DMS 实例

1. 在 Azure 门户中，选择 **+ 创建资源**，搜索 Azure 数据库迁移服务，然后从下拉列表选择 **Azure 数据库迁移服务**。

    ![Azure 市场](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-marketplace.png)

2. 在“Azure 数据库迁移服务”屏幕上，选择“创建”   。

    ![创建 Azure 数据库迁移服务实例](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-create1.png)
  
3. 在“创建迁移服务”屏幕上，指定服务的名称、订阅、新的或现有资源组以及位置。

4. 选择现有虚拟网络或新建一个。

    虚拟网络为 Azure 数据库迁移服务提供对源 PostgreSQL 服务器和目标 Azure Database for PostgreSQL 实例的访问权限。

    有关如何在 Azure 门户中创建虚拟网络的详细信息，请参阅[使用 Azure 门户创建虚拟网络](../virtual-network/quick-create-portal.md)一文。

5. 选择定价层。

    有关成本和定价层的详细信息，请参阅[价格页](https://aka.ms/dms-pricing)。

    ![配置 Azure 数据库迁移服务实例设置](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-settings4.png)

6. 选择“查看 + 创建”以创建服务。

   服务创建将在约 10 到 15 分钟内完成。

## <a name="create-a-migration-project"></a>创建迁移项目

创建服务后，在 Azure 门户中找到并打开它，然后创建一个新的迁移项目。

1. 在 Azure 门户中，选择“所有服务”，搜索 Azure 数据库迁移服务，然后选择“Azure 数据库迁移服务”。

      ![查找 Azure 数据库迁移服务的所有实例](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-search.png)

2. 在“Azure 数据库迁移服务”屏幕上，搜索所创建的 Azure 数据库迁移服务实例名称，选择该实例，然后选择“+ 新建迁移项目” 。

3. 在“新建迁移项目”屏幕上指定项目名称，在“源服务器类型”文本框中选择“PostgreSQL”，在“目标服务器类型”文本框中选择“Azure Database for PostgreSQL”    。

4. 在“选择活动类型”部分选择“联机数据迁移”。 

    ![创建 Azure 数据库迁移服务项目](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-create-project.png)

    > [!NOTE]
    > 也可以现在就选择“仅创建项目”来创建迁移项目，在以后再执行迁移。

5. 选择“保存”，注意成功使用 Azure 数据库迁移服务迁移数据所要满足的要求，然后选择“创建并运行活动” 。

## <a name="specify-source-details"></a>指定源详细信息

1. 在“添加源详细信息”  屏幕上，指定源 PostgreSQL 实例的连接详细信息。

    ![“添加源详细信息”屏幕](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-add-source-details.png)

2. 选择“保存” 。

## <a name="specify-target-details"></a>指定目标详细信息

1. 在“目标详细信息”屏幕上，指定目标超大规模 (Citus) 服务器的连接详细信息，该服务器是超大规模 (Citus) 的预配实例，DVD Rental 架构通过使用 pg\dump 部署到该实例 。

    ![“目标详细信息”屏幕](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-add-target-details.png)

2. 选择“保存”，然后在“映射到目标数据库”屏幕上，映射源和目标数据库以进行迁移。 

    如果目标数据库包含的数据库名称与源数据库的相同，则 Azure 数据库迁移服务默认会选择目标数据库。

    ![“映射到目标数据库”屏幕](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-map-target-databases.png)

3. 选择“保存”，然后在“迁移设置”屏幕上接受默认值 。

    ![“迁移设置”屏幕](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-migration-settings.png)

4. 选择“保存”，在“迁移摘要”屏幕上的“活动名称”文本框中指定迁移活动的名称，然后查看摘要，确保源和目标详细信息与此前指定的信息相符  。

    ![“迁移摘要”屏幕](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-migration-summary.png)

## <a name="run-the-migration"></a>运行迁移

* 选择“运行迁移”。

    迁移活动窗口随即出现，活动的“状态”应更新并显示为“正在进行备份” 。

## <a name="monitor-the-migration"></a>监视迁移

1. 在迁移活动屏幕上选择“刷新”，以便更新显示，直到迁移的“状态”显示为“完成”。

     ![监视迁移过程](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-monitor-migration.png)

2. 完成迁移后，请在“数据库名称”下选择特定数据库即可转到“完整数据加载”和“增量数据同步”操作的迁移状态  。

   > [!NOTE]
   > “完整数据加载”会显示初始加载迁移状态，而“增量数据同步”则会显示变更数据捕获 (CDC) 状态。 

     ![完整数据加载详细信息](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-full-data-load-details.png)

     ![增量数据同步详细信息](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-incremental-data-sync-details.png)

## <a name="perform-migration-cutover"></a>执行迁移直接转换

完成初始的完整加载以后，数据库会被标记为“直接转换可供执行”。

1. 如果准备完成数据库迁移，请选择“启动直接转换”。 

2. 等到“挂起的更改”  计数器显示“0”  以确保源数据库的所有传入事务都已停止，选中“确认”  复选框，然后选择“应用”  。

    ![“完成直接转换”屏幕](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-complete-cutover.png)

3. 当数据库迁移状态显示“已完成”时[重新创建序列](https://wiki.postgresql.org/wiki/Fixing_Sequences)（如适用），并将应用程序连接到 Azure Database for PostgreSQL 的新目标实例。

## <a name="next-steps"></a>后续步骤

* 若要了解联机迁移到 Azure Database for PostgreSQL 时的已知问题和限制，请参阅 [Azure Database for PostgreSQL 联机迁移的已知问题和解决方法](known-issues-azure-postgresql-online.md)一文。
* 若要了解 Azure 数据库迁移服务，请参阅[什么是 Azure 数据库迁移服务？](./dms-overview.md)一文。
* 有关 Azure Database for PostgreSQL 的信息，请参阅[什么是 Azure Database for PostgreSQL？](../postgresql/overview.md)一文。
