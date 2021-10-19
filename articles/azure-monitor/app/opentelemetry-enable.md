---
title: 为 .NET、Node.js 和 Python 应用程序启用 Azure Monitor OpenTelemetry
description: 提供在应用程序中使用 OpenTelemetry 启用 Azure Monitor 的操作指南
ms.topic: conceptual
ms.date: 10/11/2021
author: mattmccleary
ms.author: mmcc
ms.openlocfilehash: eb20d283e092be8b5932d1427f7e7064afa9c654
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2021
ms.locfileid: "129859570"
---
# <a name="enable-azure-monitor-opentelemetry-exporter-for-net-nodejs-and-python-applications-preview"></a>为 .NET、Node.js 和 Python 应用程序启用 Azure Monitor OpenTelemetry 导出器（预览版）

本文介绍如何启用和配置基于 OpenTelemetry 的 Azure Monitor（预览版）产品/服务。 阅读完本文中的说明后，你将能够将 OpenTelemetry 跟踪发送到 Azure Monitor Application Insights。

> [!IMPORTANT]
> 目前可提供适用于 .NET、Node.js 和 Python 应用程序的预览版 Azure Monitor OpenTelemetry 导出器。
> 有关 beta 版本、预览版或尚未正式发布的版本的 Azure 功能所适用的法律条款，请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="limitations-of-preview-release"></a>预览版本的限制

### <a name="net"></a>[.NET](#tab/net)

请仔细考虑此预览版是否适合你使用。 该版本仅启用分布式跟踪，且不包含以下各项：
 - 指标 API（自定义指标、[预先聚合的指标](pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics)）
 - [实时指标](live-stream.md)
 - 日志记录 API（控制台日志、日志记录库等）
 - 自动捕获未经处理的异常
 - [探查器](profiler-overview.md)
 - [快照调试程序](snapshot-debugger.md)
 - 脱机磁盘存储
 - [Azure AD 身份验证](azure-ad-authentication.md)
 - [采样](sampling.md)
 - 在 Azure 环境中自动填充云角色名称和云角色实例
 - 使用 [Application Insights JavaScript SDK](javascript.md) 时，自动填充用户 ID 和经过身份验证的用户 ID
 - 自动填充用户 IP（以确定位置属性）
 - 能够重写[操作名称](correlation.md#data-model-for-telemetry-correlation)
 - 能够手动设置用户 ID 或经过身份验证的用户 ID
 - 将操作名称传播到依赖项遥测
 - 通过 Azure Functions 辅助角色传播分布式跟踪上下文（检测库）

若需使用完整功能，用户应该使用现有的 Application Insights [ASP.NET](asp-net.md) 或 [ASP.NET Core](asp-net-core.md) SDK，直到基于 OpenTelemetry 的产品/服务发展到成熟阶段。

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

请仔细考虑此预览版是否适合你使用。 该版本仅启用分布式跟踪，且不包含以下各项：
 - 指标 API（自定义指标、[预先聚合的指标](pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics)）
 - [实时指标](live-stream.md)
 - 日志记录 API（控制台日志、日志记录库等）
 - 自动捕获未经处理的异常
 - [Azure AD 身份验证](azure-ad-authentication.md)
 - [采样](sampling.md)
 - 在 Azure 环境中自动填充云角色名称和云角色实例
 - 使用 [Application Insights JavaScript SDK](javascript.md) 时，自动填充用户 ID 和经过身份验证的用户 ID
 - 自动填充用户 IP（以确定位置属性）
 - 能够重写[操作名称](correlation.md#data-model-for-telemetry-correlation)
 - 能够手动设置用户 ID 或经过身份验证的用户 ID
 - 将操作名称传播到依赖项遥测

若需使用完整功能，用户应该使用现有的 [Application Insights Node.js SDK](nodejs.md)，直到基于 OpenTelemetry 的产品/服务发展到成熟阶段。

> [!WARNING] 
> 目前，此导出器仅适用于 Node.js 环境。 使用 [Application Insights JavaScript SDK](javascript.md) 处理 Web/浏览器方案。

### <a name="python"></a>[Python](#tab/python)

请仔细考虑此预览版是否适合你使用。 该版本仅启用分布式跟踪，且不包含以下各项：
 - 指标 API（自定义指标、[预先聚合的指标](pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics)）
 - [实时指标](live-stream.md)
 - 日志记录 API（控制台日志、日志记录库等）
 - 自动捕获未经处理的异常
 - 脱机磁盘存储
 - [Azure AD 身份验证](azure-ad-authentication.md)
 - [采样](sampling.md)
 - 在 Azure 环境中自动填充云角色名称和云角色实例
 - 使用 [Application Insights JavaScript SDK](javascript.md) 时，自动填充用户 ID 和经过身份验证的用户 ID
 - 自动填充用户 IP（以确定位置属性）
 - 能够重写[操作名称](correlation.md#data-model-for-telemetry-correlation)
 - 能够手动设置用户 ID 或经过身份验证的用户 ID
 - 将操作名称传播到依赖项遥测
 - 通过 Azure Functions 辅助角色传播分布式跟踪上下文（检测库）

若需使用完整功能，用户应该使用现有的 [Application Insights Python-OpenCensus SDK](opencensus-python.md)，直到基于 OpenTelemetry 的产品/服务发展到成熟阶段。

---

## <a name="get-started"></a>入门

遵循本部分提供的操作步骤，即可使用 OpenTelemetry 检测应用程序。

### <a name="prerequisites"></a>先决条件

- Azure 订阅 - [免费创建 Azure 订阅](https://azure.microsoft.com/free/)
- Application Insights 资源 - [创建 Application Insights 资源](create-workspace-resource.md#create-workspace-based-resource)

### <a name="net"></a>[.NET](#tab/net)

- 应用程序使用官方支持的 [.NET Core](https://dotnet.microsoft.com/download/dotnet) 或 [.NET Framework](https://dotnet.microsoft.com/download/dotnet-framework) >= `.NET Framework 4.6.1` 版本。


### <a name="nodejs"></a>[Node.js](#tab/nodejs)

- 应用程序使用 Node.js 运行时的官方[支持版本](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/monitor/monitor-opentelemetry-exporter#currently-supported-environments)。
  - [OpenTelemetry 支持的运行时](https://github.com/open-telemetry/opentelemetry-js#supported-runtimes)
  - [Azure Monitor OpenTelemetry 导出器支持的运行时](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/monitor/monitor-opentelemetry-exporter#currently-supported-environments)

### <a name="python"></a>[Python](#tab/python)

- 使用 3.6 版及更高版本的 Python 应用程序


---

### <a name="install-the-client-libraries"></a>安装客户端库

#### <a name="net"></a>[.NET](#tab/net)

安装最新的 [Azure.Monitor.OpenTelemetry.Exporter](https://www.nuget.org/packages/Azure.Monitor.OpenTelemetry.Exporter) NuGet 包：

```dotnetcli
dotnet add package --prerelease Azure.Monitor.OpenTelemetry.Exporter 
```

如果收到“没有可用的 Azure.Monitor.OpenTelemetry.Exporter 安装包版本”等错误，很可能是由于未设置 NuGet 包的来源。 可以尝试使用 `-s` 选项指定来源：

```dotnetcli
# Install the latest package with NuGet package source specified
dotnet add package --prerelease Azure.Monitor.OpenTelemetry.Exporter -s https://api.nuget.org/v3/index.json
```

#### <a name="nodejs"></a>[Node.js](#tab/nodejs)

要安装这些包：

- [@opentelemetry/sdk-trace-base](https://www.npmjs.com/package/@opentelemetry/sdk-trace-base)
- [@opentelemetry/sdk-trace-node](https://www.npmjs.com/package/@opentelemetry/sdk-trace-node)
- [@azure/monitor-opentelemetry-exporter](https://www.npmjs.com/package/@azure/monitor-opentelemetry-exporter)

```sh
npm install @opentelemetry/sdk-trace-base
npm install @opentelemetry/sdk-trace-node
npm install @azure/monitor-opentelemetry-exporter
```

以下包还可用于本文稍后介绍的一些特定方案。

- [@opentelemetry/api](https://www.npmjs.com/package/@opentelemetry/api)
- [@opentelemetry/resources](https://www.npmjs.com/package/@opentelemetry/resources)
- [@opentelemetry/semantic-conventions](https://www.npmjs.com/package/@opentelemetry/semantic-conventions)
- [@opentelemetry/instrumentation-http](https://www.npmjs.com/package/@opentelemetry/instrumentation-http)

```sh
npm install @opentelemetry/api
npm install @opentelemetry/resources
npm install @opentelemetry/semantic-conventions
npm install @opentelemetry/instrumentation-http
```

#### <a name="python"></a>[Python](#tab/python)

安装最新的 [azure-monitor-opentelemetry-exporter](https://pypi.org/project/azure-monitor-opentelemetry-exporter/) Pypi 包。

```sh
pip install azure-monitor-opentelemetry-exporter 
```

---

### <a name="enable-azure-monitor-application-insights"></a>启用 Azure Monitor Application Insights

#### <a name="add-opentelemetry-instrumentation-code"></a>添加 OpenTelemetry 检测代码

##### <a name="net"></a>[.NET](#tab/net)

> [!NOTE]
> 以下指南显示如何为 C# Azure Monitor 控制台应用程序启用 Azure Monitor Application Insights。
> 
> 有关其他应用程序类型的指南，请查看 OpenTelemetry GitHub 自述文件：
> - [ASP.NET](https://github.com/open-telemetry/opentelemetry-dotnet/blob/main/src/OpenTelemetry.Instrumentation.AspNet/README.md)
> - [ASP.NET Core](https://github.com/open-telemetry/opentelemetry-dotnet/blob/main/src/OpenTelemetry.Instrumentation.AspNetCore/README.md)
> - [HttpClient 和 HttpWebRequest](https://github.com/open-telemetry/opentelemetry-dotnet/blob/main/src/OpenTelemetry.Instrumentation.Http/README.md)
> 
> 用于将数据发送到 Application Insights 的 `AddAzureMonitorTraceExporter` 扩展方法适用于所有列出的应用程序类型。
> 
> 有关其他资源，请参阅 [GitHub 上的 OpenTelemetry 示例](https://github.com/open-telemetry/opentelemetry-dotnet/tree/main/examples)。 

```csharp
using System.Diagnostics;
using Azure.Monitor.OpenTelemetry.Exporter;
using OpenTelemetry;
using OpenTelemetry.Trace;

public class Program
{
    private static readonly ActivitySource MyActivitySource = new ActivitySource(
        "OTel.AzureMonitor.Demo");

    public static void Main()
    {
        using var tracerProvider = Sdk.CreateTracerProviderBuilder()
            .AddSource("OTel.AzureMonitor.Demo")
            .AddAzureMonitorTraceExporter(o =>
            {
                o.ConnectionString = "<Your Connection String>";
            })
            .Build();

        using (var activity = MyActivitySource.StartActivity("TestActivity"))
        {
            activity?.SetTag("CustomTag1", "Value1");
            activity?.SetTag("CustomTag2", "Value2");
        }

        System.Console.WriteLine("Press Enter key to exit.");
        System.Console.ReadLine();
    }
}
```

> [!NOTE]
> 命名空间 `System.Diagnostics` 中的 `Activity` 和 `ActivitySource` 类别分别代表 `Span` 和 `Tracer` 的 OpenTelemetry 概念。 直接使用构造函数而不是 `TracerProvider` 创建 `ActivitySource`（每个 [`ActivitySource`](https://github.com/open-telemetry/opentelemetry-dotnet/tree/main/docs/trace/customizing-the-sdk#activity-source) 都必须使用 `AddSource()` 显式连接至 `TracerProvider`）。 这是因为 OpenTelemetry 跟踪 API 的某些部分会直接合并到 .NET 运行时中。 [了解详细信息](https://github.com/open-telemetry/opentelemetry-dotnet/blob/main/src/OpenTelemetry.Api/README.md#introduction-to-opentelemetry-net-tracing-api)。

##### <a name="nodejs"></a>[Node.js](#tab/nodejs)

以下代码演示如何在简单的 Node.js 应用程序中启用 OpenTelemetry。

```typescript
const { AzureMonitorTraceExporter } = require("@azure/monitor-opentelemetry-exporter");
const { BatchSpanProcessor, Span } = require("@opentelemetry/sdk-trace-base");
const { NodeTracerProvider } = require("@opentelemetry/sdk-trace-node");

const provider = new NodeTracerProvider();
provider.register();

// Create an exporter instance
const exporter = new AzureMonitorTraceExporter({
  instrumentationKey: "<Your Connection String>"
});

// Add the exporter to the provider
provider.addSpanProcessor(
  new BatchSpanProcessor(exporter, {
    bufferTimeout: 15000,
    bufferSize: 1000
  })
);
// Create a span. A span must be closed.
const parentSpan = tracer.startSpan("main");
for (let i = 0; i < 10; i += 1) {
   doWork(parentSpan);
}
// Be sure to end the span.
parentSpan.end();

function doWork(parent: Span) {
  // Start another span. In this example, the main method already started a
  // span, so that'll be the parent span, and this will be a child span.
  const ctx = opentelemetry.trace.setSpan(opentelemetry.context.active(), parent);
  const span = tracer.startSpan("doWork", undefined, ctx);
  // simulate some random work.
  for (let i = 0; i <= Math.floor(Math.random() * 40000000); i += 1) {
    // empty
  }
  // Set attributes to the span.
  span.setAttribute("key", "value");
  // Annotate our span to capture metadata about our operation
  span.addEvent("invoking doWork");
  span.end();
}
```

##### <a name="python"></a>[Python](#tab/python)

以下代码演示如何在简单的 Python 应用程序中启用 OpenTelemetry。

```python
import os
from opentelemetry import trace
from opentelemetry.sdk.trace import TracerProvider
from opentelemetry.sdk.trace.export import BatchSpanProcessor

from azure.monitor.opentelemetry.exporter import AzureMonitorTraceExporter

exporter = AzureMonitorTraceExporter.from_connection_string(
    "<Your Connection String>"
)

trace.set_tracer_provider(TracerProvider())
tracer = trace.get_tracer(__name__)
span_processor = BatchSpanProcessor(exporter)
trace.get_tracer_provider().add_span_processor(span_processor)

with tracer.start_as_current_span("hello"):
    print("Hello, World!")

```

---

#### <a name="set-application-insights-connection-string"></a>设置 Application Insights 连接字符串

将上述代码中的占位符 `<Your Connection String>` 替换为 Application Insights 中的连接字符串。

:::image type="content" source="media/opentelemetry/connection-string.png" alt-text="屏幕截图：Application Insights 连接字符串。":::

#### <a name="confirm-data-is-flowing"></a>确认有数据流

运行应用程序，然后打开 Azure 门户中的“Application Insights 资源”选项卡。 数据可能在数分钟后才会显示在门户中。

> [!NOTE]
> 如果无法运行应用程序或未如预期获取数据，请转到[故障排除](#troubleshooting)。

:::image type="content" source="media/opentelemetry/server-requests.png" alt-text="屏幕截图：“Application Insights 概述”选项卡，其中突出显示服务器请求和服务器响应时间。":::

> [!IMPORTANT]
> 如果有两个或多个服务向同一 Application Insights 资源发出遥测数据，则需要[设置云角色名称](#set-cloud-role-name-and-cloud-role-instance)以在应用程序映射中正确表示这些服务。

## <a name="set-cloud-role-name-and-cloud-role-instance"></a>设置云角色名称和云角色实例

可以通过[资源](https://github.com/open-telemetry/opentelemetry-specification/blob/main/specification/resource/sdk.md#resource-sdk)属性设置[云角色名称](app-map.md#understanding-cloud-role-name-within-the-context-of-the-application-map)和云角色实例。 这会将云角色名称和云角色实例从默认值更新为适用于团队的内容。 它将在应用程序映射的节点下作为名称显示。 云角色名称使用 `service.namespace` 和 `service.name` 属性，但如果未设置 `service.namespace`，则它将回滚到 `service.name`。 云角色实例使用 `service.instance.id` 属性值。

### <a name="net"></a>[.NET](#tab/net)

```csharp
// Setting Role name and Role instance
var resourceAttributes = new Dictionary<string, object> {
    { "service.name", "my-service" },
    { "service.namespace", "my-namespace" },
    { "service.instance.id", "my-instance" }};
var resourceBuilder = ResourceBuilder.CreateDefault().AddAttributes(resourceAttributes);
// Done setting Role name and Role instance

// Set ResourceBuilder on the provider
using var tracerProvider = Sdk.CreateTracerProviderBuilder()
        .SetResourceBuilder(resourceBuilder)
        .AddSource("OTel.AzureMonitor.Demo")
        .AddAzureMonitorTraceExporter(o =>
        {
            o.ConnectionString = "<Your Connection String>";
        })
        .Build();
```


### <a name="nodejs"></a>[Node.js](#tab/nodejs)

```typescript
...
import { NodeTracerProvider, NodeTracerConfig } from "@opentelemetry/sdk-trace-node";
import { Resource } from "@opentelemetry/resources";
import { SemanticResourceAttributes } from "@opentelemetry/semantic-conventions";

// ----------------------------------------
// Setting Role name and role instance
// ----------------------------------------
const config: NodeTracerConfig = {
        resource: new Resource({
            [SemanticResourceAttributes.SERVICE_NAME]: "my-helloworld-service",
            [SemanticResourceAttributes.SERVICE_NAMESPACE]: "my-namespace",
            [SemanticResourceAttributes.SERVICE_INSTANCE_ID]: "my-instance",
        }),
    };
// ----------------------------------------
// Done setting Role name and role instance
// ----------------------------------------
const provider = new NodeTracerProvider(config);
...
```

### <a name="python"></a>[Python](#tab/python)

```python
...
from opentelemetry.sdk.resources import SERVICE_NAME, SERVICE_NAMESPACE, SERVICE_INSTANCE_ID, Resource
trace.set_tracer_provider(
    TracerProvider(
        resource=Resource.create(
            {
                SERVICE_NAME: "my-helloworld-service",
# ----------------------------------------
# Setting Role name and role instance
# ----------------------------------------
                SERVICE_NAMESPACE: "my-namespace",
                SERVICE_INSTANCE_ID: "my-instance",
# ----------------------------------------------
# Done setting Role name and role instance
# ----------------------------------------------
            }
        )
    )
)
...
```

---

参考：[资源语义约定](https://github.com/open-telemetry/opentelemetry-specification/blob/main/specification/resource/semantic_conventions/README.md)

## <a name="sampling"></a>采样

虽然 OpenTelemetry 支持采样，但 Azure Monitor OpenTelemetry 导出器目前尚不支持采样。

> [!WARNING]
> 在 OpenTelemetry 中启用采样会导致标准指标和基于日志的指标出现明显不正确的问题，而这将对所有 Application Insights 体验造成负面影响。 此外，与现有 Application Insights SDK 一起启用采样将导致跟踪中断。

## <a name="instrumentation-libraries"></a>检测库
<!-- Microsoft has tested and validated that the following instrumentation libraries will work with the **Preview** Release. -->
以下库经过验证，可以与预览版一起使用：

> [!WARNING]
> 检测库基于实验性 OpenTelemetry 规范。 Microsoft 的预览版支持承诺是为了确保下面列出的库发出 Azure Monitor Application Insights 数据，但中断性变更或实验性映射可能会阻止某些数据元素。

### <a name="http"></a>HTTP

#### <a name="net"></a>[.NET](#tab/net)

- [ASP.NET](https://github.com/open-telemetry/opentelemetry-dotnet/blob/1.0.0-rc7/src/OpenTelemetry.Instrumentation.AspNet/README.md) 版本：[1.0.0-rc7](https://www.nuget.org/packages/OpenTelemetry.Instrumentation.AspNet/1.0.0-rc7)
- [ASP.NET Core](https://github.com/open-telemetry/opentelemetry-dotnet/blob/1.0.0-rc7/src/OpenTelemetry.Instrumentation.AspNetCore/README.md) 版本：[1.0.0-rc7](https://www.nuget.org/packages/OpenTelemetry.Instrumentation.AspNetCore/1.0.0-rc7)
- [HTTP 客户端](https://github.com/open-telemetry/opentelemetry-dotnet/blob/1.0.0-rc7/src/OpenTelemetry.Instrumentation.Http/README.md)版本：[1.0.0-rc7](https://www.nuget.org/packages/OpenTelemetry.Instrumentation.Http/1.0.0-rc7)

#### <a name="nodejs"></a>[Node.js](#tab/nodejs)

- [http/https](https://github.com/open-telemetry/opentelemetry-js/tree/main/experimental/packages/opentelemetry-instrumentation-http/README.md) 版本[：0.26.0](https://www.npmjs.com/package/@opentelemetry/instrumentation-http/v/0.26.0)


#### <a name="python"></a>[Python](#tab/python)

- [Django](https://github.com/open-telemetry/opentelemetry-python-contrib/tree/main/instrumentation/opentelemetry-instrumentation-django/README.md) 版本：[0.24b0](https://pypi.org/project/opentelemetry-instrumentation-django/0.24b0/)
- [Flask](https://github.com/open-telemetry/opentelemetry-python-contrib/tree/main/instrumentation/opentelemetry-instrumentation-flask/README.md) 版本：[0.24b0](https://pypi.org/project/opentelemetry-instrumentation-flask/0.24b0/)
- [Requests](https://github.com/open-telemetry/opentelemetry-python-contrib/tree/main/instrumentation/opentelemetry-instrumentation-requests/README.md) 版本：[0.24b0](https://pypi.org/project/opentelemetry-instrumentation-requests/0.24b0/)

---

### <a name="database"></a>数据库

#### <a name="net"></a>[.NET](#tab/net)

- [SQL client](https://github.com/open-telemetry/opentelemetry-dotnet/blob/1.0.0-rc7/src/OpenTelemetry.Instrumentation.SqlClient/README.md) 版本：[1.0.0-rc7](https://www.nuget.org/packages/OpenTelemetry.Instrumentation.SqlClient/1.0.0-rc7)

#### <a name="nodejs"></a>[Node.js](#tab/nodejs)

- [mysql](https://github.com/open-telemetry/opentelemetry-js-contrib/tree/main/plugins/node/opentelemetry-instrumentation-mysql) 版本：[0.25.0](https://www.npmjs.com/package/@opentelemetry/instrumentation-mysql/v/0.25.0)

#### <a name="python"></a>[Python](#tab/python)

- [Psycopg2](https://github.com/open-telemetry/opentelemetry-python-contrib/tree/main/instrumentation/opentelemetry-instrumentation-psycopg2) 版本：[0.24b0](https://pypi.org/project/opentelemetry-instrumentation-psycopg2/0.24b0/)

---

> [!NOTE]
> 预览版产品/服务仅包含处理 HTTP 和数据库请求的检测。 有关详细信息，请参阅 [OpenTelemetry 语义约定](https://github.com/open-telemetry/opentelemetry-specification/tree/main/specification/trace/semantic_conventions)。

## <a name="modify-telemetry"></a>修改遥测

### <a name="add-span-attributes"></a>添加范围属性

可以使用以下两种方法之一添加范围属性。
1. 使用[检测库](#instrumentation-libraries)提供的选项。
2. 添加自定义范围处理器。

这些属性可能包括向遥测添加自定义业务维度。 还可以使用属性来设置 Application Insights 架构中的可选字段，如用户 ID 或客户端 IP。

> [!TIP]
> 使用“检测库提供的选项”（若可用）具有可使用整个上下文的优势，这意味着用户可以选择添加或筛选其他属性。 例如，HttpClient 检测库的扩充选项包括为用户提供 httpRequestMessage 本身的访问权限，以便从中选择任何内容，并存储为属性。

#### <a name="add-custom-property"></a>添加自定义属性

添加到活动/范围的任何[属性](#add-span-attributes)都将导出为自定义属性。 它们将在 Application Insights 的请求和/或依赖项表中填充 customDimensions 字段。

##### <a name="net"></a>[.NET](#tab/net)

1. 许多检测库都提供扩充选项。 若需相关指导，请参阅各个检测库的自述文件。
    - [ASP.NET](https://github.com/open-telemetry/opentelemetry-dotnet/blob/1.0.0-rc7/src/OpenTelemetry.Instrumentation.AspNet/README.md#enrich)
    - [ASP.NET Core](https://github.com/open-telemetry/opentelemetry-dotnet/blob/1.0.0-rc7/src/OpenTelemetry.Instrumentation.AspNetCore/README.md#enrich)
    - [HttpClient 和 HttpWebRequest](https://github.com/open-telemetry/opentelemetry-dotnet/blob/1.0.0-rc7/src/OpenTelemetry.Instrumentation.Http/README.md#enrich)

2. 使用自定义处理器：

> [!TIP]
> 在使用 Azure Monitor 导出器之前，添加如下所示的处理器。

```csharp
using var tracerProvider = Sdk.CreateTracerProviderBuilder()
        .AddSource("OTel.AzureMonitor.Demo")
        .AddProcessor(new ActivityEnrichingProcessor())
        .AddAzureMonitorTraceExporter(o =>
        {
                o.ConnectionString = "<Your Connection String>"
        })
        .Build();
```

使用以下代码将 `ActivityEnrichingProcessor.cs` 添加至你的项目。

```csharp
using System.Diagnostics;
using OpenTelemetry;
using OpenTelemetry.Trace;

public class ActivityEnrichingProcessor : BaseProcessor<Activity>
{
    public override void OnEnd(Activity activity)
    {
        // The updated activity will be available to all processors which are called after this processor.
        activity.DisplayName = "Updated-" + activity.DisplayName;
        activity.SetTag("CustomDimension1", "Value1");
        activity.SetTag("CustomDimension2", "Value2");
    }
}
```


##### <a name="nodejs"></a>[Node.js](#tab/nodejs)

使用自定义处理器：

> [!TIP]
> 在使用 Azure Monitor 导出器之前，添加如下所示的处理器。

```typescript
import { AzureMonitorTraceExporter } from "@azure/monitor-opentelemetry-exporter";
import { NodeTracerProvider } from "@opentelemetry/sdk-trace-node";
import { ReadableSpan, SimpleSpanProcessor, Span, SpanProcessor } from "@opentelemetry/sdk-trace-base";

class SpanEnrichingProcessor implements SpanProcessor{
    forceFlush(): Promise<void>{
        return Promise.resolve();
    }
    shutdown(): Promise<void>{
        return Promise.resolve();
    }
    onStart(_span: Span): void{}
    onEnd(span: ReadableSpan){
        span.attributes["CustomDimension1"] = "value1";
        span.attributes["CustomDimension2"] = "value2";
    }
}

const provider = new NodeTracerProvider();
const azureExporter = new AzureMonitorTraceExporter();
provider.addSpanProcessor(new SpanEnrichingProcessor());
provider.addSpanProcessor(new SimpleSpanProcessor(azureExporter));

```

##### <a name="python"></a>[Python](#tab/python)

使用自定义处理器：

> [!TIP]
> 在使用 Azure Monitor 导出器之前，添加如下所示的处理器。

```python
...
from opentelemetry.sdk.trace import TracerProvider
from opentelemetry.sdk.trace.export import BatchSpanProcessor

trace.set_tracer_provider(TracerProvider())
span_processor = BatchSpanProcessor(exporter)
span_enrich_processor = SpanEnrichingProcessor()
trace.get_tracer_provider().add_span_processor(span_enrich_processor)
trace.get_tracer_provider().add_span_processor(span_processor)
...
```

使用以下代码将 `SpanEnrichingProcessor.py` 添加至你的项目。

```python
from opentelemetry.sdk.trace import SpanProcessor

class SpanEnrichingProcessor(SpanProcessor):

    def on_end(self, span):
        span._name = "Updated-" + span.name
        span._attributes["CustomDimension1"] = "Value1"
        span._attributes["CustomDimension2"] = "Value2"

```
---

#### <a name="set-user-ip"></a>设置用户 IP

可以通过设置活动/范围的 `http.client_ip` 属性来填充请求的 /client_IP 字段。 Application Insights 使用 IP 地址生成用户位置属性，然后[默认放弃它](ip-collection.md#default-behavior)。

##### <a name="net"></a>[.NET](#tab/net)

使用添加[自定义属性示例](#add-custom-property)，但更改 `ActivityEnrichingProcessor.cs` 中的以下代码行：

```C#
activity.SetTag("http.client_ip", "<IP Address>");
```

##### <a name="nodejs"></a>[Node.js](#tab/nodejs)

使用添加[自定义属性示例](#add-custom-property)，但更改以下代码行：

```typescript
...
import { SemanticAttributes } from "@opentelemetry/semantic-conventions";

class SpanEnrichingProcessor implements SpanProcessor{
    ...

    onEnd(span: ReadableSpan){
        span.attributes[SemanticAttributes.HTTP_CLIENT_IP] = "<IP Address>";
    }
}
```

##### <a name="python"></a>[Python](#tab/python)

使用添加[自定义属性示例](#add-custom-property)，但更改 `SpanEnrichingProcessor.py` 中的以下代码行：

```python
span._attributes["http.client_ip"] = "<IP Address>"
```

---
<!--

#### Set user ID or authenticated user ID

You can populate the _user_Id_ or _user_Authenticatedid_ field for requests by setting `xyz` or `xyz` attribute on activity/span. User ID is an anonymous user identifier and Authenticated User ID is a known user identifier.

> [!IMPORTANT]
> Consult applicable privacy laws before setting Authenticated User ID.

##### [.NET](#tab/net)

Use the add [custom property example](#add-custom-property), except change out the following lines of code:

```C#
Placeholder
```

##### [Node.js](#tab/nodejs)

Use the add [custom property example](#add-custom-property), except change out the following lines of code:

```typescript
...
import { SemanticAttributes } from "@opentelemetry/semantic-conventions";

class SpanEnrichingProcessor implements SpanProcessor{
    ...

    onEnd(span: ReadableSpan){
        span.attributes[SemanticAttributes.ENDUSER_ID] = "<User ID>";
    }
}
```

##### [Python](#tab/python)

Use the add [custom property example](#add-custom-property), except change out the following lines of code:

```python
span._attributes["enduser.id"] = "<User ID>"
```

---
-->

### <a name="filter-telemetry"></a>筛选遥测数据

在退出应用程序之前，可以使用以下方法筛选出遥测数据。

#### <a name="net"></a>[.NET](#tab/net)

1. 许多检测库都提供筛选选项。 若需相关指导，请参阅各个检测库的自述文件。
    - [ASP.NET](https://github.com/open-telemetry/opentelemetry-dotnet/blob/1.0.0-rc7/src/OpenTelemetry.Instrumentation.AspNet/README.md#filter)
    - [ASP.NET Core](https://github.com/open-telemetry/opentelemetry-dotnet/blob/1.0.0-rc7/src/OpenTelemetry.Instrumentation.AspNetCore/README.md#filter)
    - [HttpClient 和 HttpWebRequest](https://github.com/open-telemetry/opentelemetry-dotnet/blob/1.0.0-rc7/src/OpenTelemetry.Instrumentation.Http/README.md#filter)

2. 使用自定义处理器：
    
    ```csharp
    using var tracerProvider = Sdk.CreateTracerProviderBuilder()
            .AddSource("OTel.AzureMonitor.Demo")
            .AddProcessor(new ActivityFilteringProcessor())
            .AddAzureMonitorTraceExporter(o =>
            {
                    o.ConnectionString = "<Your Connection String>"
            })
            .Build();
    ```
    
    使用以下代码将 `ActivityFilteringProcessor.cs` 添加至你的项目。
    
    ```csharp
    using System.Diagnostics;
    using OpenTelemetry;
    using OpenTelemetry.Trace;
    
    public class ActivityFilteringProcessor : BaseProcessor<Activity>
    {
        public override void OnStart(Activity activity)
        {
            // prevents all exporters from exporting internal activities
            if (activity.Kind == ActivityKind.Internal)
            {
                activity.IsAllDataRequested = false;
            }
        }
    }
    ```


3. 如果未使用 `AddSource("ActivitySourceName")` 显式添加特定源，则不导出使用该源创建的任何活动。
    
    <!---
    ### Get Trace ID or Span ID
    You may use X or Y to get trace ID and/or span ID. Adding trace ID and/or span ID to existing logging telemetry enables better correlation when debugging and diagnosing issues.
    
    > [!NOTE]
    > If you are manually creating spans for log-based metrics and alerting, you will need to update them to use the metrics API (after it is released) to ensure accuracy.
    
    ```C#
    Placeholder
    ```
    
    For more information, see [GitHub Repo](link).
    --->

#### <a name="nodejs"></a>[Node.js](#tab/nodejs)

1. 排除多个 http 检测库提供的 url 选项。

    下面的示例演示如何排除使用 [http/https 检测库](https://github.com/open-telemetry/opentelemetry-js/tree/main/experimental/packages/opentelemetry-instrumentation-http)跟踪的某个 url。
    
    ```typescript
    ...
    import { HttpInstrumentation, HttpInstrumentationConfig } from "@opentelemetry/instrumentation-http";
    
    ...
    const httpInstrumentationConfig: HttpInstrumentationConfig = {
        ignoreIncomingPaths: [new RegExp(/dc.services.visualstudio.com/i)]
    };
    const httpInstrumentation = new HttpInstrumentation(httpInstrumentationConfig);
    provider.register();
    registerInstrumentations({
        instrumentations: [
            httpInstrumentation,
        ]
    });
    
    ```

2. 使用自定义处理器。 你可以使用自定义范围处理器来排除导出某些范围。 若要将范围标记为不导出，请将其 `TraceFlag` 设置为 `DEFAULT`。
使用添加[自定义属性示例](#add-custom-property)，但更改以下代码行：

    ```typescript
    ...
    import { SpanKind, TraceFlags } from "@opentelemetry/api";
    
    class SpanEnrichingProcessor implements SpanProcessor{
        ...
    
        onEnd(span: ReadableSpan) {
            if(span.kind == SpanKind.INTERNAL){
                span.spanContext().traceFlags = TraceFlags.NONE;
            }
        }
    }
    ```

#### <a name="python"></a>[Python](#tab/python)

1. 排除多个 http 检测库提供的 url 选项。

    下面的示例演示如何排除使用 [Flask](https://github.com/open-telemetry/opentelemetry-python-contrib/tree/main/instrumentation/opentelemetry-instrumentation-flask) 检测跟踪的某个 url。
    
    ```python
    ...
    import flask
    
    from opentelemetry.instrumentation.flask import FlaskInstrumentor
    
    # You may also populate OTEL_PYTHON_FLASK_EXCLUDED_URLS env variable
    # List will consist of comma delimited regexes representing which URLs to exclude
    excluded_urls = "client/.*/info,healthcheck"
    
    FlaskInstrumentor().instrument(excluded_urls=excluded_urls) # Do this before flask.Flask
    app = flask.Flask(__name__)
    ...
    ```

2. 使用自定义处理器。 你可以使用自定义范围处理器来排除导出某些范围。 若要将范围标记为不导出，请将其 `TraceFlag` 设置为 `DEFAULT`。
    
    ```python
    ...
    from opentelemetry.sdk.trace import TracerProvider
    from opentelemetry.sdk.trace.export import BatchSpanProcessor
    
    trace.set_tracer_provider(TracerProvider())
    span_processor = BatchSpanProcessor(exporter)
    span_filter_processor = SpanFilteringProcessor()
    trace.get_tracer_provider().add_span_processor(span_filter_processor)
    trace.get_tracer_provider().add_span_processor(span_processor)
    ...
    ```
    
    使用以下代码将 `SpanFilteringProcessor.py` 添加至你的项目。
    
    ```python
    from opentelemetry.trace import SpanContext, SpanKind, TraceFlags
    from opentelemetry.sdk.trace import SpanProcessor
    
    class SpanFilteringProcessor(SpanProcessor):
    
        # prevents exporting spans from internal activities
        def on_start(self, span):
            if span._kind is SpanKind.INTERNAL:
                span._context = SpanContext(
                    span.context.trace_id,
                    span.context.span_id,
                    span.context.is_remote,
                    TraceFlags.DEFAULT,
                    span.context.trace_state,
                )
    
    ```
    
    <!-- For more information, see [GitHub Repo](link). -->
    <!---
    ### Get Trace ID or Span ID
    You may use X or Y to get trace ID and/or span ID. Adding trace ID and/or span ID to existing logging telemetry enables better correlation when debugging and diagnosing issues.
    
    > [!NOTE]
    > If you are manually creating spans for log-based metrics and alerting, you will need to update them to use the metrics API (after it is released) to ensure accuracy.
    
    ```python
    Placeholder
    ```
    
    For more information, see [GitHub Repo](link).
    --->

---

## <a name="enable-otlp-exporter"></a>启用 OTLP 导出器

你可能想要启用 OpenTelemetry 协议 (OTLP) 导出器和 Azure Monitor 导出器，以便将遥测发送到两个位置。

> [!NOTE]
> 为方便起见，只显示了 OTLP 导出器。 我们并未正式支持使用 OTLP 导出器或是其下游的任何组件或第三方体验。 我们建议你使用 [OpenTelemetry-Collector](https://github.com/open-telemetry/opentelemetry-collector) 打开问题，以处理 Azure Support 边界外的 OpenTelemetry 问题。

#### <a name="net"></a>[.NET](#tab/net)

1. 在你的项目中安装 [OpenTelemetry.Exporter.OpenTelemetryProtocol](https://www.nuget.org/packages/OpenTelemetry.Exporter.OpenTelemetryProtocol/) 包以及 [Azure.Monitor.OpenTelemetry.Exporter](https://www.nuget.org/packages/Azure.Monitor.OpenTelemetry.Exporter)。

2. 添加以下代码片段。 此示例假设你已经安装一个 OpenTelemetry 收集器，并且其中正在运行一个 OTLP 接收器。 有关详细信息，请参阅[此处](https://github.com/open-telemetry/opentelemetry-dotnet/blob/main/examples/Console/TestOtlpExporter.cs)示例。

```csharp
// sends data to Application Insights as well as OTLP
using var tracerProvider = Sdk.CreateTracerProviderBuilder()
        .AddSource("OTel.AzureMonitor.Demo")
        .AddAzureMonitorTraceExporter(o =>
        {
            o.ConnectionString = "<Your Connection String>"
        })
        .AddOtlpExporter()
        .Build();
```


#### <a name="nodejs"></a>[Node.js](#tab/nodejs)

1. 在你的项目中安装 [OpenTelemetry Collector Exporter](https://www.npmjs.com/package/@opentelemetry/exporter-otlp-http) 包以及 [Azure Monitor OpenTelemetry 导出器](https://www.npmjs.com/package/@azure/monitor-opentelemetry-exporter)。


```sh
    npm install @opentelemetry/exporter-otlp-http
    npm install @azure/monitor-opentelemetry-exporter
```

2. 添加以下代码片段。 此示例假设你已经安装一个 OpenTelemetry 收集器，并且其中正在运行一个 OTLP 接收器。 有关详细信息，请参阅[此处](https://github.com/open-telemetry/opentelemetry-js/tree/main/examples/otlp-exporter-node)示例。

```typescript
const { BasicTracerProvider, SimpleSpanProcessor } = require('@opentelemetry/sdk-trace-base');
const { OTLPTraceExporter } = require('@opentelemetry/exporter-otlp-http');

const provider = new BasicTracerProvider();
const azureMonitorExporter = new AzureMonitorTraceExporter({
  instrumentationKey: os.environ["APPLICATIONINSIGHTS_CONNECTION_STRING"]
});
const otlpExporter = new OTLPTraceExporter();
provider.addSpanProcessor(new SimpleSpanProcessor(azureMonitorExporter));
provider.addSpanProcessor(new SimpleSpanProcessor(otlpExporter));
provider.register();
```

#### <a name="python"></a>[Python](#tab/python)

1. 安装 [azure-monitor-opentelemetry-exporter](https://pypi.org/project/azure-monitor-opentelemetry-exporter/) 和 [opentelemetry-exporter-otlp](https://pypi.org/project/opentelemetry-exporter-otlp/) 包。

2. 添加以下代码片段。 此示例假设你已经安装一个 OpenTelemetry 收集器，并且其中正在运行一个 OTLP 接收器。 有关详细信息，请参阅此[自述文件](https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/monitor/azure-monitor-opentelemetry-exporter/samples/traces#collector)。

```python
from opentelemetry import trace 

from opentelemetry.exporter.otlp.proto.grpc.trace_exporter import OTLPSpanExporter
from opentelemetry.sdk.trace import TracerProvider
from opentelemetry.sdk.trace.export import BatchSpanProcessor

from azure.monitor.opentelemetry.exporter import AzureMonitorTraceExporter

trace.set_tracer_provider(TracerProvider())
tracer = trace.get_tracer(__name__) 

exporter = AzureMonitorTraceExporter.from_connection_string(
    "<Your Connection String>"
)
otlp_exporter = OTLPSpanExporter(endpoint="http://localhost:4317")
span_processor = BatchSpanProcessor(otlp_exporter) 
trace.get_tracer_provider().add_span_processor(span_processor)

with tracer.start_as_current_span("test"):
    print("Hello world!")
```

---

## <a name="troubleshooting"></a>疑难解答

### <a name="enable-diagnostic-logging"></a>启用诊断记录

#### <a name="net"></a>[.NET](#tab/net)

Azure Monitor 导出器使用 EventSource 执行自己的内部日志记录。 通过选择加入名为“OpenTelemetry-AzureMonitor-Exporter”的来源，任何 EventListener 都可以使用导出器日志。 有关详细的故障排除步骤，请参阅 [OpenTelemetry 故障排除](https://github.com/open-telemetry/opentelemetry-dotnet/tree/main/src/OpenTelemetry#troubleshooting)。

#### <a name="nodejs"></a>[Node.js](#tab/nodejs)

Azure Monitor 导出器使用 OpenTelemetry API 记录器记录内部日志；你可以使用以下代码启用此功能。 

```typescript
const { diag, DiagConsoleLogger, DiagLogLevel } = require("@opentelemetry/api");
const { NodeTracerProvider } = require("@opentelemetry/sdk-trace-node");

const provider = new NodeTracerProvider();
diag.setLogger(new DiagConsoleLogger(), DiagLogLevel.ALL);
provider.register();
```

#### <a name="python"></a>[Python](#tab/python)

Azure Monitor 导出器使用 Python 标准日志记录[库](https://docs.python.org/3/library/logging.html)进行自己的内部日志记录。 对于不规则活动，OpenTelemetry API 和 Azure Monitor 导出器日志通常记录在“WARNING”或“ERROR”严重性级别中，常规/成功活动则记录在“INFO”级别中。 默认 Python 日志记录库的默认严重性级别为 WARNING，因此必须更改严重性级别才能查看此级别下的日志。 下面的示例演示如何将所有严重性级别的日志输出到控制台和文件。

```python
...
import logging

logging.basicConfig(format="%(asctime)s:%(levelname)s:%(message)s", level=logging.DEBUG)

logger = logging.getLogger(__name__)
file = logging.FileHandler("example.log")
stream = logging.StreamHandler()
logger.addHandler(file)
logger.addHandler(stream)
...

```

---

### <a name="known-issues"></a>已知问题

Azure Monitor OpenTelemetry 导出器的已知问题包括： 

- 依赖关系遥测缺少操作名称，这会对失败和性能选项卡体验产生负面影响。
- 请求和依赖项遥测缺少设备型号，这会对设备群体分析造成不利影响。
- 数据库服务器名称不在依赖项名称中，这会导致系统错误聚合不同服务器上的同名表。

## <a name="support"></a>支持

- 查看本文中的故障排除步骤。
- 有关 Azure 支持问题，可打开 [Azure 支持票证](https://azure.microsoft.com/support/create-ticket/)。

### <a name="net"></a>[.NET](#tab/net)

有关 OpenTelemetry 问题，请直接与 [OpenTelemetry .NET 社区](https://github.com/open-telemetry/opentelemetry-dotnet)联系。

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

有关 OpenTelemetry 问题，请直接与 [OpenTelemetry JavaScript 社区](https://github.com/open-telemetry/opentelemetry-js)联系。

### <a name="python"></a>[Python](#tab/python)

有关 OpenTelemetry 问题，请直接与 [OpenTelemetry Python 社区](https://github.com/open-telemetry/opentelemetry-python)联系。

---

## <a name="opentelemetry-feedback"></a>OpenTelemetry 反馈

- 填写 OpenTelemetry 社区的[客户反馈调查](https://docs.google.com/forms/d/e/1FAIpQLScUt4reClurLi60xyHwGozgM9ZAz8pNAfBHhbTZ4gFWaaXIRQ/viewform)。
- 加入 [OpenTelemetry 早期采用者社区](https://aka.ms/AzMonOTel/)，告诉 Microsoft 有关你本人的信息。
- 在 [Microsoft 技术社区](https://techcommunity.microsoft.com/t5/azure-monitor/bd-p/AzureMonitor)与其他 Azure Monitor 用户联系。

## <a name="next-steps"></a>后续步骤

### <a name="net"></a>[.NET](#tab/net)

- 在 [Azure Monitor 导出器 GitHub 存储库](https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/monitor/Azure.Monitor.OpenTelemetry.Exporter)中查看源代码。
- 若要安装 NuGet 包、检查更新或查看发行说明，请访问 [Azure Monitor 导出器 NuGet 包](https://www.nuget.org/packages/Azure.Monitor.OpenTelemetry.Exporter/)页面。
- 通过 [Azure Monitor 的示例应用程序](https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/monitor/Azure.Monitor.OpenTelemetry.Exporter/tests/Azure.Monitor.OpenTelemetry.Exporter.Tracing.Customization)，进一步了解 Azure Monitor Application Insights 和 OpenTelemetry。
- 若要详细了解 OpenTelemetry 及其社区，请访问 [OpenTelemetry .NET GitHub 存储库](https://github.com/open-telemetry/opentelemetry-dotnet)。
- [启用 Web/浏览器用户监视](javascript.md)，以开启使用体验。


### <a name="nodejs"></a>[Node.js](#tab/nodejs)

- 在 [Azure Monitor 导出器 GitHub 存储库](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/monitor/monitor-opentelemetry-exporter)中查看源代码。
- 若要安装 NPM 包、检查更新或查看发行说明，请访问 [Azure Monitor 导出器 NPM 包](https://www.npmjs.com/package/@azure/monitor-opentelemetry-exporter)页面。
- 通过 [Azure Monitor 的示例应用程序](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/monitor/monitor-opentelemetry-exporter/samples)，进一步了解 Azure Monitor Application Insights 和 OpenTelemetry。
- 若要详细了解 OpenTelemetry 及其社区，请访问 [OpenTelemetry JavaScript GitHub 存储库](https://github.com/open-telemetry/opentelemetry-js)。
- [启用 Web/浏览器用户监视](javascript.md)，以开启使用体验。

### <a name="python"></a>[Python](#tab/python)

- 在 [Azure Monitor 导出器 GitHub 存储库](https://github.com/Azure/azure-sdk-for-python/blob/main/sdk/monitor/azure-monitor-opentelemetry-exporter/README.md)中查看源代码。
- 若要安装 PyPI 包、检查更新或查看发行说明，请访问 [Azure Monitor 导出器 PyPI 包](https://pypi.org/project/azure-monitor-opentelemetry-exporter/)页面。
-  通过 [Azure Monitor 的示例应用程序](https://github.com/Azure-Samples/azure-monitor-opentelemetry-python)，进一步了解 Azure Monitor Application Insights 和 OpenTelemetry。
- 若要详细了解 OpenTelemetry 及其社区，请访问 [OpenTelemetry Python GitHub 存储库](https://github.com/open-telemetry/opentelemetry-python)。
- [启用 Web/浏览器用户监视](javascript.md)，以开启使用体验。

---
