---
title: 在 PIM 中创建对 Azure 资源角色的访问评审 - Azure AD | Microsoft Docs
description: 了解如何在 Azure AD Privileged Identity Management (PIM) 中创建 Azure 资源角色的访问评审。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: pim
ms.date: 04/27/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: f676ca94b6e1e6333a4cc4f862b60a02e71d24e9
ms.sourcegitcommit: 86ca8301fdd00ff300e87f04126b636bae62ca8a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2021
ms.locfileid: "122195207"
---
# <a name="create-an-access-review-of-azure-resource-roles-in-privileged-identity-management"></a>在 Privileged Identity Management 中创建对 Azure 资源角色的访问评审

员工对有特权的 Azure 资源角色的访问需求会随着时间推移而变化。 若要降低与过时角色分配相关的风险，应定期查看访问权限。 可以使用 Azure Active Directory (Azure AD) Privileged Identity Management (PIM) 为 Azure 资源角色的特权访问创建访问评审。 还可以配置自动进行的定期访问评审。 本文介绍如何创建一个或多个访问评审。

## <a name="prerequisite-license"></a>必备许可证

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)] 有关 PIM 许可证的详细信息，请参阅[使用 Privileged Identity Management 所要满足的许可证要求](subscription-requirements.md)。

> [!Note]
> 目前，你可以使用租户中处于活动状态的 Azure Active Directory Premium P2 版本将访问评审的范围限定为可访问 Azure AD 和 Azure 资源角色（预览版）的服务主体。 服务主体的许可模型将会最终确定，以便正式发布此功能。可能需要额外的许可证。

## <a name="prerequisite-role"></a>必备角色

 若要创建访问评审，必须具有资源的[所有者](../../role-based-access-control/built-in-roles.md#owner)或[用户访问管理员](../../role-based-access-control/built-in-roles.md#user-access-administrator) Azure 角色。

## <a name="open-access-reviews"></a>打开访问评审

1. 使用分配有某个必备角色的用户登录到 [Azure 门户](https://portal.azure.com/)。

1. 选择“标识监管”。

1. 在左侧菜单中，选择“Azure AD Privileged Identity Management”下的“Azure 资源” 。

1. 选择要管理的资源，例如订阅。

    ![Azure 资源 - 选择资源以创建访问评审](./media/pim-resource-roles-start-access-review/access-review-select-resource.png)

1. 在“管理”下，选择“访问评审”  。

    ![Azure 资源 - 访问评审列表，其中显示所有评审的状态](./media/pim-resource-roles-start-access-review/access-reviews.png)

1. 单击“新建”创建新的访问评审。

1. 命名访问评审。 可选择为评审提供说明。 名称和说明向评审者显示。

    ![创建访问评审 - 评审名称和说明](./media/pim-resource-roles-start-access-review/name-description.png)

1. 设置“开始日期”。 默认情况下，访问评审只进行一次，从创建的时候开始，在一个月内结束。 可以更改开始和结束日期，使访问评审在将来的时间开始，并持续所需的天数。

    ![开始日期、频率、持续时间、结束、次数和结束日期](./media/pim-resource-roles-start-access-review/start-end-dates.png)

1. 若要使访问评审定期进行，请将“频率”设置从“一次”更改为“每周”、“每月”、“每季度”、“每年”或“每半年”。 使用“持续时间”滑块或文本框来定义定期进行的一系列评审每次的运行天数（可供审阅者输入）。 例如，每月评审的最长持续时间可以设置为 27 天，以免评审时间重叠。

1. 使用“结束”设置指定如何结束定期访问评审系列。 系列的结束方式有三种：持续运行，无限期地开始评审；运行至指定日期；运行至已完成定义的评审数目。 你、其他用户管理员或其他全局管理员可以在创建后停止此系列，只需在“设置”中更改日期，然后此系列就会在该日期结束。

1. 在“用户”部分，选择评审的范围。 若要评审用户，请选择“用户”，或选择“(预览)服务主体”，以评审对 Azure 角色具有访问权限的计算机帐户。

    选择“用户”后，分配给角色的组的成员身份将扩展到该组的单个成员。 选择“服务主体”后，只会评审具有直接成员身份（不通过嵌套组）的服务主体。  

    ![要查看其角色成员身份的用户范围](./media/pim-resource-roles-start-access-review/users.png)


1. 在“评审角色成员身份”下，选择要评审的特权 Azure 角色。 

    > [!NOTE]
    > 选择多个角色会创建多个访问评审。 例如，选择五个角色会创建五个单独的访问评审。
    如果创建 Azure AD 角色的访问评审，下面显示了审阅成员身份列表的示例。

1. 在“分配类型”中，按将主体分配给角色的方式确定评审的范围。 选择“仅合格分配”来评审合格的分配（无论创建评审时激活状态如何），或者选择“仅活动分配”来评审活动分配 。 选择“所有活动且合格的分配”可评审所有分配而不考虑类型。

    ![分配类型的审阅者列表](./media/pim-resource-roles-start-access-review/assignment-type-select.png)

1. 在“审阅者”部分选择一人或多人来评审所有用户。 也可以选择让成员评审自己的访问权限。

    ![所选用户或成员（自我）的审阅者列表](./media/pim-resource-roles-start-access-review/reviewers.png)

    - **所选用户** - 使用此选项可指定一个特定用户来完成评审。 无论评审范围如何，均可使用此选项，并且选定的审阅者可以评审用户和服务主体。 
    - 成员(自我) - 使用此选项可让用户评审其自己的角色分配。 仅当评审的范围限定为“用户”时，此选项才可用。
    - **管理员** - 使用此选项可让用户的管理员查看其角色分配。 仅当评审的范围限定为“用户”时，此选项才可用。 选择“管理员”后，还可以选择指定一个后备审阅者。 当用户未在目录中指定任何管理员时，系统会要求后备审阅者评审用户。 

### <a name="upon-completion-settings"></a>完成后的设置

1. 若要指定评审完成后发生的情况，请展开“完成后的设置”部分。

    ![完成后设置自动应用，不响应评审](./media/pim-resource-roles-start-access-review/upon-completion-settings.png)

1. 若要自动删除被拒绝用户的访问权限，请将“将结果自动应用到资源”设置为“启用”。  若要在评审完成后手动应用结果，请将开关设置为“禁用”。 

1. 使用“如果审阅者未答复”列表指定对于审阅者在评审期限内未评审的用户要执行的操作。  此设置不影响审阅者已手动评审的用户。 如果最终的审阅者决策是“拒绝”，则会删除用户的访问权限。

    - **不更改** - 将用户访问权限保持不变
    - **删除访问权限** - 删除用户的访问权限
    - **批准访问权限** - 批准用户的访问权限
    - **采用建议** - 根据系统的建议拒绝或批准用户的持续访问权限

1. 可以向其他需要接收评审完成情况更新的用户或组发送通知（预览版）。 除评审创建者之外的利益干系人使用此功能可了解最新的评审进度。 若要使用此功能，请选择“选择用户或组”并在要接收完成度状态时添加其他用户或组。

    ![完成后的设置 - 添加要接收通知的其他用户](./media/pim-resource-roles-start-access-review/upon-completion-settings-additional-receivers.png) 

### <a name="advanced-settings"></a>高级设置

1. 若要指定其他设置，请展开“高级设置”部分。

    ![显示建议的高级设置，需要批准原因、邮件通知和提醒](./media/pim-resource-roles-start-access-review/advanced-settings.png)

1. 将“显示建议”设置为“启用”，以基于用户的访问权限信息向评审者显示系统建议。 

1. 将“需要提供审批原因”设置为“启用”，以要求审阅者提供批准原因。  

1. 将“邮件通知”设置为“启用”，以便在访问评审开始时让 Azure AD 向评审者发送电子邮件通知，并在评审完成时向管理员发送电子邮件通知。  

1. 将“提醒”设置为“启用”，让 Azure AD 向所有审阅者发送访问评审正在进行的提醒 。 审阅者将在审核过程中途收到提醒，无论他们当时是否已完成评审。
1. 发送给审阅者的电子邮件的内容是根据审阅详细信息（如审阅名称、资源名称、截止日期等）自动生成的。如果你需要一种方式来传达其他信息（例如其他说明或联系人信息），则可在审阅者电子邮件的“其他内容”中指定这些详细信息，这些信息将包含在发送给分配的审阅者的邀请和提醒电子邮件中。 下面突出显示的部分是将要显示此信息的位置。

    ![突出显示发送给审阅者的电子邮件内容](./media/pim-resource-roles-start-access-review/email-info.png)

## <a name="start-the-access-review"></a>启动访问评审

指定访问评审的设置后，单击“启动”。 访问评审将显示在列表中，并带有其状态指示器。

![访问评审列表，其中显示已启动评审的状态](./media/pim-resource-roles-start-access-review/access-reviews-list.png)

默认情况下，在评审开始后不久，Azure AD 会向评审者发送一封电子邮件。 如果选择不让 Azure AD 发送电子邮件，请务必通知评审者有一个访问评审任务等待他们完成。 可以向他们显示有关如何[评审 Azure 资源角色访问权限](pim-resource-roles-perform-access-review.md)的说明。

## <a name="manage-the-access-review"></a>管理访问审阅

可以在访问评审的“概述”  页上跟踪评审者完成评审的进度。 在[评审完成](pim-resource-roles-complete-access-review.md)之前，目录中的任何访问权限都不会更改。

![显示评审详细信息的访问评审概述页](./media/pim-resource-roles-start-access-review/access-review-overview.png)

如果这是一次性评审，则请在访问评审期限结束后或管理员停止了访问评审后，按照[完成 Azure 资源角色的访问评审](pim-resource-roles-complete-access-review.md)中的步骤查看并应用结果。  

若要管理一系列访问评审，请导航到访问评审，此时会在“计划的评审”中找到即将进行的评审，然后即可相应地编辑结束日期或添加/删除评审者。

根据你在“完成后操作”  设置中的选择，自动应用会在评审的结束日期之后执行，或在你手动停止评审后执行。 评审状态将从“已完成”变为各种中间状态（例如“正在应用”），并最终变为“已应用”状态    。 几分钟后，应当会看到被拒绝的用户（如果有）被从角色中删除。

## <a name="next-steps"></a>后续步骤

- [评审对 Azure 资源角色的访问权限](pim-resource-roles-perform-access-review.md)
- [完成 Azure 资源角色的访问评审](pim-resource-roles-complete-access-review.md)
- [创建 Azure AD 角色的访问评审](pim-how-to-start-security-review.md)
