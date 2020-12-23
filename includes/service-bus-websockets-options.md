---
title: include 文件
description: include 文件
services: service-bus-messaging
author: clemensv
ms.service: service-bus-messaging
ms.topic: include
ms.date: 11/24/2020
ms.author: clemensv
ms.custom: include file
ms.openlocfilehash: fe205524c5aef6a43771cc09c810da217678d108
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "96022128"
---
AMQP 协议选项通过端口 TCP 443 运行，就像 HTTP/REST API 一样，但在功能上与纯 AMQP 相同。 此选项的初始连接延迟略高，因为共享 HTTPS 端口时要权衡额外的握手往返和开销。 如果选择此模式，TCP 端口 443 足以进行通信。 以下选项允许选择普通 AMQP 或 AMQP WebSockets 模式：

| 语言 | 选项   |
| -------- | ----- |
| .NET     | 具有 [TransportType.Amqp](/dotnet/api/microsoft.azure.servicebus.transporttype?view=azure-dotnet) 或 [TransportType.AmqpWebSockets](/dotnet/api/microsoft.azure.servicebus.transporttype?view=azure-dotnet) 的 [ServiceBusConnection.TransportType](/dotnet/api/microsoft.azure.servicebus.servicebusconnection.transporttype?view=azure-dotnet) 属性 |
| Java     | 具有 [com.microsoft.azure.servicebus.primitives.TransportType.AMQP](/java/api/com.microsoft.azure.servicebus.primitives.transporttype?view=azure-java-stable) 或 [com.microsoft.azure.servicebus.primitives.TransportType.AMQP_WEB_SOCKETS](/java/api/com.microsoft.azure.servicebus.primitives.transporttype?view=azure-java-stable) 的 [com.microsoft.azure.servicebus.ClientSettings](/java/api/com.microsoft.azure.servicebus.clientsettings.clientsettings?view=azure-java-stable) |
| 节点  | [ServiceBusClientOptions](/javascript/api/@azure/service-bus/servicebusclientoptions?view=azure-node-latest) 具有 `webSocket` 构造函数参数。 |
| Python | 具有 [TransportType.Amqp](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-servicebus/latest/azure.servicebus.html#azure.servicebus.TransportType) 或 [TransportType.AmqpOverWebSocket](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-servicebus/latest/azure.servicebus.html#azure.servicebus.TransportType) 的 [ServiceBusClient.transport_type](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-servicebus/latest/azure.servicebus.html#azure.servicebus.ServiceBusClient) |