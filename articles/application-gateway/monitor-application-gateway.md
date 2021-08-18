---
title: 监视 Azure 应用程序网关
description: 在此处开始了解如何监视 Azure 应用程序网关
author: vhorne
ms.author: victorh
ms.service: application-gateway
ms.topic: conceptual
ms.date: 06/10/2021
ms.openlocfilehash: 7789590a2d13e6811e0ad8e7d2fa53cea9e0069a
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114295977"
---
<!-- VERSION 2.2
Template for the main monitoring article for Azure services. 
Keep the required sections and add/modify any content for any information specific to your service. 
This article should be in your TOC with the name *monitor-[Azure Application Gateway].md* and the TOC title "Monitor Azure Application Gateway". 
Put accompanying reference information into an article in the Reference section of your TOC with the name *monitor-[service-name]-reference.md* and the TOC title "Monitoring data". 
Keep the headings in this order. 
-->

# <a name="monitoring-azure-application-gateway"></a>监视 Azure 应用程序网关
<!-- REQUIRED. Please keep headings in this order -->
<!-- Most services can use this section unchanged. Add to it if there are any unique charges if your service has significant monitoring beyond Azure Monitor. -->

当你的关键应用程序和业务流程依赖于 Azure 资源时，你需要监视这些资源的可用性、性能和操作。 

本文介绍 Azure 应用程序网关生成的监视数据。 Azure 应用程序网关使用 [Azure Monitor](../azure-monitor/overview.md)。 如果不熟悉所有 Azure 服务普遍使用的 Azure Monitor 功能，请参阅[使用 Azure Monitor 监视 Azure 资源](../azure-monitor/essentials/monitor-azure-resource.md)。


<!-- Optional diagram showing monitoring for your service. If you need help creating one, contact robb@microsoft.com -->

## <a name="monitoring-overview-page-in-azure-portal"></a>Azure 门户中的监视概述页
<!-- OPTIONAL. Please keep headings in this order -->
<!-- Most services can use this section unchanged. Edit it if there are any unique charges if your service has significant monitoring beyond Azure Monitor. -->

Azure 门户中每个应用程序网关的“概述”页包含以下指标：

- 请求总数合计
- 失败的请求合计
- 按 HttpStatus 列出的响应状态合计
- 吞吐量合计
- CurrentConnection 合计
- 按 BackendPool HttpSettings 列出的正常主机平均计数
- 按 BackendPool HttpSettings 列出的不正常主机平均计数

这只是为应用程序网关提供的一部分指标。 有关详细信息，请参阅[有关监视 Azure 应用程序网关数据的参考](monitor-application-gateway-reference.md)。


## <a name="azure-monitor-network-insights"></a>Azure Monitor 网络见解

<!-- OPTIONAL SECTION.  Only include if your service has an "insight" associated with it. Examples of insights include
  - CosmosDB https://docs.microsoft.com/azure/azure-monitor/insights/cosmosdb-insights-overview
  - If you still aren't sure, contact azmondocs@microsoft.com.>
-->

Azure 中的某些服务在 Azure 门户中具有一个特殊且醒目的预生成仪表板，可以从此仪表板着手监视服务。 这些特殊仪表板称为“见解”。

<!-- Give a quick outline of what your "insight page" provides and refer to another article that gives details -->

Azure Monitor 网络见解为已部署的所有网络资源（包括应用程序网关）提供有关运行状况和指标的综合视图，并且无需任何配置。 有关详细信息，请查看 [Azure Monitor 网络见解](../azure-monitor/insights/network-insights-overview.md)。

## <a name="monitoring-data"></a>监视数据 

<!-- REQUIRED. Please keep headings in this order -->
Azure 应用程序网关收集的监视数据的类型与[来自 Azure 资源的监视数据](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data)中所述的其他 Azure 资源相同。 

有关 Azure 应用程序网关创建的指标和日志指标的详细信息，请参阅[有关监视 Azure 应用程序网关的数据参考](monitor-application-gateway-reference.md)。

<!-- If your service has additional non-Azure Monitor monitoring data then outline and refer to that here. Also include that information in the data reference as appropriate. -->

## <a name="collection-and-routing"></a>收集和路由

<!-- REQUIRED. Please keep headings in this order -->

平台指标和活动日志会自动收集和存储，但你可以使用诊断设置将其路由到其他位置。  

在创建诊断设置并将其路由到一个或多个位置之前，不会收集和存储资源日志。

<!-- Include any additional information on collecting logs.  The number of things that diagnostics settings control is expanding -->

有关使用 Azure 门户、CLI 或 PowerShell 创建诊断设置的详细过程，请参阅[创建诊断设置以收集 Azure 中的平台日志和指标](../azure-monitor/essentials/diagnostic-settings.md)。 创建诊断设置时，请指定要收集的日志类别。 [Azure 应用程序网关监视数据参考](monitor-application-gateway-reference.md#resource-logs)中列出了适用于 Azure 应用程序网关的类别。

<!-- OPTIONAL: Add specific examples of configuration for this service. For example, CLI and PowerShell commands for creating diagnostic setting. Ideally, customers should set up a policy to automatically turn on collection for services. Azure monitor has Resource Manager template examples you can point to. See https://docs.microsoft.com/azure/azure-monitor/samples/resource-manager-diagnostic-settings.  Contact azmondocs@microsoft.com if you have questions.   -->

以下部分将讨论可以收集的指标和日志。

## <a name="analyzing-metrics"></a>分析指标

<!-- REQUIRED. Please keep headings in this order 
If you don't support metrics, say so. Some services may be only onboarded to logs -->

可以从“Azure Monitor”菜单中打开“指标”，使用指标资源管理器根据来自其他 Azure 服务的指标分析 Azure 应用程序网关的指标 。 有关使用此工具的详细信息，请参阅 [Azure 指标资源管理器入门](../azure-monitor/essentials/metrics-getting-started.md)。 

<!-- Point to the list of metrics available in your monitor-service-reference article. -->
有关为 Azure 应用程序网关收集的平台指标列表，请参阅[有关监视应用程序网关的数据参考指标](monitor-application-gateway-reference.md#metrics)。  


若要参考，可以查看 [Azure Monitor 中所有受支持的资源指标](../azure-monitor/essentials/metrics-supported.md)列表。

<!--  Optional: Call out additional information to help your customers. For example, you can include additional information here about how to use metrics explorer specifically for your service. Remember that the UI is subject to change quite often so you will need to maintain these screenshots yourself if you add them in. -->

## <a name="analyzing-logs"></a>分析日志

<!-- REQUIRED. Please keep headings in this order
If you don't support resource logs, say so. Some services may be only onboarded to metrics and the activity log. -->

Azure Monitor 日志中的数据以表形式存储，每个表具有自己独有的属性集。  

Azure Monitor 中的所有资源日志都具有后跟服务特定字段的相同字段。 [Azure 资源日志的通用架构和特定于服务的架构](../azure-monitor/essentials/resource-logs-schema.md#top-level-common-schema)中概述了通用架构。 

[活动日志](../azure-monitor/essentials/activity-log.md)是 Azure 中的一种平台日志，可用于深入了解订阅级别事件。 你可以单独查看它或将它路由到 Azure Monitor 日志，然后便可以在其中使用 Log Analytics 执行复杂得多的查询。  

有关为 Azure 应用程序网关收集的资源日志类型列表，请参阅[有关监视 Azure 应用程序网关数据的参考](monitor-application-gateway-reference.md#resource-logs)。

有关 Azure Monitor 日志使用的表和可供 Log Analytics 查询的表的列表，请参阅[有关监视 Azure 应用程序网关数据的参考](monitor-application-gateway-reference.md#azure-monitor-logs-tables)。  

<!--  Optional: Call out additional information to help your customers. For example, you can include additional information here about log usage or what logs are most important. Remember that the UI is subject to change quite often so you will need to maintain these screenshots yourself if you add them in. -->

### <a name="sample-kusto-queries"></a>示例 Kusto 查询

<!-- REQUIRED if you support logs. Please keep headings in this order -->
<!-- Add sample Log Analytics Kusto queries for your service. -->

> [!IMPORTANT]
> 从应用程序网关菜单中选择“日志”时，会打开 Log Analytics，其中的查询范围设置为当前应用程序网关。 这意味着日志查询只包含来自该资源的数据。 如果你想要运行的查询包含来自其他应用程序网关或其他 Azure 服务的数据，请在“Azure Monitor”菜单中选择“日志” 。 有关详细信息，请参阅 [Azure Monitor Log Analytics 中的日志查询范围和时间范围](/azure/azure-monitor/log-query/scope/)。

<!-- REQUIRED: Include queries that are helpful for figuring out the health and state of your service. Ideally, use some of these queries in the alerts section. It's possible that some of your queries may be in the Log Analytics UI (sample or example queries). Check if so.  -->

可使用以下查询来帮助监视应用程序网关资源。 

<!-- Put in a code section here. -->  
```Kusto
// Requests per hour 
// Count of the incoming requests on the Application Gateway. 
// To create an alert for this query, click '+ New alert rule'
AzureDiagnostics
| where ResourceType == "APPLICATIONGATEWAYS" and OperationName == "ApplicationGatewayAccess"
| summarize AggregatedValue = count() by bin(TimeGenerated, 1h), _ResourceId
| render timechart 
```

```kusto
// Failed requests per hour 
// Count of requests to which Application Gateway responded with an error. 
// To create an alert for this query, click '+ New alert rule'
AzureDiagnostics
| where ResourceType == "APPLICATIONGATEWAYS" and OperationName == "ApplicationGatewayAccess" and httpStatus_d > 399
| summarize AggregatedValue = count() by bin(TimeGenerated, 1h), _ResourceId
| render timechart
```

```kusto
// Top 10 Client IPs 
// Count of requests per client IP. 
AzureDiagnostics
| where ResourceType == "APPLICATIONGATEWAYS" and OperationName == "ApplicationGatewayAccess"
| summarize AggregatedValue = count() by clientIP_s
| top 10 by AggregatedValue
```

```kusto
// Errors by user agent 
// Number of errors by user agent. 
// To create an alert for this query, click '+ New alert rule'
AzureDiagnostics
| where ResourceType == "APPLICATIONGATEWAYS" and OperationName == "ApplicationGatewayAccess" and httpStatus_d > 399
| summarize AggregatedValue = count() by userAgent_s, _ResourceId
| sort by AggregatedValue desc
```

## <a name="alerts"></a>警报

<!-- SUGGESTED: Include useful alerts on metrics, logs, log conditions or activity log. Ask your PMs if you don't know. 
This information is the BIGGEST request we get in Azure Monitor so do not avoid it long term. People don't know what to monitor for best results. Be prescriptive  
-->

在监视数据中发现重要情况时，Azure Monitor 警报会主动通知你。 有了警报，你就可以在客户注意到你的系统中的问题之前确定和解决它们。 可以在[指标](../azure-monitor/alerts/alerts-metric-overview.md)、[日志](../azure-monitor/alerts/alerts-unified-log.md)和[活动日志](../azure-monitor/alerts/activity-log-alerts.md)上设置警报。 不同类型的警报各有优缺点

<!-- only include next line if applications run on your service and work with App Insights. --> 

如果你要创建或运行一个使用应用程序网关的应用程序，[Azure Monitor Application Insights](../azure-monitor/overview.md#application-insights) 可提供其他类型的警报。
<!-- end -->

下表列出了常用和建议的应用程序网关警报规则。

<!-- Fill in the table with metric and log alerts that would be valuable for your service. Change the format as necessary to make it more readable -->

应用程序网关 v1

| 警报类型 | 条件 | 说明  |
|:---|:---|:---|
|指标|CPU 利用率超过 80%|正常情况下，CPU 使用率不应经常超过 90%，因为这可能导致托管在应用程序网关后面的网站中出现延迟，并破坏客户端体验。|
|指标|不正常主机计数超过了阈值|指示应用程序网关无法成功探测的后端服务器数。 此警报捕获了应用程序网关实例无法连接到后端的问题。 如果此数字超出后端容量的 20%，则会发出警报。|
|指标|响应状态（4xx、5xx）超过了阈值|当应用程序网关响应状态为 4xx 或 5xx 时发出此警报。 可能会由于暂时性问题而偶然出现 4xx 或 5xx 响应。 应该在生产环境中观察网关，以确定警报的静态阈值或对其使用动态阈值。|
|指标|失败的请求数超过了阈值|当失败的请求数指标超过了阈值时发出此警报。 应该在生产环境中观察网关，以确定警报的静态阈值或对其使用动态阈值。|


应用程序网关 v2

| 警报类型 | 条件 | 说明  |
|:---|:---|:---|
|指标|计算单位利用率超过了平均利用率的 75%|计算单位是对应用程序网关的计算使用量的度量。 检查过去 1 个月内的平均计算单位使用量，并设置在计算单位使用量超出 75% 的平均使用量时会发出的警报。|
|指标|容量单位利用率超过了峰值利用率的 75%|容量单位根据吞吐量、计算和连接计数来表示总体网关使用量。 检查过去 1 个月内的最大容量单位使用量，并设置在容量单位使用量超出 75% 的高峰使用量时会发出的警报。|
|指标|不正常主机计数超过了阈值|指示应用程序网关无法成功探测的后端服务器数。 这将捕获应用程序网关实例无法连接到后端的问题。 如果此数字超出后端容量的 20%，则会发出警报。|
|指标|响应状态（4xx、5xx）超过了阈值|当应用程序网关响应状态为 4xx 或 5xx 时发出此警报。 可能会由于暂时性问题而偶然出现 4xx 或 5xx 响应。 应该在生产环境中观察网关，以确定警报的静态阈值或对其使用动态阈值。|
|指标|失败的请求数超过了阈值|当失败的请求数指标超过了阈值时发出此警报。 应该在生产环境中观察网关，以确定警报的静态阈值或对其使用动态阈值。|
|指标|后端最后一个字节响应时间超过了阈值|指示从开始与后端服务器建立连接，到收到响应正文的最后一个字节的间隔时间。 创建一个在后端响应延迟比常规值高出某个阈值时会发出的警报。|
|指标|应用程序网关总时间超过了阈值|此间隔时间是根据从应用程序网关收到 HTTP 请求的第一个字节的时间，到将最后一个响应字节发送到客户端的时间计算的。 应该创建一个在后端响应延迟比常规值高出某个阈值时会发出的警报。|

## <a name="next-steps"></a>后续步骤

<!-- Add additional links. You can change the wording of these and add more if useful.   -->

- 有关应用程序网关创建的指标、日志和其他重要值的参考信息，请参阅[有关监视应用程序网关数据的参考](monitor-application-gateway-reference.md)。

- 有关监视 Azure 资源的详细信息，请参阅[使用 Azure Monitor 监视 Azure 资源](../azure-monitor/essentials/monitor-azure-resource.md)。