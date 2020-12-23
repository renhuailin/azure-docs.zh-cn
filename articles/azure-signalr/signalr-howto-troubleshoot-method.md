---
title: Azure SignalR 服务故障排除实践
description: 了解如何排查连接和消息传递问题
author: YanJin
ms.service: signalr
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: yajin1
ms.openlocfilehash: 413bb88deac96c1ca12e8a9d25fc9cd16edf4616
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183951"
---
# <a name="how-to-troubleshoot-connectivity-and-message-delivery-issues"></a>如何排查连接和消息传递问题

本指南介绍了多种方法来帮助执行自我诊断，以直接查明根本原因或缩小问题的范围。 当向我们报告以进行进一步调查时，自我诊断结果也很有用。

首先，需要从 Azure 门户中检查哪些 [ServiceMode](./concept-service-mode.md) 是 Azure SignalR 服务 (也称为 **ASRS**) 配置为。

:::image type="content" source="./media/signalr-howto-troubleshoot-method/service-mode.png" alt-text="ServiceMode":::

* 对于 `Default` 模式，请参阅 [默认模式故障排除](#default_mode_tsg)

* 对于 `Serverless` 模式，请参阅 [无服务器模式故障排除](#serverless_mode_tsg)

* 对于 `Classic` 模式，请参阅 [经典模式故障排除](#classic_mode_tsg)

<a name="default_mode_tsg"></a>

## <a name="default-mode-troubleshooting"></a>默认模式故障排除

当 **ASRS** 处于 *默认* 模式时，有 **三个** 角色： *客户端*、 *服务器* 和 *服务*：

* *客户* 端： *客户端* 代表连接到 **ASRS** 的客户端。 在本指南中，连接客户端和 **ASRS** 的持久连接称为 *客户端连接* 。

* *服务器*： *服务器* 代表为客户端协商提供服务并托管 SignalR 逻辑的服务器 `Hub` 。 在本指南中， *服务器* 和 **ASRS** 之间的持续连接称为 *服务器连接* 。

* *Service*： *服务* 是本指南中 **ASRS** 的短名称。

有关整个体系结构和工作流的详细介绍，请参阅 [Azure SignalR 服务的内部机制](https://github.com/Azure/azure-signalr/blob/dev/docs/internal.md) 。

可以通过多种方式来缩小问题范围。 

* 如果该问题是直接发生的，或者是可重现的，则直接的方法是查看持续的流量。 

* 如果问题很难重现，跟踪和日志可能会有所帮助。

### <a name="how-to-view-the-traffic-and-narrow-down-the-issue"></a>如何查看流量并缩小问题范围

捕获日常流量是缩小问题范围的最直接的方法。 你可以使用以下所述的选项捕获 [网络跟踪](/aspnet/core/signalr/diagnostics#network-traces) ：

* [使用 Fiddler 收集网络跟踪](/aspnet/core/signalr/diagnostics#network-traces)

* [使用 tcpdump 收集网络跟踪](/aspnet/core/signalr/diagnostics#collect-a-network-trace-with-tcpdump-macos-and-linux-only)

* [在浏览器中收集网络跟踪](/aspnet/core/signalr/diagnostics#collect-a-network-trace-in-the-browser)

<a name="view_traffic_client"></a>

#### <a name="client-requests"></a>客户端请求

对于 SignalR 持续性连接，首先连接 `/negotiate` 到托管应用服务器，然后重定向到 Azure SignalR 服务，并建立与 Azure SignalR 服务的实际持续连接。 有关详细步骤，请参阅 [Azure SignalR 服务的内部机制](https://github.com/Azure/azure-signalr/blob/dev/docs/internal.md) 。

在客户端网络跟踪中，检查哪个请求失败，其中包含什么状态代码和响应，并在 [故障排除指南](./signalr-howto-troubleshoot-guide.md)中查找解决方案。

#### <a name="server-requests"></a>服务器请求数

SignalR *服务器* 在 *服务器* 和 *服务* 之间维护 *服务器连接*。 当应用服务器启动时，它将启动到 Azure SignalR 服务的 **WebSocket** 连接。 所有客户端流量都通过 Azure SignalR 服务路由到这些 *服务器连接*，然后调度到 `Hub` 。 *服务器连接* 断开时，路由到此 *服务器连接* 的客户端将受到影响。 我们的 Azure SignalR SDK 有一个逻辑 "始终重试"，以最多1分钟的延迟重新连接 *服务器连接* ，以最大程度地降低影响。

由于 Azure SignalR 服务的网络不稳定或定期维护，或者托管的应用服务器更新/维护，*服务器连接* 可能会下降。 只要客户端具有断开连接/重新连接机制，影响就会很小，就像任何客户端导致断开连接。

查看服务器端网络跟踪，查看状态代码和错误详细信息 *服务器连接* 丢弃或被 *服务* 拒绝的原因，并查找 [故障排除指南](./signalr-howto-troubleshoot-guide.md)中的根本原因。


### <a name="how-to-add-logs"></a>如何添加日志

日志可用于诊断问题并监视运行状态。

<a name="add_logs_client"></a>

#### <a name="how-to-enable-client-side-log"></a>如何启用客户端日志

使用自承载 SignalR 时，客户端日志记录体验完全相同。

##### <a name="enable-client-side-logging-for-aspnet-core-signalr"></a>为启用客户端日志记录 `ASP.NET Core SignalR`

* [JavaScript 客户端日志记录](/aspnet/core/signalr/diagnostics#javascript-client-logging)

* [.NET 客户端日志记录](/aspnet/core/signalr/diagnostics#net-client-logging)


##### <a name="enable-client-side-logging-for-aspnet-signalr"></a>为启用客户端日志记录 `ASP.NET SignalR`

* [.NET 客户端](/aspnet/signalr/overview/testing-and-debugging/enabling-signalr-tracing#enabling-tracing-in-the-net-client-windows-desktop-apps)

* [在 Windows Phone 8 个客户端中启用跟踪](/aspnet/signalr/overview/testing-and-debugging/enabling-signalr-tracing#enabling-tracing-in-windows-phone-8-clients)

* [在 JavaScript 客户端中启用跟踪](/aspnet/signalr/overview/testing-and-debugging/enabling-signalr-tracing#enabling-tracing-in-the-javascript-client)

<a name="add_logs_server"></a>

#### <a name="how-to-enable-server-side-log"></a>如何启用服务器端日志

##### <a name="enable-server-side-logging-for-aspnet-core-signalr"></a>启用服务器端日志记录 `ASP.NET Core SignalR`

用于 `ASP.NET Core SignalR` 与 `ILogger` 框架中提供的基于的 [日志记录](/aspnet/core/fundamentals/logging/?tabs=aspnetcore2x&view=aspnetcore-2.1) 集成的服务器端日志记录 `ASP.NET Core` 。 你可以使用启用服务器端日志记录 `ConfigureLogging` ，例如以下示例：

```cs
.ConfigureLogging((hostingContext, logging) =>
        {
            logging.AddConsole();
            logging.AddDebug();
        })
```

Azure SignalR 的记录器类别始终以开头 `Microsoft.Azure.SignalR` 。 若要从 Azure SignalR 启用详细日志，请在如下所示的 `Information` 文件 **appsettings.js** 中将前面的前缀配置为 level：

```JSON
{
    "Logging": {
        "LogLevel": {
            ...
            "Microsoft.Azure.SignalR": "Information",
            ...
        }
    }
}
```

检查是否记录了任何异常的警告/错误日志。 


##### <a name="enable-server-side-traces-for-aspnet-signalr"></a>启用服务器端跟踪 `ASP.NET SignalR`

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

检查是否记录了任何异常的警告/错误日志。 


#### <a name="how-to-enable-logs-inside-azure-signalr-service"></a>如何在 Azure SignalR service 内启用日志

还可为 Azure SignalR 服务 [启用诊断日志](./signalr-howto-diagnostic-logs.md) ，这些日志提供连接到 azure SignalR 服务的每个连接的详细信息。

<a name="serverless_mode_tsg"></a>

## <a name="serverless-mode-troubleshooting"></a>无服务器模式故障排除

当 **ASRS** 处于 *无服务器* 模式时，仅 **ASP.NET Core SignalR** 支持 `Serverless` 模式，而 **ASP.NET SignalR** 不支持此模式。 **NOT**

若要在模式下诊断连接问题 `Serverless` ，最直接的方法是 [查看客户端流量](#view_traffic_client)。 启用 [客户端日志](#add_logs_client) 和 [服务端日志](#add_logs_server) 也会有所帮助。

<a name="classic_mode_tsg"></a>

## <a name="classic-mode-troubleshooting"></a>经典模式故障排除

`Classic` 模式已过时，不鼓励使用。 在此模式下，Azure SignalR 服务使用连接的 *服务器连接* 来确定当前服务是否处于 `default` 模式或 `serverless` 模式。 这可能会导致一些中间客户端连接问题，因为当突然中断所有连接的 *服务器连接* 时（例如由于网络不稳定），Azure SignalR 相信它现在已切换到 `serverless` 模式，并且在此期间连接的客户端永远不会路由到托管应用服务器。 启用 [服务端日志](#add_logs_server) ，并检查是否有任何客户端记录为 `ServerlessModeEntered` ，但某些客户端从不会到达应用服务器端。 如果有，请 [中止这些客户端连接](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md#API) ，并让客户端重新启动。

排查 `classic` 模式连接和消息传递问题类似于 [排除默认模式问题](#default_mode_tsg)。

## <a name="service-health"></a>服务运行状况

可以在运行状况 api 中检查服务运行状况。

* 请求： GET `https://{instance_name}.service.signalr.net/api/v1/health`

* 响应状态代码：
  * 200：正常。
  * 503：你的服务不正常。 你可以：
    * 等待几分钟以便自动完成恢复。
    * 检查 ip 地址是否与门户中的 ip 地址相同。
    * 或重新启动实例。
    * 如果上述所有选项都不起作用，请通过在 Azure 门户中添加新的支持请求来联系我们。

有关 [灾难恢复](./signalr-concept-disaster-recovery.md)的详细信息。

## <a name="next-steps"></a>后续步骤

本指南介绍了如何排查连接和消息传递问题。 你还可以了解如何处理常见问题。 

> [!div class="nextstepaction"]
> [故障排除指南](./signalr-howto-troubleshoot-guide.md)