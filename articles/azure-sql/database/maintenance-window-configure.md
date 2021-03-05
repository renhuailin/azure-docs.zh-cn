---
title: " (预览配置维护时段) "
description: 了解如何设置在 Azure SQL 数据库、弹性池和托管实例数据库上执行计划内维护的时间。
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/04/2021
ms.openlocfilehash: 210f0c52a2b27492bfa2181473043df3537157d2
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2021
ms.locfileid: "102183193"
---
# <a name="configure-maintenance-window-preview"></a> (预览配置维护时段) 
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]


在资源创建过程中或在创建资源后，为 Azure SQL 数据库、弹性池或 Azure SQL 托管实例数据库配置 [维护时段 (预览) ](maintenance-window.md) 。 

*默认情况下，系统默认* 维护时段为早晨8点的 Azure 区域 (本地时间，以避免高峰) 的工作时间中断。 如果 *系统默认* 维护时段不是最佳时间，请选择其他可用维护时段之一。

更改为不同维护时段的功能不可用于每个服务级别或每个区域。 有关可用性的详细信息，请参阅 [维护时段可用性](maintenance-window.md#availability)。

> [!Important]
> 配置维护时段是一种长时间运行的异步操作，类似于更改 Azure SQL 资源的服务层。 该资源在操作过程中可用，但在操作结束时会发生短暂的故障转移，并且即使在中断长时间运行的事务的情况下，也通常会持续到8秒。 若要将故障转移的影响降至最低，应在高峰时段执行操作。

## <a name="configure-maintenance-window-during-database-creation"></a>在数据库创建期间配置维护时段 

# <a name="portal"></a>[Portal](#tab/azure-portal)

若要在创建数据库、弹性池或托管实例时配置维护时段，请在 "**其他设置**" 页上设置所需的 **维护时段**。 

## <a name="set-the-maintenance-window-while-creating-a-single-database-or-elastic-pool"></a>创建单一数据库或弹性池时设置维护时段

有关创建新数据库或池的分步信息，请参阅 [创建 AZURE SQL 数据库单一数据库](single-database-create-quickstart.md)。

   :::image type="content" source="media/maintenance-window-configure/additional-settings.png" alt-text="&quot;创建数据库其他设置&quot; 选项卡":::


## <a name="set-the-maintenance-window-while-creating-a-managed-instance"></a>创建托管实例时设置维护时段

有关创建新的托管实例的分步信息，请参阅 [创建 AZURE SQL 托管实例](../managed-instance/instance-create-quickstart.md)。

   :::image type="content" source="media/maintenance-window-configure/additional-settings-mi.png" alt-text="创建托管实例的其他设置选项卡":::




# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

下面的示例演示如何使用 Azure PowerShell 配置维护时段。 可以 [安装 Azure PowerShell](/powershell/azure/install-az-ps)或使用 Azure Cloud Shell。

## <a name="launch-azure-cloud-shell"></a>启动 Azure Cloud Shell

Azure Cloud Shell 是免费的交互式 shell，可以使用它运行本文中的步骤。 它预安装有常用 Azure 工具并将其配置与帐户一起使用。 

若要打开 Cloud Shell，只需要从代码块的右上角选择“试一试”。  也可以通过转到 [https://shell.azure.com/powershell](https://shell.azure.com/powershell) 在单独的浏览器标签页中启动 Cloud Shell。 选择“复制”以复制代码块，将其粘贴到 Cloud Shell 中，然后按 Enter 来运行它。


## <a name="discover-available-maintenance-windows"></a>发现可用维护时段

设置维护时段时，每个区域都有其自己的维护时段选项，这些选项对应于数据库或池所在区域的时区。 

### <a name="discover-sql-database-and-elastic-pool-maintenance-windows"></a>发现 SQL 数据库和弹性池维护时段 

下面的示例使用 [AzMaintenancePublicConfiguration](/powershell/module/az.maintenance/get-azmaintenancepublicconfiguration) cmdlet 返回 *eastus2* 区域的可用维护时段。 对于数据库和弹性池，将设置 `MaintenanceScope` 为 `SQLDB` 。

   ```powershell-interactive
   $location = "eastus2"

   Write-Host "Available maintenance schedules in ${location}:"
   $configurations = Get-AzMaintenancePublicConfiguration
   $configurations | ?{ $_.Location -eq $location -and $_.MaintenanceScope -eq "SQLDB"}
   ```

### <a name="discover-sql-managed-instance-maintenance-windows"></a>发现 SQL 托管实例维护时段 

下面的示例使用 [AzMaintenancePublicConfiguration](/powershell/module/az.maintenance/get-azmaintenancepublicconfiguration) cmdlet 返回 *eastus2* 区域的可用维护时段。 对于托管实例，将设置 `MaintenanceScope` 为 `SQLManagedInstance` 。

   ```powershell-interactive
   $location = "eastus2"

   Write-Host "Available maintenance schedules in ${location}:"
   $configurations = Get-AzMaintenancePublicConfiguration
   $configurations | ?{ $_.Location -eq $location -and $_.MaintenanceScope -eq "SQLManagedInstance"}
   ```


## <a name="set-the-maintenance-window-while-creating-a-single-database"></a>创建单一数据库时设置维护时段

以下示例创建一个新数据库，并使用 [AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) cmdlet 设置维护时段。 `-MaintenanceConfigurationId`对于您的数据库区域，必须设置为有效的值。 若要获取区域的有效值，请参阅 [发现可用维护时段](#discover-available-maintenance-windows)。


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



## <a name="set-the-maintenance-window-while-creating-an-elastic-pool"></a>创建弹性池时设置维护时段

以下示例创建一个新的弹性池，并使用 [AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) cmdlet 设置维护时段。 维护时段设置为弹性池，因此池中的所有数据库都有池的维护时段计划。 `-MaintenanceConfigurationId`对于池的区域，必须设置为有效的值。 若要获取区域的有效值，请参阅 [发现可用维护时段](#discover-available-maintenance-windows)。


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

## <a name="set-the-maintenance-window-while-creating-a-managed-instance"></a>创建托管实例时设置维护时段

以下示例创建新的托管实例，并使用 [AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) cmdlet 设置维护时段。 维护时段设置在实例上，因此实例中的所有数据库都具有实例的维护时段计划。 对于 `-MaintenanceConfigurationId` ， *MaintenanceConfigName* 必须是实例区域的有效值。 若要获取区域的有效值，请参阅 [发现可用维护时段](#discover-available-maintenance-windows)。


   ```powershell
   New-AzSqlInstance -Name "your_mi_name" `
     -ResourceGroupName "your_resource_group_name" `
     -Location "your_mi_location" `
     -SubnetId /subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/Microsoft.Network/virtualNetworks/{VNETName}/subnets/{SubnetName} `
     -MaintenanceConfigurationId "/subscriptions/{SubID}/providers/Microsoft.Maintenance/publicMaintenanceConfigurations/SQL_{Region}_{MaintenanceConfigName}" `
     -AsJob
   ```

# <a name="cli"></a>[CLI](#tab/azure-cli)

下面的示例演示如何使用 Azure CLI 配置维护时段。 可以 [安装 Azure CLI](/cli/azure/install-azure-cli)或使用 Azure Cloud Shell。 

使用 Azure CLI 配置维护时段仅适用于 SQL 托管实例。

## <a name="launch-azure-cloud-shell"></a>启动 Azure Cloud Shell

Azure Cloud Shell 是免费的交互式 shell，可以使用它运行本文中的步骤。 它预安装有常用 Azure 工具并将其配置与帐户一起使用。 

若要打开 Cloud Shell，只需要从代码块的右上角选择“试一试”。  也可以通过转到 [https://shell.azure.com/cli](https://shell.azure.com/cli) 在单独的浏览器标签页中启动 Cloud Shell。 选择“复制”以复制代码块，将其粘贴到 Cloud Shell 中，然后按 Enter 来运行它。

## <a name="discover-available-maintenance-windows"></a>发现可用维护时段

设置维护时段时，每个区域都有其自己的维护时段选项，这些选项对应于数据库或池所在区域的时区。

### <a name="discover-sql-database-and-elastic-pool-maintenance-windows"></a>发现 SQL 数据库和弹性池维护时段

下面的示例使用 [az 维护公共-configuration list](/cli/azure/ext/maintenance/maintenance/public-configuration#ext_maintenance_az_maintenance_public_configuration_list)命令返回 *eastus2* 区域的可用维护时段。 对于数据库和弹性池，将设置 `maintenanceScope` 为 `SQLDB` 。

   ```azurecli
   location="eastus2"

   az maintenance public-configuration list --query "[?location=='$location'&&contains(maintenanceScope,'SQLDB')]"
   ```

### <a name="discover-sql-managed-instance-maintenance-windows"></a>发现 SQL 托管实例维护时段

下面的示例使用 [az 维护公共-configuration list](/cli/azure/ext/maintenance/maintenance/public-configuration#ext_maintenance_az_maintenance_public_configuration_list)命令返回 *eastus2* 区域的可用维护时段。 对于托管实例，将设置 `maintenanceScope` 为 `SQLManagedInstance` 。

   ```azurecli
   az maintenance public-configuration list --query "[?location=='eastus2'&&contains(maintenanceScope,'SQLManagedInstance')]"
   ```

## <a name="set-the-maintenance-window-while-creating-a-single-database"></a>创建单一数据库时设置维护时段

下面的示例创建一个新数据库，并使用 [az sql db create](/cli/azure/sql/db#az_sql_db_create) 命令设置维护时段。 `--maint-config-id` `-m` 对于您的数据库区域，必须将 (或) 设置为有效的值。 若要获取区域的有效值，请参阅 [发现可用维护时段](#discover-available-maintenance-windows)。


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

## <a name="set-the-maintenance-window-while-creating-an-elastic-pool"></a>创建弹性池时设置维护时段

以下示例创建一个新的弹性池，并使用 [az sql 弹性池 create](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_create) cmdlet 设置维护时段。 维护时段设置为弹性池，因此池中的所有数据库都有池的维护时段计划。 `--maint-config-id`必须将 (或 `-m`) 设置为池区域的有效值。 若要获取区域的有效值，请参阅 [发现可用维护时段](#discover-available-maintenance-windows)。


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

## <a name="set-the-maintenance-window-while-creating-a-managed-instance"></a>创建托管实例时设置维护时段

以下示例创建新的托管实例，并使用 [az sql mi create](/cli/azure/sql/mi#az_sql_mi_create)设置维护时段。 维护时段设置在实例上，因此实例中的所有数据库都具有实例的维护时段计划。 对于你的实例的区域， *MaintenanceConfigName* 必须是有效的值。 若要获取区域的有效值，请参阅 [发现可用维护时段](#discover-available-maintenance-windows)。

   ```azurecli
   az sql mi create -g mygroup -n myinstance -l mylocation -i -u myusername -p mypassword --subnet /subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/Microsoft.Network/virtualNetworks/{VNETName}/subnets/{SubnetName} -m /subscriptions/{SubID}/providers/Microsoft.Maintenance/publicMaintenanceConfigurations/SQL_{Region}_{MaintenanceConfigName}
   ```

-----

## <a name="configure-maintenance-window-for-existing-databases"></a>为现有数据库配置维护时段


将维护时段选择应用到数据库时，短暂的故障转移 (几秒钟) 可能会在某些情况下出现，因为 Azure 会应用所需的更改。

# <a name="portal"></a>[Portal](#tab/azure-portal)

以下步骤使用 Azure 门户设置现有数据库、弹性池或托管实例上的维护时段：


## <a name="set-the-maintenance-window-for-an-existing-database-or-elastic-pool"></a>为现有数据库或弹性池设置维护时段

1. 导航到要为其设置维护时段的 SQL 数据库或弹性池。
1. 在 " **设置** " 菜单中选择 " **维护**"，然后选择所需的维护时段。

   :::image type="content" source="media/maintenance-window-configure/maintenance.png" alt-text="&quot;SQL 数据库维护&quot; 页":::


## <a name="set-the-maintenance-window-for-an-existing-managed-instance"></a>设置现有托管实例的维护时段

1. 导航到要为其设置维护时段的托管实例。
1. 在 " **设置** " 菜单中选择 " **维护**"，然后选择所需的维护时段。

   :::image type="content" source="media/maintenance-window-configure/maintenance-mi.png" alt-text="SQL 托管实例维护页":::



# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

## <a name="set-the-maintenance-window-for-an-existing-database"></a>设置现有数据库的维护时段

下面的示例使用 [AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) cmdlet 在现有数据库上设置维护时段。 `-MaintenanceConfigurationId`对于您的数据库区域，必须设置为有效的值。 若要获取区域的有效值，请参阅 [发现可用维护时段](#discover-available-maintenance-windows)。

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

## <a name="set-the-maintenance-window-on-an-existing-elastic-pool"></a>在现有弹性池上设置维护时段

下面的示例使用 [AzSqlElasticPool](/powershell/module/az.sql/set-azsqlelasticpool) cmdlet 在现有弹性池上设置维护时段。 务必要确保 `$maintenanceConfig` 该值是池区域的有效值，这一点很重要。  若要获取区域的有效值，请参阅 [发现可用维护时段](#discover-available-maintenance-windows)。

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



## <a name="set-the-maintenance-window-on-an-existing-managed-instance"></a>在现有托管实例上设置维护时段

下面的示例使用 [AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance) cmdlet 在现有托管实例上设置维护时段。 务必确保 `$maintenanceConfig` 该值必须为你的实例区域的有效值。  若要获取区域的有效值，请参阅 [发现可用维护时段](#discover-available-maintenance-windows)。


   ```powershell-interactive
   Set-AzSqlInstance -Name "your_mi_name" `
     -ResourceGroupName "your_resource_group_name" `
     -MaintenanceConfigurationId "/subscriptions/{SubID}/providers/Microsoft.Maintenance/publicMaintenanceConfigurations/SQL_{Region}_{MaintenanceConfigName}" `
     -AsJob
   ```

# <a name="cli"></a>[CLI](#tab/azure-cli)

下面的示例演示如何使用 Azure CLI 配置维护时段。 可以 [安装 Azure CLI](/cli/azure/install-azure-cli)或使用 Azure Cloud Shell。

## <a name="set-the-maintenance-window-for-an-existing-database"></a>设置现有数据库的维护时段

下面的示例使用 [az sql db update](/cli/azure/sql/db#az_sql_db_update) 命令在现有数据库上设置维护时段。 `--maint-config-id` `-m` 对于您的数据库区域，必须将 (或) 设置为有效的值。 若要获取区域的有效值，请参阅 [发现可用维护时段](#discover-available-maintenance-windows)。

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

## <a name="set-the-maintenance-window-on-an-existing-elastic-pool"></a>在现有弹性池上设置维护时段

下面的示例使用 [az sql 弹性池 update](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update) 命令在现有弹性池上设置维护时段。 务必要确保 `maintenanceConfig` 该值是池区域的有效值，这一点很重要。  若要获取区域的有效值，请参阅 [发现可用维护时段](#discover-available-maintenance-windows)。

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

## <a name="set-the-maintenance-window-on-an-existing-managed-instance"></a>在现有托管实例上设置维护时段

下面的示例使用 [az sql mi update](/cli/azure/sql/mi#az_sql_mi_update)设置维护时段。 维护时段设置在实例上，因此实例中的所有数据库都具有实例的维护时段计划。 对于 `-MaintenanceConfigurationId` ， *MaintenanceConfigName* 必须是实例区域的有效值。 若要获取区域的有效值，请参阅 [发现可用维护时段](#discover-available-maintenance-windows)。

   ```azurecli
   az sql mi update -g mygroup  -n myinstance -m /subscriptions/{SubID}/providers/Microsoft.Maintenance/publicMaintenanceConfigurations/SQL_{Region}_{MainteanceConfigName}
   ```

-----

## <a name="cleanup-resources"></a>清理资源

完成后，请务必删除不需要的资源，以免产生不必要的费用。

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. 导航到不再需要的 SQL 数据库或弹性池。
1. 在 " **概述** " 菜单中，选择删除资源的选项。


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

- 若要了解有关维护时段的详细信息，请参阅 [维护时段 (预览) ](maintenance-window.md)。
- 有关详细信息，请参阅 [维护窗口常见问题解答](maintenance-window-faq.yml)。
- 若要了解有关优化性能的信息，请参阅 [AZURE Sql 数据库和 AZURE sql 托管实例中的监视和性能优化](monitor-tune-overview.md)。
