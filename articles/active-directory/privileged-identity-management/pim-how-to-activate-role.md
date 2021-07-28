---
title: 在 PIM 中激活 Azure AD 角色 - Azure Active Directory | Microsoft Docs
description: 了解如何在 Azure AD Privileged Identity Management (PIM) 中激活 Azure AD 角色。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 05/28/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7d5e6102a46e8015fa4fb7a1a148950e98629681
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/02/2021
ms.locfileid: "110793892"
---
# <a name="activate-my-azure-ad-roles-in-pim"></a>在 PIM 中激活我的 Azure AD 角色

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) 简化了企业管理以特权身份访问 Azure AD 和其他 Microsoft 联机服务（如 Microsoft 365 或 Microsoft Intune）中的资源的方式。  

如果你符合管理角色的资格，则必须在需要执行特权操作时激活角色分配 。 例如，如果你偶尔管理 Microsoft 365 功能，则组织的特权角色管理员可能不会让你成为永久性的全局管理员，因为该角色也影响其他服务。 但是，他们会让你符合 Azure AD 角色（例如 Exchange Online 管理员）的资格。 可以在需要权限时，请求暂时分配该角色，并将在预定的时段内拥有管理员控制权。

本文面向需要在 Privileged Identity Management 中激活其 Azure AD 角色的管理员。

## <a name="determine-your-version-of-pim"></a>确定 PIM 版本

从 2019 年 11 月开始，Privileged Identity Management 的 Azure AD 角色部分将更新为与 Azure 资源角色的体验相匹配的新版本。 这将创建附加功能以及[对现有 API 的更改](azure-ad-roles-features.md#api-changes)。 在推出新版本时，本文中遵循的过程取决于当前拥有的 Privileged Identity Management 版本。 按照本部分中的步骤确定所拥有的 Privileged Identity Management 的版本。 了解 Privileged Identity Management 版本之后，可以选择本文中与该版本匹配的过程。

1. 使用[特权角色管理员](../roles/permissions-reference.md#privileged-role-administrator)角色登录到 [Azure 门户](https://portal.azure.com/)。
1. 打开“Azure AD Privileged Identity Management”。  如果在概述页的顶部有横幅，请按照本文“新版本”选项卡中的说明进行操作  。 否则，请按照“先前版本”选项卡中的说明操作  。

    [![选择“Azure AD”>“Privileged Identity Management”。](media/pim-how-to-add-role-to-user/pim-new-version.png)](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

# <a name="new-version"></a>[新版本](#tab/new)

## <a name="activate-a-role-for-new-version"></a>激活新版本的角色

需要充当某个 Azure AD 角色时，可在 Privileged Identity Management 中通过打开“我的角色”请求激活。

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. 打开“Azure AD Privileged Identity Management”。  有关如何将 Privileged Identity Management 磁贴添加到仪表板的信息，请参阅[开始使用 Privileged Identity Management](pim-getting-started.md)。

1. 选择“我的角色”，然后选择“Azure AD 角色”，查看符合条件的 Azure AD 角色的列表。 

    ![显示可以激活的角色的“我的角色”页](./media/pim-how-to-activate-role/my-roles.png)

1. 在“Azure AD 角色”列表中，找到要激活的角色。

    ![Azure AD 角色 - 我的合格角色列表](./media/pim-how-to-activate-role/activate-link.png)

1. 选择“激活”打开“激活”窗格。

    ![Azure AD 角色 - 激活页面包含持续时间和范围](./media/pim-how-to-activate-role/activate-page.png)

1. 选择“需要附加验证”，并按照说明提供其他安全验证。 在每个会话中只需执行身份验证一次。

    ![用于提供安全验证（例如 PIN 码）的屏幕](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. 多重身份验证后，选择“激活，然后继续”。

    ![在激活角色之前使用 MFA 验证身份](./media/pim-how-to-activate-role/activate-role-mfa-banner.png)

1. 如果要指定缩小的范围，请选择“范围”以打开筛选器窗格。 在筛选器窗格中，可以指定需要访问的 Azure AD 资源。 最佳做法是请求访问所需的最少资源。

1. 根据需要指定自定义的激活开始时间。 Azure AD 角色将在选定时间后激活。

1. 在“原因”框中，输入该激活请求的原因。

1. 选择“激活”。

    如果[角色需要审批](pim-resource-roles-approval-workflow.md)才能激活，则浏览器右上角会显示一条通知，告知你请求正在等待审批。

    ![“激活请求正在等待审批”通知](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="view-the-status-of-your-requests-for-new-version"></a>查看新版本的请求状态

可以查看等待激活的请求的状态。

1. 打开 Azure AD Privileged Identity Management。

1. 选择“我的请求”，查看你的 Azure AD 角色和 Azure 资源角色请求列表。

    ![显示挂起的请求的“我的请求 - Azure AD”页](./media/pim-how-to-activate-role/my-requests-page.png)

1. 向右滚动以查看“请求状态”列。

## <a name="cancel-a-pending-request-for-new-version"></a>取消新版本的挂起请求

如果不需要激活需要审批的角色，随时可以取消等待中的请求。

1. 打开 Azure AD Privileged Identity Management。

1. 选择“我的请求”。

1. 针对想要取消的角色，选择“取消”链接。

    选择“取消”会取消该请求。 若要再次激活该角色，必须提交新的激活请求。

   ![突出显示“取消”操作的“我的请求”列表](./media/pim-resource-roles-activate-your-roles/resources-my-requests-cancel.png)

## <a name="troubleshoot-for-new-version"></a>对新版本进行故障排除

### <a name="permissions-are-not-granted-after-activating-a-role"></a>激活角色后，权限未被授予

在 Privileged Identity Management 中激活角色时，激活可能不会立即传播到需要特权角色的所有门户。 有时，即使更改已传播，门户中的 Web 缓存也可能会导致更改不能立即生效。 如果激活延迟，请从尝试执行该操作的门户注销，然后重新登录。 在 Azure 门户中，PIM 会自动注销并重新登录。

# <a name="previous-version"></a>[先前版本](#tab/previous)

## <a name="activate-a-role-previous-version"></a>激活角色（以前版本）

需要充当某个 Azure AD 角色时，可在 Privileged Identity Management 中使用“我的角色”导航选项请求激活。

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. 打开“Azure AD Privileged Identity Management”。  有关如何将 Privileged Identity Management 磁贴添加到仪表板的信息，请参阅[开始使用 Privileged Identity Management](pim-getting-started.md)。

1. 选择“Azure AD 角色”。

1. 选择“我的角色”，查看你有资格获取的 Azure AD 角色列表。

    ![Azure AD 角色 -“我的角色”列表，显示符合条件的或活跃的角色](./media/pim-how-to-activate-role/directory-roles-my-roles.png)

1. 找到想要激活的角色。

    ![Azure AD 角色 -“我的符号条件的角色”列表，显示“激活”链接](./media/pim-how-to-activate-role/directory-roles-my-roles-activate.png)

1. 选择“激活”打开“角色激活详细信息”窗格。

1. 如果角色需要多重身份验证 (MFA)，请选择“验证你的身份，然后继续”。 只需在每个会话中执行身份验证一次。

    ![“在激活角色之前使用 MFA 验证身份”窗格](./media/pim-how-to-activate-role/directory-roles-my-roles-mfa.png)

1. 选择“验证我的身份”，并按照说明提供其他安全验证。

    ![询问你的联系方式的“其他安全验证”页](./media/pim-how-to-activate-role/additional-security-verification.png)

1. 选择“激活”打开“激活”窗格。

    ![“激活”窗格，用于指定开始时间、持续时间、票证和原因](./media/pim-how-to-activate-role/directory-roles-activate.png)

1. 根据需要指定自定义的激活开始时间。

1. 指定激活持续时间。

1. 在“激活原因”框中，输入该激活请求的原因。 某些角色要求提供问题票证编号。

    ![“已完成激活”窗格，其中包含自定义开始时间、持续时间、票证和原因](./media/pim-how-to-activate-role/directory-roles-activation-pane.png)

1. 选择“激活”  。

    如果角色不需要审批，则会出现一个“激活状态”窗格，其中显示激活状态。

    ![“激活状态”页，显示激活的三个阶段](./media/pim-how-to-activate-role/activation-status.png)

    在所有阶段都完成后，选择“注销”链接，从 Azure 门户中注销。 重新登录门户后，即可使用此角色。

    如果[角色需要审批](./azure-ad-pim-approval-workflow.md)才能激活，则浏览器右上角会显示一条 Azure 通知，告知你请求正在等待审批。

## <a name="view-the-status-of-your-requests-previous-version"></a>查看请求的状态（以前版本）

可以查看等待激活的请求的状态。

1. 打开 Azure AD Privileged Identity Management。

1. 选择“Azure AD 角色”。

1. 选择“我的请求”以查看请求列表。

    ![Azure AD 角色 -“我的请求”列表](./media/pim-how-to-activate-role/directory-roles-my-requests.png)

## <a name="deactivate-a-role-previous-version"></a>停用角色（以前版本）

角色激活后，在达到时间限制（符合条件的时间段）时会自动停用。

如果提前完成了管理员任务，也可以在 Azure AD Privileged Identity Management 中手动停用角色。

1. 打开 Azure AD Privileged Identity Management。

1. 选择“Azure AD 角色”。

1. 选择“我的角色”  。

1. 选择“活动角色”，以查看活动角色的列表。

1. 找到已用完的角色，然后选择“停用”。

## <a name="cancel-a-pending-request-previous-version"></a>取消挂起的请求（以前版本）

如果不需要激活需要审批的角色，随时可以取消等待中的请求。

1. 打开 Azure AD Privileged Identity Management。

1. 选择“Azure AD 角色”。

1. 选择“我的请求”。

1. 针对想要取消的角色，选择“取消”按钮。

    选择“取消”会取消该请求。 若要再次激活该角色，必须提交新的激活请求。

   ![“我的请求”列表，突出显示了“取消”按钮](./media/pim-how-to-activate-role/directory-role-cancel.png)

## <a name="troubleshoot-previous-version"></a>故障排除（以前版本）

### <a name="permissions-are-not-granted-after-activating-a-role"></a>激活角色后，权限未被授予

当你在 Privileged Identity Management 中激活角色时，在除 Azure 门户以外的管理门户中（如 Office 365 门户）激活可能会延迟。 如果激活延迟，请从所在门户注销，然后重新登录。 然后使用 Privileged Identity Management 验证是否已将你列为角色的成员。

 ---

## <a name="next-steps"></a>后续步骤

- [查看 Azure AD 角色的审核历史记录](pim-how-to-use-audit-log.md)
