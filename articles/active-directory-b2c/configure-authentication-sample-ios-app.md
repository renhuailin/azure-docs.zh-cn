---
title: 使用 Azure Active Directory B2C 在示例 iOS Swift 应用程序中配置身份验证
description: 使用 Azure Active Directory B2C 在 iOS Swift 应用程序中登录并注册用户。
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
ms.openlocfilehash: 1e6a76b5a534bb127dfaaab8e787cb446430c8d6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778180"
---
# <a name="configure-authentication-in-a-sample-ios-swift-application-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 在示例 iOS Swift 应用程序中配置身份验证

本文使用一个示例 [iOS Swift](https://developer.apple.com/swift/) 应用程序，说明如何向移动应用添加 Azure Active Directory B2C (Azure AD B2C) 身份验证。

## <a name="overview"></a>概述

OpenID Connect (OIDC) 是基于 OAuth 2.0 构建的身份验证协议，可用于将用户安全地登录到应用程序。 该移动应用示例将 [MSAL](../active-directory/develop/msal-overview.md) 库与 OpenId Connect 授权代码 PKCE 流结合使用。 MSAL 库是 Microsoft 提供的库，可简化向移动应用添加身份验证和授权支持的过程。 

登录流涉及以下步骤：

1. 用户打开应用并选择“登录”。
1. 应用打开移动设备的系统浏览器，并启动发送至 Azure AD B2C 的身份验证请求。
1. 用户[注册或登录](add-sign-up-and-sign-in-policy.md)、[重置密码](add-password-reset-policy.md)或使用[社交帐户](add-identity-provider.md)登录。
1. 成功登录后，Azure AD B2C 向应用返回一个授权代码。
1. 应用执行下列操作：
    1. 使用此授权代码来交换 ID 令牌、访问令牌和刷新令牌。
    1. 读取 ID 令牌声明。
    1. 将令牌存储到内存中缓存供以后使用。

### <a name="app-registration-overview"></a>应用注册概述

若要让应用能够使用 Azure AD B2C 登录并调用 Web API，需要在 Azure AD B2C 目录中注册两个应用程序。  

- 注册移动应用后，你的应用就能够使用 Azure AD B2C 进行登录。 在应用注册过程中，请指定重定向 URI。 重定向 URI 是用户通过 Azure AD B2C 完成身份验证之后 Azure AD B2C 将用户重定向到的终结点。 应用注册过程会生成一个应用程序 ID（也称为“客户端 ID”），作为该移动应用的唯一标识。  例如“应用 ID：1”。

- Web API 注册使你的应用可以调用受保护的 Web API。 注册将公开 Web API 权限（范围）。 应用注册过程会生成一个应用程序 ID，作为 Web API 的唯一标识。  例如，“应用 ID：2”。 向移动应用（应用 ID：1）授予对 Web API 作用域（应用 ID：2）的权限。 


以下关系图描述了应用注册和应用程序体系结构。

![图中描绘了涉及 Web API、注册和令牌的移动应用。](./media/configure-authentication-sample-ios-app/mobile-app-with-api-architecture.png) 

### <a name="call-to-a-web-api"></a>调用 Web API

[!INCLUDE [active-directory-b2c-app-integration-call-api](../../includes/active-directory-b2c-app-integration-call-api.md)]

### <a name="sign-out"></a>注销

[!INCLUDE [active-directory-b2c-app-integration-sign-out-flow](../../includes/active-directory-b2c-app-integration-sign-out-flow.md)]

## <a name="prerequisites"></a>先决条件

运行以下内容的计算机： 

- [Xcode](https://developer.apple.com/xcode/) 13 或更高版本。
- 适用于 Swift 和 Objective-C Cocoa 项目的 [CocoaPods](https://cocoapods.org/) 依赖项管理器。


## <a name="step-1-configure-your-user-flow"></a>步骤 1：配置用户流

[!INCLUDE [active-directory-b2c-app-integration-add-user-flow](../../includes/active-directory-b2c-app-integration-add-user-flow.md)]

## <a name="step-2-register-mobile-applications"></a>步骤 2：注册移动应用程序

在此步骤中，请创建移动应用和 Web API 应用程序注册，并指定 Web API 的作用域。

### <a name="21-register-the-web-api-app"></a>2.1 注册 Web API 应用

[!INCLUDE [active-directory-b2c-app-integration-register-api](../../includes/active-directory-b2c-app-integration-register-api.md)]

### <a name="22-configure-web-api-app-scopes"></a>2.2 配置 Web API 应用的作用域

[!INCLUDE [active-directory-b2c-app-integration-api-scopes](../../includes/active-directory-b2c-app-integration-api-scopes.md)]


### <a name="23-register-the-mobile-app"></a>2.3 注册移动应用

按照以下步骤创建移动应用注册：

1. 选择“应用注册”，然后选择“新建注册” 。
1. 输入应用程序的“名称”。 例如 iOs-app1。
1. 在“支持的帐户类型”下，选择“任何标识提供者或组织目录中的帐户(用于通过用户流对用户进行身份验证)” 。 
1. 在“重定向 URI”下，选择“公共客户端/本机(移动和桌面)”，然后输入 `msauth.com.microsoft.identitysample.MSALiOS://auth` 。
1. 选择“注册”  。
1. 完成应用注册后，选择“概述”。
1. 记录“应用程序(客户端) ID”，以便在稍后配置移动应用程序时使用。
    ![屏幕截图显示了如何获取移动应用程序 ID。](./media/configure-authentication-sample-ios-app/get-azure-ad-b2c-app-id.png)  


### <a name="24-grant-the-mobile-app-permissions-for-the-web-api"></a>2.4 为 Web API 授予移动应用权限

[!INCLUDE [active-directory-b2c-app-integration-grant-permissions](../../includes/active-directory-b2c-app-integration-grant-permissions.md)]

## <a name="step-3-configure-the-sample-web-api"></a>步骤 3：配置示例 Web API

此示例获取移动应用可以在相关作用域内用于 Web API 的访问令牌。  若要通过代码调用 Web API，请执行以下步骤：

1. 使用现有 Web API 或新建一个。 有关详细信息，请参阅[使用 Azure AD B2C 在自己的 Web API 中启用身份验证](enable-authentication-web-api.md)。
1. 更改示例代码以[调用 Web API](enable-authentication-iOs-app.md#call-a-web-api)。

配置 Web API 后，复制 Web API 终结点的 URI。 你在后续步骤中将使用该 Web API 终结点。

> [!TIP]
> 如果没有 Web API，仍可运行此示例。 在这种情况下，应用会返回访问令牌，但无法调用 Web API。 

## <a name="step-4-get-the-ios-mobile-app-sample"></a>步骤 4：获取 iOS 移动应用示例

1. [下载 zip 文件](https://github.com/Azure-Samples/active-directory-b2c-ios-swift-native-msal/archive/refs/heads/vNext.zip)，或从 [GitHub 存储库](https://github.com/Azure-Samples/active-directory-b2c-ios-swift-native-msal)克隆示例 Web 应用程序。 

    ```bash
    git clone https://github.com/Azure-Samples/active-directory-b2c-ios-swift-native-msal/tree/vNext.git
    ``` 

1. 使用 [CocoaPods](https://cocoapods.org/) 安装 MSAL 库。 在终端窗口中，导航到项目根文件夹。 此文件夹包含 `podfile`。 运行以下命令：

    ```bash
    pod install
    ```

1. 使用 Xcode 打开 `MSALiOS.xcworkspace` 工作区。



## <a name="step-5-configure-the-sample-mobile-app"></a>步骤 5：配置示例移动应用

打开 `ViewController.swift` 文件。 `ViewController` 类成员包含有关 Azure AD B2C 标识提供者的信息。 移动应用使用此信息来与 Azure AD B2C 建立信任关系、将用户登录和注销、获取令牌以及验证令牌。 

更新以下成员：

|键  |值  |
|---------|---------|
|kTenantName| Azure AD B2C 租户的完整[租户名称](tenant-management.md#get-your-tenant-name)。 例如 `contoso.onmicrosoft.com`。|
|kAuthorityHostName|Azure AD B2C [租户名称](tenant-management.md#get-your-tenant-name)的第一部分。 例如 `contoso.b2clogin.com`。|
|kClientID|[步骤 2.3](#23-register-the-mobile-app) 中的移动应用 ID。|
|kRedirectUri|[步骤 2.3](#23-register-the-mobile-app) 中的移动应用程序重定向 URI，即 `msauth.com.microsoft.identitysample.MSALiOS://auth`。|
|kSignupOrSigninPolicy| 你在[步骤 1](#step-1-configure-your-user-flow) 中创建的注册或登录用户流或自定义策略。|
|kEditProfilePolicy|编辑你在[步骤 1](#step-1-configure-your-user-flow) 中创建的个人资料用户流或自定义策略。|
|kGraphURI| （可选）你在[步骤 3](#step-3-configure-the-sample-web-api) 中创建的 Web API 终结点。 例如 `https://contoso.azurewebsites.net/hello`。|
| kScopes | 你在[步骤 2.4](#24-grant-the-mobile-app-permissions-for-the-web-api) 中创建的 Web API 作用域。| 



## <a name="step-6-run-and-test-the-mobile-app"></a>步骤 6：运行并测试移动应用

1. 使用[连接的 iOS 设备的模拟器](https://developer.apple.com/documentation/xcode/running-your-app-in-the-simulator-or-on-a-device)生成并运行项目。

1. 选择“登录”。 然后，使用你的 Azure AD B2C 本地帐户或社交帐户注册或登录。

    ![屏幕截图演示了如何启动登录流。](./media/configure-authentication-sample-ios-app/sign-in.png)

1. 身份验证成功后，你会在导航栏中看到显示名称。

    ![屏幕截图显示了 Azure AD B2C 访问令牌和用户 ID。](./media/configure-authentication-sample-ios-app/post-sign-in.png) 

## <a name="next-steps"></a>后续步骤

* 了解如何[在自己的 iOS 应用程序中启用身份验证](enable-authentication-ios-app.md)
* [在 iOS 应用程序中配置身份验证选项](enable-authentication-ios-app-options.md)
* [在自己的 Web API 中启用身份验证](enable-authentication-web-api.md)
