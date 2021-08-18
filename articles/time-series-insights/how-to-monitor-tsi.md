---
title: 监视时序见解 | Microsoft Docs
description: 监视时序见解的可用性、性能和操作。
author: tedvilutis
ms.author: tvilutis
manager: cnovak
ms.reviewer: orspodek
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/10/2020
ms.openlocfilehash: caf47ca56114ce559288ea1d115b2c09b9959324
ms.sourcegitcommit: 4f185f97599da236cbed0b5daef27ec95a2bb85f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2021
ms.locfileid: "112369356"
---
# <a name="monitoring-time-series-insights"></a>监视时序见解

当你的关键应用程序和业务流程依赖于 Azure 资源时，你需要监视这些资源的可用性、性能和操作。 本文介绍时序见解生成的监视数据，以及如何使用 Azure Monitor 的功能在此数据的基础上进行分析和发出警报。

## <a name="monitor-overview"></a>Monitor 概述

针对每个时序见解环境的 Azure 门户中的“概述”页包括资源使用情况的简要视图，例如已收到的消息数和已存储的字节数。 这些信息非常有用，但此页面只提供少量的监视数据。 创建资源后，其中的某些数据会自动收集，并可供分析。 你可以使用某些配置启用其他数据收集类型。

## <a name="what-is-azure-monitor"></a>什么是 Azure Monitor

时序见解使用 [Azure Monitor](../azure-monitor/overview.md) 创建监视数据。Azure Monitor 是 Azure 中的一个全堆栈监视服务，提供用于监视 Azure 资源以及其他云中的资源和本地资源的整套功能。

一开始可以阅读[使用 Azure Monitor 监视 Azure 资源](../azure-monitor/essentials/monitor-azure-resource.md)一文，其中介绍了以下概念：

- 说明是 Azure Monitor？
- 与监视相关的成本
- 监视 Azure 中收集的数据
- 配置数据收集
- Azure 中用于分析监视数据并就其发出警报的标准工具

本文中的以下各部分将介绍从 Azure 时序见解收集的特定数据。 这些部分还提供了使用 Azure 工具配置数据收集和分析此数据的示例。

> [!TIP]
> 若想了解与 Azure Monitor 相关的成本，请参阅[使用情况和估计成本](../azure-monitor//usage-estimated-costs.md)。 若要了解数据在 Azure Monitor 中显示需要花多长时间，请参阅 [日志数据引入时间](../azure-monitor/logs/data-ingestion-time.md)。

## <a name="monitoring-data-from-azure-time-series-insights"></a>监视来自 Azure 时序见解的数据

Azure 时序见解收集的监视数据的类型与 [Azure 资源的监视数据](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data)中所述的其他 Azure 资源相同。

请参阅 [Azure 时序见解监视数据引用](how-to-monitor-tsi-reference.md)来获取可以收集的日志和指标的详细参考信息。

## <a name="collection-and-routing"></a>收集和路由

平台指标会自动收集和存储，但你可以使用诊断设置将其路由到其他位置。

在创建诊断设置并将其路由到一个或多个位置之前，不会收集和存储资源日志。
有关使用 Azure 门户、CLI 或 PowerShell 创建诊断设置的详细过程，请参阅[创建诊断设置以收集 Azure 中的平台日志和指标](../azure-monitor/essentials/diagnostic-settings.md)。 创建诊断设置时，请指定要收集的日志类别。

可以从 Azure 时序见解的以下类别收集日志：

   | Category | 说明 |
   |---|---|
   | 流入量  | 入口类别跟踪入口管道中发生的错误。 此类别包括接收事件（如连接到事件源失败）和处理事件（如分析事件负载时出现错误）时发生的错误。 |

## <a name="analyzing-metrics"></a>分析指标

可以从“Azure Monitor”菜单中打开“指标”，以分析 Azure 时序见解指标以及来自其他 Azure 服务的指标。 有关使用此工具的详细信息，请参阅 [Azure 指标资源管理器入门](../azure-monitor/essentials/metrics-getting-started.md)。

有关收集的平台指标的列表，请参阅[监视 Azure 时序见解数据参考](how-to-monitor-tsi-reference.md#metrics)

此示例显示 Azure 时序见解环境中收到的来自所有事件源的字节数。

入口收到的字节数 [![Azure 时序入口收到的字节数](media/how-to-monitor-tsi/ingress-received-bytes.png)](media/how-to-monitor-tsi/ingress-received-bytes.png#lightbox)

该示例显示在 Azure 时序见解环境中成功处理并可用于查询的字节数。

入口存储的字节数 [![Azure 时序入口存储的字节数](media/how-to-monitor-tsi/ingress-stored-bytes.png)](media/how-to-monitor-tsi/ingress-stored-bytes.png#lightbox)

## <a name="analyzing-logs"></a>分析日志
你可以将资源日志作为存储帐户中的 blob 以及事件数据进行访问，也可以通过 Log Analytics 查询访问资源日志。

Azure Monitor 日志中的数据以表形式存储，每个表包含自己独有的属性集。

Azure Monitor 中的所有资源日志都具有后跟服务特定字段的相同字段。 [Azure Monitor 资源日志架构](../azure-monitor/essentials/resource-logs-schema.md#top-level-common-schema)概述了常见架构。 有关为 Azure 时序见解收集的资源日志类型的列表，请参阅 [Azure 时序见解监视数据参考](how-to-monitor-tsi-reference.md#resource-logs)。

Azure 时序见解将数据存储在以下表中。

| 表 | 说明 |
|:---|:---|
| TSIIngress | 存储来自入口类别的数据的表。 入口类别跟踪入口管道中发生的错误。 此类别包括接收事件（如连接到事件源失败）和处理事件（如分析事件负载时出现错误）时发生的错误。

若要将数据路由到 Azure Monitor 日志，你必须创建一个诊断设置，以便将资源日志或平台指标发送到 Log Analytics 工作区。 若要了解详细信息，请参阅[收集和路由](../iot-hub/monitor-iot-hub.md#collection-and-routing)。

## <a name="sample-queries"></a>示例查询

下面是有助于监视 Azure 时序见解环境的查询：

+ 获取有关过去五天内事件源连接失败的详细信息：

    ```Kusto
   TSIIngress
   | where OperationName == "Microsoft.TimeSeriesInsights/environments/eventsources/ingress/connect"
   | where _ResourceId contains "<your environment name, event source name, or the full event source resource URL>"
   | where TimeGenerated > ago(5d)

    ```
+ 获取过去五天内收到的无效邮件的详细信息：

    ```Kusto
   TSIIngress
   | where OperationName == "Microsoft.TimeSeriesInsights/environments/eventsources/ingress/deserialize"
   | where _ResourceId contains "<your environment name, event source name, or the full event source resource URL>"
   | where TimeGenerated > ago(5d)

    ```

## <a name="alerts"></a>警报

在监视数据中发现重要情况时，Azure Monitor 警报会主动通知你。 有了警报，你就可以在客户注意到你的系统中的问题之前确定和解决它们。 可以在[指标](../azure-monitor/alerts/alerts-metric-overview.md)、[日志](../azure-monitor/alerts/alerts-unified-log.md)和[活动日志](../azure-monitor/alerts/activity-log-alerts.md)上设置警报。 不同类型的警报各有优缺点。

当基于平台指标创建警报规则时，请注意，对于以计数单位收集的时序见解平台指标，某些聚合可能不可用或无法使用。

## <a name="next-steps"></a>后续步骤

* 请参阅 [Azure 时序见解监视数据参考](how-to-monitor-tsi-reference.md)，获取由 Azure 时序见解创建的日志和指标的参考信息。
* 有关监视 Azure 资源的详细信息，请参阅[通过 Azure Monitor 监视 Azure 资源](../azure-monitor/essentials/monitor-azure-resource.md)。