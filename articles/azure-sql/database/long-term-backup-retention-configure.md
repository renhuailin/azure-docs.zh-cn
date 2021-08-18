---
title: Azure SQL 数据库：管理长期备份保留
description: 了解如何使用 Azure 门户和 PowerShell 在 Azure 存储中存储和还原 Azure SQL 数据库的自动备份（最长可达 10 年）
services: sql-database
ms.service: sql-db-mi
ms.subservice: backup-restore
ms.custom: devx-track-azurepowershell
ms.devlang: ''
ms.topic: how-to
author: SQLSourabh
ms.author: sourabha
ms.reviewer: mathoma
ms.date: 12/16/2020
ms.openlocfilehash: 343b015f2c28e48105b6c76d30ebcc81f9b94b53
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121730432"
---
# <a name="manage-azure-sql-database-long-term-backup-retention"></a>管理 Azure SQL 数据库长期备份保留
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

借助 Azure SQL 数据库，可以设置[长期备份保留](long-term-retention-overview.md)策略 (LTR) 以自动将备份保留在单独的 Azure Blob 存储容器中最多 10 年。 然后，可以通过 Azure 门户或 PowerShell 使用这些备份来恢复数据库。 [Azure SQL 托管实例](../managed-instance/long-term-backup-retention-configure.md)也支持长期保留策略。

## <a name="using-the-azure-portal"></a>使用 Azure 门户

以下各部分说明如何使用 Azure 门户设置长期保留策略、管理可用的长期保留备份以及从可用备份进行还原。

### <a name="configure-long-term-retention-policies"></a>配置长期保留策略

可以对 SQL 数据库进行配置，使其[保留自动备份](long-term-retention-overview.md)的时间长于你的服务层级的保留期。

1. 在 Azure 门户中，导航到服务器，然后选择“备份”。 选择“保留策略”选项卡以修改备份保留设置。

   ![保留策略体验](./media/long-term-backup-retention-configure/ltr-policies-tab.png)

2. 在“保留策略”选项卡上，选择要为其设置或修改长期备份保留策略的数据库。 未选定的数据库不受影响。

   ![选择数据库以配置备份保留策略](./media/long-term-backup-retention-configure/ltr-policies-tab-configure.png)

3. 在“配置策略”窗格中，为每周、每月或每年备份指定所需的保留期。 选择的保留期为“0”表明不应设置长期备份保留期。

   ![“配置策略”窗格](./media/long-term-backup-retention-configure/ltr-configure-policies.png)

4. 选择“应用”以将所选保留设置应用于所有选定数据库。

> [!IMPORTANT]
> 启用长期备份保留策略时，最长可能需要 7 天以后才能查看和还原第一个备份。 有关 LTR 备份频率的详细信息，请参阅[长期备份保留](long-term-retention-overview.md)。

### <a name="view-backups-and-restore-from-a-backup"></a>查看备份并从备份进行还原

查看通过 LTR 策略为特定数据库保留的备份，并从这些备份进行还原。

1. 在 Azure 门户中，导航到服务器，然后选择“备份”。 若要查看特定数据库的可用 LTR 备份，请在“可用 LTR 备份”列下选择“管理”。 将显示一个窗格，其中包含所选数据库的可用 LTR 备份的列表。

   ![可用备份体验](./media/long-term-backup-retention-configure/ltr-available-backups-tab.png)

1. 在显示的“可用 LTR 备份”窗格中，查看可用备份。 可以选择要从中进行还原或删除的备份。

   ![查看可用 LTR 备份](./media/long-term-backup-retention-configure/ltr-available-backups-manage.png)

1. 若要从可用 LTR 备份进行还原，请选择要从中进行还原的备份，然后选择“还原”。

   ![从可用 LTR 备份进行还原](./media/long-term-backup-retention-configure/ltr-available-backups-restore.png)

1. 选择新数据库的名称，然后选择“查看 + 创建”以查看还原的详细信息。 选择“创建”以从所选备份还原数据库。

   ![配置还原详细信息](./media/long-term-backup-retention-configure/restore-ltr.png)

1. 在工具栏上，选择通知图标可查看还原作业的状态。

   ![还原作业进度](./media/long-term-backup-retention-configure/restore-job-progress-long-term.png)

1. 完成还原作业后，打开“SQL 数据库”页面以查看新还原的数据库。

> [!NOTE]
> 可以在此处使用 SQL Server Management Studio 连接到还原的数据库以执行所需的任务，例如 [从还原的数据库提取一些数据，以便将其复制到现有的数据库中；或者删除现有的数据库，并将还原的数据库重命名为现有的数据库名称](recovery-using-backups.md#point-in-time-restore)。

## <a name="using-powershell"></a>使用 PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> PowerShell Azure 资源管理器模块仍受 Azure SQL 数据库的支持，但所有未来的开发都是针对 Az.Sql 模块的。 若要了解这些 cmdlet，请参阅 [AzureRM.Sql](/powershell/module/AzureRM.Sql/)。 Az 模块和 AzureRm 模块中的命令参数大体上是相同的。

以下各部分展示了如何使用 PowerShell 配置长期备份保留、查看 Azure 存储中的备份，以及从 Azure 存储中的备份进行还原。

### <a name="azure-roles-to-manage-long-term-retention"></a>用于管理长期保留的 Azure 角色

对于 **Get-AzSqlDatabaseLongTermRetentionBackup** 和 **Restore-AzSqlDatabase**，需要具有以下角色之一：

- “订阅所有者”角色或
- “SQL Server 参与者”角色或
- 具有以下权限的自定义角色：

   Microsoft.Sql/locations/longTermRetentionBackups/read  Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionBackups/read  Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/read

对于 **Remove-AzSqlDatabaseLongTermRetentionBackup**，需要具有以下角色之一：

- “订阅所有者”角色或
- 具有以下权限的自定义角色：

   Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete

> [!NOTE]
> “SQL Server 参与者”角色没有删除 LTR 备份的权限。

可以在“订阅”或“资源组”范围内授予 Azure RBAC 权限。 但是，若要访问属于已删除服务器的 LTR 备份，必须在该服务器的“订阅”范围内授予此权限。

- Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete

### <a name="create-an-ltr-policy"></a>创建 LTR 策略

```powershell
# get the SQL server
$subId = "<subscriptionId>"
$serverName = "<serverName>"
$resourceGroup = "<resourceGroupName>"
$dbName = "<databaseName>"

Connect-AzAccount
Select-AzSubscription -SubscriptionId $subId

$server = Get-AzSqlServer -ServerName $serverName -ResourceGroupName $resourceGroup

# create LTR policy with WeeklyRetention = 12 weeks. MonthlyRetention and YearlyRetention = 0 by default.
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -WeeklyRetention P12W

# create LTR policy with WeeklyRetention = 12 weeks, YearlyRetention = 5 years and WeekOfYear = 16 (week of April 15). MonthlyRetention = 0 by default.
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -WeeklyRetention P12W -YearlyRetention P5Y -WeekOfYear 16
```

### <a name="view-ltr-policies"></a>查看 LTR 策略

此示例展示了如何列出服务器内的 LTR 策略

```powershell
# get all LTR policies within a server
$ltrPolicies = Get-AzSqlDatabase -ResourceGroupName $resourceGroup -ServerName $serverName | `
    Get-AzSqlDatabaseLongTermRetentionPolicy

# get the LTR policy of a specific database
$ltrPolicies = Get-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup
```

### <a name="clear-an-ltr-policy"></a>清除 LTR 策略

此示例展示了如何从数据库中清除 LTR 策略

```powershell
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -RemovePolicy
```

### <a name="view-ltr-backups"></a>查看 LTR 备份

此示例展示了如何列出服务器内的 LTR 备份。

```powershell
# get the list of all LTR backups in a specific Azure region
# backups are grouped by the logical database id, within each group they are ordered by the timestamp, the earliest backup first
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location

# get the list of LTR backups from the Azure region under the named server
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName

# get the LTR backups for a specific database from the Azure region under the named server
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName -DatabaseName $dbName

# list LTR backups only from live databases (you have option to choose All/Live/Deleted)
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -DatabaseState Live

# only list the latest LTR backup for each database
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName -OnlyLatestPerDatabase
```

### <a name="delete-ltr-backups"></a>删除 LTR 备份

此示例展示了如何从备份列表中删除 LTR 备份。

```powershell
# remove the earliest backup
$ltrBackup = $ltrBackups[0]
Remove-AzSqlDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```

> [!IMPORTANT]
> 删除 LTR 备份操作是不可逆的。 若要在删除服务器后删除 LTR 备份，必须具有“订阅”范围权限。 可以通过筛选“删除长期保留备份”操作，在 Azure Monitor 中设置有关每次删除的通知。 活动日志包含有关发出请求的人员和时间的信息。 有关详细说明，请参阅[创建活动日志警报](../../azure-monitor/alerts/alerts-activity-log.md)。

### <a name="restore-from-ltr-backups"></a>从 LTR 备份进行还原

此示例展示了如何从 LTR 备份进行还原。 请注意，此接口没有更改，但是资源 ID 参数现在要求提供 LTR 备份资源 ID。

```powershell
# restore a specific LTR backup as an P1 database on the server $serverName of the resource group $resourceGroup
Restore-AzSqlDatabase -FromLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId -ServerName $serverName -ResourceGroupName $resourceGroup `
    -TargetDatabaseName $dbName -ServiceObjectiveName P1
```

> [!IMPORTANT]
> 若要在删除服务器或资源组后从 LTR 备份进行还原，必须具有服务器订阅范围内的权限，并且该订阅必须处于活动状态。 还必须省略可选的 -ResourceGroupName 参数。

> [!NOTE]
> 从此处，可使用 SQL Server Management Studio 连接到已还原的数据库，执行所需任务，例如从恢复的数据库中提取一部分数据，复制到现有数据库或删除现有数据库，并将已还原的数据库重命名为现有数据库名。 请参阅[时间点还原](recovery-using-backups.md#point-in-time-restore)。

## <a name="limitations"></a>限制
- 从 LTR 备份进行还原时，“读取扩展”属性处于禁用状态。 若要在还原的数据库上启用读取扩展，请在数据库创建完后对其进行更新。
- 从 LTR 备份（在数据库位于弹性池时创建）进行还原时，需要指定目标服务级别目标。 

## <a name="next-steps"></a>后续步骤

- 若要了解服务生成的自动备份，请参阅 [自动备份](automated-backups-overview.md)
- 若要了解长期备份保留，请参阅 [长期备份保留](long-term-retention-overview.md)
