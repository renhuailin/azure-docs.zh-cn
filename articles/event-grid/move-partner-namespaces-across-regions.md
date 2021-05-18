---
title: 将 Azure 事件网格合作伙伴命名空间移到另一个区域
description: 本文介绍如何将 Azure 事件网格合作伙伴命名空间从一个区域移到另一个区域。
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/20/2020
ms.openlocfilehash: 6783db6b9bb1c7d48b308234a179925d6f30e281
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "89083631"
---
# <a name="move-azure-event-grid-partner-namespaces-to-another-region"></a>将 Azure 事件网格合作伙伴命名空间移到另一个区域
由于多方面的原因，你可能需要将资源移动到另一个区域。 例如，为了利用新的 Azure 区域，为了满足内部策略和监管要求，或者，为了应对容量规划要求。 

下面是本文介绍的概要步骤： 

- 将合作伙伴命名空间资源导出到 Azure 资源管理器模板。 删除模板中事件通道资源的定义。 事件通道可能会具有对合作伙伴主题的 Azure 资源管理器 ID（归某个客户所有）的引用。 因此，无法通过使用目标区域中的模板来创建事件通道。  
- 请使用该模板将合作伙伴命名空间部署到目标区域。 然后，在目标区域的新合作伙伴命名空间中创建事件通道。 
- 若要完成移动，请从源区域删除合作伙伴命名空间。 

    > [!NOTE]
    > - 不支持将合作伙伴主题导出到 Azure 资源管理器模板，因为客户无法直接创建合作伙伴主题。 
    > - 合作伙伴注册是全局资源（未绑定到任何特定区域），因此，将它们从一个区域移到另一个区域是不适用的。 

## <a name="prerequisites"></a>先决条件
- 请确保在目标区域中可以使用事件网格服务。 参阅[各区域的产品可用性](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all)。

## <a name="prepare"></a>准备
若要开始操作，请导出用于该合作伙伴命名空间的资源管理器模板。 

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 在顶部的搜索栏中，键入“事件网格合作伙伴命名空间”，然后从结果列表中选择“事件网格合作伙伴命名空间” 。 
3. 选择要导出到资源管理器模板的合作伙伴命名空间。 
4. 在“事件网格合作伙伴命名空间”页上，选择左侧菜单中“设置”下的“导出模板”，然后选择工具栏上的“下载”   。 

    :::image type="content" source="./media/move-partner-namespaces-across-regions/download-template.png" alt-text="导出模板 -> 下载" lightbox="./media/move-partner-namespaces-across-regions/download-template.png":::   
5. 找到从门户下载的 .zip 文件，并将该文件解压缩到所选的文件夹。 此 zip 文件包含模板和参数 JSON 文件。 
1. 在你选择的编辑器中打开 template.json。 
8. 将主题资源的 `location` 更新为目标区域或位置。 若要获取位置代码，请参阅 [Azure 位置](https://azure.microsoft.com/global-infrastructure/locations/)。 区域的代码是不带空格的区域名称，例如 `West US` 等同于 `westus`。

    ```json
    {
        "type": "Microsoft.EventGrid/partnerNamespaces",
        "apiVersion": "2020-04-01-preview",
        "name": "[parameters('partnerNamespace_name')]",
        "location": "westus",
        "properties": {
            "partnerRegistrationFullyQualifiedId": "[parameters('partnerRegistrations_ContosoCorpAccount1_externalid')]"
        }
    }
    ``` 
1. 保存该模板。 

## <a name="recreate"></a>重新创建 
部署该模板以在目标区域中创建合作伙伴命名空间。 

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
    1. 对于“位置”，请选择目标区域。 如果选择了现有资源组，则此设置为只读。 
    1. 对于“合作伙伴命名空间名称”，请输入新合作伙伴命名空间的名称。 
    1. 对于合作伙伴注册的外部 ID，请按以下格式输入合作伙伴注册的资源 ID：`/subscriptions/<Azure subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.EventGrid/partnerRegistrations/<Partner registration name>`。
    1. 选择“我同意上述条款和条件”复选框。     
    1. 选择“查看 + 创建”，以启动部署过程。 
    1. 在“查看 + 创建”页上，查看设置，然后选择“创建” 。 

## <a name="discard-or-clean-up"></a>丢弃或清理
若要完成移动，请删除源区域中的合作伙伴命名空间。  

如果需要重新开始，请删除目标区域中的合作伙伴命名空间，然后重复本文的[准备](#prepare)和[重新创建](#recreate)部分中的步骤。

若要通过使用 Azure 门户来删除合作伙伴命名空间，请执行以下操作：

1. 在 Azure 门户顶部的搜索窗口中，键入“事件网格合作伙伴命名空间”，然后从搜索结果中选择“事件网格合作伙伴命名空间” 。 
2. 选择要删除的合作伙伴命名空间，并从工具栏中选择“删除”。 
3. 确认删除，以删除该合作伙伴命名空间。 

## <a name="next-steps"></a>后续步骤
你已经了解如何将事件网格合作伙伴命名空间从一个区域移到另一个区域。 若要了解如何跨区域移动系统主题、自定义主题和域，请参阅以下文章。

- [跨区域移动系统主题](move-system-topics-across-regions.md)。 
- [跨区域移动自定义主题](move-custom-topics-across-regions.md)。 
- [跨区域移动域](move-domains-across-regions.md)。

若要详细了解在 Azure 中如何在区域之间移动资源以及如何进行灾难恢复，请参阅以下文章：[将资源移动到新的资源组或订阅](../azure-resource-manager/management/move-resource-group-and-subscription.md)。