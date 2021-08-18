---
title: 配置授予数据访问权限所需的安全性 - Azure 时序见解 | Microsoft Docs
description: 了解如何在 Azure 时序见解环境中配置安全性和权限并管理数据访问策略。
ms.service: time-series-insights
services: time-series-insights
author: tedvilutis
ms.author: tvilutis
manager: dviso
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 12/02/2020
ms.custom: seodec18
ms.openlocfilehash: ebbb3c02263d860822482e2e19293d9a032274ef
ms.sourcegitcommit: 8942cdce0108372d6fc5819c71f7f3cf2f02dc60
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2021
ms.locfileid: "113135354"
---
# <a name="grant-data-access-to-an-environment"></a>授予对环境的数据访问权限

本文讨论了两种类型的 Azure 时序见解访问策略。

> [!Warning]
> 访问策略授予 Azure AD 用户和/或组数据平面对时序见解环境的访问权限。
> Azure Active Directory 与租户绑定。 因此，如果决定在租户之间移动订阅，请确保遵循[以下部分](#procedure-for-when-the-subscription-is-moved-across-tenants)中介绍的过程。

## <a name="sign-in-to-azure-time-series-insights"></a>登录到 Azure 时序见解

1. 登录到 [Azure 门户](https://portal.azure.com/)。
1. 通过在“搜索”框中输入 `Time Series Insights environments` 找到 Azure 时序见解环境。 在搜索结果中选择 `Time Series Insights environments`。
1. 从列表中选择 Azure 时序见解环境。

## <a name="grant-data-access"></a>授予数据访问权限

遵循以下步骤向用户主体授予数据访问权限。

1. 选择“数据访问策略”，然后选择“+ 添加”   。

    [![选择并添加数据访问策略](media/data-access/data-access-select-add-button.png)](media/data-access/data-access-select-add-button.png#lightbox)

1. 选择“选择用户”。  搜索用户名称或电子邮件地址，查找要添加的用户。 选择“选择”以确认选择  。

    [![选择要添加的用户](media/data-access/data-access-select-user-to-confirm.png)](media/data-access/data-access-select-user-to-confirm.png#lightbox)

1. 选择“选择角色”。  为用户选择相应的访问角色：

    * 若要允许用户更改引用数据，以及与环境的其他用户共享保存的查询和透视，请选择“参与者”  。

    * 否则请选择“读者”，允许用户查询环境中的数据，以及在环境中保存个人的非共享查询  。

   选择“确定”，确认角色选择  。

    [![确认所选角色](media/data-access/data-access-select-a-role.png)](media/data-access/data-access-select-a-role.png#lightbox)

1. 在“选择用户角色”页中，选择“确定”   。

    [![在“选择用户角色”页中，选择“确定”](media/data-access/data-access-confirm-user-and-role.png)](media/data-access/data-access-confirm-user-and-role.png#lightbox)

1. 确认“数据访问策略”页列出了用户和每个用户的角色  。

    [![验证用户和角色是否正确](media/data-access/data-access-verify-and-confirm-assignments.png)](media/data-access/data-access-verify-and-confirm-assignments.png#lightbox)

## <a name="provide-guest-access-from-another-azure-ad-tenant"></a>提供从另一个 Azure AD 租户进行来宾访问的权限

`Guest` 角色不是管理角色。 它是一个术语，是指从一个租户被邀请到另一个租户的帐户。 在将来宾帐户邀请到租户目录后，它就可以像任何其他帐户一样拥有相同的访问控制。 可以使用“访问控制(IAM)”边栏选项卡授予对 Azure 时序见解环境的管理访问权限。 也可以通过“数据访问策略”边栏选项卡授予对环境中数据的访问权限。 若要详细了解 Azure Active Directory (Azure AD) 租户来宾访问权限，请阅读[在 Azure 门户中添加 Azure Active Directory B2B 协作用户](../active-directory/external-identities/add-users-administrator.md)。

请按照以下步骤将来宾对 Azure 时序见解环境的访问权限授予另一个租户的 Azure AD 用户。

1. 转到 Azure 门户，单击“Azure Active Directory”，在“概述”选项卡上向下滚动，然后选择“来宾用户”  。

    [![选择“数据访问策略”，然后选择“+ 邀请”](media/data-access/data-access-invite-another-aad-tenant.png)](media/data-access/data-access-invite-another-aad-tenant.png#lightbox)

1. 输入要邀请的用户的电子邮件地址。 此电子邮件地址必须与 Azure AD 关联。 你可以根据情况在邀请中包括个人消息。

    [![输入查找所选用户所需的电子邮件地址](media/data-access/data-access-invite-guest-by-email.png)](media/data-access/data-access-invite-guest-by-email.png#lightbox)

1. 查找屏幕上显示的确认气泡。 也可以单击“通知”来确认已经添加了来宾用户。

    [![查找要显示的确认气泡](media/data-access/data-access-confirmation-bubble.png)](media/data-access/data-access-confirmation-bubble.png#lightbox)

1. 返回到时序见解环境，以添加新创建的来宾用户。 按照“授予数据访问权限”下的说明，单击“数据访问策略” 。 选择用户。 搜索已邀请的来宾用户的电子邮件地址，找到要添加的用户。 然后单击“选择”以确认选择  。

    [![选择用户并确认所做的选择](media/data-access/data-access-select-invited-person-confirmation.png)](media/data-access/data-access-select-invited-person-confirmation.png#lightbox)

1. 选择“选择角色”。  为来宾用户选择相应的访问角色：

    * 若要允许用户更改引用数据，以及与环境的其他用户共享保存的查询和透视，请选择“参与者”  。

    * 否则请选择“读者”，允许用户查询环境中的数据，以及在环境中保存个人的非共享查询  。

   选择“确定”，确认角色选择  。

    [![确认角色选择](media/data-access/data-access-select-ok-and-confirm.png)](media/data-access/data-access-select-ok-and-confirm.png#lightbox)

1. 在“选择用户角色”页中，选择“确定”   。

1. 确认“数据访问策略”  页列出了来宾用户和每个来宾用户的角色。

    [![验证是否正确分配了用户和角色](media/data-access/data-access-confirm-invited-users-and-roles.png)](media/data-access/data-access-confirm-invited-users-and-roles.png#lightbox)

1. 现在，来宾用户会在上面指定的电子邮件地址收到邀请电子邮件。 来宾用户会选择“开始”来确认接受邀请并连接到 Azure Cloud。 

    [![来宾选择“开始”即可接受邀请](media/data-access/data-access-email-invitation.png)](media/data-access/data-access-email-invitation.png#lightbox)

1. 选择“开始”后，来宾用户会看到一个与管理员的组织关联的权限框。  通过选择“接受”授予权限后，来宾用户就会登录。 

    [![来宾查看权限并接受](media/data-access/data-access-grant-permission-sign-in.png)](media/data-access/data-access-grant-permission-sign-in.png#lightbox)

1. 管理员与其来宾[共享环境 URL](time-series-insights-parameterized-urls.md)。

1. 当来宾用户登录到你用来邀请他们的电子邮件地址并接受邀请后，系统就会将其定向到 Azure 门户。

1. 来宾现在可以使用管理员提供的环境 URL 访问共享环境。 来宾将该 URL 输入其 Web 浏览器即可立刻进行访问。

1. 在时序资源管理器的右上角选择其配置文件图标后，将向来宾用户显示管理员的租户。

    [![在 insights.azure.com 上选择头像](media/data-access/data-access-select-tenant-and-instance.png)](media/data-access/data-access-select-tenant-and-instance.png#lightbox)

    来宾用户在选择管理员的租户以后，即可选择共享的 Azure 时序见解环境。

    现在，他们拥有了与你在 **步骤 5** 中提供的角色相关联的所有功能。

    [![来宾用户从下拉列表中选择你的 Azure 租户](media/data-access/data-access-all-capabilities.png)](media/data-access/data-access-all-capabilities.png#lightbox)

## <a name="procedure-for-when-the-subscription-is-moved-across-tenants"></a>跨租户移动订阅的过程

时序见解数据访问策略由 Azure Active Directory 支持，绑定到订阅所属的 Azure 租户。

向其授予数据访问策略的 Azure AD 对象以及时序见解环境本身应该位于同一个租户下。 如果没有位于同一个租户下，则这些对象将无权访问环境。

如果计划将环境所属的订阅移动至其他租户，请务必更新数据访问策略以反映新租户下的 Azure AD 对象。

为了顺利完成该过程，请执行以下步骤。

### <a name="before-moving-a-subscription-to-another-tenant"></a>在将订阅移动到另一个租户之前

- 请确保在环境仍处于源租户中时，保留环境中的当前数据访问策略分配的列表。
- 请确定希望其在订阅迁移到目标租户中的 Active Directory 后仍有权访问环境的用户、组或应用。
- 请确保自己在订阅移动后至少拥有订阅的参与者访问权限，或者能与某位具有相应权限的人合作，以便能在目标租户的环境中重新应用数据访问策略。

### <a name="after-moving-a-subscription-to-another-tenant"></a>在将订阅移动到另一个租户之后

拥有对目标租户中订阅的参与者访问权限，可以

- 删除随环境一起迁移的所有数据访问策略，因为它们属于源租户。
- 使用上述步骤向环境重新授予访问策略，现在指向目标租户中的 Azure AD 对象。

## <a name="next-steps"></a>后续步骤

* 阅读[身份验证和授权](time-series-insights-authentication-and-authorization.md)以获取 Azure Active Directory 应用注册步骤。

* 在 [Azure 时序见解资源管理器](./concepts-ux-panels.md)中查看环境。
