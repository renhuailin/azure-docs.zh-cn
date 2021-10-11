---
title: 教程：为 VM 配置路由首选项 - Azure PowerShell
description: 本教程介绍如何使用 Azure PowerShell 来创建具有已设置所选路由首选项的公共 IP 地址的 VM。
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: tutorial
ms.date: 10/01/2021
ms.custom: template-tutorial
ms.openlocfilehash: 5442bbd177feab409daa7ed5eb5bad95aeed9e04
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129368789"
---
# <a name="tutorial-configure-routing-preference-for-a-vm-using-azure-powershell"></a>教程：使用 Azure PowerShell 为 VM 配置路由首选项

本教程介绍如何为虚拟机配置路由首选项。 如果你选择 Internet 作为路由首选项，来自虚拟机 (VM) 的 Internet 限制流量会通过 ISP 网络进行路由。 默认是通过 Microsoft 全球网络路由。

在本教程中，你将了解：

> [!div class="checklist"]
> * 创建为 Internet 路由首选项配置的公共 IP 地址。
> * 创建虚拟机。
> * 验证公共 IP 地址是否设置为 Internet 路由首选项。

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 本地安装的 Azure PowerShell 或 Azure Cloud Shell

如果选择在本地安装并使用 PowerShell，则本文需要 Azure PowerShell 模块 5.4.1 或更高版本。 运行 `Get-Module -ListAvailable Az` 查找已安装的版本。 如果需要进行升级，请参阅 [Install Azure PowerShell module](/powershell/azure/install-Az-ps)（安装 Azure PowerShell 模块）。 如果在本地运行 PowerShell，则还需运行 `Connect-AzAccount` 以创建与 Azure 的连接。

## <a name="create-a-resource-group"></a>创建资源组

Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。

在 westus2 位置，使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 创建一个名为“TutorVMRoutePref-rg”的资源组 。

```azurepowershell-interactive
New-AzResourceGroup -Name 'TutorVMRoutePref-rg' -Location 'westus2'

```

## <a name="create-a-public-ip-address"></a>创建公共 IP 地址

在 TutorVMRoutePref-rg 中，使用 [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) 创建一个名为 myPublicIP 的标准区域冗余公共 IPv4 地址 。 Internet 的标记作为 PowerShell 命令中的一个参数应用于公共 IP 地址，以启用 Internet 路由首选项  。

```azurepowershell-interactive
## Create IP tag for Internet and Routing Preference. ##
$tag = @{
    IpTagType = 'RoutingPreference'
    Tag = 'Internet'   
}
$ipTag = New-AzPublicIpTag @tag

## Create IP. ##
$ip = @{
    Name = 'myPublicIP'
    ResourceGroupName = 'TutorVMRoutePref-rg'
    Location = 'westus2'
    Sku = 'Standard'
    AllocationMethod = 'Static'
    IpAddressVersion = 'IPv4'
    IpTag = $ipTag
    Zone = 1,2,3   
}
New-AzPublicIpAddress @ip
```

## <a name="create-virtual-machine"></a>创建虚拟机

使用 [New-AzVM](/powershell/module/az.compute/new-azvm) 创建虚拟机。 上一部分中创建的公共 IP 地址作为 PowerShell 命令的一部分添加，并在创建过程中附加到 VM。

```azurepowershell-interactive
## Create virtual machine. ##
$vm = @{
    ResourceGroupName = 'TutorVMRoutePref-rg'
    Location = 'West US 2'
    Name = 'myVM'
    PublicIpAddressName = 'myPublicIP'
}
New-AzVM @vm
```

## <a name="verify-internet-routing-preference"></a>验证 Internet 路由首选项

使用 [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) 验证是否为公共 IP 地址配置了 Internet 路由首选项。

```azurepowershell-interactive
$ip = @{
    ResourceGroupName = 'TutorVMRoutePref-rg'
    Name = 'myPublicIP'
}  
Get-AzPublicIPAddress @ip | select -ExpandProperty IpTags

```

## <a name="clean-up-resources"></a>清理资源

用完虚拟机和公共 IP 地址后，请使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 删除资源组及其包含的所有资源。

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'TutorVMRoutePref-rg'

```

## <a name="next-steps"></a>后续步骤

请继续学习下一篇文章，了解如何创建具有混合路由首选项的虚拟机：
> [!div class="nextstepaction"]
> [为虚拟机配置两个路由首选项选项](routing-preference-mixed-network-adapter-portal.md)

