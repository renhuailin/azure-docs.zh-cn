---
title: Microsoft 标识平台身份验证库 |Microsoft
description: 与 Microsoft 标识平台兼容的客户端库和中间件的列表。 使用这些库来添加对用户登录 (身份验证的支持) 和 (授权) 应用程序的受保护的 web API 访问权限。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 01/29/2021
ms.author: marsma
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.openlocfilehash: 228a15e9e9e27cbcfd71d4762db2f4ab9f6dfffe
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/17/2021
ms.locfileid: "100560148"
---
# <a name="microsoft-identity-platform-authentication-libraries"></a>Microsoft 标识平台身份验证库

下表显示了针对多种应用程序类型的 Microsoft 身份验证库支持。 它们包括到库源代码的链接、在何处获取应用程序项目的包、库是否支持用户登录 (authentication) 、访问受保护的 web Api (授权) ，或同时访问这两者。

Microsoft 标识平台已由 OpenID Foundation 作为 [认证的 OpenID 提供程序](https://openid.net/certification/)进行了认证。 如果希望使用 Microsoft 身份验证库以外的其他库 (MSAL) 或另一个 Microsoft 支持的库，请选择一个具有 [认证的 OpenID connect 实现](https://openid.net/developers/certified/)。

如果选择手动编码 [OAuth 2.0 或 OpenID connect 1.0](active-directory-v2-protocols.md)的协议级别实现，请密切注意每个标准规范中的安全注意事项，并遵循 [MICROSOFT sdl][Microsoft-SDL] (sdl) 方法的软件开发生命周期。

## <a name="single-page-application-spa"></a>单页面应用程序 (SPA) 

单页应用程序完全在浏览器图面上运行，并 (HTML、CSS 和 JavaScript) 动态或在应用程序加载时，获取页面数据。 它可以调用 web Api 来与后端数据源进行交互。

由于 SPA 的代码完全在浏览器中运行，因此它被认为是无法安全地存储机密的 *公共客户端* 。

| 语言/框架 | 项目于<br/>GitHub                                                                                                    | 程序包                                                                      | 到达<br/>started                             | 用户登录                                         | 访问 web Api                                                 | 公开发布 (GA) *或*<br/>公共预览版<sup>1</sup> |
|----------------------|--------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------|:-----------------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Angular              | [MSAL 角2。0](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)         | [@azure/msal-angular](https://www.npmjs.com/package/@azure/msal-angular)     | —                                               | ![库可以请求用户登录的 ID 令牌。][y] | ![库可以请求受保护的 web Api 的访问令牌。][y] | 公共预览版                                               |
| Angular              | [MSAL Angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/msal-angular-v1/lib/msal-angular) | [@azure/msal-angular](https://www.npmjs.com/package/@azure/msal-angular)     | [教程](tutorial-v2-angular.md)              | ![库可以请求用户登录的 ID 令牌。][y] | ![库可以请求受保护的 web Api 的访问令牌。][y] | GA                                                           |
| AngularJS            | [MSAL AngularJS](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angularjs)         | [@azure/msal-angularjs](https://www.npmjs.com/package/@azure/msal-angularjs) | —                                               | ![库可以请求用户登录的 ID 令牌。][y] | ![库可以请求受保护的 web Api 的访问令牌。][y] | 公共预览版                                               |
| JavaScript           | [MSAL.js 2。0](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)              | [@azure/msal-browser](https://www.npmjs.com/package/@azure/msal-browser)     | [教程](tutorial-v2-javascript-auth-code.md) | ![库可以请求用户登录的 ID 令牌。][y] | ![库可以请求受保护的 web Api 的访问令牌。][y] | GA                                                           |
| JavaScript           | [MSAL.js 1。0](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-core)              | [@azure/msal-core](https://www.npmjs.com/package/@azure/msal-core)     | [教程](tutorial-v2-javascript-spa.md) | ![库可以请求用户登录的 ID 令牌。][y] | ![库可以请求受保护的 web Api 的访问令牌。][y] | GA                                                           |
| React                | [MSAL 响应](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-react)                 | [@azure/msal-react](https://www.npmjs.com/package/@azure/msal-react)         | —                                               | ![库可以请求用户登录的 ID 令牌。][y] | ![库可以请求受保护的 web Api 的访问令牌。][y] | 公共预览版                                               |
<!--
| Vue | [Vue MSAL]( https://github.com/mvertopoulos/vue-msal) | [vue-msal]( https://www.npmjs.com/package/vue-msal) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [Microsoft Azure 预览的补充使用条款][preview-tos] 适用于 *公共预览版* 中的库。

## <a name="web-application"></a>Web 应用程序

Web 应用程序在服务器上运行代码，该服务器生成 HTML、CSS 和 JavaScript，并将其发送到要呈现的用户的 web 浏览器。 用户的标识在用户浏览器 (前端) 和 web 服务器 (后端) 之间进行会话维护。

由于 web 应用程序的代码在 web 服务器上运行，因此它被视为可安全存储机密的 *机密客户端* 。

| 语言/框架 | 项目于<br/>GitHub                                                                                     | 程序包                                                                                                    | 到达<br/>started                               | 用户登录                                            | 访问 web Api                                                    | 公开发布 (GA) *或*<br/>公共预览版<sup>1</sup> |
|----------------------|-----------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------|:-------------------------------------------------:|:--------------------------------------------------------:|:------------------------------------------------------------------:|:------------------------------------------------------------:|
| .NET                 | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)                        | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)                      | —                                                 | ![库无法请求用户登录的 ID 令牌。][n] | ![库可以请求受保护的 web Api 的访问令牌。][y]    | GA                                                           |
| ASP.NET Core         | [ASP.NET 安全性](/aspnet/core/security/)                                                                | [Microsoft.AspNetCore.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) | —                                                 | ![库可以请求用户登录的 ID 令牌。][y]    | ![库无法请求受保护的 web Api 的访问令牌。][n] | GA                                                           |
| ASP.NET Core         | [Microsoft.Identity.Web](https://github.com/AzureAD/microsoft-identity-web)                               | [Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web)                            | —                                                 | ![库可以请求用户登录的 ID 令牌。][y]    | ![库可以请求受保护的 web Api 的访问令牌。][y]    | GA                                                           |
| Java                 | [MSAL4J](https://github.com/AzureAD/microsoft-authentication-library-for-java)                            | [msal4j](https://search.maven.org/artifact/com.microsoft.azure/msal4j)                                     | [快速入门](quickstart-v2-java-webapp.md)        | ![库可以请求用户登录的 ID 令牌。][y]    | ![库可以请求受保护的 web Api 的访问令牌。][y]    | GA                                                           |
| Node.js              | [MSAL Node](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) | [msal-节点](https://www.npmjs.com/package/@azure/msal-node)                                                | [快速入门](quickstart-v2-nodejs-webapp-msal.md) | ![库可以请求用户登录的 ID 令牌。][y]    | ![库可以请求受保护的 web Api 的访问令牌。][y]    | GA                                               |
| Node.js              | [Azure AD Passport](https://github.com/AzureAD/passport-azure-ad)                                         | [passport-azure-ad](https://www.npmjs.com/package/passport-azure-ad)                                       | [快速入门](quickstart-v2-nodejs-webapp.md)      | ![库可以请求用户登录的 ID 令牌。][y]    | ![库可以请求受保护的 web Api 的访问令牌。][y] | GA                                                           |
| Python               | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)                     | [msal](https://pypi.org/project/msal)                                                                      | [快速入门](quickstart-v2-python-webapp.md)      | ![库可以请求用户登录的 ID 令牌。][y]    | ![库可以请求受保护的 web Api 的访问令牌。][y]    | GA                                                           |
<!--
| Java | [ScribeJava](https://github.com/scribejava/scribejava) | [ScribeJava 3.2.0](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | ![X indicating no.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
| Java | [Gluu oxAuth](https://github.com/GluuFederation/oxAuth) | [oxAuth 3.0.2](https://github.com/GluuFederation/oxAuth/releases/tag/3.0.2) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
| Node.js | [openid-client](https://github.com/panva/node-openid-client/) | [openid-client 2.4.5](https://github.com/panva/node-openid-client/releases/tag/v2.4.5) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
| PHP | [PHP League oauth2-client](https://github.com/thephpleague/oauth2-client) | [oauth2-client 1.4.2](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | ![X indicating no.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
| Ruby | [OmniAuth](https://github.com/omniauth/omniauth) | [omniauth 1.3.1](https://github.com/omniauth/omniauth/releases/tag/v1.3.1)<br/>[omniauth-oauth2 1.4.0](https://github.com/intridea/omniauth-oauth2) | ![X indicating no.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [Microsoft Azure 预览的补充使用条款][preview-tos] 适用于 *公共预览版* 中的库。

## <a name="desktop-application"></a>桌面应用程序

通常，桌面应用程序是二进制 (编译的) 代码，该代码会显示一个用户界面，旨在在用户的桌面上运行。

由于桌面应用程序在用户桌面上运行，因此它被视为不能安全地存储机密的 *公共客户端* 。

| 语言/框架 | 项目于<br/>GitHub                                                                                     | 程序包                                                                               | 到达<br/>started                        | 用户登录                                         | 访问 web Api                                                 | 公开发布 (GA) *或*<br/>公共预览版<sup>1</sup> |
|----------------------|-----------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|:------------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Electron             | [MSAL Node](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) | [@azure/msal-node](https://www.npmjs.com/package/@azure/msal-node)                    | [教程](tutorial-v2-nodejs-desktop.md)   | ![库可以请求用户登录的 ID 令牌。][y] | ![库可以请求受保护的 web Api 的访问令牌。][y] | GA                                               |
| Java                 | [MSAL4J](https://github.com/AzureAD/microsoft-authentication-library-for-java)                            | [msal4j](https://mvnrepository.com/artifact/com.microsoft.azure/msal4j)               | —                                          | ![库可以请求用户登录的 ID 令牌。][y] | ![库可以请求受保护的 web Api 的访问令牌。][y] | GA                                                           |
| macOS (Swift/Obj)   | [适用于 iOS 和 macOS 的 MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-objc)            | [MSAL](https://cocoapods.org/pods/MSAL)                                               | [教程](tutorial-v2-ios.md)             | ![库可以请求用户登录的 ID 令牌。][y] | ![库可以请求受保护的 web Api 的访问令牌。][y] | GA                                                           |
| UWP                  | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)                        | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) | [教程](tutorial-v2-windows-uwp.md)     | ![库可以请求用户登录的 ID 令牌。][y] | ![库可以请求受保护的 web Api 的访问令牌。][y] | GA                                                           |
| WPF                  | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)                        | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) | [教程](tutorial-v2-windows-desktop.md) | ![库可以请求用户登录的 ID 令牌。][y] | ![库可以请求受保护的 web Api 的访问令牌。][y] | GA                                                           |
<!--
| Java | Scribe | [Scribe Java](https://mvnrepository.com/artifact/org.scribe/scribe) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
| React Native | [React Native App Auth](https://github.com/FormidableLabs/react-native-app-auth/blob/main/docs/config-examples/azure-active-directory.md) | [react-native-app-auth](https://www.npmjs.com/package/react-native-app-auth) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [Microsoft Azure 预览的补充使用条款][preview-tos] 适用于 *公共预览版* 中的库。

## <a name="mobile-application"></a>移动应用程序

移动应用程序通常是二进制 (编译的) 代码，用于显示用户界面，并在用户的移动设备上运行。

由于移动应用程序在用户的移动设备上运行，因此它被认为是无法安全存储机密的 *公共客户端* 。

| 平台          | 项目于<br/>GitHub                                                                          | 程序包                                                                               | 到达<br/>started                    | 用户登录                                         | 访问 web Api                                                 | 公开发布 (GA) *或*<br/>公共预览版<sup>1</sup> |
|-------------------|------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|:--------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Android (Java)    | [MSAL Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)        | [MSAL](https://mvnrepository.com/artifact/com.microsoft.identity.client/msal)         | [快速入门](quickstart-v2-android.md) | ![库可以请求用户登录的 ID 令牌。][y] | ![库可以请求受保护的 web Api 的访问令牌。][y] | GA                                                           |
| Android (Kotlin)   | [MSAL Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)        | [MSAL](https://mvnrepository.com/artifact/com.microsoft.identity.client/msal)         | —                                      | ![库可以请求用户登录的 ID 令牌。][y] | ![库可以请求受保护的 web Api 的访问令牌。][y] | GA                                                           |
| iOS (Swift/Obj)  | [适用于 iOS 和 macOS 的 MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [MSAL](https://cocoapods.org/pods/MSAL)                                               | [教程](tutorial-v2-ios.md)         | ![库可以请求用户登录的 ID 令牌。][y] | ![库可以请求受保护的 web Api 的访问令牌。][y] | GA                                                           |
| Xamarin (.NET)    | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)             | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) | —                                      | ![库可以请求用户登录的 ID 令牌。][y] | ![库可以请求受保护的 web Api 的访问令牌。][y] | GA                                                           |
<!--
| React Native |[React Native App Auth](https://github.com/FormidableLabs/react-native-app-auth/blob/main/docs/config-examples/azure-active-directory.md) | [react-native-app-auth](https://www.npmjs.com/package/react-native-app-auth) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [Microsoft Azure 预览的补充使用条款][preview-tos] 适用于 *公共预览版* 中的库。

## <a name="service--daemon"></a>服务/守护程序

服务和守护程序通常用于服务器到服务器和其他无人参与的 (，有时称为无 *外设*) 通信。 由于键盘上没有用户输入凭据或同意资源访问权限，因此当请求授权访问 web API 的资源时，这些应用程序会自行进行身份验证，而不是用户身份验证。

在服务器上运行的服务或后台程序被视为可安全存储其机密的 *机密客户端* 。

| 语言/框架 | 项目于<br/>GitHub                                                                 | 程序包                                                                                | 到达<br/>started                           | 用户登录                                            | 访问 web Api                                                 | 公开发布 (GA) *或*<br/>公共预览版<sup>1</sup> |
|----------------------|---------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------|:---------------------------------------------:|:--------------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| .NET                 | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)    | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client/) | [快速入门](quickstart-v2-netcore-daemon.md) | ![库无法请求用户登录的 ID 令牌。][n] | ![库可以请求受保护的 web Api 的访问令牌。][y] | GA                                                           |
| Java                 | [MSAL4J](https://github.com/AzureAD/microsoft-authentication-library-for-java)        | [msal4j](https://javadoc.io/doc/com.microsoft.azure/msal4j/latest/index.html)          | —                                             | ![库无法请求用户登录的 ID 令牌。][n] | ![库可以请求受保护的 web Api 的访问令牌。][y] | GA                                                           |
| 节点               | [MSAL Node](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) | [msal-节点](https://www.npmjs.com/package/@azure/msal-node)  | [快速入门](quickstart-v2-nodejs-console.md)  | ![库无法请求用户登录的 ID 令牌。][n] | ![库可以请求受保护的 web Api 的访问令牌。][y] | GA  |
| Python               | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python) | [msal-python](https://github.com/AzureAD/microsoft-authentication-library-for-python)  | —  | ![库无法请求用户登录的 ID 令牌。][n] | ![库可以请求受保护的 web Api 的访问令牌。][y] | GA |
<!--
|PHP| [The PHP League oauth2-client](https://oauth2-client.thephpleague.com/usage/) | [League\OAuth2](https://oauth2-client.thephpleague.com/) | ![Green check mark.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [Microsoft Azure 预览的补充使用条款][preview-tos] 适用于 *公共预览版* 中的库。

## <a name="next-steps"></a>后续步骤

有关 Microsoft 身份验证库的详细信息，请参阅 [Microsoft 身份验证库概述 (MSAL) ](msal-overview.md)。

<!--Image references-->
[y]: ./media/common/yes.png
[n]: ./media/common/no.png

<!--Reference-style links -->
[AAD-App-Model-V2-Overview]: v2-overview.md
[Microsoft-SDL]: https://www.microsoft.com/securityengineering/sdl/
[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
