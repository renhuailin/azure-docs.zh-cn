---
title: 在 PIM 中完成对 Azure 资源和 Azure AD 角色的访问评审 - Azure AD | Microsoft Docs
description: 了解如何在 Azure Active Directory Privileged Identity Management 中完成对 Azure 资源和 Azure AD 角色的访问评审。
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 10/07/2021
ms.author: curtand
ms.reviewer: shaunliu
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7148284bef15b2e3f40a39652d0cb222ed5793c0
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2021
ms.locfileid: "129669979"
---
# <a name="complete-an-access-review-of-azure-resource-and-azure-ad-roles-in-pim"></a>在 PIM 中完成对 Azure 资源和 Azure AD 角色的访问评审

[访问评审开始](pim-create-azure-ad-roles-and-resource-roles-review.md)后，特权角色管理员可以评审特权访问。 Azure Active Directory (Azure AD) 中的 Privileged Identity Management (PIM) 会自动发送一封电子邮件，提示用户评审其访问。 如果用户未收到电子邮件，可以向他们发送[如何执行访问评审](pim-perform-azure-ad-roles-and-resource-roles-review.md)的相关说明。

创建评审后，请按照本文中的步骤完成评审并查看结果。

## <a name="complete-access-reviews"></a>完成访问评审

1. 登录到 [Azure 门户](https://portal.azure.com/)。 对于“Azure 资源”，导航到“Privileged Identity Management”，然后从仪表板中的“管理”下选择“Azure 资源”。 对于“Azure AD 角色”，从同一仪表板中选择“Azure AD 角色”。

2. 对于“Azure 资源”，在“Azure 资源”下选择你的资源，然后从仪表板中选择“访问评审”。 对于“Azure AD 角色”，直接前往仪表板上的“访问评审”。

3. 选择要管理的访问审阅。 下面是“Azure 资源”和“Azure AD 角色”的“访问评审”概览的示例屏幕截图。

    :::image type="content" source="media/pim-complete-azure-ad-roles-and-resource-roles-review/rbac-azure-ad-roles-home-list.png" alt-text="显示角色、所有者、开始日期、结束日期和状态的访问评审列表的屏幕截图。" lightbox="media/pim-complete-azure-ad-roles-and-resource-roles-review/rbac-azure-ad-roles-home-list.png":::

在详细信息页上，以下选项可用于管理“Azure 资源”和“Azure AD 角色”的评审：

![用于管理“Azure 资源”中的评审的选项 -“停止”、“重置”、“应用”、“删除”的屏幕截图。](media/pim-complete-azure-ad-roles-and-resource-roles-review/rbac-access-review-menu.png)

### <a name="stop-an-access-review"></a>停止访问评审

所有访问审阅都有结束日期，但可以使用“停止”按钮提前结束。 只有在评审实例处于活动状态时，才能选择“停止”按钮。 停止后，无法重新开始审阅。

### <a name="reset-an-access-review"></a>重置访问评审

当评审实例处于活动状态且审阅者至少做出一个决策时，你可以通过选择“重置”按钮来重置访问评审，删除对其所做的所有决策。 重置访问评审后，所有用户都会被重新标记为“未评审”。

### <a name="apply-an-access-review"></a>应用访问评审

完成访问评审后，无论是因为你已经达到结束日期还是已经手动停止，“应用”按钮都会删除被拒绝用户对该角色的访问。 如果在评审期间拒绝了用户的访问，在此步骤中将删除其角色分配。 如果在创建评审时配置了“自动应用”设置，则此按钮会始终被禁用，因为评审将自动应用，而不是手动应用。

### <a name="delete-an-access-review"></a>删除访问评审

如果不想要进一步了解审阅，请将其删除。 若要从 Privileged Identity Management 服务中删除访问评审，请选择“删除”按钮。

> [!IMPORTANT]
> 系统不会要求你确认此破坏性更改，因此请验证你是否要删除该评审。

## <a name="results"></a>结果

在“结果”页上，可以查看和下载审阅结果列表。

:::image type="content" source="media/pim-complete-azure-ad-roles-and-resource-roles-review/rbac-access-review-azure-ad-results.png" alt-text="“结果”页的屏幕截图，其中列出了用户、结果、原因、审阅者、应用者以及 Azure AD 角色的应用结果。" lightbox="media/pim-complete-azure-ad-roles-and-resource-roles-review/rbac-access-review-azure-ad-results.png":::

> [!Note]
> Azure AD 角色有一个可分配角色的组的概念，其中可将组分配给角色。 在这种情况下，该组会显示在评审中，而不是展开组的成员，审阅者将批准或拒绝整个组。

:::image type="content" source="media/pim-complete-azure-ad-roles-and-resource-roles-review/rbac-access-review-azure-resource-results.png" alt-text="“结果”页的屏幕截图，其中列出了用户、结果、原因、审阅者、应用者以及 Azure 资源角色的应用结果。" lightbox="media/pim-complete-azure-ad-roles-and-resource-roles-review/rbac-access-review-azure-resource-results.png":::

> [!Note]
>如果将组分配给 Azure 资源角色，Azure 资源角色的审阅者将看到嵌套组中用户的扩展列表。 如果审阅者拒绝嵌套组的成员，则该拒绝结果不会被成功应用，因为不会从嵌套组中删除用户。

## <a name="reviewers"></a>审阅者

在“审阅者”页上，可以查看现有访问审阅的审阅者以及为其添加审阅者。 还可以提醒审阅者在此处完成其评审。

> [!Note]
> 如果所选的审阅者类型是用户或组，则可以随时将更多用户或组添加为主要审阅者。 此外，还可以随时删除主要审阅者。 如果审阅者类型是管理者，则可以将用户或组添加为后备审阅者，以完成对没有管理者的用户的评审。 不能删除后备审阅者。

:::image type="content" source="media/pim-complete-azure-ad-roles-and-resource-roles-review/rbac-access-review-azure-resource-reviewers.png" alt-text="“审阅者”页的屏幕截图，其中列出了 Azure 资源角色的名称和用户主体名称。" lightbox="media/pim-complete-azure-ad-roles-and-resource-roles-review/rbac-access-review-azure-resource-reviewers.png":::

## <a name="next-steps"></a>后续步骤

- [在 PIM 中创建对 Azure 资源和 Azure AD 角色的访问评审](pim-create-azure-ad-roles-and-resource-roles-review.md)
- [在 PIM 中执行对 Azure 资源和 Azure AD 角色的访问评审](pim-perform-azure-ad-roles-and-resource-roles-review.md)
