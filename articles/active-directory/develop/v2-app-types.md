---
title: Microsoft 标识平台的应用程序类型 | Azure
description: Microsoft 标识平台支持的应用类型和方案。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 11/13/2020
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev, fasttrack-edit, contperf-fy21q2
ms.openlocfilehash: c775efa92c249904348f1ced18994c42e9ecc8eb
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128597809"
---
# <a name="application-types-for-the-microsoft-identity-platform"></a>Microsoft 标识平台的应用程序类型

Microsoft 标识平台支持各种现代应用体系结构的身份验证，所有这些体系结构都基于行业标准协议 [OAuth 2.0 或 OpenID Connect](active-directory-v2-protocols.md)。 本指南介绍可以通过 Microsoft 标识平台生成的应用的类型，而不考虑首选语言或平台。 该信息旨在帮助你在开始处理[应用程序方案](authentication-flows-app-scenarios.md#application-scenarios)中的代码之前了解高级方案。

## <a name="the-basics"></a>基础知识

必须在 Azure 门户[应用注册](https://go.microsoft.com/fwlink/?linkid=2083908)中注册使用 Microsoft 标识平台的每个应用。 应用注册过程将收集这些值并将其分配给应用：

* 用于唯一标识应用的“应用程序(客户端) ID”。
* 用于将响应定向回应用的重定向 URI
* 其他一些特定于方案的值，例如支持的帐户类型

有关详细信息，请了解如何[注册应用](quickstart-register-app.md)。

注册应用后，应用将通过向终结点发送请求来与 Microsoft 标识平台通信。 我们提供开源框架和库，用于处理这些请求的详细信息。 还可以通过创建对这些终结点的请求，选择自行实现身份验证逻辑：

```HTTP
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

## <a name="single-page-apps-javascript"></a>单页应用 (JavaScript)

许多新式应用都有一个单页应用前端（主要以 JavaScript 编写），通常使用 Angular、React 或 Vue 之类的框架。 Microsoft 标识平台支持这些应用的方式是使用 [OpenID Connect](v2-protocols-oidc.md) 协议进行身份验证，以及使用 [OAuth 2.0 隐式授权流](v2-oauth2-implicit-grant-flow.md)或更新的 [OAuth 2.0 授权代码 + PKCE 流](v2-oauth2-auth-code-flow.md)进行授权（见下文）。

下面的流程图演示了 OAuth 2.0 授权代码授予（省略了有关 PKCE 的详细信息），其中应用从 Microsoft 标识平台 `authorize` 终结点接收代码，并使用跨站点 Web 请求将其兑换为访问令牌和刷新令牌。 访问令牌每 24 小时过期一次，应用必须使用刷新令牌请求另一个代码。 除了访问令牌外，通常还通过相同的流和/或单独的 OpenID Connect 请求（此处未显示）来请求 `id_token`（表示已登录到客户端应用程序的用户）。

:::image type="content" source="media/v2-oauth-auth-code-spa/active-directory-oauth-code-spa.svg" alt-text="显示单页应用和安全令牌服务终结点之间的 OAuth 2 授权代码流的图表。" border="false":::

若要查看此方案的实际运行情况，请参阅[教程：使用授权代码流让用户登录并从 JavaScript SPA 调用 Microsoft Graph API](tutorial-v2-javascript-auth-code.md)。

### <a name="authorization-code-flow-vs-implicit-flow"></a>授权代码流与隐式流

在 OAuth 2.0 历史上，大多建议使用[隐式流](v2-oauth2-implicit-grant-flow.md)来生成单页应用。 随着[第三方 Cookie](reference-third-party-cookies-spas.md) 的删除，以及对隐式流安全问题的[日益关注](https://tools.ietf.org/html/draft-ietf-oauth-security-topics-14)，我们已转为使用授权代码流来生成单页应用。

为了确保应用在 Safari 和其他注重隐私的浏览器中的兼容性，我们不再建议使用隐式流，而建议使用授权代码流。

## <a name="web-apps"></a>Web 应用

对于通过浏览器访问的 Web 应用（.NET、PHP、Java、Ruby、Python、Node 等），可以使用 [OpenID Connect](active-directory-v2-protocols.md) 来执行用户登录。 在 OpenID Connect 中，Web 应用接收 ID 令牌。 ID 令牌是一个安全令牌，用于验证用户的标识并以声明形式提供有关用户的信息：

```JSON
// Partial raw ID token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded ID token
{
    "name": "John Smith",
    "email": "john.smith@gmail.com",
    "oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
    ...
}
```

若要更详细地了解 Microsoft 标识平台中使用的不同类型的令牌，请查看[访问令牌](access-tokens.md)参考和 [id_token 参考](id-tokens.md)

在 Web 服务器应用中，登录身份验证流采用以下高级步骤：

![显示 Web 应用身份验证流](./media/v2-app-types/convergence-scenarios-webapp.svg)

使用从 Microsoft 标识平台接收的公共签名密钥验证 ID 令牌便可确保用户的标识正确。 设置会话 Cookie，在后续页面请求中将其用于识别用户。

若要查看此方案的实际运行情况，请尝试[可将用户登录的 Web 应用的方案](scenario-web-app-sign-user-overview.md)中的代码示例。

除了简单登录，Web 服务器应用可能还需要访问其他 Web 服务，例如 REST API。 在这种情况下，Web 服务器应用可以使用 [OAuth 2.0 授权代码流](v2-oauth2-auth-code-flow.md)参与合并的 OpenID Connect 和 OAuth 2.0 流。 有关此方案的详细信息，请阅读 [Web 应用和 Web API 入门](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIDConnect-DotNet)。

## <a name="web-apis"></a>Web API

可使用 Microsoft 标识平台来保护 Web 服务，例如应用的 RESTful Web API。 Web API 可以在多种平台和语言中实现。 它们还可以使用 Azure Functions 中的 HTTP 触发器来实现。 Web API 使用 OAuth 2.0 访问令牌（而不是 ID 令牌和会话 Cookie）来保护数据并对传入的请求进行身份验证。 Web API 调用方会在 HTTP 请求的授权标头中附加一个访问令牌，如下所示：

```HTTP
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

Web API 使用此访问令牌来验证 API 调用方的标识，并从访问令牌中编码的声明里提取调用方的相关信息。 若要更详细地了解 Microsoft 标识平台中使用的不同类型的令牌，请查看[访问令牌](access-tokens.md)参考和 [id_token](id-tokens.md) 参考。

Web API 可让用户通过公开权限（也称为[范围](v2-permissions-and-consent.md)）来选择添加/排除特定的功能或数据。 为了使调用应用能够获取某个范围的权限，用户必须在执行流的过程中同意该范围。 Microsoft 标识平台向用户请求权限，并将这些权限记录到 Web API 收到的所有访问令牌中。 Web API 验证每次调用中接收的访问令牌，并执行授权检查。

Web API 可以从各种应用接收访问令牌，其中包括 Web 服务器应用、桌面和移动应用、单页应用、服务器端守护程序，甚至其他 Web API。 Web API 的高级流如下所示：

![显示 Web API 身份验证流](./media/v2-app-types/convergence-scenarios-webapi.svg)

若要了解如何使用 OAuth2 访问令牌来保护 Web API，请查看[受保护的 Web API 方案](scenario-protected-web-api-overview.md)中提供的 Web API 代码示例。

在许多情况下，Web API 还需要对由 Microsoft 标识平台保护的其他下游 Web API 发出出站请求。 为执行此操作，Web API 可以利用代理流，它允许 Web API 将传入的访问令牌替换为要在出站请求中使用的另一个访问令牌。 有关详细信息，请参阅 [Microsoft 标识平台和 OAuth 2.0 代理流](v2-oauth2-on-behalf-of-flow.md)。

## <a name="mobile-and-native-apps"></a>移动和本机应用

安装在设备中的应用（如移动和桌面应用）通常需要访问用于存储数据和代表用户执行各种功能的后端服务或 Web API。 这些应用可以使用 [OAuth 2.0 授权代码流](v2-oauth2-auth-code-flow.md)将登录凭据和授权添加到后端服务。

在此流中，应用在用户登录时从 Microsoft 标识平台接收授权代码。 授权代码表示应用有权代表登录用户调用后端服务。 应用可以通过在后台交换授权代码获得 OAuth 2.0 访问令牌和刷新令牌。 应用可以使用访问令牌在 HTTP 请求中向 Web API 进行身份验证，并可以在旧的访问令牌过期时，用刷新令牌获取新的访问令牌。

![显示本机应用身份验证流](./media/v2-app-types/convergence-scenarios-native.svg)

> [!NOTE]
> 如果应用程序使用默认的系统 Web 视图，请查看 [Azure AD 身份验证和授权错误代码](reference-aadsts-error-codes.md)中有关“确认我的登录”功能和错误代码 AADSTS50199 的信息。

## <a name="daemons-and-server-side-apps"></a>守护程序和服务器端应用

对于包含长时间运行的进程或无需用户交互便可操作的应用，还需要通过其他方法来访问受保护的资源，例如 Web API。 这些应用可以通过 OAuth 2.0 客户端凭据流使用应用的标识（而不是用户的委派标识）来进行身份验证和获取令牌。 可以使用客户端机密或证书证明应用的身份。 有关详细信息，请参阅[使用 Microsoft 标识平台的 .NET Core 守护程序控制台应用程序](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2)。

在此流中，应用通过直接与 `/token` 终结点交互来获取访问权限：

![显示守护程序应用身份验证流](./media/v2-app-types/convergence-scenarios-daemon.svg)

若要生成守护程序，请参阅[客户端凭据文档](v2-oauth2-client-creds-grant-flow.md)，或者尝试 [.NET 示例应用](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2)。

## <a name="next-steps"></a>后续步骤

你已经熟悉 Microsoft 标识平台支持的应用程序类型，接下来详细了解 [OAuth 2.0 和 OpenID Connect](active-directory-v2-protocols.md)，以了解不同方案使用的协议组件。
