---
title: 快速入门：在通用 Windows 平台应用中让用户登录并调用 Microsoft Graph | Azure
titleSuffix: Microsoft identity platform
description: 本快速入门介绍通用 Windows 平台 (UWP) 应用程序如何获取访问令牌并调用受 Microsoft 标识平台保护的 API。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/07/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:UWP
ms.openlocfilehash: 5f142cf84e0e7e75d2ed61fbad9896d89e2364a0
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123436529"
---
# <a name="quickstart-call-the-microsoft-graph-api-from-a-universal-windows-platform-uwp-application"></a>快速入门：从通用 Windows 平台 (UWP) 应用程序调用 Microsoft Graph API

在本快速入门中，你将下载并运行一个代码示例，该示例演示通用 Windows 平台 (UWP) 应用程序如何让用户登录并获取访问令牌来调用 Microsoft Graph API。 

有关说明，请参阅[示例工作原理](#how-the-sample-works)。

> [!div renderon="docs"]
> ## <a name="prerequisites"></a>先决条件
>
> * 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
> * [Visual Studio 2019](https://visualstudio.microsoft.com/vs/)
>
> ## <a name="register-and-download-your-quickstart-app"></a>注册并下载快速入门应用
> [!div renderon="docs" class="sxs-lookup"]
> 可以使用两个选项来启动快速入门应用程序：
> * [快速][选项 1：注册并自动配置应用，然后下载代码示例](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [手动][选项 2：注册并手动配置应用程序和代码示例](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>选项 1：注册并自动配置应用，然后下载代码示例
>
> 1. 转到 <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/UwpQuickstartPage/sourceType/docs" target="_blank">Azure 门户 - 应用注册</a>快速入门体验。
> 1. 输入应用程序的名称并选择“注册”。
> 1. 遵照说明下载内容，并一键式自动配置新应用程序。
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>选项 2：注册并手动配置应用程序和代码示例
> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>步骤 1：注册应用程序
> 若要注册应用程序并将应用的注册信息添加到解决方案，请执行以下步骤：
> 1. 登录 <a href="https://portal.azure.com/" target="_blank">Azure 门户</a>。
> 1. 如果有权访问多个租户，请使用顶部菜单中的“目录 + 订阅”筛选器:::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::，选择要在其中注册应用程序的租户。
> 1. 搜索并选择“Azure Active Directory”。
> 1. 在“管理”下，选择“应用注册” > “新建注册”  。
> 1. 输入应用程序的名称（例如 `UWP-App-calling-MsGraph`）。 应用的用户可能会看到此名称，你稍后可对其进行更改。
> 1. 在“支持的帐户类型”部分，选择“任何组织目录中的帐户和个人 Microsoft 帐户(例如 Skype、Xbox、Outlook.com)”。 
> 1. 选择“注册”创建应用程序，然后记录“应用程序(客户端) ID”，以供在后面的步骤中使用 。
> 1. 在“管理”下，选择“身份验证”。 
> 1. 选择“添加平台” > “移动和桌面应用程序” 。
> 1. 在“重定向 URI”下选择“Web”。`https://login.microsoftonline.com/common/oauth2/nativeclient`
> 1. 选择“配置” 。

> [!div renderon="portal" class="sxs-lookup"]
> #### <a name="step-1-configure-the-application"></a>步骤 1：配置应用程序
> 为使此快速入门中的代码示例正常运行，请添加重定向 URI `https://login.microsoftonline.com/common/oauth2/nativeclient`。
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [执行此更改]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![已配置](media/quickstart-v2-uwp/green-check.png) 应用程序已使用这些属性进行配置。

#### <a name="step-2-download-the-visual-studio-project"></a>步骤 2：下载 Visual Studio 项目

> [!div renderon="docs"]
> [下载 Visual Studio 项目](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip)

> [!div class="sxs-lookup" renderon="portal"]
> 使用 Visual Studio 2019 运行项目。
> [!div class="sxs-lookup" renderon="portal" id="autoupdate" class="nextstepaction"]
> [下载代码示例](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip)

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>步骤 3：应用已配置并可以运行
> 我们已经为项目配置了应用属性的值，并且该项目已准备好运行。

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-visual-studio-project"></a>步骤 3：配置 Visual Studio 项目
>
> 1. 将 .zip 存档解压缩到驱动器根附近的本地文件夹中。 例如，解压缩到 C:\Azure-Samples。
> 1. 在 Visual Studio 中打开项目。 如果出现提示，请安装“通用 Windows 平台开发”工作负载和任何单独的 SDK 组件。
> 1. 在 MainPage.Xaml.cs 中，将 `ClientId` 变量的值更改为先前注册的应用程序的“应用程序(客户端) ID”。
>
>    ```csharp
>    private const string ClientId = "Enter_the_Application_Id_here";
>    ```
>
>    可以在 Azure 门户中应用的“概述”窗格上找到“应用程序(客户端) ID”（“Azure Active Directory” > “应用注册” > {你的应用注册}”）   。
> 1. 针对该包创建并选择一个新的自签名测试证书：
>     1. 在“解决方案资源管理器”中，双击 Package.appxmanifest 文件。
>     1. 选择“打包” > “选择证书...” > “创建...”  。
>     1. 输入密码，然后选择“确定”。 创建一个名为 Native_UWP_V2_TemporaryKey.pfx 的证书。 
>     1. 选择“确定”来关闭“选择证书”对话框，然后验证解决方案资源管理器中是否有“Native_UWP_V2_TemporaryKey.pfx” 。
>     1. 在“解决方案资源管理器”中，右键单击 Native_UWP_V2 项目，并选择“属性”  。
>     1. 选择“签名”，然后在“选择强名称密钥文件”下拉列表中选择你创建的 .pfx 。

#### <a name="step-4-run-the-application"></a>步骤 4：运行应用程序

在本地计算机上运行示例应用程序：

1. 在 Visual Studio 工具栏中，选择适当的平台（可能为 **x64** 或 **x86**，不是 ARM）。 目标设备应从“设备”更改为“本地计算机” 。
1. 选择“调试” > “在不调试的情况下启动”。
    
    如果系统提示你执行此操作，你可能首先需要启用“开发人员模式”，然后再次执行“启动（不调试）”以启动该应用 。

出现应用的窗口时，可以选择“调用 Microsoft Graph API”按钮，输入凭据，并同意应用程序请求的权限。 如果成功，应用程序会显示从对 Microsoft Graph API 的调用中获取的一些令牌信息和数据。

## <a name="how-the-sample-works"></a>示例工作原理

![显示本快速入门生成的示例应用的工作原理](media/quickstart-v2-uwp/uwp-intro.svg)

### <a name="msalnet"></a>MSAL.NET

MSAL ([Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)) 是一个库，用于用户登录和请求安全令牌。 安全令牌用于访问受 Microsoft 标识平台保护的 API。 可在 Visual Studio 的包管理器控制台中运行以下命令，以便安装 MSAL：

```powershell
Install-Package Microsoft.Identity.Client
```

### <a name="msal-initialization"></a>MSAL 初始化

可以通过添加以下代码，为 MSAL 添加引用：

```csharp
using Microsoft.Identity.Client;
```

然后，系统将使用以下代码对 MSAL 进行初始化：

```csharp
public static IPublicClientApplication PublicClientApp;
PublicClientApp = PublicClientApplicationBuilder.Create(ClientId)
                                                .WithRedirectUri("https://login.microsoftonline.com/common/oauth2/nativeclient")
                                                    .Build();
```

`ClientId` 的值为在 Azure 门户中注册的应用的“应用程序(客户端) ID”。 可以在 Azure 门户的应用的“概览”页中找到此值。

### <a name="requesting-tokens"></a>请求令牌

MSAL 有两种在 UWP 应用中获取令牌的方法：`AcquireTokenInteractive` 和 `AcquireTokenSilent`。

#### <a name="get-a-user-token-interactively"></a>以交互方式获取用户令牌

在某些情况下，需要强制用户通过弹出窗口与 Microsoft 标识平台进行交互，以验证其凭据或授予许可。 示例包括：

- 用户首次登录应用程序
- 由于密码已过期，用户可能需要重新输入凭据的情况
- 应用程序正在请求访问需要用户同意的资源时
- 需要双重身份验证的情况

```csharp
authResult = await App.PublicClientApp.AcquireTokenInteractive(scopes)
                      .ExecuteAsync();
```

`scopes` 参数包含所请求的范围，例如 `{ "user.read" }`（针对 Microsoft Graph）或 `{ "api://<Application ID>/access_as_user" }`（针对自定义 Web API）。

#### <a name="get-a-user-token-silently"></a>以无提示方式获取用户令牌

使用 `AcquireTokenSilent` 方法可获取令牌，以在初始 `AcquireTokenInteractive` 方法后访问受保护资源。 你不希望在用户每次需要访问资源时都要求其验证其凭据。 大多数时候，你希望在无需任何用户交互的情况下进行令牌获取和续订

```csharp
var accounts = await App.PublicClientApp.GetAccountsAsync();
var firstAccount = accounts.FirstOrDefault();
authResult = await App.PublicClientApp.AcquireTokenSilent(scopes, firstAccount)
                                      .ExecuteAsync();
```

* `scopes` 包含所请求的范围，例如 `{ "user.read" }`（针对 Microsoft Graph）或 `{ "api://<Application ID>/access_as_user" }`（针对自定义 Web API）。
* `firstAccount` 指定缓存中的第一个用户帐户（MSAL 在单个应用中支持多个用户）。

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>后续步骤

试用 Windows 桌面教程，了解有关构建应用程序和新功能的完整分布指南，包括本快速入门的完整说明。

> [!div class="nextstepaction"]
> [UWP - 调用 Graph API 教程](tutorial-v2-windows-uwp.md)
