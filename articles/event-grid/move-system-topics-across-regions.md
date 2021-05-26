---
title: 将 Azure 事件网格系统主题移动到另一个区域
description: 本文介绍了如何将 Azure 事件网格系统主题从一个区域移动到另一个区域。
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/28/2020
ms.openlocfilehash: 5b8db19b1ef99d81f47ec19254e1f6622c6a4ba2
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2021
ms.locfileid: "110481043"
---
# <a name="move-azure-event-grid-system-topics-to-another-region"></a>将 Azure 事件网格系统主题移动到另一个区域
由于多方面的原因，你可能需要将资源移动到另一个区域。 例如，为了利用新的 Azure 区域，为了满足内部策略和监管要求，或者，为了应对容量规划要求。 

下面是本文介绍的概要步骤： 

- 将包含 Azure 存储帐户及其关联的系统主题的资源组导出到资源管理器模板。 你还可以仅导出系统主题的模板。 如果走此路线，请记得在移动系统主题之前将 Azure 事件源（在此示例中为 Azure 存储帐户）移动到另一个区域。 然后，在系统主题的已导出模板中，更新目标区域中的存储帐户的外部 ID。 
- **修改模板** 来添加 `endpointUrl` 属性，使之指向订阅系统主题的 Webhook。 导出系统主题时，其订阅（在本例中为 Webhook）也将导出到模板，但 `endpointUrl` 属性不包括在内。 因此，你需要将其更新为指向订阅该主题的终结点。 另外，还要更新 `location` 属性的值，使之指向新位置或区域。 对于其他类型的事件处理程序，你只需更新位置。 
- **使用模板将资源部署到** 目标区域。 你需要指定要在目标区域中创建的存储帐户和系统主题的名称。 
- **验证部署**。 验证将文件上传到目标区域中的 blob 存储时是否调用 Webhook。 
- 若要 **完成移动**，请从源区域中删除资源（事件源和系统主题）。 

## <a name="prerequisites"></a>先决条件
- 完成[快速入门：利用 Azure 门户将 Blob 存储事件路由到 Web 终结点](blob-event-quickstart-portal.md)（在源区域中）。 此步骤是 **可选的**。 可以执行此步骤来测试本文中的步骤。 在与应用服务和应用服务计划不同的资源组中保留存储帐户。 
- 请确保在目标区域中可以使用事件网格服务。 参阅[各区域的产品可用性](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all)。

## <a name="prepare"></a>准备
若要开始，请为包含系统事件源（Azure 存储帐户）及其关联的系统主题的资源组导出资源管理器模板。 

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧菜单中选择“资源组”。 然后，选择为其创建了系统主题的事件源所在的资源组。 在以下示例中，它是 **Azure 存储** 帐户。 资源组包含存储帐户及其关联的系统主题。 

    :::image type="content" source="./media/move-system-topics-across-regions/resource-group-page.png" alt-text="资源组页面":::        
3. 在左侧菜单中的“设置”下选择“导出模板”，然后在工具栏上选择“下载”  。 

    :::image type="content" source="./media/move-system-topics-across-regions/export-template-menu.png" alt-text="存储帐户 -“导出模板”页面":::        
5. 找到从门户下载的 **.zip** 文件，将该文件解压缩到所选的文件夹。 此 zip 文件包含模板和参数 JSON 文件。 
1. 在所选编辑器中打开 template.json。 
1. Webhook 的 URL 未导出到模板。 因此，请执行以下步骤：
    1. 在模板文件中，搜索“WebHook”。 
    1. 在“属性”部分的最后一行的末尾添加一个逗号 (`,`) 字符。 在此示例中，它是 `"preferredBatchSizeInKilobytes": 64`。 
    1. 添加 `endpointUrl` 属性并将值设置为你的 Webhook URL，如以下示例所示。 

        ```json
        "destination": {
            "properties": {
                "maxEventsPerBatch": 1,
                "preferredBatchSizeInKilobytes": 64,
                "endpointUrl": "https://mysite.azurewebsites.net/api/updates"
            },
            "endpointType": "WebHook"
        }
        ```

        > [!NOTE]
        > 对于其他类型的事件处理程序，所有属性都会导出到模板。 你只需将 `location` 属性更新为目标区域，如下一步所示。 
7. 将存储帐户资源的 `location` 更新为目标区域或位置。 若要获取位置代码，请参阅 [Azure 位置](https://azure.microsoft.com/global-infrastructure/locations/)。 区域的代码是不带空格的区域名称，例如 `West US` 等同于 `westus`。

    ```json
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2019-06-01",
    "name": "[parameters('storageAccounts_spegridstorage080420_name')]",
    "location": "westus",
    ```
8. 重复此步骤以更新模板中系统主题资源的 `location`。 

    ```json
    "type": "Microsoft.EventGrid/systemTopics",
    "apiVersion": "2020-04-01-preview",
    "name": "[parameters('systemTopics_spegridsystopic080420_name')]",
    "location": "westus",
    ```
1. 保存该模板。 

## <a name="recreate"></a>重新创建 
部署模板，在目标区域中为存储帐户创建一个存储帐户和系统主题。 

1. 在 Azure 门户中，选择“创建资源”。
2. 在“搜索市场”中键入“模板部署”，然后按 **ENTER**。 
3. 选择“模板部署”。
4. 选择“创建” 。
5. 选择“在编辑器中生成自己的模板”。
6. 选择“加载文件”，然后按说明加载在上一部分下载的 **template.json** 文件。
7. 选择“保存”以保存该模板。 
8. 在“自定义部署”页上执行以下步骤。 
    1. 选择 Azure 订阅。 
    1. 选择目标区域中现有的资源组，或者创建一个资源组。 
    1. 对于“区域”，请选择目标区域。 如果选择了现有资源组，则此设置为只读。
    1. 对于“系统主题名称”，请输入将与存储帐户关联的系统主题的名称。  
    1. 对于“存储帐户名称”，请输入要在目标区域中创建的存储帐户的名称。 

        :::image type="content" source="./media/move-system-topics-across-regions/deploy-template.png" alt-text="部署资源管理器模板":::
    5. 在页面底部选择“查看 + 创建”。 
    1. 在“查看 + 创建”页上查看设置，然后选择“创建” 。 

## <a name="verify"></a>验证
1. 部署成功后，选择“转到资源组”。 
1. 在“资源组”页面上，验证是否已创建事件源（在本例中为 Azure 存储帐户）和系统主题。 
1. 将文件上传到 Azure Blob 存储中的容器，并验证 Webhook 是否已收到该事件。 有关详细信息，请参阅[将事件发送到终结点](blob-event-quickstart-portal.md#send-an-event-to-your-endpoint)。

## <a name="discard-or-clean-up"></a>丢弃或清理
若要完成移动，请在源区域中删除包含存储帐户及其关联的系统主题的资源组。  

如果需要重新开始，请在目标区域中删除源源组，然后重复本文的[准备](#prepare)和[重新创建](#recreate)部分中的步骤。

若要使用 Azure 门户删除资源组（源或目标），请执行以下操作：

1. 在 Azure 门户顶部的搜索窗口中，键入“资源组”，然后从搜索结果中选择“资源组” 。 
2. 选择要删除的资源组，然后从工具栏中选择“删除”。 

    删除资源组
3. 在确认页上输入资源组的名称，然后选择“删除”。  

## <a name="next-steps"></a>后续步骤
你已了解如何将 Azure 事件源及其关联的系统主题从一个区域移动到另一个区域。 若要了解如何跨区域移动自定义主题、域和合作伙伴命名空间，请参阅以下文章。

- [跨区域移动自定义主题](move-custom-topics-across-regions.md)。 
- [跨区域移动域](move-domains-across-regions.md)。 

若要详细了解在 Azure 中如何在区域之间移动资源以及如何进行灾难恢复，请参阅以下文章：[将资源移动到新的资源组或订阅](../azure-resource-manager/management/move-resource-group-and-subscription.md)。
