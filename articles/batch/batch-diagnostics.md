---
title: 指标、警报和诊断日志
description: 记录并分析 Azure Batch 帐户资源（诸如池和任务）的诊断日志事件。
ms.topic: how-to
ms.date: 04/13/2021
ms.custom: seodec18
ms.openlocfilehash: 61aaca84b609aaf7513c6de6f0f7e73aef5a5efe
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389309"
---
# <a name="batch-metrics-alerts-and-logs-for-diagnostic-evaluation-and-monitoring"></a>用于诊断评估和监视的 Batch 指标、警报和日志

Azure Monitor 收集 Azure Batch 帐户中资源的[指标](../azure-monitor/essentials/data-platform-metrics.md)和[诊断日志](../azure-monitor/essentials/platform-logs-overview.md)。

可以通过多种方式收集和使用此数据，以监视 Batch 帐户和诊断问题。 还可以配置[指标警报](../azure-monitor/alerts/alerts-overview.md)，以便在某项指标达到指定值时收到通知。

## <a name="batch-metrics"></a>Batch 指标

[指标](../azure-monitor/essentials/data-platform-metrics.md)是 Azure 资源发出并由 Azure Monitor 服务使用的 Azure 遥测数据（也称为性能计数器）。 Batch 帐户中的指标示例包括“池创建事件”、“低优先级节点计数”和“任务完成事件”。 这些指标可帮助识别趋势，并可用于数据分析。

请参阅[支持的 Batch 指标列表](../azure-monitor/essentials/metrics-supported.md#microsoftbatchbatchaccounts)。

指标：

- 无需经过额外的配置，便已在每个 Batch 帐户中默认启用
- 每分钟生成一次
- 不会自动保留，但有 30 天的历史记录滚动更新周期。 可将活动指标保留为诊断日志记录的一部分。

## <a name="view-batch-metrics"></a>查看 Batch 指标

在 Azure 门户中，Batch 帐户的“概述”页面会默认显示关键的节点、核心和任务指标。

若要查看 Batch 帐户的其他指标，请执行以下操作：

1. 在 Azure 门户中，选择“所有服务” > “Batch 帐户”，然后选择你的 Batch 帐户的名称。
1. 在“监视”下，选择“指标”。 
1. 选择“添加指标”，然后从下拉列表中选择一个指标。
1. 为指标选择“聚合”选项。 对于基于计数的指标（如“专用核心计数”或“低优先级节点计数”），请使用“Avg”聚合。 对于基于事件的指标（如“池重设大小完成事件数”），请使用“计数”聚合。 避免使用 Sum 聚合，它会将图表周期内收到的所有数据点的值相加。
1. 若要添加其他指标，请重复步骤 3 和 4。

还可以使用 Azure Monitor API 以编程方式检索指标。 有关示例，请参阅[使用 .NET 检索 Azure Monitor 指标](/samples/azure-samples/monitor-dotnet-metrics-api/monitor-dotnet-metrics-api/)。

> [!NOTE]
> 过去 3 分钟内发出的指标可能仍然正在聚合，因此在此时间范围内，值可能会因记录不全而少报。 指标不保证送达，并且可能会受到乱序传递、数据丢失和/或数据重复的影响。

## <a name="batch-metric-alerts"></a>Batch 指标警报

你可以配置准实时指标警报。当指定指标的值超过分配的阈值时，会触发这些警报。 当警报“激活”（当阈值越过并满足警报条件时）以及“已解决”（当阈值再次超过并且不再满足条件）时，警报将生成通知。

由于指标传递可能会出现不一致，例如乱序传递、数据丢失或重复，因此我们建议避免在单个数据点上触发的警报， 而是改用阈值来解决所有不一致，例如乱序传递、数据丢失和一段时间内的重复。

例如，你可能想要配置一个当低优先级核心计数降到特定级别时触发的指标警报，以便能够调整池的组成部分。 为了获得最佳结果，请设置 10 分钟或以上的周期，如果平均低优先级核心计数在整个周期内低于阈值，则触发警报。 这样就可以有时间来聚合指标，以便获得更准确的结果。

若要在 Azure 门户中配置指标警报，请执行以下操作：

1. 选择“所有服务” > “Batch 帐户”，然后选择 Batch 帐户的名称。
1. 在“监视”下，选择“警报”，然后选择“新建警报规则”。  
1. 选择“添加条件”，然后选择一个指标。
1. 为“图表周期”、“阈值”、“运算符”和“聚合类型”选择所需的值   。
1. 输入“阈值”，然后选择阈值的“单位” 。  然后选择“完成”。
1. 通过选择现有操作组或创建新的操作组，将一个[操作组](../azure-monitor/alerts/action-groups.md)添加到警报中。
1. 在“预警规则详细信息”部分中，输入“预警规则名称”和“说明”  。 如果想要立即启用警报，请确保已选中“在创建时启用预警规则”框。
1. 选择“创建警报规则”。

有关创建指标警报的详细信息，请参阅[了解指标警报在 Azure Monitor 中的工作方式](../azure-monitor/alerts/alerts-metric-overview.md)和[使用 Azure Monitor 创建、查看和管理指标警报](../azure-monitor/alerts/alerts-metric.md)。

还可以使用 [Azure Monitor REST API](/rest/api/monitor/) 配置准实时警报。 有关详细信息，请参阅 [Microsoft Azure 中的警报概述](../azure-monitor/alerts/alerts-overview.md)。 若要将作业、任务或特定于池的信息包含在警报中，请参阅[使用 Azure Monitor 警报响应事件](../azure-monitor/alerts/tutorial-response.md)。

## <a name="batch-diagnostics"></a>Batch 诊断

[诊断日志](../azure-monitor/essentials/platform-logs-overview.md)包含 Azure 资源发出的、描述每个资源的操作的信息。 对于 Batch，可以收集以下日志：

- **ServiceLog**：在单个资源（例如池或任务）的生命周期内由 [Batch 服务发出的事件](#service-log-events)。
- **AllMetrics**：Batch 帐户级别的指标。

必须为要监视的每个 Batch 帐户显式启用诊断设置。

### <a name="log-destination-options"></a>日志目标选项

一种常见场景是选择 Azure 存储帐户作为日志目标。 若要在 Azure 存储中存储日志，请在启用日志收集之前创建帐户。 如果已将某个存储帐户关联到了 Batch 帐户，可以选择该帐户作为日志目标。

此外，还可以：

- 将 Batch 诊断日志事件流式传输到 [Azure 事件中心](../event-hubs/event-hubs-about.md)。 数据中心每秒可以接受数百万事件，用户可以使用任何实时分析提供程序转换并存储这些事件。
- 将诊断日志发送到 [Azure Monitor 日志](../azure-monitor/logs/log-query-overview.md)（可用于分析这些日志），或者导出诊断日志以在 Power BI 或 Excel 中进行分析。

> [!NOTE]
> 使用 Azure 服务存储或处理诊断日志数据可能会产生额外的费用。

### <a name="enable-collection-of-batch-diagnostic-logs"></a>启用 Batch 诊断日志的收集

若要在 Azure 门户中创建新的诊断设置，请执行以下步骤。

1. 在 Azure 门户中，选择“所有服务” > “Batch 帐户”，然后选择你的 Batch 帐户的名称。
2. 在“监视”下，选择“诊断设置” 。
3. 在“诊断设置”中，选择“添加诊断设置” 。
4. 输入设置名称。
5. 选择目标：“发送到 Log Analytics”、“存档到存储帐户”或“流式传输到事件中心”  。 如果选择存储帐户，则可以选择为每个日志保留数据的天数。 如果未指定保留天数，则数据在存储帐户的生存期内会一直保留。
6. 选择“ServiceLog”和/或“AllMetrics”。
7. 选择“保存”以创建诊断设置。

也可以通过[在 Azure 门户中创建诊断设置](../azure-monitor/essentials/diagnostic-settings.md)、使用[资源管理器模板](../azure-monitor/essentials/resource-manager-diagnostic-settings.md)或使用 Azure PowerShell 或 Azure CLI 来启用日志收集。 有关详细信息，请参阅 [Azure 平台日志概述](../azure-monitor/essentials/platform-logs-overview.md)。

### <a name="access-diagnostics-logs-in-storage"></a>访问存储中的诊断日志

如果[在存储帐户中存档 Batch 诊断日志](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)，则在发生相关的事件后，会立即在存储帐户中创建一个存储容器。 根据以下命名模式创建 Blob：

```json
insights-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/
RESOURCEGROUPS/{resource group name}/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/{Batch account name}/y={four-digit numeric year}/
m={two-digit numeric month}/d={two-digit numeric day}/
h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

例如：

```json
insights-metrics-pt1m/resourceId=/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/
RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/MYBATCHACCOUNT/y=2018/m=03/d=05/h=22/m=00/PT1H.json
```

每个 `PT1H.json` Blob 文件包含 JSON 格式的事件，这些事件是在 Blob URL 中指定的小时（例如 `h=12`）内发生的。 在当前的小时内发生的事件将追加​​到 `PT1H.json` 文件。 分钟值 (`m=00`) 始终为 `00`，因为诊断日志事件按小时细分成单个 blob。 （所有时间均是 UTC 时间。）

以下是 `PT1H.json` 日志文件中 `PoolResizeCompleteEvent` 条目的示例。 它包括有关专用和低优先级节点的当前和目标数量以及操作的开始和结束时间的信息：

```json
{ "Tenant": "65298bc2729a4c93b11c00ad7e660501", "time": "2019-08-22T20:59:13.5698778Z", "resourceId": "/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/BATCHACCOUNTS/MYBATCHACCOUNT/", "category": "ServiceLog", "operationName": "PoolResizeCompleteEvent", "operationVersion": "2017-06-01", "properties": {"id":"MYPOOLID","nodeDeallocationOption":"Requeue","currentDedicatedNodes":10,"targetDedicatedNodes":100,"currentLowPriorityNodes":0,"targetLowPriorityNodes":0,"enableAutoScale":false,"isAutoPool":false,"startTime":"2019-08-22 20:50:59.522","endTime":"2019-08-22 20:59:12.489","resultCode":"Success","resultMessage":"The operation succeeded"}}
```

若要以编程方式访问存储帐户中的日志，请使用存储 API。

### <a name="service-log-events"></a>服务日志事件

Azure Batch 服务日志包含 Batch 服务在单个 Batch 资源（例如池或任务）的生存期内发出的事件。 Batch 发出的每个事件以 JSON 格式记录。 例如，下面是一个 **池创建事件** 样本的正文：

```json
{
    "id": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Standard_F1s",
    "imageType": "VirtualMachineConfiguration",
    "cloudServiceConfiguration": {
        "osFamily": "3",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "virtualMachineConfiguration": {
          "imageReference": {
            "publisher": " ",
            "offer": " ",
            "sku": " ",
            "version": " "
          },
          "nodeAgentId": " "
        },
    "resizeTimeout": "300000",
    "targetDedicatedNodes": 2,
    "targetLowPriorityNodes": 2,
    "taskSlotsPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoScale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

Batch 服务发出的服务日志事件包括以下各项：

- [池创建](batch-pool-create-event.md)
- [池删除启动](batch-pool-delete-start-event.md)
- [池删除完成](batch-pool-delete-complete-event.md)
- [池调整大小启动](batch-pool-resize-start-event.md)
- [池调整大小完成](batch-pool-resize-complete-event.md)
- [池自动缩放](batch-pool-autoscale-event.md)
- [任务启动](batch-task-start-event.md)
- [任务完成](batch-task-complete-event.md)
- [任务失败](batch-task-fail-event.md)
- [任务计划失败](batch-task-schedule-fail-event.md)

## <a name="next-steps"></a>后续步骤

- 了解适用于生成批处理解决方案的[批处理 API 和工具](batch-apis-tools.md)。
- 详细了解如何[监视 Batch 解决方案](monitoring-overview.md)。
