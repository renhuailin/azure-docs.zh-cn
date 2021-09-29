---
title: 通信服务日志
titleSuffix: An Azure Communication Services concept document
description: 了解 Azure 通信服务中的记录
author: probableprime
manager: chpalm
services: azure-communication-services
ms.author: rifox
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: data
ms.openlocfilehash: 8c0b1f403508ddc8f704518b2caa784b9c8894de
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128671975"
---
# <a name="communication-services-logs"></a>通信服务日志

Azure 通信服务提供的日志记录功能可用于监视和调试通信服务解决方案。 可通过 Azure 门户配置这些功能。

## <a name="enable-diagnostic-logs-in-your-resource"></a>在资源中启用诊断日志

创建资源时，默认情况下会关闭日志记录功能。 若要启用日志记录功能，请在“监视”部分下的“资源”菜单中，导航到“诊断设置”边栏选项卡 。 然后，单击“添加诊断设置”。

接下来，选择所需的存档目标。 目前，我们支持将存储帐户和 Log Analytics 作为存档目标。 选择想要捕获的日志类型后，保存诊断设置。
 
新设置在大约 10 分钟后生效。 日志将开始显示在通信服务资源的“日志”窗格内配置的存档目标中。

:::image type="content" source="./media/diagnostic-settings.png" alt-text="ACS 诊断设置选项。":::

有关如何配置诊断的详细信息，请参阅 [Azure 资源日志](../../azure-monitor/essentials/platform-logs-overview.md)概述。

## <a name="resource-log-categories"></a>资源日志类别

通信服务提供了三种可供启用的日志类型：

* **使用情况日志** - 提供与每个计费服务产品相关联的使用情况数据
* **聊天操作日志** - 提供与聊天服务相关的基本信息
* **短信操作日志** - 提供与短信服务相关的基本信息
* **身份验证操作日志** - 提供与身份验证服务相关的基本信息

### <a name="usage-logs-schema"></a>使用情况日志架构

| 属性 | 说明 |
| -------- | ---------------|
| 时间戳 | 生成日志的时间戳 (UTC)。 |
| 操作名称 | 与日志记录相关联的操作。 |
| 操作版本 | 如果使用 API 执行 operationName，则 `api-version` 与该操作关联。 如果没有与此操作相对应的 API，则该版本表示该操作的版本，以防与操作相关联的属性在将来发生更改。 |
| 类别 | 事件的日志类别。 类别是可以在特定资源上启用或禁用日志的粒度。 在事件的属性 blob 内显示的属性在特定日志类别和资源类型中相同。 |
| 相关性 ID | 关联事件的 ID。 可用于标识多个表之间的关联事件。 |
| 属性 | 适用于各种通信服务模式的其他数据。 |
| 记录 ID | 给定使用情况记录的唯一 ID。 |
| 使用情况类型 | 使用情况模式。 （例如 聊天、PSTN、NAT 等） |
| unit 类型 | 对于给定的使用情况模式，使用情况所基于的单位类型。 （例如分钟、兆字节、消息等）。 |
| 数量 | 此记录使用或消耗的单位数。 |

### <a name="chat-operational-logs"></a>聊天操作日志

| 属性 | 说明 |
| -------- | ---------------|
| TimeGenerated | 生成日志的时间戳 (UTC)。 |
| OperationName | 与日志记录相关联的操作。 |
| CorrelationID | 关联事件的 ID。 可用于标识多个表之间的关联事件。 |
| OperationVersion | 如果使用 API 执行 operationName，则 api-version 与该操作关联。 如果没有与此操作相对应的 API，则该版本表示该操作的版本，以防与操作相关联的属性在将来发生更改。 |
| 类别 | 事件的日志类别。 类别是可以在特定资源上启用或禁用日志的粒度。 在事件的属性 blob 内显示的属性在特定日志类别和资源类型中相同。 |
| ResultType | 操作的状态。 |
| ResultSignature | 操作的子状态。 如果该操作对应于 REST API 调用，则此字段为相应 REST 调用的 HTTP 状态代码。 |
| ResultDescription | 此操作的静态文本说明。 |
| DurationMs | 操作持续时间，以毫秒为单位。 |
| CallerIpAddress | 调用方 IP 地址，前提是该操作对应于来自某个具有公开可用 IP 地址的实体的 API 调用。 |
| Level | 事件的严重级别。 |
| URI | 请求的 URI。 |
| UserId | 请求发送方的用户 ID。 |
| ChatThreadId | 与请求关联的聊天线程 ID。 |
| ChatMessageId | 与请求关联的聊天消息 ID。 |
| SdkType | 请求中使用的 SDK 类型。 |
| PlatformType | 请求中使用的平台类型。 |

### <a name="sms-operational-logs"></a>短信操作日志

| 属性 | 说明 |
| -------- | ---------------|
| TimeGenerated | 生成日志的时间戳 (UTC)。 |
| OperationName | 与日志记录相关联的操作。 |
| CorrelationID | 关联事件的 ID。 可用于标识多个表之间的关联事件。 |
| OperationVersion | 如果使用 API 执行 operationName，则 api-version 与该操作关联。 如果没有与此操作相对应的 API，则该版本表示该操作的版本，以防与操作相关联的属性在将来发生更改。 |
| 类别 | 事件的日志类别。 类别是可以在特定资源上启用或禁用日志的粒度。 在事件的属性 blob 内显示的属性在特定日志类别和资源类型中相同。 |
| ResultType | 操作的状态。 |
| ResultSignature | 操作的子状态。 如果该操作对应于 REST API 调用，则此字段为相应 REST 调用的 HTTP 状态代码。 |
| ResultDescription | 此操作的静态文本说明。 |
| DurationMs | 操作持续时间，以毫秒为单位。 |
| CallerIpAddress | 调用方 IP 地址，前提是该操作对应于来自某个具有公开可用 IP 地址的实体的 API 调用。 |
| Level | 事件的严重级别。 |
| URI | 请求的 URI。 |
| OutgoingMessageLength | 传出消息中的字符数。 |
| IncomingMessageLength | 传入消息中的字符数。 |
| DeliveryAttempts | 尝试发送此消息的次数。 |
| PhoneNumber | 要从中发送短信的电话号码。 |
| SdkType | 请求中使用的 SDK 类型。 |
| PlatformType | 请求中使用的平台类型。 |
| 方法 | 请求中使用的方法。 |

### <a name="authentication-operational-logs"></a>身份验证操作日志

| properties | 说明 |
| -------- | ---------------|
| TimeGenerated | 生成日志的时间戳 (UTC)。 |
| OperationName | 与日志记录相关联的操作。 |
| CorrelationID | 关联事件的 ID。 可用于标识多个表之间的关联事件。 |
| OperationVersion | 如果使用 API 执行 `operationName`，则 `api-version` 与该操作关联。 如果没有与此操作相对应的 API，则该版本表示该操作的版本，以防与操作相关联的属性在将来发生更改。 |
| 类别 | 事件的日志类别。 类别是可以在特定资源上启用或禁用日志的粒度。 在事件的属性 blob 内显示的属性在特定日志类别和资源类型中相同。 |
| ResultType | 操作的状态。 |
| ResultSignature | 操作的子状态。 如果该操作对应于 REST API 调用，则此字段为相应 REST 调用的 HTTP 状态代码。 |
| DurationMs | 操作持续时间，以毫秒为单位。 |
| CallerIpAddress | 调用方 IP 地址，前提是该操作对应于来自某个具有公开可用 IP 地址的实体的 API 调用。 |
| Level | 事件的严重级别。 |
| URI | 请求的 URI。 |
| SdkType | 请求中使用的 SDK 类型。 |
| PlatformType | 请求中使用的平台类型。 |
| 标识 | 与操作相关的通信服务标识。 |
| 作用域 | 访问令牌中显示的通信服务范围。 |
