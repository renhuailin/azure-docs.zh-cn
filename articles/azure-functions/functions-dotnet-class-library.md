---
title: 使用 Azure Functions 开发 C# 类库函数
description: 了解如何使用 C# 开发代码，并将其发布为在进程内与 Azure Functions 运行时配合运行的类库。
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 07/24/2021
ms.openlocfilehash: 5681a985769f49429e5b92946311c3d10ea8ecf6
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121862344"
---
# <a name="develop-c-class-library-functions-using-azure-functions"></a>使用 Azure Functions 开发 C# 类库函数

<!-- When updating this article, make corresponding changes to any duplicate content in functions-reference-csharp.md -->

本文介绍了如何在 .NET 类库中使用 C# 开发 Azure Functions。

>[!IMPORTANT]
>本文支持在进程内与运行时配合运行的 .NET 类库函数。 Functions 还通过在进程外独立于运行时运行 C# 函数，来支持 .NET 1.5。 有关详细信息，请参阅 [.NET 隔离进程函数](dotnet-isolated-process-guide.md)。

作为 C# 开发人员，你可能还会对以下文章之一感兴趣：

| 入门 | 概念| 引导式学习/示例 |
|--| -- |--| 
| <ul><li>[使用 Visual Studio](functions-create-your-first-function-visual-studio.md)</li><li>[使用 Visual Studio Code](create-first-function-vs-code-csharp.md)</li><li>[使用命令行工具](create-first-function-cli-csharp.md)</li></ul> | <ul><li>[托管选项](functions-scale.md)</li><li>[性能&nbsp;注意事项](functions-best-practices.md)</li><li>[Visual Studio 开发](functions-develop-vs.md)</li><li>[依赖关系注入](functions-dotnet-dependency-injection.md)</li></ul> | <ul><li>[创建无服务器应用程序](/learn/paths/create-serverless-applications/)</li><li>[C# 示例](/samples/browse/?products=azure-functions&languages=csharp)</li></ul> |

Azure Functions 支持 C# 和 C# 脚本编程语言。 如果要寻找有关[在 Azure 门户中使用 C#](functions-create-function-app-portal.md) 的指南，请参阅 [C# 脚本 (.csx) 开发人员参考](functions-reference-csharp.md)。

[!INCLUDE [functions-dotnet-supported-versions](../../includes/functions-dotnet-supported-versions.md)]

### <a name="functions-v2x-considerations"></a>Functions v2.x 注意事项

面向最新 2.x 版 (`~2`) 的函数应用将自动升级，以在 .NET Core 3.1 上运行。 由于 .NET Core 版本之间存在中断性变更，因此，针对 .NET Core 2.2 开发和编译的所有应用并非都可以安全升级到 .NET Core 3.1。 可以通过将函数应用固定为 `~2.0` 来选择退出此升级。 Functions 还会检测不兼容的 API，并可将应用固定为 `~2.0`，以防止在 .NET Core 3.1 上错误执行。 

>[!NOTE]
>如果函数应用已固定为 `~2.0`，而你将此版本目标更改为 `~2`，则函数应用可能会中断工作。 如果使用 ARM 模板进行部署，请检查模板中的版本。 如果发生这种情况，请改回版本以面向 `~2.0`，并解决兼容性问题。 

面向 `~2.0` 的函数应用将继续在 .NET Core 2.2 上运行。 此 .NET Core 版本不再接收安全更新和其他维护更新。 有关详细信息，请参阅[此公告页](https://github.com/Azure/app-service-announcements/issues/266)。 

应该尽快使函数与 .NET Core 3.1 兼容。 解决这些问题后，将版本改回到 `~2` 或升级到 `~3`。 若要详细了解如何选择 Functions 运行时的目标版本，请参阅[如何选择 Azure Functions 运行时的目标版本](set-runtime-version.md)。

在 Linux 上的高级或专用（应用服务）计划中运行时，请不要选择特定的映像作为目标，而是通过将 `linuxFxVersion` 站点配置设置指定为 `DOCKER|mcr.microsoft.com/azure-functions/dotnet:2.0.14786-appservice` 来固定版本。若要了解如何设置 `linuxFxVersion`，请参阅 [Linux 上的手动版本更新](set-runtime-version.md#manual-version-updates-on-linux)。

## <a name="functions-class-library-project"></a>Functions 类库项目

在 Visual Studio 中，**Azure Functions** 项目模板会创建一个 C# 类库项目，它包含以下文件：

* [host.json](functions-host-json.md) - 存储着在本地或者在 Azure 中运行时会影响项目中的所有函数的配置设置。
* [local.settings.json](functions-develop-local.md#local-settings-file) - 存储着在本地运行时使用的应用设置和连接字符串。 此文件包含机密且不会发布到 Azure 中的函数应用中。 而是，应[将应用设置添加到函数应用](functions-develop-vs.md#function-app-settings)。

生成项目时，在生成输出目录中生成如下例所示的文件夹结构：

```
<framework.version>
 | - bin
 | - MyFirstFunction
 | | - function.json
 | - MySecondFunction
 | | - function.json
 | - host.json
```

部署到 Azure 中函数应用的正是此目录。 Functions 运行时 [2.x 版](functions-versions.md) 中所需的绑定扩展[作为 NuGet 包添加到项目中](./functions-bindings-register.md#vs)。

> [!IMPORTANT]
> 生成过程将为每个函数创建一个 *function.json* 文件。 此 *function.json* 文件不应直接编辑。 无法通过编辑此文件来更改绑定配置或禁用函数。 要了解如何禁用函数，请参阅[如何禁用函数](disable-function.md)。


## <a name="methods-recognized-as-functions"></a>识别为函数的方法

在类库中，函数是具有 `FunctionName` 和触发器属性的静态方法，如以下示例中所示：

```csharp
public static class SimpleExample
{
    [FunctionName("QueueTrigger")]
    public static void Run(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        ILogger log)
    {
        log.LogInformation($"C# function processed: {myQueueItem}");
    }
} 
```

`FunctionName` 属性将该方法标记为函数入口点。 该名称在项目中必须是唯一的，以字母开头，并且只包含字母、数字、`_` 和 `-`，长度不得超过 127 个字符。 项目模板通常创建一个名为 `Run` 的方法，但方法名称可以是任何有效的 C# 方法名称。

触发器属性指定触发器类型并将输入数据绑定到一个方法参数。 示例函数将由一条队列消息触发，并且队列消息将传递到该方法中的 `myQueueItem` 参数。

## <a name="method-signature-parameters"></a>方法签名参数

方法签名可能包含不与触发器属性一起使用的参数。 下面是可以包含的其他一些参数：

* [输入和输出绑定](functions-triggers-bindings.md)通过使用属性修饰来进行此类标记。  
* 用于[日志](#logging)的 `ILogger` 或 `TraceWriter`（仅限[版本 1.x](functions-versions.md#creating-1x-apps)）参数。
* 用于[正常关闭](#cancellation-tokens)的 `CancellationToken` 参数。
* 用于获取触发器元数据的[绑定表达式](./functions-bindings-expressions-patterns.md)参数。

函数签名中的参数顺序并不重要。 例如，可以在其他绑定之前或之后放置触发器参数，也可以在触发器或绑定参数之前或之后添加记录器参数。

### <a name="output-bindings"></a>输出绑定

一个函数可以有零个或一个使用输出参数定义的输出绑定。 

以下示例对上一个示例进行了修改，其中添加了一个名为 `myQueueItemCopy` 的输出队列绑定。 该函数将触发函数的消息内容写入到另一个队列中的新消息。

```csharp
public static class SimpleExampleWithOutput
{
    [FunctionName("CopyQueueMessage")]
    public static void Run(
        [QueueTrigger("myqueue-items-source")] string myQueueItem, 
        [Queue("myqueue-items-destination")] out string myQueueItemCopy,
        ILogger log)
    {
        log.LogInformation($"CopyQueueMessage function processed: {myQueueItem}");
        myQueueItemCopy = myQueueItem;
    }
}
```

该函数退出时，将写入分配给输出绑定的值。 只需将值分配给多个输出参数，即可在一个函数中使用多个输出绑定。 

绑定参考文章（例如，[存储队列](functions-bindings-storage-queue.md)）说明了可用于触发器、输入或输出绑定特性的参数类型。

### <a name="binding-expressions-example"></a>绑定表达式示例

以下代码从应用设置中获取要监视的队列的名称，并在 `insertionTime` 参数中获取队列消息创建时间。

```csharp
public static class BindingExpressionsExample
{
    [FunctionName("LogQueueMessage")]
    public static void Run(
        [QueueTrigger("%queueappsetting%")] string myQueueItem,
        DateTimeOffset insertionTime,
        ILogger log)
    {
        log.LogInformation($"Message content: {myQueueItem}");
        log.LogInformation($"Created at: {insertionTime}");
    }
}
```

## <a name="autogenerated-functionjson"></a>自动生成的 function.json

生成过程会在生成文件中的一个函数文件夹中创建一个 *function.json* 文件。 如前所述，此文件不应直接编辑。 无法通过编辑此文件来更改绑定配置或禁用函数。 

此文件的用途是向缩放控制器提供用于[对消耗计划做出缩放决策](event-driven-scaling.md)的信息。 因此，该文件仅包含触发器信息，而不包含输入/输出绑定。

生成的 *function.json* 文件包括一个 `configurationSource` 属性，该属性告诉运行时使用 .NET 属性进行绑定，而不是使用 *function.json* 配置。 下面是一个示例：

```json
{
  "generatedBy": "Microsoft.NET.Sdk.Functions-1.0.0.0",
  "configurationSource": "attributes",
  "bindings": [
    {
      "type": "queueTrigger",
      "queueName": "%input-queue-name%",
      "name": "myQueueItem"
    }
  ],
  "disabled": false,
  "scriptFile": "..\\bin\\FunctionApp1.dll",
  "entryPoint": "FunctionApp1.QueueTrigger.Run"
}
```

## <a name="microsoftnetsdkfunctions"></a>Microsoft.NET.Sdk.Functions

*function.json* 文件生成是由 NuGet 包 [Microsoft\.NET\.Sdk\.Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions) 生成的。 

Functions 运行时的 1.x 版本和 2.x 版本使用相同的包。 1\.x 项目和 2.x 项目的不同之处在于目标框架。 下面是 *.csproj* 文件的相关部分，其中显示了包含相同 `Sdk` 包的不同目标框架：

# <a name="v2x"></a>[v2.x+](#tab/v2)

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.1</TargetFramework>
  <AzureFunctionsVersion>v2</AzureFunctionsVersion>
</PropertyGroup>
<ItemGroup>
  <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.8" />
</ItemGroup>
```

# <a name="v1x"></a>[v1.x](#tab/v1)

```xml
<PropertyGroup>
  <TargetFramework>net461</TargetFramework>
</PropertyGroup>
<ItemGroup>
  <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.8" />
</ItemGroup>
```
---


`Sdk` 包的依赖关系是触发器和绑定。 1\.x 项目是指 1.x 触发器和绑定，因为这些触发器和绑定面向 .NET Framework，而 2.x 触发器和绑定面向 .NET Core。

`Sdk` 包也依赖于 [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json)，并间接依赖于 [WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage)。 这些依赖关系确保项目使用的包版本与项目面向的 Functions 运行时版本兼容。 例如，`Newtonsoft.Json` 的 11 版可用于 .NET Framework 4.6.1，但面向 .NET Framework 4.6.1 的 Functions 运行时仅与 `Newtonsoft.Json` 9.0.1 兼容。 因此该项目中的函数代码也只能使用 `Newtonsoft.Json` 9.0.1。

GitHub 存储库 [azure\-functions\-vs\-build\-sdk](https://github.com/Azure/azure-functions-vs-build-sdk) 中提供了适用于 `Microsoft.NET.Sdk.Functions` 的源代码。

## <a name="local-runtime-version"></a>本地运行时版本

Visual Studio 使用 [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) 在本地计算机上运行 Functions 项目。 Core Tools 是适用于 Functions 运行时的命令行接口。

如果使用 Windows 安装程序 (MSI) 包或 npm 来安装 Core Tools，则不会影响 Visual Studio 使用的 Core Tools 版本。 对于 Functions 运行时版本 1.x，Visual Studio 在 %USERPROFILE%\AppData\Local\Azure.Functions.Cli 中存储 Core Tools 版本且存储最新版本  。 对于 Functions 2.x，Core Tools 包含在 Azure Functions 和 Web Jobs Tools 扩展中  。 对于 1.x 和 2.x，运行 Functions 项目时可以看到控制台输出中正在使用何种版本：

```terminal
[3/1/2018 9:59:53 AM] Starting Host (HostId=contoso2-1518597420, Version=2.0.11353.0, ProcessId=22020, Debug=False, Attempt=0, FunctionsExtensionVersion=)
```

## <a name="readytorun"></a>ReadyToRun

可以将函数应用编译为 [ReadyToRun 二进制文件](/dotnet/core/whats-new/dotnet-core-3-0#readytorun-images)。 ReadyToRun 是一种预先编译形式，可以提高启动性能，帮助降低在[消耗计划](consumption-plan.md)中运行时的[冷启动](event-driven-scaling.md#cold-start)的影响。

ReadyToRun 在 .NET 3.0 中提供，并且需要 [Azure Functions 运行时版本 3.0](functions-versions.md)。

若要将项目编译为 ReadyToRun，请通过添加 `<PublishReadyToRun>` 和 `<RuntimeIdentifier>` 元素来更新项目文件。 以下是用于发布到 Windows 32 位函数应用的配置。

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp3.1</TargetFramework>
  <AzureFunctionsVersion>v3</AzureFunctionsVersion>
  <PublishReadyToRun>true</PublishReadyToRun>
  <RuntimeIdentifier>win-x86</RuntimeIdentifier>
</PropertyGroup>
```

> [!IMPORTANT]
> ReadyToRun 目前不支持交叉编译。 必须在与部署目标相同的平台上生成应用。 此外，请注意函数应用中配置的“位数”。 例如，如果 Azure 中的函数应用为 Windows 64 位，则在 Windows 上编译应用时必须使用 `win-x64` 作为[运行时标识符](/dotnet/core/rid-catalog)。

也可以通过命令行使用 ReadyToRun 生成应用。 有关详细信息，请参阅 [`dotnet publish`](/dotnet/core/tools/dotnet-publish) 中的 `-p:PublishReadyToRun=true` 选项。

## <a name="supported-types-for-bindings"></a>绑定支持的类型

每个绑定都具有其自己支持的类型；例如，blob 触发器属性可以应用于字符串参数、POCO 参数、`CloudBlockBlob` 参数或任何其他几种受支持的类型之一。 [适用于 blob 绑定的绑定参考文章](functions-bindings-storage-blob-trigger.md#usage)列出了所有受支持的参数类型。 有关详细信息，请参阅[触发器和绑定](functions-triggers-bindings.md)与[每个绑定类型的绑定参考文档](functions-triggers-bindings.md#next-steps)。

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="binding-to-method-return-value"></a>绑定到方法返回值

通过将属性应用于方法返回值，可以对输出绑定使用方法返回值。 有关示例，请参阅[触发器和绑定](./functions-bindings-return-value.md)。 

仅当成功的函数执行始终将返回值传递给输出绑定时，才使用返回值。 否则，请使用 `ICollector` 或 `IAsyncCollector`，如以下部分所示。

## <a name="writing-multiple-output-values"></a>写入多个输出值

若要将多个值写入输出绑定，或者如果成功的函数调用可能无法将任何内容传递给输出绑定，请使用 [`ICollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) 或 [`IAsyncCollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) 类型。 这些类型是只写集合，当方法完成时写入输出绑定。

此示例使用 `ICollector` 将多个队列消息写入到同一队列：

```csharp
public static class ICollectorExample
{
    [FunctionName("CopyQueueMessageICollector")]
    public static void Run(
        [QueueTrigger("myqueue-items-source-3")] string myQueueItem,
        [Queue("myqueue-items-destination")] ICollector<string> myDestinationQueue,
        ILogger log)
    {
        log.LogInformation($"C# function processed: {myQueueItem}");
        myDestinationQueue.Add($"Copy 1: {myQueueItem}");
        myDestinationQueue.Add($"Copy 2: {myQueueItem}");
    }
}
```

## <a name="async"></a>异步

要使函数[异步](/dotnet/csharp/programming-guide/concepts/async/)，请使用 `async` 关键字并返回 `Task` 对象。

```csharp
public static class AsyncExample
{
    [FunctionName("BlobCopy")]
    public static async Task RunAsync(
        [BlobTrigger("sample-images/{blobName}")] Stream blobInput,
        [Blob("sample-images-copies/{blobName}", FileAccess.Write)] Stream blobOutput,
        CancellationToken token,
        ILogger log)
    {
        log.LogInformation($"BlobCopy function processed.");
        await blobInput.CopyToAsync(blobOutput, 4096, token);
    }
}
```

不能在异步函数中使用 `out` 参数。 对于输出绑定，请改用[函数返回值](#binding-to-method-return-value)或[收集器对象](#writing-multiple-output-values)。

## <a name="cancellation-tokens"></a>取消令牌

函数可以接受 [CancellationToken](/dotnet/api/system.threading.cancellationtoken) 参数，以使操作系统能够在函数即将终止时通知代码。 可以使用此通知来确保该函数不会意外终止，导致数据处于不一致状态。

下面的示例演示了如何检查即将发生的函数终止。

```csharp
public static class CancellationTokenExample
{
    public static void Run(
        [QueueTrigger("inputqueue")] string inputText,
        TextWriter logger,
        CancellationToken token)
    {
        for (int i = 0; i < 100; i++)
        {
            if (token.IsCancellationRequested)
            {
                logger.WriteLine("Function was cancelled at iteration {0}", i);
                break;
            }
            Thread.Sleep(5000);
            logger.WriteLine("Normal processing for queue message={0}", inputText);
        }
    }
}
```

## <a name="logging"></a>日志记录

在函数代码中，可以将输出写入日志，这些日志在 Application Insights 中显示为跟踪。 若要将内容写入日志，建议的方法是包括一个 [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger) 类型的参数（通常名为 `log`）。 Functions 运行时的版本 1.x 使用了 `TraceWriter`，后者也将内容写入 Application Insights，但不支持结构化日志记录。 不要使用 `Console.Write` 来写入日志，因为 Application Insights 不会捕获此数据。 

### <a name="ilogger"></a>ILogger

在函数定义中包括一个 [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger) 参数，该参数支持[结构化日志记录](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)。

使用 `ILogger` 对象，可以调用 [ILogger 上的 `Log<level>` 扩展方法](/dotnet/api/microsoft.extensions.logging.loggerextensions#methods)来创建日志。 以下代码会写入类别为“`Function.<YOUR_FUNCTION_NAME>.User.`”的 `Information` 日志：

```cs
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger logger)
{
    logger.LogInformation("Request for item with key={itemKey}.", id);
```

若要详细了解 Functions 如何实现 `ILogger`，请参阅[收集遥测数据](functions-monitoring.md#collecting-telemetry-data)。 以 `Function` 为前缀的类别假定你使用的是 `ILogger` 实例。 如果选择改用 `ILogger<T>`，则类别名称可能改为基于 `T`。  

### <a name="structured-logging"></a>结构化日志记录

占位符的顺序（而不是其名称）确定日志消息中使用的参数。 假设有以下代码：

```csharp
string partitionKey = "partitionKey";
string rowKey = "rowKey";
logger.LogInformation("partitionKey={partitionKey}, rowKey={rowKey}", partitionKey, rowKey);
```

如果保留相同的消息字符串并颠倒参数的顺序，则生成的消息文本将在错误的位置生成值。

以这种方式处理占位符，以便可以执行结构化日志记录。 Application Insights 存储参数名称值对和消息字符串。 结果是消息参数变为可以查询的字段。

如果记录器方法调用类似于前面的示例，则可以查询字段 `customDimensions.prop__rowKey`。 添加 `prop__` 前缀以确保运行时添加的字段和函数代码添加的字段之间没有冲突。

此外，可以通过引用字段 `customDimensions.prop__{OriginalFormat}` 查询原始消息字符串。  

下面是 `customDimensions` 数据的示例 JSON 表示形式：

```json
{
  "customDimensions": {
    "prop__{OriginalFormat}":"C# Queue trigger function processed: {message}",
    "Category":"Function",
    "LogLevel":"Information",
    "prop__message":"c9519cbf-b1e6-4b9b-bf24-cb7d10b1bb89"
  }
}
```

### <a name="log-custom-telemetry"></a><a name="log-custom-telemetry-in-c-functions"></a>记录自定义遥测数据

Functions 特定版本的 Application Insights SDK 可用于将自定义遥测数据从函数发送到 Application Insights：[Microsoft.Azure.WebJobs.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Logging.ApplicationInsights)。 在命令提示符中使用以下命令来安装此包：

# <a name="command"></a>[命令](#tab/cmd)

```cmd
dotnet add package Microsoft.Azure.WebJobs.Logging.ApplicationInsights --version <VERSION>
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -Version <VERSION>
```

---

在此命令中，将 `<VERSION>` 替换为此包的版本，该版本支持 [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs/) 的已安装版本。 

以下 C# 示例使用[自定义遥测 API](../azure-monitor/app/api-custom-events-metrics.md)。 示例针对的是 .NET 类库，但对于 C# 脚本，Application Insights 代码是相同的。

# <a name="v2x"></a>[v2.x+](#tab/v2)

版本 2.x 和更高版本运行时使用 Application Insights 中的较新功能自动将遥测与当前操作进行关联。 不需要手动设置操作 `Id`、`ParentId` 或 `Name` 字段。

```cs
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;

using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
using System.Linq;

namespace functionapp0915
{
    public class HttpTrigger2
    {
        private readonly TelemetryClient telemetryClient;

        /// Using dependency injection will guarantee that you use the same configuration for telemetry collected automatically and manually.
        public HttpTrigger2(TelemetryConfiguration telemetryConfiguration)
        {
            this.telemetryClient = new TelemetryClient(telemetryConfiguration);
        }

        [FunctionName("HttpTrigger2")]
        public Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", Route = null)]
            HttpRequest req, ExecutionContext context, ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            DateTime start = DateTime.UtcNow;

            // Parse query parameter
            string name = req.Query
                .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
                .Value;

            // Write an event to the customEvents table.
            var evt = new EventTelemetry("Function called");
            evt.Context.User.Id = name;
            this.telemetryClient.TrackEvent(evt);

            // Generate a custom metric, in this case let's use ContentLength.
            this.telemetryClient.GetMetric("contentLength").TrackValue(req.ContentLength);

            // Log a custom dependency in the dependencies table.
            var dependency = new DependencyTelemetry
            {
                Name = "GET api/planets/1/",
                Target = "swapi.co",
                Data = "https://swapi.co/api/planets/1/",
                Timestamp = start,
                Duration = DateTime.UtcNow - start,
                Success = true
            };
            dependency.Context.User.Id = name;
            this.telemetryClient.TrackDependency(dependency);

            return Task.FromResult<IActionResult>(new OkResult());
        }
    }
}
```

在此示例中，自定义指标数据在发送到 customMetrics 表之前由主机聚合。 若要了解详细信息，请参阅 Application Insights 中的 [GetMetric](../azure-monitor/app/api-custom-events-metrics.md#getmetric) 文档。 

在本地运行时，必须使用 Application Insights 键将 `APPINSIGHTS_INSTRUMENTATIONKEY` 设置添加到 [local.settings.json](functions-develop-local.md#local-settings-file) 文件中。


# <a name="v1x"></a>[v1.x](#tab/v1)

```cs
using System;
using System.Net;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.Azure.WebJobs;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;
using System.Linq;

namespace functionapp0915
{
    public static class HttpTrigger2
    {
        private static string key = TelemetryConfiguration.Active.InstrumentationKey = 
            System.Environment.GetEnvironmentVariable(
                "APPINSIGHTS_INSTRUMENTATIONKEY", EnvironmentVariableTarget.Process);

        private static TelemetryClient telemetryClient = 
            new TelemetryClient() { InstrumentationKey = key };

        [FunctionName("HttpTrigger2")]
        public static async Task<HttpResponseMessage> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
            HttpRequestMessage req, ExecutionContext context, ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            DateTime start = DateTime.UtcNow;

            // Parse query parameter
            string name = req.GetQueryNameValuePairs()
                .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
                .Value;

            // Get request body
            dynamic data = await req.Content.ReadAsAsync<object>();

            // Set name to query string or body data
            name = name ?? data?.name;
         
            // Track an Event
            var evt = new EventTelemetry("Function called");
            UpdateTelemetryContext(evt.Context, context, name);
            telemetryClient.TrackEvent(evt);
            
            // Track a Metric
            var metric = new MetricTelemetry("Test Metric", DateTime.Now.Millisecond);
            UpdateTelemetryContext(metric.Context, context, name);
            telemetryClient.TrackMetric(metric);
            
            // Track a Dependency
            var dependency = new DependencyTelemetry
            {
                Name = "GET api/planets/1/",
                Target = "swapi.co",
                Data = "https://swapi.co/api/planets/1/",
                Timestamp = start,
                Duration = DateTime.UtcNow - start,
                Success = true
            };
            UpdateTelemetryContext(dependency.Context, context, name);
            telemetryClient.TrackDependency(dependency);
        }
        
        // Correlate all telemetry with the current Function invocation
        private static void UpdateTelemetryContext(TelemetryContext context, ExecutionContext functionContext, string userName)
        {
            context.Operation.Id = functionContext.InvocationId.ToString();
            context.Operation.ParentId = functionContext.InvocationId.ToString();
            context.Operation.Name = functionContext.FunctionName;
            context.User.Id = userName;
        }
    }    
}
```
---

请勿调用 `TrackRequest` 或 `StartOperation<RequestTelemetry>`，因为将显示函数调用的重复请求。  Functions 运行时自动跟踪请求。

不要设置 `telemetryClient.Context.Operation.Id`。 当多个函数同时运行时，这种全局设置会导致不正确的关联。 请改为创建新的遥测实例（`DependencyTelemetry`、`EventTelemetry`）并修改其 `Context` 属性。 然后将遥测实例传入到 `TelemetryClient` 的相应 `Track` 方法（`TrackDependency()`、`TrackEvent()`、`TrackMetric()`）。 此方法可确保遥测具有当前函数调用的正确关联详细信息。


## <a name="environment-variables"></a>环境变量

若要获取环境变量或应用设置值，请使用 `System.Environment.GetEnvironmentVariable`，如以下代码示例所示：

```csharp
public static class EnvironmentVariablesExample
{
    [FunctionName("GetEnvironmentVariables")]
    public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
    {
        log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
        log.LogInformation(GetEnvironmentVariable("AzureWebJobsStorage"));
        log.LogInformation(GetEnvironmentVariable("WEBSITE_SITE_NAME"));
    }

    private static string GetEnvironmentVariable(string name)
    {
        return name + ": " +
            System.Environment.GetEnvironmentVariable(name, EnvironmentVariableTarget.Process);
    }
}
```

在本地开发和在 Azure 中运行时，都可以从环境变量读取应用设置。 在本地开发时，应用设置来自 *local.settings.json* 文件中的 `Values` 集合。 在这两个环境（本地和 Azure）中，`GetEnvironmentVariable("<app setting name>")` 都会检索命名应用设置的值。 例如，在本地运行时，如果 *local.settings.json* 文件包含 `{ "Values": { "WEBSITE_SITE_NAME": "My Site Name" } }`，则会返回“My Site Name”。

[System.Configuration.ConfigurationManager.AppSettings](/dotnet/api/system.configuration.configurationmanager.appsettings) 属性是用于获取应用设置值的替代 API，但我们建议你使用 `GetEnvironmentVariable`，如下所示。

## <a name="binding-at-runtime"></a>在运行时绑定

在 C# 和其他 .NET 语言中，可以使用 [命令性](https://en.wikipedia.org/wiki/Imperative_programming)绑定模式，而不是在属性中使用“声明性”[*绑定*](https://en.wikipedia.org/wiki/Declarative_programming) 。 当绑定参数需要在运行时（而非在设计时）计算时，命令性绑定很有用。 通过此模式，可以在函数代码中动态绑定到受支持的输入和输出绑定。

如下所示定义命令性绑定：

- **不要** 在函数签名中包括用于所需的命令性绑定的属性。
- 传递输入参数 [`Binder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) 或 [`IBinder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs)。
- 使用下面的 C# 模式执行数据绑定。

  ```cs
  using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
  {
      ...
  }
  ```

  `BindingTypeAttribute` 是定义了绑定的 .NET 属性，`T` 是该绑定类型所支持的输入或输出类型。 `T` 不能是 `out` 参数类型（例如 `out JObject`）。 例如，移动应用表输出绑定支持[六种输出类型](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22)，但对于命令性绑定，仅可使用 [ICollector\<T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) 或 [IAsyncCollector\<T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs)。

### <a name="single-attribute-example"></a>单属性示例

下面的示例代码使用在运行时定义的 blob 路径创建[存储 blob 输出绑定](functions-bindings-storage-blob-output.md)，然后将字符串写入此 blob。

```cs
public static class IBinderExample
{
    [FunctionName("CreateBlobUsingBinder")]
    public static void Run(
        [QueueTrigger("myqueue-items-source-4")] string myQueueItem,
        IBinder binder,
        ILogger log)
    {
        log.LogInformation($"CreateBlobUsingBinder function processed: {myQueueItem}");
        using (var writer = binder.Bind<TextWriter>(new BlobAttribute(
                    $"samples-output/{myQueueItem}", FileAccess.Write)))
        {
            writer.Write("Hello World!");
        };
    }
}
```

[BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs) 定义[存储 blob](functions-bindings-storage-blob.md) 输入或输出绑定，[TextWriter](/dotnet/api/system.io.textwriter) 是支持的输出绑定类型。

### <a name="multiple-attributes-example"></a>多属性示例

上一个示例获取函数应用的主存储帐户连接字符串（即 `AzureWebJobsStorage`）的应用设置。 通过添加 [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) 和将属性数组传入 `BindAsync<T>()`，可指定要用于存储帐户的自定义应用设置。 使用一个 `Binder` 参数而非 `IBinder`。  例如：

```cs
public static class IBinderExampleMultipleAttributes
{
    [FunctionName("CreateBlobInDifferentStorageAccount")]
    public async static Task RunAsync(
            [QueueTrigger("myqueue-items-source-binder2")] string myQueueItem,
            Binder binder,
            ILogger log)
    {
        log.LogInformation($"CreateBlobInDifferentStorageAccount function processed: {myQueueItem}");
        var attributes = new Attribute[]
        {
        new BlobAttribute($"samples-output/{myQueueItem}", FileAccess.Write),
        new StorageAccountAttribute("MyStorageAccount")
        };
        using (var writer = await binder.BindAsync<TextWriter>(attributes))
        {
            await writer.WriteAsync("Hello World!!");
        }
    }
}
```

## <a name="triggers-and-bindings"></a>触发器和绑定 

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [详细了解触发器和绑定](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [详细了解有关 Azure Functions 的最佳做法](functions-best-practices.md)
