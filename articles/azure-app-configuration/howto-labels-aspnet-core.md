---
title: 使用标签提供按环境配置值。
titleSuffix: Azure App Configuration
description: 本文介绍如何使用标签检索当前运行应用的环境的应用配置值。
ms.service: azure-app-configuration
author: AlexandraKemperMS
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 3/12/2020
ms.author: alkemper
ms.openlocfilehash: e6d9aadff5fba66aef260c674f5a01904b289da2
ms.sourcegitcommit: b11257b15f7f16ed01b9a78c471debb81c30f20c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2021
ms.locfileid: "111592351"
---
# <a name="use-labels-to-provide-per-environment-configuration-values"></a>使用标签提供按环境配置值。

许多应用程序需要针对不同的环境使用不同的配置。 假设应用程序有一个配置值，该配置值定义了用于其后端数据库的连接字符串。 应用程序开发者使用的数据库与生产环境中使用的数据库不同。 当应用程序从开发环境转移到生产环境时，应用程序使用的数据库连接字符串必须进行更改。

在 Azure 应用配置中，可以使用“标签”为同一个键定义不同的值。 例如，可以定义单个键，对开发和生产环境使用不同的值。 可以指定在连接到应用配置时要加载哪个标签。

为了演示此功能，需修改在[快速入门：使用 Azure 应用配置创建 ASP.NET Core 应用](./quickstart-aspnet-core-app.md)中创建的 Web 应用，对开发环境与生产环境使用不同的配置设置。 在继续操作之前，请先完成该快速入门。

## <a name="specify-a-label-when-adding-a-configuration-value"></a>在添加配置值时指定标签

在 Azure 门户中，转到“配置资源管理器”并找到你在该快速入门中创建的 TestApp:Settings:FontColor 键。 选择其上下文菜单，然后选择“添加值”。

> [!div class="mx-imgBorder"]
> ![“添加值”菜单项](media/labels-add-value.png)

在“添加值”屏幕上，输入“红色”作为值，输入“开发”作为标签。    将“内容类型”保留为空。 选择“应用”。

## <a name="load-configuration-values-with-a-specified-label"></a>加载具有指定标签的配置值

默认情况下，Azure 应用配置仅加载没有标签的配置值。 如果你为配置值定义了标签，则需要指定在连接到应用配置时要使用的标签。

在上一部分中，你为开发环境创建了一个不同的配置值。 使用 `HostingEnvironment.EnvironmentName` 变量来动态确定应用当前在哪个环境中运行。 若要了解详细信息，请参阅[在 ASP.NET Core 中使用多个环境](/aspnet/core/fundamentals/environments)。

添加对 [Microsoft.Extensions.Configuration.AzureAppConfiguration](/dotnet/api/microsoft.extensions.configuration.azureappconfiguration) 命名空间的引用，以便访问 [KeyFilter](/dotnet/api/microsoft.extensions.configuration.azureappconfiguration.keyfilter) 和 [LabelFilter](/dotnet/api/microsoft.extensions.configuration.azureappconfiguration.labelfilter) 类。

```csharp
using Microsoft.Extensions.Configuration.AzureAppConfiguration;
``` 

通过将环境名称传递给 `Select` 方法，加载其标签与当前环境对应的配置值：

### <a name="net-core-5x"></a>[.NET Core 5.x](#tab/core5x)

```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
        {
            var settings = config.Build();
            config.AddAzureAppConfiguration(options =>
                options
                    .Connect(settings.GetConnectionString("AppConfig"))
                    // Load configuration values with no label
                    .Select(KeyFilter.Any, LabelFilter.Null)
                    // Override with any configuration values specific to current hosting env
                    .Select(KeyFilter.Any, hostingContext.HostingEnvironment.EnvironmentName)
            );
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
                options
                    .Connect(settings.GetConnectionString("AppConfig"))
                    // Load configuration values with no label
                    .Select(KeyFilter.Any, LabelFilter.Null)
                    // Override with any configuration values specific to current hosting env
                    .Select(KeyFilter.Any, hostingContext.HostingEnvironment.EnvironmentName)
            );
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
                options
                    .Connect(settings.GetConnectionString("AppConfig"))
                    // Load configuration values with no label
                    .Select(KeyFilter.Any, LabelFilter.Null)
                    // Override with any configuration values specific to current hosting env
                    .Select(KeyFilter.Any, hostingContext.HostingEnvironment.EnvironmentName)
            );
        })
        .UseStartup<Startup>();
```
---


> [!IMPORTANT]
> 前面的代码片段使用机密管理器工具加载应用配置连接字符串。 有关使用机密管理器存储连接字符串的信息，请参阅[将 Azure 应用配置与 ASP.NET Core 结合使用的快速入门](quickstart-aspnet-core-app.md)。

`Select` 方法将调用两次。 第一次，它加载没有标签的配置值。 然后，它加载其标签与当前环境对应的配置值。 这些特定于环境的值会替代没有标签的任何对应值。 不需要为每个键都定义特定于环境的值。 如果某个键没有值具有与当前环境对应的标签，它将使用没有标签的值。

## <a name="test-in-different-environments"></a>在不同的环境中测试

打开 `Properties` 目录下的 `launchSettings.json` 文件。 在 `profiles` 下找到 `config` 条目。 在 `environmentVariables` 部分中，将 `ASPNETCORE_ENVIRONMENT` 变量设置为 `Production`。

设置新值后，生成并运行你的应用程序。

```dotnetcli
dotnet build
dotnet run
```

在 Web 浏览器中转到 `http://localhost:5000`。 你会注意到，字体颜色为黑色。

![以生产配置运行的 Web 应用程序](media/labels-website-prod.png)

更新 `launchSettings.json`，将 `ASPNETCORE_ENVIRONMENT` 变量设置为 `Development`。 再次运行 `dotnet run`。 

你会注意到，字体颜色现在为红色。 这是因为应用程序现在使用具有 `Development` 标签的 `TestApp:Settings:FontColor` 的值。 所有其他配置值保持与其生产值相同。

![以开发配置运行的 Web 应用程序](media/labels-website-dev.png)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [ASP.NET Core 中的配置](/aspnet/core/fundamentals/configuration/)
