---
title: Microsoft 标识平台身份验证和授权的代码示例
description: Microsoft 维护的代码示例的索引，这些示例演示多种应用程序类型、开发语言和框架的身份验证和授权。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: sample
ms.workload: identity
ms.date: 08/27/2021
ms.author: marsma
ms.reviewer: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 3c3d6a4ed060f26e523bc29f2afe843352b6ee2b
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129229860"
---
# <a name="microsoft-identity-platform-code-samples"></a>Microsoft 标识平台代码示例

这些代码示例由 Microsoft 构建和维护，它们演示如何通过使用 Azure AD 和 Microsoft 标识平台来进行多种[应用程序类型](v2-app-types.md)、开发语言和框架的身份验证和授权。

- 让用户登录到 Web 应用程序，并提供对受保护的 Web API 的授权访问。
- 通过要求提供用于执行 API 操作的访问令牌来保护 Web API。

每个代码示例都包含 README.md 文件，该文件描述如何生成项目（如果适用）和如何运行示例应用程序。 代码中的注释有助于了解使用身份验证库和标识平台来实现身份验证和授权的关键节。

## <a name="single-page-applications"></a>单页应用程序

这些示例展示了如何编写由 Microsoft 标识平台保护的单页应用程序。 这些示例使用下列种类的 MSAL.js 之一。

> [!div class="mx-tdCol2BreakAll"]
> | 语言/<br/>平台 | 代码示例 <br/>（在 GitHub 上） | Auth<br/> 库 | 身份验证流 |  
> | ------- | -------- | ------------- | -------------- |  
> | Angular | &#8226; [使用户登录](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/tree/main/1-Authentication/1-sign-in/README.md)<br/>&#8226; [使用户登录 (B2C)](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/tree/main/1-Authentication/2-sign-in-b2c/README.md) <br/> &#8226; [调用 Microsoft Graph](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/tree/main/2-Authorization-I/1-call-graph/README.md)<br/>&#8226; [调用 .NET Core Web API](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/tree/main/3-Authorization-II/1-call-api)<br/>&#8226; [调用 .NET Core Web API (B2C)](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/tree/main/3-Authorization-II/2-call-api-b2c)<br/>&#8226; [通过 OBO 调用 Microsoft Graph](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/tree/main/7-AdvancedScenarios/1-call-api-obo/README.md)<br/>&#8226; [使用 PoP 调用 .NET Core Web API](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/tree/main/7-AdvancedScenarios/2-call-api-pop/README.md)<br/>&#8226; [使用应用角色进行访问控制](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/tree/main/5-AccessControl/1-call-api-roles/README.md)<br/> &#8226; [使用安全组进行访问控制](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/tree/main/5-AccessControl/2-call-api-groups/README.md)<br/>&#8226; [部署到 Azure 存储和应用服务](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/tree/main/4-Deployment/README.md)| MSAL Angular | &#8226; 采用 PKCE 的授权代码<br/>&#8226; On-Behalf-Of (OBO) <br/>&#8226; 所有权证明 (PoP)|
> | Blazor WebAssembly | &#8226; [使用户登录](https://github.com/Azure-Samples/ms-identity-blazor-wasm/blob/main/WebApp-OIDC/MyOrg/README.md)<br/>&#8226; [使用户登录 (B2C)](https://github.com/Azure-Samples/ms-identity-blazor-wasm/blob/main/WebApp-OIDC/B2C/README.md)<br/>&#8226; [调用 Microsoft Graph](https://github.com/Azure-Samples/ms-identity-blazor-wasm/blob/main/WebApp-graph-user/Call-MSGraph/README.md)<br/>&#8226; [部署到 Azure 应用服务](https://github.com/Azure-Samples/ms-identity-blazor-wasm/blob/main/Deploy-to-Azure/README.md) | MSAL.js | 采用 PKCE 的授权代码 |
> | Javascript | &#8226; [使用户登录](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/tree/main/1-Authentication/1-sign-in/README.md)<br/>&#8226; [使用户登录 (B2C)](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/tree/main/1-Authentication/2-sign-in-b2c/README.md) <br/> &#8226; [调用 Microsoft Graph](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/tree/main/2-Authorization-I/1-call-graph/README.md)<br/>&#8226; [调用 Node.js Web API](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/tree/main/3-Authorization-II/1-call-api/README.md)<br/>&#8226; [调用 Node.js Web API (B2C)](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/tree/main/3-Authorization-II/2-call-api-b2c/README.md)<br/>&#8226; [通过 OBO 调用 Microsoft Graph](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/tree/main/4-AdvancedGrants/1-call-api-graph/README.md)<br/>&#8226; [通过 OBO 和 CA 调用 Node.js Web API](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/tree/main/4-AdvancedGrants/2-call-api-api-ca/README.md)<br/>&#8226; [部署到 Azure 存储和应用服务](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/tree/main/5-Deployment/README.md)| MSAL.js | &#8226; 采用 PKCE 的授权代码<br/>&#8226; On-Behalf-Of (OBO) <br/>&#8226; 条件访问 (CA) |
> | React | &#8226; [使用户登录](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/1-Authentication/1-sign-in/README.md)<br/>&#8226; [使用户登录 (B2C)](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/1-Authentication/2-sign-in-b2c/README.md) <br/> &#8226; [调用 Microsoft Graph](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/2-Authorization-I/1-call-graph/README.md)<br/>&#8226; [调用 Node.js Web API](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/3-Authorization-II/1-call-api)<br/>&#8226; [调用 Node.js Web API (B2C)](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/3-Authorization-II/2-call-api-b2c)<br/>&#8226; [通过 OBO 调用 Microsoft Graph](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/6-AdvancedScenarios/1-call-api-obo/README.md)<br/>&#8226; [使用 PoP 调用 Node.js Web API](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/6-AdvancedScenarios/2-call-api-pop/README.md)<br/>&#8226; [使用应用角色进行访问控制](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/5-AccessControl/1-call-api-roles/README.md)<br/>&#8226; [使用安全组进行访问控制](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/5-AccessControl/2-call-api-groups/README.md)<br/>&#8226; [部署到 Azure 存储和应用服务](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/4-Deployment/1-deploy-storage/README.md)<br/>&#8226; [部署到 Azure Static Web Apps](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/4-Deployment/2-deploy-static/README.md)| MSAL React  | &#8226; 采用 PKCE 的授权代码<br/>&#8226; On-Behalf-Of (OBO) <br/>&#8226; 条件访问 (CA)<br/>&#8226; 所有权证明 (PoP) |

## <a name="web-applications"></a>Web 应用程序

以下示例演示了将用户登录的 Web 应用。 一些示例还演示了使用用户标识调用 Microsoft Graph 或你自己的 Web API 的应用程序。

> [!div class="mx-tdCol2BreakAll"]
> | 语言/<br/>平台 | 代码示例<br/> （在 GitHub 上） | Auth<br/> 库 | 身份验证流 |
> | ------- | --------------------------- | ------------- | -------------- |
> | ASP.NET Core| ASP.NET Core 系列 <br/> &#8226; [使用户登录](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/1-WebApp-OIDC/README.md) <br/> &#8226; [使用户登录 (B2C)](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/1-WebApp-OIDC/1-5-B2C/README.md) <br/> &#8226; [调用 Microsoft Graph](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/2-WebApp-graph-user/2-1-Call-MSGraph/README.md) <br/> &#8226; [自定义令牌缓存](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/2-WebApp-graph-user/2-2-TokenCache/README.md) <br/> &#8226; [调用 Graph（多租户）](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/2-WebApp-graph-user/2-3-Multi-Tenant/README.md) <br/> &#8226; [调用 Azure REST API](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/3-WebApp-multi-APIs/README.md) <br/> &#8226; [保护 Web API](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/4-WebApp-your-API/4-1-MyOrg/README.md) <br/> &#8226; [保护 Web API (B2C)](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/4-WebApp-your-API/4-2-B2C/README.md) <br/> &#8226; [保护多租户 Web API](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/4-WebApp-your-API/4-3-AnyOrg/Readme.md) <br/> &#8226; [使用应用角色进行访问控制](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/5-WebApp-AuthZ/5-1-Roles/README.md) <br/> &#8226; [使用安全组进行访问控制](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/5-WebApp-AuthZ/5-2-Groups/README.md) <br/> &#8226; [部署到 Azure 存储和应用服务](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/6-Deploy-to-Azure/README.md) | &#8226; MSAL.NET<br/> &#8226; Microsoft.Identity.Web | &#8226; OpenID Connect <br/> &#8226; 授权代码 <br/> &#8226; On-Behalf-Of|
> | Blazor | Blazor Server 系列 <br/> &#8226; [使用户登录](https://github.com/Azure-Samples/ms-identity-blazor-server/tree/main/WebApp-OIDC/MyOrg) <br/> &#8226; [使用户登录 (B2C)](https://github.com/Azure-Samples/ms-identity-blazor-server/tree/main/WebApp-OIDC/B2C) <br/> &#8226; [调用 Microsoft Graph](https://github.com/Azure-Samples/ms-identity-blazor-server/tree/main/WebApp-graph-user/Call-MSGraph) <br/> &#8226; [调用 Web API](https://github.com/Azure-Samples/ms-identity-blazor-server/tree/main/WebApp-your-API/MyOrg) <br/> &#8226; [调用 Web API (B2C)](https://github.com/Azure-Samples/ms-identity-blazor-server/tree/main/WebApp-your-API/B2C) | MSAL.NET | 采用 PKCE 的授权代码|
> | ASP.NET Core|[高级令牌缓存场景](https://github.com/Azure-Samples/ms-identity-dotnet-advanced-token-cache) | &#8226; MSAL.NET <br/> &#8226; Microsoft.Identity.Web | On-Behalf-Of (OBO) |
> | ASP.NET Core|[使用条件访问身份验证上下文执行升级\-身份验证](https://github.com/Azure-Samples/ms-identity-dotnetcore-ca-auth-context-app/blob/main/README.md) | &#8226; MSAL.NET <br/> &#8226; Microsoft.Identity.Web | 授权代码 |
> | ASP.NET Core|[Active Directory FS 到 Azure AD 的迁移](https://github.com/Azure-Samples/ms-identity-dotnet-adfs-to-aad) | MSAL.NET | &#8226; SAML <br/> &#8226; OpenID Connect |
> | ASP.NET | &#8226; [Microsoft Graph 训练示例](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp) <br/> &#8226; [使用户登录并调用 Microsoft Graph](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect) <br/> &#8226; [使用户登录并以管理员限制的范围调用 Microsoft Graph](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) <br/> &#8226; [快速入门：使用户登录](https://github.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) | MSAL.NET | &#8226; OpenID Connect <br/> &#8226; 授权代码 |
> | Java </p> Spring |Azure AD Spring Boot 入门版系列 <br/> &#8226; [使用户登录](https://github.com/Azure-Samples/ms-identity-java-spring-tutorial/tree/main/1-Authentication/sign-in) <br/> &#8226; [使用户登录 (B2C)](https://github.com/Azure-Samples/ms-identity-java-spring-tutorial/tree/main/1-Authentication/sign-in-b2c) <br/> &#8226; [调用 Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-spring-tutorial/tree/main/2-Authorization-I/call-graph) <br/> &#8226; [使用应用角色进行访问控制](https://github.com/Azure-Samples/ms-identity-java-spring-tutorial/tree/main/3-Authorization-II/roles) <br/> &#8226; [使用组进行访问控制](https://github.com/Azure-Samples/ms-identity-java-spring-tutorial/tree/main/3-Authorization-II/groups) <br/> &#8226; [部署到 Azure 应用服务](https://github.com/Azure-Samples/ms-identity-java-spring-tutorial/tree/main/4-Deployment/deploy-to-azure-app-service) | &#8226; MSAL Java <br/> &#8226; Azure AD Boot Starter | 授权代码 |
> | Java </p> Servlet | 无 Spring Servlet 系列 <br/> &#8226; [使用户登录](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/tree/main/1-Authentication/sign-in) <br/> &#8226; [使用户登录 (B2C)](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/tree/main/1-Authentication/sign-in-b2c) <br/> &#8226; [调用 Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/tree/main/2-Authorization-I/call-graph) <br/> &#8226; [使用应用角色进行访问控制](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/tree/main/3-Authorization-II/roles) <br/> &#8226; [使用安全组进行访问控制](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/tree/main/3-Authorization-II/groups) <br/> &#8226; [部署到 Azure 应用服务](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/tree/main/4-Deployment/deploy-to-azure-app-service) | MSAL Java | 授权代码 |
> | Java | [使用户登录并调用 Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp)| MSAL Java | 授权代码 |
> | Java </p> Spring| 让用户登录并通过 OBO 调用 Microsoft Graph </p> &#8226; [Web API](https://github.com/Azure-Samples/ms-identity-java-webapi) | MSAL Java | &#8226; 授权代码 <br/> &#8226; On-Behalf-Of (OBO) |
> | Node.js </p> Express | Express Web 应用系列 <br/> &#8226; [使用户登录](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-tutorial/blob/main/1-Authentication/1-sign-in/README.md)<br/> &#8226; [使用户登录 (B2C)](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-tutorial/blob/main/1-Authentication/2-sign-in-b2c/README.md)<br/> &#8226; [调用 Microsoft Graph](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-tutorial/blob/main/2-Authorization/1-call-graph/README.md)<br/> &#8226; [部署到 Azure 应用服务](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-tutorial/blob/main/3-Deployment/README.md)<br/> &#8226; [使用应用角色进行访问控制](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-tutorial/blob/main/4-AccessControl/1-app-roles/README.md)<br/> &#8226; [使用安全组进行访问控制](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-tutorial/blob/main/4-AccessControl/2-security-groups/README.md) <br/> &#8226; [使用户登录的 Web 应用](https://github.com/Azure-Samples/ms-identity-node) | MSAL Node | 授权代码 |
> | Python </p> Flask | Flask 系列 <br/> &#8226; [使用户登录](https://github.com/Azure-Samples/ms-identity-python-flask-tutorial) <br/> &#8226; [使用户登录 (B2C)](https://github.com/Azure-Samples/ms-identity-python-flask-tutorial) <br/>&#8226; [使用户登录并调用 Microsoft Graph](https://github.com/Azure-Samples/ms-identity-python-webapp) <br/> &#8226; [调用 Microsoft Graph](https://github.com/Azure-Samples/ms-identity-python-flask-tutorial) <br/> &#8226; [部署到 Azure 应用服务](https://github.com/Azure-Samples/ms-identity-python-flask-tutorial) | MSAL Python | 授权代码 |
> | Python </p> Django | Django 系列 <br/> &#8226; [使用户登录](https://github.com/Azure-Samples/ms-identity-python-django-tutorial/tree/main/1-Authentication/sign-in) <br/> &#8226; [使用户登录 (B2C)](https://github.com/Azure-Samples/ms-identity-python-django-tutorial/tree/main/1-Authentication/sign-in-b2c) <br/> &#8226; [调用 Microsoft Graph](https://github.com/Azure-Samples/ms-identity-python-django-tutorial/tree/main/2-Authorization-I/call-graph) <br/> &#8226; [部署到 Azure 应用服务](https://github.com/Azure-Samples/ms-identity-python-django-tutorial/tree/main/3-Deployment/deploy-to-azure-app-service)| MSAL Python | 授权代码 |
> | Ruby | Graph 训练 <br/> &#8226; [使用户登录并调用 Microsoft Graph](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp) | OmniAuth OAuth2 | 授权代码 |

## <a name="web-api"></a>Web API

以下示例展示了如何使用 Microsoft 标识平台保护 Web API，以及如何从 Web API 调用下游 API。

> [!div class="mx-tdCol2BreakAll"]
> | 语言/<br/>平台 | 代码示例 <br/> （在 GitHub 上） |Auth<br/> 库 |身份验证流 |
> | ----------- | ----------- |----------- |----------- |
> | ASP.NET | [调用 Microsoft Graph](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof) | MSAL.NET | On-Behalf-Of (OBO) |
> | ASP.NET Core | [使用户登录并调用 Microsoft Graph](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) | MSAL.NET | On-Behalf-Of (OBO) |
> | Java | [用户登录](https://github.com/Azure-Samples/ms-identity-java-webapi) | MSAL Java | On-Behalf-Of (OBO) |
> | Node.js | &#8226; [保护 Node.js web API](https://github.com/Azure-Samples/active-directory-javascript-nodejs-webapi-v2) <br/> &#8226; [使用 Azure AD B2C 保护 Node.js Web API](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) | MSAL Node | 授权持有者 |

## <a name="desktop"></a>桌面

以下示例展示了以用户身份访问 Microsoft Graph API 或你自己的 Web API 的公共客户端桌面应用程序。 除了使用工作区应用程序管理器 (WAM) 的桌面（控制台）示例，所有这些客户端应用程序均使用 Microsoft 身份验证库 (MSAL)。

> [!div class="mx-tdCol2BreakAll"]
> | 语言/<br/>平台 | 代码示例 <br/> （在 GitHub 上） | Auth<br/> 库 | 身份验证流 |  
> | ------- | -------- | ------------- | -------------- |
> | .NET Core | &#8226; [调用 Microsoft Graph](https://github.com/Azure-Samples/ms-identity-dotnet-desktop-tutorial/tree/master/1-Calling-MSGraph/1-1-AzureAD) <br/> &#8226; [使用令牌缓存调用 Microsoft Graph](https://github.com/Azure-Samples/ms-identity-dotnet-desktop-tutorial/tree/master/2-TokenCache) <br/> &#8226; [使用自定义 Web UI HTML 调用 Microsoft Graph](https://github.com/Azure-Samples/ms-identity-dotnet-desktop-tutorial/tree/master/3-CustomWebUI/3-1-CustomHTML) <br/> &#8226; [使用自定义 Web 浏览器调用 Microsoft Graph](https://github.com/Azure-Samples/ms-identity-dotnet-desktop-tutorial/tree/master/3-CustomWebUI/3-2-CustomBrowser) <br/> &#8226; [通过设备代码流使用户登录](https://github.com/Azure-Samples/ms-identity-dotnet-desktop-tutorial/tree/master/4-DeviceCodeFlow) | MSAL.NET |&#8226; 采用 PKCE 的授权代码 <br/> &#8226; 设备代码 |
> | .NET | &#8226; [使用守护程序控制台调用 Microsoft Graph](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/tree/master/1-Call-MSGraph) <br/> &#8226; [使用守护程序控制台调用 Web API](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/master/2-Call-OwnApi/README.md) | MSAL.NET | 采用 PKCE 的授权代码 |
> | .NET | [通过集成 Windows 身份验证调用受保护的 API](https://github.com/azure-samples/active-directory-dotnet-iwa-v2) | MSAL.NET | Windows 集成身份验证 |
> | Java | [调用 Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-desktop/) | MSAL Java | Windows 集成身份验证 |
> | Node.js | [用户登录](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-desktop) | MSAL Node | 采用 PKCE 的授权代码 |
> | Powershell | [通过使用用户名/密码登录用户来调用 Microsoft Graph](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2) | MSAL.NET | 资源所有者密码凭据 |
> | Python | [用户登录](https://github.com/Azure-Samples/ms-identity-python-desktop) | MSAL Python | 采用 PKCE 的授权代码 |
> | 通用 Window 平台 (UWP) | [调用 Microsoft Graph](https://github.com/Azure-Samples/active-directory-xamarin-native-v2/tree/main/2-With-broker) | MSAL.NET | Web 帐户管理器 |
> | Windows Presentation Foundation (WPF) | [使用户登录并调用 Microsoft Graph](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) | MSAL.NET | 采用 PKCE 的授权代码 |
> | XAML | &#8226; [使用户登录并调用 ASP.NET Core Web API](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/1.%20Desktop%20app%20calls%20Web%20API) <br/> &#8226; [使用户登录并调用 Microsoft Graph](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | MSAL.NET | 采用 PKCE 的授权代码 |

## <a name="mobile"></a>移动型

以下示例展示了以用户身份访问 Microsoft Graph API 或你自己的 Web API 的公共客户端移动应用程序。 这些客户端应用程序均使用 Microsoft 身份验证库 (MSAL)。

> [!div class="mx-tdCol2BreakAll"]
> | 语言/<br/>平台 | 代码示例 <br/> （在 GitHub 上） |Auth<br/> 库 |身份验证流 |
> | ----------- | ----------- |----------- |----------- |
> | iOS | &#8226; [调用 Microsoft Graph 本机](https://github.com/Azure-Samples/ms-identity-mobile-apple-swift-objc) <br/> &#8226; [使用 Azure AD nxoauth 调用 Microsoft Graph](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) | MSAL iOS | 采用 PKCE 的授权代码 |
> | Java | [使用户登录并调用 Microsoft Graph](https://github.com/Azure-Samples/ms-identity-android-java) | MSAL Android | 采用 PKCE 的授权代码 |
> | Kotlin | [使用户登录并调用 Microsoft Graph](https://github.com/Azure-Samples/ms-identity-android-kotlin) | MSAL Android | 采用 PKCE 的授权代码 |
> | Xamarin | &#8226; [使用户登录并调用 Microsoft Graph](https://github.com/Azure-Samples/active-directory-xamarin-native-v2/tree/main/1-Basic) <br/>&#8226; [通过代理使用户登录并调用 Microsoft Graph](https://github.com/Azure-Samples/active-directory-xamarin-native-v2/tree/main/2-With-broker) | MSAL.NET | 采用 PKCE 的授权代码 |

## <a name="service--daemon"></a>服务/守护程序

下面的示例展示了一个应用程序，它使用自己的标识（没有用户）访问 Microsoft Graph API。

> [!div class="mx-tdCol2BreakAll"]
> | 语言/<br/>平台 | 代码示例 <br/> （在 GitHub 上） |Auth<br/> 库 |身份验证流 |
> | ----------- | ----------- |----------- |----------- |
> | ASP.NET| &#8226; [调用 Microsoft Graph](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/tree/master/1-Call-MSGraph) <br/> &#8226; [调用 Web API](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/tree/master/2-Call-OwnApi)<br/> &#8226; [调用自己的 Web API](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/tree/master/4-Call-OwnApi-Pop) <br/> &#8226; [使用托管标识和 Azure key vault](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/tree/master/3-Using-KeyVault) <br/> &#8226; [具有 Microsoft 标识平台终结点的多租户](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp) | MSAL.NET | 客户端凭据授予|
> | Java | [调用 Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-daemon)| MSAL Java| 客户端凭据授予|
> | Node.js | [使用户登录并调用 Web API](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-console) | MSAL Node | 客户端凭据授予 |
> | Python | &#8226; [使用机密调用 Microsoft Graph](https://github.com/Azure-Samples/ms-identity-python-daemon/tree/master/1-Call-MsGraph-WithSecret) <br/> &#8226; [使用证书调用 Microsoft Graph](https://github.com/Azure-Samples/ms-identity-python-daemon/tree/master/2-Call-MsGraph-WithCertificate) | MSAL Python| 客户端凭据授予|

## <a name="azure-functions-as-web-apis"></a>Azure Functions 作为 Web API

以下示例演示如何使用 HttpTrigger 保护 Azure Function 并通过 Microsoft 标识平台公开 Web API，以及如何从 Web API 调用下游 API。

> [!div class="mx-tdCol2BreakAll"]
> | 语言/<br/>平台 | 代码示例 <br/> （在 GitHub 上） |Auth<br/> 库 |身份验证流 |
> | ----------- | ----------- |----------- |----------- |
> | .NET | [受 Azure AD 保护的 .NET Azure Function Web API](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions) | MSAL.NET | 授权代码 |
> | Node.js | [受 Azure AD 保护的 Node.js Azure Function Web API](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions) | MSAL Node | 授权持有者 |
> | Node.js | [代表用户调用 Microsoft Graph API](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions) | MSAL Node| On-Behalf-Of (OBO)|
> | Python | [受 Azure AD 保护的 Python Azure Function Web API](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions) | MSAL Python | 授权代码 |

## <a name="headless"></a>无外设

以下示例展示了在没有 Web 浏览器的设备上运行的公共客户端应用程序。 该应用可以是命令行工具，可以是在 Linux 或 Mac 上运行的应用，还可以是 IoT 应用程序。 此示例提供了一个应用，以用户身份访问 Microsoft Graph API，该用户以交互方式在另一台设备上登录（例如移动电话）。 此客户端应用程序使用 Microsoft 身份验证库 (MSAL)。

> [!div class="mx-tdCol2BreakAll"]
> | 语言/<br/>平台 | 代码示例 <br/> （在 GitHub 上） |Auth<br/> 库 |身份验证流 |
> | ----------- | ----------- |----------- |----------- |
> | .NET Core | [从纯文本设备调用受保护的 API](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2) | MSAL.NET | 设备代码|
> | Java | [使登录用户并调用受保护的 API](https://github.com/Azure-Samples/ms-identity-java-devicecodeflow) | MSAL Java | 设备代码 |
> | Python | [调用 Microsoft Graph](https://github.com/Azure-Samples/ms-identity-python-devicecodeflow) | MSAL Python | 设备代码 |

## <a name="multi-tenant-saas"></a>多租户 SaaS

以下示例演示了如何配置应用程序，使其接受来自 Azure Active Directory (Azure AD) 租户的登录。 将应用程序配置为多租户是指，你可向多个组织提供软件即服务 (SaaS) 应用程序，使它们的用户能够在同意后登录到你的应用程序。

> [!div class="mx-tdCol2BreakAll"]
> | 语言/<br/>平台 | 代码示例 <br/> （在 GitHub 上） |Auth<br/> 库 |身份验证流 |
> | ----------- | ----------- |----------- |----------- |
> | ASP.NET Core | [ASP.NET Core MVC Web 应用程序调用 Microsoft Graph API](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-3-Multi-Tenant) | MSAL.NET | OpenID 连接 |
> | ASP.NET Core | [ASP.NET Core MVC Web 应用程序调用 ASP.NET Core Web API](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/4-WebApp-your-API/4-3-AnyOrg) | MSAL.NET | 授权代码 |

## <a name="next-steps"></a>后续步骤

若要深入了解更多示例代码，请参阅：

- [从 Angular 使用户登录并调用 Microsoft Graph API](tutorial-v2-angular-auth-code.md)
- [在 Node.js 和 Express Web 应用中使用户登录](tutorial-v2-nodejs-webapp-msal.md)
- [从通用 Windows 平台调用 Microsoft Graph API](tutorial-v2-windows-uwp.md)
