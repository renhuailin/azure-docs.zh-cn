---
title: 添加或删除用户 - Azure Active Directory | Microsoft Docs
description: 有关如何使用 Azure Active Directory 添加新用户或删除现有用户的说明。
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 05/04/2021
ms.author: ajburnle
ms.reviewer: krbain
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 34342111aa0067b4f275820ac0111692c4a590f6
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2021
ms.locfileid: "108762562"
---
# <a name="add-or-delete-users-using-azure-active-directory"></a>使用 Azure Active Directory 添加或删除用户

在 Azure Active Directory (Azure AD) 组织中添加新用户或删除现有用户。 只有用户管理员或全局管理员可以添加或删除用户。

## <a name="add-a-new-user"></a>添加新用户

可使用 Azure Active Directory 门户创建新用户。

>[!Note]
>“用户名”和“电子邮件地址”属性不能包含重音字符。

若要添加新用户，请执行以下步骤：

1. 以组织的“用户管理员”角色登录 [Azure 门户](https://portal.azure.com/)。

1. 在任意页面中搜索并选择“Azure Active Directory”。 

1. 依次选择“用户”、“新建用户”。  

    ![通过“用户”（Azure AD 中的“所有用户”）添加用户](media/add-users-azure-active-directory/add-user-in-users-all-users.png)

1. 在“用户”页上输入此用户的信息： 

   - **Name**。 必需。 新用户的名字和姓氏。 例如，*Mary Parker*。

   - **用户名**。 必需。 新用户的用户名。 例如，`mary@contoso.com`。

     用户名的域名部分必须是初始默认域名“\<yourdomainname>.onmicrosoft.com”，或自定义域名（例如“contoso.com”） 。 若要详细了解如何创建自定义域名，请参阅[使用 Azure Active Directory 门户添加自定义域名](add-custom-domain.md)。

   - **组**。 （可选）可以将用户添加到一个或多个现有组。 也可以在以后将用户添加到组中。 有关将用户添加到组的详细信息，请参阅[使用 Azure Active Directory 创建基本组并添加成员](active-directory-groups-create-azure-portal.md)。

   - **目录角色**：如果需要用户的 Azure AD 管理权限，则可以将其添加到 Azure AD 角色。 可以将用户分配为全局管理员，或者分配为 Azure AD 中有限的管理员角色中的一个或多个。 有关分配角色的详细信息，请参阅[如何向用户分配角色](active-directory-users-assign-role-azure-portal.md)。

   - **工作信息**：可在此处添加有关用户的详细信息，或者以后再添加。 有关添加用户信息的详细信息，请参阅[如何添加或更改用户个人资料信息](active-directory-users-profile-azure-portal.md)。

1. 复制“密码”框中提供的自动生成的密码。  需将此密码提供给用户，供其在首次登录时使用。

1. 选择“创建”  。

随即会创建该用户，并将其添加到 Azure AD 组织中。

## <a name="add-a-new-guest-user"></a>添加新的来宾用户

还可以通过从“新用户”页中选择“邀请用户”来邀请新的来宾用户与你的组织进行协作。 如果组织的外部协作设置已配置为允许你邀请来宾，则会向用户发送电子邮件邀请，用户需要接受邀请才能开始协作。 有关邀请 B2B 协作用户的详细信息，请参阅[邀请 B2B 用户加入 Azure Active Directory](../external-identities/add-users-administrator.md)

## <a name="add-a-consumer-user"></a>添加使用者用户

某些情况下，可能需要在 Azure Active Directory B2C (Azure AD B2C) 目录中手动创建所有者帐户。 有关创建使用者帐户的详细信息，请参阅[在 Azure AD B2C 中创建和删除使用者用户](../../active-directory-b2c/manage-users-portal.md)。

## <a name="add-a-new-user-within-a-hybrid-environment"></a>在混合环境内添加新用户

如果你的环境中同时包含 Azure Active Directory（云）和 Windows Server Active Directory（本地），则可以通过同步现有用户帐户数据来添加新用户。 有关混合环境和用户的详细信息，请参阅[将本地目录与 Azure Active Directory 集成](../hybrid/whatis-hybrid-identity.md)。

## <a name="delete-a-user"></a>删除用户

可使用 Azure Active Directory 门户删除现有用户。

>[!Note]
>必须具有“全局管理员”或“用户管理员”角色分配，以删除组织中的用户。 全局管理员可以删除任何用户，包括其他管理员。 用户管理员可以删除任何非管理员用户、支持管理员和其他用户管理员。 有关详细信息，请参阅 [Azure AD 中的管理员角色权限](../roles/permissions-reference.md)。

若要删除用户，请执行以下步骤：

1. 使用组织的用户管理员帐户登录到 [Azure 门户](https://portal.azure.com/)。

1. 在任意页面中搜索并选择“Azure Active Directory”。 

1. 搜索并选择要从 Azure AD 租户中删除的用户。 例如，_Mary Parker_。

1. 选择“删除用户”  。

    ![“用户 - 所有用户”页，其中突出显示了“删除用户”](media/add-users-azure-active-directory/delete-user-all-users-blade.png)

用户将被删除并且不再显示在“用户 - 所有用户”页上。  可在接下来的 30 天内于“已删除用户”页查看该用户，在此期间可将其还原  。 有关还原用户的详细信息，请参阅[使用 Azure Active Directory 还原或永久删除最近删除的用户](active-directory-users-restore.md)。

删除某个用户后，该用户使用的任何许可证可供其他用户使用。

>[!Note]
>若要更新其授权来源为 Windows Server Active Directory 的用户的标识、联系信息或工作信息，必须使用 Windows Server Active Directory。 完成更新后，必须等待下一个同步周期完成才能看到更改。

## <a name="next-steps"></a>后续步骤

添加用户后，可以执行以下基本流程：

- [添加或更改个人资料信息](active-directory-users-profile-azure-portal.md)

- [向用户分配角色](active-directory-users-assign-role-azure-portal.md)

- [创建基本组并添加成员](active-directory-groups-create-azure-portal.md)

- [使用动态组和用户](../enterprise-users/groups-create-rule.md)

也可执行其他用户管理任务，例如[从其他目录添加来宾用户](../external-identities/what-is-b2b.md)或[还原已删除的用户](active-directory-users-restore.md)。 有关其他可用操作的详细信息，请参阅 [Azure Active Directory 用户管理和文档](../enterprise-users/index.yml)。