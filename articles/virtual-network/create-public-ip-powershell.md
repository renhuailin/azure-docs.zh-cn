---
title: 创建公共 IP - Azure PowerShell
titleSuffix: Azure Virtual Network
description: 了解如何使用 Azure PowerShell 创建公共 IP
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.topic: how-to
ms.date: 05/03/2021
ms.author: allensu
ms.openlocfilehash: dc9a6aa398a8cab2927bc8890ceaeb4d0026714c
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2021
ms.locfileid: "113434800"
---
# <a name="create-a-public-ip-address-using-azure-powershell"></a>使用 Azure PowerShell 创建公共 IP 地址

本文介绍如何使用 Azure PowerShell 来创建公共 IP 地址资源。 

有关支持公共 IP 的资源的详细信息，请参阅[公共 IP 地址](./public-ip-addresses.md)。
## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 本地安装的 Azure PowerShell 或 Azure Cloud Shell

如果选择在本地安装并使用 PowerShell，则本文需要 Azure PowerShell 模块 5.4.1 或更高版本。 运行 `Get-Module -ListAvailable Az` 查找已安装的版本。 如果需要进行升级，请参阅 [Install Azure PowerShell module](/powershell/azure/install-Az-ps)（安装 Azure PowerShell 模块）。 如果在本地运行 PowerShell，则还需运行 `Connect-AzAccount` 以创建与 Azure 的连接。

## <a name="create-a-resource-group"></a>创建资源组
Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。

在 eastus2 位置，使用名为 myResourceGroup 的 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 创建资源组。

```azurepowershell-interactive
$rg =@{
    Name = 'myResourceGroup'
    Location = 'eastus2'
}
New-AzResourceGroup @rg
```
## <a name="create-standard-sku-public-ip-with-zones"></a>创建带区域的标准 SKU 公共 IP

在本节中，将创建一个带区域的公共 IP。 公共 IP 地址可以是区域冗余，也可以是区域性的。

### <a name="zone-redundant"></a>区域冗余

>[!NOTE]
>以下命令适用于 Az.Network 模块版本 4.5.0 或更高版本。  有关当前正在使用的 Powershell 模块的详细信息，请参阅 [PowerShellGet 文档](/powershell/module/powershellget/)。

在 myResourceGroup 中使用 [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) 创建名为 myStandardZRPublicIP 的标准区域冗余公共 IPv4 地址。 

若要创建 IPv6 地址，请将“版本”参数设置为“IPv6” 。

```azurepowershell-interactive
$ip = @{
    Name = 'myStandardZRPublicIP'
    ResourceGroupName = 'myResourceGroup'
    Location = 'eastus2'
    Sku = 'Standard'
    AllocationMethod = 'Static'
    IpAddressVersion = 'IPv4'
    Zone = 1,2,3
}
New-AzPublicIpAddress @ip
```
> [!IMPORTANT]
> 对于版本低于 4.5.0 的 Az.Network 模块，请运行上述命令，而无需指定区域参数来创建区域冗余 IP 地址。 
>

### <a name="zonal"></a>局部区域

若要在 myResourceGroup 中名为 myStandardZonalPublicIP 的区域 2 中创建标准区域公共 IPv4 地址，请使用以下命令。

若要创建 IPv6 地址，请将“版本”参数设置为“IPv6” 。

```azurepowershell-interactive
$ip = @{
    Name = 'myStandardZonalPublicIP'
    ResourceGroupName = 'myResourceGroup'
    Location = 'eastus2'
    Sku = 'Standard'
    AllocationMethod = 'Static'
    IpAddressVersion = 'IPv4'
    Zone = 2
}
New-AzPublicIpAddress @ip
```
>[!NOTE]
>上述区域选项仅在具有[可用性区域](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)的区域中是有效选择。

## <a name="create-standard-public-ip-without-zones"></a>创建不带区域的标准公共 IP

在本部分，你将创建一个非区域性的 IP 地址。  

>[!NOTE]
>以下命令适用于 Az.Network 模块版本 4.5.0 或更高版本。  有关当前正在使用的 Powershell 模块的详细信息，请参阅 [PowerShellGet 文档](/powershell/module/powershellget/)。

在 myResourceGroup 中，使用 [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) 创建一个标准公共 IPv4 地址作为名为 myStandardPublicIP 的非区域性资源 。 

若要创建 IPv6 地址，请将“版本”参数设置为“IPv6” 。

```azurepowershell-interactive
$ip = @{
    Name = 'myStandardPublicIP'
    ResourceGroupName = 'myResourceGroup'
    Location = 'eastus2'
    Sku = 'Standard'
    AllocationMethod = 'Static'
    IpAddressVersion = 'IPv4'
}
New-AzPublicIpAddress @ip
```
在命令中删除“区域”参数对所有区域有效。  

对于不带[可用性区域](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)的区域中的标准公共 IP 地址，默认选择删除“区域”参数。

## <a name="create-a-basic-public-ip"></a>创建基本公共 IP

在本部分，你将创建基本公共 IP。 基本公共 IP 不支持可用性区域。

在 myResourceGroup 中使用 [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) 创建名为 myBasicPublicIP 的基本静态公共 IPv4 地址。  

若要创建 IPv6 地址，请将“版本”参数设置为“IPv6” 。 

```azurepowershell-interactive
$ip = @{
    Name = 'myStandardPublicIP'
    ResourceGroupName = 'myResourceGroup'
    Location = 'eastus2'
    Sku = 'Basic'
    AllocationMethod = 'Static'
    IpAddressVersion = 'IPv4'
}
New-AzPublicIpAddress @ip
```
如果可接受 IP 地址随时间的推移发生更改，可通过将 AllocationMethod 更改为 Dynamic 来选择动态 IP 分配 。 

>[!NOTE]
> 基本 IPv6 地址必须始终为“Dynamic”。

## <a name="routing-preference-and-tier"></a>路由首选项和层

标准 SKU 静态公共 IPv4 地址支持路由首选项或全局层功能。

### <a name="routing-preference"></a>路由首选项

默认情况下，公共 IP 地址的路由首选项设置为“Microsoft 网络”，该网络通过 Microsoft 的全球广域网向用户传送流量。  

相比传输 ISP 网络，选择“Internet”可最大限度减少 Microsoft 网络漫游，从而按成本优化费率传送流量。  

有关路由首选项的详细信息，请参阅[什么是路由首选项（预览）？](./routing-preference-overview.md)。

此命令使用类型为 Internet 的路由首选项创建新的标准区域冗余公用 IPv4 地址：

```azurepowershell-interactive
## Create IP tag for Internet and Routing Preference. ##
$tag = ${
    IpTagType = 'RoutingPreference'
    Tag = 'Internet'   
}
$ipTag = New-AzPublicIpTag @tag

## Create IP. ##
$ip = @{
    Name = 'myStandardPublicIP'
    ResourceGroupName = 'myResourceGroup'
    Location = 'eastus2'
    Sku = 'Standard'
    AllocationMethod = 'Static'
    IpAddressVersion = 'IPv4'
    IpTag = $ipTag
    Zone = 1,2,3   
}
New-AzPublicIpAddress @ip
```
### <a name="tier"></a>层

公共 IP 地址与单个区域相关联。 全局层使一个 IP 地址跨越多个区域。 跨区域负载均衡器的前端需要全局层。  

有关详细信息，请参阅[跨区域负载均衡器](../load-balancer/cross-region-overview.md)。

以下命令将创建一个全局 IPv4 地址。 此地址可与跨区域负载均衡器的前端相关联。

```azurepowershell-interactive
$ip = @{
    Name = 'myStandardPublicIP-Global'
    ResourceGroupName = 'myResourceGroup'
    Location = 'eastus2'
    Sku = 'Standard'
    AllocationMethod = 'Static'
    IpAddressVersion = 'IPv4'
    Tier = 'Global'
}
New-AzPublicIpAddress @ip
```
>[!NOTE]
>全局层地址不支持可用性区域。

## <a name="additional-information"></a>其他信息 

若要详细了解本操作指南中列出的各个参数，请参阅[管理公共 IP 地址](./virtual-network-public-ip-address.md#create-a-public-ip-address)。

## <a name="next-steps"></a>后续步骤
- [将公共 IP 地址关联到虚拟机](./associate-public-ip-address-vm.md#azure-portal)
- 详细了解 Azure 中的[公共 IP 地址](./public-ip-addresses.md#public-ip-addresses)。
- 详细了解所有[公共 IP 地址设置](virtual-network-public-ip-address.md#create-a-public-ip-address)。
