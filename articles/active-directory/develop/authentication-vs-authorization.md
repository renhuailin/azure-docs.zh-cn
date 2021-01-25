---
title: 身份验证和授权 | Azure
titleSuffix: Microsoft identity platform
description: 了解 Microsoft 标识平台中身份验证和授权的基本知识。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: b81b34010736bce33085cb1ebf0faa3da6a41bd6
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/25/2021
ms.locfileid: "98755389"
---
# <a name="authentication-vs-authorization"></a>身份验证和授权

本文介绍身份验证和授权。 它还简要介绍了如何使用 Microsoft 标识平台在 web 应用、web Api 或调用受保护的 web Api 的应用程序中对用户进行身份验证和授权。 如果你看到不熟悉的术语，请尝试我们的 [术语表](developer-glossary.md) 或 [Microsoft 标识平台视频](identity-videos.md)，其中介绍了基本概念。

## <a name="authentication"></a>身份验证

*身份验证* 是证明您是您所说的。 有时会缩短为 *身份验证*。 Microsoft 标识平台使用 [OpenID connect](https://openid.net/connect/) 协议来处理身份验证。

## <a name="authorization"></a>授权

授权是指向经过身份验证的参与方授予执行某项操作的权限的操作。 它指定了你可访问的数据以及可使用该数据执行的操作。 授权有时会缩短为 *授权。* Microsoft 标识平台使用 [OAuth 2.0](https://oauth.net/2/) 协议处理授权。

## <a name="authentication-and-authorization-using-the-microsoft-identity-platform"></a>使用 Microsoft 标识平台进行身份验证和授权

当你需要在多个应用中添加或删除用户时，创建每个应用程序，每个应用程序都维护其自己的用户名和密码信息。 相反，你的应用程序可以将该责任委托给一个集中的标识提供者。

Azure Active Directory (Azure AD) 就是云中的一个集中标识提供程序。 将身份验证和授权委派给它可以实现如下方案：

- 要求用户位于特定位置的条件性访问策略。
- 使用 [多重身份验证](../authentication/concept-mfa-howitworks.md)，有时称为双因素身份验证或2FA。
- 使用户能够登录一次，然后自动登录到共享同一集中目录的所有 web 应用。 此功能称为 *(SSO) 的单一登录*。

Microsoft 标识平台通过提供标识即服务简化了应用程序开发人员的授权和身份验证。 它支持行业标准协议和不同平台的开源库，以帮助你快速开始编码。 它允许开发人员生成应用程序，这些应用程序可以登录所有 Microsoft 标识，获取用于调用 [Microsoft Graph](https://developer.microsoft.com/graph/)、访问 Microsoft api 或访问开发人员构建的其他 api 的令牌。

此视频介绍了 Microsoft 标识平台和新式身份验证的基础知识： 

> [!VIDEO https://www.youtube.com/embed/tkQJSHFsduY]

下面是 Microsoft 标识平台使用的协议的比较：

* **Oauth 与 Openid connect**：平台使用 oauth 进行授权，并使用 OpenID CONNECT (OIDC) 进行身份验证。 OpenID Connect 构建在 OAuth 2.0 的基础之上，因此两者的术语和流很相似。 甚至可以通过 OpenID Connect) 对用户 (进行身份验证，并获取授权，以访问用户拥有的受保护资源 (通过 OAuth 2.0) 请求。 有关详细信息，请参阅 [OAuth 2.0 和 OpenID Connect 协议](active-directory-v2-protocols.md)和 [OpenID Connect 协议](v2-protocols-oidc.md)。
* **Oauth 与 SAML**：平台使用 OAuth 2.0 进行授权，使用 saml 进行身份验证。 若要详细了解如何将这些协议一起使用来对用户进行身份验证并获得授权来访问受保护的资源，请参阅 [Microsoft 标识平台和 OAuth 2.0 SAML 持有者断言流](./scenario-token-exchange-saml-oauth.md)。
* **Openid connect 与 saml**：平台使用 OpenID CONNECT 和 saml 对用户进行身份验证，并启用单一登录。 SAML 身份验证通常用于标识提供者（如 Active Directory 联合身份验证服务 (AD FS) 联合到 Azure AD），因此通常用于企业应用程序。 OpenID Connect 通常用于纯粹位于云中的应用程序，例如移动应用程序、网站和 web Api。

## <a name="next-steps"></a>后续步骤

有关涉及身份验证和授权的其他主题，请执行以下操作：

* 若要了解如何在授权和身份验证中使用访问令牌、刷新令牌和 ID 令牌，请参阅 [安全令牌](security-tokens.md)。
* 若要了解注册应用程序以使其能够与 Microsoft 标识平台集成的过程，请参阅 [应用程序模型](application-model.md)。