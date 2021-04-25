---
title: Microsoft 标识平台身份验证库 | Azure
description: 与 Microsoft 标识平台兼容的客户端库和中间件的列表。 使用这些库可添加对用户登录（身份验证）和对应用程序的受保护的 Web API 访问（授权）的支持。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 03/30/2021
ms.author: marsma
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.openlocfilehash: 321a5c473df30dd6f00bbcd1294d48ce8da34009
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2021
ms.locfileid: "106060374"
---
# <a name="microsoft-identity-platform-authentication-libraries"></a>Microsoft 标识平台身份验证库

下表显示了针对多种应用程序类型的 Microsoft 身份验证库支持。 它们包括指向库源代码的链接，获取应用项目包的位置以及库是否支持用户登录（身份验证），访问受保护的 Web API（授权）或两者兼而有之。

Microsoft 标识平台已由 OpenID Foundation 认证为[经认证的 OpenID 提供程序](https://openid.net/certification/)。 如果你希望使用 Microsoft 身份验证库 (MSAL) 以外的其他库或 Microsoft 支持的其他库，请选择具有[经认证的 OpenID Connect 实现](https://openid.net/developers/certified/)的库。

如果选择手动编码自己的 [OAuth 2.0 或 OpenID Connect 1.0](active-directory-v2-protocols.md) 的协议级实现，请密切注意每个标准规范中的安全性注意事项，并遵循软件开发生命周期 (SDL) 方法，例如 [Microsoft SDL][Microsoft-SDL]。

## <a name="single-page-application-spa"></a>单页面应用程序 (SPA)

单页应用程序完全在浏览器中运行，并动态提取或在应用程序加载时提取页面数据（HTML、CSS 和 JavaScript）。 它可以调用 Web API 与后端数据源进行交互。

因为 SPA 的代码完全在浏览器中运行，所以它被视为公共客户端，无法安全存储机密。

[!INCLUDE [active-directory-develop-libraries-spa](../../../includes/active-directory-develop-libraries-spa.md)]

## <a name="web-application"></a>Web 应用程序

Web 应用程序在服务器上运行代码，该服务器生成 HTML、CSS 和 JavaScript 并将其发送到用户的 Web 浏览器以进行呈现。 系统将用户标识作为用户浏览器（前端）和 Web 服务器（后端）之间的会话来进行维护。

由于 Web 应用程序的代码在 Web 服务器上运行，因此它被视为可以安全存储机密的机密客户端。

[!INCLUDE [active-directory-develop-libraries-webapp](../../../includes/active-directory-develop-libraries-webapp.md)]

## <a name="desktop-application"></a>桌面应用程序

桌面应用程序通常是二进制（编译的）代码，它显示用户界面，并设计为在用户的桌面上运行。

由于桌面应用程序在用户的桌面上运行，因此它被视为无法安全存储机密的公共客户端。

[!INCLUDE [active-directory-develop-libraries-desktop](../../../includes/active-directory-develop-libraries-desktop.md)]

## <a name="mobile-application"></a>移动应用程序

移动应用程序通常是二进制（编译的）代码，它显示用户界面，并设计为在用户的移动设备上运行。

由于移动应用程序在用户的移动设备上运行，因此它被视为无法安全存储机密的公共客户端。

[!INCLUDE [active-directory-develop-libraries-mobile](../../../includes/active-directory-develop-libraries-mobile.md)]

## <a name="service--daemon"></a>服务/守护程序

服务和守护程序通常用于服务器到服务器通信以及其他无人参与（有时称为无外设）的通信。 因为没有用户在键盘上输入凭据或同意访问资源，所以在请求对 Web API 资源的授权访问时，这些应用程序将以自身而非用户身份进行身份验证。

在服务器上运行的服务或守护程序被视为可以安全存储其机密的机密客户端。

[!INCLUDE [active-directory-develop-libraries-daemon](../../../includes/active-directory-develop-libraries-daemon.md)]

## <a name="next-steps"></a>后续步骤

有关 Microsoft 身份验证库的详细信息，请参阅 [Microsoft 身份验证库 (MSAL) 概述](msal-overview.md)。

<!--Image references-->
[y]: ./media/common/yes.png
[n]: ./media/common/no.png

<!--Reference-style links -->
[AAD-App-Model-V2-Overview]: v2-overview.md
[Microsoft-SDL]: https://www.microsoft.com/securityengineering/sdl/
[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
