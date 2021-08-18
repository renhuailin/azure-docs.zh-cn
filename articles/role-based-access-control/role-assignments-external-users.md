---
title: 使用 Azure 门户向外部来宾用户分配 Azure 角色 - Azure RBAC
description: 了解如何使用 Azure 门户和 Azure 基于角色的访问控制 (Azure RBAC) 向组织外部的用户授予对 Azure 资源的访问权限。
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: how-to
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 06/28/2021
ms.author: rolyon
ms.custom: it-pro,subject-rbac-steps
ms.openlocfilehash: 175beacc486c4b59919bf20300bbd06f7b9aa1a7
ms.sourcegitcommit: 1c12bbaba1842214c6578d914fa758f521d7d485
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2021
ms.locfileid: "112988385"
---
# <a name="assign-azure-roles-to-external-guest-users-using-the-azure-portal"></a>使用 Azure 门户向外部来宾用户分配 Azure 角色

[Azure 基于角色的访问控制 (RBAC)](overview.md) 可以为大型组织和中小型企业提供更好的安全管理，与中小企业合作的外部协作者、供应商或自由职业者需要访问你环境中的特定资源，但不一定需要访问整个基础架构或任何与计费相关的范围。 你可以使用 [Azure Active Directory B2B](../active-directory/external-identities/what-is-b2b.md) 中的功能与外部来宾用户合作，并且可以使用 Azure RBAC 仅授予来宾用户在你的环境中需要的权限。

## <a name="prerequisites"></a>先决条件

若要分配 Azure 角色或删除角色分配，必须满足以下条件：

- `Microsoft.Authorization/roleAssignments/write` 和 `Microsoft.Authorization/roleAssignments/delete` 权限，例如[用户访问管理员](built-in-roles.md#user-access-administrator)或[所有者](built-in-roles.md#owner)

## <a name="when-would-you-invite-guest-users"></a>你何时将邀请来宾用户？

下面是一些你可能会将来宾用户邀请到你的组织并向其授权的示例场景：

- 允许仅拥有电子邮件帐户的外部个体私营供应商访问项目的 Azure 资源。
- 允许外部合作伙伴管理某些资源或整个订阅。
- 允许组织外的支持工程师（如 Microsoft 支持部门）临时访问 Azure 资源，以解决问题。

## <a name="permission-differences-between-member-users-and-guest-users"></a>成员用户与来宾用户之间的权限差异

目录的原生成员（成员用户）具有的权限不同于从另一个目录作为 B2B 协作来宾（来宾用户）邀请的用户。 例如，成员用户可以读取几乎所有目录信息，而来宾用户的目录权限则受限。 有关成员用户和来宾用户的详细信息，请参阅 [Azure Active Directory 中的默认用户权限是什么？](../active-directory/fundamentals/users-default-permissions.md)。

## <a name="add-a-guest-user-to-your-directory"></a>将来宾用户添加到目录

按照以下步骤，使用“Azure Active Directory”页将来宾用户添加到你的目录。

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 请确保你的组织的外部协作设置已配置为允许你邀请来宾。 有关详细信息，请参阅[启用 B2B 外部协作并管理谁可以邀请来宾](../active-directory/external-identities/delegate-invitations.md)。

1. 单击“Azure Active Directory” > “用户” > “新建来宾用户”  。

    ![Azure 门户中“新建来宾用户”功能的屏幕截图。](./media/role-assignments-external-users/invite-guest-user.png)

1. 按照步骤添加新的来宾用户。 有关详细信息，请参阅[在 Azure 门户中添加 Azure Active Directory B2B 协作用户](../active-directory/external-identities/add-users-administrator.md#add-guest-users-to-the-directory)。

将来宾用户添加到目录后，你可以向来宾用户发送指向共享应用程序的直接链接，或者来宾用户可以单击邀请电子邮件中的接受邀请链接。

![来宾用户邀请电子邮件的屏幕截图。](./media/role-assignments-external-users/invite-email.png)

来宾用户要想能够访问你的目录，必须完成邀请过程。

![来宾用户邀请审阅权限的屏幕截图。](./media/role-assignments-external-users/invite-review-permissions.png)

有关邀请过程的详细信息，请参阅 [Azure Active Directory B2B 协作邀请兑换](../active-directory/external-identities/redemption-experience.md)。

## <a name="assign-a-role-to-a-guest-user"></a>向来宾用户分配角色

在 Azure RBAC 中，若要授予访问权限，需分配一个角色。 若要向来宾用户分配角色，则需要按照与处理成员用户、组、服务主体或托管标识[相同的步骤](role-assignments-portal.md)。 若要向不同作用域的来宾用户分配角色，请按以下步骤操作。

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 在顶部的“搜索”框中，搜索要授予对其的访问权限的范围。 例如，搜索“管理组”、“订阅”、“资源组”或某个特定资源  。

1. 单击该范围的特定资源。

1. 单击“访问控制(IAM)”。

    下面显示了资源组的“访问控制(IAM)”页的示例。

    ![用于预览版体验的资源组访问控制 (IAM) 页的屏幕截图。](./media/shared/rg-access-control.png)

1. 单击“角色分配”选项卡以查看在此范围内的角色分配。

1. 单击“添加” > “添加角色分配(预览)”。

    如果没有分配角色的权限，则将禁用“添加角色分配”选项。

    ![预览体验的“添加”>“添加角色分配”菜单的屏幕截图。](./media/shared/add-role-assignment-menu-preview.png)

    随即打开“添加角色分配”窗格。

1. 在“角色”选项卡中选择一个角色，例如“虚拟机参与者” 。

   ![预览版体验的含有“角色”选项卡的“添加角色分配”页的屏幕截图。](./media/shared/roles.png)

1. 在“成员”选项卡上，选择“用户、组或服务主体” 。

   ![预览版体验的含有“成员”选项卡的“添加角色分配”页的屏幕截图。](./media/shared/members.png)

1. 单击“选择成员”。

1. 查找并选择来宾用户。 如果没有在列表中看到用户，则可在“选择”框中键入相应内容，以便在目录中搜索显示名称或电子邮件地址。

    可以在“选择”框中键入，以在目录中搜索显示名称或电子邮件地址。

    ![预览体验的“选择成员”窗格的屏幕截图。](./media/role-assignments-external-users/select-members.png)

1. 单击“选择”，将来宾用户添加到“成员”列表。

1. 在“查看 + 分配”选项卡上单击“查看 + 分配” 。

    稍等片刻，就会在所选范围为来宾用户分配角色。

    ![“虚拟机参与者”角色分配的屏幕截图。](./media/role-assignments-external-users/access-control-role-assignments.png)

## <a name="assign-a-role-to-a-guest-user-not-yet-in-your-directory"></a>向目录中尚未包含的来宾用户分配角色

若要向来宾用户分配角色，则需要按照与处理成员用户、组、服务主体或托管标识[相同的步骤](role-assignments-portal.md)。

如果来宾用户还不在你的目录中，则可直接从“选择成员”窗格邀请用户。

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 在顶部的“搜索”框中，搜索要授予对其的访问权限的范围。 例如，搜索“管理组”、“订阅”、“资源组”或某个特定资源  。

1. 单击该范围的特定资源。

1. 单击“访问控制(IAM)”。

1. 单击“添加” > “添加角色分配(预览)”。

    如果没有分配角色的权限，则将禁用“添加角色分配”选项。

    ![预览体验的“添加”>“添加角色分配”菜单的屏幕截图。](./media/shared/add-role-assignment-menu-preview.png)

    随即打开“添加角色分配”窗格。

1. 在“角色”选项卡中选择一个角色，例如“虚拟机参与者” 。

1. 在“成员”选项卡上，选择“用户、组或服务主体” 。

   ![预览版体验的含有“成员”选项卡的“添加角色分配”页的屏幕截图。](./media/shared/members.png)

1. 单击“选择成员”。

1. 在“选择”框中，键入要邀请的人员的电子邮件地址，然后选择该人员。

    ![“选择成员”窗格中“请来宾用户”的屏幕截图。](./media/role-assignments-external-users/select-members-new-guest.png)

1. 单击“选择”，将来宾用户添加到“成员”列表。

1. 在“查看 + 分配”选项卡上单击“查看 + 分配”以将来宾用户添加到目录、分配角色并发送邀请 。

    过一会儿，你将看到有关角色分配的通知和有关邀请的信息。

    ![角色分配和已邀请用户通知的屏幕截图。](./media/role-assignments-external-users/invited-user-notification.png)

1. 若要手动邀请来宾用户，请右键单击并复制通知中的邀请链接。 请勿单击此邀请链接，因为它会启动邀请过程。

    邀请链接将采用以下格式：

    `https://login.microsoftonline.com/redeem?rd=https%3a%2f%2finvitations.microsoft.com%2fredeem%2f%3ftenant%3d0000...`

1. 将邀请链接发送给来宾用户以完成邀请过程。

    有关邀请过程的详细信息，请参阅 [Azure Active Directory B2B 协作邀请兑换](../active-directory/external-identities/redemption-experience.md)。

## <a name="remove-a-guest-user-from-your-directory"></a>从目录中删除来宾用户

在从目录中删除来宾用户之前，应该先删除该来宾用户的所有角色分配。 遵循以下步骤从目录中删除来宾用户。

1. 在来宾用户具有角色分配的某个作用域（例如管理组、订阅、资源组或资源）内打开“访问控制(标识和访问管理)”。

1. 单击“角色分配”选项卡以查看所有角色分配。

1. 在角色分配列表中，在具有要删除的角色分配的来宾用户旁边添加一个复选标记。

   ![已选择要删除的角色分配的屏幕截图。](./media/role-assignments-external-users/remove-role-assignment-select.png)

1. 单击 **“删除”** 。

   ![删除角色分配消息的屏幕截图。](./media/shared/remove-role-assignment.png)

1. 在显示的“删除角色分配”消息中，单击“是”。

1. 在左侧导航栏中，单击“Azure Active Directory” > “用户”。

1. 单击要删除的来宾用户。

1. 单击“删除” 。

   ![删除来宾用户的屏幕截图。](./media/role-assignments-external-users/delete-guest-user.png)

1. 在出现的删除消息中单击“是”。

## <a name="troubleshoot"></a>故障排除

### <a name="guest-user-cannot-browse-the-directory"></a>来宾用户无法浏览目录

来宾用户的目录权限受到限制。 例如，来宾用户无法浏览目录，也无法搜索组或应用程序。 有关详细信息，请参阅 [Azure Active Directory 中的默认用户权限是什么？](../active-directory/fundamentals/users-default-permissions.md)。

![来宾用户无法浏览目录中的用户的屏幕截图。](./media/role-assignments-external-users/directory-no-users.png)

如果来宾用户在目录中需要额外的权限，则可以向来宾用户分配某个 Azure AD 角色。 如果你确实希望来宾用户对目录拥有完全读取访问权限，则可以在 Azure AD 中将来宾用户添加到[目录读取者](../active-directory/roles/permissions-reference.md#directory-readers)角色。 有关详细信息，请参阅[在 Azure 门户中添加 Azure Active Directory B2B 协作用户](../active-directory/external-identities/add-users-administrator.md)。

![分配目录读取者角色的屏幕截图。](./media/role-assignments-external-users/directory-roles.png)

### <a name="guest-user-cannot-browse-users-groups-or-service-principals-to-assign-roles"></a>来宾用户无法浏览用户、组或服务主体来分配角色

来宾用户的目录权限受到限制。 即使来宾用户是作用域中的[所有者](built-in-roles.md#owner)，但如果他们尝试通过分配角色向他人授予访问权限，则也无法浏览用户、组或服务主体的列表。

![来宾用户无法浏览安全主体来分配角色的屏幕截图。](./media/role-assignments-external-users/directory-no-browse.png)

如果来宾用户知道某人在目录中的确切登录名，则他们可以授予访问权限。 如果你确实希望来宾用户对目录拥有完全读取访问权限，则可以在 Azure AD 中将来宾用户添加到[目录读取者](../active-directory/roles/permissions-reference.md#directory-readers)角色。 有关详细信息，请参阅[在 Azure 门户中添加 Azure Active Directory B2B 协作用户](../active-directory/external-identities/add-users-administrator.md)。

### <a name="guest-user-cannot-register-applications-or-create-service-principals"></a>来宾用户无法注册应用程序或创建服务主体

来宾用户的目录权限受到限制。 如果来宾用户需要能够注册应用程序或创建服务主体，你可以在 Azure AD 中将来宾用户添加到[应用程序开发者](../active-directory/roles/permissions-reference.md#application-developer)角色。 有关详细信息，请参阅[在 Azure 门户中添加 Azure Active Directory B2B 协作用户](../active-directory/external-identities/add-users-administrator.md)。

![来宾用户无法注册应用程序的屏幕截图。](./media/role-assignments-external-users/directory-access-denied.png)

### <a name="guest-user-does-not-see-the-new-directory"></a>来宾用户看不到新目录

如果已向来宾用户授予了对某个目录的访问权限，但当他们尝试在其“目录”页面中切换时，他们看不到新目录在 Azure 门户中列出，则请确保来宾用户已完成邀请过程。 有关邀请过程的详细信息，请参阅 [Azure Active Directory B2B 协作邀请兑换](../active-directory/external-identities/redemption-experience.md)。

### <a name="guest-user-does-not-see-resources"></a>来宾用户看不到资源

如果已向来宾用户授予了对某个目录的访问权限，但他们在 Azure 门户中看不到自己有权访问的资源，请确保来宾用户选择了正确的目录。 来宾用户可能有权访问多个目录。 若要切换目录，请在左上方单击“设置” > “目录”，然后单击相应的目录 。

![Azure 门户中门户设置目录部分的屏幕截图。](./media/role-assignments-external-users/directory-switch.png)

## <a name="next-steps"></a>后续步骤

- [在 Azure 门户中添加 Azure Active Directory B2B 协作用户](../active-directory/external-identities/add-users-administrator.md)
- [Azure Active Directory B2B 协作用户的属性](../active-directory/external-identities/user-properties.md)
- [B2B 协作邀请电子邮件的元素 - Azure Active Directory](../active-directory/external-identities/invitation-email-elements.md)
- [将来宾用户添加为共同管理员](classic-administrators.md#add-a-guest-user-as-a-co-administrator)