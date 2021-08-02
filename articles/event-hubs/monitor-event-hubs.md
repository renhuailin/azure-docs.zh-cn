---
title: 监视 Azure 事件中心
description: 了解如何使用 Azure Monitor 查看、分析和创建有关 Azure 事件中心指标的警报。
ms.topic: conceptual
ms.custom: subject-monitoring
ms.date: 06/13/2021
ms.openlocfilehash: 1a7ea209b3aaf47095723f8d2831fff4a94ffe54
ms.sourcegitcommit: 23040f695dd0785409ab964613fabca1645cef90
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/14/2021
ms.locfileid: "112063585"
---
# <a name="monitor-azure-event-hubs"></a>监视 Azure 事件中心
当你的关键应用程序和业务流程依赖于 Azure 资源时，你需要监视这些资源的可用性、性能和操作。 本文介绍了 Azure 事件中心生成的监视数据，以及如何使用 Azure Monitor 对此数据进行分析和发出警报。

## <a name="what-is-azure-monitor"></a>说明是 Azure Monitor？
Azure 事件中心使用 [Azure Monitor](../azure-monitor/overview.md)（在 Azure 中是完整的堆栈监视服务）创建监视数据。 Azure Monitor 提供了一组用于监视 Azure 资源的完整功能。 它还可以监视其他云和本地的资源。

一开始可以阅读[使用 Azure Monitor 监视 Azure 资源](../azure-monitor/essentials/monitor-azure-resource.md)一文，其中介绍了以下概念：

- 说明是 Azure Monitor？
- 与监视相关的成本
- 监视 Azure 中收集的数据
- 配置数据收集
- Azure 中用于分析监视数据并就其发出警报的标准工具

本文中的以下各部分将介绍从 Azure 事件中心收集的特定数据。 这些部分还提供了使用 Azure 工具配置数据收集和分析此数据的示例。

> [!TIP]
> 若想了解与 Azure Monitor 相关的成本，请参阅[使用情况和估计成本](../azure-monitor//usage-estimated-costs.md)。 若要了解数据在 Azure Monitor 中显示需要花多长时间，请参阅 [日志数据引入时间](../azure-monitor/logs/data-ingestion-time.md)。

## <a name="monitoring-data-from-azure-event-hubs"></a>监视来自 Azure 事件中心的数据
Azure 事件中心收集与[监视 Azure 资源中的数据](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data)中所述的其他 Azure 资源相同的监视数据。 

有关 Azure 事件中心创建的日志和指标的详细参考，请参阅 [Azure 事件中心监视数据参考](monitor-event-hubs-reference.md)。

## <a name="collection-and-routing"></a>收集和路由
平台指标和活动日志会自动收集和存储，但可以使用诊断设置将其路由到其他位置。  

在创建诊断设置并将其路由到一个或多个位置之前，不会收集和存储资源日志。

有关使用 Azure 门户、CLI 或 PowerShell 创建诊断设置的详细过程，请参阅[创建诊断设置以收集 Azure 中的平台日志和指标](../azure-monitor/essentials/diagnostic-settings.md)。 创建诊断设置时，请指定要收集的日志类别。 Azure 事件中心的类别在 [Azure 事件中心监视数据参考](monitor-event-hubs-reference.md#resource-logs)中列出。

如果你使用 Azure 存储来存储诊断日志记录信息，则信息将存储在名为“insights-insight-logs-operationlogs”和“insights-pt1m”的容器中。 操作日志的示例 URL：`https://<Azure Storage account>.blob.core.windows.net/insights-logs-operationallogs/resourceId=/SUBSCRIPTIONS/<Azure subscription ID>/RESOURCEGROUPS/<Resource group name>/PROVIDERS/MICROSOFT.SERVICEBUS/NAMESPACES/<Namespace name>/y=<YEAR>/m=<MONTH-NUMBER>/d=<DAY-NUMBER>/h=<HOUR>/m=<MINUTE>/PT1H.json`。 指标日志的 URL 与之类似。 

如果你使用 Azure 事件中心来存储诊断日志记录信息，则信息将存储在名为“insights-insight-logs-operationlogs”和“insights-pt1m”的事件中心中。 你还可以选择自己的事件中心。 

如果你使用 Log Analytics 存储诊断日志记录信息，则这些信息将存储在名为“AzureDiagnostics”和“AzureMetrics”的表中。 

> [!IMPORTANT]
> 启用这些设置需要额外的 Azure 服务（存储帐户、事件中心或 Log Analytics），这可能会增加成本。 若要估算成本，请访问 [Azure 定价计算器](https://azure.microsoft.com/pricing/calculator)。

> [!NOTE]
> 启用诊断设置中的指标时，当前发送到存储帐户、事件中心或 log analytics 的信息中并不包含维度信息。

以下部分将讨论可以收集的指标和日志。

## <a name="analyzing-metrics"></a>分析指标
可以在你事件中心命名空间主页上的“Azure Monitor”部分中选择“指标”，以此来分析 Azure 事件中心的指标以及其他 Azure 服务的指标。 有关使用此工具的详细信息，请参阅 [Azure 指标资源管理器入门](../azure-monitor/essentials/metrics-getting-started.md)。 有关收集的平台指标的列表，请参阅[监视 Azure 事件中心数据引用指标](monitor-event-hubs-reference.md#metrics)。

![事件中心命名空间的指标资源管理器已选择](./media/monitor-event-hubs/metrics.png)

若要参考，可以查看 [Azure Monitor 中所有受支持的资源指标](../azure-monitor/essentials/metrics-supported.md)列表。

> [!TIP]
> Azure Monitor 指标数据有效期为 90 天。 但在创建图表时，只直观显示 30 天的数据。 例如，如果想要直观显示 90 天的数据，必须将它拆分成 90 天内包含 30 天的数据的三个图表。

### <a name="filtering-and-splitting"></a>筛选和拆分
对于支持维度的指标，应用筛选器时可以使用维度值。 例如，将设置为 `EntityName` 的筛选器添加到事件中心的名称。 还可以按维度来拆分指标，将指标的不同部分进行直观比较。 有关筛选和拆分的详细信息，请参阅 [Azure Monitor 的高级功能](../azure-monitor/essentials/metrics-charts.md)。

:::image type="content" source="./media/monitor-event-hubs/metrics-filter-split.png" alt-text="显示筛选和拆分指标的映像":::

## <a name="analyzing-logs"></a>分析日志
使用 Azure Monitor Log Analytics 需要创建诊断配置，并启用 __将信息发送到 Log Analytics__。 有关详细信息，请参阅[收集和路由](#collection-and-routing)部分。 Azure Monitor 日志中的数据以表形式存储，每个表包含自己独有的属性集。 Azure 事件中心将数据存储在以下表中：AzureDiagnostics 和 AzureMetrics。

> [!IMPORTANT]
> 在 Azure 事件中心菜单中选择“日志”时，Log Analytics 随即打开，其查询范围设置为当前工作区。 这意味着日志查询只包含来自该资源的数据。 如果希望运行的查询包含其他数据库或其他 Azure 服务的数据，请从“Azure Monitor”菜单中选择“日志”。 有关详细信息，请参阅 [Azure Monitor Log Analytics 中的日志查询范围和时间范围](../azure-monitor/logs/scope.md)。

请参阅 [Azure 事件中心监视数据参考](monitor-event-hubs-reference.md)，详细了解相关日志和指标。

### <a name="sample-kusto-queries"></a>示例 Kusto 查询

> [!IMPORTANT]
> 在 Azure 事件中心菜单中选择“日志”时，Log Analytics 随即打开，其查询范围设置为当前 Azure 事件中心命名空间。 这意味着日志查询只包含来自该资源的数据。 如果希望运行包含其他工作区或其他 Azure 服务数据的查询，请从“Azure Monitor”菜单中选择“日志” 。 有关详细信息，请参阅 [Azure Monitor Log Analytics 中的日志查询范围和时间范围](../azure-monitor/logs/scope.md)。

以下是可用于帮助监视 Azure 事件中心资源的示例查询： 

+ 获取过去 7 天的错误

    ```Kusto
    AzureDiagnostics
    | where TimeGenerated > ago(7d)
    | where ResourceProvider =="MICROSOFT.EVENTHUB"
    | where Category == "OperationalLogs"
    | summarize count() by "EventName"
    ```

+ 获取导致“找不到密钥”错误的密钥保管库的尝试访问。

    ```Kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.EVENTHUB" 
    | where Category == "Error" and OperationName == "wrapkey"
    | project Message
    ```

+ 获取使用密钥保管库执行的操作来禁用或还原密钥。

    ```Kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.EVENTHUB"
    | where Category == "info" and OperationName == "disable" or OperationName == "restore"
    | project Message
    ```
+ 获取捕获失败及其持续时间（以秒为单位）

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.EVENTHUB"
    | where Category == "ArchiveLogs"
    | summarize count() by "failures", "durationInSeconds"    
    ```
    
## <a name="alerts"></a>警报
你可以在事件中心命名空间主页上的“Azure Monitor”部分中选择“警报”，以此访问 Azure 事件中心的警报。 请参阅[使用 Azure Monitor 创建、查看和管理指标警报](../azure-monitor/alerts/alerts-metric.md)，详细了解如何创建警报。


## <a name="next-steps"></a>后续步骤

- 有关日志和指标的参考，请参阅[监视 Azure 事件中心数据参考](monitor-event-hubs-reference.md)。
- 有关监视 Azure 资源的详细信息，请参阅[通过 Azure Monitor 监视 Azure 资源](../azure-monitor/essentials/monitor-azure-resource.md)。
