---
title: 安全令牌 | Azure
titleSuffix: Microsoft identity platform
description: 了解 Microsoft 标识平台中的安全令牌的基础知识。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/27/2021
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 2567a75c0accd7e2bb932d8578e1a225518f5ce3
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2021
ms.locfileid: "129092749"
---
# <a name="security-tokens"></a>安全令牌

对于用户位于全球各地且用户不一定从企业网络登录的应用而言，集中式标识提供者尤其有用。 Microsoft 标识平台会对用户进行身份验证，并提供安全令牌，如[访问令牌](developer-glossary.md#access-token)、[刷新令牌](developer-glossary.md#refresh-token)和 [ID 令牌](developer-glossary.md#id-token)。 使用安全令牌，[客户端应用程序](developer-glossary.md#client-application)可以访问[资源服务器](developer-glossary.md#resource-server)上受保护的资源。

**访问令牌**：访问令牌是由 [授权服务器](developer-glossary.md#authorization-server)作为 [OAuth 2.0](active-directory-v2-protocols.md) 流的一部分颁发的安全令牌。 它包含有关令牌所针对的用户和资源的信息。 这些信息可用于访问 Web API 和其他受保护的资源。 访问令牌由资源进行验证，以授予对客户端应用的访问权限。 若要详细了解 Microsoft 标识平台如何颁发访问令牌，请参阅[访问令牌](access-tokens.md)。

**刷新令牌**：由于访问令牌只在短时间内有效，因此授权服务器有时会在颁发访问令牌的同时颁发“刷新令牌”。 然后，客户端应用程序可以在需要时使用此刷新令牌交换新的访问令牌。 若要详细了解 Microsoft 标识平台如何使用刷新令牌来撤销权限，请参阅[刷新令牌](refresh-tokens.md)。

**ID 令牌**：ID 令牌作为 [OpenID Connect](v2-protocols-oidc.md) 流的一部分发送到客户端应用程序。 它们可以与访问令牌一起发送，也可以代替访问令牌发送。 客户端使用 ID 令牌对用户进行身份验证。 若要详细了解 Microsoft 标识平台如何颁发 ID 令牌，请参阅 [ID 令牌](id-tokens.md)。

> [!NOTE]
> 本文讨论 OAuth2 和 OpenID Connect 协议使用的安全令牌。 许多企业应用程序使用 SAML 对用户进行身份验证。 有关 SAML 断言的信息，请参阅 [Azure Active Directory SAML 令牌参考](reference-saml-tokens.md)。

## <a name="validate-security-tokens"></a>验证安全令牌

由为其生成了令牌的应用、已让用户登录的 Web 应用或所调用的 Web API 负责验证令牌。 令牌由授权服务器使用私钥签名。 授权服务器发布相应的公钥。 若要验证令牌，应用需使用授权服务器公钥验证签名，以验证签名是使用私钥创建的。

令牌仅在有限的时间内有效。 通常，授权服务器提供一对令牌，如：

* 用于访问应用程序或受保护资源的访问令牌。
* 用于在访问令牌即将过期时刷新访问令牌的刷新令牌。

访问令牌作为 `Authorization` 标头中的持有者令牌传递给 Web API。 应用可以向授权服务器提供刷新令牌。 如果未撤消用户对应用的访问权限，它将获得一个新的访问令牌和一个新的刷新令牌。 用户离职的场景就是这样处理的。 当授权服务器收到刷新令牌时，如果用户不再获得授权，则授权服务器不会颁发另一个有效的访问令牌。

## <a name="json-web-tokens-and-claims"></a>JSON Web 令牌和声明

Microsoft 标识平台将安全令牌实现为包含声明的 JSON Web 令牌 (JWT)。 由于 JWT 用作安全令牌，这种形式的身份验证有时称为“JWT 身份验证”。

[声明](developer-glossary.md#claim)将有关某个实体（例如客户端应用程序或[资源所有者](developer-glossary.md#resource-owner)）的断言提供给另一个实体（例如资源服务器）。 声明也可以称为 JWT 声明或 JSON Web 令牌声明。

声明是对令牌主体相关事实进行中继的名称或值对。 例如，声明可能包含由授权服务器进行身份验证的安全主体的相关事实。 特定令牌中提供的声明取决于许多事项，例如令牌类型、用于对使用者进行身份验证的凭据类型，以及应用程序配置。

应用程序可以使用声明来完成各种任务，例如：

* 验证令牌。
* 标识令牌使用者的[租户](developer-glossary.md#tenant)。
* 显示用户信息。
* 确定使用者的授权。

声明由提供如下信息的键值对组成：

* 生成令牌的安全令牌服务器。
* 令牌生成日期。
* 使用者，例如用户（守护程序除外）。
* 受众，即为其生成了令牌的应用。
* 请求了令牌的应用（客户端）。 对于 Web 应用，该应用可能与受众相同。

若要详细了解 Microsoft 标识平台如何实现令牌和声明信息，请参阅[访问令牌](access-tokens.md)和 [ID 令牌](id-tokens.md)。

## <a name="how-each-flow-emits-tokens-and-codes"></a>每个流如何发出令牌和代码

根据客户端的生成方式，客户端可以使用 Microsoft 标识平台支持的一种（或几种）身份验证流。 这些流可以生成各种令牌（ID 令牌、刷新令牌、访问令牌）和授权代码。 它们需要不同的令牌才能工作。 此表提供了概述。

|流向 | 需要 | ID 令牌 | 访问令牌 | 刷新令牌 | 授权代码 |
|-----|----------|----------|--------------|---------------|--------------------|
|[授权代码流](v2-oauth2-auth-code-flow.md) | | x | x | x | x|
|[隐式流](v2-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[混合 OIDC 流](v2-protocols-oidc.md#protocol-diagram-access-token-acquisition)| | x  | |          |            x   |
|[刷新令牌兑换](v2-oauth2-auth-code-flow.md#refresh-the-access-token) | 刷新令牌 | x | x | x| |
|[代理流](v2-oauth2-on-behalf-of-flow.md) | 访问令牌| x| x| x| |
|[客户端凭据](v2-oauth2-client-creds-grant-flow.md) | | | x（仅限应用）| | |

通过隐式模式颁发的令牌由于通过 URL 传回浏览器而具有长度限制，其中 `response_mode` 是 `query` 或 `fragment`。 有些浏览器对可以放在浏览器栏中的 URL 的大小有限制，当 URL 太长时会失败。 因此，这些令牌没有 `groups` 或 `wids` 声明。

## <a name="next-steps"></a>后续步骤

有关 Microsoft 标识平台中的身份验证和授权的详细信息，请参阅以下文章：

* 若要了解身份验证和授权的基本概念，请参阅[身份验证与授权](authentication-vs-authorization.md)。
* 若要了解如何注册应用程序以进行集成，请参阅[应用程序模型](application-model.md)。
* 若要了解 Web、桌面和移动应用的登录流，请参阅[应用登录流](app-sign-in-flow.md)。
