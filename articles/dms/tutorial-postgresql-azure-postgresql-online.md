---
title: 教程：通过 Azure CLI 将 PostgreSQL 联机迁移到 Azure Database for PostgreSQL
titleSuffix: Azure Database Migration Service
description: 了解如何通过 CLI 使用 Azure 数据库迁移服务执行从本地 PostgreSQL 到 Azure Database for PostgreSQL 的联机迁移。
services: dms
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019, devx-track-azurecli
ms.topic: tutorial
ms.date: 04/11/2020
ms.openlocfilehash: 52d5c7a500652b0090cf9b21400a9c45f2bf54e7
ms.sourcegitcommit: b044915306a6275c2211f143aa2daf9299d0c574
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113033724"
---
# <a name="tutorial-migrate-postgresql-to-azure-db-for-postgresql-online-using-dms-via-the-azure-cli"></a>教程：通过 Azure CLI 使用 DMS 将 PostgreSQL 联机迁移到 Azure DB for PostgreSQL

可以使用 Azure 数据库迁移服务在尽量缩短停机时间的情况下，将数据库从本地 PostgreSQL 实例迁移到 [Azure Database for PostgreSQL](../postgresql/index.yml)。 换而言之，实现这种迁移只会对应用程序造成极短暂的停机。 本教程介绍如何在 Azure 数据库迁移服务中使用联机迁移活动将 **DVD Rental** 示例数据库从 PostgreSQL 9.6 的本地实例迁移到 Azure Database for PostgreSQL。

本教程介绍如何执行下列操作：
> [!div class="checklist"]
>
> * 使用 pg_dump 实用程序迁移示例架构。
> * 创建 Azure 数据库迁移服务的实例。
> * 使用 Azure 数据库迁移服务创建迁移项目。
> * 运行迁移。
> * 监视迁移。

> [!NOTE]
> 使用 Azure 数据库迁移服务执行联机迁移需要基于“高级”定价层创建实例。 我们对磁盘进行加密，以防止在迁移过程中数据被盗。

> [!IMPORTANT]
> 为获得最佳迁移体验，Microsoft 建议在目标数据库所在的 Azure 区域中创建 Azure 数据库迁移服务的实例。 跨区域或地理位置移动数据可能会减慢迁移过程并引入错误。

## <a name="prerequisites"></a>先决条件

要完成本教程，需要：

* 下载并安装 [PostgreSQL 社区版](https://www.postgresql.org/download/) 9.5、9.6 或 10。 源 PostgreSQL 服务器版本必须是 9.5.11、9.6.7、10 或更高版本。 有关详细信息，请参阅[支持的 PostgreSQL 数据库版本](../postgresql/concepts-supported-versions.md)一文。

    另请注意，目标 Azure Database for PostgreSQL 版本必须等于或晚于本地 PostgreSQL 版本。 例如，PostgreSQL 9.6 只能迁移到 Azure Database for PostgreSQL 9.6、10 或 11，而不能迁移到 Azure Database for PostgreSQL 9.5。

* [在 Azure Database for PostgreSQL 中创建实例](../postgresql/quickstart-create-server-database-portal.md)或[创建 Azure Database for PostgreSQL - 超大规模 (Citus) 服务器](../postgresql/quickstart-create-hyperscale-portal.md)。
* 使用 Azure 资源管理器部署模型创建适合 Azure 数据库迁移服务的 Microsoft Azure 虚拟网络，它将使用 [ExpressRoute](../expressroute/expressroute-introduction.md) 或 [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) 为本地源服务器提供站点到站点连接。 有关创建虚拟网络的详细信息，请参阅[虚拟网络文档](../virtual-network/index.yml)，尤其是提供了分步详细信息的快速入门文章。

    > [!NOTE]
    > 在虚拟网络设置期间，如果将 ExpressRoute 与 Microsoft 的网络对等互连一起使用，则请将以下服务[终结点](../virtual-network/virtual-network-service-endpoints-overview.md)添加到要在其中预配该服务的子网：
    >
    > * 目标数据库终结点（例如，SQL 终结点、Cosmos DB 终结点等）
    > * 存储终结点
    > * 服务总线终结点
    >
    > Azure 数据库迁移服务缺少 Internet 连接，因此必须提供此配置。

* 请确保虚拟网络的网络安全组(NSG) 规则不阻止 ServiceBus、存储服务和 AzureMonitor 的 ServiceTag 出站端口 443。 有关虚拟网络 NSG 流量筛选的更多详细信息，请参阅[使用网络安全组筛选网络流量](../virtual-network/virtual-network-vnet-plan-design-arm.md)一文。
* 配置[针对数据库引擎访问的 Windows 防火墙](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)。
* 打开 Windows 防火墙，使 Azure 数据库迁移服务能够访问源 PostgreSQL 服务器（默认情况下为 TCP 端口 5432）。
* 在源数据库的前面使用了防火墙设备时，可能需要添加防火墙规则以允许 Azure 数据库迁移服务访问要迁移的源数据库。
* 为 Azure Database for PostgreSQL 创建服务器级[防火墙规则](../postgresql/concepts-firewall-rules.md)，以允许 Azure 数据库迁移服务访问目标数据库。 提供用于 Azure 数据库迁移服务的虚拟网络子网范围。
* 可以通过两种方法来调用 CLI：

  * 在 Azure 门户右上角选择“Cloud Shell”按钮：

       ![Azure 门户中的“Cloud Shell”按钮](media/tutorial-postgresql-to-azure-postgresql-online/cloud-shell-button.png)

  * 在本地安装并运行 CLI。 若要管理此迁移所需的 Azure 资源，必须安装命令行工具 CLI 2.18 或更高版本。

       若要下载 CLI，请按照[安装 Azure CLI](/cli/azure/install-azure-cli) 一文中的说明操作。 该文章还列出了支持 Azure CLI 的平台。

       若要设置适用于 Linux 的 Windows 子系统 (WSL)，请按照 [Windows 10 Installation Guide](/windows/wsl/install-win10)（Windows 10 安装指南）中的说明操作

* 通过编辑 postgresql.config 文件并设置以下参数，在源服务器上启用逻辑复制：

  * wal_level = **logical**
  * max_replication_slots = [槽数]，建议设置为“5 个槽” 
  * max_wal_senders =[并发任务数] - max_wal_senders 参数设置可以运行的并发任务数，建议设置为“10 个任务” 

## <a name="migrate-the-sample-schema"></a>迁移示例架构

若要完成所有数据库对象（例如表架构、索引和存储过程），需从源数据库提取架构并将其应用到此数据库。

1. 使用 pg_dump -s 命令为数据库创建架构转储文件。 

    ```
    pg_dump -o -h hostname -U db_username -d db_name -s > your_schema.sql
    ```

    例如，若要转储 dvdrental 数据库的架构文件，请执行以下命令：
    ```
    pg_dump -o -h localhost -U postgres -d dvdrental -s  > dvdrentalSchema.sql
    ```

    若要详细了解如何使用 pg_dump 实用程序，请参阅 [pg-dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html#PG-DUMP-EXAMPLES) 教程中的示例。

2. 在目标环境中创建一个空数据库，即 Azure Database for PostgreSQL。

    有关如何连接和创建数据库的详细信息，请参阅文章[在 Azure 门户中创建 Azure Database for PostgreSQL 服务器](../postgresql/quickstart-create-server-database-portal.md)或[在 Azure 门户中创建 Azure Database for PostgreSQL - 超大规模 (Citus) 服务器](../postgresql/quickstart-create-hyperscale-portal.md)。

3. 通过还原架构转储文件，将架构导入已创建的目标数据库。

    ```
    psql -h hostname -U db_username -d db_name < your_schema.sql 
    ```

    例如：

    ```
    psql -h mypgserver-20170401.postgres.database.azure.com  -U postgres -d dvdrental < dvdrentalSchema.sql
    ```

  > [!NOTE]
   > 迁移服务在内部处理外键和触发器的启用/禁用，以确保可靠且稳定的数据迁移。 因此，你不需要担忧对目标数据库架构所做的任何修改。

## <a name="provisioning-an-instance-of-dms-using-the-azure-cli"></a>使用 Azure CLI 预配 DMS 的实例

1. 安装 dms 同步扩展：
   * 通过运行以下命令登录到 Azure：
       ```azurecli
       az login
       ```

   * 系统提示时，请打开 Web 浏览器并输入用于对设备进行身份验证的代码。 按照列出的说明操作。

   * PostgreSQL 联机迁移现已在常规 CLI 包（2.18.0 和更高版本）中提供，无需 `dms-preview` 扩展。 如果你过去安装了该扩展，可使用以下步骤将其删除：
        * 若要检查是否已安装 `dms-preview` 扩展，请运行以下命令：
        
            ```azurecli
            az extension list -o table
            ```

        * 如果已安装 `dms-preview` 扩展，若要将其卸载，请运行以下命令：
        
            ```azurecli
            az extension remove --name dms-preview
            ```

        * 若要验证是否已正确卸载 `dms-preview` 扩展，请运行以下命令，然后，列表中不应会出现 `dms-preview` 扩展：

            ```azurecli
            az extension list -o table
            ```

      > [!IMPORTANT]
      > 对于 Azure DMS 支持的其他迁移路径，仍可能需要 `dms-preview` 扩展。 请查看特定迁移路径的文档以确定是否需要该扩展。 此文档介绍了扩展的要求，其内容专门与从 PostgreSQL 联机迁移到 Azure Database for PostgreSQL 相关。

   * 任何时候都可以通过运行以下命令来查看所有在 DMS 中受支持的命令：

       ```azurecli
       az dms -h
       ```

   * 如果有多个 Azure 订阅，请运行以下命令，以便设置需要用来预配 DMS 服务实例的订阅。

        ```azurecli
       az account set -s 97181df2-909d-420b-ab93-1bff15acb6b7
        ```

2. 通过运行以下命令来预配 DMS 的实例：

   ```azurecli
   az dms create -l <location> -n <newServiceName> -g <yourResourceGroupName> --sku-name Premium_4vCores --subnet/subscriptions/{vnet subscription id}/resourceGroups/{vnet resource group}/providers/Microsoft.Network/virtualNetworks/{vnet name}/subnets/{subnet name} –tags tagName1=tagValue1 tagWithNoValue
   ```

   例如，以下命令会创建一项具有下述参数的服务：

   * 位置：美国东部 2
   * 订阅：97181df2-909d-420b-ab93-1bff15acb6b7
   * 资源组名称：PostgresDemo
   * DMS 服务名称：PostgresCLI

   ```azurecli
   az dms create -l eastus2 -g PostgresDemo -n PostgresCLI --subnet /subscriptions/97181df2-909d-420b-ab93-1bff15acb6b7/resourceGroups/ERNetwork/providers/Microsoft.Network/virtualNetworks/AzureDMS-CORP-USC-VNET-5044/subnets/Subnet-1 --sku-name Premium_4vCores
   ```

   创建 DMS 服务的实例需要大约 10-12 分钟。

3. 若要确定 DMS 代理的 IP 地址，以便将其添加到 Postgres pg_hba.conf 文件，请运行以下命令：

    ```azurecli
    az network nic list -g <ResourceGroupName>--query '[].ipConfigurations | [].privateIpAddress'
    ```

    例如：

    ```azurecli
    az network nic list -g PostgresDemo --query '[].ipConfigurations | [].privateIpAddress'
    ```

    获得的结果应该类似于以下地址： 

    ```output
    [
      "172.16.136.18"
    ]
    ```

4. 将 DMS 代理的 IP 地址添加到 Postgres pg_hba.conf 文件。

    * 在 DMS 中预配完以后，记下 DMS IP 地址。
    * 将 IP 地址添加到源中与以下条目类似的 pg_hba.conf 文件：

        ```
        host     all            all        172.16.136.18/10    md5
        host     replication    postgres   172.16.136.18/10    md5
        ```

5. 接下来请运行以下命令，创建 PostgreSQL 迁移项目：
    
    ```azurecli
    az dms project create -l <location> -g <ResourceGroupName> --service-name <yourServiceName> --source-platform PostgreSQL --target-platform AzureDbforPostgreSQL -n <newProjectName>
    ```

    例如，以下命令会创建一个使用下述参数的项目：

    * 位置：美国中西部
    * 资源组名称：PostgresDemo
    * 服务名称：PostgresCLI
    * 项目名称：PGMigration
    * 源平台：PostgreSQL
    * 目标平台：AzureDbForPostgreSql   

     ```azurecli
     az dms project create -l westcentralus -n PGMigration -g PostgresDemo --service-name PostgresCLI --source-platform PostgreSQL --target-platform AzureDbForPostgreSql
     ```

6. 使用以下步骤创建 PostgreSQL 迁移任务。

    此步骤包括使用源 IP、UserID 和密码，目标 IP、UserID 和密码以及任务类型来建立连接。

    * 若要查看选项的完整列表，请运行以下命令：

        ```azurecli
        az dms project task create -h
        ```

        源和目标连接的输入参数都引用一个包含对象列表的 json 文件。

        适用于 PostgreSQL 连接的连接 JSON 对象的格式。
        
        ```json
        {
            // if this is missing or null, you will be prompted
            "userName": "user name",
            // if this is missing or null (highly recommended) you will  be prompted  
            "password": null,
            "serverName": "server name",
            // if this is missing, it will default to the 'postgres' database
            "databaseName": "database name",
            // if this is missing, it will default to 5432 
            "port": 5432                
        }
        ```

        此外还有一个数据库选项 json 文件，该文件列出 json 对象。 对于 PostgreSQL，数据库选项 JSON 对象的格式如下所示：

        ```json
        [
            {
                "name": "source database",
                "target_database_name": "target database",
                "selectedTables": [
                    "schemaName1.tableName1",
                    ...n
                ]
            },
            ...n
        ]
        ```

    * 若要创建源连接 JSON，请打开记事本，复制以下 JSON 并将其粘贴到文件中。 根据源服务器修改该文件后，将其保存到 C:\DMS\source.json。

        ```json
        {
            "userName": "postgres",    
            "password": null,
            "serverName": "13.51.14.222",
            "databaseName": "dvdrental", 
            "port": 5432                
        }
        ```

    * 若要创建目标连接 JSON，请打开记事本，复制以下 JSON 并将其粘贴到文件中。 根据目标服务器修改该文件后，将其保存到 C:\DMS\target.json。
    
        ```json
        {
            "userName": " dms@builddemotarget",    
            "password": null,           
            "serverName": " builddemotarget.postgres.database.azure.com",
            "databaseName": "inventory", 
            "port": 5432                
        }
        ```

    * 创建一个数据库选项 JSON 文件，用于列出要迁移的数据库的清单和映射：

        * 创建要迁移的表的列表，或者可以使用 SQL 查询从源数据库生成该列表。 下面提供了一个用于生成表列表的示例查询。 如果使用此查询，请记得删除最后一个表名称末尾的最后一个逗号，使之成为有效的 JSON 数组。 
        
            ```sql
            SELECT
                FORMAT('%s,', REPLACE(FORMAT('%I.%I', schemaname, tablename), '"', '\"')) AS SelectedTables
            FROM 
                pg_tables
            WHERE 
                schemaname NOT IN ('pg_catalog', 'information_schema');
            ```

        * 为每个数据库创建包含一个条目的数据库选项 JSON 文件，该条目包含源数据库和目标数据库名称，以及要迁移的选定表的列表。 可以使用上述 SQL 查询的输出来填充“selectedTables”数组。 请注意，如果选定表列表为空，则服务将包含所有要迁移的、具有匹配架构和表名称的表。
        
            ```json
            [
                {
                    "name": "dvdrental",
                    "target_database_name": "dvdrental",
                    "selectedTables": [
                        "schemaName1.tableName1",
                        "schemaName1.tableName2",                    
                        ...
                        "schemaNameN.tableNameM"
                    ]
                },
                ... n
            ]
            ```

    * 运行以下命令并在其中指定源连接、目标连接和数据库选项 JSON 文件。

        ```azurecli
        az dms project task create -g PostgresDemo --project-name PGMigration --source-connection-json c:\DMS\source.json --database-options-json C:\DMS\option.json --service-name PostgresCLI --target-connection-json c:\DMS\target.json --task-type OnlineMigration -n runnowtask    
        ```

    现在已成功提交一个迁移任务。

7. 若要显示任务进度，请运行以下命令：

    * 查看简短的常规任务状态
        ```azurecli
        az dms project task show --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name runnowtask
        ```

    * 查看详细的任务状态，包括迁移进度信息

        ```azurecli
        az dms project task show --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name runnowtask --expand output
        ```

    * 还可以使用 [JMESPATH](https://jmespath.org/) 查询格式来仅提取 expand 命令输出中的 migrationState：

        ```azurecli
        az dms project task show --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name runnowtask --expand output --query 'properties.output[].migrationState'
        ```

        在输出中，有多个参数指示了不同迁移步骤的进度。 例如，查看以下输出：

        ```json
        {
            "output": [
                // Database Level
                {
                    "appliedChanges": 0, // Total incremental sync applied after full load
                    "cdcDeleteCounter": 0, // Total delete operation  applied after full load
                    "cdcInsertCounter": 0, // Total insert operation applied after full load
                    "cdcUpdateCounter": 0, // Total update operation applied after full load
                    "databaseName": "inventory",
                    "endedOn": null,
                    "fullLoadCompletedTables": 2, //Number of tables completed full load
                    "fullLoadErroredTables": 0, //Number of tables that contain migration error
                    "fullLoadLoadingTables": 0, //Number of tables that are in loading status
                    "fullLoadQueuedTables": 0, //Number of tables that are in queued status
                    "id": "db|inventory",
                    "incomingChanges": 0, //Number of changes after full load
                    "initializationCompleted": true,
                    "latency": 0,
                    //Status of migration task
                    "migrationState": "READY_TO_COMPLETE", //READY_TO_COMPLETE => the database is ready for cutover
                    "resultType": "DatabaseLevelOutput",
                    "startedOn": "2018-07-05T23:36:02.27839+00:00"
                }, {
                    "databaseCount": 1,
                    "endedOn": null,
                    "id": "dd27aa3a-ed71-4bff-ab34-77db4261101c",
                    "resultType": "MigrationLevelOutput",
                    "sourceServer": "138.91.123.10",
                    "sourceVersion": "PostgreSQL",
                    "startedOn": "2018-07-05T23:36:02.27839+00:00",
                    "state": "PENDING",
                    "targetServer": "builddemotarget.postgres.database.azure.com",
                    "targetVersion": "Azure Database for PostgreSQL"
                },
                // Table 1
                {
                    "cdcDeleteCounter": 0,
                    "cdcInsertCounter": 0,
                    "cdcUpdateCounter": 0,
                    "dataErrorsCount": 0,
                    "databaseName": "inventory",
                    "fullLoadEndedOn": "2018-07-05T23:36:20.740701+00:00", //Full load completed time
                    "fullLoadEstFinishTime": "1970-01-01T00:00:00+00:00",
                    "fullLoadStartedOn": "2018-07-05T23:36:15.864552+00:00", //Full load started time
                    "fullLoadTotalRows": 10, //Number of rows loaded in full load
                    "fullLoadTotalVolumeBytes": 7056, //Volume in Bytes in full load
                    "id": "or|inventory|public|actor",
                    "lastModifiedTime": "2018-07-05T23:36:16.880174+00:00",
                    "resultType": "TableLevelOutput",
                    "state": "COMPLETED", //State of migration for this table
                    "tableName": "public.catalog", //Table name
                    "totalChangesApplied": 0 //Total sync changes that applied after full load
                },
                //Table 2
                {
                    "cdcDeleteCounter": 0,
                    "cdcInsertCounter": 50,
                    "cdcUpdateCounter": 0,
                    "dataErrorsCount": 0,
                    "databaseName": "inventory",
                    "fullLoadEndedOn": "2018-07-05T23:36:23.963138+00:00",
                    "fullLoadEstFinishTime": "1970-01-01T00:00:00+00:00",
                    "fullLoadStartedOn": "2018-07-05T23:36:19.302013+00:00",
                    "fullLoadTotalRows": 112,
                    "fullLoadTotalVolumeBytes": 46592,
                    "id": "or|inventory|public|address",
                    "lastModifiedTime": "2018-07-05T23:36:20.308646+00:00",
                    "resultType": "TableLevelOutput",
                    "state": "COMPLETED",
                    "tableName": "public.orders",
                    "totalChangesApplied": 0
                }
            ],
            // DMS migration task state
            "state": "Running", //Running => service is still listening to any changes that might come in
            "taskType": null
        }
        ```

## <a name="cutover-migration-task"></a>直接转换迁移任务

完全加载以后，即可对数据库进行直接转换。 完全加载以后，当新事务传入时，DMS 任务可能仍在应用所做的更改，具体取决于源服务器的繁忙程度。

若要确保所有数据都已捕获，请验证源和目标数据库的行计数。 例如，可以验证状态输出中的以下详细信息：

```
Database Level
"migrationState": "READY_TO_COMPLETE" => Status of migration task. READY_TO_COMPLETE means database is ready for cutover
"incomingChanges": 0 => Check for a period of 5-10 minutes to ensure no new incoming changes need to be applied to the target server

Table Level (for each table)
"fullLoadTotalRows": 10    => The row count matches the initial row count of the table
"cdcDeleteCounter": 0      => Number of deletes after the full load
"cdcInsertCounter": 50     => Number of inserts after the full load
"cdcUpdateCounter": 0      => Number of updates after the full load
```

1. 使用以下命令执行直接转换数据库迁移任务：

    ```azurecli
    az dms project task cutover -h
    ```

    例如，以下命令将对“Inventory”数据库启动切换：

    ```azurecli
    az dms project task cutover --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name runnowtask  --object-name Inventory
    ```

2. 若要监视直接转换进度，请运行以下命令：

    ```azurecli
    az dms project task show --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name runnowtask
    ```
3. 当数据库迁移状态显示“已完成”时[重新创建序列](https://wiki.postgresql.org/wiki/Fixing_Sequences)（如适用），并将应用程序连接到 Azure Database for PostgreSQL 的新目标实例。

## <a name="service-project-task-cleanup"></a>服务、项目、任务清理

如需取消或删除任何 DMS 任务、项目或服务，请按以下顺序进行取消：

* 取消任何正在运行的任务
* 删除任务
* 删除项目
* 删除 DMS 服务

1. 若要取消正在运行的任务，请使用以下命令：

    ```azurecli
    az dms project task cancel --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name runnowtask
     ```

2. 若要删除正在运行的任务，请使用以下命令：
    ```azurecli
    az dms project task delete --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name runnowtask
    ```

3. 若要取消正在运行的项目，请使用以下命令：
     ```azurecli
    az dms project task cancel -n runnowtask --project-name PGMigration -g PostgresDemo --service-name PostgresCLI
     ```

4. 若要删除正在运行的项目，请使用以下命令：
    ```azurecli
    az dms project task delete -n runnowtask --project-name PGMigration -g PostgresDemo --service-name PostgresCLI
    ```

5. 若要删除 DMS 服务，请使用以下命令：

     ```azurecli
    az dms delete -g ProgresDemo -n PostgresCLI
     ```

## <a name="next-steps"></a>后续步骤

* 若要了解联机迁移到 Azure Database for PostgreSQL 时的已知问题和限制，请参阅 [Azure Database for PostgreSQL 联机迁移的已知问题和解决方法](known-issues-azure-postgresql-online.md)一文。
* 若要了解 Azure 数据库迁移服务，请参阅[什么是 Azure 数据库迁移服务？](./dms-overview.md)一文。
* 有关 Azure Database for PostgreSQL 的信息，请参阅[什么是 Azure Database for PostgreSQL？](../postgresql/overview.md)一文。