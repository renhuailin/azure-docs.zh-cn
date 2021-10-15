---
title: 将 Google 添加为 B2B 的标识提供者 - Azure AD
description: 与 Google 联合，使来宾用户能够使用其自己的 Gmail 帐户登录到你的 Azure AD 应用。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 10/01/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan, has-adal-ref
ms.collection: M365-identity-device-management
ms.openlocfilehash: 510e2207a2c25c5da5f4de08f3bf16fbf6cf4c7d
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129354298"
---
# <a name="add-google-as-an-identity-provider-for-b2b-guest-users"></a>将 Google 添加为 B2B 来宾用户的标识提供者

通过设置 Google 联合身份验证，可让受邀的用户使用其自己的 Gmail 帐户登录到你的共享应用和资源，而无需创建 Microsoft 帐户。 将 Google 添加为应用程序的登录选项之一后，在“登录”页面上，用户只需输入他们用于登录 Google 的 Gmail 地址即可。

![Google 用户的登录选项](media/google-federation/sign-in-with-google-overview.png)

> [!NOTE]
> Google 联合专为 Gmail 用户设计。 若要与 Google Workspace 域联合，请使用 [SAML/WS-Fed 标识提供者联合](direct-federation.md)。

> [!IMPORTANT]
>
> - 从 2021 年 7 月 12 日开始，如果 Azure AD B2B 客户设置了新的 Google 集成，将其用于自定义应用程序或业务线应用程序的自助注册或用于邀请外部用户，则会为 Gmail 用户阻止身份验证（错误屏幕显示在[预期内容](#what-to-expect)中）。 仅当在 2021 年 7 月 12 日之后为自助注册用户流或邀请创建 Google 集成或业务线应用程序未移至系统 Web 视图时，才发生此问题。 由于默认情况下启用了系统 Web 视图，因此大多数应用不会受到影响。 为了避免此问题，我们强烈建议你在为自助注册创建任何新的 Google 集成之前，将 Gmail 身份验证移至系统浏览器。 请参阅[嵌入式 Web 视图所需的操作](#action-needed-for-embedded-frameworks)。
> - 从 2021 年 9 月 30 日开始，Google 将[弃用 Web 视图登录支持](https://developers.googleblog.com/2021/06/upcoming-security-changes-to-googles-oauth-2.0-authorization-endpoint.html)。 如果你的应用使用嵌入式 Web 视图对用户进行身份验证，而你将 Google 联合身份验证与 [Azure AD B2C](../../active-directory-b2c/identity-provider-google.md) 或 Azure AD B2B 配合使用来进行外部用户邀请或[自助注册](identity-providers.md)，则 Google Gmail 用户将无法进行身份验证。 [了解详细信息](#deprecation-of-web-view-sign-in-support)。

## <a name="what-is-the-experience-for-the-google-user"></a>Google 用户体验是什么？

当 Google 用户兑换邀请时，其体验因是否已登录到 Google 而异：

- 系统将提示未登录到 Google 的来宾用户执行此操作。
- 系统将提示已登录 Google 的来宾用户选择他们要使用的帐户。 他们必须选择你在邀请他们时所用的帐户。

如果来宾用户看到“标头过长”错误，可以清除其 Cookie，或者打开私密或 Incognito 窗口并再次尝试登录。

![显示 Google 登录页面屏幕截图。](media/google-federation/google-sign-in.png)

## <a name="sign-in-endpoints"></a>登录终结点

Google 来宾用户现在可以使用[公共终结点](redemption-experience.md#redemption-and-sign-in-through-a-common-endpoint)（也就是不包含租户上下文的常规应用 URL）登录到多租户或 Microsoft 第一方应用。 在登录过程中，来宾用户选择“登录选项”，然后选择“登录到组织” 。 然后，用户需要键入组织的名称并继续使用其 Google 凭据登录。

Google 来宾用户还可以使用包含租户信息的应用程序终结点，例如：

  * `https://myapps.microsoft.com/?tenantid=<your tenant ID>`
  * `https://myapps.microsoft.com/<your verified domain>.onmicrosoft.com`
  * `https://portal.azure.com/<your tenant ID>`

还可以通过添加租户信息，为 Google 来宾用户提供指向应用程序或资源的直接链接，例如 `https://myapps.microsoft.com/signin/Twitter/<application ID?tenantId=<your tenant ID>`。

## <a name="deprecation-of-web-view-sign-in-support"></a>弃用 web-view 登录支持

从 2021 年 9 月 30 日开始，Google 将[弃用嵌入式 Web 视图登录支持](https://developers.googleblog.com/2021/06/upcoming-security-changes-to-googles-oauth-2.0-authorization-endpoint.html)。 如果你的应用使用嵌入式 Web 视图对用户进行身份验证，而你将 Google 联合身份验证与 [Azure AD B2C](../../active-directory-b2c/identity-provider-google.md) 或 Azure AD B2B 配合使用来进行[外部用户邀请](google-federation.md)或[自助注册](identity-providers.md)，则 Google Gmail 用户将无法进行身份验证。

以下是将影响 Gmai l用户的已知方案：
- Windows 上的 Microsoft 应用（例如 Teams 和 Power Apps） 
- 使用 [WebView](/windows/communitytoolkit/controls/wpf-winforms/webview) 控件、[WebView2](/microsoft-edge/webview2/) 或陈旧的 WebBrowser 控件进行身份验证的 Windows 应用。 这些应用应迁移到使用 Web 帐户管理器 (WAM) 流。
- 使用 WebView UI 元素的 Android 应用程序 
- 使用 UIWebView/WKWebview 的 iOS 应用程序 
- [使用 ADAL 的应用](../develop/howto-get-list-of-all-active-directory-auth-library-apps.md)

此更改不会影响：
- Web 应用
- 通过网站访问的 Microsoft 365 服务（例如 SharePoint Online、Office Web Apps 和 Teams Web 应用）
- 使用系统 web-view 进行身份验证的移动应用（iOS 上的 [SFSafariViewController](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller)、Android 上的[自定义标签](https://developer.chrome.com/docs/android/custom-tabs/overview/)）。  
- Google Workspace 标识，例如，将[基于 SAML 的联合](direct-federation.md)与 Google Workspace 结合使用时
- 使用 Web 帐户管理器(WAM)或 Web 身份验证代理 (WAB) 的 Windows 应用。  

### <a name="action-needed-for-embedded-web-views"></a>嵌入式 web-view 所需的操作

修改应用以使用系统浏览器进行登录。 有关详细信息，请参阅 MSAL.NET 文档中的[嵌入式与系统 Web UI](../develop/msal-net-web-browsers.md#embedded-vs-system-web-ui)。 默认情况下，所有 MSAL SDK 都使用系统 web-view。

### <a name="what-to-expect"></a>期望

从 9 月 30 日开始，Microsoft 将全局推出一个设备登录流，作为解决方法提供给仍在使用嵌入式 Web 视图的应用，以确保不会阻止身份验证。

### <a name="how-to-sign-in-with-the-device-sign-in-flow"></a>如何使用设备登录流登录

设备登录流会提示在嵌入式 Web 视图中使用 Gmail 帐户登录的用户在单独的浏览器中输入代码，然后才能完成登录。 如果用户在浏览器中没有活动会话的情况下首次使用其 Gmail 帐户登录，他们将在屏幕上看到以下内容。 如果现有的 Gmail 帐户已登录，则其中一些步骤可能会取消。

1. 在“登录”屏幕上，用户输入其 Gmail 地址，然后选择“下一步”。 

   ![显示登录屏幕的屏幕截图](media/google-federation/1-sign-in.png)

1. 出现以下屏幕，提示用户打开新窗口，导航到 https://microsoft.com/devicelogin ，然后输入显示的 9 位数字母数字代码。

   ![显示 9 位数代码的屏幕截图](media/google-federation/2-sign-in-code.png)

1. 此操作将打开设备登录页面，用户可以在此页面中输入代码。 

   ![显示设备登录页面的屏幕截图](media/google-federation/3-enter-code.png)

1. 如果代码匹配，出于安全目的，系统会要求用户重新输入其电子邮件以确认其应用和登录位置。

   ![显示重新输入电子邮件的屏幕截图](media/google-federation/4-sign-in-reenter-email.png)

1. 用户使用其电子邮件和密码登录 Google。

   ![显示 Google 登录屏幕的屏幕截图](media/google-federation/5-sign-in-with-google.png)

1. 系统将再次要求用户确认要登录的应用。

   ![显示应用程序确认屏幕的屏幕截图](media/google-federation/6-confirm-sign-in.png)

1. 用户选择“继续”。 提示确认用户已登录。 用户关闭选项卡或窗口，并返回到第一个屏幕，此时用户已登录到应用。

   ![显示登录确认的屏幕截图](media/google-federation/7-app-sign-in-confirmed.png)

或者，现有 Gmail 用户和新 Gmail 用户可以使用电子邮件一次性密码进行登录。 若要让 Gmail 用户使用电子邮件一次性密码，请执行以下操作：

1. [启用电子邮件一次性密码](one-time-passcode.md#enable-email-one-time-passcode)
2. [删除 Google 联合身份验证](google-federation.md#how-do-i-remove-google-federation)
3. [重置 Gmail 用户的兑换状态](reset-redemption-status.md)，以便他们今后可以使用电子邮件一次性密码。

如果要请求延期，受影响的（一或多个）OAuth 客户端 ID 的客户应该已收到来自 Google Developers 的电子邮件，其中包含有关一次性政策实施延期的信息，该延期必须在 2022 年 1 月 31 日之前结束，具体内容如下：

- “如有必要，你可以为列出的每个 OAuth 客户端 ID 请求 **适用于嵌入式 Web 视图的政策实施延期**，延期是一次性的，至 2022 年 1 月 31 日结束。 为清楚起见，适用于嵌入式 Web 视图的政策将于 2022 年 2 月 1 日起实施，没有任何例外或延期。”

迁移到允许使用 web-view 进行身份验证的应用程序将不会受到影响，并且像往常一样，将不允许用户通过 Google 进行身份验证。

如果应用程序未迁移到允许 web-view 进行身份验证，则受影响的 Gmail 用户将看到以下屏幕。

![Google 登录错误（如果应用没有迁移到系统浏览器）](media/google-federation/google-sign-in-error-ewv.png)

### <a name="distinguishing-between-cefelectron-and-embedded-web-views"></a>区分 CEF/Electron 和嵌入式 web-view

除了[弃用嵌入式 web-view 和框架登录支持](#deprecation-of-web-view-sign-in-support)之外，Google 还将[弃用基于 Chromium 嵌入式框架 (CEF) 的 Gmail 身份验证](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html)。 对于基于 CEF 构建的应用程序（如 Electron 应用），Google 将于 2021 年 6 月 30 日禁用身份验证。 受影响的应用程序已直接从 Google 收到通知，本文档中未涵盖这些这些应用程序。  本文档涉及上述嵌入式 Web 视图，Google 将在 2021 年 9 月 30 日的某个单独日期限制这些 Web 视图。

### <a name="action-needed-for-embedded-frameworks"></a>嵌入式框架所需的操作

按照 [Google 指南](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html)操作，以确定应用是否受到影响。

## <a name="step-1-configure-a-google-developer-project"></a>步骤 1：配置 Google 开发人员项目

首先，在 Google Developers Console 中创建一个新项目，以获取稍后要添加到 Azure Active Directory (Azure AD) 的客户端 ID 和客户端密码。 
1. 转到 Google API (https://console.developers.google.com )，并使用 Google 帐户登录。 我们建议使用共享团队 Google 帐户。
2. 如果系统提示你接受服务条款，请接受。
3. 创建新项目：在页面的左上角，选择项目列表，然后在“选择项目”页上，选择“新建项目”。
4. 在“新建项目”页上，为项目指定一个名称（例如“Azure AD B2B”），然后选择“创建”： 
   
   ![显示“新建项目”页的屏幕截图](media/google-federation/google-new-project.png)

4. 在“API 和服务”页上，选择新项目下的“查看”。

5. 在 API 卡上选择“转到 API 概述”。 选择“OAuth 同意屏幕”。

6. 依次选择“外部”、“创建”。 

7. 在“OAuth 同意屏幕”上，输入“应用程序名称”：

   ![显示 Google OAuth 同意屏幕的屏幕截图。](media/google-federation/google-oauth-consent-screen.png)

8. 滚动到“已授权域”部分，然后输入“microsoftonline.com”：

   ![显示“已授权域”部分的屏幕截图。](media/google-federation/google-oauth-authorized-domains.PNG)

9. 选择“保存”。

10. 选择“凭据”。 在“创建凭据”菜单中，选择“OAuth 客户端 ID” ：

    ![显示 Google API“创建凭据”菜单的屏幕截图。](media/google-federation/google-api-credentials.png)

11. 在“应用程序类型”下，选择“Web 应用程序”。 为应用程序指定合适的名称，例如 Azure AD B2B。 在“已授权的重定向 URI”下，输入以下 URI：
    - `https://login.microsoftonline.com`
    - `https://login.microsoftonline.com/te/<tenant ID>/oauth2/authresp` <br>（其中 `<tenant ID>` 是租户 ID）
    - `https://login.microsoftonline.com/te/<tenant name>.onmicrosoft.com/oauth2/authresp` <br>（其中 `<tenant name>` 是租户名）
   
    > [!NOTE]
    > 若要查找租户 ID，请转到 [Azure 门户](https://portal.azure.com)。 在“Azure Active Directory”下，选择“属性”并复制“租户 ID”  。

    ![显示“已授权重定向 URI”部分的屏幕截图。](media/google-federation/google-create-oauth-client-id.png)

12. 选择“创建”  。 复制客户端 ID 和客户端密码。 在 Azure 门户中添加标识提供者时，将使用它们。

    ![显示 OAuth 客户端 ID 和客户端密码的屏幕截图。](media/google-federation/google-auth-client-id-secret.png)

13. 可以将项目保留为“测试”的发布状态，并将测试用户添加到 OAuth 同意屏幕。 也可以选择 OAuth 同意屏幕上的“发布应用”按钮，以便让任何拥有 Google 帐户的用户都可以使用该应用。

## <a name="step-2-configure-google-federation-in-azure-ad"></a>步骤 2：在 Azure AD 中配置 Google 联合 

现在，你将设置 Google 客户端 ID 和客户端密码。 可以使用 Azure 门户或 PowerShell 执行此操作。 务必通过邀请自己来测试 Google 联合身份验证配置。 使用 Gmail 地址，并尝试使用受邀的 Google 帐户兑换邀请。 

**在 Azure 门户中配置 Google 联合身份验证** 
1. 转到 [Azure 门户](https://portal.azure.com)。 在左侧窗格中，选择“Azure Active Directory”。 
2. 选择“外部标识”。
3. 选择“所有标识提供者”，然后选择 Google 按钮。
4. 输入之前获得的客户端 ID 和客户端密码。 选择“保存”： 

   ![显示“添加 Google 标识提供者”页面的屏幕截图。](media/google-federation/google-identity-provider.png)

**使用 PowerShell 配置 Google 联合**
1. 安装最新版本的 Azure AD PowerShell for Graph 模块 ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview))。
2. 运行以下命令：`Connect-AzureAD`
3. 根据登录提示使用托管的全局管理员帐户登录。  
4. 运行以下命令： 
   
   `New-AzureADMSIdentityProvider -Type Google -Name Google -ClientId <client ID> -ClientSecret <client secret>`
 
   > [!NOTE]
   > 使用在“步骤 1：配置 Google 开发人员项目”中创建的应用客户端 ID 和客户端密码。 有关详细信息，请参阅 [New-AzureADMSIdentityProvider](/powershell/module/azuread/new-azureadmsidentityprovider?view=azureadps-2.0-preview&preserve-view=true)。 

## <a name="how-do-i-remove-google-federation"></a>如何删除 Google 联合？

可以删除 Google 联合设置。 如果这样做，那么已兑现邀请的 Google 来宾用户将无法登录。 但您可以通过[重置这些用户的兑换状态](reset-redemption-status.md)，让这些用户能够再次访问你的资源。
 
**在 Azure AD 门户中删除 Google 联合身份验证**
1. 转到 [Azure 门户](https://portal.azure.com)。 在左侧窗格中，选择“Azure Active Directory”。 
2. 选择“外部标识”。
3. 选择“所有标识提供者”。
4. 在“Google”行上，选择省略号按钮 (…)，然后选择“删除”。 
   
   ![显示社交标识提供者的“删除”按钮的屏幕截图。](media/google-federation/google-social-identity-providers.png)

1. 选择“是”确认删除。 

**使用 PowerShell 删除 Google 联合身份验证** 
1. 安装最新版本的 Azure AD PowerShell for Graph 模块 ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview))。
2. 运行 `Connect-AzureAD`。  
4. 根据登录提示，使用托管的全局管理员帐户登录。  
5. 输入以下命令：

    `Remove-AzureADMSIdentityProvider -Id Google-OAUTH`

   > [!NOTE]
   > 有关详细信息，请参阅 [Remove-AzureADMSIdentityProvider](/powershell/module/azuread/Remove-AzureADMSIdentityProvider?view=azureadps-2.0-preview&preserve-view=true)。