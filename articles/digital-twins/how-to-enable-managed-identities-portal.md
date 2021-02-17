---
title: " (预览) 门户中启用路由事件的托管标识"
titleSuffix: Azure Digital Twins
description: 请参阅如何为 Azure 数字孪生启用系统分配的标识并将其用于转发事件，使用 Azure 门户。
author: baanders
ms.author: baanders
ms.date: 1/21/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 5c216661e45e5ed34a95e8a56002f3ad175b089a
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/16/2021
ms.locfileid: "100545899"
---
# <a name="enable-a-managed-identity-for-routing-azure-digital-twins-events-preview-azure-portal"></a>启用托管标识，以将 Azure 数字孪生事件路由 (预览) ： Azure 门户

[!INCLUDE [digital-twins-managed-service-identity-selector.md](../../includes/digital-twins-managed-service-identity-selector.md)]

本文介绍了如何为当前处于预览)  (的[Azure 数字孪生实例启用系统分配的标识](concepts-security.md#managed-identity-for-accessing-other-resources-preview)，并在将事件转发到受支持的目标（如[事件中心](../event-hubs/event-hubs-about.md)、[服务总线](../service-bus-messaging/service-bus-messaging-overview.md)   目标和[Azure 存储容器](../storage/blobs/storage-blobs-introduction.md)）时使用标识。

本文逐步介绍了如何使用 [**Azure 门户**](https://portal.azure.com)。

下面是本文中所述的步骤： 

1. 使用系统分配的标识创建 Azure 数字孪生实例，或在现有的 Azure 数字孪生实例上启用系统分配的标识。 
1. 向标识中添加适当的角色或角色。 例如，如果终结点是事件中心，则将 **Azure 事件中心数据发送方** 角色分配给标识; 如果终结点是服务总线，则将其分配给 **Azure 服务总线数据发送方角色** 。
1. 在 Azure 数字孪生中创建一个可以使用系统分配的标识进行身份验证的终结点。

## <a name="enable-system-managed-identities-for-an-instance"></a>为实例启用系统管理的标识 

当你在 Azure 数字孪生实例上启用系统分配的标识时，Azure 会在 [Azure Active Directory (Azure AD) ](../active-directory/fundamentals/active-directory-whatis.md)中自动为其创建标识。 然后，可以使用该标识对 Azure 数字孪生终结点进行身份验证，以便进行事件转发。

可以在 **实例的初始安装过程** 中启用 Azure 数字孪生实例的系统管理的标识，也可以 **稍后在已存在的实例上启用它**。

这些创建方法中的任何一种都将为你的实例提供相同的配置选项和相同的最终结果。 本部分介绍如何执行这两项操作。

### <a name="add-a-system-managed-identity-during-instance-creation"></a>在实例创建过程中添加系统管理的标识

在本部分中，你将了解如何在当前创建的 Azure 数字孪生实例上启用系统管理的标识。 本部分重点介绍创建过程的托管标识步骤;有关创建新的 Azure 数字孪生实例的完整演练，请参阅 [*如何：设置实例和身份验证*](how-to-set-up-instance-portal.md)。

"系统管理的标识" 选项位于实例设置的 " **高级** " 选项卡中。

在此选项卡中，选择 "**系统托管标识**" 的 **"打开**" 选项以打开此功能。

:::image type="content" source="media/how-to-enable-managed-identities/create-instance-advanced.png" alt-text="显示 Azure 数字孪生的 &quot;创建资源&quot; 对话框的 &quot;高级&quot; 选项卡的 Azure 门户屏幕截图。选项卡名称周围有一个突出显示，即 &quot;系统托管标识&quot; 的 &quot;打开&quot; 选项，导航按钮 (查看 + 创建、上一个、下一步：高级) 。":::

然后，你可以使用底部导航按钮继续实例安装程序的其余部分。

### <a name="add-a-system-managed-identity-to-an-existing-instance"></a>向现有实例添加系统管理的标识

在本部分中，会将系统管理的标识添加到已存在的 Azure 数字孪生实例。

1. 首先，在浏览器中导航到 [Azure 门户](https://portal.azure.com) 。

1. 在门户搜索栏中搜索实例的名称，并选择它以查看其详细信息。

1. 选择左侧菜单中的 " **标识 (预览")** 。

1. 在此页上，选择 **"打开" 选项以** 打开此功能。

1. 单击 " **保存** " 按钮，然后单击 **"是"** 进行确认。

    :::image type="content" source="media/how-to-enable-managed-identities/identity-digital-twins.png" alt-text="Azure 门户的屏幕截图，显示 Azure 数字孪生实例的标识 (预览版) 页。&quot;Azure 数字孪生实例&quot; 菜单中的页面名称周围有一个突出显示，&quot;状态&quot; 选项、&quot;保存&quot; 按钮和 &quot;是确认&quot; 按钮。":::

保存更改后，此页面上将为新标识的 **对象 ID** 和 **权限** 显示更多字段。

如果需要，可以从此处复制 **对象 ID** ，并使用 " **权限** " 按钮查看分配给该标识的 Azure 角色。 若要设置一些角色，请转到下一节。

## <a name="assign-azure-roles-to-the-identity"></a>将 Azure 角色分配到标识 

为 Azure 数字孪生实例创建系统分配的标识后，需要为其分配适当的角色以使用不同类型的 [终结点](concepts-route-events.md) 进行身份验证，以便将事件转发到受支持的目标。 本部分介绍角色选项，以及如何将它们分配到系统分配的标识。

>[!NOTE]
> 这是一个重要的步骤，如果没有它，标识将无法访问终结点，并且不会传递事件。

### <a name="supported-destinations-and-azure-roles"></a>支持的目标和 Azure 角色 

下面是标识访问终结点所需的最少角色，具体取决于目标的类型。 具有更高权限的角色 (如数据所有者角色) 也将起作用。

| 目标 | Azure 角色 |
| --- | --- |
| Azure 事件中心 | Azure 事件中心数据发送方 |
| Azure 服务总线 | Azure 服务总线数据发送方 |
| Azure 存储容器 | 存储 Blob 数据参与者 |

有关终结点、路由和 Azure 数字孪生中支持路由的目标类型的详细信息，请参阅 [*概念：事件路由*](concepts-route-events.md)。

### <a name="assign-the-role"></a>分配角色

>[!NOTE]
> 此部分必须由具有管理用户对 Azure 资源的用户访问权限的 Azure 用户完成， (包括授予和委派) 的权限。 满足此要求的常见角色包括 " *所有者*"、" *帐户管理员*" 或 " *用户访问管理员* " 和 " *参与者*" 的组合。 有关 Azure 数字孪生角色的权限要求的详细信息，请参阅 [*如何：设置实例和身份验证*](how-to-set-up-instance-portal.md#prerequisites-permission-requirements)。

若要为标识分配角色，请先打开 [Azure 门户](https://portal.azure.com)。

1. 通过在门户搜索栏中搜索 (事件中心、服务总线主题或存储容器) 来导航到终结点资源。 
1. 在左侧菜单中选择 " **访问控制 (IAM)** 。
1. 选择 " **+ 添加** " 按钮以添加新的角色分配。

    :::image type="content" source="media/how-to-enable-managed-identities/add-role-assignment-1.png" alt-text="显示事件中心的访问控制 (IAM) 页的 Azure 门户屏幕截图。&quot;+ 添加&quot; 按钮将突出显示。" lightbox="media/how-to-enable-managed-identities/add-role-assignment-1.png":::

1. 在 " **添加角色分配** " 页上，填写以下值：
    * **角色**：从下拉菜单中选择所需的角色。
    * **分配访问权限**：在 " **系统分配的托管标识**" 下，选择 " **数字孪生**"。
    * **订阅**：选择订阅。 这将显示所选订阅中的所有 Azure 数字孪生管理标识。
    * **选择**：在此处选择要为其分配角色的 Azure 数字孪生实例的托管标识。 托管标识的名称与实例的名称相匹配，因此请选择 Azure 数字孪生实例的名称。 选择它时，实例的标识将显示在窗格底部的 " **选定成员** " 部分中。

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-enable-managed-identities/add-role-assignment-2.png" alt-text="将列出的字段填充到 &quot;添加角色分配&quot; 对话框中":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

输入完详细信息后，请选择 " **保存**"。

## <a name="create-an-endpoint-with-identity-based-authorization"></a>创建具有基于标识的授权的终结点

为 Azure 数字孪生实例设置系统管理的标识并向其分配适当的角色 () ，你可以创建能够使用标识进行身份验证的 Azure 数字孪生 [终结点](how-to-manage-routes-portal.md#create-an-endpoint-for-azure-digital-twins) 。 此选项仅适用于事件中心和服务总线类型终结点， (事件网格) 不支持此选项。

>[!NOTE]
> 不能编辑已使用基于密钥的标识更改为基于标识的身份验证的终结点。 首次创建终结点时，必须选择身份验证类型。

按照 [说明创建 Azure 数字孪生终结点](how-to-manage-routes-portal.md#create-an-endpoint-for-azure-digital-twins)。

当你完成终结点类型所需的详细信息的步骤时，请确保选择 " **基于标识** " 作为 "身份验证类型"。

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-event-hub-authentication.png" alt-text="创建事件中心类型终结点的屏幕截图。" lightbox="media/how-to-manage-routes-portal/create-endpoint-event-hub-authentication.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

完成终结点设置，并选择 " **保存**"。

## <a name="considerations-for-disabling-system-managed-identities"></a>禁用系统托管标识的注意事项

由于标识是与使用它的终结点分开管理的，因此请务必考虑对标识或其角色所做的任何更改对 Azure 数字孪生实例中的终结点的影响。 如果已禁用标识，或者删除了某个终结点的必要角色，则终结点可能变为不可访问，并且事件流将中断。

若要继续使用通过已禁用的托管标识设置的终结点，则需要删除终结点，并使用不同的身份验证类型 [重新创建它](how-to-manage-routes-portal.md#create-an-endpoint-for-azure-digital-twins) 。 在此更改后，事件可能需要长达一小时的时间才能继续传递到终结点。

## <a name="next-steps"></a>后续步骤

详细了解 Azure AD 中的托管标识： 
* [*Azure 资源的托管标识*](../active-directory/managed-identities-azure-resources/overview.md)