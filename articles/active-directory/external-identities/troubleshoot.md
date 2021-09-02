---
title: B2B 协作故障排除 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory B2B 协作的常见问题的补救措施
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: troubleshooting
ms.date: 07/13/2021
tags: active-directory
ms.author: mimart
author: msmimart
ms.custom:
- it-pro
- seo-update-azuread-jan"
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7068ff38338e92843a957f50f63309412b63b31c
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113759795"
---
# <a name="troubleshooting-azure-active-directory-b2b-collaboration"></a>Azure Active Directory B2B 协作故障排除

以下是 Azure Active Directory (Azure AD) B2B 协作的常见问题的一些补救措施。

   > [!IMPORTANT]
   >
   > - 从 2021 年 7 月 12 日开始，如果 Azure AD B2B 客户设置了新的 Google 集成，将其用于自定义应用程序或业务线应用程序的自助注册，则在身份验证转移到系统 Web 视图之前，无法使用 Google 标识进行身份验证。 [了解详细信息](google-federation.md#deprecation-of-web-view-sign-in-support)。
   > - 从 2021 年 9 月 30 日开始，Google 将[弃用嵌入式 Web 视图登录支持](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html)。 如果你的应用使用嵌入式 Web 视图对用户进行身份验证，而你将 Google 联合身份验证与 [Azure AD B2C](../../active-directory-b2c/identity-provider-google.md) 或 Azure AD B2B 配合使用来进行[外部用户邀请](google-federation.md)或[自助注册](identity-providers.md)，则 Google Gmail 用户将无法进行身份验证。 [了解详细信息](google-federation.md#deprecation-of-web-view-sign-in-support)。
   > - 从 2021 年 10 月起，Microsoft 将不再支持兑换通过创建用于 B2B 协作方案的非托管 Azure AD 帐户和租户进行的邀请。 在准备期间，我们鼓励客户选择加入现已正式发布的[电子邮件一次性密码身份验证](one-time-passcode.md)。

## <a name="ive-added-an-external-user-but-do-not-see-them-in-my-global-address-book-or-in-the-people-picker"></a>我已添加外部用户，但在全局通讯簿或人员选取器中看不到这些用户

在外部用户未填充到列表中的情况下，可能需要几分钟复制对象。

## <a name="a-b2b-guest-user-is-not-showing-up-in-sharepoint-onlineonedrive-people-picker"></a>B2B 来宾用户没有显示在 SharePoint Online/OneDrive 人员选取器中

默认情况下，搜索现有来宾用户的功能在 SharePoint Online (SPO) 人员选取器中处于“关闭”状态以匹配旧行为。

可使用“ShowPeoplePickerSuggestionsForGuestUsers”设置在租户和网站集级别启用此功能。 可使用 Set-SPOTenant 和 Set-SPOSite cmdlet 设置此功能，这将允许用户搜索目录中的所有现有来宾用户。 租户范围中的更改不会影响已经预配的 SPO 站点。

## <a name="my-guest-invite-settings-and-domain-restrictions-arent-being-respected-by-sharepoint-onlineonedrive"></a>SharePoint Online/OneDrive 未遵守我的来宾邀请设置和域限制

默认情况下，SharePoint Online 和 OneDrive 有其自己的一组外部用户选项，不使用 Azure AD 的设置。  需要启用 [SharePoint 和 OneDrive 与 Azure AD B2B 的集成](/sharepoint/sharepoint-azureb2b-integration-preview)，以确保选项在那些应用程序中一致。
## <a name="invitations-have-been-disabled-for-directory"></a>已对目录禁用邀请

如果收到无权邀请用户的通知，请在“Azure Active Directory”>“用户设置”>“外部用户”>“管理外部协作设置”下验证你的用户帐户是否有权邀请外部用户：

![显示了”外部用户”设置的屏幕截图](media/troubleshoot/external-user-settings.png)

如果最近修改了这些设置或为用户分配了“来宾邀请者”角色，可能有 15-60 分钟的延迟更改才生效。

## <a name="the-user-that-i-invited-is-receiving-an-error-during-redemption"></a>我邀请的用户在兑换过程中收到错误

常见错误包括：

### <a name="invitees-admin-has-disallowed-emailverified-users-from-being-created-in-their-tenant"></a>当发生以下情况时，被邀请者的管理员禁止在其租户中创建电子邮件验证的用户

受邀用户所在组织正在使用 Azure Active Directory，但其中不存在特定用户帐户（例如，用户不存在于 AAD contoso.comAzure AD contoso.com 中）。 contoso.com 的管理员可能会设置一个策略以阻止创建用户。 用户必须向其管理员进行核实以确定是否允许外部用户。 外部用户的管理员可能需要在其域中允许电子邮件验证的用户（请参阅有关允许电子邮件验证的用户的此[文章](/powershell/module/msonline/set-msolcompanysettings)）。

![错误，指出租户不允许经电子邮件验证的用户](media/troubleshoot/allow-email-verified-users.png)

### <a name="external-user-does-not-exist-already-in-a-federated-domain"></a>外部用户尚未存在于联合域中

如果使用联合身份验证，并且用户在 Azure Active Directory 中不存在，则无法邀请该用户。

要解决此问题，外部用户的管理员必须将该用户的帐户同步到 Azure Active Directory。

### <a name="external-user-has-a-proxyaddress-that-conflicts-with-a-proxyaddress-of-an-existing-local-user"></a>外部用户拥有的 proxyAddress 与现有本地用户的 proxyAddress 存在冲突

当我们检查用户能否受邀加入你的租户时，我们检查的其中一项便是 proxyAddress 是否存在冲突。 其中包括用户在其主租户中的任何 proxyAddresses，以及你的租户中本地用户的任何 proxyAddress。 对于外部用户，我们会将电子邮件添加到现有 B2B 用户的 proxyAddress。 对于本地用户，可以要求他们使用其既有帐户进行登录。

## <a name="i-cant-invite-an-email-address-because-of-a-conflict-in-proxyaddresses"></a>由于 proxyAddresses 中的冲突，无法邀请电子邮件地址

当目录中的其他对象具有与它的某个 proxyAddresses 相同的受邀电子邮件地址时，就会发生这种情况。 若要解决此冲突，请从[用户](/graph/api/resources/user?view=graph-rest-1.0&preserve-view=true)对象中删除电子邮件，并删除关联的[联系人](/graph/api/resources/contact?view=graph-rest-1.0&preserve-view=true)对象，然后再次尝试邀请此电子邮件。

## <a name="the-guest-user-object-doesnt-have-a-proxyaddress"></a>来宾用户对象没有 proxyAddress

在邀请外部来宾用户时，有时此操作会与现有的[联系人对象](/graph/api/resources/contact?view=graph-rest-1.0&preserve-view=true)冲突。 在出现这种情况时，就会创建没有 proxyAddress 的来宾用户。 这意味着用户将无法使用[实时兑换](redemption-experience.md#redemption-through-a-direct-link)或[电子邮件一次性密码身份验证](one-time-passcode.md#user-experience-for-one-time-passcode-guest-users)来兑换此帐户。

## <a name="how-does--which-is-not-normally-a-valid-character-sync-with-azure-ad"></a>“\#”（这通常不是有效字符）如何与 Azure AD 进行同步？

由于受邀帐户 user@contoso.com 变为 user_contoso.com#EXT#@fabrikam.onmicrosoft.com，“\#”是 Azure AD B2B 协作或外部用户的 UPN 中的保留字符。 因此，不允许来自本地的 UPN 中的 \# 登录到 Azure 门户。 

## <a name="i-receive-an-error-when-adding-external-users-to-a-synchronized-group"></a>我将外部用户添加到同步组时，收到错误

外部用户只能添加到“已分配”或“安全”组，而不能分配到在本地控制的组。

## <a name="my-external-user-did-not-receive-an-email-to-redeem"></a>我的外部用户未收到用于兑换的电子邮件

被邀请者应该向其 ISP 或垃圾邮件筛选器查询，以确保允许以下地址：Invites@microsoft.com

> [!NOTE]
>
> - 对于中国世纪互联运营的 Azure 服务，发送方地址是 Invites@oe.21vianet.com。
> - 对于 Azure AD 政府云，发送方地址为 invites@azuread.us。

## <a name="i-notice-that-the-custom-message-does-not-get-included-with-invitation-messages-at-times"></a>我发现邀请消息有时不包含自定义消息

为遵守隐私法规，在以下情况下，API 不会在电子邮件邀请中包含自定义邮件：

- 邀请方在邀请租户中没有电子邮件地址
- 应用服务主体发送邀请

如果此方案非常重要，则可取消 API 邀请电子邮件，并通过所选的电子邮件机制发送邮件。 请咨询所属组织的法律顾问，确保通过这种方式发送的任何电子邮件均符合隐私法规。

## <a name="you-receive-an-aadsts65005-error-when-you-try-to-log-in-to-an-azure-resource"></a>尝试登录到 Azure 资源时收到“AADSTS65005”错误

具有来宾帐户的某个用户无法登录，并收到以下错误消息：

```plaintext
    AADSTS65005: Using application 'AppName' is currently not supported for your organization contoso.com because it is in an unmanaged state. An administrator needs to claim ownership of the company by DNS validation of contoso.com before the application AppName can be provisioned.
```

该用户具有 Azure 用户帐户，是已被放弃的或非托管的病毒性租户。 此外，租户中没有全局管理员。

若要解决此问题，你必须接管被放弃的租户。 请参阅[在 Azure Active Directory 中以管理员身份接管非管理的目录](../enterprise-users/domains-admin-takeover.md)。 你还必须访问相关域后缀的面向 Internet 的 DNS，以便提供直接证据来证明你控制着该命名空间。 在租户返回到托管状态后，请与客户讨论保留用户和经验证的域名是否是其组织的最佳选择。

## <a name="a-guest-user-with-a-just-in-time-or-viral-tenant-is-unable-to-reset-their-password"></a>具有实时或“病毒性”租户的来宾用户无法重置其密码

如果标识租户是实时 (JIT) 或病毒性租户（独立的不受管 Azure 租户），则只有来宾用户可以重置其密码。 有时，组织将[接管在员工使用其工作电子邮件地址注册服务时创建的病毒性租户的管理](../enterprise-users/domains-admin-takeover.md)。 组织接管病毒性租户后，只有该组织中的管理员可以重置用户密码或启用 SSPR。 如果需要，作为邀请方组织，你可以从目录中删除来宾用户帐户并重新发送邀请。

## <a name="a-guest-user-is-unable-to-use-the-azuread-powershell-v1-module"></a>来宾用户无法使用 AzureAD PowerShell V1 模块

从 2019 年 11 月 18 日开始，你的目录中的来宾用户（所定义的其 userType  属性为 Guest  的用户帐户）被系统阻止使用 AzureAD PowerShell V1 模块。 之后，用户需要成为成员用户（userType  为 Member  ）或使用 AzureAD PowerShell V2 模块。

## <a name="in-an-azure-us-government-tenant-i-cant-invite-a-b2b-collaboration-guest-user"></a>在 Azure 美国政府租户中，我无法邀请 B2B 协作来宾用户

在 Azure 美国政府云中，当前仅支持同时在 Azure 美国政府云中且同时支持 B2B 协作的租户之间进行 B2B 协作。 如果你邀请的用户不在属于 Azure 美国政府云的租户中，或者不支持 B2B 协作，则会出现错误。 有关详细信息和限制，请参阅 [Azure Active Directory Premium P1 和 P2 变体](../../azure-government/compare-azure-government-global-azure.md#azure-active-directory-premium-p1-and-p2)。

## <a name="i-receive-the-error-that-azure-ad-cannot-find-the-aad-extensions-app-in-my-tenant"></a>我收到 Azure AD 在我的租户中找不到 aad-extensions-app 的错误

使用自助注册功能（如自定义用户属性或用户流）时，会自动创建一个名为 `aad-extensions-app. Do not modify. Used by AAD for storing user data.` 的应用。 Azure AD 外部标识使用此应用存储注册的用户和收集的自定义属性的相关信息。

如果将 `aad-extensions-app` 意外删除，则你有 30 天的时间可以恢复它。 你可以使用 Azure AD PowerShell 模块恢复此应用。

1. 启动 Azure AD PowerShell 模块并运行 `Connect-AzureAD`。
1. 以要为其恢复已删除应用的 Azure AD 租户的全局管理员身份登录。
1. 运行 PowerShell 命令 `Get-AzureADDeletedApplication`。
1. 在列表中找到显示名称以 `aad-extensions-app` 开头的应用程序，并复制其 `ObjectId` 属性值。
1. 运行 PowerShell 命令 `Restore-AzureADDeletedApplication -ObjectId {id}`。 将此命令的 `{id}` 部分替换为之前步骤中的 `ObjectId`。

现在应在 Azure 门户中看到恢复的应用。

## <a name="a-guest-user-was-invited-successfully-but-the-email-attribute-is-not-populating"></a>已成功邀请来宾用户，但电子邮件属性未填充

假设你无意中邀请了一位电子邮件地址与目录中已有的用户对象匹配的来宾用户。 系统将创建来宾用户对象，但电子邮件地址将添加到 `otherMail` 属性，而不是添加到 `mail` 或 `proxyAddresses` 属性。 若要避免此问题，可以使用以下 PowerShell 步骤在 Azure AD 目录中搜索存在冲突的用户对象：

1. 打开 Azure AD PowerShell 模块并运行 `Connect-AzureAD`。
1. 以要在其中检查重复联系人对象的 Azure AD 租户的全局管理员身份登录。
1. 运行 PowerShell 命令 `Get-AzureADContact -All $true | ? {$_.ProxyAddresses -match 'user@domain.com'}`。
1. 运行 PowerShell 命令 `Get-AzureADContact -All $true | ? {$_.Mail -match 'user@domain.com'}`。

## <a name="next-steps"></a>后续步骤

[获取对 B2B 协作的支持](../fundamentals/active-directory-troubleshooting-support-howto.md)
