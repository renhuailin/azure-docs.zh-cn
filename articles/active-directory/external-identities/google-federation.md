---
title: 将 Google 添加为 B2B 的标识提供者 - Azure AD
description: 与 Google 联合，使来宾用户能够使用其自己的 Gmail 帐户登录到你的 Azure AD 应用。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 03/02/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5a397c17821d16594ccfb48175a8a141cb9f390b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "101687815"
---
# <a name="add-google-as-an-identity-provider-for-b2b-guest-users"></a>将 Google 添加为 B2B 来宾用户的标识提供者

通过设置 Google 联合身份验证，可让受邀的用户使用其自己的 Gmail 帐户登录到你的共享应用和资源，而无需创建 Microsoft 帐户。

将 Google 添加为应用程序的登录选项之一后，用户可以在“登录”页上输入他们用于登录 Google 的电子邮件，或者选择“登录选项”，再选择“使用 Google 登录”。 无论哪种情况，都会将他们重定向到 Google 登录页进行身份验证。

![Google 用户的登录选项](media/google-federation/sign-in-with-google-overview.png)

> [!NOTE]
> Google 联合专为 Gmail 用户设计。 要与 G Suite 域进行联合身份验证，请使用[直接联合身份验证](direct-federation.md)。

> [!IMPORTANT]
> 从 2021 年 1 月 4 日开始，Google 将[弃用 WebView 登录支持](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html)。 如果要通过 Gmail 使用 Google 联合身份验证或自助服务注册，则应[测试业务线本机应用程序的兼容性](google-federation.md#deprecation-of-webview-sign-in-support)。

## <a name="what-is-the-experience-for-the-google-user"></a>Google 用户体验是什么？

当 Google 用户兑换邀请时，其体验因是否已登录到 Google 而异：

- 系统将提示未登录到 Google 的来宾用户执行此操作。
- 如果来宾用户已登录到 Google，则系统会提示他们选择要使用的帐户。 他们必须选择你在邀请他们时所用的帐户。

如果来宾用户看到“标头过长”错误，可以清除其 Cookie，或者打开私密或 Incognito 窗口并再次尝试登录。

![显示 Google 登录页面屏幕截图。](media/google-federation/google-sign-in.png)

## <a name="sign-in-endpoints"></a>登录终结点

Google 来宾用户现在可以使用[公共终结点](redemption-experience.md#redemption-and-sign-in-through-a-common-endpoint)（也就是不包含租户上下文的常规应用 URL）登录到多租户或 Microsoft 第一方应用。 在登录过程中，来宾用户选择“登录选项”，然后选择“登录到组织” 。 然后，用户需要键入组织的名称并继续使用其 Google 凭据登录。

Google 来宾用户还可以使用包含租户信息的应用程序终结点，例如：

  * `https://myapps.microsoft.com/?tenantid=<your tenant ID>`
  * `https://myapps.microsoft.com/<your verified domain>.onmicrosoft.com`
  * `https://portal.azure.com/<your tenant ID>`

还可以通过添加租户信息，为 Google 来宾用户提供指向应用程序或资源的直接链接，例如 `https://myapps.microsoft.com/signin/Twitter/<application ID?tenantId=<your tenant ID>`。

## <a name="deprecation-of-webview-sign-in-support"></a>弃用 WebView 登录支持

从 2021 年 1 月 4 日开始，Google 将[弃用 WebView 登录支持](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html)。 如果你使用的是 Google 联合身份验证或[通过 Gmail 自助服务注册](identity-providers.md)，则应测试业务线本机应用程序的兼容性。 如果应用包括需要身份验证的 Web 视图内容，Google Gmail 用户将无法进行身份验证。 以下是将影响 Gmail 用户的已知方案：

- 在较旧版本的 Windows 上使用嵌入式 WebView 或 WebAccountManager (WAM) 的 Windows 应用。
- 你开发的使用嵌入式浏览器框架进行身份验证的其他本机应用。

此更改不会影响：

- 在最新版本的 Windows 上使用嵌入式 WebView 或 WebAccountManager (WAM) 的 Windows 应用。
- Microsoft iOS 应用
- G Suite 标识，例如，将基于 SAML 的[直接联合身份验证](direct-federation.md)与 G Suite 联合身份验证使用时

我们将继续测试各种平台和方案，并将相应地更新本文。
### <a name="to-test-your-apps-for-compatibility"></a>测试应用的兼容性

1. 遵循 [Google 指导](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html)，确定应用是否受影响。
2. 使用 Fiddler 或其他测试工具，在登录过程中插入标头并使用 Google 外部标识测试登录：

   1. 当请求发送到 accounts.google.com 时，向 HTTP 请求头添加 Google-Accounts-Check-OAuth-Login:true。
   1. 尝试登录到应用，方法是在 accounts.google.com 登录页中输入 Gmail 地址。
   1. 如果登录失败，出现错误，如“此浏览器或应用可能不安全”，则会阻止你的 Google 外部标识登录。

3. 使用以下方法之一解决该问题：

   - 如果 Windows 应用在较旧版本的 Windows 上使用嵌入式 Web 视图或 WebAccountManager (WAM)，请更新到最新版本的 Windows。
   - 修改应用以使用系统浏览器进行登录。 有关详细信息，请参阅 MSAL.NET 文档中的[嵌入式与系统 Web UI](../develop/msal-net-web-browsers.md#embedded-vs-system-web-ui)。  


## <a name="step-1-configure-a-google-developer-project"></a>步骤 1：配置 Google 开发人员项目
首先，在 Google Developers Console 中创建一个新项目，以获取稍后要添加到 Azure Active Directory (Azure AD) 的客户端 ID 和客户端密码。 
1. 转到 Google API (https://console.developers.google.com )，并使用 Google 帐户登录。 我们建议使用共享团队 Google 帐户。
2. 如果系统提示你接受服务条款，请接受。
3. 创建新项目：在页面的左上角，选择项目列表，然后在“选择项目”页上，选择“新建项目”。
4. 在“新建项目”页上，为项目指定一个名称（例如“Azure AD B2B”），然后选择“创建”： 
   
   ![显示“新建项目”页的屏幕截图](media/google-federation/google-new-project.png)

4. 在“API 和服务”页上，选择新项目下的“查看”。

5. 选择 API 卡上的“转到 API 概述”。 选择“OAuth 同意屏幕”。

6. 依次选择“外部”、“创建”。 

7. 在“OAuth 同意屏幕”上，输入“应用程序名称”：

   ![显示 Google OAuth 同意屏幕的屏幕截图。](media/google-federation/google-oauth-consent-screen.png)

8. 滚动到“已授权域”部分，然后输入“microsoftonline.com”：

   ![显示“已授权域”部分的屏幕截图。](media/google-federation/google-oauth-authorized-domains.PNG)

9. 选择“保存”。

10. 选择“凭据”。 在“创建凭据”菜单中，选择“OAuth 客户端 ID” ：

    ![显示 Google API“创建凭据”菜单的屏幕截图。](media/google-federation/google-api-credentials.png)

11. 在“应用程序类型”下，选择“Web 应用程序”。 为应用程序指定适当的名称，如“Azure AD B2B”。 在“已授权的重定向 URI”下，输入以下 URI：
    - `https://login.microsoftonline.com`
    - `https://login.microsoftonline.com/te/<tenant ID>/oauth2/authresp` <br>（其中 `<tenant ID>` 是租户 ID）
   
    > [!NOTE]
    > 若要查找租户 ID，请转到 [Azure 门户](https://portal.azure.com)。 在“Azure Active Directory”下，选择“属性”并复制“租户 ID”  。

    ![显示“已授权重定向 URI”部分的屏幕截图。](media/google-federation/google-create-oauth-client-id.png)

12. 选择“创建”  。 复制客户端 ID 和客户端密码。 在 Azure 门户中添加标识提供者时，将使用它们。

    ![显示 OAuth 客户端 ID 和客户端密码的屏幕截图。](media/google-federation/google-auth-client-id-secret.png)

## <a name="step-2-configure-google-federation-in-azure-ad"></a>步骤 2：在 Azure AD 中配置 Google 联合 
现在，你将设置 Google 客户端 ID 和客户端密码。 可以使用 Azure 门户或 PowerShell 执行此操作。 请务必通过邀请自己测试 Google 联合身份验证配置。 使用 Gmail 地址，尝试使用受邀的 Google 帐户兑换邀请。 

**在 Azure 门户中配置 Google 联合身份验证** 
1. 转到 [Azure 门户](https://portal.azure.com)。 在左侧窗格中，选择“Azure Active Directory”。 
2. 选择“外部标识”。
3. 选择“所有标识提供者”，然后选择“Google”按钮。
4. 输入前面获取的客户端 ID 和客户端密码。 选择“保存”： 

   ![显示“添加 Google 标识提供者”页面的屏幕截图。](media/google-federation/google-identity-provider.png)

**使用 PowerShell 配置 Google 联合**
1. 安装最新版本的 Azure AD PowerShell for Graph 模块 ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview))。
2. 运行以下命令：`Connect-AzureAD`
3. 根据登录提示使用托管的全局管理员帐户登录。  
4. 运行以下命令： 
   
   `New-AzureADMSIdentityProvider -Type Google -Name Google -ClientId <client ID> -ClientSecret <client secret>`
 
   > [!NOTE]
   > 使用在“步骤 1：配置 Google 开发人员项目”中创建的应用客户端 ID 和客户端密码。 有关详细信息，请参阅 [New-AzureADMSIdentityProvider](/powershell/module/azuread/new-azureadmsidentityprovider?view=azureadps-2.0-preview)。 
 
## <a name="how-do-i-remove-google-federation"></a>如何删除 Google 联合？
可以删除 Google 联合设置。 如果这样做，已兑换邀请的 Google 来宾用户将无法登录。 不过，你可以通过从目录中删除用户并重新邀请，再次向用户授予对资源的访问权限。 
 
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
   > 有关详细信息，请参阅 [Remove-AzureADMSIdentityProvider](/powershell/module/azuread/Remove-AzureADMSIdentityProvider?view=azureadps-2.0-preview)。
