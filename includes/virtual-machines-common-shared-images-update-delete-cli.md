---
title: include 文件
description: include 文件
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: cynthn
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: b966f68e19794aadebff76e3e9b29ed79a32eebe
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/20/2021
ms.locfileid: "107800209"
---
## <a name="update-resources"></a>更新资源

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

## <a name="delete-resources"></a>删除资源

需按相反的顺序删除资源，先删除映像版本。 删除所有映像版本以后，即可删除映像定义。 删除所有映像定义以后，即可删除库。 

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