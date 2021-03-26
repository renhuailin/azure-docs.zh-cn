---
title: Azure IoT 中心 MQTT 5 支持（预览）
description: 了解 IoT 中心的 MQTT 5 支持
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 11/19/2020
ms.author: jlian
ms.openlocfilehash: fb2cc0b81083936a67bcd465e0408b9f4b53996b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "96603206"
---
# <a name="iot-hub-mqtt-5-support-overview-preview"></a>IoT 中心 MQTT 5 支持概述（预览）

版本：2.0 api-version：2020-10-01-preview

本文档阐释了基于 MQTT 5.0 版协议的 IoT 中心数据平面 API。 有关此 API 中的完整定义，请参阅 [API 参考](iot-hub-mqtt-5-reference.md)。

## <a name="prerequisites"></a>先决条件

- 在全新的 IoT 中心[启用预览模式](iot-hub-preview-mode.md)，以便试用 MQTT 5。
- 必须事先了解 [MQTT 5 规范](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html)。

## <a name="level-of-support-and-limitations"></a>支持级别和限制

对 MQTT 5 的 IoT 中心支持处于预览阶段，在以下方面受到限制（通过 `CONNACK` 属性传达给客户端，除非明确说明）：

- 尚无正式的 [Azure IoT 中心设备 SDK](iot-hub-devguide-sdks.md) 支持。
- 不支持订阅标识符。
- 不支持共享订阅。
- 不支持 `RETAIN`。
- `Maximum QoS` 为 `1`。
- `Maximum Packet Size` 为 `256 KiB`（服从于每个操作的进一步限制）。
- 不支持分配的客户端 ID。
- `Keep Alive` 仅限 `19 min`（运行情况检查的最大延迟 - `28.5 min`）。
- `Topic Alias Maximum` 为 `10`。
- `Response Information` 不受支持；即使 `CONNECT` 包含 `Request Response Information` 属性，`CONNACK` 也不会返回 `Response Information` 属性。
- `Receive Maximum`（当符合 `QoS: 1` 时允许的未完成的未确认 `PUBLISH` 数据包的最大数目（在从客户端到服务器的方向））为 `16`。
- 单个客户端的订阅数不能超出 `50` 个。
  达到此限制时，`SUBACK` 会返回订阅的 `0x97`（超出配额）原因代码。

## <a name="connection-lifecycle"></a>连接生命周期

### <a name="connection"></a>连接

若要使用此 API 将客户端连接到 IoT 中心，请按 MQTT 5 规范建立连接。
在成功进行 TLS 握手后，客户端必须在 30 秒内发送 `CONNECT` 数据包，否则服务器会关闭连接。
下面是 `CONNECT` 数据包的示例：

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

- `Authentication Method` 属性是必需的，用于标识所使用的身份验证方法。 有关身份验证方法的详细信息，请参阅[身份验证](#authentication)。
- `Authentication Data` 属性处理取决于 `Authentication Method`。 如果 `Authentication Method` 设置为 `SAS`，则 `Authentication Data` 是必需的，其中必须包含有效的签名。 有关身份验证数据的详细信息，请参阅[身份验证](#authentication)。
- `api-version` 属性是必需的，必须将其设置为此规范的标头中提供的 API 版本值，才能使此规范得以应用。
- `host` 属性定义租户的主机名。 它是必需的，除非在 TLS 握手期间在客户端 Hello 记录中提供了 SNI 扩展
- `sas-at` 定义连接的时间。
- `sas-expiry` 定义提供的 SAS 的过期时间。
- `client-agent` 可以传达与创建连接的客户端相关的信息。

> [!NOTE]
> 在整个规范中，`Authentication Method` 和具有大写名称的其他属性都是 MQTT 5 中的一类属性 - MQTT 5 规范中对其进行了详细介绍。 `api-version` 和短划线形式的其他属性是特定于 IoT 中心 API 的用户属性。

IoT 中心在完成身份验证并获取为连接提供支持所需的数据后，会使用 `CONNACK` 数据包进行响应。 如果成功地建立了连接，则 `CONNACK` 将如下所示：

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

`CONNECT` 客户端上的 `Authentication Method` 属性定义它用于此连接的身份验证类型：

- `SAS` - 共享访问签名在 `CONNECT` 的 `Authentication Data` 属性中提供。
- `X509` - 客户端依赖于客户端证书身份验证。

 如果身份验证方法与客户端在 IoT 中心的已配置方法不匹配，则身份验证会失败。

> [!NOTE]
> 此 API 要求在 `CONNECT` 数据包中设置 `Authentication Method` 属性。 如果未提供 `Authentication Method` 属性，则连接会失败，并出现“`Bad Request`”响应。

不支持以前的 API 版本中使用的用户名/密码身份验证。

#### <a name="sas"></a>SAS

对于基于 SAS 的身份验证，客户端必须提供连接上下文的签名。 这用于证明 MQTT 连接的真实性。 签名必须基于客户端在 IoT 中心的配置中的两个身份验证密钥之一，或基于[共享访问策略](iot-hub-devguide-security.md)的两个共享访问密钥之一。

要签名的字符串必须采用如下格式：

```text
{host name}\n
{Client Id}\n
{sas-policy}\n
{sas-at}\n
{sas-expiry}\n
```

- `host name` 派生自 SNI 扩展（在 TLS 握手期间由客户端在客户端 Hello 记录中提供）或 `CONNECT` 数据包中的 `host` 用户属性。
- `Client Id` 是 `CONNECT` 数据包中的客户端标识符。
- `sas-policy` - 如果存在，则此项会定义用于身份验证的 IoT 中心访问策略。 它编码为 `CONNECT` 数据包上的用户属性。 可选：省略它意味着将改用设备注册表中的身份验证设置。
- `sas-at` - 如果存在，则此项会指定连接时间 - 当前时间。 它编码为 `CONNECT` 数据包上类型为 `time` 的用户属性。
- `sas-expiry` 定义身份验证的过期时间。 它是 `CONNECT` 数据包上 `time` 类型的用户属性。 此属性为必需。

对于可选参数，如果省略，则必须在要签名的字符串中改用空字符串。

HMAC-SHA256 用来基于设备的对称密钥对之一对字符串进行哈希处理。 然后，哈希值将设置为 `Authentication Data` 属性的值。

#### <a name="x509"></a>X509

如果 `Authentication Method` 属性设置为 `X509`，则 IoT 中心会根据提供的客户端证书对连接进行身份验证。

#### <a name="reauthentication"></a>重新身份验证

如果使用基于 SAS 的身份验证，建议使用生存期短的身份验证令牌。 为了让连接保持经身份验证的状态并防止因过期而断开连接，客户端必须通过发送带有 `Reason Code: 0x19`（重新身份验证）的 `AUTH` 数据包来重新进行身份验证：

```yaml
-> AUTH
    Reason_Code: 0x19
    Authentication_Method: SAS
    Authentication_Data: {SAS bytes}
    sas-at: {current time}
    sas-expiry: {SAS expiry time}
```

规则:

- `Authentication Method` 必须与用于初始身份验证的身份验证方法相同
- 如果最初使用基于共享访问策略的 SAS 对连接进行身份验证，则在重新身份验证中使用的签名必须基于同一策略。

如果重新身份验证成功，IoT 中心会发送包含 `Reason Code: 0x00`（成功）的 `AUTH` 数据包。 否则，IoT 中心会发送包含 `Reason Code: 0x87`（未授权）的 `DISCONNECT` 数据包并关闭连接。

### <a name="disconnection"></a>断开连接

服务器可能会断开与客户端的连接，原因如下：

- 客户端行为方式不当，导致无法以否定确认（或响应）的形式对其直接进行响应，
- 服务器无法让连接状态保持最新，
- 具有相同标识的客户端已连接。

服务器可能会断开连接，并提供 MQTT 5.0 规范中定义的任何原因代码。 值得注意的代码：

- `135`（未授权），在重新身份验证失败、当前 SAS 令牌过期或设备的凭据更改时出现。
- `142`（会话已占用），在已打开具有相同客户端标识的新连接时出现。
- `159`（超出连接速率），在超出 IoT 中心的连接速率时出现。  
- `131`（特定于实现的错误），用于此 API 中定义的任何自定义错误。 `status` 和 `reason` 属性将用于传达有关连接断开原因的更多详细信息（有关详细信息，请参阅[响应](#response)）。

## <a name="operations"></a>操作

此 API 中的所有功能都表示为操作。 下面是“发送遥测数据”操作的示例：

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

有关此 API 中的操作的完整规范，请参阅 [API 参考](iot-hub-mqtt-5-reference.md)。

> [!NOTE]
> 此规范中的所有示例都是从客户端的角度展示的。 `->` 符号表示发送数据包的客户端，`<-` 表示接收数据包的客户端。

### <a name="message-topics-and-subscriptions"></a>消息主题和订阅

在此 API 的操作消息中使用的主题以 `$iothub/` 开头。
MQTT 代理语义不适用于这些操作（有关详细信息，请参阅“[以 \$ 开头的主题](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html#_Toc3901246)”）。
不支持以不在此 API 中定义的 `$iothub/` 开头的主题：

- 向未定义的主题发送消息会导致“`Not Found`”响应（有关详细信息，请参阅[响应](#response)，见下）。
- 订阅未定义的主题会导致 `SUBACK` 并出现“`Reason Code: 0x8F`”（主题筛选器无效）。

主题名称和属性名称区分大小写，必须完全匹配。 例如，`$iothub/telemetry/` 不受支持，而 `$iothub/telemetry` 受支持。

> [!NOTE]
> 不支持在订阅中的 `$iothub/..` 下使用通配符。 也就是说，客户端无法订阅 `$iothub/+` 或 `$iothub/#`。 尝试这样做会导致 `SUBACK` 并出现“`Reason Code: 0xA2`”（不支持通配符订阅）。 在包含路径参数的操作的主题名称中，仅支持使用单段通配符 (`+`) 来代替路径参数。

### <a name="interaction-types"></a>交互类型

此 API 中的所有操作都基于以下两种交互类型之一：

- 带有可选确认的消息 (MessageAck)
- 请求-响应 (ReqRep)

操作也因方向而异（取决于交换的初始消息的方向）：

- 客户端到服务器 (c2s)
- 服务器到客户端 (s2c)

例如，“发送遥测数据”是“具有确认的消息”类型的客户端到服务器操作，而“处理直接方法”是“请求-响应”类型的服务器到客户端操作。

#### <a name="message-acknowledgement-interactions"></a>消息-确认交互

“带有可选确认的消息”(MessageAck) 交互表示为 MQTT 中 `PUBLISH` 和 `PUBACK` 数据包的交换。 确认是可选操作，发送方可以选择不发送带 `QoS: 0` 的 `PUBLISH` 数据包来请求该确认。

> [!NOTE]
> 如果因客户端所声明的 `Maximum Packet Size` 而必须截断 `PUBACK` 数据包中的属性，则 IoT 中心会保留尽可能多的用户属性，只要不超出给定的限制即可。 首先列出的用户属性比后面列出的有更高的机会发送；`Reason String` 属性的优先级最低。

##### <a name="example-of-simple-messageack-interaction"></a>简单 MessageAck 交互的示例

消息：

```yaml
PUBLISH
    QoS: 1
    Packet_Id: 34
    Topic: $iothub/{request.path}
    Payload: <any>
```

确认（成功）：

```yaml
PUBACK
    Packet_Id: 34
    Reason_Code: 0
```

#### <a name="request-response-interactions"></a>请求-响应交互

在请求-响应 (ReqRep) 交互中，请求和响应都转换为带 `QoS: 0` 的 `PUBLISH` 数据包。

`Correlation Data` 属性必须在二者中都设置，用于将响应数据包与请求数据包匹配。

此 API 对所有 ReqRep 操作使用单个响应主题，即 `$iothub/responses`。 对于客户端到服务器的操作，不需要订阅/取消订阅本主题 - 服务器假定所有客户端都已进行了订阅。

##### <a name="example-of-simple-reqrep-interaction"></a>简单 ReqRep 交互的示例

请求：

```yaml
PUBLISH
    QoS: 0
    Topic: $iothub/{request.path}
    Correlation_Data: 0x01 0xFA
    Payload: ...
```

响应（成功）：

```yaml
PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: 0x01 0xFA
    Payload: ...
```

ReqRep 交互不支持将带 `QoS: 1` 的 `PUBLISH` 数据包作为请求或响应消息。 发送请求 `PUBLISH` 会导致“`Bad Request`”响应。

`Correlation Data` 属性中支持的最大长度为 16 字节。 如果 `PUBLISH` 数据包上的 `Correlation Data` 属性设置为长度超过 16 字节的值，则 IoT 中心会发送结果为“`Bad Request`”的 `DISCONNECT`，并关闭连接。 此行为仅适用于在此 API 中交换的数据包。

> [!NOTE]
> 关联数据是任意字节的序列，例如，它不一定是 UTF-8 字符串。
>
> ReqRep 使用预定义的主题进行响应；请求 `PUBLISH` 数据包中的“响应主题”属性（如果已由发送方设置）会被忽略。

IoT 中心会自动让客户端订阅所有客户端到服务器的 ReqRep 操作的响应主题。 即使客户端显式取消了响应主题的订阅，IoT 中心也会自动恢复订阅。 对于服务器到客户端的 ReqRep 交互，设备仍需进行订阅。

### <a name="message-properties"></a>消息属性

操作属性（系统定义的或用户定义的）以 MQTT 5 中的数据包属性的形式表示。

用户属性名称区分大小写，必须完全按定义的拼写。 例如，`Trace-ID` 不受支持，而 `trace-id` 受支持。

如果请求的用户属性超出规范，且没有前缀 `@`，则会导致错误。

系统属性编码为一类属性（例如 `Content Type`）或用户属性。 规范提供了支持的系统属性的详尽列表。
所有一类属性都会被忽略，除非规范中显式声明了对它们的支持。

如果允许使用用户定义的属性，则其名称必须遵循 `@{property name}` 格式。 用户定义的属性仅支持有效的 UTF-8 字符串值。 例如，值为 `15` 的 `MyProperty1` 属性必须编码为名称为 `@MyProperty` 且值为 `15` 的用户属性。

如果 IoT 中心无法识别用户属性，系统会将其视为错误，IoT 中心会使用包含 `Reason Code: 0x83`（特定于实现的错误）和 `status: 0100`（错误的请求）的 `PUBACK` 进行响应。 如果未请求确认 (QoS:0)，则会发送回包含相同错误的 `DISCONNECT` 数据包，并终止连接。

除 `string` 外，此 API 还定义下列数据类型：

- `time`：自 `1970-01-01T00:00:00.000Z` 以来经历的毫秒数。 例如，`1600987195320` 表示 `2020-09-24T22:39:55.320Z`。
- `u32`：无符号的 32 位整数。
- `u64`：无符号的 64 位整数。
- `i32`：带符号的 32 位整数。

### <a name="response"></a>响应

交互可能会产生不同的结果：`Success`、`Bad Request`、`Not Found`，等等。
可通过 `status` 用户属性将结果彼此区分开来。 `PUBACK` 数据包中的 `Reason Code`（适用于 MessageAck 交互）在涵义上与 `status` 匹配（如果可能）。

> [!NOTE]
> 如果客户端在 CONNECT 数据包中指定“`Request Problem Information: 0`”，则不会按照 MQTT 5 规范发送 `PUBACK` 数据包上的任何用户属性，包括 `status` 属性。 在这种情况下，客户端仍然可以根据 `Reason Code` 来确定确认是肯定的还是否定的。 

每个交互都有默认设置（即成功）。 它的 `Reason Code` 为 `0`，`status` 属性为“未设置”。 否则：

- 对于 MessageAck 交互，`PUBACK` 会获得除 0x0（成功）外的 `Reason Code`。 可以提供 `status` 属性来进一步阐明结果。
- 对于 ReqRep 交互，响应 `PUBLISH` 会获得 `status` 属性集。
- 由于无法直接使用 `QoS: 0` 来响应 MessageAck 交互，因此将改为发送包含响应信息的 `DISCONNECT` 数据包，然后断开连接。

示例:

错误的请求 (MessageAck)：

```yaml
PUBACK
    Reason_Code: 131
    status: 0100
    reason: Unknown property `test`
```

未授权 (MessageAck)：

```yaml
PUBACK
    Reason_Code: 135
    status: 0101
```

未授权 (ReqRep)：

```yaml
PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: ...
    status: 0101
```

在需要时，IoT 中心会设置以下用户属性：

- `status` - IoT 中心的扩展代码，表示操作的状态。 此代码可用于区分结果。
- `trace-id` - 操作的跟踪 ID；IoT 中心可能会保留与操作有关的其他诊断，这些诊断可用于内部调查。
- `reason` - 可供用户阅读的消息，其提供的详细信息说明了为何操作最终处于 `status` 属性所指示的状态。

> [!NOTE]
> 如果客户端将 CONNECT 数据包中的 `Maximum Packet Size` 属性设置为一个很小的值，则不是所有用户属性都合适并会出现在数据包中。
> 
> `reason` 仅适用于人员，不应在客户端逻辑中使用。 此 API 允许在任何时间点更改消息（没有警告），也允许更改版本。
>
> 如果客户端在 CONNECT 数据包中发送 `RequestProblemInformation: 0`，则用户属性不会按 [MQTT 5 规范](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html#_Toc3901053)包含在确认中。

#### <a name="status-code"></a>状态代码

`status` 属性带有操作的状态代码。 它经过优化，可以提高计算机读取效率。
它包含在字符串中编码为十六进制的双字节无符号整数，例如 `0501`。
代码结构（位图）：

```text
7 6 5 4 3 2 1 0 | 7 6 5 4 3 2 1 0
0 0 0 0 0 R T T | C C C C C C C C
```

第一个字节用于标志：

- 0 位和 1 位表示结果类型：
  - `00` - 成功
  - `01` - 客户端错误
  - `10` - 服务器错误
- 2 位：`1` 表示错误可重试
- 3 到 7 位为保留位，必须设置为 `0`

第二个字节包含不同的实际响应代码。 标志不同的错误代码的第二个字节的值可以相同。 例如，可能会出现 `0001`、`0101`、`0201`、`0301` 错误代码，其涵义各不相同。

例如，`Too Many Requests` 是客户端的可重试错误，其自身的代码为 `1`。 其值为 `0000 0101 0000 0001` 或 `0x0501`。

客户端可以使用类型位来确定操作是否已成功结束。 客户端还可以使用可重试位来确定是否应重试操作。

## <a name="recommendations"></a>建议

### <a name="session-management"></a>会话管理

`CONNACK` 数据包带有 `Session Present` 属性，用于指示服务器是否还原了以前创建的会话。 使用此属性来确定是订阅主题还是跳过订阅操作（因为之前已订阅过）。

若要依赖 `Session Present`，客户端必须跟踪所做的订阅（也就是说，发送了 `SUBSCRIBE` 数据包并收到包含成功原因代码的 `SUBACK`），或者必须确保订阅单个 `SUBSCRIBE`/`SUBACK` 交换中的所有主题。 否则，如果客户端发送两个 `SUBSCRIBE` 数据包，而服务器仅成功处理其中一个数据包，则服务器会在 `CONNACK` 中传达 `Session Present: 1`，同时仅接受客户端的订阅的一部分。

若要防止版本较早的客户端未订阅所有主题这一情况，最好是在客户端行为发生更改（例如，在固件更新过程中发生更改）时无条件地进行订阅。 另外，若要确保不保留过时的订阅（会占用允许的最大订阅数），请将不再使用的订阅显式取消。

### <a name="batching"></a>批处理

不需通过特殊格式来发送一批消息。 为了降低 TLS 和网络中占用大量资源的操作的开销，请将数据包（`PUBLISH`、`PUBACK`、`SUBSCRIBE`，等等）捆绑在一起，然后再将其提交到底层的 TLS/TCP 堆栈。 另外，客户端还可以在“批”中简化主题别名的操作：

- 将完整的主题名称置于连接的第一个 `PUBLISH` 数据包中，然后将主题别名与其关联。
- 为同一主题放置以下数据包，其中包含空的主题名称和主题别名属性。

## <a name="migration"></a>迁移

本部分列出了 API 中相对于[以前的 MQTT API](iot-hub-mqtt-support.md) 的更改。

- 传输协议为 MQTT 5。 以前的为 MQTT 3.1.1。
- SAS 身份验证的上下文信息直接包含在 `CONNECT` 数据包中，而不是与签名一起进行编码。
- “身份验证方法”用于指示所使用的身份验证方法。
- “共享访问签名”放置在“身份验证数据”属性中。 过去使用“以前的密码”字段。
- 操作主题不同：
  - 遥测：`$iothub/telemetry` 代替 `devices/{Client Id}/messages/events`。
  - 命令：`$iothub/commands` 代替 `devices/{Client Id}/messages/devicebound`。
  - 报告的补丁孪生体：`$iothub/twin/patch/reported` 代替 `$iothub/twin/PATCH/properties/reported`。
  - 通知孪生体所需状态已更改：`$iothub/twin/patch/desired` 代替 `$iothub/twin/PATCH/properties/desired`。
- 针对客户端-服务器型请求-响应操作的响应主题的订阅不是必需的。
- 使用用户属性，而不是对主题名称段中的属性进行编码。
- 属性名称的拼写采用“短划线形式”的命名约定，而不是带有特殊前缀的缩写。 用户定义的属性现在改为需要前缀。 例如，`$.mid` 现在为 `message-id`，而 `myProperty1` 则变为 `@myProperty1`。
- “关联数据”属性用于关联请求-响应操作的请求消息和响应消息，代替在主题中编码的 `$rid` 属性。
- `iothub-connection-auth-method` 属性不再标记在遥测事件上。
- 在缺少订阅的情况下，不会从设备中清除 C2D 命令。 它们会保持排队状态，直到设备进行订阅，或者直到它们过期。

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

替代确认（受限）：

```yaml
<- PUBACK
    Packet_Id: 31
    Reason_Code: 151
    status: 0501
```

---

### <a name="send-get-twins-state"></a>发送“获取孪生体的状态”

请求：

```yaml
-> PUBLISH
    QoS: 0
    Topic: $iothub/twin/get
    Correlation_Data: 0x01 0xFA
    Payload: <empty>
```

响应（成功）：

```yaml
<- PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: 0x01 0xFA
    Payload: <twin/desired state>
```

响应（不允许）：

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

响应（成功）：

```yaml
-> PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: 0x0A 0x10
    response-code: 200 # user defined response code
    Payload: <data>
```

> [!NOTE]
> `status` 未设置 - 这是一个成功响应。

“设备不可用”响应：

```yaml
-> PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: 0x0A 0x10
    status: 0603
```

---

### <a name="error-while-using-qos-0-part-1"></a>使用 QoS 0 时出错，第 1 部分

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

### <a name="error-while-using-qos-0-part-2"></a>使用 QoS 0 时出错，第 2 部分

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

- 若要查看 MQTT 5 预览版 API 参考，请参阅 [IoT 中心数据平面 MQTT 5 API 参考](iot-hub-mqtt-5-reference.md)。
- 若要按 C# 示例进行操作，请参阅 [GitHub 示例存储库](https://github.com/Azure-Samples/iot-hub-mqtt-5-preview-samples-csharp)。