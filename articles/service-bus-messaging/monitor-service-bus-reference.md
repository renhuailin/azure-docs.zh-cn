---
title: 监视 Azure 服务总线数据参考
description: 监视 Azure 服务总线需要的重要参考资料。
ms.topic: reference
ms.custom: subject-monitoring
ms.date: 05/18/2021
ms.openlocfilehash: a32972b6400831a4045082fa5d0255b06ed66210
ms.sourcegitcommit: 23040f695dd0785409ab964613fabca1645cef90
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/14/2021
ms.locfileid: "112061283"
---
# <a name="monitoring-azure-service-bus-data-reference"></a>监视 Azure 服务总线数据参考
有关收集和分析 Azure 服务总线监视数据的详细信息，请参阅[监视 Azure 服务总线](monitor-service-bus.md)。

## <a name="metrics"></a>指标
本部分列出了为 Azure 服务总线自动收集的所有平台指标。 这些指标的资源提供程序是 Microsoft.ServiceBus/namespaces。

### <a name="request-metrics"></a>请求指标
计算数据量和管理操作请求数。

| 标准名称 |  可通过诊断设置导出 | 计价单位 | 聚合类型 |  说明 | 维度 | 
| ---------- | ---------- | ----- | --- | --- | --- | 
| 传入请求数| 是 | 计数 | 总计 | 在指定的期间内向服务总线服务发出的请求数。 | EntityName | 
|成功的请求数| 否 | 计数 | 总计 | 在指定的期间内向服务总线服务发出的成功请求数。 | 实体名称<br/>OperationResult|
|服务器错误数| 否 | 计数 | 总计 | 由于服务总线服务发生错误，在指定期间内未处理的请求数。 | 实体名称<br/>OperationResult|
|用户错误数 | 否 | 计数 | 总计 | 由于存在用户错误，在指定期间内未处理的请求数。 | 实体名称|
|限制的请求数| 否 | 计数 | 总计 | 由于使用量超标，而被限制的请求数。 |  实体名称|

下面两种类型的错误被归类为“用户错误”：

1. 客户端错误（在 HTTP 中为 400 错误）。
2. 在处理消息时发生的错误，如 [MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception)。


### <a name="message-metrics"></a>消息指标

| 标准名称 |  可通过诊断设置导出 | 计价单位 | 聚合类型 |  说明 | 维度 | 
| ---------- | ---------- | ----- | --- | --- | --- | 
|传入消息数| 是 | 计数 | 总计 | 在指定的期间内发送到服务总线的事件或消息数。 此指标不包括自动转发的消息。 | 实体名称|
|传出消息数| 是 | 计数 | 总计 | 在指定的期间内从服务总线收到的事件或消息数。 | 实体名称|
| 消息| 否 | 计数 | 平均值 | 队列/主题中的消息计数。 | 实体名称 |
| 活动消息| 否 | 计数 | 平均值 | 队列/主题中的活动消息的计数。 | 实体名称 |
| 死信消息| 否 | 计数 | 平均值 | 队列/主题中的死信消息计数。  | 实体名称 |
| 计划的消息| 否 | 计数 | 平均值 | 队列/主题中的计划消息计数。 | 实体名称 |
| 大小 | 否 | 字节 | 平均值 | 实体（队列或主题）的大小，以字节为单位。 | 实体名称 | 

> [!NOTE]
> 消息的值，活动、死信、计划、已完成和已放弃的消息的值都是时间点值。 在该时间点之后立即使用的传入消息可能不会反映在这些指标中。 

### <a name="connection-metrics"></a>连接指标

| 标准名称 |  可通过诊断设置导出 | 计价单位 | 聚合类型 |  说明 | 维度 | 
| ---------- | ---------- | ----- | --- | --- | --- | 
|活动连接数| 否 | 计数 | 总计 | 命名空间上及其中实体上的活动连接数。 此指标的值为时间点值。 在该时间点之后立即处于活动状态的连接可能不会反映在这些指标中。 | |
|打开的连接数 | 否 | 计数 | 平均值 | 打开的连接数。 | 实体名称|
|关闭的连接数 | 否 | 计数 | 平均值 | 关闭的连接数。 | 实体名称|

### <a name="resource-usage-metrics"></a>资源使用情况指标

> [!NOTE] 
> 以下指标仅适用于高级层。 
> 
> 用于监视高级层命名空间中是否存在中断的重要指标包括：**每个命名空间的 CPU 使用率** 和 **每个命名空间的内存大小**。 使用 Azure Monitor 为这些指标[设置警报](../azure-monitor/alerts/alerts-metric.md)。
> 
> 你可以监视的另一个指标是：**限制的请求数**。 不过，只要命名空间保持在其内存、CPU 和中转连接限制内，就不会出现问题。 有关详细信息，请参阅 [Azure 服务总线高级层中的限制](service-bus-throttling.md#throttling-in-azure-service-bus-premium-tier)

| 标准名称 |  可通过诊断设置导出 | 计价单位 | 聚合类型 |  说明 | 维度 | 
| ---------- | ---------- | ----- | --- | --- | --- | 
|每个命名空间的 CPU 使用率| 否 | CPU | 百分比 | 命名空间的 CPU 使用百分比。 | 副本 |
|每个命名空间的内存使用量| 否 | 内存用量 | 百分比 | 命名空间的内存使用百分比。 | 副本 |

### <a name="error-metrics"></a>错误指标
| 标准名称 |  可通过诊断设置导出 | 计价单位 | 聚合类型 |  说明 | 维度 |
| ------------------- | ----------------- | --- | --- | --- | --- | 
|服务器错误数| 否 | 计数 | 总计 | 由于服务总线服务发生错误，在指定期间内未处理的请求数。 | 实体名称<br/><br/>操作结果 |
|用户错误数 | 否 | 计数 | 总计 | 由于存在用户错误，在指定期间内未处理的请求数。 | 实体名称<br/><br/>操作结果|

## <a name="metric-dimensions"></a>指标维度

Azure 总线服务支持对 Azure Monitor 中的指标使用以下维度。 为指标添加维度是可选的。 如果不添加维度，则指标是在命名空间级别指定的。 

|维度名称|说明|
| ------------------- | ----------------- |
|实体名称| 总线服务支持命名空间下的消息实体。|

## <a name="resource-logs"></a>资源日志
本节列出了可为 Azure 服务总线收集的资源日志类型。

- 操作日志
- 虚拟网络和 IP 筛选日志

### <a name="operational-logs"></a>操作日志
运行日志条目包括下表列出的元素：

| 名称 | 说明 |
| ------- | ------- |
| ActivityId | 内部 ID，用于标识指定的活动 |
| EventName | 操作名称 |
| ResourceId | Azure Resource Manager 资源 ID |
| SubscriptionId | 订阅 ID |
| EventTimeString | 操作时间 |
| EventProperties | 操作属性 |
| 状态 | 操作状态 |
| 调用方 | 操作的调用方（Azure 门户或管理客户端） |
| Category | OperationalLogs |

下面是运行日志 JSON 字符串的示例：

```json
{
  "ActivityId": "0000000000-0000-0000-0000-00000000000000",
  "EventName": "Create Queue",
  "resourceId": "/SUBSCRIPTIONS/<AZURE SUBSCRPTION ID>/RESOURCEGROUPS/<RESOURCE GROUP NAME>/PROVIDERS/MICROSOFT.SERVICEBUS/NAMESPACES/<SERVICE BUS NAMESPACE NAME>",
  "SubscriptionId": "0000000000-0000-0000-0000-00000000000000",
  "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
  "EventProperties": "{\"SubscriptionId\":\"0000000000-0000-0000-0000-00000000000000\",\"Namespace\":\"mynamespace\",\"Via\":\"https://mynamespace.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
  "Status": "Succeeded",
  "Caller": "ServiceBus Client",
  "category": "OperationalLogs"
}
```

### <a name="events-and-operations-captured-in-operational-logs"></a>在操作日志中捕获的事件和操作
操作日志捕获针对 Azure 服务总线命名空间执行的所有管理操作。 针对 Azure 服务总线执行的数据操作量很大，因此系统不会捕获数据操作。

> [!NOTE]
> 为了帮助你更好地跟踪数据操作，我们建议使用客户端跟踪。

在操作日志中捕获以下管理操作： 

| 作用域 | 操作|
|-------| -------- |
| 命名空间 | <ul> <li> 创建命名空间</li> <li> 更新命名空间 </li> <li> 删除命名空间 </li> <li> 更新命名空间 SharedAccess 策略 </li> </ul> | 
| 队列 | <ul> <li> 创建队列</li> <li> 更新队列</li> <li> 删除队列 </li> <li> 自动删除 - 删除队列 </li> </ul> | 
| 主题 | <ul> <li> 创建主题 </li> <li> 更新主题 </li> <li> 删除主题 </li> <li> 自动删除 - 删除主题 </li> </ul> |
| 订阅 | <ul> <li> 创建订阅 </li> <li> 更新订阅 </li> <li> 删除订阅 </li> <li> 自动删除 - 删除订阅 </li> </ul> |

> [!NOTE]
> 目前，不会在操作日志中跟踪“读取”操作。

## <a name="azure-monitor-logs-tables"></a>Azure Monitor 日志表
Azure 服务总线使用 Azure Monitor 日志中的 Kusto 表。 可以通过 Log Analytics 查询这些表。 有关服务使用的 Kusto 表的列表，请参阅[ Azure Monitor 日志表参考](/azure/azure-monitor/reference/tables/tables-resourcetype#service-bus)。


## <a name="next-steps"></a>后续步骤
- 有关监视 Azure 服务总线的详细信息，请参阅[监视 Azure 服务总线](monitor-service-bus.md)。
- 有关监视 Azure 资源的详细信息，请参阅[通过 Azure Monitor 监视 Azure 资源](../azure-monitor/essentials/monitor-azure-resource.md)。
