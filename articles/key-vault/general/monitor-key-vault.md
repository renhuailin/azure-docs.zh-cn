---
title: 监视 Azure Key Vault
description: 从此处开始了解如何监视 Azure Key Vault
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 09/21/2021
ms.author: mbaldwin
ms.custom: subject-monitoring
ms.openlocfilehash: fbb92ac1d1d6992a78577a8dfc062d6730302f2d
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129353357"
---
# <a name="monitoring-azure-key-vault"></a>监视 Azure Key Vault

当你的关键应用程序和业务流程依赖于 Azure 资源时，你需要监视这些资源的可用性、性能和操作。 对于 Azure Key Vault，在开始缩放时监视服务很重要，因为发送到 Key Vault 的请求数量将会增加。 这可能会增加请求的延迟时间，并且在极端情况下，会导致请求被限制，从而影响服务的性能。

本文介绍 Key Vault 生成的监视数据。 Key Vault 使用 [Azure Monitor](/azure/azure-monitor/overview)。 如果你不熟悉使用此功能的所有 Azure 服务通用的 Azure Monitor 功能，请参阅[使用 Azure Monitor 监视 Azure 资源](/azure/azure-monitor/essentials/monitor-azure-resource)。

## <a name="monitoring-overview-page-in-azure-portal"></a>Azure 门户中的监视概述页面

Azure 门户中每个 Key Vault 的“概述”页面在“监视”选项卡上包含以下指标：

- 请求总数
- 平均延迟
- 成功率

你也可以选择“其他指标”（或左侧边栏中“监视”下的“指标”选项卡）来查看这些指标：

- 总体服务 API 延迟
- Vault 整体可用性
- Vault 整体饱和度
- 服务 API 命中总计
- 服务 API 结果总计

## <a name="key-vault-insights"></a>Key Vault 见解

Azure 中的某些服务在 Azure 门户中具有一个特殊且醒目的预生成仪表板，可以从此仪表板着手监视服务。 这些特殊仪表板称为“见解”。

Key Vault 见解提供 Key Vault 请求、性能、失败和延迟的统一视图，在其中可以全面监视密钥保管库。 有关完整详细信息，请参阅[使用 Key Vault 见解监视 Key Vault 服务](../../azure-monitor/insights/key-vault-insights-overview.md)。

## <a name="monitoring-data"></a>监视数据

Key Vault 收集与[监视 Azure 资源中的数据](../../azure-monitor/insights/monitor-azure-resource.md#monitoring-data-from-Azure-resources)中所述的其他 Azure 资源类型相同的监视数据。

有关 Key Vault 创建的指标和日志指标的详细信息，请参阅[监视 Key Vault 数据参考](monitor-key-vault-reference.md)。

## <a name="collection-and-routing"></a>收集和路由

平台指标和活动日志会自动收集和存储，但你可以使用诊断设置将其路由到其他位置。  

在创建诊断设置并将其路由到一个或多个位置之前，不会收集和存储资源日志。

有关使用 Azure 门户、CLI 或 PowerShell 创建诊断设置的详细过程，请参阅[创建诊断设置以收集 Azure 中的平台日志和指标](../../azure-monitor/platform/diagnostic-settings.md)。 创建诊断设置时，请指定要收集的日志类别。 Key Vault 的类别在 [Key Vault 监视数据参考](monitor-key-vault-reference.md#resource-logs)中列出。

若要为 Key Vault 创建诊断设置，请参阅[启用 Key Vault 日志记录](howto-logging.md)。  以下部分将讨论可以收集的指标和日志。

## <a name="analyzing-metrics"></a>分析指标

可以从“Azure Monitor”菜单中打开“指标”，使用指标资源管理器根据来自其他 Azure 服务的指标分析 Key Vault 的指标 。 有关使用此工具的详细信息，请参阅 [Azure 指标资源管理器入门](/azure/azure-monitor/platform/metrics-getting-started)。

有关为 Key Vault 收集的平台指标列表，请参阅[监视 Key Vault 数据参考指标](monitor-key-vault-reference.md#metrics)  

## <a name="analyzing-logs"></a>分析日志

Azure Monitor 日志中的数据以表形式存储，每个表具有自己独有的属性集。  

Azure Monitor 中的所有资源日志都具有后跟服务特定字段的相同字段。 [Azure Monitor 资源日志架构](../../azure-monitor/platform/diagnostic-logs-schema.md#top-level-resource-logs-schema)概述了常见架构 

[活动日志](../../azure-monitor/platform/activity-log.md)是 Azure 中的一种平台日志，可用于深入了解订阅级别的事件。 你可以单独查看它或将它路由到 Azure Monitor 日志，然后便可以在其中使用 Log Analytics 执行复杂得多的查询。  

有关为 Key Vault 收集的资源日志类型列表，请参阅[监视 Key Vault 数据参考](monitor-key-vault-reference.md#resource-logs)  

有关 Azure Monitor 日志使用并可通过 Log Analytics 查询的表列表，请参阅[监视 Key Vault 数据参考](monitor-key-vault-reference.md#azure-monitor-logs-tables)  

### <a name="sample-kusto-queries"></a>示例 Kusto 查询

> [!IMPORTANT]
> 从 Key Vault 菜单中选择“日志”时，Log Analytics 随即打开，其查询范围设置为当前 Key Vault。 这意味着日志查询只包含来自该资源的数据。 如果要运行的查询包含来自他 Key Vault 或其他 Azure 服务的数据，请从“Azure Monitor”菜单中选择“日志”。 有关详细信息，请参阅 [Azure Monitor Log Analytics 中的日志查询范围和时间范围](/azure/azure-monitor/log-query/scope/)。

可在“日志搜索”搜索栏中输入下面这些查询，以便监视 Key Vault 资源。 这些查询使用[新语言](/azure/azure-monitor/logs/log-query-overview)。

* 是否有慢速请求？

    ```Kusto
    // List of KeyVault requests that took longer than 1sec. 
    // To create an alert for this query, click '+ New alert rule'
    let threshold=1000; // let operator defines a constant that can be further used in the query

    AzureDiagnostics
    | where ResourceProvider =="MICROSOFT.KEYVAULT" 
    | where DurationMs > threshold
    | summarize count() by OperationName, _ResourceId
    ```

* 是否有故障？

    ```Kusto
    // Count of failed KeyVault requests by status code. 
    // To create an alert for this query, click '+ New alert rule'

    AzureDiagnostics
    | where ResourceProvider =="MICROSOFT.KEYVAULT" 
    | where httpStatusCode_d >= 300 and not(OperationName == "Authentication" and httpStatusCode_d == 401)
    | summarize count() by requestUri_s, ResultSignature, _ResourceId
    // ResultSignature contains HTTP status, e.g. "OK" or "Forbidden"
    // httpStatusCode_d contains HTTP status code returned
    ```

* 输入反序列化错误

    ```Kusto
    // Shows errors caused due to malformed events that could not be deserialized by the job. 
    // To create an alert for this query, click '+ New alert rule'

    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.KEYVAULT" and parse_json(properties_s).DataErrorType in ("InputDeserializerError.InvalidData", "InputDeserializerError.TypeConversionError", "InputDeserializerError.MissingColumns", "InputDeserializerError.InvalidHeader", "InputDeserializerError.InvalidCompressionType")
    | project TimeGenerated, Resource, Region_s, OperationName, properties_s, Level, _ResourceId
    ```

* 此 KeyVault 的活跃状态如何？

    ```Kusto
    //  
    // Line chart showing trend of KeyVault requests volume, per operation over time. 
    // KeyVault diagnostic currently stores logs in AzureDiagnostics table which stores logs for multiple services. 
    // Filter on ResourceProvider for logs specific to a service.

    AzureDiagnostics
    | where ResourceProvider =="MICROSOFT.KEYVAULT" 
    | summarize count() by bin(TimeGenerated, 1h), OperationName // Aggregate by hour
    | render timechart

    ```

* 谁在调用此 KeyVault？ 

    ```Kusto
    // List of callers identified by their IP address with their request count.  
    // KeyVault diagnostic currently stores logs in AzureDiagnostics table which stores logs for multiple services. 
    // Filter on ResourceProvider for logs specific to a service.

    AzureDiagnostics
    | where ResourceProvider =="MICROSOFT.KEYVAULT"
    | summarize count() by CallerIPAddress
    ```

* 此 KeyVault 处理请求的速度有多快？ 

    ```Kusto
    // Line chart showing trend of request duration over time using different aggregations. 
 
    AzureDiagnostics
    | where ResourceProvider =="MICROSOFT.KEYVAULT" 
    | summarize avg(DurationMs) by requestUri_s, bin(TimeGenerated, 1h) // requestUri_s contains the URI of the request
    | render timechart
    ```

* 上个月发生了哪些更改？ 

    ```Kusto
    // Lists all update and patch requests from the last 30 days. 
    // KeyVault diagnostic currently stores logs in AzureDiagnostics table which stores logs for multiple services. 
    // Filter on ResourceProvider for logs specific to a service.

    AzureDiagnostics
    | where TimeGenerated > ago(30d) // Time range specified in the query. Overrides time picker in portal.
    | where ResourceProvider =="MICROSOFT.KEYVAULT" 
    | where OperationName == "VaultPut" or OperationName = "VaultPatch"
    | sort by TimeGenerated desc
    ```


## <a name="alerts"></a>警报

在监视数据中发现重要情况时，Azure Monitor 警报会主动通知你。 有了警报，你就可以在客户注意到你的系统中的问题之前确定和解决它们。 可以在[指标](../../azure-monitor/platform/alerts-metric-overview.md)、[日志](../../azure-monitor/platform/alerts-unified-log.md)和[活动日志](../../azure-monitor/platform/activity-log-alerts.md)上设置警报。 不同类型的警报各有优缺点

如果要创建或运行在 Azure Key Vault 上运行的应用程序，[Azure Monitor Application Insights](/azure/azure-monitor/overview#application-insights) 可能会提供其他类型的警报。

下面是 Azure Key Vault 的一些常见和建议警报规则。

- Key Vault 可用性低于 100%（静态阈值）
- Key Vault 延迟大于 500ms（静态阈值）
- 保管库整体饱和度大于 75%（静态阈值）
- 保管库整体饱和度超过平均数（动态阈值）
- 错误代码总计高于平均值（动态阈值）

有关更多详细信息，请参阅 [Azure Key Vault 警报](alert.md)。

## <a name="next-steps"></a>后续步骤

恭喜，你现在正在监视 Azure Key Vault。 如果还想设置警报，请参阅 [Azure Key Vault 警报](alert.md)。

- 有关 Key Vault 创建的指标、日志和其他重要值的参考信息，请参阅[监视 Azure Key Vault 数据参考](monitor-key-vault-reference.md)。
- 有关监视 Azure 资源的详细信息，请参阅[使用 Azure Monitor 监视 Azure 资源](/azure/azure-monitor/insights/monitor-azure-resource)。
