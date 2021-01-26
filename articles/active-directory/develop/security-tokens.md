---
title: 安全令牌 | Azure
titleSuffix: Microsoft identity platform
description: 了解 Microsoft 标识平台中安全令牌的基本知识。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/11/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 6d9f5538d377be1414089e591559344bde4f381a
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/26/2021
ms.locfileid: "98795647"
---
# <a name="security-tokens"></a>安全令牌

集中式标识提供者特别适用于在全球各地有用户登录不一定从企业网络登录的应用。 Microsoft 标识平台会对用户进行身份验证，并提供安全令牌，如 [访问令牌](developer-glossary.md#access-token)、 [刷新令牌](developer-glossary.md#refresh-token)和 [ID 令牌](developer-glossary.md#id-token)。 使用安全令牌， [客户端应用程序](developer-glossary.md#client-application) 可以访问 [资源服务器](developer-glossary.md#resource-server)上受保护的资源。

**访问令牌**：访问令牌是 [授权服务器](developer-glossary.md#authorization-server) 颁发的一种安全令牌，作为 [OAuth 2.0](active-directory-v2-protocols.md) 流的一部分。 它包含有关用户的信息，以及令牌的目标资源。 此信息可用于访问 web Api 和其他受保护的资源。 访问令牌由资源进行验证，以授予对客户端应用的访问权限。 若要详细了解 Microsoft 标识平台如何发出访问令牌，请参阅 [访问令牌](access-tokens.md)。

**刷新令牌**：由于访问令牌仅在一小段时间内有效，因此，在发出访问令牌时，授权服务器有时会发出刷新令牌。 然后，客户端应用程序可以在需要时使用此刷新令牌交换新的访问令牌。 若要详细了解 Microsoft 标识平台如何使用刷新令牌来废除权限，请参阅 [令牌吊销](access-tokens.md#token-revocation)。

**Id 令牌**： id 令牌作为 [OpenID connect](v2-protocols-oidc.md) 流的一部分发送到客户端应用程序。 它们可以与或而不是访问令牌一起发送。 客户端使用 ID 令牌对用户进行身份验证。 若要了解有关 Microsoft 标识平台如何颁发 ID 令牌的详细信息，请参阅 [id 令牌](id-tokens.md)。

> [!NOTE]
> 本文讨论 OAuth2 和 OpenID Connect 协议使用的安全令牌。 许多企业应用程序使用 SAML 对用户进行身份验证。 有关 SAML 断言的信息，请参阅 [AZURE ACTIVE DIRECTORY saml 令牌参考](reference-saml-tokens.md)。

## <a name="validate-security-tokens"></a>验证安全令牌

这取决于为其生成令牌的应用、登录用户的 web 应用或用于验证令牌的 web API。 授权服务器使用私钥对令牌进行签名。 授权服务器发布相应的公钥。 若要验证令牌，应用程序将使用授权服务器公钥来验证签名是否是使用私钥创建的。

令牌仅在有限的时间内有效。 通常，授权服务器提供一对令牌，如：

* 访问令牌，用于访问应用程序或受保护的资源。
* 刷新标记，用于在访问令牌接近过期时刷新访问令牌。

访问令牌作为 `Authorization` 标头中的持有者令牌传递给 Web API。 应用可以向授权服务器提供刷新令牌。 如果用户对该应用程序的访问未被吊销，它将返回一个新的访问令牌和一个新的刷新令牌。 用户离职的场景就是这样处理的。 当授权服务器收到刷新令牌时，如果用户不再获得授权，则不会发出其他有效的访问令牌。

## <a name="json-web-tokens-and-claims"></a>JSON Web 令牌和声明

Microsoft 标识平台将安全令牌作为 JSON Web 令牌实现 (包含 *声明* 的 jwt) 。 由于 JWT 用作安全令牌，这种形式的身份验证有时称为“JWT 身份验证”。

[声明](developer-glossary.md#claim)将有关某个实体（例如客户端应用程序或[资源所有者](developer-glossary.md#resource-owner)）的断言提供给另一个实体（例如资源服务器）。 声明也可能称为 JWT 声明或 JSON Web 令牌声明。

声明是中继令牌主体相关事实的名称或值对。 例如，声明可能包含有关授权服务器进行身份验证的安全主体的事实。 特定令牌中存在的声明取决于许多因素，例如令牌类型、用于验证主体身份的凭据类型和应用程序配置。

应用程序可以对各种任务使用声明，如：

* 验证令牌。
* 标识令牌使用者的 [租户](developer-glossary.md#tenant)。
* 显示用户信息。
* 确定使用者的授权。

声明由提供如下信息的键值对组成：

* 生成令牌的安全令牌服务器。
* 生成令牌的日期。
* Subject (例如用户--守护程序) 除外。
* 受众，这是为其生成令牌的应用。
* 请求了令牌的应用（客户端）。 对于 web 应用，此应用可能与受众相同。

若要了解有关 Microsoft 标识平台如何实现令牌和声明信息的详细信息，请参阅 [访问令牌](access-tokens.md) 和 [ID 令牌](id-tokens.md)。

## <a name="how-each-flow-emits-tokens-and-codes"></a>每个流如何发出令牌和代码

根据客户端的生成方式，客户端可以使用 Microsoft 标识平台支持的一种（或几种）身份验证流。 这些流可以生成各种令牌 (ID 令牌、刷新令牌、访问令牌) 和授权代码。 它们需要不同的令牌才能工作。 此表提供了概述。

|流向 | 需要 | ID 令牌 | 访问令牌 | 刷新令牌 | 授权代码 |
|-----|----------|----------|--------------|---------------|--------------------|
|[授权代码流](v2-oauth2-auth-code-flow.md) | | x | x | x | x|
|[隐式流](v2-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[混合 OIDC 流](v2-protocols-oidc.md#protocol-diagram-access-token-acquisition)| | x  | |          |            x   |
|[刷新令牌兑换](v2-oauth2-auth-code-flow.md#refresh-the-access-token) | 刷新令牌 | x | x | x| |
|[代理流](v2-oauth2-on-behalf-of-flow.md) | 访问令牌| x| x| x| |
|[客户端凭据](v2-oauth2-client-creds-grant-flow.md) | | | x (仅限应用) | | |

通过隐式模式颁发的令牌具有长度限制，因为它们通过 URL 传递回浏览器，其中 `response_mode` 是 `query` 或 `fragment` 。 某些浏览器对可放置在浏览器栏中的 URL 大小有限制，但如果过长，则会失败。 因此，这些令牌没有 `groups` 或 `wids` 声明。

## <a name="next-steps"></a>后续步骤

有关 Microsoft 标识平台中的身份验证和授权的详细信息，请参阅以下文章：

* 若要了解身份验证和授权的基本概念，请参阅 [身份验证与授权](authentication-vs-authorization.md)。
* 若要了解如何注册应用程序以进行集成，请参阅 [应用程序模型](application-model.md)。
* 若要了解 web、桌面和移动应用的登录流，请参阅 [应用登录流](app-sign-in-flow.md)。
