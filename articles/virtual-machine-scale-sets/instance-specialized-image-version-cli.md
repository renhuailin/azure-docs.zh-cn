---
title: 使用 Azure CLI 从专用化映像版本创建规模集
description: 使用 Azure CLI 从共享映像库中的专用化映像版本创建规模集。
author: cynthn
ms.service: virtual-machine-scale-sets
ms.subservice: shared-image-gallery
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/01/2020
ms.author: cynthn
ms.reviewer: mimckitt
ms.custom: devx-track-azurecli
ms.openlocfilehash: 361ea49a9ab740cc981beac3952301a137e629af
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122691238"
---
# <a name="create-a-scale-set-using-a-specialized-image-version-with-the-azure-cli"></a>使用 Azure CLI 通过专用化映像版本创建规模集

**适用于：** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 统一规模集

从共享映像库中存储的[专用化映像版本](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images)创建规模集。 若要使用通用化映像版本创建规模集，请参阅[从通用化映像创建规模集](instance-generalized-image-version-cli.md)。

如果选择在本地安装并使用 CLI，本教程要求运行 Azure CLI 2.4.0 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI]( /cli/azure/install-azure-cli)。

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

使用 [`az vmss create`](/cli/azure/vmss#az_vmss_create) 创建一个规模集，使用 `--specialized` 参数来指示映像是专用化映像。

使用 `--image` 的映像定义 ID 从可用的最新映像版本创建规模集实例。 还可以通过提供 `--image` 的映像版本 ID 从特定版本创建规模集实例。 请注意，使用特定映像版本意味着：如果该特定映像版本由于已删除或已从区域中删除而无法使用，则自动化可能会失败。 建议使用映像定义 ID 来创建新的 VM（除非需要特定的映像版本）。

在此示例中，我们将从 myImageDefinition 映像的最新版本创建实例。

```azurecli
az group create --name myResourceGroup --location eastus
az vmss create \
   --resource-group myResourceGroup \
   --name myScaleSet \
   --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
   --specialized
```


## <a name="next-steps"></a>后续步骤
[Azure 映像生成器（预览版）](../virtual-machines/image-builder-overview.md)可以帮助自动创建映像版本，你甚至可以使用它进行更新以及[从现有的映像版本创建新映像版本](../virtual-machines/linux/image-builder-gallery-update-image-version.md)。 

此外可以使用模板创建共享映像库资源。 提供多个 Azure 快速入门模板： 

- [创建共享映像库](https://azure.microsoft.com/resources/templates/sig-create/)
- [在共享的映像库中创建映像定义](https://azure.microsoft.com/resources/templates/sig-image-definition-create/)
- [在共享映像库中创建映像版本](https://azure.microsoft.com/resources/templates/sig-image-version-create/)