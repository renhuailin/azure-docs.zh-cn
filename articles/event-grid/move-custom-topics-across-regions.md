---
title: 将 Azure 事件网格自定义主题移动到另一个区域
description: 本文介绍了如何将 Azure 事件网格自定义主题从一个区域移动到另一个区域。
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/28/2020
ms.openlocfilehash: 0a8e4283b0b9ed2a6bb7080e9a0fa05bf8f409cc
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2021
ms.locfileid: "110472208"
---
# <a name="move-azure-event-grid-custom-topics-to-another-region"></a>将 Azure 事件网格自定义主题移动到另一个区域
由于多方面的原因，你可能需要将资源移动到另一个区域。 例如，为了利用新的 Azure 区域，为了满足内部策略和监管要求，或者，为了应对容量规划要求。 

下面是本文介绍的概要步骤： 

- 将自定义主题资源导出到 Azure 资源管理器模板。 

    > [!IMPORTANT]
    > 只有自定义主题会导出到该模板。 该主题的任何订阅都不会导出。
- 使用该模板将自定义主题部署到目标区域。 
- 在目标区域中手动创建订阅。 在当前区域中将自定义主题导出到模板时，只有该主题会导出。 模板中不会包括订阅，因此，在目标区域中创建自定义主题后，请手动创建这些订阅。 
- **验证部署**。 验证目标区域中是否已创建自定义主题。 
- 若要完成移动，请从源区域中删除自定义主题。 

## <a name="prerequisites"></a>先决条件
- 完成[快速入门：在源区域中将自定义事件路由到 Web 终结点](custom-event-quickstart-portal.md)。 请执行此步骤，以便可以测试本文中的步骤。 
- 请确保在目标区域中可以使用事件网格服务。 参阅[各区域的产品可用性](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all)。

## <a name="prepare"></a>准备
若要开始操作，请导出用于该自定义主题的资源管理器模板。 

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在搜索栏中，键入“事件网格主题”，然后从结果列表中选择“事件网格主题” 。 

    :::image type="content" source="./media/move-custom-topics-across-regions/search-topics.png" alt-text="搜索并选择事件网格主题":::
3. 选择要导出到资源管理器模板的主题。 

    :::image type="content" source="./media/move-custom-topics-across-regions/select-custom-topic.png" alt-text="选择自定义主题":::   
4. 在“事件网格主题”页上，选择左侧菜单中“设置”下的“导出模板”，然后选择工具栏上的“下载”   。 

    :::image type="content" source="./media/move-custom-topics-across-regions/export-template-download.png" alt-text="导出模板 -> 下载":::   

    > [!IMPORTANT]
    > 只有主题会导出到该模板。 对该主题的订阅不会导出。 因此，需要在将主题移动到目标区域后为该主题创建订阅。 
5. 找到从门户下载的 .zip 文件，并将该文件解压缩到你选择的某个文件夹中。 此 zip 文件包含模板和参数 JSON 文件。 
1. 在你选择的编辑器中打开 template.json。 
8. 将主题资源的 `location` 更新为目标区域或位置。 若要获取位置代码，请参阅 [Azure 位置](https://azure.microsoft.com/global-infrastructure/locations/)。 区域的代码是不带空格的区域名称，例如 `West US` 等同于 `westus`。

    ```json
    "type": "Microsoft.EventGrid/topics",
    "apiVersion": "2020-06-01",
    "name": "[parameters('topics_mytopic0130_name')]",
    "location": "westus"
    ```
1. 保存该模板。 

## <a name="recreate"></a>重新创建 
部署该模板以在目标区域中创建自定义主题。 

1. 在 Azure 门户中，选择“创建资源”。
2. 在“搜索市场”中键入“模板部署”，然后按 **ENTER**。 
3. 选择“模板部署”。
4. 选择“创建” 。
5. 选择“在编辑器中生成自己的模板”。
6. 选择“加载文件”，然后按说明加载在上一部分下载的 **template.json** 文件。
7. 选择“保存”以保存该模板。 
8. 在“自定义部署”页上执行以下步骤： 
    1. 选择 Azure 订阅。 
    1. 选择目标区域中现有的资源组，或者创建一个资源组。 
    1. 对于“区域”，请选择目标区域。 如果选择了现有资源组，则此设置为只读。 
    1. 对于“主题名称”，请为该主题输入新名称。 
    1. 在页面底部选择“查看 + 创建”。 
    
        :::image type="content" source="./media/move-custom-topics-across-regions/deploy-template.png" alt-text="自定义部署":::
    1. 在“查看 + 创建”页上，查看设置，然后选择“创建” 。 

## <a name="verify"></a>验证

1. 部署成功后，选择“转到资源”。 

    :::image type="content" source="./media/move-custom-topics-across-regions/navigate-custom-topic.png" alt-text="转到资源":::
1. 请确认看到了该自定义主题的“事件网格主题”页面。   
1. 按照[将自定义事件路由到 Web 终结点](custom-event-quickstart-portal.md#send-an-event-to-your-topic)中的步骤，将事件发送到该主题。 验证是否调用了 Webhook 事件处理程序。 

## <a name="discard-or-clean-up"></a>丢弃或清理
若要完成移动，请删除源区域中的自定义主题。  

如果需要重新开始，请删除目标区域中的主题，然后重复本文的[准备](#prepare)和[重新创建](#recreate)部分中的步骤。

若要通过使用 Azure 门户来删除自定义主题，请执行以下步骤：

1. 在 Azure 门户顶部的搜索窗口中，键入“事件网格主题”，然后从搜索结果中选择“事件网格主题”。 
2. 选择要删除的主题，然后从工具栏中选择“删除”。 
3. 在确认页上，输入资源组的名称，然后选择“删除”。  

若要通过使用 Azure 门户删除包含自定义主题的资源组，请执行以下步骤：

1. 在 Azure 门户顶部的搜索窗口中，键入“资源组”，然后从搜索结果中选择“资源组” 。 
2. 选择要删除的资源组，然后从工具栏中选择“删除”。 
3. 在确认页上，输入资源组的名称，然后选择“删除”。  

## <a name="next-steps"></a>后续步骤
你已经了解了如何将事件网格自定义主题从一个区域移动到另一个区域。 若要了解如何跨区域移动系统主题、域和合作伙伴命名空间，请参阅以下文章。

- [跨区域移动系统主题](move-system-topics-across-regions.md)。 
- [跨区域移动域](move-domains-across-regions.md)。 

若要详细了解在 Azure 中如何在区域之间移动资源以及如何进行灾难恢复，请参阅以下文章：[将资源移动到新的资源组或订阅](../azure-resource-manager/management/move-resource-group-and-subscription.md)。