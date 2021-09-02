---
title: 如何在应用程序中使用启用了连续访问评估的 API | Azure
titleSuffix: Microsoft identity platform
description: 如何通过添加对连续访问评估的支持、启用可根据关键事件和策略评估进行撤销的生存期较长的令牌，来提高应用安全性和复原能力。
services: active-directory
author: knicholasa
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/09/2021
ms.author: nichola
ms.reviewer: ''
ms.openlocfilehash: 816975db5ee6fd613e077dd7d268825c6601b3fe
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114444434"
---
# <a name="how-to-use-continuous-access-evaluation-enabled-apis-in-your-applications"></a>如何在应用程序中使用启用了连续访问评估的 API

[连续访问评估](../conditional-access/concept-continuous-access-evaluation.md) (CAE) 是一项 Azure AD 功能，它可以根据[关键事件](../conditional-access/concept-continuous-access-evaluation.md#critical-event-evaluation)和[策略评估](../conditional-access/concept-continuous-access-evaluation.md#conditional-access-policy-evaluation-preview)撤销访问令牌，而不用依赖基于生存期的令牌过期。 对于某些资源 API，由于风险和策略是实时评估的，因此这可能会将令牌生存期提高到最多 28 小时。 Microsoft 身份验证库 (MSAL) 将主动刷新这些生存期较长的令牌，从而提高应用程序的复原能力。

本文介绍如何在应用程序中使用启用了 CAE 的 API。 不使用 MSAL 的应用程序可以添加对[声明质询、声明请求和客户端功能](claims-challenge.md)的支持以使用 CAE。

## <a name="implementation-considerations"></a>实现注意事项

若要使用连续访问评估，应用及其访问的资源 API 必须已启用 CAE。 但是，准备代码以使用启用了 CAE 的资源不会阻止你使用未启用 CAE 的 API。

如果资源 API 实现了 CAE，并且你的应用程序声明它可以处理 CAE，那么你的应用将会获取该资源的 CAE 标记。 出于此原因，如果声明了应用已 CAE 就绪，那么你的应用程序必须为所有接受 Microsoft 标识访问令牌的资源 API 处理 CAE 声明质询。 如果未在这些 API 调用中处理 CAE 响应，你的应用程序可能会在使用一个仍处于已返回的令牌生存期内，但由于 CAE 而被撤销的令牌来重试 API 调用的循环中结束。

## <a name="the-code"></a>代码

第一步是添加代码以处理由于 CAE 而拒绝该调用的资源 API 的响应。 启用 CAE 后，当访问令牌已被撤销或者 API 检测到所使用的 IP 地址发生变化时，API 将返回 401 状态和 WWW-Authenticate 标头。 WWW-Authenticate 标头包含一个声明质询，应用程序可以使用该质询来获取新的访问令牌。

例如：

```console
HTTP 401; Unauthorized
WWW-Authenticate=Bearer
  authorization_uri="https://login.windows.net/common/oauth2/authorize",
  error="insufficient_claims",
  claims="eyJhY2Nlc3NfdG9rZW4iOnsibmJmIjp7ImVzc2VudGlhbCI6dHJ1ZSwgInZhbHVlIjoiMTYwNDEwNjY1MSJ9fX0="
```

你的应用将检查以下内容：

- 返回 401 状态的 API 调用
- 是否存在包含以下参数的 WWW-Authenticate 标头：
  - 值为“insufficient_claims”的“error”参数
  - “claims”参数

满足这些条件时，应用可以使用 MSAL.NET `WwwAuthenticateParameters` 类提取声明质询并对其进行解码。

```csharp
if (APIresponse.IsSuccessStatusCode)
{
    // ...
}
else
{
    if (APIresponse.StatusCode == System.Net.HttpStatusCode.Unauthorized
        && APIresponse.Headers.WwwAuthenticate.Any())
    {
        string claimChallenge = WwwAuthenticateParameters.GetClaimChallengeFromResponseHeaders(APIresponse.Headers);
```

然后，你的应用将使用声明质询获取资源的新访问令牌。

```csharp
try
{
    authResult = await _clientApp.AcquireTokenSilent(scopes, firstAccount)
        .WithClaims(claimChallenge)
        .ExecuteAsync()
        .ConfigureAwait(false);
}
catch (MsalUiRequiredException)
{
    try
    {
        authResult = await _clientApp.AcquireTokenInteractive(scopes)
            .WithClaims(claimChallenge)
            .WithAccount(firstAccount)
            .ExecuteAsync()
            .ConfigureAwait(false);
    }
    // ...
```

一旦你的应用程序准备好处理启用了 CAE 的资源返回的声明质询，你就可以告诉 Microsoft 标识你的应用已 CAE 就绪。 若要在 MSAL 应用程序中执行此操作，请使用“cp1”的客户端功能构建公共客户端。

```csharp
_clientApp = PublicClientApplicationBuilder.Create(App.ClientId)
    .WithDefaultRedirectUri()
    .WithAuthority(authority)
    .WithClientCapabilities(new [] {"cp1"})
    .Build();
```

可以通过使用户登录到应用程序，然后使用 Azure 门户撤销用户会话，来测试应用程序。 应用下次调用启用了 CAE 的 API 时，系统将要求用户重新进行身份验证。

## <a name="next-steps"></a>后续步骤

- [持续访问评估](../conditional-access/concept-continuous-access-evaluation.md)概念概述
- [声明质询、声明请求和客户端功能](claims-challenge.md)
