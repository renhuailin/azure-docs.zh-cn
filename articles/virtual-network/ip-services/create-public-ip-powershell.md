---
title: 快速入门：创建公共 IP - PowerShell
titleSuffix: Azure Virtual Network
description: 在本快速入门中了解如何使用 Azure PowerShell 创建公共 IP
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: quickstart
ms.date: 10/01/2021
ms.author: allensu
ms.openlocfilehash: 51a59615d0f87943e195a3f189d96eccaf67623f
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129369057"
---
# <a name="quickstart-create-a-public-ip-address-using-powershell"></a>快速入门：使用 PowerShell 创建公共 IP 地址

本快速入门介绍如何创建 Azure 公共 IP 地址。 Azure 中的公共 IP 地址用于以公共方式连接到 Azure 资源。 公共 IP 地址有两种可用 SKU：基本和标准。 公共 IP 地址可分为两层：区域和全局。 公共 IP 地址的路由首选项在创建时设置。 可用的选项包括 Internet 路由和 Microsoft 网络路由。

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 本地安装的 Azure PowerShell 或 Azure Cloud Shell

如果选择在本地安装并使用 PowerShell，则本文需要 Azure PowerShell 模块 5.4.1 或更高版本。 运行 `Get-Module -ListAvailable Az` 查找已安装的版本。 如果需要进行升级，请参阅 [Install Azure PowerShell module](/powershell/azure/install-Az-ps)（安装 Azure PowerShell 模块）。 如果在本地运行 PowerShell，则还需运行 `Connect-AzAccount` 以创建与 Azure 的连接。

## <a name="create-a-resource-group"></a>创建资源组
Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。

使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 在“eastus2”位置创建名为“QuickStartCreateIP-rg”的资源组 。

```azurepowershell-interactive
$rg =@{
    Name = 'QuickStartCreateIP-rg'
    Location = 'eastus2'
}
New-AzResourceGroup @rg
```
## <a name="create-public-ip"></a>创建公共 IP

# <a name="standard-sku"></a>[**标准 SKU**](#tab/create-public-ip-standard)

>[!NOTE]
>对于生产工作负荷，建议使用标准 SKU 公共 IP。  有关 SKU 的详细信息，请参阅[公共 IP 地址](public-ip-addresses.md)。
>
>以下命令适用于 Az.Network 模块版本 4.5.0 或更高版本。  有关当前正在使用的 Powershell 模块的详细信息，请参阅 [PowerShellGet 文档](/powershell/module/powershellget/)。

在本部分，你将创建一个包含区域的公共 IP。 公共 IP 地址可以是区域冗余，也可以是区域性的。

使用 [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) 在 QuickStartCreateIP-rg 中创建名为 myStandardPublicIP 的标准区域冗余公共 IPv4 地址。 

若要创建 IPv6 地址，请将 `--IpAddressVersion` 参数修改为“IPv6” 。

```azurepowershell-interactive
$ip = @{
    Name = 'myStandardPublicIP'
    ResourceGroupName = 'QuickStartCreateIP-rg'
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

# <a name="basic-sku"></a>[**基本 SKU**](#tab/create-public-ip-basic)

>[!NOTE]
>对于生产工作负荷，建议使用标准 SKU 公共 IP。  有关 SKU 的详细信息，请参阅[公共 IP 地址](public-ip-addresses.md)。

在本部分，你将创建基本公共 IP。 基本公共 IP 不支持可用性区域。

使用 [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) 在 QuickStartCreateIP-rg 中创建名为 myBasicPublicIP 的基本静态公共 IPv4 地址。  

若要创建 IPv6 地址，请将 `--IpAddressVersion` 参数修改为“IPv6” 。 

```azurepowershell-interactive
$ip = @{
    Name = 'myBasicPublicIP'
    ResourceGroupName = 'QuickStartCreateIP-rg'
    Location = 'eastus2'
    Sku = 'Basic'
    AllocationMethod = 'Static'
    IpAddressVersion = 'IPv4'
}
New-AzPublicIpAddress @ip
```
如果可接受 IP 地址随时间的推移发生更改，可通过将 `-AllocationMethod` 更改为 Dynamic 来选择动态 IP 分配。  

>[!NOTE]
> 基本 IPv6 地址必须始终为“Dynamic”。

---

## <a name="create-a-zonal-or-no-zone-public-ip-address"></a>创建区域或非区域公共 IP 地址

此部分介绍如何创建区域或非区域公共 IP 地址。

# <a name="zonal"></a>[**局部区域**](#tab/create-public-ip-zonal)

若要在 QuickStartCreateIP-rg 的区域 2 中创建名为 myStandardPublicIP-zonal 的标准区域公共 IPv4 地址，请使用以下命令。

若要创建 IPv6 地址，请将 `--IpAddressVersion` 参数修改为“IPv6” 。

```azurepowershell-interactive
$ip = @{
    Name = 'myStandardPublicIP-zonal'
    ResourceGroupName = 'QuickStartCreateIP-rg'
    Location = 'eastus2'
    Sku = 'Standard'
    AllocationMethod = 'Static'
    IpAddressVersion = 'IPv4'
    Zone = 2
}
New-AzPublicIpAddress @ip
```
>[!NOTE]
>上述区域选项仅是具有[可用性区域](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)的区域中的有效选项。

# <a name="non-zonal"></a>[非区域](#tab/create-public-ip-non-zonal)

在本部分，你将创建一个非区域性的 IP 地址。  

>[!NOTE]
>以下命令适用于 Az.Network 模块版本 4.5.0 或更高版本。  有关当前正在使用的 PowerShell 模块的详细信息，请参阅 [PowerShellGet 文档](/powershell/module/powershellget/)。

使用 [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) 在 QuickStartCreateIP-rg 中创建一个标准公共 IPv4 地址作为名为 myStandardPublicIP-nozone 的非区域资源。 

若要创建 IPv6 地址，请将 `-IpAddressVersion` 参数修改为“IPv6” 。

```azurepowershell-interactive
$ip = @{
    Name = 'myStandardPublicIP-nozone'
    ResourceGroupName = 'QuickStartCreateIP-rg'
    Location = 'eastus2'
    Sku = 'Standard'
    AllocationMethod = 'Static'
    IpAddressVersion = 'IPv4'
}
New-AzPublicIpAddress @ip
```
在命令中删除 `-Zone` 参数的操作对所有区域有效。  

对于没有[可用性区域](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)的区域中的标准公共 IP 地址，默认选择删除 `-Zone` 参数。

---

## <a name="routing-preference-and-tier"></a>路由首选项和层

标准 SKU 静态公共 IPv4 地址支持路由首选项或全局层功能。

# <a name="routing-preference"></a>[路由首选项](#tab/routing-preference)

默认情况下，公共 IP 地址的路由首选项设置为“Microsoft 网络”，该网络通过 Microsoft 的全球广区网络向用户传送流量。  

相比传输 ISP 网络，选择“Internet”可最大限度减少 Microsoft 网络漫游，从而按成本优化费率传送流量。  

有关路由首选项的详细信息，请参阅[什么是路由首选项（预览）？](routing-preference-overview.md)。

此命令使用类型为 Internet 的路由首选项创建新的标准区域冗余公用 IPv4 地址：

```azurepowershell-interactive
## Create IP tag for Internet and Routing Preference. ##
$tag = @{
    IpTagType = 'RoutingPreference'
    Tag = 'Internet'   
}
$ipTag = New-AzPublicIpTag @tag

## Create IP. ##
$ip = @{
    Name = 'myStandardPublicIP-RP'
    ResourceGroupName = 'QuickStartCreateIP-rg'
    Location = 'eastus2'
    Sku = 'Standard'
    AllocationMethod = 'Static'
    IpAddressVersion = 'IPv4'
    IpTag = $ipTag
    Zone = 1,2,3   
}
New-AzPublicIpAddress @ip
```

# <a name="tier"></a>[**层**](#tab/tier)

公共 IP 地址与单个区域相关联。 全局层可跨多个区域扩展 IP 地址。 跨区域负载均衡器前端需要全局层。  

有关详细信息，请参阅[跨区域负载均衡器](../../load-balancer/cross-region-overview.md)。

以下命令将创建一个全局 IPv4 地址。 此地址可与跨区域负载均衡器的前端相关联。

```azurepowershell-interactive
$ip = @{
    Name = 'myStandardPublicIP-Global'
    ResourceGroupName = 'QuickStartCreateIP-rg'
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

---

## <a name="clean-up-resources"></a>清理资源

用完虚拟机和公共 IP 地址后，请使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 删除资源组及其包含的所有资源。

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'TutorVMRoutePref-rg'

```

## <a name="next-steps"></a>后续步骤

请继续学习下一篇文章，了解如何创建公共 IP 前缀：
> [!div class="nextstepaction"]
> [使用 PowerShell 创建公共 IP 前缀](create-public-ip-prefix-powershell.md)