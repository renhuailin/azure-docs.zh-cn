---
title: Azure Active Directory B2C 与应用示例集成 | Microsoft Docs
description: 将 Azure AD B2C 与移动、桌面、Web 和单页应用程序集成的代码示例。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/02/2020
ms.custom: mvc
ms.topic: sample
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 007873ea1e1ffbfa31fd8be7284a6ec5e0e02979
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114479575"
---
# <a name="azure-active-directory-b2c-code-samples"></a>Azure Active Directory B2C 代码示例

以下各表提供了应用程序示例的链接，包括 iOS、Android、.NET 和 Node.js。

## <a name="mobile-and-desktop-apps"></a>移动和桌面应用

| 示例 | 说明 |
|--------| ----------- |
| [ios-swift-native-msal](https://github.com/Azure-Samples/active-directory-b2c-ios-swift-native-msal) | 用 Swift 编写的一个 iOS 示例，它使用 OAuth 2.0 对 Azure AD B2C 用户进行身份验证并调用一个 API |
| [android-native-msal](https://github.com/Azure-Samples/ms-identity-android-java#b2cmodefragment-class) | 一个简单的 Android 应用，它展示了如何使用 MSAL 来通过 Azure Active Directory B2C 对用户进行身份验证，并使用生成的令牌访问一个 Web API。 |
| [ios-native-appauth](https://github.com/Azure-Samples/active-directory-b2c-ios-native-appauth) | 此示例展示了如何利用第三方库来使用 Objective-C 开发 iOS 应用程序，用以通过我们的 Azure AD B2C 标识服务对 Microsoft 标识用户进行身份验证。 |
| [android-native-appauth](https://github.com/Azure-Samples/active-directory-b2c-android-native-appauth) | 此示例展示了如何利用第三方库来开发 Android 应用程序，用以通过我们的 B2C 标识服务对 Microsoft 标识用户进行身份验证并使用 OAuth 2.0 访问令牌调用 Web API。 |
| [dotnet-desktop](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) | 此示例展示了 Windows Desktop .NET (WPF) 应用程序如何使用 Azure AD B2C 完成用户登录，使用 MSAL.NET 获取访问令牌，并调用一个 API。 |
| [xamarin-native](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) | 一个简单的 Xamarin Forms 应用，它展示了如何使用 MSAL 来通过 Azure Active Directory B2C 对用户进行身份验证，并使用生成的令牌访问一个 Web API。 |

## <a name="web-apps-and-apis"></a>Web 应用和 API

| 示例 | 说明 |
|--------| ----------- |
| [dotnet-webapp-and-webapi](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi) | 调用了一个 .NET Web API 的 .NET Web 应用程序的组合示例，两者都使用 Azure AD B2C 提供保护。 |
| [dotnetcore-webapp-openidconnect](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-5-B2C) | 使用 OpenID Connect 在 Azure AD B2C 中登录用户的 ASP.NET Core Web 应用程序。 |
| [dotnetcore-webapp-msal-api](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/4-WebApp-your-API/4-2-B2C) | 一个 ASP.NET Core Web 应用程序，它可以使用 Azure AD B2C 完成用户登录，使用 MSAL.NET 获取访问令牌，并调用一个 API。 |
| [openidconnect-nodejs](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS) | 一个 Node.js 应用，它提供了一种便捷的方法来使用 OpenID Connect 设置通过 Express 开发的 Web 应用程序。 |
| [javascript-nodejs-webapi](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) | 一个用于 Azure AD B2C 的小 node.js Web API，它展示了如何使用 passport.js 保护 Web API 并接受 B2C 访问令牌。 |
| [ms-identity-python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/master/README_B2C.md) | 演示如何将 Microsoft 标识平台的 B2C 与 Python Web 应用程序集成。  |

## <a name="single-page-apps"></a>单页应用

| 示例 | 说明 |
|--------| ----------- |
| [ms-identity-javascript-angular-tutorial](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/tree/main/3-Authorization-II/2-call-api-b2c) | 调用 Web API 的 Angular 单页应用程序 (SPA)。 使用 MSAL Angular 完成向 Azure AD B2C 的身份验证。 此示例通过 PKCE 使用授权代码流。 |
| [ms-identity-javascript-react-tutorial](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/3-Authorization-II/2-call-api-b2c) | 调用 Web API 的 React 单页应用程序 (SPA)。 使用 MSAL React 完成向 Azure AD B2C 的身份验证。 此示例通过 PKCE 使用授权代码流。 |
| [ms-identity-b2c-javascript-spa](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa) | 调用 Web API 的 VanillaJS 单页应用程序 (SPA)。 通过 MSAL.js 使用 Azure AD B2C 完成身份验证。 此示例通过 PKCE 使用授权代码流。 |
| [javascript-nodejs-management](https://github.com/Azure-Samples/ms-identity-b2c-javascript-nodejs-management/tree/main/Chapter1) | 一个 VanillaJS 单页应用程序 (SPA)，它调用 Microsoft Graph 来管理 B2C 目录中的用户。 通过 MSAL.js 使用 Azure AD B2C 完成身份验证。 此示例通过 PKCE 使用授权代码流。|

## <a name="consoledaemon-apps"></a>控制台/守护程序应用

| 示例 | 说明 |
|--------| ----------- |
| [javascript-nodejs-management](https://github.com/Azure-Samples/ms-identity-b2c-javascript-nodejs-management/tree/main/Chapter2) | 一个 Node.js 快速控制台守护程序应用程序，它使用自己的标识调用 Microsoft Graph 来管理 B2C 目录中的用户。 通过使用 MSAL Node 完成向 Azure AD B2C 的身份验证。 此示例使用授权代码流。|
| [dotnetcore-b2c-account-management](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management) | 一个 .NET Core 控制台应用程序，它使用自己的标识调用 Microsoft Graph 来管理 B2C 目录中的用户。 通过使用 MSAL.NET 完成向 Azure AD B2C 的身份验证。 此示例使用授权代码流。|

## <a name="saml-test-application"></a>SAML 测试应用程序

| 示例 | 说明 |
|--------| ----------- |
| [saml-sp-tester](https://github.com/azure-ad-b2c/saml-sp-tester/tree/master/source-code) | SAML 测试应用程序，用于测试已配置为充当 SAML 标识提供者的 Azure AD B2C。 |

## <a name="api-connectors"></a>API 连接器

下表提供了一些代码示例的链接，这些示例使用 [API 连接器](api-connectors-overview.md)在用户流中利用 Web API。

### <a name="azure-function-quickstarts"></a>Azure Function 快速入门

| 示例                                                                                                                          | 说明                                                                                                                                               |
| ------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [.NET Core](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-api-connector-azure-function-validate) | 此 .NET Core Azure Function 示例演示如何将登录限制为特定电子邮件域并验证用户提供的信息。 |
| [Node.js](https://github.com/Azure-Samples/active-directory-nodejs-external-identities-api-connector-azure-function-validate)   | 此 Node.js Azure Function 示例演示如何将登录限制为特定电子邮件域并验证用户提供的信息。  |
| [Python](https://github.com/Azure-Samples/active-directory-python-external-identities-api-connector-azure-function-validate)    | 此 Python Azure Function 示例演示如何将登录限制为特定电子邮件域并验证用户提供的信息。    |


### <a name="automated-fraud-protection-services--captcha"></a>自动防欺诈服务和 CAPTCHA
| 示例                                                                                                            | 说明                                                                                                                          |
| ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| [Arkose Labs 欺诈和滥用防护](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose) | 此示例演示如何使用 Arkose Labs 欺诈和滥用防护服务来保护用户登录。 |
| [reCAPTCHA](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-captcha) | 此示例演示如何使用 reCAPTCHA 质询来保护用户登录，以防止自动滥用。 |


### <a name="identity-verification"></a>身份验证

| 示例                                                                                                            | 说明                                                                                                                          |
| ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| [IDology](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-idology-identity-verification) | 此示例演示如何使用 API 连接器与 IDology 集成，以在注册流中验证用户标识。 |
| [Experian](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-experian-identity-verification) | 此示例演示如何使用 API 连接器与 Experian 集成，以在注册流中验证用户标识。 |


### <a name="other"></a>其他

| 示例                                                                                                            | 说明                                                                                                                          |
| ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| [邀请码](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-invitation-code) | 此示例演示如何使用邀请码限制特定访问群体注册。|
| [API 连接器社区示例](https://github.com/azure-ad-b2c/api-connector-samples) | 此存储库包含由 API 连接器启用的社区维护的方案示例。|
