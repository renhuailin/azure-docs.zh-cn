---
title: Application Insights SDK 中的筛选和预处理 | Microsoft Docs
description: 为 SDK 编写遥测处理器和遥测初始化表达式，以在将遥测发送到 Application Insights 门户之前筛选属性或将其添加到数据。
ms.topic: conceptual
ms.date: 11/23/2016
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 5fe23d6cae1b363cdb0c70cba561953368412361
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128645125"
---
# <a name="filter-and-preprocess-telemetry-in-the-application-insights-sdk"></a>Application Insights SDK 中的筛选和预处理遥测

可以为 Application Insights SDK 编写和配置插件，在将遥测发送到 Application Insights 服务之前自定义遥测的扩充和处理方式。

* [采样](sampling.md)可在不影响统计信息的情况下减少遥测量。 它还保留相关数据点，以便你可以在诊断问题时导航这些点。 在门户中，总计相乘以补偿采样。
* 使用遥测处理器进行筛选，就可以先在 SDK 中筛选出遥测，然后再将其发送到服务器。 例如，可以通过排除机器人请求减少遥测量。 与采样相比，筛选是更基本的减少流量的方法。 它让你可以更好地控制传输的数据，但会影响统计信息。 例如，你可以筛选出所有成功的请求。
* 对于从应用发送的任何遥测（包括来自标准模块的遥测），[遥测初始化表达式可以添加或修改属性](#add-properties)。 例如，可以添加计算得出的值或在门户中筛选数据所依据的版本号。
* [SDK API](./api-custom-events-metrics.md) 用于发送自定义事件和指标。

开始之前：

* 为应用程序安装相应的 SDK：[ASP.NET](asp-net.md)、[ASP.NET Core](asp-net-core.md)、[用于 .NET/.NET Core 的非 HTTP/辅助角色](worker-service.md)或 [JavaScript](javascript.md)。

<a name="filtering"></a>

## <a name="filtering"></a>筛选

使用此技术可以直接控制要在遥测流中包含或排除的内容。 可以通过筛选删除要发送到 Application Insights 的遥测项。 你可以将筛选与采样结合使用，也可以单独使用。

若要筛选遥测，请编写遥测处理器并通过 `TelemetryConfiguration` 注册它。 所有遥测都通过处理器。 你可以选择从流中删除它或将它提供给链中的下一处理器。 包括来自标准模块的遥测（例如 HTTP 请求收集器和依赖项收集器）以及自行跟踪的遥测。 例如，你可以筛选出有关机器人请求或成功依赖项调用的遥测。

> [!WARNING]
> 使用处理器筛选从 SDK 发送的遥测会使门户中看到的统计信息出现偏差，并使它难以跟进相关项目。
>
> 此时，考虑使用[采样](./sampling.md)。
>
>

### <a name="create-a-telemetry-processor-c"></a>创建遥测处理器 (C#)

1. 若要创建筛选器，请实现 `ITelemetryProcessor`。

    遥测处理器会构建一个处理链。 当你实例化遥测处理器时，系统会为你提供链中下一个处理器的引用。 将遥测数据点传递到处理方法时，该方法会生效，然后调用（或不调用）链中的下一个遥测处理器。

    ```csharp
    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.Extensibility;

    public class SuccessfulDependencyFilter : ITelemetryProcessor
    {
        private ITelemetryProcessor Next { get; set; }

        // next will point to the next TelemetryProcessor in the chain.
        public SuccessfulDependencyFilter(ITelemetryProcessor next)
        {
            this.Next = next;
        }

        public void Process(ITelemetry item)
        {
            // To filter out an item, return without calling the next processor.
            if (!OKtoSend(item)) { return; }

            this.Next.Process(item);
        }

        // Example: replace with your own criteria.
        private bool OKtoSend (ITelemetry item)
        {
            var dependency = item as DependencyTelemetry;
            if (dependency == null) return true;

            return dependency.Success != true;
        }
    }
    ```

2. 添加处理器。

ASP.NET 应用

在 ApplicationInsights.config 中插入此代码片段：

```xml
<TelemetryProcessors>
  <Add Type="WebApplication9.SuccessfulDependencyFilter, WebApplication9">
     <!-- Set public property -->
     <MyParamFromConfigFile>2-beta</MyParamFromConfigFile>
  </Add>
</TelemetryProcessors>
```

可通过在类中提供公共命名属性，传递 .config 文件中的字符串值。

> [!WARNING]
> 注意将 .config 文件中的类型名称和任何属性名称匹配到代码中的类和属性名称。 如果 .config 文件引用不存在的类型或属性，该 SDK 在发送任何遥测时可能静默失败。
>

或者， 可以在代码中初始化筛选器。 在合适的初始化类（例如 `Global.asax.cs` 中的 AppStart）中，将处理器插入链：

```csharp
var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
builder.Use((next) => new SuccessfulDependencyFilter(next));

// If you have more processors:
builder.Use((next) => new AnotherProcessor(next));

builder.Build();
```

在此点后创建的遥测客户端将使用你的处理器。

ASP.NET Core/辅助角色服务应用

> [!NOTE]
> 使用 `ApplicationInsights.config` 或 `TelemetryConfiguration.Active` 添加处理器对于 ASP.NET Core 应用程序无效，或者如果你在使用 Microsoft.ApplicationInsights.WorkerService SDK，也将无效。

对于使用 [ASP.NET Core](asp-net-core.md#adding-telemetry-processors) 或 [WorkerService](worker-service.md#adding-telemetry-processors) 编写的应用，若要添加新的遥测处理器，请在 `IServiceCollection` 上使用 `AddApplicationInsightsTelemetryProcessor` 扩展方法，如下所示。 此方法在 `Startup.cs` 类的 `ConfigureServices` 方法中调用。

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        // ...
        services.AddApplicationInsightsTelemetry();
        services.AddApplicationInsightsTelemetryProcessor<SuccessfulDependencyFilter>();

        // If you have more processors:
        services.AddApplicationInsightsTelemetryProcessor<AnotherProcessor>();
    }
```

### <a name="example-filters"></a>示例筛选器

#### <a name="synthetic-requests"></a>综合请求

筛选出机器人和 Web 测试。 尽管指标资源管理器提供筛选出综合源的选项，但此选项可通过在 SDK 自身中筛选它们减少流量和引入大小。

```csharp
public void Process(ITelemetry item)
{
  if (!string.IsNullOrEmpty(item.Context.Operation.SyntheticSource)) {return;}

  // Send everything else:
  this.Next.Process(item);
}
```

#### <a name="failed-authentication"></a>身份验证失败

筛选出带有“401”响应的请求。

```csharp
public void Process(ITelemetry item)
{
    var request = item as RequestTelemetry;

    if (request != null &&
    request.ResponseCode.Equals("401", StringComparison.OrdinalIgnoreCase))
    {
        // To filter out an item, return without calling the next processor.
        return;
    }

    // Send everything else
    this.Next.Process(item);
}
```

#### <a name="filter-out-fast-remote-dependency-calls"></a>筛选出快速远程依赖项调用

如果只想诊断速度较慢的调用，可筛掉速度较快的调用。

> [!NOTE]
> 这项筛选操作会使门户上看到的统计信息出现偏差。
>
>

```csharp
public void Process(ITelemetry item)
{
    var request = item as DependencyTelemetry;

    if (request != null && request.Duration.TotalMilliseconds < 100)
    {
        return;
    }
    this.Next.Process(item);
}
```

#### <a name="diagnose-dependency-issues"></a>诊断依赖项问题

[本博客](https://azure.microsoft.com/blog/implement-an-application-insights-telemetry-processor/)介绍了通过自动将常规 Ping 发送到依赖项诊断依赖项问题的项目。

<a name="add-properties"></a>

### <a name="javascript-web-applications"></a>JavaScript Web 应用程序

**使用 ITelemetryInitializer 进行筛选**

1. 创建遥测初始化程序回调函数。 回调函数将 `ITelemetryItem` 作为参数，该项是正在处理的事件。 从此回调返回 `false` 将导致筛选掉遥测项。

   ```JS
   var filteringFunction = (envelope) => {
     if (envelope.data.someField === 'tobefilteredout') {
         return false;
     }
  
     return true;
   };
   ```

2. 添加遥测初始化程序回调：

   ```JS
   appInsights.addTelemetryInitializer(filteringFunction);
   ```

## <a name="addmodify-properties-itelemetryinitializer"></a>添加/修改属性：ITelemetryInitializer

通过遥测初始化表达式使用其他信息来扩充遥测，或者重写通过标准遥测模块设置的遥测属性。

例如，Web 程序包的 Application Insights 将收集有关 HTTP 请求的遥测。 默认情况下，它标记为所有请求失败，并且响应代码 >= 400。 但是，如果希望将 400 视为成功，可以提供一个设置成功属性的遥测初始化表达式。

如果提供了遥测初始化表达式，只要调用任何 Track*() 方法，就会调用它。 这包括由标准遥测模块调用的 `Track()` 方法。 按照约定，这些模块不会设置已由初始化表达式设置的任何属性。 在调用遥测处理器之前调用遥测初始值设定项。 因此，由初始值设定项完成的任何扩充对于处理器来说都是可见的。

**定义初始值设定项**

*C#*

```csharp
using System;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;

namespace MvcWebRole.Telemetry
{
  /*
   * Custom TelemetryInitializer that overrides the default SDK
   * behavior of treating response codes >= 400 as failed requests
   *
   */
  public class MyTelemetryInitializer : ITelemetryInitializer
  {
    public void Initialize(ITelemetry telemetry)
    {
        var requestTelemetry = telemetry as RequestTelemetry;
        // Is this a TrackRequest() ?
        if (requestTelemetry == null) return;
        int code;
        bool parsed = Int32.TryParse(requestTelemetry.ResponseCode, out code);
        if (!parsed) return;
        if (code >= 400 && code < 500)
        {
            // If we set the Success property, the SDK won't change it:
            requestTelemetry.Success = true;

            // Allow us to filter these requests in the portal:
            requestTelemetry.Properties["Overridden400s"] = "true";
        }
        // else leave the SDK to set the Success property
    }
  }
}
```

ASP.NET **应用：加载初始值设定项**

在 ApplicationInsights.config 中：

```xml
<ApplicationInsights>
  <TelemetryInitializers>
    <!-- Fully qualified type name, assembly name: -->
    <Add Type="MvcWebRole.Telemetry.MyTelemetryInitializer, MvcWebRole"/>
    ...
  </TelemetryInitializers>
</ApplicationInsights>
```

或者，你可以在代码中实例化初始化表达式，例如在 Global.aspx.cs 中：

```csharp
protected void Application_Start()
{
    // ...
    TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
}
```

查看[此示例](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole)的详细信息。

ASP.NET **Core/辅助角色服务应用：加载初始值设定项**

> [!NOTE]
> 使用 `ApplicationInsights.config` 或 `TelemetryConfiguration.Active` 添加初始化表达式对于 ASP.NET Core 应用程序无效，或者如果你在使用 Microsoft.ApplicationInsights.WorkerService SDK，也将无效。

对于使用 [ASP.NET Core](asp-net-core.md#adding-telemetryinitializers) 或 [WorkerService](worker-service.md#adding-telemetryinitializers) 编写的应用，若要添加新的遥测初始化表达式，请将其添加到依赖项注入容器中，如下所示。 这可以在 `Startup.ConfigureServices` 方法中完成。

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```
### <a name="javascript-telemetry-initializers"></a>JavaScript 遥测初始值设定项
*JavaScript*

使用 onInit 回调片段插入遥测初始化表达式：

```html
<script type="text/javascript">
!function(T,l,y){<!-- Removed the Snippet code for brevity -->}(window,document,{
src: "https://js.monitor.azure.com/scripts/b/ai.2.min.js",
crossOrigin: "anonymous",
onInit: function (sdk) {
  sdk.addTelemetryInitializer(function (envelope) {
    envelope.data.someField = 'This item passed through my telemetry initializer';
  });
}, // Once the application insights instance has loaded and initialized this method will be called
cfg: { // Application Insights Configuration
    instrumentationKey: "YOUR_INSTRUMENTATION_KEY"
}});
</script>
```

有关遥测项上可用的非自定义属性摘要，请参阅 [Application Insights 导出数据模型](./export-data-model.md)。

可添加任意数量的初始值设定项。 系统按它们的添加顺序对其进行调用。

### <a name="opencensus-python-telemetry-processors"></a>OpenCensus Python 遥测处理器

OpenCensus Python 遥测处理器是简单的回调函数，可在导出遥测之前调用这些函数来处理遥测。 回调函数必须接受[信封](https://github.com/census-instrumentation/opencensus-python/blob/master/contrib/opencensus-ext-azure/opencensus/ext/azure/common/protocol.py#L86)数据类型作为其参数。 若要筛掉不想导出的遥测，请确保回调函数返回 `False`。 可在 [GitHub](https://github.com/census-instrumentation/opencensus-python/blob/master/contrib/opencensus-ext-azure/opencensus/ext/azure/common/protocol.py) 上查看信封中 Azure Monitor 数据类型的架构。

> [!NOTE]
> 可以通过更改 `tags` 字段中的 `ai.cloud.role` 属性来修改 `cloud_RoleName`。

```python
def callback_function(envelope):
    envelope.tags['ai.cloud.role'] = 'new_role_name'
```

```python
# Example for log exporter
import logging

from opencensus.ext.azure.log_exporter import AzureLogHandler

logger = logging.getLogger(__name__)

# Callback function to append '_hello' to each log message telemetry
def callback_function(envelope):
    envelope.data.baseData.message += '_hello'
    return True

handler = AzureLogHandler(connection_string='InstrumentationKey=<your-instrumentation_key-here>')
handler.add_telemetry_processor(callback_function)
logger.addHandler(handler)
logger.warning('Hello, World!')
```
```python
# Example for trace exporter
import requests

from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace import config_integration
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['requests'])

# Callback function to add os_type: linux to span properties
def callback_function(envelope):
    envelope.data.baseData.properties['os_type'] = 'linux'
    return True

exporter = AzureExporter(
    connection_string='InstrumentationKey=<your-instrumentation-key-here>'
)
exporter.add_telemetry_processor(callback_function)
tracer = Tracer(exporter=exporter, sampler=ProbabilitySampler(1.0))
with tracer.span(name='parent'):
response = requests.get(url='https://www.wikipedia.org/wiki/Rabbit')
```

```python
# Example for metrics exporter
import time

from opencensus.ext.azure import metrics_exporter
from opencensus.stats import aggregation as aggregation_module
from opencensus.stats import measure as measure_module
from opencensus.stats import stats as stats_module
from opencensus.stats import view as view_module
from opencensus.tags import tag_map as tag_map_module

stats = stats_module.stats
view_manager = stats.view_manager
stats_recorder = stats.stats_recorder

CARROTS_MEASURE = measure_module.MeasureInt("carrots",
                                            "number of carrots",
                                            "carrots")
CARROTS_VIEW = view_module.View("carrots_view",
                                "number of carrots",
                                [],
                                CARROTS_MEASURE,
                                aggregation_module.CountAggregation())

# Callback function to only export the metric if value is greater than 0
def callback_function(envelope):
    return envelope.data.baseData.metrics[0].value > 0

def main():
    # Enable metrics
    # Set the interval in seconds in which you want to send metrics
    exporter = metrics_exporter.new_metrics_exporter(connection_string='InstrumentationKey=<your-instrumentation-key-here>')
    exporter.add_telemetry_processor(callback_function)
    view_manager.register_exporter(exporter)

    view_manager.register_view(CARROTS_VIEW)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    mmap.measure_int_put(CARROTS_MEASURE, 1000)
    mmap.record(tmap)
    # Default export interval is every 15.0s
    # Your application should run for at least this amount
    # of time so the exporter will meet this interval
    # Sleep can fulfill this
    time.sleep(60)

    print("Done recording metrics")

if __name__ == "__main__":
    main()
```
可添加任意数量的处理器。 系统按它们的添加顺序对其进行调用。 如果某个处理器引发异常，则它不会影响后面的处理器。

### <a name="example-telemetryinitializers"></a>示例 TelemetryInitializer

#### <a name="add-a-custom-property"></a>添加自定义属性

以下初始值设定项示例将自定义属性添加到每个被跟踪的遥测。

```csharp
public void Initialize(ITelemetry item)
{
  var itemProperties = item as ISupportProperties;
  if(itemProperties != null && !itemProperties.Properties.ContainsKey("customProp"))
    {
        itemProperties.Properties["customProp"] = "customValue";
    }
}
```

#### <a name="add-a-cloud-role-name"></a>添加云角色名称

以下初始化表达式示例将云角色名称设置为每个被跟踪的遥测。

```csharp
public void Initialize(ITelemetry telemetry)
{
    if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
    {
        telemetry.Context.Cloud.RoleName = "MyCloudRoleName";
    }
}
```

#### <a name="add-information-from-httpcontext"></a>从 HttpContext 添加信息

下面的初始化表达式示例从 [`HttpContext`](/aspnet/core/fundamentals/http-context) 读取数据并将其附加到 `RequestTelemetry` 实例。 `IHttpContextAccessor` 是通过构造函数依赖项注入自动提供的。

```csharp
public class HttpContextRequestTelemetryInitializer : ITelemetryInitializer
{
    private readonly IHttpContextAccessor httpContextAccessor;

    public HttpContextRequestTelemetryInitializer(IHttpContextAccessor httpContextAccessor)
    {
        this.httpContextAccessor =
            httpContextAccessor ??
            throw new ArgumentNullException(nameof(httpContextAccessor));
    }

    public void Initialize(ITelemetry telemetry)
    {
        var requestTelemetry = telemetry as RequestTelemetry;
        if (requestTelemetry == null) return;

        var claims = this.httpContextAccessor.HttpContext.User.Claims;
        Claim oidClaim = claims.FirstOrDefault(claim => claim.Type == "oid");
        requestTelemetry.Properties.Add("UserOid", oidClaim?.Value);
    }
}
```

## <a name="itelemetryprocessor-and-itelemetryinitializer"></a>ITelemetryProcessor 和 ITelemetryInitializer

遥测处理器和遥测初始值设定项之间的区别是什么？

* 它们的功能有部分重叠。 二者都可用于添加或修改遥测的属性，但我们建议你使用初始化表达式执行这项操作。
* 遥测初始化表达式始终在遥测处理器之前运行。
* 遥测初始化表达式可以多次调用。 按照约定，它们不会设置任何已设置的属性。
* 利用遥测处理器，你可以完全替换或丢弃遥测项。
* 可保证为所有遥测项调用所有注册的遥测初始化表达式。 对于遥测处理器，SDK 可保证调用第一个遥测处理器。 是否调用余下的处理器取决于前面的遥测处理器。
* 通过遥测初始化表达式使用其他属性来扩充遥测，或者重写现有的遥测。 使用遥测处理器筛掉遥测。

## <a name="troubleshoot-applicationinsightsconfig"></a>ApplicationInsights.config 故障排除

* 确认完全限定的类型名称和程序集名称是正确的。
* 确认 applicationinsights.config 文件在输出目录中并且包含所有最新更改。

## <a name="reference-docs"></a>参考文档

* [API 概述](./api-custom-events-metrics.md)
* [ASP.NET 参考](/previous-versions/azure/dn817570(v=azure.100))

## <a name="sdk-code"></a>SDK 代码

* [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-aspnetcore)
* [ASP.NET SDK](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS)

## <a name="next-steps"></a><a name="next"></a>后续步骤
* [搜索事件和日志](./diagnostic-search.md)
* [采样](./sampling.md)
* [故障排除](../faq.yml)

