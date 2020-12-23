---
title: 从映像版本创建托管磁盘
description: 从共享映像库中的映像版本创建托管磁盘。
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/27/2020
ms.author: cynthn
ms.reviewer: olayemio
ms.openlocfilehash: 40d3ff736194c4634b949af52ee7b09db20dd06d
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2020
ms.locfileid: "95026125"
---
# <a name="create-a-managed-disk-from-an-image-version"></a>从映像版本创建托管磁盘

如果需要，可以从映像版本中将 OS 或单个数据磁盘作为托管磁盘从共享映像库中存储的映像版本导出。


## <a name="cli"></a>CLI

使用 [az sig image 版本列表](/cli/azure/sig/image-version#az_sig_image_version_list)列出库中的映像版本。 在此示例中，我们将在 myGallery 映像库中查找 myImageDefinition 映像定义中包含的所有映像版本。

```azurecli-interactive
az sig image-version list \
   --resource-group myResourceGroup\
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   -o table
```

将 `source` 变量设置为映像版本的 ID，然后使用 [az disk create](/cli/azure/disk?view=azure-cli-latest#az_disk_create) 创建托管磁盘。 

在此示例中，我们将导出映像版本的 OS 磁盘，以在名为 *myResourceGroup* 的资源组中创建名为 *myManagedOSDisk**的托管* 磁盘。 

```azurecli-interactive
source="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<galleryImageDefinition>/versions/<imageVersion>"

az disk create --resource-group myResourceGroup --location EastUS --name myManagedOSDisk --gallery-image-reference $source 
```



如果要从映像版本导出数据磁盘，请添加 `--gallery-image-reference-lun` 以指定要导出的数据磁盘的 LUN 位置。 

在此示例中，我们导出位于映像版本的 LUN 0 的数据磁盘，在名为 *myResourceGroup* 的资源组中的 *EastUS* 区域中创建名为 *myManagedDataDisk* 的托管磁盘。 

```azurecli-interactive
source="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<galleryImageDefinition>/versions/<imageVersion>"

az disk create --resource-group myResourceGroup --location EastUS --name myManagedDataDisk --gallery-image-reference $source --gallery-image-reference-lun 0
``` 

## <a name="powershell"></a>PowerShell

使用 [AzResource](/powershell/module/az.resources/get-azresource)列出库中的映像版本。 

```azurepowershell-interactive
Get-AzResource `
   -ResourceType Microsoft.Compute/galleries/images/versions | `
   Format-Table -Property Name,ResourceId,ResourceGroupName
```

获得全部所需信息后，可以使用 [Get-AzGalleryImageVersion](/powershell/module/az.compute/get-azgalleryimageversion) 来获取要使用的源映像版本并将其分配给变量。 在本示例中，我们将在 `myResourceGroup` 资源组的 `myGallery` 源库中，获取 `myImageDefinition` 定义的 `1.0.0` 映像版本。

```azurepowershell-interactive
$sourceImgVer = Get-AzGalleryImageVersion `
   -GalleryImageDefinitionName myImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name 1.0.0
```

将变量设置 `source` 为映像版本的 ID 后，使用 [AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) 创建磁盘配置，并使用 [AzDisk](/powershell/module/az.compute/new-azdisk) 创建磁盘。 

在此示例中，我们将导出映像版本的 OS 磁盘，以在名为 *myResourceGroup* 的资源组中创建名为 *myManagedOSDisk**的托管* 磁盘。 

创建磁盘配置。
```azurepowershell-interactive
$diskConfig = New-AzDiskConfig `
   -Location EastUS `
   -CreateOption FromImage `
   -GalleryImageReference @{Id = $sourceImgVer.Id}
```

创建该磁盘。

```azurepowershell-interactive
New-AzDisk -Disk $diskConfig `
   -ResourceGroupName myResourceGroup `
   -DiskName myManagedOSDisk
```

如果要导出映像版本中的数据磁盘，请将 LUN ID 添加到磁盘配置，以指定要导出的数据磁盘的 LUN 位置。 

在此示例中，我们导出位于映像版本的 LUN 0 的数据磁盘，在名为 *myResourceGroup* 的资源组中的 *EastUS* 区域中创建名为 *myManagedDataDisk* 的托管磁盘。 

创建磁盘配置。
```azurepowershell-interactive
$diskConfig = New-AzDiskConfig `
   -Location EastUS `
   -CreateOption FromImage `
   -GalleryImageReference @{Id = $sourceImgVer.Id; Lun=0}
```

创建该磁盘。

```azurepowershell-interactive
New-AzDisk -Disk $diskConfig `
   -ResourceGroupName myResourceGroup `
   -DiskName myManagedDataDisk
```

## <a name="next-steps"></a>后续步骤

还可以使用 [Azure CLI](image-version-managed-image-cli.md) 或 [PowerShell](image-version-managed-image-powershell.md) 从托管磁盘创建映像版本。


