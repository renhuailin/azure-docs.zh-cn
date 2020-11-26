---
title: Azure SignalR 服务故障排除指南
description: 了解如何排查常见问题
author: YanJin
ms.service: signalr
ms.topic: conceptual
ms.date: 11/06/2020
ms.author: yajin1
ms.openlocfilehash: cc17dcef7a554bee2715c79ba7d0c2356db2c6b3
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185651"
---
# <a name="troubleshooting-guide-for-azure-signalr-service-common-issues"></a>Azure SignalR 服务常见问题疑难解答指南

本指南旨在根据客户在过去几年内遇到和解决的常见问题提供有用的故障排除指南。

## <a name="access-token-too-long"></a>访问令牌太长

### <a name="possible-errors"></a>可能的错误：

* 客户端 `ERR_CONNECTION_`
* 414 URI 太长
* 413 有效负载太大
* 访问令牌不得长于4K。 413 请求实体太大

### <a name="root-cause"></a>根本原因：

对于 HTTP/2，单个标头的最大长度为 **4 K**，因此，如果使用浏览器访问 Azure 服务，则会出现 `ERR_CONNECTION_` 此限制的错误。

对于 HTTP/1.1 或 c # 客户端，最大 URI 长度为 **12 k**，最大标头长度为 **16 k**。

使用 SDK 版本 **1.0.6** 或更高版本 `/negotiate` 时，将 `413 Payload Too Large` 在生成的访问令牌大于 **4 K** 时引发。

### <a name="solution"></a>解决方案：

默认情况下， `context.User.Claims` 在向 **ASRS** (z **A** **S** ignal **R** **S** ervice) 生成 JWT 访问令牌时，将包含中的声明，以便在客户端连接到时，保留这些声明，并可将其从 **ASRS** 传递到 `Hub` `Hub` 。

在某些情况下， `context.User.Claims` 利用来存储应用服务器的大量信息，其中的大多数信息都不是由 s 使用，而是由 `Hub` 其他组件使用。

生成的访问令牌通过网络传递，对于 WebSocket/SSE 连接，访问令牌通过查询字符串传递。 作为最佳做法，我们建议在中心需要时，仅通过 **ASRS** 将 **所需** 的声明传递给应用服务器。

你可以在 `ClaimsProvider` 访问令牌中自定义传递到 **ASRS** 的声明。

对于 ASP.NET Core：
```cs
services.AddSignalR()
        .AddAzureSignalR(options =>
            {
                // pick up necessary claims
                options.ClaimsProvider = context => context.User.Claims.Where(...);
            });
```

对于 ASP.NET：
```cs
services.MapAzureSignalR(GetType().FullName, options =>
            {
                // pick up necessary claims
                options.ClaimsProvider = context.Authentication?.User.Claims.Where(...);
            });
```

## <a name="tls-12-required"></a>需要 TLS 1。2

### <a name="possible-errors"></a>可能的错误：

* ASP.NET "无服务器可用" 错误 [#279](https://github.com/Azure/azure-signalr/issues/279)
* ASP.NET "该连接未处于活动状态，无法将数据发送到该服务"。 错误 [#324](https://github.com/Azure/azure-signalr/issues/324)
* "向 https://发出 HTTP 请求时出错 <API endpoint> 。 发生此错误的原因可能是，服务器证书未正确配置，而在 HTTPS 情况下 HTTP.SYS。 如果客户端与服务器之间的安全绑定不匹配，也可能导致此错误。

### <a name="root-cause"></a>根本原因：

出于安全考虑，Azure 服务仅支持 TLS 1.2。 对于 .NET framework，TLS 1.2 可能不是默认协议。 因此，无法成功建立到 ASRS 的服务器连接。

### <a name="troubleshooting-guide"></a>故障排除指南

1. 如果可以在本地重现此错误，请取消选中 *仅我的代码* 并引发所有 CLR 异常，并在本地调试应用服务器以查看引发的异常。
    * 取消选中 *仅我的代码*

        :::image type="content" source="./media/signalr-howto-troubleshoot-guide/uncheck-just-my-code.png" alt-text="取消选中仅我的代码":::

    * 引发 CLR 异常

        :::image type="content" source="./media/signalr-howto-troubleshoot-guide/throw-clr-exceptions.png" alt-text="引发 CLR 异常":::

    * 请参阅调试应用服务器端代码时引发的异常：
        
        :::image type="content" source="./media/signalr-howto-troubleshoot-guide/tls-throws.png" alt-text="异常引发":::

2. 对于 ASP.NET，你还可以将以下代码添加到 `Startup.cs` 以启用详细跟踪，并查看日志中的错误。
```cs
app.MapAzureSignalR(this.GetType().FullName);
// Make sure this switch is called after MapAzureSignalR
GlobalHost.TraceManager.Switch.Level = SourceLevels.Information;
```

### <a name="solution"></a>解决方案：

将以下代码添加到启动：
```cs
ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls12;
```

## <a name="400-bad-request-returned-for-client-requests"></a>400为客户端请求返回了错误的请求

### <a name="root-cause"></a>根本原因

检查客户端请求是否有多个 `hub` 查询字符串。 `hub` 是一个保留的查询参数，如果服务在查询中检测到多个查询参数，则将引发 400 `hub` 。

## <a name="401-unauthorized-returned-for-client-requests"></a>针对客户端请求返回“401 未授权”

### <a name="root-cause"></a>根本原因

当前，JWT 令牌生存期的默认值为1小时。

对于 ASP.NET Core SignalR，使用 WebSocket 传输类型时，它是正常的。

对于 ASP.NET Core SignalR 的其他传输类型（SSE 和长轮询），这意味着默认情况下连接最多可以保留1小时。

对于 ASP.NET SignalR，客户端会在一段 `/ping` 时间后向服务发送一个 KeepAlive 请求，当 `/ping` 发生故障时，客户端将 **中止** 连接并且永远不会重新连接。 这意味着，对于 ASP.NET SignalR，默认令牌生存期使得所有传输类型的连接持续 **时间最** 长为1小时。

### <a name="solution"></a>解决方案

出于安全考虑，不建议扩展 TTL。 建议在发生此类401时，添加客户端的重新连接逻辑以重新启动连接。 当客户端重新启动连接时，它会与应用服务器协商以再次获取 JWT 令牌并获取续订的令牌。

查看 [此处](#restart_connection) 以了解如何重新启动客户端连接。

## <a name="404-returned-for-client-requests"></a>针对客户端请求返回 404

对于 SignalR 持续性连接，首先连接 `/negotiate` 到 Azure SignalR 服务，并建立与 Azure SignalR 服务的实际连接。

### <a name="troubleshooting-guide"></a>故障排除指南

* 按照 [如何查看传出请求](#view_request) ，以获取从客户端到服务的请求。
* 出现404时，检查请求的 URL。 如果 URL 面向 web 应用，并且类似于 `{your_web_app}/hubs/{hubName}` ，请检查客户端 `SkipNegotiation` 是否为 `true` 。 使用 Azure SignalR 时，客户端将在首次与应用服务器协商时接收重定向 URL。 使用 Azure SignalR 时，客户端 **不** 应跳过协商。
* 调用后处理连接请求超过 **5** 秒时，可能会发生另一 404 `/negotiate` 。 如果对服务的请求速度较慢，请检查客户端请求的时间戳，并向我们提出问题。

## <a name="404-returned-for-aspnet-signalrs-reconnect-request"></a>ASP.NET SignalR 的重新连接请求返回了404

对于 ASP.NET SignalR，当 [客户端连接断开](#client_connection_drop)时，它将使用相同 `connectionId` 的三次重新连接，然后停止连接。 `/reconnect` 如果由于网络间歇问题而导致连接断开，可以 `/reconnect` 成功地重新建立持久连接，这会有所帮助。 例如，在其他情况下，客户端连接被丢弃，因为路由服务器连接已断开，或 SignalR 服务有一些内部错误，如实例重启/故障转移/部署，连接不再存在，因此会 `/reconnect` 返回 `404` 。 这是中的预期行为 `/reconnect` ，三次后重试连接停止。 建议在连接停止时进行 [连接重新启动](#restart_connection) 逻辑。

## <a name="429-too-many-requests-returned-for-client-requests"></a>429 (为客户端请求返回的请求太多) 

如果你的 **并发** 连接计数超出限制，429将返回。

对于 **免费** 实例，对于 **标准** 实例，**并发** 连接计数限制为20，**每个单位** 的 **并发** 连接计数限制为 1 K，这意味着 Unit100 允许 100-K 并发连接。

连接包括客户端和服务器连接。 查看 [此处](./signalr-concept-messages-and-connections.md#how-connections-are-counted) 以了解如何对连接进行计数。

## <a name="500-error-when-negotiate-azure-signalr-service-is-not-connected-yet-please-try-again-later"></a>500协商时出错： Azure SignalR 服务尚未连接，请稍后重试。

### <a name="root-cause"></a>根本原因

如果未连接到 Azure SignalR Service，则会报告此错误。

### <a name="troubleshooting-guide"></a>故障排除指南

启用服务器端跟踪，在服务器尝试连接到 Azure SignalR 服务时查看错误详细信息。

#### <a name="enable-server-side-logging-for-aspnet-core-signalr"></a>为 ASP.NET Core SignalR 启用服务器端日志记录

ASP.NET Core SignalR 的服务器端日志记录与 `ILogger` ASP.NET Core 框架中提供的基于的 [日志记录](/aspnet/core/fundamentals/logging/?tabs=aspnetcore2x&view=aspnetcore-2.1) 集成在一起。 你可以使用启用服务器端日志记录 `ConfigureLogging` ，例如以下示例：
```cs
.ConfigureLogging((hostingContext, logging) =>
        {
            logging.AddConsole();
            logging.AddDebug();
        })
```
Azure SignalR 的记录器类别始终以开头 `Microsoft.Azure.SignalR` 。 若要从 Azure SignalR 启用详细日志，请在如下所示的 `Debug` 文件 **appsettings.js** 中将前面的前缀配置为 level：
```JSON
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

使用 SDK 版本 >= 时 `1.0.0` ，可以通过将以下内容添加到来启用跟踪 `web.config` ： ([详细信息](https://github.com/Azure/azure-signalr/issues/452#issuecomment-478858102)) 
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

## <a name="client-connection-drops"></a>客户端连接断开

当客户端连接到 Azure SignalR 时，客户端与 Azure SignalR 之间的持续连接有时可能会出于不同的原因而下降。 本部分介绍导致此类连接删除的几个可能，并提供一些有关如何识别根本原因的指导。

### <a name="possible-errors-seen-from-the-client-side"></a>客户端出现可能的错误

* `The remote party closed the WebSocket connection without completing the close handshake`
* `Service timeout. 30.00ms elapsed without receiving a message from service.`
* `{"type":7,"error":"Connection closed with an error."}`
* `{"type":7,"error":"Internal server error."}`

### <a name="root-cause"></a>根本原因：

在各种情况下，客户端连接可能会下降：
* 当 `Hub` 引发传入请求的异常时。
* 当客户端路由到的服务器连接断开时，请参阅下一节，了解有关 [服务器连接删除](#server_connection_drop)的详细信息。
* 如果客户端与 SignalR 服务之间发生网络连接问题。
* SignalR 服务有一些内部错误，如实例重启、故障转移、部署等。

### <a name="troubleshooting-guide"></a>故障排除指南

1. 打开应用服务器端日志以查看是否发生了异常
2. 检查应用服务器端事件日志以查看应用服务器是否已重新启动
3. 为我们创建一个问题，提供时间范围，并通过电子邮件向我们发送资源名称


## <a name="client-connection-increases-constantly"></a>客户端连接持续增加

这可能是由于客户端连接的使用不正确而导致的。 如果有人忘记停止/释放 SignalR 客户端，则连接将保持打开状态。

### <a name="possible-errors-seen-from-the-signalrs-metrics-that-is-in-monitoring-section-of-azure-portal-resource-menu"></a>在 Azure 门户资源菜单的 "监视" 部分的 SignalR 指标中出现可能的错误

在 Azure SignalR 的指标中，客户端连接会持续很长时间。

:::image type="content" source="./media/signalr-howto-troubleshoot-guide/client-connection-increasing-constantly.jpg" alt-text="客户端连接不断增长":::

### <a name="root-cause"></a>根本原因：

SignalR 客户端连接 `DisposeAsync` 永远不会被调用，连接保持打开状态。

### <a name="troubleshooting-guide"></a>故障排除指南

1. 检查 SignalR 客户端是否 **永不** 关闭。

### <a name="solution"></a>解决方案

检查是否关闭了连接。 `HubConnection.DisposeAsync()`使用时，请手动调用以停止连接。

例如：

```C#
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

### <a name="common-improper-client-connection-usage"></a>常见的客户端连接使用不正确

#### <a name="azure-function-example"></a>Azure Function 示例 

当有人在 Azure Function 方法中建立 SignalR 客户端连接，而不是使其成为函数类的静态成员时，通常会出现此问题。 你可能预计只会建立一个客户端连接，但你会发现，在 Azure 门户资源菜单的 "监视" 部分的指标中，客户端连接数不断增加，所有这些连接仅在 Azure Function 或 Azure SignalR 服务重启后才会删除。 这是因为对于 **每个** 请求，Azure 函数会创建 **一个** 客户端连接，如果不在 Function 方法中停止客户端连接，则客户端会将连接连接到 Azure SignalR 服务。

#### <a name="solution"></a>解决方案

* 如果在 Azure 函数中使用 SignalR 客户端或将 SignalR 客户端用作单一实例，请记得关闭客户端连接。
* 你可以在其他任何位置创建 SignalR 客户端，并使用 [Azure SignalR 服务 Azure Functions 绑定](https://github.com/Azure/azure-functions-signalrservice-extension) 将客户端 [协商](https://github.com/Azure/azure-functions-signalrservice-extension/blob/dev/samples/simple-chat/csharp/FunctionApp/Functions.cs#L22) 到 azure SignalR，而不是在 Azure 函数中使用 SignalR 客户端。 还可以利用绑定来 [发送消息](https://github.com/Azure/azure-functions-signalrservice-extension/blob/dev/samples/simple-chat/csharp/FunctionApp/Functions.cs#L40)。 可在 [此处](https://github.com/Azure/azure-functions-signalrservice-extension/tree/dev/samples)找到用于协商客户端和发送消息的示例。 可在 [此处](https://github.com/Azure/azure-functions-signalrservice-extension)找到详细信息。
* 当你在 Azure 函数中使用 SignalR 客户端时，可能会有更好的体系结构来应对你的方案。 检查是否设计了正确的无服务器体系结构。 可以 [在 Azure Functions 中通过 SignalR 服务绑定来引用实时无服务器应用程序](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService)。

<a name="server_connection_drop"></a>

## <a name="server-connection-drops"></a>服务器连接断开

在后台启动应用服务器时，Azure SDK 开始启动到远程 Azure SignalR 的服务器连接。 如 [Azure SignalR 服务的内部](https://github.com/Azure/azure-signalr/blob/dev/docs/internal.md)所述，azure SignalR 会将传入客户端流量路由到这些服务器连接。 删除服务器连接后，还将关闭它所提供的所有客户端连接。

由于应用服务器与 SignalR 服务之间的连接是永久性连接，因此可能遇到网络连接问题。 在服务器 SDK 中，我们始终将策略 **重新连接** 到服务器连接。 作为最佳做法，我们还鼓励用户使用随机延迟时间向客户端添加连续重新连接逻辑，以避免对服务器的大规模同时发送请求。

Azure SignalR 服务会定期发布新版本，有时会修补或升级 Azure 范围内的操作系统，或者偶尔会从从属服务中断。 这可能会导致服务中断很短，但只要客户端具有断开连接/重新连接机制，此影响就会很小，就像任何客户端导致断开连接的情况一样。

本部分介绍服务器连接丢弃的几个可能，并提供一些有关如何识别根本原因的指导。

### <a name="possible-errors-seen-from-server-side"></a>服务器端出现可能的错误：

* `[Error]Connection "..." to the service was dropped`
* `The remote party closed the WebSocket connection without completing the close handshake`
* `Service timeout. 30.00ms elapsed without receiving a message from service.`

### <a name="root-cause"></a>根本原因：

服务器-服务连接由 **ASRS** **(z** **S** ignal **R** **s** ervice) 关闭。

### <a name="troubleshooting-guide"></a>故障排除指南

1. 打开应用服务器端日志以查看是否发生了异常
2. 检查应用服务器端事件日志以查看应用服务器是否已重新启动
3. 为我们创建一个问题，提供时间范围，并通过电子邮件向我们发送资源名称

## <a name="tips"></a>提示

<a name="view_request"></a>

* 如何查看客户端发出的传出请求？
采用 ASP.NET Core 例如 (ASP.NET 类似) ：
    * 从浏览器：

        以 Chrome 为例，可以使用 **F12** 打开控制台窗口，并切换到 " **网络** " 选项卡。你可能需要使用 **F5** 刷新页面，以从头开始捕获网络。

        :::image type="content" source="./media/signalr-howto-troubleshoot-guide/chrome-network.gif" alt-text="Chrome 视图网络":::

    * 从 c # 客户端：

        可以使用 [Fiddler](https://www.telerik.com/fiddler)查看本地 web 流量。 支持 WebSocket 流量，因为 Fiddler 4.5。

        :::image type="content" source="./media/signalr-howto-troubleshoot-guide/fiddler-view-network-inline.png" alt-text="Fiddler 视图网络" lightbox="./media/signalr-howto-troubleshoot-guide/fiddler-view-network.png":::

<a name="restart_connection"></a>

* 如何重新启动客户端连接？
    
    以下 [示例代码](https://github.com/Azure/azure-signalr/tree/dev/samples) 包含通过 *ALWAYS RETRY* 策略重新启动连接逻辑：

    * [ASP.NET Core c # 客户端](https://github.com/Azure/azure-signalr/tree/dev/samples/ChatSample/ChatSample.CSharpClient/Program.cs#L64)

    * [ASP.NET Core JavaScript 客户端](https://github.com/Azure/azure-signalr/tree/dev/samples/ChatSample/ChatSample/wwwroot/index.html#L164)

    * [ASP.NET c # 客户端](https://github.com/Azure/azure-signalr/tree/dev/samples/AspNet.ChatSample/AspNet.ChatSample.CSharpClient/Program.cs#L78)

    * [ASP.NET JavaScript 客户端](https://github.com/Azure/azure-signalr/tree/dev/samples/AspNet.ChatSample/AspNet.ChatSample.JavaScriptClient/wwwroot/index.html#L71)

## <a name="next-steps"></a>后续步骤

在本指南中，已了解如何处理常见问题。 你还可以了解更常见的故障排除方法。 

> [!div class="nextstepaction"]
> [如何排查连接和消息传递问题](./signalr-howto-troubleshoot-method.md)