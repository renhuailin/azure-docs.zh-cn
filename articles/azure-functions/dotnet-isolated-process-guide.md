---
title: 适用于 .NET 5.0 的 .NET 独立过程指南 Azure Functions
description: '了解如何在 Azure 中的进程外使用 .NET 独立进程在 .NET 5.0 上运行 c # 函数。'
ms.service: azure-functions
ms.topic: conceptual
ms.date: 03/01/2021
ms.custom: template-concept
ms.openlocfilehash: 13dd2f81aa7714c37e329d3a9e63a3cfcfd43cb0
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102054547"
---
# <a name="guide-for-running-functions-on-net-50-in-azure"></a>在 Azure 中的 .NET 5.0 上运行函数的指南

_.NET 5.0 支持目前以预览版提供。_

本文介绍如何使用 c # 来开发 .NET 隔离处理函数，这些函数在 Azure Functions 进程外运行。 在进程外运行使你可以将函数代码与 Azure Functions 运行时分离。 它还提供了一种方法，用于创建和运行面向当前 .NET 5.0 版本的函数。 

如果不需要支持 .NET 5.0 或在进程外运行函数，则可能要改为 [开发 c # 类库函数](functions-dotnet-class-library.md)。

## <a name="why-net-isolated-process"></a>为什么 .NET 独立进程？

之前 Azure Functions 仅支持 .NET 函数的紧密集成模式，这种模式在与主机相同的进程中 [作为类库](functions-dotnet-class-library.md) 运行。 此模式提供主机进程和函数之间的深度集成。 例如，.NET 类库函数可以共享绑定 Api 和类型。 但是，这种集成还要求主机进程与 .NET 函数之间的耦合更紧密。 例如，运行在进程内的 .NET 函数需要在与函数运行时相同的 .NET 版本上运行。 为了使您能够在这些约束之外运行，现在可以选择在隔离的进程中运行。 此进程隔离还允许您开发使用当前 .NET 版本 (如 .NET 5.0) ，而不是由函数运行时本机支持的函数。

因为这些函数在单独的进程中运行，所以 .NET 独立主机隔离函数应用和 .NET 类库函数应用之间存在一些 [功能差异](#differences-with-net-class-library-functions) 。

### <a name="benefits-of-running-out-of-process"></a>在进程外运行的好处

在进程外运行时，您的 .NET 函数可以利用以下优势： 

+ 冲突更少：由于函数在单独的进程中运行，因此应用中使用的程序集不会与宿主进程使用的同一程序集的不同版本发生冲突。  
+ 完全控制该过程：你可以控制应用程序的启动，并可以控制所使用的配置和中间件的开始时间。
+ 依赖关系注入：由于你可以完全控制该过程，因此可以使用当前的 .NET 行为进行依赖关系注入，并将中间件并入函数应用中。 

## <a name="supported-versions"></a>支持的版本

当前支持在进程外运行的 .NET 版本只有 .NET 5.0。

## <a name="net-isolated-project"></a>.NET 独立项目

.NET 隔离函数项目基本上是面向 .NET 5.0 的 .NET 控制台应用程序项目。 下面是任何 .NET 独立主机中所需的基本文件：

+ 文件[host.js](functions-host-json.md) 。
+ 文件[local.settings.js](functions-run-local.md#local-settings-file) 。
+ C # 项目文件 ( .csproj) ，用于定义项目和依赖项。
+ Program.cs 文件，它是应用程序的入口点。

## <a name="package-references"></a>包引用

在进程外运行时，.NET 项目使用一组唯一的包，这些包同时实现核心功能和绑定扩展。 

### <a name="core-packages"></a>核心包 

在隔离的进程中运行 .NET 函数需要以下包：

+ [Microsoft.Azure.Functions.Worker](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker/)
+ [Microsoft.Azure.Functions.Worker.Sdk](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker.Sdk/)

### <a name="extension-packages"></a>扩展包

由于在 .NET 隔离的进程中运行的函数使用不同的绑定类型，因此它们需要一组唯一的绑定扩展包。 

你将 [在 ""](https://www.nuget.org/packages?q=Microsoft.Azure.Functions.Worker.Extensions)下找到这些扩展包。
 
## <a name="start-up-and-configuration"></a>启动和配置 

使用 .NET 隔离函数时，可以访问函数应用的启动，此功能通常在 Program.cs 中。 你负责创建和启动自己的主机实例。 因此，你还可以直接访问应用的配置管道。 在进程外运行时，可以更轻松地注入依赖项并运行中间件。 

下面的代码显示了管道的一个示例 `HostBuilder` ：

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" range="20-33":::

`HostBuilder`用于生成并返回完全初始化的 `IHost` 实例，该实例以异步方式运行以启动函数应用。 

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" range="35":::

### <a name="configuration"></a>配置

具有主机生成器管道的访问权限意味着可以在初始化期间设置任何特定于应用的配置。 这些配置适用于在单独的进程中运行的 function app。 若要更改函数 host 或 trigger 和 binding 配置，仍需要使用 [ 文件的host.js](functions-host-json.md)。      

下面的示例演示如何添加 `args` 作为命令行参数读取的配置： 
 
:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" range="21-24" :::

`ConfigureAppConfiguration`方法用于配置生成过程和应用程序的其余部分。 此示例还使用 [iconfigurationbuilder.addazurekeyvault](/dotnet/api/microsoft.extensions.configuration.iconfigurationbuilder?view=dotnet-plat-ext-5.0&preserve-view=true)，这样可以更轻松地添加多个配置项目。 由于 `ConfigureAppConfiguration` 返回相同的实例 [`IConfiguration `](/dotnet/api/microsoft.extensions.configuration.iconfiguration?view=dotnet-plat-ext-5.0&preserve-view=true) ，因此你也可以多次调用它来添加多个配置项目。 可以从和访问完整的配置集 [`HostBuilderContext.Configuration`](/dotnet/api/microsoft.extensions.hosting.hostbuildercontext.configuration?view=dotnet-plat-ext-5.0&preserve-view=true) [`IHost.Services`](/dotnet/api/microsoft.extensions.hosting.ihost.services?view=dotnet-plat-ext-5.0&preserve-view=true) 。

若要了解有关配置的详细信息，请参阅 [ASP.NET Core 中的配置](/aspnet/core/fundamentals/configuration/?view=aspnetcore-5.0&preserve-view=true)。 

### <a name="dependency-injection"></a>依赖项注入

与 .NET 类库相比，依赖关系注入得以简化。 不必创建启动类来注册服务，只需 `ConfigureServices` 在主机生成器上调用，并使用上的扩展方法 [`IServiceCollection`](/dotnet/api/microsoft.extensions.dependencyinjection.iservicecollection?view=dotnet-plat-ext-5.0&preserve-view=true) 来注入特定服务。 

下面的示例注入单一实例服务依赖项：  
 
:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" range="29-32" :::

若要了解详细信息，请参阅 [ASP.NET Core 中的依赖关系注入](/aspnet/core/fundamentals/dependency-injection?view=aspnetcore-5.0&preserve-view=true)。

### <a name="middleware"></a>中间件

.NET 独立主机还支持中间件注册，方法是使用类似于 ASP.NET 中存在的模型的模型。 此模型使你能够将逻辑注入调用管道，以及在执行函数之前和之后。

尽管尚未公开完整的中间件注册 Api 集，但支持中间件注册，我们已将示例应用程序的示例添加到了中间件文件夹下。

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" range="25-28" :::

## <a name="execution-context"></a>执行上下文

.NET 独立主机将 `FunctionContext` 对象传递给函数方法。 使用此对象，可以 [`ILogger`](/dotnet/api/microsoft.extensions.logging.ilogger?view=dotnet-plat-ext-5.0&preserve-view=true) 通过调用 `GetLogger` 方法并提供字符串来获取要写入日志的实例 `categoryName` 。 若要了解详细信息，请参阅 [日志记录](#logging)。 

## <a name="bindings"></a>绑定 

绑定是通过对方法、参数和返回类型使用特性来定义的。 函数方法是具有 `Function` 和触发器特性的方法，它应用于输入参数，如以下示例中所示：

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/SampleApp/Queue/QueueFunction.cs" range="13-16" :::

触发器属性指定触发器类型并将输入数据绑定到一个方法参数。 上一个示例函数由队列消息触发，队列消息传递给参数中的方法 `myQueueItem` 。

`Function` 属性将该方法标记为函数入口点。 该名称在项目中必须是唯一的，以字母开头，并且只包含字母、数字、`_` 和 `-`，长度不得超过 127 个字符。 项目模板通常创建一个名为 `Run` 的方法，但方法名称可以是任何有效的 C# 方法名称。

由于 .NET 独立项目在单独的工作进程中运行，因此绑定无法利用丰富的绑定类，如 `ICollector<T>` 、 `IAsyncCollector<T>` 和 `CloudBlockBlob` 。 对于从基础服务 Sdk 继承的类型（如 [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient) 和 [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)），也不提供直接支持。 绑定依赖于字符串、数组和可序列化的类型，例如 (Poco) 的纯旧式类对象。 

对于 HTTP 触发器，必须使用 `HttpRequestData` 和 `HttpResponseData` 来访问请求和响应数据。 这是因为在进程外运行时，不能访问原始 HTTP 请求和响应对象。 

### <a name="input-bindings"></a>输入绑定

函数可以具有零个或多个可向函数传递数据的输入绑定。 与触发器一样，通过将绑定特性应用于输入参数来定义输入绑定。 当函数执行时，运行时将尝试获取绑定中指定的数据。 请求的数据通常依赖于触发器使用绑定参数提供的信息。  

### <a name="output-bindings"></a>输出绑定

若要写入到输出绑定，必须将输出绑定特性应用到函数方法，该方法定义了如何向绑定的服务写入。 方法返回的值将写入到输出绑定。 例如，下面的示例使用输出绑定将一个字符串值写入名为 `functiontesting2` 的消息队列：

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/SampleApp/Queue/QueueFunction.cs" range="13-23" :::

### <a name="multiple-output-bindings"></a>多个输出绑定

写入到输出绑定的数据始终是函数的返回值。 如果需要写入多个输出绑定，则必须创建自定义返回类型。 此返回类型必须将输出绑定特性应用于类的一个或多个属性。 下面的示例同时写入 HTTP 响应和队列输出绑定：

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Function1/Function1.cs" range="14-33":::

### <a name="http-trigger"></a>HTTP 触发器

HTTP 触发器将传入的 HTTP 请求消息转换为 `HttpRequestData` 传递到函数的对象。 此对象提供来自请求的数据，包括 `Headers` 、 `Cookies` 、 `Identities` 、 `URL` 和可选消息 `Body` 。 此对象是 HTTP 请求对象的表示形式，而不是请求本身。 

同样，该函数返回一个 `HttpReponseData` 对象，该对象提供用于创建 HTTP 响应的数据，其中包括消息 `StatusCode` 、 `Headers` 和（可选）消息 `Body` 。  

下面的代码是一个 HTTP 触发器 

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/SampleApp/Http/HttpFunction.cs" range="15-30" :::

## <a name="logging"></a>日志记录

在独立 .NET 中，可以使用 [`ILogger`](/dotnet/api/microsoft.extensions.logging.ilogger?view=dotnet-plat-ext-5.0&preserve-view=true) 从传递给函数的对象获取的实例来写入日志 `FunctionContext` 。 调用 `GetLogger` 方法，传递一个字符串值，该值是写入日志的类别的名称。 类别通常是从中写入日志的特定函数的名称。 若要了解有关类别的详细信息，请参阅 [监视一文](functions-monitoring.md#log-levels-and-categories)。 

下面的示例演示如何 `ILogger` 在函数中获取和写入日志：

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/SampleApp/Http/HttpFunction.cs" range="19-20" ::: 

使用的各种方法 `ILogger` 来编写各种日志级别，例如 `LogWarning` 或 `LogError` 。 若要了解有关日志级别的详细信息，请参阅 [监视一文](functions-monitoring.md#log-levels-and-categories)。

[`ILogger`](/dotnet/api/microsoft.extensions.logging.ilogger?view=dotnet-plat-ext-5.0&preserve-view=true)使用[依赖关系注入](#dependency-injection)时也提供。

## <a name="differences-with-net-class-library-functions"></a>与 .NET 类库函数之间的差异

本部分介绍 .NET 5.0 上运行的与在进程中运行的 .NET 类库函数相比，.NET 上运行的功能和行为差异的当前状态：

| 功能/行为 |  正在处理的 ( .NET Core 3.1)  | 不在进程外 ( .NET 5.0)  |
| ---- | ---- | ---- |
| .NET 版本 | LTS ( .NET Core 3.1)  | 当前 ( .NET 5.0)  |
| 核心包 | [Microsoft.NET.Sdk.Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) | [Microsoft.Azure.Functions.Worker](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker/)<br/>[Microsoft.Azure.Functions.Worker](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker.Sdk) | 
| 绑定扩展包 | [`Microsoft.Azure.WebJobs.Extensions.*`](https://www.nuget.org/packages?q=Microsoft.Azure.WebJobs.Extensions)  | 下 [`Microsoft.Azure.Functions.Worker.Extensions.*`](https://www.nuget.org/packages?q=Microsoft.Azure.Functions.Worker.Extensions) | 
| 日志记录 | [`ILogger`](/dotnet/api/microsoft.extensions.logging.ilogger?view=dotnet-plat-ext-5.0&preserve-view=true) 传递给函数 | [`ILogger`](/dotnet/api/microsoft.extensions.logging.ilogger?view=dotnet-plat-ext-5.0&preserve-view=true) 获取自 `FunctionContext` |
| 取消令牌 | [支持](functions-dotnet-class-library.md#cancellation-tokens) | 不支持 |
| 输出绑定 | Out 参数 | 返回值 |
| 输出绑定类型 |  `IAsyncCollector`、 [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient?view=azure-dotnet&preserve-view=true)、 [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet&preserve-view=true)和其他客户端特定类型 | 简单类型、JSON 可序列化类型和数组。 |
| 多个输出绑定 | 支持 | [支持](#multiple-output-bindings) |
| HTTP 触发器 | [`HttpRequest`](/dotnet/api/microsoft.aspnetcore.http.httprequest?view=aspnetcore-5.0&preserve-view=true)/[`ObjectResult`](/dotnet/api/microsoft.aspnetcore.mvc.objectresult?view=aspnetcore-5.0&preserve-view=true) | `HttpRequestData`/`HttpResponseData` |
| Durable Functions | [支持](durable/durable-functions-overview.md) | 不支持 | 
| 命令性绑定 | [支持](functions-dotnet-class-library.md#binding-at-runtime) | 不支持 |
| 项目 function.js | 已生成 | 未生成 |
| 配置 | [host.json](functions-host-json.md) | [host.js](functions-host-json.md) 和 [自定义初始化](#configuration) |
| 依赖项注入 | [支持](functions-dotnet-dependency-injection.md)  | [支持](#dependency-injection) |
| 中间件 | 不支持 | [支持](#middleware) |
| 冷启动时间 | 典型 | 更长时间，因为它是实时启动。 在 Linux 上运行，而不是在 Windows 上运行，以减少潜在的延迟。 |
| ReadyToRun | [支持](functions-dotnet-class-library.md#readytorun) | _TBD_ |


## <a name="next-steps"></a>后续步骤

+ [详细了解触发器和绑定](functions-triggers-bindings.md)
+ [详细了解有关 Azure Functions 的最佳做法](functions-best-practices.md)
