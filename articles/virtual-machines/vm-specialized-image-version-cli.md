---
title: 使用 Azure CLI 从专用化映像版本创建 VM
description: 使用 Azure CLI 从共享映像库中的专用化映像版本创建 VM。
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 04/23/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: devx-track-azurecli
ms.openlocfilehash: 64e5e879e37b7454a5fa82a8f0f22db0c59cc7fc
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122697892"
---
# <a name="create-a-vm-using-a-specialized-image-version-with-the-azure-cli"></a>使用 Azure CLI 通过专用化映像版本创建 VM

**适用于：** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 灵活规模集

从共享映像库中存储的[专用化映像版本](./shared-image-galleries.md#generalized-and-specialized-images)创建 VM。 若要使用通用化映像版本创建 VM，请参阅[从通用化映像版本创建 VM](vm-generalized-image-version-cli.md)。

在此示例中，请根据需要替换资源名称。 

使用 [az sig image-definition list](/cli/azure/sig/image-definition#az_sig_image_definition_list) 列出库中的映像定义，以查看定义的名称和 ID。

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
az sig image-definition list \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --query "[].[name, id]" \
   --output tsv
```

结合 --specialized 参数使用 [az vm create](/cli/azure/vm#az_vm_create) 创建 VM 可以指明该映像是专用化映像。 

使用 `--image` 的映像定义 ID 从可用的最新映像版本创建 VM。 还可以通过为 `--image` 提供映像版本 ID 从特定版本创建 VM。 

在此示例中，我们将从 myImageDefinition 映像的最新版本创建 VM。

```azurecli
az group create --name myResourceGroup --location eastus
az vm create --resource-group myResourceGroup \
    --name myVM \
    --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
    --specialized
```


## <a name="next-steps"></a>后续步骤
[Azure 映像生成器（预览版）](./image-builder-overview.md)可以帮助自动创建映像版本，你甚至可以使用它进行更新以及[从现有的映像版本创建新映像版本](./linux/image-builder-gallery-update-image-version.md)。 

此外可以使用模板创建共享映像库资源。 提供多个 Azure 快速入门模板： 

- [创建共享映像库](https://azure.microsoft.com/resources/templates/sig-create/)
- [在共享的映像库中创建映像定义](https://azure.microsoft.com/resources/templates/sig-image-definition-create/)
- [在共享映像库中创建映像版本](https://azure.microsoft.com/resources/templates/sig-image-version-create/)
