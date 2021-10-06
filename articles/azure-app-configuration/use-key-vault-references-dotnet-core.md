---
title: 关于在 ASP.NET Core 应用中使用 Azure 应用程序配置 Key Vault 引用的教程 | Microsoft Docs
description: 本教程介绍如何在 ASP.NET Core 应用中使用 Azure 应用程序配置的 Key Vault 引用
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 04/08/2020
ms.author: alkemper
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 9f095bbadf8f395b809d46c8beea5f6665932d12
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129357916"
---
# <a name="tutorial-use-key-vault-references-in-an-aspnet-core-app"></a>教程：在 ASP.NET Core 应用中使用 Key Vault 引用

本教程介绍如何将 Azure 应用程序配置服务与 Azure Key Vault 配合使用。 应用程序配置和 Key Vault 是互补性的服务，大多数应用程序部署中会同时使用两者。

应用程序配置可以创建密钥来引用存储在 Key Vault 中的值，以帮助你结合使用这两个服务。 当应用程序配置创建此类密钥时，它会存储 Key Vault 值的 URI，而不是值本身。

应用程序使用应用程序配置客户端提供程序检索 Key Vault 引用，就如同检索应用程序配置中存储的任何其他密钥一样。 在这种情况下，存储在应用程序配置中的值是引用 Key Vault 中的值的 URI。 这些值不是 Key Vault 值或凭据。 由于客户端提供程序将密钥识别为 Key Vault 引用，因此它使用 Key Vault 来检索其值。

应用程序负责向应用程序配置和 Key Vault 进行适当的身份验证。 这两项服务不直接通信。

本教程介绍如何在代码中实现 Key Vault 引用。 它建立在快速入门中介绍的 Web 应用之上。 在继续操作之前，请先完成[使用应用程序配置创建 ASP.NET Core 应用](./quickstart-aspnet-core-app.md)。

你可以使用任何代码编辑器执行本教程中的步骤。 例如，[Visual Studio Code](https://code.visualstudio.com/) 是适用于 Windows、macOS 和 Linux 操作系统的跨平台代码编辑器。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 创建一个应用程序配置密钥，用于引用 Key Vault 中存储的值
> * 从 ASP.NET Core Web 应用程序访问此密钥的值。

## <a name="prerequisites"></a>先决条件

在开始学习本教程之前，请安装 [.NET Core SDK](https://dotnet.microsoft.com/download)。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-vault"></a>创建保管库

1. 选择 Azure 门户左上角的“创建资源”选项： 

    ![屏幕截图显示 Azure 门户中的“创建资源”选项。](./media/quickstarts/search-services.png)
1. 在搜索框中键入“密钥保管库”，然后从下拉列表中选择“密钥保管库” 。
1. 在结果列表中，选择左侧的“Key Vault”  。
1. 在“Key Vault”中选择“添加”。  
1. 在“创建 Key Vault”中的右侧提供以下信息： 
    - 选择“订阅”以选择订阅。 
    - 在“资源组”中，选择现有的资源组名称，或者选择“新建”并输入资源组名称 。
    - 在“Key Vault 名称”中，必须输入唯一的名称。 
    - 在“区域”下拉列表中，选择一个位置。 
1. 将“创建 Key Vault”的其他选项保留默认值。 
1. 单击“查看 + 创建”。
1. 系统将验证并显示你输入的数据。 单击“创建”。

目前，只有你的 Azure 帐户有权访问这个新保管库。


## <a name="add-a-secret-to-key-vault"></a>向 Key Vault 添加机密

只需执行几个额外的步骤即可将机密添加到保管库。 在本例中，我们将添加一条消息，用于测试 Key Vault 检索功能。 此消息名为 **Message**，我们将在其中存储“Hello from Key Vault”值。

1. 在 Key Vault 属性页中选择“机密”  。
1. 选择“生成/导入”。 
1. 在“创建机密”窗格中输入以下值： 
    - **上传选项**：输入 **Manual**。
    - **Name**：输入 **Message**。
    - **值**：输入 **Hello from Key Vault**。
1. 将“创建机密”的其他属性保留默认值。 
1. 选择“创建”  。

## <a name="add-a-key-vault-reference-to-app-configuration"></a>将 Key Vault 引用添加到应用程序配置

1. 登录 [Azure 门户](https://portal.azure.com)。 选择“所有资源”，然后选择在快速入门中创建的应用程序配置存储实例  。

1. 选择“配置资源管理器”。 

1. 选择“+ 创建” > “Key Vault 引用”，然后指定以下值：  
    - **密钥**：选择“TestApp:Settings:KeyVaultMessage”。 
    - **标签**：将此值保留空白。
    - “订阅”、“资源组”和“Key Vault”：    输入上一部分在 Key Vault 中创建的值相对应的值。
    - **机密**：选择在上一部分创建的名为 **Message** 的机密。

![用于创建新密钥保管库引用的窗体的屏幕截图](./media/create-key-vault-reference.png)

## <a name="update-your-code-to-use-a-key-vault-reference"></a>更新代码以使用 Key Vault 引用

1. 运行以下命令，添加对所需 NuGet 包的引用：

    ```dotnetcli
    dotnet add package Azure.Identity
    ```

1. 打开 *Program.cs*，添加对以下所需包的引用：

    ```csharp
    using Azure.Identity;
    ```

1. 通过调用 `config.AddAzureAppConfiguration` 方法，更新 `CreateWebHostBuilder` 方法以使用应用配置。 包括 `ConfigureKeyVault` 选项，并将正确的凭据传递到 Key Vault。

     #### <a name="net-core-5x"></a>[.NET Core 5.x](#tab/core5x)

    ```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                config.AddAzureAppConfiguration(options =>
                {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                            .ConfigureKeyVault(kv =>
                            {
                                kv.SetCredential(new DefaultAzureCredential());
                            });
                });
            })
            .UseStartup<Startup>());
    ```

    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                config.AddAzureAppConfiguration(options =>
                {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                            .ConfigureKeyVault(kv =>
                            {
                                kv.SetCredential(new DefaultAzureCredential());
                            });
                });
            })
            .UseStartup<Startup>());
    ```
    
    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                config.AddAzureAppConfiguration(options =>
                {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                            .ConfigureKeyVault(kv =>
                            {
                                kv.SetCredential(new DefaultAzureCredential());
                            });
                });
            })
            .UseStartup<Startup>();
    ```

1. 在初始化与“应用配置”服务的连接时，你已通过调用 `ConfigureKeyVault` 方法设置了与 Key Vault 的连接。 初始化之后，可以像访问普通的应用程序配置密钥值一样访问 Key Vault 引用值。

    若要了解此过程的运作方式，请打开“视图” > “主页”文件夹中的 *Index.cshtml*。   将其内容替换为以下代码：

    ```html
    @using Microsoft.Extensions.Configuration
    @inject IConfiguration Configuration

    <style>
        body {
            background-color: @Configuration["TestApp:Settings:BackgroundColor"]
        }
        h1 {
            color: @Configuration["TestApp:Settings:FontColor"];
            font-size: @Configuration["TestApp:Settings:FontSize"]px;
        }
    </style>

    <h1>@Configuration["TestApp:Settings:Message"]
        and @Configuration["TestApp:Settings:KeyVaultMessage"]</h1>
    ```

    像访问 **TestApp:Settings:Message** 配置值一样访问 Key Vault 引用 **TestApp:Settings:KeyVaultMessage** 的值。


## <a name="grant-your-app-access-to-key-vault"></a>请授予应用对密钥保管库的访问权限

Azure 应用程序配置不会访问你的密钥保管库。 应用将直接读取密钥保管库，因此你需要为应用授予对密钥保管库中机密的读取访问权限。 这样，机密始终会保留在你的应用中。 可以使用[密钥保管库访问策略](../key-vault/general/assign-access-policy-portal.md)或 [Azure 基于角色的访问控制](../key-vault/general/rbac-guide.md)来授予访问权限。

在上面的代码中使用 `DefaultAzureCredential`。 它是一个自动尝试多个凭据类型（如 `EnvironmentCredential`、`ManagedIdentityCredential`、`SharedTokenCacheCredential` 和 `VisualStudioCredential`）的聚合令牌凭据。 有关详细信息，请参阅 [DefaultAzureCredential 类](/dotnet/api/azure.identity.defaultazurecredential)。 可以显式将 `DefaultAzureCredential` 替换为任何凭据类型。 但是，通过使用 `DefaultAzureCredential`，你可以本地和 Azure 环境中运行相同的代码。 例如，你可以授予对密钥保管库的凭据访问权限。 使用 Visual Studio 进行本地开发时，`DefaultAzureCredential` 将自动回退到 `SharedTokenCacheCredential` 或 `VisualStudioCredential`。

或者，可以设置 AZURE_TENANT_ID、AZURE_CLIENT_ID 和 AZURE_CLIENT_SECRET 环境变量，然后，`DefaultAzureCredential` 将使用你通过 `EnvironmentCredential` 获取的客户端机密对密钥保管库进行身份验证。 将应用部署到启用了托管标识的 Azure 服务（例如 Azure 应用服务、Azure Kubernetes 服务或 Azure 容器实例）后，为 Azure 服务的托管标识授予对密钥保管库的访问权限。 当应用在 Azure 中运行时，`DefaultAzureCredential` 会自动使用 `ManagedIdentityCredential`。 可以使用同一个托管标识对应用程序配置和密钥保管库进行身份验证。 有关详细信息，请参阅[如何使用托管标识访问应用程序配置](howto-integrate-azure-managed-service-identity.md)。

## <a name="build-and-run-the-app-locally"></a>在本地生成并运行应用

1. 要通过使用 .NET Core CLI 生成应用，请在命令行界面中执行以下命令：

    ```dotnetcli
    dotnet build
    ```

1. 完成生成后，使用以下命令在本地运行 Web 应用：

    ```dotnetcli
    dotnet run
    ```

1. 启动浏览器窗口并转到 `http://localhost:5000`，即本地托管的 Web 应用的默认 URL。

    ![快速入门 - 本地应用启动](./media/key-vault-reference-launch-local.png)



## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>后续步骤

在本教程中，你已在应用程序配置中创建一个引用密钥保管库中存储的机密的密钥。
若要了解如何自动从密钥保管库重载机密和证书，请继续学习下一篇教程：

> [!div class="nextstepaction"]
> [自动从密钥保管库重新加载机密和证书](./reload-key-vault-secrets-dotnet.md)

若要了解如何使用托管标识来简化对应用程序配置和密钥保管库的访问，请参阅以下教程：

> [!div class="nextstepaction"]
> [托管标识集成](./howto-integrate-azure-managed-service-identity.md)
