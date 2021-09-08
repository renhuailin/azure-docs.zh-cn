---
title: 使用 Azure AD B2C 在 iOS Swift 应用中启用身份验证
description: 本文介绍如何使用 Azure Active Directory B2C 构建基块在 iOS Swift 应用程序中启用身份验证。 了解如何使用 Azure AD B2C 在 iOS Swift 应用程序中登录和注册用户。
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
ms.openlocfilehash: bb590ee57cc78c27c0e6cec7dc54cc15a9fd7648
ms.sourcegitcommit: ef448159e4a9a95231b75a8203ca6734746cd861
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123186094"
---
# <a name="enable-authentication-in-your-own-ios-swift-app-by-using-azure-ad-b2c"></a>使用 Azure AD B2C 在你自己的 iOS Swift 应用中启用身份验证

本文介绍如何将 Azure Active Directory B2C (Azure AD B2C) 身份验证添加到你自己的 iOS Swift 移动应用程序。 了解如何将 iOS Swift 应用程序与[适用于 iOS 的 Microsoft 身份验证库 (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-objc) 集成。 

将本文与[在示例 iOS Swift 应用程序中配置身份验证](./configure-authentication-sample-ios-app.md)结合使用，并用你自己的 iOS Swift 应用来替换示例 iOS Swift 应用。 你完成本文中的说明后，应用程序将接受通过 Azure AD B2C 进行登录。

## <a name="prerequisites"></a>先决条件

查看[使用 Azure AD B2C 在示例 iOS Swift 应用中配置身份验证](configure-authentication-sample-ios-app.md)中的先决条件和集成说明。

## <a name="create-an-ios-swift-app-project"></a>创建 iOS Swift 应用项目

如果你还没有 iOS Swift 应用程序，请执行以下步骤设置新项目：

1. 打开 [Xcode](https://developer.apple.com/xcode/)，然后选择“文件” > “新建” > “项目”  。
1. 对于 iOS 应用，请选择“iOS” > “应用”，然后选择“下一步”  。
1. 对于“为新项目选择选项”，请提供以下信息：
    1. “产品名称”，例如 `MSALiOS`。
    1. “组织标识符”，例如 `contoso.com`。
    1. 对于“界面”，请选择“Storyboard” 。
    1. 对于“生命周期”，请选择“UIKit 应用委托”。
    1. 对于“语言”，请选择“Swift” 。 
1. 选择“下一步”。
1. 选择用于创建应用的文件夹，然后选择“创建”。


## <a name="step-1-install-the-msal-library"></a>步骤 1：安装 MSAL 库

1. 使用 [CocoaPods](https://cocoapods.org/) 安装 MSAL 库。   在你的项目的 .xcodeproj 文件所在的文件夹中，如果 podfile 文件不存在，请创建一个名为 podfile 的空文件  。 将以下代码添加到 Podfile 文件：

   ```
   use_frameworks!

   target '<your-target-here>' do
      pod 'MSAL'
   end
   ```

1. 将 `<your-target-here>` 替换为项目的名称（例如 `MSALiOS`）。 有关详细信息，请参阅 [Podfile 语法参考](https://guides.cocoapods.org/syntax/podfile.html#podfile)。
1. 在终端窗口中，转到包含 podfile 文件的文件夹，然后运行 pod install 以安装 MSAL 库 。
1. 运行 `pod install` 命令后，会创建一个 \<your project name>.xcworkspace 文件。 若要在 Xcode 中重新加载项目，请关闭 Xcode，然后打开 \<your project name>.xcworkspace 文件。

## <a name="step-2-set-the-app-url-scheme"></a>步骤 2：设置应用 URL 方案

当用户进行身份验证时，Azure AD B2C 会使用在 Azure AD B2C 应用程序注册中配置的重定向 URI 向应用发送授权代码。 

MSAL 默认重定向 URI 的格式为 `msauth.[Your_Bundle_Id]://auth`。 例如 `msauth.com.microsoft.identitysample.MSALiOS://auth`，其中 `msauth.com.microsoft.identitysample.MSALiOS` 是 URL 方案。

在此步骤中，请使用 `CFBundleURLSchemes` 数组注册 URL 方案。 你的应用程序会侦听 URL 方案，以捕获来自 Azure AD B2C 的回调。 

在 Xcode 中，将 [Info.plist 文件](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Introduction/Introduction.html)作为源代码文件打开。 在 `<dict>` 部分中，添加以下 XML 片段： 

```xml
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>msauth.com.microsoft.identitysample.MSALiOS</string>
        </array>
    </dict>
</array>
<key>LSApplicationQueriesSchemes</key>
<array>
    <string>msauthv2</string>
    <string>msauthv3</string>
</array>
```

## <a name="step-3-add-the-authentication-code"></a>步骤 3：添加身份验证代码

[示例代码](configure-authentication-sample-ios-app.md#step-4-get-the-ios-mobile-app-sample)包含一个 `UIViewController` 类。 该类：

- 定义用户界面的结构。
- 包含有关 Azure AD B2C 标识提供者的信息。 应用使用此信息来建立与 Azure AD B2C 之间的信任关系。 
- 包含用于对用户进行身份验证、获取令牌并对其进行验证的身份验证代码。

选择用户将在其中进行身份验证的 `UIViewController`。  在 `UIViewController` 中，将代码和 [GitHub 中提供的代码](https://github.com/Azure-Samples/active-directory-b2c-ios-swift-native-msal/blob/vNext/MSALiOS/ViewController.swift)合并。 

## <a name="step-4-configure-your-ios-swift-app"></a>步骤 4：配置 iOS Swift 应用

[添加身份验证代码](#step-3-add-the-authentication-code)后，为你的 iOS Swift 应用配置 Azure AD B2C 设置。 Azure AD B2C 标识提供者设置是在上一部分选择的 `UIViewController` 类中配置的。 

若要了解如何配置 iOS Swift 应用，请参阅[使用 Azure AD B2C 在示例 iOS Swift 应用中配置身份验证](configure-authentication-sample-ios-app.md#step-5-configure-the-sample-mobile-app)。

## <a name="step-5-run-and-test-the-mobile-app"></a>步骤 5：运行并测试移动应用

1. 使用[连接的 iOS 设备的模拟器](https://developer.apple.com/documentation/xcode/running-your-app-in-the-simulator-or-on-a-device)生成并运行项目。
1. 选择“登录”，然后使用你的 Azure AD B2C 本地帐户或社交帐户注册或登录。
1. 成功进行身份验证后，你将在导航栏中看到显示名称。

## <a name="step-6-customize-your-code-building-blocks"></a>步骤 6：自定义代码构建基块

本部分介绍了用来为 iOS Swift 应用启用身份验证的代码构建基块。 它列出了 UIViewController 的方法并介绍了自定义代码的方式。 

### <a name="step-61-instantiate-a-public-client-application"></a>步骤 6.1：实例化公用客户端应用程序

公共客户端应用程序不可信，不能安全地保存应用程序机密，并且它们也不包含客户端机密。 在 [viewDidLoad](https://developer.apple.com/documentation/uikit/uiviewcontroller/1621495-viewdidload) 中，使用一个公共客户端应用程序对象来实例化 MSAL。

下面的 Swift 代码片段演示了如何使用 `MSALPublicClientApplicationConfig` 配置对象来初始化 MSAL。 

配置对象提供了有关 Azure AD B2C 环境的信息。 例如，它提供客户端 ID、重定向 URI 和向 Azure AD B2C 生成身份验证请求的授权。 有关配置对象的信息，请参阅[配置示例移动应用](configure-authentication-sample-ios-app.md#step-5-configure-the-sample-mobile-app)。

```swift
do {

    let signinPolicyAuthority = try self.getAuthority(forPolicy: self.kSignupOrSigninPolicy)
    let editProfileAuthority = try self.getAuthority(forPolicy: self.kEditProfilePolicy)
    
    let pcaConfig = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: kRedirectUri, authority: signinPolicyAuthority)
    pcaConfig.knownAuthorities = [signinPolicyAuthority, editProfileAuthority]
    
    self.applicationContext = try MSALPublicClientApplication(configuration: pcaConfig)
    self.initWebViewParams()
    
    } catch {
        self.updateLoggingText(text: "Unable to create application \(error)")
    }
```

`initWebViewParams` 方法配置[交互式身份验证](../active-directory/develop/customize-webviews.md)体验。 

下面的 Swift 代码片段使用系统 Web 视图来初始化 `webViewParameters` 类成员。 有关详细信息，请参阅[针对 iOS/macOS 自定义浏览器和 Web 视图](../active-directory/develop/customize-webviews.md)。

```swift
func initWebViewParams() {
    self.webViewParameters = MSALWebviewParameters(authPresentationViewController: self)
    self.webViewParameters?.webviewType = .default
}
```

### <a name="step-62-start-an-interactive-authorization-request"></a>步骤 6.2：启动交互式授权请求

交互式授权请求是一个流，其中将使用系统 Web 视图提示用户进行注册或登录。 当用户选择“登录”按钮时，会调用 `authorizationButton` 方法。

`authorizationButton` 方法使用授权请求的相关数据准备 `MSALInteractiveTokenParameters` 对象。 `acquireToken` 方法使用 `MSALInteractiveTokenParameters` 通过系统 Web 视图对用户进行身份验证。

以下代码片段演示如何启动交互式授权请求： 

```swift
let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: self.webViewParameters!)
parameters.promptType = .selectAccount
parameters.authority = authority

applicationContext.acquireToken(with: parameters) { (result, error) in

// On error code    
guard let result = result else {
    self.updateLoggingText(text: "Could not acquire token: \(error ?? "No error information" as! Error)")
    return
}

// On success code
self.accessToken = result.accessToken
self.updateLoggingText(text: "Access token is \(self.accessToken ?? "Empty")")
}
```
 
在用户完成授权流后（可能成功也可能不成功），结果会返回到 `acquireToken` 方法的[闭包](https://docs.swift.org/swift-book/LanguageGuide/Closures.html)。 

`acquireToken` 方法返回 `result` 和 `error` 对象。 使用此闭包可以：

- 在身份验证完成后使用信息来更新移动应用 UI。
- 使用访问令牌调用 Web API 服务。
- 处理身份验证错误（例如，当用户取消登录流时）。
 
### <a name="step-63-call-a-web-api"></a>步骤 6.3：调用 Web API

若要调用[基于令牌的授权 Web API](enable-authentication-web-api.md)，应用需要一个有效的访问令牌。 此应用执行以下操作：

1. 获取对 Web API 终结点拥有所需权限（范围）的访问令牌。
1. 使用以下格式，在 HTTP 请求的授权标头中将该访问令牌作为持有者令牌进行传递：

```http
Authorization: Bearer <access-token>
```

当用户[以交互方式进行身份验证](#step-62-start-an-interactive-authorization-request)时，应用会在 `acquireToken` 闭包中获取访问令牌。 对于后续的 Web API 调用，请使用以无提示方式获取令牌的方法 (`acquireTokenSilent`)，如本部分所述。 

`acquireTokenSilent` 方法执行以下操作：

1. 它尝试从令牌缓存中提取具有所请求范围的访问令牌。 如果该令牌存在且未过期，则会返回该令牌。 
1. 如果该令牌不存在于令牌缓存中或者已过期，则 MSAL 库会尝试使用刷新令牌来获取新的访问令牌。 
1. 如果该刷新令牌不存在或已过期，则返回异常。 在这种情况下，你应当提示用户[以交互方式登录](#step-62-start-an-interactive-authorization-request)。  

以下代码片段演示如何获取访问令牌：

```swift
do {

// Get the authority using the sign-in or sign-up user flow
let authority = try self.getAuthority(forPolicy: self.kSignupOrSigninPolicy)

// Get the current account from the application context
guard let thisAccount = try self.getAccountByPolicy(withAccounts: applicationContext.allAccounts(), policy: kSignupOrSigninPolicy) else {
    self.updateLoggingText(text: "There is no account available!")
    return
}

// Configure the acquire token silent parameters
let parameters = MSALSilentTokenParameters(scopes: kScopes, account:thisAccount)
parameters.authority = authority
parameters.loginHint = "username"

// Acquire token silent
self.applicationContext.acquireTokenSilent(with: parameters) { (result, error) in
    if let error = error {
        
        let nsError = error as NSError
        
        // interactionRequired means we need to ask the user to sign in. This usually happens
        // when the user's Refresh Token is expired or if the user has changed their password
        // among other possible reasons.
        
        if (nsError.domain == MSALErrorDomain) {
            
            if (nsError.code == MSALError.interactionRequired.rawValue) {
                
                // Start an interactive authorization code
                // Notice we supply the account here. This ensures we acquire token for the same account
                // as we originally authenticated.
                
                ...
            }
        }
        
        self.updateLoggingText(text: "Could not acquire token: \(error)")
        return
    }
    
    guard let result = result else {
        
        self.updateLoggingText(text: "Could not acquire token: No result returned")
        return
    }
    
    // On success, set the access token to the accessToken class member. 
    // The callGraphAPI method uses the access token to call a web API  
    self.accessToken = result.accessToken
    ...
}
} catch {
self.updateLoggingText(text: "Unable to construct parameters before calling acquire token \(error)")
}
```

`callGraphAPI` 方法检索访问令牌，并调用 Web API，如下所示：

```swift
@objc func callGraphAPI(_ sender: UIButton) {
    guard let accessToken = self.accessToken else {
        self.updateLoggingText(text: "Operation failed because could not find an access token!")
        return
    }
    
    let sessionConfig = URLSessionConfiguration.default
    sessionConfig.timeoutIntervalForRequest = 30
    let url = URL(string: self.kGraphURI)
    var request = URLRequest(url: url!)
    request.setValue("Bearer \(accessToken)", forHTTPHeaderField: "Authorization")
    let urlSession = URLSession(configuration: sessionConfig, delegate: self, delegateQueue: OperationQueue.main)
    
    self.updateLoggingText(text: "Calling the API....")
    
    urlSession.dataTask(with: request) { data, response, error in
        guard let validData = data else {
            self.updateLoggingText(text: "Could not call API: \(error ?? "No error information" as! Error)")
            return
        }
        
        let result = try? JSONSerialization.jsonObject(with: validData, options: [])
        
        guard let validResult = result as? [String: Any] else {
            self.updateLoggingText(text: "Nothing returned from API")
            return
        }
        
        self.updateLoggingText(text: "API response: \(validResult.debugDescription)")
        }.resume()
}
```

### <a name="step-64-sign-out-users"></a>步骤 6.4：注销用户

通过 MSAL 进行注销会从应用程序中删除关于用户的所有已知信息。 使用注销方法可注销用户并更新 UI。 例如，可以隐藏受保护的 UI 元素、隐藏注销按钮或显示登录按钮。

下面的代码片段演示了如何注销用户：

```swift
@objc func signoutButton(_ sender: UIButton) {
do {
    
    
    let thisAccount = try self.getAccountByPolicy(withAccounts: applicationContext.allAccounts(), policy: kSignupOrSigninPolicy)
    
    if let accountToRemove = thisAccount {
        try applicationContext.remove(accountToRemove)
    } else {
        self.updateLoggingText(text: "There is no account to signing out!")
    }
    
    ...
    
} catch  {
    self.updateLoggingText(text: "Received error signing out: \(error)")
}
}
```

## <a name="next-steps"></a>后续步骤

了解如何：
* [使用 Azure AD B2C 在 iOS Swift 应用中配置身份验证选项](enable-authentication-ios-app-options.md)
* [使用 Azure AD B2C 在自己的 Web API 中启用身份验证](enable-authentication-web-api.md)
