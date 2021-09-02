---
title: 使用 Azure Active Directory B2C 在示例 Android 应用程序中配置身份验证
description: 使用 Azure Active Directory B2C 在 Android 应用程序中登录并注册用户。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/05/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: b77ef28c5161c92bf8eb6c22cf62d5af41698441
ms.sourcegitcommit: 6ea4d4d1cfc913aef3927bef9e10b8443450e663
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2021
ms.locfileid: "121777789"
---
# <a name="configure-authentication-in-a-sample-android-application-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 在示例 Android 应用程序中配置身份验证

本文使用一个示例 Android 应用程序（Kotlin 和 Java），说明如何向移动应用添加 Azure Active Directory B2C (Azure AD B2C) 身份验证。

## <a name="overview"></a>概述

OpenID Connect (OIDC) 是基于 OAuth 2.0 构建的身份验证协议，可用于将用户安全登录到应用程序。 该移动应用示例将 [MSAL](../active-directory/develop/msal-overview.md) 库与 OpenId Connect 授权代码 PKCE 流结合使用。 MSAL 库是 Microsoft 提供的库，可简化向移动应用添加身份验证和授权支持的过程。 

登录流涉及以下步骤：

1. 用户打开应用并选择“登录”。
1. 应用打开移动设备的系统浏览器，并启动发送至 Azure AD B2C 的身份验证请求。
1. 用户[注册或登录](add-sign-up-and-sign-in-policy.md)、[重置密码](add-password-reset-policy.md)或使用[社交帐户](add-identity-provider.md)登录。
1. 成功登录后，Azure AD B2C 向应用返回一个授权代码。
1. 应用执行下列操作：
    1. 将此授权代码交换为 ID 令牌、访问令牌和刷新令牌。
    1. 读取 ID 令牌声明。
    1. 将令牌存储在内存中的缓存中供以后使用。

### <a name="app-registration-overview"></a>应用注册概述

若要让应用能够使用 Azure AD B2C 登录并调用 Web API，需要在 Azure AD B2C 目录中注册两个应用程序。  

- 移动应用注册能你的应用能够使用 Azure AD B2C 登录。 在应用注册过程中，请指定重定向 URI。 重定向 URI 是用户在使用 Azure AD B2C 完成身份验证后，Azure AD B2C 将用户重定向到的终结点。 此应用注册过程会生成应用程序 ID（也称为“客户端 ID”），作为该移动应用的唯一标识。  例如“应用 ID：1”。

- Web API 注册使你的应用可以调用受保护的 Web API。 注册将公开 Web API 权限（范围）。 应用注册过程将生成应用程序 ID，作为 Web API 的唯一标识。  例如，“应用 ID：2”。 向移动应用（应用 ID：1）授予对 Web API 范围（应用 ID：2）的权限。 


以下关系图描述了应用注册和应用程序体系结构。

![使用 Web API 调用注册和令牌的移动应用](./media/configure-authentication-sample-android-app/mobile-app-with-api-architecture.png) 

### <a name="call-to-a-web-api"></a>调用 Web API

[!INCLUDE [active-directory-b2c-app-integration-call-api](../../includes/active-directory-b2c-app-integration-call-api.md)]

### <a name="sign-out"></a>注销

[!INCLUDE [active-directory-b2c-app-integration-sign-out-flow](../../includes/active-directory-b2c-app-integration-sign-out-flow.md)]

## <a name="prerequisites"></a>先决条件

运行以下内容的计算机： 


- [Java 开发工具包 (JDK)](https://openjdk.java.net/) 8 或更高版本。
- [Apache Maven](https://maven.apache.org/)
- [Android API Level 16](https://developer.android.com/studio/releases/platforms) 或更高版本。
- [Android studio](https://developer.android.com/studio) 或其他代码编辑器。


## <a name="step-1-configure-your-user-flow"></a>步骤 1：配置用户流

[!INCLUDE [active-directory-b2c-app-integration-add-user-flow](../../includes/active-directory-b2c-app-integration-add-user-flow.md)]

## <a name="step-2-register-mobile-applications"></a>步骤 2：注册移动应用程序

在此步骤中，创建移动应用和 Web API 应用程序注册，并指定 Web API 的范围。

### <a name="21-register-the-web-api-app"></a>2.1 注册 Web API 应用

[!INCLUDE [active-directory-b2c-app-integration-register-api](../../includes/active-directory-b2c-app-integration-register-api.md)]

### <a name="22-configure-web-api-app-scopes"></a>2.2 配置 Web API 应用的作用域

[!INCLUDE [active-directory-b2c-app-integration-api-scopes](../../includes/active-directory-b2c-app-integration-api-scopes.md)]


### <a name="23-register-the-mobile-app"></a>2.3 注册移动应用

按照以下步骤创建移动应用注册：

1. 选择“应用注册”，然后选择“新建注册” 。
1. 输入应用程序的“名称”。 例如 android-app1。
1. 在“支持的帐户类型”下，选择“任何标识提供者或组织目录中的帐户(用于通过用户流对用户进行身份验证)” 。 
1. 在“重定向 URI”下选择“公共客户端/本机（移动和桌面）”，然后在 URL 文本框中输入下列 URI 之一： 
    - 对于 Kotlin 示例：`msauth://com.azuresamples.msalandroidkotlinapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D`
    - 对于 Java 示例：`msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D`
1. 选择“注册”  。
1. 完成应用注册后，选择“概述”。
1. 记录“应用程序（客户端）ID”，以便在稍后配置移动应用程序时使用。

    ![获取移动应用程序 ID](./media/configure-authentication-sample-android-app/get-azure-ad-b2c-app-id.png)  


### <a name="24-grant-the-mobile-app-permissions-for-the-web-api"></a>2.4 为 Web API 授予移动应用权限

[!INCLUDE [active-directory-b2c-app-integration-grant-permissions](../../includes/active-directory-b2c-app-integration-grant-permissions.md)]

## <a name="step-3-get-the-android-mobile-app-sample"></a>步骤 3：获取 Android 移动应用示例

下载下列示例之一：[Kotlin](https://github.com/Azure-Samples/ms-identity-android-kotlin/archive/refs/heads/master.zip) 或 [Java](https://github.com/Azure-Samples/ms-identity-android-java/archive/refs/heads/master.zip)。 将示例 ZIP 文件提取到工作文件夹。

或从 GitHub 克隆示例 Android 移动应用程序。 

#### <a name="kotlin"></a>[Kotlin](#tab/kotlin)


```bash
git clone https://github.com/Azure-Samples/ms-identity-android-kotlin
```

#### <a name="java"></a>[Java](#tab/java)

```bash
git clone https://github.com/Azure-Samples/ms-identity-android-java
```

--- 


## <a name="step-4-configure-the-sample-web-api"></a>步骤 4：配置示例 Web API

此示例获取一个访问令牌，该令牌具有移动应用可对 Web API 使用的相关范围。 若要通过代码调用 Web API，请执行以下步骤：

1. 使用现有 Web API 或新建一个。 有关详细信息，请参阅[使用 Azure AD B2C 在自己的 Web API 中启用身份验证](enable-authentication-web-api.md)。
1. 更改示例代码以[调用 Web API](enable-authentication-android-app.md#call-a-web-api)。

## <a name="step-5-configure-the-sample-mobile-app"></a>步骤 5：配置示例移动应用

使用 Android Studio 或其他代码编辑器打开示例项目。  然后打开 `/app/src/main/res/raw/auth_config_b2c.json` 文件。 

*auth_config_b2c.json* 配置文件包含有关 Azure AD B2C 标识提供者的信息。 移动应用使用此信息来与 Azure AD B2C 建立信任关系、将用户登录和注销、获取令牌以及验证令牌。 

更新应用设置的以下属性：

|键  |值  |
|---------|---------|
| client_id | [步骤 2.3](#23-register-the-mobile-app) 中的移动应用 ID。 | 
| [redirect_uri](../active-directory/develop/msal-client-application-configuration.md#redirect-uri) | 来自[步骤 2.3](#23-register-the-mobile-app) 的移动应用程序重定向 URI。 | 
| [authorities](../active-directory/develop/msal-client-application-configuration.md#authority)| 颁发机构是一个 URL，表示 MSAL 可从中请求令牌的目录。 使用以下格式：`https://<your-tenant-name>.b2clogin.com/<your-tenant-name>.onmicrosoft.com/<your-sign-in-sign-up-policy>`。 将 `<your-tenant-name>` 替换为 Azure AD B2C [租户的名称](tenant-management.md#get-your-tenant-name)。 然后，将 `<your-sign-in-sign-up-policy>` 替换为在[步骤 1](#step-1-configure-your-user-flow) 中创建的用户流或自定义策略。 | 


打开 `B2CConfiguration` 类，并更新以下类成员：

|键  |值  |
|---------|---------|
| 策略| 列出用户流，或在[步骤 1](#step-1-configure-your-user-flow) 中创建的自定义策略。|
| azureAdB2CHostName| Azure AD B2C [租户名称](tenant-management.md#get-your-tenant-name)的第一部分。 例如 `https://contoso.b2clogin.com`。|
| tenantName| Azure AD B2C 租户的完整[租户名称](tenant-management.md#get-your-tenant-name)。 例如 `contoso.onmicrosoft.com`。|
| 范围| 在[步骤 2.4](#24-grant-the-mobile-app-permissions-for-the-web-api) 中创建的 Web API 范围。|


## <a name="step-6-run-and-test-the-mobile-app"></a>步骤 6：运行并测试移动应用

1. 生成并运行该项目。
1. 选择汉堡图标。
    
    ![屏幕截图演示了如何选择汉堡图标。](./media/configure-authentication-sample-android-app/select-hamburger-icon.png)

1. 选择“B2C 模式”。

    ![屏幕截图演示了如何选择 B2C 模式。](./media/configure-authentication-sample-android-app/select-azure-ad-b2c-mode.png)

1. 选择“运行用户流”。

    ![屏幕截图演示了如何启动登录流。](./media/configure-authentication-sample-android-app/select-policy-and-sign-in.png)

1. 使用你的 Azure AD B2C 本地帐户或社交帐户注册或登录。

1. 身份验证成功后，你会在导航栏中看到显示名称。

    ![Azure AD B2C 访问令牌和用户 ID。](./media/configure-authentication-sample-android-app/access-token.png) 

## <a name="next-steps"></a>后续步骤

* 了解如何[在自己的 Android 应用程序中启用身份验证](enable-authentication-android-app.md)
* [在 Android 应用程序中配置身份验证选项](enable-authentication-android-app-options.md)
* [在自己的 Web API 中启用身份验证](enable-authentication-web-api.md)
