---
title: 在 Azure 事件网格系统主题上启用托管标识
description: 本文介绍如何为 Azure 事件网格系统主题启用托管服务标识。
ms.topic: how-to
ms.date: 03/25/2021
ms.openlocfilehash: d8219cf9cba4ce0a4fb5ddbcf695f1ec72de36fe
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2021
ms.locfileid: "106280507"
---
# <a name="assign-a-system-managed-identity-to-an-event-grid-system-topic"></a>向事件网格系统主题分配系统托管的标识
本文介绍如何为现有的事件网格系统主题启用系统托管的标识。 若要了解托管标识，请参阅[什么是 Azure 资源托管标识](../active-directory/managed-identities-azure-resources/overview.md)。  

> [!IMPORTANT]
> 目前，在创建新的系统主题，即，在支持系统主题的 Azure 资源上创建事件订阅时，无法启用系统托管的标识。 


## <a name="use-azure-portal"></a>使用 Azure 门户
以下过程说明如何为系统主题启用系统托管的标识。 

1. 转到 [Azure 门户](https://portal.azure.com)。
2. 在顶部搜索栏中，搜索“事件网格系统主题”。
3. 选择要为其启用托管标识的系统主题。 
4. 在左侧菜单中选择“标识”。 对于位于全球位置的系统主题，将不显示此选项。 
5. 打开启用标识的开关。 
1. 在工具栏上选择“保存”，保存该设置。 

    :::image type="content" source="./media/managed-service-identity/identity-existing-system-topic.png" alt-text="系统主题的“标识”页"::: 
1. 在确认消息中选择“是”。 

    :::image type="content" source="./media/managed-service-identity/identity-existing-system-topic-confirmation.png" alt-text="向系统主题分配标识 - 确认"::: 
1. 确认显示了系统分配的托管标识的对象 ID，以及用于分配角色的链接。 

    :::image type="content" source="./media/managed-service-identity/identity-existing-system-topic-completed.png" alt-text="向系统主题分配标识 - 完成"::: 

## <a name="global-azure-sources"></a>全球 Azure 源
只能为区域 Azure 资源启用系统托管的标识。 对于与全球 Azure 资源（例如 Azure 订阅、资源组或 Azure Maps）关联的系统主题，无法启用系统托管的标识。 这些全球源的系统主题也不会与特定的区域关联。 你将看不到其位置设置为“全球”的系统主题的“标识”页 。 

:::image type="content" source="./media/managed-service-identity/system-topic-location-global.png" alt-text="位置设置为“全球”的系统主题"::: 



## <a name="next-steps"></a>后续步骤
在目标（例如，服务总线队列）上将标识添加到相应角色（例如，服务总线数据发送方）。 有关详细步骤，请参阅[授予托管标识对事件网格目标的访问权限](add-identity-roles.md)。 