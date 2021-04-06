---
title: 处理 MSAL.NET 中的错误和异常
titleSuffix: Microsoft identity platform
description: 了解如何处理 MSAL.NET 中的错误和异常、条件访问声明质询以及重试。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/26/2020
ms.author: marsma
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 565acd745ba5d7fdec71f306d3851e599838f7d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "99584038"
---
# <a name="handle-errors-and-exceptions-in-msalnet"></a>处理 MSAL.NET 中的错误和异常

[!INCLUDE [Active directory error handling introduction](../../../includes/active-directory-develop-error-handling-introduction.md)]

## <a name="error-handling-in-msalnet"></a>MSAL.NET 中的错误处理

处理 .NET 异常时，可以使用异常类型本身和 `ErrorCode` 成员来区分不同的异常。 `ErrorCode` 的值是 [MsalError](/dotnet/api/microsoft.identity.client.msalerror) 类型的常量。

也可以查看 [MsalClientException](/dotnet/api/microsoft.identity.client.msalexception)、[MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) 和 [MsalUIRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception) 字段。

如果引发 [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception)，请尝试查看[身份验证和授权错误代码](reference-aadsts-error-codes.md)，以查看其中是否列出了相关代码。

### <a name="common-net-exceptions"></a>常见的 .NET 异常

下面是可能引发的常见异常和一些可能的缓解措施：  

| 异常 | 错误代码 | 缓解措施|
| --- | --- | --- |
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception) | AADSTS65001：用户或管理员尚未许可使用名为“{appName}”、ID 为“{appId}”的应用程序。 针对此用户和资源发送交互式授权请求。| 首先获取用户同意。 如果未使用 .NET Core（它没有任何 Web UI），请调用 `AcquireTokeninteractive`（仅一次）。 如果使用 .NET Core 或者不希望执行 `AcquireTokenInteractive`，则用户可以导航到某个 URL 来提供许可：`https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read`。 要调用 `AcquireTokenInteractive`，请使用 `app.AcquireTokenInteractive(scopes).WithAccount(account).WithClaims(ex.Claims).ExecuteAsync();`|
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception) | AADSTS50079：用户必须使用[多重身份验证 (MFA)](../authentication/concept-mfa-howitworks.md)。| 无缓解措施。 如果已为租户配置 MFA 并且 Azure Active Directory (AAD) 决定对其进行强制实施，则回退到 `AcquireTokenInteractive` 或 `AcquireTokenByDeviceCode` 等交互式流。|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) |AADSTS90010：/common 或 /consumers 终结点不支持此授权类型 。 请使用 */organizations* 或特定于租户的终结点。 使用了 */common*。| 根据 Azure AD 发出的消息中所述，颁发机构需要使用一个租户或 */organizations*。|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) | AADSTS70002：请求正文必须包含以下参数：`client_secret or client_assertion`。| 如果应用程序未注册为 Azure AD 中的公共客户端应用程序，则可能引发此异常。 在 Azure 门户中编辑应用程序的清单，并将 `allowPublicClient` 设置为 `true`。 |
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception)| `unknown_user Message`：无法识别已登录的用户| 库无法查询当前的 Windows 已登录用户，或者此用户未加入 AD 或 Azure AD（已加入工作区的用户不受支持）。 缓解措施 1：在 UWP 中，检查应用程序是否具有以下功能：企业身份验证、专用网络（客户端和服务器）、用户帐户信息。 缓解措施 2：实现自己的逻辑以提取用户名（例如 john@contoso.com），并使用 `AcquireTokenByIntegratedWindowsAuth` 表单来提取用户名。|
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception)|integrated_windows_auth_not_supported_managed_user| 此方法依赖于 Active Directory (AD) 公开的协议。 如果在 Azure AD 中创建了一个用户但该用户不受 AD 的支持（“托管”用户），则此方法将会失败。 在 AD 中创建的受 Azure AD 支持的用户（“联合”用户）可以受益于这种非交互式身份验证方法。 缓解：使用交互式身份验证。|

### `MsalUiRequiredException`

调用 `AcquireTokenSilent()` 时，从 MSAL.NET 返回的其中一个常见状态代码是 `MsalError.InvalidGrantError`。 此状态代码表示应用程序应再次调用身份验证库，但要在交互模式下调用（用于公共客户端应用程序的 AcquireTokenInteractive 或 AcquireTokenByDeviceCodeFlow 会在 Web 应用中执行质询）。 这是因为在颁发身份验证令牌之前，需要进行其他用户交互。

大多数情况下，`AcquireTokenSilent` 失败的原因是，令牌缓存没有与请求匹配的令牌。 访问令牌将在 1 小时后过期，`AcquireTokenSilent` 将尝试基于刷新令牌获取新的令牌（在 OAuth2 术语中，这是刷新令牌流）。 此流也可能因各种原因而失败，例如，如果租户管理员配置了更严格的登录策略。 

交互的目标在于让用户执行一项操作。 在这些条件中，有些对于用户来说很容易解决（例如，通过单击接受使用条款），而有些则无法通过当前配置进行解决（例如，有问题的计算机需要连接到特定的公司网络）。 有些条件可帮助用户设置多重身份验证，或者在其设备上安装 Microsoft Authenticator。

### <a name="msaluirequiredexception-classification-enumeration"></a>`MsalUiRequiredException` 分类枚举

MSAL 公开一个 `Classification` 字段，你可以读取该字段，以便提供更好的用户体验。 例如，用于告知用户其密码已过期，或者他们需要许可使用某些资源。 支持的值是 `UiRequiredExceptionClassification` 枚举的一部分：

| 分类    | 含义           | 建议的处理方式 |
|-------------------|-------------------|----------------------|
| BasicAction | 在交互式身份验证流中，条件可以通过用户交互来解决。 | 调用 AcquireTokenInteractively()。 |
| AdditionalAction | 在交互式身份验证流之外，条件可以通过与系统进行其他补救交互来解决。 | 调用 AcquireTokenInteractively() 以显示一条说明补救操作的消息。 如果用户不太可能完成补救操作，则调用应用程序可能会选择隐藏需要 additional_action 的流。 |
| MessageOnly      | 条件目前无法解决。 启动交互式身份验证流将显示一条说明条件的消息。 | 调用 AcquireTokenInteractively() 以显示一条说明条件的消息。 用户读取该消息并关闭窗口后，AcquireTokenInteractively() 将返回 UserCanceled 错误。 如果用户不太可能从该消息中获益，则调用应用程序可能会选择隐藏导致 message_only 的流。|
| ConsentRequired  | 用户许可缺失或已撤销。 | 调用 AcquireTokenInteractively()，以取得用户许可。 |
| UserPasswordExpired | 用户的密码已过期。 | 调用 AcquireTokenInteractively()，以便用户可以重置其密码。 |
| PromptNeverFailed| 通过参数 prompt=never 调用交互式身份验证，强制 MSAL 依赖浏览器 cookie，而不是显示浏览器。 此操作已失败。 | 在没有 Prompt.None 的情况下，调用 AcquireTokenInteractively() |
| AcquireTokenSilentFailed | MSAL SDK 没有足够的信息，无法从缓存中获取令牌。 这可能是因为缓存中没有令牌，或找不到帐户。 此错误消息包含更多详细信息。  | 调用 AcquireTokenInteractively()。 |
| 无    | 未提供更多详细信息。 在交互式身份验证流中，条件可以通过用户交互来解决。 | 调用 AcquireTokenInteractively()。 |

## <a name="net-code-example"></a>.NET 代码示例

```csharp
AuthenticationResult res;
try
{
 res = await application.AcquireTokenSilent(scopes, account)
        .ExecuteAsync();
}
catch (MsalUiRequiredException ex) when (ex.ErrorCode == MsalError.InvalidGrantError)
{
 switch (ex.Classification)
 {
  case UiRequiredExceptionClassification.None:
   break;
  case UiRequiredExceptionClassification.MessageOnly:
  // You might want to call AcquireTokenInteractive(). Azure AD will show a message
  // that explains the condition. AcquireTokenInteractively() will return UserCanceled error
  // after the user reads the message and closes the window. The calling application may choose
  // to hide features or data that result in message_only if the user is unlikely to benefit 
  // from the message
  try
  {
      res = await application.AcquireTokenInteractive(scopes).ExecuteAsync();
  }
  catch (MsalClientException ex2) when (ex2.ErrorCode == MsalError.AuthenticationCanceledError)
  {
   // Do nothing. The user has seen the message
  }
  break;

  case UiRequiredExceptionClassification.BasicAction:
  // Call AcquireTokenInteractive() so that the user can, for instance accept terms
  // and conditions

  case UiRequiredExceptionClassification.AdditionalAction:
  // You might want to call AcquireTokenInteractive() to show a message that explains the remedial action. 
  // The calling application may choose to hide flows that require additional_action if the user 
  // is unlikely to complete the remedial action (even if this means a degraded experience)

  case UiRequiredExceptionClassification.ConsentRequired:
  // Call AcquireTokenInteractive() for user to give consent.
  
  case UiRequiredExceptionClassification.UserPasswordExpired:
  // Call AcquireTokenInteractive() so that user can reset their password
  
  case UiRequiredExceptionClassification.PromptNeverFailed:
  // You used WithPrompt(Prompt.Never) and this failed
  
  case UiRequiredExceptionClassification.AcquireTokenSilentFailed:
  default:
  // May be resolved by user interaction during the interactive authentication flow.
  res = await application.AcquireTokenInteractive(scopes)
                         .ExecuteAsync(); break;
 }
}
```
[!INCLUDE [Active directory error handling claims challenges](../../../includes/active-directory-develop-error-handling-claims-challenges.md)]

从 MSAL.NET 调用需要条件访问的 API 时，应用程序需要处理声明质询异常。 此错误将显示为 [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception)，其中的 [Claims](/dotnet/api/microsoft.identity.client.msalserviceexception.claims) 属性不为空。

要处理声明质询，需要使用 `PublicClientApplicationBuilder` 类的 `.WithClaim()` 方法。

[!INCLUDE [Active directory error handling retries](../../../includes/active-directory-develop-error-handling-retries.md)]

### <a name="http-error-codes-500-600"></a>HTTP 错误代码 500-600

对于 HTTP 错误代码为 500-600 的错误，MSAL.NET 实现一个简单的重试一次机制。

[MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) 将 `System.Net.Http.Headers.HttpResponseHeaders` 作为 `namedHeaders` 属性展现。 可以利用错误代码中的附加信息来提高应用程序的可靠性。 对于前面所述的情况，可以使用 `RetryConditionHeaderValue` 类型的 `RetryAfterproperty` 并计算重试时间。

下面是使用客户端凭据流的守护程序示例。 可以将此流修改为使用任何用于获取令牌的方法。

```csharp

bool retry = false;
do
{
    TimeSpan? delay;
    try
    {
         result = await publicClientApplication.AcquireTokenForClient(scopes, account).ExecuteAsync();
    }
    catch (MsalServiceException serviceException)
    {
         if (serviceException.ErrorCode == "temporarily_unavailable")
         {
             RetryConditionHeaderValue retryAfter = serviceException.Headers.RetryAfter;
             if (retryAfter.Delta.HasValue)
             {
                 delay = retryAfter.Delta;
             }
             else if (retryAfter.Date.HasValue)
             {
                 delay = retryAfter.Date.Value.Offset;
             }
         }
    }
    // . . .
    if (delay.HasValue)
    {
        Thread.Sleep((int)delay.Value.TotalMilliseconds); // sleep or other
        retry = true;
    }
} while (retry);
```

## <a name="next-steps"></a>后续步骤

请考虑启用 [MSAL.NET 中的日志](msal-logging-dotnet.md)，以帮助诊断并调试问题。
