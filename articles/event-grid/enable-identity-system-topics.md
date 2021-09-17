---
title: 在 Azure 事件网格系统主题上启用托管标识
description: 本文介绍如何为 Azure 事件网格系统主题启用托管服务标识。
ms.topic: how-to
ms.date: 08/20/2021
ms.openlocfilehash: d5d8c15c818f1a9735b6cf32fe48276fb311e8ea
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2021
ms.locfileid: "122633556"
---
# <a name="assign-a-system-managed-identity-to-an-event-grid-system-topic"></a>向事件网格系统主题分配系统托管的标识
本文介绍如何将系统分配或用户分配的标识分配给现有的事件网格系统主题。 若要了解托管标识，请参阅[什么是 Azure 资源托管标识](../active-directory/managed-identities-azure-resources/overview.md)。  

> [!IMPORTANT]
> 你可以为系统主题启用系统分配的标识或用户分配的标识，但不能同时启用这两者。 最多可以将两个由用户分配的标识分配给系统主题。 

## <a name="enable-managed-identity-for-an-existing-system-topic"></a>针对系统主题启用托管标识
本节演示如何为现有系统主题启用托管标识。 

1. 转到 [Azure 门户](https://portal.azure.com)。
2. 在顶部搜索栏中，搜索“事件网格系统主题”。
3. 选择要为其启用托管标识的系统主题。 
4. 在左侧菜单中选择“标识”。 对于位于全球位置的系统主题，将不显示此选项。 

### <a name="enable-system-assigned-identity"></a>启用系统分配的标识
1. 打开启用标识的开关。 
1. 在工具栏上选择“保存”，保存该设置。 

    :::image type="content" source="./media/managed-service-identity/identity-existing-system-topic.png" alt-text="系统主题的“标识”页。"::: 
1. 在确认消息中选择“是”。 

    :::image type="content" source="./media/managed-service-identity/identity-existing-system-topic-confirmation.png" alt-text="向系统主题分配标识 - 确认。"::: 
1. 确认显示了系统分配的托管标识的对象 ID，以及用于分配角色的链接。 

    :::image type="content" source="./media/managed-service-identity/identity-existing-system-topic-completed.png" alt-text="向系统主题分配标识 - 完成。"::: 

### <a name="enable-user-assigned-identity"></a>启用用户分配的标识

1. 首先，按照[管理用户分配的托管标识](../active-directory/managed-identities-azure-resources/how-manage-user-assigned-managed-identities.md)一文中的说明，创建用户分配的标识。 
1. 在“标识”页上，切换到右侧窗格中的“用户分配”选项卡，然后选择工具栏上的“+ 添加”。  

    :::image type="content" source="./media/managed-service-identity/system-topic-user-identity-add-button.png" alt-text="显示“标识”页的“用户分配”选项卡中选择了“添加”按钮的图像。":::
1. 在“添加用户托管标识”窗口中，执行以下步骤：
    1. 选择包含用户分配标识的 Azure 订阅。 
    1. 选择用户分配的标识。 
    1. 选择 **添加** 。 
1. 刷新“用户分配”选项卡中的列表以查看刚刚添加的用户分配标识。

## <a name="enable-managed-identity-when-creating-a-system-topic"></a>创建系统主题时启用托管标识

1. 在 Azure 门户的搜索栏中，搜索并选择“事件网格系统主题”。 
1. 在“事件网格系统主题”页上的工具栏中选择“创建”。  
1. 在创建向导的“基本”页上，执行以下步骤： 
    1. 对于“主题类型”，选择支持系统主题的主题类型。 在以下示例中，选择了“存储帐户”。 
    2. 对于“订阅”，选择包含 Azure 资源的 Azure 订阅。 
    1. 对于“资源组”，选择包含 Azure 资源的资源组。 
    1. 对于“资源”，选择资源。 
    1. 指定系统主题的名称。
    1. 启用托管标识：
        1. 要启用系统分配的标识，请选择“启用系统分配的标识”。 
        
            :::image type="content" source="./media/managed-service-identity/system-topic-creation-enable-managed-identity.png" alt-text="显示系统主题创建向导屏幕截图的图像，其中选择了“系统分配的标识”选项。":::            
        1. 要启用用户分配的标识： 
            1. 选择“用户分配的标识”，然后“选择添加用户身份”。  
        
                :::image type="content" source="./media/managed-service-identity/system-topic-creation-enable-user-identity.png" alt-text="显示系统主题创建向导屏幕截图的图像，其中选择了“用户分配的标识”选项。":::            
            1. 在“添加用户托管标识”窗口中，执行以下步骤：
                1. 选择包含用户分配标识的 Azure 订阅。 
                1. 选择用户分配的标识。 
                1. 选择 **添加** 。                         

> [!NOTE]
> 目前，在支持系统主题的 Azure 资源上创建事件订阅时，无法为新的系统主题启用托管标识。 


## <a name="global-azure-sources"></a>全球 Azure 源
只能为区域 Azure 资源启用系统托管的标识。 对于与全球 Azure 资源（例如 Azure 订阅、资源组或 Azure Maps）关联的系统主题，无法启用系统托管的标识。 这些全球源的系统主题也不会与特定的区域关联。 你将看不到其位置设置为“全球”的系统主题的“标识”页 。 

:::image type="content" source="./media/managed-service-identity/system-topic-location-global.png" alt-text="位置设置为“全球”的系统主题"::: 



## <a name="next-steps"></a>后续步骤
在目标（例如，服务总线队列）上将标识添加到相应角色（例如，服务总线数据发送方）。 有关详细步骤，请参阅[授予托管标识对事件网格目标的访问权限](add-identity-roles.md)。 