---
title: 在 Privileged Identity Management 中分配 Azure 资源角色 - Azure Active Directory | Microsoft Docs
description: 了解如何在 Azure AD Privileged Identity Management (PIM) 中分配 Azure 资源角色。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 06/15/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2c50d62d5c8f24ed25258305411f9ed045098c7f
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/16/2021
ms.locfileid: "112232824"
---
# <a name="assign-azure-resource-roles-in-privileged-identity-management"></a>在 Privileged Identity Management 中分配 Azure 资源角色

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) 可以管理内置的 Azure 资源角色以及自定义角色，包括但不限于：

- “所有者”
- 用户访问管理员
- 参与者
- 安全管理员
- 安全管理器

> [!NOTE]
> 默认情况下，分配到“所有者”或“用户访问管理员”订阅角色的用户或组成员以及在 Azure AD 中启用订阅管理的 Azure AD 全局管理员具有资源管理员权限。 这些管理员可以对 Azure 资源使用 Privileged Identity Management 来分配角色、配置角色设置，以及审查访问权限。 如果没有资源管理员权限，用户将无法管理资源的 Privileged Identity Management。 查看 [Azure 内置角色](../../role-based-access-control/built-in-roles.md)列表。

Privileged Identity Management 支持内置的和自定义的 Azure 角色。 有关 Azure 自定义角色的详细信息，请参阅 [Azure 自定义角色](../../role-based-access-control/custom-roles.md)。

## <a name="role-assignment-conditions"></a>角色分配条件

可以使用 Azure 基于属性的访问控制 (Azure ABAC) 预览版，通过 Privileged Identity Management (PIM) 对符合条件的角色分配设置资源条件。 使用 PIM 时，最终用户必须激活符合条件的角色分配，才能获得执行特定操作的权限。 通过在 PIM 中使用 Azure ABAC 条件，不仅可以使用精细的条件限制用户对资源的角色权限，还可以使用 PIM 中通过限时设置、审批工作流和审核核线索等保护角色分配。 有关详细信息，请参阅 [Azure 基于属性的访问控制公共预览版](../../role-based-access-control/conditions-overview.md)。

## <a name="assign-a-role"></a>分配角色

遵循以下步骤可使用户符合 Azure 资源角色的条件。

1. 使用“所有者”或“用户访问管理员”角色权限登录到 [Azure 门户](https://portal.azure.com/)。

1. 打开“Azure AD Privileged Identity Management”。 

1. 选择“Azure 资源”  。

1. 使用资源筛选器可查找所需的受管理资源。

    ![要管理的 Azure 资源的列表](./media/pim-resource-roles-assign-roles/resources-list.png)

1. 选择要管理的资源以打开资源概览页。

1. 在“管理”下，选择“角色” 以查看 Azure 资源的角色列表。

    ![Azure 资源角色](./media/pim-resource-roles-assign-roles/resources-roles.png)

1. 选择“添加分配”以打开“添加分配”窗格。 

1. 选择“选择角色”以打开“选择角色”页 。

    ![“新建分配”窗格](./media/pim-resource-roles-assign-roles/resources-select-role.png)

1. 选择要分配的角色，然后单击“选择”  。

    此时会打开“选择成员或组”窗格。

1. 选择要向角色分配的成员或组，然后单击“选择”  。

    ![“选择成员或组”窗格](./media/pim-resource-roles-assign-roles/resources-select-member-or-group.png)

1. 在“设置”选项卡的“分配类型”列表中，选择“合格”或“活动” 。

    ![“成员身份设置”窗格](./media/pim-resource-roles-assign-roles/resources-membership-settings-type.png)

    Azure 资源的 Privileged Identity Management 提供了两种不同的分配类型：

    - “合格”分配要求该角色的成员执行某个操作才能使用该角色。 操作可能包括执行多重身份验证 (MFA) 检查、提供业务理由或请求获得指定审批者的批准。

    - “活动”  分配不要求成员执行任何操作便可使用该角色。 分配为“活动”的成员始终具有分配给该角色的权限。

1. 若要指定特定分配持续时间，请更改开始和结束日期与时间。

1. 完成后，选择“分配”。

1. 创建新的角色分配后，会显示状态通知。

    ![新建分配 - 通知](./media/pim-resource-roles-assign-roles/resources-new-assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>更新或删除现有的角色分配

按照以下步骤更新或删除现有的角色分配。

1. 打开“Azure AD Privileged Identity Management”。

1. 选择“Azure 资源” 。

1. 选择要管理的资源以打开其概览页。

1. 在“管理”下，选择“角色” 以查看 Azure 资源的角色列表。

    ![Azure 资源角色 - 选择角色](./media/pim-resource-roles-assign-roles/resources-update-select-role.png)

1. 选择要更新或删除的角色。

1. 在“合格角色”或“活动角色”选项卡上查找角色分配。

    ![更新或删除角色分配](./media/pim-resource-roles-assign-roles/resources-update-remove.png)

1. 若要添加或更新条件以优化 Azure 资源访问，请在角色分配的“条件”列中选择“添加”或“查看/编辑”  。 目前，Privileged Identity Management 中的存储 Blob 数据所有者、存储 Blob 数据读取者和 Blob 存储 Blob 数据参与者角色是 [Azure 基于属性的访问控制公共预览版](../../role-based-access-control/conditions-overview.md)支持的仅有的两个角色。

    ![更新或删除访问控制的属性](./media/pim-resource-roles-assign-roles/resources-abac-update-remove.png)

1. 选择“更新”或“删除”以更新或删除角色分配。

    有关扩展角色分配的信息，请参阅[在 Privileged Identity Management 中扩展或续订 Azure 资源角色](pim-resource-roles-renew-extend.md)。

## <a name="next-steps"></a>后续步骤

- [在 Privileged Identity Management 中扩展或续订 Azure 资源角色](pim-resource-roles-renew-extend.md)
- [在 Privileged Identity Management 中配置 Azure 资源角色设置](pim-resource-roles-configure-role-settings.md)
- [在 Privileged Identity Management 中分配 Azure AD 角色](pim-how-to-add-role-to-user.md)
