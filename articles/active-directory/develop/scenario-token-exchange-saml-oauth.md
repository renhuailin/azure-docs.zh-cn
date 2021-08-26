---
title: 在 Azure Active Directory 中使用 SAML 和 OIDC/OAuth 的Microsoft 标识平台令牌交换方案
description: 了解在 Azure Active Directory 中使用 SAML 和 OIDC/OAuth 时的常见令牌交换方案。
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 12/08/2020
ms.author: kenwith
ms.reviewer: paulgarn
ms.custom: has-adal-ref
ms.openlocfilehash: 527c9bfac3df50d012f3b0a805d0ac4ec1ea9067
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2021
ms.locfileid: "122428959"
---
# <a name="microsoft-identity-platform-token-exchange-scenarios-with-saml-and-oidcoauth"></a>采用 SAML 和 OIDC/OAuth 的 Microsoft 标识平台令牌交换方案

SAML 和 OpenID Connect (OIDC)/OAuth 都是常用来实现单一登录 (SSO) 的协议。 有些应用可能仅实现 SAML，而其他应用可能仅实现 OIDC/OAuth。 这两种协议都使用令牌来传达密钥。 有关 SAML 的详细信息，请参阅[单一登录 SAML 协议](single-sign-on-saml-protocol.md)。 有关 OIDC/OAuth 的详细信息，请参阅 [Microsoft 标识平台上的 OAuth 2.0 和 OpenID Connect 协议](active-directory-v2-protocols.md)。

本文概述应用实现 SAML 但调用使用 OIDC/OAuth 的图形 API 的常见方案。 我们为使用此方案的人员提供基本指导。

## <a name="scenario-you-have-a-saml-token-and-want-to-call-the-graph-api"></a>方案：你具有 SAML 令牌，并且想要调用图形 API
许多应用都通过 SAML 实现。 但图形 API 使用 OIDC/OAuth 协议。 可以将 OIDC/OAuth 功能添加到 SAML 应用，但这种方法并不简单。 当 OAuth 功能在应用中可用后，则可以使用图形 API。

一般策略是将 OIDC/OAuth 堆栈添加到应用中。 由于应用实现这两种标准，所以可以使用会话 cookie。 不会显式交换令牌。 你将使用 SAML 登录用户，进而生成一个会话 cookie。 当图形 API 调用 OAuth 流时，可使用会话 cookie 进行身份验证。 这种策略假定已通过条件访问检查，并且用户已获得授权。

> [!NOTE]
> 建议使用 Microsoft 身份验证库 (MSAL) 来添加 OIDC/OAuth 行为。 有关 MSAL 的详细信息，请参阅 [Microsoft 身份验证库 (MSAL) 概述](msal-overview.md)。 上一个库被称为 Active Directory 身份验证库 (ADAL)，但不建议使用它作为 MSAL 进行替换。

## <a name="next-steps"></a>后续步骤
- [身份验证流和应用程序方案](authentication-flows-app-scenarios.md)
