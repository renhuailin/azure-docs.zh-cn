---
title: 教程：创建使用 Microsoft 标识平台进行身份验证的 Windows Presentation Foundation (WPF) 应用 | Azure
titleSuffix: Microsoft identity platform
description: 在本教程中，我们生成一个使用 Microsoft 标识平台将用户登录的 WPF 应用程序，并获取访问令牌以代表用户调用 Microsoft Graph API。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 12/12/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 182fa929b5edf1486d0e8d2372db71ea1ee9747f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128637867"
---
# <a name="tutorial-call-the-microsoft-graph-api-from-a-windows-desktop-app"></a>教程：从 Windows 桌面应用调用 Microsoft Graph API

在本教程中，将生成一个本机 Windows 桌面 .NET (XAML) 应用，用户可登录该应用并获取访问令牌来调用 Microsoft 图形 API。 

完成本指南后，你的应用程序将能够调用使用个人帐户（包括 outlook.com、live.com 等）的受保护 API。 应用程序还将使用任何使用 Azure Active Directory 的公司或组织提供的工作和学校帐户。

本教程的内容：

> [!div class="checklist"]
> * 在 Visual Studio 中创建 Windows Presentation Foundation (WPF) 项目
> * 安装适用于 .NET 的 Microsoft 身份验证库 (MSAL)
> * 在 Azure 门户中注册应用程序
> * 添加代码以支持用户登录和注销
> * 添加代码以调用 Microsoft Graph API
> * 测试应用程序

## <a name="prerequisites"></a>先决条件

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/)

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>本指南生成的示例应用的工作原理

![显示本教程生成的示例应用的工作原理](./media/active-directory-develop-guidedsetup-windesktop-intro/windesktophowitworks.svg)

本指南中创建的示例应用程序是一个 Windows 桌面应用程序，它能够查询 Microsoft Graph API 或 Web API，而该 API 接受来自 Microsoft 标识平台终结点的令牌。 对于此方案，通过 Authorization 标头将令牌添加到 HTTP 请求。 Microsoft 身份验证库 (MSAL) 处理令牌获取和续订。

## <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>负责获得用于访问受保护 Web API 的令牌

对用户进行身份验证后，示例应用程序会收到一个令牌，该令牌可用于查询受 Microsoft 标识平台保护的 Microsoft Graph API 或 Web API。

API（如 Microsoft Graph）需要令牌以允许访问特定资源。 例如，需要使用令牌读取用户的配置文件、访问用户的日历或发送电子邮件。 应用程序可通过指定 API 作用域来使用 MSAL 请求访问令牌，从而访问这些资源。 然后对于针对受保护资源发出的每个调用，将此访问令牌添加到 HTTP 授权标头。

MSAL 负责管理缓存和刷新访问令牌，因此应用程序无需执行这些任务。

## <a name="nuget-packages"></a>NuGet 包

本指南使用以下 NuGet 包：

|库|说明|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Microsoft 身份验证库 (MSAL.NET)|

## <a name="set-up-your-project"></a>设置项目

在本部分中，你将创建一个新项目，用于演示如何将 Windows 桌面 .NET 应用程序 (XAML) 与“使用 Microsoft 登录”集成，使该应用程序能够查询需要令牌的 Web API。

使用本指南创建的应用程序将显示一个用于调用图的按钮、一个用于在屏幕上显示结果的区域和一个注销按钮。

> [!NOTE]
> 想要改为下载此示例的 Visual Studio 项目？ [下载一个项目](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/archive/msal3x.zip)，并且在执行该项目之前跳到[配置步骤](#register-your-application)来配置代码示例。
>

若要创建应用程序，请执行以下操作：

1. 在 Visual Studio 中，选择“文件” > “新建” > “项目”。
2. 在“模板”下，选择“Visual C#” 。
3. 选择“WPF 应用(.NET Framework)”，具体取决于所使用的 Visual Studio 版本。

## <a name="add-msal-to-your-project"></a>将 MSAL 添加到项目

1. 在 Visual Studio 中，选择“工具” > “NuGet 包管理器”> “包管理器控制台”  。
2. 在“包管理器控制台”窗口中，粘贴以下 Azure PowerShell 命令：

    ```powershell
    Install-Package Microsoft.Identity.Client -Pre
    ```

    > [!NOTE]
    > 此命令将安装 Microsoft 身份验证库。 MSAL 负责获取、缓存和刷新用于访问受 Azure Active Directory v2.0 保护的 API 的用户令牌
    >

## <a name="register-your-application"></a>注册应用程序

可以通过以下两种方式之一注册应用程序。

### <a name="option-1-express-mode"></a>选项 1：快速模式

可以通过执行以下操作快速注册应用程序：
1. 转到 <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/WinDesktopQuickstartPage/sourceType/docs" target="_blank">Azure 门户 - 应用注册</a>快速入门体验。
1. 输入应用程序的名称并选择“注册”。
1. 遵照说明下载内容，并只需单击一下自动配置新应用程序。

### <a name="option-2-advanced-mode"></a>选项 2：高级模式

若要注册应用程序并将应用程序注册信息添加到解决方案，请执行以下操作：
1. 登录 <a href="https://portal.azure.com/" target="_blank">Azure 门户</a>。
1. 如果有权访问多个租户，请使用顶部菜单中的“目录 + 订阅”筛选器 :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::，以切换到要在其中注册应用程序的租户。
1. 搜索并选择“Azure Active Directory”。
1. 在“管理”下，选择“应用注册” > “新建注册”  。
1. 输入应用程序的名称（例如 `Win-App-calling-MsGraph`）。 应用的用户可能会看到此名称，你稍后可对其进行更改。
1. 在“受支持的帐户类型”部分下，选择“任何组织目录(任何 Azure AD 目录 - 多租户)中的帐户和个人 Microsoft 帐户(例如，Skype、Xbox)” 。
1. 选择“注册”  。
1. 在“管理”下，选择“身份验证” > “添加平台”  。
1. 选择“移动和桌面应用程序”。
1. 在“重定向 URI”部分中，选择 https://login.microsoftonline.com/common/oauth2/nativeclient 。
1. 选择“配置” 。
1. 转到 Visual Studio，打开 App.xaml.cs 文件，然后将下面代码片段中的 `Enter_the_Application_Id_here` 替换为刚注册并复制的应用程序 ID。

    ```csharp
    private static string ClientId = "Enter_the_Application_Id_here";
    ```

## <a name="add-the-code-to-initialize-msal"></a>添加代码以初始化 MSAL

此步骤将创建用于处理与 MSAL 的交互（例如令牌处理）的类。

1. 打开 *App.xaml.cs* 文件，将 MSAL 的引用添加到类：

    ```csharp
    using Microsoft.Identity.Client;
    ```
   <!-- Workaround for Docs conversion bug -->

2. 将 App 类更新为：

    ```csharp
    public partial class App : Application
    {
        static App()
        {
            _clientApp = PublicClientApplicationBuilder.Create(ClientId)
                .WithAuthority(AzureCloudInstance.AzurePublic, Tenant)
                .WithDefaultRedirectUri()
                .Build();
        }

        // Below are the clientId (Application Id) of your app registration and the tenant information.
        // You have to replace:
        // - the content of ClientID with the Application Id for your app registration
        // - the content of Tenant by the information about the accounts allowed to sign-in in your application:
        //   - For Work or School account in your org, use your tenant ID, or domain
        //   - for any Work or School accounts, use `organizations`
        //   - for any Work or School accounts, or Microsoft personal account, use `common`
        //   - for Microsoft Personal account, use consumers
        private static string ClientId = "0b8b0665-bc13-4fdc-bd72-e0227b9fc011";

        private static string Tenant = "common";

        private static IPublicClientApplication _clientApp ;

        public static IPublicClientApplication PublicClientApp { get { return _clientApp; } }
    }
    ```

## <a name="create-the-application-ui"></a>创建应用程序 UI

本部分说明应用程序如何查询受保护的后端服务器，例如 Microsoft Graph。

项目模板中应自动创建了 *MainWindow.xaml* 文件。 打开此文件，然后将应用程序的 \<Grid> 节点替换为以下代码：

```xml
<Grid>
    <StackPanel Background="Azure">
        <StackPanel Orientation="Horizontal" HorizontalAlignment="Right">
            <Button x:Name="CallGraphButton" Content="Call Microsoft Graph API" HorizontalAlignment="Right" Padding="5" Click="CallGraphButton_Click" Margin="5" FontFamily="Segoe Ui"/>
            <Button x:Name="SignOutButton" Content="Sign-Out" HorizontalAlignment="Right" Padding="5" Click="SignOutButton_Click" Margin="5" Visibility="Collapsed" FontFamily="Segoe Ui"/>
        </StackPanel>
        <Label Content="API Call Results" Margin="0,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="ResultText" TextWrapping="Wrap" MinHeight="120" Margin="5" FontFamily="Segoe Ui"/>
        <Label Content="Token Info" Margin="0,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="TokenInfoText" TextWrapping="Wrap" MinHeight="70" Margin="5" FontFamily="Segoe Ui"/>
    </StackPanel>
</Grid>
```

## <a name="use-msal-to-get-a-token-for-the-microsoft-graph-api"></a>使用 MSAL 获取 Microsoft Graph API 的令牌

本部分使用 MSAL 获取 Microsoft Graph API 的令牌。

1. 在 *MainWindow.xaml.cs* 文件中，将 MSAL 的引用添加到类：

    ```csharp
    using Microsoft.Identity.Client;
    ```

2. 将 `MainWindow` 类代码替换为以下内容：

    ```csharp
    public partial class MainWindow : Window
    {
        //Set the API Endpoint to Graph 'me' endpoint
        string graphAPIEndpoint = "https://graph.microsoft.com/v1.0/me";

        //Set the scope for API call to user.read
        string[] scopes = new string[] { "user.read" };


        public MainWindow()
        {
            InitializeComponent();
        }

      /// <summary>
        /// Call AcquireToken - to acquire a token requiring user to sign-in
        /// </summary>
        private async void CallGraphButton_Click(object sender, RoutedEventArgs e)
        {
            AuthenticationResult authResult = null;
            var app = App.PublicClientApp;
            ResultText.Text = string.Empty;
            TokenInfoText.Text = string.Empty;

            var accounts = await app.GetAccountsAsync();
            var firstAccount = accounts.FirstOrDefault();

            try
            {
                authResult = await app.AcquireTokenSilent(scopes, firstAccount)
                    .ExecuteAsync();
            }
            catch (MsalUiRequiredException ex)
            {
                // A MsalUiRequiredException happened on AcquireTokenSilent.
                // This indicates you need to call AcquireTokenInteractive to acquire a token
                System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

                try
                {
                    authResult = await app.AcquireTokenInteractive(scopes)
                        .WithAccount(accounts.FirstOrDefault())
                        .WithPrompt(Prompt.SelectAccount)
                        .ExecuteAsync();
                }
                catch (MsalException msalex)
                {
                    ResultText.Text = $"Error Acquiring Token:{System.Environment.NewLine}{msalex}";
                }
            }
            catch (Exception ex)
            {
                ResultText.Text = $"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}";
                return;
            }

            if (authResult != null)
            {
                ResultText.Text = await GetHttpContentWithToken(graphAPIEndpoint, authResult.AccessToken);
                DisplayBasicTokenInfo(authResult);
                this.SignOutButton.Visibility = Visibility.Visible;
            }
        }
        }
    ```

### <a name="more-information"></a>详细信息

#### <a name="get-a-user-token-interactively"></a>以交互方式获取用户令牌

调用 `AcquireTokenInteractive` 方法将出现提示用户登录的窗口。 当用户首次需要访问受保护的资源时，应用程序通常会要求用户进行交互式登录。 当用于获取令牌的无提示操作失败时（例如，当用户的密码过期时），用户也可能需要登录。

#### <a name="get-a-user-token-silently"></a>以无提示方式获取用户令牌

`AcquireTokenSilent` 方法处理令牌获取和续订，无需进行任何用户交互。 首次执行 `AcquireTokenInteractive` 后，通常使用 `AcquireTokenSilent` 方法获得用于访问受保护资源的令牌，以便进行后续调用，因为调用请求或续订令牌都以无提示方式进行。

最终，`AcquireTokenSilent` 方法会失败。 失败可能是因为用户已注销，或者在另一设备上更改了密码。 MSAL 检测到可以通过请求交互式操作解决问题时，它将引发 `MsalUiRequiredException` 异常。 应用程序可以通过两种方式处理此异常：

* 它可以立即调用 `AcquireTokenInteractive`。 此调用会导致系统提示用户进行登录。 此模式通常用于联机应用程序，这类应用程序中没有可供用户使用的脱机内容。 此指导式设置生成的示例遵循此模式，第一次执行示例时可以看到其正在运行。

* 由于没有用户使用过该应用程序，因此 `PublicClientApp.Users.FirstOrDefault()` 包含一个 null 值，并且引发 `MsalUiRequiredException` 异常。

* 此示例中的代码随后处理此异常，方法是通过调用 `AcquireTokenInteractive` 使其显示用户登录提示。

* 它可以改为向用户呈现视觉指示，要求用户进行交互式登录，以便他们可以选择适当的时间进行登录。 也可以让应用程序稍后重试 `AcquireTokenSilent`。 当用户可以使用其他应用程序功能而不导致中断时（例如，当脱机内容在应用程序中可用时），会频繁使用此模式。 在这种情况下，用户可以决定何时需要登录来访问受保护的资源或刷新过期信息。 也可让应用程序决定在网络临时不可用又还原后，是否重试 `AcquireTokenSilent`。

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>使用刚获得的令牌调用 Microsoft Graph API

将以下新方法添加到 `MainWindow.xaml.cs`。 该方法用于通过 Authorize 标头对图形 API 执行 `GET` 请求：

```csharp
/// <summary>
/// Perform an HTTP GET request to a URL using an HTTP Authorization header
/// </summary>
/// <param name="url">The URL</param>
/// <param name="token">The token</param>
/// <returns>String containing the results of the GET operation</returns>
public async Task<string> GetHttpContentWithToken(string url, string token)
{
    var httpClient = new System.Net.Http.HttpClient();
    System.Net.Http.HttpResponseMessage response;
    try
    {
        var request = new System.Net.Http.HttpRequestMessage(System.Net.Http.HttpMethod.Get, url);
        //Add the token in Authorization header
        request.Headers.Authorization = new System.Net.Http.Headers.AuthenticationHeaderValue("Bearer", token);
        response = await httpClient.SendAsync(request);
        var content = await response.Content.ReadAsStringAsync();
        return content;
    }
    catch (Exception ex)
    {
        return ex.ToString();
    }
}
```

### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>对受保护 API 进行 REST 调用的详细信息

在此示例应用程序中，请使用 `GetHttpContentWithToken` 方法对需要令牌的受保护资源发出 HTTP `GET` 请求，然后将内容返回给调用方。 此方法在 HTTP 授权标头中添加获取的令牌。 此示例中的资源是 Microsoft Graph API *me* 终结点，用于显示用户的个人资料信息。

## <a name="add-a-method-to-sign-out-a-user"></a>添加注销用户的方法

将以下方法添加到 `MainWindow.xaml.cs` 文件可注销用户：

```csharp
/// <summary>
/// Sign out the current user
/// </summary>
private async void SignOutButton_Click(object sender, RoutedEventArgs e)
{
    var accounts = await App.PublicClientApp.GetAccountsAsync();

    if (accounts.Any())
    {
        try
        {
            await App.PublicClientApp.RemoveAsync(accounts.FirstOrDefault());
            this.ResultText.Text = "User has signed-out";
            this.CallGraphButton.Visibility = Visibility.Visible;
            this.SignOutButton.Visibility = Visibility.Collapsed;
        }
        catch (MsalException ex)
        {
            ResultText.Text = $"Error signing-out user: {ex.Message}";
        }
    }
}
```

### <a name="more-information-about-user-sign-out"></a>有关用户注销的详细信息

`SignOutButton_Click` 方法删除 MSAL 用户缓存中的用户，这样可有效地告知 MSAL 忘记当前用户，使将来获取令牌的请求只能在交互模式下取得成功。

虽然此示例中的应用程序支持单个用户，但 MSAL 也支持多个帐户可能同时登录的情况。 用户在其中具有多个帐户的电子邮件应用程序就是一个示例。

## <a name="display-basic-token-information"></a>显示基本令牌信息

若要显示有关令牌的基本信息，请将以下方法添加到 *MainWindow.xaml.cs* 文件：

```csharp
/// <summary>
/// Display basic information contained in the token
/// </summary>
private void DisplayBasicTokenInfo(AuthenticationResult authResult)
{
    TokenInfoText.Text = "";
    if (authResult != null)
    {
        TokenInfoText.Text += $"Username: {authResult.Account.Username}" + Environment.NewLine;
        TokenInfoText.Text += $"Token Expires: {authResult.ExpiresOn.ToLocalTime()}" + Environment.NewLine;
    }
}
```

### <a name="more-information"></a>详细信息

除了用于调用 Microsoft Graph API 的访问令牌，MSAL 还可以在用户登录后获取 ID 令牌。 此令牌包含一小部分与用户相关的信息。 `DisplayBasicTokenInfo` 方法显示包含在令牌中的基本信息。 例如，它显示用户的显示名称和 ID，以及令牌到期日期和表示访问令牌本身的字符串。 多次选择“调用 Microsoft Graph API”按钮，便会发现后续请求使用了同一令牌。 而且还会注意到，在 MSAL 决定续订令牌时，到期日期也延长了。

[!INCLUDE [5. Test and Validate](../../../includes/active-directory-develop-guidedsetup-windesktop-test.md)]

## <a name="next-steps"></a>后续步骤

在我们的多部分场景系列中，详细了解如何构建可调用受保护 Web API 的桌面应用：

> [!div class="nextstepaction"]
> [方案：用于调用 Web API 的 桌面应用](scenario-desktop-overview.md)
