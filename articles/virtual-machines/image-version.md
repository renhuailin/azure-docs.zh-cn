---
title: 创建映像定义和映像版本
description: 了解如何在 Azure 的共享映像库中创建映像。
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 08/31/2021
ms.author: cynthn
ms.custom: ''
ms.openlocfilehash: f0ff3a0cc6bc228951fa47eb5723c520684d1dc5
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123452296"
---
# <a name="create-an-image-definition-and-an-image-version"></a>创建映像定义和映像版本 

[共享映像库](shared-image-galleries.md)大大简化了整个组织中的自定义映像共享。 自定义映像类似于市场映像，不同的是自定义映像的创建者是自己。 自定义映像可用于启动部署任务，例如预加载应用程序、应用程序配置和其他 OS 配置。 

使用共享映像库，你可以在 AAD 租户内在同一区域或跨区域与组织中的其他用户共享自定义 VM 映像。 选择要共享哪些映像，要在哪些区域中共享，以及希望与谁共享它们。 你可以创建多个库，以便可以按逻辑方式对共享映像进行分组。 

共享映像库功能具有多种资源类型：

[!INCLUDE [virtual-machines-shared-image-gallery-resources](./includes/virtual-machines-shared-image-gallery-resources.md)]


## <a name="before-you-begin"></a>开始之前

若要完成本文中的步骤，必须已有一个共享映像库，以及 Azure 中可用的映像的源。 映像源可以是：
- 订阅中的 VM。 可以从[专用化和通用化](shared-image-galleries.md#generalized-and-specialized-images) VM 中捕获映像。 
- 托管映像，
- 托管 OS 和数据磁盘。
- 存储帐户中作为 VHD 的 OS 和数据磁盘。
- 同一订阅中相同库或其他库中的其他映像版本。

如果映像包含数据磁盘，则数据磁盘大小不能超过 1 TB。

映像定义名称可能包含大写或小写字母、数字、点、短划线和句点。 若要详细了解可以为映像定义指定的值，请参阅[映像定义](shared-image-galleries.md#image-definitions)。

允许用于映像版本的字符为数字和句点。 数字必须在 32 位整数范围内。 格式：*MajorVersion*.*MinorVersion*.*Patch*。

通过本文进行操作时，请根据需要替换资源名称。

## <a name="create-an-image"></a>创建映像 

选择下面的选项来创建映像定义和映像版本：

### <a name="portal"></a>[门户](#tab/portal)

若要在门户中从 VM 创建映像，请参阅[捕获 VM 的映像](capture-image-portal.md)。 

若要使用 VM 以外的源创建映像，请执行以下步骤。

1. 转到 [Azure 门户](https://portal.azure.com)，然后搜索并选择“共享映像库”。
1. 从列表中选择需要使用的库。
1. 在映像库的页面上，从页面顶部选择“添加新的映像定义”。 
1. 在“向共享映像库添加新的映像定义”页上的“基本信息”选项卡中，选择“区域”。 
1. 对于“映像定义名称”，键入一个类似 myImageDefinition 的名称。
1. 对于“操作系统”，根据你的源选择正确的选项。  
1. 对于“VM 代系”，根据你的源选择相应的选项。 大多数情况下都会选择“第 1 代”。 有关详细信息，请参阅[对第 2 代 VM 的支持](generation-2.md)。
1. 对于“操作系统状态”，根据你的源选择相应的选项。 有关详细信息，请参阅[通用化和专用化](shared-image-galleries.md#generalized-and-specialized-images)。
1. 对于“发布者”，键入一个类似 myPublisher 的唯一名称。 
1. 对于“产品/服务”，键入一个类似 myOffer 的唯一名称。
1. 对于“SKU”，键入一个类似 mySKU 的唯一名称。
1. 在页面底部，选择“查看 + 创建”。
1. 映像定义通过验证后，请选择“创建”。
1. 部署完成后，选择“转到资源”。
1. 在映像定义页面的“开始使用”选项卡上，选择“创建版本”。
1. 在“区域”中，选择你需要创建映像的区域。 在某些情况下，源必须位于创建映像的同一区域中。 如果在后面的下拉列表中看不到你的源，请尝试更改映像的区域。 稍后始终可以将映像复制到其他区域。
1. 对于“版本号”，键入一个类似 1.0.0 的数字。 映像版本名称应遵循“主要版本.次要版本.修补版本”格式（使用整数）。   
1. 在“源”中，从下拉列表中选择要用于源的文件类型。 有关每种源类型的具体详细信息，请参阅下表。

    | 源 | 其他字段 |
    |---|---|
    | 磁盘或快照 | - 对于“OS 磁盘”，在下拉列表中选择对应的磁盘或快照。 <br> - 若要添加数据磁盘，请键入 LUN 编号，然后从下拉列表中选择数据磁盘。 |
    | 映像版本 | - 从下拉列表中选择“源库”。 <br> - 从下拉列表中选择正确的映像定义。 <br>- 从下拉列表中选择要使用的现有映像版本。 |
    | 托管映像 | \- 从下拉列表中选择“源映像”。 <br>托管映像必须位于与你在“实例详细信息”中选择的相同区域内。
    | 存储帐户中的 VHD | 选择“浏览”，选择 VHD 的存储帐户。 |

1. 在“从最新版本中排除”中，保留默认值“否”，除非你在使用 `latest`（而不是版本号）创建 VM 时不希望使用此版本。
1. 对于“生命周期结束日期”，从日历中选择你认为此版本应停止使用的日期。
1. 在“复制””选项卡中，从下拉列表中选择存储类型。
1. 设置“默认复制计数”，你可以为你添加的每个区域覆盖此计数。 
1. 需要复制到源区域，因此列表中的第一个副本将处于创建映像的区域。 若要添加更多副本，可以从下拉列表中选择区域，并根据需要调整副本计数。
1. 完成操作后，选择“查看 + 创建”。 Azure 将验证配置。
1. 映像版本通过验证后，请选择“创建”。
1. 部署完成后，选择“转到资源”。

可能需要花费一段时间才能将映像复制到所有目标区域。

还可以从门户中将现有 VM 捕获为映像。 有关详细信息，请参阅[在门户中创建 VM 映像](capture-image-portal.md)。

### <a name="cli"></a>[CLI](#tab/cli)

映像定义为映像创建一个逻辑分组。 它们用于管理有关映像版本的信息，这些版本是在其中创建的。

使用 [az sig image-definition create](/cli/azure/sig/image-definition#az_sig_image_definition_create) 在库中创建一个映像定义。 请确保映像定义的类型正确。 如果已通用化 VM（使用适用于 Windows 的 Sysprep，或适用于 Linux 的 waagent -deprovision），则应使用 `--os-state generalized` 创建通用化映像定义。 若要在不删除现有用户帐户的情况下使用 VM，请使用 `--os-state specialized` 创建专用化映像定义。

有关可为映像定义指定的参数的详细信息，请参阅[映像定义](shared-image-galleries.md#image-definitions)。

在此示例中，映像定义名为 myImageDefinition，适用于[专用化](shared-image-galleries.md#generalized-and-specialized-images) Linux OS 映像。 若要使用 Windows OS 创建映像的定义，请使用 `--os-type Windows`。 

```azurecli-interactive 
az sig image-definition create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --publisher myPublisher \
   --offer myOffer \
   --sku mySKU \
   --os-type Linux \
   --os-state specialized
```

> [!NOTE]
> 对于将包含从第三方市场映像继承的映像的映像定义，计划信息必须与第三方映像中的计划信息完全一致。 创建映像定义时，通过添加 `--plan-name`、`--plan-product` 和 `--plan-publisher` 在映像定义中包含计划信息。
>

**创建映像版本**

使用 [az sig image version create](/cli/azure/sig/image-version#az_sig_image_version_create) 创建映像版本。  

用于创建映像的语法会发生变化，具体取决于你使用的源。 只要你只有一个 OS 源，就可以混用源类型。 也可以对每个数据磁盘使用不同的源。

| 源  | 参数集 |
|---|---|
| **OS 磁盘**| |
| 使用 VM ID 的 VM| `--managed-image <Resource ID of the VM>` |
| 托管映像或其他映像版本 | `--managed-image <Resource ID of the managed image or image version` |
| 快照或托管磁盘 | `--os-snapshot <Resource ID of the snapshot or managed disk>` |
| 存储帐户中的 VHD | `--os-vhd-uri <URI> --os-vhd-storage-account <storage account name>`.  | 
| **数据磁盘** |
| 快照或托管磁盘 | `--data-snapshots <Resource ID of the snapshot or managed disk> --data-snapshot-luns <LUN number>` |
| 存储帐户中的 VHD | `--data-vhds-sa <storageaccountname> --data-vhds-uris <URI> --data-vhds-luns <LUN number>` |

有关如何为映像指定不同源的详细示例，请参阅 [az sig image-version create 示例](/cli/azure/sig/image-version#az_sig_image_version_create-examples)。

在下面的示例中，我们将从 VM 创建一个映像。 映像版本为 1.0.0。我们将使用区域冗余存储在“美国中西部”区域创建 2 个副本，在“美国中南部”区域创建 1 个副本，在“美国东部 2”区域创建 1 个副本。 复制区域必须包含源 VM 所在的区域。

在创建映像之前，建议停止/解除分配 VM。

请将此示例中的 `--managed-image` 值替换为 VM 的 ID。

```azurecli-interactive 
az sig image-version create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "westcentralus" "southcentralus=1" "eastus=1=standard_zrs" \
   --replica-count 2 \
   --managed-image "/subscriptions/<Subscription ID>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM"
```

> [!NOTE]
> 需等待映像版本彻底生成并复制完毕，然后才能使用同一托管映像来创建另一映像版本。
>
> 创建映像版本时，还可以通过添加 `--storage-account-type  premium_lrs` 将映像存储在高级存储中，或者通过添加 `--storage-account-type  standard_zrs` 将其存储在[区域冗余存储](../storage/common/storage-redundancy.md)中。

### <a name="powershell"></a>[PowerShell](#tab/powershell)

映像定义为映像创建一个逻辑分组。 制作映像定义时，请确保它具有所有正确信息。 如果你已通用化映像的源（使用适用于 Windows 的 Sysprep，或适用于 Linux 的 waagent -deprovision），则应使用 `-OsState generalized` 创建映像定义。 如果未通用化源，请使用 `-OsState specialized` 创建映像定义。

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
> 对于将包含从第三方映像继承的映像的映像定义，计划信息必须与第三方映像中的计划信息完全一致。 创建映像定义时，通过添加 `-PurchasePlanName`、`-PurchasePlanProduct` 和 `-PurchasePlanPublisher` 在映像定义中包含计划信息。
>

**创建映像版本**

使用 [New-AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion) 创建映像版本。 

用于创建映像的语法会发生变化，具体取决于你使用的源。 

| 源  | 参数集 |
|---|---|
| **OS 磁盘**| |
| 使用 VM ID 的 VM| `-SourceImageId <Resource ID of the VM>` |
| 托管映像或其他映像版本 | `-SourceImageId <Resource ID of the managed image or image version` |
| 快照或托管磁盘 | `-OSDiskImage <Resource ID of the snapshot or managed disk>` |
| **数据磁盘** |
| 快照或托管磁盘 | `-DataDiskImage @{Source = @{Id=<source_id>}; Lun=<LUN>; SizeInGB = <Size in GB>; HostCaching = <Caching> }` |


在下面的示例中，我们将从 VM 创建一个映像版本。 在使用 [Stop-AzVM](/powershell/module/az.compute/stop-azvm) 创建映像之前，建议停止/解除分配 VM。

在此示例中，映像版本为 1.0.0，该版本被复制到美国中西部和美国中南部数据中心。 选择复制的目标区域时，请记住，你还需包括源区域作为复制的目标。


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

### <a name="rest"></a>[REST](#tab/rest)

使用 [REST API](/rest/api/compute/gallery-images/create-or-update) 创建映像定义

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/galleries/{galleryName}/images/{galleryDefinitionName}?api-version=2019-12-01

{
    "location": "eastus",
    "properties": {
        "hyperVGeneration": "V1",
        "identifier": {
            "offer": "myOffer",
            "publisher": "myPublisher",
            "sku": "mySKU"
        },
        "osState": "Specialized",
        "osType": "Linux",
    },
}
```

使用 [REST API](/rest/api/compute/gallery-image-versions/create-or-update) 创建映像版本。 在此示例中，我们将从 VM 创建一个映像版本。 若要使用其他源，请传入源的资源 ID（例如，传入 OS 磁盘快照的 ID）。

```rest
# @name imageVersion
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/galleries/{galleryName}/images/{galleryDefinitionName}/versions/{galleryImageVersionName}?api-version=2019-12-01

{
    "location": "{region}",
    "properties": {
        "publishingProfile": {
            "endOfLifeDate": "2024-12-02T00:00:00+00:00",
            "replicaCount": 1,
            "storageAccountType": "Standard_ZRS",
            "targetRegions": [
                {
                    "name": "eastus",
                    "regionalReplicaCount": 2,
                    "storageAccountType": "Standard_LRS",
                },
                {
                    "name": "westus2",
                }
            ]
        },
        "storageProfile": {
            "source": {
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}"
            }
        }
    }
}
```

---


## <a name="next-steps"></a>后续步骤

有关如何提供购买计划信息的信息，请参阅[在创建映像时提供 Azure 市场购买计划信息](marketplace-images.md)。
