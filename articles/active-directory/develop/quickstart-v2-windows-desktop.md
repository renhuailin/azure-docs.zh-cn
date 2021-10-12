---
title: 快速入门：在 Windows 桌面应用中让用户登录并调用 Microsoft Graph | Azure
description: 本快速入门介绍 Windows 桌面 .NET (XAML) 应用程序如何获取访问令牌并调用受 Microsoft 标识平台保护的 API。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 12/12/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: c27288094adb55c58a9d75e3b269580c3d6e93f9
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129232948"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-from-a-windows-desktop-app"></a>快速入门：获取令牌并从 Windows 桌面应用中调用 Microsoft Graph API

在本快速入门中，你将下载并运行一个代码示例，该示例演示 Windows desktop .NET (WPF) 应用程序如何让用户登录并获取访问令牌来调用 Microsoft Graph API。 

有关说明，请参阅[示例工作原理](#how-the-sample-works)。

> [!div renderon="docs"]
> ## <a name="prerequisites"></a>先决条件
>
> * 安装了[通用 Windows 平台开发](/windows/uwp/get-started/get-set-up)工作负荷的 [Visual Studio 2019](https://visualstudio.microsoft.com/vs/)
>
> ## <a name="register-and-download-your-quickstart-app"></a>注册并下载快速入门应用
> 可以使用两个选项来启动快速入门应用程序：
> * [快速][选项 1：注册并自动配置应用，然后下载代码示例](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [手动][选项 2：注册并手动配置应用程序和代码示例](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>选项 1：注册并自动配置应用，然后下载代码示例
>
> 1. 转到 <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/WinDesktopQuickstartPage/sourceType/docs" target="_blank">Azure 门户 - 应用注册</a>快速入门体验。
> 1. 输入应用程序的名称并选择“注册”。
> 1. 遵照说明下载内容，并只需单击一下自动配置新应用程序。
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>选项 2：注册并手动配置应用程序和代码示例
>
> #### <a name="step-1-register-your-application"></a>步骤 1：注册应用程序
> 若要手动注册应用程序并将应用的注册信息添加到解决方案，请执行以下步骤：
>
> 1. 登录 <a href="https://portal.azure.com/" target="_blank">Azure 门户</a>。
> 1. 如果有权访问多个租户，请使用顶部菜单中的“目录 + 订阅”筛选器 :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::，以切换到要在其中注册应用程序的租户。
> 1. 搜索并选择“Azure Active Directory”  。
> 1. 在“管理”下，选择“应用注册” > “新建注册”  。
> 1. 输入应用程序的名称（例如 `Win-App-calling-MsGraph`）。 应用的用户可能会看到此名称，你稍后可对其进行更改。
> 1. 在“支持的帐户类型”部分，选择“任何组织目录中的帐户和个人 Microsoft 帐户(例如 Skype、Xbox、Outlook.com)”。 
> 1. 选择“注册”以创建应用程序。
> 1. 在“管理”下，选择“身份验证”。 
> 1. 选择“添加平台” > “移动和桌面应用程序” 。
> 1. 在“重定向 URI”部分中，选择 `https://login.microsoftonline.com/common/oauth2/nativeclient`，然后在“自定义重定向 URI”中添加 `ms-appx-web://microsoft.aad.brokerplugin/{client_id}`，其中 `{client_id}` 是应用程序的应用程序（客户端）ID（与 `msal{client_id}://auth` 复选框中显示的 GUID 相同） 。
> 1. 选择“配置” 。

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>步骤 1：在 Azure 门户中配置应用程序
> 为使此快速入门中的代码示例正常运行，请添加重定向 URI `https://login.microsoftonline.com/common/oauth2/nativeclient` 和 `ms-appx-web://microsoft.aad.brokerplugin/{client_id}`。
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [执行此更改]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![已配置](media/quickstart-v2-windows-desktop/green-check.png) 应用程序已使用这些属性进行配置。

#### <a name="step-2-download-your-visual-studio-project"></a>步骤 2：下载 Visual Studio 项目

> [!div renderon="docs"]
> [下载 Visual Studio 项目](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/archive/msal3x.zip)

> [!div class="sxs-lookup" renderon="portal"]
> 使用 Visual Studio 2019 运行项目。
> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [下载代码示例](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/archive/msal3x.zip)

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>步骤 3：应用已配置并可以运行
> 我们已经为项目配置了应用属性的值，并且该项目已准备好运行。

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-visual-studio-project"></a>步骤 3：配置 Visual Studio 项目
> 1. 将 zip 文件提取到靠近磁盘根目录的本地文件夹，例如 **C:\Azure-Samples**。
> 1. 在 Visual Studio 中打开项目。
> 1. 编辑 **App.Xaml.cs** 并将字段 `ClientId` 和 `Tenant` 的值替换为以下代码：
>
>    ```csharp
>    private static string ClientId = "Enter_the_Application_Id_here";
>    private static string Tenant = "Enter_the_Tenant_Info_Here";
>    ```
>
> 其中：
> - `Enter_the_Application_Id_here` - 是已注册应用程序的 **应用程序（客户端）ID**。
>    
>    若要查找“应用程序(客户端) ID”的值，请转到 Azure 门户中应用的“概述”页 。
> - `Enter_the_Tenant_Info_Here` - 设置为以下选项之一：
>   - 如果应用程序支持“此组织目录中的帐户”，请将该值替换为 **租户 ID** 或 **租户名称**（例如 contoso.microsoft.com）
>   - 如果应用程序支持“任何组织目录中的帐户”，请将该值替换为`organizations`
>   - 如果应用支持“任何组织目录中的帐户和个人 Microsoft 帐户”，请将此值替换为“`common`”。
>
>     若要查找“目录(租户) ID”和“支持的帐户类型”的值，请转到 Azure 门户中应用的“概述”页。  
>

## <a name="more-information"></a>详细信息

### <a name="how-the-sample-works"></a>示例工作原理
![显示本快速入门生成的示例应用的工作原理](media/quickstart-v2-windows-desktop/windesktop-intro.svg)

### <a name="msalnet"></a>MSAL.NET
MSAL ([Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)) 是一个库，用于用户登录和请求令牌，此类令牌用于访问受 Microsoft 标识平台保护的 API。 可在 Visual Studio 的包管理器控制台中运行以下命令，以便安装 MSAL：

```powershell
Install-Package Microsoft.Identity.Client -IncludePrerelease
```

### <a name="msal-initialization"></a>MSAL 初始化

可以通过添加以下代码，为 MSAL 添加引用：

```csharp
using Microsoft.Identity.Client;
```

然后，使用以下代码对 MSAL 进行初始化：

```csharp
IPublicClientApplication publicClientApp = PublicClientApplicationBuilder.Create(ClientId)
                .WithRedirectUri("https://login.microsoftonline.com/common/oauth2/nativeclient")
                .WithAuthority(AzureCloudInstance.AzurePublic, Tenant)
                .Build();
```

|其中： | 说明 |
|---------|---------|
| `ClientId` | 是在 Azure 门户中注册的应用程序的 **应用程序(客户端) ID**。 可以在 Azure 门户的应用的“概览”页中找到此值。 |

### <a name="requesting-tokens"></a>请求令牌

MSAL 有两种获取令牌的方法：`AcquireTokenInteractive` 和 `AcquireTokenSilent`。

#### <a name="get-a-user-token-interactively"></a>以交互方式获取用户令牌

在某些情况下，需要强制用户通过弹出窗口来与 Microsoft 标识平台进行交互，以验证其凭据或授予同意。 示例包括：

- 用户首次登录应用程序
- 由于密码已过期，用户可能需要重新输入凭据的情况
- 应用程序正在请求访问用户需要同意的资源的情况
- 需要双重身份验证的情况

```csharp
authResult = await App.PublicClientApp.AcquireTokenInteractive(_scopes)
                                      .ExecuteAsync();
```

|其中：| 说明 |
|---------|---------|
| `_scopes` | 包含所请求的范围，例如 `{ "user.read" }`（针对 Microsoft Graph）或 `{ "api://<Application ID>/access_as_user" }`（针对自定义 Web API）。 |

#### <a name="get-a-user-token-silently"></a>以无提示方式获取用户令牌

你不希望在用户每次需要访问资源时都要求其验证其凭据。 大多数情况下，你希望在无需任何用户交互的情况下进行令牌获取和续订。 可以使用 `AcquireTokenSilent` 方法获取令牌，以在初始 `AcquireTokenInteractive` 方法后访问受保护资源：

```csharp
var accounts = await App.PublicClientApp.GetAccountsAsync();
var firstAccount = accounts.FirstOrDefault();
authResult = await App.PublicClientApp.AcquireTokenSilent(scopes, firstAccount)
                                      .ExecuteAsync();
```

|其中： | 说明 |
|---------|---------|
| `scopes` | 包含所请求的范围，例如 `{ "user.read" }`（针对 Microsoft Graph）或 `{ "api://<Application ID>/access_as_user" }`（针对自定义 Web API）。 |
| `firstAccount` | 指定缓存中的第一个用户（MSAL 支持单个应用中的多个用户）。 |

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>后续步骤

试用 Windows 桌面教程，了解有关构建应用程序和新功能的完整分布指南，包括本快速入门的完整说明。

> [!div class="nextstepaction"]
> [调用 Graph API 教程](./tutorial-v2-windows-desktop.md)
