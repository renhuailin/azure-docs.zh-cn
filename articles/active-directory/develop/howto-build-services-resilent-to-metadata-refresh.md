---
title: 如何：构建可复原 Azure AD 的 OpenID Connect 元数据刷新的服务 | Azure
titleSuffix: Microsoft identity platform
description: 了解如何确保你的 Web 应用或 Web api 可复原 Azure AD 的 OpenID Connect 元数据刷新。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 04/21/2021
ms.author: jmprieur
ms.reviewer: marsma, shermanouko
ms.custom: aaddev
ms.openlocfilehash: 8eb9db4d89f6d3c517afb57d6343387343ac5325
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2021
ms.locfileid: "108169155"
---
# <a name="how-to-build-services-that-are-resilient-to-azure-ads-openid-connect-metadata-refresh"></a>如何：构建可复原 Azure AD 的 OpenID Connect 元数据刷新的服务

受保护的 Web API 需要验证访问令牌。 Web 应用还要验证 ID 令牌。 令牌验证包含多个部分：检查令牌是否属于应用程序、是否由受信赖的标识提供者 (IDP) 核发以及其生存期是否仍处于有效范围内且未经篡改。 此外还可以执行特殊验证。 例如，应用需要（在嵌入令牌时）验证签名和签名密钥是否受信任，以及令牌是否未经重放。 如果签名密钥未嵌入令牌中，则需要向标识提供者提取（发现或元数据）。 有时，还需要在运行时动态获取密钥。

Web 应用和 Web API 需要刷新过时的 OpenID Connect 元数据以使其具有复原能力。 本文可帮助指导如何实现复原性应用。 其适用于 ASP.NET Core、ASP.NET 经典版和 System.identitymodel。

## <a name="aspnet-core"></a>ASP.NET Core

使用最新版本的[Microsoft.IdentityModel.*](https://www.nuget.org/packages?q=Microsoft.IdentityModel)，并根据下方指导手动操作。

采用 Startup.cs 的 `ConfigureServices` 方法时，请确保将 `JwtBearerOptions.RefreshOnIssuerKeyNotFound` 设置为 true，并且使用最新的.Microsoft.IdentityModel.* 库。 系统默认启用此属性。

```csharp
  services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, options =>
  {
    …
    // shouldn’t be necessary as it’s true by default
    options.RefreshOnIssuerKeyNotFound = true;
    …
   };
```

## <a name="aspnet-owin"></a>ASP.NET/OWIN

Microsoft 建议你移至 ASP.NET Core，因为 ASP.NET 上的开发已停止。 

如果使用的是 ASP.NET 经典版，请使用最新的[Microsoft.IdentityModel.*](https://www.nuget.org/packages?q=Microsoft.IdentityModel)。

OWIN 自动刷新 `OpenIdConnectConfiguration` 的时间间隔为 24 小时。 仅当在超过 24 小时时间段后收到请求时，系统才会触发此刷新。 正如我们所知，除重新启动应用程序之外，没有任何方法可以更改此值或提前触发刷新。

## <a name="microsoftidentitymodel"></a>Microsoft.IdentityModel

如果你亲自验证令牌，以在 Azure 函数中为例，请使用最新版本的[Microsoft.IdentityModel.*](https://www.nuget.org/packages?q=Microsoft.IdentityModel)并按照以下代码片段中所示的元数据指导进行操作。

```csharp
ConfigurationManager<OpenIdConnectConfiguration> configManager = 
  new ConfigurationManager<OpenIdConnectConfiguration>("http://someaddress.com", 
                                                       new OpenIdConnectConfigurationRetriever());
OpenIdConnectConfiguration config = await configManager.GetConfigurationAsync().ConfigureAwait(false);
TokenValidationParameters validationParameters = new TokenValidationParameters()
{
  …
  IssuerSigningKeys = config.SigningKeys;
  …
}

JsonWebTokenHandler tokenHandler = new JsonWebTokenHandler();
result = Handler.ValidateToken(jwtToken, validationParameters);
if (result.Exception != null && result.Exception is SecurityTokenSignatureKeyNotFoundException)
{
  configManager.RequestRefresh();
  config = await configManager.GetConfigurationAsync().ConfigureAwait(false);
  validationParameters = new TokenValidationParameters()
  {
    …
    IssuerSigningKeys = config.SigningKeys,
    …
  };
  // attempt to validate token again after refresh
  result = Handler.ValidateToken(jwtToken, validationParameters);
}
```

## <a name="next-steps"></a>后续步骤

若要了解详细信息，请参阅[受保护 Web API 中的令牌验证](scenario-protected-web-api-app-configuration.md#token-validation)
