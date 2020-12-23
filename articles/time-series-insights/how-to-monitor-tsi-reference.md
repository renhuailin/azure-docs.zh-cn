---
title: 监视 Azure 时序见解数据引用 |Microsoft Docs
description: 用于监视 Azure 时序见解的参考文档。
author: deepakpalled
ms.author: lyhughes
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/10/2020
ms.custom: lyrana
ms.openlocfilehash: 0b564ddfdea2cf24b7f9b1bc608d47fa4cfe541b
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/17/2020
ms.locfileid: "97632572"
---
# <a name="monitoring-azure-time-series-insights-data-reference"></a>监视 Azure 时序见解数据引用

了解 Azure 时序见解环境中 Azure Monitor 收集的数据和资源。 有关收集和分析监视数据的详细信息，请参阅 [监视时序见解]( ./how-to-monitor-tsi.md) 。

## <a name="metrics"></a>指标

本部分列出了为 Azure 时序见解收集的所有自动收集的平台指标。 有关所有 Azure Monitor 支持指标的列表 (包括 Azure 时序见解) ，请参阅 [Azure Monitor 支持的指标](../azure-monitor/platform/metrics-supported.md)。 这些指标的资源提供程序是 [timeseriesinsights-environment-with-eventhub/环境/eventsources](../azure-monitor/platform/metrics-supported.md#microsofttimeseriesinsightsenvironmentseventsources) 和 [timeseriesinsights-environment-with-eventhub/环境](../azure-monitor/platform/metrics-supported.md#microsofttimeseriesinsightsenvironments)。


### <a name="ingress"></a>流入量
 
|指标|指标显示名称|单位|聚合类型|说明|
|---|---|---|---|---|
|IngressReceivedBytes|入口收到的字节数|字节|总计|从事件源读取的字节数的计数|
|IngressReceivedInvalidMessages|入口收到的无效消息数|计数|总计|从事件源中读取的无效消息的计数|
|IngressReceivedMessages|入口收到的消息数|计数|总计|从事件源中读取的消息的计数|
|IngressReceivedMessagesCountLag|入口收到的消息数计数延迟|计数|平均值|上次排队的消息在事件源分区中的序列号与在入口中进行处理的消息的序列号之间的差异|
|IngressReceivedMessagesTimeLag|入口收到的消息数时间延迟|秒|最大值|消息在事件源中排队与消息在入口中处理之间的时间差异|
|IngressStoredBytes|入口存储的字节数|字节|总计|已成功处理且可用于查询的事件的总大小|
|IngressStoredEvents|入口存储的事件数|计数|总计|已成功处理并可供查询的平展事件的计数|

### <a name="storage"></a>存储

|指标|指标显示名称|单位|聚合类型|说明|
|---|---|---|---|---|
|WarmStorageMaxProperties|暖存储最大属性数|计数|最大值|S1/S2 SKU 环境允许使用的最大属性数，以及 PAYG SKU 暖存储允许的最大属性数|
|WarmStorageUsedProperties|暖存储已用属性数 |计数|最大值|S1/S2 SKU 环境已使用的属性数，以及 PAYG SKU 暖存储已使用的属性数|

## <a name="resource-logs"></a>资源日志

本部分列出了可为 Azure 时序见解环境收集的资源日志类型。

| 类别 | 显示名称 | 说明 |
|----- |----- |----- |
| 流入量 | TSIIngress | 入口类别跟踪入站管道中发生的错误。 此类别包括接收事件时出现的错误，这些错误 (例如连接到事件源失败) 和处理事件， (例如分析事件负载) 时出现错误。 |

## <a name="schemas"></a>架构
Azure 时序见解正在使用以下架构

### <a name="tsiingress-table"></a>TSIIngress 表

| 属性 | 说明 |
|----- |----- |
| TimeGenerated |  (UTC) 生成此事件的时间。 |
| 位置 | 资源的位置。 |
| 类别 | 日志事件的类别。 |
| OperationName | 事件的操作名称。 |
| CorrelationId | 请求的相关 ID。 |
| Level | 事件的严重级别。 |
| ResultDescription | 操作结果的说明，如 "收到禁止的错误"。 |
| 消息 | 与错误关联的消息。 包含有关问题的详细信息以及如何缓解错误的详细信息。 |
| ErrorCode | 与错误关联的代码 |
| EventSourceType | 事件源的类型。 它可以是事件中心或 IoT 中心。 |
| EventSourceProperties | 特定于事件源的属性的集合。 包含使用者组和访问密钥名称等详细信息。 |
