---
title: 包含文件
description: include 文件
services: azure-communication-services
author: peiliu
manager: vravikumar
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 06/30/2021
ms.topic: include
ms.custom: include file
ms.author: peiliu
ms.openlocfilehash: 983ce28f736bd36255e65073b202ff6c5761b059
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114292434"
---
## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 适用于操作系统的最新版本 [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core)。
- 活动的通信服务资源和连接字符串。 [创建通信服务资源](../create-communication-resource.md)。
- 在 Azure 门户中创建 [Application Insights 资源](../../../azure-monitor/app/create-new-resource.md)。

## <a name="setting-up"></a>设置

### <a name="create-a-new-c-application"></a>新建 C# 应用程序

在控制台窗口（例如 cmd、PowerShell 或 Bash）中，使用 `dotnet new` 命令创建名为 `TelemetryAppInsightsQuickstart` 的新控制台应用。 此命令将创建包含单个源文件的简单“Hello World”C# 项目：**Program.cs**。

```console
dotnet new console -o TelemetryAppInsightsQuickstart
```

将目录更改为新创建的应用文件夹，并使用 `dotnet build` 命令编译应用程序。

```console
cd TelemetryAppInsightsQuickstart
dotnet build
```

### <a name="install-the-package"></a>安装包

仍在应用程序目录中时，使用 `dotnet add package` 命令安装适用于 .NET 包的 Azure 通信服务标识库。

```console
dotnet add package Azure.Communication.Identity --version 1.0.0
```

还需要安装 Azure Monitor Exporter for OpenTelemetry 库。

```console
dotnet add package Azure.Monitor.OpenTelemetry.Exporter
```

### <a name="set-up-the-app-framework"></a>设置应用框架

从项目目录中执行以下操作：

1. 在文本编辑器中打开 Program.cs 文件
2. 添加 `using` 指令以包括 `Azure.Communication.Identity` 命名空间
3. 添加 `using` 指令以包括 `Azure.Monitor.OpenTelemetry.Exporter` 命名空间；
4. 添加 `using` 指令以包括 `OpenTelemetry.Trace` 命名空间；
5. 更新 `Main` 方法声明以支持异步代码

使用以下代码以开始执行以下操作：

```csharp
using System;
using Azure.Communication;
using Azure.Communication.Identity;
using Azure.Monitor.OpenTelemetry.Exporter;
using OpenTelemetry.Trace;

namespace TelemetryAppInsightsQuickstart
{
    class Program
    {
        static async Task Main(string[] args)
        {
            Console.WriteLine("Azure Communication Services - Export Telemetry to Application Insights");

            // Quickstart code goes here
        }
    }
}
```
## <a name="setting-up-the-telemetry-tracer-with-communication-identity-sdk-calls"></a>通过调用通信标识 SDK 设置遥测跟踪器

使用连接字符串初始化 `CommunicationIdentityClient`。 了解如何[管理资源的连接字符串](../create-communication-resource.md#store-your-connection-string)。

创建客户端后，必须定义一个 `Activity Source` 来跟踪所有活动。 然后，你可以使用 `Activity Source` 来启动 `Activity`，后者将用于跟踪 `CreateUserAsync` SDK 调用。 请注意，还可以使用 `SetTag` 方法定义要在每个 `Activity` 中跟踪的自定义属性。

将为 `GetTokenAsync` 函数执行类似的跟踪模式。

创建一个名为 `TracedSample` 的新函数并添加以下代码：

```csharp
public static async Task TracedSample()
{
    // This code demonstrates how to fetch your connection string
    // from an environment variable.
    string connectionString = Environment.GetEnvironmentVariable("COMMUNICATION_SERVICES_CONNECTION_STRING");
    var client = new CommunicationIdentityClient(connectionString);

    using var source = new ActivitySource("Quickstart.IdentityTelemetry");
    CommunicationUserIdentifier identity = null;

    using (var activity = source.StartActivity("Create User Activity"))
    {
        var identityResponse = await client.CreateUserAsync();

        identity = identityResponse.Value;
        Console.WriteLine($"\nCreated an identity with ID: {identity.Id}");
        activity?.SetTag("Identity id", identity.Id);
    }

    using (var activity = source.StartActivity("Get Token Activity"))
    {
        var tokenResponse = await client.GetTokenAsync(identity, scopes: new[] { CommunicationTokenScope.Chat });

        activity?.SetTag("Token value", tokenResponse.Value.Token);
        activity?.SetTag("Expires on", tokenResponse.Value.ExpiresOn);

        Console.WriteLine($"\nIssued an access token with 'chat' scope that expires at {tokenResponse.Value.ExpiresOn}:");
    }
}
```

## <a name="funneling-telemetry-data-to-application-insights"></a>将遥测数据汇集到 Application Insights

SDK 调用与活动包装在一起后，你可以添加 OpenTelemetry 跟踪导出程序，并将数据汇集到 Application Insights 资源中。

可以选择使用某些资源属性定义字典，这些属性将显示在 Application Insights 中。
然后调用 `AddSource`，并使用在 `TracedSample` 中定义的同一个“活动源”名称。
还需要从 Application Insights 资源中获取连接字符串，并将其传递给 `AddAzureMonitorTraceExporter()`。 这会将遥测数据汇集到 Application Insights 资源。

最后，调用并等待包含我们的 SDK 调用的 `TracedSample()` 函数。

将以下代码添加到 `Main` 方法中：

```csharp
var resourceAttributes = new Dictionary<string, object> { { "service.name", "<service-name>" }, { "service.instance.id", "<service-instance-id>" } };
var resourceBuilder = ResourceBuilder.CreateDefault().AddAttributes(resourceAttributes);

using var tracerProvider = Sdk.CreateTracerProviderBuilder()
    .SetResourceBuilder(resourceBuilder)
    .AddSource("Quickstart.IdentityTelemetry")
    .AddAzureMonitorTraceExporter(o =>
    {
        o.ConnectionString = Environment.GetEnvironmentVariable("APPLICATION_INSIGHTS_CONNECTION_STRING");
    })
    .Build();

await TracedSample();
```

## <a name="run-the-code"></a>运行代码

从应用程序目录使用 `dotnet run` 命令运行应用程序。

```console
dotnet run
```