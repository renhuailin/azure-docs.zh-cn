---
title: 实例化公共客户端应用 (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: 了解如何通过适用于 .NET 的 Microsoft 身份验证库 (MSAL.NET) 使用配置选项实例化公共客户端应用程序。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 04/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 68f4437ce75bfe2a9017133ed523bb5e9ce10a8c
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124787123"
---
# <a name="instantiate-a-public-client-application-with-configuration-options-using-msalnet"></a>通过 MSAL.NET 使用配置选项实例化公共客户端应用程序

本文介绍如何使用适用于 .NET 的 Microsoft 身份验证库 (MSAL.NET) 实例化[公共客户端应用程序](msal-client-applications.md)。  应用程序使用设置文件中定义的配置选项进行实例化。

在初始化应用程序之前，首先需要[注册](quickstart-register-app.md)它，以便应用可以与 Microsoft 标识平台集成。 注册后，可能需要以下信息（可在 Azure 门户中找到）：

- 客户端 ID（表示 GUID 的字符串）
- 标识提供者 URL（为实例命名）和应用程序的登录受众。 这两个参数统称为颁发机构。
- 如果你仅在为组织编写业务线应用程序（也称为单租户应用程序），则为租户 ID。
- 对于 Web 应用，有时对于公共客户端应用（特别是当你的应用需要使用中转站时），还将需要设置 redirectUri，标识提供者将在其中使用安全令牌联系你的应用程序。

## <a name="default-reply-uri"></a>默认回复 URI

在 MSAL.NET 4.1+ 中，默认重定向 URI（回复 URI）现在可以通过 `public PublicClientApplicationBuilder WithDefaultRedirectUri()` 方法进行设置。 此方法会将公共客户端应用程序的重定向 URI 属性设置为建议的默认值。

此方法的行为取决于当前使用的平台。 下表描述了在某些平台上设置的重定向 URI：

平台  | 重定向 URI  
---------  | --------------
桌面应用 (.NET FW) | `https://login.microsoftonline.com/common/oauth2/nativeclient` 
UWP | `WebAuthenticationBroker.GetCurrentApplicationCallbackUri()` 的值
.NET Core | `http://localhost`

对于 UWP 平台，增强体验的方式是通过将值设置为 `WebAuthenticationBroker.GetCurrentApplicationCallbackUri()` 的结果，使用浏览器来启用 SSO。 

对于 .NET Core，MSAL.Net 会将值设置为本地主机，使用户能够使用系统浏览器进行交互式身份验证。

> [!NOTE]
> 对于桌面方案中的嵌入式浏览器，MSAL 会截获使用的重定向 URI，以检测是否从标识提供者处返回了一个响应（即是否已返回授权代码）。 因此，可以在任何云中使用此 URI，而不会看到到该 URI 的实际重定向。 这意味着你可以并且应该在任何云中使用 `https://login.microsoftonline.com/common/oauth2/nativeclient`。 如果你愿意，你也可以使用任何其他 URI，前提是在 MSAL 和应用注册中正确配置重定向 URI。 在应用程序注册中指定默认 URI 意味着 MSAL 中的设置量会最少。


.NET Core 控制台应用程序可以具有以下 *appsettings.json* 配置文件：

```json
{
  "Authentication": {
    "AzureCloudInstance": "AzurePublic",
    "AadAuthorityAudience": "AzureAdMultipleOrgs",
    "ClientId": "ebe2ab4d-12b3-4446-8480-5c3828d04c50"
  },

  "WebAPI": {
    "MicrosoftGraphBaseEndpoint": "https://graph.microsoft.com"
  }
}
```

以下代码使用 .NET 配置框架读取此文件：

```csharp
public class SampleConfiguration
{
    /// <summary>
    /// Authentication options
    /// </summary>
    public PublicClientApplicationOptions PublicClientApplicationOptions { get; set; }

    /// <summary>
    /// Base URL for Microsoft Graph (it varies depending on whether the application is ran
    /// in Microsoft Azure public clouds or national / sovereign clouds
    /// </summary>
    public string MicrosoftGraphBaseEndpoint { get; set; }

    /// <summary>
    /// Reads the configuration from a json file
    /// </summary>
    /// <param name="path">Path to the configuration json file</param>
    /// <returns>SampleConfiguration as read from the json file</returns>
    public static SampleConfiguration ReadFromJsonFile(string path)
    {
        // .NET configuration
        IConfigurationRoot Configuration;
        var builder = new ConfigurationBuilder()
          .SetBasePath(Directory.GetCurrentDirectory())
        .AddJsonFile(path);
        Configuration = builder.Build();

        // Read the auth and graph endpoint config
        SampleConfiguration config = new SampleConfiguration()
        {
            PublicClientApplicationOptions = new PublicClientApplicationOptions()
        };
        Configuration.Bind("Authentication", config.PublicClientApplicationOptions);
        config.MicrosoftGraphBaseEndpoint = Configuration.GetValue<string>("WebAPI:MicrosoftGraphBaseEndpoint");
        return config;
    }
}
```

以下代码使用设置文件中的配置创建应用程序：

```csharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .Build();
```

