---
title: Azure Functions 中 .NET 5.0 的 .NET 隔离进程指南
description: 了解如何使用 .NET 隔离进程在 Azure 中的进程外 .NET 5.0 上运行 C# 函数。
ms.service: azure-functions
ms.topic: conceptual
ms.date: 03/01/2021
ms.custom: template-concept
ms.openlocfilehash: dacf3436ce98d839ad5b45361f1573c98c62d3e7
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102563636"
---
# <a name="guide-for-running-functions-on-net-50-in-azure"></a>有关在 Azure 中的 .NET 5.0 上运行函数的指南

本文介绍如何使用 C# 开发 .NET 隔离进程函数，这些函数在 Azure Functions 中的进程外运行。 通过进程外的运行，可将函数代码与 Azure Functions 运行时分离开来。 此外，还可以创建并运行面向当前 .NET 5.0 版本的函数。 

| 入门 | 概念| 示例 |
|--|--|--| 
| <ul><li>[使用 Visual Studio Code](dotnet-isolated-process-developer-howtos.md?pivots=development-environment-vscode)</li><li>[使用命令行工具](dotnet-isolated-process-developer-howtos.md?pivots=development-environment-cli)</li><li>[使用 Visual Studio](dotnet-isolated-process-developer-howtos.md?pivots=development-environment-vs)</li></ul> | <ul><li>[托管选项](functions-scale.md)</li><li>[监视](functions-monitoring.md)</li> | <ul><li>[参考示例](https://github.com/Azure/azure-functions-dotnet-worker/tree/main/samples)</li></ul> |

如果你不需要支持 .NET 5.0 或者在进程外运行函数，可以改而[开发 C# 类库函数](functions-dotnet-class-library.md)。

## <a name="why-net-isolated-process"></a>为何使用 .NET 隔离进程？

过去，Azure Functions 仅支持 .NET 函数的紧密集成模式，这种模式在主机所用的同一进程中[作为类库](functions-dotnet-class-library.md)运行。 此模式在主机进程与函数之间提供深度集成。 例如，.NET 类库函数可以共享绑定 API 和类型。 但是，这种集成还要求在主机进程与 .NET 函数之间实现更紧密的耦合。 例如，在进程内运行的 .NET 函数需要在 Functions 运行时所用的同一 .NET 版本上运行。 为了能够摆脱这些约束顺利运行，现在可以选择在隔离的进程中运行。 借助这种进程隔离，还可以开发使用当前 .NET 版本（例如 .NET 5.0）且不受 Functions 运行时原生支持的函数。

由于这些函数在独立的进程中运行，.NET 隔离的函数应用与 .NET 类库函数应用之间存在一些[特性和功能差异](#differences-with-net-class-library-functions)。

### <a name="benefits-of-running-out-of-process"></a>在进程外运行的优势

在进程外运行时，.NET 函数可利用以下优势： 

+ 减少冲突：由于函数在独立的进程中运行，因此应用中使用的程序集不会与主机进程所用的相同程序集的不同版本发生冲突。  
+ 全面控制进程：可以控制应用的启动，并可控制所用的配置和启动的中间件。
+ 依赖项注入：由于可以全面控制进程，因此可以使用当前的 .NET 依赖项注入行为，并将中间件整合到函数应用中。 

## <a name="supported-versions"></a>支持的版本

目前支持进程外运行的 .NET 版本只有 .NET 5.0。

## <a name="net-isolated-project"></a>.NET 隔离项目

简单而言，.NET 隔离函数项目是一个面向 .NET 5.0 的 .NET 控制台应用项目。 下面是任何 .NET 隔离项目中所需的基本文件：

+ [host.json](functions-host-json.md) 文件。
+ [local.settings.json](functions-run-local.md#local-settings-file) 文件。
+ C# 项目文件 (.csproj)，用于定义项目和依赖项。
+ Program.cs 文件，应用的入口点。

## <a name="package-references"></a>包引用

在进程外运行时，.NET 项目将使用一组唯一的包，这些包实现核心功能和绑定扩展。 

### <a name="core-packages"></a>核心包 

需要使用以下包在隔离的进程中运行 .NET 函数：

+ [Microsoft.Azure.Functions.Worker](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker/)
+ [Microsoft.Azure.Functions.Worker.Sdk](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker.Sdk/)

### <a name="extension-packages"></a>扩展包

由于在 .NET 隔离进程中运行的函数使用不同的绑定类型，因此它们需要一组唯一的绑定扩展包。 

可以在 [Microsoft.Azure.Functions.Worker.Extensions](https://www.nuget.org/packages?q=Microsoft.Azure.Functions.Worker.Extensions) 下找到这些扩展包。

## <a name="start-up-and-configuration"></a>启动和配置 

使用 .NET 隔离函数时，可以访问函数应用的启动代码（通常在 Program.cs 中）。 你需要负责创建并启动自己的主机实例。 因此，你还可以直接访问应用的配置管道。 在进程外运行时，注入依赖项和运行中间件的操作要容易得多。 

以下代码显示了 `HostBuilder` 管道的示例：

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_startup":::

`HostBuilder` 用于生成并返回已完全初始化的 `IHost` 实例，该实例以异步方式运行以启动函数应用。 

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_host_run":::

### <a name="configuration"></a>Configuration

能够访问主机生成器管道意味着，可以在初始化期间设置任何特定于应用的配置。 这些配置将应用到在独立进程中运行的函数应用。 若要更改 Functions 主机或触发器以及绑定配置，仍需使用 [host.json 文件](functions-host-json.md)。      

以下示例演示如何添加作为命令行参数读取的 `args` 配置： 
 
:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_configure_app" :::

`ConfigureAppConfiguration` 方法用于配置生成进程和应用程序的剩余部分。 此示例还使用 [IConfigurationBuilder](/dotnet/api/microsoft.extensions.configuration.iconfigurationbuilder?view=dotnet-plat-ext-5.0&preserve-view=true)，因此可以更轻松地添加多个配置项。 由于 `ConfigureAppConfiguration` 返回相同的 [`IConfiguration`](/dotnet/api/microsoft.extensions.configuration.iconfiguration?view=dotnet-plat-ext-5.0&preserve-view=true) 实例，因此也可以多次调用此方法来添加多个配置项。 可以从 [`HostBuilderContext.Configuration`](/dotnet/api/microsoft.extensions.hosting.hostbuildercontext.configuration?view=dotnet-plat-ext-5.0&preserve-view=true) 和 [`IHost.Services`](/dotnet/api/microsoft.extensions.hosting.ihost.services?view=dotnet-plat-ext-5.0&preserve-view=true) 访问完整的配置集。

若要详细了解配置，请参阅 [ASP.NET Core 中的配置](/aspnet/core/fundamentals/configuration/?view=aspnetcore-5.0&preserve-view=true)。 

### <a name="dependency-injection"></a>依赖关系注入

与 .NET 类库相比，依赖项注入已得到简化。 不必创建启动类来注册服务，而只需在主机生成器中调用 `ConfigureServices`，并使用 [`IServiceCollection`](/dotnet/api/microsoft.extensions.dependencyinjection.iservicecollection?view=dotnet-plat-ext-5.0&preserve-view=true) 中的扩展方法来注入特定的服务。 

以下示例注入单一实例服务依赖项：  
 
:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_dependency_injection" :::

有关详细信息，请参阅 [ASP.NET Core 中的依赖项注入](/aspnet/core/fundamentals/dependency-injection?view=aspnetcore-5.0&preserve-view=true)。

<!--### Middleware

.NET isolated also supports middleware registration, again by using a model similar to what exists in ASP.NET. This model gives you the ability to inject logic into the invocation pipeline, and before and after functions execute.

While the full middleware registration set of APIs is not yet exposed, we do support middleware registration and have added an example to the sample application under the Middleware folder.

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_middleware" :::-->

## <a name="execution-context"></a>执行上下文

.NET 隔离进程将 `FunctionContext` 对象传递给函数方法。 使用此对象可以通过调用 `GetLogger` 方法并提供 `categoryName` 字符串，来获取要写入到日志的 [`ILogger`](/dotnet/api/microsoft.extensions.logging.ilogger?view=dotnet-plat-ext-5.0&preserve-view=true) 实例。 有关详细信息，请参阅[日志记录](#logging)。 

## <a name="bindings"></a>绑定 

绑定是通过在方法、参数和返回类型中使用特性定义的。 函数方法是包含 `Function` 以及一个应用于输入参数的触发器特性的方法，如以下示例中所示：

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/SampleApp/Queue/QueueFunction.cs" id="docsnippet_queue_trigger" :::

触发器属性指定触发器类型并将输入数据绑定到一个方法参数。 以上示例函数将由一条队列消息触发，队列消息将传递给方法中的 `myQueueItem` 参数。

`Function` 属性将该方法标记为函数入口点。 该名称在项目中必须是唯一的，以字母开头，并且只包含字母、数字、`_` 和 `-`，长度不得超过 127 个字符。 项目模板通常创建一个名为 `Run` 的方法，但方法名称可以是任何有效的 C# 方法名称。

由于 .NET 隔离项目在独立的工作进程中运行，因此绑定无法利用丰富的绑定类，例如 `ICollector<T>`、`IAsyncCollector<T>` 和 `CloudBlockBlob`。 此外，不直接支持继承自底层服务 SDK 的类型，例如 [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient) 和 [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)。 绑定依赖于字符串、数组和可序列化类型，例如普通旧类对象 (POCO)。 

对于 HTTP 触发器，必须使用 `HttpRequestData` 和 `HttpResponseData` 来访问请求与响应数据。 这是因为，在进程外运行时，无法访问原始 HTTP 请求和响应对象。 

### <a name="input-bindings"></a>输入绑定

一个函数可以有零个或多个可向函数传递数据的输入绑定。 与触发器一样，输入绑定是通过向输入参数应用绑定特性来定义的。 执行函数时，运行时将尝试获取绑定中指定的数据。 请求的数据通常依赖于触发器使用绑定参数提供的信息。  

### <a name="output-bindings"></a>输出绑定

若要写入到输出绑定，必须将输出绑定特性应用到函数方法，该方法定义了如何写入到绑定的服务。 该方法返回的值将写入到输出绑定。 例如，以下示例使用输出绑定将一个字符串值写入到名为 `functiontesting2` 的消息队列：

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/SampleApp/Queue/QueueFunction.cs" id="docsnippet_queue_output_binding" :::

### <a name="multiple-output-bindings"></a>多个输出绑定

写入到输出绑定的数据始终是函数的返回值。 如果需要写入到多个输出绑定，必须创建自定义返回类型。 在此返回类型中，必须已将输出绑定特性应用到类的一个或多个属性。 以下示例写入到 HTTP 响应和队列输出绑定：

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Function1/Function1.cs" id="docsnippet_multiple_outputs":::

### <a name="http-trigger"></a>HTTP 触发器

HTTP 触发器将传入的 HTTP 请求消息转换为要传递给函数的 `HttpRequestData` 对象。 此对象提供请求中的数据，包括 `Headers`、`Cookies`、`Identities`、`URL` 和可选消息 `Body`。 此对象是 HTTP 请求对象的表示形式，而不是请求本身。 

同样，函数返回一个 `HttpReponseData` 对象，该对象提供用于创建 HTTP 响应的数据，包括消息 `StatusCode`、`Headers` 和可选消息 `Body`。  

以下代码是一个 HTTP 触发器 

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/SampleApp/Http/HttpFunction.cs" id="docsnippet_http_trigger" :::

## <a name="logging"></a>日志记录

在 .NET 隔离进程中，可以使用 [`ILogger`](/dotnet/api/microsoft.extensions.logging.ilogger?view=dotnet-plat-ext-5.0&preserve-view=true) 实例写入日志，该实例是从传递给函数的 `FunctionContext` 对象获取的。 调用 `GetLogger` 方法并传递一个字符串值，该值是在其中写入日志的类别的名称。 该类别通常是从中写入日志的特定函数的名称。 若要详细了解类别，请参阅[有关监视的文章](functions-monitoring.md#log-levels-and-categories)。 

以下示例演示如何获取 `ILogger` 并在函数中写入日志：

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/SampleApp/Http/HttpFunction.cs" id="docsnippet_logging" ::: 

使用 `ILogger` 的各种方法写入各种日志级别，例如 `LogWarning` 或 `LogError`。 若要详细了解日志级别，请参阅[有关监视的文章](functions-monitoring.md#log-levels-and-categories)。

使用[依赖项注入](#dependency-injection)时还会提供 [`ILogger`](/dotnet/api/microsoft.extensions.logging.ilogger?view=dotnet-plat-ext-5.0&preserve-view=true)。

## <a name="differences-with-net-class-library-functions"></a>与 .NET 类库函数之间的差异

本部分介绍 .NET 5.0 进程外运行与 .NET 类库函数进程内运行的当前功能差异和行为差异状态：

| 功能/行为 |  进程内 (.NET Core 3.1) | 进程外 (.NET 5.0) |
| ---- | ---- | ---- |
| .NET 版本 | LTS (.NET Core 3.1) | 当前版本 (.NET 5.0) |
| 核心包 | [Microsoft.NET.Sdk.Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) | [Microsoft.Azure.Functions.Worker](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker/)<br/>[Microsoft.Azure.Functions.Worker](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker.Sdk) | 
| 绑定扩展包 | [`Microsoft.Azure.WebJobs.Extensions.*`](https://www.nuget.org/packages?q=Microsoft.Azure.WebJobs.Extensions)  | 在 [`Microsoft.Azure.Functions.Worker.Extensions.*`](https://www.nuget.org/packages?q=Microsoft.Azure.Functions.Worker.Extensions) 下 | 
| 日志记录 | 传递给函数的 [`ILogger`](/dotnet/api/microsoft.extensions.logging.ilogger?view=dotnet-plat-ext-5.0&preserve-view=true) | 从 `FunctionContext` 获取的 [`ILogger`](/dotnet/api/microsoft.extensions.logging.ilogger?view=dotnet-plat-ext-5.0&preserve-view=true) |
| 取消令牌 | [支持](functions-dotnet-class-library.md#cancellation-tokens) | 不支持 |
| 输出绑定 | 输出参数 | 返回值 |
| 输出绑定类型 |  `IAsyncCollector`、[DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient?view=azure-dotnet&preserve-view=true)、[BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet&preserve-view=true) 和其他特定于客户端的类型 | 简单类型、JSON 可序列化类型和数组。 |
| 多个输出绑定 | 支持 | [支持](#multiple-output-bindings) |
| HTTP 触发器 | [`HttpRequest`](/dotnet/api/microsoft.aspnetcore.http.httprequest?view=aspnetcore-5.0&preserve-view=true)/[`ObjectResult`](/dotnet/api/microsoft.aspnetcore.mvc.objectresult?view=aspnetcore-5.0&preserve-view=true) | `HttpRequestData`/`HttpResponseData` |
| Durable Functions | [支持](durable/durable-functions-overview.md) | 不支持 | 
| 命令性绑定 | [支持](functions-dotnet-class-library.md#binding-at-runtime) | 不支持 |
| function.json 项目 | 已生成 | 不生成 |
| Configuration | [host.json](functions-host-json.md) | [host.json](functions-host-json.md) 和[自定义初始化](#configuration) |
| 依赖关系注入 | [支持](functions-dotnet-dependency-injection.md)  | [支持](#dependency-injection) |
| 中间件 | 不支持 | 支持 |
| 冷启动时间 | 典型 | 因为是即时启动，所以时间更长。 在 Linux 而不是 Windows 上运行，以减少潜在的延迟。 |
| ReadyToRun | [支持](functions-dotnet-class-library.md#readytorun) | _TBD_ |

## <a name="known-issues"></a>已知问题

有关运行 .NET 隔离进程函数时存在的已知问题的解决方法，请参阅[此已知问题页](https://aka.ms/AAbh18e)。 若要报告问题，请[在此 GitHub 存储库中创建问题](https://github.com/Azure/azure-functions-dotnet-worker/issues/new/choose)。  

## <a name="next-steps"></a>后续步骤

+ [详细了解触发器和绑定](functions-triggers-bindings.md)
+ [详细了解有关 Azure Functions 的最佳做法](functions-best-practices.md)
