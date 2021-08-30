---
title: Azure SQL 托管实例：长期备份保留
description: 了解如何使用 Azure 门户和 PowerShell 为 Azure SQL 托管实例在单独的 Azure Blob 存储容器上存储和还原自动备份。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: backup-restore
ms.custom: devx-track-azurepowershell
ms.devlang: ''
ms.topic: how-to
author: SQLSourabh
ms.author: sourabha
ms.reviewer: mathoma
ms.date: 07/13/2021
ms.openlocfilehash: cc25b431c6e1eb4cbb6b31cfc5f9b1a1a26f8a03
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121751244"
---
# <a name="manage-azure-sql-managed-instance-long-term-backup-retention"></a>管理 Azure SQL 托管实例长期备份保留
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

在 Azure SQL 托管实例中，可以配置[长期备份保留](../database/long-term-retention-overview.md)策略 (LTR)，这是一项公共预览功能。 这样就可以在单独的 Azure Blob 存储容器中自动将数据库备份保留长达 10 年的时间。 然后，可以通过 Azure 门户和 PowerShell 使用这些备份来恢复数据库。

   > [!IMPORTANT]
   > 托管实例的 LTR 当前在 Azure 公共区域中以公共预览版提供。 

以下各部分展示了如何使用 Azure 门户和 PowerShell 配置长期备份保留、查看 Azure SQL 存储中的备份，以及从 Azure SQL 存储中的备份进行还原。


## <a name="using-the-azure-portal"></a>使用 Azure 门户

以下各部分说明如何使用 Azure 门户设置长期保留策略、管理可用的长期保留备份以及从可用备份进行还原。

### <a name="configure-long-term-retention-policies"></a>配置长期保留策略

可以对 SQL 托管实例进行配置，使其[保留自动备份](../database/long-term-retention-overview.md)的时间长于你的服务层级的保留期。

1. 在 Azure 门户中，选择你的托管实例，然后单击“备份”。 在“保留策略”选项卡上，选择要为其设置或修改长期备份保留策略的数据库。 更改将不会应用于未选择的任何数据库。 

   ![管理备份链接](./media/long-term-backup-retention-configure/ltr-configure-ltr.png)

2. 在“配置策略”窗格中，为每周、每月或每年备份指定所需的保留期。 选择的保留期为“0”表明不应设置长期备份保留期。

   ![配置策略](./media/long-term-backup-retention-configure/ltr-configure-policies.png)

3. 完成后，单击“应用”。

> [!IMPORTANT]
> 启用长期备份保留策略时，最长可能需要 7 天以后才能查看和还原第一个备份。 有关 LTR 备份频率的详细信息，请参阅[长期备份保留](../database/long-term-retention-overview.md)。

### <a name="view-backups-and-restore-from-a-backup"></a>查看备份并从备份进行还原

查看通过 LTR 策略为特定数据库保留的备份，并从这些备份进行还原。

1. 在 Azure 门户中，选择你的托管实例，然后单击“备份”。 在“可用备份”选项卡上，选择要查看其可用备份的数据库。 单击“管理”。 

   ![选择数据库](./media/long-term-backup-retention-configure/ltr-available-backups-select-database.png)

1. 在“管理备份”窗格中，查看可用备份。

   ![查看备份](./media/long-term-backup-retention-configure/ltr-available-backups.png)

1. 选择要从中进行还原的备份，单击“还原”，然后在还原页面上指定新的数据库名称。 备份和源将在此页面上预填充。 

   ![选择要还原的备份](./media/long-term-backup-retention-configure/ltr-available-backups-restore.png)
   
   ![还原](./media/long-term-backup-retention-configure/ltr-restore.png)

1. 单击“审阅并创建”查看还原详细信息。 然后单击“创建”，以便从所选备份还原数据库。

1. 在工具栏上，单击通知图标可查看还原作业的状态。

   ![还原作业进度](./media/long-term-backup-retention-configure/restore-job-progress-long-term.png)

1. 完成还原作业后，打开“托管实例概述”页面以查看新还原的数据库。

> [!NOTE]
> 可以在此处使用 SQL Server Management Studio 连接到还原的数据库以执行所需的任务，例如 [从还原的数据库提取一些数据，以便将其复制到现有的数据库中；或者删除现有的数据库，并将还原的数据库重命名为现有的数据库名称](../database/recovery-using-backups.md#point-in-time-restore)。


## <a name="using-powershell"></a>使用 PowerShell
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> Azure SQL 数据库仍然支持 PowerShell Azure 资源管理器模块，但后续开发将在 Az.Sql 模块中完成。 若要了解这些 cmdlet，请参阅 [AzureRM.Sql](/powershell/module/AzureRM.Sql/)。 Az 模块和 AzureRm 模块中的命令参数大体上是相同的。

以下各部分展示了如何使用 PowerShell 配置长期备份保留、查看 Azure 存储中的备份，以及从 Azure 存储中的备份进行还原。

### <a name="azure-rbac-roles-to-manage-long-term-retention"></a>用于管理长期保留的 Azure RBAC 角色

对于 Get-AzSqlInstanceDatabaseLongTermRetentionBackup 和 Restore-AzSqlInstanceDatabase，你需要有以下角色之一：

- “订阅所有者”角色或
- “托管实例参与者”角色或
- 具有以下权限的自定义角色：
  - `Microsoft.Sql/locations/longTermRetentionManagedInstanceBackups/read`
  - `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionManagedInstanceBackups/read`
  - `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/read`

对于 Remove-AzSqlInstanceDatabaseLongTermRetentionBackup，需要具有以下角色之一：

- “订阅所有者”角色或
- 具有以下权限的自定义角色：
  - `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/delete`

> [!NOTE]
> “托管实例参与者”角色没有删除 LTR 备份的权限。

可以在“订阅”或“资源组”范围内授予 Azure RBAC 权限。 但是，若要访问属于已删除实例的 LTR 备份，必须在该实例的“订阅”范围内授予此权限。

- `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/delete`

### <a name="create-an-ltr-policy"></a>创建 LTR 策略

```powershell
# get the Managed Instance
$subId = "<subscriptionId>"
$instanceName = "<instanceName>"
$resourceGroup = "<resourceGroupName>"
$dbName = "<databaseName>"

Connect-AzAccount

Select-AzSubscription -SubscriptionId $subId

$instance = Get-AzSqlInstance -Name $instanceName -ResourceGroupName $resourceGroup

# create LTR policy with WeeklyRetention = 12 weeks. MonthlyRetention and YearlyRetention = 0 by default.
$LTRPolicy = @{
    InstanceName = $instanceName 
    DatabaseName = $dbName 
    ResourceGroupName = $resourceGroup 
    WeeklyRetention = 'P12W'
}
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy @LTRPolicy

# create LTR policy with WeeklyRetention = 12 weeks, YearlyRetention = 5 years and WeekOfYear = 16 (week of April 15). MonthlyRetention = 0 by default.
$LTRPolicy = @{
    InstanceName = $instanceName 
    DatabaseName = $dbName 
    ResourceGroupName = $resourceGroup 
    WeeklyRetention = 'P12W' 
    YearlyRetention = 'P5Y' 
    WeekOfYear = '16'
}
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy @LTRPolicy
```

### <a name="view-ltr-policies"></a>查看 LTR 策略

此示例展示了如何列出单个数据库的实例内的 LTR 策略

```powershell
# gets the current version of LTR policy for a database
$LTRPolicies = @{
    InstanceName = $instanceName 
    DatabaseName = $dbName 
    ResourceGroupName = $resourceGroup
}
Get-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy @LTRPolicy 
```

此示例演示如何列出实例上所有数据库的 LTR 策略

```powershell
# gets the current version of LTR policy for all of the databases on an instance

$Databases = Get-AzSqlInstanceDatabase -ResourceGroupName $resourceGroup -InstanceName $instanceName

$LTRParams = @{
    InstanceName = $instanceName
    ResourceGroupName = $resourceGroup
}

foreach($database in $Databases.Name){
    Get-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy @LTRParams  -DatabaseName $database
 }
```

### <a name="clear-an-ltr-policy"></a>清除 LTR 策略

此示例展示了如何从数据库中清除 LTR 策略

```powershell
# remove the LTR policy from a database
$LTRPolicy = @{
    InstanceName = $instanceName 
    DatabaseName = $dbName 
    ResourceGroupName = $resourceGroup 
    RemovePolicy = $true
}
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy @LTRPolicy
```

### <a name="view-ltr-backups"></a>查看 LTR 备份

此示例展示了如何列出实例内的 LTR 备份。

```powershell

$instance = Get-AzSqlInstance -Name $instanceName -ResourceGroupName $resourceGroup

# get the list of all LTR backups in a specific Azure region
# backups are grouped by the logical database id, within each group they are ordered by the timestamp, the earliest backup first
Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location

# get the list of LTR backups from the Azure region under the given managed instance
$LTRBackupParam = @{
    Location = $instance.Location 
    InstanceName = $instanceName
}
Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam

# get the LTR backups for a specific database from the Azure region under the given managed instance
$LTRBackupParam = @{
    Location = $instance.Location 
    InstanceName = $instanceName
    DatabaseName = $dbName
}
Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam

# list LTR backups only from live databases (you have option to choose All/Live/Deleted)
$LTRBackupParam = @{
    Location = $instance.Location 
    DatabaseState = 'Live'
}
Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam

# only list the latest LTR backup for each database
$LTRBackupParam = @{
    Location = $instance.Location 
    InstanceName = $instanceName
    OnlyLatestPerDatabase = $true
}
Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam 
```

### <a name="delete-ltr-backups"></a>删除 LTR 备份

此示例展示了如何从备份列表中删除 LTR 备份。

```powershell
# remove the earliest backup
# get the LTR backups for a specific database from the Azure region under the given managed instance
$LTRBackupParam = @{
    Location = $instance.Location 
    InstanceName = $instanceName
    DatabaseName = $dbName
}
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam
$ltrBackup = $ltrBackups[0]
Remove-AzSqlInstanceDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```

> [!IMPORTANT]
> 删除 LTR 备份操作是不可逆的。 若要在删除实例后删除 LTR 备份，必须有“订阅”范围权限。 可以通过筛选“删除长期保留备份”操作，在 Azure Monitor 中设置有关每次删除的通知。 活动日志包含有关发出请求的人员和时间的信息。 有关详细说明，请参阅[创建活动日志警报](../../azure-monitor/alerts/alerts-activity-log.md)。

### <a name="restore-from-ltr-backups"></a>从 LTR 备份进行还原

此示例展示了如何从 LTR 备份进行还原。 请注意，此接口没有更改，但是资源 ID 参数现在要求提供 LTR 备份资源 ID。

```powershell
# restore a specific LTR backup as an P1 database on the instance $instanceName of the resource group $resourceGroup
$LTRBackupParam = @{
    Location = $instance.Location 
    InstanceName = $instanceName
    DatabaseName = $dbname
    OnlyLatestPerDatabase = $true
}
$ltrBackup = Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam 

$RestoreLTRParam = @{
    TargetInstanceName          = $instanceName 
    TargetResourceGroupName     = $resourceGroup 
    TargetInstanceDatabaseName  = $dbName
    FromLongTermRetentionBackup = $true
    ResourceId                  = $ltrBackup.ResourceId 
}
Restore-AzSqlInstanceDatabase @RestoreLTRParam
```

> [!IMPORTANT]
> 若要在删除实例后从 LTR 备份进行还原，必须有实例订阅范围内的权限，并且该订阅必须处于活动状态。 还必须省略可选的 -ResourceGroupName 参数。

> [!NOTE]
> 从此处，可使用 SQL Server Management Studio 连接到已还原的数据库，执行所需任务，例如从恢复的数据库中提取一部分数据，复制到现有数据库或删除现有数据库，并将已还原的数据库重命名为现有数据库名。 请参阅[时间点还原](../database/recovery-using-backups.md#point-in-time-restore)。

## <a name="next-steps"></a>后续步骤

- 若要了解服务生成的自动备份，请参阅 [自动备份](../database/automated-backups-overview.md)
- 若要了解长期备份保留，请参阅 [长期备份保留](../database/long-term-retention-overview.md)
