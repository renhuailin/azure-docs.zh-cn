---
title: 了解 MSAL | Azure
titleSuffix: Microsoft identity platform
description: 借助 Microsoft 身份验证库 (MSAL)，应用程序开发人员可以获取令牌，以调用受保护的 Web API。 这些 Web API 可以是 Microsoft Graph、其他 Microsoft API、第三方 Web API 或你自己的 Web API。 MSAL 支持多个应用程序体系结构和平台。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/22/2021
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev, identityplatformtop40, has-adal-ref
ms.openlocfilehash: 405843ad0d56a75dac20dc2a6c85d8bc15600312
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2021
ms.locfileid: "122429101"
---
# <a name="overview-of-the-microsoft-authentication-library-msal"></a>Microsoft 身份验证库 (MSAL) 的概述
借助 Microsoft 身份验证库 (MSAL)，开发人员能够从 Microsoft 标识平台获取[令牌](developer-glossary.md#security-token)，以便对用户进行身份验证并访问受保护的 Web API。 它可用于提供对 Microsoft Graph、其他 Microsoft API、第三方 Web API 或你自己的 Web API 的安全访问。 MSAL 支持许多不同的应用程序体系结构和平台，包括 .NET、JavaScript、Java、Python、Android 和 iOS。

MSAL 为你提供了许多获取令牌的方法，将一致的 API 用于许多平台。 使用 MSAL 具有以下好处：

* 无需直接在应用程序中对协议使用 OAuth 库或代码。
* 代表用户或代表应用程序获取令牌（如果适用于平台）。
* 维护令牌缓存，并在即将过期时为你刷新令牌。 你不需要自行处理令牌过期。
* 帮助你指定你希望应用程序登录的受众（你的组织、一系列组织、工作和学校以及 Microsoft 个人帐户、Azure AD B2C 的社交身份、主权云和国家云中的用户）。
* 可帮助你通过配置文件设置应用程序。
* 可显示可操作异常、日志和遥测，从而帮助你对应用进行故障排除。

> [!VIDEO https://www.youtube.com/embed/zufQ0QRUHUk]

## <a name="application-types-and-scenarios"></a>应用程序类型和方案
通过使用 MSAL，可从许多应用程序类型获取令牌：Web 应用程序、Web API、单页应用 (JavaScript)、移动和本机应用程序，以及守护程序和服务器端应用程序。

可以在许多应用程序方案中使用 MSAL，包含以下方案：

* [单页应用程序 (JavaScript)](scenario-spa-overview.md)
* [Web 应用登录用户](scenario-web-app-sign-user-overview.md)
* [Web 应用程序登录用户并代表用户调用 web API](scenario-web-app-call-api-overview.md)
* [保护 Web API，因此仅通过身份验证的用户可以访问它](scenario-protected-web-api-overview.md)
* [Web API 代表已登录用户调用其他下游 Web API](scenario-web-api-call-api-overview.md)
* [桌面应用程序代表已登录用户调用 Web API](scenario-desktop-overview.md)
* [代表以交互方式登录的用户调用 Web API 的移动应用程序](scenario-mobile-overview.md)。
* [桌面/服务守护程序应用程序代表自己调用 Web API](scenario-daemon-overview.md)

## <a name="languages-and-frameworks"></a>语言和框架

| 库 | 支持的平台和框架|
| --- | --- |
| [适用于 Android 的 MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-android)|Android|
| [MSAL Angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angular)| 采用 Angular 和 Angular.js 框架的单页应用|
| [适用于 iOS 和 macOS 的 MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-objc)|iOS 和 macOS|
| [MSAL Go（预览版）](https://github.com/AzureAD/microsoft-authentication-library-for-go)|Windows、macOS、Linux|
| [MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java)|Windows、macOS、Linux|
| [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)| JavaScript/TypeScript 框架，例如 Vue.js、Ember.js 或 Durandal.js|
| [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)| .NET Framework、.NET Core、Xamarin Android、Xamarin iOS、通用 Windows 平台|
| [MSAL Node](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node)|Express 的 Web 应用、Electron 的桌面应用、跨平台控制台应用|
| [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)|Windows、macOS、Linux|
| [MSAL React](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-react)| 采用 React 和基于 React 的库（Next.js、Gatsby.js）的单页应用|

## <a name="migrate-apps-that-use-adal-to-msal"></a>将使用 ADAL 的应用迁移到 MSAL

Active Directory 身份验证库 (ADAL) 与适用于开发人员的 Azure AD (v1.0) 终结点集成，其中 MSAL 与 Microsoft 标识平台集成。 V1.0 终结点支持工作帐户，但不支持个人帐户。 V2.0 终结点将 Microsoft 个人帐户和工作帐户统一到单个身份验证系统。 此外，借助 MSAL，还可以获取 Azure AD B2C 的身份验证。

有关如何迁移到 MSAL 的详细信息，请参阅[将应用程序迁移到 Microsoft 身份验证库 (MSAL)](msal-migration.md)。
