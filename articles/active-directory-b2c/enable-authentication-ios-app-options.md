---
title: 使用 Azure Active Directory B2C 启用 iOS Swift 移动应用程序选项
description: 本文讨论使用 Azure Active Directory B2C 启用 iOS Swift 移动应用程序选项的几种方法。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/29/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 128c5000e6614e769100a92303007317eadaed96
ms.sourcegitcommit: ef448159e4a9a95231b75a8203ca6734746cd861
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123220703"
---
# <a name="enable-authentication-options-in-an-ios-swift-app-by-using-azure-ad-b2c"></a>使用 Azure AD B2C 在 iOS Swift 应用中启用身份验证选项 

本文介绍如何启用、自定义和增强 iOS Swift 应用程序的 Azure Active Directory B2C (Azure AD B2C) 身份验证体验。 

在开始之前，请先熟悉以下文章： 
* [使用 Azure AD B2C 在示例 iOS Swift 应用中配置身份验证](configure-authentication-sample-ios-app.md)
* [使用 Azure AD B2C 在你自己的 iOS Swift 应用中启用身份验证](enable-authentication-ios-app.md)

[!INCLUDE [active-directory-b2c-app-integration-custom-domain](../../includes/active-directory-b2c-app-integration-custom-domain.md)]

若要在身份验证 URL 中使用自定义域和租户 ID，请执行以下操作： 

1. 请遵循[启用自定义域](custom-domain.md)中的指导。
1. 将 `kAuthorityHostName` 类成员更新为你的自定义域。
1. 将 `kTenantName` 类成员更新为你的[租户 ID](tenant-management.md#get-your-tenant-id)。

以下 Swift 代码显示了更改前的应用设置：

```swift
let kTenantName = "contoso.onmicrosoft.com" 
let kAuthorityHostName = "contoso.b2clogin.com" 
```

以下 Swift 代码显示了更改后的应用设置：

```swift
let kTenantName = "00000000-0000-0000-0000-000000000000" 
let kAuthorityHostName = "login.contoso.com" 
```

[!INCLUDE [active-directory-b2c-app-integration-login-hint](../../includes/active-directory-b2c-app-integration-login-hint.md)]

1. 如果使用的是自定义策略，请按照[设置直接登录](direct-signin.md#prepopulate-the-sign-in-name)中的说明添加所需的输入声明。 
1. 查找 Microsoft 身份验证库 (MSAL) 配置对象，然后添加含登录提示的 `withLoginHint()` 方法。

```swift
let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: self.webViewParameters!)
parameters.promptType = .selectAccount
parameters.authority = authority
parameters.loginHint = "bob@contoso.com"
// More settings here

applicationContext.acquireToken(with: parameters) { (result, error) in
...
```

[!INCLUDE [active-directory-b2c-app-integration-domain-hint](../../includes/active-directory-b2c-app-integration-domain-hint.md)]

1. 检查外部标识提供者的域名。 有关详细信息，请参阅[将登录重定向到社交服务提供商](direct-signin.md#redirect-sign-in-to-a-social-provider)。 
1. 创建或使用现有列表对象来存储额外的查询参数。
1. 将具有相应域名的 `domain_hint` 参数添加到列表中（如 `facebook.com`）。
1. 将额外的查询参数列表传递到 MSAL 配置对象的 `extraQueryParameters` 属性中。

```swift
let extraQueryParameters: [String: String] = ["domain_hint": "facebook.com"]

let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: self.webViewParameters!)
parameters.promptType = .selectAccount
parameters.authority = authority
parameters.extraQueryParameters = extraQueryParameters
// More settings here

applicationContext.acquireToken(with: parameters) { (result, error) in
...
```

[!INCLUDE [active-directory-b2c-app-integration-ui-locales](../../includes/active-directory-b2c-app-integration-ui-locales.md)]

1. [配置语言自定义](language-customization.md)。
1. 创建或使用现有列表对象来存储额外的查询参数。
1. 将具有相应语言代码的 `ui_locales` 参数添加到列表中（如 `en-us`）。
1. 将额外的查询参数列表传递到 MSAL 配置对象的 `extraQueryParameters` 属性中。

```swift
let extraQueryParameters: [String: String] = ["ui_locales": "en-us"]

let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: self.webViewParameters!)
parameters.promptType = .selectAccount
parameters.authority = authority
parameters.extraQueryParameters = extraQueryParameters
// More settings here

applicationContext.acquireToken(with: parameters) { (result, error) in
...
```

[!INCLUDE [active-directory-b2c-app-integration-custom-parameters](../../includes/active-directory-b2c-app-integration-custom-parameters.md)]

1. 配置 [ContentDefinitionParameters](customize-ui-with-html.md#configure-dynamic-custom-page-content-uri) 元素。
1. 创建或使用现有列表对象来存储额外的查询参数。
1. 添加自定义查询字符串参数（如 `campaignId`）。 设置参数值（如 `germany-promotion`）。
1. 将额外的查询参数列表传递到 MSAL 配置对象的 `extraQueryParameters` 属性中。

```swift
let extraQueryParameters: [String: String] = ["campaignId": "germany-promotion"]

let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: self.webViewParameters!)
parameters.promptType = .selectAccount
parameters.authority = authority
parameters.extraQueryParameters = extraQueryParameters
// More settings here

applicationContext.acquireToken(with: parameters) { (result, error) in
...
```


[!INCLUDE [active-directory-b2c-app-integration-id-token-hint](../../includes/active-directory-b2c-app-integration-id-token-hint.md)]

1. 在自定义策略中，定义 [ID 令牌提示技术配置文件](id-token-hint.md)。
1. 在代码中，生成或获取 ID 令牌，然后将令牌设置为变量（如 `idToken`）。 
1. 创建或使用现有列表对象来存储额外的查询参数。
1. 使用可存储 ID 令牌的相应变量添加 `id_token_hint` 参数。
1. 将额外的查询参数列表传递到 MSAL 配置对象的 `extraQueryParameters` 属性中。

```swift
let extraQueryParameters: [String: String] = ["id_token_hint": idToken]

let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: self.webViewParameters!)
parameters.promptType = .selectAccount
parameters.authority = authority
parameters.extraQueryParameters = extraQueryParameters
// More settings here

applicationContext.acquireToken(with: parameters) { (result, error) in
...
```

[!INCLUDE [active-directory-b2c-app-integration-logging](../../includes/active-directory-b2c-app-integration-logging.md)]


在发出任何 MSAL 请求之前，应尽早在应用启动序列中设置 MSAL 记录器。 在 AppDelegate.swift `application` 方法中配置 MSAL [日志记录](../active-directory/develop/msal-logging-ios.md)。

下面的代码片段演示如何配置 MSAL 日志记录：

```swift
func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
        
        MSALGlobalConfig.loggerConfig.logLevel = .verbose
        MSALGlobalConfig.loggerConfig.setLogCallback { (logLevel, message, containsPII) in
            
            // If PiiLoggingEnabled is set YES, this block will potentially contain sensitive information (Personally Identifiable Information), but not all messages will contain it.
            // containsPII == YES indicates if a particular message contains PII.
            // You might want to capture PII only in debug builds, or only if you take necessary actions to handle PII properly according to legal requirements of the region
            if let displayableMessage = message {
                if (!containsPII) {
                    #if DEBUG
                    // NB! This sample uses print just for testing purposes
                    // You should only ever log to NSLog in debug mode to prevent leaking potentially sensitive information
                    print(displayableMessage)
                    #endif
                }
            }
        }
        return true
    }
```

## <a name="embedded-web-view-experience"></a>嵌入式 Web 视图体验

交互式身份验证需要使用 Web 浏览器。 默认情况下，MSAL 库使用系统 Web 视图。 在登录期间，MSAL 库会弹出带有 Azure AD B2C 用户界面的 iOS 系统 Web 视图。  

有关详细信息，请参阅[针对 iOS/macOS 自定义浏览器和 Web 视图](../active-directory/develop/customize-webviews.md)一文。

根据你的要求，你可以使用嵌入式 Web 视图。 MSAL 中的嵌入式 Web 视图和系统 Web 视图之间存在视觉和单一登录行为差异。

![屏幕截图展示系统 Web 视图体验和嵌入式 Web 视图体验之间的差异。](./media/enable-authentication-ios-app-options/system-web-browser-vs-embedded-view.png)

> [!IMPORTANT]
> 建议使用平台默认值，通常为系统浏览器。 系统浏览器能够更好地记住以前登录的用户。 某些标识提供者（如 Google）不支持嵌入式视图体验。

要更改此行为，请将 `MSALWebviewParameters` 的 `webviewType` 属性更改为 `wkWebView`。 以下示例演示了如何将 Web 视图类型更改为嵌入式视图： 

```swift
func initWebViewParams() {
    self.webViewParameters = MSALWebviewParameters(authPresentationViewController: self)
    
    // Use embedded view experience
    self.webViewParameters?.webviewType = .wkWebView
}
```

## <a name="next-steps"></a>后续步骤

- 若要了解更多信息，请参阅[适用于 iOS Swift 的 MSAL 配置选项](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)。
