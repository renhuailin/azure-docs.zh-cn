---
title: 适用于 Apple 设备的 Microsoft 企业 SSO 插件
titleSuffix: Microsoft identity platform | Azure
description: 了解适用于 iOS 和 macOS 设备的 Microsoft Azure Active Directory SSO 插件。
services: active-directory
author: brandwe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/15/2020
ms.author: brandwe
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: b7ec6ab8b52d9d43d898f481a2f36310e5c0897d
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2020
ms.locfileid: "94561074"
---
# <a name="microsoft-enterprise-sso-plug-in-for-apple-devices-preview"></a>适用于 Apple 设备的 Microsoft 企业 SSO 插件（预览版）

>[!IMPORTANT]
> 此功能 [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

适用于 Apple 设备的 Microsoft 企业 SSO 插件为支持 Apple [企业单一登录](https://developer.apple.com/documentation/authenticationservices)功能的所有应用程序中的 Azure Active Directory (Azure AD) 帐户提供单一登录 (SSO)  。 此插件是在 Microsoft 与 Apple 的密切合作下开发的，可以提高应用程序的可用性，同时提供 Apple 和 Microsoft 能够提供的最佳保护。

在此公共预览版中，企业 SSO 插件仅适用于 iOS 设备，并仅在特定的 Microsoft 应用程序中分发。

## <a name="features"></a>功能

适用于 Apple 设备的 Microsoft 企业 SSO 插件具有以下优势：

- 为支持 Apple 企业单一登录功能的所有应用程序中的 Azure AD 帐户提供 SSO。
- 在 Microsoft Authenticator 中自动交付，可由任何移动设备管理 (MDM) 解决方案启用。

## <a name="requirements"></a>要求

若要使用适用于 Apple 设备的 Microsoft 企业 SSO 插件：

- 必须在设备上安装 iOS 13.0 或更高版本。
- 必须在设备上安装提供适用于 Apple 设备的 Microsoft 企业 SSO 插件的 Microsoft 应用程序。 对于公共预览版，这些应用程序包括 [Microsoft Authenticator 应用](../user-help/user-help-auth-app-overview.md)。
- 设备必须已在 MDM 中注册（例如，使用 Microsoft Intune 注册）。
- 必须将配置推送到设备，才能在该设备上启用适用于 Apple 设备的 Microsoft 企业 SSO 插件。 此项安全约束是 Apple 要求的。

## <a name="enable-the-sso-plug-in-with-mobile-device-management-mdm"></a>使用移动设备管理 (MDM) 启用 SSO 插件

若要启用适用于 Apple 设备的 Microsoft 企业 SSO 插件，需要通过 MDM 服务向设备发送一个信号。 由于 Microsoft 在 [Microsoft Authenticator 应用程序](..//user-help/user-help-auth-app-overview.md)中包含企业 SSO 插件，因此请使用 MDM 将应用程序配置为启用 Microsoft 企业 SSO 插件。

使用以下参数配置适用于 Apple 设备的 Microsoft 企业 SSO 插件：

- **类型** ：重定向
- **扩展 ID** ：`com.microsoft.azureauthenticator.ssoextension`
- **团队 ID** ：（iOS 不需要此字段）
- **URL** ：
  - `https://login.microsoftonline.com`
  - `https://login.microsoft.com`
  - `https://sts.windows.net`
  - `https://login.partner.microsoftonline.cn`
  - `https://login.chinacloudapi.cn`
  - `https://login.microsoftonline.de`
  - `https://login.microsoftonline.us`
  - `https://login.usgovcloudapi.net`
  - `https://login-us.microsoftonline.com`
  
### <a name="additional-configuration-options"></a>其他配置选项
可以添加其他配置选项，以将 SSO 功能扩展到其他应用。

#### <a name="enable-sso-for-apps-that-dont-use-msal"></a>为不使用 MSAL 的应用启用 SSO

借助 SSO 插件，任何应用程序都可以加入单一登录，即使它们不是使用 Microsoft SDK（例如 Microsoft 身份验证库 (MSAL)）开发的也可以。

已下载 Microsoft Authenticator 应用并在组织中注册其设备的设备会自动安装 SSO 插件。 你的组织目前可能会在以下方案中使用 Authenticator 应用：例如多重身份验证、无密码身份验证和条件访问。 虽然 Microsoft 简化了 Intune 的 Microsoft Endpoint Manager 内的配置，但可以使用任何 MDM 提供程序为应用程序启用该插件。 允许列表用于配置这些应用程序，以使用 Authenticator 应用安装的 SSO 插件。

仅支持使用本机 Apple 网络技术或 Web 视图的应用。 如果应用程序附带其自己的网络层实现，则不支持 Microsoft 企业 SSO 插件。  

使用以下参数为不使用 MSAL 的应用配置 Microsoft 企业 SSO 插件：

- 键：`AppAllowList`
- 类型：`String`
- **值** ：允许加入 SSO 的应用程序的应用程序捆绑包 ID 的逗号分隔列表
- **示例** ：`com.contoso.workapp, com.contoso.travelapp`

MDM 管理员允许[同意的应用](./application-consent-experience.md)加入 SSO，从而可以通过无提示的方式为最终用户获取令牌。 因此，仅将受信任的应用程序添加到允许列表，这一点很重要。 

无需将使用 MSAL 或 ASWebAuthenticationSession 的应用程序添加到此列表中。 这些应用程序默认处于启用状态。 

#### <a name="allow-creating-sso-session-from-any-application"></a>允许从任何应用程序创建 SSO 会话

默认情况下，Microsoft 企业 SSO 插件仅在 SSO 插件已有共享凭据的情况下为授权应用提供 SSO。 Microsoft 企业 SSO 插件在令牌获取过程中如果被其他基于 ADAL 或 MSAL 的应用程序调用，则可以获取共享凭据。 大多数 Microsoft 应用使用 Microsoft Authenticator 或 SSO 插件。 这意味着默认尽量在本机应用流之外启用 SSO。  

启用 `browser_sso_interaction_enabled` 标志可以使非 MSAL 应用和 Safari 浏览器进行初始启动并获取共享凭据。 如果 Microsoft 企业 SSO 插件还没有共享凭据，则每当从 Safari 浏览器、ASWebAuthenticationSession、SafariViewController 或其他允许的本机应用程序中的 Azure AD URL 请求登录时，该插件都将尝试获取一个凭据。  

- 键：`browser_sso_interaction_enabled`
- 类型：`Integer`
- **值** ：1 或 0

建议启用此标志，跨所有应用获得更一致的体验。 此项默认禁用。 

#### <a name="disable-oauth2-application-prompts"></a>禁用 OAuth2 应用程序提示

Microsoft 企业 SSO 插件通过将共享凭据附加到来自允许的应用程序的网络请求中来提供 SSO。 某些 OAuth2 应用程序可能会在协议层上强制实施最终用户提示。 这些应用将忽略共享凭据。  

启用 `disable_explicit_app_prompt` 标志会限制本机和 Web 应用程序在协议层上强制实施最终用户提示并绕过 SSO 的功能。

- 键：`disable_explicit_app_prompt`
- 类型：`Integer`
- **值** ：1 或 0

建议启用此标志，跨所有应用获得更一致的体验。 此项默认禁用。 

#### <a name="use-intune-for-simplified-configuration"></a>使用 Intune 简化配置

可以使用 Microsoft Intune 作为 MDM 服务来方便配置 Microsoft 企业 SSO 插件。 有关详细信息，请参阅 [Intune 配置文档](/intune/configuration/ios-device-features-settings)。

## <a name="using-the-sso-plug-in-in-your-application"></a>在应用程序中使用 SSO 插件

[适用于 Apple 设备的 Microsoft 身份验证库 (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-objc) 1.1.0 及更高版本支持适用于 Apple 设备的 Microsoft 企业 SSO 插件。

若要为 Frontline Worker 方案构建应用程序，请参阅[适用于 iOS 设备的共享设备模式](msal-ios-shared-devices.md)，以获取该功能的其他设置。

## <a name="how-the-sso-plug-in-works"></a>SSO 插件的工作方式

Microsoft 企业 SSO 插件依赖于 [Apple 的企业单一登录框架](https://developer.apple.com/documentation/authenticationservices/asauthorizationsinglesignonprovider?language=objc)。 加入到框架中的标识提供者可以拦截其域的网络流量，并改进或更改这些请求的处理方式。 例如，SSO 插件可以显示其他 UI，以安全方式收集最终用户凭据、需要进行 MFA 或以无提示方式向应用程序提供令牌。

本机应用程序还可以实现自定义操作，并直接与 SSO 插件进行对话。
可在 [Apple 提供的 2019 WWDC 视频](https://developer.apple.com/videos/play/tech-talks/301/)中了解单一登录框架

### <a name="applications-that-use-msal"></a>使用 MSAL 的应用程序

针对工作和学校帐户，[适用于 Apple 设备的 Microsoft 身份验证库 (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-objc) 版本 1.1.0 及更高版本本机支持适用于 Apple 设备的 Microsoft 企业 SSO 插件。 

如果遵循[所有建议的步骤](./quickstart-v2-ios.md)并使用默认的[重定向 URI 格式](./redirect-uris-ios.md)，则无需进行特殊配置。 当应用程序在具有 SSO 插件的设备上运行时，MSAL 将自动为所有交互式和无提示令牌请求以及帐户枚举和帐户删除操作调用该应用程序。 由于 MSAL 实现了依赖于自定义操作的本机 SSO 插件协议，此设置可为最终用户提供最流畅的本机体验。 

如果 MDM 未启用 SSO 插件，但设备上存在 Microsoft Authenticator 应用，则 MSAL 将为任何交互式令牌请求改用 Microsoft Authenticator 应用。 SSO 插件与 Microsoft Authenticator 应用共享 SSO。

### <a name="applications-that-dont-use-msal"></a>不使用 MSAL 的应用程序

如果管理员将应用程序显式添加到允许列表，则不使用 MSAL 的应用程序仍可获取 SSO。 

只要满足以下条件，这些应用就不需要更改代码：

- 应用程序正在使用 Apple 框架执行网络请求（例如 [WKWebView](https://developer.apple.com/documentation/webkit/wkwebview) 和 [NSURLSession](https://developer.apple.com/documentation/foundation/nsurlsession)） 
- 应用程序使用标准协议与 Azure AD 通信（例如 OAuth2、SAML 和 WS 联合身份验证）
- 应用程序不在本机 UI 中收集纯文本用户名和密码

在这种情况下，应用程序创建网络请求并打开 Web 浏览器以让用户登录时，系统将提供 SSO。 用户重定向到 Azure AD 登录 URL 时，SSO 插件将验证 URL 并检查该 URL 是否有可用的 SSO 凭据。 如果有可用的 SSO 凭据，则 SSO 插件会将 SSO 凭据传递到 Azure AD，这将授权应用程序完成网络请求，而无需要求用户输入其凭据。 此外，如果已知设备 Azure AD，SSO 插件还会传递设备证书，以满足基于设备的条件性访问检查的需要。 

为支持非 MSAL 应用程序的 SSO，SSO 插件实现的协议类似于 " [什么是主刷新令牌？](../devices/concept-primary-refresh-token.md#browser-sso-using-prt)" 中所述的 Windows browser 插件。 

与基于 MSAL 的应用相比，SSO 插件通过与应用提供的现有浏览器登录体验集成，对非 MSAL 应用执行的操作更加透明。 最终用户将看到他们熟悉的体验，好处是不必在每个应用程序中执行其他登录。 例如，SSO 插件不会显示本机帐户选取器，而是将 SSO 会话添加到基于 Web 的帐户选取器体验中。 

## <a name="next-steps"></a>后续步骤

有关 iOS 上的共享设备模式的详细信息，请参阅[适用于 iOS 设备的共享设备模式](msal-ios-shared-devices.md)。
