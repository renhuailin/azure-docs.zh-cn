---
title: 将旧版 WindowsAzure.ServiceBus .NET Framework 库与 AMQP 1.0 配合使用 | Microsoft Docs
description: 本文介绍如何将旧版 WindowsAzure.ServiceBus .NET Framework 库与 AMQP（高级消息队列协议）配合使用。
ms.topic: article
ms.date: 04/30/2021
ms.openlocfilehash: 160da6a770e58e9a76e966f018d25dc9bc8a8c76
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2021
ms.locfileid: "108770058"
---
# <a name="use-legacy-windowsazureservicebus-net-framework-library-with-amqp-10"></a>将旧版 WindowsAzure.ServiceBus .NET Framework 库与 AMQP 1.0 配合使用

> [!NOTE]
> 本文面向 WindowsAzure.ServiceBus 包的现有用户，他们希望在此包中改用 AMQP。 尽管此包仍会接收关键的 bug 修复，但我们强烈建议升级到新的 [Azure.Messaging.ServiceBus](https://www.nuget.org/packages/Azure.Messaging.ServiceBus) 包，该包已从 2020 年 11 月开始发布，并且默认支持 AMQP。

默认情况下，WindowsAzure.ServiceBus 包使用名为“服务总线消息协议”(SBMP) 的基于 SOAP 的专用协议来与服务总线服务通信。 版本 2.1 中添加了对 AMQP 1.0 的支持，我们建议使用此协议，而不要使用默认协议。

若要使用 AMQP 1.0 而不是默认协议，需要对服务总线连接字符串进行显式配置，或者通过 [TransportType](/dotnet/api/microsoft.servicebus.messaging.transporttype) 选项在客户端构造函数中进行配置。 除了此更改之外，在使用 AMQP 1.0 时应用程序代码基本保持不变。

使用 AMQP 时，有几项 API 功能不受支持。 这些不受支持的功能在[行为差异](#behavioral-differences)部分列出。 在使用 AMQP 时，一些高级配置设置还具有不同的含义。

## <a name="configure-connection-string-to-use-amqp-10"></a>配置连接字符串以使用 AMQP 1.0

将连接字符串追加到 `;TransportType=Amqp`，指示客户端使用 AMQP 1.0 来与服务总线建立连接。
例如， 

`Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp`

其中 `namespace` 和 `SAS key` 是在创建服务总线命名空间时从 [Azure 门户][Azure portal]获取的。 有关详细信息，请参阅[使用 Azure 门户创建服务总线命名空间][Create a Service Bus namespace using the Azure portal]。

### <a name="amqp-over-websockets"></a>基于 WebSockets 的 AMQP
若要通过 WebSocket 使用 AMQP，请在连接字符串中将 `TransportType` 设置为 `AmqpWebSockets`。 例如：`Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=AmqpWebSockets`。 

## <a name="message-serialization"></a>消息序列化

使用默认协议时，.NET 客户端库的默认序列化行为是使用 [DataContractSerializer][DataContractSerializer] 类型序列化 [BrokeredMessage][BrokeredMessage] 实例，以便在客户端库和服务总线服务之间传输。 使用 AMQP 传输模式时，客户端库使用 AMQP 类型系统将[中转消息][BrokeredMessage]序列化为 AMQP 消息。 此序列化使得消息能够由可能在不同平台上运行的接收应用程序接收和解释，例如，使用 JMS API 来访问服务总线的 Java 应用程序。

构造 [BrokeredMessage][BrokeredMessage] 实例时，可以提供 .NET 对象作为构造函数的参数以充当消息的正文。 对于可映射到 AMQP 基元类型的对象，正文序列化为 AMQP 数据类型。 如果该对象不能直接映射到 AMQP 基元类型（即，应用程序定义的自定义类型），则使用 [DataContractSerializer][DataContractSerializer]序列化对象，并且已序列化的字节将在 AMQP 数据消息中发送。

为了便于使用非 .NET 客户端进行互操作，仅在消息的正文中使用可直接序列化为 AMQP 类型的 .NET 类型。 下表详细介绍了这些类型以及到 AMQP 类型系统的相应映射。

| .NET 正文对象类型 | 映射的 AMQP 类型 | AMQP 正文部分类型 |
| --- | --- | --- |
| bool |boolean |AMQP 值 |
| 字节 |ubyte |AMQP 值 |
| ushort |ushort |AMQP 值 |
| uint |uint |AMQP 值 |
| ulong |ulong |AMQP 值 |
| sbyte |字节 |AMQP 值 |
| short |short |AMQP 值 |
| int |int |AMQP 值 |
| long |long |AMQP 值 |
| float |float |AMQP 值 |
| Double |Double |AMQP 值 |
| decimal |decimal128 |AMQP 值 |
| char |char |AMQP 值 |
| DateTime |timestamp |AMQP 值 |
| Guid |uuid |AMQP 值 |
| byte[] |binary |AMQP 值 |
| string |string |AMQP 值 |
| System.Collections.IList |list |AMQP 值：集合中包含的项只能是此表中定义的类型。 |
| System.Array |数组 |AMQP 值：集合中包含的项只能是此表中定义的类型。 |
| System.Collections.IDictionary |map |AMQP 值：集合中包含的项只能是此表中所定义的类型。注意：仅支持字符串键。 |
| Uri |描述型 string（请参阅下表） |AMQP 值 |
| DateTimeOffset |描述型 long（请参阅下表） |AMQP 值 |
| TimeSpan |描述型 long（请参阅下文） |AMQP 值 |
| Stream |binary |AMQP 数据（可能有多个）。 数据部分包含从流对象读取的原始字节。 |
| 其他对象 |binary |AMQP 数据（可能有多个）。 包含使用 DataContractSerializer 或应用程序提供的序列化程序的对象的已序列化二进制值。 |

| .NET 类型 | 映射的 AMQP 描述类型 | 注释 |
| --- | --- | --- |
| Uri |`<type name=”uri” class=restricted source=”string”> <descriptor name=”com.microsoft:uri” /></type>` |Uri.AbsoluteUri |
| DateTimeOffset |`<type name=”datetime-offset” class=restricted source=”long”> <descriptor name=”com.microsoft:datetime-offset” /></type>` |DateTimeOffset.UtcTicks |
| TimeSpan |`<type name=”timespan” class=restricted source=”long”> <descriptor name=”com.microsoft:timespan” /></type>` |TimeSpan.Ticks |

## <a name="behavioral-differences"></a>行为差异

与默认协议相比，使用 AMQP 时，WindowsAzure.ServiceBus API 的行为会出现一些细微的差别：

* 将忽略 [OperationTimeout][OperationTimeout] 属性。
* `MessageReceiver.Receive(TimeSpan.Zero)` 是以 `MessageReceiver.Receive(TimeSpan.FromSeconds(10))` 的形式实现的。
* 通过锁定令牌完成消息只能由最初收到消息的消息接收方完成。

## <a name="control-amqp-protocol-settings"></a>控制 AMQP 协议设置

[.NET API](/dotnet/api/) 公开了几项设置以控制 AMQP 协议的行为：

* [MessageReceiver.PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagereceiver.prefetchcount#Microsoft_ServiceBus_Messaging_MessageReceiver_PrefetchCount)：控制应用于链接的初始额度。 默认值为 0。
* [MessagingFactorySettings.AmqpTransportSettings.MaxFrameSize](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.maxframesize#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_MaxFrameSize)：控制在打开连接时协商期间提供的最大 AMQP 帧大小。 默认值为 65,536 字节。
* [MessagingFactorySettings.AmqpTransportSettings.BatchFlushInterval](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.batchflushinterval#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_BatchFlushInterval)：如果可批量传输，则此值确定发送处置的最大延迟。 默认情况下由发送方/接收方继承。 单个发送方/接收方可以覆盖默认值（即 20 毫秒）。
* [MessagingFactorySettings.AmqpTransportSettings.UseSslStreamSecurity](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.usesslstreamsecurity#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_UseSslStreamSecurity)：控制是否通过 TLS 连接建立 AMQP 连接。 默认值为 **true**。

## <a name="next-steps"></a>后续步骤

准备好了解详细信息？ 请访问以下链接：

* [服务总线 AMQP 概述]
* [AMQP 1.0 协议指南]

[Create a Service Bus namespace using the Azure portal]: service-bus-create-namespace-portal.md
[DataContractSerializer]: /dotnet/api/system.runtime.serialization.datacontractserializer
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Microsoft.ServiceBus.Messaging.MessagingFactory.AcceptMessageSession]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory.acceptmessagesession#Microsoft_ServiceBus_Messaging_MessagingFactory_AcceptMessageSession
[OperationTimeout]: /dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout#Microsoft_ServiceBus_Messaging_MessagingFactorySettings_OperationTimeout
[Azure portal]: https://portal.azure.com
[服务总线 AMQP 概述]: service-bus-amqp-overview.md
[AMQP 1.0 协议指南]: service-bus-amqp-protocol-guide.md
