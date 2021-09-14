---
title: 创建共享映像库
description: 了解如何在 Azure 创建共享映像库
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 07/15/2021
ms.author: cynthn
ms.custom: template-how-to
ms.openlocfilehash: c7462886c2f85e07f060caa7a11a39a782e38cea
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123452279"
---
# <a name="create-a-gallery-for-storing-and-sharing-images"></a>创建一个库来存储和共享映像


[共享映像库](./shared-image-galleries.md)大大简化了整个组织中的自定义映像共享。 自定义映像类似于市场映像，不同的是自定义映像的创建者是自己。 自定义映像可用于启动部署任务，例如预加载应用程序、应用程序配置和其他 OS 配置。 

使用共享映像库，你可以在 AAD 租户内在同一区域或跨区域与组织中的其他用户共享自定义 VM 映像。 选择要共享哪些映像，要在哪些区域中共享，以及希望与谁共享它们。 你可以创建多个库，以便可以按逻辑方式对共享映像进行分组。 

库是顶级资源，它提供完整的 Azure 基于角色的访问控制 (Azure RBAC)。 你可以控制映像的版本，并且可以选择将每个映像版本复制到一组不同的 Azure 区域。 库仅适用于托管映像。

共享映像库功能具有多种资源类型。 

[!INCLUDE [virtual-machines-shared-image-gallery-resources](./includes/virtual-machines-shared-image-gallery-resources.md)]

## <a name="create-a-gallery"></a>创建库

映像库是用于启用映像共享的主要资源。 允许用于库名称的字符为大写或小写字母、数字、点和句点。 库名称不能包含短划线。 库名称在你的订阅中必须唯一。 

选择下列选项之一来创建库：

### <a name="portal"></a>[门户](#tab/portal)

以下示例在“myGalleryRG”资源组中创建名为“myGallery”的库 。

1. 通过 https://portal.azure.com 登录到 Azure 门户。
1. 在搜索框中使用“共享映像库”类型，在结果中选择“共享映像库”。 
1. 在“共享映像库”页中，单击“添加”。 
1. 在“创建共享映像库”页上，选择正确的订阅。
1. 在“资源组”中选择“新建”，键入 *myGalleryRG* 作为名称。 
1. 在“名称”中，键入 *myGallery* 作为库名称。
1. 对“区域”保留默认值。
1. 可以键入库的简短说明，例如“我的映像库，用于测试”。 然后单击“查看 + 创建”。
1. 通过验证后，选择“创建”。
1. 部署完成后，选择“转到资源”。

### <a name="cli"></a>[CLI](#tab/cli)

使用 [az sig create](/cli/azure/sig#az_sig_create) 创建一个映像库。 以下示例在“美国东部”创建名为 myGalleryRG 的资源组，以及名为 myGallery 的库    。

```azurecli-interactive
az group create --name myGalleryRG --location eastus
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

### <a name="powershell"></a>[PowerShell](#tab/powershell)

使用 [New-AzGallery](/powershell/module/az.compute/new-azgallery) 创建映像库。 以下示例在“myGalleryRG”资源组中创建名为“myGallery”的库 。

```azurepowershell-interactive
$resourceGroup = New-AzResourceGroup `
   -Name 'myGalleryRG' `
   -Location 'West Central US'  
$gallery = New-AzGallery `
   -GalleryName 'myGallery' `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -Description 'Shared Image Gallery for my organization'  
```


### <a name="rest"></a>[REST](#tab/rest)

使用 [REST API](/rest/api/resources/resource-groups/create-or-update) 创建资源组。

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}?api-version=2021-04-01

{
  "location": "eastus"
}
```

使用 [REST API](/rest/api/compute/galleries/create-or-update) 创建库。

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/galleries/{galleryName}?api-version=2019-12-01

{
  "properties": {
    "description": "Shared Image Gallery for my organization"
  },
  "location": "eastus",
}
```
---

## <a name="next-steps"></a>后续步骤

创建[映像定义和映像版本](image-version.md)。

[Azure 映像生成器（预览版）](./image-builder-overview.md)可以帮助自动创建映像版本，你甚至可以使用它进行更新以及[从现有的映像版本创建新映像版本](./windows/image-builder-gallery-update-image-version.md)。 

此外可以使用模板创建共享映像库资源。 提供多个 Azure 快速入门模板： 

- [创建共享映像库](https://azure.microsoft.com/resources/templates/sig-create/)
- [在共享的映像库中创建映像定义](https://azure.microsoft.com/resources/templates/sig-image-definition-create/)
- [在共享映像库中创建映像版本](https://azure.microsoft.com/resources/templates/sig-image-version-create/)