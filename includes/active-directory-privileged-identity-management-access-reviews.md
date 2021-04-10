---
title: include 文件
description: include 文件
services: active-directory
author: ajburnle
ms.service: active-directory
ms.topic: include
ms.date: 12/07/2020
ms.author: ajburnle
ms.custom: include file
ms.openlocfilehash: 9c91eb6cfa18c5302a83347f671e4552befcf3e2
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102623575"
---
## <a name="create-one-or-more-access-reviews"></a>创建一个或多个访问评审

1. 单击“新建”创建新的访问评审。

1. 命名访问评审。 可选择为评审提供说明。 名称和说明向评审者显示。

    ![创建访问评审 - 评审名称和说明](./media/active-directory-privileged-identity-management-access-reviews/name-description.png)

1. 设置“开始日期”。 默认情况下，访问评审只进行一次，从创建的时候开始，在一个月内结束。 可以更改开始和结束日期，使访问评审在将来的时间开始，并持续所需的天数。

    ![开始日期、频率、持续时间、结束、次数和结束日期](./media/active-directory-privileged-identity-management-access-reviews/start-end-dates.png)

1. 若要使访问评审定期进行，请将“频率”设置从“一次”更改为“每周”、“每月”、“每季度”、“每年”或“每半年”。 使用“持续时间”滑块或文本框来定义定期进行的一系列评审每次的运行天数（可供审阅者输入）。 例如，每月评审的最长持续时间可以设置为 27 天，以免评审时间重叠。

1. 使用“结束”设置指定如何结束定期访问评审系列。 系列的结束方式有三种：持续运行，无限期地开始评审；运行至指定日期；运行至已完成定义的评审数目。 你、其他用户管理员或其他全局管理员可以在创建后停止此系列，只需在“设置”中更改日期，然后此系列就会在该日期结束。

1. 在“用户”部分中，选择要查看其成员身份的一个或多个角色。

    ![要查看其角色成员身份的用户范围](./media/active-directory-privileged-identity-management-access-reviews/users.png)

    > [!NOTE]
    > - 此处选择的角色包括[永久和符合条件的角色](../articles/active-directory/privileged-identity-management/pim-how-to-add-role-to-user.md)。
    > - 选择多个角色会创建多个访问评审。 例如，选择五个角色会创建五个单独的访问评审。
    > - 对于分配了组的角色，将在访问评审中审查与所审阅角色关联的每个组的访问权限。

    如果创建 Azure AD 角色的访问评审，下面显示了审阅成员身份列表的示例。

    ![列出可以选择的 Azure AD 角色的审阅成员身份窗格](./media/active-directory-privileged-identity-management-access-reviews/review-membership.png)

    如果创建 Azure 资源角色的访问评审，下图显示了审阅成员身份列表的示例。

    ![列出可以选择的 Azure 资源角色的审阅成员身份窗格](./media/active-directory-privileged-identity-management-access-reviews/review-membership-azure-resource-roles.png)

1. 在“审阅者”部分选择一人或多人来评审所有用户。 也可以选择让成员评审自己的访问权限。

    ![所选用户或成员（自我）的审阅者列表](./media/active-directory-privileged-identity-management-access-reviews/reviewers.png)

    - 所选用户 - 如果不知道谁需要访问，请使用此选项。 使用此选项，可以将审阅分配给资源所有者或组管理员完成。
    - 成员(自我) - 使用此选项可让用户评审其自己的角色分配。 如果选择了此选项，则分配给该角色的组不会成为评审的一部分。
    - 管理员 - 使用此选项可让用户的管理员查看其角色分配。 选择“管理员”后，还可以选择指定一个后备审阅者。 当用户未在目录中指定任何管理员时，系统会要求后备审阅者评审用户。 如果选择了一个组，则后备审阅者将审阅分配给该角色的组。 

### <a name="upon-completion-settings"></a>完成后的设置

1. 若要指定评审完成后发生的情况，请展开“完成后的设置”部分。

    ![完成后设置自动应用，不响应评审](./media/active-directory-privileged-identity-management-access-reviews/upon-completion-settings.png)

1. 若要自动删除被拒绝用户的访问权限，请将“将结果自动应用到资源”设置为“启用”。  若要在评审完成后手动应用结果，请将开关设置为“禁用”。 

1. 使用“如果审阅者未答复”列表指定对于审阅者在评审期限内未评审的用户要执行的操作。  此设置不影响审阅者已手动评审的用户。 如果最终的审阅者决策是“拒绝”，则会删除用户的访问权限。

    - **不更改** - 将用户访问权限保持不变
    - **删除访问权限** - 删除用户的访问权限
    - **批准访问权限** - 批准用户的访问权限
    - **采用建议** - 根据系统的建议拒绝或批准用户的持续访问权限

### <a name="advanced-settings"></a>高级设置

1. 若要指定其他设置，请展开“高级设置”部分。

    ![显示建议的高级设置，需要批准原因、邮件通知和提醒](./media/active-directory-privileged-identity-management-access-reviews/advanced-settings.png)

1. 将“显示建议”设置为“启用”，以基于用户的访问权限信息向评审者显示系统建议。 

1. 将“需要提供审批原因”设置为“启用”，以要求审阅者提供批准原因。  

1. 将“邮件通知”设置为“启用”，以便在访问评审开始时让 Azure AD 向评审者发送电子邮件通知，并在评审完成时向管理员发送电子邮件通知。  

1. 将“提醒”设置为“启用”，让 Azure AD 向尚未完成其审阅的审阅者发送访问评审正在进行的提醒。  
1. 发送给审阅者的电子邮件的内容是根据审阅详细信息（如审阅名称、资源名称、截止日期等）自动生成的。如果你需要一种方式来传达其他信息（例如其他说明或联系人信息），则可在审阅者电子邮件的“其他内容”中指定这些详细信息，这些信息将包含在发送给分配的审阅者的邀请和提醒电子邮件中。 下面突出显示的部分是将要显示此信息的位置。

    ![突出显示发送给审阅者的电子邮件内容](./media/active-directory-privileged-identity-management-access-reviews/email-info.png)