---
title: 将 Azure 数据库资源从 Azure 德国迁移到全球 Azure
description: 本文介绍如何将 Azure 数据库资源从 Azure 德国迁移到全球 Azure
ms.topic: article
ms.date: 03/29/2021
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfmigrate, devx-track-azurepowershell
ms.openlocfilehash: 6fe6d653712e034c13f3b755e906de491b4dd49a
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/29/2021
ms.locfileid: "117028998"
---
# <a name="migrate-database-resources-to-global-azure"></a>将数据库资源迁移到全球 Azure

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

本文中的信息可帮助你将 Azure 数据库资源从 Azure 德国迁移到全球 Azure。

## <a name="sql-database"></a>SQL 数据库

若要迁移较小的 Azure SQL 数据库工作负载且不使已迁移的数据库保持联机，请使用导出功能创建 BACPAC 文件。 BACPAC 文件是一个压缩 (zip) 文件，包含来自 SQL Server 数据库的元数据和数据。 创建 BACPAC 文件后，可将该文件复制到目标环境（例如，使用 AzCopy）并使用导入功能重新生成数据库。 请注意以下事项：

- 为使导出的内容在事务上保持一致，请确保符合以下条件之一：
  - 在导出过程中不会发生任何写入活动。
  - 从 SQL 数据库的事务一致性副本导出。
- 若要导出到 Azure Blob 存储，BACPAC 文件大小限制为 200 GB。 对于更大的 BACPAC 文件，请导出到本地存储。
- 如果从 SQL 数据库导出内容的操作所需的时间超过 20 小时，可能会取消该操作。 请查看以下文章，获取有关如何提高性能的提示。

> [!NOTE]
> 由于服务器的 DNS 名称在导出过程中会更改，完成导出操作后，连接字符串也会更改。

参考信息：

- 了解如何[将数据库导出到 BACPAC 文件](../azure-sql/database/database-export.md)。
- 了解如何[将 BACPAC 文件导入数据库](../azure-sql/database/database-import.md)。
- 查看 [Azure SQL 数据库文档](/azure/sql-database/)。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


## <a name="migrate-sql-database-using-active-geo-replication"></a>使用活动异地复制迁移 SQL 数据库

对于无法导出到 BACPAC 文件的过大数据库，或者要将数据库从一个云迁移到另一个云并使其保持联机，同时尽量减少迁移过程造成的停机时间，你可以配置从 Azure 德国到全球 Azure 的活动异地复制。

> [!IMPORTANT]
> 仅支持使用 Transact-SQL (T-SQL) 配置活动异地复制以将数据库迁移到全球 Azure，并且在迁移之前，必须请求启用订阅，以支持迁移到全球 Azure。 若要提交请求，必须使用[此支持请求链接](#requesting-access)。 

> [!Note]
> Azure 全球云区域“德国中西部”和“德国北部”是 Azure 德国云中支持活动异地复制的区域。 如果需要将某个备用全球 Azure 区域用作最终的数据库目标，在迁移到全球 Azure 完成后，建议配置一个从“德国中西部”或“德国北部”到所需 Azure 全球云区域的附加异地复制链接。

有关活动异地复制成本的详细信息，请参阅 [Azure SQL 数据库定价](https://azure.microsoft.com/pricing/details/sql-database/single/)中标题为“活动异地复制”的部分。

使用活动异地复制迁移数据库要求全球 Azure 中有一个 Azure SQL 逻辑服务器。 可以使用门户、Azure PowerShell、Azure CLI 等方式创建该服务器，但仅支持使用 Transact-SQL (T-SQL) 配置活动异地复制以从 Azure 德国迁移到全球 Azure。

> [!IMPORTANT]
> 在云之间迁移时，主要节点（Azure 德国）和辅助节点（全球 Azure）服务器名称前缀必须不同。 如果服务器名称相同，则运行 ALTER DATABASE 语句将会成功，但迁移会失败。 例如，如果主服务器名称的前缀为 `myserver` (`myserver.database.cloudapi.de`)，则全球 Azure 中辅助服务器名称的前缀不能是 `myserver`。


`ALTER DATABASE` 语句可让你指定全球 Azure 中的目标服务器，只需使用该服务器在目标端的完全限定 DNS 服务器名称即可。 


```sql
ALTER DATABASE [sourcedb] add secondary on server [public-server.database.windows.net]
```


- `sourcedb` 表示全球 Azure 中 Azure SQL 服务器上的数据库名称。 
- `public-server.database.windows.net` 表示存在于全球 Azure 中的 Azure SQL 服务器名称，数据库应迁移到其中。 命名空间“database.windows.net”是必需的。请将 public-server 替换为全球 Azure 中你的逻辑 SQL 服务器名称。 全球 Azure 中服务器的名称必须与 Azure 德国中的主服务器不同。


该命令是针对托管要迁移的本地数据库的 Azure 德国服务器上的 master 数据库执行的。 
- T-SQL start-copy API 通过查找该服务器的 master 数据库中具有相同 SQL 登录名/用户名的用户，对公有云服务器中已登录的用户进行身份验证。 这种方法不区分云；因此，T-SQL API 用于启动跨云复制。 有关权限和有关此主题的详细信息，请参阅[创建和使用活动异地复制](../azure-sql/database/active-geo-replication-overview.md)和 [ALTER DATABASE (Transact-SQL)](/sql/t-sql/statements/alter-database-transact-sql/)。
- 除了指示全球 Azure 中 Azure SQL 逻辑服务器的初始 T-SQL 命令扩展有所不同之外，活动异地复制过程的其余步骤与本地云中的现有执行是相同的。 有关创建活动异地复制的详细步骤，请参阅[创建和使用活动异地复制](../azure-sql/database/active-geo-replication-overview.md)，但请注意一种例外情况：辅助数据库是在全球 Azure 中创建的辅助逻辑服务器中创建的。 
- 一旦辅助数据库存在于全球 Azure 中（作为 Azure 德国数据库的联机副本），客户就可以使用 ALTER DATABASE T-SQL 命令对此数据库启动从 Azure 德国到全球 Azure 的数据库故障转移（参阅下表）。
- 故障转移后，一旦辅助数据库变成全球 Azure 中的主数据库，你就可以随时停止活动异地复制并删除 Azure 德国端的辅助数据库（参阅下表以及插图中所述的步骤）。 
- 故障转移后，Azure 德国中的辅助数据库在被删除之前会持续产生费用。
      
- 只能使用 `ALTER DATABASE` 命令设置活动异地复制以将 Azure 德国数据库迁移到全球 Azure。 
- 不能使用 Azure 门户、Azure 资源管理器、PowerShell 或 CLI 配置活动异地复制来完成这种迁移。 

若要将数据库从 Azure 德国迁移到全球 Azure，请执行以下操作：   

1.  选择 Azure 德国中的用户数据库，例如 `azuregermanydb`
2.  在全球 Azure（公有云）中创建逻辑服务器，例如 `globalazureserver`。 该服务器的完全限定域名 (FQDN) 为 `globalazureserver.database.windows.net`。
3.  在 Azure 德国的服务器上执行此 T-SQL 命令，启动从 Azure 德国到全球 Azure 的活动异地复制。 请注意，完全限定的 DNS 名称用于公共服务器 `globalazureserver.database.windows.net`。 这是为了指示目标服务器位于全球 Azure 而不是 Azure 德国中。

    ```sql
    ALTER DATABASE [azuregermanydb] ADD SECONDARY ON SERVER [globalazureserver.database.windows.net];
    ```

4.  当准备好通过复制将读写工作负载转移到全球 Azure 服务器时，请在全球 Azure 服务器上执行此 T-SQL 命令，以启动到全球 Azure 的计划内故障转移。

    ```sql
    ALTER DATABASE [azuregermanydb] FAILOVER;
    ```

5.  可以在执行故障转移过程之前或之后终止活动异地复制链接。 在完成计划内故障转移后执行以下 T-SQL 命令会删除与全球 Azure 中数据库（读写副本）之间的异地复制链接。 此命令应在当前异地主数据库的逻辑服务器上（即，在全球 Azure 服务器上）运行。 迁移过程到此完成。

    ```sql
    ALTER DATABASE [azuregermanydb] REMOVE SECONDARY ON SERVER [azuregermanyserver];
    ```

    在执行计划内故障转移之前执行以下 T-SQL 命令也会停止迁移过程，但在这种情况下，Azure 德国中的数据库仍保持为读写副本。 此 T-SQL 命令也应在当前异地主数据库的逻辑服务器（在本例中为 Azure 德国服务器）上运行。

    ```sql
    ALTER DATABASE [azuregermanydb] REMOVE SECONDARY ON SERVER [globalazureserver];
    ```

还可以使用活动异地复制来执行这些将 Azure SQL 数据库从 Azure 德国迁移到全球 Azure 的步骤。


有关详细信息，请查看下表，其中列出了用于管理故障转移的 T-SQL 命令。 支持使用以下命令在 Azure 德国与全球 Azure 之间进行跨云活动异地复制： 
 
|Command |说明|
|:--|:--| 
|[ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current&preserve-view=true) |使用 ADD SECONDARY ON SERVER 参数为现有数据库创建辅助数据库，并开始数据复制| 
|[ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current&preserve-view=true) |使用 FAILOVER 或 FORCE_FAILOVER_ALLOW_DATA_LOSS 将辅助数据库切换为主数据库，启动故障转移  |
|[ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current&preserve-view=true) |使用 REMOVE SECONDARY ON SERVER 终止 SQL 数据库和指定的辅助数据库之间的数据复制。  |
 
### <a name="active-geo-replication-monitoring-system-views"></a>活动异地复制监视系统视图 
 
|Command |说明|
|:--|:--| 
|[sys.geo_replication_links](/sql/relational-databases/system-dynamic-management-views/sys-geo-replication-links-azure-sql-database?view=azuresqldb-current&preserve-view=true)|返回有关 Azure SQL 数据库服务器上每个数据库的所有现有复制链接的信息。 |
|[sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database?view=azuresqldb-current&preserve-view=true) |获取有关给定 SQL 数据库的复制链路的上次复制时间、上次复制滞后时间和其他信息。 |
|[sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database?view=azuresqldb-current&preserve-view=true) | 显示所有数据库操作的状态，包括复制链接的状态。 | 
|[sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync?view=azuresqldb-current&preserve-view=true) | 使应用程序等待，直到所有提交的事务已复制，并由活动辅助数据库确认。 |
 

## <a name="migrate-sql-database-long-term-retention-backups"></a>迁移 Azure SQL 数据库长期保留备份

使用异地复制或 BACPAC 文件迁移某个数据库时，不会复制该数据库在 Azure 德国中的长期保留备份。 若要将现有的长期保留备份迁移到目标全球 Azure 区域，可以使用“复制长期保留备份”过程。 

>[!Note]
>此处所述的 LTR 备份复制方法只能将 LTR 备份从 Azure 德国复制到全球 Azure。 不支持使用这些方法复制 PITR 备份。 
>

### <a name="pre-requisites"></a>先决条件

1. 在开始复制 LTR 备份之前，全球 Azure 中的、要将这些备份复制到的目标数据库必须存在。 建议先使用[活动异地复制](#migrate-sql-database-using-active-geo-replication)迁移源数据库，然后再启动 LTR 备份复制。 这会确保将数据库备份复制到正确的目标数据库。 如果要复制已删除的数据库的 LTR 备份，则不需要执行此步骤。 复制已删除的数据库的 LTR 备份时，将在目标区域中创建一个虚拟的 DatabaseID。 
2. 安装此 [PowerShell Az 模块](https://www.powershellgallery.com/packages/Az.Sql/3.0.0-preview)
3. 在开始之前，请确保在订阅或资源组范围授予所需的 [Azure RBAC 角色](../azure-sql/database/long-term-backup-retention-configure.md#azure-roles-to-manage-long-term-retention) 。 注意：若要访问属于已删除的服务器的 LTR 备份，必须在该服务器的订阅范围内授予权限。 . 


### <a name="limitations"></a>限制  

- 不支持故障转移组。 这意味着，迁移 Azure 德国数据库的客户需要在故障转移期间自行管理连接字符串。
- 不支持 Azure 门户、Azure 资源管理器 API、PowerShell 或 CLI。 这意味着，每次进行 Azure 德国迁移都需要通过 T-SQL 管理活动异地复制设置和故障转移。
- 客户无法在全球 Azure 中为 Azure 德国数据库创建多个异地辅助副本。
- 必须从 Azure 德国区域启动异地辅助副本的创建。
- 对于 Azure 德国中的数据库，客户只能将其迁移到全球 Azure。 目前不支持其他跨云迁移。 
- Azure 德国用户数据库中的 Azure AD 用户将会迁移，但在迁移的数据库所在的新 Azure AD 租户中不可用。 若要启用这些用户，必须手动将其删除，然后使用新迁移的数据库所在的新 Azure AD 租户中可用的当前 Azure AD 用户重新创建这些用户。  


### <a name="copy-long-term-retention-backups-using-powershell"></a>使用 PowerShell 复制长期保留备份

已引入新的 PowerShell 命令 Copy-AzSqlDatabaseLongTermRetentionBackup，使用该命令可将长期保留备份从 Azure 德国复制到 Azure 全球区域。 

1. 使用 backupname 复制 LTR 备份。以下示例演示如何使用 backupname 将 LTR 备份从 Azure 德国复制到 Azure 全球区域。  

```powershell
# Source database and target database info
$location = "<location>"
$sourceRGName = "<source resourcegroup name>"
$sourceServerName = "<source server name>"
$sourceDatabaseName = "<source database name>"
$backupName = "<backup name>"
$targetDatabaseName = "<target database name>"
$targetSubscriptionId = "<target subscriptionID>"
$targetRGName = "<target resource group name>"
$targetServerFQDN = "<targetservername.database.windows.net>"

Copy-AzSqlDatabaseLongTermRetentionBackup 
    -Location $location 
    -ResourceGroupName $sourceRGName 
    -ServerName $sourceServerName 
    -DatabaseName $sourceDatabaseName
    -BackupName $backupName
    -TargetDatabaseName $targetDatabaseName 
    -TargetSubscriptionId $targetSubscriptionId
    -TargetResourceGroupName $targetRGName
    -TargetServerFullyQualifiedDomainName $targetServerFQDN 
```

2. 使用备份 resourceID 复制 LTR 备份。以下示例演示如何使用备份 resourceID 将 LTR 备份从 Azure 德国复制到 Azure 全球区域。 此示例也可用于复制已删除的数据库的备份。 

```powershell
$location = "<location>"
# list LTR backups for All databases (you have option to choose All/Live/Deleted)
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $location -DatabaseState All

# select the LTR backup you want to copy
$ltrBackup = $ltrBackups[0]
$resourceID = $ltrBackup.ResourceId

# Source Database and target database info
$targetDatabaseName = "<target database name>"
$targetSubscriptionId = "<target subscriptionID>"
$targetRGName = "<target resource group name>"
$targetServerFQDN = "<targetservername.database.windows.net>"

Copy-AzSqlDatabaseLongTermRetentionBackup 
    -ResourceId $resourceID 
    -TargetDatabaseName $targetDatabaseName 
    -TargetSubscriptionId $targetSubscriptionId
    -TargetResourceGroupName $targetRGName
    -TargetServerFullyQualifiedDomainName $targetServerFQDN
```


### <a name="limitations"></a>限制  

- 只会在主数据库上创建[时间点还原 (PITR)](../azure-sql/database/recovery-using-backups.md#point-in-time-restore) 备份，这是设计使然。 使用异地灾难恢复从 Azure 德国迁移数据库时，在故障转移后，将开始在新的主数据库上进行 PITR 备份。 但是，不会迁移（Azure 德国中先前的主数据库上的）现有 PITR 备份。 如果需要使用 PITR 备份来支持任何时间点还原方案，则需要从 Azure 德国的 PITR 备份还原数据库，然后将恢复的数据库迁移到全球 Azure。 
- 长期保留策略不会随数据库一起迁移。 如果你有针对 Azure 德国中数据库的[长期保留 (LTR)](../azure-sql/database/long-term-retention-overview.md) 策略，则迁移后需要针对新数据库重新创建 LTR 策略。 


### <a name="requesting-access"></a>请求访问权限

若要使用异地复制将数据库从 Azure 德国迁移到全球 Azure，需要启用 Azure 德国中的订阅才能成功配置跨云迁移。

若要启用 Azure 德国订阅，必须使用以下链接创建迁移支持请求：   

1. 浏览到以下[迁移支持请求](https://portal.microsoftazure.de/#create/Microsoft.Support/Parameters/%7B%0D%0A++++%22pesId%22%3A+%22f3dc5421-79ef-1efa-41a5-42bf3cbb52c6%22%2C%0D%0A++++%22supportTopicId%22%3A+%229fc72ed5-805f-3894-eb2b-b1f1f6557d2d%22%2C%0D%0A++++%22contextInfo%22%3A+%22Migration+from+cloud+Germany+to+Azure+global+cloud+%28Azure+SQL+Database%29%22%2C%0D%0A++++%22caller%22%3A+%22NoSupportPlanCloudGermanyMigration%22%2C%0D%0A++++%22severity%22%3A+%223%22%0D%0A%7D)。

2. 在“基本信息”选项卡上，输入“异地灾难恢复迁移”作为“摘要”，然后选择“下一步: 解决方案” 
 
   :::image type="content" source="media/germany-migration-databases/support-request-basics.png" alt-text="“新建支持请求”表单":::

3. 查看“建议的步骤”，然后选择“下一步: 详细信息” 。 

   :::image type="content" source="media/germany-migration-databases/support-request-solutions.png" alt-text="所需的支持请求信息":::

4. 在“详细信息”页上提供以下信息：

   1. 在“说明”框中，输入要迁移到的全球 Azure 订阅 ID。 若要将数据库迁移到多个订阅，请添加要将数据库迁移到的全球 Azure ID 列表。
   1. 提供联系信息：姓名、公司名称、电子邮件地址或电话号码。
   1. 填写表单，然后选择“下一步: 查看 + 创建”。

   :::image type="content" source="media/germany-migration-databases/support-request-details.png" alt-text="支持请求详细信息":::


5. 查看支持请求，然后选择“创建”。 


处理该请求后，我们将与你联系。



## <a name="azure-cosmos-db"></a>Azure Cosmos DB

可以使用 Azure Cosmos DB 数据迁移工具将数据迁移到 Azure Cosmos DB。 Azure Cosmos DB 数据迁移工具是一个开源解决方案，可将数据从不同的源导入 Azure Cosmos DB，这些源包括：JSON 文件、MongoDB、SQL Server、CSV 文件、Azure 表存储、Amazon DynamoDB、HBase 和 Azure Cosmos 容器。


Azure Cosmos DB 数据迁移工具以图形界面工具或命令行工具的形式提供。 [Azure Cosmos DB 数据迁移工具](https://github.com/azure/azure-documentdb-datamigrationtool) GitHub 存储库中提供了源代码。 Microsoft 下载中心提供了[该工具的编译版](https://www.microsoft.com/download/details.aspx?id=46436)。

若要迁移 Azure Cosmos DB 资源，建议完成以下步骤：

1. 查看应用程序运行时间要求和帐户配置，以确定最佳操作计划。
1. 运行数据迁移工具，将 Azure 德国中的帐户配置克隆到新区域。
1. 如果可以使用维护时段，请运行数据迁移工具以将源中的数据复制到目标。
1. 如果无法使用维护时段，请运行该工具以将源中的数据复制到目标，然后完成以下步骤：
   1. 使用配置驱动的方法对应用程序中的读取/写入进行更改。
   1. 完成首次同步。
   1. 设置增量同步并与更改源同步。
   1. 将读取指向新帐户，并验证应用程序。
   1. 停止写入旧帐户，验证是否已同步更改源，然后将写入指向新帐户。
   1. 停止该工具并删除旧帐户。
1. 运行该工具，以验证新旧帐户之间的数据是否一致。

参考信息：

- 若要了解如何使用数据迁移工具，请参阅[教程：使用数据迁移工具将数据迁移到 Azure Cosmos DB](../cosmos-db/import-data.md)。
- 若要了解 Cosmos DB，请参阅[欢迎使用 Azure Cosmos DB](../cosmos-db/introduction.md)。


## <a name="azure-cache-for-redis"></a>用于 Redis 的 Azure 缓存

可以使用几个选项将 Azure Cache for Redis 实例从 Azure 德国迁移到全球 Azure。 选择哪个选项取决于你的要求。

### <a name="option-1-accept-data-loss-create-a-new-instance"></a>选项 1：接受数据丢失，创建新实例

如果以下两种情况都属实，则此方法的效果最好：

- 使用 Azure Cache for Redis 作为暂时性数据缓存。
- 应用程序自动在新区域中重新填充缓存数据。

若要在接受数据丢失的情况下进行迁移并创建新实例，请执行以下操作：

1. 在新的目标区域中创建新的 Azure Cache for Redis 实例。
1. 将应用程序更新为使用新区域中的新实例。
1. 删除源区域中的旧 Azure Cache for Redis 实例。

### <a name="option-2-copy-data-from-the-source-instance-to-the-target-instance"></a>选项 2：将数据从源实例复制到目标实例

Azure Cache for Redis 团队的某位成员编写了一个开源工具，用于将数据从一个 Azure Cache for Redis 实例复制到另一个实例，而无需使用导入或导出功能。 有关该工具的信息，请参阅下面的步骤 4。

若要将数据从源实例复制到目标实例，请执行以下操作：

1. 在源区域中创建一个 VM。 如果 Azure Cache for Redis 中的数据集很大，请确保选择相对强大的 VM 大小以最大程度地减少复制时间。
1. 在目标区域中创建新的 Azure Cache for Redis 实例。
1. 从目标实例中刷新数据。 （请务必不要从源实例刷新。 之所以需要刷新，是因为复制工具不会覆盖目标位置的现有键。）
1. 使用以下工具自动将数据从源 Azure Cache for Redis 实例复制到目标 Azure Cache for Redis 实例：[工具源](https://github.com/deepakverma/redis-copy)和[工具下载](https://github.com/deepakverma/redis-copy/releases/download/alpha/Release.zip)。

> [!NOTE]
> 此过程可能需要很长时间才能完成，具体取决于数据集的大小。

### <a name="option-3-export-from-the-source-instance-import-to-the-destination-instance"></a>选项 3：从源实例导出，然后导入到目标实例

此方法利用仅在高级层中提供的功能。

若要从源实例导出，然后导入到目标实例，请执行以下操作：

1. 在目标区域中创建新的高级层 Azure Cache for Redis 实例。 使用与源 Azure Cache for Redis 实例相同的大小。
1. [从源缓存导出数据](../azure-cache-for-redis/cache-how-to-import-export-data.md)，或使用 [Export-AzRedisCache PowerShell cmdlet](/powershell/module/az.rediscache/export-azrediscache)。

   > [!NOTE]
   > 导出的 Azure 存储帐户必须与缓存实例位于同一区域。

1. 将导出的 Blob 复制到目标区域中的存储帐户（例如，使用 AzCopy）。
1. [将数据导入到目标缓存](../azure-cache-for-redis/cache-how-to-import-export-data.md)，或使用 [Import-AzRedisCAche PowerShell cmdlet](/powershell/module/az.rediscache/import-azrediscache)。
1. 重新配置应用程序以使用目标 Azure Cache for Redis 实例。

### <a name="option-4-write-data-to-two-azure-cache-for-redis-instances-read-from-one-instance"></a>选项 4：将数据写入两个 Azure Cache for Redis 实例，并从一个实例读取

要使用此方法，必须修改你的应用程序。 应用程序需要将数据写入多个缓存实例，并从其中一个缓存实例读取。 如果存储在 Azure Cache for Redis 中的数据符合以下条件，则此方法会很有作用：
- 数据定期刷新。 
- 所有数据将写入目标 Azure Cache for Redis 实例。
- 你有足够的时间刷新所有数据。

参考信息：

- 查看 [Azure Cache for Redis 概述](../azure-cache-for-redis/cache-overview.md)。

## <a name="postgresql-and-mysql"></a>PostgreSQL 和 MySQL

有关详细信息，请参阅 [PostgreSQL](../postgresql/index.yml)和 [MySQL](../mysql/index.yml) 的“备份和迁移数据”部分中列出的文章。

![PostgreSQL 和 MySQL](./media/germany-migration-main/databases.png)

## <a name="next-steps"></a>后续步骤

了解用于在以下服务类别中迁移资源的工具、方法和建议：

- [计算](./germany-migration-compute.md)
- [联网](./germany-migration-networking.md)
- [存储](./germany-migration-storage.md)
- [Web](./germany-migration-web.md)
- [分析](./germany-migration-analytics.md)
- [IoT](./germany-migration-iot.md)
- [集成](./germany-migration-integration.md)
- [标识](./germany-migration-identity.md)
- [安全性](./germany-migration-security.md)
- [管理工具](./germany-migration-management-tools.md)
- [介质](./germany-migration-media.md)
