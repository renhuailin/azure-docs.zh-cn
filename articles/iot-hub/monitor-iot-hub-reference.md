---
title: 监视 Azure IoT 中心数据参考
description: 监视 Azure IoT 中心时需要的重要参考资料
author: robinsh
ms.author: robinsh
ms.topic: reference
ms.service: iot-hub
ms.custom: subject-monitoring
ms.date: 10/22/2020
ms.openlocfilehash: 6c7fd0a310663b664d33027951ade885b83d458a
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/17/2021
ms.locfileid: "100570961"
---
# <a name="monitoring-azure-iot-hub-data-reference"></a>监视 Azure IoT 中心数据参考

请参阅[监视 Azure IoT 中心](monitor-iot-hub.md)，详细了解如何收集和分析 Azure IoT 中心的监视数据。

## <a name="metrics"></a>指标

本节列出了 Azure IoT 中心自动收集的所有平台指标。 IoT 中心指标的资源提供程序命名空间为 Microsoft.Devices，类型命名空间为 IoTHubs 。

以下小节按常规类别细分了 IoT 中心平台指标，并按它们在 Azure 门户中显示的名称列出它们。 还提供了与每个小节中显示的指标相关的信息。

你还可以在 Azure Monitor 文档中的 [Microsoft.Devices/IotHubs](../azure-monitor/essentials/metrics-supported.md#microsoftdevicesiothubs) 下找到一个按指标名称列出所有 IoT 中心平台指标的表。 请注意，此表并不提供本文中涵盖的一些信息，如一些指标[支持的聚合](#supported-aggregations)。

若要了解其他 Azure 服务支持的指标，请参阅 [Azure Monitor 支持的指标](../azure-monitor/essentials/metrics-supported.md)。

本节主题

- [监视 Azure IoT 中心数据参考](#monitoring-azure-iot-hub-data-reference)
  - [指标](#metrics)
    - [支持的聚合](#supported-aggregations)
    - [云到设备命令指标](#cloud-to-device-command-metrics)
    - [云到设备直接方法指标](#cloud-to-device-direct-methods-metrics)
    - [云到设备孪生操作指标](#cloud-to-device-twin-operations-metrics)
    - [配置指标](#configurations-metrics)
    - [每日配额指标](#daily-quota-metrics)
    - [设备指标](#device-metrics)
    - [设备遥测指标](#device-telemetry-metrics)
    - [设备到云孪生操作指标](#device-to-cloud-twin-operations-metrics)
    - [事件网格指标](#event-grid-metrics)
    - [作业指标](#jobs-metrics)
    - [路由指标](#routing-metrics)
    - [孪生查询指标](#twin-query-metrics)
  - [指标维度](#metric-dimensions)
  - [资源日志](#resource-logs)
    - [连接](#connections)
    - [设备遥测](#device-telemetry)
    - [云到设备的命令](#cloud-to-device-commands)
    - [设备标识操作](#device-identity-operations)
    - [文件上传操作](#file-upload-operations)
    - [Routes](#routes)
    - [设备到云孪生操作](#device-to-cloud-twin-operations)
    - [云到设备孪生操作](#cloud-to-device-twin-operations)
    - [孪生查询](#twin-queries)
    - [作业操作](#jobs-operations)
    - [直接方法](#direct-methods)
    - [分布式跟踪（预览版）](#distributed-tracing-preview)
      - [IoT 中心 D2C（设备到云）日志](#iot-hub-d2c-device-to-cloud-logs)
      - [IoT 中心流入日志](#iot-hub-ingress-logs)
      - [IoT 中心流出日志](#iot-hub-egress-logs)
    - [配置](#configurations)
    - [设备流（预览版）](#device-streams-preview)
  - [Azure Monitor 日志表](#azure-monitor-logs-tables)
  - [另请参阅](#see-also)

### <a name="supported-aggregations"></a>支持的聚合

每个表中的“聚合类型”列对应于为图表或警报选择指标时使用的默认聚合。

   ![显示指标聚合的屏幕截图](./media/monitor-iot-hub-reference/aggregation-type.png)

对于大多数指标，所有聚合类型都是有效的；但是，对于计数指标，即“计价单位”列值为“计数”的指标，只有一些聚合是有效的 。 计数指标可以是以下两种类型之一：

* 对于单点计数指标，IoT 中心每次执行测量操作时都会注册一个数据点（实质上为 1）。 然后 Azure Monitor 在指定的粒度上对这些数据点进行求和。 单点指标的示例有发送的遥测消息数和已完成的 C2D 消息传递次数 。 对于这些指标，唯一相关的聚合类型为总计（总和）。 通过门户可选择最小值、最大值和平均值；然而，这些值始终为 1。

* 对于快照计数指标，当进行测量操作时，IoT 中心将登记总计数。 当前，IoT 中心发布了三种快照指标：已使用的消息总数、设备总数（预览）和已连接设备数（预览）  。 因为这些指标在每次发出时都显示一个“总数”，所以在指定的粒度上对它们进行求和没有任何意义。 Azure Monitor 仅限为这些指标的聚合类型选择平均值、最小值和最大值。

### <a name="cloud-to-device-command-metrics"></a>云到设备命令指标

|指标显示名称|指标|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|过期的 C2D 消息数（预览版）|C2DMessagesExpired|计数|总计|过期的云到设备消息数|无|
|已完成的 C2D 消息传递次数|c2d.commands.egress.complete.success|计数|总计|设备成功完成的云到设备消息传递次数|无|
|丢弃的 C2D 消息数|c2d.commands.egress.abandon.success|计数|总计|设备丢弃的云到设备消息数|无|
|拒绝的 C2D 消息数|c2d.commands.egress.reject.success|计数|总计|设备拒绝的云到设备消息数|无|

对于“计价单位”值为“计数”的指标，只有总计（总和）聚合有效 。 最小值、最大值和平均值聚合始终返回 1。 有关详细信息，请参阅[支持的聚合](#supported-aggregations)。

### <a name="cloud-to-device-direct-methods-metrics"></a>云到设备直接方法指标

|指标显示名称|指标|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|失败的直接方法调用数|c2d.methods.failure|计数|总计|所有失败直接方法调用的计数。|无|
|直接方法调用的请求大小|c2d.methods.requestSize|字节|平均值|所有成功的直接方法请求计数。|无|
|直接方法调用的响应大小|c2d.methods.responseSize|字节|平均值|所有成功的直接方法响应计数。|无|
|成功的直接方法调用数|c2d.methods.success|计数|总计|所有成功的直接方法调用的计数。|无|

对于“计价单位”值为“计数”的指标，只有总计（总和）聚合有效 。 最小值、最大值和平均值聚合始终返回 1。 有关详细信息，请参阅[支持的聚合](#supported-aggregations)。

### <a name="cloud-to-device-twin-operations-metrics"></a>云到设备孪生操作指标

|指标显示名称|指标|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|后端的失败孪生读取数|c2d.twin.read.failure|计数|总计|由后端发起的所有失败孪生读取的计数。|无|
|后端的失败孪生更新数|c2d.twin.update.failure|计数|总计|由后端发起的所有失败孪生更新的计数。|无|
|后端的孪生读取的响应大小|c2d.twin.read.size|字节|平均值|由后端发起的所有成功孪生读取的计数。|无|
|后端的失败孪生更新大小|c2d.twin.update.size|字节|平均值|由后端发起的所有成功孪生更新的总大小。|无|
|后端的成功孪生读取数|c2d.twin.read.success|计数|总计|由后端发起的所有成功孪生读取的计数。|无|
|后端的成功孪生更新数|c2d.twin.update.success|计数|总计|由后端发起的所有成功孪生更新的计数。|无|

对于“计价单位”值为“计数”的指标，只有总计（总和）聚合有效 。 最小值、最大值和平均值聚合始终返回 1。 有关详细信息，请参阅[支持的聚合](#supported-aggregations)。

### <a name="configurations-metrics"></a>配置指标

|指标显示名称|指标|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|配置指标|配置|计数|总计|在一组目标设备上为设备配置和 IoT Edge 部署执行的 CRUD 操作总数。 这还包括由于这些配置而修改设备孪生或模块孪生的操作的数量。|无|

对于“计价单位”值为“计数”的指标，只有总计（总和）聚合有效 。 最小值、最大值和平均值聚合始终返回 1。 有关详细信息，请参阅[支持的聚合](#supported-aggregations)。

### <a name="daily-quota-metrics"></a>每日配额指标

|指标显示名称|指标|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|设备数据用量总计|deviceDataUsage|字节|总计|从与 IotHub 相连的任意设备传出的字节，以及传入到与 IotHub 相连的任意设备的字节|无|
|设备数据用量总计（预览）|deviceDataUsageV2|字节|总计|从与 IotHub 相连的任意设备传出的字节，以及传入到与 IotHub 相连的任意设备的字节|无|
|已使用的消息总数|dailyMessageQuotaUsed|计数|平均值|今天使用的消息总数。 这是累积值，每日 00:00 UTC 重置为零。|无|

对于“已使用的消息总数”，只支持最小值、最大值和平均值聚合。 有关详细信息，请参阅[支持的聚合](#supported-aggregations)。

### <a name="device-metrics"></a>设备指标

|指标显示名称|指标|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|设备总数（已弃用）|devices.totalDevices|计数|总计|已注册到 IoT 中心的设备数目|无|
|连接的设备数（已弃用） |devices.connectedDevices.allProtocol|计数|总计|已连接到 IoT 中心的设备数目|无|
|设备总数（预览）|totalDeviceCount|计数|平均值|已注册到 IoT 中心的设备数目|无|
|连接设备数（预览）|connectedDeviceCount|计数|平均值|已连接到 IoT 中心的设备数目|无|

对于设备总数（已弃用）和连接的设备数（已弃用），只有总计（总和）聚合有效 。 最小值、最大值和平均值聚合始终返回 1。 有关详细信息，请参阅[支持的聚合](#supported-aggregations)。

对于设备总数（预览）和已连接设备数（预览），只有最小值、最大值和平均值聚合有效 。 有关详细信息，请参阅[支持的聚合](#supported-aggregations)。

已连接设备数（预览）和设备总数（预览）不能通过诊断设置导出 。

### <a name="device-telemetry-metrics"></a>设备遥测指标

|指标显示名称|指标|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|限制错误数|d2c.telemetry.ingress.sendThrottle|计数|总计|由于设备吞吐量限制而导致的限制错误数|无|
|遥测消息发送尝试次数|d2c.telemetry.ingress.allProtocol|计数|总计|尝试发送到 IoT 中心的、设备到云的遥测消息数|无|
|发送的遥测消息数|d2c.telemetry.ingress.success|计数|总计|成功发送到 IoT 中心的、设备到云的遥测消息数|无|

对于“计价单位”值为“计数”的指标，只有总计（总和）聚合有效 。 最小值、最大值和平均值聚合始终返回 1。 有关详细信息，请参阅[支持的聚合](#supported-aggregations)。

### <a name="device-to-cloud-twin-operations-metrics"></a>设备到云孪生操作指标

|指标显示名称|指标|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|设备的失败孪生读取数|d2c.twin.read.failure|计数|总计|由设备发起的所有失败孪生读取的计数。|无|
|设备的失败孪生更新数|d2c.twin.update.failure|计数|总计|由设备发起的所有失败的孪生更新的计数。|无|
|设备的孪生读取的响应大小|d2c.twin.read.size|字节|平均值|由设备发起的所有成功的孪生读取的数量。|无|
|设备的孪生更新的大小|d2c.twin.update.size|字节|平均值|由设备发起的所有成功的孪生更新的总大小。|无|
|设备的成功孪生读取数|d2c.twin.read.success|计数|总计|由设备发起的所有成功孪生读取的计数。|无|
|设备的成功孪生更新数|d2c.twin.update.success|计数|总计|由设备发起的所有成功的孪生更新的计数。|无|

对于“计价单位”值为“计数”的指标，只有总计（总和）聚合有效 。 最小值、最大值和平均值聚合始终返回 1。 有关详细信息，请参阅[支持的聚合](#supported-aggregations)。

### <a name="event-grid-metrics"></a>事件网格指标

|指标显示名称|指标|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|事件网格传送（预览版）|EventGridDeliveries|计数|总计|发布到事件网格的 IoT 中心事件的数量。 使用 Result 维度表示成功和失败请求的数量。 EventType 维度显示事件的类型 (https://aka.ms/ioteventgrid) 。|Result、<br/>EventType<br>有关详细信息，请参阅[指标维度](#metric-dimensions)。|
|事件网格延迟（预览）|EventGridLatency|毫秒|平均值|从生成 IoT 中心事件到将事件发布到事件网格的平均延迟（毫秒）。 此数值是所有事件类型的平均。 若要查看特定事件类型的延迟，请使用 EventType 维度。|EventType<br>有关详细信息，请参阅[指标维度](#metric-dimensions)。|

对于“计价单位”值为“计数”的指标，只有总计（总和）聚合有效 。 最小值、最大值和平均值聚合始终返回 1。 有关详细信息，请参阅[支持的聚合](#supported-aggregations)。

### <a name="jobs-metrics"></a>作业指标

|指标显示名称|指标|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|已完成的作业|jobs.completed|计数|总计|所有已完成的作业的计数。|无|
|对列出作业的失败调用数|jobs.listJobs.failure|计数|总计|对列出作业的所有失败调用的计数。|无|
|方法调用作业的创建失败数|jobs.createDirectMethodJob.failure|计数|总计|直接方法调用作业创建失败的所有次数。|无|
|孪生更新作业创建失败数|jobs.createTwinUpdateJob.failure|计数|总计|孪生更新作业创建失败的所有次数。|无|
|失败的作业取消数|jobs.cancelJob.failure|计数|总计|用来取消作业的调用失败的次数。|无|
|失败的作业查询数|jobs.queryJobs.failure|计数|总计|对查询作业的所有失败调用的计数。|无|
|失败的作业数|jobs.failed|计数|总计|所有失败的作业的计数。|无|
|对列出作业的成功调用数|jobs.listJobs.success|计数|总计|对列出作业的所有成功调用的计数。|无|
|方法调用作业的创建成功数|jobs.createDirectMethodJob.success|计数|总计|直接方法调用作业创建成功的所有次数。|无|
|孪生更新作业创建成功数|jobs.createTwinUpdateJob.<br>success|计数|总计|孪生更新作业创建成功的所有次数。|无|
|成功的作业取消数|jobs.cancelJob.success|计数|总计|用来取消作业的调用成功的次数。|无|
|成功的作业查询数|jobs.queryJobs.success|计数|总计|对查询作业的所有成功调用的计数。|无|

对于“计价单位”值为“计数”的指标，只有总计（总和）聚合有效 。 最小值、最大值和平均值聚合始终返回 1。 有关详细信息，请参阅[支持的聚合](#supported-aggregations)。

### <a name="routing-metrics"></a>路由指标

|指标显示名称|指标|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
| 路由传递尝试次数（预览版） |RoutingDeliveries | 计数 | 总计 |这是路由传递指标。 使用维度标识特定终结点或特定路由源的传递状态。| Result、<br>RoutingSource、<br>EndpointType、<br>FailureReasonCategory、<br>EndpointName<br>有关详细信息，请参阅[指标维度](#metric-dimensions)。 |
| 路由传递数据大小（以字节为单位）（预览版）|RoutingDataSizeInBytesDelivered| 字节 | 总计 |IoT 中心路由到自定义终结点和内置终结点的总字节数。 使用维度标识路由到特定终结点或特定路由源的数据大小。| RoutingSource、<br>EndpointType<br>EndpointName<br>有关详细信息，请参阅[指标维度](#metric-dimensions)。|
| 路由延迟（预览版） |RoutingDeliveryLatency| 毫秒 | 平均值 |这是路由传递延迟指标。 使用维度标识特定终结点或特定路由源的延迟。| RoutingSource、<br>EndpointType、<br>EndpointName<br>有关详细信息，请参阅[指标维度](#metric-dimensions)。|
|路由：将 Blob 传送到存储的次数|d2c.endpoints.egress.storage.blobs|计数|总计|IoT 中心路由将 Blob 传送到存储终结点的次数。|无|
|路由：传送到存储的数据量|d2c.endpoints.egress.storage.bytes|字节|总计|IoT 中心路由传送到存储终结点的数据量（字节）。|无|
|路由：事件中心的消息延迟|d2c.endpoints.latency.eventHubs|毫秒|平均值|消息进入 IoT 中心与进入事中心类型的自定义终结点之间的平均延迟（毫秒）。 这不包括指向内置终结点（事件）的消息路由。|无|
|路由：服务总线队列的消息延迟|d2c.endpoints.latency.serviceBusQueues|毫秒|平均值|消息进入 IoT 中心与消息进入服务总线队列终结点之间的平均延迟（毫秒）。|无|
|路由：服务总线主题的消息延迟|d2c.endpoints.latency.serviceBusTopics|毫秒|平均值|消息进入 IoT 中心与消息进入服务总线主题终结点之间的平均延迟（毫秒）。|无|
|路由：消息/事件的消息延迟|d2c.endpoints.latency.builtIn.events|毫秒|平均值|消息进入 IoT 中心与遥测消息进入内置终结点（消息/事件）以及回退路由之间的平均延迟（毫秒）。|无|
|路由：存储的消息延迟|d2c.endpoints.latency.storage|毫秒|平均值|消息进入 IoT 中心与消息进入存储终结点之间的平均延迟（毫秒）。|无|
|路由：消息传送到事件中心的次数|d2c.endpoints.egress.eventHubs|计数|总计|IoT 中心路由成功将消息传送到事件中心类型的自定义终结点的次数。 这不包括指向内置终结点（事件）的消息路由。|无|
|路由：消息传送到服务总线队列的次数|d2c.endpoints.egress.serviceBusQueues|计数|总计|IoT 中心路由成功将消息传送到服务总线队列终结点的次数。|无|
|路由：消息传送到服务总线主题的次数|d2c.endpoints.egress.serviceBusTopics|计数|总计|IoT 中心路由成功将消息传送到服务总线主题终结点的次数。|无|
|路由：消息传送到回退路由的次数|d2c.telemetry.egress.fallback|计数|总计|IoT 中心路由将消息传送到与回退路由关联的终结点的次数。|无|
|路由：消息传送到消息/事件的次数|d2c.endpoints.egress.builtIn.events|计数|总计|IoT 中心路由成功将消息传送到内置终结点（消息/事件）以及回退路由的次数。|无|
|路由：消息传送到存储的次数|d2c.endpoints.egress.storage|计数|总计|IoT 中心路由成功将消息传送到存储终结点的次数。|无|
|路由：遥测消息传送次数|d2c.telemetry.egress.success|计数|总计|使用 IoT 中心路由将消息成功传送到所有终结点的次数。 如果某条消息已路由到多个终结点，则每成功传送一次，此值就会加 1。 如果某条消息多次路由到同一终结点，则每成功传送一次，此值就会加 1。|无|
|路由：遥测消息删除次数 |d2c.telemetry.egress.dropped|计数|总计|由于终结点消亡，IoT 中心路由删除消息的次数。 此值不会统计已传送到回退路由的消息，因为已删除的消息不会传送到回退路由。|无|
|路由：遥测消息不兼容|d2c.telemetry.egress.invalid|计数|总计|消息由于与终结点不兼容而无法由 IoT 中心路由传送的次数。 当 IoT 中心尝试将消息传递到终结点时，消息与终结点不兼容，传递失败并收到非暂时性错误。 不会重试无效的消息。 此值不包括重试次数。|无|
|路由：遥测消息孤立次数 |d2c.telemetry.egress.orphaned|计数|总计|禁用路由消息时，IoT 中心路由孤立了次数消息，因为它们与任何路由查询都不匹配。|无|

对于“计价单位”值为“计数”的指标，只有总计（总和）聚合有效 。 最小值、最大值和平均值聚合始终返回 1。 有关详细信息，请参阅[支持的聚合](#supported-aggregations)。

### <a name="twin-query-metrics"></a>孪生查询指标

|指标显示名称|指标|计价单位|聚合类型|说明|维度|
|---|---|---|---|---|---|
|失败的孪生查询数|twinQueries.failure|计数|总计|所有失败孪生查询的计数。|无|
|成功的孪生查询数|twinQueries.success|计数|总计|所有成功孪生查询的计数。|无|
|孪生查询结果大小|twinQueries.resultSize|字节|平均值|所有成功的孪生查询结果的总大小。|无|

对于“计价单位”值为“计数”的指标，只有总计（总和）聚合有效 。 最小值、最大值和平均值聚合始终返回 1。 有关详细信息，请参阅[支持的聚合](#supported-aggregations)。

## <a name="metric-dimensions"></a>指标维度

Azure IoT 中心具有以下与它的一些路由和事件网格指标关联的维度。

|维度名称 | 说明|
|---|---|
||
|EndpointName| 端点名称。|
|**EndpointType**|下列中的一个：eventHubs、serviceBusQueues、cosmosDB、serviceBusTopics   。 builtin 或 blobStorage 。|
|**EventType**| 以下事件网格事件类型之一：Microsoft.Devices.DeviceCreated。 Microsoft.Devices.DeviceDeleted、Microsoft.Devices.DeviceConnected、Microsoft.Devices.DeviceDisconnected 或 Microsoft.Devices.DeviceTelemetry   。 有关详细信息，请参阅[事件类型](iot-hub-event-grid.md#event-types)。|
|**FailureReasonCategory**| 下列中的一个：无效、已删除、已孤立或 null   。|
|**结果**| 成功或失败 。|
|**RoutingSource**| 设备消息<br>孪生更改事件<br>设备生命周期事件|

若要了解有关指标维度的详细信息，请参阅[多维指标](../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics)。

## <a name="resource-logs"></a>资源日志

本节列出为 Azure IoT 中心收集的所有资源日志类别类型和架构。 所有 IoT 中心日志的资源提供程序和类型均为 [Microsoft.Devices/IotHubs](../azure-monitor/essentials/resource-logs-categories.md#microsoftdevicesiothubs)。

本节主题

- [监视 Azure IoT 中心数据参考](#monitoring-azure-iot-hub-data-reference)
  - [指标](#metrics)
    - [支持的聚合](#supported-aggregations)
    - [云到设备命令指标](#cloud-to-device-command-metrics)
    - [云到设备直接方法指标](#cloud-to-device-direct-methods-metrics)
    - [云到设备孪生操作指标](#cloud-to-device-twin-operations-metrics)
    - [配置指标](#configurations-metrics)
    - [每日配额指标](#daily-quota-metrics)
    - [设备指标](#device-metrics)
    - [设备遥测指标](#device-telemetry-metrics)
    - [设备到云孪生操作指标](#device-to-cloud-twin-operations-metrics)
    - [事件网格指标](#event-grid-metrics)
    - [作业指标](#jobs-metrics)
    - [路由指标](#routing-metrics)
    - [孪生查询指标](#twin-query-metrics)
  - [指标维度](#metric-dimensions)
  - [资源日志](#resource-logs)
    - [连接](#connections)
    - [设备遥测](#device-telemetry)
    - [云到设备的命令](#cloud-to-device-commands)
    - [设备标识操作](#device-identity-operations)
    - [文件上传操作](#file-upload-operations)
    - [Routes](#routes)
    - [设备到云孪生操作](#device-to-cloud-twin-operations)
    - [云到设备孪生操作](#cloud-to-device-twin-operations)
    - [孪生查询](#twin-queries)
    - [作业操作](#jobs-operations)
    - [直接方法](#direct-methods)
    - [分布式跟踪（预览版）](#distributed-tracing-preview)
      - [IoT 中心 D2C（设备到云）日志](#iot-hub-d2c-device-to-cloud-logs)
      - [IoT 中心流入日志](#iot-hub-ingress-logs)
      - [IoT 中心流出日志](#iot-hub-egress-logs)
    - [配置](#configurations)
    - [设备流（预览版）](#device-streams-preview)
  - [Azure Monitor 日志表](#azure-monitor-logs-tables)
  - [另请参阅](#see-also)

### <a name="connections"></a>连接

连接类别跟踪设备连接，并断开事件与 IoT 中心和错误的连接。 此类别用于识别未经授权的连接尝试或者在失去与设备的连接时发出警报。

> [!NOTE]
> 若要获得设备的可靠连接状态，请检查[设备检测信号](iot-hub-devguide-identity-registry.md#device-heartbeat)。

```json
{
   "records":
   [
        {
            "time": " UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "deviceConnect",
            "category": "Connections",
            "level": "Information",
            "properties": "{\"deviceId\":\"<deviceId>\",\"sdkVersion\":\"<sdkVersion>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"maskedIpAddress\":\"<maskedIpAddress>\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="device-telemetry"></a>设备遥测

设备遥测类别跟踪在 IoT 中心发生的、与遥测管道相关的错误。 此类别包括发送遥测事件（例如限制）和接收遥测事件（例如未经授权的读取者）时发生的错误。 此类别无法捕捉设备本身运行的代码所造成的错误。

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "ingress",
            "category": "DeviceTelemetry",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"deviceId\":\"<deviceId>\",\"batching\":\"0\",\"messageSizeInBytes\":\"<messageSizeInBytes>\",\"EventProcessedUtcTime\":\"<UTC timestamp>\",\"EventEnqueuedUtcTime\":\"<UTC timestamp>\",\"partitionId\":\"1\"}", 
            "location": "Resource location"
        }
    ]
}
```

### <a name="cloud-to-device-commands"></a>云到设备的命令

云到设备的命令类别跟踪在 IoT 中心发生且与云到设备的消息管道相关的错误。 此类别包括在以下情况下发生的错误：

* 发送云到设备消息时（例如“未经授权的发件人”错误），
* 接收云到设备消息时（例如“超出传递计数”错误），以及
* 接收云到设备消息反馈时（例如“反馈已过期”错误）。

此类别不捕捉当云到设备消息已成功传递但设备未正确进行处理时出现的错误。

```json
{
    "records":
    [
        {
            "time": " UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "messageExpired",
            "category": "C2DCommands",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"deviceId\":\"<deviceId>\",\"messageId\":\"<messageId>\",\"messageSizeInBytes\":\"<messageSize>\",\"protocol\":\"Amqp\",\"deliveryAcknowledgement\":\"<None, NegativeOnly, PositiveOnly, Full>\",\"deliveryCount\":\"0\",\"expiryTime\":\"<timestamp>\",\"timeInSystem\":\"<timeInSystem>\",\"ttl\":<ttl>, \"EventProcessedUtcTime\":\"<UTC timestamp>\",\"EventEnqueuedUtcTime\":\"<UTC timestamp>\", \"maskedIpAddress\": \"<maskedIpAddress>\", \"statusCode\": \"4XX\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="device-identity-operations"></a>设备标识操作

设备标识操作类别跟踪你尝试在 IoT 中心的标识注册表中创建、更新或删除条目时所发生的错误。 预配方案就很适合跟踪此类别。

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "get",
            "category": "DeviceIdentityOperations",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"maskedIpAddress\":\"<maskedIpAddress>\",\"deviceId\":\"<deviceId>\", \"statusCode\":\"4XX\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="file-upload-operations"></a>文件上传操作

文件上传类别跟踪在 IoT 中心发生且与文件上传功能相关的错误。 此类别包括：

* SAS URI 发生的错误，例如，它在设备向中心通知某个完成的上传前失效。

* 由设备报告的失败上传。

* 创建 IoT 中心通知消息期间在存储中找不到文件时发生的错误。

此类别不能捕获在设备将文件上传到存储时直接发生的错误。

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "ingress",
            "category": "FileUploadOperations",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"blobUri\":\"http//bloburi.com\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="routes"></a>路由

[消息路由](./iot-hub-devguide-messages-d2c.md)类别跟踪消息路由评估期间发生的错误以及 IoT 中心感知到的终结点运行状况。 此类别包括诸如下列项的事件：

* 规则评估结果为“未定义”，
* IoT 中心将某个终结点标记为死终结点，或者
* 从终结点收到的任何错误。

此类别不包含有关消息本身的特定错误（例如设备限制错误），这些错误在“设备遥测”类别下报告。

```json
{
    "records":
    [
        {
            "time":"2019-12-12T03:25:14Z",
            "resourceId":"/SUBSCRIPTIONS/91R34780-3DEC-123A-BE2A-213B5500DFF0/RESOURCEGROUPS/ANON-TEST/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/ANONHUB1",
            "operationName":"endpointUnhealthy",
            "category":"Routes",
            "level":"Error",
            "resultType":"403004",
            "resultDescription":"DeviceMaximumQueueDepthExceeded",
            "properties":"{\"deviceId\":null,\"endpointName\":\"anon-sb-1\",\"messageId\":null,\"details\":\"DeviceMaximumQueueDepthExceeded\",\"routeName\":null,\"statusCode\":\"403\"}",
            "location":"westus"
        }
    ]
}
```

以下是有关路由资源日志的更多详细信息：

* [路由资源日志错误代码列表](troubleshoot-message-routing.md#diagnostics-error-codes)
* [路由资源日志操作名称列表](troubleshoot-message-routing.md#diagnostics-operation-names)

### <a name="device-to-cloud-twin-operations"></a>设备到云孪生操作

设备到云孪生操作类别跟踪设备孪生上设备发起的事件。 这些操作可能包括获取孪生、更新报告属性和订阅所需属性。

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "update",
            "category": "D2CTwinOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authenticationType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="cloud-to-device-twin-operations"></a>云到设备孪生操作

云到设备孪生操作类别跟踪设备孪生上服务发起的事件。 这些操作可能获取孪生、更新或替换标记，以及更新或替换所需属性。

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "read",
            "category": "C2DTwinOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"sdkVersion\":\"<sdkVersion>\",\"messageSize\":\"<messageSize>\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="twin-queries"></a>孪生查询

孪生查询类别报告在云中针对设备孪生发起的查询请求。

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "query",
            "category": "TwinQueries",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"query\":\"<twin query>\",\"sdkVersion\":\"<sdkVersion>\",\"messageSize\":\"<messageSize>\",\"pageSize\":\"<pageSize>\", \"continuation\":\"<true, false>\", \"resultSize\":\"<resultSize>\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="jobs-operations"></a>作业操作

作业操作类别报告在多个设备上更新设备孪生或调用直接方法的作业请求。 这些请求在云中发起。

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "jobCompleted",
            "category": "JobsOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"jobId\":\"<jobId>\", \"sdkVersion\": \"<sdkVersion>\",\"messageSize\": <messageSize>,\"filter\":\"DeviceId IN ['1414ded9-b445-414d-89b9-e48e8c6285d5']\",\"startTimeUtc\":\"Wednesday, September 13, 2017\",\"duration\":\"0\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="direct-methods"></a>直接方法

直接方法类别跟踪发送到单个设备的请求-响应交互。 这些请求在云中发起。

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "send",
            "category": "DirectMethods",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":<messageSize>, \"RequestSize\": 1, \"ResponseSize\": 1, \"sdkVersion\": \"2017-07-11\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="distributed-tracing-preview"></a>分布式跟踪（预览版）

分布式跟踪类别跟踪执行跟踪上下文标头的消息的相关 ID。 若要完全启用这些日志，必须通过以下方法来更新客户端代码： [使用 Iot 中心进行端到端的 "分析和诊断" iot 应用程序 (预览 ") ](iot-hub-distributed-tracing.md)。

请注意， `correlationId` 符合 [W3C 跟踪上下文](https://github.com/w3c/trace-context) 提议，其中包含 `trace-id` 和 `span-id` 。

#### <a name="iot-hub-d2c-device-to-cloud-logs"></a>IoT 中心 D2C（设备到云）日志

当包含有效跟踪属性的消息到达 IoT 中心时，IoT 中心会记录此日志。

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubD2C",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-0144d2590aacd909-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Receive message success",
            "durationMs": "",
            "properties": "{\"messageSize\": 1, \"deviceId\":\"<deviceId>\", \"callerLocalTimeUtc\": : \"2017-02-22T03:27:28.633Z\", \"calleeLocalTimeUtc\": \"2017-02-22T03:27:28.687Z\"}",
            "location": "Resource location"
        }
    ]
}
```

此时不会计算 `durationMs`，因为 IoT 中心的时钟可能不会与设备时钟同步，所以持续时间计算可能产生误导。 我们建议使用 `properties` 部分中的时间戳编写逻辑，以捕获设备到云延迟的峰值。

| 属性 | 类型 | 说明 |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **messageSize** | Integer | 以字节为单位的设备到云消息的大小 |
| **deviceId** | ASCII 7 位字母数字字符字符串 | 设备的标识 |
| **callerLocalTimeUtc** | UTC 时间戳 | 设备本地时钟报告的消息创建时间 |
| **calleeLocalTimeUtc** | UTC 时间戳 | IoT 中心服务端时钟报告的消息到达 IoT 中心网关的时间 |

#### <a name="iot-hub-ingress-logs"></a>IoT 中心流入日志

当包含有效跟踪属性的消息写入内部或内置事件中心时，IoT 中心会记录此日志。

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubIngress",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-349810a9bbd28730-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Ingress message success",
            "durationMs": "10",
            "properties": "{\"isRoutingEnabled\": \"true\", \"parentSpanId\":\"0144d2590aacd909\"}",
            "location": "Resource location"
        }
    ]
}
```

在 `properties` 部分中，此日志包含有关消息入口的其他信息。

| 属性 | 类型 | 说明 |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **isRoutingEnabled** | 字符串 | True 或 false，指示 IoT 中心是否启用了消息路由 |
| **parentSpanId** | 字符串 | 父消息的 [span-id](https://w3c.github.io/trace-context/#parent-id)，在这种情况下为 D2C 消息跟踪 |

#### <a name="iot-hub-egress-logs"></a>IoT 中心流出日志

当[路由](iot-hub-devguide-messages-d2c.md)已启用且消息写入到[终结点](iot-hub-devguide-endpoints.md)时，IoT 中心会记录此日志。 如果未启用路由，IoT 中心不会记录此日志。

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubEgress",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-98ac3578922acd26-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Egress message success",
            "durationMs": "10",
            "properties": "{\"endpointType\": \"EventHub\", \"endpointName\": \"myEventHub\", \"parentSpanId\":\"349810a9bbd28730\"}",
            "location": "Resource location"
        }
    ]
}
```

在 `properties` 部分中，此日志包含有关消息入口的其他信息。

| 属性 | 类型 | 说明 |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **点** | 字符串 | 路由终结点的名称 |
| **endpointType** | 字符串 | 路由终结点的类型 |
| **parentSpanId** | 字符串 | 父消息的 [span-id](https://w3c.github.io/trace-context/#parent-id)，在这种情况下为 IoT 中心流入消息跟踪 |

### <a name="configurations"></a>配置

IoT 中心配置日志跟踪有关自动设备管理功能集的事件和错误。

```json
{
    "records":
    [
         {
             "time": "2019-09-24T17:21:52Z",
             "resourceId": "Resource Id",
             "operationName": "ReadManyConfigurations",
             "category": "Configurations",
             "resultType": "",
             "resultDescription": "",
             "level": "Information",
             "durationMs": "17",
             "properties": "{\"configurationId\":\"\",\"sdkVersion\":\"2018-06-30\",\"messageSize\":\"0\",\"statusCode\":null}",
             "location": "southcentralus"
         }
    ]
}
```

### <a name="device-streams-preview"></a>设备流（预览版）

设备流类别跟踪发送到单个设备的请求-响应交互。

```json
{
    "records":
    [
         {
             "time": "2019-09-19T11:12:04Z",
             "resourceId": "Resource Id",
             "operationName": "invoke",
             "category": "DeviceStreams",
             "resultType": "",
             "resultDescription": "",    
             "level": "Information",
             "durationMs": "74",
             "properties": "{\"deviceId\":\"myDevice\",\"moduleId\":\"myModule\",\"sdkVersion\":\"2019-05-01-preview\",\"requestSize\":\"3\",\"responseSize\":\"5\",\"statusCode\":null,\"requestName\":\"myRequest\",\"direction\":\"c2d\"}",
             "location": "Central US"
         }
    ]
}
```

## <a name="azure-monitor-logs-tables"></a>Azure Monitor 日志表
<!-- REQUIRED. Please keep heading in this order -->

本节介绍与 Azure IoT 中心相关并可通过 Log Analytics 进行查询的所有 Azure Monitor 日志 Kusto 表。 有关这些表的列表以及指向 IoT 中心资源类型的详细信息的链接，请参阅 Azure Monitor 日志表参考中的 [IoT 中心](/azure/azure-monitor/reference/tables/tables-resourcetype#iot-hub)。

有关所有 Azure Monitor 日志/Log Analytics 表的参考，请参阅 [Azure Monitor 日志表参考](/azure/azure-monitor/reference/tables/tables-resourcetype)。

## <a name="see-also"></a>另请参阅

* 有关监视 Azure IoT 中心的说明，请参阅[监视 Azure IoT 中心](monitor-iot-hub.md)。
* 有关监视 Azure 资源的详细信息，请参阅[使用 Azure Monitor 监视 Azure 资源](../azure-monitor/essentials/monitor-azure-resource.md)。
