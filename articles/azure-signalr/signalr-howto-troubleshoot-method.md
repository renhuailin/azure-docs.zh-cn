---
title: Azure SignalR 服务故障排除做法
description: 了解如何排查连接性问题和消息传送问题
author: yjin81
ms.service: signalr
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: yajin1
ms.openlocfilehash: a85e61536e04bd670cd49127f906c69727acc8e9
ms.sourcegitcommit: 8942cdce0108372d6fc5819c71f7f3cf2f02dc60
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2021
ms.locfileid: "113134972"
---
# <a name="how-to-troubleshoot-connectivity-and-message-delivery-issues"></a>如何排查连接性问题和消息传送问题

本指南介绍了多种有助于执行自我诊断的方法，用于直接查明根本原因或缩小问题的范围。 自我诊断结果也很有用，可以将其报告给我们进行进一步的调查。

首先，你需要从 Azure 门户中检查 Azure SignalR 服务（也称为 ASRS）配置为哪种 [ServiceMode](./concept-service-mode.md)。

:::image type="content" source="./media/signalr-howto-troubleshoot-method/service-mode.png" alt-text="ServiceMode":::

* 对于 `Default` 模式，请参考[默认模式故障排除](#default_mode_tsg)

* 对于 `Serverless` 模式，请参考[无服务器模式故障排除](#serverless_mode_tsg)

* 对于 `Classic` 模式，请参考[经典模式故障排除](#classic_mode_tsg)

其次，需要捕获服务跟踪以进行故障排除。 若要了解如何捕获跟踪，请参阅[如何捕获服务跟踪](#how-to-capture-service-traces)。

[有关于故障排除的问题或反馈？请告诉我们。](https://aka.ms/asrs/survey/troubleshooting)

## <a name="how-to-capture-service-traces"></a>如何捕获服务跟踪

为了简化故障排除过程，Azure SignalR 服务提供了实时跟踪工具来公开有关连接和消息类别的服务跟踪  。 跟踪包括但不限于已连接/已断开连接事件、已收到消息/已留言事件。 使用实时跟踪工具，可以对实时跟踪执行捕获、查看、排序、筛选和导出操作。 有关更多详细信息，请参阅[如何使用实时跟踪工具](./signalr-howto-troubleshoot-live-trace.md)。

[有关于故障排除的问题或反馈？请告诉我们。](https://aka.ms/asrs/survey/troubleshooting)

<a name="default_mode_tsg"></a>

## <a name="default-mode-troubleshooting"></a>默认模式故障排除

当 ASRS 处于默认模式时，有三个角色：客户端、服务器和服务：

* *客户端*：客户端代表连接到 ASRS 的客户端。 在本指南中，连接客户端和 ASRS 的持久连接称为客户端连接。

* *服务器*：服务器代表为客户端协商提供服务并承载着 SignalR `Hub` 逻辑的服务器。 在本指南中，服务器与 ASRS 之间的持久连接称为服务器连接。

* *服务*：在本指南中，“服务”是 ASRS 的短名称。

有关整个体系结构和工作流的详细介绍，请参阅 [Azure SignalR 服务的内部机制](https://github.com/Azure/azure-signalr/blob/dev/docs/internal.md)。

有多种方式可帮助你缩小问题范围。 

* 如果问题正在发生或者是可重现的，则直接了当的方式是查看正在产生的流量。 

* 如果问题很难重现，则跟踪和日志可能会有所帮助。

### <a name="how-to-view-the-traffic-and-narrow-down-the-issue"></a>如何查看流量并缩小问题范围

捕获正在产生的流量是缩小问题范围的最直接了当的方式。 你可以使用下述选项捕获[网络跟踪](/aspnet/core/signalr/diagnostics#network-traces)：

* [使用 Fiddler 收集网络跟踪](/aspnet/core/signalr/diagnostics#network-traces)

* [使用 tcpdump 收集网络跟踪](/aspnet/core/signalr/diagnostics#collect-a-network-trace-with-tcpdump-macos-and-linux-only)

* [在浏览器中收集网络跟踪](/aspnet/core/signalr/diagnostics#collect-a-network-trace-in-the-browser)

<a name="view_traffic_client"></a>

#### <a name="client-requests"></a>客户端请求

对于 SignalR 持久性连接，它首先 `/negotiate` 到你的托管应用服务器，然后重定向到 Azure SignalR 服务，最后建立到 Azure SignalR 服务的实际持久性连接。 有关详细步骤，请参阅 [Azure SignalR 服务的内部机制](https://github.com/Azure/azure-signalr/blob/dev/docs/internal.md)。

在获取的客户端网络跟踪中，检查哪个请求失败以及返回了什么状态代码和响应，并在[故障排除指南](./signalr-howto-troubleshoot-guide.md)中查找解决方案。

#### <a name="server-requests"></a>服务器请求数

SignalR 服务器维护服务器与服务之间的服务器连接。   当应用服务器启动时，它会启动到 Azure SignalR 服务的 WebSocket 连接。 所有客户端流量都通过 Azure SignalR 服务路由到这些服务器连接，然后被分派到 `Hub`。 当某个服务器连接断开时，路由到此服务器连接的客户端会受影响。 我们的 Azure SignalR SDK 有一个“始终重试”逻辑，它以最多 1 分钟的延迟重新连接服务器连接，以最大程度地降低影响。

服务器连接可能会由于以下原因而断开：网络不稳定、Azure SignalR 服务的定期维护，或者你的托管应用服务器进行更新/维护。 只要客户端存在断开连接/重新连接机制，影响就会很小，就像任何客户端导致的断开连接-重新连接一样。

查看服务器端网络跟踪，以找出服务器连接断开或被服务拒绝的状态代码和错误详细信息，并在[故障排除指南](./signalr-howto-troubleshoot-guide.md)中查找根本原因。

[有关于故障排除的问题或反馈？请告诉我们。](https://aka.ms/asrs/survey/troubleshooting)

### <a name="how-to-add-logs"></a>如何添加日志

日志可用来诊断问题并监视运行状态。

<a name="add_logs_client"></a>

#### <a name="how-to-enable-client-side-log"></a>如何启用客户端日志

客户端日志记录体验与使用自承载 SignalR 时的体验完全相同。

##### <a name="enable-client-side-logging-for-aspnet-core-signalr"></a>为 `ASP.NET Core SignalR` 启用客户端日志记录

* [JavaScript 客户端日志记录](/aspnet/core/signalr/diagnostics#javascript-client-logging)

* [.NET 客户端日志记录](/aspnet/core/signalr/diagnostics#net-client-logging)


##### <a name="enable-client-side-logging-for-aspnet-signalr"></a>为 `ASP.NET SignalR` 启用客户端日志记录

* [.NET 客户端](/aspnet/signalr/overview/testing-and-debugging/enabling-signalr-tracing#enabling-tracing-in-the-net-client-windows-desktop-apps)

* [在 Windows Phone 8 客户端中启用跟踪](/aspnet/signalr/overview/testing-and-debugging/enabling-signalr-tracing#enabling-tracing-in-windows-phone-8-clients)

* [在 JavaScript 客户端中启用跟踪](/aspnet/signalr/overview/testing-and-debugging/enabling-signalr-tracing#enabling-tracing-in-the-javascript-client)

<a name="add_logs_server"></a>

#### <a name="how-to-enable-server-side-log"></a>如何启用服务器端日志

##### <a name="enable-server-side-logging-for-aspnet-core-signalr"></a>为 `ASP.NET Core SignalR` 启用服务器端日志记录

`ASP.NET Core SignalR` 的服务器端日志记录与 `ASP.NET Core` Framework 中提供的基于 `ILogger` 的[日志记录](/aspnet/core/fundamentals/logging/?tabs=aspnetcore2x&preserve-view=true&view=aspnetcore-2.1)集成。 你可以使用 `ConfigureLogging` 来启用服务器端日志记录，示例用法如下：

```cs
.ConfigureLogging((hostingContext, logging) =>
        {
            logging.AddConsole();
            logging.AddDebug();
        })
```

Azure SignalR 的记录器类别始终以 `Microsoft.Azure.SignalR` 开头。 若要从 Azure SignalR 启用详细日志，请在 appsettings.json 文件中将前面的前缀配置为 `Information` 级别，如下所示：

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


##### <a name="enable-server-side-traces-for-aspnet-signalr"></a>为 `ASP.NET SignalR` 启用服务器端跟踪

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

检查是否记录了任何异常的警告/错误日志。 


#### <a name="how-to-enable-logs-inside-azure-signalr-service"></a>如何在 Azure SignalR 服务内启用日志

你还可以为 Azure SignalR 服务[启用诊断日志](./signalr-howto-diagnostic-logs.md)，这些日志提供连接到 Azure SignalR 服务的每个连接的详细信息。

<a name="serverless_mode_tsg"></a>

[有关于故障排除的问题或反馈？请告诉我们。](https://aka.ms/asrs/survey/troubleshooting)

## <a name="serverless-mode-troubleshooting"></a>无服务器模式故障排除

当 ASRS 处于无服务器模式时，只有 ASP.NET Core SignalR 支持 `Serverless` 模式，ASP.NET SignalR 不支持此模式。

若要在 `Serverless` 模式下诊断连接性问题，最直接了当的方式是[查看客户端流量](#view_traffic_client)。 启用[客户端日志](#add_logs_client)和[服务端日志](#add_logs_server)可能也会有所帮助。

<a name="classic_mode_tsg"></a>

[有关于故障排除的问题或反馈？请告诉我们。](https://aka.ms/asrs/survey/troubleshooting)

## <a name="classic-mode-troubleshooting"></a>经典模式故障排除

`Classic` 模式已过时，不建议使用。 在此模式下，Azure SignalR 服务使用已连接的服务器连接来确定当前服务是处于 `default` 模式还是 `serverless` 模式。 这可能会导致出现一些中间客户端连接性问题，因为当所有已连接的服务器连接突然断开（例如，由于网络不稳定而断开）时，Azure SignalR 会认为它现在已切换到 `serverless` 模式，因此在此期间连接的客户端永远不会路由到托管应用服务器。 如果你有托管应用服务器，但某些客户端永远不会到达应用服务器端，请启用[服务端日志](#add_logs_server)并检查是否有任何记录为 `ServerlessModeEntered` 的客户端。 如果有，请[中止这些客户端连接](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md#API)并让客户端重启，这可能会有帮助。

排查 `classic` 模式连接性问题和消息传送问题与[排查默认模式问题](#default_mode_tsg)类似。

[有关于故障排除的问题或反馈？请告诉我们。](https://aka.ms/asrs/survey/troubleshooting)

## <a name="service-health"></a>服务运行状况

你可以检查运行状况 API 来查看服务运行状况。

* 请求：GET `https://{instance_name}.service.signalr.net/api/v1/health`

* 响应状态代码：
  * 200：正常。
  * 503：你的服务不正常。 可以执行以下操作：
    * 等待几分钟以便自动恢复。
    * 检查 IP 地址是否与门户中的 IP 地址相同。
    * 或者重启实例。
    * 如果上述所有选项都不起作用，请通过在 Azure 门户中添加新的支持请求来联系我们。

详细了解[灾难恢复](./signalr-concept-disaster-recovery.md)。

[有关于故障排除的问题或反馈？请告诉我们。](https://aka.ms/asrs/survey/troubleshooting)

## <a name="next-steps"></a>后续步骤

在本指南中，你已了解如何排查连接性问题和消息传送问题。 你还可以了解如何处理常见问题。 

> [!div class="nextstepaction"]
> [故障排除指南](./signalr-howto-troubleshoot-guide.md)