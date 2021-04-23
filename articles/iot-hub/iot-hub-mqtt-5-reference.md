---
title: Azure IoT 中心 MQTT 5 API 参考（预览）
description: 了解 IoT 中心的 MQTT 5 API 参考
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: reference
ms.date: 11/19/2020
ms.author: jlian
ms.openlocfilehash: 5f0af7d6bf16a05fad1ca9df5db1729abd088010
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "96603209"
---
# <a name="iot-hub-data-plane-mqtt-5-api-reference"></a>IoT 中心数据平面 MQTT 5 API 参考

本文档定义了 IoT 中心数据平面 API 版本 2.0（api-version：`2020-10-01-preview`）中可用的操作。

## <a name="operations"></a>操作

### <a name="get-twin"></a>Get Twin

获取孪生体状态

#### <a name="request"></a>请求

主题名称：`$iothub/twin/get`

属性：无

有效负载：空

#### <a name="success-response"></a>成功响应

属性：无

有效负载：孪生体

#### <a name="alternative-responses"></a>替代响应

| 状态 | 名称 | 说明 |
| :----- | :--- | :---------- |
| 0100 |  错误的请求 | 操作消息格式不正确，无法处理。 |
| 0101 |  未授权 | 客户端无权执行此操作。 |
| 0102 |  不允许 | 不允许执行操作。 |
| 0501 |  已中止 | 每个 SKU 的请求速率太高 |
| 0502 |  超出配额 | 超过每个当前 SKU 的每日配额 |
| 0601 |  服务器错误 | 内部服务器错误 |
| 0602 |  超时 | 操作超时，无法完成。 |
| 0603 |  服务器忙 | 服务器忙 |

#### <a name="pseudo-code-sample"></a>伪代码示例

```

-> PUBLISH
    QoS: 0
    Topic: $iothub/twin/get
<- PUBLISH
    QoS: 0
    Topic: $iothub/responses

```

### <a name="patch-twin-reported"></a>报告的补丁孪生体

报告的补丁孪生体的状态

#### <a name="request"></a>请求

主题名称：`$iothub/twin/patch/reported`

**属性**：

| 名称 | 类型 | 必需 | 说明 |
| :--- | :--- | :------- | :---------- |
| if-version | u64 | 否 |  |

有效负载：TwinState

#### <a name="success-response"></a>成功响应

**属性**：

| 名称 | 类型 | 必需 | 说明 |
| :--- | :--- | :------- | :---------- |
| 版本 | u64 | 是 | 应用补丁后报告的状态的版本 |

有效负载：空

#### <a name="alternative-responses"></a>替代响应

| 状态 | 名称 | 说明 |
| :----- | :--- | :---------- |
| 0104 |  不满足前提条件 | 不满足前提条件，导致请求被取消 |
| 0100 |  错误的请求 | 操作消息格式不正确，无法处理。 |
| 0101 |  未授权 | 客户端无权执行此操作。 |
| 0102 |  不允许 | 不允许执行操作。 |
| 0501 |  已中止 | 每个 SKU 的请求速率太高 |
| 0502 |  超出配额 | 超过每个当前 SKU 的每日配额 |
| 0601 |  服务器错误 | 内部服务器错误 |
| 0602 |  超时 | 操作超时，无法完成。 |
| 0603 |  服务器忙 | 服务器忙 |

#### <a name="pseudo-code-sample"></a>伪代码示例

```

-> PUBLISH
    QoS: 0
    Topic: $iothub/twin/patch/reported
    [if-version: <u64>]
<- PUBLISH
    QoS: 0
    Topic: $iothub/responses

```

### <a name="receive-commands"></a>接收命令

接收并处理命令

#### <a name="message"></a>消息

主题名称：`$iothub/commands`

**属性**：

| 名称 | 类型 | 必需 | 说明 |
| :--- | :--- | :------- | :---------- |
| sequence-no | u64 | 是 | 消息的序列号 |
| enqueued-time | time | 是 | 消息进入系统时的时间戳 |
| delivery-count | u32 | 是 | 尝试消息传递的次数 |
| creation-time | time | 否 | 创建消息时的时间戳（由发送方提供） |
| message-id | string | 否 | 消息标识（由发送方提供） |
| user-id | string | 否 | 用户标识（由发送方提供） |
| correlation-id | string | 否 | 关联标识（由发送方提供） |
| 内容类型 | string | 否 | 确定有效负载的内容类型 |
| content-encoding | string | 否 | 确定有效负载的内容编码 |

有效负载：任意字节序列

#### <a name="success-acknowledgment"></a>成功确认

指示客户端已接受命令进行处理

属性：无

有效负载：空

#### <a name="alternative-acknowledgments"></a>替代确认

| 原因代码 | 状态 | 名称 | 说明 |
| :---------- | :----- | :--- | :---------- |
| 131 | 0603 | 放弃 | 指示此时不会处理命令，将来应重新传送该命令。 |
| 131 | 0100 | 拒绝 | 指示命令被客户端拒绝，不应再次尝试它。 |

#### <a name="pseudo-code-sample"></a>伪代码示例

```

-> SUBSCRIBE
    - Topic: $iothub/commands
      QoS: 1
<- PUBLISH
    QoS: 1
    Topic: $iothub/commands
    sequence-no: <u64>enqueued-time: <time>delivery-count: <u32>[creation-time: <time>][message-id: <string>][user-id: <string>][correlation-id: <string>][Content Type: <string>][content-encoding: <string>]
    Payload: ...

-> PUBACK

```

### <a name="receive-direct-methods"></a>接收直接方法

接收并处理直接方法调用

#### <a name="request"></a>请求

主题名称：`$iothub/methods/{name}`

属性：无

有效负载：任意字节序列

#### <a name="success-response"></a>成功响应

**属性**：

| 名称 | 类型 | 必需 | 说明 |
| :--- | :--- | :------- | :---------- |
| response-code | u32 | 是 |  |

有效负载：任意字节序列

#### <a name="alternative-responses"></a>替代响应

| 状态 | 名称 | 说明 |
| :----- | :--- | :---------- |
| 06A0 |  不可用 | 指示无法通过此连接访问客户端。 |

#### <a name="pseudo-code-sample"></a>伪代码示例

```

-> SUBSCRIBE
    - Topic: methods/{name}
      QoS: 0
<- SUBACK
<- PUBLISH
    QoS: 0
    Topic: $iothub/methods/{name}
-> PUBLISH
    QoS: 0
    Topic: $iothub/responses

```

### <a name="receive-twin-desired-state-changes"></a>接收孪生体所需状态更改

接收对孪生体所需状态的更新

#### <a name="message"></a>消息

主题名称：`$iothub/twin/patch/desired`

**属性**：

| 名称 | 类型 | 必需 | 说明 |
| :--- | :--- | :------- | :---------- |
| 版本 | u64 | 是 | 与此更新匹配的所需状态的版本 |

有效负载：TwinState

#### <a name="pseudo-code-sample"></a>伪代码示例

```

-> SUBSCRIBE
    - Topic: $iothub/twin/patch/desired
      QoS: 0
<- PUBLISH
    QoS: 0
    Topic: $iothub/twin/patch/desired
    version: <u64>
    Payload: ...

```

### <a name="send-telemetry"></a>发送遥测数据

通过路由配置将消息发布到遥测数据通道（默认情况下为 EventHubs）或其他终结点。

#### <a name="message"></a>消息

主题名称：`$iothub/telemetry`

**属性**：

| 名称 | 类型 | 必需 | 说明 |
| :--- | :--- | :------- | :---------- |
| 内容类型 | string | 否 | 在发布后的消息中转换为 `content-type` 系统属性 |
| content-encoding | string | 否 | 在发布后的消息中转换为 `content-encoding` 系统属性 |
| message-id | string | 否 | 在发布后的消息中转换为 `message-id` 系统属性 |
| user-id | string | 否 | 在发布后的消息中转换为 `user-id` 系统属性 |
| correlation-id | string | 否 | 在发布后的消息中转换为 `correlation-id` 系统属性 |
| creation-time | time | 否 | 在发布后的消息中转换为 `iothub-creation-time-utc` 属性 |

有效负载：任意字节序列

#### <a name="success-acknowledgment"></a>成功确认

已成功将消息发布到遥测数据通道

属性：无

有效负载：空

#### <a name="alternative-acknowledgments"></a>替代确认

| 原因代码 | 状态 | 名称 | 说明 |
| :---------- | :----- | :--- | :---------- |
| 131 | 0100 | 错误的请求 | 操作消息格式不正确，无法处理。 |
| 135 | 0101 | 未授权 | 客户端无权执行此操作。 |
| 131 | 0102 | 不允许 | 不允许执行操作。 |
| 131 | 0601 | 服务器错误 | 内部服务器错误 |
| 151 | 0501 | 已中止 | 每个 SKU 的请求速率太高 |
| 151 | 0502 | 超出配额 | 超过每个当前 SKU 的每日配额 |
| 131 | 0602 | 超时 | 操作超时，无法完成。 |
| 131 | 0603 | 服务器忙 | 服务器忙 |

#### <a name="pseudo-code-sample"></a>伪代码示例

```
-> PUBLISH
    QoS: 1
    Topic: $iothub/telemetry
    [Content Type: <string>]
    [content-encoding: <string>]
    [message-id: <string>]
    [user-id: <string>]
    [correlation-id: <string>]
    [creation-time: <time>]

<- PUBACK

```

## <a name="responses"></a>响应

### <a name="bad-request"></a>错误的请求

操作消息格式不正确，无法处理。

原因代码：`131`

状态：`0100`

**属性**：

| 名称 | 类型 | 必需 | 说明 |
| :--- | :--- | :------- | :---------- |
| reason | string | 否 | 包含有关该消息的无效具体内容的信息 |

有效负载：空

### <a name="conflict"></a>Conflict

操作与另一个正在进行的操作冲突。

原因代码：`131`

状态：`0103`

**属性**：

| 名称 | 类型 | 必需 | 说明 |
| :--- | :--- | :------- | :---------- |
| trace-id | string | 否 | 与错误的其他诊断的关联的跟踪 ID |
| reason | string | 否 | 包含有关该消息的无效具体内容的信息 |

有效负载：空

### <a name="not-allowed"></a>不允许

不允许执行操作。

原因代码：`131`

状态：`0102`

**属性**：

| 名称 | 类型 | 必需 | 说明 |
| :--- | :--- | :------- | :---------- |
| reason | string | 否 | 包含有关该消息的无效具体内容的信息 |

有效负载：空

### <a name="not-authorized"></a>未授权

客户端无权执行此操作。

原因代码：`135`

状态：`0101`

**属性**：

| 名称 | 类型 | 必需 | 说明 |
| :--- | :--- | :------- | :---------- |
| trace-id | string | 否 | 与错误的其他诊断的关联的跟踪 ID |

有效负载：空

### <a name="not-found"></a>未找到

所请求的资源不存在

原因代码：`131`

状态：`0504`

**属性**：

| 名称 | 类型 | 必需 | 说明 |
| :--- | :--- | :------- | :---------- |
| reason | string | 否 | 包含有关该消息的无效具体内容的信息 |

有效负载：空

### <a name="not-modified"></a>未修改

未根据提供的前提条件修改资源。

原因代码：`0`

状态：`0001`

属性：无

有效负载：空

### <a name="precondition-failed"></a>不满足前提条件

不满足前提条件，导致请求被取消

原因代码：`131`

状态：`0104`

属性：无

有效负载：空

### <a name="quota-exceeded"></a>超出配额

超过每个当前 SKU 的每日配额

原因代码：`151`

状态：`0502`

属性：无

有效负载：空

### <a name="resource-exhausted"></a>资源已耗尽

资源没有容量来完成操作

原因代码：`131`

状态：`0503`

**属性**：

| 名称 | 类型 | 必需 | 说明 |
| :--- | :--- | :------- | :---------- |
| reason | string | 否 | 包含有关该消息的无效具体内容的信息 |

有效负载：空

### <a name="server-busy"></a>服务器忙

服务器忙

原因代码：`131`

状态：`0603`

**属性**：

| 名称 | 类型 | 必需 | 说明 |
| :--- | :--- | :------- | :---------- |
| trace-id | string | 否 | 与错误的其他诊断的关联的跟踪 ID |

有效负载：空

### <a name="server-error"></a>服务器错误

内部服务器错误

原因代码：`131`

状态：`0601`

**属性**：

| 名称 | 类型 | 必需 | 说明 |
| :--- | :--- | :------- | :---------- |
| trace-id | string | 否 | 与错误的其他诊断的关联的跟踪 ID |

有效负载：空

### <a name="target-failed"></a>目标失败

目标已响应，但响应无效或格式不正确

原因代码：`131`

状态：`06A2`

**属性**：

| 名称 | 类型 | 必需 | 说明 |
| :--- | :--- | :------- | :---------- |
| reason | string | 否 | 包含有关该消息的无效具体内容的信息 |

有效负载：空

### <a name="target-timeout"></a>目标超时

等待目标完成请求时超时

原因代码：`131`

状态：`06A1`

**属性**：

| 名称 | 类型 | 必需 | 说明 |
| :--- | :--- | :------- | :---------- |
| trace-id | string | 否 | 与错误的其他诊断的关联的跟踪 ID |
| reason | string | 否 | 包含有关该消息的无效具体内容的信息 |

有效负载：空

### <a name="target-unavailable"></a>目标不可用

无法访问目标来完成请求

原因代码：`131`

状态：`06A0`

属性：无

有效负载：空

### <a name="throttled"></a>已中止

每个 SKU 的请求速率太高

原因代码：`151`

状态：`0501`

属性：无

有效负载：空

### <a name="timeout"></a>超时

操作超时，无法完成。

原因代码：`131`

状态：`0602`

**属性**：

| 名称 | 类型 | 必需 | 说明 |
| :--- | :--- | :------- | :---------- |
| trace-id | string | 否 | 与错误的其他诊断的关联的跟踪 ID |

有效负载：空

