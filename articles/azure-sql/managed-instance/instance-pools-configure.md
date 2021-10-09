---
title: 将 SQL 托管实例部署到实例池
titleSuffix: Azure SQL Managed Instance
description: 本文介绍如何创建和管理 Azure SQL 托管实例池（预览版）。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: deployment-configuration
ms.custom: devx-track-azurepowershell
ms.devlang: ''
ms.topic: how-to
author: urosmil
ms.author: urmilano
ms.reviewer: mathoma
ms.date: 09/05/2019
ms.openlocfilehash: a003370180471e02f4801bffd2477f0c50faa99d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128676535"
---
# <a name="deploy-azure-sql-managed-instance-to-an-instance-pool"></a>将 Azure SQL 托管实例部署到实例池
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

本文详细介绍了如何创建[实例池](instance-pools-overview.md)并向其部署 Azure SQL 托管实例。 

## <a name="instance-pool-operations"></a>实例池操作

下表显示了与实例池相关的可用操作以及它们是否在 Azure 门户、PowerShell 和 Azure CLI 中提供。

|Command|Azure 门户|PowerShell|Azure CLI|
|:---|:---|:---|:---|
|创建实例池|否|是|是|
|更新实例池（属性数目有限）|否 |是 | 是|
|检查实例池的使用情况和属性|否|是 | 是 |
|删除实例池|否|是|是|
|在实例池中创建托管实例|否|是|否|
|更新托管实例的资源使用情况|是 |是|否|
|检查托管实例的使用情况和属性|是|是|否|
|从池中删除托管实例|是|是|否|
|在池中的实例中创建数据库|是|是|否|
|从 SQL 托管实例中删除数据库|是|是|否|

# <a name="powershell"></a>[PowerShell](#tab/powershell)

要使用 PowerShell，请[安装最新版 PowerShell Core](/powershell/scripting/install/installing-powershell#powershell)，并按照说明[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps)。

可用的 [PowerShell 命令](/powershell/module/az.sql/)：

|Cmdlet |说明 |
|:---|:---|
|[New-AzSqlInstancePool](/powershell/module/az.sql/new-azsqlinstancepool/) | 创建 SQL 托管实例池。 |
|[Get-AzSqlInstancePool](/powershell/module/az.sql/get-azsqlinstancepool/) | 返回实例池的相关信息。 |
|[Set-AzSqlInstancePool](/powershell/module/az.sql/set-azsqlinstancepool/) | 在 SQL 托管实例中设置实例池的属性。 |
|[Remove-AzSqlInstancePool](/powershell/module/az.sql/remove-azsqlinstancepool/) | 删除 SQL 托管实例中的实例池。 |
|[Get-AzSqlInstancePoolUsage](/powershell/module/az.sql/get-azsqlinstancepoolusage/) | 返回有关 SQL 托管实例池使用情况的信息。 |

对于与池中的实例和单个实例相关的操作，请使用标准[托管实例命令](api-references-create-manage-instance.md#powershell-create-and-configure-managed-instances)，但在对池中的实例使用这些命令时，必须填充“实例池名称”属性。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

为 Azure CLI 准备环境。

[!INCLUDE [azure-cli-prepare-your-environment-no-header](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

可用的 [Azure CLI](/cli/azure/sql) 命令：

|Cmdlet |说明 |
|:---|:---|
|[az sql instance-pool create](/cli/azure/sql/instance-pool#az_sql_instance_pool_create) | 创建 SQL 托管实例池。 |
|[az sql instance-pool show](/cli/azure/sql/instance-pool#az_sql_instance_pool_show) | 返回实例池的相关信息。 |
|[az sql instance-pool update](/cli/azure/sql/instance-pool#az_sql_instance_pool_update) | 在 SQL 托管实例中设置或更新实例池的属性。 |
|[az sql instance-pool delete](/cli/azure/sql/instance-pool#az_sql_instance_pool_delete) | 删除 SQL 托管实例中的实例池。 |

---

## <a name="deployment-process"></a>部署过程

要将托管实例部署到实例池，必须首先部署实例池，这是一个运行时间较长的一次性操作，该操作的持续时间与部署[在一个空子网中创建的单个实例](sql-managed-instance-paas-overview.md#management-operations)的持续时间相同。 之后，你可以将托管实例部署到池中，这个操作相对较快，通常最长需要五分钟。 在此操作过程中，必须明确指定实例池参数。

在公共预览版中，仅支持使用 PowerShell 和 Azure 资源管理器模板来执行这两个操作。 Azure 门户体验当前不可用。

将托管实例部署到池之后，可通过 Azure 门户更改它在“定价层”页面上的属性。

## <a name="create-a-virtual-network-with-a-subnet"></a>创建带子网的虚拟网络 

要在同一虚拟网络中放置多个实例池，请参阅以下文章：

- [确定 Azure SQL 托管实例的 VNet 子网大小](vnet-subnet-determine-size.md)。
- 使用 [Azure 门户模板](virtual-network-subnet-create-arm-template.md)或按照[准备现有虚拟网络](vnet-existing-add-subnet.md)中的说明创建新的虚拟网络和子网。
 

## <a name="create-an-instance-pool"></a>创建实例池 

完成前面的步骤后，就可创建实例池了。

实例池存在以下限制：

- 公共预览版中仅提供常规用途和 Gen5。
- 池名称只能包含小写字母、数字和连字符，且不能以连字符开头。
- 如果要使用 Azure 混合权益，则在实例池级别应用它。 可在创建池时设置许可证类型，也可在创建后随时对其进行更新。

> [!IMPORTANT]
> 部署实例池的操作耗时较长，大约需要四个半小时。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要获取网络参数：

```powershell
$virtualNetwork = Get-AzVirtualNetwork -Name "miPoolVirtualNetwork" -ResourceGroupName "myResourceGroup"
$subnet = Get-AzVirtualNetworkSubnetConfig -Name "miPoolSubnet" -VirtualNetwork $virtualNetwork
```

若要创建实例池：

```powershell
$instancePool = New-AzSqlInstancePool `
  -ResourceGroupName "myResourceGroup" `
  -Name "mi-pool-name" `
  -SubnetId $subnet.Id `
  -LicenseType "LicenseIncluded" `
  -VCore 8 `
  -Edition "GeneralPurpose" `
  -ComputeGeneration "Gen5" `
  -Location "westeurope"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要获取虚拟网络参数，请运行以下命令：

```azurecli
az network vnet show --resource-group MyResourceGroup --name miPoolVirtualNetwork
```

若要获取虚拟子网参数，请运行以下命令：

```azurecli
az network vnet subnet show --resource group MyResourceGroup --name miPoolSubnet --vnet-name miPoolVirtualNetwork
```

若要创建实例池：

```azurecli
az sql instance-pool create
    --license-type LicenseIncluded 
    --location westeurope
    --name mi-pool-name
    --capacity 8
    --tier GeneralPurpose
    --family Gen5 
    --resrouce-group myResourceGroup
    --subnet miPoolSubnet
    --vnet-name miPoolVirtualNetwork
```

---

> [!IMPORTANT]
> 由于部署实例池的操作耗时较长，因此需要先等到它完成，然后才能运行本文中的其他后续步骤。

## <a name="create-a-managed-instance"></a>创建托管实例

成功部署实例池后，就可在其中创建托管实例。

要创建托管实例，请执行以下命令：

```powershell
$instanceOne = $instancePool | New-AzSqlInstance -Name "mi-one-name" -VCore 2 -StorageSizeInGB 256
```

在池中部署实例需要几分钟时间。 创建第一个实例后，可创建其他实例：

```powershell
$instanceTwo = $instancePool | New-AzSqlInstance -Name "mi-two-name" -VCore 4 -StorageSizeInGB 512
```

## <a name="create-a-database"></a>创建数据库 

要创建和管理池中的托管实例中的数据库，请使用单个实例命令。

若要在托管实例中创建数据库：

```powershell
$poolinstancedb = New-AzSqlInstanceDatabase -Name "mipooldb1" -InstanceName "poolmi-001" -ResourceGroupName "myResourceGroup"
```


## <a name="get-pool-usage"></a>获取池使用情况 
 
若要获取池中实例的列表：

```powershell
$instancePool | Get-AzSqlInstance
```


若要获取池的资源使用情况：

```powershell
$instancePool | Get-AzSqlInstancePoolUsage
```


若要获取池及其中实例的详细使用情况概述：

```powershell
$instancePool | Get-AzSqlInstancePoolUsage –ExpandChildren
```

若要列出实例中的数据库：

```powershell
$databases = Get-AzSqlInstanceDatabase -InstanceName "pool-mi-001" -ResourceGroupName "resource-group-name"
```


> [!NOTE]
> 若要查看每个实例池和池内部署的托管实例的数据库数量限制，请访问[实例池资源限制](instance-pools-overview.md#resource-limitations)部分。


## <a name="scale"></a>缩放 


使用数据库填充托管实例后，可能会遇到有关存储或性能的实例限制问题。 在这种情况下，如果尚未超出池的使用量，则可缩放实例。
缩放池中的托管实例的操作需耗时几分钟。 执行缩放操作的先决条件是实例池级别上有空闲的 vCore 和存储空间。

若要更新 vCore 数量和存储大小：

```powershell
$instanceOne | Set-AzSqlInstance -VCore 8 -StorageSizeInGB 512 -InstancePoolName "mi-pool-name"
```


若要仅更新存储大小：

```powershell
$instance | Set-AzSqlInstance -StorageSizeInGB 1024 -InstancePoolName "mi-pool-name"
```

## <a name="connect"></a>连接 

要连接到池中的托管实例，需要执行以下两个步骤：

1. [为实例启用公共终结点](#enable-the-public-endpoint)。
2. [将入站规则添加到网络安全组 (NSG)](#add-an-inbound-rule-to-the-network-security-group)。

完成这两个步骤后，可使用在实例创建过程中提供的公共终结点地址、端口和凭据连接到该实例。 

### <a name="enable-the-public-endpoint"></a>启用公共终结点

要为实例启用公共终结点，可通过 Azure 门户或使用以下 PowerShell 命令实现：


```powershell
$instanceOne | Set-AzSqlInstance -InstancePoolName "pool-mi-001" -PublicDataEndpointEnabled $true
```

也可在实例创建期间设置此参数。

### <a name="add-an-inbound-rule-to-the-network-security-group"></a>向网络安全组添加入站规则 

可通过 Azure 门户或使用 PowerShell 命令来完成此步骤，也可在为托管实例准备好子网后随时执行。

有关详细信息，请参阅[允许网络安全组上的公共终结点流量](public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group)。


## <a name="move-an-existing-single-instance-to-a-pool"></a>将现有的单个实例移到池中
 
公共预览版存在很多局限，其中一个就是无法将实例移入和移出池。 解决方案依赖于数据库从池外部的实例到已在池中的实例的时间点还原。 

两个实例都必须在同一订阅和区域中。 当前不支持跨区域和跨订阅还原。

该过程会导致停机一段时间。

若要移动现有数据库：

1. 暂停要从中迁移的托管实例上的工作负载。
2. 生成用于创建系统数据库的脚本，然后在实例池中的实例上执行这些脚本。
3. 执行各个数据库从单个实例到池中实例的时间点还原。

    ```powershell
    $resourceGroupName = "my resource group name"
    $managedInstanceName = "my managed instance name"
    $databaseName = "my source database name"
    $pointInTime = "2019-08-21T08:51:39.3882806Z"
    $targetDatabase = "name of the new database that will be created"
    $targetResourceGroupName = "resource group of instance pool"
    $targetInstanceName = "pool instance name"
       
    Restore-AzSqlInstanceDatabase -FromPointInTimeBackup `
      -ResourceGroupName $resourceGroupName `
      -InstanceName $managedInstanceName `
      -Name $databaseName `
      -PointInTime $pointInTime `
      -TargetInstanceDatabaseName $targetDatabase `
      -TargetResourceGroupName $targetResourceGroupName `
      -TargetInstanceName $targetInstanceName
    ```

4. 将应用程序指向新实例，并恢复其工作负载。

如果有多个数据库，请对每个数据库重复此过程。


## <a name="next-steps"></a>后续步骤

- 有关功能和比较列表，请参阅 [SQL 常用功能](../database/features-comparison.md)。
- 有关 VNet 配置的详细信息，请参阅 [SQL 托管实例 VNet 配置](connectivity-architecture-overview.md)。
- 有关创建托管实例以及从备份文件还原数据库的快速入门，请参阅[创建托管实例](instance-create-quickstart.md)。
- 有关使用 Azure 数据库迁移服务进行迁移的教程，请参阅[使用数据库迁移服务进行 SQL 托管实例迁移](../../dms/tutorial-sql-server-to-managed-instance.md)。
- 有关使用内置故障排除智能对 SQL 托管实例数据库性能进行的高级监视，请参阅[使用 Azure SQL Analytics 监视 Azure SQL 托管实例](../../azure-monitor/insights/azure-sql.md)。
- 有关定价信息，请参阅 [SQL 托管实例定价](https://azure.microsoft.com/pricing/details/sql-database/managed/)。
