---
title: 在 PIM 中激活特权访问组角色 - Azure AD | Microsoft Docs
description: 了解如何在 Azure AD Privileged Identity Management (PIM) 中激活特权访问组角色。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 07/01/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: a95d2bc1d08870792d48fc61fb0fb6ec24440e3b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778039"
---
# <a name="activate-my-privileged-access-group-roles-in-privileged-identity-management"></a>在 Privileged Identity Management 中激活特权访问组角色

使用 Privileged Identity Management (PIM) 来允许特权访问组的合格角色成员计划在某个指定日期和时间进行角色激活。 他们还可以选择激活持续时间，最长可以达到管理员配置的最长持续时间。

本文适用于要在 Privileged Identity Management 中激活其特权访问组角色的合格成员。

## <a name="activate-a-role"></a>激活角色

在需要充当特权访问组角色时，可以通过在 Privileged Identity Management 中使用“我的角色”导航选项来请求激活。

1. 使用全局管理员或组所有者权限[登录到 Azure AD 门户](https://aad.portal.azure.com)。

1. 打开 [Privileged Identity Management](https://portal.azure.com/#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart)。

1. 选择“特权访问组(预览版)”，然后选择“激活角色”，以打开特权访问组的“我的角色”页  。

    ![PIM 中的特权访问角色页](./media/groups-activate-roles/groups-select-group.png)

1. 在“我的角色”页上，在要激活的合格分配的行中，选择“激活” 。

    ![合格角色分配行中的“激活”链接](./media/groups-activate-roles/groups-activate-link.png)

1. 如果角色需要多重身份验证，请选择“验证你的身份，然后继续”。 只需在每个会话中执行身份验证一次。

    ![在激活角色之前使用 MFA 验证身份](./media/groups-activate-roles/groups-my-roles-mfa.png)

1. 选择“验证我的身份”，并按照说明提供其他安全验证。

    ![用于提供安全验证（例如 PIN 码）的屏幕](./media/groups-activate-roles/groups-mfa-enter-code.png)

1. 根据需要指定自定义的激活开始时间。 成员或所有者只会在所选时间之后激活。

1. 在“原因”框中，输入该激活请求的原因。

    ![显示了持续时间和理由的激活页](./media/groups-activate-roles/groups-activate-page.png)

1. 选择“激活”  。

如果[角色需要审批](pim-resource-roles-approval-workflow.md)才能激活，在浏览器右上角会显示 Azure 通知，告知请求正在等待审批。

## <a name="view-the-status-of-your-requests"></a>查看请求的状态

可以查看等待激活的请求的状态。

1. 打开 Azure AD Privileged Identity Management。

1. 选择“我的请求”，以查看 Azure AD 角色和特权访问组角色请求的列表。

1. 如果需要，请向右滚动，以查看“请求状态”列。

## <a name="cancel-a-pending-request"></a>取消挂起的请求

如果不需要激活需要审批的角色，随时可以取消等待中的请求。

1. 打开 Azure AD Privileged Identity Management。

1. 选择“我的请求”。

1. 针对想要取消的角色，选择“取消”链接。

    选择“取消”会取消该请求。 若要再次激活该角色，必须提交新的激活请求。

## <a name="troubleshoot"></a>故障排除

### <a name="permissions-are-not-granted-after-activating-a-role"></a>激活角色后，权限未被授予

在 Privileged Identity Management 中激活角色时，激活可能不会立即传播到需要特权角色的所有门户。 有时，即使更改已传播，门户中的 Web 缓存也可能会导致更改不能立即生效。 如果激活已延迟，应当按照以下步骤操作。

1. 注销 Azure 门户，然后重新登录。
1. 在 Privileged Identity Management 中，验证是否已将你列为角色的成员。

## <a name="next-steps"></a>后续步骤

- [在 Privileged Identity Management 中延期或续订特权访问组角色](groups-renew-extend.md)
- [在 Privileged Identity Management 中分配特权访问组角色](groups-assign-member-owner.md)
