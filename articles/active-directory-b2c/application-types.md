---
title: Azure AD B2C 支持的应用程序类型
titleSuffix: Azure AD B2C
description: 了解可与 Azure Active Directory B2C 配合使用的应用程序类型。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/17/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6e4f54f736a47cc6b351ff02b8fb7f8bad1c5c56
ms.sourcegitcommit: f0168d80eb396ce27032aa02fe9da5a0c10b5af3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2021
ms.locfileid: "112552687"
---
# <a name="application-types-that-can-be-used-in-active-directory-b2c"></a>可在 Azure Active Directory B2C 中使用的应用程序类型
 
Azure Active Directory B2C (Azure AD B2C) 支持各种新式应用程序体系结构的身份验证。 所有这些体系结构都以行业标准协议 [OAuth 2.0](protocols-overview.md) 或 [OpenID Connect](protocols-overview.md) 为基础。 本文介绍可独立于首选语言或平台构建的应用程序类型。 在开始构建应用程序之前，不妨从中了解一些高级方案。

必须通过 [Azure 门户](https://portal.azure.com/)将使用 Azure AD B2C 的每个应用程序注册到 [Azure AD B2C 租户](tutorial-create-tenant.md)中。 应用程序注册过程将收集和分配一些值，例如：

* 用于唯一标识应用程序的应用程序 ID。
* 可用于将响应定向回应用程序的 **回复 URL**。

发送到 Azure AD B2C 的每个请求都指定了 **用户流**（内置策略）或用于控制 Azure AD B2C 行为的 **自定义策略**。 两种策略类型都可以用来创建一系列自定义程度很高的用户体验。

每个应用程序的交互遵循类似的高级模式：

1. 应用程序将用户定向到 v2.0 终结点以执行[策略](user-flow-overview.md)。
2. 用户根据策略定义完成策略。
3. 应用程序从 v2.0 终结点接收安全令牌。
4. 应用程序使用该安全令牌访问受保护的信息或受保护的资源。
5. 资源服务器验证安全令牌，确认是否可以授予访问权限。
6. 应用程序定期刷新安全令牌。

根据要构建的应用程序类型，这些步骤可能稍有不同。

## <a name="web-applications"></a>Web 应用程序

对于托管在服务器中通过浏览器访问的 Web 应用程序（包括 .NET、PHP、Java、Ruby、Python 和 Node.js），Azure AD B2C 支持使用 [OpenID Connect](protocols-overview.md) 实现所有用户体验。 在 OpenID Connect 的 Azure AD B2C 实现中，Web 应用程序通过向 Azure AD 发出身份验证请求，来发起用户体验。 请求的结果是 `id_token`。 此安全令牌代表用户的标识。 它还以声明形式提供用户的相关信息：

```json
// Partial raw id_token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded id_token
{
    "name": "John Smith",
    "email": "john.smith@gmail.com",
    "oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
    ...
}
```

请参阅 [Azure AD B2C 令牌参考](tokens-overview.md)，详细了解应用程序可用的令牌和声明类型。

在 Web 应用程序中，每次执行[策略](user-flow-overview.md)都要采用以下高级步骤：

1. 用户浏览到 Web 应用程序。
2. Web 应用程序将用户重定向到指示要执行的策略的 Azure AD B2C。
3. 用户完成策略。
4. Azure AD B2C 将 `id_token` 返回到浏览器。
5. `id_token` 发布到重定向 URI。
6. 验证 `id_token` 并设置会话 Cookie。
7. 安全页返回至用户。

使用从 Azure AD 收到的公共签名密钥来验证 `id_token` ，就足以验证用户的标识。 此进程也会设置可在后续页面请求中用于识别用户的会话 Cookie。

若要查看此方案的实际运行情况，请尝试运行 [入门部分](overview.md)中提供的 Web 应用程序登录代码示例之一。

除了简化登录，Web 服务器应用程序可能还需要访问后端 Web 服务。 在此情况下，Web 应用程序可以执行稍有不同的 [OpenID Connect 流](openid-connect.md)，使用授权代码和刷新令牌来获取令牌。 以下 [Web API 部分](#web-apis)描述了此方案。

## <a name="single-page-applications"></a>单页应用程序
许多新式 Web 应用程序都构建为客户端单页应用程序 (SPA)。 开发人员使用 JavaScript 或 SPA 框架（例如 Angular、Vue 和 React）来编写它们。 这些应用程序在 Web 浏览器上运行，与传统的服务器端 Web 应用程序相比，它们具有不同的身份验证特征。

Azure AD B2C 提供了两个选项，用于允许单页应用程序让用户登录并获取用于访问后端服务或 Web API 的令牌：

### <a name="authorization-code-flow-with-pkce"></a>授权代码流（带有 PKCE）
- [OAuth 2.0 授权代码流（使用 PKCE）](./authorization-code-flow.md)。 授权代码流允许应用程序用授权代码来交换 **ID** 令牌（表示已经过身份验证的用户），以及交换调用受保护 API 所需的 **访问** 令牌。 此外，它还返回 **刷新** 令牌，这类令牌提供以用户身份长期访问资源而无需与这些用户交互的权限。 

这是 **建议的** 做法。 拥有使用期有限的刷新令牌可以帮助你的应用适应 Safari ITP 之类的[新式浏览器 cookie 隐私限制](../active-directory/develop/reference-third-party-cookies-spas.md)。

若要利用此流，应用程序可以使用支持它的身份验证库，如 [MSAL.js 2.x](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)。

<!-- ![Single-page applications-auth](./media/tutorial-single-page-app/spa-app-auth.svg) -->
![单页应用程序 - 授权](./media/tutorial-single-page-app/active-directory-oauth-code-spa.png)

### <a name="implicit-grant-flow"></a>隐式授予流
- [OAuth 2.0 隐式流](implicit-flow-single-page-application.md)。 某些框架（如 [MSAL.js 1.x](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-core)）仅支持隐式授权流。 隐式授权流允许应用程序获取 **ID** 和 **访问** 令牌。 与授权代码流不同，隐式授权流不会返回 **刷新令牌**。 

此身份验证流不包括使用 Electron 和 React-Native 之类的跨平台 JavaScript 框架的应用程序方案。 这些方案需要更多功能才能与本机平台进行交互。

## <a name="web-apis"></a>Web API

可使用 Azure AD B2C 保护 Web 服务，例如应用程序的 RESTful Web API。 Web API 可以使用 OAuth 2.0 保护其数据，使用令牌对传入的 HTTP 请求进行身份验证。 Web API 的调用方在 HTTP 请求的授权标头中附加一个令牌：

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
``` 

然后，Web API 就可以使用此令牌来验证 API 调用方的标识，并从令牌中编码的声明里提取调用方的相关信息。 请参阅 [Azure AD B2C token reference](tokens-overview.md)（Azure AD B2C 令牌参考），详细了解应用可用的令牌和声明类型。

Web API 可从许多类型的客户端（包括 Web 应用程序、桌面和移动应用程序、单页应用程序、服务器端守护程序，甚至其他 Web API）接收令牌。 下面是 Web 应用程序调用 Web API 的完整流程示例：

1. Web 应用程序执行策略，用户完成用户体验。
2. Azure AD B2C 将 (OpenID Connect) `id_token` 和授权代码返回到浏览器。
3. 浏览器将 `id_token` 和授权代码发布到重定向 URI。
4. Web 服务器验证 `id_token` 并设置会话 Cookie。
5. Web 服务器通过提供授权代码、应用程序客户端 ID 和客户端凭据，请求 Azure AD B2C 提供 `access_token`。
6. `access_token` 和 `refresh_token` 返回到 Web 服务器。
7. 使用授权标头中的 `access_token` 调用 Web API。
8. Web API 对令牌进行验证。
9. 安全数据将返回给 Web 应用程序。

有关授权代码、刷新令牌的详细信息和获取令牌的步骤，请参阅 [OAuth 2.0 protocol](authorization-code-flow.md)（OAuth 2.0 协议）。

若要了解如何使用 Azure AD B2C 保护 Web API，请查看 [入门部分](overview.md)中的 Web API 教程。

## <a name="mobile-and-native-applications"></a>移动和本机应用程序

安装在设备中的应用程序（例如移动和桌面应用程序）通常需要代表用户访问后端服务或 Web API。 可将自定义的标识管理体验添加到本机应用程序，使用 Azure AD B2C 和 [OAuth 2.0 授权代码流](authorization-code-flow.md)安全调用后端服务。

在此流中，应用程序执行[策略](user-flow-overview.md)，在用户完成策略之后，从 Azure AD 接收 `authorization_code`。 `authorization_code` 表示应用程序有权代表当前登录的用户调用后端服务。 然后，该应用程序即可在后台将 `authorization_code` 交换成 `access_token` 和 `refresh_token`。  应用程序可以在 HTTP 请求中使用 `access_token` 向后端 Web API 进行身份验证。 它还可以使用 `refresh_token` 获取新的 `access_token`（如果旧令牌已过期）。

## <a name="current-limitations"></a>当前限制

### <a name="unsupported-application-types"></a>不受支持的应用程序类型

#### <a name="daemonsserver-side-applications"></a>守护程序/服务器端应用程序

包含长时运行进程或不需要用户操作的应用程序还需要通过其他方法访问受保护的资源，例如 Web API。 这些应用程序可使用应用程序的标识（而不是用户的委派标识）并使用 OAuth 2.0 客户端凭据流来进行身份验证和获取令牌。 客户端凭据流与代表流不同，代表流不会应用于服务器到服务器的身份验证。

尽管 Azure AD B2C 身份验证服务目前不直接支持 OAuth 2.0 客户端凭据授予流，但你可以使用 Azure AD 和 Microsoft 标识平台/令牌 (https://login.microsoftonline.com/your-tenant-name.onmicrosoft.com/oauth2/v2.0/token) 终结点为 Azure AD B2C 租户中的应用程序设置客户端凭据流。 Azure AD B2C 租户与 Azure AD 企业租户共享某些功能。

若要设置客户端凭据流，请参阅 [Azure Active Directory v2.0 和 OAuth 2.0 客户端凭据流](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md)。 如 [Azure AD 令牌参考](../active-directory/develop/id-tokens.md)中所述，身份验证成功后会收到格式化的令牌，以便 Azure AD 可使用它。

有关注册管理应用程序的说明，请参阅[使用 Microsoft Graph 管理 Azure AD B2C](microsoft-graph-get-started.md)。

#### <a name="web-api-chains-on-behalf-of-flow"></a>Web API 链（代理流）

许多体系结构包含需要调用另一个下游 Web API 的 Web API，这两者都受 Azure AD B2C 的保护。 此方案常见于具有 Web API 后端的本机客户端，并调用 Microsoft Graph API 等 Microsoft 联机服务。

可以使用 OAuth 2.0 JWT 持有者凭据授权（也称为“代理流”）来支持这种链接的 Web API 方案。  但是，Azure AD B2C 中目前尚未实现代理流。

## <a name="next-steps"></a>后续步骤

详细了解 [Azure Active Directory B2C 中的用户流](user-flow-overview.md)提供的内置策略。
