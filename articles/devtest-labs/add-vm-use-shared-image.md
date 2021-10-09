---
title: 使用共享映像添加 VM
description: 了解如何在 Azure 开发测试实验室中使用附加的共享映像库中的映像添加虚拟机 (VM)
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 7107fd22142f00e422e0f302520962537d65b10e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128621804"
---
# <a name="add-a-vm-using-an-image-from-the-attached-shared-image-gallery"></a>使用附加的共享映像库中的映像添加 VM
Azure 开发测试实验室允许你将共享映像库附加到实验室，然后将库中的映像用作在实验室中创建的 VM 的基础。 若要了解如何将共享映像库附加到实验室，请参阅[配置共享映像库](configure-shared-image-gallery.md)。 本文介绍如何通过使用附加的共享映像库中的映像作为基础将 VM 添加到实验室。 

## <a name="azure-portal"></a>Azure 门户
本部分介绍如何使用 Azure 门户基于附加的共享映像库中的映像将 VM 添加到实验室。 本部分不提供使用 Azure 门户创建 VM 的详细分步说明。 有关这些详细信息，请参阅[创建 VM - Azure 门户](devtest-lab-add-vm.md)。 它仅突出显示从附加的共享映像库中选择映像并选择想要使用的映像版本的步骤。 

将 VM 添加到实验室时，可以从附加的共享映像库中选择一个映像作为基础映像： 

![选择一个共享映像作为基础](./media/add-vm-use-shared-image/select-shared-image-for-base.png)

然后，在“创建实验室资源”页的“高级设置”选项卡上，可以选择要用作基础映像的映像版本：

![选择映像版本](./media/add-vm-use-shared-image/select-version-shared-image.png)

创建 VM 后，可以切换到使用不同版本的映像。 

## <a name="resource-manager-template"></a>资源管理器模板
如果使用 Azure 资源管理器模板来使用共享映像库映像创建虚拟机，请在“属性”部分中为 sharedImageId 指定一个值。 请参阅以下示例： 

```json
"resources": [
{
    ...
    "properties": {
         "sharedImageId": "/subscriptions/111111111-1111-1111-1111-111111111111/resourcegroups/mydtlrg/providers/microsoft.devtestlab/labs/mydtllab/sharedgalleries/spsig/sharedimages/myimagefromgallery",
        "sharedImageVersion": "1.0.1",
        ...
    }
}
],
```

有关完整的资源管理器模板示例，请参阅 GitHub 存储库中的[使用共享映像库映像创建虚拟机](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-vm-username-pwd-sharedimage)示例。 

## <a name="rest-api"></a>REST API

1. 首先，需要获取共享映像库中映像的 ID。 一种方式是通过使用以下 GET 命令列出附加的共享映像库中的所有映像。 

    ```rest
    GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries/{name}/sharedimages?api-version= 2018-10-15-preview
    ```
2. 通过将从上一次调用中收到的共享映像的 ID 传递到 `properties.SharedImageId`，在虚拟机上调用 PUT 方法。

## <a name="next-steps"></a>后续步骤
若要了解如何将共享映像库附加到实验室并对其进行配置，请参阅[配置共享映像库](configure-shared-image-gallery.md)。