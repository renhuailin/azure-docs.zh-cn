---
title: JavaScript 单页应用程序方案
titleSuffix: Microsoft identity platform
description: 了解如何使用 Microsoft 标识平台构建单页应用程序（方案概述）。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: marsma
ms.custom: aaddev, identityplatformtop40, devx-track-js
ms.openlocfilehash: 270b3e3f4c5e462b66129413d7acf983efd3f8eb
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2021
ms.locfileid: "113357618"
---
# <a name="scenario-single-page-application"></a>方案：单页应用程序

了解生成单页应用程序 (SPA) 所需的一切。

## <a name="getting-started"></a>入门

如果尚未创建你的首个应用，请通过完成 Javascript SPA 快速入门创建：

[快速入门：单页应用程序](./quickstart-v2-javascript-auth-code.md)

## <a name="overview"></a>概述

许多新式 Web 应用程序都是作为客户端单页应用程序构建的。 开发人员使用 JavaScript 或 SPA 框架（例如 Angular、Vue 和 React）来编写它们。 这些应用程序在 Web 浏览器上运行，与传统的服务器端 Web 应用程序相比，它们具有不同的身份验证特征。

Microsoft 标识平台提供了 **两个** 选项来允许单页应用程序登录用户并获取用于访问后端服务或 Web API 的令牌。

- [OAuth 2.0 授权代码流（使用 PKCE）](./v2-oauth2-auth-code-flow.md)。 授权代码流允许应用程序用授权代码来交换 **ID** 令牌（表示已经过身份验证的用户），以及交换调用受保护 API 所需的 **访问** 令牌。 此外，它还返回 **刷新** 令牌，这类令牌提供以用户身份长期访问资源而无需与这些用户交互的权限。 这是 **建议的** 做法。

![单页应用程序 - 授权](./media/scenarios/spa-app-auth.svg)

- [OAuth 2.0 隐式流](./v2-oauth2-implicit-grant-flow.md)。 隐式授权流允许应用程序获取 **ID** 和 **访问** 令牌。 与授权代码流不同，隐式授权流不会返回 **刷新令牌**。

![单页应用程序 - 隐式](./media/scenarios/spa-app.svg)

此身份验证流不包括使用 Electron 和 React-Native 之类的跨平台 JavaScript 框架的应用程序方案。 它们需要使用其他功能才能与本机平台交互。

## <a name="specifics"></a>详情

若要为应用程序启用此方案，需要：

* 通过 Azure Active Directory (Azure AD) 进行应用程序注册。 在隐式授权流和授权代码流的注册步骤不同。
* 带有注册的应用程序属性（例如应用程序 ID）的应用程序配置。
* 使用适用于 JavaScript 的 Microsoft 身份验证库 (MSAL.js) 执行身份验证流以登录并获取令牌。

## <a name="recommended-reading"></a>推荐阅读内容

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>后续步骤

转到此方案中的下一篇文章，[应用注册](scenario-spa-app-registration.md)。
