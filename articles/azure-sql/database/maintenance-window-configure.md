---
title: 配置维护时段（预览版）
description: 了解如何设置要对 Azure SQL 数据库、弹性池和托管实例数据库执行计划内维护的时间。
services: sql-database
ms.service: sql-db-mi
ms.subservice: deployment-configuration
ms.topic: how-to
author: scott-kim-sql
ms.author: scottkim
ms.reviewer: mathoma
ms.date: 03/23/2021
ms.openlocfilehash: f2f12238d5c75b2d36d09efa2fddd76bf6b1ec1d
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/29/2021
ms.locfileid: "110694371"
---
# <a name="configure-maintenance-window-preview"></a>配置维护时段（预览版）
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]


可以在创建资源期间或者在创建资源后的任何时间，配置 Azure SQL 数据库、弹性池或 Azure SQL 托管实例数据库的[维护时段（预览版）](maintenance-window.md)。 

系统默认的维护时段为每日下午 5 点至上午 8 点（资源所在的 Azure 区域的本地时间），以避免在业务高峰期发生中断。 如果系统默认的维护时段不是最佳时间，可以选择其他某个可用维护时段。

并非在每个服务级别或每个区域中都可以改用其他维护时段。 有关可用性的详细信息，请参阅[维护时段可用性](maintenance-window.md#availability)。

> [!Important]
> 配置维护时段是一种长时间运行的异步操作，类似于更改 Azure SQL 资源的服务层级。 该资源在操作过程中可用，只在操作结束时会发生短暂的重新配置，即使在长期运行的事务中断的情况下，通常最多也仅持续 8 秒。 若要将重新配置的影响降至最低，应在高峰时段之外执行操作。

## <a name="configure-maintenance-window-during-database-creation"></a>在创建数据库期间配置维护时段 

# <a name="portal"></a>[Portal](#tab/azure-portal)

若要在创建数据库、弹性池或托管实例时配置维护时段，请在“其他设置”页上设置所需的“维护时段” 。 

## <a name="set-the-maintenance-window-while-creating-a-single-database-or-elastic-pool"></a>创建单一数据库或弹性池时设置维护时段

有关创建新数据库或池的分步操作信息，请参阅[创建 Azure SQL 数据库单一数据库](single-database-create-quickstart.md)。

   :::image type="content" source="media/maintenance-window-configure/additional-settings.png" alt-text="“创建数据库”-“其他设置”选项卡":::


## <a name="set-the-maintenance-window-while-creating-a-managed-instance"></a>在创建托管实例时设置维护时段

有关创建新托管实例的分步操作信息，请参阅[创建 Azure SQL 托管实例](../managed-instance/instance-create-quickstart.md)。

   :::image type="content" source="media/maintenance-window-configure/additional-settings-mi.png" alt-text="“创建托管实例”-“其他设置”选项卡":::




# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

以下示例演示如何使用 Azure PowerShell 配置维护时段。 可以[安装 Azure PowerShell](/powershell/azure/install-az-ps) 或使用 Azure Cloud Shell。

## <a name="launch-azure-cloud-shell"></a>启动 Azure Cloud Shell

Azure Cloud Shell 是免费的交互式 shell，可以使用它运行本文中的步骤。 它预安装有常用 Azure 工具并将其配置与帐户一起使用。 

若要打开 Cloud Shell，只需要从代码块的右上角选择“试一试”。  也可以通过转到 [https://shell.azure.com/powershell](https://shell.azure.com/powershell) 在单独的浏览器标签页中启动 Cloud Shell。 选择“复制”以复制代码块，将其粘贴到 Cloud Shell 中，然后按 Enter 来运行它。


## <a name="discover-available-maintenance-windows"></a>发现可用维护时段

设置维护时段时，每个区域会提供自己的维护时段选项，这些选项对应于数据库或池所在区域的时区。 

### <a name="discover-sql-database-and-elastic-pool-maintenance-windows"></a>发现 SQL 数据库和弹性池维护时段 

以下示例使用 [Get-AzMaintenancePublicConfiguration](/powershell/module/az.maintenance/get-azmaintenancepublicconfiguration) cmdlet 返回 eastus2 区域的可用维护时段。 对于数据库和弹性池，请将 `MaintenanceScope` 设置为 `SQLDB`。

   ```powershell-interactive
   $location = "eastus2"

   Write-Host "Available maintenance schedules in ${location}:"
   $configurations = Get-AzMaintenancePublicConfiguration
   $configurations | ?{ $_.Location -eq $location -and $_.MaintenanceScope -eq "SQLDB"}
   ```

### <a name="discover-sql-managed-instance-maintenance-windows"></a>发现 SQL 托管实例维护时段 

以下示例使用 [Get-AzMaintenancePublicConfiguration](/powershell/module/az.maintenance/get-azmaintenancepublicconfiguration) cmdlet 返回 eastus2 区域的可用维护时段。 对于托管实例，请将 `MaintenanceScope` 设置为 `SQLManagedInstance`。

   ```powershell-interactive
   $location = "eastus2"

   Write-Host "Available maintenance schedules in ${location}:"
   $configurations = Get-AzMaintenancePublicConfiguration
   $configurations | ?{ $_.Location -eq $location -and $_.MaintenanceScope -eq "SQLManagedInstance"}
   ```


## <a name="set-the-maintenance-window-while-creating-a-single-database"></a>在创建单一数据库时设置维护时段

以下示例使用 [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) cmdlet 创建一个新数据库并设置维护时段。 必须将 `-MaintenanceConfigurationId` 设置为对你的数据库所在区域有效的值。 若要获取对区域有效的值，请参阅[发现可用维护时段](#discover-available-maintenance-windows)。


   ```powershell-interactive
    # Set variables for your database
    $resourceGroupName = "your_resource_group_name"
    $serverName = "your_server_name"
    $databaseName = "your_db_name"
    
    # Set selected maintenance window
    $maintenanceConfig = "SQL_EastUS2_DB_1"

    Write-host "Creating a gen5 2 vCore database with maintenance window ${maintenanceConfig} ..."
    $database = New-AzSqlDatabase `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName `
      -Edition GeneralPurpose `
      -ComputeGeneration Gen5 `
      -VCore 2 `
      -MaintenanceConfigurationId $maintenanceConfig
    $database
   ```



## <a name="set-the-maintenance-window-while-creating-an-elastic-pool"></a>在创建弹性池时设置维护时段

以下示例使用 [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) cmdlet 创建一个新弹性池并设置维护时段。 该维护时段是针对弹性池设置的，因此该池中的所有数据库都采用该池的维护时段计划。 必须将 `-MaintenanceConfigurationId` 设置为对你的池所在区域有效的值。 若要获取对区域有效的值，请参阅[发现可用维护时段](#discover-available-maintenance-windows)。


   ```powershell-interactive
    # Set variables for your pool
    $resourceGroupName = "your_resource_group_name"
    $serverName = "your_server_name"
    $poolName = "your_pool_name"
    
    # Set selected maintenance window
    $maintenanceConfig = "SQL_EastUS2_DB_2"

    Write-host "Creating a Standard 50 pool with maintenance window ${maintenanceConfig} ..."
    $pool = New-AzSqlElasticPool `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -ElasticPoolName $poolName `
      -Edition "Standard" `
      -Dtu 50 `
      -DatabaseDtuMin 10 `
      -DatabaseDtuMax 20 `
      -MaintenanceConfigurationId $maintenanceConfig
    $pool
   ```

## <a name="set-the-maintenance-window-while-creating-a-managed-instance"></a>在创建托管实例时设置维护时段

以下示例使用 [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) cmdlet 创建一个新托管实例并设置维护时段。 该维护时段是针对实例设置的，因此该实例中的所有数据库都采用该实例的维护时段计划。 对于 `-MaintenanceConfigurationId`，MaintenanceConfigName 必须是对实例所在区域有效的值。 若要获取对区域有效的值，请参阅[发现可用维护时段](#discover-available-maintenance-windows)。


   ```powershell
   New-AzSqlInstance -Name "your_mi_name" `
     -ResourceGroupName "your_resource_group_name" `
     -Location "your_mi_location" `
     -SubnetId /subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/Microsoft.Network/virtualNetworks/{VNETName}/subnets/{SubnetName} `
     -MaintenanceConfigurationId "/subscriptions/{SubID}/providers/Microsoft.Maintenance/publicMaintenanceConfigurations/SQL_{Region}_{MaintenanceConfigName}" `
     -AsJob
   ```

# <a name="cli"></a>[CLI](#tab/azure-cli)

以下示例演示如何使用 Azure CLI 配置维护时段。 可以[安装 Azure CLI](/cli/azure/install-azure-cli) 或使用 Azure Cloud Shell。 

使用 Azure CLI 配置维护时段的操作仅适用于 SQL 托管实例。

## <a name="launch-azure-cloud-shell"></a>启动 Azure Cloud Shell

Azure Cloud Shell 是免费的交互式 shell，可以使用它运行本文中的步骤。 它预安装有常用 Azure 工具并将其配置与帐户一起使用。 

若要打开 Cloud Shell，只需要从代码块的右上角选择“试一试”。  也可以通过转到 [https://shell.azure.com/cli](https://shell.azure.com/cli) 在单独的浏览器标签页中启动 Cloud Shell。 选择“复制”以复制代码块，将其粘贴到 Cloud Shell 中，然后按 Enter 来运行它。

## <a name="discover-available-maintenance-windows"></a>发现可用维护时段

设置维护时段时，每个区域会提供自己的维护时段选项，这些选项对应于数据库或池所在区域的时区。

### <a name="discover-sql-database-and-elastic-pool-maintenance-windows"></a>发现 SQL 数据库和弹性池维护时段

以下示例使用 [az maintenance public-configuration list](/cli/azure/maintenance/public-configuration#az_maintenance_public_configuration_list) 命令返回 eastus2 区域的可用维护时段。 对于数据库和弹性池，请将 `maintenanceScope` 设置为 `SQLDB`。

   ```azurecli
   location="eastus2"

   az maintenance public-configuration list --query "[?location=='$location'&&contains(maintenanceScope,'SQLDB')]"
   ```

### <a name="discover-sql-managed-instance-maintenance-windows"></a>发现 SQL 托管实例维护时段

以下示例使用 [az maintenance public-configuration list](/cli/azure/maintenance/public-configuration#az_maintenance_public_configuration_list) 命令返回 eastus2 区域的可用维护时段。 对于托管实例，请将 `maintenanceScope` 设置为 `SQLManagedInstance`。

   ```azurecli
   az maintenance public-configuration list --query "[?location=='eastus2'&&contains(maintenanceScope,'SQLManagedInstance')]"
   ```

## <a name="set-the-maintenance-window-while-creating-a-single-database"></a>在创建单一数据库时设置维护时段

以下示例使用 [az sql db create](/cli/azure/sql/db#az_sql_db_create) 命令创建一个新数据库并设置维护时段。 必须将 `--maint-config-id`（或 `-m`）设置为对你的数据库所在区域有效的值。 若要获取对区域有效的值，请参阅[发现可用维护时段](#discover-available-maintenance-windows)。


   ```azurecli
    # Set variables for your database
    resourceGroupName="your_resource_group_name"
    serverName="your_server_name"
    databaseName="your_db_name"

    # Set selected maintenance window
    maintenanceConfig="SQL_EastUS2_DB_1"

    # Create database
    az sql db create \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $databaseName \
      --edition GeneralPurpose \
      --family Gen5 \
      --capacity 2 \
      --maint-config-id $maintenanceConfig
   ```

## <a name="set-the-maintenance-window-while-creating-an-elastic-pool"></a>在创建弹性池时设置维护时段

以下示例使用 [az sql elastic-pool create](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_create) cmdlet 创建一个新弹性池并设置维护时段。 该维护时段是针对弹性池设置的，因此该池中的所有数据库都采用该池的维护时段计划。 必须将 `--maint-config-id`（或 `-m`）设置为对你的池所在区域有效的值。 若要获取对区域有效的值，请参阅[发现可用维护时段](#discover-available-maintenance-windows)。


   ```azurecli
    # Set variables for your pool
    resourceGroupName="your_resource_group_name"
    serverName="your_server_name"
    poolName="your_pool_name"

    # Set selected maintenance window
    maintenanceConfig="SQL_EastUS2_DB_2"

    # Create elastic pool
    az sql elastic-pool create \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $poolName \
      --edition GeneralPurpose \
      --family Gen5 \
      --capacity 2 \
      --maint-config-id $maintenanceConfig
   ```

## <a name="set-the-maintenance-window-while-creating-a-managed-instance"></a>在创建托管实例时设置维护时段

以下示例使用 [az sql mi create](/cli/azure/sql/mi#az_sql_mi_create) 创建一个新托管实例并设置维护时段。 该维护时段是针对实例设置的，因此该实例中的所有数据库都采用该实例的维护时段计划。 MaintenanceConfigName 必须是对实例所在区域有效的值。 若要获取对区域有效的值，请参阅[发现可用维护时段](#discover-available-maintenance-windows)。

   ```azurecli
   az sql mi create -g mygroup -n myinstance -l mylocation -i -u myusername -p mypassword --subnet /subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/Microsoft.Network/virtualNetworks/{VNETName}/subnets/{SubnetName} -m /subscriptions/{SubID}/providers/Microsoft.Maintenance/publicMaintenanceConfigurations/SQL_{Region}_{MaintenanceConfigName}
   ```

-----

## <a name="configure-maintenance-window-for-existing-databases"></a>为现有数据库配置维护时段


将所选的维护时段应用于数据库时，在某些情况下可能会经历短暂的重新配置过程（几秒钟），因为 Azure 要应用所需的更改。

# <a name="portal"></a>[Portal](#tab/azure-portal)

以下步骤使用 Azure 门户对现有数据库、弹性池或托管实例设置维护时段：


## <a name="set-the-maintenance-window-for-an-existing-database-or-elastic-pool"></a>为现有数据库或弹性池设置维护时段

1. 导航到要为其设置维护时段的 SQL 数据库或弹性池。
1. 在“设置”菜单中选择“维护”，然后选择所需的维护时段 。

   :::image type="content" source="media/maintenance-window-configure/maintenance.png" alt-text="SQL 数据库“维护”页":::


## <a name="set-the-maintenance-window-for-an-existing-managed-instance"></a>为现有托管实例设置维护时段

1. 导航到要为其设置维护时段的托管实例。
1. 在“设置”菜单中选择“维护”，然后选择所需的维护时段 。

   :::image type="content" source="media/maintenance-window-configure/maintenance-mi.png" alt-text="SQL 托管实例“维护”页":::



# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

## <a name="set-the-maintenance-window-for-an-existing-database"></a>为现有数据库设置维护时段

以下示例使用 [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) cmdlet 对现有数据库设置维护时段。 必须将 `-MaintenanceConfigurationId` 设置为对你的数据库所在区域有效的值。 若要获取对区域有效的值，请参阅[发现可用维护时段](#discover-available-maintenance-windows)。

   ```powershell-interactive
    # Select different maintenance window
    $maintenanceConfig = "SQL_EastUS2_DB_2"

    Write-host "Changing database maintenance window to ${maintenanceConfig} ..."
    $database = Set-AzSqlDatabase `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName `
      -MaintenanceConfigurationId $maintenanceConfig
    $database
   ```

## <a name="set-the-maintenance-window-on-an-existing-elastic-pool"></a>对现有弹性池设置维护时段

以下示例使用 [Set-AzSqlElasticPool](/powershell/module/az.sql/set-azsqlelasticpool) cmdlet 对现有弹性池设置维护时段。 请务必确保 `$maintenanceConfig` 值对于你的池所在区域有效。  若要获取对区域有效的值，请参阅[发现可用维护时段](#discover-available-maintenance-windows)。

   ```powershell-interactive
    # Select different maintenance window
    $maintenanceConfig = "SQL_EastUS2_DB_1"
    
    Write-host "Changing pool maintenance window to ${maintenanceConfig} ..."
    $pool = Set-AzSqlElasticPool `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -ElasticPoolName $poolName `
      -MaintenanceConfigurationId $maintenanceConfig
    $pool
   ```



## <a name="set-the-maintenance-window-on-an-existing-managed-instance"></a>对现有托管实例设置维护时段

以下示例使用 [Set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance) cmdlet 对现有托管实例设置维护时段。 请务必确保 `$maintenanceConfig` 值对于你的实例所在区域有效。  若要获取对区域有效的值，请参阅[发现可用维护时段](#discover-available-maintenance-windows)。


   ```powershell-interactive
   Set-AzSqlInstance -Name "your_mi_name" `
     -ResourceGroupName "your_resource_group_name" `
     -MaintenanceConfigurationId "/subscriptions/{SubID}/providers/Microsoft.Maintenance/publicMaintenanceConfigurations/SQL_{Region}_{MaintenanceConfigName}" `
     -AsJob
   ```

# <a name="cli"></a>[CLI](#tab/azure-cli)

以下示例演示如何使用 Azure CLI 配置维护时段。 可以[安装 Azure CLI](/cli/azure/install-azure-cli) 或使用 Azure Cloud Shell。

## <a name="set-the-maintenance-window-for-an-existing-database"></a>为现有数据库设置维护时段

以下示例使用 [az sql db update](/cli/azure/sql/db#az_sql_db_update) 命令对现有数据库设置维护时段。 必须将 `--maint-config-id`（或 `-m`）设置为对你的数据库所在区域有效的值。 若要获取对区域有效的值，请参阅[发现可用维护时段](#discover-available-maintenance-windows)。

   ```azurecli
    # Select different maintenance window
    maintenanceConfig="SQL_EastUS2_DB_2"

    # Update database
    az sql db update \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $databaseName \
      --maint-config-id $maintenanceConfig
   ```

## <a name="set-the-maintenance-window-on-an-existing-elastic-pool"></a>对现有弹性池设置维护时段

以下示例使用 [az sql elastic-pool update](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update) 命令对现有弹性池设置维护时段。 请务必确保 `maintenanceConfig` 值对于你的池所在区域有效。  若要获取对区域有效的值，请参阅[发现可用维护时段](#discover-available-maintenance-windows)。

   ```azurecli
    # Select different maintenance window
    maintenanceConfig="SQL_EastUS2_DB_1"

    # Update pool
    az sql elastic-pool update \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $poolName \
      --maint-config-id $maintenanceConfig
   ```

## <a name="set-the-maintenance-window-on-an-existing-managed-instance"></a>对现有托管实例设置维护时段

以下示例使用 [az sql mi update](/cli/azure/sql/mi#az_sql_mi_update) 设置维护时段。 该维护时段是针对实例设置的，因此该实例中的所有数据库都采用该实例的维护时段计划。 对于 `-MaintenanceConfigurationId`，MaintenanceConfigName 必须是对实例所在区域有效的值。 若要获取对区域有效的值，请参阅[发现可用维护时段](#discover-available-maintenance-windows)。

   ```azurecli
   az sql mi update -g mygroup  -n myinstance -m /subscriptions/{SubID}/providers/Microsoft.Maintenance/publicMaintenanceConfigurations/SQL_{Region}_{MainteanceConfigName}
   ```

-----

## <a name="cleanup-resources"></a>清理资源

完成后，请务必删除不再需要的资源，以免产生不必要的费用。

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. 导航到不再需要的 SQL 数据库或弹性池。
1. 在“概述”菜单中，选择用于删除该资源的选项。


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell-interactive
   # Delete database
   Remove-AzSqlDatabase `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName

   # Delete elastic pool
   Remove-AzSqlElasticPool `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -ElasticPoolName $poolName
   ```

# <a name="cli"></a>[CLI](#tab/azure-cli)

   ```azurecli
   az sql db delete \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $databaseName

   az sql elastic-pool delete \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $poolName
   ```

-----

## <a name="next-steps"></a>后续步骤

- 若要详细了解维护时段，请参阅[维护时段（预览版）](maintenance-window.md)。
- 有关详细信息，请参阅[维护时段常见问题解答](maintenance-window-faq.yml)。
- 若要了解如何优化性能，请参阅 [Azure SQL 数据库与 Azure SQL 托管实例中的监视和性能优化](monitor-tune-overview.md)。
