---
title: 创建对组和应用程序的访问评审 - Azure AD
description: 了解如何在 Azure Active Directory 访问评审中创建对组成员或应用程序访问的访问评审。
services: active-directory
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 5/6/2021
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3b4426f10ced8d8cda294c3a80923712f59e8466
ms.sourcegitcommit: 86ca8301fdd00ff300e87f04126b636bae62ca8a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2021
ms.locfileid: "122195360"
---
# <a name="create-an-access-review-of-groups-and-applications-in-azure-ad-access-reviews"></a>在 Azure AD 访问评审中创建对组和应用程序的访问评审

员工和来宾对组和应用程序的访问权限会不断变化。 为了降低与过期访问权限分配相关的风险，管理员可以使用 Azure Active Directory (Azure AD) 针对组成员或应用程序访问权限创建访问评审。 Microsoft 365 和安全组所有者还可以使用 Azure AD 为组成员创建访问评审（预览版），前提是全局或用户管理员在“访问评审设置”边栏选项卡中启用了该设置。 如果需要定期评审访问权限，则还可以创建定期访问评审。 有关这些方案的详细信息，请参阅[管理用户访问权限](manage-user-access-with-access-reviews.md)和[管理来宾访问权限](manage-guest-access-with-access-reviews.md)。

你可以观看有关如何启用访问评审的短片：

>[!VIDEO https://www.youtube.com/embed/X1SL2uubx9M]

本文介绍如何针对组成员或应用程序访问权限创建一个或多个访问评审。

## <a name="prerequisites"></a>必备条件

- Azure AD Premium P2
- 全局管理员或用户管理员
- Microsoft 365 和安全组所有者（预览版）

有关详细信息，请参阅[许可证要求](access-reviews-overview.md#license-requirements)。

## <a name="create-one-or-more-access-reviews"></a>创建一个或多个访问评审

1. 登录到 Azure 门户并打开[“标识治理”页](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)。

2. 在左侧菜单中，单击“访问评审”。 

3. 单击“新建访问评审”创建新的访问评审。 

    ![标识治理中的“访问评审”窗格](./media/create-access-review/access-reviews.png)

4. 在“步骤 1:选择要评审的内容”中，选择你要评审哪个资源。

    ![创建访问评审 - 评审名称和说明](./media/create-access-review/select-what-review.png)

5. 如果在步骤 1 中选择了“团队 + 组”，则步骤 2 中有两个选项
   - 所有包含来宾用户的 Microsoft 365 组。 如果你要针对组织中所有 Microsoft Teams 和 Microsoft 365 组中的所有来宾用户创建定期评审，请选择此选项。 可以选择通过单击“选择要排除的组”来排除某些组。
   - 选择“团队 + 组”。 如果要指定需评审的一组有限团队和/或组，请选择此选项。 单击此选项后，你会在右侧看到要从中进行选择的组的列表。

     ![团队和组](./media/create-access-review/teams-groups.png)

     ![在用户界面中选择的团队和组](./media/create-access-review/teams-groups-detailed.png)

6. 如果你在第 1 步中选择了“应用程序”，则在第 2 步中可以选择一个或多个应用程序

    >[!NOTE]
    > 选择多个组和/或应用程序会导致创建多个访问评审。 例如，如果你选择 5 个要评审的组，则会导致创建 5 个单独的访问评审

   ![当选择了应用程序而非组时显示的接口](./media/create-access-review/select-application-detailed.png)

7. 接下来，你可以在第 3 步中选择评审范围。 选项有：
   - 仅限来宾用户。 选择此选项可将访问评审限定为目录中的 Azure AD B2B 来宾用户。
   - 所有人。 选择此选项可将访问评审的范围限定为与资源关联的所有用户对象。

    >[!NOTE]
    > 如果你在第 2 步中选择了“包含来宾用户的所有 Microsoft 365 组”，则第 3 步中唯一的选项是评审来宾用户

8. 单击“下一步:评审”。

9. 在“选择审阅者”部分，选择一人或多人来执行访问评审。 可以选择：
    - 组所有者（仅在对团队或组执行评审时可用）
    - 选定的用户或组
    - 用户评审自己的访问权限
    - 用户的经理。
    如果你选择“用户的经理”或“组所有者”，则还可以指定回退审阅者。 当用户未在目录中指定任何经理或者组没有所有者时，系统会要求回退审阅者执行评审。

    ![新建访问评审](./media/create-access-review/new-access-review.png)

10. 在“指定评审重复周期”部分，你可以指定一个频率，例如“每周”、“每月”、“每季”、“半年”、“每年”。 然后指定“持续时间”，用于定义评审将开放多长时间供审阅者进行输入。 例如，每月评审的最长持续时间可以设置为 27 天，以免评审时间重叠。 你可能希望缩短持续时间，以确保尽早应用你的审阅者输入。 接下来，你可以选择“开始日期”和“结束日期”。

    ![选择评审的发生频率](./media/create-access-review/frequency.png)

11. 在页面底部单击“下一步: 设置”按钮。

12. 在“完成后操作设置”中，你可以指定评审完成后发生的情况

    ![创建访问评审 - 完成后操作设置](./media/create-access-review/upon-completion-settings-new.png)

    如果要自动删除被拒绝用户的访问权限，请将“将结果自动应用到资源”设置为“启用” 。 若要在评审完成后手动应用结果，请将开关设置为“禁用”。 
    
    使用“如果审阅者未答复”列表指定对于审阅者在评审期限内未评审的用户要执行的操作。 此设置不影响审阅者已手动评审的用户。 如果最终的审阅者决策是“拒绝”，则会删除用户的访问权限。

    - **不更改** - 将用户访问权限保持不变
    - **删除访问权限** - 删除用户的访问权限
    - **批准访问权限** - 批准用户的访问权限
    - **采用建议** - 根据系统的建议拒绝或批准用户的持续访问权限

    ![完成后操作设置选项](./media/create-access-review/upon-completion-settings-new.png)

    使用将应用于被拒绝的来宾用户的“操作”，以指定来宾用户被拒绝时发生的情况。
    - 从资源中删除用户的成员身份会删除被拒绝用户对要评审的组或应用程序的访问权限，他们仍将能登录到租户。
    - 阻止用户在 30 天内登录，然后将用户从租户中删除，这样会阻止被拒绝用户登录到租户，无论他们是否有权访问其他资源。 如果出现错误，或者管理员决定重新启用某人的访问权限，该管理员可以在用户被禁用后 30 天内执行此操作。 如果没有对禁用的用户执行任何操作，则会从租户中删除这些用户。

    若要详细了解删除不能再访问你组织中资源的来宾用户的最佳做法，请阅读标题为[使用 Azure AD Identity Governance 评审和删除不再具有资源访问权限的外部用户](access-reviews-external-users.md)的文章。


    > [!NOTE]
    > 对于范围超出来宾用户的评审，要对被拒绝的来宾用户应用的操作不可配置。 对于“包含来宾用户的所有 Microsoft 365 组”的评审，此操作也不可配置。 当不可配置时，将对被拒绝的用户使用从资源中删除用户的成员身份这一默认选项。

13. 可以向其他用户或组发送通知（预览版），接收评审完成度消息。 除评审创建者之外的利益干系人使用此功能可了解最新的评审进度。 若要使用此功能，请选择“选择用户或组”并在要接收完成度状态时添加其他用户或组。

14. 在“启用评审决策助手”中，选择你是否希望审阅者在评审过程中收到建议。

    ![启用决策助手选项](./media/create-access-review/helpers.png)

15. 在“高级设置”部分，你可以执行以下操作：
    - 将“必须提供理由”设置为“启用”，以要求审阅者提供批准原因。 
    - 将“电子邮件通知”设置为“启用”，以便让 Azure AD 在访问评审开始时向审阅者发送电子邮件通知，在评审完成时向管理员发送电子邮件通知。 
    - 将“提醒”设置为“启用”，让 Azure AD 向所有审阅者发送访问评审正在进行的提醒 。 审阅者将在审核过程中途收到提醒，无论他们当时是否已完成评审。
    - 发送给审阅者的电子邮件的内容是根据评审详细信息（如评审名称、资源名称、截止日期等）自动生成的。如果你需要一种方式来传达其他信息（例如其他说明或联系人信息），则可在“审阅者电子邮件的其他内容”部分指定这些详细信息。 你输入的信息包含在发送给分配的审阅者的邀请和提醒电子邮件中。 下图中突出显示的部分显示了此信息的显示位置。

      ![有关审阅者的其他内容](./media/create-access-review/additional-content-reviewer.png)

16. 单击“下一步:查看 + 创建”以转到下一页

17. 命名访问评审。 可选择为评审提供说明。 名称和说明向评审者显示。

18. 查看信息，然后选择“创建”。

       ![“创建评审”屏幕](./media/create-access-review/create-review.png)

## <a name="allow--group-owners-to-create-and-manage-access-reviews-preview"></a>允许群所有者创建和管理访问评审（预览版）

必备角色：全局管理员或用户管理员

1. 登录到 Azure 门户并打开[“标识治理”页](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)。

1. 在左侧菜单的“访问评审”下，选择“设置” 。

1. 在“委托人”（可创建和管理访问评审）页面上，将“组所有者可以创建和管理其拥有的组的访问评审”设置为“是” 。

    ![创建评审 - 允许群所有者进行评审](./media/create-access-review/group-owners-review-access.png)

    > [!NOTE]
    > 默认情况下，该设置设置为“否”，因此必须对其进行更新，允许组所有者创建和管理访问评审。

## <a name="start-the-access-review"></a>启动访问评审

指定访问评审的设置后，单击“启动”。 访问评审将显示在列表中，并带有其状态指示器。

![访问评审及其状态的列表](./media/create-access-review/access-reviews-list.png)

默认情况下，在评审开始后不久，Azure AD 会向评审者发送一封电子邮件。 如果选择不让 Azure AD 发送电子邮件，请务必通知评审者有一个访问评审任务等待他们完成。 可以向他们显示有关如何[评审对组或应用程序的访问权限](perform-access-review.md)的说明。 如果评审工作是让来宾评审他们自己的访问权限，则可以显示有关如何[评审自己对组或应用程序的访问权限](review-your-access.md)的说明。

如果将来宾指定为评审者，但他们尚未接受邀请，则他们不会收到来自访问评审的电子邮件，因为在评审前必须先接受邀请。

## <a name="access-review-status-table"></a>访问评审状态表

| 状态 | 定义 |
|--------|------------|
|NotStarted | 已创建评审，用户发现正在等待启动。 |
|正在初始化   | 正在进行用户发现，以标识属于评审的所有用户。 |
|正在启动 | 正在启动评审。 如果启用电子邮件通知，则会将电子邮件发送给评审者。 |
|正在进行 | 已开始评审。 如果启用电子邮件通知，则电子邮件已发送给评审者。 评审者可以在截止日期之前提交决定。 |
|正在完成 | 评审正在完成，电子邮件将发送到评审所有者。 |
|正在自动评审 | 评审处于系统评审阶段。 系统正在为未根据建议或预先配置的决定评审的用户记录决定。 |
|已自动评审 | 系统已为未评审的所有用户记录了决定。 如果启用了“自动应用”，则评审可以进入“正在应用”阶段了。 |
|正在应用 | 对于已批准的用户，访问权限不会更改。 |
|已应用 | 已经从源或目录删除拒绝的用户（如果有）。 |
|失败 | 无法进行评审。 此错误可能与删除租户、进行许可证更改或进行其他内部租户更改相关。 |

## <a name="create-reviews-via-apis"></a>通过 API 创建评审

也可以使用 API 创建访问评审。 在 Azure 门户中管理组和应用程序用户的访问评审的方法也可以使用 Microsoft Graph API 来实现。 
+ 有关详细信息，请参阅 [Azure AD 访问评审 API 参考](/graph/api/resources/accessreviewsv2-root)。
+ 有关教程，请参阅[使用访问评审 API 评审对 Microsoft 365 组的来宾访问](/graph/tutorial-accessreviews-m365group)。
+ 有关代码示例，请参阅[通过 Microsoft Graph 检索 Azure AD 访问评审的示例](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/m-p/236096)。

## <a name="next-steps"></a>后续步骤

- [评审组或应用程序的访问权限](perform-access-review.md)
- [评审自己对组或应用程序的访问权限](review-your-access.md)
- [完成组或应用程序的访问评审](complete-access-review.md)
