---
title: 配置公共 IP 地址的路由首选项 - Azure PowerShell
titlesuffix: Azure Virtual Network
description: 了解如何使用 Azure PowerShell 配置公共 IP 地址的路由首选项。
services: virtual-network
documentationcenter: na
author: asudbring
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2021
ms.author: allensu
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9b2e38ca43bfa3bc8dcd229d6538f8a7784f9882
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129367355"
---
# <a name="configure-routing-preference-for-a-public-ip-address-using-azure-powershell"></a>使用 Azure PowerShell 配置公共 IP 地址的路由首选项

本文介绍了如何使用 Azure PowerShell 为公共 IP 地址配置通过 ISP 网络路由流量的路由首选项（即“Internet”选项）。 创建公共 IP 地址后，可以将它与以下 Azure 资源关联，以用于 Internet 的入站和出站流量：

* 虚拟机
* 虚拟机规模集
* Azure Kubernetes 服务 (AKS)
* 面向 Internet 的负载均衡器
* 应用程序网关
* Azure 防火墙

默认情况下，对于所有 Azure 服务，公共 IP 地址的路由首选项设置为“Microsoft 全球网络”，并且可以与任何 Azure 服务相关联。

如果还没有 Azure 订阅，请现在就创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]
如果选择在本地安装和使用 PowerShell，则本文需要 Azure PowerShell 模块 6.9.0 或更高版本。 运行 `Get-Module -ListAvailable Az` 查找已安装的版本。 如果需要进行升级，请参阅 [Install Azure PowerShell module](/powershell/azure/install-Az-ps)（安装 Azure PowerShell 模块）。 如果在本地运行 PowerShell，则还需运行 `Connect-AzAccount` 来创建与 Azure 的连接。

## <a name="create-a-resource-group"></a>创建资源组

使用 [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup) 创建资源组。 此示例在 eastus 位置中创建一个名为 myResourceGroup 的资源组：

```azurepowershell
$rg = New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-public-ip-with-internet-routing-preference"></a>创建具有“Internet”路由首选项的公共 IP

以下命令在美国东部 Azure 区域新建路由首选项类型为“Internet”的公共 IP：

```azurepowershell
$iptagtype="RoutingPreference"
$tagName = "Internet"
$ipTag = New-AzPublicIpTag -IpTagType $iptagtype -Tag $tagName 
# attach the tag
$publicIp = New-AzPublicIpAddress  `
-Name "MyPublicIP" `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location `
-IpTag $ipTag `
-AllocationMethod Static `
-Sku Standard `
-IpAddressVersion IPv4
```

可以将上面创建的公共 IP 地址与 [Windows](../../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 或 [Linux](../../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 虚拟机相关联。 请参阅“教程”页上的“CLI”部分：[将公共 IP 地址与虚拟机相关联](../../virtual-network/associate-public-ip-address-vm.md#azure-cli)，以将公共 IP 关联到 VM。 也可以将上面创建的公共 IP 地址与 [Azure 负载均衡器](../../load-balancer/load-balancer-overview.md)相关联，具体方法为将它分配到负载均衡器前端配置。 此公共 IP 地址充当负载均衡型虚拟 IP 地址 (VIP)。

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组、VM 和所有相关资源，可以使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 命令来删除它们。

```azurepowershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>后续步骤

- 详细了解[公共 IP 地址中的路由首选项](routing-preference-overview.md)。
- [使用 Azure PowerShell 为 VM 配置路由首选项](../../virtual-network/configure-routing-preference-virtual-machine-powershell.md)。
