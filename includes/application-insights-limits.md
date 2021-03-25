---
title: include 文件
description: include 文件
services: application-insights
author: lgayhardt
ms.service: application-insights
ms.topic: include
ms.date: 08/06/2019
ms.author: lagayhar
ms.custom: include file
ms.openlocfilehash: 1c4f6b876a4aa80c7e51f2bb3ca88234203d0daa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "101726167"
---
每个应用程序（即每个检测密钥）的指标和事件数都有一些限制。 限制取决于选择的[定价计划](https://azure.microsoft.com/pricing/details/application-insights/)。

| 资源 | 默认限制 | 注意
| --- | --- | --- |
| 每日的总数据量 | 100 GB | 可以通过设置一个上限来减少数据。 如果需要更多数据，可以在门户中最多将上限提高到 1,000 GB。 如需大于 1,000 GB 的容量，请将电子邮件发送到 AIDataCap@microsoft.com。
| 限制 | 32,000 事件/秒 | 限制按分钟计量。
| 数据保留日志 | [30 - 730 天](../articles/azure-monitor/app/pricing.md#change-the-data-retention-period)  | 此资源用于[日志](../articles/azure-monitor/logs/log-query-overview.md)。
| 数据保留指标 | 90 天| 此资源用于[指标资源管理器](../articles/azure-monitor/essentials/metrics-charts.md)。
| [可用性多步骤测试](../articles/azure-monitor/app/availability-multistep.md)详细结果保留 | 90 天 | 此资源提供了每个步骤的详细结果。
| 最大遥测项大小 | 64 KB |
| 每批最大遥测项数 | 64 K |
| 属性和指标名称长度 | 150 | 请参阅[类型架构](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond)。
| 属性值字符串长度 | 8,192  | 请参阅[类型架构](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond)。
| 跟踪和异常消息长度 | 32,768  | 请参阅[类型架构](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond)。
| 每个应用的[可用性测试](../articles/azure-monitor/app/monitor-web-app-availability.md)计数 | 100 |
| [探查器](../articles/azure-monitor/app/profiler.md)数据保留期 | 5 天 |
| 每天发送的[探查器](../articles/azure-monitor/app/profiler.md)数据量 | 10 GB |

有关详细信息，请参阅[关于 Application Insights 中的定价和配额](../articles/azure-monitor/app/pricing.md)。