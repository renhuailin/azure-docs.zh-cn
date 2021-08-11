---
title: 教程：将弹性池添加到故障转移组
description: 使用 Azure 门户、PowerShell 或 Azure CLI 将 Azure SQL 数据库弹性池添加到故障转移组。
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: seo-lt-2019 sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: tutorial
author: BustosMSFT
ms.author: robustos
ms.reviewer: mathoma
ms.date: 08/27/2019
ms.openlocfilehash: 0bd944f6e34383ea68dbf7fc410f6ae8b1050b2c
ms.sourcegitcommit: 5fabdc2ee2eb0bd5b588411f922ec58bc0d45962
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2021
ms.locfileid: "112541835"
---
# <a name="tutorial-add-an-azure-sql-database-elastic-pool-to-a-failover-group"></a>教程：将 Azure SQL 数据库弹性池添加到故障转移组
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

使用 Azure 门户为 Azure SQL 数据库弹性池配置故障转移组并测试故障转移。  在本教程中，您将学习如何执行以下操作：

> [!div class="checklist"]
>
> - 创建单一数据库。
> - 将数据库添加到弹性池中。
> - 在两个服务器之间创建两个弹性池的[故障转移组](auto-failover-group-overview.md)。
> - 测试故障转移。

## <a name="prerequisites"></a>先决条件

若要完成本教程，请确保做好以下准备：

- Azure 订阅。 [创建 Batch 帐户](https://azure.microsoft.com/free/)（如果还没有该帐户）。

## <a name="1---create-a-single-database"></a>1 - 创建单一数据库

[!INCLUDE [sql-database-create-single-database](../includes/sql-database-create-single-database.md)]

## <a name="2---add-the-database-to-an-elastic-pool"></a>2 - 将数据库添加到弹性池中

在此步骤中，我们将创建一个弹性池并向其添加数据库。

# <a name="portal"></a>[Portal](#tab/azure-portal)

使用 Azure 门户创建弹性池。

1. 在 Azure 门户的左侧菜单中选择“Azure SQL”。 如果 Azure SQL 不在列表中，请选择“所有服务”，然后在搜索框中键入 Azure SQL 。 （可选）选择“Azure SQL”旁边的星号将其收藏并将其添加为左侧导航栏中的项。
1. 选择“+ 添加”以打开“选择 SQL 部署选项”页。 可以通过在“数据库”磁贴上选择“显示详细信息”来查看有关不同数据库的其他信息。
1. 在“SQL 数据库”磁贴的“资源类型”下拉菜单中选择“弹性池”。 选择“创建”以创建弹性池。

    ![选择弹性池](./media/failover-group-add-elastic-pool-tutorial/select-azure-sql-elastic-pool.png)

1. 使用以下值配置弹性池：
   - **名称**：提供弹性池的唯一名称，例如 `myElasticPool`。
   - **订阅**：从下拉列表中选择订阅。
   - **ResourceGroup**：从下拉菜单中选择在第 1 部分创建的资源组 `myResourceGroup`。
   - **服务器**：从下拉菜单中选择在第 1 部分创建的服务器。  

       ![为弹性池创建新服务器](./media/failover-group-add-elastic-pool-tutorial/use-existing-server-for-elastic-pool.png)

   - **计算 + 存储**：选择“配置弹性池”以配置计算和存储，并将单一数据库添加到弹性池中。 在“池设置”选项卡上，保留默认值“第 5 代”、2 个 vCore 和 32GB 存储。

1. 在“配置”页上选择“数据库”选项卡，然后选择“添加数据库”。   选择在第 1 部分创建的数据库，然后选择“应用”将其添加到弹性池中。 再次选择“应用”以应用弹性池设置并关闭“配置”页。 

    ![将数据库添加到弹性池](./media/failover-group-add-elastic-pool-tutorial/add-database-to-elastic-pool.png)

1. 选择“查看 + 创建”以检查弹性池设置，然后选择“创建”以创建弹性池。 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

使用 PowerShell 创建弹性池和辅助服务器。

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   $poolName = "myElasticPool"
   $databaseName = "mySampleDatabase"
   $drLocation = "West US"
   $drServerName = "mysqlsecondary-$(Get-Random)"
   $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # The ip address range that you want to allow to access your server
   # Leaving at 0.0.0.0 will prevent outside-of-azure connections
   # $startIp = "0.0.0.0"
   # $endIp = "0.0.0.0"

   # Show randomized variables
   Write-host "DR Server name is" $drServerName
   Write-host "Failover group name is" $failoverGroupName

   # Create primary Gen5 elastic 2 vCore pool
   Write-host "Creating elastic pool..."
   $elasticPool = New-AzSqlElasticPool -ResourceGroupName $resourceGroupName `
       -ServerName $serverName `
       -ElasticPoolName $poolName `
       -Edition "GeneralPurpose" `
       -vCore 2 `
       -ComputeGeneration Gen5
   $elasticPool

   # Add single db into elastic pool
   Write-host "Creating elastic pool..."
   $addDatabase = Set-AzSqlDatabase -ResourceGroupName $resourceGroupName `
       -ServerName $serverName `
       -DatabaseName $databaseName `
       -ElasticPoolName $poolName
   $addDatabase
   ```

本教程的此部分使用以下 PowerShell cmdlet：

| 命令 | 注释 |
|---|---|
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | 为 Azure SQL 数据库创建弹性数据库池。|
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | 设置数据库的属性，或将现有数据库移到弹性池中。 |

---

## <a name="3---create-the-failover-group"></a>3 - 创建故障转移组

此步骤在现有的服务器与另一区域中的新服务器之间创建一个[故障转移组](auto-failover-group-overview.md)。 然后，将弹性池添加到该故障转移组。

# <a name="portal"></a>[Portal](#tab/azure-portal)

使用 Azure 门户创建故障转移组。

1. 在 [Azure 门户](https://portal.azure.com)的左侧菜单中选择“Azure SQL”。 如果 **Azure SQL** 不在列表中，请选择“所有服务”，然后在搜索框中键入 Azure SQL。 （可选）选择“Azure SQL”旁边的星号将其收藏并将其添加为左侧导航栏中的项。
1. 选择在上一部分中创建的弹性池，例如 `myElasticPool`。
1. 在“概述”窗格上，选择 **服务器名称** 下的服务器名称以打开服务器的设置。
  
    ![打开弹性池的服务器](./media/failover-group-add-elastic-pool-tutorial/server-for-elastic-pool.png)

1. 在“设置”窗格下选择“故障转移组”，然后选择“添加组”以创建新的故障转移组。  

    ![添加新的故障转移组](./media/failover-group-add-elastic-pool-tutorial/elastic-pool-failover-group.png)

1. 在“故障转移组”页上输入或选择以下值，然后选择“创建”： 
    - **故障转移组名称**：键入唯一的故障转移组名称，例如 `failovergrouptutorial`。
    - **辅助服务器**：选择“配置所需设置”选项，然后选择“创建新服务器”。 或者，可以选择现有的服务器作为辅助服务器。 为新的辅助服务器输入以下值之后，选择“选择”。
        - **服务器名称**：键入辅助服务器的唯一名称，例如 `mysqlsecondary`。
        - **服务器管理员登录名**：键入 `azureuser`
        - **密码**：键入符合密码要求的复杂密码。
        - 位置：从下拉列表中选择一个位置，例如 `East US`。 此位置不能与主服务器的位置相同。

       > [!NOTE]
       > 服务器登录名和防火墙设置必须与主服务器相匹配。

       ![为故障转移组创建辅助服务器](./media/failover-group-add-elastic-pool-tutorial/create-secondary-failover-server.png)

1. 选择“组中的数据库”，然后选择在第 2 部分创建的弹性池。 此时应会出现一条警告，提示你将在辅助服务器上创建弹性池。 选择该警告，然后选择“确定”以在辅助服务器上创建弹性池。 

   ![将弹性池添加到故障转移组中](./media/failover-group-add-elastic-pool-tutorial/add-elastic-pool-to-failover-group.png)

1. 选择“选择”以将弹性池设置应用到故障转移组，然后选择“创建”以创建故障转移组。  将弹性池添加到故障转移组的操作会自动启动异地复制过程。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

使用 PowerShell 创建故障转移组。

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $poolName = "myElasticPool"
   # $databaseName = "mySampleDatabase"
   # $drLocation = "West US"
   # $drServerName = "mysqlsecondary-$(Get-Random)"
   $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # Create a secondary server in the failover region
   Write-host "Creating a secondary server in the failover region..."
   New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -Location $drLocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
         -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   Write-host "Secondary server =" $drServerName

   # Create a server firewall rule that allows access from the specified IP range
   Write-host "Configuring firewall for secondary server..."
   New-AzSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
   Write-host "Firewall configured"

   # Create secondary Gen5 elastic 2 vCore pool
   Write-host "Creating secondary elastic pool..."
   $elasticPool = New-AzSqlElasticPool -ResourceGroupName $resourceGroupName `
       -ServerName $drServerName `
       -ElasticPoolName $poolName `
       -Edition "GeneralPurpose" `
       -vCore 2 `
       -ComputeGeneration Gen5
   $elasticPool

   # Create a failover group between the servers
   Write-host "Creating failover group..."
   New-AzSqlDatabaseFailoverGroup `
     ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -PartnerServerName $drServerName  `
      FailoverGroupName $failoverGroupName `
      FailoverPolicy Automatic `
      -GracePeriodWithDataLossHours 2
   Write-host "Failover group created successfully."

   # Add elastic pool to the failover group
   Write-host "Enumerating databases in elastic pool...."
   $FailoverGroup = Get-AzSqlDatabaseFailoverGroup `
                    -ResourceGroupName $resourceGroupName `
                    -ServerName $serverName `
                    -FailoverGroupName $failoverGroupName
   $databases = Get-AzSqlElasticPoolDatabase `
                  -ResourceGroupName $resourceGroupName `
                  -ServerName $serverName `
                  -ElasticPoolName $poolName
   Write-host "Adding databases to failover group..."
   $failoverGroup = $failoverGroup | Add-AzSqlDatabaseToFailoverGroup `
                                     -Database $databases
   $failoverGroup
   ```

本教程的此部分使用以下 PowerShell cmdlet：

| 命令 | 注释 |
|---|---|
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | 创建托管数据库和弹性池的服务器。 |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | 为服务器创建防火墙规则。 |
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | 为 Azure SQL 数据库创建弹性池。|
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | 新建故障转移组。 |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | 将一个或更多个 Azure SQL 数据库添加到故障转移组。 |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | 获取或列出 Azure SQL 数据库故障转移组。 |

---

## <a name="4---test-failover"></a>4 - 测试故障转移

此步骤将故障转移组故障转移到辅助服务器，然后使用 Azure 门户故障回复。

# <a name="portal"></a>[Portal](#tab/azure-portal)

使用 Azure 门户测试故障转移组的故障转移。

1. 在 [Azure 门户](https://portal.azure.com)的左侧菜单中选择“Azure SQL”。 如果 **Azure SQL** 不在列表中，请选择“所有服务”，然后在搜索框中键入 Azure SQL。 （可选）选择“Azure SQL”旁边的星号将其收藏并将其添加为左侧导航栏中的项。
1. 选择在上一部分中创建的弹性池，例如 `myElasticPool`。
1. 在 **服务器名称** 下选择服务器的名称以打开服务器的设置。

    ![打开弹性池的服务器](./media/failover-group-add-elastic-pool-tutorial/server-for-elastic-pool.png)

1. 在“设置”窗格下选择“故障转移组”，然后选择在第 2 部分创建的故障转移组。 
  
   ![在门户中选择故障转移组](./media/failover-group-add-elastic-pool-tutorial/select-failover-group.png)

1. 查看哪个服务器是主服务器，哪个服务器是辅助服务器。
1. 在“任务”窗格中选择“故障转移”，以故障转移包含弹性池的故障转移组。
1. 在告知将会断开 TDS 会话连接的警告中选择“是”。

   ![对包含数据库的故障转移组进行故障转移](./media/failover-group-add-elastic-pool-tutorial/failover-sql-db.png)

1. 查看哪个服务器是主服务器，哪个服务器是辅助服务器。 如果故障转移成功，这两个服务器的角色应会交换。
1. 再次选择“故障转移”，将故障转移组故障回复到原始设置。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

使用 PowerShell 测试故障转移组的故障转移。

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $poolName = "myElasticPool"
   # $databaseName = "mySampleDatabase"
   # $drLocation = "West US"
   # $drServerName = "mysqlsecondary-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # Check role of secondary replica
   Write-host "Confirming the secondary server is secondary...."
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole

   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..."
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failover group failed over to" $drServerName
   ```

将故障转移组故障转移到辅助服务器，然后使用 PowerShell 故障回复。

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $poolName = "myElasticPool"
   # $databaseName = "mySampleDatabase"
   # $drLocation = "West US"
   # $drServerName = "mysqlsecondary-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # Check role of secondary replica
   Write-host "Confirming the secondary server is now primary"
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole

   # Revert failover to primary server
   Write-host "Failing over failover group to the primary...."
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failover group failed over to" $serverName
   ```

本教程的此部分使用以下 PowerShell cmdlet：

| 命令 | 注释 |
|---|---|
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | 获取或列出 Azure SQL 数据库故障转移组。 |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| 执行 Azure SQL 数据库故障转移组的故障转移。 |

---

## <a name="clean-up-resources"></a>清理资源

通过删除资源组来清理资源。

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. 在 [Azure 门户](https://portal.azure.com)中导航到你的资源组。
1. 选择“删除资源组”即可删除该资源组中的所有资源以及该组本身。
1. 在文本框中键入资源组的名称 `myResourceGroup`，然后选择“删除”以删除该资源组。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

使用 PowerShell 清理资源。

   ```powershell-interactive
   # Set variables for your server and database
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"

   # Clean up resources by removing the resource group
   Write-host "Removing resource group..."
   Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
   Write-host "Resource group removed =" $resourceGroupName
   ```

本教程的此部分使用以下 PowerShell cmdlet：

| 命令 | 注释 |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 删除资源组 |

---

> [!IMPORTANT]
> 若要保留资源组但删除辅助数据库，请先将其从故障转移组中移除，然后再将其删除。 如果在从故障转移组中移除辅助数据库之前将其删除，则可能会导致不可预知的行为。

## <a name="full-script"></a>完整脚本

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/failover-groups/add-elastic-pool-to-failover-group-az-ps.ps1 "Add elastic pool to a failover group")]

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 创建用于存储所有资源的资源组。 |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | 创建托管数据库和弹性池的服务器。 |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | 为服务器创建防火墙规则。 |
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | 创建数据库。 |
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | 为 Azure SQL 数据库创建弹性数据库池。|
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | 设置数据库的属性，或将现有数据库移到弹性池中。 |
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | 新建故障转移组。 |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | 获取一个和多个 SQL 数据库中的数据库。 |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | 将一个或更多个 Azure SQL 数据库添加到故障转移组。 |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | 获取或列出 Azure SQL 数据库故障转移组。 |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| 执行 Azure SQL 数据库故障转移组的故障转移。 |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 删除资源组 |

# <a name="portal"></a>[Portal](#tab/azure-portal)

没有适用于 Azure 门户的脚本。

---

## <a name="next-steps"></a>后续步骤

在本教程中，你已将一个 Azure SQL 数据库弹性池添加到故障转移组，并测试了故障转移。 你已了解如何：

> [!div class="checklist"]
>
> - 创建单一数据库。
> - 将数据库添加到弹性池中。
> - 在两个服务器之间创建两个弹性池的[故障转移组](auto-failover-group-overview.md)。
> - 测试故障转移。

转到下一教程，其中介绍了如何使用 DMS 进行迁移。

> [!div class="nextstepaction"]
> [教程：使用 DMS 将 SQL Server 迁移到共用数据库](../../dms/tutorial-sql-server-to-azure-sql.md?toc=/azure/sql-database/toc.json)
