---
title: 使用 Azure Active Directory B2C 在示例 WPF 桌面应用程序中配置身份验证
description: 使用 Azure Active Directory B2C 在 WPF 桌面应用程序中登录并注册用户。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 08/04/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: fb9f90e93ed13faf64703cb511cd540f9af685ba
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778174"
---
# <a name="configure-authentication-in-a-sample-wpf-desktop-application-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 在示例 WPF 桌面应用程序中配置身份验证

本文使用一个示例 [WPF 桌面](/visualstudio/designers/getting-started-with-wpf.md)应用程序，说明如何向桌面应用添加 Azure Active Directory B2C (Azure AD B2C) 身份验证。

## <a name="overview"></a>概述

OpenID Connect (OIDC) 是基于 OAuth 2.0 构建的身份验证协议，可用于将用户安全登录到应用程序。 该桌面应用示例将 [MSAL](../active-directory/develop/msal-overview.md) 库与 OpenId Connect 授权代码 PKCE 流结合使用。 MSAL 库是 Microsoft 提供的库，可简化向桌面应用添加身份验证和授权支持的过程。 

登录流涉及以下步骤：

1. 用户打开应用并选择“登录”。
1. 应用打开桌面设备的系统浏览器，并发起到 Azure AD B2C 的身份验证请求。
1. 用户[注册或登录](add-sign-up-and-sign-in-policy.md)、[重置密码](add-password-reset-policy.md)或使用[社交帐户](add-identity-provider.md)登录。
1. 成功登录后，Azure AD B2C 向应用返回一个授权代码。
1. 应用执行下列操作：
    1. 用授权代码交换 ID 令牌、访问令牌和刷新令牌。
    1. 读取 ID 令牌声明。
    1. 将令牌存储在内存中缓存中供以后使用。

### <a name="app-registration-overview"></a>应用注册概述

若要让应用能够使用 Azure AD B2C 登录并调用 Web API，需要在 Azure AD B2C 目录中注册两个应用程序。  

- 通过桌面应用程序注册，你的应用可使用 Azure AD B2C 进行登录。 在应用注册过程中，请指定重定向 URI。 重定向 URI 是用户通过 Azure AD B2C 完成身份验证之后 Azure AD B2C 将用户重定向到的终结点。 应用注册过程会生成应用程序 ID（也称为“客户端 ID”），作为桌面应用的唯一标识 。 例如“应用 ID：1”。

- Web API 注册使你的应用可以调用受保护的 Web API。 注册将公开 Web API 权限（范围）。 应用注册过程会生成应用程序 ID，作为 Web API 的唯一标识。  例如，“应用 ID：2”。 向桌面应用（应用 ID：1）授予对 Web API 范围（应用 ID：2）的权限。 

以下关系图描述了应用注册和应用程序体系结构。

![图中介绍了具有 Web API、注册和令牌的桌面应用。](./media/configure-authentication-sample-wpf-desktop-app/desktop-app-with-api-architecture.png) 

### <a name="call-to-a-web-api"></a>调用 Web API

[!INCLUDE [active-directory-b2c-app-integration-call-api](../../includes/active-directory-b2c-app-integration-call-api.md)]

### <a name="sign-out"></a>注销

[!INCLUDE [active-directory-b2c-app-integration-sign-out-flow](../../includes/active-directory-b2c-app-integration-sign-out-flow.md)]

## <a name="prerequisites"></a>先决条件

一台运行带 .NET 桌面开发的 [Visual Studio 2019](https://www.visualstudio.com/downloads/) 的计算机。

## <a name="step-1-configure-your-user-flow"></a>步骤 1：配置用户流

[!INCLUDE [active-directory-b2c-app-integration-add-user-flow](../../includes/active-directory-b2c-app-integration-add-user-flow.md)]

## <a name="step-2-register-your-applications"></a>步骤 2：注册应用程序

在此步骤中，创建桌面应用和 Web API 应用程序注册，并指定 Web API 的范围。

### <a name="21-register-the-web-api-app"></a>2.1 注册 Web API 应用

[!INCLUDE [active-directory-b2c-app-integration-register-api](../../includes/active-directory-b2c-app-integration-register-api.md)]

### <a name="22-configure-web-api-app-scopes"></a>2.2 配置 Web API 应用的作用域

[!INCLUDE [active-directory-b2c-app-integration-api-scopes](../../includes/active-directory-b2c-app-integration-api-scopes.md)]


### <a name="23-register-the-desktop-app"></a>2.3 注册桌面应用

请按照以下步骤创建桌面应用注册：

1. 选择“应用注册”，然后选择“新建注册” 。
1. 输入应用程序的“名称”。 例如，desktop-app1。
1. 在“支持的帐户类型”下，选择“任何标识提供者或组织目录中的帐户(用于通过用户流对用户进行身份验证)” 。 
1. 在“重定向 URL”下，选择“公共客户端/本机(移动和桌面)”，然后输入：`https://your-tenant-name.b2clogin.com/oauth2/nativeclient` 。 将 `your-tenant-name` 替换为你的[租户名称](tenant-management.md#get-your-tenant-name)。 有关更多选项，请参阅[配置重定向 URI](enable-authentication-wpf-desktop-app-options.md#configure-redirect-uri)。
1. 选择“注册”  。
1. 完成应用注册后，选择“概述”。
1. 记下“应用程序(客户端) ID”，以便在稍后配置桌面应用程序时使用。
    ![屏幕截图显示了如何获取桌面应用程序 ID。](./media/configure-authentication-sample-wpf-desktop-app/get-azure-ad-b2c-app-id.png)  

### <a name="24-grant-the-desktop-app-permissions-for-the-web-api"></a>2.4 为 Web API 授予桌面应用权限

[!INCLUDE [active-directory-b2c-app-integration-grant-permissions](../../includes/active-directory-b2c-app-integration-grant-permissions.md)]

## <a name="step-3-configure-the-sample-web-api"></a>步骤 3：配置示例 Web API

此示例获取一个访问令牌，该令牌具有桌面应用可对 Web API 使用的相关范围。  若要通过代码调用 Web API，请执行以下步骤：

1. 使用现有 Web API，或新建一个。 有关详细信息，请参阅[使用 Azure AD B2C 在自己的 Web API 中启用身份验证](enable-authentication-web-api.md)。
1. 配置 Web API 后，复制 Web API 终结点的 URI。 你将在以下步骤中使用该 Web API 终结点。

> [!TIP]
> 如果没有 Web API，仍可运行此示例。 在这种情况下，应用将返回访问令牌，但无法调用 Web API。 

## <a name="step-4-get-the-wpf-desktop-app-sample"></a>步骤 4：获取 WPF 桌面应用示例

1. [下载 zip 文件](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git)，或从 [GitHub 存储库](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git)克隆示例 Web 应用程序。 

    ```bash
    git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
    ``` 

1. 在 Visual Studio 中打开“active-directory-b2c-wpf”解决方案 (`active-directory-b2c-wpf.sln`)。



## <a name="step-5-configure-the-sample-desktop-app"></a>步骤 5：配置示例桌面应用

在 active-directory-b2c-wpf 项目中，打开 App.xaml.cs 文件。 `App.xaml.cs` 类成员包含 Azure AD B2C 标识提供者的相关信息。 桌面应用使用这些信息来与 Azure AD B2C 建立信任关系、将用户登录和注销、获取令牌以及验证令牌。 

更新以下成员：

|键  |值  |
|---------|---------|
|`TenantName`|Azure AD B2C [租户名称](tenant-management.md#get-your-tenant-name)的第一部分。 例如 `contoso.b2clogin.com`。|
|`ClientId`|[步骤 2.3](#23-register-the-desktop-app) 中的桌面应用程序 ID。|
|`PolicySignUpSignIn`| 注册或登录用户流，或在[步骤 1](#step-1-configure-your-user-flow) 中创建的自定义策略。|
|`PolicyEditProfile`|编辑个人资料用户流，或在[步骤 1](#step-1-configure-your-user-flow) 中创建的自定义策略。|
|`ApiEndpoint`| （可选）在[步骤 3](#step-3-configure-the-sample-web-api) 中创建的 Web API 终结点。 例如 `https://contoso.azurewebsites.net/hello`。|
| `ApiScopes` | 在[步骤 2.4](#24-grant-the-desktop-app-permissions-for-the-web-api) 中创建的 Web API 范围。| 

最终的 App.xaml.cs 文件应类似于下面的 C# 代码：

```csharp
public partial class App : Application
{

private static readonly string TenantName = "contoso";
private static readonly string Tenant = $"{TenantName}.onmicrosoft.com";
private static readonly string AzureAdB2CHostname = $"{TenantName}.b2clogin.com";
private static readonly string ClientId = "<web-api-app-application-id>";
private static readonly string RedirectUri = $"https://{TenantName}.b2clogin.com/oauth2/nativeclient";

public static string PolicySignUpSignIn = "b2c_1_susi";
public static string PolicyEditProfile = "b2c_1_edit_profile";
public static string PolicyResetPassword = "b2c_1_reset";

public static string[] ApiScopes = { $"https://{Tenant}//api/tasks.read" };
public static string ApiEndpoint = "https://contoso.azurewebsites.net/hello";
```

## <a name="step-6-run-and-test-the-desktop-app"></a>步骤 6：运行并测试桌面应用

1. [还原 NuGet 包](/nuget/consume-packages/package-restore.md)。
1. 按 **F5** 生成并运行示例。
1. 选择“登录”。 然后，使用你的 Azure AD B2C 本地帐户或社交帐户注册或登录。

    ![屏幕截图演示了如何启动登录流。](./media/configure-authentication-sample-wpf-desktop-app/sign-in.png)

1. 成功注册或登录后，WPF 应用的下方窗格中会显示令牌详细信息。

    ![屏幕截图显示了 Azure AD B2C 访问令牌和用户 ID。](./media/configure-authentication-sample-wpf-desktop-app/post-signin.png) 

1. 选择“调用 API”，以调用 Web API。


## <a name="next-steps"></a>后续步骤

* [使用 Azure Active Directory B2C 在 WPF 桌面应用程序中配置身份验证选项](enable-authentication-wpf-desktop-app-options.md)
