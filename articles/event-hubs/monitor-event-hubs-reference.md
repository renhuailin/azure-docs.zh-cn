---
title: 监视 Azure 事件中心数据参考
description: 监视 Azure 事件中心时需要的重要参考资料。
ms.topic: reference
ms.custom: subject-monitoring
ms.date: 06/11/2021
ms.openlocfilehash: 4dc40cef074a223a2cc34e3a87c9acae840f8dbf
ms.sourcegitcommit: 0beea0b1d8475672456da0b3a4485d133283c5ea
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2021
ms.locfileid: "112992038"
---
# <a name="monitoring-azure-event-hubs-data-reference"></a>监视 Azure 事件中心数据参考
有关收集和分析 Azure 事件中心监视数据的详细信息，请参阅[监视 Azure 事件中心](monitor-event-hubs.md)。

## <a name="metrics"></a>指标
本部分列出了为 Azure 事件中心自动收集的所有平台指标。 这些指标的资源提供程序是 Microsoft.EventHub/clusters 或 Microsoft.EventHub/clusters。

### <a name="request-metrics"></a>请求指标
计算数据量和管理操作请求数。

| 标准名称 |  可通过诊断设置导出 | 计价单位 | 聚合类型 |  说明 | 维度 | 
| ---------- | ---------- | ----- | --- | --- | --- | 
| 传入请求数| 是 | 计数 | 总计 | 在指定时间段内向事件中心服务发送的请求的数量。 | 实体名称| 
| 成功的请求数| 否 | 计数 | 总计 | 在指定时间段内向事件中心服务成功发送的请求的数量。 |  实体名称<br/><br/>操作结果 | 
| 限制的请求数| 否 | 计数 | 总计 |  由于使用量超标，而被限制的请求数。 | 实体名称<br/><br/>操作结果 |

下面两种类型的错误被归类为用户错误：

1. 客户端错误（在 HTTP 中为 400 错误）。
2. 处理消息时发生的错误。


### <a name="message-metrics"></a>消息指标
| 标准名称 |  可通过诊断设置导出 | 计价单位 | 聚合类型 |  说明 | 维度 | 
| ---------- | ---------- | ----- | --- | --- | --- | 
|传入消息数|  是 | 计数 | 总计 | 在指定期间内发送到事件中心的事件或邮件数。 | 实体名称|
|传出消息数| 是 | 计数 | 总计 | 在指定时间段内从事件中心接收的事件或消息的数量。 | 实体名称 | 
| 捕获的消息数| 否 | 计数| 总计 | 所捕获消息的数量。  |  实体名称 | 
|传入字节数 | 是 |  字节 | 总计 | 在指定时间段内事件中心的传入字节数。  | 实体名称| 
|传出字节数 | 是 |  字节 | 总计 |在指定时间段内事件中心的传出字节数。  | 实体名称 | 
| 大小 | 否 |  字节 | 平均值 |  事件中心的大小（以字节为单位）。|实体名称 |


> [!NOTE]
> 这些值是时间点值。 在该时间点之后立即使用的传入消息可能不会反映在这些指标中。 

### <a name="capture-metrics"></a>捕获指标
| 标准名称 |  可通过诊断设置导出 | 计价单位 | 聚合类型 |  说明 | 维度 | 
| ------------------- | ----------------- | --- | --- | --- | --- | 
| 捕获的消息数| 否 | 计数| 总计 | 所捕获消息的数量。  | 实体名称 |
| 捕获的字节数 | 否 | 字节 | 总计 | 事件中心的捕获字节数 | 实体名称 | 
| 捕获积压工作 (backlog) | 否 | 计数| 总计 | 事件中心捕获积压工作的数量 | 实体名称 | 


### <a name="connection-metrics"></a>连接指标
| 标准名称 |  可通过诊断设置导出 | 计价单位 | 聚合类型 |  说明 | 维度 | 
| ------------------- | ----------------- | --- | --- | --- | --- | 
|活动连接数| 否 | 计数 | 平均值 | 命名空间上及其中实体（事件中心）上的活动连接数。 此指标的值为时间点值。 在该时间点之后立即处于活动状态的连接可能不会反映在这些指标中。| 实体名称 | 
|打开的连接数 | 否 | 计数 | 平均值 |  打开的连接数。 | 实体名称 | 
|关闭的连接数 | 否 | 计数 | 平均值|  关闭的连接数。 | 实体名称 | 

### <a name="error-metrics"></a>错误指标
| 标准名称 |  可通过诊断设置导出 | 计价单位 | 聚合类型 |  说明 | 维度 |
| ------------------- | ----------------- | --- | --- | --- | --- | 
|服务器错误数| 否 | 计数 | 总计 | 在指定时间段内，因事件中心服务错误未处理的请求的数量。 | 实体名称<br/><br/>操作结果 |
|用户错误数 | 否 | 计数 | 总计 | 由于存在用户错误，在指定期间内未处理的请求数。 | 实体名称<br/><br/>操作结果|
|超过配额错误 | 否 |计数 | 总计 | 在指定时间段内超出配额所导致错误的数量。 | 实体名称<br/><br/>操作结果|

> [!NOTE]
> 逻辑应用创建 epoch 接收器，接收器可以从一个节点移到另一个节点，具体取决于服务负载。 在这些移动过程中，可能会发生 `ReceiverDisconnection` 异常。 它们被计为事件中心服务端的用户错误。 逻辑应用可从事件中心客户端收集故障，以便你在用户日志中查看它们。

## <a name="metric-dimensions"></a>指标维度

Azure 事件中心支持对 Azure Monitor 中的指标使用以下维度。 为指标添加维度是可选的。 如果不添加维度，则指标是在命名空间级别指定的。 

|维度名称|说明|
| ------------------- | ----------------- |
|实体名称| 事件中心的名称。|

## <a name="resource-logs"></a>资源日志
[!INCLUDE [event-hubs-diagnostic-log-schema](./includes/event-hubs-diagnostic-log-schema.md)]



## <a name="azure-monitor-logs-tables"></a>Azure Monitor 日志表
Azure 事件中心使用 Azure Monitor 日志中的 Kusto 表。 你可以通过 Log Analytics 查询这些表。 有关服务使用的 Kusto 表的列表，请参阅[ Azure Monitor 日志表参考](/azure/azure-monitor/reference/tables/tables-resourcetype#event-hubs)。


## <a name="next-steps"></a>后续步骤
- 有关监视 Azure 事件中心的详细信息，请参阅[监视 Azure 事件中心](monitor-event-hubs.md)。
- 有关监视 Azure 资源的详细信息，请参阅[通过 Azure Monitor 监视 Azure 资源](../azure-monitor/essentials/monitor-azure-resource.md)。
