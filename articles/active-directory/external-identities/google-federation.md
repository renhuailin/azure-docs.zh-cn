---
title: 将 Google 添加为 B2B 的标识提供者 - Azure AD
description: 与 Google 联合，使来宾用户能够使用其自己的 Gmail 帐户登录到你的 Azure AD 应用。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 06/08/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: c21e03870a53858fe877410a7cd75fdc7e82a83b
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111963513"
---
# <a name="add-google-as-an-identity-provider-for-b2b-guest-users"></a>将 Google 添加为 B2B 来宾用户的标识提供者

通过设置 Google 联合身份验证，可让受邀的用户使用其自己的 Gmail 帐户登录到你的共享应用和资源，而无需创建 Microsoft 帐户。

将 Google 添加为应用程序的登录选项之一后，用户可以在“登录”页上输入他们用于登录 Google 的电子邮件，或者选择“登录选项”，再选择“使用 Google 登录”。 无论哪种情况，都会将他们重定向到 Google 登录页进行身份验证。

![Google 用户的登录选项](media/google-federation/sign-in-with-google-overview.png)

> [!NOTE]
> Google 联合专为 Gmail 用户设计。 若要与 G Suite 域联合，请使用 [SAML/WS-Fed 标识提供者联合](direct-federation.md)。

> [!IMPORTANT]
> 从 2021 年下半年开始，Google 将[弃用 Web 视图登录支持](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html)。 如果正在对 B2B 邀请或 [Azure AD B2C](../../active-directory-b2c/identity-provider-google.md) 使用 Google 联合身份验证，或者正在将自助注册与 Gmail 一起使用，那么当你的应用通过嵌入的 Web 视图对用户进行身份验证时，Google Gmail 用户将无法登录。 [了解详细信息](#deprecation-of-web-view-sign-in-support)。

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

从 2021 年下半年开始，Google 将[弃用嵌入式 web-view 登录支持](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html)。 如果正在对 B2B 或 [Azure AD B2C](../../active-directory-b2c/identity-provider-google.md) 使用 Google 联合身份验证，正在将[自助注册与 Gmail 一起使用](identity-providers.md)，如果你的应用通过嵌入的 Web 视图对用户进行身份验证，那么 Google Gmail 用户将无法进行身份验证。

以下是将影响 Gmai l用户的已知方案：
- 使用 [WebView](/windows/communitytoolkit/controls/wpf-winforms/webview) 控件、[WebView2](/microsoft-edge/webview2/) 或陈旧的 WebBrowser 控件进行身份验证的 Windows 应用。 这些应用应迁移到使用 Web 帐户管理器 (WAM) 流。
- 使用 WebView UI 元素的 Android 应用程序 
- 使用 UIWebView/WKWebview 的 iOS 应用程序 
- 使用 ADAL 的应用

此更改不会影响：

- Windows 上的 Microsoft 应用
- Web 应用
- 使用系统 web-view 进行身份验证的移动应用（iOS 上的 [SFSafariViewController](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller)、Android 上的[自定义标签](https://developer.chrome.com/docs/android/custom-tabs/overview/)）。  
- G Suite 标识，例如，将[基于 SAML 的联合](direct-federation.md)与 G Suite 结合使用时

我们正在向 Google 确认此更改是否会影响以下内容：
- 使用 Web 帐户管理器(WAM)或 Web 身份验证代理 (WAB) 的 Windows 应用。  

我们将继续测试各种平台和方案，并将相应地更新本文。
### <a name="action-needed-for-embedded-web-views"></a>嵌入式 web-view 所需的操作
修改应用以使用系统浏览器进行登录。 有关详细信息，请参阅 MSAL.NET 文档中的[嵌入式与系统 Web UI](../develop/msal-net-web-browsers.md#embedded-vs-system-web-ui)。 默认情况下，所有 MSAL SDK 都使用系统 web-view。
### <a name="what-to-expect"></a>期望
在 Google 于 2021 年下半年实施这些更改之前，Microsoft 将为仍在使用嵌入式 web-view 的应用部署解决方法，以确保身份验证不会被阻止。

迁移到允许使用 web-view 进行身份验证的应用程序将不会受到影响，并且像往常一样，将不允许用户通过 Google 进行身份验证。

如果应用程序未迁移到允许 web-view 进行身份验证，则受影响的 Gmail 用户将看到以下屏幕。

![Google 登录错误（如果应用没有迁移到系统浏览器）](media/google-federation/google-sign-in-error-ewv.png)

当 Google 分享了更多细节时，我们将更新这份文档。

### <a name="distinguishing-between-cefelectron-and-embedded-web-views"></a>区分 CEF/Electron 和嵌入式 web-view
除了[弃用嵌入式 web-view 和框架登录支持](#deprecation-of-web-view-sign-in-support)之外，Google 还将[弃用基于 Chromium 嵌入式框架 (CEF) 的 Gmail 身份验证](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html)。 对于基于 CEF 构建的应用程序（如 Electron 应用），Google 将于 2021 年 6 月 30 日禁用身份验证。 受影响的应用程序已直接从 Google 收到通知，本文档中未涵盖这些这些应用程序。  本文档涉及上述嵌入式 web-view，Google 将在 2021 年晚些时候的某个单独日期限制这些 web-view。

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
   
    > [!NOTE]
    > 若要查找租户 ID，请转到 [Azure 门户](https://portal.azure.com)。 在“Azure Active Directory”下，选择“属性”并复制“租户 ID”  。

    ![显示“已授权重定向 URI”部分的屏幕截图。](media/google-federation/google-create-oauth-client-id.png)

12. 选择“创建”  。 复制客户端 ID 和客户端密码。 在 Azure 门户中添加标识提供者时，将使用它们。

    ![显示 OAuth 客户端 ID 和客户端密码的屏幕截图。](media/google-federation/google-auth-client-id-secret.png)

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