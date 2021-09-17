---
title: 在 Azure 实验室服务中添加用户作为实验室创建者
description: 本文介绍如何在 Azure 实验室服务中向实验室帐户的实验室创建者角色添加用户。 实验室创建者可以在此实验室帐户中创建实验室。
ms.topic: article
ms.date: 07/26/2021
ms.custom: subject-rbac-steps
ms.openlocfilehash: a2135ab6580d39d6c63f7e948a29f0f0ede4efd6
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2021
ms.locfileid: "122634191"
---
# <a name="add-lab-creators-to-a-lab-account-in-azure-lab-services"></a>在 Azure 实验室服务中向实验室帐户添加实验室创建者
本文介绍如何在 Azure 实验室服务中将用户作为实验室创建者添加到实验室帐户中。 然后，这些用户可以在实验室帐户中创建实验室。 

## <a name="add-microsoft-user-account-to-lab-creator-role"></a>将 Microsoft 用户帐户添加到实验室创建者角色
若要在实验室帐户中设置课堂实验室，用户必须是实验室帐户中“实验室创建者”角色的成员。 用来创建实验室帐户的帐户会自动添加到此角色。 如果打算使用同一用户帐户创建课堂实验室，可以跳过此步骤。 若要使用其他用户帐户创建教室实验室，请执行以下步骤： 

为教师提供创建课程实验室的权限，并将他们添加为“实验室创建者”角色：有关详细步骤，请参阅[使用 Azure 门户分配 Azure 角色](../role-based-access-control/role-assignments-portal.md)。


1. 在“实验室帐户”页上，选择“访问控制 (IAM)” 

1. 选择“添加” > “+ 添加角色分配（预览版）”。 

    ![打开了“添加角色分配”菜单的“访问控制(IAM)”页。](../../includes/role-based-access-control/media/add-role-assignment-menu-generic.png)

1. 在“角色”选项卡上，选择“实验室创建者”角色。 

    ![选中了“角色”选项卡的“添加角色分配”页。](../../includes/role-based-access-control/media/add-role-assignment-role-generic.png)

1. 在“成员”选项卡上，选择要添加到“实验室创建者”角色的用户

1. 在“查看 + 分配”选项卡上，选择“查看 + 分配”，以分配角色 。




    > [!NOTE]
    > 如果要添加非 Microsoft 帐户用户作为实验室创建者，请参阅[添加非 Microsoft 帐户用户作为实验室创建者](#add-a-non-microsoft-account-user-as-a-lab-creator)部分。 

## <a name="add-a-non-microsoft-account-user-as-a-lab-creator"></a>添加非 Microsoft 帐户用户作为实验室创建者
若要添加用户作为实验室创建者，请使用他们的电子邮件帐户。 可以使用以下类型的电子邮件帐户：

- 由大学 Azure Active Directory (AAD) 提供的电子邮件帐户。
- Microsoft 电子邮件帐户，如 `@outlook.com`、`@hotmail.com`、`@msn.com` 或 `@live.com`。
- 非 Microsoft 电子邮件帐户，如 Yahoo 或 Google 提供的帐户。 但是，这些类型的帐户必须与 Microsoft 帐户链接。
- 一个 GitHub 帐户。 该帐户必须与 Microsoft 帐户链接。

### <a name="using-a-non-microsoft-email-account"></a>使用非 Microsoft 电子邮件帐户
实验室创建者/讲师可以使用非 Microsoft 电子邮件帐户来注册和登录课堂实验室。  但是，登录实验室服务门户需要讲师首先创建一个链接到他们的非 Microsoft 电子邮件地址的 Microsoft 帐户。

许多讲师可能已经有一个链接到非 Microsoft 电子邮件地址的 Microsoft 帐户。 例如，如果讲师已经将其电子邮件地址与其他 Microsoft 的其它产品或服务（如 Office、Skype、OneDrive 或 Windows）一起使用，则讲师已经获得了一个 Microsoft 帐户。  

当讲师登录实验室服务门户时，系统会提示他们输入电子邮件地址和密码。 如果讲师尝试使用未链接 Microsoft 帐户的非 Microsoft 帐户进行登录，则讲师将收到以下错误消息： 

![错误消息](./media/how-to-configure-student-usage/cant-find-account.png)

若要注册 Microsoft 帐户，讲师应前往 [http://signup.live.com](http://signup.live.com)。  


### <a name="using-a-github-account"></a>使用 GitHub 帐户
讲师还可以使用现有的 GitHub 帐户注册和登录到课堂实验室。 如果讲师已有一个链接到其 GitHub 帐户的 Microsoft 帐户，则他们可以登录并提供其密码，如前一部分中所示。 如果讲师尚未将其 GitHub 帐户链接到 Microsoft 帐户，则应选择“登录”选项：

![“登录选项”链接](./media/how-to-configure-student-usage/signin-options.png)

在“登录选项”页中，选择“使用 GitHub 登录” 。

![“使用 GitHub 登录”链接](./media/how-to-configure-student-usage/signin-github.png)

最后，系统将提示他们创建一个链接到其 GitHub 帐户的 Microsoft 帐户。 如果讲师选择“下一步”，则会自动执行登录过程。  然后讲师会立即登录并连接到课堂实验室。


## <a name="next-steps"></a>后续步骤
请参阅以下文章：

- [以实验室所有者身份创建并管理实验室](how-to-manage-classroom-labs.md)
- [以实验室所有者身份设置并发布模板](how-to-create-manage-template.md)
- [以实验室所有者身份配置并控制实验室的使用](how-to-configure-student-usage.md)
- [以实验室用户身份访问实验室](how-to-use-classroom-lab.md)
