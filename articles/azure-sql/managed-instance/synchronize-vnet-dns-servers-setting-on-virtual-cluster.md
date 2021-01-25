---
title: 在 SQL 托管实例虚拟群集上同步虚拟网络的 DNS 服务器设置
description: 了解如何在 SQL 托管实例虚拟群集上同步虚拟网络 DNS 服务器设置。
services: sql-database
ms.service: sql-managed-instance
author: srdan-bozovic-msft
ms.author: srbozovi
ms.topic: how-to
ms.date: 01/17/2021
ms.openlocfilehash: 0da38475c0e3c766cabbf765ea89dc5714a5b830
ms.sourcegitcommit: 3c8964a946e3b2343eaf8aba54dee41b89acc123
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/25/2021
ms.locfileid: "98747561"
---
# <a name="synchronize-virtual-network-dns-servers-setting-on-sql-managed-instance-virtual-cluster"></a>在 SQL 托管实例虚拟群集上同步虚拟网络的 DNS 服务器设置
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

本文介绍何时以及如何在 SQL 托管实例虚拟群集上同步虚拟网络 DNS 服务器设置。

## <a name="when-to-synchronize-the-dns-setting"></a>何时同步 DNS 设置

有几个方案（如数据库邮件，将服务器链接到云或混合环境中的其他 SQL Server 实例）需要从 SQL 托管实例解析专用主机名。 在这种情况下，需要在 Azure 中配置自定义 DNS。 有关详细信息，请参阅为 [AZURE SQL 托管实例配置自定义 DNS](custom-dns-configure.md) 。

如果在创建托管实例 [虚拟群集](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture) 后实现此更改，则需要将虚拟群集上的 DNS 服务器设置与虚拟网络配置同步。

> [!IMPORTANT]
> 同步 DNS 服务器设置将影响虚拟群集中托管的所有托管实例。

## <a name="how-to-synchronize-the-dns-setting"></a>如何同步 DNS 设置

### <a name="azure-rbac-permissions-required"></a>所需的 Azure RBAC 权限

用户同步 DNS 服务器配置将需要以下 Azure 角色之一：

- “订阅所有者”角色或
- “托管实例参与者”角色或
- 具有以下权限的自定义角色：
  - `Microsoft.Sql/virtualClusters/updateManagedInstanceDnsServers/action`

### <a name="use-azure-powershell"></a>使用 Azure PowerShell

获取虚拟网络，其中 DNS 服务器设置已更新。

```PowerShell
$ResourceGroup = 'enter resource group of virtual network'
$VirtualNetworkName = 'enter virtual network name'
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroup $ResourceGroup -Name $VirtualNetworkName
```
使用 PowerShell 命令 [AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction) 为子网中的所有虚拟群集同步 DNS 服务器配置。

```PowerShell
Get-AzSqlVirtualCluster `
    | where SubnetId -match $virtualNetwork.Id `
    | select Id `
    | Invoke-AzResourceAction -Action updateManagedInstanceDnsServers -Force
```
### <a name="use-the-azure-cli"></a>使用 Azure CLI

获取虚拟网络，其中 DNS 服务器设置已更新。

```Azure CLI
resourceGroup="auto-failover-group"
virtualNetworkName="vnet-fog-eastus"
virtualNetwork=$(az network vnet show -g $resourceGroup -n $virtualNetworkName --query "id" -otsv)
```

使用 Azure CLI 命令 [az resource invoke](/cli/azure/resource?view=azure-cli-latest#az_resource_invoke_action) 来同步子网中所有虚拟群集的 DNS 服务器配置。

```Azure CLI
az sql virtual-cluster list --query "[? contains(subnetId,'$virtualNetwork')].id" -o tsv \
    | az resource invoke-action --action updateManagedInstanceDnsServers --ids @-
```
## <a name="next-steps"></a>后续步骤

- 详细了解如何配置自定义 DNS [为 AZURE SQL 配置自定义 dns 托管实例](custom-dns-configure.md)。
- 有关概述，请参阅[什么是 Azure SQL 托管实例？](sql-managed-instance-paas-overview.md)。
