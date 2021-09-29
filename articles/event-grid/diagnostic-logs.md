---
title: Azure 事件网格 - 主题或域的诊断日志
description: 本文提供了有关 Azure 事件网格主题或域的诊断日志的概念信息。
ms.topic: conceptual
ms.date: 09/28/2021
ms.openlocfilehash: 057d4856e6a1bc0574639def731dffc99f988994
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129216890"
---
#  <a name="diagnostic-logs-for-azure-event-grid-topicsdomains"></a>Azure 事件网格主题/域的诊断日志
诊断设置允许事件网格用户在以下任一位置捕获和查看“发布和传递失败”日志：存储帐户、事件中心或 Log Analytics 工作区。 本文提供了日志的架构和示例日志条目。


## <a name="schema-for-publishdelivery-failure-logs"></a>发布/传递失败日志的架构

| 属性名称 | 数据类型 | 说明 |
| ------------- | --------- | ----------- | 
| 时间 | DateTime | 生成日志项目的时间 <p>**示例值：** 01-29-2020 09:52:02.700</p> |
| EventSubscriptionName | String | 事件订阅的名称 <p>**示例值：** “EVENTSUB1”</p> <p>仅传递失败日志有此属性。</p>  |
| Category | String | 日志类别名称。 <p>**示例值：** “DeliveryFailures”或“PublishFailures” | 
| OperationName | String | 操作的名称导致了失败。<p>**示例值：** “Deliver”，用于传递失败。 |
| Message | String | 用户的日志消息，说明失败的原因和其他详细信息。 |
| ResourceId | String | 主题/域资源的资源 ID<p>**示例值：** `/SUBSCRIPTIONS/SAMPLE-SUBSCRIPTION-ID/RESOURCEGROUPS/SAMPLE-RESOURCEGROUP/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/TOPIC1` |

## <a name="example"></a>示例

```json
{
    "time": "2019-11-01T00:17:13.4389048Z",
    "resourceId": "/SUBSCRIPTIONS/SAMPLE-SUBSCTIPTION-ID /RESOURCEGROUPS/SAMPLE-RESOURCEGROUP-NAME/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/SAMPLE-TOPIC-NAME ",
    "eventSubscriptionName": "SAMPLEDESTINATION",
    "category": "DeliveryFailures",
    "operationName": "Deliver",
    "message": "Message:outcome=NotFound, latencyInMs=2635, id=xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx, systemId=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx, state=FilteredFailingDelivery, deliveryTime=11/1/2019 12:17:10 AM, deliveryCount=0, probationCount=0, deliverySchema=EventGridEvent, eventSubscriptionDeliverySchema=EventGridEvent, fields=InputEvent, EventSubscriptionId, DeliveryTime, State, Id, DeliverySchema, LastDeliveryAttemptTime, SystemId, fieldCount=, requestExpiration=1/1/0001 12:00:00 AM, delivered=False publishTime=11/1/2019 12:17:10 AM, eventTime=11/1/2019 12:17:09 AM, eventType=Type, deliveryTime=11/1/2019 12:17:10 AM, filteringState=FilteredWithRpc, inputSchema=EventGridEvent, publisher=DIAGNOSTICLOGSTEST-EASTUS.EASTUS-1.EVENTGRID.AZURE.NET, size=363, fields=Id, PublishTime, SerializedBody, EventType, Topic, Subject, FilteringHashCode, SystemId, Publisher, FilteringTopic, TopicCategory, DataVersion, MetadataVersion, InputSchema, EventTime, fieldCount=15, url=sb://diagnosticlogstesting-eastus.servicebus.windows.net/, deliveryResponse=NotFound: The messaging entity 'sb://diagnosticlogstesting-eastus.servicebus.windows.net/eh-diagnosticlogstest' could not be found. TrackingId:c98c5af6-11f0-400b-8f56-c605662fb849_G14, SystemTracker:diagnosticlogstesting-eastus.servicebus.windows.net:eh-diagnosticlogstest, Timestamp:2019-11-01T00:17:13, referenceId: ac141738a9a54451b12b4cc31a10dedc_G14:"
}
```

`Outcome` 的值可能为 `Aborted`、`TimedOut`、`GenericError` 和 `Busy`。 事件网格记录在 `message` 中从事件处理程序接收的任何信息。 例如，对于 `GenericError`，它将记录 HTTP 状态代码、错误代码和错误消息。 

## <a name="next-steps"></a>后续步骤
若要了解如何为主题或域启用诊断日志，请参阅[启用诊断日志](enable-diagnostic-logs-topic.md)。
