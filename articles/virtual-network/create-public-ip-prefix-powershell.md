---
title: 创建公共 IP 地址前缀 - PowerShell
titlesuffix: Azure Virtual Network
description: 了解如何使用 PowerShell 创建公共 IP 地址前缀。
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 07/12/2021
ms.author: allensu
ms.openlocfilehash: 9e3c939574dcbccc775de3db8330a33a21ae4a6a
ms.sourcegitcommit: aaaa6ee55f5843ed69944f5c3869368e54793b48
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2021
ms.locfileid: "113667246"
---
# <a name="create-a-public-ip-address-prefix-using-powershell"></a>使用 PowerShell 创建公共 IP 地址前缀

了解公共 IP 地址前缀，以及如何创建、更改和删除此类地址。 公共 IP 地址前缀是一系列连续的标准 SKU 公共 IP 地址。 

创建公共 IP 地址资源时，可以从前缀分配一个静态公共 IP 地址，并将该地址关联到虚拟机、负载均衡器或其他资源。 有关详细信息，请参阅[公共 IP 地址前缀概述](public-ip-address-prefix.md)。

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

## <a name="create-a-public-ip-address-prefix"></a>创建公共 IP 地址前缀

在本部分中，你将使用 Azure PowerShell 创建区域冗余、区域和非区域公共 IP 前缀。 

示例中的前缀为：

* **IPv4** - /28（16 个地址）

* **IPv6** - /124（16 个地址）

有关可用前缀大小的详细信息，请参阅[前缀大小](public-ip-address-prefix.md#prefix-sizes)。

通过名为 myPublicIpPrefix 的 [New-AzPublicIpPrefix](/powershell/module/az.network/new-azpublicipprefix) 在 eastus2 位置创建公共 IP 前缀 。

### <a name="zone-redundant-ipv4-prefix"></a>区域冗余 IPv4 前缀

若要创建 IPv4 公共 IP 前缀，请在 IpAddressVersion 参数中输入“IPv4” 。 要创建区域冗余 IPv4 前缀，请在“区域”参数中输入“1、2、3” 。

```azurepowershell-interactive
$ipv4 =@{
    Name = 'myPublicIpPrefix'
    ResourceGroupName = 'myResourceGroup'
    Location = 'eastus2'
    PrefixLength = '28'
    IpAddressVersion = 'IPv4'
    Zone = 1,2,3
}
New-AzPublicIpPrefix @ipv4
```

### <a name="zonal-ipv4-prefix"></a>区域 IPv4 前缀

若要创建 IPv4 公共 IP 前缀，请在 IpAddressVersion 参数中输入“IPv4” 。 在“区域”参数中输入“2”，以在区域 2 中创建区域 IP 前缀 。

```azurepowershell-interactive
$ipv4 =@{
    Name = 'myPublicIpPrefix'
    ResourceGroupName = 'myResourceGroup'
    Location = 'eastus2'
    PrefixLength = '28'
    IpAddressVersion = 'IPv4'
    Zone = 2
}
New-AzPublicIpPrefix @ipv4
```

>[!NOTE]
>上述区域选项仅是具有[可用性区域](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)的区域中的有效选项。

### <a name="non-zonal-ipv4-prefix"></a>非区域性 IPv4 前缀

若要创建 IPv4 公共 IP 前缀，请在 IpAddressVersion 参数中输入“IPv4” 。 删除“区域”参数以创建非区域 IP 前缀。

```azurepowershell-interactive
$ipv4 =@{
    Name = 'myPublicIpPrefix'
    ResourceGroupName = 'myResourceGroup'
    Location = 'eastus2'
    PrefixLength = '28'
    IpAddressVersion = 'IPv4'
}
New-AzPublicIpPrefix @ipv4
```

在命令中删除“区域”参数对所有区域有效。  

对于不带[可用性区域](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)的区域中的标准公共 IP 地址，默认选择删除“区域”参数。

### <a name="zone-redundant-ipv6-prefix"></a>区域冗余 IPv6 前缀

要创建 IPv4 公共 IP 前缀，请在 IpAddressVersion 参数中输入 IPv6 。 要创建区域冗余 IPv6 前缀，请在“区域”参数中输入“1、2、3” 。

```azurepowershell-interactive
$ipv6 =@{
    Name = 'myPublicIpPrefix'
    ResourceGroupName = 'myResourceGroup'
    Location = 'eastus2'
    PrefixLength = '124'
    IpAddressVersion = 'IPv6'
    Zone = 1,2,3
}
New-AzPublicIpPrefix @ipv6
```

### <a name="zonal-ipv6-prefix"></a>区域 IPv6 前缀

若要创建 IPv6 公共 IP 前缀，请在 IpAddressVersion 参数中输入 IPv6 。 在“区域”参数中输入“2”，以在区域 2 中创建区域 IP 前缀 。

```azurepowershell-interactive
$ipv6 =@{
    Name = 'myPublicIpPrefix'
    ResourceGroupName = 'myResourceGroup'
    Location = 'eastus2'
    PrefixLength = '124'
    IpAddressVersion = 'IPv6'
    Zone = 2
}
New-AzPublicIpPrefix @ipv6
```

>[!NOTE]
>上述区域选项仅是具有[可用性区域](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)的区域中的有效选项。

### <a name="non-zonal-ipv6-prefix"></a>非区域性 IPv6 前缀

若要创建 IPv6 公共 IP 前缀，请在 IpAddressVersion 参数中输入 IPv6 。 删除“区域”参数以创建非区域 IP 前缀。

```azurepowershell-interactive
$ipv6 =@{
    Name = 'myPublicIpPrefix'
    ResourceGroupName = 'myResourceGroup'
    Location = 'eastus2'
    PrefixLength = '124'
    IpAddressVersion = 'IPv6'
}
New-AzPublicIpPrefix @ipv6
```

在命令中删除“区域”参数对所有区域有效。  

对于不带[可用性区域](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)的区域中的标准公共 IP 地址，默认选择删除“区域”参数。

## <a name="create-a-static-public-ip-address-from-a-prefix"></a>从前缀创建静态公共 IP 地址

在创建前缀后，必须从前缀创建静态 IP 地址。 在本部分，你将从先前创建的前缀创建一个静态 IP 地址。

创建 myPublicIpPrefix 前缀中具有 [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) 的公共 IP 地址。

### <a name="ipv4-address"></a>IPv4 地址

若要创建 IPv4 公共 IP 地址，请在 IpAddressVersion 参数中输入“IPv4” 。

```azurepowershell-interactive
$pf =@{
    Name = 'myPublicIpPrefix'
    ResourceGroupName = 'myResourceGroup'
}
$prefix = Get-AzPublicIpPrefix @pf

$ipv4 =@{
    Name = 'myPublicIpAddress'
    ResourceGroupName = 'myResourceGroup'
    Location = 'eastus2'
    Sku = 'Standard'
    Tier = 'Regional'
    AllocationMethod = 'Static'
    IpAddressVersion = 'IPv4'
    PublicIpPrefix = $prefix
}
New-AzPublicIpAddress @ipv4
```

### <a name="ipv6-address"></a>IPv6 地址

若要创建 IPv6 公共 IP 前缀，请在 IpAddressVersion 参数中输入 IPv6 。
```azurepowershell-interactive
$pf =@{
    Name = 'myPublicIpPrefix'
    ResourceGroupName = 'myResourceGroup'
}
$prefix = Get-AzPublicIpPrefix @pf

$ipv6 =@{
    Name = 'myPublicIpAddress'
    ResourceGroupName = 'myResourceGroup'
    Location = 'eastus2'
    Sku = 'Standard'
    Tier = 'Regional'
    AllocationMethod = 'Static'
    IpAddressVersion = 'IPv6'
    PublicIpPrefix = $prefix
}
New-AzPublicIpAddress @ipv6
```

>[!NOTE]
>仅使用标准 SKU 创建的静态公共 IP 地址可从前缀范围进行分配。 若要详细了解公共 IP 地址 SKU，请参阅[公共 IP 地址](./public-ip-addresses.md#public-ip-addresses)。

## <a name="delete-a-prefix"></a>删除前缀

本部分介绍如何删除前缀。

若要删除公共 IP 前缀，请使用 [ Remove-AzPublicIpPrefix](/powershell/module/az.network/remove-azpublicipprefix)。

```azurepowershell-interactive
$pr =@{
    Name = 'myPublicIpPrefix'
    ResourceGroupName = 'myResourceGroup'
}
Remove-AzPublicIpPrefix @pr
```

>[!NOTE]
>如果前缀中的地址关联到公共 IP 地址资源，必须先删除公共 IP 地址资源。 请参阅[删除公共 IP 地址](virtual-network-public-ip-address.md#view-modify-settings-for-or-delete-a-public-ip-address)。

## <a name="clean-up-resources"></a>清理资源

在本文中，你创建了一个公共 IP 前缀，并从该前缀创建了一个公共 IP。 

完成公共 IP 前缀后，删除资源组及其包含的所有资源：

```azurepowershell-interactive
Remove-AzResourceGroup -ResourceGroupName 'myResourceGroup'
```

## <a name="next-steps"></a>后续步骤

- 了解使用[公共 IP 前缀](public-ip-address-prefix.md)的方案和优势。
