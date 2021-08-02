---
title: 限制来宾用户访问权限 - Azure Active Directory | Microsoft Docs
description: 使用 Azure 门户、PowerShell 或 Microsoft Graph 在 Azure Active Directory 中限制来宾用户访问权限
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 06/01/2021
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: f1b9ceb41300d8a53d192602812f1e3c04017609
ms.sourcegitcommit: eb20dcc97827ef255cb4ab2131a39b8cebe21258
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/03/2021
ms.locfileid: "111371456"
---
# <a name="restrict-guest-access-permissions-in-azure-active-directory"></a>在 Azure Active Directory 中限制来宾访问权限

Azure Active Directory (Azure AD) 允许你在 Azure AD 中限制外部来宾用户可以在其组织中看到的内容。 默认情况下，系统会在 Azure AD 中为来宾用户设置有限的权限级别，而为成员用户的默认设置是完整的用户权限集。 这是 Azure AD 组织的外部协作设置中新来宾用户权限级别，该级别提供限制性更强的访问权限，因此，来宾访问级别为：

权限级别         | 访问级别 | 值
----------------         | ------------ | -----
与成员用户相同     | 来宾与成员用户对 Azure AD 资源的访问权限相同 | a0b1b346-4d3e-4e8b-98f8-753987be4970
有限的访问权限（默认设置） | 来宾可以查看所有非隐藏组的成员身份 | 10dae51f-b6af-4016-8d66-8c2a99b929b3
**受限的访问权限（新增）**  | **来宾不能查看任何组的成员身份** | **2af84b1e-32c8-42b7-82bc-daa82404023b**

当来宾访问权限受限时，来宾只能查看其自己的用户配置文件。 即使来宾按用户主体名称或 objectId 搜索，也不允许其查看其他用户。 受限访问权限还限制来宾用户查看其所在组的成员身份。 有关总体的默认用户权限（包括来宾用户权限）的详细信息，请参阅 [Azure Active Directory 中的默认用户权限是什么？](../fundamentals/users-default-permissions.md)。

## <a name="permissions-and-licenses"></a>权限和许可证

必须是“全局管理员”或“特权角色管理员”角色才能配置来宾用户访问权限。 限制来宾访问权限时无额外的许可要求。

## <a name="update-in-the-azure-portal"></a>Azure 门户中的更新

我们对 Azure 门户中针对来宾用户权限的现有控件进行了更改。

1. 使用全局管理员权限登录到 [Azure AD 管理中心](https://aad.portal.azure.com)。
1. 在你的组织的“Azure Active Directory”概览页上，选择“用户设置”。
1. 在“外部用户”下，选择“管理外部协作设置”。  
1. 在“外部协作设置”页上，选择“来宾用户访问权限限制为其自己的目录对象的属性和成员身份”选项。

    ![Azure AD 外部协作设置页面](./media/users-restrict-guest-permissions/external-collaboration-settings.png)

1. 选择“保存”。 对于来宾用户，更改可能需要长达 15 分钟的时间才能生效。

## <a name="update-with-the-microsoft-graph-api"></a>通过 Microsoft Graph API 进行更新

我们添加了新的 Microsoft Graph API 来配置 Azure AD 组织中的来宾权限。 可以进行以下 API 调用来分配任何权限级别。 此处使用的 guestUserRoleId 的值用来说明限制性最强的来宾用户设置。 若要详细了解如何使用 Microsoft Graph 设置来宾权限，请参阅 [authorizationPolicy 资源类型](/graph/api/resources/authorizationpolicy)。

### <a name="configuring-for-the-first-time"></a>首次配置

````PowerShell
POST https://graph.microsoft.com/beta/policies/authorizationPolicy/authorizationPolicy

{
  "guestUserRoleId": "2af84b1e-32c8-42b7-82bc-daa82404023b"
}
````

响应应当是 Success 204。

### <a name="updating-the-existing-value"></a>更新现有值

````PowerShell
PATCH https://graph.microsoft.com/beta/policies/authorizationPolicy/authorizationPolicy

{
  "guestUserRoleId": "2af84b1e-32c8-42b7-82bc-daa82404023b"
}
````

响应应当是 Success 204。

### <a name="view-the-current-value"></a>查看当前值

````PowerShell
GET https://graph.microsoft.com/beta/policies/authorizationPolicy/authorizationPolicy
````

示例响应:

````PowerShell
{
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#policies/authorizationPolicy/$entity",
    "id": "authorizationPolicy",
    "displayName": "Authorization Policy",
    "description": "Used to manage authorization related settings across the company.",
    "enabledPreviewFeatures": [],
    "guestUserRoleId": "10dae51f-b6af-4016-8d66-8c2a99b929b3",
    "permissionGrantPolicyIdsAssignedToDefaultUserRole": [
        "user-default-legacy"
    ]
}
````

## <a name="update-with-powershell-cmdlets"></a>使用 PowerShell cmdlet 进行更新

对于此功能，我们添加了通过 PowerShell v2 cmdlet 配置受限权限的功能。 Get 和 Set PowerShell cmdlet 已在版本 2.0.2.85 中发布。

### <a name="get-command-get-azureadmsauthorizationpolicy"></a>Get 命令：Get-AzureADMSAuthorizationPolicy

示例：

````PowerShell
PS C:\WINDOWS\system32> Get-AzureADMSAuthorizationPolicy

Id                                                : authorizationPolicy
OdataType                                         :
Description                                       : Used to manage authorization related settings across the company.
DisplayName                                       : Authorization Policy
EnabledPreviewFeatures                            : {}
GuestUserRoleId                                   : 10dae51f-b6af-4016-8d66-8c2a99b929b3
PermissionGrantPolicyIdsAssignedToDefaultUserRole : {user-default-legacy}
````

### <a name="set-command-set-azureadmsauthorizationpolicy"></a>Set 命令：Set-AzureADMSAuthorizationPolicy

示例：

````PowerShell
PS C:\WINDOWS\system32> Set-AzureADMSAuthorizationPolicy -GuestUserRoleId '2af84b1e-32c8-42b7-82bc-daa82404023b'
````

> [!NOTE]
> 当要求输入时，必须输入 authorizationPolicy 作为 ID。

## <a name="supported-microsoft-365-services"></a>支持的 Microsoft 365 服务

### <a name="supported-services"></a>支持的服务

所谓支持，是指体验符合预期；具体而言，它与当前的来宾体验相同。

- Teams
- Outlook (OWA)
- SharePoint
- Teams 中的 Planner
- Planner Web 应用

### <a name="services-currently-not-supported"></a>当前不支持的服务

目前不受支持的服务可能与新的来宾限制设置存在兼容性问题。

- 窗体
- Planner 移动应用
- Project
- Yammer

## <a name="frequently-asked-questions-faq"></a>常见问题解答 (FAQ)

问题 | Answer
-------- | ------
这些权限在何处适用？ | 这些目录级权限跨 Azure AD 服务和门户（包括 Microsoft Graph、PowerShell v2、Azure 门户和“我的应用”门户）强制执行。 利用 Microsoft 365 组执行协作方案的 Microsoft 365 服务（特别是 Outlook、Microsoft Teams 和 SharePoint）也会受到影响。
受限权限如何影响来宾可以查看的组？ | 无论是默认来宾权限还是受限来宾权限，来宾都不能枚举组或用户列表。 来宾可以在 Azure 门户和“我的应用”门户中查看他们所属的组，具体取决于相关权限：<li>**默认权限**：要在 Azure 门户中查找自己所属的组，来宾必须在“所有用户”列表中搜索其对象 ID，然后选择“组”。 来宾可在其中查看其所属的组的列表，包括名称和电子邮件等所有组详细信息。 在“我的应用”门户中，他们可以看到自己拥有的组及所属的组的列表。</li><li>**受限来宾权限**：在 Azure 门户中，来宾仍可查找其所属的组的列表，方法是在“所有用户”列表中搜索其对象 ID，然后选择“组”。 它们只能看到有关组（特别是对象 ID）的非常有限的详细信息。 按照设计，“名称”和“电子邮件”列是空白的，并且“组类型”无法识别。 在“我的应用”门户中，来宾无法访问自己拥有的组及所属的组的列表。</li><br>有关来自图形 API 的目录权限的更详细比较，请参阅[默认用户权限](../fundamentals/users-default-permissions.md#member-and-guest-users)。
此功能影响“我的应用”门户的哪些部分？ | “我的应用”门户中的组功能会认可这些新权限。 这包括用来在“我的应用”中查看组列表和组成员身份的所有路径。 未对组磁贴可用性进行任何更改。 组磁贴可用性仍由 Azure 门户中的现有组设置控制。
这些权限是否替代了 SharePoint 或 Microsoft Teams 来宾设置？ | 否。 这些现有设置仍然会控制这些应用程序中的体验和访问权限。 例如，如果你在 SharePoint 中看到问题，请仔细检查你的外部共享设置。
Planner 和 Yammer 中有哪些已知的兼容性问题？ | <li>权限设置为“受限”后，登录到 Planner 移动应用的来宾将无法访问其计划或任何任务。<li>权限设置为“受限”后，登录到 Yammer 的来宾将无法离开组。
我在租户中的现有来宾权限是否会更改？ | 你的当前设置未进行任何更改。 我们保留了与现有设置的后向兼容性。 你可以决定何时进行更改。
是否会默认设置这些权限？ | 否。 现有的默认权限保持不变。 你可以选择将权限设置得更为严格。
此功能是否有任何许可要求？ | 没有，此功能没有新的许可要求。

## <a name="next-steps"></a>后续步骤

- 若要详细了解 Azure AD 中的现有来宾权限，请参阅 [Azure Active Directory 中的默认用户权限是什么？](../fundamentals/users-default-permissions.md)
- 若要查看用于限制来宾访问的 Microsoft Graph API 方法，请参阅 [authorizationPolicy 资源类型](/graph/api/resources/authorizationpolicy)
- 若要撤销用户的所有访问权限，请参阅[在 Azure AD 中撤销用户访问权限](users-revoke-access.md)
