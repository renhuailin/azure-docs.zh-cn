---
title: 使用托管标识来访问应用程序配置
titleSuffix: Azure App Configuration
description: 使用托管标识向“Azure 应用程序配置”进行身份验证
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.custom: devx-track-csharp, fasttrack-edit
ms.topic: conceptual
ms.date: 04/08/2021
ms.openlocfilehash: 2aefa7ed7ce63b1ecbe735f21d372133ba4c7825
ms.sourcegitcommit: b11257b15f7f16ed01b9a78c471debb81c30f20c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2021
ms.locfileid: "111592405"
---
# <a name="use-managed-identities-to-access-app-configuration"></a>使用托管标识来访问应用程序配置

Azure Active Directory [托管标识](../active-directory/managed-identities-azure-resources/overview.md)简化云应用程序的机密管理。 使用托管标识，你的代码可使用为其运行的 Azure 服务创建的服务主体。 使用托管标识而不是存储在 Azure Key Vault 中的单独凭据或本地连接字符串。

Azure 应用程序配置及其 .NET Core、.NET Framework 和 Java Spring 客户端库拥有内置的托管标识支持。 虽然并非必须使用托管标识，但借助它便无需再使用包含机密的访问令牌。 你的代码只能使用服务终结点访问应用程序配置存储区。 可以直接在代码中嵌入此 URL，而不会泄露任何机密。

本文介绍如何利用管理标识访问应用程序配置。 它建立在快速入门中介绍的 Web 应用之上。 在继续操作之前，先[使用应用程序配置创建 ASP.NET Core 应用](./quickstart-aspnet-core-app.md)。

> [!IMPORTANT]
> 托管标识不能用于对本地运行的应用程序进行身份验证。 必须将应用程序部署到支持托管标识的 Azure 服务。 本文使用 Azure 应用服务作为示例，但相同的概念也适用于任何其他支持托管标识的 Azure 服务，例如 [Azure Kubernetes 服务](../aks/use-azure-ad-pod-identity.md)、[Azure 虚拟机](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)和 [Azure 容器实例](../container-instances/container-instances-managed-identity.md)。 如果在其中某一种服务中托管工作负载，也可以利用该服务的托管标识支持。

你可以使用任何代码编辑器执行本教程中的步骤。 [Visual Studio Code](https://code.visualstudio.com/) 是 Windows、macOS 和 Linux 平台上提供的一个卓越选项。

在本文中，学习如何：

> [!div class="checklist"]
> * 授予对应用程序配置的托管身份访问权限。
> * 配置应用以在连接到应用程序配置时使用托管标识。


## <a name="prerequisites"></a>先决条件

若要完成本教程，必须满足以下先决条件：

* [.NET Core SDK](https://dotnet.microsoft.com/download)。
* [已配置的 Azure Cloud Shell](../cloud-shell/quickstart.md)。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="add-a-managed-identity"></a>添加托管标识

要在门户中设置托管标识，首先创建应用程序，然后启用该功能。

1. 在 [Azure 门户](https://portal.azure.com)中访问应用服务资源。 如果没有可使用的现有应用服务资源，请创建一个。 

1. 在左侧窗格向下滚动到“设置”组，然后选择“标识”。

1. 在“系统分配”选项卡中，将“状态”切换为“启用”并选择“保存”。

1. 当提示启用系统分配的托管标识时，选择“是”。

    ![在应用服务中设置托管标识](./media/set-managed-identity-app-service.png)

## <a name="grant-access-to-app-configuration"></a>授予对应用配置的访问权限

1. 在 [Azure 门户](https://portal.azure.com)中，选择“所有资源”，然后选择在快速入门中创建的应用程序配置存储区。

1. 选择“访问控制(IAM)”。

1. 在“检查访问权限”选项卡中，选择“添加角色分配”卡 UI 中的“添加”。

1. 在“角色”下，选择“应用程序配置数据读取者” 。 将“访问权限分配对象”下，选择“应用服务”（在“系统分配的托管标识”下）。

1. 在“订阅”下，选择 Azure 订阅。 选择应用的应用服务资源。

1. 选择“保存”。

    ![添加托管标识](./media/add-managed-identity.png)


## <a name="use-a-managed-identity"></a>使用托管标识

1. 添加对 Azure.Identity 包的引用：

    ```bash
    dotnet add package Azure.Identity
    ```

1. 查找应用程序配置存储区的终结点。 此 URL 列于 Azure 门户中的存储的“访问密钥”选项卡上。

1. 打开“appsettings.json”，并添加以下脚本。 将 \<service_endpoint>（含括号）替换为应用程序配置存储区的 URL。

    ```json
    "AppConfig": {
        "Endpoint": "<service_endpoint>"
    }
    ```

1. 打开 Program.cs，然后添加对 `Azure.Identity` 和 `Microsoft.Azure.Services.AppAuthentication` 命名空间的引用：

    ```csharp-interactive
    using Azure.Identity;
    ```

1. 如果只需要访问直接存储在应用程序配置中的值，请通过替换 `config.AddAzureAppConfiguration()` 方法（可在 `Microsoft.Azure.AppConfiguration.AspNetCore` 包中找到）来更新 `CreateWebHostBuilder` 方法。

    > [!IMPORTANT]
    > `CreateHostBuilder` 替换 .NET Core 3.0 中的 `CreateWebHostBuilder`。  根据环境选择正确的语法。

    ### <a name="net-core-5x"></a>[.NET Core 5.x](#tab/core5x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
                webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
                {
                    var settings = config.Build();
                    config.AddAzureAppConfiguration(options =>
                        options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential()));
                })
                .UseStartup<Startup>());
    ```

    ### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
                webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
                {
                    var settings = config.Build();
                    config.AddAzureAppConfiguration(options =>
                        options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential()));
                })
                .UseStartup<Startup>());
    ```

    ### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
               .ConfigureAppConfiguration((hostingContext, config) =>
               {
                   var settings = config.Build();
                   config.AddAzureAppConfiguration(options =>
                       options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential()));
               })
               .UseStartup<Startup>();
    ```

    ---

    > [!NOTE]
    > 如果要使用用户分配的托管标识，请确保在创建 [ManagedIdentityCredential](/dotnet/api/azure.identity.managedidentitycredential) 时指定 clientId。
    >```csharp
    >config.AddAzureAppConfiguration(options =>
    >       {
    >           options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential("<your_clientId>"))
    >        });
    >```
    >如“[Azure 资源的托管标识常见问题解答](../active-directory/managed-identities-azure-resources/known-issues.md)”中所述，系统将采用默认方法来解析使用的是哪个托管标识。 在这种情况下，Azure 标识库将强制你指定所需的标识，以免将来可能出现运行时问题（例如，如果添加了一个新的用户分配的托管标识，或启用了系统分配的托管标识）。 因此，即使只定义了一个用户分配的托管标识，并且没有系统分配的托管标识，也需要指定 clientId。
 
    

## <a name="deploy-your-application"></a>部署应用程序

使用托管标识需要将应用部署到 Azure 服务。 托管标识不能用于对本地运行的应用进行身份验证。 若要部署你在[使用应用配置创建 ASP.NET Core 应用](./quickstart-aspnet-core-app.md)快速入门中创建并修改的 .NET Core 应用，以便使用托管标识，请按照[发布 Web 应用](/azure/app-service/quickstart-dotnetcore?tabs=netcore31&pivots=development-environment-vs#publish-your-web-app)中的指南操作。

除了应用服务，许多其他 Azure 服务也支持托管标识。 有关详细信息，请参阅[支持 Azure 资源托管标识的服务](/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities)。

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>后续步骤
在本教程中，通过添加 Azure 托管标识简化了应用配置访问并改进了应用凭据管理。 若要了解有关如何使用应用程序配置的更多信息，请继续阅读 Azure CLI 示例。

> [!div class="nextstepaction"]
> [CLI 示例](./cli-samples.md)