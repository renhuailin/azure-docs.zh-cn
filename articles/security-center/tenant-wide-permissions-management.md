---
title: 在 Azure 安全中心授予和请求租户范围的权限
description: 了解如何在 Azure 安全中心管理租户范围的权限
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 03/11/2021
ms.openlocfilehash: 99e7e09ac1b73dfb24cea14fe1b407f491e4ade5
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/16/2021
ms.locfileid: "112236888"
---
# <a name="grant-and-request-tenant-wide-visibility"></a>授予和请求租户范围的可见性

具有 Azure Active Directory (AD)“全局管理员”角色的用户可能要承担租户范围内的责任，但缺乏 Azure 权限，无法在 Azure 安全中心查看组织范围内的信息。 需要进行特权提升，因为 Azure AD 角色分配不会授予对 Azure 资源的访问权限。 

## <a name="grant-tenant-wide-permissions-to-yourself"></a>向你自己授予租户范围的权限

为你自己分配租户级权限：

1. 如果你的组织使用 [Azure AD Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-configure.md) 或任何其他 PIM 工具管理资源访问权限，则对于执行以下过程的用户，全局管理员角色必须处于活动状态。

1. 以在租户的根管理组上不具有分配权限的全局管理员用户身份，打开安全中心的概述页面，并在横幅中选择“租户范围可见性”链接 。 

    :::image type="content" source="media/security-center-management-groups/enable-tenant-level-permissions-banner.png" alt-text="在 Azure 安全中心启用租户级权限。":::

1. 选择要分配的 Azure 新角色。 

    :::image type="content" source="media/security-center-management-groups/enable-tenant-level-permissions-form.png" alt-text="用于定义要分配给用户的租户级权限的窗体。":::

    > [!TIP]
    > 通常情况下，若要在根级别应用策略，需要安全管理员角色，若要提供租户级可见性，安全读取者角色就足够了。 有关这些角色授予的权限的详细信息，请参阅[安全管理员内置角色说明](../role-based-access-control/built-in-roles.md#security-admin)或[安全读取者内置角色说明](../role-based-access-control/built-in-roles.md#security-reader)。
    >
    > 要了解特定于安全中心的这些角色之间的差异，请参阅[角色和允许的操作](security-center-permissions.md#roles-and-allowed-actions)中的表。

    组织范围的视图是通过在租户的根管理组级别授予角色实现的。  

1. 注销 Azure 门户，然后重新登录。

1. 获得提升的访问权限后，立即打开或刷新 Azure 安全中心，验证能否查看 Azure AD 租户下的所有订阅。 

上述简单过程会自动为你执行多个操作：

1. 暂时提升用户的权限。
1. 使用新权限，将用户分配到根管理组中所需的 Azure RBAC 角色。
1. 删除提升的权限。

有关 Azure AD 权限提升过程的更多详细信息，请参阅[提升访问权限以管理所有 Azure 订阅和管理组](../role-based-access-control/elevate-access-global-admin.md)。


## <a name="request-tenant-wide-permissions-when-yours-are-insufficient"></a>当你的权限不足时，请申请租户范围的权限。

如果你登录到安全中心并看到一个横幅，指出你的视图具有局限性，则可以通过单击来向组织的全局管理员发送请求。 在请求中，你可以指出你希望分配到的角色，全局管理员将决定要授予哪个角色。 

全局管理员将决定是接受还是拒绝这些请求。 

> [!IMPORTANT]
> 每七天只能提交一个请求。

若要向全局管理员请求提升的权限，请执行以下操作：

1. 在 Azure 门户中，打开 Azure 安全中心。

1. 如果你看到横幅“你看到的只是有限的信息”。 选择它。

    :::image type="content" source="media/security-center-management-groups/request-tenant-permissions.png" alt-text="横幅通知用户他们可以请求租户范围内的权限。":::

1. 在详细的请求表单中，选择所需的角色和你需要这些权限的理由。

    :::image type="content" source="media/security-center-management-groups/request-tenant-permissions-details.png" alt-text="用于向 Azure 全局管理员请求租户范围权限的详细信息页。":::

1. 选择“请求访问权限”。

    将向全局管理员发送一封电子邮件。 此电子邮件包含指向安全中心的链接，而全局管理员可在该安全中心批准或拒绝该请求。

    :::image type="content" source="media/security-center-management-groups/request-tenant-permissions-email.png" alt-text="向全局管理员发送电子邮件来请求新权限。":::

    全局管理员选择“检查请求”并完成该过程之后，会通过电子邮件将决定发送给发出请求的用户。 

## <a name="next-steps"></a>后续步骤

在以下相关页中详细了解安全中心权限：

- [Azure 安全中心中的权限](security-center-permissions.md)