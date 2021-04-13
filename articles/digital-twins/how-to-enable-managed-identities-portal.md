---
title: 启用用于路由事件的托管标识（预览版）- 门户
titleSuffix: Azure Digital Twins
description: 了解如何使用 Azure 门户为 Azure 数字孪生启用系统分配的标识，并使用该标识转发事件。
author: baanders
ms.author: baanders
ms.date: 1/21/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 1211cd306f10fb349bd42568697443ff103a171c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "101703077"
---
# <a name="enable-a-managed-identity-for-routing-azure-digital-twins-events-preview-azure-portal"></a>启用用于路由 Azure 数字孪生事件的托管标识（预览版）：Azure 门户

[!INCLUDE [digital-twins-managed-service-identity-selector.md](../../includes/digital-twins-managed-service-identity-selector.md)]

本文介绍如何启用 [Azure 数字孪生实例的系统分配的标识](concepts-security.md#managed-identity-for-accessing-other-resources-preview)（目前为预览版），并在将事件转发到受支持的目标（例如[事件中心](../event-hubs/event-hubs-about.md)、[服务总线](../service-bus-messaging/service-bus-messaging-overview.md)目标）和 [Azure 存储容器](../storage/blobs/storage-blobs-introduction.md)时使用标识。

本文将逐步介绍如何使用 [**Azure 门户**](https://portal.azure.com)完成此过程。

本文中所涉及的步骤如下： 

1. 创建一个带有系统分配的标识的 Azure 数字孪生实例，或者在现有的 Azure 数字孪生实例上启用系统分配的标识。 
1. 将一个或多个适当的角色添加到该标识。 例如，如果终结点是事件中心，则将“Azure 事件中心数据发送者”角色分配给该标识；如果终结点是服务总线，则将“Azure 服务总线数据发送者”角色分配给该标识。 
1. 在 Azure 数字孪生中创建一个可以使用系统分配的标识进行身份验证的终结点。

## <a name="enable-system-managed-identities-for-an-instance"></a>为实例启用系统托管标识 

当你在 Azure 数字孪生实例上启用系统分配的标识时，Azure 会在 [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) 中自动为该实例创建一个标识。 然后，可以使用该标识向 Azure 数字孪生终结点进行身份验证，以转发事件。

可以在 **对实例进行初始设置的过程中** 为 Azure 数字孪生实例启用系统托管标识，或者 **稍后在已存在的实例上启用系统托管标识**。

这些创建方法都将为实例提供相同的配置选项和相同的最终结果。 本部分将介绍如何使用这两种方法。

### <a name="add-a-system-managed-identity-during-instance-creation"></a>在创建实例的过程中添加系统托管标识

在本部分，你将了解如何在当前正在创建的 Azure 数字孪生实例上启用系统托管标识。 本部分重点介绍创建过程中的托管标识步骤；有关创建新的 Azure 数字孪生实例的完整演练，请参阅[操作指南：设置实例和身份验证](how-to-set-up-instance-portal.md)。

系统托管标识选项位于实例设置的“高级”选项卡中。

在此选项卡中，选择“系统托管标识”对应的“打开”选项以打开此功能。 

:::image type="content" source="media/how-to-enable-managed-identities/create-instance-advanced.png" alt-text="Azure 门户的屏幕截图，其中显示了 Azure 数字孪生的“创建资源”对话框的“高级”选项卡。选项卡名称已突出显示，另外还突出显示了“系统托管标识”对应的“打开”选项和导航按钮（“查看 + 创建”、“上一步”、“下一步: 高级”）。":::

然后，可以使用底部的导航按钮继续完成余下的实例设置。

### <a name="add-a-system-managed-identity-to-an-existing-instance"></a>将系统托管标识添加到现有实例

在本部分，你要将一个系统托管标识添加到已存在的 Azure 数字孪生实例。

1. 首先，在浏览器中导航到 [Azure 门户](https://portal.azure.com)。

1. 在门户搜索栏中搜索你的实例名称，然后选择该实例查看其详细信息。

1. 在左侧菜单中选择“标识(预览版)”。

1. 在此页上，选择“打开”选项以打开此功能。

1. 点击“保存”按钮，然后点击“是”以确认。 

    :::image type="content" source="media/how-to-enable-managed-identities/identity-digital-twins.png" alt-text="Azure 门户的屏幕截图，其中显示了 Azure 数字孪生实例的“标识(预览版)”页。Azure 数字孪生实例菜单中的页名称已突出显示，另外还突出显示了“状态”对应的“打开”选项、“保存”按钮和“是”确认按钮。":::

保存更改后，此页将显示新标识的更多字段：“对象 ID”和“权限”。 

如果需要，可以复制此处的“对象 ID”，并使用“权限”按钮查看分配给该标识的 Azure 角色。  若要设置一些角色，请继续阅读下一部分。

## <a name="assign-azure-roles-to-the-identity"></a>将 Azure 角色分配给标识 

为 Azure 数字孪生实例创建系统分配的标识后，需要为其分配适当的角色来通过不同类型的[终结点](concepts-route-events.md)进行身份验证，以将事件转发到受支持的目标。 本部分将介绍角色选项，以及如何将它们分配给系统分配的标识。

>[!NOTE]
> 这是一个重要步骤 — 如果不执行此步骤，标识将无法访问终结点，并且事件不会被传送。

### <a name="supported-destinations-and-azure-roles"></a>支持的目标和 Azure 角色 

下面是标识为了访问终结点而需要的最小角色，所需的具体角色取决于目标的类型。 分配权限更高的角色（例如“数据所有者”角色）也同样起作用。

| 目标 | Azure 角色 |
| --- | --- |
| Azure 事件中心 | Azure 事件中心数据发送方 |
| Azure 服务总线 | Azure 服务总线数据发送方 |
| Azure 存储容器 | 存储 Blob 数据参与者 |

有关 Azure 数字孪生中支持用于路由的终结点、路由和目标类型的详细信息，请参阅[概念：事件路由](concepts-route-events.md)。

### <a name="assign-the-role"></a>分配角色

[!INCLUDE [digital-twins-permissions-required.md](../../includes/digital-twins-permissions-required.md)]

若要为标识分配角色，请先打开 [Azure 门户](https://portal.azure.com)。

1. 在门户搜索栏中搜索你的终结点资源（事件中心、服务总线主题或存储容器）名称并导航到该资源。 
1. 在左侧菜单中选择“访问控制(IAM)”。
1. 选择“+ 添加”按钮以添加一个新的角色分配。

    :::image type="content" source="media/how-to-enable-managed-identities/add-role-assignment-1.png" alt-text="显示事件中心的“访问控制(IAM)”页的 Azure 门户屏幕截图。已突出显示“+ 添加”按钮。" lightbox="media/how-to-enable-managed-identities/add-role-assignment-1.png":::

1. 在随后出现的“添加角色分配”页上填写值：
    * **角色**：从下拉菜单中选择所需的角色。
    * **将访问权限分配给**：在“系统分配的托管标识”下，选择“数字孪生”。 
    * **订阅**：选择订阅。 此时会显示所选订阅中的所有 Azure 数字孪生托管标识。
    * **选择**：在此处选择要为其分配角色的 Azure 数字孪生实例的托管标识。 托管标识的名称与实例的名称相匹配，因此请选择你的 Azure 数字孪生实例的名称。 选择该名称时，实例的标识将显示在窗格底部的“选定成员”部分。

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-enable-managed-identities/add-role-assignment-2.png" alt-text="将所列字段填充到“添加角色分配”对话框中":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

输入完详细信息后，选择“保存”。

## <a name="create-an-endpoint-with-identity-based-authentication"></a>创建使用基于身份的身份验证的终结点

为 Azure 数字孪生实例设置系统托管标识并为其分配适当的角色后，可以创建能够使用该标识进行身份验证的 Azure 数字孪生[终结点](how-to-manage-routes-portal.md#create-an-endpoint-for-azure-digital-twins)。 此选项仅适用于事件中心和服务总线类型终结点（事件网格不支持此选项）。

>[!NOTE]
> 对于已使用基于密钥的标识创建的终结点，不能通过对其进行编辑来将其更改为使用基于标识的身份验证。 必须选择最初创建终结点时指定的身份验证类型。

请按照[有关创建 Azure 数字孪生终结点的说明](how-to-manage-routes-portal.md#create-an-endpoint-for-azure-digital-twins)操作。

在执行填写终结点类型所需的详细信息的步骤时，请务必选择“基于标识”作为“身份验证类型”。

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-event-hub-authentication.png" alt-text="创建“事件中心”类型终结点的屏幕截图。" lightbox="media/how-to-manage-routes-portal/create-endpoint-event-hub-authentication.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

完成终结点设置，然后选择“保存”。

## <a name="considerations-for-disabling-system-managed-identities"></a>有关禁用系统托管标识的注意事项

由于标识与使用该标识的终结点是分开管理的，因此必须考虑到对标识或其角色做出的任何更改可能给 Azure 数字孪生实例中的终结点造成的影响。 如果禁用标识或者从中删除某个终结点的必要角色，终结点可能变得不可访问，并且事件流将会中断。

若要继续使用某个终结点，而该终结点是使用现已禁用的托管标识设置的，则需要删除该终结点，然后使用不同的身份验证类型[重新创建它](how-to-manage-routes-portal.md#create-an-endpoint-for-azure-digital-twins)。 做出此项更改后，最长可能需要经过一小时，事件才会继续传送到终结点。

## <a name="next-steps"></a>后续步骤

详细了解 Azure AD 中的托管标识： 
* [*Azure 资源的托管标识*](../active-directory/managed-identities-azure-resources/overview.md)