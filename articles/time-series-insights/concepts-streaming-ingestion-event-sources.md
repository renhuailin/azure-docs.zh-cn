---
title: 流式引入事件源 - Azure 时序见解第 2 代 | Microsoft Docs
description: 了解如何将数据流式传输到 Azure 时序见解第 2 代中。
author: tedvilutis
ms.author: tvilutis
manager: cnovak
ms.reviewer: orspodek
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: fa920138753d02da03284907b6ecd415046c0963
ms.sourcegitcommit: 6323442dbe8effb3cbfc76ffdd6db417eab0cef7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/28/2021
ms.locfileid: "110617632"
---
# <a name="azure-time-series-insights-gen2-event-sources"></a>Azure 时序见解第 2 代事件源

Azure 时序见解第 2 代环境最多可以有两个流式传输事件源。 支持使用两种类型的 Azure 资源作为输入：

- [Azure IoT 中心](../iot-hub/about-iot-hub.md)
- [Azure 事件中心](../event-hubs/event-hubs-about.md)

必须以 UTF-8 编码的 JSON 形式发送事件。

## <a name="create-or-edit-event-sources"></a>创建或编辑事件源

事件源是你的中心与 Azure 时序见解第 2 代环境之间的链接，而且你的资源组中创建了一个类型为 `Time Series Insights event source` 的单独资源。 IoT 中心或事件中心资源可与 Azure 时序见解第 2 代环境位于同一 Azure 订阅中，也可位于不同订阅中。 但是，最佳做法是将 Azure 时序见解环境和 IoT 中心或事件中心放置在同一 Azure 区域中。

可使用 [Azure 门户](./tutorial-set-up-environment.md#create-an-azure-time-series-insights-gen2-environment)、[Azure CLI](/cli/azure/tsi/event-source)、[Azure 资源管理器模板](time-series-insights-manage-resources-using-azure-resource-manager-template.md)和 [REST API](/rest/api/time-series-insights/management(gen1/gen2)/eventsources) 来创建、编辑或删除环境的事件源。

> [!WARNING]
> 不要限制时序见解使用的中心或事件源的公共 Internet 访问，否则必要的连接将断开。

## <a name="start-options"></a>启动选项

创建事件源时，可指定应收集的预先存在的数据。 此设置是可选的。 可以使用以下选项：

| 名称   |  说明  |  Azure 资源管理器模板示例 |
|----------|-------------|------|
| EarliestAvailable | 引入 IoT 或事件中心内存储的所有预先存在的数据 | `"ingressStartAt": {"type": "EarliestAvailable"}` |
| EventSourceCreationTime |  开始引入在创建事件源后到达的数据。 在创建事件源之前流式传输的所有预先存在的数据都将被忽略。 这是 Azure 门户中的默认设置   |   `"ingressStartAt": {"type": "EventSourceCreationTime"}` |
| CustomEnqueuedTime | 环境将引入自定义排队时间 (UTC) 之后的数据。 将引入并存储在自定义排队时间或之后排队到 IoT 或事件中心的所有事件。 在自定义排队时间之前到达的所有事件都将被忽略。 请注意，“排队时间”指的是事件到达 IoT 或事件中心的时间 (UTC)。 这与事件正文中的自定义[时间戳属性](./concepts-streaming-ingestion-event-sources.md#event-source-timestamp)不同。 |     `"ingressStartAt": {"type": "CustomEnqueuedTime", "time": "2021-03-01T17:00:00.20Z"}` |

> [!IMPORTANT]
>
> - 如果选择 EarliestAvailable，而且有大量预先存在的数据，则初始延迟可能较高，因为 Azure 时序见解第 2 代环境会处理所有数据。
> - 在索引数据时，这种高延迟应该最终会缓解。 如果持续遇到较高的延迟，请通过 Azure 门户提交支持票证。

- EarliestAvailable

![EarliestAvailable 图示](media/concepts-streaming-event-sources/event-source-earliest-available.png)

- EventSourceCreationTime

![EventSourceCreationTime 图示](media/concepts-streaming-event-sources/event-source-creation-time.png)

- CustomEnqueuedTime

![CustomEnqueuedTime 图示](media/concepts-streaming-event-sources/event-source-custom-enqueued-time.png)

## <a name="streaming-ingestion-best-practices"></a>流式引入最佳做法

- 始终为 Azure 时序见解第 2 代环境创建唯一的使用者组以使用来自事件源的数据。 重用使用者组可能会导致随机断开连接，还可能导致数据丢失。

- 在同一 Azure 区域中配置 Azure 时序见解第 2 代环境和 IoT 中心和/或事件中心。 尽管可以在单独的区域中配置事件源，但此方案不受支持，并且我们不能保证高可用性。

- 请勿超出环境的[吞吐量速率限制](./concepts-streaming-ingress-throughput-limits.md)或每个分区的限制。

- 配置一个当你的环境在处理数据的过程中遇到问题时要发送的延迟[警报](./time-series-insights-environment-mitigate-latency.md#monitor-latency-and-throttling-with-alerts)。 请参阅下面的[生产工作负荷](./concepts-streaming-ingestion-event-sources.md#production-workloads)，了解建议的警报条件。

- 流式传输引入仅限用于近实时数据和最新数据，不支持流式传输历史数据。

- 了解如何对属性进行转义以及 JSON [数据如何平展和存储。](./concepts-json-flattening-escaping-rules.md)

- 提供事件源连接字符串时，请遵循最低权限原则。 对于事件中心，请配置仅包含“发送”声明的共享访问策略；对于 IoT 中心，请仅使用“服务连接”权限。

> [!CAUTION]
> 如果删除 IoT 中心或事件中心，并重新创建同名的新资源，则需要创建新的事件源，并附加新的 IoT 中心或事件中心。 在完成此步骤之前，将不会引入数据。

## <a name="production-workloads"></a>生产工作负荷

除了上述最佳做法，我们建议你为业务关键型工作负荷实现以下各项。

- 将 IoT 中心或事件中心的数据保留时间增加到最高 7 天。

- 在 Azure 门户中创建环境警报。 基于平台[指标](./how-to-monitor-tsi-reference.md#metrics)的警报使你可以验证端到端管道行为。 [此处](./time-series-insights-environment-mitigate-latency.md#monitor-latency-and-throttling-with-alerts)是有关创建和管理警报的说明。 建议的警报条件：

  - IngressReceivedMessagesTimeLag 大于 5 分钟
  - IngressReceivedBytes 为 0
- 请在 IoT 中心分区或事件中心分区之间保持引入负载均衡。

### <a name="historical-data-ingestion"></a>历史数据引入

Azure 时序见解第 2 代目前不支持使用流式传输管道导入历史数据。 如果需要将过去的数据导入到环境中，请遵循以下准则：

- 不要并行传输实时和历史数据。 引入无序数据将导致查询性能下降。
- 按时间顺序引入历史数据，以获得最佳性能。
- 不超过以下引入吞吐率上限。
- 如果数据早于你的 Warm 存储保留期，请禁用 Warm 存储。

## <a name="event-source-timestamp"></a>事件源时间戳

配置事件源时，系统会要求你提供时间戳 ID 属性。 时间戳属性用于跟踪一段时间内的事件，这是将要在[查询 API](/rest/api/time-series-insights/dataaccessgen2/query/execute) 中用作时间戳 `$ts` 的时间，它还将用于在 Azure 时序见解资源管理器中绘制序列。 如果在创建时未提供此属性，或者事件中缺少时间戳属性，则会将事件的 IoT 中心或事件中心排队时间用作默认值。 时间戳属性值以 UTC 格式存储。

通常情况下，用户会选择自定义时间戳属性，并使用传感器或标记生成读数时的时间，而不是使用默认的中心排队时间。 当设备出现间歇性连接中断，并将一批延迟的消息转发到 Azure 时序见解第 2 代时，尤其需要这样做。

如果你的自定义时间戳在嵌套 JSON 对象或数组中，则需要根据[平展和转义命名约定](concepts-json-flattening-escaping-rules.md)提供正确的属性名称。 例如，[此处](concepts-json-flattening-escaping-rules.md#example-a)显示的 JSON 有效负载的事件源时间戳 应当输入为 `"values.time"`。

### <a name="time-zone-offsets"></a>时区偏移

时间戳必须以 ISO 8601 格式发送并将以 UTC 格式存储。 如果提供了时区偏移，则会应用该偏移，然后会以 UTC 格式存储和返回时间。 如果偏移格式不正确，则会忽略它。 在你的解决方案可能没有原始偏移的上下文的情况下，你可以在另外的单独事件属性中发送偏移数据，以确保它会保留，并且你的应用程序可以在查询响应中进行引用。

时区偏移应设置为以下格式之一：

±HHMMZ<br />
±HH:MM<br />
±HH:MMZ

## <a name="next-steps"></a>后续步骤

- 阅读 [JSON 平展和转义规则](./concepts-json-flattening-escaping-rules.md)以了解如何存储事件。

- 了解你的环境的[吞吐量限制](./concepts-streaming-ingress-throughput-limits.md)