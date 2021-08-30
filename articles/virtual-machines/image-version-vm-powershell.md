---
title: 从 VM 创建映像
description: 了解如何使用 Azure PowerShell，在共享映像库中从 Azure 中的现有 VM 创建映像。
author: cynthn
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b3b0de7b9229f8bfd43e41f26ccb7d7d7c790b92
ms.sourcegitcommit: abf31d2627316575e076e5f3445ce3259de32dac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2021
ms.locfileid: "114205108"
---
# <a name="create-an-image-from-a-vm"></a>从 VM 创建映像

如果要使用现有 VM 生成多个相同的 VM，你可以使用该 VM 在共享映像库中通过 Azure PowerShell 创建映像。 还可以使用 [Azure CLI](image-version-vm-cli.md) 从 VM 创建映像。

你可以使用 Azure PowerShell 从[专用化和通用化](./shared-image-galleries.md#generalized-and-specialized-images) VM 捕获映像。 

映像库中的映像具有两个组件，我们将在此示例中创建这两个组件：
- “映像定义”包含有关映像及其使用要求的信息。 这包括了该映像是 Windows 还是 Linux 映像、是专用映像还是通用映像、发行说明以及最低和最高内存要求。 它是某种映像类型的定义。 
- 使用共享映像库时，将使用 **映像版本** 来创建 VM。 可根据环境的需要创建多个映像版本。 创建 VM 时，将使用该映像版本来为 VM 创建新磁盘。 可以多次使用映像版本。


## <a name="before-you-begin"></a>准备阶段

若要完成本文中的操作，必须拥有现有的共享映像库，以及 Azure 中现有的 VM 作为源。 

如果 VM 附加了数据磁盘，则数据磁盘大小不能超过 1 TB。

通过本文进行操作时，请根据需要替换资源名称。


## <a name="get-the-gallery"></a>获取库

可以按名称列出所有库和映像定义。 结果的格式是 `gallery\image definition\image version`。

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Compute/galleries | Format-Table
```

找到正确的库和映像定义后，为它们创建变量以供稍后使用。 此示例会获取 myResourceGroup 资源组中名为 myGallery 的库 。

```azurepowershell-interactive
$gallery = Get-AzGallery `
   -Name myGallery `
   -ResourceGroupName myResourceGroup
```

## <a name="get-the-vm"></a>获取 VM

可以使用 [Get-AzVM](/powershell/module/az.compute/get-azvm) 查看资源组中可用的 VM 列表。 了解 VM 的名称及其所在资源组后，可以再次使用 `Get-AzVM` 来获取 VM 对象并将其存储在变量中，供稍后使用。 此示例从“myResourceGroup”资源组获取名为 sourceVM 的 VM，并将其分配给变量 $sourceVm 。 

```azurepowershell-interactive
$sourceVm = Get-AzVM `
   -Name sourceVM `
   -ResourceGroupName myResourceGroup
```

在使用 [Stop-AzVM](/powershell/module/az.compute/stop-azvm) 创建映像之前，建议停止/解除分配 VM。

```azurepowershell-interactive
Stop-AzVM `
   -ResourceGroupName $sourceVm.ResourceGroupName `
   -Name $sourceVm.Name `
   -Force
```

## <a name="create-an-image-definition"></a>创建映像定义 

映像定义为映像创建一个逻辑分组。 它们用于管理有关映像的信息。 映像定义名称可能包含大写或小写字母、数字、点、短划线和句点。 

制作映像定义时，请确保它具有所有正确信息。 如果已通用化 VM（使用适用于 Windows 的 Sysprep，或适用于 Linux 的 waagent -deprovision），则应使用 `-OsState generalized` 创建映像定义。 如果未通用化 VM，请使用 `-OsState specialized` 创建映像定义。

若要详细了解可以为映像定义指定的值，请参阅[映像定义](./shared-image-galleries.md#image-definitions)。

使用 [New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion) 创建映像定义。 

在此示例中，映像定义名为 myImageDefinition，适用于运行 Windows 的专用化 VM。 若要为使用 Linux 的映像创建定义，请使用 `-OsType Linux`。 

```azurepowershell-interactive
$imageDefinition = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState specialized `
   -OsType Windows `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```
> [!NOTE]
> 对于将包含从第三方映像继承的映像的映像定义，计划信息必须与第三方映像中的计划信息完全匹配。 创建映像定义时，通过添加 `-PurchasePlanName`、`-PurchasePlanProduct` 和 `-PurchasePlanPublisher` 在映像定义中包含计划信息。
>

## <a name="create-an-image-version"></a>创建映像版本

使用 [New-AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion) 创建映像版本。 

允许用于映像版本的字符为数字和句点。 数字必须在 32 位整数范围内。 格式：*MajorVersion*.*MinorVersion*.*Patch*。

在此示例中，映像版本为 1.0.0，该版本被复制到美国中西部和美国中南部数据中心。 选择复制的目标区域时，请记住，你还需包括源区域作为复制的目标。

若要从 VM 创建映像版本，请对 `-SourceImageId` 使用 `$vm.Id.ToString()`。

```azurepowershell-interactive
   $region1 = @{Name='South Central US';ReplicaCount=1}
   $region2 = @{Name='East US';ReplicaCount=2}
   $targetRegions = @($region1,$region2)

$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $imageDefinition.Name`
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -TargetRegion $targetRegions  `
   -SourceImageId $sourceVm.Id.ToString() `
   -PublishingProfileEndOfLifeDate '2020-12-01' `  
   -asJob 
```

可能需要一段时间才能将该映像复制到所有目标区域，因此我们创建了作业，以便可以跟踪进度。 要查看作业的进度，请键入 `$job.State`。

```azurepowershell-interactive
$job.State
```

> [!NOTE]
> 需等待映像版本彻底生成并复制完毕，然后才能使用同一托管映像来创建另一映像版本。
>
> 创建映像版本时，还可以通过添加 `-StorageAccountType Premium_LRS` 将映像存储在高级存储中，或者通过添加 `-StorageAccountType Standard_ZRS` 将其存储在[区域冗余存储](../storage/common/storage-redundancy.md)中。
>

## <a name="next-steps"></a>后续步骤

验证新映像版本正常工作后，即可创建 VM。 从[专用化映像版本](vm-specialized-image-version-powershell.md)或[通用化映像版本](vm-generalized-image-version-powershell.md)创建 VM。

有关如何提供购买计划信息的信息，请参阅[在创建映像时提供 Azure 市场购买计划信息](marketplace-images.md)。
