---
title: ADAL.NET 与 MSAL.NET 应用之间的差异 | Azure
titleSuffix: Microsoft identity platform
description: 了解适用于 .NET 的 Microsoft 身份验证库 (MSAL.NET) 与适用于 .NET 的 Azure AD 身份验证库 (ADAL.NET) 之间的差异。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 06/09/2021
ms.author: jmprieur
ms.reviewer: saeeda, shermanouko
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: ea83d0098eaa44ffa6510a5f0fc8cea18115815c
ms.sourcegitcommit: cd8e78a9e64736e1a03fb1861d19b51c540444ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2021
ms.locfileid: "112970252"
---
# <a name="differences-between-adalnet-and-msalnet-apps"></a>ADAL.NET 与 MSAL.NET 应用之间的差异

将应用程序从使用 ADAL 迁移到使用 MSAL 可以提高安全性和复原能力。 本文概述了 MSAL.NET 与 ADAL.NET 之间的差异。 在大多数情况下，都可以使用 MSAL.NET 和 Microsoft 标识平台，这是最新一代的 Microsoft 身份验证库。 使用 MSAL.NET 可以获取通过 Azure AD（工作和学校帐户）、Microsoft（个人）帐户 (MSA) 或 Azure AD B2C 登录到应用程序的用户的令牌。

如果你已熟悉面向开发人员的 ADAL.NET 和 Azure AD (v1.0) 终结点，请阅读 [Microsoft 标识平台有何不同？](../azuread-dev/azure-ad-endpoint-comparison.md)。 如果应用程序需要使用早期版本的 [Active Directory 联合身份验证服务 (ADFS)](/windows-server/identity/active-directory-federation-services) 将用户登录，则你仍然需要使用 ADAL.NET。 有关详细信息，请参阅 [ADFS 支持](https://aka.ms/msal-net-adfs-support)。

|   | **ADAL NET**  | **MSAL NET**  |
|-----------------------------|--------------------------------------------|---------------------|
| **NuGet 包和命名空间**  |ADAL.NET 是从 [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) NuGet 包使用的。 命名空间为 `Microsoft.IdentityModel.Clients.ActiveDirectory`。  | 添加 [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) NuGet 包，并使用 `Microsoft.Identity.Client` 命名空间。 如果要生成机密客户端应用程序，请参阅 [Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web)。 |
| **范围和资源**  | ADAL.NET 获取资源的令牌。 |  MSAL.NET 获取资源的范围。 多个 MSAL.NET `AcquireTokenXXX` 替代需要一个范围参数 (`IEnumerable<string> scopes`)。 此参数是一个简单的字符串列表，这些字符串声明请求的权限和资源。 已知的范围是 [Microsoft Graph 范围](/graph/permissions-reference)。 还可[使用 MSAL.NET 访问 v1.0 资源](#scopes)。 |
| **核心类**  | ADAL.NET 使用 [AuthenticationContext](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AuthenticationContext:-the-connection-to-Azure-AD) 来表示通过颁发机构与安全令牌服务 (STS) 或授权服务器建立的连接。 | MSAL.NET 是围绕[客户端应用程序](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Client-Applications)设计的。 它定义了面向公共客户端应用程序的 `IPublicClientApplication` 接口和面向机密客户端应用程序的 `IConfidentialClientApplication`接口，以及面向这两种类型应用程序的通用协定的基接口 `IClientApplicationBase`。|
| **令牌获取**  | 在公共客户端中，ADAL 使用 `AcquireTokenAsync` 和 `AcquireTokenSilentAsync` 进行身份验证调用。 | 在公共客户端中，MSAL 使用 `AcquireTokenInteractive` 和 `AcquireTokenSilent` 进行相同的身份验证调用。 参数不同于 ADAL 参数。 <br><br>在机密客户端应用程序中，存在具有显式名称的[令牌获取方法](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Acquiring-Tokens)，具体取决于方案。 不同之处在于，在 MSAL.NET 中，不再需要在每个 AcquireTokenXX 调用中传入应用程序的 `ClientID`。 只需在生成 `IPublicClientApplication` 或 `IConfidentialClientApplication` 时设置 `ClientID` 一次。|
|  **IAccount 和 IUser**  | ADAL 通过 IUser 接口定义用户的概念。 但用户是人或软件代理。 因此，一个用户可以拥有一个或多个 Microsoft 标识平台帐户（多个 Azure AD 帐户、Azure AD B2C、Microsoft 个人帐户）。 该用户还可以负责一个或多个 Microsoft 标识平台帐户。 | MSAL.NET 通过 IAccount 接口定义帐户的概念。 IAccount 接口表示单个帐户的相关信息。 用户可以在不同的租户中拥有多个帐户。 由于已经提供了主帐户信息，因此 MSAL.NET 可以在来宾方案中提供更有用的信息。 有关详细信息，请参阅 [IUser 与 IAccount 之间的差异](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/msal-net-2-released#iuser-is-replaced-by-iaccount)。|
|  **缓存持久性**  | ADAL.NET 允许使用 `BeforeAccess` 和 `BeforeWrite` 方法扩展 `TokenCache` 类，以便在没有安全存储的平台（.NET Framework 和.NET Core）上实现所需的持久性功能。 有关详细信息，请参阅 [ADAL.NET 中的令牌缓存序列化](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization)。 | MSAL.NET 将令牌缓存用作密封类，并消除了扩展该类的功能。 因此，令牌缓存持久性的实现必须采用与密封令牌缓存交互的帮助程序类的形式。 [MSAL.NET 中的令牌缓存序列化](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization)中介绍了这种交互。 公共客户端应用程序的序列化（请参阅[适用于公共客户端应用程序的令牌缓存](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization#token-cache-for-a-public-client-application)）和机密客户端应用程序（请参阅[适用于 Web 应用或 Web API 的令牌缓存](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization#token-cache-for-a-public-client-application)）的序列化将有所不同。 |
| **通用颁发机构** | ADAL 使用 Azure AD v1.0。 Azure AD v1.0 中的 `https://login.microsoftonline.com/common` 颁发机构（ADAL 使用的颁发机构）允许用户使用任何 AAD 组织帐户（工作或学校帐户）登录。 Azure AD v1.0 不允许使用 Microsoft 个人帐户登录。 有关详细信息，请参阅 [ADAL.NET 中的颁发机构验证](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AuthenticationContext:-the-connection-to-Azure-AD#authority-validation)。 | MSAL 使用 Azure AD v2.0。 Azure AD v2.0 中的 `https://login.microsoftonline.com/common` 颁发机构允许用户使用任何 AAD 组织（工作或学校）帐户或 Microsoft 个人帐户登录。 若要限制仅在 MSAL 中使用组织帐户（工作或学校帐户）登录，需要使用 `https://login.microsoftonline.com/organizations` 终结点。  有关详细信息，请参阅[公共客户端应用程序](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Client-Applications#publicclientapplication)中的 `authority` 参数。 |

## <a name="supported-grants"></a>支持的授权

下面汇总了公共和机密客户端应用程序的 MSAL.NET 和 ADAL.NET 支持的授权比较。

### <a name="public-client-applications"></a>公共客户端应用程序

下图汇总了在公共客户端应用程序上 ADAL.NET 与 MSAL.NET 之间的一些差异。

[![屏幕截图显示了在公共客户端应用程序上 ADAL.NET 与 MSAL.NET 之间的一些差异。](media/msal-compare-msaldotnet-and-adaldotnet/differences.png)](media/msal-compare-msaldotnet-and-adaldotnet/differences.png#lightbox)

下面是适用于桌面和移动应用程序的 ADAL.NET 和 MSAL.NET 支持的授权。

授予                             | MSAL.NET                                                                                                                     | ADAL.NET                                                                                                                                                                                                   |
--------------------------------- | ---------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
交互                       | [在 MSAL.NET 中以交互方式获取令牌](scenario-desktop-acquire-token.md#acquire-a-token-interactively)    | [交互式身份验证](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-interactively---Public-client-application-flows)                                              |
Windows 集成身份验证 | [Windows 集成身份验证](scenario-desktop-acquire-token.md#integrated-windows-authentication)         | [Windows 上的集成身份验证 (Kerberos)](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-Integrated-authentication-on-Windows-(Kerberos))  |
用户名/密码               | [用户名/密码身份验证](scenario-desktop-acquire-token.md#username-and-password)                      | [使用用户名和密码获取令牌](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-with-username-and-password)                                        |
设备代码流                  | [设备代码流](scenario-desktop-acquire-token.md#command-line-tool-without-a-web-browser)                    | [没有 Web 浏览器的设备的设备配置文件](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Device-profile-for-devices-without-web-browsers)                                |

### <a name="confidential-client-applications"></a>机密客户端应用程序

下图汇总了在机密客户端应用程序上 ADAL.NET 与 MSAL.NET 之间的一些差异。
 
[![屏幕截图显示了在机密客户端应用程序上 ADAL.NET 与 MSAL.NET 之间的一些差异。](media/msal-net-migration/confidential-client-application.png)](media/msal-net-migration/confidential-client-application.png#lightbox)


下面是适用于 Web 应用程序、Web API 和守护程序应用程序的 ADAL.NET、MSAL.NET 和 Microsoft.Identity.Web 支持的授权。

应用类型              | 授予              | MSAL.NET                                                                                                                             | ADAL.NET                                                                                                                                                                                              |
------------------------ | ------------------ | ------------------------------------------------------------------------------------------------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
Web 应用、Web API、守护程序 | 客户端凭据 | [MSAL.NET 中的客户端凭据流](scenario-daemon-acquire-token.md#acquiretokenforclient-api)                        | [ADAL.NET 中的客户端凭据流](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Client-credential-flows)                                                               |
Web API                  | 代表       | [在 MSAL.NET 中代表](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/on-behalf-of)                 | [代表用户使用 ADAL.NET 进行服务到服务的调用](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Service-to-service-calls-on-behalf-of-the-user)               |
Web 应用                  | 身份验证代码          | [使用 MSAL.NET 通过 Web 应用中的授权代码获取令牌](scenario-web-app-call-api-acquire-token.md)  | [使用 ADAL.NET 通过 Web 应用中的授权代码获取令牌](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-with-authorization-codes-on-web-apps) |

## <a name="migrating-from-adal-2x-with-refresh-tokens"></a>使用刷新令牌从 ADAL 2.x 迁移

ADAL.NET v2.X 中公开了刷新令牌，使你能够通过缓存这些令牌并使用 ADAL 2.x 提供的 `AcquireTokenByRefreshToken` 方法，来围绕这些令牌的使用开发解决方案。

其中的某些解决方案在如下所述的场景中使用：

- 长期服务，即当用户不再保持连接/不再登录到应用时，代表用户执行刷新仪表板等操作。
- WebFarm 方案，允许客户端将刷新令牌带入 Web 服务（高速缓存是在客户端，而不是在服务器端完成，而且 cookie 经过加密）。

出于安全原因，MSAL.NET 不公开刷新令牌。 MSAL 会为你处理刷新令牌。

幸运的是，MSAL.NET 现在会提供一个 API，可将以前的刷新令牌（通过 ADAL 获取）迁移到 `IConfidentialClientApplication` 中：

```csharp
/// <summary>
/// Acquires an access token from an existing refresh token and stores it and the refresh token into
/// the application user token cache, where it will be available for further AcquireTokenSilent calls.
/// This method can be used in migration to MSAL from ADAL v2 and in various integration
/// scenarios where you have a RefreshToken available.
/// (see https://aka.ms/msal-net-migration-adal2-msal2)
/// </summary>
/// <param name="scopes">Scope to request from the token endpoint.
/// Setting this to null or empty will request an access token, refresh token and ID token with default scopes</param>
/// <param name="refreshToken">The refresh token from ADAL 2.x</param>
IByRefreshToken.AcquireTokenByRefreshToken(IEnumerable<string> scopes, string refreshToken);
```

使用此方法，你可以提供以前用过的刷新令牌以及所需的任何范围（资源）。 该刷新令牌将交换一个新令牌，并缓存到应用程序中。

由于此方法适用于非典型的场景，因此，必须先将 `IConfidentialClientApplication` 强制转换为 `IByRefreshToken`，然后才能使用它来访问刷新令牌。

以下代码片段演示了机密客户端应用程序中的一些迁移代码。

```csharp
TokenCache userCache = GetTokenCacheForSignedInUser();
string rt = GetCachedRefreshTokenForSignedInUser();

IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.Create(clientId)
 .WithAuthority(Authority)
 .WithRedirectUri(RedirectUri)
 .WithClientSecret(ClientSecret)
 .Build();
IByRefreshToken appRt = app as IByRefreshToken;

AuthenticationResult result = await appRt.AcquireTokenByRefreshToken(null, rt)
                                         .ExecuteAsync()
                                         .ConfigureAwait(false);
```

`GetCachedRefreshTokenForSignedInUser` 检索利用 ADAL 2.x 的某个旧版应用程序存储在某个存储中的刷新令牌。 `GetTokenCacheForSignedInUser` 反序列化已登录用户的缓存（因为机密客户端应用程序应该为每个用户配置一个缓存）。

在缓存中存储新的刷新令牌时，`AuthenticationResult` 值中将返回访问令牌和 ID 令牌。 对于可提供刷新令牌的各种集成方案，也可以使用此方法。

## <a name="v10-and-v20-tokens"></a>v1.0 和 v2.0 令牌

有两种令牌版本： v1.0 令牌和 v2.0 令牌。 ADAL 使用的 v1.0 终结点发出 v1.0 ID 令牌，而 MSAL 使用的 v2.0 终结点则发出 v2.0 ID 令牌。 但是，这两个终结点都会发出 Web API 接受的令牌版本的访问令牌。 开发人员可以使用 Web API 应用程序清单的属性来选择接受的令牌版本。 请参阅[应用程序清单](reference-app-manifest.md)参考文档中的 `accessTokenAcceptedVersion`。

有关 v1.0 和 v2.0 令牌的详细信息，请参阅 [Azure Active Directory 访问令牌](access-tokens.md)。

## <a name="exceptions"></a>异常

### <a name="interaction-required-exceptions"></a>“需要交互”异常

使用 MSAL.NET 可以根据 [AcquireTokenSilent](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-a-cached-token) 中所述捕获 `MsalUiRequiredException`。

```csharp
catch(MsalUiRequiredException exception)
{
 try {“try to authenticate interactively”}
}
```

有关详细信息，请参阅[处理 MSAL.NET 中的错误和异常](msal-error-handling-dotnet.md)

ADAL.NET 中的显式异常较少。 例如，当 ADAL 中的无提示身份验证失败时，处理过程是捕获异常并查找 `user_interaction_required` 错误代码：

```csharp
catch(AdalException exception)
{
 if (exception.ErrorCode == "user_interaction_required")
 {
  try
  {“try to authenticate interactively”}}
 }
}
```

有关 ADAL.NET 的详细信息，请参阅[在公共客户端应用程序中获取令牌的建议模式](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-a-cached-token#recommended-pattern-to-acquire-a-token)。

### <a name="handling-claim-challenge-exceptions"></a>处理声明质询异常

如果资源需要来自用户的更多声明（例如双因素身份验证），有时 Azure AD 在获取令牌时会引发一个异常。

在 MSAL.NET 中，声明质询异常按以下方式进行处理：

- `Claims` 在 `MsalServiceException` 中显示。
- 可对 `AcquireTokenXXX` 生成器应用 `.WithClaim(claims)` 方法。

有关详细信息，请参阅 [处理 MsalUiRequiredException](msal-error-handling-dotnet.md#msaluirequiredexception)。

在 ADAL.NET 中，声明质询异常按以下方式进行处理：

- `AdalClaimChallengeException` 是派生自 `AdalServiceException` 的异常。 `Claims` 成员包含某个带有预期声明的 JSON 片段。 
- 接收此异常的公共客户端应用程序需要调用具有声明参数的 `AcquireTokenInteractive` 替代。 此 `AcquireTokenInteractive` 替代甚至不会尝试命中缓存，因为没有这个必要。 原因在于，缓存中的令牌没有适当的声明（否则不会引发 `AdalClaimChallengeException`）。 因此，无需查看缓存。 请注意，可以在执行 OBO 的 Web API 中接收 `ClaimChallengeException`，但需要在调用此 Web API 的公共客户端应用程序中调用 `AcquireTokenInteractive`。

有关详细信息（包括示例），请参阅[处理 AdalClaimChallengeException](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Exceptions-in-ADAL.NET#handling-adalclaimchallengeexception)。


## <a name="scopes"></a>作用域

ADAL 使用包含 `resourceId` 字符串的资源概念，而 MSAL.NET 使用范围。 下面是 Azure AD 使用的逻辑：

- 对于使用 v1.0 访问令牌（只能使用此类令牌）的 ADAL (v1.0) 终结点，`aud=resource`。
- 对于要求资源访问令牌接受 v2.0 令牌的 MSAL（v2.0 终结点），`aud=resource.AppId`。
- 对于要求资源访问令牌接受 v1.0 令牌的 MSAL（v2.0 终结点），Azure AD 将分析请求的范围中的所需受众。 方法是提取最后一个斜杠前面的所有内容并将其用作资源标识符。 因此，如果 `https://database.windows.net` 预期受众为 `https://database.windows.net/`，则需要请求一个范围 `https://database.windows.net//.default`，（请注意在 ./default 之前使用双斜杠）。 以下示例 1 和 2 对此进行了说明。

### <a name="example-1"></a>示例 1

若要获取接受 v1.0 令牌的应用程序（例如 Microsoft Graph API 实例，网址为 `https://graph.microsoft.com`）的令牌，则需要将所需的资源标识符与该资源所需的 OAuth2 权限进行连接以创建 `scopes`。

例如，若要通过应用 ID URI 为 `ResourceId` 的 v1.0 Web API 访问用户名，需要使用：

```csharp
var scopes = new [] { ResourceId+"/user_impersonation" };
```

若要使用 Microsoft Graph API (`https://graph.microsoft.com/`) 通过 MSAL.NET Azure Active Directory 进行读取和写入，需要按以下代码片段所示创建范围列表：

```csharp
string ResourceId = "https://graph.microsoft.com/"; 
string[] scopes = { ResourceId + "Directory.Read", ResourceId + "Directory.Write" }
```

### <a name="example-2"></a>示例 2

如果 resourceId 以“/”结尾，则需要在写入范围值时添加两个斜杠“//”。 若要写入对应于 Azure 资源管理器 API (`https://management.core.windows.net/`) 的范围，请求以下范围（请注意有两个斜杠）。

```csharp
var resource = "https://management.core.windows.net/"
var scopes = new[] {"https://management.core.windows.net//user_impersonation"};
var result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

// then call the API: https://management.azure.com/subscriptions?api-version=2016-09-01
```

这是因为，资源管理器 API 要求在其受众声明 (`aud`) 中使用一个斜杠，然后再使用一个斜杠来分隔 API 名称与范围。

如果要获取 v1.0 应用程序的所有静态范围的令牌，请创建范围列表，如以下代码片段所示：

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] { ResourceId+"/.default" };
```

使用客户端凭据流时，要传递的范围也是 `/.default`。 此范围会让 Azure AD 知道管理员在应用程序注册中许可的所有应用级权限。

## <a name="next-steps"></a>后续步骤

[将应用从 ADAL 迁移到 MSAL](msal-net-migration.md)
[迁移 ADAL.NET 机密客户端应用以使用 MSAL.NET](msal-net-migration-confidential-client.md)
