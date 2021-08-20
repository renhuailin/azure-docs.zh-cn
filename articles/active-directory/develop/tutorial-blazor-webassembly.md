---
title: 教程 - 从 Blazor WebAssembly 应用让用户登录并调用受保护的 API
titleSuffix: Microsoft identity platform
description: 在本教程中，将使用 Blazor WebAssembly (WASM) 应用中的 Microsoft 标识平台让用户登录并调用受保护的 API。
author: knicholasa
ms.author: nichola
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.date: 10/16/2020
ms.openlocfilehash: ae8016251926e8afab10f1bccee8f53e204c7a7a
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114461101"
---
# <a name="tutorial-sign-in-users-and-call-a-protected-api-from-a-blazor-webassembly-app"></a>教程：从 Blazor WebAssembly 应用让用户登录并调用受保护的 API

在本教程中，你将使用 Microsoft 标识平台并在 Azure Active Directory (Azure AD) 中注册应用，从而构建 Blazor WebAssembly 应用，用户可登录该应用并通过 Microsoft Graph 检索数据。 

在本教程中：

> [!div class="checklist"]
>
> * 创建新的 Blazor WebAssembly 应用，并将其配置为通过 Microsoft 标识平台使用 Azure Active Directory (Azure AD) [进行身份验证和授权](authentication-vs-authorization.md)
> * 从受保护的 Web API（在本例中为 [Microsoft Graph](/graph/overview)）中检索数据

本教程使用 .NET Core 3.1。 .NET 文档包含有关如何使用 ASP.NET Core 5.0 [保护 Blazor WebAssembly 应用](/aspnet/core/blazor/security/webassembly/graph-api)的说明。 

我们还提供了[关于 Blazor Server 的教程](tutorial-blazor-server.md)。 

## <a name="prerequisites"></a>先决条件

* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)
* 可在其中注册应用的 Azure AD 租户。 如果无权访问 Azure AD 租户，可以通过注册到 [Microsoft 365 开发人员计划](https://developer.microsoft.com/microsoft-365/dev-program)或创建 [Azure 免费帐户](https://azure.microsoft.com/free)来获取一个租户。

## <a name="register-the-app-in-the-azure-portal"></a>在 Azure 门户中注册应用

使用 Azure Active Directory (Azure AD) 进行身份验证的每个应用都必须注册到 Azure AD。 按照[注册应用程序](quickstart-register-app.md)中的说明及以下规范进行操作：

- 对于“支持的帐户类型”设置，请选择“仅限此组织目录中的帐户”。 
- 将“重定向 URI”下拉列表设置为“单页应用程序(SPA)”，并输入 `https://localhost:5001/authentication/login-callback`。 在 Kestrel 上运行的应用的默认端口为 5001。 如果应用通过一个不同的端口提供，请指定该端口号而非 `5001`。

注册后，在“管理”下，选择“身份验证” > “隐式授权和混合流”  。 选择“访问令牌”和“ID 令牌”，然后选择“保存”  。

> 备注：如果使用的是 .NET 6 或更高版本，则不需要使用隐式授权。 最新模板使用 MSAL Browser 2.0，并支持使用 PKCE 的授权代码流

## <a name="create-the-app-using-the-net-core-cli"></a>使用 .NET Core CLI 创建应用

若要创建应用，需要使用最新的 Blazor 模板。 可以通过以下命令为 .NET Core CLI 安装这些模板：

```dotnetcli
dotnet new -i Microsoft.Identity.Web.ProjectTemplates::1.9.1
```

然后，运行以下命令来创建应用程序。 将命令中的占位符替换为你的应用的概览页面中的正确信息，然后在命令行界面中执行该命令。 使用 `-o|--output` 选项指定的输出位置将创建一个项目文件夹（如果该文件夹不存在）并成为应用程序名称的一部分。

```dotnetcli
dotnet new blazorwasm2 --auth SingleOrg --calls-graph -o {APP NAME} --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

| 占位符   | Azure 门户中的名称       | 示例                                |
| ------------- | ----------------------- | -------------------------------------- |
| `{APP NAME}`  | &mdash;                 | `BlazorWASMSample`                         |
| `{CLIENT ID}` | 应用程序（客户端）ID | `41451fa7-0000-0000-0000-69eff5a761fd` |
| `{TENANT ID}` | 目录（租户）ID   | `e86c78e2-0000-0000-0000-918e0565a45e` |

## <a name="test-the-app"></a>测试应用

现在可以生成并运行应用了。 运行此模板应用时，必须使用 --framework 指定要运行的框架。 本教程使用 .NET Standard 2.1，但该模板也支持其他框架。

```dotnetcli
dotnet run --framework netstandard2.1
```

在浏览器中导航到 `https://localhost:5001`，使用 Azure AD 用户帐户登录，以查看通过 Microsoft 标识平台运行并让用户登录的应用。

[本主题的 ASP.NET 文档](/aspnet/core/blazor/security/webassembly/standalone-with-azure-active-directory#authentication-package)中说明了此模板的组件，这些组件允许通过 Microsoft 标识平台使用 Azure AD 进行登录。

## <a name="retrieving-data-from-a-protected-api-microsoft-graph"></a>通过受保护的 API (Microsoft Graph) 检索数据

[Microsoft Graph](/graph/overview) 包含使用户可访问 Microsoft 365 数据的 API，并且支持 Microsoft 标识平台颁发的令牌，这使得它成为很棒的受保护 API，可用作示例。 在本部分，你将添加代码来调用 Microsoft Graph，并在应用程序的“提取数据”页面上显示用户的电子邮件。

本部分采用常见方法编写，该方法使用命名客户端调用受保护的 API。 这一方法可用于其他要调用的受保护 API。 但是，如果你确实计划从应用程序调用 Microsoft Graph，那么可使用 Graph SDK 来减少样板。 .NET 文档包含有关[如何使用 Graph SDK](/aspnet/core/blazor/security/webassembly/graph-api?view=aspnetcore-5.0&preserve-view=true) 的说明。

在开始之前，请注销你的应用，因为你将对所需权限进行更改，并且你的当前令牌将不起作用。 如果你尚未这样做，请再次运行应用，并在更新以下代码之前选择“注销”。

现在，你将更新应用的注册和代码，以拉取用户的电子邮件并在应用中显示这些消息。

首先，将 `Mail.Read` API 权限添加到应用的注册，使 Azure AD 知道该应用将请求访问其用户的电子邮件。

1. 在 Azure 门户的“应用注册”中选择你的应用。
1. 在“管理”下选择“API 权限”。
1. 选择“添加权限” > “Microsoft Graph” 。
1. 选择“委托的权限”，然后搜索并选择“Mail.Read”权限。
1. 选择“添加权限”。

接下来，将以下项添加到 netstandard2.1“ItemGroup”中项目的 .csproj 文件中。 这使你可以在下一步中创建自定义 HttpClient。

```xml
<PackageReference Include="Microsoft.Extensions.Http" Version="3.1.7" />
```

然后修改后面几个步骤中指定的代码。 这些更改会将[访问令牌](access-tokens.md)添加到发送至 Microsoft Graph API 的传出请求中。 [ASP.NET Core Blazor WebAssembly 其他安全方案](/aspnet/core/blazor/security/webassembly/additional-scenarios)中更加详细地讨论了此模式。

首先，使用以下代码创建名为 GraphAPIAuthorizationMessageHandler.cs 的新文件。 该处理程序用于将 `User.Read` 和 `Mail.Read` 作用域的访问令牌添加到发送至 Microsoft Graph API 的传出请求中。

```csharp
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class GraphAPIAuthorizationMessageHandler : AuthorizationMessageHandler
{
    public GraphAPIAuthorizationMessageHandler(IAccessTokenProvider provider,
        NavigationManager navigationManager)
        : base(provider, navigationManager)
    {
        ConfigureHandler(
            authorizedUrls: new[] { "https://graph.microsoft.com" },
            scopes: new[] { "https://graph.microsoft.com/User.Read", "https://graph.microsoft.com/Mail.Read" });
    }
}
```

然后，将 Program.cs 中 `Main` 方法的内容替换为以下代码。 该代码利用新的 `GraphAPIAuthorizationMessageHandler`，并将 `User.Read` 和 `Mail.Read` 添加为用户首次登录时应用将请求的默认作用域。

```csharp
var builder = WebAssemblyHostBuilder.CreateDefault(args);
builder.RootComponents.Add<App>("app");

builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("User.Read");
    options.ProviderOptions.DefaultAccessTokenScopes.Add("Mail.Read");
});

await builder.Build().RunAsync();
```

最后，将 FetchData.razor 页的内容替换为以下代码。 此代码从 Microsoft Graph API 提取用户电子邮件数据，并将其显示为列表。 在 `OnInitializedAsync` 中，创建使用正确的访问令牌的新 `HttpClient`，并将其用于向 Microsoft Graph API 发出请求。

```c#
@page "/fetchdata"
@using System.ComponentModel.DataAnnotations
@using System.Text.Json.Serialization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@using Microsoft.Extensions.Logging
@inject IAccessTokenProvider TokenProvider
@inject IHttpClientFactory ClientFactory
@inject IHttpClientFactory HttpClientFactory

<p>This component demonstrates fetching data from a service.</p>

@if (messages == null)
{
    <p><em>Loading...</em></p>
}
else
{
    <h1>Hello @userDisplayName !!!!</h1>
    <table class="table">
        <thead>
            <tr>
                <th>Subject</th>
                <th>Sender</th>
                <th>Received Time</th>
            </tr>
        </thead>
        <tbody>
            @foreach (var mail in messages)
            {
                <tr>
                    <td>@mail.Subject</td>
                    <td>@mail.Sender</td>
                    <td>@mail.ReceivedTime</td>
                </tr>
            }
        </tbody>
    </table>
}

@code {

    private string userDisplayName;
    private List<MailMessage> messages = new List<MailMessage>();

    private HttpClient _httpClient;

    protected override async Task OnInitializedAsync()
    {
        _httpClient = HttpClientFactory.CreateClient("GraphAPI");
        try {
            var dataRequest = await _httpClient.GetAsync("https://graph.microsoft.com/beta/me");

            if (dataRequest.IsSuccessStatusCode)
            {
                var userData = System.Text.Json.JsonDocument.Parse(await dataRequest.Content.ReadAsStreamAsync());
                userDisplayName = userData.RootElement.GetProperty("displayName").GetString();
            }

            var mailRequest = await _httpClient.GetAsync("https://graph.microsoft.com/beta/me/messages?$select=subject,receivedDateTime,sender&$top=10");

            if (mailRequest.IsSuccessStatusCode)
            {
                var mailData = System.Text.Json.JsonDocument.Parse(await mailRequest.Content.ReadAsStreamAsync());
                var messagesArray = mailData.RootElement.GetProperty("value").EnumerateArray();

                foreach (var m in messagesArray)
                {
                    var message = new MailMessage();
                    message.Subject = m.GetProperty("subject").GetString();
                    message.Sender = m.GetProperty("sender").GetProperty("emailAddress").GetProperty("address").GetString();
                    message.ReceivedTime = m.GetProperty("receivedDateTime").GetDateTime();
                    messages.Add(message);
                }
            }
        }
        catch (AccessTokenNotAvailableException ex)
        {
            // Tokens are not valid - redirect the user to log in again
            ex.Redirect();
        }
    }

    public class MailMessage
    {
        public string Subject;
        public string Sender;
        public DateTime ReceivedTime;
    }
}
```

现在重新启动应用。 你会注意到，系统将提示你向应用提供访问权限以阅读你的电子邮件。 当应用请求 `Mail.Read` 作用域时，会出现这种情况。

在授权同意后，导航到“提取数据”页来阅读某封电子邮件。

:::image type="content" source="./media/tutorial-blazor-webassembly/final-app.png" alt-text="最终应用的屏幕截图。它有一个显示为“Hello Nicholas”的标题，它显示了属于 Nicholas 的电子邮件列表。":::

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [Microsoft 标识平台最佳做法和建议](./identity-platform-integration-checklist.md)
