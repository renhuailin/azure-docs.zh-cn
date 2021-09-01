---
title: 如何使用 Azure SignalR 服务的实时跟踪工具
description: 了解如何使用 Azure SignalR 服务的实时跟踪工具
author: wanlwanl
ms.author: wanl
ms.service: signalr
ms.topic: conceptual
ms.date: 06/30/2021
ms.openlocfilehash: 5a1cf026759a8112901624d9db89d7c344930cb8
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114297421"
---
# <a name="how-to-use-live-trace-tool-for-azure-signalr-service"></a>如何使用 Azure SignalR 服务的实时跟踪工具

实时跟踪工具是一个 Web 应用，用于捕获和显示 Azure SignalR 服务中的实时跟踪。 可以实时收集实时跟踪，无需依赖其他服务。
只需单击一下，即可启用和禁用实时跟踪功能。 还可以选择感兴趣的任何日志类别。

> [!NOTE]
> 请注意，实时跟踪将计为出站消息。

## <a name="launch-the-live-trace-tool"></a>启动实时跟踪工具

1. 转到 Azure 门户。
2. 选中“启用实时跟踪”。
3. 单击工具栏中的“保存”按钮，并等待更改生效。
4. 在 Azure Web PubSub 服务实例的“诊断设置”页上，选择“打开实时跟踪工具”。 

    :::image type="content" source="media/signalr-howto-troubleshoot-live-trace/live-traces-with-live-trace-tool.png" alt-text="启动实时跟踪工具的屏幕截图。":::

## <a name="capture-live-traces"></a>捕获实时跟踪

实时跟踪工具提供一些基本功能，有助于捕获实时跟踪以进行故障排除。

* 捕获：使用实时跟踪工具开始从 Azure Web PubSub 实例捕获实时跟踪。
* 清除：清除捕获的实时跟踪。
* 导出：将实时跟踪导出到文件。 当前支持的文件格式为 CSV 文件。
* 日志筛选器：使用实时跟踪工具，可以通过某个特定的关键字筛选捕获的实时跟踪。 通用分隔符（如空格、逗号、分号等）可视为关键词。 
* 状态：“状态”显示实时跟踪工具与特定实例是已连接还是已断开连接。

:::image type="content" source="./media/signalr-howto-troubleshoot-live-trace/live-trace-tool-capture.png" alt-text="使用实时跟踪工具捕获实时跟踪的屏幕截图。":::

实时跟踪工具捕获的实时跟踪包含用于故障排除的详细信息。 

| 名称 | 说明 |
| ------------ |  ------------------------ | 
| 时间 | 日志事件时间 |
| 日志级别 | 日志事件级别（跟踪/调试/信息/警告/错误） |
| 事件名称 | 事件的操作名称 |
| Message | 日志事件的详细消息 |
| 异常 | Azure Web PubSub 服务的运行时异常 |
| 集线器 | 用户定义的中心名称 |
| 连接 ID | 连接的标识 |
| 连接 ID | 连接的类型。 允许的值为 `Server`（服务器与服务之间的连接），以及客户端 `Client`（客户端和服务之间的连接）|
| 用户 ID | 用户的标识 |
| IP | 客户端 IP 地址 |
| 服务器粘滞 | 客户端的路由模式。 允许的值为 `Disabled`、`Preferred` 和 `Required`。 有关详细信息，请参阅[服务器粘滞模式](https://github.com/Azure/azure-signalr/blob/master/docs/run-asp-net-core.md#serverstickymode) |
| 传输 | 客户端可用来发送 HTTP 请求的传输。 允许的值为 `WebSockets`、`ServerSentEvents` 和 `LongPolling`。 有关详细信息，请参阅 [Http 传输类型](/dotnet/api/microsoft.aspnetcore.http.connections.httptransporttype) |

## <a name="next-steps"></a>后续步骤

在本指南中，你已经了解如何使用实时跟踪工具。 此外，你还可以了解如何处理常见问题：
* 故障排除指南：若要了解如何排查有关实时跟踪的典型问题，请参阅[故障排除指南](./signalr-howto-troubleshoot-guide.md)。
* 故障排除方法：若要通过自我诊断直接找出问题的根本原因或缩小问题范围，请参阅[故障排除方法简介](./signalr-howto-troubleshoot-method.md)。