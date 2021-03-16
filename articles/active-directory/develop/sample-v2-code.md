---
title: Microsoft 标识平台的代码示例
description: 提供可用 Microsoft 标识平台代码示例的索引，这些示例按方案进行组织。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: sample
ms.workload: identity
ms.date: 11/04/2020
ms.author: marsma
ms.reviewer: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: d61ddee1ac510b6d3528a2221a52fd266a2dd5ad
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2021
ms.locfileid: "102436492"
---
# <a name="microsoft-identity-platform-code-samples-v20-endpoint"></a>Microsoft 标识平台代码示例（v2.0 终结点）

你可以使用 Microsoft 标识平台执行以下操作：

- 向 Web 应用程序和 Web API 添加身份验证和授权。
- 要求使用访问令牌来访问受保护的 Web API。

本文简要介绍了 Microsoft 标识平台示例并提供了这些示例的链接。 这些示例将展示其工作原理，并提供可以在应用程序中使用的代码片段。 在代码示例页上，可以找到在要求、安装和设置方面提供帮助的详细自述主题。 代码中的注释可帮助你理解关键部分。

若要了解每种示例类型的基本方案，请参阅 [Microsoft 标识平台的应用类型](v2-app-types.md)。

你也可以为 GitHub 上的示例做出补充。 若要了解如何操作，请参阅 [Microsoft Azure Active Directory 示例和文档](https://github.com/Azure-Samples?page=3&query=active-directory)。

## <a name="single-page-applications"></a>单页应用程序

这些示例展示了如何编写由 Microsoft 标识平台保护的单页应用程序。 这些示例使用下列种类的 MSAL.js 之一。

| 平台 | 说明 | 链接 |
| -------- | --------------------- | -------- |
| ![此图显示了 JavaScript 徽标](media/sample-v2-code/logo_js.png) [JavaScript (MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | SPA 调用 Microsoft Graph |[javascript-graphapi-v2](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2) |
| ![此图显示了 JavaScript 徽标](media/sample-v2-code/logo_js.png) [JavaScript (MSAL.js 2.0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser) | SPA 使用含 PKCE 的身份验证代码流调用 Microsoft Graph |[javascript-v2](https://github.com/Azure-Samples/ms-identity-javascript-v2) |
| ![此图显示了 JavaScript 徽标](media/sample-v2-code/logo_js.png) [JavaScript (MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | SPA 调用 B2C |[b2c-javascript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp) |
| ![此图显示了 JavaScript 徽标](media/sample-v2-code/logo_js.png) [JavaScript (MSAL.js 2.0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser) | SPA 使用含 PKCE 的身份验证代码流调用 B2C |[b2c-javascript-spa](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa) |
| ![此图显示了 Angular 徽标](media/sample-v2-code/logo_angular.png) [Angular (MSAL Angular)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)| SPA 调用 Microsoft Graph  | [active-directory-javascript-singlepageapp-angular](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular) |
| ![此图显示了 Angular 徽标](media/sample-v2-code/logo_angular.png) [Angular (MSAL Angular 2.0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)| SPA 使用含 PKCE 的身份验证代码流调用 Microsoft Graph | [ms-identity-javascript-angular-spa](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa) |
| ![此图显示了 Angular 徽标](media/sample-v2-code/logo_angular.png) [Angular (MSAL Angular 2.0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)| SPA 调用自定义 Web API | [ms-identity-javascript-angular-spa-aspnetcore-webapi](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnetcore-webapi) |
| ![此图显示了 Angular 徽标](media/sample-v2-code/logo_angular.png) [Angular (MSAL Angular)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) | SPA 调用 B2C |[active-directory-b2c-javascript-angular-spa](https://github.com/Azure-Samples/active-directory-b2c-javascript-angular-spa) |
| ![此图显示了 React 徽标](media/sample-v2-code/logo_react.png) [React (MSAL React)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-react)| SPA 使用含 PKCE 的身份验证代码流调用 Microsoft Graph | [ms-identity-javascript-react-spa](https://github.com/Azure-Samples/ms-identity-javascript-react-spa) |
| ![此图显示了 React 徽标](media/sample-v2-code/logo_react.png) [React (MSAL.js 2.0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core)| SPA 调用自定义 Web API，后者反过来调用 Microsoft Graph  | [ms-identity-javascript-react-spa-dotnetcore-webapi-obo](https://github.com/Azure-Samples/ms-identity-javascript-react-spa-dotnetcore-webapi-obo) |
| ![此图显示了 JavaScript 徽标](media/sample-v2-code/logo_js.png) [JavaScript (MSAL.js 2.0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser) | SPA 调用自定义 Web API，后者反过来调用 Microsoft Graph  | [ms-identity-javascript-tutorial-chapter4-obo](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/tree/main/4-AdvancedGrants/1-call-api-graph) |
| ![此图显示了 Angular 徽标](media/sample-v2-code/logo_angular.png) [Angular (MSAL Angular 2.0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) | SPA 使用应用角色和安全组调用自定义 Web API |[ms-identity-javascript-angular-spa-dotnetcore-webapi-roles-groups](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-dotnetcore-webapi-roles-groups) |
| ![此图显示了 Blazor 徽标](media/sample-v2-code/logo-blazor.png) [Blazor WebAssembly (MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser) | Blazor WebAssembly 教程：通过 Azure Active Directory 让用户登录并调用 API |[ms-identity-blazor-wasm](https://github.com/Azure-Samples/ms-identity-blazor-wasm) |

## <a name="web-applications"></a>Web 应用程序

以下示例演示了将用户登录的 Web 应用。 一些示例还演示了使用用户标识调用 Microsoft Graph 或你自己的 Web API 的应用程序。

| 平台 | 仅让用户登录 | 让用户登录并调用 Microsoft Graph |
| -------- | ------------------- | --------------------------------- |
| ![此图显示了 ASP.NET Core 徽标](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core | [ASP.NET Core WebApp 让用户登录教程](https://aka.ms/aspnetcore-webapp-sign-in) | [ASP.NET Core Web 应用调用 Microsoft Graph](https://aka.ms/aspnetcore-webapp-call-msgraph) 阶段中的同一示例</p>高级示例：[从后台应用、API 和服务访问已登录用户的令牌缓存](https://github.com/Azure-Samples/ms-identity-dotnet-advanced-token-cache) |
| ![此图显示了 ASP.NET Framework 徽标](media/sample-v2-code/logo_NETframework.png)</p>ASP.NET Core | 请参阅[适用于开发人员的 AD FS 到 Azure AD 应用程序迁移手册](https://github.com/Azure-Samples/ms-identity-dotnet-adfs-to-aad)了解如何将与 Active Directory 联合身份验证服务 (AD FS) 集成的应用程序安全可靠地迁移到 Azure Active Directory (Azure AD) | |
| ![此图显示了 ASP.NET Framework 徽标](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET | [ASP.NET 快速入门](https://github.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) </p> [dotnet-webapp-openidconnect-v2](https://github.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |  [dotnet-admin-restricted-scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) </p> |[msgraph-training-aspnetmvcapp](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp)
| ![此图显示了 Java 徽标](media/sample-v2-code/logo_java.png)  |[Java Servlet 教程 - 第 1.1 章](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/blob/main/README.md)使用 AAD 登录| |
| ![此图显示了 Java 徽标](media/sample-v2-code/logo_java.png)  |[Java Servlet 教程 - 第 1.2 章](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/blob/main/README.md)使用 B2C 登录 |
| ![此图显示了 Java 徽标](media/sample-v2-code/logo_java.png)  | | [Java Servlet 教程 - 第 2.1 章](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/blob/main/README.md)使用 AAD 登录并调用 Graph|
| ![此图显示了 Java 徽标](media/sample-v2-code/logo_java.png)  |[Java Servlet 教程 - 第 3.1 章](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/blob/main/README.md)使用 AAD 登录并使用角色声明控制访问| |
| ![此图显示了 Java 徽标](media/sample-v2-code/logo_java.png)  | | [Java Servlet 教程 - 第 3.2 章](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/blob/main/README.md)使用 AAD 登录并使用组声明控制访问|
| ![此图显示了 Java 徽标](media/sample-v2-code/logo_java.png) | |[Java Servlet 教程 - 第 4.1 章](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/blob/main/README.md)部署到 Azure 应用服务|
| ![此图显示了 Java 徽标](media/sample-v2-code/logo_java.png)  | | [ms-identity-java-webapp](https://github.com/Azure-Samples/ms-identity-java-webapp) |
| ![此图显示了 Java 徽标](media/sample-v2-code/logo_java.png)  | [ms-identity-b2c-java-servlet-webapp-authentication](https://github.com/Azure-Samples/ms-identity-b2c-java-servlet-webapp-authentication)|  |
| ![此图显示了 Node.js 徽标](media/sample-v2-code/logo_nodejs.png)</p>Node.js (MSAL Node) | [Express Web 应用登录用户教程](https://github.com/Azure-Samples/ms-identity-node) | |
| ![此图显示了 Python 徽标](media/sample-v2-code/logo_python.png)  | [Python Flask 教程 - 第 1.1 章](https://github.com/Azure-Samples/ms-identity-python-flask-tutorial/blob/main/README.md)使用 AAD 登录  |  |
| ![此图显示了 Python 徽标](media/sample-v2-code/logo_python.png)  | [Python Flask 教程 - 第 1.2 章](https://github.com/Azure-Samples/ms-identity-python-flask-tutorial/blob/main/README.md)使用 B2C 登录                    |  |
| ![此图显示了 Python 徽标](media/sample-v2-code/logo_python.png)  | | [Python Flask 教程 - 第 2.1 章](https://github.com/Azure-Samples/ms-identity-python-flask-tutorial/blob/main/README.md)使用 AAD 登录并调用 Graph |
| ![此图显示了 Python 徽标](media/sample-v2-code/logo_python.png)  | |[Python Flask 教程 - 第 3.1 章](https://github.com/Azure-Samples/ms-identity-python-flask-tutorial/blob/main/README.md)部署到 Azure 应用服务  |
| ![此图显示了 Python 徽标](media/sample-v2-code/logo_python.png)  | [Python Django 教程 - 第 1.1 章](https://github.com/Azure-Samples/ms-identity-python-django-tutorial/blob/main/README.md)使用 AAD 登录  | |
| ![此图显示了 Python 徽标](media/sample-v2-code/logo_python.png)  | [Python Django 教程 - 第 1.2 章](https://github.com/Azure-Samples/ms-identity-python-django-tutorial/blob/main/README.md)使用 B2C 登录                    |  |
| ![此图显示了 Python 徽标](media/sample-v2-code/logo_python.png)  | | [Python Django 教程 - 第 2.1 章](https://github.com/Azure-Samples/ms-identity-python-django-tutorial/blob/main/README.md)使用 AAD 登录并调用 Graph|
| ![此图显示了 Python 徽标](media/sample-v2-code/logo_python.png)  | | [Python Django 教程 - 第 3.1 章](https://github.com/Azure-Samples/ms-identity-python-django-tutorial/blob/main/README.md)部署到 Azure 应用服务                    |
| ![此图显示了 Python 徽标](media/sample-v2-code/logo_python.png)  | | [Python Flask Web 应用](https://github.com/Azure-Samples/ms-identity-python-webapp) |
| ![此图显示了 Ruby 徽标](media/sample-v2-code/logo_ruby.png) |                   | [msgraph-training-rubyrailsapp](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp) |
| ![此图显示了 Blazor 徽标](media/sample-v2-code/logo-blazor.png)</p>Blazor 服务器 | [Blazor Server 应用用户登录教程](https://github.com/Azure-Samples/ms-identity-blazor-server/tree/main/WebApp-OIDC) | [Blazor Server 应用调用 Microsoft Graph](https://github.com/Azure-Samples/ms-identity-blazor-server/tree/main/WebApp-graph-user/Call-MSGraph)</p>Chapterwise 教程：[通过 Azure Active Directory 让用户登录并调用 API 的 Blazor Server 应用](https://github.com/Azure-Samples/ms-identity-blazor-server) |

## <a name="desktop-and-mobile-public-client-apps"></a>桌面和移动公共客户端应用

以下示例展示了以用户身份访问 Microsoft Graph API 或你自己的 Web API 的公共客户端应用程序（桌面或移动应用程序）。 除了使用 WAM 的桌面（控制台）示例，所有这些客户端应用程序均使用 Microsoft 身份验证库 (MSAL)。

| 客户端应用程序 | 平台 | 流/授权 | 调用 Microsoft Graph | 调用 ASP.NET Core Web API |
| ------------------ | -------- |  ----------| ---------- | ------------------------- |
| 桌面教程 (.NET Core) - 可选择使用：</p>- 跨平台令牌缓存</p>- 自定义 Web UI | ![此图显示了 .NET/C# 徽标](media/sample-v2-code/logo_NETcore.png) | [授权代码](msal-authentication-flows.md#authorization-code)| [ms-identity-dotnet-desktop-tutorial](https://github.com/azure-samples/ms-identity-dotnet-desktop-tutorial) | |
| 桌面 (WPF)      | ![此图显示了 .NET 桌面/C# 徽标](media/sample-v2-code/logo_NET.png) | [授权代码](msal-authentication-flows.md#authorization-code)| [dotnet-desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | [dotnet-native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi) |
| 桌面（控制台）   | ![此图显示了 .NET/C#（桌面）徽标](media/sample-v2-code/logo_NET.png) | [Windows 集成身份验证](msal-authentication-flows.md#integrated-windows-authentication) | [dotnet-iwa-v2](https://github.com/azure-samples/active-directory-dotnet-iwa-v2) |  |
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
| ![此图显示了 Angular 徽标](media/sample-v2-code/logo_angular.png) [Angular (MSAL Angular 2.0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) | 多租户 SPA 调用图形 API |[ms-identity-javascript-angular-spa-aspnet-webapi-multitenant](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnet-webapi-multitenant/tree/master/Chapter1) |
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
