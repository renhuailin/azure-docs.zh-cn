---
title: 如何在应用程序中使用启用了持续存取评估的 Api |Microsoft
titleSuffix: Microsoft identity platform
description: 如何提高应用程序安全性和复原能力，方法是添加对连续访问评估的支持，启用可根据关键事件和策略评估进行吊销的长时间访问令牌。
services: active-directory
author: knicholasa
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/06/2020
ms.author: nichola
ms.reviewer: ''
ms.openlocfilehash: cf5a7a5902484536d0cf2a1844be469f29e15f4b
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2020
ms.locfileid: "96348460"
---
# <a name="how-to-use-continuous-access-evaluation-enabled-apis-in-your-applications"></a>如何在应用程序中使用启用了持续存取评估的 Api

[持续访问评估](../conditional-access/concept-continuous-access-evaluation.md) (CAE) 是一种新兴行业标准，允许根据 [关键事件](../conditional-access/concept-continuous-access-evaluation.md#critical-event-evaluation) 和 [策略评估](../conditional-access/concept-continuous-access-evaluation.md#conditional-access-policy-evaluation-preview) 来撤销访问令牌，而不是依赖基于生存期的令牌过期。 对于某些资源 Api，由于风险和策略是实时评估的，因此可以将令牌生存期增加到最多28小时。 Microsoft 身份验证库会主动刷新这些长时间生存期的令牌 (MSAL) ，从而提高应用程序的复原能力。

本文介绍如何在应用程序中使用启用了 CAE 的 Api。

## <a name="implementation-considerations"></a>实现注意事项

若要使用连续访问评估，应用和它访问的资源 API 必须 CAE 启用。 但是，准备代码以使用启用 CAE 的资源不会阻止你使用未启用 CAE 的 Api。 

如果资源 API 实现 CAE，并且你的应用程序声明它可以处理 CAE，你的应用将获取该资源的 CAE 标记。 出于此原因，如果你将应用程序 CAE 声明为 ready，你的应用程序必须处理所有接受 Microsoft 标识访问令牌的资源 Api 的 CAE 声明质询。 如果未在这些 API 调用中处理 CAE 响应，你的应用程序可能会在循环中最终重试 API 调用，该令牌仍处于令牌的返回生存期，但由于 CAE 而被吊销。 

## <a name="the-code"></a>代码

第一步是添加代码以处理来自拒绝调用（由于 CAE）的资源 API 的响应。 使用 CAE，当访问令牌已被吊销或者 API 检测到所使用的 IP 地址发生变化时，Api 将返回401状态和 WWW-Authenticate 标头。 WWW-Authenticate 标头包含一个声明质询，应用程序可使用该质询获取新的访问令牌。

例如：

```console
HTTP 401; Unauthorized
WWW-Authenticate=Bearer
 authorization_uri="https://login.windows.net/common/oauth2/authorize",
 error="insufficient_claims",
 claims="eyJhY2Nlc3NfdG9rZW4iOnsibmJmIjp7ImVzc2VudGlhbCI6dHJ1ZSwgInZhbHVlIjoiMTYwNDEwNjY1MSJ9fX0="
```

你的应用将检查以下内容：

- 返回401状态的 API 调用
- WWW-Authenticate 标头是否存在，其中包含：
  - 值为 "insufficient_claims" 的 "error" 参数
  - "声明" 参数

满足这些条件时，应用可以提取和解码声明质询。

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
        AuthenticationHeaderValue bearer = APIresponse.Headers.WwwAuthenticate.First
            (v => v.Scheme == "Bearer");
        IEnumerable<string> parameters = bearer.Parameter.Split(',').Select(v => v.Trim()).ToList();
        var error = GetParameter(parameters, "error");

        if (null != error && "insufficient_claims" == error)
        {
            var claimChallengeParameter = GetParameter(parameters, "claims");
            if (null != claimChallengeParameter)
            {
                var claimChallengebase64Bytes = System.Convert.FromBase64String(claimChallengeParameter);
                var claimChallenge = System.Text.Encoding.UTF8.GetString(claimChallengebase64Bytes);
                var newAccessToken = await GetAccessTokenWithClaimChallenge(scopes, claimChallenge);
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

一旦你的应用程序准备好处理启用了 CAE 的资源返回的声明质询，你就可以告诉 Microsoft 标识你的应用已 CAE 就绪。 若要在 MSAL 应用程序中执行此操作，请使用 "cp1" 的客户端功能构建公用客户端。

```csharp
_clientApp = PublicClientApplicationBuilder.Create(App.ClientId)
    .WithDefaultRedirectUri()
    .WithAuthority(authority)
    .WithClientCapabilities(new [] {"cp1"})
    .Build();
```

可以通过将用户登录到应用程序，然后使用 Azure 门户吊销用户会话，来测试应用程序。 应用下次调用启用了 CAE 的 API 时，系统将要求用户重新进行身份验证。

## <a name="next-steps"></a>后续步骤

若要了解详细信息，请参阅 [持续访问评估](/conditional-access/concept-continuous-access-evaluation.md)。
