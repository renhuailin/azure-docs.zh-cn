---
title: 将 Azure 应用配置与 Azure Functions 结合使用的快速入门 | Microsoft Docs
description: 在本快速入门中，使用 Azure 应用程序配置和 C# 创建 Azure Functions 应用。 创建并连接到应用程序配置存储。 在本地测试函数。
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
ms.topic: quickstart
ms.date: 06/02/2021
ms.author: alkemper
ms.openlocfilehash: cf76cdc52ba7b4ebcf99e0b32d9b56e59a9bf837
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2021
ms.locfileid: "111411658"
---
# <a name="quickstart-create-an-azure-functions-app-with-azure-app-configuration"></a>快速入门：使用 Azure 应用程序配置创建 Azure Functions 应用

在本快速入门中，你会将 Azure 应用程序配置服务合并到 Azure Functions 应用中，以集中存储和管理与代码分离的所有应用程序设置。

## <a name="prerequisites"></a>先决条件

- Azure 订阅 - [创建免费帐户](https://azure.microsoft.com/free/dotnet)
- 包含 **Azure 开发** 工作负载的 [Visual Studio 2019](https://visualstudio.microsoft.com/vs)。
- [Azure Functions 工具](../azure-functions/functions-develop-vs.md#check-your-tools-version)

## <a name="create-an-app-configuration-store"></a>创建应用配置存储区

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. 选择“配置资源管理器” > “+ 创建” > “键-值”来添加以下键值对    ：

    | 密钥 | 值 |
    |---|---|
    | TestApp:Settings:Message | Azure 应用配置的数据 |

    暂时将“标签”和“内容类型”保留为空   。

8. 选择“应用”。

## <a name="create-a-functions-app"></a>创建 Functions 应用

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

## <a name="connect-to-an-app-configuration-store"></a>连接到应用程序配置存储区
此项目将使用 [.NET Azure Functions 中的依赖项注入](../azure-functions/functions-dotnet-dependency-injection.md)，并将 Azure 应用程序配置添加为额外的配置源。

1. 右键单击项目，然后选择“管理 NuGet 包”  。 在“浏览”选项卡中，搜索以下 NuGet 包并将其添加到项目中。
   - [Microsoft.Extensions.Configuration.AzureAppConfiguration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureAppConfiguration/) 版本 4.1.0 或更高版本
   - [Microsoft.Azure.Functions.Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/) 版本 1.1.0 或更高版本 

2. 使用以下代码添加新文件 Startup.cs。 它定义了一个名为 `Startup` 的类，该类实现了 `FunctionsStartup` 抽象类。 程序集属性用于指定 Azure Functions 启动期间使用的类型名称。

    重写 `ConfigureAppConfiguration` 方法，并通过调用 `AddAzureAppConfiguration()` 将 Azure 应用程序配置提供程序添加为额外的配置源。 `Configure` 方法保留为空，因为此时无需注册任何服务。
    
    ```csharp
    using System;
    using Microsoft.Azure.Functions.Extensions.DependencyInjection;
    using Microsoft.Extensions.Configuration;

    [assembly: FunctionsStartup(typeof(FunctionApp.Startup))]

    namespace FunctionApp
    {
        class Startup : FunctionsStartup
        {
            public override void ConfigureAppConfiguration(IFunctionsConfigurationBuilder builder)
            {
                string cs = Environment.GetEnvironmentVariable("ConnectionString");
                builder.ConfigurationBuilder.AddAzureAppConfiguration(cs);
            }

            public override void Configure(IFunctionsHostBuilder builder)
            {
            }
        }
    }
    ```

3. 打开 Function1.cs，添加以下命名空间。

    ```csharp
    using Microsoft.Extensions.Configuration;
    ```

   添加一个构造函数，用于通过依赖项注入获得 `IConfiguration` 实例。

    ```csharp
    private readonly IConfiguration _configuration;

    public Function1(IConfiguration configuration)
    {
        _configuration = configuration;
    }
    ```

4. 更新 `Run` 方法以从配置中读取值。

    ```csharp
    public async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        string keyName = "TestApp:Settings:Message";
        string message = _configuration[keyName];

        return message != null
            ? (ActionResult)new OkObjectResult(message)
            : new BadRequestObjectResult($"Please create a key-value with the key '{keyName}' in App Configuration.");
    }
    ```

    > [!NOTE]
    > `Function1` 类和 `Run` 方法不应是静态的。 如果已自动生成 `static` 修饰符，请将其删除。

## <a name="test-the-function-locally"></a>在本地测试函数

1. 设置名为“ConnectionString”的环境变量，并将其设置为应用程序配置存储区的访问密钥。 如果使用 Windows 命令提示符，则请运行以下命令并重启命令提示符，这样更改才会生效：

    ```cmd
        setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    如果使用 Windows PowerShell，请运行以下命令：

    ```azurepowershell
        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    如果使用 macOS 或 Linux，则请运行以下命令：

    ```bash
        export ConnectionString='connection-string-of-your-app-configuration-store'
    ```

2. 按 F5 测试函数。 如果系统提示，请按 Visual Studio 的请求下载和安装 Azure Functions Core (CLI) 工具。 你还需要启用防火墙例外，这样工具才能处理 HTTP 请求。

3. 从 Azure Functions 运行时输出复制函数的 URL。

    ![在 VS 中的函数调试快速入门](./media/quickstarts/function-visual-studio-debugging.png)

4. 将 HTTP 请求的 URL 粘贴到浏览器的地址栏。 下图显示了浏览器中函数返回的本地 GET 请求的响应。

    ![本地函数启动快速入门](./media/quickstarts/dotnet-core-function-launch-local.png)

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>后续步骤

在本快速入门中，你创建了一个新的应用程序配置存储区，并通过[应用程序配置提供程序](/dotnet/api/Microsoft.Extensions.Configuration.AzureAppConfiguration)将其用于 Azure Functions 应用。 若要了解如何更新 Azure Functions 应用以动态刷新配置，请继续学习下一个教程。

> [!div class="nextstepaction"]
> [在 Azure Functions 应用中实现动态配置](./enable-dynamic-configuration-azure-functions-csharp.md)
