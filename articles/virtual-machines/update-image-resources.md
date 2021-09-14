---
title: 列出、更新和删除映像资源
description: 列出、更新和删除共享映像库中的映像资源。
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 08/05/2021
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: af41cd667da05abcd05d4c04d913e82567eec5ca
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123452277"
---
# <a name="list-update-and-delete-image-resources"></a>列出、更新和删除映像资源 

可使用 Azure CLI 或 Azure PowerShell 管理共享映像库资源。

## <a name="list-information"></a>列出信息

### <a name="cli"></a>[CLI](#tab/cli)

使用 [az sig list](/cli/azure/sig#az_sig_list) 获取有关可用映像库的位置、状态和其他信息。

```azurecli-interactive 
az sig list -o table
```

使用 [az sig image-definition list](/cli/azure/sig/image-definition#az_sig_image_definition_list) 列出库中的映像定义，包括有关 OS 类型和状态的信息。

```azurecli-interactive 
az sig image-definition list --resource-group myGalleryRG --gallery-name myGallery -o table
```

使用 [az sig image-version list](/cli/azure/sig/image-version#az_sig_image_version_list) 列出库中的共享映像版本。

```azurecli-interactive
az sig image-version list --resource-group myGalleryRG --gallery-name myGallery --gallery-image-definition myImageDefinition -o table
```

使用 [az sig image-version show](/cli/azure/sig/image-version#az_sig_image_version_show) 获取映像版本的 ID。

```azurecli-interactive
az sig image-version show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --query "id"
```

### <a name="powershell"></a>[PowerShell](#tab/powershell)

按名称列出所有库。

```azurepowershell-interactive
$galleries = Get-AzResource -ResourceType Microsoft.Compute/galleries
$galleries.Name
```

按名称列出所有映像定义。

```azurepowershell-interactive
$imageDefinitions = Get-AzResource -ResourceType Microsoft.Compute/galleries/images
$imageDefinitions.Name
```

按名称列出所有映像版本。

```azurepowershell-interactive
$imageVersions = Get-AzResource -ResourceType Microsoft.Compute/galleries/images/versions
$imageVersions.Name
```

删除映像版本。 此示例将删除名为“1.0.0”的映像版本。

```azurepowershell-interactive
Remove-AzGalleryImageVersion `
   -GalleryImageDefinitionName myImageDefinition `
   -GalleryName myGallery `
   -Name 1.0.0 `
   -ResourceGroupName myGalleryRG
```


---


## <a name="update-resources"></a>更新资源

### <a name="cli"></a>[CLI](#tab/cli)
对于能够更新的内容，存在一些限制。 以下项目可以更新： 

共享映像库：
- 说明

映像定义：
- 建议的 vCPU 数
- 建议的内存
- 说明
- 生命周期终结日期

映像版本：
- 区域副本计数
- 目标区域数
- 从最新项中排除
- 生命周期终结日期

如果打算添加副本区域，请勿删除源托管映像。 源托管映像是将映像版本复制到其他区域所需的。 

使用 [az sig update](/cli/azure/sig#az_sig_update) 更新对库的说明。 

```azurecli-interactive
az sig update \
   --gallery-name myGallery \
   --resource-group myGalleryRG \
   --set description="My updated description."
```


使用 [az sig image-definition update](/cli/azure/sig/image-definition#az_sig_image_definition_update) 更新对映像定义的说明。

```azurecli-interactive
az sig image-definition update \
   --gallery-name myGallery\
   --resource-group myGalleryRG \
   --gallery-image-definition myImageDefinition \
   --set description="My updated description."
```

使用 [az sig image-version update](/cli/azure/sig/image-definition#az_sig_image_definition_update) 更新要向其添加需复制的区域的映像版本。 此更改需要一定的时间，因为需将映像复制到新区域。

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --add publishingProfile.targetRegions  name=eastus
```

本示例说明如何使用 [az sig image-version update](/cli/azure/sig/image-definition#az_sig_image_definition_update) 将此映像版本从作为最新映像使用的版本中排除。

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --set publishingProfile.excludeFromLatest=true
```

本示例说明如何使用 [az sig image-version update](/cli/azure/sig/image-definition#az_sig_image_definition_update) 将此映像版本包括在考虑作为最新映像使用的版本中。

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --set publishingProfile.excludeFromLatest=false
```

### <a name="powershell"></a>[PowerShell](#tab/powershell)


对于能够更新的内容，存在一些限制。 以下项目可以更新： 

共享映像库：
- 说明

映像定义：
- 建议的 vCPU 数
- 建议的内存
- 说明
- 生命周期终结日期

映像版本：
- 区域副本计数
- 目标区域数
- 从最新项中排除
- 生命周期终结日期

如果打算添加副本区域，请勿删除源托管映像。 源托管映像是将映像版本复制到其他区域所需的。 

若要更新库的说明，请使用 [Update-AzGallery](/powershell/module/az.compute/update-azgallery)。

```azurepowershell-interactive
Update-AzGallery `
   -Name $gallery.Name ` 
   -ResourceGroupName $resourceGroup.Name
```

此示例演示如何使用 [Update-AzGalleryImageDefinition](/powershell/module/az.compute/update-azgalleryimagedefinition) 来更新映像定义的生命周期结束日期。

```azurepowershell-interactive
Update-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -Name $galleryImage.Name `
   -ResourceGroupName $resourceGroup.Name `
   -EndOfLifeDate 01/01/2030
```

此示例演示如何使用 [Update-AzGalleryImageVersion](/powershell/module/az.compute/update-azgalleryimageversion) 来排除此映像版本，使之不能用作最新映像。

```azurepowershell-interactive
Update-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name `
   -GalleryName $gallery.Name `
   -Name $galleryVersion.Name `
   -ResourceGroupName $resourceGroup.Name `
   -PublishingProfileExcludeFromLatest
```

本示例说明如何使用 [Update-AzGalleryImageVersion](/powershell/module/az.compute/update-azgalleryimageversion) 将此映像版本包含在其中，以考虑作为最新映像。

```azurepowershell-interactive
Update-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name `
   -GalleryName $gallery.Name `
   -Name $galleryVersion.Name `
   -ResourceGroupName $resourceGroup.Name `
   -PublishingProfileExcludeFromLatest:$false
```

---




## <a name="delete-resources"></a>删除资源

需按相反的顺序删除资源，先删除映像版本。 删除所有映像版本以后，即可删除映像定义。 删除所有映像定义以后，即可删除库。 

### <a name="cli"></a>[CLI](#tab/cli)

使用 [az sig image-version delete](/cli/azure/sig/image-version#az_sig_image_version_delete) 删除映像版本。

```azurecli-interactive
az sig image-version delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 
```

使用 [az sig image-definition delete](/cli/azure/sig/image-definition#az_sig_image_definition_delete) 删除映像定义。

```azurecli-interactive
az sig image-definition delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition
```


使用 [az sig delete](/cli/azure/sig#az_sig_delete) 删除映像库。

```azurecli-interactive
az sig delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery
```

### <a name="powershell"></a>[PowerShell](#tab/powershell)



```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$gallery = "myGallery"
$imageDefinition = "myImageDefinition"
$imageVersion = "myImageVersion"

Remove-AzGalleryImageVersion `
   -GalleryImageDefinitionName $imageDefinition `
   -GalleryName $gallery `
   -Name $imageVersion `
   -ResourceGroupName $resourceGroup

Remove-AzGalleryImageDefinition `
   -ResourceGroupName $resourceGroup `
   -GalleryName $gallery `
   -GalleryImageDefinitionName $imageDefinition

Remove-AzGallery `
   -Name $gallery `
   -ResourceGroupName $resourceGroup

Remove-AzResourceGroup -Name $resourceGroup
```

---

## <a name="next-steps"></a>后续步骤

[Azure 映像生成器（预览版）](./image-builder-overview.md)可以帮助自动创建映像版本，你甚至可以使用它进行更新以及[从现有的映像版本创建新映像版本](./linux/image-builder-gallery-update-image-version.md)。