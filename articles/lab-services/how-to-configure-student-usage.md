---
title: 在 Azure 实验室服务的实验室中配置使用设置
description: 了解如何配置实验室的学生数，将学生注册到实验室，控制学生可以使用 VM 的小时数，以及其他方面。
ms.topic: article
ms.date: 12/01/2020
ms.openlocfilehash: 380a587eecb276c457b93ca3c3f3ac08b2239275
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791957"
---
# <a name="add-and-manage-lab-users"></a>添加和管理实验室用户

本文介绍如何将学生用户添加到实验室、向实验室注册这些用户、控制他们可使用虚拟机 (VM) 的小时数，以及其他方面。 

添加用户时，默认情况下，“限制访问”选项处于打开状态，除非他们在用户列表中，否则即使学生有注册链接，他们也无法注册到实验室。 只有列出的用户可以使用你发送的注册链接注册到实验室。 你可以关闭“限制访问”，这样一来，学生只要有注册链接就可以注册到实验室。 

本文介绍如何将用户添加到实验室。

## <a name="add-users-from-an-azure-ad-group"></a>从 Azure AD 组添加用户

### <a name="overview"></a>概述

现在可以将实验室用户列表同步到现有 Azure Active Directory (Azure AD) 组，这样就无需手动添加或删除用户。 

可以在组织的 Azure Active Directory 中创建 Azure AD 组，以管理对组织资源和基于云的应用的访问。 若要了解详细信息，请参阅 [Azure AD 组](../active-directory/fundamentals/active-directory-manage-groups.md)。 如果组织使用 Microsoft Office 365 或 Azure 服务，则组织已有管理 Azure Active Directory 的管理员。 

### <a name="sync-users-with-azure-ad-group"></a>将用户与 Azure AD 组同步

> [!IMPORTANT]
> 确保用户列表为空。 如果实验室中存在手动添加或通过导入 CSV 文件添加的现有用户，则不会显示用于将实验室同步到现有组的选项。 

1. 登录到 [Azure 实验室服务网站](https://labs.azure.com/)。
1. 选择要使用的实验室。
1. 在左窗格中，选择“用户”。 
1. 单击“从组同步”。 

    :::image type="content" source="./media/how-to-configure-student-usage/add-users-sync-group.png" alt-text="通过从 Azure AD 组同步来添加用户":::
    
1. 系统将提示你选择要将实验室同步到的现有 Azure AD 组。 
    
    如果在列表中看不到 Azure AD 组，可能是由于以下原因造成的：

    -   如果你是 Azure Active Directory 的来宾用户（通常是在拥有 Azure AD 的组织之外），你将无法在 Azure AD 内搜索组。 在这种情况下，你将无法向实验室添加 Azure AD 组。 
    -   通过 Teams 创建 Azure AD 组不会显示在此列表中。 你可以在 Teams 内添加 Azure 实验室服务应用，以便直接从中创建和管理实验室。 请参阅有关[从 Teams 内部管理实验室用户列表](how-to-manage-user-lists-within-teams.md)的详细信息。 
1. 选择要将实验室同步到 Azure AD 组后，单击“添加”。
1. 同步实验室后，它会将 Azure AD 组内的所有人作为用户拉入实验室，你将看到更新的用户列表。 只有此 Azure AD 组中的人员才能访问你的实验室。 用户列表将每 24 小时刷新一次，以与 Azure AD 组的最新成员身份匹配。 还可以单击“用户”选项卡中的“同步”按钮，手动同步到 Azure AD 组中的最新更改。
1. 单击“全部邀请”按钮邀请用户加入实验室，这将向所有用户发送一封电子邮件，其中包含实验室的注册链接。 

### <a name="automatic-management-of-virtual-machines-based-on-changes-to-the-azure-ad-group"></a>基于对 Azure AD 组的更改自动管理虚拟机 

实验室同步到 Azure AD 组后，实验室中的虚拟机数将自动与组中的用户数相匹配。 你将无法再手动更新实验室容量。 将用户添加到 Azure AD 组后，实验室会自动为该用户添加一个虚拟机。 从 Azure AD 组中删除某个用户后，实验室会自动从实验室删除该用户的虚拟机。 

## <a name="add-users-manually-from-emails-or-csv-file"></a>从电子邮件或 CSV 文件中手动添加用户

在本部分中，你将手动添加学生（通过电子邮件地址或上传 CSV 文件）。 

### <a name="add-users-by-email-address"></a>按电子邮件地址添加用户

1. 在左窗格中，选择“用户”。 
1. 单击“手动添加用户”。 

    :::image type="content" source="./media/how-to-configure-student-usage/add-users-manually.png" alt-text="手动添加用户":::
1. 选择“按电子邮件地址添加”（默认），在单独的行或用分号分隔的单个行上输入学生的电子邮件地址。 

    :::image type="content" source="./media/how-to-configure-student-usage/add-users-email-addresses.png" alt-text="添加用户的电子邮件地址":::
1. 选择“保存”。 

    该列表显示当前用户的电子邮件地址和状态，无论他们是否已注册到实验室。 

    :::image type="content" source="./media/how-to-configure-student-usage/list-of-added-users.png" alt-text="用户列表":::

    > [!NOTE]
    > 学生注册到实验室后，列表将显示其名称。 列表中显示的名称是按 Azure Active Directory 中学生的名字和姓氏构造的。 

### <a name="add-users-by-uploading-a-csv-file"></a>通过上传 CSV 文件添加用户

还可以通过上载包含其电子邮件地址的 CSV 文件来添加用户。 

CSV 文本文件用于存储以逗号分隔 (CSV) 的表格数据（数字和文本）。 CSV 文件存储以逗号分隔的信息，而不是将信息存储在列字段中（如电子表格中）。 CSV 文件中的每一行都相同数量的以逗号分隔的“字段”。 可以使用 Excel 轻松创建和编辑 CSV 文件。

1. 在 Microsoft Excel 中，创建一个在一列中列出学生电子邮件地址的 CSV 文件。

    :::image type="content" source="./media/how-to-configure-student-usage/csv-file-with-users.png" alt-text="CSV 文件中的用户列表":::
1. 在“用户”窗格的顶部，选择“添加用户”，然后选择“上传 CSV”  。
1. 选择包含学生电子邮件地址的 CSV 文件，然后选择“打开”。

    “添加用户”窗口显示 CSV 文件中的电子邮件地址列表。 
1. 选择“保存”。 
1. 在“用户”窗格中，查看添加的学生列表。 

    :::image type="content" source="./media/how-to-configure-student-usage/list-of-added-users.png" alt-text="用户”窗格中已添加用户的列表":::

## <a name="send-invitations-to-users"></a>向用户发送邀请

要向新用户发送注册链接，请使用以下方法之一。 

如果为实验室启用了“限制访问”选项，则只有列出的用户可以使用注册链接注册到实验室。 默认情况下会启用此选项。 

### <a name="invite-all-users"></a>邀请所有用户

此方法说明如何向所有列出的学生发送包含注册链接和可选消息的电子邮件。

1. 在“用户”窗格中，选择“全部邀请” 。 

    ![“全部邀请”按钮](./media/tutorial-setup-classroom-lab/invite-all-button.png)

1. 在“通过电子邮件发送邀请”窗口中，输入可选的消息，然后选择“发送”。  

    电子邮件自动包含注册链接。 要单独获取并保存注册链接，请选择“用户”窗格顶部的省略号 (...)，然后选择“注册链接”。 

    ![“通过电子邮件发送注册链接”窗口](./media/tutorial-setup-classroom-lab/send-email.png)

    “用户”列表的“邀请”列显示添加的每个用户的邀请状态。 状态应更改为“正在发送”，然后更改为“发送日期”。 **\<date>** 

### <a name="invite-selected-users"></a>邀请所选用户

此方法说明了如何仅邀请特定学生，并获取可与其他人共享的注册链接。

1. 在“用户”窗格中，选择列表中的一个或多个学生。 

1. 在所选学生的行中，选择“信封”图标，或在工具栏上选择“邀请”。 

    ![邀请所选用户](./media/how-to-configure-student-usage/invite-selected-users.png)

1. 在“通过电子邮件发送邀请”页上，输入可选的消息，然后选择“发送”。   

    ![向选定用户发送电子邮件](./media/how-to-configure-student-usage/send-invitation-to-selected-users.png)

    “用户”窗格在表的“邀请”列中显示此操作的状态。 邀请电子邮件包含学生可用于向实验室注册的注册链接。

## <a name="get-the-registration-link"></a>获取注册链接

在本部分中，可以从门户获取注册链接，并使用自己的电子邮件应用程序发送该链接。 

1. 在“用户”窗格中，选择“注册链接”。 

    ![学生注册链接](./media/how-to-configure-student-usage/registration-link-button.png)

1. 在“用户注册”窗口中，选择“复制”，然后选择“完成”。 

    ![“用户注册”窗口](./media/how-to-configure-student-usage/registration-link.png)

    将链接复制到剪贴板。 
    
1. 在电子邮件应用程序中，粘贴注册链接，然后将电子邮件发送给学生，以便学生可以注册班级。 

## <a name="view-registered-users"></a>查看已注册用户

1. 转到 [Azure 实验室服务](https://labs.azure.com)网站。 
1. 选择“登录”，然后输入凭据。 Azure 实验室服务支持组织帐户和 Microsoft 帐户。
1. 在“我的实验室”页上，选择要跟踪其使用情况的实验室。 
1. 在左窗格中，选择“用户”，或选择“用户”磁贴。 

    “用户”窗格显示已向实验室注册的学生列表。  

    ![已注册用户的列表](./media/tutorial-track-usage/registered-users.png)

## <a name="set-quotas-for-users"></a>为用户设置配额

通过执行以下操作，可以为每个学生设置小时配额： 

1. 在“用户”窗格中，选择工具栏上的“每用户配额：\<number> 小时”。 
1. 在“每用户配额”窗口中，指定要在计划的课堂时间之外为每个学生分配的小时数，然后选择“保存”。

    ![“每用户配额”窗口](./media/how-to-configure-student-usage/quota-per-user.png)    

    更改后的值现在显示在工具栏和用户列表中的“每用户配额：\<number of hours>”上，如下所示：

    ![每用户配额（小时数）](./media/how-to-configure-student-usage/quot-per-user-after.png)

    > [!IMPORTANT]
    > [VM 的计划内运行时间](how-to-create-schedules.md)不计入分配给学生的配额。 配额是指学生在 VM 上花费的计划外时间。 

## <a name="set-additional-quotas-for-specific-users"></a>为特定用户设置其他配额

除了你在之前部分中的所有用户设置的公用配额之外，还可以为特定学生指定配额。 例如，如果你作为讲师，将所有学生的配额设置为 10 小时，并为特定学生设置 5 小时的额外配额，则该学生获取 15 (10 + 5) 小时的配额。 如果稍后更改公用配额，例如15，则学生获取 20 (15 + 5) 小时的配额。 请记住，此整体配额不在计划时间内。 学生在计划时间内使用实验室 VM 的时间不计为此配额。 

要设置其他配额，请执行以下操作：

1. 在“用户”窗格中，从列表中选择一个学生，然后在工具栏上选择“调整配额”。 

    ![“调整配额”按钮](./media/how-to-configure-student-usage/adjust-quota-button.png)

1. 在“调整 \<selected user or users email address> 的配额”中，输入要为选定的一名或多名学生授予的额外实验室小时数，然后选择“应用”。 

    ![“调整配额...”窗口](./media/how-to-configure-student-usage/additional-quota.png)

    “使用情况”列显示所选学生的更新配额。 

    ![用户的新使用情况](./media/how-to-configure-student-usage/new-usage-hours.png)

## <a name="student-accounts"></a>学生帐户

要将学生添加到课堂实验室，请使用其电子邮件帐户。 学生可能有以下类型的电子邮件帐户：

- 由大学 Azure Active Directory 实例提供的学生电子邮件帐户。
- Microsoft 域电子邮件帐户，如 outlook.com、 hotmail.com、msn.com 或 live.com。   
- 非 Microsoft 电子邮件帐户，如 Yahoo! 或者 Google 提供的帐户。 但是，这些类型的帐户必须与 Microsoft 帐户链接。
- 一个 GitHub 帐户。 该帐户必须与 Microsoft 帐户链接。

### <a name="use-a-non-microsoft-email-account"></a>使用非 Microsoft 电子邮件帐户

学生可以使用非 Microsoft 电子邮件帐户来注册和登录到课堂实验室。  但注册要求它们首先创建一个 Microsoft 帐户以链接到非 Microsoft 电子邮件地址。

许多学生可能已经有一个链接到非 Microsoft 电子邮件地址的 Microsoft 帐户。 例如，如果学生已经将其电子邮件地址与其他 Microsoft 产品或服务（如 Office、Skype、OneDrive 或 Windows）一起使用，则学生已经获得了一个 Microsoft 帐户。  

当学生使用注册链接登录到课堂时，系统将提示他们输入其电子邮件地址和密码。 如果学生尝试使用未链接到 Microsoft 帐户的非 Microsoft 帐户登录，则将收到以下错误消息： 

![登录时出现错误消息](./media/how-to-configure-student-usage/cant-find-account.png)

下面是学生[注册 Microsoft 帐户](http://signup.live.com)的链接。  

> [!IMPORTANT]
> 学生登录到课堂实验室时，不会为学生提供创建 Microsoft 帐户的选项。 出于此原因，我们建议你在向使用非 Microsoft 帐户的学生发送的课堂实验室注册电子邮件中包含注册链接 http://signup.live.com 。

### <a name="use-a-github-account"></a>使用 GitHub 帐户

学生还可以使用现有的 GitHub 帐户注册和登录到课堂实验室。 如果他们已有一个链接到其 GitHub 帐户的 Microsoft 帐户，则学生可以登录并提供其密码，如前一部分中所示。 

如果尚未将其 GitHub 帐户链接到 Microsoft 帐户，则可以执行以下操作：

1. 选择“登录选项”链接，如下所示：

    ![“登录选项”链接](./media/how-to-configure-student-usage/signin-options.png)

1. 在“登录选项”窗口中，选择“用 GitHub 登录”。

    ![“用 GitHub 登录”链接](./media/how-to-configure-student-usage/signin-github.png)

    在出现提示时，学生会创建一个链接到其 GitHub 帐户的 Microsoft 帐户。 如果学生选择“下一步”，则会自动执行链接。 然后学生会立即登录并连接到课堂实验室。

## <a name="export-a-list-of-users-to-a-csv-file"></a>将用户列表导出为 CSV 文件

1. 转到“用户”窗格。
1. 在工具栏上选择省略号“...”，然后选择“导出 CSV”。 

    ![“导出 CSV”按钮](./media/how-to-export-users-virtual-machines-csv/users-export-csv.png)

## <a name="next-steps"></a>后续步骤

请参阅以下文章：

- 对于管理员：[创建和管理实验室帐户](how-to-manage-lab-accounts.md)
- 对于实验室所有者：[创建和管理实验室](how-to-manage-classroom-labs.md)和[设置和发布模板](how-to-create-manage-template.md)
- 对于实验室用户：[访问实验室](how-to-use-classroom-lab.md)