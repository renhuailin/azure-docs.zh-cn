---
title: Azure SignalR 服务故障排除指南
description: 了解如何排查常见问题
author: yjin81
ms.service: signalr
ms.topic: conceptual
ms.date: 11/06/2020
ms.author: yajin1
ms.openlocfilehash: 1e909273ae2413a67da6e3975e5a2bc50a68685d
ms.sourcegitcommit: ef448159e4a9a95231b75a8203ca6734746cd861
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123186814"
---
# <a name="troubleshooting-guide-for-azure-signalr-service-common-issues"></a>Azure SignalR 服务常见问题故障排除指南

本指南旨在根据客户过去几年内遇到和解决的常见问题提供有用的故障排除指南。

## <a name="access-token-too-long"></a>访问令牌太长

### <a name="possible-errors"></a>可能的错误

* 客户端 `ERR_CONNECTION_`
* 414 URI 太长
* 413 有效负载太大
* 访问令牌不得长于 4 K。 413 请求实体太大

### <a name="root-cause"></a>根本原因

对于 HTTP/2，单个标头的最大长度为 4 K。因此，如果使用浏览器访问 Azure 服务，则会出现有关此限制的 `ERR_CONNECTION_` 错误。

对于 HTTP/1.1 或 C# 客户端，最大 URI 长度为 12 K，最大标头长度 16 K。

使用 SDK 1.0.6 或更高版本时，`/negotiate` 会在生成的访问令牌大于 4 K 时引发“`413 Payload Too Large`”错误。

### <a name="solution"></a>解决方案

默认情况下，在生成针对 ASRS（**A** zure **S** ignal **R** **S** ervice，即 Azure SignalR 服务）的 JWT 访问令牌时，会包括 `context.User.Claims` 中的声明，这样，这些声明会被保留，并可以在客户端连接到 `Hub` 时从 ASRS 传递到 `Hub`。

在某些情况下，会使用 `context.User.Claims` 来存储应用服务器的大量信息，其中的大多数信息不是供 `Hub` 使用，而是供其他组件使用。

生成的访问令牌通过网络传递。对于 WebSocket/SSE 连接，访问令牌通过查询字符串传递。 因此，我们建议仅当 Hub 需要时才通过 ASRS 将必需的声明从客户端传递给应用服务器，这是最佳做法。 

可以通过 `ClaimsProvider` 在访问令牌中自定义传递给 ASRS 的声明。

以下代码适用于 ASP.NET Core：

```csharp
services.AddSignalR()
        .AddAzureSignalR(options =>
            {
                // pick up necessary claims
                options.ClaimsProvider = context => context.User.Claims.Where(...);
            });
```

以下代码适用于 ASP.NET：

```csharp
services.MapAzureSignalR(GetType().FullName, options =>
            {
                // pick up necessary claims
                options.ClaimsProvider = context.Authentication?.User.Claims.Where(...);
            });
```

[有关于故障排除的问题或反馈？请告诉我们。](https://aka.ms/asrs/survey/troubleshooting)

## <a name="tls-12-required"></a>需要 TLS 1.2

### <a name="possible-errors"></a>可能的错误

* ASP.NET 的“无可用服务器”错误 [#279](https://github.com/Azure/azure-signalr/issues/279)
* ASP.NET 的“连接未处于活动状态，无法将数据发送到服务。” 错误 [#324](https://github.com/Azure/azure-signalr/issues/324)
* “向 `https://<API endpoint>` 发出 HTTP 请求时出错。 此错误的原因可能是未在 HTTPS 用例中正确使用 HTTP.SYS 配置服务器证书。 此外，客户端与服务器之间的安全绑定不匹配也可能造成此错误。”

### <a name="root-cause"></a>根本原因

出于安全考虑，Azure 服务仅支持 TLS 1.2。 使用 .NET Framework 时，TLS 1.2 可能不是默认协议。 因此，无法成功建立与 ASRS 的服务器连接。

### <a name="troubleshooting-guide"></a>故障排除指南

1. 如果可以在本地重现此错误，请取消选中“仅我的代码”，引发所有 CLR 异常，并在本地调试应用服务器以查看引发的具体异常。
    * 取消选中“仅我的代码”

        :::image type="content" source="./media/signalr-howto-troubleshoot-guide/uncheck-just-my-code.png" alt-text="取消选中“仅我的代码”":::

    * 引发 CLR 异常

        :::image type="content" source="./media/signalr-howto-troubleshoot-guide/throw-clr-exceptions.png" alt-text="引发 CLR 异常":::

    * 请查看调试应用服务器端代码时引发的异常：
        
        :::image type="content" source="./media/signalr-howto-troubleshoot-guide/tls-throws.png" alt-text="引发异常":::

2. 对于 ASP.NET 错误，还可以将以下代码添加到 `Startup.cs`，以便启用详细的跟踪并查看日志中的错误。

    ```cs
    app.MapAzureSignalR(this.GetType().FullName);
    // Make sure this switch is called after MapAzureSignalR
    GlobalHost.TraceManager.Switch.Level = SourceLevels.Information;
    ```

### <a name="solution"></a>解决方案

将以下代码添加到 Startup.cs：

```csharp
ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls12;
```

[有关于故障排除的问题或反馈？请告诉我们。](https://aka.ms/asrs/survey/troubleshooting)

## <a name="400-bad-request-returned-for-client-requests"></a>针对客户端请求返回了“400 错误请求”

### <a name="root-cause"></a>根本原因

检查客户端请求是否有多个 `hub` 查询字符串。 `hub` 是保留的查询参数。如果服务检测到查询中有多个 `hub`，则会引发 400 错误。

[有关于故障排除的问题或反馈？请告诉我们。](https://aka.ms/asrs/survey/troubleshooting)

## <a name="401-unauthorized-returned-for-client-requests"></a>针对客户端请求返回“401 未授权”

### <a name="root-cause"></a>根本原因

JWT 令牌生存期的默认值目前为 1 小时。

对于 ASP.NET Core SignalR，它在使用 WebSocket 传输类型时是正常的。

对于 ASP.NET Core SignalR 的其他传输类型（SSE 和长轮询），这意味着默认情况下连接最多可以保持 1 小时。

对于 ASP.NET SignalR，客户端会不时地将 `/ping` KeepAlive 请求发送给服务。当 `/ping` 失败时，客户端会中止连接，且不再重新连接。 这意味着，对于 ASP.NET SignalR，默认令牌生存期会使连接最多持续 1 小时，而无论传输类型是哪一种。

### <a name="solution"></a>解决方案

出于安全考虑，不建议延长 TTL。 建议在发生此类 401 错误时，添加客户端的重新连接逻辑以重启连接。 客户端在重启连接时，会与应用服务器协商以再次获取 JWT 令牌并获取续订的令牌。

请查看[此文](#restart_connection)，了解如何重启客户端连接。

[有关于故障排除的问题或反馈？请告诉我们。](https://aka.ms/asrs/survey/troubleshooting)

## <a name="404-returned-for-client-requests"></a>针对客户端请求返回 404

对于 SignalR 持久性连接，它首先与 Azure SignalR 服务执行 `/negotiate`，然后建立与 Azure SignalR 服务的实际连接。

### <a name="troubleshooting-guide"></a>故障排除指南

* 按照[如何查看传出请求](#view_request)操作，以获取从客户端到服务的请求。
* 出现 404 时，请检查请求的 URL。 如果 URL 是针对你的 Web 应用，并且类似于 `{your_web_app}/hubs/{hubName}`，则请检查客户端 `SkipNegotiation` 是否为 `true`。 使用 Azure SignalR 时，客户端会在首次与应用服务器协商时接收重定向 URL。 使用 Azure SignalR 时，客户端不应跳过协商。
* 如果在调用 `/negotiate` 后过了 5 秒以上才处理连接请求，则可能会发生另一 404 错误。 如果对服务请求的响应较慢，请检查客户端请求的时间戳，并向我们提出问题。

[有关于故障排除的问题或反馈？请告诉我们。](https://aka.ms/asrs/survey/troubleshooting)

## <a name="404-returned-for-aspnet-signalrs-reconnect-request"></a>针对 ASP.NET SignalR 的重新连接请求返回了 404

对于 ASP.NET SignalR，当[客户端连接断开](#client_connection_drop)时，它会使用相同的 `connectionId` 重新连接三次，然后才停止连接。 如果连接断开是由于网络间歇性问题，则可以使用 `/reconnect`。`/reconnect` 可以成功地重新建立持久性连接。 在其他情况下，例如，在客户端连接断开是因为路由的服务器连接断开的情况下，或者在 SignalR 服务有一些内部错误（如实例重启/故障转移/部署错误）的情况下，连接不再存在，因此 `/reconnect` 会返回 `404`。 它是 `/reconnect` 的预期行为，三次重试后连接会停止。 建议在连接停止时使用[连接重启](#restart_connection)逻辑。

[有关于故障排除的问题或反馈？请告诉我们。](https://aka.ms/asrs/survey/troubleshooting)

## <a name="429-too-many-requests-returned-for-client-requests"></a>针对客户端请求返回“429 (请求过多)”

存在两种情况。

### <a name="concurrent-connection-count-exceeds-limit"></a>**并发** 连接计数超出限制。

对于免费实例，并发连接计数限制为 20。对于标准实例，每个单位的并发连接计数限制为 1K，这意味着 100 个单位允许 100 K 个并发连接。    

连接包括客户端连接和服务器连接。 请查看[此文](./signalr-concept-messages-and-connections.md#how-connections-are-counted)，了解如何进行连接计数。

### <a name="too-many-negotiate-requests-at-the-same-time"></a>同时发出了过多的协商请求

我们建议先经过一段随机的延迟再重新连接；有关重试示例，请查看[此文](#restart_connection)。

[有关于故障排除的问题或反馈？请告诉我们。](https://aka.ms/asrs/survey/troubleshooting)

## <a name="500-error-when-negotiate-azure-signalr-service-is-not-connected-yet-please-try-again-later"></a>协商时出现 500 错误：Azure SignalR 服务尚未连接，请稍后重试

### <a name="root-cause"></a>根本原因

如果没有与 Azure SignalR 服务的服务器连接，则会报告此错误。

### <a name="troubleshooting-guide"></a>故障排除指南

启用服务器端跟踪，以便在服务器尝试连接到 Azure SignalR 服务时查明错误详情。

### <a name="enable-server-side-logging-for-aspnet-core-signalr"></a>为 ASP.NET Core SignalR 启用服务器端日志记录

ASP.NET Core SignalR 的服务器端日志记录与在 ASP.NET Core Framework 中提供的基于 `ILogger` 的[日志记录](/aspnet/core/fundamentals/logging/?tabs=aspnetcore2x&view=aspnetcore-2.1&preserve-view=true)集成。 你可以使用 `ConfigureLogging` 来启用服务器端日志记录，示例用法如下：

```csharp
.ConfigureLogging((hostingContext, logging) =>
        {
            logging.AddConsole();
            logging.AddDebug();
        })
```

Azure SignalR 的记录器类别始终以 `Microsoft.Azure.SignalR` 开头。 若要从 Azure SignalR 启用详细日志，请在 appsettings.json 文件中将前面的前缀配置为 `Debug` 级别，如下所示：

```json
{
    "Logging": {
        "LogLevel": {
            ...
            "Microsoft.Azure.SignalR": "Debug",
            ...
        }
    }
}
```

#### <a name="enable-server-side-traces-for-aspnet-signalr"></a>为 ASP.NET SignalR 启用服务器端跟踪

使用 >= `1.0.0` 的 SDK 版本时，可以通过将以下内容添加到 `web.config` 来启用跟踪：（[详细信息](https://github.com/Azure/azure-signalr/issues/452#issuecomment-478858102)）

```xml
<system.diagnostics>
    <sources>
      <source name="Microsoft.Azure.SignalR" switchName="SignalRSwitch">
        <listeners>
          <add name="ASRS" />
        </listeners>
      </source>
    </sources>
    <!-- Sets the trace verbosity level -->
    <switches>
      <add name="SignalRSwitch" value="Information" />
    </switches>
    <!-- Specifies the trace writer for output -->
    <sharedListeners>
      <add name="ASRS" type="System.Diagnostics.TextWriterTraceListener" initializeData="asrs.log.txt" />
    </sharedListeners>
    <trace autoflush="true" />
  </system.diagnostics>
```

<a name="client_connection_drop"></a>

[有关于故障排除的问题或反馈？请告诉我们。](https://aka.ms/asrs/survey/troubleshooting)

## <a name="client-connection-drops"></a>客户端连接断开

当客户端连接到 Azure SignalR 时，客户端与 Azure SignalR 之间的持久性连接有时可能会因不同的原因而断开。 此部分介绍导致此类连接断开的几种可能性，并提供一些有关如何确定根本原因的指导。

### <a name="possible-errors-seen-from-the-client-side"></a>客户端出现的可能错误

* `The remote party closed the WebSocket connection without completing the close handshake`
* `Service timeout. 30.00ms elapsed without receiving a message from service.`
* `{"type":7,"error":"Connection closed with an error."}`
* `{"type":7,"error":"Internal server error."}`

### <a name="root-cause"></a>根本原因

客户端连接可能会在各种情况下断开：
* 当 `Hub` 引发传入请求的异常时。
* 当客户端路由到的服务器连接断开时。请参阅下一部分，了解有关[服务器连接断开](#server_connection_drop)的详细信息。
* 当客户端与 SignalR 服务之间发生网络连接问题时。
* 当 SignalR 服务有一些内部错误（如实例重启错误、故障转移错误、部署错误等）时。

### <a name="troubleshooting-guide"></a>故障排除指南

1. 打开应用服务器端日志以查看是否发生了异常
2. 检查应用服务器端事件日志以查看应用服务器是否已重启
3. 创建一个将提交给我们的问题，提供时间范围，并通过电子邮件向我们发送资源名称

[有关于故障排除的问题或反馈？请告诉我们。](https://aka.ms/asrs/survey/troubleshooting)

## <a name="client-connection-increases-constantly"></a>客户端连接计数不断增加

可能是客户端连接使用不当导致的。 如果用户忘记停止/释放 SignalR 客户端，则连接会保持打开状态。

### <a name="possible-errors-seen-from-the-signalrs-metrics-that-is-in-monitoring-section-of-azure-portal-resource-menu"></a>在 Azure 门户资源菜单的“监视”部分的 SignalR 指标中出现可能的错误

在 Azure SignalR 的指标中，客户端连接计数会不断增加，持续很长时间。

:::image type="content" source="./media/signalr-howto-troubleshoot-guide/client-connection-increasing-constantly.jpg" alt-text="客户端连接计数不断增加":::

### <a name="root-cause"></a>根本原因

从未调用 SignalR 客户端连接的 `DisposeAsync`，因此连接保持打开状态。

### <a name="troubleshooting-guide"></a>故障排除指南

检查 SignalR 客户端是否 **从未** 关闭。

### <a name="solution"></a>解决方案

检查是否关闭了连接。 手动调用 `HubConnection.DisposeAsync()`，以便在使用连接后停止连接。

例如：

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl(...)
    .Build();
try
{
    await connection.StartAsync();
    // Do your stuff
    await connection.StopAsync();
}
finally
{
    await connection.DisposeAsync();
}
```

### <a name="common-improper-client-connection-usage"></a>常见的客户端连接使用不当情况

#### <a name="azure-function-example"></a>Azure 函数示例 

当有人在 Azure 函数方法中建立 SignalR 客户端连接，而不是使其成为 Azure 函数类的静态成员时，通常会出现此问题。 你可能预计只会建立一个客户端连接，但却发现 Azure 门户资源菜单的“监视”部分的指标中客户端连接计数不断增加，所有这些连接只有在 Azure 函数或 Azure SignalR 服务重启后才会断开。 这是因为，对于每个请求，Azure 函数都会创建一个客户端连接，而如果你不在 Azure 函数方法中停止客户端连接，则客户端会让到 Azure SignalR 服务的连接保持活动状态。 

#### <a name="solution"></a>解决方案

* 如果在 Azure 函数中使用 SignalR 客户端或将 SignalR 客户端用作单一实例，请记得关闭客户端连接。
* 无需在 Azure 函数中使用 SignalR 客户端，你可以在其他任何位置创建 SignalR 客户端，并使用[适用于 Azure SignalR 服务的 Azure Functions 绑定](https://github.com/Azure/azure-functions-signalrservice-extension)，以便通过[协商](https://github.com/Azure/azure-functions-signalrservice-extension/blob/dev/samples/simple-chat/csharp/FunctionApp/Functions.cs#L22)将客户端连接到 Azure SignalR。 此外，还可以利用绑定来[发送消息](https://github.com/Azure/azure-functions-signalrservice-extension/blob/dev/samples/simple-chat/csharp/FunctionApp/Functions.cs#L40)。 可在[此处](https://github.com/Azure/azure-functions-signalrservice-extension/tree/dev/samples)找到用于协商客户端和发送消息的示例。 可在[此处](https://github.com/Azure/azure-functions-signalrservice-extension)找到更多信息。
* 在 Azure 函数中使用 SignalR 客户端时，可能会有更适合你的方案的体系结构。 检查是否设计了正确的无服务器体系结构。 可参阅[在 Azure Functions 中使用 SignalR 服务绑定的实时无服务器应用程序](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService)。

<a name="server_connection_drop"></a>

[有关于故障排除的问题或反馈？请告诉我们。](https://aka.ms/asrs/survey/troubleshooting)

## <a name="server-connection-drops"></a>服务器连接断开

当应用服务器在后台启动时，Azure SDK 就会开始启动到远程 Azure SignalR 的服务器连接。 如 [Azure SignalR 服务内部情况](https://github.com/Azure/azure-signalr/blob/dev/docs/internal.md)所述，Azure SignalR 会将传入客户端流量路由到这些服务器连接。 断开服务器连接后，它所处理的所有客户端连接也会关闭。

应用服务器与 SignalR 服务之间的连接是持久性连接，因此可能会遇到网络连接问题。 在服务器 SDK 中，我们对服务器连接实施“始终进行重新连接”策略。 我们还建议用户使用随机延迟时间向客户端添加连续重新连接逻辑，避免向服务器同时发送大规模的请求，这是最佳做法。

我们会定期发布 Azure SignalR 服务的新版本，有时会修补或升级 Azure 范围内的操作系统，偶尔也会使用依赖服务。 这可能会导致服务中断很短的时间，但只要客户端存在断开连接/重新连接机制，此影响就会很小，就像任何客户端导致的断开连接-重新连接一样。

此部分介绍导致服务器连接断开的几种可能性，并提供一些有关如何确定根本原因的指导。

### <a name="possible-errors-seen-from-the-server-side"></a>服务器端出现的可能错误

* `[Error]Connection "..." to the service was dropped`
* `The remote party closed the WebSocket connection without completing the close handshake`
* `Service timeout. 30.00ms elapsed without receiving a message from service.`

### <a name="root-cause"></a>根本原因

服务器-服务连接通过 ASRS（**A** zure **S** ignal **R** **S** ervice，Azure SignalR 服务）关闭。

如果发生 ping 超时，原因可能是服务器端的 CPU 使用率较高或线程池资源枯竭。

对于 ASP.NET SignalR，SDK 1.6.0 中已修复一个已知问题。 将 SDK 升级到最新版本。

## <a name="thread-pool-starvation"></a>线程池资源枯竭

如果服务器的资源枯竭，则意味着没有任何线程在处理消息。 所有线程在特定方法中均未响应。

通常，这种情况是异步超同步或者异步方法中的 `Task.Result`/`Task.Wait()` 导致的。

请参阅 [ASP.NET Core 性能最佳做法](/aspnet/core/performance/performance-best-practices#avoid-blocking-calls)。

详细了解[线程池资源枯竭](/archive/blogs/vancem/diagnosing-net-core-threadpool-starvation-with-perfview-why-my-service-is-not-saturating-all-cores-or-seems-to-stall)。

### <a name="how-to-detect-thread-pool-starvation"></a>如何检测线程池资源枯竭

检查线程计数。 如果当时没有出现高峰，请执行以下步骤：
* 如果使用的是 Azure 应用服务，请检查指标中的线程计数。 检查 `Max` 聚合：
    
  :::image type="content" source="media/signalr-howto-troubleshoot-guide/metrics-thread-count.png" alt-text="Azure 应用服务中“最大线程计数”窗格的屏幕截图。":::

* 如果使用的是 .NET Framework，可以在服务器 VM 的性能监视器中找到[指标](/dotnet/framework/debug-trace-profile/performance-counters#lock-and-thread-performance-counters)。
* 如果在容器中使用 .NET Core，请参阅[在容器中收集诊断数据](/dotnet/core/diagnostics/diagnostics-in-containers)。

还可以使用代码来检测线程池资源枯竭：

```csharp
public class ThreadPoolStarvationDetector : EventListener
{
    private const int EventIdForThreadPoolWorkerThreadAdjustmentAdjustment = 55;
    private const uint ReasonForStarvation = 6;

    private readonly ILogger<ThreadPoolStarvationDetector> _logger;

    public ThreadPoolStarvationDetector(ILogger<ThreadPoolStarvationDetector> logger)
    {
        _logger = logger;
    }

    protected override void OnEventSourceCreated(EventSource eventSource)
    {
        if (eventSource.Name == "Microsoft-Windows-DotNETRuntime")
        {
            EnableEvents(eventSource, EventLevel.Informational, EventKeywords.All);
        }
    }

    protected override void OnEventWritten(EventWrittenEventArgs eventData)
    {
        // See: https://docs.microsoft.com/en-us/dotnet/framework/performance/thread-pool-etw-events#threadpoolworkerthreadadjustmentadjustment
        if (eventData.EventId == EventIdForThreadPoolWorkerThreadAdjustmentAdjustment &&
            eventData.Payload[3] as uint? == ReasonForStarvation)
        {
            _logger.LogWarning("Thread pool starvation detected!");
        }
    }
}
```
    
将此代码添加到服务中：
    
```csharp
service.AddSingleton<ThreadPoolStarvationDetector>();
```

然后，检查当服务器连接断开（根据 ping 超时来判断）时生成的日志。

### <a name="how-to-find-the-root-cause-of-thread-pool-starvation"></a>如何找到线程池资源枯竭的根本原因

若要找到线程池资源枯竭的根本原因，请执行以下操作：

* 转储内存，然后分析调用堆栈。 有关详细信息，请参阅[收集和分析内存转储](https://devblogs.microsoft.com/dotnet/collecting-and-analyzing-memory-dumps/)。
* 检测到线程池资源枯竭时，使用 [clrmd](https://github.com/microsoft/clrmd) 来转储内存。 然后记录调用堆栈。

### <a name="troubleshooting-guide"></a>故障排除指南

1. 打开应用服务器端日志以查看是否有异常情况发生。
2. 检查应用服务器端事件日志以查看应用服务器是否已重启。
3. 创建问题。 提供时间范围，并通过电子邮件将资源名称告诉我们。

[有关于故障排除的问题或反馈？请告诉我们。](https://aka.ms/asrs/survey/troubleshooting)

## <a name="tips"></a>提示

<a name="view_request"></a>

### <a name="how-to-view-the-outgoing-request-from-the-client"></a>如何查看客户端的传出请求？

以 ASP.NET Core 的为例（ASP.NET 的类似）：

* 从浏览器：以 Chrome 为例，可以使用 F12 打开控制台窗口，并切换到“网络”选项卡。可能需要使用 F5 刷新页面，以从头开始捕获网络。

    :::image type="content" source="./media/signalr-howto-troubleshoot-guide/chrome-network.gif" alt-text="Chrome 的“查看网络”":::

* 从 C# 客户端：

    可以使用 [Fiddler](https://www.telerik.com/fiddler) 查看本地 Web 流量。 从 Fiddler 4.5 开始，支持 WebSocket 流量。

    :::image type="content" source="./media/signalr-howto-troubleshoot-guide/fiddler-view-network-inline.png" alt-text="Fiddler 的“查看网络”" lightbox="./media/signalr-howto-troubleshoot-guide/fiddler-view-network.png":::

<a name="restart_connection"></a>

### <a name="how-to-restart-client-connection"></a>如何重启客户端连接？
    
下面是[示例代码](https://github.com/Azure/azure-signalr/tree/dev/samples)，其中包含可以与“始终重试”策略配合使用的重启连接逻辑：

* [ASP.NET Core C# 客户端](https://github.com/Azure/azure-signalr/tree/dev/samples/ChatSample/ChatSample.CSharpClient/Program.cs#L64)

* [ASP.NET Core JavaScript 客户端](https://github.com/Azure/azure-signalr/blob/dev/samples/ChatSample/ChatSample.Net50/wwwroot/index.html#L171)

* [ASP.NET C# 客户端](https://github.com/Azure/azure-signalr/tree/dev/samples/AspNet.ChatSample/AspNet.ChatSample.CSharpClient/Program.cs#L78)

* [ASP.NET JavaScript 客户端](https://github.com/Azure/azure-signalr/tree/dev/samples/AspNet.ChatSample/AspNet.ChatSample.JavaScriptClient/wwwroot/index.html#L71)

[有关于故障排除的问题或反馈？请告诉我们。](https://aka.ms/asrs/survey/troubleshooting)

## <a name="next-steps"></a>后续步骤

在本指南中，你了解了如何处理常见问题。 你还可以了解更多常用的故障排除方法。 

> [!div class="nextstepaction"]
> [如何排查连接问题和消息传送问题](./signalr-howto-troubleshoot-method.md)
