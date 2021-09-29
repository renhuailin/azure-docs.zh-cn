---
title: 适用于 Apple 设备的 Microsoft 企业 SSO 插件
titleSuffix: Microsoft identity platform | Azure
description: 了解适用于 iOS、iPadOS 和 macOS 设备的 Azure Active Directory SSO 插件。
services: active-directory
author: brandwe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/10/2021
ms.author: brandwe
ms.reviewer: brandwe
ms.custom: aaddev, has-adal-ref
ms.openlocfilehash: e03b288934ce01a25a8ee7b4ad3569af6507b8a6
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124734767"
---
# <a name="microsoft-enterprise-sso-plug-in-for-apple-devices-preview"></a>适用于 Apple 设备的 Microsoft 企业 SSO 插件（预览版）

>[!IMPORTANT]
> 此功能 [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

借助适用于 Apple 设备的 Microsoft 企业 SSO 插件，macOS、iOS 和 iPadOS 上的 Azure Active Directory (Azure AD) 帐户可在支持 Apple [企业单一登录](https://developer.apple.com/documentation/authenticationservices)功能的所有应用程序上进行单一登录 (SSO)。 该插件可为你的企业可能依赖但尚未支持最新标识库或协议的较旧应用程序提供 SSO。 此插件是在 Microsoft 与 Apple 的密切合作下开发的，可以提高应用程序的可用性，同时提供可用的最佳保护。

企业 SSO 插件目前是下列应用中的内置功能：

* [Microsoft Authenticator](https://support.microsoft.com/account-billing/how-to-use-the-microsoft-authenticator-app-9783c865-0308-42fb-a519-8cf666fe0acc)：iOS、iPadOS
* Microsoft Intune [公司门户](/mem/intune/apps/apps-company-portal-macos)macOS

## <a name="features"></a>功能

适用于 Apple 设备的 Microsoft 企业 SSO 插件具有以下优势：

- 为支持 Apple 企业 SSO 功能的所有应用程序中的 Azure AD 帐户提供 SSO。
- 可由任何移动设备管理 (MDM) 解决方案启用。
- 将 SSO 扩展到尚未使用 Microsoft 标识平台库的应用程序。
- 将 SSO 扩展到使用 OAuth 2、OpenID Connect 和 SAML 的应用程序。

## <a name="requirements"></a>要求

若要使用适用于 Apple 设备的 Microsoft 企业 SSO 插件：

- 设备必须支持且已安装具有适用于 Apple 设备的 Microsoft 企业 SSO 插件的应用：
  - iOS 13.0 及更高版本：[Microsoft Authenticator 应用](https://support.microsoft.com/account-billing/how-to-use-the-microsoft-authenticator-app-9783c865-0308-42fb-a519-8cf666fe0acc)
  - iPadOS 13.0 及更高版本：[Microsoft Authenticator 应用](https://support.microsoft.com/account-billing/how-to-use-the-microsoft-authenticator-app-9783c865-0308-42fb-a519-8cf666fe0acc)
  - macOS 10.15 及更高版本：[Intune 公司门户应用](/mem/intune/user-help/enroll-your-device-in-intune-macos-cp)
- 设备必须在 MDM 中注册，例如通过 Microsoft Intune。
- 必须将配置推送到设备，以启用企业 SSO 插件。 Apple 需要此安全约束。

### <a name="ios-requirements"></a>iOS 要求：
- 必须在设备上安装 iOS 13.0 或更高版本。
- 必须在设备上安装提供适用于 Apple 设备的 Microsoft 企业 SSO 插件的 Microsoft 应用程序。 对于公共预览版，这些应用程序是 [Microsoft Authenticator 应用](https://support.microsoft.com/account-billing/how-to-use-the-microsoft-authenticator-app-9783c865-0308-42fb-a519-8cf666fe0acc)。


### <a name="macos-requirements"></a>macOS 要求：
- 必须在设备上安装 macOS 10.15 或更高版本。 
- 必须在设备上安装提供适用于 Apple 设备的 Microsoft 企业 SSO 插件的 Microsoft 应用程序。 对于公共预览版，这些应用程序包括 [Intune 公司门户应用](/mem/intune/user-help/enroll-your-device-in-intune-macos-cp)。

## <a name="enable-the-sso-plug-in"></a>启用 SSO 插件

使用以下信息可通过 MDM 启用 SSO 插件。
### <a name="microsoft-intune-configuration"></a>Microsoft Intune 配置

如果使用 Microsoft Intune 作为 MDM 服务，则可使用内置配置文件设置来启用 Microsoft 企业 SSO 插件：

1. 对配置文件的 [SSO 应用扩展](/mem/intune/configuration/device-features-configure#single-sign-on-app-extension)设置进行配置。 
1. 如果尚未分配配置文件，请[将配置文件分配给用户或设备组](/mem/intune/configuration/device-profile-assign)。

当每台设备下次向 Intune 签入，启用 SSO 插件的配置文件设置会自动应用于组的设备。

### <a name="manual-configuration-for-other-mdm-services"></a>其他 MDM 服务的手动配置

如果不将 Intune 用于 MDM，可以为 Apple 设备配置可扩展单一登录配置文件有效负载。 使用以下参数来配置 Microsoft 企业 SSO 插件及其配置选项。

。iOS 设置：

- **扩展 ID**：`com.microsoft.azureauthenticator.ssoextension`
- 团队 ID：iOS 不需要此字段。

macOS 设置：

- **扩展 ID**：`com.microsoft.CompanyPortalMac.ssoextension`
- **团队 ID**：`UBF8T346G9`

通用设置：

- **类型**：重定向
  - `https://login.microsoftonline.com`
  - `https://login.microsoft.com`
  - `https://sts.windows.net`
  - `https://login.partner.microsoftonline.cn`
  - `https://login.chinacloudapi.cn`
  - `https://login.microsoftonline.de`
  - `https://login.microsoftonline.us`
  - `https://login.usgovcloudapi.net`
  - `https://login-us.microsoftonline.com`
  
### <a name="more-configuration-options"></a>更多配置选项
可以添加更多配置选项，将 SSO 功能扩展到其他应用。

#### <a name="enable-sso-for-apps-that-dont-use-a-microsoft-identity-platform-library"></a>为不使用 Microsoft 标识平台库的应用启用 SSO

借助 SSO 插件，任何应用程序都可以加入 SSO，即使它们不是使用 Microsoft SDK（例如 Microsoft 身份验证库 (MSAL)）开发的也可以。

具有以下特征的设备会自动安装 SSO 插件：
* 已在 iOS 或 iPadOS 上下载 Authenticator 应用，或已在 macOS 上下载 Intune 公司门户应用。
* 将设备注册到组织。 

你的组织可能会在以下方案中使用 Authenticator 应用：例如多重身份验证 (MFA)、无密码身份验证和条件访问。 使用 MDM 提供程序可以为应用程序启用 SSO 插件。 Microsoft 使你可以在 Intune 的 Microsoft Endpoint Manager 内轻松配置插件。 允许列表用于配置这些应用程序来使用 SSO 插件。

>[!IMPORTANT]
> Microsoft 企业 SSO 插件仅支持使用本机 Apple 网络技术或 Web 视图的应用。 它不支持提供自己的网络层实现的应用程序。  

采用以下参数为不使用 Microsoft 标识平台库的应用配置 Microsoft 企业 SSO 插件。

#### <a name="enable-sso-for-all-managed-apps"></a>为所有托管应用启用 SSO

- 键：`Enable_SSO_On_All_ManagedApps`
- 类型：`Integer`
- 值：1 或 0。

如果启用此标志（将其值设置为 `1`），则不在 `AppBlockList` 中的所有 MDM 托管应用都可以参与 SSO。

#### <a name="enable-sso-for-specific-apps"></a>为特定应用启用 SSO

- 键：`AppAllowList`
- 类型：`String`
- 值：允许加入 SSO 的应用程序的应用程序捆绑包 ID 的逗号分隔列表。
- **示例**：`com.contoso.workapp, com.contoso.travelapp`

>[!NOTE]
> 默认允许 Safari 和 Safari 视图服务参与 SSO。 可以通过在 AppBlockList 中添加 Safari 和 Safari 视图服务的捆绑 ID 将其配置为不参与 SSO。 iOS 捆绑 ID：[com.apple.mobilesafari, com.apple.SafariViewService]；macOS 捆绑 ID：com.apple.Safari

#### <a name="enable-sso-for-all-apps-with-a-specific-bundle-id-prefix"></a>为具有特定捆绑 ID 前缀的所有应用启用 SSO
- 键：`AppPrefixAllowList`
- 类型：`String`
- 值：允许加入 SSO 的应用程序的应用程序包 ID 前缀的逗号分隔列表。 此参数会使所有以特定前缀开头的应用都可加入 SSO。
- **示例**：`com.contoso., com.fabrikam.`

#### <a name="disable-sso-for-specific-apps"></a>为特定应用禁用 SSO

- 键：`AppBlockList`
- 类型：`String`
- 值：不允许其参与 SSO 的应用程序的逗号分隔应用程序捆绑 ID 列表。
- **示例**：`com.contoso.studyapp, com.contoso.travelapp`

若要为 Safari 或 Safari 视图服务禁用 SSO，必须通过将其捆绑 ID 添加到 `AppBlockList` 来显式禁用： 

- iOS：`com.apple.mobilesafari`、`com.apple.SafariViewService`
- macOS：`com.apple.Safari`

#### <a name="enable-sso-through-cookies-for-a-specific-application"></a>对特定应用程序启用通过 Cookie 进行 SSO

为具有高级网络设置的某些应用启用 SSO 时，这些应用可能会遇到意外的问题。 例如，可能有错误消息指出网络请求已取消或中断。

如果即使在你通过其他设置启用 SSO 之后，用户在登录到某个应用程序时也会遇到问题，请尝试将该应用程序添加到 `AppCookieSSOAllowList` 以解决问题。

- 键：`AppCookieSSOAllowList`
- 类型：`String`
- 值：可加入 SSO 的应用程序的应用程序包 ID 前缀的逗号分隔列表。 以列出的前缀开头的所有应用都允许加入 SSO。
- **示例**：`com.contoso.myapp1, com.fabrikam.myapp2`

其他要求：若要使用 `AppCookieSSOAllowList` 为应用程序启用 SSO，还必须添加其捆绑 ID 前缀 `AppPrefixAllowList`。

请仅对发生意外登录失败的应用程序尝试此配置。 

#### <a name="summary-of-keys"></a>键的摘要

| 键 | 类型 | 值 |
|--|--|--|
| `Enable_SSO_On_All_ManagedApps` | Integer | `1` 表示为所有托管应用启用 SSO，`0` 表示为所有托管应用禁用 SSO。 |
| `AppAllowList` | 字符串<br/>（逗号分隔的列表） | 允许其参与 SSO 的应用程序的捆绑 ID。 |
| `AppBlockList` | 字符串<br/>（逗号分隔的列表） | 不允许其参与 SSO 的应用程序的捆绑 ID。 |
| `AppPrefixAllowList` | 字符串<br/>（逗号分隔的列表） | 允许其参与 SSO 的应用程序的捆绑 ID 前缀。 |
| `AppCookieSSOAllowList` | 字符串<br/>（逗号分隔的列表） | 允许其加入 SSO 的、但使用特殊网络设置并且在使用其他设置时会出现 SSO 问题的应用程序的捆绑 ID 前缀。 添加到 `AppCookieSSOAllowList` 的应用也必须添加到 `AppPrefixAllowList`。 |

#### <a name="settings-for-common-scenarios"></a>常用方案的设置

- 方案：我想要为大部分而不是所有托管应用程序启用 SSO。

    | 键 | 值 |
    | -------- | ----------------- |
    | `Enable_SSO_On_All_ManagedApps` | `1` |
    | `AppBlockList` | 要阻止其参与 SSO 的应用的捆绑 ID（逗号分隔的列表）。 |

- 方案：我想要为 Safari 禁用 SSO（默认已启用），但要为所有托管应用启用 SSO。

    | 键 | 值 |
    | -------- | ----------------- |
    | `Enable_SSO_On_All_ManagedApps` | `1` |
    | `AppBlockList` | 要阻止其参与 SSO 的 Safari 应用的捆绑 ID（逗号分隔的列表）。<br/><li>对于 iOS：`com.apple.mobilesafari`、`com.apple.SafariViewService`<br/><li>对于 macOS：`com.apple.Safari` |

- 方案：我想要对所有托管应用以及少量的非托管应用启用 SSO，但要为其他几个应用禁用 SSO。

    | 键 | 值 |
    | -------- | ----------------- |
    | `Enable_SSO_On_All_ManagedApps` | `1` |
    | `AppAllowList` | 要允许其参与 SSO 的应用的捆绑 ID（逗号分隔的列表）。 |
    | `AppBlockList` | 要阻止其参与 SSO 的应用的捆绑 ID（逗号分隔的列表）。 |


##### <a name="find-app-bundle-identifiers-on-ios-devices"></a>在 iOS 设备上查找应用程序包标识符

Apple 不提供从 App Store 获取应用程序包 ID 的简单方法。 要获取要用于 SSO 的应用的应用程序包 ID，最简单的方法是询问供应商或应用开发人员。 如果该选项不可用，那么可使用 MDM 配置来查找应用程序包 ID： 

1. 在 MDM 配置中暂时启用以下标志：

    - 键：`admin_debug_mode_enabled`
    - 类型：`Integer`
    - **值**：1 或 0
1. 当此标志启用时，在设备上登录到你想要知道其应用程序包 ID 的 iOS 应用。 
1. 在 Authenticator 应用中，选择“帮助” > “发送日志” > “查看日志”。 
1. 在日志文件中，查找以下行：`[ADMIN MODE] SSO extension has captured following app bundle identifiers`。 此行应捕获 SSO 扩展可见的所有应用程序包 ID。 

使用应用程序包 ID 为应用配置 SSO。 

#### <a name="allow-users-to-sign-in-from-unknown-applications-and-the-safari-browser"></a>允许用户从未知应用程序和 Safari 浏览器登录

默认情况下，只有当用户从使用 Microsoft 标识平台库（如 MSAL 或 Azure Active Directory 身份验证库 (ADAL)）的应用登录时，Microsoft 企业 SSO 插件才为已获授权的应用提供 SSO。 当另一个使用 Microsoft 标识平台库的应用在 Microsoft 企业 SSO 插件获取新令牌的过程中将其调用时，该插件也会获得共享凭据。

启用 `browser_sso_interaction_enabled` 标志后，没有使用 Microsoft 标识平台库的应用可执行初始启动并获取共享凭据。 Safari 浏览器也可执行初始启动并获取共享凭据。 

如果 Microsoft 企业 SSO 插件还没有共享凭据，则每当从 Safari 浏览器、ASWebAuthenticationSession、SafariViewController 或其他允许的本机应用程序中的 Azure AD URL 请求登录时，该插件都将尝试获取一个凭据。 

使用以下这些参数启用标志： 

- 键：`browser_sso_interaction_enabled`
- 类型：`Integer`
- **值**：1 或 0

macOS 需要此设置，以便可用在所有应用中提供一致的体验。 iOS 和 iPadOS 不需要此设置，因为大多数应用都使用 Authenticator 应用程序进行登录。 但建议启用此设置，因为如果某些应用程序未在 iOS 或 iPadOS 上使用 Authenticator 应用，则此标志会改善体验。 默认情况下，此设置处于禁用状态。

#### <a name="disable-asking-for-mfa-during-initial-bootstrapping"></a>禁止在初始启动过程中要求进行 MFA

默认情况下，在初始启动过程中和获取共享凭据期间，Microsoft 企业 SSO 插件始终会提示用户进行 MFA。 系统会提示用户进行 MFA，即使用户打开的应用程序并不需要。 此行为允许在所有其他应用程序中轻松使用共享凭据，而无需在以后需要 MFA 时提示用户。 由于在整体上减少了对用户的提示，因此此设置通常是一种很好的决策。

启用 `browser_sso_disable_mfa` 后，在初始启动过程中和获取共享凭据期间会关闭 MFA。 在这种情况下，仅当应用程序或资源需要 MFA 时才会提示用户。 

若要启用标志，请使用以下这些参数：

- 键：`browser_sso_disable_mfa`
- 类型：`Integer`
- **值**：1 或 0

建议将此标志保持为禁用状态，因为它会减少提示用户登录的次数。 如果组织很少使用 MFA，则你可能要启用该标志。 但建议更频繁地使用 MFA。 因此，该标志在默认情况下处于禁用状态。

#### <a name="disable-oauth-2-application-prompts"></a>禁用 OAuth 2 应用程序提示

Microsoft 企业 SSO 插件通过将共享凭据附加到来自允许的应用程序的网络请求中来提供 SSO。 但是，某些 OAuth 2 应用程序可能会在协议层上错误地强制执行最终用户提示。 如果你遇到此问题，则还会发现对这些应用忽略了共享凭据。 即使 Microsoft 企业 SSO 插件适用于其他应用程序，也会提示用户登录。  

启用 `disable_explicit_app_prompt` 标志会限制本机应用程序和 Web 应用程序在协议层上强制实施最终用户提示并绕过 SSO 的功能。 若要启用标志，请使用以下这些参数：

- 键：`disable_explicit_app_prompt`
- 类型：`Integer`
- **值**：1 或 0

建议启用此标志，跨所有应用获得一致的体验。 此项默认禁用。 

#### <a name="use-intune-for-simplified-configuration"></a>使用 Intune 简化配置

可以使用 Intune 作为 MDM 服务来方便配置 Microsoft 企业 SSO 插件。 例如，可以使用 Intune 启用该插件，并将旧应用添加到允许列表，以便它们实现 SSO。 

有关详细信息，请参阅 [Intune 配置文档](/intune/configuration/ios-device-features-settings)。

## <a name="use-the-sso-plug-in-in-your-application"></a>在应用程序中使用 SSO 插件

[适用于 Apple 设备的 MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-objc) 1.1.0 及更高版本支持适用于 Apple 设备的 Microsoft 企业 SSO 插件。 建议为 Microsoft 企业 SSO 插件添加支持。 它可确保获取 Microsoft 标识平台的完整功能。

如果要为一线工作人员方案构建应用程序，请参阅[适用于 iOS 设备的共享设备模式](msal-ios-shared-devices.md)，以获取设置信息。

## <a name="understand-how-the-sso-plug-in-works"></a>了解 SSO 插件的工作原理

Microsoft 企业 SSO 插件依赖于 [Apple 企业 SSO 框架](https://developer.apple.com/documentation/authenticationservices/asauthorizationsinglesignonprovider?language=objc)。 加入该框架的标识提供者可以拦截其域的网络流量，并改进或更改这些请求的处理方式。 例如，SSO 插件可以显示更多 UI，以安全方式收集最终用户凭据、需要进行 MFA 或以无提示方式向应用程序提供令牌。

本机应用程序还可以实现自定义操作，并直接与 SSO 插件进行通信。 有关详细信息，请参阅此 [2019 Apple 全球开发者会议视频](https://developer.apple.com/videos/play/tech-talks/301/)。

### <a name="applications-that-use-msal"></a>使用 MSAL 的应用程序

对于工作和学校帐户，[适用于 Apple 设备的 MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-objc) 1.1.0 及更高版本在本机支持适用于 Apple 设备的 Microsoft 企业 SSO 插件。 

如果遵循[所有建议的步骤](./quickstart-v2-ios.md)并使用默认的[重定向 URI 格式](./redirect-uris-ios.md)，则无需进行任何特殊配置。 在具有 SSO 插件的设备上，MSAL 会自动为所有交互式和无提示令牌请求调用它。 还会为帐户枚举和帐户删除操作调用它。 由于 MSAL 实现了依赖于自定义操作的本机 SSO 插件协议，此设置可为最终用户提供最流畅的本机体验。 

如果 MDM 未启用 SSO 插件，但设备上存在 Microsoft Authenticator 应用，则 MSAL 会为任何交互式令牌请求改用 Authenticator 应用。 SSO 插件与 Authenticator 应用共享 SSO。

### <a name="applications-that-dont-use-msal"></a>不使用 MSAL 的应用程序

如果管理员将没有使用 Microsoft 标识平台库（例如 MSAL）的应用程序显式添加到允许列表中，那么这些应用程序仍可实现 SSO。 

只要满足以下条件，便无需更改这些应用中的代码：

- 应用程序使用 Apple 框架运行网络请求。 例如，这些框架包括 [WKWebView](https://developer.apple.com/documentation/webkit/wkwebview) 和 [NSURLSession](https://developer.apple.com/documentation/foundation/nsurlsession)。 
- 应用程序使用标准协议与 Azure AD 通信。 例如，这些协议包括 OAuth 2、SAML 和 WS 联合身份验证。
- 应用程序不在本机 UI 中收集纯文本用户名和密码。

在这种情况下，应用程序创建网络请求并打开 Web 浏览器以让用户登录时，系统将提供 SSO。 用户重定向到 Azure AD 登录 URL 时，SSO 插件将验证 URL 并检查该 URL 是否有 SSO 凭据。 如果找到凭据，则 SSO 插件会将凭据传递到 Azure AD，后者会授权应用程序完成网络请求，而无需要求用户输入凭据。 此外，如果 Azure AD 知道该设备，那么 SSO 插件会传递设备证书，以满足基于设备的条件访问检查。 

为了支持非 MSAL 应用使用 SSO，SSO 插件实现了一个与[什么是主刷新令牌？](../devices/concept-primary-refresh-token.md#browser-sso-using-prt)中所述的 Windows 浏览器插件类似的协议。 

与基于 MSAL 的应用相比，SSO 插件对非 MSAL 应用执行的操作更加透明。 它与应用提供的现有浏览器登录体验集成。 

最终用户会看到熟悉的体验，无需在每个应用程序中都再次登录。 例如，SSO 插件不会显示本机帐户选取器，而是将 SSO 会话添加到基于 Web 的帐户选取器体验中。 

## <a name="next-steps"></a>后续步骤

了解[适用于 iOS 设备的共享设备模式](msal-ios-shared-devices.md)。