---
title: 教程 - Web 应用以用户身份访问 Microsoft Graph | Azure
description: 本教程介绍如何访问 Microsoft Graph 中的已登录用户的数据。
services: microsoft-graph, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 09/23/2021
ms.author: ryanwi
ms.reviewer: stsoneff
ms.custom: azureday1
ms.openlocfilehash: 332552d361c4c8c43b7b4bfa981050c829a79762
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128624513"
---
# <a name="tutorial-access-microsoft-graph-from-a-secured-app-as-the-user"></a>教程：以用户身份从安全的应用访问 Microsoft Graph

了解如何从 Azure 应用服务上运行的 Web 应用访问 Microsoft Graph。

:::image type="content" alt-text="显示如何访问 Microsoft Graph 流的示意图。" source="./media/scenario-secure-app-access-microsoft-graph/web-app-access-graph.svg" border="false":::

你希望从 Web 应用添加对 Microsoft Graph 的访问权限，并以已登录用户的身份执行一些操作。 本部分介绍如何向 Web 应用授予委托的权限，以及如何从 Azure Active Directory (Azure AD) 获取已登录用户的配置文件信息。

在本教程中，你将了解：

> [!div class="checklist"]
>
> * 向 Web 应用授予委托的权限。
> * 为已登录的用户从 Web 应用调用 Microsoft Graph。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

* 在[启用了应用服务身份验证/授权模块](scenario-secure-app-authentication-app-service.md)的 Azure 应用服务上运行的 Web 应用程序。

## <a name="grant-front-end-access-to-call-microsoft-graph"></a>授予前端访问权限以调用 Microsoft Graph

现在，你已在 Web 应用上启用了身份验证和授权，该 Web 应用在 Microsoft 标识平台上注册，并由 Azure AD 应用程序提供支持。 在此步骤中，授予 Web 应用相应权限，为用户访问 Microsoft Graph。 （严格说来，授予 Web 应用的 Azure AD 应用程序相应权限，为用户访问 Microsoft Graph Azure AD 应用程序。）

在 [Azure 门户](https://portal.azure.com)菜单中，选择“Azure Active Directory”，或在任意页面中搜索并选择“Azure Active Directory”。

选择“应用注册” > “拥有的应用程序” > “查看此目录中的所有应用程序”。 选择你的 Web 应用名称，然后选择“API 权限”。

依次选择“添加权限”、“Microsoft API”和“Microsoft Graph”。

选择“委托的权限”，然后从列表中选择“User.Read” 。 选择“添加权限”。

## <a name="configure-app-service-to-return-a-usable-access-token"></a>对应用服务进行配置，使之返回可用的访问令牌

Web 应用现在具有以已登录用户身份访问 Microsoft Graph 所需的权限。 在此步骤中，配置应用服务身份验证和授权，以便获取用于访问 Microsoft Graph 的可用访问令牌。 为完成此步骤，需要为下游服务 (Microsoft Graph) 添加 User.Read 范围：`https://graph.microsoft.com/User.Read`。

> [!IMPORTANT]
> 如果未将应用服务配置为返回可用的访问令牌，则在代码中调用 Microsoft 图形 API 时会收到 ```CompactToken parsing failed with error code: 80049217``` 错误。

# <a name="azure-resource-explorer"></a>[Azure 资源浏览器](#tab/azure-resource-explorer)
转到 [Azure 资源浏览器](https://resources.azure.com/)并使用资源树找到 Web 应用。 资源 URL 应类似于 `https://resources.azure.com/subscriptions/subscriptionId/resourceGroups/SecureWebApp/providers/Microsoft.Web/sites/SecureWebApp20200915115914`。

此时会打开 Azure 资源浏览器，Web 应用在资源树中处于选中状态。 在页面顶部选择“读/写”，以便启用编辑 Azure 资源的功能。

在左侧浏览器中，向下钻取到“config” > “authsettingsV2”。

在“authsettingsV2”视图中，选择“编辑”。 找到 identityProviders -> azureActiveDirectory 的 login 节，添加以下 loginParameters 设置：`"loginParameters":[ "response_type=code id_token","scope=openid offline_access profile https://graph.microsoft.com/User.Read" ]`   。

```json
"identityProviders": {
    "azureActiveDirectory": {
      "enabled": true,
      "login": {
        "loginParameters":[
          "response_type=code id_token",
          "scope=openid offline_access profile https://graph.microsoft.com/User.Read"
        ]
      }
    }
  }
},
```

选择“PUT”，对设置进行保存。 此设置可能需要几分钟才能生效。 现在，Web 应用已配置为使用适当的访问令牌访问 Microsoft Graph。 如果未进行设置，Microsoft Graph 将返回一个错误，指示压缩令牌的格式不正确。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

使用 Azure CLI 调用应用服务 Web 应用 REST API 以[获取](/rest/api/appservice/web-apps/get-auth-settings)并[更新](/rest/api/appservice/web-apps/update-auth-settings)身份验证配置设置，使 Web 应用能够调用 Microsoft Graph。 打开命令窗口并登录到 Azure CLI：

```azurecli
az login
```

获取现有的“config/authsettingsv2”设置，并将其保存到本地 authsettings.json 文件中。

```azurecli
az rest --method GET --url '/subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.Web/sites/{WEBAPP_NAME}/config/authsettingsv2/list?api-version=2020-06-01' > authsettings.json
```

使用你偏好的文本编辑器打开 authsettings.json 文件。 找到 identityProviders -> azureActiveDirectory 的 login 节，添加以下 loginParameters 设置：`"loginParameters":[ "response_type=code id_token","scope=openid offline_access profile https://graph.microsoft.com/User.Read" ]`   。

```json
"identityProviders": {
    "azureActiveDirectory": {
      "enabled": true,
      "login": {
        "loginParameters":[
          "response_type=code id_token",
          "scope=openid offline_access profile https://graph.microsoft.com/User.Read"
        ]
      }
    }
  }
},
```

保存对文件 authsettings.json 所做的更改，并将本地设置上传到你的 Web 应用：

```azurecli
az rest --method PUT --url '/subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.Web/sites/{WEBAPP_NAME}/config/authsettingsv2?api-version=2020-06-01' --body @./authsettings.json
```
---

## <a name="call-microsoft-graph-net"></a>调用 Microsoft Graph (.NET)

Web 应用现在具有所需的权限，并且还将 Microsoft Graph 的客户端 ID 添加到登录参数中。 使用 [Microsoft.Identity.Web 库](https://github.com/AzureAD/microsoft-identity-web/)，Web 应用将获取一个访问令牌，用于向 Microsoft Graph 进行身份验证。 在 1.2.0 版和更高版本中，Microsoft.Identity.Web 库与应用服务身份验证/授权模块集成，且可与之一起运行。 Microsoft.Identity.Web 检测到该 Web 应用托管在应用服务中，并从应用服务身份验证/授权模块获取访问令牌。 然后，使用 Microsoft Graph API 将访问令牌传递给经过身份验证的请求。

若要查看作为示例应用程序一部分的代码，请参阅 [GitHub 上的示例](https://github.com/Azure-Samples/ms-identity-easyauth-dotnet-storage-graphapi/tree/main/2-WebApp-graphapi-on-behalf)。

> [!NOTE]
> Web 应用无需 Microsoft.Identity.Web 库即可进行基础身份验证/授权或向 Microsoft Graph 验证请求。 只有启用了应用服务身份验证/授权模块才能[安全地调用下游 API](tutorial-auth-aad.md#call-api-securely-from-server-code)。
> 
> 但是，应用服务身份验证/授权旨在用于更基本的身份验证场景。 对于更复杂的场景（例如处理自定义声明），需要 Microsoft.Identity.Web 库或 [Microsoft 身份验证库](../active-directory/develop/msal-overview.md)。 在一开始就有更多的设置和配置工作，但 Microsoft.Identity.Web 库可与应用服务身份验证/授权模块同时运行。 当 Web 应用以后需要处理更复杂的场景时，你可以禁用应用服务身份验证/授权模块，而 Microsoft.Identity.Web 将已是应用的一部分。

### <a name="install-client-library-packages"></a>安装客户端库包

使用 .NET Core 命令行接口或 Visual Studio 中的包管理器控制台，在项目中安装 [Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web/) 和 [Microsoft.Identity.Web.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph) NuGet 包。

# <a name="command-line"></a>[命令行](#tab/command-line)

打开一个命令行，并切换到包含项目文件的目录。

运行安装命令。

```dotnetcli
dotnet add package Microsoft.Identity.Web.MicrosoftGraph

dotnet add package Microsoft.Identity.Web
```

# <a name="package-manager"></a>[包管理器](#tab/package-manager)

在 Visual Studio 中打开项目/解决方案，并使用“工具” > “NuGet 包管理器” > “包管理器控制台”命令打开控制台  。

运行安装命令。
```powershell
Install-Package Microsoft.Identity.Web.MicrosoftGraph

Install-Package Microsoft.Identity.Web
```

---

### <a name="startupcs"></a>Startup.cs

在 Startup.cs 文件中，```AddMicrosoftIdentityWebApp``` 方法会将 Microsoft.Identity.Web 添加到 Web 应用。 ```AddMicrosoftGraph``` 方法会添加 Microsoft Graph 支持。

```csharp
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using Microsoft.Identity.Web;
using Microsoft.AspNetCore.Authentication.OpenIdConnect;

// Some code omitted for brevity.
public class Startup
{
    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
                .AddMicrosoftIdentityWebApp(Configuration.GetSection("AzureAd"))
                    .EnableTokenAcquisitionToCallDownstreamApi()
                        .AddMicrosoftGraph(Configuration.GetSection("Graph"))
                        .AddInMemoryTokenCaches();

        services.AddRazorPages();
    }
}

```

### <a name="appsettingsjson"></a>appsettings.json

AzureAd 指定 Microsoft.Identity.Web 库的配置。 在 [Azure 门户](https://portal.azure.com)中，从门户菜单中选择“Azure Active Directory”，然后选择“应用注册” 。 选择启用应用服务身份验证/授权模块时创建的应用注册。 （该应用注册应具有与 Web 应用相同的名称。）可在应用注册概述页面中找到租户 ID 和客户 ID。 可以在租户的 Azure AD 概述页面中找到域名。

Graph 会指定 Microsoft Graph 终结点和应用所需的初始范围。

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "fourthcoffeetest.onmicrosoft.com",
    "TenantId": "[tenant-id]",
    "ClientId": "[client-id]",
    // To call an API
    "ClientSecret": "[secret-from-portal]", // Not required by this scenario
    "CallbackPath": "/signin-oidc"
  },

  "Graph": {
    "BaseUrl": "https://graph.microsoft.com/v1.0",
    "Scopes": "user.read"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  },
  "AllowedHosts": "*"
}
```

### <a name="indexcshtmlcs"></a>Index.cshtml.cs

下面的示例演示如何以已登录用户身份调用 Microsoft Graph 并获取一些用户信息。 ```GraphServiceClient``` 对象已注入到控制器中，并且 Microsoft.Identity.Web 库已为你配置了身份验证。

```csharp
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc.RazorPages;
using Microsoft.Graph;
using System.IO;
using Microsoft.Identity.Web;
using Microsoft.Extensions.Logging;

// Some code omitted for brevity.

[AuthorizeForScopes(Scopes = new[] { "user.read" })]
public class IndexModel : PageModel
{
    private readonly ILogger<IndexModel> _logger;
    private readonly GraphServiceClient _graphServiceClient;

    public IndexModel(ILogger<IndexModel> logger, GraphServiceClient graphServiceClient)
    {
        _logger = logger;
        _graphServiceClient = graphServiceClient;
    }

    public async Task OnGetAsync()
    {
        try
        {
            var user = await _graphServiceClient.Me.Request().GetAsync();
            ViewData["Me"] = user;
            ViewData["name"] = user.DisplayName;

            using (var photoStream = await _graphServiceClient.Me.Photo.Content.Request().GetAsync())
            {
                byte[] photoByte = ((MemoryStream)photoStream).ToArray();
                ViewData["photo"] = Convert.ToBase64String(photoByte);
            }
        }
        catch (Exception ex)
        {
            ViewData["photo"] = null;
        }
    }
}
```

## <a name="clean-up-resources"></a>清理资源

如果已完成本教程，并且不再需要 Web 应用或相关资源，请[清理创建的资源](scenario-secure-app-clean-up-resources.md)。

## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何执行以下操作：

> [!div class="checklist"]
>
> * 向 Web 应用授予委托的权限。
> * 为已登录的用户从 Web 应用调用 Microsoft Graph。

> [!div class="nextstepaction"]
> [应用服务以应用身份访问 Microsoft Graph](scenario-secure-app-access-microsoft-graph-as-app.md)
