---
title: 使用 Azure Active Directory B2C 在示例 Web 应用中配置身份验证
description: 本文讨论如何在 ASP.NET Web 应用程序中使用 Azure Active Directory B2C 执行用户登录和注册。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/15/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 60f3383c58ab297fd5c2199d532591b1d3a38b8f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128572485"
---
# <a name="configure-authentication-in-a-sample-web-app-by-using-azure-ad-b2c"></a>使用 Azure AD B2C 在示例 Web 应用中配置身份验证

本文使用一个示例 ASP.NET Web 应用，说明如何向 Web 应用添加 Azure Active Directory B2C (Azure AD B2C) 身份验证。

> [!IMPORTANT]
> 本文引用的示例 ASP.NET Web 应用不能用于调用 REST API，因为此应用会返回 ID 令牌，而非访问令牌。 有关可以调用 REST API 的 Web 应用，请参阅[使用 Azure AD B2C 保护通过 ASP.NET Core 生成的 Web API](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/4-WebApp-your-API/4-2-B2C)。  

## <a name="overview"></a>概述

OpenID Connect (OIDC) 是在 OAuth 2.0 上构建的身份验证协议。 可以使用 OIDC 安全地将用户登录到应用程序。 此 Web 应用示例使用 [Microsoft Identity Web](https://www.nuget.org/packages/Microsoft.Identity.Web)。 Microsoft Identity Web 是一组 ASP.NET Core 库，可简化向 Web 应用添加身份验证和授权支持的过程。 

登录流涉及以下步骤：

1. 用户前往 Web 应用，并选择“登录”。 
1. 应用发起身份验证请求，并将用户重定向到 Azure AD B2C。
1. 用户[注册或登录并](add-sign-up-and-sign-in-policy.md)[重置密码](add-password-reset-policy.md)。 或者，用户也可以使用[社交帐户](add-identity-provider.md)登录。
1. 用户成功登录后，Azure AD B2C 向应用返回 ID 令牌。
1. 应用验证 ID 令牌，读取声明，并向用户返回安全页。

当 ID 令牌过期或应用会话失效时，应用会发出新身份验证请求，并将用户重定向到 Azure AD B2C。 如果 Azure AD B2C [SSO 会话](session-behavior.md)处于活动状态，Azure AD B2C 会发出访问令牌，但不会提示用户再次登录。 如果 Azure AD B2C 会话过期或无效，则系统会提示用户重新登录。

### <a name="sign-out"></a>注销

[!INCLUDE [active-directory-b2c-app-integration-sign-out-flow](../../includes/active-directory-b2c-app-integration-sign-out-flow.md)] 

## <a name="prerequisites"></a>先决条件

运行以下系统之一的计算机： 

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 具有 ASP.NET 和 Web 开发工作负载的 [Visual Studio 2019 16.8 或更高版本](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)
* [.NET 5.0 SDK](https://dotnet.microsoft.com/download/dotnet)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [Visual Studio Code](https://code.visualstudio.com/download)
* [用于 Visual Studio Code 的 C#（最新版本）](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [.NET 5.0 SDK](https://dotnet.microsoft.com/download/dotnet)

---

## <a name="step-1-configure-your-user-flow"></a>步骤 1：配置用户流

[!INCLUDE [active-directory-b2c-app-integration-add-user-flow](../../includes/active-directory-b2c-app-integration-add-user-flow.md)]

## <a name="step-2-register-a-web-application"></a>步骤 2：注册 Web 应用

若要让应用程序使用 Azure AD B2C 登录，请在 Azure AD B2C 目录中注册应用。 注册应用会在应用与 Azure AD B2C 之间建立信任关系。  

在应用注册过程中，要指定重定向 URI。 重定向 URI 是用户通过 Azure AD B2C 完成身份验证之后 Azure AD B2C 将用户重定向到的终结点。 此应用注册过程会生成应用程序 ID（也称为“客户端 ID”），作为该应用的唯一标识 。 注册应用后，Azure AD B2C 将使用应用程序 ID 和重定向 URI 来创建身份验证请求。 

### <a name="step-21-register-the-app"></a>步骤 2.1：注册应用

若要创建 Web 应用注册，请执行下列操作：

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 请确保使用的是包含 Azure AD B2C 租户的目录。 在门户工具栏中选择“目录 + 订阅”图标。
1. 在“门户设置 | 目录+订阅”页上的“目录名称”列表中找到你的 Azure AD B2C 目录，然后选择“切换”。
1. 在 Azure 门户中，搜索并选择“Azure AD B2C”。
1. 选择“应用注册”，然后选择“新建注册” 。
1. 在“名称”下，输入应用程序的名称（例如 webapp1）。
1. 在“支持的帐户类型”下，选择“任何标识提供者或组织目录中的帐户(用于通过用户流对用户进行身份验证)” 。 
1. 在“重定向 URI”下，选择“Web”，然后在 URL 框中输入 `https://localhost:5001/signin-oidc` 。
1. 在“权限”下，选中“授予对 OpenID 和脱机访问权限的管理员许可”复选框。 
1. 选择“注册”  。
1. 选择“概述”。
1. 记录“应用程序(客户端) ID”，以便在稍后配置 Web 应用程序时使用。

    ![屏幕截图：用于记录 Web 应用程序 ID 的 Web 应用概述页。](./media/configure-authentication-sample-web-app/get-azure-ad-b2c-app-id.png)  


### <a name="step-22-enable-id-tokens"></a>步骤 2.2：启用 ID 令牌

对于直接从 Azure AD B2C 请求 ID 令牌的 Web 应用，请在应用注册中启用隐式授权流。

1. 在左侧窗格中的“管理”下，选择“身份验证” 。
1. 在“隐式授权”下，选中“ID 令牌(用于隐式和混合流)”和“访问令牌(用于隐式流)”复选框  。
1. 选择“保存”。

## <a name="step-3-get-the-web-app-sample"></a>步骤 3：获取 Web 应用示例

[下载 zip 文件](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/refs/heads/master.zip)，或从 GitHub 克隆示例 Web 应用。 

```bash
git clone https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2
```

将示例文件提取到文件夹，该文件夹路径的总字符长度须不得超过 260 个字符。

## <a name="step-4-configure-the-sample-web-app"></a>步骤 4：配置示例 Web 应用

在示例文件夹中的 1-WebApp-OIDC/1-5-B2C/ 文件夹下，使用 Visual Studio 或 Visual Studio Code 打开 WebApp-OpenIDConnect-DotNet.csproj 项目。 

在项目根文件夹下，打开 appsettings.json 文件。 此文件包含有关 Azure AD B2C 标识提供者的信息。 更新以下应用设置属性： 

|部分  |密钥  |值  |
|---------|---------|---------|
|AzureAdB2C|实例| Azure AD B2C [租户名称](tenant-management.md#get-your-tenant-name)的第一部分（例如 `https://contoso.b2clogin.com`）。|
|AzureAdB2C|域| Azure AD B2C 租户的完整[租户名称](tenant-management.md#get-your-tenant-name)（例如 `contoso.onmicrosoft.com`）。|
|AzureAdB2C|ClientId| [步骤 2](#step-2-register-a-web-application) 中的 Web API 应用程序 ID。|
|AzureAdB2C|SignUpSignInPolicyId|用户流，或在[步骤 1](#step-1-configure-your-user-flow) 中创建的自定义策略。|

你的最终配置文件应类似于下面的 JSON：

```JSON
"AzureAdB2C": {
  "Instance": "https://contoso.b2clogin.com",
  "Domain": "contoso.onmicrosoft.com",
  "ClientId": "<web-app-application-id>",
  "SignedOutCallbackPath": "/signout/<your-sign-up-in-policy>",
  "SignUpSignInPolicyId": "<your-sign-up-in-policy>"
}
```

## <a name="step-5-run-the-sample-web-app"></a>步骤 5：运行示例 Web 应用

1. 生成并运行该项目。
1. 转到  `https://localhost:5001` 。 
1. 选择“注册/登录”。

    ![项目欢迎页上“注册/登录”按钮的屏幕截图。](./media/configure-authentication-sample-web-app/web-app-sign-in.png)

1. 完成注册或登录过程。

身份验证成功后，你会在导航栏中看到显示名称。 若要查看 Azure AD B2C 令牌返回到应用的声明，请选择“声明”。

![Web 应用令牌声明的屏幕截图。](./media/configure-authentication-sample-web-app/web-app-token-claims.png)

## <a name="deploy-your-application"></a>部署应用程序 

在生产应用程序中，应用注册重定向 URI 通常是运行应用的可公开访问的终结点，比如 `https://contoso.com/signin-oidc`。 

可以随时在注册的应用程序中添加和修改重定向 URI。 重定向 URI 存在以下限制：

* 回复 URL 必须以方案 `https` 开头。
* 回复 URL 区分大小写。 其大小写必须与正在运行的应用程序的 URL 路径的大小写匹配。 

## <a name="next-steps"></a>后续步骤

* 了解有关[代码示例](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-5-B2C#about-the-code)的详细信息。
* 了解如何[使用 Azure AD B2C 在自己的 Web 应用中启用身份验证](enable-authentication-web-application.md)。
