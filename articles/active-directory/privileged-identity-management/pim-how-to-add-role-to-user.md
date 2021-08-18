---
title: 在 PIM 中分配 Azure AD 角色 - Azure Active Directory | Microsoft Docs
description: 了解如何在 Azure AD Privileged Identity Management (PIM) 中分配 Azure AD 角色。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 06/03/2021
ms.author: curtand
ms.collection: M365-identity-device-management
ms.custom: subject-rbac-steps
ms.openlocfilehash: a741ce7fff528fbe1f4120f4138a88d7b6e2e915
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/16/2021
ms.locfileid: "112233000"
---
# <a name="assign-azure-ad-roles-in-privileged-identity-management"></a>在 Privileged Identity Management 中分配 Azure AD 角色

使用 Azure Active Directory (Azure AD)，全局管理员可以完成 **永久性的** Azure AD 管理员角色分配。 可以使用 [Azure 门户](../roles/permissions-reference.md)或 [PowerShell 命令](/powershell/module/azuread#directory_roles)创建这些角色分配。

Azure AD Privileged Identity Management (PIM) 服务还允许特权角色管理员进行永久管理员角色分配。 此外，特权角色管理员可将用户设置为 Azure AD 管理员角色的 **合格** 用户。 符合条件的管理员可在需要时激活角色，在完成任务后，其权限随即失效。

Privileged Identity Management 支持内置角色和自定义 Azure AD 角色。 有关 Azure AD 自定义角色的详细信息，请参阅 [Azure Active Directory 中基于角色的访问控制](../roles/custom-overview.md)。

## <a name="assign-a-role"></a>分配角色

遵循以下步骤可使用户符合 Azure AD 管理员角色的条件。

1. 使用“[特权角色管理员](../roles/permissions-reference.md#privileged-role-administrator)”角色成员的用户身份登录到 [Azure 门户](https://portal.azure.com/)。

1. 打开“Azure AD Privileged Identity Management”。 

1. 选择“Azure AD 角色”。

1. 选择“角色”，查看 Azure AD 权限的角色列表。

    ![已选择“添加分配”操作的“角色”页的屏幕截图。](./media/pim-how-to-add-role-to-user/roles-list.png)

1. 选择“添加分配”以打开“添加分配”页。 

1. 选择“选择角色”以打开“选择角色”页 。

    ![“新建分配”窗格](./media/pim-how-to-add-role-to-user/select-role.png)

1. 依次选择要分配的角色、要向角色分配的成员、“下一步”。

1. 在“成员身份设置”窗格的“分配类型”列表中，选择“合格”或“活动”。

    - “合格”  分配要求该角色的成员执行某个操作才能使用该角色。 操作可能包括执行多重身份验证 (MFA) 检查、提供业务理由或请求获得指定审批者的批准。

    - “活动”  分配不要求成员执行任何操作便可使用该角色。 分配为“活动”的成员始终具有分配给该角色的权限。

1. 若要指定特定分配持续时间，请添加开始和结束日期与时间框。 完成后，选择“分配”以创建新的角色分配。

    ![成员身份设置 - 日期和时间](./media/pim-how-to-add-role-to-user/start-and-end-dates.png)

1. 分配角色后，会显示分配状态通知。

    ![新建分配 - 通知](./media/pim-how-to-add-role-to-user/assignment-notification.png)

## <a name="assign-a-role-with-restricted-scope"></a>向角色分配受限范围

对于某些角色，所授予权限的范围可以限制为单个管理单元、服务主体或应用程序。 若要分配范围为一个管理单元的角色，则可参考此过程。 有关支持管理单元范围的角色列表，请参阅[分配范围限定为一个管理单元的角色](../roles/admin-units-assign-roles.md)。 此功能目前正在向 Azure AD 组织推出。

1. 使用特权角色管理员权限登录到 [Azure Active Directory 管理中心](https://aad.portal.azure.com)。

1. 选择“Azure Active Directory” > “角色和管理员”。 。

1. 选择“用户管理员”。

    ![在门户中打开角色时，可以使用“添加分配”命令](./media/pim-how-to-add-role-to-user/add-assignment.png)

1. 选择“添加分配”。

    ![如果角色支持范围，可以选择一个范围](./media/pim-how-to-add-role-to-user/add-scope.png)

1. 在“添加分配”页上，你可以：

   - 选择要分配给角色的用户或组
   - 选择角色范围（本例中为管理单元）
   - 对于范围，选择一个管理单元

有关创建管理单元的详细信息，请参阅[添加和删除管理单元](../roles/admin-units-manage.md)。

## <a name="update-or-remove-an-existing-role-assignment"></a>更新或删除现有的角色分配

按照以下步骤更新或删除现有的角色分配。 **仅限 Azure AD P2 授权客户**：不要通过 Azure AD 和 Privileged Identity Management (PIM) 为角色分配活动组。 有关详细说明，请参阅[已知问题](../roles/groups-concept.md#known-issues)。

1. 打开“Azure AD Privileged Identity Management”。

1. 选择“Azure AD 角色”。

1. 选择“角色”以查看 Azure AD 的角色列表。

1. 选择要更新或删除的角色。

1. 在“合格角色”或“活动角色”选项卡上查找角色分配。

    ![更新或删除角色分配](./media/pim-how-to-add-role-to-user/remove-update-assignments.png)

1. 选择“更新”或“删除”以更新或删除角色分配。

## <a name="next-steps"></a>后续步骤

- [在 Privileged Identity Management 中配置 Azure AD 管理员角色设置](pim-how-to-change-default-settings.md)
- [在 Privileged Identity Management 中分配 Azure 资源角色](pim-resource-roles-assign-roles.md)