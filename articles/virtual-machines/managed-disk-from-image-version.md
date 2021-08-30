---
title: 将映像版本导出到托管磁盘
description: 将映像版本从共享映像库导出到托管磁盘。
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/27/2020
ms.author: cynthn
ms.reviewer: olayemio
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d4a3d1e8b1eb1c3a959e9f48e0c46379d6162644
ms.sourcegitcommit: 6f21017b63520da0c9d67ca90896b8a84217d3d3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2021
ms.locfileid: "114653037"
---
# <a name="export-an-image-version-to-a-managed-disk"></a>将映像版本导出到托管磁盘

你可以将操作系统或映像版本的单个数据磁盘导出为共享映像库中存储的映像版本的托管磁盘。


## <a name="cli"></a>CLI

使用 [az sig image-version list](/cli/azure/sig/image-version#az_sig_image_version_list) 列出库中的映像版本。 在此示例中，我们将在 myGallery 映像库中查找 myImageDefinition 映像定义中包含的所有映像版本。

```azurecli-interactive
az sig image-version list \
   --resource-group myResourceGroup\
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   -o table
```

将 `source` 变量设置为映像版本的 ID，然后使用 [az disk create](/cli/azure/disk#az_disk_create) 创建托管磁盘。 

在此示例中，我们将导出映像版本的 OS 磁盘，在 EastUS 区域的名为“myResourceGroup”的资源组中创建一个名为“myManagedOSDisk”的托管磁盘  。 

```azurecli-interactive
source="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<galleryImageDefinition>/versions/<imageVersion>"

az disk create --resource-group myResourceGroup --location EastUS --name myManagedOSDisk --gallery-image-reference $source 
```



如果要从映像版本导出数据磁盘，请添加 `--gallery-image-reference-lun` 以指定要导出的数据磁盘的 LUN 位置。 

在此示例中，我们将导出位于映像版本的 LUN 0 的数据磁盘，在 EastUS 区域的名为“myResourceGroup”的资源组中创建一个名为“myManagedDataDisk”的托管磁盘  。 

```azurecli-interactive
source="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<galleryImageDefinition>/versions/<imageVersion>"

az disk create --resource-group myResourceGroup --location EastUS --name myManagedDataDisk --gallery-image-reference $source --gallery-image-reference-lun 0
``` 

## <a name="powershell"></a>PowerShell

使用 [Get-AzResource](/powershell/module/az.resources/get-azresource) 列出库中映像版本。 

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

将 `source` 变量设置为映像版本的 ID 后，使用 [New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) 创建磁盘配置，并使用 [New-AzDisk](/powershell/module/az.compute/new-azdisk) 创建磁盘。 

在此示例中，我们将导出映像版本的 OS 磁盘，在 EastUS 区域的名为“myResourceGroup”的资源组中创建一个名为“myManagedOSDisk”的托管磁盘  。 

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

如果要从映像版本导出数据磁盘，请将 LUN ID 添加到磁盘配置，以指定要导出的数据磁盘的 LUN 位置。 

在此示例中，我们将导出位于映像版本的 LUN 0 的数据磁盘，在 EastUS 区域的名为“myResourceGroup”的资源组中创建一个名为“myManagedDataDisk”的托管磁盘  。 

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


