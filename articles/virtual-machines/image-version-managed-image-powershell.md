---
title: 将托管映像克隆到共享映像库
description: 了解如何使用 Azure PowerShell 将托管映像克隆到共享映像库中的某个映像版本。
author: cynthn
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 91d83cbc2af684057f7faad0d9621da1d049be25
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122689008"
---
# <a name="clone-a-managed-image-to-a-shared-image-gallery-image"></a>将托管映像克隆到共享映像库映像

适用于：:heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 灵活规模集 :heavy_check_mark: 统一规模集

如果打算将现有托管映像克隆并移动到共享映像库，可以直接从托管映像创建共享映像库映像。 测试新映像后，可以删除源托管映像。 还可以使用 [Azure CLI](image-version-managed-image-cli.md) 从托管映像迁移到共享映像库。

映像库中的映像具有两个组件，我们将在此示例中创建这两个组件：
- “映像定义”包含有关映像及其使用要求的信息。 这包括了该映像是 Windows 还是 Linux 映像、是专用映像还是通用映像、发行说明以及最低和最高内存要求。 它是某种映像类型的定义。 
- 使用共享映像库时，将使用 **映像版本** 来创建 VM。 可根据环境的需要创建多个映像版本。 创建 VM 时，将使用该映像版本来为 VM 创建新磁盘。 可以多次使用映像版本。


## <a name="before-you-begin"></a>准备阶段

若要完成本文，必须有一个现有的托管映像。 如果托管映像包含数据磁盘，则数据磁盘大小不能超过 1 TB。

通过本文进行操作时，请根据需要替换资源组和 VM 名称。

## <a name="get-the-gallery"></a>获取库

可以按名称列出所有库和映像定义。 结果采用 `gallery\image definition\image version` 格式。

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Compute/galleries | Format-Table
```

找到正确的库后，创建一个变量供稍后使用。 此示例获取 *myResourceGroup* 资源组中名为 *myGallery* 的库。

```azurepowershell-interactive
$gallery = Get-AzGallery `
   -Name myGallery `
   -ResourceGroupName myResourceGroup
```


## <a name="create-an-image-definition"></a>创建映像定义 

映像定义为映像创建一个逻辑分组。 它们用于管理有关映像的信息。 映像定义名称可能包含大写或小写字母、数字、点、短划线和句点。 

创建映像定义时，请确保它包含所有正确信息。 由于托管映像始终会通用化，因此应设置 `-OsState generalized`。 

若要详细了解可以为映像定义指定的值，请参阅[映像定义](./shared-image-galleries.md#image-definitions)。

使用 [New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion) 创建映像定义。 在此示例中，映像定义名为 *myImageDefinition*，适用于通用化 Windows OS。 若要使用 Linux OS 创建映像的定义，请使用 `-OsType Linux`。 

```azurepowershell-interactive
$imageDefinition = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState generalized `
   -OsType Windows `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```

> [!NOTE]
> 对于将包含从第三方映像继承的映像的映像定义，计划信息必须与第三方映像中的计划信息完全匹配。 创建映像定义时，通过添加 `-PurchasePlanName`、`-PurchasePlanProduct` 和 `-PurchasePlanPublisher` 在映像定义中包含计划信息。
>

## <a name="get-the-managed-image"></a>获取托管映像

可使用 [Get-AzImage](/powershell/module/az.compute/get-azimage) 查看资源组中可用的映像列表。 了解映像名称及其所在的资源组后，就可以再次使用 `Get-AzImage` 来获取映像对象并将其存储在变量中以便以后使用。 此示例将从“myResourceGroup”资源组获取名为“myImage”的映像，并将其分配给变量“$managedImage” 。 

```azurepowershell-interactive
$managedImage = Get-AzImage `
   -ImageName myImage `
   -ResourceGroupName myResourceGroup
```


## <a name="create-an-image-version"></a>创建映像版本

使用 [New-AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion) 从托管映像创建映像版本。 

允许用于映像版本的字符为数字和句点。 数字必须在 32 位整数范围内。 格式：*MajorVersion*.*MinorVersion*.*Patch*。

在此示例中，映像版本为 1.0.0，该版本被复制到美国中西部和美国中南部数据中心。 选择复制的目标区域时，请记住，你还需包括源区域作为复制的目标。 


```azurepowershell-interactive
$region1 = @{Name='South Central US';ReplicaCount=1}
$region2 = @{Name='West Central US';ReplicaCount=2}
$targetRegions = @($region1,$region2)
$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $imageDefinition.Name `
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $imageDefinition.ResourceGroupName `
   -Location $imageDefinition.Location `
   -TargetRegion $targetRegions  `
   -SourceImageId $managedImage.Id.ToString() `
   -PublishingProfileEndOfLifeDate '2020-12-31' `
   -asJob 
```

可能需要一段时间才能将该映像复制到所有目标区域，因此我们创建了作业，以便可以跟踪进度。 若要查看进度，请键入 `$job.State`。

```azurepowershell-interactive
$job.State
```


> [!NOTE]
> 需等待映像版本彻底生成并复制完毕，然后才能使用同一托管映像来创建另一映像版本。 
>
> 创建映像版本时，还可以通过添加 `-StorageAccountType Premium_LRS` 将映像存储在高级存储中，或者通过添加 `-StorageAccountType Standard_ZRS` 将其存储在[区域冗余存储](../storage/common/storage-redundancy.md)中。
>

## <a name="delete-the-managed-image"></a>删除托管映像

确认新映像版本正常工作后，可以删除托管映像。

```azurepowershell-interactive
Remove-AzImage `
   -ImageName $managedImage.Name `
   -ResourceGroupName $managedImage.ResourceGroupName
```

## <a name="next-steps"></a>后续步骤

确认复制完成后，可以从[通用化映像](vm-generalized-image-version-powershell.md)创建 VM。

有关如何提供购买计划信息的信息，请参阅[在创建映像时提供 Azure 市场购买计划信息](marketplace-images.md)。
