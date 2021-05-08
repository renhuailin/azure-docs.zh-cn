---
title: PowerShell：将 SQL Server 联机迁移到 SQL 托管实例
titleSuffix: Azure Database Migration Service
description: 了解如何使用 Azure PowerShell 和 Azure 数据库迁移服务从 SQL Server 联机迁移到 Azure SQL 托管实例。
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: ''
ms.topic: how-to
ms.date: 12/16/2020
ms.openlocfilehash: 7f09db2e1f98d48e91dfea2642969ff4ca360967
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "98697771"
---
# <a name="migrate-sql-server-to-sql-managed-instance-online-with-powershell--azure-database-migration-service"></a>使用 PowerShell 和 Azure 数据库迁移服务将 SQL Server 联机迁移到 SQL 托管实例

在本文中，我们将使用 Microsoft Azure PowerShell 将还原为 SQL Server 2005 或更高版本的本地实例的“Adventureworks2016”数据库联机迁移到 Azure SQL 托管实例。 可以使用 Microsoft Azure PowerShell 中的 `Az.DataMigration` 模块，将数据库从 SQL Server 实例迁移到 SQL 托管实例。

在本文中，学习如何：
> [!div class="checklist"]
>
> * 创建资源组。
> * 创建 Azure 数据库迁移服务的实例。
> * 在 Azure 数据库迁移服务实例中创建迁移项目。
> * 运行联机迁移。

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

本文提供联机迁移的步骤，但也可以进行[脱机](howto-sql-server-to-azure-sql-managed-instance-powershell-offline.md)迁移。


## <a name="prerequisites"></a>先决条件

若要完成这些步骤，需满足以下条件：

* [SQL Server 2016 或更高版本](https://www.microsoft.com/sql-server/sql-server-downloads)（任意版本）。
* **AdventureWorks2016** 数据库的本地副本，可从 [此处](/sql/samples/adventureworks-install-configure)下载。
* 启用 TCP / IP 协议，使用 SQL Server Express 安装的情况下会默认禁用该协议。 遵循[启用或禁用服务器网络协议](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure)一文启用 TCP/IP 协议。
* 配置[针对数据库引擎访问的 Windows 防火墙](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)。
* Azure 订阅。 如果没有订阅，请在开始之前[创建一个免费帐户](https://azure.microsoft.com/free/)。
* SQL 托管实例。 可按照[创建 SQL 托管实例](../azure-sql/managed-instance/instance-create-quickstart.md)一文中的详述创建 SQL 托管实例。
* 下载并安装[数据迁移助手](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 或更高版本。
* 使用 Azure 资源管理器部署模型创建 Microsoft Azure 虚拟网络，该模型使用 [ExpressRoute](../expressroute/expressroute-introduction.md) 或 [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) 为本地源服务器提供具有站点到站点连接的 Azure 数据库迁移服务。
* 已使用[执行 SQL Server 迁移评估](/sql/dma/dma-assesssqlonprem)一文中所述的数据迁移助手完成对本地数据库和架构迁移的评估。
* 使用 [Install-Module PowerShell cmdlet](/powershell/module/powershellget/Install-Module) 从 PowerShell 库下载并安装 `Az.DataMigration` 模块（0.7.2 或更高版本）。
* 确保用于连接到源 SQL Server 实例的凭据具有 [CONTROL SERVER](/sql/t-sql/statements/grant-server-permissions-transact-sql) 权限。
* 确保用于连接到目标 SQL 托管实例的凭据具有目标 SQL 托管实例数据库的 CONTROL DATABASE 权限。

    > [!IMPORTANT]
    > 对于联机迁移，必须事先安装 Azure Active Directory 凭据。 有关详细信息，请参阅[使用门户创建可访问资源的 Azure AD 应用程序和服务主体](../active-directory/develop/howto-create-service-principal-portal.md)一文。

## <a name="create-a-resource-group"></a>创建资源组

Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。

使用 [`New-AzResourceGroup`](/powershell/module/az.resources/new-azresourcegroup) 命令创建资源组。

以下示例在“美国东部”区域创建名为“myResourceGroup”的资源组。

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

## <a name="create-an-instance-of-dms"></a>创建 DMS 的实例

可以通过 `New-AzDataMigrationService` cmdlet 创建 Azure 数据库迁移服务的新实例。
此 cmdlet 需要以下必需参数：

* Azure 资源组名称。 可以使用 [`New-AzResourceGroup`](/powershell/module/az.resources/new-azresourcegroup) 命令创建前述 Azure 资源组，并提供其名称作为参数。
* 服务名称。 与 Azure 数据库迁移服务的所需唯一服务名称相对应的字符串。
* *位置*。 指定服务的位置。 指定 Azure 数据中心位置，例如“美国西部”或“东南亚”。
* Sku。 此参数对应于 DMS Sku 名称。 目前支持的 SKU 名称为 *Basic_1vCore*、*Basic_2vCores* 和 *GeneralPurpose_4vCores*。
* 虚拟子网标识符。 可以使用 cmdlet [`New-AzVirtualNetworkSubnetConfig`](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) 创建子网。

以下示例使用名为“MyVNET”的虚拟网络和名为“MySubnet”的子网，在位于“美国东部”的资源组“MyDMSResourceGroup”中创建名为“MyDMS”的服务。


```powershell
$vNet = Get-AzVirtualNetwork -ResourceGroupName MyDMSResourceGroup -Name MyVNET

$vSubNet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vNet -Name MySubnet

$service = New-AzDms -ResourceGroupName myResourceGroup `
  -ServiceName MyDMS `
  -Location EastUS `
  -Sku Basic_2vCores `  
  -VirtualSubnetId $vSubNet.Id`
```

## <a name="create-a-migration-project"></a>创建迁移项目

在创建 Azure 数据库迁移服务实例以后，创建迁移项目。 Azure 数据库迁移服务项目需要源和目标实例的连接信息，以及要在项目中迁移的数据库的列表。
定义源和目标连接性连接字符串。

以下脚本定义了源 SQL Server 连接详细信息： 

```powershell
# Source connection properties
$sourceDataSource = "<mysqlserver.domain.com/privateIP of source SQL>"
$sourceUserName = "domain\user"
$sourcePassword = "mypassword"
```

以下脚本定义了目标 SQL 托管实例连接详细信息： 

```powershell
# Target MI connection properties
$targetMIResourceId = "/subscriptions/<subid>/resourceGroups/<rg>/providers/Microsoft.Sql/managedInstances/<myMI>"
$targetUserName = "<user>"
$targetPassword = "<password>"
```



### <a name="define-source-and-target-database-mapping"></a>定义源和目标数据库映射

提供要在此迁移项目中迁移的数据库

以下脚本使用提供的名称将源数据库映射到目标 SQL 托管实例上的相应新数据库。

```powershell
# Selected databases (Source database name to target database name mapping)
$selectedDatabasesMap = New-Object System.Collections.Generic.Dictionary"[String,String]" 
$selectedDatabasesMap.Add("<source  database name>", "<target database name> ")
```

对于多个数据库，请使用以下格式将数据库列表添加到上述脚本中：

```powershell
$selectedDatabasesMap = New-Object System.Collections.Generic.Dictionary"[String,String]" 
$selectedDatabasesMap.Add("<source  database name1>", "<target database name1> ")
$selectedDatabasesMap.Add("<source  database name2>", "<target database name2> ")
```

### <a name="create-dms-project"></a>创建 DMS 项目

可以在 DMS 实例中创建 Azure 数据库迁移服务项目。

```powershell
# Create DMS project
$project = New-AzDataMigrationProject `
  -ResourceGroupName $dmsResourceGroupName `
  -ServiceName $dmsServiceName `
  -ProjectName $dmsProjectName `
  -Location $dmsLocation `
  -SourceType SQL `
  -TargetType SQLMI `

# Create selected databases object
$selectedDatabases = @();
foreach ($sourceDbName in $selectedDatabasesMap.Keys){
    $targetDbName = $($selectedDatabasesMap[$sourceDbName])
    $selectedDatabases += New-AzDmsSelectedDB -MigrateSqlServerSqlDbMi `
      -Name $sourceDbName `
      -TargetDatabaseName $targetDbName `
      -BackupFileShare $backupFileShare `
}
```



### <a name="create-a-backup-fileshare-object"></a>创建备份 FileShare 对象

现在，使用 New-AzDmsFileShare cmdlet 创建 FileShare 对象，用于 Azure 数据库迁移服务可将源数据库备份迁移到的本地 SMB 网络共享。

```powershell
# SMB Backup share properties
$smbBackupSharePath = "\\shareserver.domain.com\mybackup"
$smbBackupShareUserName = "domain\user"
$smbBackupSharePassword = "<password>"

# Create backup file share object
$smbBackupSharePasswordSecure = ConvertTo-SecureString -String $smbBackupSharePassword -AsPlainText -Force
$smbBackupShareCredentials = New-Object System.Management.Automation.PSCredential ($smbBackupShareUserName, $smbBackupSharePasswordSecure)
$backupFileShare = New-AzDmsFileShare -Path $smbBackupSharePath -Credential $smbBackupShareCredentials
```

### <a name="define-the-azure-storage"></a>定义 Azure 存储 

选择要用于迁移的 Azure 存储容器： 

```powershell
# Storage resource id
$storageAccountResourceId = "/subscriptions/<subscriptionname>/resourceGroups/<rg>/providers/Microsoft.Storage/storageAccounts/<mystorage>"
```


### <a name="configure-azure-active-directory-app"></a>配置 Azure Active Directory 应用

提供用于联机 SQL 托管实例迁移的 Azure Active Directory 所需的详细信息： 

```powershell
# AAD properties
$AADAppId = "<appid-guid>"
$AADAppKey = "<app-key>"

# Create AAD object
$AADAppKeySecure = ConvertTo-SecureString $AADAppKey -AsPlainText -Force
$AADApp = New-AzDmsAadApp -ApplicationId $AADAppId -AppKey $AADAppKeySecure
```


## <a name="create-and-start-a-migration-task"></a>创建并启动迁移任务

接下来，创建并启动 Azure 数据库迁移服务任务。 使用变量调用源和目标，并列出要迁移的数据库表： 


```powershell
# Managed Instance online migration properties
$dmsTaskName = "testmigration1"

# Create source connection info
$sourceConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource $sourceDataSource `
  -AuthType WindowsAuthentication `
  -TrustServerCertificate:$true
$sourcePasswordSecure = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCredentials = New-Object System.Management.Automation.PSCredential ($sourceUserName, $sourcePasswordSecure)

# Create target connection info
$targetConnInfo = New-AzDmsConnInfo -ServerType SQLMI `
    -MiResourceId $targetMIResourceId
$targetPasswordSecure = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCredentials = New-Object System.Management.Automation.PSCredential ($targetUserName, $targetPasswordSecure)
```

以下示例创建并启动联机迁移任务： 

```powershell
# Create DMS migration task
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDbMiSync `
  -ResourceGroupName $dmsResourceGroupName `
  -ServiceName $dmsServiceName `
  -ProjectName $dmsProjectName `
  -TaskName $dmsTaskName `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCredentials `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCredentials `
  -SelectedDatabase  $selectedDatabases `
  -BackupFileShare $backupFileShare `
  -AzureActiveDirectoryApp $AADApp `
  -StorageResourceId $storageAccountResourceId
```

有关详细信息，请参阅 [New-AzDataMigrationTask](/powershell/module/az.datamigration/new-azdatamigrationtask)。

## <a name="monitor-the-migration"></a>监视迁移

若要监视迁移，请执行以下任务。

### <a name="check-the-status-of-task"></a>检查任务的状态

```powershell
# Get migration task status details
$migTask = Get-AzDataMigrationTask `
                    -ResourceGroupName $dmsResourceGroupName `
                    -ServiceName $dmsServiceName `
                    -ProjectName $dmsProjectName `
                    -Name $dmsTaskName `
                    -ResultType DatabaseLevelOutput `
                    -Expand

# Task state will be either of 'Queued', 'Running', 'Succeeded', 'Failed', 'FailedInputValidation' or 'Faulted'
$taskState = $migTask.ProjectTask.Properties.State

# Display task state
$taskState | Format-List
```

使用以下命令获取错误列表：

```powershell
# Get task errors
$taskErrors = $migTask.ProjectTask.Properties.Errors

# Display task errors
foreach($taskError in $taskErrors){
    $taskError |  Format-List
}


# Get database level details
$databaseLevelOutputs = $migTask.ProjectTask.Properties.Output

# Display database level details
foreach($databaseLevelOutput in $databaseLevelOutputs){

    # This is the source database name.
    $databaseName = $databaseLevelOutput.SourceDatabaseName;

    Write-Host "=========="
    Write-Host "Start migration details for database " $databaseName
    # This is the status for that database - It will be either of:
    # INITIAL, FULL_BACKUP_UPLOADING, FULL_BACKUP_UPLOADED, LOG_FILES_UPLOADING,
    # CUTOVER_IN_PROGRESS, CUTOVER_INITIATED, CUTOVER_COMPLETED, COMPLETED, CANCELLED, FAILED
    $databaseMigrationState = $databaseLevelOutput.MigrationState;

    # Details about last restored backup. This contains file names, LSN, backup date, etc 
    $databaseLastRestoredBackup = $databaseLevelOutput.LastRestoredBackupSetInfo
        
    # Details about last restored backup. This contains file names, LSN, backup date, etc 
    $databaseLastRestoredBackup = $databaseLevelOutput.LastRestoredBackupSetInfo

    # Details about last Currently active/most recent backups. This contains file names, LSN, backup date, etc 
    $databaseActiveBackpusets = $databaseLevelOutput.ActiveBackupSets

    # Display info
    $databaseLevelOutput | Format-List

    Write-Host "Currently active/most recent backupset details:"
    $databaseActiveBackpusets  | select BackupStartDate, BackupFinishedDate, FirstLsn, LastLsn -ExpandProperty ListOfBackupFiles | Format-List

    Write-Host "Last restored backupset details:"
    $databaseLastRestoredBackupFiles  | Format-List

    Write-Host "End migration details for database " $databaseName
    Write-Host "=========="
}
```

## <a name="performing-the-cutover"></a>执行交接 

在联机迁移过程中，将执行数据库的完整备份和还原，然后继续还原 BackupFileShare 中存储的事务日志。

在 Azure SQL 托管实例中的数据库已使用最新数据进行更新并与源数据库同步后，你可以执行交接。

以下示例将完成交接/迁移。 用户可自行调用此命令。

```powershell
$command = Invoke-AzDmsCommand -CommandType CompleteSqlMiSync `
                               -ResourceGroupName myResourceGroup `
                               -ServiceName $service.Name `
                               -ProjectName $project.Name `
                               -TaskName myDMSTask `
                               -DatabaseName "Source DB Name"
```

## <a name="deleting-the-instance-of-azure-database-migration-service"></a>删除 Azure 数据库迁移服务的实例

完成迁移后，可以删除 Azure 数据库迁移服务实例：

```powershell
Remove-AzDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```

## <a name="additional-resources"></a>其他资源

有关其他迁移方案（源/目标对）的信息，请参阅 Microsoft [数据库迁移指南](https://datamigration.microsoft.com/)。

## <a name="next-steps"></a>后续步骤

在[什么是 Azure 数据库迁移服务？](./dms-overview.md)一文中详细了解 Azure 数据库迁移服务。