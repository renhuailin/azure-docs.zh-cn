---
title: Microsoft 标识平台令牌交换方案，其中包含 SAML 和 OIDC/OAuth in Azure Active Directory
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
ms.openlocfilehash: 92d0dad86b3f048eb96dd7b17ed09f6e20d7cde2
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2021
ms.locfileid: "98063291"
---
# <a name="microsoft-identity-platform-token-exchange-scenarios-with-saml-and-oidcoauth"></a>具有 SAML 和 OIDC/OAuth 的 Microsoft 标识平台令牌交换方案

SAML 和 OpenID Connect (OIDC) /OAuth 是常用协议，用于实现 (SSO) 的单一 Sign-On。 某些应用可能仅实现 SAML，而其他应用可能仅实现 OIDC/OAuth。 这两个协议都使用令牌来传达机密。 若要了解有关 SAML 的详细信息，请参阅 [单一 Sign-On SAML 协议](single-sign-on-saml-protocol.md)。 若要了解有关 OIDC/OAuth 的详细信息，请参阅 [Microsoft 标识平台上的 OAuth 2.0 和 OpenID connect 协议](active-directory-v2-protocols.md)。

本文概述了应用程序实现 SAML 但需要调入图形 API 的常见方案，此方案使用 OIDC/OAuth。 为使用此方案的人员提供基本指导。

## <a name="scenario-you-have-a-saml-token-and-want-to-call-the-graph-api"></a>方案：你具有 SAML 令牌，并且想要调用图形 API
许多应用都是通过 SAML 实现的。 但图形 API 使用 OIDC/OAuth 协议。 将 OIDC/OAuth 功能添加到 SAML 应用程序是可能的，但这种方法并不简单。 在应用中提供 OAuth 功能后，可以使用图形 API。

常规策略是将 OIDC/OAuth 堆栈添加到应用。 使用可实现这两种标准的应用，可以使用会话 cookie。 不会显式交换令牌。 使用 SAML 登录用户时，会生成会话 cookie。 当图形 API 调用 OAuth 流时，将使用会话 cookie 进行身份验证。 此策略假定条件访问检查通过，并且用户已获得授权。

> [!NOTE]
> 用于添加 OIDC/OAuth 行为的建议库是 (MSAL) 的 Microsoft 身份验证库。 若要了解有关 MSAL 的详细信息，请参阅 [Microsoft 身份验证库概述 (MSAL) ](msal-overview.md)。  (ADAL) Active Directory 身份验证库调用了以前的库，但不建议将其替换为 MSAL。

## <a name="next-steps"></a>后续步骤
- [身份验证流和应用程序方案](authentication-flows-app-scenarios.md)
