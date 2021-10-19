---
title: 管理你的 Azure Active Directory B2C
titleSuffix: Azure Active Directory B2C
description: 了解如何管理你的 Azure Active Directory B2C 租户。 了解 Azure AD B2C 支持哪些 Azure AD 功能，如何使用管理员角色来管理资源，以及如何将工作帐户和来宾用户添加到 Azure AD B2C 租户中。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 10/04/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 7e364b3ce5231370643f9c9ea730dec22ab9022a
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129615935"
---
# <a name="manage-your-azure-active-directory-b2c-tenant"></a>管理你的 Azure Active Directory B2C 租户

在 Azure Active Directory B2C (Azure AD B2C) 中，租户表示使用者用户的目录。 每个 Azure AD B2C 租户都是独特的，独立于任何其他 Azure AD B2C 租户。 Azure AD B2C 租户不同于你可能已有的 Azure Active Directory 租户。 本文介绍如何管理你的 Azure AD B2C 租户。

## <a name="prerequisites"></a>先决条件
- 如果尚未创建自己的 [Azure AD B2C 租户](tutorial-create-tenant.md)，请立即创建一个。 可以使用现有的 Azure AD B2C 租户。
- 了解 [Azure AD B2C 中的用户帐户](user-overview.md)。
- 了解[控制资源访问的用户角色](roles-resource-access-control.md)。


## <a name="add-an-administrator-work-account"></a>添加管理员（工作帐户）

若要创建新的管理帐户，请执行以下步骤：

1. 使用“全局管理员”或“特权角色管理员”权限登录到 [Azure 门户](https://portal.azure.com/)。
1. 请确保使用的是包含 Azure AD B2C 租户的目录。 在门户工具栏中选择“目录 + 订阅”图标。
1. 在“门户设置 | 目录+订阅”页上的“目录名称”列表中找到你的 Azure AD B2C 目录，然后选择“切换”。
1. 在“Azure 服务”下，选择“Azure AD B2C”。 或者，使用搜索框查找并选择“Azure AD B2C”。
1. 在“管理”下，选择“用户” 。
1. 选择“新建用户”。
1. 在“用户”页上输入此用户的信息： 

   - **Name**。 必需。 新用户的名字和姓氏。 例如，*Mary Parker*。
   - **用户名**。 必需。 新用户的用户名。 例如，`mary@contoso.com`。
     用户名的域部分必须是初始默认域名“\<yourdomainname>.onmicrosoft.com”。
   - **组**。 （可选）可以将用户添加到一个或多个现有组。 也可以在以后将用户添加到组中。 
   - **目录角色**：如果需要用户的 Azure AD 管理权限，则可以将其添加到 Azure AD 角色。 可以将用户分配为全局管理员，或者分配为 Azure AD 中有限的管理员角色中的一个或多个。 有关分配角色的详细信息，请参阅[使用角色控制资源访问](roles-resource-access-control.md)。
   - **工作信息**：可在此处添加有关用户的详细信息，或者以后再添加。 

1. 复制“密码”框中提供的自动生成的密码。  需将此密码提供给用户，供其在首次登录时使用。
1. 选择“创建”  。

现已创建用户并将其添加到 Azure AD B2C 租户。 最好是在 Azure AD B2C 租户中至少有一个原生的工作帐户被分配为全局管理员角色。 可以将此帐户视为一个紧急访问帐户。

## <a name="invite-an-administrator-guest-account"></a>邀请管理员（来宾帐户）

你还可以邀请新的来宾用户管理你的租户。 如果你的组织同时还有 Azure AD，则来宾帐户是首选选项，因为可以从外部管理此标识的生命周期。 

若要邀请用户，请执行以下步骤：

1. 使用“全局管理员”或“特权角色管理员”权限登录到 [Azure 门户](https://portal.azure.com/)。
1. 请确保使用的是包含 Azure AD B2C 租户的目录。 在门户工具栏中选择“目录 + 订阅”图标。
1. 在“门户设置 | 目录+订阅”页上的“目录名称”列表中找到你的 Azure AD B2C 目录，然后选择“切换”。
1. 在“Azure 服务”下，选择“Azure AD B2C”。 或者，使用搜索框查找并选择“Azure AD B2C”。
1. 在“管理”下，选择“用户” 。
1. 选择“新来宾帐户”。
1. 在“用户”页上输入此用户的信息： 

   - **Name**。 必需。 新用户的名字和姓氏。 例如，*Mary Parker*。
   - 电子邮件地址。 必需。 你要邀请的用户的电子邮件地址。 例如，`mary@contoso.com`。   
   - 个人消息：添加将包含在邀请电子邮件中的个人消息。
   - **组**。 （可选）可以将用户添加到一个或多个现有组。 也可以在以后将用户添加到组中。
   - **目录角色**：如果需要用户的 Azure AD 管理权限，则可以将其添加到 Azure AD 角色。 可以将用户分配为全局管理员，或者分配为 Azure AD 中有限的管理员角色中的一个或多个。 有关分配角色的详细信息，请参阅[使用角色控制资源访问](roles-resource-access-control.md)。
   - **工作信息**：可在此处添加有关用户的详细信息，或者以后再添加。

1. 选择“创建”。

将向用户发送邀请电子邮件。 用户需要接受邀请才能登录。

### <a name="resend-the-invitation-email"></a>重新发送邀请电子邮件

如果来宾未收到邀请电子邮件，或邀请已过期，你可以重新发送邀请。 作为邀请电子邮件的替代方法，你可以为来宾提供直接链接以接受邀请。 若要重新发送邀请并获取直接链接，请执行以下操作：

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 请确保使用的是包含 Azure AD B2C 租户的目录。 在门户工具栏中选择“目录 + 订阅”图标。
1. 在“门户设置 | 目录+订阅”页上的“目录名称”列表中找到你的 Azure AD B2C 目录，然后选择“切换”。
1. 在“Azure 服务”下，选择“Azure AD B2C”。 或者，使用搜索框查找并选择“Azure AD B2C”。
1. 在“管理”下，选择“用户” 。
1. 搜索并选择要向其重新发送邀请的用户。
1. 在“用户 | 个人资料”页上的“标识”下，选择“(管理)”。
        ![屏幕截图显示如何重新发送来宾帐户邀请电子邮件。](./media/tenant-management/guest-account-resend-invite.png)

1. 对于“重新发送邀请?”，请选择“是”。 当系统显示“是否确定要重新发送邀请?”时，选择“是”。
1. Azure AD B2C 将发送邀请。 你还可以复制邀请 URL，并将其直接提供给来宾。
    
    ![屏幕截图显示如何获取邀请 URL。](./media/tenant-management/guest-account-invitation-url.png)  
 
## <a name="add-a-role-assignment"></a>添加角色分配

你可以在[创建用户](#add-an-administrator-work-account)或[邀请来宾用户](#invite-an-administrator-guest-account)时分配角色。 可以为用户添加角色、更改角色或删除角色：

1. 使用“全局管理员”或“特权角色管理员”权限登录到 [Azure 门户](https://portal.azure.com/)。
1. 请确保使用的是包含 Azure AD B2C 租户的目录。 在门户工具栏中选择“目录 + 订阅”图标。
1. 在“门户设置 | 目录+订阅”页上的“目录名称”列表中找到你的 Azure AD B2C 目录，然后选择“切换”。
1. 在“Azure 服务”下，选择“Azure AD B2C”。 或者，使用搜索框查找并选择“Azure AD B2C”。
1. 在“管理”下，选择“用户” 。
1. 选择要更改其角色的用户。 然后选择“已分配的角色”。
1. 选择“添加分配”，再选择要分配的角色（例如“应用程序管理员”），然后选择“添加”。

## <a name="remove-a-role-assignment"></a>删除角色分配

如果你需要删除用户角色分配，请执行以下步骤：

1. 选择“Azure AD B2C”，再选择“用户”，然后搜索并选择用户。
1. 选择“分配的角色”。 选择要删除的角色，例如“应用程序管理员”，然后选择“删除分配”。

## <a name="review-administrator-account-role-assignments"></a>审阅管理员帐户角色分配

在审核过程中，通常会检查为哪些用户分配了 Azure AD B2C 目录中的特定角色。 使用以下步骤审核当前为哪些用户分配了特权角色。

1. 使用“全局管理员”或“特权角色管理员”权限登录到 [Azure 门户](https://portal.azure.com/)。
1. 请确保使用的是包含 Azure AD B2C 租户的目录。 在门户工具栏中选择“目录 + 订阅”图标。
1. 在“门户设置 | 目录+订阅”页上的“目录名称”列表中找到你的 Azure AD B2C 目录，然后选择“切换”。
1. 在“Azure 服务”下，选择“Azure AD B2C”。 或者，使用搜索框查找并选择“Azure AD B2C”。
1. 在“管理”下，选择“角色和管理员”。 
1. 选择一个角色，如“全局管理员”。 “角色 | 分配”页将列出具有该角色的用户。

## <a name="delete-an-administrator-account"></a>删除管理员帐户

若要删除现有用户，必须具有“全局管理员”角色分配。 全局管理员可以删除任何用户，包括其他管理员。 用户管理员可以删除任何非管理员用户。

1. 在 Azure AD B2C 目录中选择“用户”，然后选择要删除的用户。
1. 选择“删除”，然后选择“是”，确认进行删除。

用户将被删除并且不再显示在“用户 - 所有用户”页上。  可在接下来的 30 天内于“已删除用户”页查看该用户，在此期间可将其还原  。 有关还原用户的详细信息，请参阅[使用 Azure Active Directory 还原或永久删除最近删除的用户](../active-directory/fundamentals/active-directory-users-restore.md)。

## <a name="protect-administrative-accounts"></a>保护管理帐户

建议使用多重身份验证 (MFA) 保护所有管理员帐户，以获得更高的安全性。 MFA 是登录过程中的一个身份验证过程，它提示用户提供更多形式的标识，例如他们的移动设备上的验证码或 Microsoft Authenticator 应用中的请求。

![登录屏幕截图上使用的身份验证方法](./media/tenant-management/sing-in-with-multi-factor-authentication.png)

可以启用 [Azure AD 安全默认值](../active-directory/fundamentals/concept-fundamentals-security-defaults.md)，以强制所有管理帐户均使用 MFA。

## <a name="get-your-tenant-name"></a>获取租户名称

若要获取 Azure AD B2C 租户名称，请执行以下步骤：

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 请确保使用的是包含 Azure AD B2C 租户的目录。 在门户工具栏中选择“目录 + 订阅”图标。
1. 在“门户设置 | 目录+订阅”页上的“目录名称”列表中找到你的 Azure AD B2C 目录，然后选择“切换”。
1. 在 Azure 门户中，搜索并选择“Azure AD B2C”。
1. 在“概述”中，复制“域名” 。

![屏幕截图演示如何获取 Azure AD B2C 租户名称。](./media/tenant-management/get-azure-ad-b2c-tenant-name.png)  

## <a name="get-your-tenant-id"></a>获取租户 ID

若要获取 Azure AD B2C 租户 ID，请执行以下步骤：

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 请确保使用的是包含 Azure AD B2C 租户的目录。 在门户工具栏中选择“目录 + 订阅”图标。
1. 在“门户设置 | 目录+订阅”页上的“目录名称”列表中找到你的 Azure AD B2C 目录，然后选择“切换”。
1. 在 Azure 门户中，搜索并选择“Azure Active Directory”。
1. 在“概述”中，复制“租户 ID” 。

![屏幕截图演示如何获取 Azure AD B2C 租户 ID。](./media/tenant-management/get-azure-ad-b2c-tenant-id.png)  

## <a name="next-steps"></a>后续步骤

- [清理资源并删除租户](tutorial-delete-tenant.md)
