---
title: 使用 Azure Application Insights 诊断故障和异常
description: 从 ASP.NET 应用中捕获异常以及请求遥测。
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 05/19/2021
ms.openlocfilehash: 5ad103890d481db18c3df9633bf3b58186136e93
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123431543"
---
# <a name="diagnose-exceptions-in-web-apps-with-application-insights"></a>使用 Application Insights 诊断 Web 应用中的异常

Web 应用程序中的异常可以使用 [Application Insights](./app-insights-overview.md) 进行报告。 可以将失败的请求与异常关联到客户端和服务器上的其他事件，从而快速诊断原因。 本文介绍如何设置异常报告、显式报告异常、诊断故障等。

## <a name="set-up-exception-reporting"></a>设置异常报告

可以设置 Application Insights 来报告服务器或客户端中出现的异常。 根据应用程序依赖的平台，你需要相应的扩展或 SDK。

### <a name="server-side"></a>服务器端

若要从服务器端应用程序报告异常，请考虑以下方案：

  * **Azure Web 应用**：添加 [Application Insights 扩展](./azure-web-apps.md)
  * **Azure VM 和 Azure 虚拟机规模集的托管 IIS 应用**：添加 [应用程序监视扩展](./azure-vm-vmss-apps.md)
  * 在应用代码中安装 [Application Insights SDK](./asp-net.md)，或者
  * **IIS Web 服务器**：运行 [Application Insights 代理](./status-monitor-v2-overview.md)；或者
  * **Java Web 应用**：启用 [Java 代理](./java-in-process-agent.md)

### <a name="client-side"></a>客户端

JavaScript SDK 使客户端能够报告 Web 浏览器中发生的异常。 若要在客户端上设置异常报告，请参阅[网页的 Application Insights](./javascript.md)。

### <a name="application-frameworks"></a>应用程序框架

对于某些应用程序框架，需要进行更多配置，请考虑以下技术：

  * [Web 窗体](#web-forms)
  * [MVC](#mvc)
  * [Web API 1*](#web-api-1x)
  * [Web API 2*](#web-api-2x)
  * [WCF](#wcf)

> [!IMPORTANT]
> 本文从代码示例的角度着重介绍 .NET Framework 应用。 用于 .NET Framework 的某些方法在 .NET Core SDK 中已过时。 有关详细信息，请在使用 .NET Core 生成应用时参阅 [.NET Core SDK 文档](./asp-net-core.md)。

## <a name="diagnose-exceptions-using-visual-studio"></a>使用 Visual Studio 诊断异常

在 Visual Studio 中打开应用程序解决方案。 使用 <kbd>F5</kbd> 在服务器或开发计算机上运行应用。 重新创建异常。

打开 Visual Studio 中的“Application Insights 搜索”遥测窗口。 调试时，选择“Application Insights”下拉列表。

![右键单击项目，并依次选择“Application Insights”、“打开”。](./media/asp-net-exceptions/34.png)

选择异常报告，显示器堆栈跟踪。 若要打开相关代码文件，请选择堆栈跟踪中的行引用。

如果已启用 CodeLens，你将看到有关异常的数据：

![有关异常的 CodeLens 通知。](./media/asp-net-exceptions/35.png)

## <a name="diagnose-failures-using-the-azure-portal"></a>使用 Azure 门户诊断失败

Application Insights 附带了精选的应用程序性能管理 (APM) 体验，帮助你诊断所监视应用程序中的失败。 若要开始，请选择位于“调查”部分中的 Application Insights 资源菜单中的“失败”选项 。
你将看到请求的失败率趋势，其中包括多少个请求将要失败以及多少个用户受到影响。 在“整体”视图中，你将看到一些最有用的特定于所选失败操作的分发，包括前三个响应代码、前三个异常类型以及前三种失败依赖项类型。

![失败会审视图（“操作”选项卡）](./media/asp-net-exceptions/failures0719.png)

若要查看其中每个操作子集的具有代表性的示例，请选择相应链接。 例如，若要诊断异常，可以选择要在“端到端事务详细信息”选项卡中显示的特定异常的计数：

![“端到端事务详细信息”选项卡](./media/asp-net-exceptions/end-to-end.png)

或者，可以切换到顶部的“异常”选项卡，从异常的“总体”视图开始，而不是查看特定失败操作的异常 。 这里可以看到为所监视的应用收集的所有异常。

## <a name="custom-tracing-and-log-data"></a>自定义跟踪和日志数据

要获取特定于应用的诊断数据，可在插入代码后发送自己的遥测数据。 自定义遥测或日志数据与请求、页面视图和其他自动收集的数据一起显示在诊断搜索中。

使用 <xref:Microsoft.VisualStudio.ApplicationInsights.TelemetryClient?displayProperty=fullName>，你可以使用多个 API：

* <xref:Microsoft.VisualStudio.ApplicationInsights.TelemetryClient.TrackEvent%2A?displayProperty=nameWithType> 通常用于监视使用模式，但它发送的数据还显示在诊断搜索的“自定义事件”下。 事件可以进行命名，并带有[筛选诊断搜索](./diagnostic-search.md)所依据的字符串属性和数值指标。
* <xref:Microsoft.VisualStudio.ApplicationInsights.TelemetryClient.TrackTrace%2A?displayProperty=nameWithType> 允许发送较长的数据，例如 POST 信息。
* <xref:Microsoft.VisualStudio.ApplicationInsights.TelemetryClient.TrackException%2A?displayProperty=nameWithType> 将异常详细信息（例如堆栈跟踪）发送到 Application Insights。

若要查看这些事件，请在左侧菜单中打开[搜索](./diagnostic-search.md)、选择“事件类型”下拉菜单，然后选择“自定义事件”、“跟踪”或“异常”   。

![深入了解](./media/asp-net-exceptions/customevents.png)

> [!NOTE]
> 如果应用生成大量遥测，自适应采样模块将通过仅发送具有代表性的事件部分自动减少发送到门户的量。 将以组为单位选择或取消选择属于同一操作的事件，以便可以在相关事件之间浏览。 有关详细信息，请参阅 [Application Insights 中的采样](./sampling.md)。

### <a name="how-to-see-request-post-data"></a>如何查看请求 POST 数据

请求详细信息不包含在 POST 调用中发送到应用的数据。 若要报告此数据：

* 在应用程序中[安装 SDK](./asp-net.md)。
* 在应用程序中插入代码以调用 [Microsoft.ApplicationInsights.TrackTrace()](./api-custom-events-metrics.md#tracktrace)。 在消息参数中发送 POST 数据。 允许的大小有限制，因此，应该尝试仅发送必要数据。
* 调查失败的请求时，查找关联的跟踪。

## <a name="capturing-exceptions-and-related-diagnostic-data"></a><a name="exceptions"></a>捕获异常和相关的诊断数据
首先，不会在门户中看到在应用中导致失败的所有异常。 将显示所有浏览器异常（如果在网页中使用 [JavaScript SDK](./javascript.md)）， 但大多数服务器异常由 IIS 导致，必须编写几行代码才能看到它们。

方法：

* 通过在异常处理程序中插入代码来 **显式记录异常**，从而报告这些异常。
* 通过配置 ASP.NET 框架 **自动捕获异常**。 框架类型不同，必要的附加内容也不同。

## <a name="reporting-exceptions-explicitly"></a>显式报告异常

最简单的方法是在异常处理程序中插入对 `trackException()` 的调用。

```javascript
try
{
    // ...
}
catch (ex)
{
    appInsights.trackException(ex, "handler loc",
    {
        Game: currentGame.Name,
        State: currentGame.State.ToString()
    });
}
```

```csharp
var telemetry = new TelemetryClient();

try
{
    // ...
}
catch (Exception ex)
{
    var properties = new Dictionary<string, string>
    {
        ["Game"] = currentGame.Name
    };

    var measurements = new Dictionary<string, double>
    {
        ["Users"] = currentGame.Users.Count
    };

    // Send the exception telemetry:
    telemetry.TrackException(ex, properties, measurements);
}
```

```VB
Dim telemetry = New TelemetryClient

Try
    ' ...
Catch ex as Exception
    ' Set up some properties:
    Dim properties = New Dictionary (Of String, String)
    properties.Add("Game", currentGame.Name)

    Dim measurements = New Dictionary (Of String, Double)
    measurements.Add("Users", currentGame.Users.Count)

    ' Send the exception telemetry:
    telemetry.TrackException(ex, properties, measurements)
End Try
```

属性和测量参数是可选的，但对于[筛选和添加](./diagnostic-search.md)额外信息很有用。 例如，如果有一个可运行多个游戏的应用，可查找与特定游戏相关的所有异常报表。 可向每个字典添加任意数量的项目。

## <a name="browser-exceptions"></a>浏览器异常

报告大多数浏览器异常。

如果网页包括内容分发网络或其他域中的脚本文件，确保脚本标记具有属性 `crossorigin="anonymous"`，并且服务器可发送 [CORS 标头](https://enable-cors.org/)。 这允许从这些资源中获取有关未处理的 JavaScript 异常的堆栈跟踪和详细信息。

## <a name="reuse-your-telemetry-client"></a>重用遥测客户端

> [!NOTE]
> 建议将 `TelemetryClient` 实例化一次，并在应用程序的整个生命周期内重复使用。

借助 [.NET 中的依赖项注入 (DI)](/dotnet/core/extensions/dependency-injection)、适当的 .NET SDK，并为 DI 正确配置 Application Insights，可以要求将 <xref:Microsoft.VisualStudio.ApplicationInsights.TelemetryClient> 作为构造函数参数。

```csharp
public class ExampleController : ApiController
{
    private readonly TelemetryClient _telemetryClient;

    public ExampleController(TelemetryClient telemetryClient)
    {
        _telemetryClient = telemetryClient;
    }
}
```

在前面的示例中，已将 `TelemetryClient` 注入到 `ExampleController` 类中。

## <a name="web-forms"></a>Web 窗体

在 Web 窗体中，当不存在通过 `CustomErrors` 配置的重定向时，HTTP 模块能够收集异常。 但是，如果有活动重定向，在 Global.asax.cs 中将以下行添加到 `Application_Error` 函数。

```csharp
void Application_Error(object sender, EventArgs e)
{
    if (HttpContext.Current.IsCustomErrorEnabled &&
        Server.GetLastError () != null)
    {
        _telemetryClient.TrackException(Server.GetLastError());
    }
}
```

在前面的示例中，`_telemetryClient` 是类型 <xref:Microsoft.VisualStudio.ApplicationInsights.TelemetryClient> 的类范围内变量。

## <a name="mvc"></a>MVC

从 Application Insights Web SDK 2.6 版（beta3 及更高版本）开始，Application Insights 会自动收集 MVC 5+ 控制器方法中引发的未经处理异常。 如果之前已添加自定义处理程序以跟踪此类异常，可以将将其删除以避免对异常进行双重跟踪。

当在以下位置或阶段引发异常时，在很多情况下，异常筛选器无法正确处理错误：

* 从控制器构造函数。
* 从消息处理程序。
* 在路由期间。
* 在响应内容序列化期间。
* 在应用程序启动期间。
* 在后台任务中。

仍需要手动跟踪应用程序 *处理* 的所有异常。
源自控制器的未经处理异常通常会导致 500“内部服务器错误”响应。 如果此类响应是由于处理异常而手动构造的（或根本没有异常），则可在 `ResultCode` 为 500 的相应请求遥测中跟踪它，但是 Application Insights SDK 无法跟踪相应异常。

### <a name="prior-versions-support"></a>以前版本支持

如果使用 Application Insights Web SDK 2.5（及更低版本）的 MVC 4（及更低版本），请参照以下示例跟踪异常。

如果 [CustomErrors](/previous-versions/dotnet/netframework-4.0/h0hfz6fc(v=vs.100)) 配置为 `Off`，[HTTP 模块](/previous-versions/dotnet/netframework-3.0/ms178468(v=vs.85))将可以收集异常。 但是，如果它是 `RemoteOnly`（默认值）或 `On`，异常将清除，并且 Application Insights 无法自动收集它。 可通过重写 [System.Web.Mvc.HandleErrorAttribute 类](/dotnet/api/system.web.mvc.handleerrorattribute)并应用重写类来修复该问题，如下面针对不同的 MVC 版本所示（[GitHub 源](https://github.com/AppInsightsSamples/Mvc2UnhandledExceptions/blob/master/MVC2App/Controllers/AiHandleErrorAttribute.cs)）：

```csharp
using System;
using System.Web.Mvc;
using Microsoft.ApplicationInsights;

namespace MVC2App.Controllers
{
    [AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, Inherited = true, AllowMultiple = true)]
    public class AiHandleErrorAttribute : HandleErrorAttribute
    {
        public override void OnException(ExceptionContext filterContext)
        {
            if (filterContext != null && filterContext.HttpContext != null && filterContext.Exception != null)
            {
                //If customError is Off, then AI HTTPModule will report the exception
                if (filterContext.HttpContext.IsCustomErrorEnabled)
                {   //or reuse instance (recommended!). see note above
                    var ai = new TelemetryClient();
                    ai.TrackException(filterContext.Exception);
                }
            }
            base.OnException(filterContext);
        }
    }
}
```

#### <a name="mvc-2"></a>MVC 2

将 HandleError 属性替换为控制器中的新属性。

```csharp
    namespace MVC2App.Controllers
    {
        [AiHandleError]
        public class HomeController : Controller
        {
            // Omitted for brevity
        }
    }
```

[示例](https://github.com/AppInsightsSamples/Mvc2UnhandledExceptions)

#### <a name="mvc-3"></a>MVC 3

在 Global.asax.cs 中将 `AiHandleErrorAttribute` 注册为全局筛选器：

```csharp
public class MyMvcApplication : System.Web.HttpApplication
{
    public static void RegisterGlobalFilters(GlobalFilterCollection filters)
    {
        filters.Add(new AiHandleErrorAttribute());
    }
}
```

[示例](https://github.com/AppInsightsSamples/Mvc3UnhandledExceptionTelemetry)

#### <a name="mvc-4-mvc5"></a>MVC 4、MVC5

在 FilterConfig.cs 中将 `AiHandleErrorAttribute` 注册为全局筛选器：

```csharp
public class FilterConfig
{
    public static void RegisterGlobalFilters(GlobalFilterCollection filters)
    {
        // Default replaced with the override to track unhandled exceptions
        filters.Add(new AiHandleErrorAttribute());
    }
}
```

[示例](https://github.com/AppInsightsSamples/Mvc5UnhandledExceptionTelemetry)

## <a name="web-api"></a>Web API

从 Application Insights Web SDK 2.6 版（beta3 及更高版本）开始，Application Insights 会自动收集 WebAPI 2+ 控制器方法中引发的未经处理异常。 如果之前已添加自定义处理程序以跟踪此类异常（如下面的示例中所述），则可以删除该处理程序以避免对异常进行双重跟踪。

存在大量无法处理异常筛选器的情况。 例如：

* 从控制器构造函数引发的异常。
* 从消息处理程序引发的异常。
* 在路由过程中引发的异常。
* 在响应内容序列化期间引发的异常。
* 在应用程序启动过程中引发的异常。
* 在后台任务中引发的异常。

仍需要手动跟踪应用程序 *处理* 的所有异常。
源自控制器的未经处理异常通常会导致 500“内部服务器错误”响应。 如果此类响应是由于处理异常而手动构造的（或根本没有异常），则可在 `ResultCode` 为 500 的相应请求遥测中跟踪它，但是 Application Insights SDK 无法跟踪相应异常。

### <a name="prior-versions-support"></a>以前版本支持

如果使用 Application Insights Web SDK 2.5（及更低版本）的 WebAPI 1（及更低版本），请参照以下示例跟踪异常。

#### <a name="web-api-1x"></a>Web API 1.x

替代 `System.Web.Http.Filters.ExceptionFilterAttribute`：

```csharp
using System.Web.Http.Filters;
using Microsoft.ApplicationInsights;

namespace WebAPI.App_Start
{
    public class AiExceptionFilterAttribute : ExceptionFilterAttribute
    {
    public override void OnException(HttpActionExecutedContext actionExecutedContext)
    {
        if (actionExecutedContext != null && actionExecutedContext.Exception != null)
        {  //or reuse instance (recommended!). see note above
            var ai = new TelemetryClient();
            ai.TrackException(actionExecutedContext.Exception);
        }
        base.OnException(actionExecutedContext);
    }
    }
}
```

可将此替换属性添加到特定控制器，或者将其添加到 `WebApiConfig` 类中的全局筛选器配置：

```csharp
using System.Web.Http;
using WebApi1.x.App_Start;

namespace WebApi1.x
{
    public static class WebApiConfig
    {
        public static void Register(HttpConfiguration config)
        {
            config.Routes.MapHttpRoute(
                name: "DefaultApi",
                routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional });
    
            // ...
            config.EnableSystemDiagnosticsTracing();
    
            // Capture exceptions for Application Insights:
            config.Filters.Add(new AiExceptionFilterAttribute());
        }
    }
}
```

[示例](https://github.com/AppInsightsSamples/WebApi_1.x_UnhandledExceptions)

#### <a name="web-api-2x"></a>Web API 2.x

添加 `IExceptionLogger` 的实现：

```csharp
using System.Web.Http.ExceptionHandling;
using Microsoft.ApplicationInsights;

namespace ProductsAppPureWebAPI.App_Start
{
    public class AiExceptionLogger : ExceptionLogger
    {
        public override void Log(ExceptionLoggerContext context)
        {
            if (context != null && context.Exception != null)
            {
                //or reuse instance (recommended!). see note above
                var ai = new TelemetryClient();
                ai.TrackException(context.Exception);
            }
            base.Log(context);
        }
    }
}
```

将其添加到 WebApiConfig 中的服务：

```csharp
using System.Web.Http;
using System.Web.Http.ExceptionHandling;
using ProductsAppPureWebAPI.App_Start;

namespace WebApi2WithMVC
{
    public static class WebApiConfig
    {
        public static void Register(HttpConfiguration config)
        {
            // Web API configuration and services
    
            // Web API routes
            config.MapHttpAttributeRoutes();
    
            config.Routes.MapHttpRoute(
                name: "DefaultApi",
                routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional });

            config.Services.Add(typeof(IExceptionLogger), new AiExceptionLogger());
        }
    }
}
```

[示例](https://github.com/AppInsightsSamples/WebApi_2.x_UnhandledExceptions)

作为替代项，可以：

1. 仅将 ExceptionHandler 替换为 IExceptionHandler 的自定义实现。 这仅在框架仍能够选择要发送哪个响应消息时调用（不会在终止实例连接时调用）
2. 异常筛选器（如上述 Web API 1.x 控制器上的部分中所述），并非在所有情况下都调用。

## <a name="wcf"></a>WCF

添加可扩展属性并实现 IErrorHandler 和 IServiceBehavior 的类。

```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.ServiceModel.Description;
    using System.ServiceModel.Dispatcher;
    using System.Web;
    using Microsoft.ApplicationInsights;

    namespace WcfService4.ErrorHandling
    {
      public class AiLogExceptionAttribute : Attribute, IErrorHandler, IServiceBehavior
      {
        public void AddBindingParameters(ServiceDescription serviceDescription,
            System.ServiceModel.ServiceHostBase serviceHostBase,
            System.Collections.ObjectModel.Collection<ServiceEndpoint> endpoints,
            System.ServiceModel.Channels.BindingParameterCollection bindingParameters)
        {
        }

        public void ApplyDispatchBehavior(ServiceDescription serviceDescription,
            System.ServiceModel.ServiceHostBase serviceHostBase)
        {
            foreach (ChannelDispatcher disp in serviceHostBase.ChannelDispatchers)
            {
                disp.ErrorHandlers.Add(this);
            }
        }

        public void Validate(ServiceDescription serviceDescription,
            System.ServiceModel.ServiceHostBase serviceHostBase)
        {
        }

        bool IErrorHandler.HandleError(Exception error)
        {//or reuse instance (recommended!). see note above
            var ai = new TelemetryClient();

            ai.TrackException(error);
            return false;
        }

        void IErrorHandler.ProvideFault(Exception error,
            System.ServiceModel.Channels.MessageVersion version,
            ref System.ServiceModel.Channels.Message fault)
        {
        }
      }
    }
```

将属性添加到服务实现：

```csharp
namespace WcfService4
{
    [AiLogException]
    public class Service1 : IService1
    {
        // Omitted for brevity
    }
}
```

[示例](https://github.com/AppInsightsSamples/WCFUnhandledExceptions)

## <a name="exception-performance-counters"></a>异常性能计数器

如果在服务器上[安装了 Azure Monitor Application Insights 代理](./status-monitor-v2-overview.md)，可以获取 .NET 测量的异常率图表。 这包括经处理和未经处理的 .NET 异常。

打开“指标资源管理器”选项卡，添加新图表，然后选择在“性能计数器”下列出的“异常率”。

.NET Framework 通过对间隔中的异常数进行计数并除以间隔长度计算异常率。

这与 Application Insights 门户通过对 TrackException 报告计数计算得出的“异常”计数不同。 采样间隔不同，SDK 不会为所有经处理和未经处理的异常发送 TrackException 报告。

## <a name="next-steps"></a>后续步骤

* [监视 REST、SQL 以及其他对依赖项的调用](./asp-net-dependencies.md)
* [监视器页面加载时间、浏览器异常和 AJAX 调用](./javascript.md)
* [监视性能计数器](./performance-counters.md)
