---
title: 'Azure IoT 中心 MQTT 5 支持 (预览) '
description: 了解 IoT 中心的 MQTT 5 支持
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 11/19/2020
ms.author: jlian
ms.openlocfilehash: fb2cc0b81083936a67bcd465e0408b9f4b53996b
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2020
ms.locfileid: "96603206"
---
# <a name="iot-hub-mqtt-5-support-overview-preview"></a>IoT 中心 MQTT 5 支持概述 (预览) 

**版本：** 2.0 **api-版本：** 2020-10-01-预览版

本文档通过 MQTT 版本5.0 协议定义 IoT 中心数据平面 API。 有关此 API 的完整定义，请参阅 [Api 参考](iot-hub-mqtt-5-reference.md) 。

## <a name="prerequisites"></a>先决条件

- 在全新的 IoT 中心[启用预览模式](iot-hub-preview-mode.md)，尝试 MQTT 5。
- 需要事先了解 [MQTT 5 规范](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html) 。

## <a name="level-of-support-and-limitations"></a>支持级别和限制

IoT 中心对 MQTT 5 的支持处于预览阶段，并且受以下限制：通过属性 (传达给客户端， `CONNACK` 除非明确注明) ：

- 尚无官方 [Azure IoT 中心设备 SDK](iot-hub-devguide-sdks.md) 支持。
- 不支持订阅标识符。
- 不支持共享订阅。
- 不支持 `RETAIN`。
- `Maximum QoS` 为 `1`。
- `Maximum Packet Size``256 KiB` (受) 的每个操作的进一步限制。
- 不支持分配的客户端 Id。
- `Keep Alive` 仅限对 `19 min` 活动检查的 (最大延迟– `28.5 min`) 。
- `Topic Alias Maximum` 为 `10`。
- `Response Information` 不受支持; `CONNACK` `Response Information` 即使 `CONNECT` 包含属性，也不返回属性 `Request Response Information` 。
- `Receive Maximum``PUBLISH`) ) 为的客户端-服务器方向 (， (允许的未确认未确认数据包的最大数量 `QoS: 1` `16` 。
- 单个客户端不能有多个 `50` 订阅。
  达到该限制后， `SUBACK` 将返回 `0x97` (配额超出了订阅的) 原因代码。

## <a name="connection-lifecycle"></a>连接生命周期

### <a name="connection"></a>连接

若要使用此 API 将客户端连接到 IoT 中心，请按 MQTT 5 规范建立连接。
`CONNECT`在成功进行 TLS 握手后，客户端必须在30秒内发送数据包，否则服务器将关闭连接。
下面是数据包的示例 `CONNECT` ：

```yaml
-> CONNECT
    Protocol_Version: 5
    Clean_Start: 0
    Client_Id: D1
    Authentication_Method: SAS
    Authentication_Data: {SAS bytes}
    api-version: 2020-10-10
    host: abc.azure-devices.net
    sas-at: 1600987795320
    sas-expiry: 1600987195320
    client-agent: artisan;Linux
```

- `Authentication Method` 属性是必需的，并标识所使用的身份验证方法。 有关身份验证方法的详细信息，请参阅 [身份验证](#authentication)。
- `Authentication Data` 属性处理依赖于 `Authentication Method` 。 如果 `Authentication Method` 设置为 `SAS` ，则 `Authentication Data` 是必需的，并且必须包含有效的签名。 有关身份验证数据的详细信息，请参阅 [身份验证](#authentication)。
- `api-version` 属性是必需的，并且必须设置为此规范标头中提供的 API 版本值才能应用此规范。
- `host` 属性定义租户的主机名。 这是必需的，除非在 TLS 握手期间客户端 Hello 记录中提供了 SNI 扩展
- `sas-at` 定义连接时间。
- `sas-expiry` 定义提供的 SAS 的过期时间。
- `client-agent` （可选）与有关创建连接的客户端的信息通信。

> [!NOTE]
> `Authentication Method` 在整个规范中，具有大写名称的其他属性是 MQTT 5 中的第一类属性-在 MQTT 5 规范的详细信息中进行了介绍。 `api-version` 和虚线大小写形式的其他属性是特定于 IoT 中心 API 的用户属性。

IoT 中心在 `CONNACK` 完成身份验证并获取数据以支持连接后，会在数据包中响应。 如果成功建立连接，如下 `CONNACK` 所示：

```yaml
<- CONNACK
    Session_Present: 1
    Reason_Code: 0x00
    Session_Expiry_Interval: 0xFFFFFFFF # included only if CONNECT specified value less than 0xFFFFFFFF and more than 0x00
    Receive_Maximum: 16
    Maximum_QoS: 1
    Retain_Available: 0
    Maximum_Packet_Size: 262144
    Topic_Alias_Maximum: 10
    Subscription_Identifiers_Available: 0
    Shared_Subscriptions_Available: 0
    Server_Keep_Alive: 1140 # included only if client did not specify Keep Alive or if it specified a bigger value
```

这些 `CONNACK` 数据包属性遵循 [MQTT 5 规范](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html#_Toc3901080)。 它们反映 IoT 中心的功能。

### <a name="authentication"></a>身份验证

`Authentication Method`客户端上的属性 `CONNECT` 定义它用于此连接的身份验证类型：

- `SAS` -在 `CONNECT` 的属性中提供共享访问 `Authentication Data` 签名
- `X509` -客户端依赖于客户端证书身份验证。

 如果身份验证方法与 IoT 中心中的客户端配置的方法不匹配，则身份验证将失败。

> [!NOTE]
> 此 API 需要 `Authentication Method` 在包中设置属性 `CONNECT` 。 如果 `Authentication Method` 未提供属性，则连接将失败，并出现 `Bad Request` 响应。

不支持以前的 API 版本中使用的用户名/密码身份验证。

#### <a name="sas"></a>SAS

对于基于 SAS 的身份验证，客户端必须提供连接上下文的签名。 这证明了 MQTT 连接的真实性。 签名必须基于 IoT 中心客户端配置中的两个身份验证密钥之一，或 [共享访问策略](iot-hub-devguide-security.md)的两个共享访问密钥中的一个。

要签名的字符串的格式必须如下所示：

```text
{host name}\n
{Client Id}\n
{sas-policy}\n
{sas-at}\n
{sas-expiry}\n
```

- `host name` 派生方式为：从源扩展中的客户端在客户端 Hello 记录中由客户端提供 (提供，) 或 `host` 数据包中的用户属性 `CONNECT` 。
- `Client Id` 是数据包中的客户端标识符 `CONNECT` 。
- `sas-policy` -如果存在，则定义用于身份验证的 IoT 中心访问策略。 它被编码为数据包上的用户属性 `CONNECT` 。 可选：省略此参数表示将改用设备注册表中的身份验证设置。
- `sas-at` -如果存在，则指定连接时间-当前时间。 它被编码为 `time` 包上类型的用户属性 `CONNECT` 。
- `sas-expiry` 定义身份验证的过期时间。 它是 `time` 数据包上的类型化用户属性 `CONNECT` 。 此属性为必需。

对于可选参数，如果省略，则必须使用空字符串（而不是字符串）。

HMAC-SHA256 用于基于设备的一个对称密钥对字符串进行哈希处理。 然后，将哈希值设置为 `Authentication Data` 属性的值。

#### <a name="x509"></a>X509

如果 `Authentication Method` 将属性设置为 `X509` ，则 IoT 中心会根据提供的客户端证书对连接进行身份验证。

#### <a name="reauthentication"></a>重新

如果使用基于 SAS 的身份验证，我们建议使用生存期较短的身份验证令牌。 为了保持连接经过身份验证并防止因过期而断开连接，客户端必须通过 `AUTH` `Reason Code: 0x19` (重新身份验证) 发送数据包来重新进行身份验证：

```yaml
-> AUTH
    Reason_Code: 0x19
    Authentication_Method: SAS
    Authentication_Data: {SAS bytes}
    sas-at: {current time}
    sas-expiry: {SAS expiry time}
```

规则:

- `Authentication Method` 必须与用于初始身份验证的帐户相同
- 如果最初使用基于共享访问策略的 SAS 对连接进行身份验证，则在身份验证中使用的签名必须基于相同的策略。

如果身份验证成功，IoT 中心将发送 `AUTH` 数据包并 `Reason Code: 0x00` (成功) 。 否则，IoT 中心将 `DISCONNECT` `Reason Code: 0x87` (未授权的) 发送数据包并关闭连接。

### <a name="disconnection"></a>断开连接

服务器可以断开客户端的连接，原因如下：

- 如果客户端的行为不能通过否定确认 (或响应) 直接响应，则客户端会出现异常情况。
- 服务器无法保持连接状态为 "最新"，
- 具有相同标识的客户端已连接。

服务器可能会与 MQTT 5.0 规范中定义的任何原因代码断开连接。 值得注意的是：

- `135` 当重新验证失败时， (未授权) ，当前 SAS 令牌过期或设备的凭据更改
- `142` 如果已打开具有相同客户端标识的新连接，则 (在) 上创建的会话。
- `159` 当 IoT 中心的连接速率超出时)  (连接速率  
- `131` (特定于实现的错误) 用于此 API 中定义的任何自定义错误。 `status` 和 `reason` 属性将用于传达有关断开连接原因的更多详细信息 (请参阅 [响应](#response) 获取详细信息) 。

## <a name="operations"></a>操作

此 API 中的所有功能都表示为操作。 下面是发送遥测操作的示例：

```yaml
-> PUBLISH
    QoS: 1
    Packet_Id: 3
    Topic: $iothub/telemetry
    Payload: Hello

<- PUBACK
    Packet_Id: 3
    Reason_Code: 0
```

有关此 API 中操作的完整说明，请参阅 [Api 参考](iot-hub-mqtt-5-reference.md)。

> [!NOTE]
> 此规范中的所有示例都是从客户端的角度显示的。 符号 `->` 表示客户端发送数据包， `<-` 接收。

### <a name="message-topics-and-subscriptions"></a>邮件主题和订阅

此 API 中的操作消息中使用的主题以开头 `$iothub/` 。
MQTT broker 语义不适用于这些操作 (参阅 "[主题开头 \$ ](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html#_Toc3901246)为" 了解详细信息) 。
`$iothub/`不支持以此 API 中定义的开头的主题：

- 向未定义主题发送消息会导致 `Not Found` 响应 (请参阅以下详细信息) [响应](#response)
- 订阅未定义的主题导致 `SUBACK` `Reason Code: 0x8F` (主题筛选器无效) 。

主题名称和属性名称区分大小写，并且必须是完全匹配。 例如， `$iothub/telemetry/` 在为时，不支持 `$iothub/telemetry` 。

> [!NOTE]
> 不支持订阅中的通配符 `$iothub/..` 。 即，客户端无法订阅 `$iothub/+` 或 `$iothub/#` 。 如果尝试这样做，将 `SUBACK` 导致 `Reason Code: 0xA2` 不支持 (通配符订阅) 。 仅支持使用单段通配符 (`+`) ，而不支持包含它们的操作的主题名称中的路径参数。

### <a name="interaction-types"></a>交互类型

此 API 中的所有操作都基于以下两种交互类型之一：

- 带有可选确认 (MessageAck) 的消息
- Request-Response (ReqRep) 

操作也因方向 (由 exchange) 中的初始消息方向确定：

- 客户端到服务器 (c2s) 
- 服务器到客户端 (s2c) 

例如，发送遥测是 "具有确认消息" 类型的客户端到服务器操作，而处理直接方法是 Request-Response 类型的服务器到客户端操作。

#### <a name="message-acknowledgement-interactions"></a>消息确认交互

带有可选确认 (MessageAck) 交互的消息表示为 `PUBLISH` MQTT 中的和 `PUBACK` 数据包的交换。 确认是可选的，发送方可以选择不通过发送 `PUBLISH` 数据包来请求 `QoS: 0` 。

> [!NOTE]
> 如果数据包中的属性 `PUBACK` 必须因 `Maximum Packet Size` 客户端声明而被截断，IoT 中心将保留尽可能多的用户属性，因为它可以在给定的限制范围内容纳。 首先列出的用户属性比以后列出的更高机会发送; `Reason String` 属性的优先级最低。

##### <a name="example-of-simple-messageack-interaction"></a>简单 MessageAck 交互的示例

消息：

```yaml
PUBLISH
    QoS: 1
    Packet_Id: 34
    Topic: $iothub/{request.path}
    Payload: <any>
```

确认 (成功) ：

```yaml
PUBACK
    Packet_Id: 34
    Reason_Code: 0
```

#### <a name="request-response-interactions"></a>Request-Response 交互

在 Request-Response (ReqRep) 交互，请求和响应都将转换为中 `PUBLISH` 的数据包 `QoS: 0` 。

`Correlation Data` 必须在中设置属性，并使用将响应数据包与请求数据包匹配。

此 API `$iothub/responses` 对所有 ReqRep 操作使用单个响应主题。 不需要订阅或取消订阅本主题中的客户端到服务器操作-服务器假定所有的客户端都已订阅。

##### <a name="example-of-simple-reqrep-interaction"></a>简单 ReqRep 交互的示例

请求：

```yaml
PUBLISH
    QoS: 0
    Topic: $iothub/{request.path}
    Correlation_Data: 0x01 0xFA
    Payload: ...
```

响应 (成功) ：

```yaml
PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: 0x01 0xFA
    Payload: ...
```

ReqRep 交互不支持将 `PUBLISH` 数据包 `QoS: 1` 作为请求或响应消息。 发送请求会 `PUBLISH` 导致 `Bad Request` 响应。

属性中支持的最大长度 `Correlation Data` 为16字节。 如果 `Correlation Data` 数据包上 `PUBLISH` 的属性设置为长度超过16个字节的值，则 IoT 中心将 `DISCONNECT` 随结果一起发送 `Bad Request` 并关闭连接。 此行为仅适用于在此 API 中交换的数据包。

> [!NOTE]
> 相关数据是任意字节序列，例如，不一定是 UTF-8 字符串。
>
> ReqRep 使用预定义的响应主题;请求数据包 (中的响应主题属性（ `PUBLISH` 如果发送方) 设置）被忽略。

IoT 中心会自动将客户端订阅到所有客户端到服务器的 ReqRep 操作的响应主题。 即使客户端显式取消了响应主题，IoT 中心也会自动恢复以前订阅。 对于服务器到客户端的 ReqRep 交互，设备需要订阅。

### <a name="message-properties"></a>消息属性

操作属性-系统或用户定义的-以 MQTT 5 中的数据包属性的形式表示。

用户属性名称区分大小写，并且必须完全按定义拼写。 例如， `Trace-ID` 在为时，不支持 `trace-id` 。

如果请求超出规范并且没有前缀，则会 `@` 导致错误。

系统属性被编码为第一类属性 (例如 `Content Type`) 或用户属性。 规范提供支持的系统属性的详尽列表。
所有的第一类属性都将被忽略，除非规范中显式声明了对它们的支持。

如果允许使用用户定义的属性，则其名称必须遵循格式 `@{property name}` 。 用户定义的属性仅支持有效的 UTF-8 字符串值。 例如， `MyProperty1` 具有值的属性 `15` 必须以名称和值的用户属性编码 `@MyProperty` `15` 。

如果 IoT 中心不识别用户属性，则会将其视为错误，IoT 中心会 `PUBACK` 使用 `Reason Code: 0x83` (特定于实现的错误) 和 `status: 0100` (错误请求) 来做出响应。 如果未请求确认 (QoS： 0) ，则 `DISCONNECT` 会发送回具有相同错误的数据包，并终止连接。

此 API 除了定义以下数据类型 `string` ：

- `time`：自以来的毫秒数 `1970-01-01T00:00:00.000Z` 。 例如， `1600987195320` 对于 `2020-09-24T22:39:55.320Z` ，
- `u32`：无符号32位整数号，
- `u64`：无符号64位整数号，
- `i32`：已签名32位整数。

### <a name="response"></a>响应

交互可能会产生不同的结果： `Success` 、 `Bad Request` 、 `Not Found` 等。
每个结果都按 `status` 用户属性区分。 `Reason Code` 在 `PUBACK` MessageAck 交互的数据包 (中) 匹配 `status` （如果可能）。

> [!NOTE]
> 如果客户端 `Request Problem Information: 0` 在连接数据包中指定，则将不会在数据包上发送任何用户属性 `PUBACK` 以符合 MQTT 5 规范，包括 `status` 属性。 在这种情况下，客户端仍可依赖于 `Reason Code` 确定确认是否为正数或负数。 

每个交互都有默认 (或成功) 。 它 `Reason Code` 的 `0` 和 `status` 属性为 "未设置"。 否则：

- 对于 MessageAck 交互，将 `PUBACK` 获取 `Reason Code` 不是 0X0 (Success) 。 `status` 可以提供属性以进一步阐明结果。
- 对于 ReqRep 交互，Response `PUBLISH` 获取 `status` 属性集。
- 由于没有办法直接响应 MessageAck 交互 `QoS: 0` ，因此 `DISCONNECT` 将使用响应信息发送数据包，然后断开连接。

例如：

错误的请求 (MessageAck) ：

```yaml
PUBACK
    Reason_Code: 131
    status: 0100
    reason: Unknown property `test`
```

未授权 (MessageAck) ：

```yaml
PUBACK
    Reason_Code: 135
    status: 0101
```

未授权 (ReqRep) ：

```yaml
PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: ...
    status: 0101
```

需要时，IoT 中心会设置以下用户属性：

- `status` -IoT 中心的操作状态的扩展代码。 此代码可用于区分结果。
- `trace-id` –操作的跟踪 ID;IoT 中心可能会保留与可用于内部调查的操作有关的其他诊断。
- `reason` -可读消息，提供有关操作在属性所指示状态中结束的原因的详细信息 `status` 。

> [!NOTE]
> 如果 "客户端设置" `Maximum Packet Size` 属性在 "将数据包连接到非常小的值" 中，则并非所有用户属性都适合并且不会出现在数据包中。
> 
> `reason` 仅适用于人员，不应在客户端逻辑中使用。 此 API 允许在任何时间点更改消息，而无需警告或更改版本。
>
> 如果客户端 `RequestProblemInformation: 0` 在 CONNECT 数据包中发送，则用户属性不会包含在每 [MQTT 5 规范](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html#_Toc3901053)的确认中。

#### <a name="status-code"></a>状态代码

`status` 属性带有操作的状态代码。 它针对计算机读取效率进行了优化。
它包含在字符串中编码为十六进制的双字节无符号整数，例如 `0501` 。
代码结构 (位图) ：

```text
7 6 5 4 3 2 1 0 | 7 6 5 4 3 2 1 0
0 0 0 0 0 R T T | C C C C C C C C
```

第一个字节用于标志：

- 位0和1表示结果类型：
  - `00` -成功
  - `01` -客户端错误
  - `10` -服务器错误
- bit 2： `1` 指示错误是可重试的
- 保留第3到7位，并且必须将其设置为 `0`

第二个字节包含实际不同的响应代码。 具有不同标志的错误代码可以具有相同的第二个字节值。 例如，可以有 `0001` 、 `0101` 、 `0201` ， `0301` 错误代码具有不同的含义。

例如， `Too Many Requests` 是一种客户端，具有自己的代码的可重试错误 `1` 。 其值为 `0000 0101 0000 0001` 或 `0x0501` 。

客户端可以使用类型位来确定操作是否成功结束。 客户端还可以使用可重试位来确定是否有权重试操作。

## <a name="recommendations"></a>建议

### <a name="session-management"></a>会话管理

`CONNACK` "数据包携带" `Session Present` 属性，用于指示服务器是否还原了以前创建的会话。 使用此属性来确定在之前是否订阅了主题或跳过订阅。

若要依赖 `Session Present` ，客户端必须跟踪所做的订阅 (即， `SUBSCRIBE` `SUBACK` 通过成功的原因代码) 发送数据包和接收，或者确保订阅单个 exchange 中的所有主题 `SUBSCRIBE` / `SUBACK` 。 否则，如果客户端发送两个 `SUBSCRIBE` 数据包，并且服务器仅成功处理其中的一个，则服务器将 `Session Present: 1` 在中进行通信， `CONNACK` 而只接受部分客户端订阅。

若要防止较早版本的客户端未订阅所有主题，更好的做法是，在客户端行为发生 (更改时（例如，) 固件更新过程中），更好地订阅。 此外，若要确保不存在过期订阅 (从允许的最大订阅数开始) ，请从不再使用的订阅中显式取消订阅。

### <a name="batching"></a>批处理

没有特殊的格式可用于发送一批消息。 为了降低 TLS 和网络中占用大量资源的操作的开销，请在 `PUBLISH` 将数据包处理 `PUBACK` `SUBSCRIBE` 到底层的 TLS/TCP 堆栈之前，将数据包 () 、、等捆绑在一起。 另外，客户端还可以在 "批处理" 中更轻松地创建主题别名：

- 将完整的主题名称放在连接的第一个 `PUBLISH` 数据包中，并将主题别名与其关联起来，
- 为同一个主题添加以下数据包，其中包含空主题名称和主题别名属性。

## <a name="migration"></a>迁移

本部分列出了 API 中与 [以前的 MQTT api](iot-hub-mqtt-support.md)进行的更改。

- 传输协议为 MQTT 5。 以前-MQTT 3.1.1。
- SAS 身份验证的上下文信息直接包含在数据包中， `CONNECT` 而不是与签名一起进行编码。
- 身份验证方法用于指示使用的身份验证方法。
- 共享访问签名放入身份验证数据属性。 使用以前的密码字段。
- 操作主题不同：
  - 遥测： `$iothub/telemetry` 而不是 `devices/{Client Id}/messages/events`
  - 命令： `$iothub/commands` 而不是 `devices/{Client Id}/messages/devicebound`
  - 报告的修补程序克隆： `$iothub/twin/patch/reported` 而不是 `$iothub/twin/PATCH/properties/reported`
  - 通知已更改的所需状态： `$iothub/twin/patch/desired` 而不是 `$iothub/twin/PATCH/properties/desired` 。
- 不需要对客户端-服务器请求响应操作的订阅。
- 使用用户属性，而不是对主题名称段中的属性进行编码。
- 属性名称采用 "短划线" 命名约定，而不是带有特殊前缀的缩写。 用户定义的属性现在需要前缀。 例如， `$.mid` 现在为 `message-id` ，而 `myProperty1` 变成 `@myProperty1` 。
- 相关数据属性用于关联请求响应操作的请求和响应消息，而不是 `$rid` 主题中编码的属性。
- `iothub-connection-auth-method` 在遥测事件上不再标记属性。
- 不在设备的订阅中清除 C2D 命令。 它们将保持排队状态，直到设备订阅或过期。

## <a name="examples"></a>示例

### <a name="send-telemetry"></a>发送遥测

消息：

```yaml
-> PUBLISH
    QoS: 1
    Packet_Id: 31
    Topic: $iothub/telemetry
    @myProperty1: My String Value # optional
    creation-time: 1600987195320 # optional
    @ No_Rules-ForUser-PROPERTIES: Any UTF-8 string value # optional
    Payload: <data>
```

确认：

```yaml
<- PUBACK
    Packet_Id: 31
    Reason_Code: 0
```

 (限制) 的替代确认：

```yaml
<- PUBACK
    Packet_Id: 31
    Reason_Code: 151
    status: 0501
```

---

### <a name="send-get-twins-state"></a>发送获取克隆的状态

请求：

```yaml
-> PUBLISH
    QoS: 0
    Topic: $iothub/twin/get
    Correlation_Data: 0x01 0xFA
    Payload: <empty>
```

响应 (成功) ：

```yaml
<- PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: 0x01 0xFA
    Payload: <twin/desired state>
```

不允许) 响应 (：

```yaml
<- PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: 0x01 0xFA
    status: 0102
    reason: Operation not allowed for `B2` SKU
    Payload: <empty>
```

---

### <a name="handle-direct-method-call"></a>处理直接方法调用

请求：

```yaml
<- PUBLISH
    QoS: 0
    Topic: $iothub/methods/abc
    Correlation_Data: 0x0A 0x10
    Payload: <data>
```

响应 (成功) ：

```yaml
-> PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: 0x0A 0x10
    response-code: 200 # user defined response code
    Payload: <data>
```

> [!NOTE]
> `status` 未设置-这是一个成功响应。

设备不可用响应：

```yaml
-> PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: 0x0A 0x10
    status: 0603
```

---

### <a name="error-while-using-qos-0-part-1"></a>使用 QoS 0 时出错，第1部分

请求：

```yaml
-> PUBLISH
    QoS: 0
    Topic: $iothub/twin/gett # misspelled topic name - server won't recognize it as Request-Response interaction
    Correlation_Data: 0x0A 0x10
    Payload: <data>
```

响应：

```yaml
<- DISCONNECT
    Reason_Code: 144
    reason: "Unsupported topic: `$iothub/twin/gett`"
```

---

### <a name="error-while-using-qos-0-part-2"></a>使用 QoS 0 时出错，第2部分

请求：

```yaml
-> PUBLISH # missing Correlation Data
    QoS: 0
    Topic: $iothub/twin/get
    Payload: <data>
```

响应：

```yaml
<- DISCONNECT
    Reason_Code: 131
    status: 0100
    reason: "`Correlation Data` property is missing"
```
## <a name="next-steps"></a>后续步骤

- 若要查看 MQTT 5 预览 API 参考，请参阅 [IoT 中心数据平面 MQTT 5 API 参考](iot-hub-mqtt-5-reference.md)。
- 若要遵循 c # 示例，请参阅 [GitHub 示例存储库](https://github.com/Azure-Samples/iot-hub-mqtt-5-preview-samples-csharp)。