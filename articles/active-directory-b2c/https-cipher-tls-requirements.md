---
title: TLS 和密码套件要求 - Azure AD B2C
titleSuffix: Azure AD B2C
description: 面向开发人员的有关与 Web API 终结点交互时的 HTTPS 密码套件和 TLS 要求的说明。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/30/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3732f53c3a4e77a1a10363cb53d898e6edc661db
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111960427"
---
# <a name="azure-active-directory-b2c-tls-and-cipher-suite-requirements"></a>Azure Active Directory B2C 的 TLS 和密码套件要求

Azure Active Directory B2C (Azure AD B2C) 通过[用户流](user-flow-overview.md)中的 [API 连接器](api-connectors-overview.md)和[标识提供者](oauth2-technical-profile.md)连接到终结点。 本文讨论了针对终结点的 TLS 和密码套件要求。

配置了 API 连接器和标识提供者的终结点必须发布到可公开访问的 HTTPS URI。 在使用终结点建立安全连接之前，系统会根据该连接的两端的功能在 Azure AD B2C 和终结点之间协商协议和密码。

Azure AD B2C 必须能够使用传输层安全性 (TLS) 和密码套件连接到终结点，如本文所述。

## <a name="tls-versions"></a>TLS 版本

TLS 1.2 版是一种在服务器和客户端之间提供身份验证和数据加密的加密协议。 终结点必须支持基于 TLS 1.2 版的安全通信。 较旧的 TLS 1.0 版和 1.1 版已弃用。 

## <a name="cipher-suites"></a>密码套件

密码套件是加密算法的集合。 它们提供有关如何在通过 TLS 使用 HTTPS 协议时安全地进行数据通信的基本信息。

终结点必须至少支持下面的一种密码：

- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384

## <a name="endpoints-in-scope"></a>作用域中的终结点

Azure AD B2C 环境中使用的以下终结点必须符合本文所述要求：

- [API 连接器](api-connectors-overview.md) 
- OAuth1
    - 令牌终结点 
    - 用户信息终结点
- OAuth2 和 OpenID Connect 标识提供者
    - OpenID Connect 发现终结点
    - OpenID Connect JWKS 终结点
    - 令牌终结点 
    - 用户信息终结点
- [ID 令牌提示](id-token-hint.md)
    - OpenID Connect 发现终结点
    - OpenID Connect JWKS 终结点
- [SAML 标识提供者](saml-service-provider.md)元数据终结点
- [SAML 服务提供程序](identity-provider-generic-saml.md)元数据终结点

## <a name="check-your-endpoint-compatibility"></a>检查终结点兼容性

若要验证终结点是否符合本文所述要求，请使用 TLS 密码和扫描程序工具执行测试。 使用 [SSLLABS](https://www.ssllabs.com/ssltest/analyze.html) 测试终结点。


## <a name="next-steps"></a>后续步骤

另请参阅以下文章：

- [排查不支持 TLS 1.2 的应用程序的问题](../cloud-services/applications-dont-support-tls-1-2.md)
- [TLS/SSL (Schannel SSP) 中的密码套件](/windows/win32/secauthn/cipher-suites-in-schannel)
- [如何启用 TLS 1.2](/mem/configmgr/core/plan-design/security/enable-tls-1-2)
- [解决 TLS 1.0 问题](/security/engineering/solving-tls1-problem)