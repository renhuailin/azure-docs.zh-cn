---
title: PowerShell：使用 DMS 运行从 MySQL 数据库到 Azure Database for MySQL 的脱机迁移
titleSuffix: Azure Database Migration Service
description: 了解如何通过 PowerShell 脚本使用 Azure 数据库迁移服务，将本地 MySQL 数据库迁移到 Azure Database for MySQL。
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
ms.openlocfilehash: f01f718bca7d6fc8c6226f682289ef46068aa126
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2021
ms.locfileid: "108125834"
---
# <a name="migrate-mysql-to-azure-database-for-mysql-offline-with-powershell--azure-database-migration-service"></a>使用 PowerShell 和 Azure 数据库迁移服务将 MySQL 脱机迁移到 Azure Database for MySQL

本文介绍如何通过 Microsoft Azure PowerShell 使用 Azure 数据库迁移服务的脱机迁移功能，将还原到本地实例的 MySQL 数据库迁移到 Azure Database for MySQL。 本文介绍 PowerShell 脚本的集合，按顺序执行这些脚本可以将 MySQL 数据库脱机迁移到 Azure。 你可以从 [Github 存储库](https://github.com/Azure/azure-mysql/tree/master/Azure%20DMS%20-%20MySQL%20Offline%20Migration%20Script)下载本教程中所述的完整 PowerShell 脚本。


> [!NOTE]
> 目前不能使用 Az.DataMigration 模块运行完整的数据库迁移。 同时，示例 PowerShell 脚本“按原样”提供，该脚本使用 [DMS REST API](/rest/api/datamigration/tasks/get)，并允许自动执行迁移。 当 Az.DataMigration 模块和 Azure CLI 中添加正式支持后，此脚本将被修改或弃用。 

> [!NOTE]
> 另外，支持 Amazon Relational Database Service (RDS) for MySQL 和 Amazon Aurora（基于 MySQL）作为源进行迁移。

> [!IMPORTANT]
> “MySQL 到 Azure Database for MySQL”联机迁移方案将从 2021 年 6 月 1 日起被并行化的高性能脱机迁移方案取代。 对于联机迁移，可以将此新产品/服务与[数据传入复制](../mysql/concepts-data-in-replication.md)配合使用。 也可将 [MyDumper/MyLoader](https://centminmod.com/mydumper.html) 之类的开源工具与用于联机迁移的数据传入复制配合使用。 

本文有助于自动处理以下场景：源数据库名称和目标数据库名称可以相同或不同，以及在迁移过程中，需要迁移目标数据库中具有相同名称和表结构的所有表或部分表。 虽然本文假设源为 MySQL 数据库实例，目标为 Azure Database for MySQL，但只需更改源服务器名称和凭据，本文即可用于在 Azure Database for MySQL 之间迁移。 此外，还支持从低版本的 MySQL 服务器（5.6 和更高版本）迁移到高版本。

[!INCLUDE [preview features callout](../../includes/dms-boilerplate-preview.md)]

在本文中，学习如何：
> [!div class="checklist"]
>
> * 迁移数据库架构。
> * 创建资源组。
> * 创建 Azure 数据库迁移服务的实例。
> * 在 Azure 数据库迁移服务实例中创建迁移项目。
> * 将迁移项目配置为使用 MySQL 的脱机迁移功能。
> * 运行迁移。

## <a name="prerequisites"></a>先决条件

若要完成这些步骤，需满足以下条件：

* 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free)。
* 具有 5.6 或更高版本的本地 MySQL 数据库。 如果没有，请下载并安装 [MySQL 社区版](https://dev.mysql.com/downloads/mysql/) 5.6 或更高版本。
* [在 Azure Database for MySQL 中创建一个实例](../mysql/quickstart-create-mysql-server-database-using-azure-portal.md)。 有关如何使用 Workbench 应用程序连接和创建数据库的详细信息，请参阅[使用 MySQL Workbench 进行连接并查询数据](../mysql/connect-workbench.md)一文。 Azure Database for MySQL 的版本应不低于本地 MySQL 版本。 例如，MySQL 5.7 可以迁移到 Azure Database for MySQL 5.7，或升级到 8。  
* 使用 Azure 资源管理器部署模型创建适合 Azure 数据库迁移服务的 Microsoft Azure 虚拟网络，它将使用 [ExpressRoute](../expressroute/expressroute-introduction.md) 或 [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) 为本地源服务器提供站点到站点连接。 有关创建虚拟网络的详细信息，请参阅[虚拟网络文档](../virtual-network/index.yml)，尤其是提供了分步详细信息的快速入门文章。

    > [!NOTE]
    > 在虚拟网络 (VNet) 设置期间，如果将 ExpressRoute 与 Microsoft 的网络对等互连一起使用，则请将 Microsoft.Sql 服务[终结点](../virtual-network/virtual-network-service-endpoints-overview.md)添加到要在其中预配该服务的子网。 Azure 数据库迁移服务缺少 Internet 连接，因此必须提供此配置。

* 请确保虚拟网络的网络安全组规则不阻止存储服务和 AzureMonitor 的 ServiceTag 出站端口 443。 有关虚拟网络 NSG 流量筛选的更多详细信息，请参阅[使用网络安全组筛选网络流量](../virtual-network/virtual-network-vnet-plan-design-arm.md)一文。
* 打开 Windows 防火墙，使 Azure 数据库迁移服务的虚拟网络发出的连接能够访问源 MySQL 服务器（默认情况下为 TCP 端口 3306）。
* 在源数据库的前面使用了防火墙设备时，可能需要添加防火墙规则以允许 Azure 数据库迁移服务的虚拟网络发出的连接访问要迁移的源数据库。
* 为目标 Azure Database for MySQL 创建服务器级别的[防火墙规则](../azure-sql/database/firewall-configure.md)或[配置 VNET 服务器终结点](../mysql/howto-manage-vnet-using-portal.md)，以允许 Azure 数据迁移服务的虚拟机网络访问目标数据库。
* 源 MySQL 必须使用支持的 MySQL 社区版。 若要确定 MySQL 实例的版本，请在 MySQL 实用程序或 MySQL Workbench 中运行以下命令：

    ```
    SELECT @@version;
    ```

* Azure Database for MySQL 仅支持 InnoDB 表。 若要将 MyISAM 表转换为 InnoDB，请参阅 [Converting Tables from MyISAM to InnoDB](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html)（将表从 MyISAM 转换为 InnoDB）一文
* 用户必须具有读取源数据库上数据的权限。
* 本指南将 PowerShell v7.1 与 PSEdition Core 配合使用，后者可按照[安装指南](/powershell/scripting/install/installing-powershell?view=powershell-7.1&preserve-view=true)进行安装
* 使用 [Install-Module PowerShell cmdlet](/powershell/module/powershellget/Install-Module) 从 PowerShell 库下载并安装以下模块；请务必使用“以管理员身份运行”来打开 PowerShell 命令窗口：
    * Az.Resources
    * Az.Network
    * Az.DataMigration

```powershell
Install-Module Az.Resources
Install-Module Az.Network
Install-Module Az.DataMigration
Import-Module Az.Resources
Import-Module Az.Network
Import-Module Az.DataMigration
```

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

如果在架构中有外键，则迁移任务将处理迁移过程中的并行数据加载。 在架构迁移期间不需要删除外键。

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

## <a name="log-in-to-your-microsoft-azure-subscription"></a>登录到 Microsoft Azure 订阅

按照[使用 Azure PowerShell 登录](/powershell/azure/authenticate-azureps)一文中的说明，在 PowerShell 中使用 [Connect-AzAccount PowerShell 命令](/powershell/module/az.accounts/connect-azaccount)登录到你的 Azure 订阅。

以下脚本为 PowerShell 会话登录后设置默认订阅，并为格式化的控制台日志创建帮助程序日志记录函数。

```powershell
[string] $SubscriptionName = "mySubscription"
$ErrorActionPreference = "Stop";

Connect-AzAccount
Set-AzContext -Subscription $SubscriptionName
$global:currentSubscriptionId = (Get-AzContext).Subscription.Id;

function LogMessage([string] $Message, [bool] $IsProcessing = $false) {
    if ($IsProcessing) {
        Write-Host "$(Get-Date -Format "yyyy-MM-dd HH:mm:ss"): $Message" -ForegroundColor Yellow
    }
    else {
        Write-Host "$(Get-Date -Format "yyyy-MM-dd HH:mm:ss"): $Message" -ForegroundColor Green
    }    
}
```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>注册 Microsoft.DataMigration 资源提供程序

每个 Azure 订阅只需注册一次资源提供程序。 如果没有注册，你将无法创建 Azure 数据库迁移服务的实例。

使用 [Register-AzResourceProvider](/powershell/module/az.resources/register-azresourceprovider) 命令注册资源提供程序。 以下脚本注册 Azure 数据库迁移服务所需的资源提供程序

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.DataMigration
```

## <a name="create-a-resource-group"></a>创建资源组

Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 请先创建一个资源组，然后再创建任何 DMS 资源。

使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 命令创建资源组。

以下示例在美国西部 2 区域的默认订阅 mySubscription 下创建了一个名为 myResourceGroup 的资源组  。

```powershell
# Get the details of resource group
[string] $Location = "westus2"
[string] $ResourceGroupName = "myResourceGroup"

$resourceGroup = Get-AzResourceGroup -Name $ResourceGroupName
if (-not($resourceGroup)) {
    LogMessage -Message "Creating resource group $ResourceGroupName..." -IsProcessing $true
    $resourceGroup = New-AzResourceGroup -Name $ResourceGroupName -Location $Location
    LogMessage -Message "Created resource group - $($resourceGroup.ResourceId)."
}
else { LogMessage -Message "Resource group $ResourceGroupName exists." }
```

## <a name="create-an-instance-of-azure-database-migration-service"></a>创建 Azure 数据库迁移服务的实例

可以通过使用 [New-AzDataMigrationService](/powershell/module/az.datamigration/new-azdatamigrationservice) 命令创建 Azure 数据库迁移服务的新实例。 此命令需要以下必需参数：
* Azure 资源组名称。 可以使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 命令创建前述 Azure 资源组，并提供其名称作为参数。
* 服务名称。 与 Azure 数据库迁移服务的所需唯一服务名称相对应的字符串 
* *位置*。 指定服务的位置。 指定 Azure 数据中心位置，例如“美国西部”或“东南亚”
* Sku。 此参数对应于 DMS Sku 名称。 当前支持的 Sku 名称为 Standard_1vCore、Standard_2vCores、Standard_4vCores、Premium_4vCores   。
* 虚拟子网标识符。 可以使用 [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) 命令获取子网的信息。 

以下脚本要求存在一个子网名为 default 的 myVirtualNetwork 虚拟网络，然后在同一区域中，在步骤 3 创建的资源组下，创建名为 myDmService 的数据库迁移服务  。

```powershell
# Get a reference to the DMS service - Create if not exists
[string] $VirtualNetworkName = "myVirtualNetwork"
[string] $SubnetName = "default"
[string] $ServiceName = "myDmService"

$dmsServiceResourceId = "/subscriptions/$($global:currentSubscriptionId)/resourceGroups/$ResourceGroupName/providers/Microsoft.DataMigration/services/$ServiceName"
$dmsService = Get-AzResource -ResourceId $dmsServiceResourceId -ErrorAction SilentlyContinue

# Create Azure DMS service if not existing
# Possible values for SKU currently are Standard_1vCore,Standard_2vCores,Standard_4vCores,Premium_4vCores
if (-not($dmsService)) {   
    $virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName $ResourceGroupName -Name $VirtualNetworkName
    if (-not ($virtualNetwork)) { throw "ERROR: Virtual Network $VirtualNetworkName does not exists" }

    $subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $virtualNetwork -Name $SubnetName
    if (-not ($subnet)) { throw "ERROR: Virtual Network $VirtualNetworkName does not contains Subnet $SubnetName" }

    LogMessage -Message "Creating Azure Data Migration Service $ServiceName..." -IsProcessing $true
    $dmsService = New-AzDataMigrationService `
        -ResourceGroupName $ResourceGroupName `
        -Name $ServiceName `
        -Location $resourceGroup.Location `
        -Sku Premium_4vCores `
        -VirtualSubnetId $Subnet.Id
    
    $dmsService = Get-AzResource -ResourceId $dmsServiceResourceId
    LogMessage -Message "Created Azure Data Migration Service - $($dmsService.ResourceId)."
}
else { LogMessage -Message "Azure Data Migration Service $ServiceName exists." }
```

## <a name="create-a-migration-project"></a>创建迁移项目

在创建 Azure 数据库迁移服务实例以后，将创建迁移项目。 迁移项目指定需要执行的迁移类型。

以下脚本在同一区域中，在步骤 4 创建的数据迁移服务实例下，创建一个名为 myfirstmysqlofflineproject 的迁移项目，用于从 MySQL 脱机迁移到 Azure Database for MySQL。

```powershell
# Get a reference to the DMS project - Create if not exists
[string] $ProjectName = "myfirstmysqlofflineproject"

$dmsProjectResourceId = "/subscriptions/$($global:currentSubscriptionId)/resourceGroups/$($dmsService.ResourceGroupName)/providers/Microsoft.DataMigration/services/$($dmsService.Name)/projects/$projectName"
$dmsProject = Get-AzResource -ResourceId $dmsProjectResourceId -ErrorAction SilentlyContinue

# Create Azure DMS Project if not existing
if (-not($dmsProject)) {
    LogMessage -Message "Creating Azure DMS project $projectName for MySQL migration ..." -IsProcessing $true

    $newProjectProperties = @{"sourcePlatform" = "MySQL"; "targetPlatform" = "AzureDbForMySQL" }
    $dmsProject = New-AzResource `
        -ApiVersion 2018-03-31-preview `
        -Location $dmsService.Location `
        -ResourceId $dmsProjectResourceId `
        -Properties $newProjectProperties `
        -Force

    LogMessage -Message "Created Azure DMS project $projectName - $($dmsProject.ResourceId)."
}
else { LogMessage -Message "Azure DMS project $projectName exists." }
```

## <a name="create-a-database-connection-info-object-for-the-source-and-target-connections"></a>创建源和目标连接的数据库连接信息对象

创建迁移项目后，将创建数据库连接信息。 此连接信息将在迁移过程中用于连接到源服务器和目标服务器。

以下脚本获取源和目标 MySQL 实例的服务器名称、用户名和密码，并创建连接信息对象。 该脚本会提示用户输入源和目标 MySQL 实例的密码。 对于无提示脚本，可以从 Azure Key Vault 提取凭据。 

```powershell
# Initialize the source and target database server connections
[string] $SourceServerName = "13.66.136.192"
[string] $SourceUserName = "docadmin@mysqlserver"
[securestring] $SourcePassword = Read-Host "Enter MySQL Source Server Password" -AsSecureString

[string] $TargetServerName = "migdocdevwus2mysqlsstrgt.mysql.database.azure.com"
[string] $TargetUserName = "docadmin@migdocdevwus2mysqlsstrgt"
[securestring] $TargetPassword = Read-Host "Enter MySQL Target Server Password" -AsSecureString

function InitConnection(
    [string] $ServerName,
    [string] $UserName,
    [securestring] $Password) {
    $connectionInfo = @{
        "dataSource"             = "";
        "serverName"             = "";
        "port"                   = 3306;
        "userName"               = "";
        "password"               = "";
        "authentication"         = "SqlAuthentication";
        "encryptConnection"      = $true;
        "trustServerCertificate" = $true;
        "additionalSettings"     = "";
        "type"                   = "MySqlConnectionInfo" 
    }

    $connectionInfo.dataSource = $ServerName;
    $connectionInfo.serverName = $ServerName;
    $connectionInfo.userName = $UserName;
    $connectionInfo.password = (ConvertFrom-SecureString -AsPlainText $password).ToString();
    $connectionInfo;
}

# Initialize the source and target connections
LogMessage -Message "Initializing source and target connection objects ..." -IsProcessing $true
$sourceConnInfo = InitConnection `
    $SourceServerName `
    $SourceUserName `
    $SourcePassword;

$targetConnInfo = InitConnection `
    $TargetServerName `
    $TargetUserName `
    $TargetPassword;

LogMessage -Message "Source and target connection object initialization complete."
```

## <a name="extract-the-list-of-table-names-from-the-target-database"></a>从目标数据库中提取表名称的列表

可以使用迁移任务和连接信息提取数据库表列表。 表列表将从源数据库和目标数据库中提取，以便能够进行正确的映射和验证。 

以下脚本获取源数据库和目标数据库的名称，然后通过 GetUserTablesMySql 迁移任务从数据库中提取表列表。 

```powershell
# Run scenario to get the tables from the target database to build
# the migration table mapping
[string] $TargetDatabaseName = "migtargetdb"
[string] $SourceDatabaseName = "migsourcedb"

function RunScenario([object] $MigrationService, 
    [object] $MigrationProject, 
    [string] $ScenarioTaskName, 
    [object] $TaskProperties, 
    [bool] $WaitForScenario = $true) {
    # Check if the scenario task already exists, if so remove it
    LogMessage -Message "Removing scenario if already exists..." -IsProcessing $true
    Remove-AzDataMigrationTask `
        -ResourceGroupName $MigrationService.ResourceGroupName `
        -ServiceName $MigrationService.Name `
        -ProjectName $MigrationProject.Name `
        -TaskName $ScenarioTaskName `
        -Force;

    # Start the new scenario task using the provided properties
    LogMessage -Message "Initializing scenario..." -IsProcessing $true
    New-AzResource `
        -ApiVersion 2018-03-31-preview `
        -Location $MigrationService.Location `
        -ResourceId "/subscriptions/$($global:currentSubscriptionId)/resourceGroups/$($MigrationService.ResourceGroupName)/providers/Microsoft.DataMigration/services/$($MigrationService.Name)/projects/$($MigrationProject.Name)/tasks/$($ScenarioTaskName)" `
        -Properties $TaskProperties `
        -Force | Out-Null;
    
    LogMessage -Message "Waiting for $ScenarioTaskName scenario to complete..." -IsProcessing $true
    if ($WaitForScenario) {
        $progressCounter = 0;
        do {
            if ($null -ne $scenarioTask) {
                Start-Sleep 10;
            }

            # Get calls can time out and will return a cancellation exception in that case
            $scenarioTask = Get-AzDataMigrationTask `
                -ResourceGroupName $MigrationService.ResourceGroupName `
                -ServiceName $MigrationService.Name `
                -ProjectName $MigrationProject.Name `
                -TaskName $ScenarioTaskName `
                -Expand `
                -ErrorAction Ignore;

            Write-Progress -Activity "Scenario Run $ScenarioTaskName  (Marquee Progress Bar)" `
                -Status $scenarioTask.ProjectTask.Properties.State `
                -PercentComplete $progressCounter
            
            $progressCounter += 10;
            if ($progressCounter -gt 100) { $progressCounter = 10 }
        }
        while (($null -eq $scenarioTask) -or ($scenarioTask.ProjectTask.Properties.State -eq "Running") -or ($scenarioTask.ProjectTask.Properties.State -eq "Queued"))
    }
    Write-Progress -Activity "Scenario Run $ScenarioTaskName" `
        -Status $scenarioTask.ProjectTask.Properties.State `
        -Completed
                 
    # Now get it using REST APIs so we can expand the output  
    LogMessage -Message "Getting expanded task results ..." -IsProcessing $true  
    $psToken = (Get-AzAccessToken -ResourceUrl https://management.azure.com).Token;
    $token = ConvertTo-SecureString -String $psToken -AsPlainText -Force;
    $taskResource = Invoke-RestMethod `
        -Method GET `
        -Uri "https://management.azure.com$($scenarioTask.ProjectTask.Id)?api-version=2018-03-31-preview&`$expand=output" `
        -ContentType "application/json" `
        -Authentication Bearer `
        -Token $token;
    
    $taskResource.properties;
}

# create the get table task properties by initializing the connection and 
# database name
$getTablesTaskProperties = @{
    "input"    = @{
        "connectionInfo"    = $null;
        "selectedDatabases" = $null;
    };
    "taskType" = "GetUserTablesMySql";
};

LogMessage -Message "Running scenario to get the list of tables from the target database..." -IsProcessing $true
$getTablesTaskProperties.input.connectionInfo = $targetConnInfo;
$getTablesTaskProperties.input.selectedDatabases = @($TargetDatabaseName);
# Create a name for the task
$getTableTaskName = "$($TargetDatabaseName)GetUserTables"
# Get the list of tables from the source
$getTargetTablesTask = RunScenario -MigrationService $dmsService `
    -MigrationProject $dmsProject `
    -ScenarioTaskName $getTableTaskName `
    -TaskProperties $getTablesTaskProperties;

if (-not ($getTargetTablesTask)) { throw "ERROR: Could not get target database $TargetDatabaseName table information." }
LogMessage -Message "List of tables from the target database acquired."

LogMessage -Message "Running scenario to get the list of tables from the source database..." -IsProcessing $true
$getTablesTaskProperties.input.connectionInfo = $sourceConnInfo;
$getTablesTaskProperties.input.selectedDatabases = @($SourceDatabaseName);
# Create a name for the task
$getTableTaskName = "$($SourceDatabaseName)GetUserTables"
# Get the list of tables from the source
$getSourceTablesTask = RunScenario -MigrationService $dmsService `
    -MigrationProject $dmsProject `
    -ScenarioTaskName $getTableTaskName `
    -TaskProperties $getTablesTaskProperties;

if (-not ($getSourceTablesTask)) { throw "ERROR: Could not get source database $SourceDatabaseName table information." }
LogMessage -Message "List of tables from the source database acquired."

```

## <a name="build-table-mapping-based-on-user-configuration"></a>基于用户配置生成表映射

在配置迁移任务的过程中，将创建源表和目标表之间的映射。 虽然映射处于表名称级别，但我们假设被映射表的表结构（列计数、列名称、数据类型等）完全相同。

以下脚本基于步骤 7 中提取的目标和源表列表创建映射。 如果是部分数据加载，用户可以提供表列表以筛选掉这些表。 如果未提供用户输入，则映射所有目标表。 该脚本还会检查源中是否存在具有相同名称的表。 如果源中不存在表名，则在迁移时会忽略目标表。 

```powershell
# Create the source to target table map
# Optional table settings
# DEFAULT: $IncludeTables = $null => include all tables for migration
# DEFAULT: $ExcludeTables = $null => exclude no tables from migration
# Exclude list has higher priority than include list
# Array of qualified source table names which should be migrated
[string[]] $IncludeTables = @("migsourcedb.coupons", "migsourcedb.daily_cash_sheets");
[string[]] $ExcludeTables = $null;

LogMessage -Message "Creating the table map based on the user input and database table information ..." `
    -IsProcessing $true

$targetTables = $getTargetTablesTask.Output.DatabasesToTables."$TargetDatabaseName";
$sourceTables = $getSourceTablesTask.Output.DatabasesToTables."$SourceDatabaseName";
$tableMap = New-Object 'system.collections.generic.dictionary[string,string]';

$schemaPrefixLength = $($SourceDatabaseName + ".").Length;
$tableMappingError = $false
foreach ($srcTable in $sourceTables) {
    # Removing the database name prefix from the table name so that comparison
    # can be done in cases where database name given are different
    $tableName = $srcTable.Name.Substring($schemaPrefixLength, `
            $srcTable.Name.Length - $schemaPrefixLength)

    # In case the table is part of exclusion list then ignore the table
    if ($null -ne $ExcludeTables -and $ExcludeTables -contains $srcTable.Name) {
        continue;
    }

    # Either the include list is null or the table is part of the include list then add it in the mapping
    if ($null -eq $IncludeTables -or $IncludeTables -contains $srcTable.Name) {
        # Check if the table exists in the target. If not then log TABLE MAPPING ERROR
        if (-not ($targetTables | Where-Object { $_.name -ieq "$($TargetDatabaseName).$tableName" })) {
            $tableMappingError = $true
            Write-Host "TABLE MAPPING ERROR: $($targetTables.name) does not exists in target." -ForegroundColor Red
            continue;
        }  

        $tableMap.Add("$($SourceDatabaseName).$tableName", "$($TargetDatabaseName).$tableName");
    }     
}

# In case of any table mapping errors identified, throw an error and stop the process
if ($tableMappingError) { throw "ERROR: One or more table mapping errors were identified. Please see previous messages." }
# In case no tables are in the mapping then throw error
if ($tableMap.Count -le 0) { throw "ERROR: Could not create table mapping." }
LogMessage -Message "Migration table mapping created for $($tableMap.Count) tables."
```

## <a name="create-and-configure-the-migration-task-inputs"></a>创建和配置迁移任务输入

生成表映射后，将创建 Migrate.MySql.AzureDbForMySql 类型的迁移任务的输入，并配置属性。

以下脚本创建迁移任务，并设置连接、数据库名称和表映射。

```powershell
# Create and configure the migration scenario based on the connections
# and the table mapping
$offlineMigTaskProperties = @{
    "input"    = @{
        "sourceConnectionInfo"  = $null;
        "targetConnectionInfo"  = $null;
        "selectedDatabases"     = $null;
        "optionalAgentSettings" = @{
            "EnableCacheBatchesInMemory"         = $true;
            "DisableIncrementalRowStatusUpdates" = $true;
        };
        "startedOn"             = $null;
    };
    "taskType" = "Migrate.MySql.AzureDbForMySql";
};
$offlineSelectedDatabase = @{
    "name"               = $null;
    "targetDatabaseName" = $null;
    "tableMap"           = $null;
};

LogMessage -Message "Preparing migration scenario configuration ..." -IsProcessing $true

# Select the database to be migrated
$offlineSelectedDatabase.name = $SourceDatabaseName;
$offlineSelectedDatabase.tableMap = New-Object PSObject -Property $tableMap;
$offlineSelectedDatabase.targetDatabaseName = $TargetDatabaseName;

# Set connection info and the database mapping
$offlineMigTaskProperties.input.sourceConnectionInfo = $sourceConnInfo;
$offlineMigTaskProperties.input.targetConnectionInfo = $targetConnInfo;
$offlineMigTaskProperties.input.selectedDatabases = @($offlineSelectedDatabase);
$offlineMigTaskProperties.input.startedOn = [System.DateTimeOffset]::UtcNow.ToString("O");
```

## <a name="configure-performance-tuning-parameters"></a>配置性能优化参数

PowerShell 模块中提供了几个可选参数，可以根据环境优化这些参数。 这些参数可用于提高迁移任务的性能。 所有这些参数都是可选的，其默认值为 NULL。

> [!NOTE]
> 在高级 SKU 上进行迁移时，以下性能配置显示增加了吞吐量。
> * WriteDataRangeBatchTaskCount = 12
> * DelayProgressUpdatesInStorageInterval = 30 seconds
> * ThrottleQueryTableDataRangeTaskAtBatchCount = 36

以下脚本获取参数的用户值，并设置迁移任务属性中的参数。

```powershell
# Setting optional parameters from fine tuning the data transfer rate during migration
# DEFAULT values for all the configurations is $null
LogMessage -Message "Adding optional migration performance tuning configuration ..." -IsProcessing $true
# Partitioning settings
# Optional setting that configures the maximum number of parallel reads on tables located on the source database.
[object] $DesiredRangesCount = 4
# Optional setting that configures that size of the largest batch that will be committed to the target server.
[object] $MaxBatchSizeKb = 4096
# Optional setting that configures the minimum number of rows in each batch written to the target.
[object] $MinBatchRows = $null
# Task count settings
# Optional setting that configures the number of databases that will be prepared for migration in parallel.
[object] $PrepareDatabaseForBulkImportTaskCount = $null
# Optional setting that configures the number of tables that will be prepared for migration in parallel.
[object] $PrepareTableForBulkImportTaskCount = $null
# Optional setting that configures the number of threads available to read ranges on the source.
[object] $QueryTableDataRangeTaskCount = 8
# Optional setting that configures the number of threads available to write batches to the target.
[object] $WriteDataRangeBatchTaskCount = 12
# Batch cache settings
# Optional setting that configures how much memory will be used to cache batches in memory before reads on the source are throttled.
[object] $MaxBatchCacheSizeMb = $null
# Optional setting that configures the amount of available memory at which point reads on the source will be throttled.
[object] $ThrottleQueryTableDataRangeTaskAtAvailableMemoryMb = $null
# Optional setting that configures the number of batches cached in memory that will trigger read throttling on the source.
[object] $ThrottleQueryTableDataRangeTaskAtBatchCount = 36
# Performance settings
# Optional setting that configures the delay between updates of result objects in Azure Table Storage.
[object] $DelayProgressUpdatesInStorageInterval = "00:00:30"

function AddOptionalSetting($optionalAgentSettings, $settingName, $settingValue) {
    # If no value specified for the setting, don't bother adding it to the input
    if ($null -eq $settingValue) {
        return;
    }

    # Add a new property to the JSON object to capture the setting which will be customized
    $optionalAgentSettings | add-member -MemberType NoteProperty -Name $settingName -Value $settingValue
}

# Set any optional settings in the input based on parameters to this cmdlet
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "DesiredRangesCount" $DesiredRangesCount;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "MaxBatchSizeKb" $MaxBatchSizeKb;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "MinBatchRows" $MinBatchRows;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "PrepareDatabaseForBulkImportTaskCount" $PrepareDatabaseForBulkImportTaskCount;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "PrepareTableForBulkImportTaskCount" $PrepareTableForBulkImportTaskCount;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "QueryTableDataRangeTaskCount" $QueryTableDataRangeTaskCount;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "WriteDataRangeBatchTaskCount" $WriteDataRangeBatchTaskCount;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "MaxBatchCacheSizeMb" $MaxBatchCacheSizeMb;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "ThrottleQueryTableDataRangeTaskAtAvailableMemoryMb" $ThrottleQueryTableDataRangeTaskAtAvailableMemoryMb;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "ThrottleQueryTableDataRangeTaskAtBatchCount" $ThrottleQueryTableDataRangeTaskAtBatchCount;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "DelayProgressUpdatesInStorageInterval" $DelayProgressUpdatesInStorageInterval;
```

## <a name="creating-and-running-the-migration-task"></a>创建并运行迁移任务

配置任务的输入后，现在将在代理上创建并执行任务。 此脚本将触发任务执行并等待迁移完成。

以下脚本调用配置的迁移任务并等待任务完成。

```powershell
# Running the migration scenario
[string] $TaskName = "mysqlofflinemigrate"

LogMessage -Message "Running data migration scenario ..." -IsProcessing $true
$summary = @{
    "SourceServer"   = $SourceServerName;
    "SourceDatabase" = $SourceDatabaseName;
    "TargetServer"   = $TargetServerName;
    "TargetDatabase" = $TargetDatabaseName;
    "TableCount"     = $tableMap.Count;
    "StartedOn"      = $offlineMigTaskProperties.input.startedOn;
}

Write-Host "Job Summary:" -ForegroundColor Yellow
Write-Host $(ConvertTo-Json $summary) -ForegroundColor Yellow

$migrationResult = RunScenario -MigrationService $dmsService `
    -MigrationProject $dmsProject `
    -ScenarioTaskName $TaskName `
    -TaskProperties $offlineMigTaskProperties

LogMessage -Message "Migration completed with status - $($migrationResult.state)"
#Checking for any errors or warnings captured by the task during migration
$dbLevelResult = $migrationResult.output | Where-Object { $_.resultType -eq "DatabaseLevelOutput" } 
$migrationLevelResult = $migrationResult.output | Where-Object { $_.resultType -eq "MigrationLevelOutput" }
if ($dbLevelResult.exceptionsAndWarnings) {
    Write-Host "Following database errors were captured: $($dbLevelResult.exceptionsAndWarnings)" -ForegroundColor Red
}
if ($migrationLevelResult.exceptionsAndWarnings) {
    Write-Host "Following migration errors were captured: $($migrationLevelResult.exceptionsAndWarnings)" -ForegroundColor Red
}
if ($migrationResult.errors.details) {
    Write-Host "Following task level migration errors were captured: $($migrationResult.errors.details)" -ForegroundColor Red
}
```

## <a name="deleting-the-database-migration-service"></a>删除数据库迁移服务

相同的数据库迁移服务可用于多次迁移，因此实例在创建后可以重复使用。 如果不打算继续使用数据库迁移服务，则可以使用 [Remove-AzDataMigrationService](/powershell/module/az.datamigration/remove-azdatamigrationservice?) 命令删除该服务。

以下脚本删除 Azure 数据库迁移服务实例及其关联项目。

```powershell
Remove-AzDataMigrationService -ResourceId $($dmsService.ResourceId)
```

## <a name="next-steps"></a>后续步骤

* 有关使用 DMS 执行迁移时的已知问题和限制，请参阅[常见问题 - Azure 数据库迁移服务](./known-issues-troubleshooting-dms.md)一文。
* 要在使用 DMS 时排查源数据库连接问题，请参阅[连接源数据库时的问题](./known-issues-troubleshooting-dms-source-connectivity.md)一文。
* 若要了解 Azure 数据库迁移服务，请参阅[什么是 Azure 数据库迁移服务？](./dms-overview.md)一文。
* 若要了解 Azure Database for MySQL，请参阅[什么是 Azure Database for MySQL？](../mysql/overview.md)一文。
* 有关通过门户使用 DMS 的教程，请参阅[教程：使用 DMS 将 MySQL 脱机迁移到 Azure Database for MySQL](./tutorial-mysql-azure-mysql-offline-portal.md)
