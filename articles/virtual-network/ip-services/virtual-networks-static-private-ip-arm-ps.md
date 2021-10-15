---
title: 创建具有静态专用 IP 地址的 VM - Azure PowerShell
description: 了解如何使用 Azure PowerShell 创建具有静态专用 IP 地址的虚拟机。
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 10/01/2021
ms.custom: template-how-to
ms.openlocfilehash: a98fab6f07e2616e8f3732f28446c24e9051bf09
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129367395"
---
# <a name="create-a-virtual-machine-with-a-static-private-ip-address-using-azure-powershell"></a>使用 Azure PowerShell 创建具有静态专用 IP 地址的虚拟机

系统会从指定的范围自动为虚拟机 (VM) 分配专用 IP 地址。 此范围取决于部署 VM 的子网。 VM 会保留该地址，直到此 VM 被删除。 Azure 从你创建 VM 时所在的子网动态分配下一个可用的专用 IP 地址。 如果需要子网中的特定 IP 地址，请向 VM 分配静态 IP 地址。

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

## <a name="create-a-virtual-machine"></a>创建虚拟机

使用 [New-AzVM](/powershell/module/az.compute/new-azvm) 创建虚拟机。 

以下命令将创建一个 Windows Server 虚拟机。 出现提示时，请提供要用作该虚拟机登录凭据的用户名和密码：

```azurepowershell-interactive
## Create virtual machine. ##
$vm = @{
    ResourceGroupName = 'myResourceGroup'
    Location = 'East US 2'
    Name = 'myVM'
    PublicIpAddressName = 'myPublicIP'
}
New-AzVM @vm
```

## <a name="change-private-ip-address-to-static"></a>将专用 IP 地址更改为静态

在本部分中，将把之前创建的虚拟机专用 IP 地址从“动态”更改为“静态” 。 

使用 [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) 将虚拟网络配置放入变量中。 使用 [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) 将子网配置放入变量中。 使用 [Get-AzNetworkInterface](/powershell/module/az.network/get-aznetworkinterface) 获取网络接口配置并放入变量中。 使用 [Set-AzNetworkInterfaceIpConfig](/powershell/module/az.network/set-aznetworkinterfaceipconfig) 设置网络接口的配置。 最后，使用 [Set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface) 设置虚拟机的配置。

以下命令会将虚拟机专用 IP 地址更改为静态：

```azurepowershell-interactive
## Place virtual network configuration into a variable. ##
$net = @{
    Name = 'myVM'
    ResourceGroupName = 'myResourceGroup'
}
$vnet = Get-AzVirtualNetwork @net

## Place subnet configuration into a variable. ##
$sub = @{
    Name = 'myVM'
    VirtualNetwork = $vnet
}
$subnet = Get-AzVirtualNetworkSubnetConfig @sub

## Get name of network interface and place into a variable ##
$int1 = @{
    Name = 'myVM'
    ResourceGroupName = 'myResourceGroup'
}
$vm = Get-AzVM @int1

## Place network interface configuration into a variable. ##
$nic = Get-AzNetworkInterface -ResourceId $vm.NetworkProfile.NetworkInterfaces.Id

## Set interface configuration. ##
$config =@{
    Name = 'myVM'
    PrivateIpAddress = '192.168.1.4'
    Subnet = $subnet
}
$nic | Set-AzNetworkInterfaceIpConfig @config -Primary

## Save interface configuration. ##
$nic | Set-AzNetworkInterface
```

> [!WARNING]
> 虽然可以向操作系统添加专用 IP 地址设置，但建议不要在阅读[向操作系统添加专用 IP 地址](virtual-network-network-interface-addresses.md#private)之前这样做。

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组及其包含的所有资源，请使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 将其删除：

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>后续步骤

- 详细了解 Azure 中的[公共 IP 地址](public-ip-addresses.md#public-ip-addresses)。
- 详细了解所有[公共 IP 地址设置](virtual-network-public-ip-address.md#create-a-public-ip-address)。
- 详细了解[专用 IP 地址](private-ip-addresses.md)以及如何为 Azure 虚拟机分配[静态公共 IP 地址](virtual-network-network-interface-addresses.md#add-ip-addresses)。
- 详细了解如何创建 [Linux](../../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 和 [Windows](../../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 虚拟机。