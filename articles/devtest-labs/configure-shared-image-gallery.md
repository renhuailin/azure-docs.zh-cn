---
title: 配置共享映像库
description: 了解如何在 Azure 开发测试实验室中配置共享映像库，从而使用户在创建实验室资源时能够从某个共享位置访问映像。
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: e6275b8903df7e815e763b04198265060943e124
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128634836"
---
# <a name="configure-a-shared-image-gallery-in-azure-devtest-labs"></a>在 Azure 开发测试实验室中配置共享映像库
开发测试实验室现在支持[共享映像库](../virtual-machines/shared-image-galleries.md)功能。 利用该功能，实验室用户在创建实验室资源时可以从某个共享位置访问映像。 该功能还有助于围绕自定义托管 VM 映像来构建结构和组织。 共享映像库功能支持：

- 映像的托管全局复制
- 映像的版本控制和分组，以便于管理
- 使映像对于支持可用性区域的地区中的区域冗余存储 (ZRS) 帐户高度可用。 ZRS 提高了针对区域性故障的恢复能力。
- 使用 Azure 基于角色的访问控制 (Azure RBAC)，在订阅间（甚至在租户之间）共享。

有关详细信息，请参阅[共享映像库文档](../virtual-machines/shared-image-galleries.md)。 
 
如果你有大量的托管映像需要维护，并想要使其在整个公司中可用，可将共享映像库用作存储库，以便更轻松地更新和共享映像。 作为实验室所有者，可以将现有的共享映像库附加到实验室。 在附加了此库后，实验室用户可以从这些最新映像创建计算机。 此功能的一个主要优点是，开发测试实验室现在可以跨实验室、跨订阅和跨区域充分利用共享映像。 

> [!NOTE]
> 若要了解与共享映像库服务相关的成本，请参阅[共享映像库计费](../virtual-machines/shared-image-galleries.md#billing)。

## <a name="considerations"></a>注意事项
- 一次只能将一个共享映像库附加到实验室。 如果要附加另一个库，则需要先分离一个现有的库，然后再附加另一个库。 
- 开发测试实验室目前不支持通过实验室将映像上传到库。 
- 在使用共享映像库映像创建虚拟机时，开发测试实验室始终使用此映像的最新发布版本。 但是，如果映像有多个版本，用户可以通过在虚拟机创建过程中转到“高级设置”选项卡，选择从某个早期版本来创建计算机。  
- 虽然开发测试实验室会自动尽力确保共享映像库将映像复制到实验室所在的区域，但并不总能够实现。 若要避免用户在从这些映像创建 VM 时遇到问题，请确保已将映像复制到实验室的区域。

## <a name="use-azure-portal"></a>使用 Azure 门户
1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航菜单中，选择“所有服务”。
1. 从列表中选择“开发测试实验室”。
1. 从实验室列表中选择实验室。
1. 在左侧菜单的“设置”部分中选择“配置和策略” 。
1. 在左侧菜单的“虚拟机”下选择“共享映像库” 。

    ![共享映像库菜单](./media/configure-shared-image-gallery/shared-image-galleries-menu.png)
1. 通过单击“附加”按钮并在下拉列表中选择库，将现有的共享映像库附加到实验室。

    ![Attach](./media/configure-shared-image-gallery/attach-options.png)
1. 在附加映像库后，请选择该库，以转到这个已附加的库。 将库配置为对于 VM 创建启用或禁用共享映像。 从列表中选择映像库，以对其进行配置。 

    在默认情况下，“允许将所有映像都用作虚拟机基础映像”设置为“是” 。 此设置的意思是，实验室用户在创建新实验室 VM 时可以使用附加的共享映像库中提供的所有映像。 如果需要限制对某些映像的访问，请将“允许将所有映像都用作虚拟机基础映像”更改为“否”，并选择允许在创建 VM 时使用的映像，然后选择“保存”按钮  。

    :::image type="content" source="./media/configure-shared-image-gallery/enable-disable.png" alt-text="启用或禁用映像":::

    > [!NOTE]
    > 共享映像库中通用化的映像和特殊化的映像都是受支持的。 
1. 然后，实验室用户可以通过单击“+ 添加”并在“选择基础映像”页中查找映像来使用启用的映像创建虚拟机 。

    ![实验室用户](./media/configure-shared-image-gallery/lab-users.png)
## <a name="use-azure-resource-manager-template"></a>使用 Azure Resource Manager 模板

### <a name="attach-a-shared-image-gallery-to-your-lab"></a>将共享映像库附加到实验室
如果使用 Azure 资源管理器模板将共享映像库附加到实验室，则需要在资源管理器模板的资源部分下添加该库，如以下示例中所示：

```json
"resources": [
{
    "apiVersion": "2018-10-15-preview",
    "type": "Microsoft.DevTestLab/labs",
    "name": "mylab",
    "location": "eastus",
    "resources": [
    {
        "apiVersion":"2018-10-15-preview",
        "name":"myGallery",
        "type":"sharedGalleries",
        "properties": {
            "galleryId":"/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/mySharedGalleryRg/providers/Microsoft.Compute/galleries/mySharedGallery",
            "allowAllImages": "Enabled"
        }
    }
    ]
}
```

如需完整的资源管理器模板示例，请参阅公共 GitHub 存储库中的以下资源管理器模板示例：[在创建实验室时配置共享映像库](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-lab-shared-gallery-configured)。

## <a name="use-rest-api"></a>使用 REST API

### <a name="get-a-list-of-labs"></a>获取实验室列表 

```rest
GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs?api-version= 2018-10-15-preview
```

### <a name="get-the-list-of-shared-image-galleries-associated-with-a-lab"></a>获取与实验室关联的共享映像库的列表

```rest
GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries?api-version= 2018-10-15-preview
   ```

### <a name="create-or-update-shared-image-gallery"></a>创建或更新共享映像库

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries/{name}?api-version= 2018-10-15-preview
Body: 
{
    "properties":{
        "galleryId": "[Shared Image Gallery resource Id]",
        "allowAllImages": "Enabled"
    }
}

```

### <a name="list-images-in-a-shared-image-gallery"></a>列出共享映像库中的映像

```rest
GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries/{name}/sharedimages?api-version= 2018-10-15-preview
```



## <a name="next-steps"></a>后续步骤
请参阅[使用库中共享映像创建 VM](add-vm-use-shared-image.md)，以了解如何使用附加的共享映像库中的映像来创建 VM