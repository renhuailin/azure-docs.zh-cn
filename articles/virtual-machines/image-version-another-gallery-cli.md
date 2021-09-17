---
title: 使用 CLI 从另一个库复制映像版本
description: 使用 Azure CLI 从另一个库复制映像版本。
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 904700344900d73b700bdfac01af2022425c7c69
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122689017"
---
# <a name="copy-an-image-from-another-gallery-using-the-azure-cli"></a>使用 Azure CLI 复制另一个库中的映像

适用于：:heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 灵活规模集 :heavy_check_mark: 统一规模集

如果你的组织中有多个库，则你还可以从其他库中存储的现有映像版本创建自己的映像版本。 例如，你可能会使用一个开发和测试库用于创建和测试新映像。 准备好在生产环境中使用这些映像时，可以遵循本示例将它们复制到生产库中。 也可以使用 [Azure PowerShell](image-version-another-gallery-powershell.md) 从另一个库中的映像创建映像。



## <a name="before-you-begin"></a>准备阶段

若要完成本文，必须有一个现有的源库、映像定义和映像版本。 此外，还应有一个目标库。 

必须将源映像版本复制到目标库所在的区域。 

通过本文进行操作时，请根据需要替换资源名称。



## <a name="get-information-from-the-source-gallery"></a>从源库中获取信息

需要源映像定义中的信息，以便可以在新库中创建该映像的副本。

使用 [az sig list](/cli/azure/sig#az_sig_list) 列出有关可用映像库的信息，以查找有关源库的信息。

```azurecli-interactive 
az sig list -o table
```

使用 [az sig image-definition list](/cli/azure/sig/image-definition#az_sig_image_definition_list) 列出库中的映像定义。 在此示例中，我们将搜索 *myGalleryRG* 资源组中名为 *myGallery* 的库内的映像定义。

```azurecli-interactive 
az sig image-definition list \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   -o table
```

使用 [az sig image-version list](/cli/azure/sig/image-version#az_sig_image_version_list) 列出库中映像的版本，以查找要复制到新库中的映像版本。 在此示例中，我们将查找包含在 *myImageDefinition* 映像定义中的所有映像版本。

```azurecli-interactive
az sig image-version list \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   -o table
```

获取全部所需信息后，可以使用 [az sig image-version show](/cli/azure/sig/image-version#az_sig_image_version_show) 获取源映像版本的 ID。

```azurecli-interactive
az sig image-version show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --query "id" -o tsv
```


## <a name="create-the-image-definition"></a>创建映像定义 

需要创建与操作系统、操作系统状态以及包含源映像版本的映像定义的 Hyper-V 代系相匹配的映像定义。 可以使用 [az sig image-definition show](/cli/azure/sig/image-definition#az_sig_image_definition_show) 查看在新库中重新创建映像定义所需的全部信息。

```azurecli-interactive
az sig image-definition show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition
```

输出将如下所示：

```output
{
  "description": null,
  "disallowed": null,
  "endOfLifeDate": null,
  "eula": null,
  "hyperVgeneration": "V1",
  "id": "/subscriptions/1111abcd-1a23-4b45-67g7-1234567de123/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition",
  "identifier": {
    "offer": "myOffer",
    "publisher": "myPublisher",
    "sku": "mySKU"
  },
  "location": "eastus",
  "name": "myImageDefinition",
  "osState": "Specialized",
  "osType": "Linux",
  "privacyStatementUri": null,
  "provisioningState": "Succeeded",
  "purchasePlan": null,
  "recommended": null,
  "releaseNoteUri": null,
  "resourceGroup": "myGalleryRG",
  "tags": null,
  "type": "Microsoft.Compute/galleries/images"
}
```

使用上述输出中的信息，在新库中创建新的映像定义。


```azurecli-interactive 
az sig image-definition create \
   --resource-group myNewGalleryRG \
   --gallery-name myNewGallery \
   --gallery-image-definition myImageDefinition \
   --publisher myPublisher \
   --offer myOffer \
   --sku mySKU \
   --os-type Linux \
   --hyper-v-generation V1 \
   --os-state specialized 
```

> [!NOTE]
> 对于将要包含从第三方映像继承的映像的映像定义，计划信息必须与第三方映像中的计划信息完全匹配。 创建映像定义时，通过添加 `--plan-name`、`--plan-product` 和 `--plan-publisher` 在映像定义中包含计划信息。
>


## <a name="create-the-image-version"></a>创建映像版本

使用 [az image gallery create-image-version](/cli/azure/sig/image-version#az_sig_image_version_create) 创建版本。 你需要传入托管映像的 ID 以作为创建映像版本时要使用的基线。 可以使用 [az image list](/cli/azure/image?view#az_image_list) 获取资源组中的映像的相关信息。 

允许用于映像版本的字符为数字和句点。 数字必须在 32 位整数范围内。 格式：*MajorVersion*.*MinorVersion*.*Patch*。

在此示例中，映像版本为 1.0.0，我们将使用区域冗余存储在“美国中南部”区域创建 1 个副本，并在“美国东部”区域创建 1 个副本  。


```azurecli-interactive 
az sig image-version create \
   --resource-group myNewGalleryRG \
   --gallery-name myNewGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "southcentralus=1" "eastus=1=standard_zrs" \
   --replica-count 2 \
   --managed-image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0"
```

> [!NOTE]
> 需等待映像版本彻底生成并复制完毕，然后才能使用同一托管映像来创建另一映像版本。
>
> 创建映像版本时，还可以通过添加 `--storage-account-type  premium_lrs` 将映像存储在高级存储中，或者通过添加 `--storage-account-type  standard_zrs` 将其存储在[区域冗余存储](../storage/common/storage-redundancy.md)中。
>

## <a name="next-steps"></a>后续步骤

从[通用化](vm-generalized-image-version-cli.md)或[专用化](vm-specialized-image-version-cli.md)映像版本创建 VM。

同时，试用 [Azure 映像生成器](./image-builder-overview.md)有助于自动创建映像版本，你甚至可以使用它进行更新，以及[从现有的映像版本创建新映像版本](./linux/image-builder-gallery-update-image-version.md)。 

有关如何提供购买计划信息的信息，请参阅[在创建映像时提供 Azure 市场购买计划信息](marketplace-images.md)。
