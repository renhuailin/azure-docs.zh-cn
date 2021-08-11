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
ms.date: 07/06/2021
ms.author: marsma
ms.reviewer: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: df892abcc28560e84d6708b794180c0b9182991d
ms.sourcegitcommit: 92dd25772f209d7d3f34582ccb8985e1a099fe62
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2021
ms.locfileid: "114228181"
---
# <a name="microsoft-identity-platform-code-samples"></a>Microsoft 标识平台代码示例

这些代码示例由 Microsoft 构建和维护，它们演示如何通过使用 Azure AD 和 Microsoft 标识平台来进行多种[应用程序类型](v2-app-types.md)、开发语言和框架的身份验证和授权。

- 让用户登录到 Web 应用程序，并提供对受保护的 Web API 的授权访问。
- 通过要求提供用于执行 API 操作的访问令牌来保护 Web API。

每个代码示例都包含 README.md 文件，该文件描述如何生成项目（如果适用）和如何运行示例应用程序。 代码中的注释有助于了解使用身份验证库和标识平台来实现身份验证和授权的关键节。

## <a name="single-page-applications"></a>单页应用程序

这些示例展示了如何编写由 Microsoft 标识平台保护的单页应用程序。 这些示例使用下列种类的 MSAL.js 之一。

> [!div class="mx-tdCol2BreakAll"]
> | 语言/<br/>平台 | 代码示例 | 说明 | 身份验证库 | 身份验证流 |  
> | ------- | -------- | --------------------------- | ------------- | -------------- |  
> | Angular | [GitHub 存储库](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial) | &#8226; [使用户登录](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/tree/main/1-Authentication/1-sign-in/README.md)<br/>&#8226; [使用户登录 (B2C)](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/tree/main/1-Authentication/2-sign-in-b2c/README.md) <br/> &#8226; [调用 Microsoft Graph](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/tree/main/2-Authorization-I/1-call-graph/README.md)<br/>&#8226; [调用 .NET Core Web API](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/tree/main/3-Authorization-II/1-call-api)<br/>&#8226; [调用 .NET Core Web API (B2C)](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/tree/main/3-Authorization-II/2-call-api-b2c)<br/>&#8226; [通过 OBO 调用 Microsoft Graph](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/tree/main/7-AdvancedScenarios/1-call-api-obo/README.md)<br/>&#8226; [使用 PoP 调用 .NET Core Web API](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/tree/main/7-AdvancedScenarios/2-call-api-pop/README.md)<br/>&#8226; [使用应用角色进行访问控制](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/tree/main/5-AccessControl/1-call-api-roles/README.md)<br/>&#8226; [使用安全组进行访问控制](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/tree/main/5-AccessControl/2-call-api-groups/README.md)<br/>&#8226; [部署到 Azure 存储和应用服务](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/tree/main/4-Deployment/README.md)| MSAL Angular | &#8226; 授权代码流（使用 PKCE）<br/>&#8226; 代理 (OBO) 流<br/>&#8226; 所有权证明 (PoP)|
> | Blazor WebAssembly | [GitHub 存储库](https://github.com/Azure-Samples/ms-identity-blazor-wasm) | &#8226; [使用户登录](https://github.com/Azure-Samples/ms-identity-blazor-wasm/blob/main/WebApp-OIDC/MyOrg/README.md)<br/>&#8226; [使用户登录 (B2C)](https://github.com/Azure-Samples/ms-identity-blazor-wasm/blob/main/WebApp-OIDC/B2C/README.md)<br/>&#8226; [调用 Microsoft Graph](https://github.com/Azure-Samples/ms-identity-blazor-wasm/blob/main/WebApp-graph-user/Call-MSGraph/README.md)<br/>&#8226; [部署到 Azure 应用服务](https://github.com/Azure-Samples/ms-identity-blazor-wasm/blob/main/Deploy-to-Azure/README.md) | MSAL.js | 授权代码流（使用 PKCE） |
> | JavaScript | [GitHub 存储库](https://github.com/Azure-Samples/ms-identity-javascript-tutorial) | &#8226; [使用户登录](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/tree/main/1-Authentication/1-sign-in/README.md)<br/>&#8226; [使用户登录 (B2C)](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/tree/main/1-Authentication/2-sign-in-b2c/README.md) <br/> &#8226; [调用 Microsoft Graph](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/tree/main/2-Authorization-I/1-call-graph/README.md)<br/>&#8226; [调用 Node.js Web API](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/tree/main/3-Authorization-II/1-call-api/README.md)<br/>&#8226; [调用 Node.js Web API (B2C)](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/tree/main/3-Authorization-II/2-call-api-b2c/README.md)<br/>&#8226; [通过 OBO 调用 Microsoft Graph](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/tree/main/4-AdvancedGrants/1-call-api-graph/README.md)<br/>&#8226; [通过 OBO 和 CA 调用 Node.js Web API](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/tree/main/4-AdvancedGrants/2-call-api-api-ca/README.md)<br/>&#8226; [部署到 Azure 存储和应用服务](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/tree/main/5-Deployment/README.md)| MSAL.js | &#8226; 授权代码流（使用 PKCE）<br/>&#8226; 代理 (OBO) 流<br/>&#8226; 条件访问 (CA) |
> | React | [GitHub 存储库](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial) | &#8226; [使用户登录](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/1-Authentication/1-sign-in/README.md)<br/>&#8226; [使用户登录 (B2C)](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/1-Authentication/2-sign-in-b2c/README.md) <br/> &#8226; [调用 Microsoft Graph](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/2-Authorization-I/1-call-graph/README.md)<br/>&#8226; [调用 Node.js Web API](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/3-Authorization-II/1-call-api)<br/>&#8226; [调用 Node.js Web API (B2C)](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/3-Authorization-II/2-call-api-b2c)<br/>&#8226; [通过 OBO 调用 Microsoft Graph](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/6-AdvancedScenarios/1-call-api-obo/README.md)<br/>&#8226; [使用 PoP 调用 Node.js Web API](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/6-AdvancedScenarios/2-call-api-pop/README.md)<br/>&#8226; [使用应用角色进行访问控制](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/5-AccessControl/1-call-api-roles/README.md)<br/>&#8226; [使用安全组进行访问控制](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/5-AccessControl/2-call-api-groups/README.md)<br/>&#8226; [部署到 Azure 存储和应用服务](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/4-Deployment/1-deploy-storage/README.md)<br/>&#8226; [部署到 Azure Static Web Apps](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/4-Deployment/2-deploy-static/README.md)| MSAL React  | &#8226; 授权代码流（使用 PKCE）<br/>&#8226; 代理 (OBO) 流<br/>&#8226; 条件访问 (CA)<br/>&#8226; 所有权证明 (PoP) |

## <a name="web-applications"></a>Web 应用程序

以下示例演示了将用户登录的 Web 应用。 一些示例还演示了使用用户标识调用 Microsoft Graph 或你自己的 Web API 的应用程序。

> [!div class="mx-tdCol2BreakAll"]
> | 语言/<br/>平台 | 代码示例<br/>（在 GitHub 上） | 说明 | 使用的身份验证库 | 身份验证流 |
> | ------- | -------- | --------------------------- | ------------- | -------------- |
> | ASP.NET Core|[GitHub 存储库](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2) | ASP.NET Core 系列 <br/> &#8226; [使用户登录](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/1-WebApp-OIDC/README.md) <br/> &#8226; [使用户登录 (B2C)](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/1-WebApp-OIDC/1-5-B2C/README.md) <br/> &#8226; [调用 Microsoft Graph](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/2-WebApp-graph-user/2-1-Call-MSGraph/README.md) <br/> &#8226; [自定义令牌缓存](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/2-WebApp-graph-user/2-2-TokenCache/README.md) <br/> &#8226; [调用 Graph（多租户）](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/2-WebApp-graph-user/2-3-Multi-Tenant/README.md) <br/> &#8226; [调用 Azure REST API](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/3-WebApp-multi-APIs/README.md) <br/> &#8226; [保护 Web API](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/4-WebApp-your-API/4-1-MyOrg/README.md) <br/> &#8226; [保护 Web API (B2C)](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/4-WebApp-your-API/4-2-B2C/README.md) <br/> &#8226; [保护多租户 Web API](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/4-WebApp-your-API/4-3-AnyOrg/Readme.md) <br/> &#8226; [使用应用角色进行访问控制](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/5-WebApp-AuthZ/5-1-Roles/README.md) <br/> &#8226; [使用安全组进行访问控制](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/5-WebApp-AuthZ/5-2-Groups/README.md) <br/> &#8226; [部署到 Azure 存储和应用服务](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/6-Deploy-to-Azure/README.md) | &#8226; [MSAL.NET](https://aka.ms/msal-net) <br/> &#8226; [Microsoft.Identity.Web](https://aka.ms/microsoft-identity-web) | &#8226; [OIDC 流](./v2-protocols-oidc.md) <br/> &#8226; [授权代码流](./v2-oauth2-auth-code-flow.md) <br/> &#8226; [代理 (OBO) 流](./v2-oauth2-on-behalf-of-flow.md) |
> | Blazor | [GitHub 存储库](https://github.com/Azure-Samples/ms-identity-blazor-server/) | Blazor Server 系列 <br/> &#8226; [使用户登录](https://github.com/Azure-Samples/ms-identity-blazor-server/tree/main/WebApp-OIDC/MyOrg) <br/> &#8226; [使用户登录 (B2C)](https://github.com/Azure-Samples/ms-identity-blazor-server/tree/main/WebApp-OIDC/B2C) <br/> &#8226; [调用 Microsoft Graph](https://github.com/Azure-Samples/ms-identity-blazor-server/tree/main/WebApp-graph-user/Call-MSGraph) <br/> &#8226; [调用 Web API](https://github.com/Azure-Samples/ms-identity-blazor-server/tree/main/WebApp-your-API/MyOrg) <br/> &#8226; [调用 Web API (B2C)](https://github.com/Azure-Samples/ms-identity-blazor-server/tree/main/WebApp-your-API/B2C) | MSAL.NET | |
> | ASP.NET Core|[GitHub 存储库](https://github.com/Azure-Samples/ms-identity-dotnet-advanced-token-cache) | [高级令牌缓存场景](https://github.com/Azure-Samples/ms-identity-dotnet-advanced-token-cache) | &#8226; [MSAL.NET](https://aka.ms/msal-net) <br/> &#8226; [Microsoft.Identity.Web](https://aka.ms/microsoft-identity-web) | [代理 (OBO) 流](./v2-oauth2-on-behalf-of-flow.md) |
> | ASP.NET Core|[GitHub 存储库](https://github.com/Azure-Samples/ms-identity-dotnetcore-ca-auth-context-app/blob/main/README.md) | [使用条件访问身份验证上下文来执行升级身份验证](https://github.com/Azure-Samples/ms-identity-dotnetcore-ca-auth-context-app/blob/main/README.md) | &#8226; [MSAL.NET](https://aka.ms/msal-net) <br/> &#8226; [Microsoft.Identity.Web](https://aka.ms/microsoft-identity-web) | [授权代码流](./v2-oauth2-auth-code-flow.md) |
> | ASP.NET Core|[GitHub 存储库](https://github.com/Azure-Samples/ms-identity-dotnet-adfs-to-aad) | [Active Directory FS 到 Azure AD 的迁移](https://github.com/Azure-Samples/ms-identity-dotnet-adfs-to-aad) | [MSAL.NET](https://aka.ms/msal-net) |  |
> | ASP.NET |[GitHub 存储库](https://github.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) | [快速入门：使用户登录](https://github.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) | [MSAL.NET](https://aka.ms/msal-net) |  |
> | ASP.NET |[GitHub 存储库](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect) | [使用户登录并调用 Microsoft Graph](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect) | [MSAL.NET](https://aka.ms/msal-net) |  |
> | ASP.NET |[GitHub 存储库](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) | [管理员受限范围 <br/> &#8226; 使用户登录 <br/> &#8226; 调用 Microsoft Graph](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) | [MSAL.NET](https://aka.ms/msal-net) |  |
> | ASP.NET |[GitHub 存储库](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp) | Microsoft Graph 训练示例 | [MSAL.NET](https://aka.ms/msal-net) |  |
> | Java </p> Spring |[GitHub 存储库](https://github.com/Azure-Samples/ms-identity-java-spring-tutorial) | Azure AD Spring Boot 入门版系列 <br/> &#8226; [使用户登录](https://github.com/Azure-Samples/ms-identity-java-spring-tutorial/tree/main/1-Authentication/sign-in) <br/> &#8226; [使用户登录 (B2C)](https://github.com/Azure-Samples/ms-identity-java-spring-tutorial/tree/main/1-Authentication/sign-in-b2c) <br/> &#8226; [调用 Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-spring-tutorial/tree/main/2-Authorization-I/call-graph) <br/> &#8226; [使用应用角色进行访问控制](https://github.com/Azure-Samples/ms-identity-java-spring-tutorial/tree/main/3-Authorization-II/roles) <br/> &#8226; [使用组进行访问控制](https://github.com/Azure-Samples/ms-identity-java-spring-tutorial/tree/main/3-Authorization-II/groups) <br/> &#8226; [部署到 Azure 应用服务](https://github.com/Azure-Samples/ms-identity-java-spring-tutorial/tree/main/4-Deployment/deploy-to-azure-app-service) | MSAL Java <br/> AAD Boot 入门版 | [授权代码流](./v2-oauth2-auth-code-flow.md) |
> | Java </p> Servlet |[GitHub 存储库](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication) | 无 Spring Servlet 系列 <br/> &#8226; [使用户登录](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/tree/main/1-Authentication/sign-in) <br/> &#8226; [使用户登录 (B2C)](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/tree/main/1-Authentication/sign-in-b2c) <br/> &#8226; [调用 Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/tree/main/2-Authorization-I/call-graph) <br/> &#8226; [使用应用角色进行访问控制](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/tree/main/3-Authorization-II/roles) <br/> &#8226; [使用安全组进行访问控制](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/tree/main/3-Authorization-II/groups) <br/> &#8226; [部署到 Azure 应用服务](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/tree/main/4-Deployment/deploy-to-azure-app-service) | MSAL Java | [授权代码流](./v2-oauth2-auth-code-flow.md) |
> | Java |[GitHub 存储库](https://github.com/Azure-Samples/ms-identity-java-webapp) | 使用户登录，调用 Microsoft Graph | MSAL Java | [授权代码流](./v2-oauth2-auth-code-flow.md) |
> | Java </p> Spring|[GitHub 存储库](https://github.com/Azure-Samples/ms-identity-java-webapi) | 使用户登录并通过 OBO 调用 Microsoft Graph </p> &#8226; Web API | MSAL Java | &#8226; [授权代码流](./v2-oauth2-auth-code-flow.md) <br/> &#8226; [代理 (OBO) 流](./v2-oauth2-on-behalf-of-flow.md) |
> | Node.js </p> Express |[GitHub 存储库](https://github.com/Azure-Samples/ms-identity-node) | Express Web 应用示例 <br/> &#8226; 使用户登录 | MSAL Node | [授权代码流](./v2-oauth2-auth-code-flow.md) |
> | Node.js </p> Express |[GitHub 存储库](https://github.com/Azure-Samples/ms-identity-node) | Express Web 应用系列 <br/> &#8226; [使用户登录](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-tutorial/blob/main/1-Authentication/1-sign-in/README.md)<br/> &#8226; [使用户登录 (B2C)](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-tutorial/blob/main/1-Authentication/2-sign-in-b2c/README.md)<br/> &#8226; [调用 Microsoft Graph](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-tutorial/blob/main/2-Authorization/1-call-graph/README.md)<br/> &#8226; [部署到 Azure 应用服务](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-tutorial/blob/main/3-Deployment/README.md)<br/> &#8226; [使用应用角色进行访问控制](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-tutorial/blob/main/4-AccessControl/1-app-roles/README.md)<br/> &#8226; [使用安全组进行访问控制](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-tutorial/blob/main/4-AccessControl/2-security-groups/README.md) | MSAL Node | [授权代码流](./v2-oauth2-auth-code-flow.md) |
> | Python </p> Flask |[GitHub 存储库](https://github.com/Azure-Samples/ms-identity-python-flask-tutorial) | Flask 系列 <br/> &#8226; 使用户登录 <br/> &#8226; 使用户登录 (B2C) <br/> &#8226; 调用 Microsoft Graph <br/> &#8226; 部署到 Azure 应用服务 | MSAL Python | [授权代码流](./v2-oauth2-auth-code-flow.md) |
> | Python </p> Django |[GitHub 存储库](https://github.com/Azure-Samples/ms-identity-python-django-tutorial) | Django 系列 <br/> &#8226; [使用户登录](https://github.com/Azure-Samples/ms-identity-python-django-tutorial/tree/main/1-Authentication/sign-in) <br/> &#8226; [使用户登录 (B2C)](https://github.com/Azure-Samples/ms-identity-python-django-tutorial/tree/main/1-Authentication/sign-in-b2c) <br/> &#8226; [调用 Microsoft Graph](https://github.com/Azure-Samples/ms-identity-python-django-tutorial/tree/main/2-Authorization-I/call-graph) <br/> &#8226; [部署到 Azure 应用服务](https://github.com/Azure-Samples/ms-identity-python-django-tutorial/tree/main/3-Deployment/deploy-to-azure-app-service)| MSAL Python | [授权代码流](./v2-oauth2-auth-code-flow.md) |
> | Python </p> Flask |[GitHub 存储库](https://github.com/Azure-Samples/ms-identity-python-webapp) | Flask 独立示例 <br/> [使用户登录并调用 Microsoft Graph](https://github.com/Azure-Samples/ms-identity-python-webapp) | MSAL Python | [授权代码流](./v2-oauth2-auth-code-flow.md) |
> | Ruby |[GitHub 存储库](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp) | Graph 训练 <br/> &#8226; [登录和 Microsoft Graph](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp) | | |

## <a name="desktop-and-mobile-public-client-apps"></a>桌面和移动公共客户端应用

以下示例展示了以用户身份访问 Microsoft Graph API 或你自己的 Web API 的公共客户端应用程序（桌面或移动应用程序）。 除了使用 WAM 的桌面（控制台）示例，所有这些客户端应用程序均使用 Microsoft 身份验证库 (MSAL)。

| 客户端应用程序 | 平台 | 流/授权 | 调用 Microsoft Graph | 调用 ASP.NET Core Web API |
| ------------------ | -------- |  ----------| ---------- | ------------------------- |
| 桌面教程 (.NET Core) - 可选择使用：</p>- 跨平台令牌缓存</p>- 自定义 Web UI | ![此图显示了 .NET/C# 徽标](media/sample-v2-code/logo_NETcore.png) | [授权代码](msal-authentication-flows.md#authorization-code)| [ms-identity-dotnet-desktop-tutorial](https://github.com/azure-samples/ms-identity-dotnet-desktop-tutorial) | |
| 桌面 (WPF)      | ![此图显示了 .NET 桌面/C# 徽标](media/sample-v2-code/logo_NET.png) | [授权代码](msal-authentication-flows.md#authorization-code)| [dotnet-desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | [dotnet-native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi) |
| 桌面（控制台）   | ![此图显示了 .NET/C#（桌面）徽标](media/sample-v2-code/logo_NET.png) | [Windows 集成身份验证](msal-authentication-flows.md#integrated-windows-authentication) | [dotnet-iwa-v2](https://github.com/azure-samples/active-directory-dotnet-iwa-v2) |  |
| 桌面（控制台） | ![此图显示了 .NET/C#（桌面）徽标](media/sample-v2-code/logo_NET.png) |  [授权代码](msal-authentication-flows.md#authorization-code) | [active-directory-dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/tree/master/1-Call-MSGraph) |[active-directory-dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/master/2-Call-OwnApi/README.md)  |
| 桌面（控制台） <br> 使用证书而不是机密 | ![此图显示了 .NET/C#（桌面）徽标](media/sample-v2-code/logo_NET.png) |  [授权代码](msal-authentication-flows.md#authorization-code) | [active-directory-dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/tree/master/1-Call-MSGraph#variation-daemon-application-using-client-credentials-with-certificates) |[active-directory-dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/tree/master/2-Call-OwnApi#variation-daemon-application-using-client-credentials-with-certificates)  |
| 桌面（控制台）   | ![此图显示了 Java 徽标](media/sample-v2-code/logo_java.png) | [Windows 集成身份验证](msal-authentication-flows.md#integrated-windows-authentication) |[ms-identity-java-desktop](https://github.com/Azure-Samples/ms-identity-java-desktop/) |  |
| 桌面（控制台）   | ![这是 .NET/C#（桌面）徽标](media/sample-v2-code/logo_NETcore.png) | [用户名/密码](msal-authentication-flows.md#usernamepassword) |[dotnetcore-up-v2](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2) |  |
| 使用 WAM 的桌面（控制台）  | ![这是 .NET/C#（桌面）的徽标](media/sample-v2-code/logo_NETcore.png) | 与 [Web 帐户管理器](/windows/uwp/security/web-account-manager) (WAM) 交互 |[dotnet-native-uwp-wam](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) |  |
| 桌面（控制台）   | ![此图显示了 Java 徽标](media/sample-v2-code/logo_java.png) | [用户名/密码](msal-authentication-flows.md#usernamepassword) |[ms-identity-java-desktop](https://github.com/Azure-Samples/ms-identity-java-desktop/) |  |
| 桌面（控制台）   | ![此图显示了 Python 徽标](media/sample-v2-code/logo_python.png) | [用户名/密码](msal-authentication-flows.md#usernamepassword) |[ms-identity-python-desktop](https://github.com/Azure-Samples/ms-identity-python-desktop) |  |
| Desktop (Electron)   | ![此图显示了 Node.js 徽标](media/sample-v2-code/logo_nodejs.png)</p>Node.js (MSAL Node) | [授权代码 (PKCE)](msal-authentication-flows.md#authorization-code) |[ms-identity-javascript-nodejs-desktop](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-desktop) |  |
| 移动（Android、iOS、UWP）   | ![此图显示了 .NET/C# (Xamarin) 徽标](media/sample-v2-code/logo_xamarin.png) | [授权代码](msal-authentication-flows.md#authorization-code) |[xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) |  |
| 移动 (iOS)       | ![此图显示了 iOS/Objective-C 或 Swift](media/sample-v2-code/logo_iOS.png) | [授权代码](msal-authentication-flows.md#authorization-code) |[ios-swift-objc-native-v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) </p> [ios-native-nxoauth2-v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |  |
| 桌面 (macOS)       | macOS | [授权代码](msal-authentication-flows.md#authorization-code) |[macOS-swift-objc-native-v2](https://github.com/Azure-Samples/ms-identity-macOS-swift-objc) |  |
| 移动 (Android-Java)   | ![此图显示了 Android 徽标](media/sample-v2-code/logo_Android.png) | [授权代码](msal-authentication-flows.md#authorization-code) |  [android-Java](https://github.com/Azure-Samples/ms-identity-android-java) |  |
| 移动 (Android-Kotlin)   | ![此图显示了 Android 徽标](media/sample-v2-code/logo_Android.png) | [授权代码](msal-authentication-flows.md#authorization-code) |  [android-Kotlin](https://github.com/Azure-Samples/ms-identity-android-kotlin) |  |

## <a name="daemon-applications"></a>守护程序应用程序

下面的示例展示了一个应用程序，它使用自己的标识（没有用户）访问 Microsoft Graph API。

| 客户端应用程序 | 平台 | 流/授权 | 调用 Microsoft Graph |
| ------------------ | -------- | ---------- | -------------------- |
| 控制台 | ![此图显示了 .NET Core 徽标](media/sample-v2-code/logo_NETcore.png)</p> ASP.NET  | [客户端凭据](msal-authentication-flows.md#client-credentials) | [dotnetcore-daemon-v2](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2) |
| Web 应用 | ![显示 ASP.NET 徽标的屏幕截图。](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET  | [客户端凭据](msal-authentication-flows.md#client-credentials) | [dotnet-daemon-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2) |
| 控制台 | ![此图显示了 Java 徽标](media/sample-v2-code/logo_java.png) | [客户端凭据](msal-authentication-flows.md#client-credentials) | [ms-identity-java-daemon](https://github.com/Azure-Samples/ms-identity-java-daemon) |
| 控制台 | ![此图显示了 Node.js 徽标](media/sample-v2-code/logo_nodejs.png)</p>Node.js (MSAL Node)| [客户端凭据](msal-authentication-flows.md#client-credentials) | [ms-identity-javascript-nodejs-console](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-console) |
| 控制台 | ![此图显示了 Python 徽标](media/sample-v2-code/logo_python.png) | [客户端凭据](msal-authentication-flows.md#client-credentials) | [ms-identity-python-daemon](https://github.com/Azure-Samples/ms-identity-python-daemon) |

## <a name="headless-applications"></a>无外设应用程序

以下示例展示了在没有 Web 浏览器的设备上运行的公共客户端应用程序。 该应用可以是命令行工具，可以是在 Linux 或 Mac 上运行的应用，还可以是 IoT 应用程序。 此示例提供了一个应用，以用户身份访问 Microsoft Graph API，该用户以交互方式在另一台设备上登录（例如移动电话）。 此客户端应用程序使用 Microsoft 身份验证库 (MSAL)。

| 客户端应用程序 | 平台 | 流/授权 | 调用 Microsoft Graph |
| ------------------ | -------- |  ----------| ---------- |
| 桌面（控制台）   | ![此图显示了 .NET/C#（桌面）徽标](media/sample-v2-code/logo_NETcore.png) | [设备代码流](msal-authentication-flows.md#device-code) |[dotnetcore-devicecodeflow-v2](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2) |
| 桌面（控制台）   | ![此图显示了 Java 徽标](media/sample-v2-code/logo_java.png) | [设备代码流](msal-authentication-flows.md#device-code) |[ms-identity-java-devicecodeflow](https://github.com/Azure-Samples/ms-identity-java-devicecodeflow) |
| 桌面（控制台）   | ![此图显示了 Python 徽标](media/sample-v2-code/logo_python.png) | [设备代码流](msal-authentication-flows.md#device-code) |[ms-identity-python-devicecodeflow](https://github.com/Azure-Samples/ms-identity-python-devicecodeflow) |

## <a name="multi-tenant-saas-applications"></a>多租户 SaaS 应用程序

以下示例演示了如何配置应用程序，使其接受来自 Azure Active Directory (Azure AD) 租户的登录。 将应用程序配置为多租户是指，你可向多个组织提供软件即服务 (SaaS) 应用程序，使它们的用户能够在同意后登录到你的应用程序。

| 平台 | 说明 | 链接 |
| -------- | --------------------- | -------- |
| ![此图显示了 Angular 徽标](media/sample-v2-code/logo_angular.png) [Angular (MSAL Angular 2.0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) | 多租户 SPA 调用多租户自定义 Web API |[ms-identity-javascript-angular-spa-aspnet-webapi-multitenant](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnet-webapi-multitenant/tree/master/Chapter2) |
| ![此图显示了 ASP.NET Core 徽标](media/sample-v2-code/logo_NETcore.png) [.NET Core (MSAL.NET)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | ASP.NET Core MVC Web 应用程序调用图形 API |[active-directory-aspnetcore-webapp-openidconnect-v2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-3-Multi-Tenant) |
| ![此图显示了 ASP.NET Core 徽标](media/sample-v2-code/logo_NETcore.png) [.NET Core (MSAL.NET)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | ASP.NET Core MVC Web 应用程序调用 ASP.NET Core Web API |[active-directory-aspnetcore-webapp-openidconnect-v2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/4-WebApp-your-API/4-3-AnyOrg) |

## <a name="web-apis"></a>Web API

以下示例展示了如何使用 Microsoft 标识平台保护 Web API，以及如何从 Web API 调用下游 API。

| 平台 | 示例 |
| -------- | ------------------- |
| ![此图显示了 ASP.NET Core 徽标](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core | [dotnet-native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi-calls-msgraph) 的 ASP.NET Core Web API（服务）  |
| ![此图显示了 ASP.NET 徽标](media/sample-v2-code/logo_NET.png)</p>ASP.NET MVC | [ms-identity-aspnet-webapi-onbehalfof](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof) 的 Web API（服务） |
| ![此图显示了 Java 徽标](media/sample-v2-code/logo_java.png) | [ms-identity-java-webapi](https://github.com/Azure-Samples/ms-identity-java-webapi) 的 Web API（服务） |
| ![此图显示了 Node.js 徽标](media/sample-v2-code/logo_nodejs.png)</p>Node.js (Passport.js)| [active-directory-javascript-nodejs-webapi-v2](https://github.com/Azure-Samples/active-directory-javascript-nodejs-webapi-v2) 的 Web API（服务） |
| ![此图显示了 Node.js 徽标](media/sample-v2-code/logo_nodejs.png)</p>Node.js (Passport.js)| [active-directory-b2c-javascript-nodejs-webapi](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) 的 B2C Web API（服务） |

## <a name="azure-functions-as-web-apis"></a>Azure Functions 作为 Web API

以下示例演示如何使用 HttpTrigger 保护 Azure Function 并通过 Microsoft 标识平台公开 Web API，以及如何从 Web API 调用下游 API。

| 平台 | 示例 |
| -------- | ------------------- |
| ![此图显示了 ASP.NET Core 徽标](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core | [dotnet-native-aspnetcore-v2](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions) 的 ASP.NET Core Web API（服务）Azure Functions  |
| ![此图显示了 Python 徽标](media/sample-v2-code/logo_python.png)</p>Python | [Python](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions) 的 Web API（服务） |
| ![此图显示了 Node.js 徽标](media/sample-v2-code/logo_nodejs.png)</p>Node.js (Passport.js)| [Node.js 和 passport-azure-ad](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions) 的 Web API（服务） |
| ![此图显示了 Node.js 徽标](media/sample-v2-code/logo_nodejs.png)</p>Node.js (Passport.js)| [使用 on behalf of 的 Node.js 和 passport-azure-ad](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions) 的 Web API（服务） |

## <a name="other-microsoft-graph-samples"></a>其他 Microsoft Graph 示例

若要了解演示 Microsoft Graph API 的各种使用模式（包括向 Azure AD 进行身份验证）的[示例](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet)和教程，请参阅 [Microsoft Graph Community samples & tutorials](https://github.com/microsoftgraph/msgraph-community-samples)（Microsoft Graph 社区示例和教程）。

## <a name="see-also"></a>另请参阅

[Microsoft Graph API 概念和参考](/graph/use-the-api?context=graph%2fapi%2fbeta&view=graph-rest-beta&preserve-view=true)