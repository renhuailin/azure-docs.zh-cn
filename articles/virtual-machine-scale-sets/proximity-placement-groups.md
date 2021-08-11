---
title: 虚拟机规模集的邻近放置组
description: 了解如何在 Azure 中为 Windows 虚拟机规模集创建和使用邻近放置组。
author: cynthn
ms.author: cynthn
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: proximity-placement-groups
ms.date: 07/01/2019
ms.reviewer: zivr
ms.custom: mimckitt, devx-track-azurepowershell
ms.openlocfilehash: 767ce9c132c7ca5f322175c54875a6870490052e
ms.sourcegitcommit: 832e92d3b81435c0aeb3d4edbe8f2c1f0aa8a46d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2021
ms.locfileid: "111556039"
---
# <a name="creating-and-using-proximity-placement-groups-using-powershell"></a>通过 PowerShell 创建和使用邻近放置组

为了使 VM 尽可能接近，以尽可能降低延迟，应该在[邻近放置组](../virtual-machines/co-location.md#proximity-placement-groups)中部署规模集。

邻近放置组是一种逻辑分组，用于确保 Azure 计算资源的物理位置彼此接近。 邻近放置组用于要求低延迟的工作负荷。


## <a name="create-a-proximity-placement-group"></a>创建邻近放置组
使用 [New-AzProximityPlacementGroup](/powershell/module/az.compute/new-azproximityplacementgroup) cmdlet 创建邻近放置组。 

```azurepowershell-interactive
$resourceGroup = "myPPGResourceGroup"
$location = "East US"
$ppgName = "myPPG"
New-AzResourceGroup -Name $resourceGroup -Location $location
$ppg = New-AzProximityPlacementGroup `
   -Location $location `
   -Name $ppgName `
   -ResourceGroupName $resourceGroup `
   -ProximityPlacementGroupType Standard
```

## <a name="list-proximity-placement-groups"></a>列出邻近放置组

可以使用 [Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup) cmdlet 列出所有邻近放置组。

```azurepowershell-interactive
Get-AzProximityPlacementGroup
```


## <a name="create-a-scale-set"></a>创建规模集

使用 [New-AzVMSS](/powershell/module/az.compute/new-azvmss) 创建规模集时，可使用 `-ProximityPlacementGroup $ppg.Id` 引用邻近放置组 ID，以便在邻近放置组中创建规模集。

```azurepowershell-interactive
$scalesetName = "myVM"

New-AzVmss `
  -ResourceGroupName $resourceGroup `
  -Location $location `
  -VMScaleSetName $scalesetName `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic" `
  -ProximityPlacementGroup $ppg.Id
```

可以使用 [Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup) 查看放置组中的实例。

```azurepowershell-interactive
  Get-AzProximityPlacementGroup `
   -ResourceId $ppg.Id | Format-Table `
   -Wrap `
   -Property VirtualMachineScaleSets
```

## <a name="next-steps"></a>后续步骤

也可使用 [Azure CLI](../virtual-machines/linux/proximity-placement-groups.md) 创建邻近放置组。
