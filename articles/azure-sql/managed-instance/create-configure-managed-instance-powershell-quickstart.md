---
title: 创建 Azure SQL 托管实例 - 快速入门
description: 使用 Azure PowerShell 创建 Azure SQL 托管实例的实例。
services: sql-managed-instance
ms.service: sql-managed-instance
ms.subservice: deployment-configuration
ms.custom: contperf-fy21q1, devx-track-azurecli, devx-track-azurepowershell
ms.devlang: ''
ms.topic: quickstart
author: MashaMSFT
ms.author: mathoma
ms.reviewer: ''
ms.date: 06/25/2021
ms.openlocfilehash: 6697cb9b89d3cb960f2def9ff4dcf478080b7b07
ms.sourcegitcommit: 6bd31ec35ac44d79debfe98a3ef32fb3522e3934
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2021
ms.locfileid: "113220951"
---
# <a name="quickstart-create-a-managed-instance-using-azure-powershell"></a>快速入门：使用 Azure PowerShell 创建托管实例

在此快速入门中，了解使用 Azure PowerShell 创建 [Azure SQL 托管实例](sql-managed-instance-paas-overview.md)的实例。 


## <a name="prerequisite"></a>先决条件

- 一个有效的 Azure 订阅。 如果没有帐户，请[创建一个免费帐户](https://azure.microsoft.com/free/)。
- 最新版本的 [Azure PowerShell](/powershell/azure/install-az-ps)。 

## <a name="set-variables"></a>设置变量

创建 SQL 托管实例需要在 Azure 中创建多个资源，因此 Azure PowerShell 命令依赖于变量来简化体验。 定义变量，然后在同一 PowerShell 会话内的每个部分中执行 cmdlet。 

```azurepowershell-interactive
$NSnetworkModels = "Microsoft.Azure.Commands.Network.Models"
$NScollections = "System.Collections.Generic"
# The SubscriptionId in which to create these objects
$SubscriptionId = ''
# Set the resource group name and location for your managed instance
$resourceGroupName = "myResourceGroup-$(Get-Random)"
$location = "eastus2"
# Set the networking values for your managed instance
$vNetName = "myVnet-$(Get-Random)"
$vNetAddressPrefix = "10.0.0.0/16"
$miSubnetName = "myMISubnet-$(Get-Random)"
$miSubnetAddressPrefix = "10.0.0.0/24"
#Set the managed instance name for the new managed instance
$instanceName = "myMIName-$(Get-Random)"
# Set the admin login and password for your managed instance
$miAdminSqlLogin = "SqlAdmin"
$miAdminSqlPassword = "ChangeYourAdminPassword1"
# Set the managed instance service tier, compute level, and license mode
$edition = "General Purpose"
$vCores = 4
$maxStorage = 128
$computeGeneration = "Gen5"
$license = "LicenseIncluded" #"BasePrice" or LicenseIncluded if you have don't have SQL Server licence that can be used for AHB discount
```

## <a name="create-resource-group"></a>创建资源组 

首先，连接到 Azure，设置订阅上下文，并创建资源组。 

为此，请执行此 PowerShell 脚本： 

```azurepowershell-interactive

## Connect to Azure
Connect-AzAccount

# Set subscription context
Set-AzContext -SubscriptionId $SubscriptionId 

# Create a resource group
$resourceGroup = New-AzResourceGroup -Name $resourceGroupName -Location $location -Tag @{Owner="SQLDB-Samples"}
```

## <a name="configure-networking"></a>配置网络

创建资源组后，请配置网络资源，例如虚拟网络、子网、网络安全组和路由表。 此示例演示了如何使用“为托管实例部署委派子网”脚本，在 GitHub 上以 [delegate-subnet.ps1](https://github.com/microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-managed-instance/delegate-subnet) 提供。

为此，请执行此 PowerShell 脚本： 

```azurepowershell-interactive

# Configure virtual network, subnets, network security group, and routing table
$virtualNetwork = New-AzVirtualNetwork `
                      -ResourceGroupName $resourceGroupName `
                      -Location $location `
                      -Name $vNetName `
                      -AddressPrefix $vNetAddressPrefix

                  Add-AzVirtualNetworkSubnetConfig `
                      -Name $miSubnetName `
                      -VirtualNetwork $virtualNetwork `
                      -AddressPrefix $miSubnetAddressPrefix |
                  Set-AzVirtualNetwork
                  
$scriptUrlBase = 'https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/manage/azure-sql-db-managed-instance/delegate-subnet'

$parameters = @{
    subscriptionId = $SubscriptionId
    resourceGroupName = $resourceGroupName
    virtualNetworkName = $vNetName
    subnetName = $miSubnetName
    }

Invoke-Command -ScriptBlock ([Scriptblock]::Create((iwr ($scriptUrlBase+'/delegateSubnet.ps1?t='+ [DateTime]::Now.Ticks)).Content)) -ArgumentList $parameters

$virtualNetwork = Get-AzVirtualNetwork -Name $vNetName -ResourceGroupName $resourceGroupName
$miSubnet = Get-AzVirtualNetworkSubnetConfig -Name $miSubnetName -VirtualNetwork $virtualNetwork
$miSubnetConfigId = $miSubnet.Id
```

## <a name="create-managed-instance"></a>创建托管实例 

为了增加安全性，请为 SQL 托管实例凭据创建复杂且随机的密码： 

```azurepowershell-interactive
# Create credentials
$secpassword = ConvertTo-SecureString $miAdminSqlPassword -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential ($miAdminSqlLogin, $secpassword)
```

然后创建 SQL 托管实例： 

```azurepowershell-interactive
# Create managed instance
New-AzSqlInstance -Name $instanceName `
                      -ResourceGroupName $resourceGroupName -Location $location -SubnetId $miSubnetConfigId `
                      -AdministratorCredential $credential `
                      -StorageSizeInGB $maxStorage -VCore $vCores -Edition $edition `
                      -ComputeGeneration $computeGeneration -LicenseType $license
```

此操作可能需要一些时间才能完成。 若要了解详细信息，请参阅[管理操作](management-operations-overview.md)。


## <a name="clean-up-resources"></a>清理资源

保留资源组和托管实例以继续执行后续步骤，并了解如何使用客户端虚拟机连接到 SQL 托管实例。 

用完这些资源后，可以删除创建的资源组，这也会删除该资源组中的服务器和单一数据库。

```azurepowershell-interactive
# Clean up deployment 
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```


## <a name="next-steps"></a>后续步骤

创建 SQL 托管实例后，部署客户端 VM 以连接到 SQL 托管实例，并还原示例数据库。 

> [!div class="nextstepaction"]
> [创建客户端 VM](connect-vm-instance-configure.md)
> [还原数据库](restore-sample-database-quickstart.md)


