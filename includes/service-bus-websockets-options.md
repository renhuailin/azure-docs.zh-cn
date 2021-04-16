---
title: include 文件
description: include 文件
services: service-bus-messaging
author: clemensv
ms.service: service-bus-messaging
ms.topic: include
ms.date: 04/08/2021
ms.author: clemensv
ms.custom: include file
ms.openlocfilehash: 08fccf366321b075542f36b86c9bf22d5d877167
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304405"
---
与 HTTP/REST API 一样，AMQP-over-WebSockets 协议选项通过端口 TCP 443 运行，但在功能上与普通 AMQP 相同。 由于额外的握手往返，此选项的初始连接延迟较高，并且作为共享 HTTPS 端口的折衷方案，此选项的开销略高。 如果选择此模式，TCP 端口 443 足以进行通信。 以下选项允许选择 AMQP WebSockets 模式。 

| 语言 | 选项   |
| -------- | ----- |
| .NET (Azure.Messaging.ServiceBus)    | 使用将 [ServiceBusClientOptions](/dotnet/api/azure.messaging.servicebus.servicebusclientoptions) 用作参数的构造函数创建 [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient.-ctor)。 将 [ServiceBusClientOptions.TransportType](/dotnet/api/azure.messaging.servicebus.servicebusclientoptions.transporttype) 设置为 [ServiceBusTransportType.AmqpWebSockets](/dotnet/api/azure.messaging.servicebus.servicebustransporttype) |
| .NET (Microsoft.Azure.ServiceBus)    | 创建客户端对象时，请使用将 [TransportType](/dotnet/api/microsoft.azure.servicebus.transporttype)、[ServiceBusConnection](/dotnet/api/microsoft.azure.servicebus.servicebusconnection) 或 [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.azure.servicebus.servicebusconnectionstringbuilder) 用作参数的构造函数。 <p>对于采用 `transportType` 作为参数的构造，请将参数设置为 [TransportType.AmqpWebSockets](/dotnet/api/microsoft.azure.servicebus.transporttype)。</p> <p>对于采用 `ServiceBusConnection` 作为参数的构造函数，请将 [ServiceBusConnection.TransportType](/dotnet/api/microsoft.azure.servicebus.servicebusconnection.transporttype) 设置为[TransportType.AmqpWebSockets](/dotnet/api/microsoft.azure.servicebus.transporttype)。</p> <p>如果使用 `ServiceBusConnectionStringBuilder`，则使用可让你选择指定 `transportType` 的构造函数。</p> |
| Java (com.azure.messaging.servicebus)     | 创建客户端时，请将 [ServiceBusClientBuilder.transportType](/java/api/com.azure.messaging.servicebus.servicebusclientbuilder.transporttype) 设置为 [AmqpTransportType.AMQP.AMQP_WEB_SOCKETS](/java/api/com.azure.core.amqp.amqptransporttype) |
| Java (com.microsoft.azure.servicebus)    | 创建客户端时，请将 [com.microsoft.azure.servicebus.ClientSettings](/java/api/com.microsoft.azure.servicebus.clientsettings.clientsettings#com_microsoft_azure_servicebus_ClientSettings_ClientSettings_com_microsoft_azure_servicebus_security_TokenProvider_com_microsoft_azure_servicebus_primitives_RetryPolicy_java_time_Duration_com_microsoft_azure_servicebus_primitives_TransportType_) 中的 `transportType` 设置为 [com.microsoft.azure.servicebus.primitives.TransportType.AMQP_WEB_SOCKETS](/java/api/com.microsoft.azure.servicebus.primitives.transporttype) |
| JavaScript  | 在创建服务总线客户端对象时，请使用 [ServiceBusClientOptions](/javascript/api/@azure/service-bus/servicebusclientoptions) 中的 `webSocketOptions` 属性。 |
| Python | 在创建服务总线客户端时，请将 [ServiceBusClient.transport_type](/python/api/azure-servicebus/azure.servicebus.servicebusclient) 设置为 [TransportType.AmqpOverWebSocket](/python/api/azure-servicebus/azure.servicebus.transporttype) |

