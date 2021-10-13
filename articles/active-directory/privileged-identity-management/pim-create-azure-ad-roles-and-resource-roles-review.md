---
title: 在 PIM 中为 Azure 资源和 Azure AD 角色创建访问评审 - Azure AD | Microsoft Docs
description: 了解如何在 Azure AD Privileged Identity Management (PIM) 中为 Azure 资源和 Azure AD 角色创建访问评审。
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
editor: ''
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.subservice: pim
ms.date: 10/07/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c44c27c0d99c67c4102167f924a1dcb6f87e871
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2021
ms.locfileid: "129669694"
---
# <a name="create-an-access-review-of-azure-resource-and-azure-ad-roles-in-pim"></a>在 PIM 中创建对 Azure 资源和 Azure AD 角色的访问评审

员工对 Azure 资源和 Azure AD 角色的特权访问需求会随着时间推移而变化。 若要降低与过时角色分配相关的风险，应定期查看访问权限。 可使用 Azure Active Directory (Azure AD) Privileged Identity Management (PIM) 为 Azure 资源和 Azure AD 角色的特权访问创建访问评审。 还可以配置自动进行的定期访问评审。 本文介绍如何创建一个或多个访问评审。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)] 有关 PIM 许可证的详细信息，请参阅[使用 Privileged Identity Management 所要满足的许可证要求](subscription-requirements.md)。

 若要为 Azure 资源创建访问评审，你必须分配有 Azure 资源的[所有者](../../role-based-access-control/built-in-roles.md#owner)或[用户访问管理员](../../role-based-access-control/built-in-roles.md#user-access-administrator)角色。 若要为 Azure AD 角色创建访问评审，你必须分配有[全局管理员](../roles/permissions-reference.md#global-administrator)或[特权角色管理员](../roles/permissions-reference.md#privileged-role-administrator)角色。

## <a name="create-access-reviews"></a>创建访问评审

1. 以分配有某个必备角色的用户身份登录到 [Azure 门户](https://portal.azure.com/)。

2. 选择 Identity Governance。
 
3. 对于“Azure AD 角色”，请选择“Privileged Identity Management”下的“Azure AD 角色”  。 对于 “Azure 资源”，请选择“Privileged Identity Management”下的“Azure 资源”  。

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/identity-governance.png" alt-text="选择 Azure 门户中的“Identity Governance”的屏幕截图。" lightbox="./media/pim-create-azure-ad-roles-and-resource-roles-review/identity-governance.png"::: 
 
4. 对于“Azure AD 角色”，请在“管理”下再次选择“Azure AD 角色”  。 对于“Azure 资源”，请选择想要管理的资源，例如订阅。


5. 在“管理”下，选择“访问评审”，然后选择“新建”来新建访问评审 。

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/access-reviews.png" alt-text="Azure AD 角色 - 访问评审列表，其中显示了所有评审状态的屏幕截图。":::
 
6. 命名访问评审。 可选择为评审提供说明。 名称和说明向评审者显示。

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/name-description.png" alt-text="创建访问评审 - 评审名称和说明的屏幕截图。":::

7. 设置“开始日期”。 默认情况下，访问评审只进行一次，从创建的时候开始，在一个月内结束。 可以更改开始和结束日期，使访问评审在将来的时间开始，并持续所需的天数。

   :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/start-end-dates.png" alt-text="开始日期、频率、持续时间、结束、次数和结束日期的屏幕截图。":::

8. 若要使访问评审定期进行，请将“频率”设置从“一次”更改为“每周”、“每月”、“每季度”、“每年”或“每半年”。 使用“持续时间”滑块或文本框来定义定期进行的一系列评审每次的运行天数（可供审阅者输入）。 例如，每月评审的最长持续时间可以设置为 27 天，以免评审时间重叠。

9. 使用“结束”设置指定如何结束定期访问评审系列。 系列的结束方式有三种：持续运行，无限期地开始评审；运行至指定日期；运行至已完成定义的评审数目。 你或其他可以管理评审的管理员可以在创建此系列后将其停止，只需在“设置”中更改日期，然后此系列就会在该日期结束。


10. 在“用户范围”部分，选择评审的范围。 对于“Azure AD 角色”，第一个范围选项为“用户和组”。 直接分配的用户和[可分配角色的组](../roles/groups-concept.md)将包含在此选择中。 对于“Azure 资源角色”，第一个范围将是“用户”。 选择此项，将展开分配给 Azure 资源角色的组以显示评审中的可传递用户分配。 还可选择“服务主体”，以审阅可直接访问 Azure 资源或 Azure AD 角色的计算机帐户。

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/users.png" alt-text="评审角色成员身份的“用户”范围的屏幕截图。":::

11. 在“评审角色成员身份”下，选择要评审的特权 Azure 资源或 Azure AD 角色。

    > [!NOTE]
    > 选择多个角色会创建多个访问评审。 例如，选择五个角色会创建五个单独的访问评审。

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/review-role-membership.png" alt-text="评审角色成员身份的屏幕截图。":::

12. 在“分配类型”中，按将主体分配给角色的方式确定评审的范围。 选择“仅合格分配”来评审合格的分配（无论创建评审时激活状态如何），或者选择“仅活动分配”来评审活动分配 。 选择“所有活动且合格的分配”可评审所有分配而不考虑类型。

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/assignment-type-select.png" alt-text="分配类型的审阅者列表屏幕截图。":::

13. 在“审阅者”部分选择一人或多人来评审所有用户。 也可以选择让成员评审自己的访问权限。

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/reviewers.png" alt-text="所选用户或成员（自我）的审阅者列表":::

    - **所选用户** - 使用此选项可指定一个特定用户来完成评审。 无论评审范围如何，均可使用此选项，并且选定的审阅者可以评审用户、组和服务主体。
    - 成员(自我) - 使用此选项可让用户评审其自己的角色分配。 仅当评审的范围限定为“用户和组”或“用户”时，此选项才可用 。 对于 Azure AD 角色，选择此选项后，将不对可分配角色的组进行评审。 
    - **管理员** - 使用此选项可让用户的管理员查看其角色分配。 仅当评审的范围限定为“用户和组”或“用户”时，此选项才可用 。 选择“管理员”后，还可以选择指定一个后备审阅者。 当用户未在目录中指定任何管理员时，系统会要求后备审阅者评审用户。 对于 Azure AD 角色，如果选择了可分配角色的组，后备审阅者将对其进行审阅。 

### <a name="upon-completion-settings"></a>完成后的设置

1. 若要指定评审完成后发生的情况，请展开“完成后的设置”部分。

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/upon-completion-settings.png" alt-text="屏幕截图显示“完成后的设置”下的自动应用，应评审而非响应。":::

2. 若要自动删除被拒绝用户的访问权限，请将“将结果自动应用到资源”设置为“启用”。  若要在评审完成后手动应用结果，请将开关设置为“禁用”。 

3. 使用“如果审阅者未答复”列表指定对于审阅者在评审期限内未评审的用户要执行的操作。 此设置不会影响审阅者评审的用户。

    - **不更改** - 将用户访问权限保持不变
    - **删除访问权限** - 删除用户的访问权限
    - **批准访问权限** - 批准用户的访问权限
    - **采用建议** - 根据系统的建议拒绝或批准用户的持续访问权限
    
4. 使用“将应用于被拒绝的来宾用户的操作”列表，指定来宾用户被拒绝时发生的情况。 目前，对于 Azure AD 和 Azure 资源角色评审，此设置不可编辑；如果被拒绝，来宾用户与所有用户一样，将始终无法访问资源。

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/action-to-apply-on-denied-guest-users.png" alt-text="完成后的设置 - 应用于被拒绝的来宾用户的操作的屏幕截图。":::

5. 可以向其他需要接收评审完成情况更新的用户或组发送通知。 除评审创建者之外的利益干系人使用此功能可了解最新的评审进度。 若要使用此功能，请选择“选择用户或组”并在要接收完成度状态时添加其他用户或组。

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/upon-completion-settings-additional-receivers.png" alt-text="完成后的设置 - 添加要接收通知的其他用户的屏幕截图。":::

### <a name="advanced-settings"></a>高级设置

1. 若要指定其他设置，请展开“高级设置”部分。

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/advanced-settings.png" alt-text="显示建议的高级设置，需要批准原因、邮件通知和提醒的屏幕截图。":::

1. 将“显示建议”设置为“启用”，以基于用户的访问权限信息向评审者显示系统建议。 

1. 将“需要提供审批原因”设置为“启用”，以要求审阅者提供批准原因。  

1. 将“邮件通知”设置为“启用”，以便在访问评审开始时让 Azure AD 向评审者发送电子邮件通知，并在评审完成时向管理员发送电子邮件通知。  

1. 将“提醒”设置为“启用”，让 Azure AD 向尚未完成其审阅的审阅者发送访问评审正在进行的提醒。  
1. 发送给审阅者的电子邮件的内容是根据审阅详细信息（如审阅名称、资源名称、截止日期等）自动生成的。如果你需要一种方式来传达其他信息（例如其他说明或联系人信息），则可在审阅者电子邮件的“其他内容”中指定这些详细信息，这些信息将包含在发送给分配的审阅者的邀请和提醒电子邮件中。 下面突出显示的部分是将要显示此信息的位置。

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/email-info.png" alt-text="突出显示发送给审阅者的电子邮件内容"::: 

## <a name="manage-the-access-review"></a>管理访问审阅

可以在访问评审的“概述”  页上跟踪评审者完成评审的进度。 在评审完成之前，目录中的任何访问权限都不会更改。 下面的屏幕截图显示了 Azure 资源和 Azure AD 角色访问评审的概述页面 。

:::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/access-review-overview.png" alt-text="访问评审概述页面的屏幕截图，其中显示了 Azure AD 角色的访问评审的详细信息。" lightbox="./media/pim-create-azure-ad-roles-and-resource-roles-review/access-review-overview.png":::

如果这是一次性评审，则请在访问评审期限结束后或管理员停止访问评审后，按照[完成对 Azure 资源和 Azure AD 角色的访问评审](pim-complete-azure-ad-roles-and-resource-roles-review.md)中的步骤查看并应用结果。  

若要管理一系列访问评审，请导航到访问评审，此时会在“计划的评审”中找到即将进行的评审，然后即可相应地编辑结束日期或添加/删除评审者。

根据你在“完成后操作”  设置中的选择，自动应用会在评审的结束日期之后执行，或在你手动停止评审后执行。 评审状态将从“已完成”变为各种中间状态（例如“正在应用”），并最终变为“已应用”状态    。 几分钟后，应当会看到被拒绝的用户（如果有）被从角色中删除。

> [!IMPORTANT]
> 如果将组分配给 Azure 资源角色，Azure 资源角色的审阅者将看到通过嵌套组分配访问权限的间接用户的展开列表。 如果审阅者拒绝嵌套组的成员，则该拒绝结果不会成功应用于角色，因为不会从嵌套组中删除用户。 对于 Azure AD 角色，[可分配角色的组](../roles/groups-concept.md)将显示在评审中，而不是展开组的成员，审阅者将批准或拒绝对整个组的访问。

## <a name="update-the-access-review"></a>更新访问评审

开始一个或多个访问评审后，建议修改或更新现有访问评审的设置。 下面是一些需要考虑的常见方案：

- **添加和删除审阅者** - 更新访问评审时，可选择添加除主审阅者之外的后备审阅者。 更新访问评审时，可能会删除主审阅者。 但是，不能通过设计来删除后备审阅者。

    > [!Note]
    > 只能在审阅者类型为管理员时才能添加后备审阅者。 当审阅者类型为用户时，可以添加主审阅者。

- **提醒审阅者** - 更新访问评审时，可选择在“高级设置”下启用提醒选项。 启用后，用户将在评审过程中途收到电子邮件通知，无论他们当时是否已完成评审。 

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/reminder-setting.png" alt-text="访问评审设置下的提醒选项的屏幕截图。":::

- **更新设置** - 如果访问评审是重复的，则可在“当前”和“系列”下单独进行设置。 更新“当前”下的设置只会将更改应用于当前访问评审，而更新“系列”下的设置将更新所有未来重复的设置。

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/current-v-series-setting.png" alt-text="访问评审下的设置页的屏幕截图。" lightbox="./media/pim-create-azure-ad-roles-and-resource-roles-review/current-v-series-setting.png":::
    
## <a name="next-steps"></a>后续步骤

- [在 PIM 中执行对 Azure 资源和 Azure AD 角色的访问评审](pim-perform-azure-ad-roles-and-resource-roles-review.md)
- [在 PIM 中完成对 Azure 资源和 Azure AD 角色的访问评审](pim-complete-azure-ad-roles-and-resource-roles-review.md)
