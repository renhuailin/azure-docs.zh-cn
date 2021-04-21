---
title: include 文件
description: include 文件
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 01/28/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 83d70a8d4c5806120ddb4ea776a8f4a6f7e63857
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/20/2021
ms.locfileid: "107800288"
---
## <a name="list-information"></a>列出信息

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
