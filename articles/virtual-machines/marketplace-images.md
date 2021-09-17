---
title: 使用 Azure PowerShell 指定市场购买计划信息
description: 了解如何在共享映像库中创建映像时指定 Azure 市场购买计划详细信息。
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 07/07/2020
ms.author: cynthn
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 52f4152a8f3ec1d2f812de74ed0bd238fb323330
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123451828"
---
# <a name="supply-azure-marketplace-purchase-plan-information-when-creating-images"></a>在创建映像时提供 Azure 市场购买计划信息

**适用于：** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 灵活规模集 :heavy_check_mark: 统一规模集

如果使用最初从 Azure 市场映像创建的源在共享库中创建映像，可能需要跟踪购买计划信息。 本文介绍了如何查找 VM 的购买计划信息以及之后如何在创建映像定义时使用该信息。 另外还介绍了如何在为映像创建 VM 时使用映像定义中的信息来简化提供购买计划信息。

若要详细了解如何查找和使用市场映像，请参阅[查找和使用 Azure 市场映像](./windows/cli-ps-findimage.md)。


## <a name="get-the-source-vm-information"></a>获取源 VM 信息
如果仍有原始 VM，可使用 Get-AzVM 从该 VM 中获取计划名称、发布者和产品信息。 此示例获取 myResourceGroup 资源组中名为 myVM 的 VM，然后显示 VM 的购买计划信息 。

```azurepowershell-interactive
$vm = Get-azvm `
   -ResourceGroupName myResourceGroup `
   -Name myVM
$vm.Plan
```

## <a name="create-the-image-definition"></a>创建映像定义

获取要用来存储映像的映像库。 可以先列出所有的库。

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Compute/galleries | Format-Table
```

然后为需要使用的库创建变量。 在此示例中，我们要在 myGalleryRG 资源组中为 myGallery 创建一个名为 `$gallery` 的变量 。

```azurepowershell-interactive
$gallery = Get-AzGallery `
   -Name myGallery `
   -ResourceGroupName myGalleryRG
```

使用 `-PurchasePlanPublisher`、`-PurchasePlanProduct` 和 `-PurchasePlanName` 参数创建映像定义。

```azurepowershell-interactive
 $imageDefinition = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState specialized `
   -OsType Linux `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU' `
   -PurchasePlanPublisher $vm.Plan.Publisher `
   -PurchasePlanProduct $vm.Plan.Product `
   -PurchasePlanName  $vm.Plan.Name
```

然后使用 [New-AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion) 创建[映像版本](image-version.md)。  


## <a name="create-the-vm"></a>创建 VM

在从映像创建 VM 时，可以使用映像定义中的信息，以利用 [Set-AzVMPlan](/powershell/module/az.compute/set-azvmplan) 传入发布者信息。


```azurepowershell-interactive
# Create some variables for the new VM.
$resourceGroup = "mySIGPubVM"
$location = "West Central US"
$vmName = "mySIGPubVM"

# Create a resource group
New-AzResourceGroup -Name $resourceGroup -Location $location

# Create the network resources.
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name mySubnet `
   -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -Name MYvNET `
   -AddressPrefix 192.168.0.0/16 `
   -Subnet $subnetConfig
$pip = New-AzPublicIpAddress `
   -ResourceGroupName $resourceGroup `
   -Location $location `
  -Name "mypublicdns$(Get-Random)" `
  -AllocationMethod Static `
  -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig `
   -Name myNetworkSecurityGroupRuleRDP  `
   -Protocol Tcp `
   -Direction Inbound `
   -Priority 1000 `
   -SourceAddressPrefix * `
   -SourcePortRange * `
   -DestinationAddressPrefix * `
   -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -Name myNetworkSecurityGroup `
   -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface `
   -Name $vmName `
   -ResourceGroupName $resourceGroup `
   -Location $location `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id `
  -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using Set-AzVMSourceImage -Id $imageDefinition.Id to use the latest available image version. Set-AZVMPlan is used to pass the plan information in for the VM.

$vmConfig = New-AzVMConfig `
   -VMName $vmName `
   -VMSize Standard_D1_v2   | `
   Set-AzVMSourceImage -Id $imageDefinition.Id | `
   Set-AzVMPlan `
     -Publisher $imageDefinition.PurchasePlan.Publisher `
     -Product $imageDefinition.PurchasePlan.Product `
     -Name $imageDefinition.PurchasePlan.Name | `
   Add-AzVMNetworkInterface -Id $nic.Id

# Create the virtual machine
New-AzVM `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -VM $vmConfig
```

## <a name="next-steps"></a>后续步骤

若要详细了解如何查找和使用市场映像，请参阅[查找和使用 Azure 市场映像](./windows/cli-ps-findimage.md)。
