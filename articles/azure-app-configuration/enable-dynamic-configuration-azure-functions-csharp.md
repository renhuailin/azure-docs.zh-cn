---
title: 关于在 Azure Functions 应用中使用 Azure 应用程序配置动态配置的教程 | Microsoft Docs
description: 本教程介绍如何动态更新 Azure Functions 应用的配置数据
services: azure-app-configuration
documentationcenter: ''
author: zhenlan
manager: qingye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 11/17/2019
ms.author: zhenlwa
ms.custom: devx-track-csharp, azure-functions
ms.tgt_pltfrm: Azure Functions
ms.openlocfilehash: add4b54adb02db09536f4e56a7f039c46245c182
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "97963548"
---
# <a name="tutorial-use-dynamic-configuration-in-an-azure-functions-app"></a>教程：在 Azure Functions 应用中使用动态配置

应用程序配置 .NET 配置提供程序支持缓存和动态刷新由应用程序活动驱动的配置。 本教程演示如何在代码中实现动态配置更新。 它建立在本快速入门中介绍的 Azure Functions 应用之上。 在继续本教程之前，请先完成[使用 Azure 应用程序配置创建 Azure Functions 应用](./quickstart-azure-functions-csharp.md)这一教程。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 设置 Azure Functions 应用，使其能够更新配置以响应应用程序配置存储区中的更改。
> * 将最新配置注入到 Azure Functions 调用中。

## <a name="prerequisites"></a>先决条件

- Azure 订阅 - [创建免费帐户](https://azure.microsoft.com/free/)
- 包含 Azure 开发工作负载的 [Visual Studio 2019](https://visualstudio.microsoft.com/vs) 
- [Azure Functions 工具](../azure-functions/functions-develop-vs.md#check-your-tools-version)
- 完成快速入门[使用 Azure 应用程序配置创建 Azure Functions 应用](./quickstart-azure-functions-csharp.md)

## <a name="reload-data-from-app-configuration"></a>从应用配置重载数据

1. 打开 Startup.cs 并更新 `ConfigureAppConfiguration` 方法。 

   `ConfigureRefresh` 方法注册一个设置，每当在应用程序中触发刷新时会检查更改，当你在稍后步骤中添加 `_configurationRefresher.TryRefreshAsync()` 时，将执行该操作。 `refreshAll` 参数指示应用配置提供程序在注册设置中检测到更改时重新加载整个配置。

    注册刷新的所有设置的默认缓存到期时间为 30 秒。 可以通过调用 `AzureAppConfigurationRefreshOptions.SetCacheExpiration` 方法来更新它。

    ```csharp
    public override void ConfigureAppConfiguration(IFunctionsConfigurationBuilder builder)
    {
        builder.ConfigurationBuilder.AddAzureAppConfiguration(options =>
        {
            options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                   // Load all keys that start with `TestApp:`
                   .Select("TestApp:*")
                   // Configure to reload configuration if the registered 'Sentinel' key is modified
                   .ConfigureRefresh(refreshOptions =>
                      refreshOptions.Register("TestApp:Settings:Sentinel", refreshAll: true));
        });
    }
    ```

   > [!TIP]
   > 在应用配置中更新多个键值时，通常不建议应用程序在进行所有更改之前重新加载配置。 可以注册 sentinel 密钥，并且仅在完成所有其他配置更改后更新它。 这有助于确保应用程序中配置的一致性。

2. 更新 `Configure` 方法，通过依赖项注入提供 Azure 应用程序配置服务。

    ```csharp
    public override void Configure(IFunctionsHostBuilder builder)
    {
        builder.Services.AddAzureAppConfiguration();
    }
    ```

3. 打开 Function1.cs，并添加以下命名空间。

    ```csharp
    using System.Linq;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

   更新构造函数以通过依赖项注入获取 `IConfigurationRefresherProvider` 的实例，可以从中获取 `IConfigurationRefresher` 的实例。

    ```csharp
    private readonly IConfiguration _configuration;
    private readonly IConfigurationRefresher _configurationRefresher;

    public Function1(IConfiguration configuration, IConfigurationRefresherProvider refresherProvider)
    {
        _configuration = configuration;
        _configurationRefresher = refresherProvider.Refreshers.First();
    }
    ```

4. 更新 `Run` 方法，并在 Functions 调用开始时通知使用 `TryRefreshAsync` 方法刷新配置。 如果未到达缓存过期时间范围，则将不会进行操作。 如果希望在不阻止当前函数调用的情况下刷新配置，请删除 `await` 运算符。 在这种情况下，以后的函数调用将获得更新的值。

    ```csharp
    public async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        await _configurationRefresher.TryRefreshAsync(); 

        string keyName = "TestApp:Settings:Message";
        string message = _configuration[keyName];
            
        return message != null
            ? (ActionResult)new OkObjectResult(message)
            : new BadRequestObjectResult($"Please create a key-value with the key '{keyName}' in App Configuration.");
    }
    ```

## <a name="test-the-function-locally"></a>在本地测试函数

1. 设置名为“ConnectionString”的环境变量，并将其设置为应用程序配置存储区的访问键  。 如果使用 Windows 命令提示符，则请运行以下命令并重启命令提示符，这样更改才会生效：

    ```console
    setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    如果使用 Windows PowerShell，请运行以下命令：

    ```powershell
    $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    如果使用 macOS 或 Linux，则请运行以下命令：

    ```console
    export ConnectionString='connection-string-of-your-app-configuration-store'
    ```

2. 若要测试函数，请按 F5。 如果系统提示，请按 Visual Studio 的请求下载和安装 Azure Functions Core (CLI) 工具  。 你还需要启用防火墙例外，这样工具才能处理 HTTP 请求。

3. 从 Azure Functions 运行时输出复制函数的 URL。

    ![在 VS 中的函数调试快速入门](./media/quickstarts/function-visual-studio-debugging.png)

4. 将 HTTP 请求的 URL 粘贴到浏览器的地址栏。 下图显示了浏览器中函数返回的本地 GET 请求的响应。

    ![本地函数启动快速入门](./media/quickstarts/dotnet-core-function-launch-local.png)

5. 登录 [Azure 门户](https://portal.azure.com)。 选择“所有资源”，然后选择在快速入门中创建的应用程序配置存储。

6. 选择“配置资源管理器”并更新以下密钥值：

    | 密钥 | 值 |
    |---|---|
    | TestApp:Settings:Message | Azure 应用配置的数据 - 已更新 |

   然后创建 sentinel 密钥或修改其值（如果该密钥已存在），例如，

    | 密钥 | Value |
    |---|---|
    | TestApp:Settings:Sentinel | v1 |


7. 多次刷新浏览器。 如果缓存的设置在 30 秒后过期，则页面会显示 Functions 调用的响应已有更新的值。

    ![本地函数刷新快速入门](./media/quickstarts/dotnet-core-function-refresh-local.png)

> [!NOTE]
> 本教程中使用的示例代码可从[应用程序配置 GitHub 存储库](https://github.com/Azure/AppConfiguration/tree/master/examples/DotNetCore/AzureFunction)下载。

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>后续步骤

在本教程中，你学习了如何使 Azure Functions 应用能够动态刷新来自应用程序配置的配置设置。 若要了解如何使用 Azure 托管标识来简化对应用程序配置的访问，请继续学习下一篇教程。

> [!div class="nextstepaction"]
> [托管标识集成](./howto-integrate-azure-managed-service-identity.md)
