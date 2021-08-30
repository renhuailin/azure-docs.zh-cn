---
title: 将机密客户端应用程序迁移到 MSAL.NET
titleSuffix: Microsoft identity platform
description: 了解如何将机密客户端应用程序从适用于 .NET 的 Azure Active Directory 身份验证库迁移到适用于 .NET 的 Microsoft 身份验证库。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 06/08/2021
ms.author: jmprieur
ms.reviewer: saeeda, shermanouko
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: b48056b9615bcbf1ecbcdaf3501437a18fcb987b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121728400"
---
# <a name="migrate-confidential-client-applications-from-adalnet-to-msalnet"></a>将机密客户端应用程序从 ADAL.NET 迁移到 MSAL.NET

本文介绍如何将机密客户端应用程序从适用于 .NET 的 Azure Active Directory 身份验证库 (ADAL.NET) 迁移到适用于 .NET 的 Microsoft 身份验证库 (MSAL.NET)。 机密客户端应用程序是 Web 应用、Web API 和守护程序应用程序，它们以自身名义调用另一种服务。 有关机密应用程序的详细信息，请参阅[身份验证流和应用程序方案](authentication-flows-app-scenarios.md)。 如果应用是基于 ASP.NET Core，请使用 [Microsoft.Identity.Web](microsoft-identity-web.md)。

对于应用注册：

- 无需创建新应用注册。 （保留同一客户端 ID。）
- 无需变更预授权（管理员许可的 API 权限）。

## <a name="migration-steps"></a>迁移步骤

1. 在应用中使用 ADAL.NET 查找代码。

   使用机密客户端应用程序中的 ADAL 的代码会通过以下参数实例化 `AuthenticationContext` 并调用 `AcquireTokenByAuthorizationCode` 或 `AcquireTokenAsync` 的一种替代项：

   - `resourceId` 字符串。 此变量是要调用的 Web API 的应用 ID URI。
   - `IClientAssertionCertificate` 的一个实例或 `ClientAssertion`。 此实例为应用提供客户端凭据，以证明应用的标识。

1. 确定有应用使用 ADAL.NET 后，安装 MSAL.NET NuGet 包 [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) 并更新项目库引用。 有关详细信息，请参阅[安装 NuGet 包](https://www.bing.com/search?q=install+nuget+package)。

1. 按照机密客户端场景更新代码。 一些步骤是通用的，适用于所有机密客户端场景。 其他步骤对每种场景具有唯一性。 

   机密客户端场景如下：

   - Web 应用、Web API 和守护程序控制台应用程序均支持[守护程序方案](?tabs=daemon#migrate-daemon-apps)。
   - [调用下游 Web API 的 Web API](?tabs=obo#migrate-a-web-api-that-calls-downstream-web-apis)受代表用户调用下游 Web API 的 Web API 支持。
   - 可让用户登录和调用下游 Web API 的 Web 应用支持[调用 Web API 的 Web 应用](?tabs=authcode#migrate-a-web-api-that-calls-downstream-web-apis)。

你可能为 ADAL.NET 提供了一个包装器来处理证书和缓存。 本文使用相同的方法来说明从 ADAL.NET 迁移到 MSAL.NET 的过程。 但是，此代码仅用于演示目的。 请勿复制/粘贴这些包装器或直接将其集成于代码中。

## <a name="daemon"></a>[守护程序](#tab/daemon)

### <a name="migrate-daemon-apps"></a>迁移守护程序应用

守护程序方案使用 OAuth2.0 [客户端凭据流](v2-oauth2-client-creds-grant-flow.md)。 它们也称为服务到服务调用。 应用代表自身获取令牌，而非代表用户。

#### <a name="find-out-if-your-code-uses-daemon-scenarios"></a>查明代码是否使用守护程序方案

如果应用包含以下参数的调用，则该应用的 ADAL 代码使用守护程序方案 `AuthenticationContext.AcquireTokenAsync`：

- 资源（应用 ID URI），作为第一个参数
- `IClientAssertionCertificate` 或 `ClientAssertion`，作为第二个参数

`AuthenticationContext.AcquireTokenAsync` 没有类型为 `UserAssertion` 的参数。 反之，应用则是 Web API，并且使用[调用下游 Web API 的 Web API](?tabs=obo#migrate-a-web-api-that-calls-downstream-web-apis) 方案。

#### <a name="update-the-code-of-daemon-scenarios"></a>更新守护程序方案的代码

[!INCLUDE [Common steps](includes/msal-net-adoption-steps-confidential-clients.md)]

在这种情况下，我们使用对 `IConfidentialClientApplication.AcquireTokenClient` 的调用替换对 `AuthenticationContext.AcquireTokenAsync` 的调用。

下面是对守护程序方案的 ADAL.NET 和 MSAL.NET 代码的比较：

:::row:::
:::column span="":::
    ADAL
:::column-end:::
:::column span="":::
    MSAL
:::column-end:::
:::row-end:::
:::row:::
:::column span="":::
      
```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Security.Cryptography.X509Certificates;
using System.Threading.Tasks;

public partial class AuthWrapper
{
 const string ClientId = "Guid (AppID)";
 const string authority 
   = "https://login.microsoftonline.com/{tenant}";
 // App ID URI of web API to call
 const string resourceId = "https://target-api.domain.com";
 X509Certificate2 certificate = LoadCertificate();



 public async Task<AuthenticationResult> GetAuthenticationResult()
 {


  var authContext = new AuthenticationContext(authority);
  var clientAssertionCert = new ClientAssertionCertificate(
                                  ClientId,
                                  certificate);


  var authResult = await authContext.AcquireTokenAsync(
                                      resourceId,
                                      clientAssertionCert,
                                );

  return authResult;
 }
}
```
:::column-end:::   
:::column span="":::
```csharp
using Microsoft.Identity.Client;
using System.Security.Cryptography.X509Certificates;
using System.Threading.Tasks;

public partial class AuthWrapper
{
 const string ClientId = "Guid (Application ID)";
 const string authority 
   = "https://login.microsoftonline.com/{tenant}";
 // App ID URI of web API to call
 const string resourceId = "https://target-api.domain.com";
 X509Certificate2 certificate = LoadCertificate();

 IConfidentialClientApplication app;

 public async Task<AuthenticationResult> GetAuthenticationResult()
 {
  if (app == null)
  {
   app = ConfidentialClientApplicationBuilder.Create(ClientId)
           .WithCertificate(certificate)
           .WithAuthority(authority)
           .Build();
  }

  var authResult = await app.AcquireTokenForClient(
              new [] { $"{resourceId}/.default" })
              .ExecuteAsync()
              .ConfigureAwait(false);

  return authResult;
 }
}
```
   :::column-end:::
:::row-end:::

#### <a name="benefit-from-token-caching"></a>从令牌缓存中获益

若要从内存缓存中获益，`IConfidentialClientApplication` 的实例需要保留在成员变量中。 如果在每次请求令牌时重新创建机密客户端应用程序，则不会从令牌缓存中获益。

如果选择不使用默认的内存中应用令牌缓存，则需要对 `AppTokenCache` 进行序列化。 同样，如果想要实现分布式令牌缓存，则需要序列化 `AppTokenCache`。 有关详细信息，请参阅 [Web 应用或 Web API 的令牌缓存（机密客户端应用程序）](msal-net-token-cache-serialization.md?tabs=aspnet)和 [active-directory-dotnet-v1-to-v2/ConfidentialClientTokenCache](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2/tree/master/ConfidentialClientTokenCache) 示例。

[详细了解守护程序方案](scenario-daemon-overview.md)及其在新应用程序中通过 MSAL.NET 或 Microsoft.Identity.Web 实现的方式。

## <a name="web-api-calling-downstream-web-apis"></a>[调用下游 Web API 的 Web API](#tab/obo)

### <a name="migrate-a-web-api-that-calls-downstream-web-apis"></a>迁移一个用于调用下游 Web API 的 Web API

调用下游 Web API 的 Web API 使用 OAuth2.0 [代表 (OBO)](v2-oauth2-on-behalf-of-flow.md) 流。 Web API 的代码使用从 HTTP 授权标头中检索到的令牌并对其进行验证。 此令牌与另一个令牌交换以调用下游 Web API。 此令牌在 ADAL.NET 和 MSAL.NET 中均用作 `UserAssertion` 实例。

#### <a name="find-out-if-your-code-uses-obo"></a>查明代码是否使用 OBO

如果应用包含使用以下参数的 `AuthenticationContext.AcquireTokenAsync` 调用，则应用的 ADAL 代码则使用了 OBO：

- 资源（应用 ID URI），作为第一个参数
- `IClientAssertionCertificate` 或 `ClientAssertion`，作为第二个参数
- 类型为 `UserAssertion` 的参数

#### <a name="update-the-code-by-using-obo"></a>使用 OBO 更新代码

[!INCLUDE [Common steps](includes/msal-net-adoption-steps-confidential-clients.md)]

在这种情况下，我们使用对 `IConfidentialClientApplication.AcquireTokenOnBehalfOf` 的调用替换对 `AuthenticationContext.AcquireTokenAsync` 的调用。

下面是对 ADAL.NET 和 MSAL.NET 的示例 OBO 代码的比较：

:::row:::
   :::column span="":::
      ADAL
   :::column-end:::
   :::column span="":::
      MSAL
   :::column-end:::
:::row-end:::
:::row:::
:::column span="":::
```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Security.Cryptography.X509Certificates;
using System.Threading.Tasks;

public partial class AuthWrapper
{
 const string ClientId = "Guid (AppID)";
  const string authority 
  = "https://login.microsoftonline.com/common";
 X509Certificate2 certificate = LoadCertificate();



 public async Task<AuthenticationResult> GetAuthenticationResult(
  string resourceId, 
  string tokenUsedToCallTheWebApi)
 {


  var authContext = new AuthenticationContext(authority);
  var clientAssertionCert = new ClientAssertionCertificate(
                                  ClientId,
                                  certificate);



  var userAssertion = new UserAssertion(tokenUsedToCallTheWebApi);

  var authResult = await authContext.AcquireTokenAsync(
                                      resourceId,
                                      clientAssertionCert,
                                      userAssertion,
                                );

  return authResult;
 }
}
```
:::column-end:::
:::column span="":::
```csharp
using Microsoft.Identity.Client;
using System.Security.Cryptography.X509Certificates;
using System.Threading.Tasks;

public partial class AuthWrapper
{
 const string ClientId = "Guid (Application ID)";
  const string authority 
  = "https://login.microsoftonline.com/common";
 X509Certificate2 certificate = LoadCertificate();

 IConfidentialClientApplication app;

 public async Task<AuthenticationResult> GetAuthenticationResult(
  string resourceId,
  string tokenUsedToCallTheWebApi)
 {
  if (app == null)
  {
   app = ConfidentialClientApplicationBuilder.Create(ClientId)
           .WithCertificate(certificate)
           .WithAuthority(authority)
           .Build();
  }


  var userAssertion = new UserAssertion(tokenUsedToCallTheWebApi);

  var authResult = await app.AcquireTokenOnBehalfOf(
              new string[] { $"{resourceId}/.default" },
              userAssertion)
              .ExecuteAsync()
              .ConfigureAwait(false);
  
  return authResult;
 }
}
```
:::column-end:::
:::row-end:::

#### <a name="benefit-from-token-caching"></a>从令牌缓存中获益

对于在 OBO 中进行令牌缓存，需要使用分布式的。 有关详细信息，请参阅 [Web 应用或 Web API 的令牌缓存（机密客户端应用程序）](msal-net-token-cache-serialization.md?tabs=aspnet)并通读[示例代码](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2/tree/master/ConfidentialClientTokenCache)。

```CSharp
app.UseInMemoryTokenCaches(); // or a distributed token cache.
```

[详细了解调用下游 Web API 的 Web API](scenario-web-api-call-api-overview.md) 及其在新应用程序中通过 MSAL.NET 或 Microsoft.Identity.Web 实现的方式。

## <a name="web-app-calling-web-apis"></a>[调用 Web API 的 Web 应用](#tab/authcode)

### <a name="migrate-a-web-app-that-calls-web-apis"></a>迁移用于调用 Web API 的 Web 应用

如果应用使用 ASP.NET Core，强烈建议将其更新为 Microsoft.Identity.Web，它将为你处理一切。 如需快速演示，请参阅 [Microsoft.Identity.Web 正式发布公告](https://github.com/AzureAD/microsoft-identity-web/wiki/1.0.0)。 有关如何在 Web 应用中使用它的详细信息，请参阅[为什么在 Web 应用中使用 Microsoft.Identity.Web？](https://aka.ms/ms-id-web/webapp)。

可让用户登录并代表用户调用 Web API 的 Web 应用使用 OAuth2.0 [授权代码流](v2-oauth2-auth-code-flow.md)。 通常而言：

1. Web 应用通过执行授权代码流的第一段来让用户登录。 其执行方式通过转到 Azure Active Directory (Azure AD) 中的授权终结点。 如果需要，用户登录并执行多重身份验证。 此操作的结果是，应用将接收授权代码。 到目前为止，不涉及 ADAL 和 MSAL。
2. 应用执行授权代码流的第二段。 它使用授权代码来获取访问令牌、ID 令牌和刷新令牌。 应用程序需要提供 `redirectUri` 值，该值是 Azure AD 将为其提供安全令牌的 URI。 应用接收到该 URI 后，通常会调用 ADAL 或 MSAL 的 `AcquireTokenByAuthorizationCode` 来兑换代码，并获取令牌，该令牌将存储在令牌缓存中。
3. 应用使用 ADAL 或 MSAL 调用 `AcquireTokenSilent`，这样便可以获取用于调用所需 Web API 的令牌。 这会通过 Web 应用控制器完成。

#### <a name="find-out-if-your-code-uses-the-auth-code-flow"></a>查明代码是否使用身份验证代码流

如果应用包含对 `AuthenticationContext.AcquireTokenByAuthorizationCodeAsync` 的调用，则该应用的 ADAL 代码将使用身份验证代码流。

#### <a name="update-the-code-by-using-the-authorization-code-flow"></a>使用授权代码流更新代码

[!INCLUDE [Common steps](includes/msal-net-adoption-steps-confidential-clients.md)] 

在这种情况下，我们使用对 `IConfidentialClientApplication.AcquireTokenByAuthorizationCode` 的调用替换对 `AuthenticationContext.AcquireTokenAsync` 的调用。

下面是 ADAL.NET 和 MSAL.NET 的示例授权代码流的比较：

:::row:::
   :::column span="":::
      ADAL
   :::column-end:::
   :::column span="":::
      MSAL
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
```csharp
public partial class AuthWrapper
{
 const string ClientId = "Guid (AppID)";
 const string authority 
     = "https://login.microsoftonline.com/common";
 private Uri redirectUri = new Uri("host/login_oidc");
 X509Certificate2 certificate = LoadCertificate();



 public async Task<AuthenticationResult> GetAuthenticationResult(
  string resourceId,
  string authorizationCode)
 {


  var ac = new AuthenticationContext(authority);
  var clientAssertionCert = new ClientAssertionCertificate(
                                  ClientId,
                                  certificate);



  var authResult = await ac.AcquireTokenByAuthorizationCodeAsync(
                                      authorizationCode,
                                      redirectUri,
                                      clientAssertionCert,
                                      resourceId,
                                );
  return authResult;
 }
}
```      
   :::column-end:::
   :::column span="":::
```csharp
public partial class AuthWrapper
{
 const string ClientId = "Guid (Application ID)";
 const string authority 
     = "https://login.microsoftonline.com/{tenant}";
 private Uri redirectUri = new Uri("host/login_oidc");
 X509Certificate2 certificate = LoadCertificate();

 IConfidentialClientApplication app;

 public async Task<AuthenticationResult> GetAuthenticationResult(
  string resourceId,
  string authorizationCode)
 {
  if (app == null)
  {
   app = ConfidentialClientApplicationBuilder.Create(ClientId)
           .WithCertificate(certificate)
           .WithAuthority(authority)
           .WithRedirectUri(redirectUri.ToString())
           .Build();
  }

  var authResult = await app.AcquireTokenByAuthorizationCode(
              new [] { $"{resourceId}/.default" },
              authorizationCode)
              .ExecuteAsync()
              .ConfigureAwait(false);

  return authResult;
 }
}
```
   :::column-end:::
:::row-end:::

调用 `AcquireTokenByAuthorizationCode` 可向令牌缓存添加令牌。 若要获取其他资源或租户的额外令牌，请在控制器中使用 `AcquireTokenSilent`。

#### <a name="benefit-from-token-caching"></a>从令牌缓存中获益

由于 Web 应用使用 `AcquireTokenByAuthorizationCode`，因此应用需要使用分布式令牌缓存进行令牌缓存。 有关详细信息，请参阅 [Web 应用或 Web API 的令牌缓存](msal-net-token-cache-serialization.md?tabs=aspnet)并通读[示例代码](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2/tree/master/ConfidentialClientTokenCache)。


```CSharp
app.UseInMemoryTokenCaches(); // or a distributed token cache.
```


[详细了解调用 Web API 的 Web 应用](scenario-web-app-call-api-overview.md)及其在新应用程序中通过 MSAL.NET 或 Microsoft.Identity.Web 实现的方式。

---

## <a name="msal-benefits"></a>MSAL 优势

对于应用来说，MSAL.NET 的主要优势包括：

- **复原能力**。 MSAL.NET 可通过以下方面帮助应用实现可复原性：

   - Azure AD 缓存凭据服务 (CCS) 优势。 CCS 以 Azure AD 备份的形式操作。
   - 如果调用的 API 通过[连续访问评估](app-resilience-continuous-access-evaluation.md)启用了长效令牌，则主动更新令牌。

- **安全性**。 如果想要调用的 Web API 需要，可以获取所有权证明 (PoP) 令牌。 有关详细信息，请参阅 [MSAL.NET 中的所有权证明令牌](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Proof-Of-Possession-(PoP)-tokens)

- **性能和可伸缩性**。 如果不需要与 ADAL.NET 共享缓存，则在创建机密客户端应用程序时禁用旧缓存兼容性 (`.WithLegacyCacheCompatibility(false)`)。 这会显著提高性能。
  
  ```csharp
  app = ConfidentialClientApplicationBuilder.Create(ClientId)
          .WithCertificate(certificate)
          .WithAuthority(authority)
          .WithLegacyCacheCompatibility(false)
          .Build();
  ```

## <a name="troubleshooting"></a>故障排除

以下故障排除信息提出两种假设： 

- 你的 ADAL.NET 代码正常运行。
- 你通过保留同一客户端 ID 迁移到 MSAL。

如果通过以下任一消息遇到异常： 

> `AADSTS700027: Client assertion contains an invalid signature. [Reason - The key was not found.]`

> `AADSTS90002: Tenant 'cf61953b-e41a-46b3-b500-663d279ea744' not found. This may happen if there are no active`
> `subscriptions for the tenant. Check to make sure you have the correct tenant ID. Check with your subscription`
> `administrator.`

可以使用以下步骤对异常进行故障排除：

1. 确认使用的是最新版本的 MSAL.NET。
1. 确认在生产机密客户端应用程序时设置的授权主机与使用 ADAL 时使用的授权主机类似。 具体而言，这是否为同一种[云](msal-national-cloud.md)（Azure 政府、Azure 中国世纪互联或 Azure 德国）？

## <a name="next-steps"></a>后续步骤

详细了解 [ADAL.NET 与 MSAL.NET 应用之间的差异](msal-net-differences-adal-net.md)。
详细了解 [MSAL.NET 中的令牌缓存序列化](msal-net-token-cache-serialization.md)