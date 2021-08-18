---
title: Azure Monitor 中的 Azure SQL Analytics 解决方案 | Microsoft Docs
description: Azure SQL Analytics 解决方案可帮助你管理 Azure SQL 数据库
ms.topic: conceptual
author: danimir
ms.author: danil
ms.date: 09/19/2020
ms.reviewer: carlrab
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 5a8227b862328f1d1f7f282b7d89febaa0a66040
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122178230"
---
# <a name="monitor-azure-sql-database-using-azure-sql-analytics-preview"></a>使用 Azure SQL Analytics（预览版）监视 Azure SQL 数据库

![Azure SQL Analytics 符号](./media/azure-sql/azure-sql-symbol.png)

Azure SQL Analytics 是一种高级云监视解决方案，用于在单个视图中跨多个订阅大规模监视所有 Azure SQL 数据库的性能。 Azure SQL Analytics 使用内置的智能收集和直观显示关键性能指标，以进行性能故障排除。

使用这些收集的指标，可以创建自定义监视规则和警报。 Azure SQL Analytics 可帮助确定应用程序堆栈的每个层的问题。 它使用 Azure 诊断指标和 Azure Monitor 视图，在单个 Log Analytics 工作区中呈现有关所有 Azure SQL 数据库的数据。 Azure Monitor 可帮助用户收集、关联和可视化结构化和非结构化数据。

有关使用 Azure SQL Analytics 解决方案的实践概述和典型使用方案，请观看嵌入视频：

>[!VIDEO https://www.youtube.com/embed/j-NDkN4GIzg]
>

## <a name="connected-sources"></a>连接的源

Azure SQL Analytics 是一种完全的云监视解决方案，它支持流式传输所有 Azure SQL 数据库的诊断遥测数据。 由于 Azure SQL Analytics 不使用代理连接到 Azure Monitor，因此它不支持对本地或虚拟机中托管的 SQL Server 进行监视。

| 连接的源 | 支持 | 说明 |
| --- | --- | --- |
| [诊断设置](../essentials/diagnostic-settings.md) | **是** | Azure 指标和日志数据将由 Azure 直接发送到 Azure Monitor 日志。 |
| [Azure 存储帐户](../essentials/resource-logs.md#send-to-log-analytics-workspace) | 否 | Azure Monitor 不从存储帐户中读取数据。 |
| [Windows 代理](../agents/agent-windows.md) | 否 | Azure SQL Analytics 不使用直接 Windows 代理。 |
| [Linux 代理](../vm/monitor-virtual-machine.md) | 否 | Azure SQL Analytics 不使用直接 Linux 代理。 |
| [System Center Operations Manager 管理组](../agents/om-agents.md) | 否 | Azure SQL Analytics 不使用从 Operations Manager 代理到 Azure Monitor 的直接连接。 |

## <a name="azure-sql-analytics-options"></a>Azure SQL Analytics 选项

下表概述了两个 Azure SQL Analytics 仪表板版本支持的透视图，一个版本适用于 Azure SQL 数据库，另一个版本适用于 Azure SQL 托管实例数据库。

| “Azure SQL Analytics”选项 | 说明 | SQL 数据库支持 | SQL 托管实例支持 |
| --- | ------- | ----- | ----- |
| 资源（按类型） | 对监视的所有资源进行计数的透视。 | 是 | 是 |
| 洞察力 | 提供对性能智能见解的分层向下钻取。 | 是 | 是 |
| 错误 | 提供对数据库上发生的 SQL 错误的分层向下钻取。 | 是 | 是 |
| 超时 | 提供对数据库上发生的 SQL 超时的分层向下钻取。 | 是 | 否 |
| 阻止 | 提供对数据库上发生的 SQL 阻止的分层向下钻取。 | 是 | 否 |
| 数据库等待 | 提供对数据库级别上的 SQL 等待统计信息的分层向下钻取。 包含总等待时间汇总和每个类型的等待时间。 |是 | 否 |
| 查询持续时间 | 提供对查询执行统计信息的分层向下钻取，例如查询持续时间、CPU 使用情况、数据 IO 使用情况和日志 IO 使用情况。 | 是 | 是 |
| 查询等待 | 按等待类型提供对查询等待统计信息的分层向下钻取。 | 是 | 是 |

## <a name="configuration"></a>配置

使用[从解决方案库中添加 Azure Monitor 解决方案](./solutions.md)中所述的流程，将 Azure SQL Analytics（预览）添加到 Log Analytics 工作区。

### <a name="configure-azure-sql-database-to-stream-diagnostics-telemetry"></a>配置 Azure SQL 数据库以流式传输诊断遥测数据

在工作区中创建 Azure SQL Analytics 解决方案后，需要配置每个你希望进行监视以将其诊断遥测数据流式传输到 Azure SQL Analytics 的资源。 请遵循此页面上的详细说明：

- 为数据库启用 Azure 诊断，以便[将诊断遥测数据流式传输到 Azure SQL Analytics](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md)。

上述页面还提供了有关启用通过单个 Azure SQL Analytics 工作区，在单个视图中监视多个 Azure 订阅的支持。

## <a name="using-azure-sql-analytics"></a>使用 Azure SQL Analytics

将 Azure SQL Analytics 添加到工作区时，“Azure SQL Analytics”磁贴也会添加到工作区并显示在“概览”中。 选择“查看摘要”链接可加载磁贴内容。

![Azure SQL Analytics“摘要”磁贴](./media/azure-sql/azure-sql-sol-tile-01.png)

加载后，该磁贴会显示 SQL 数据库中的数据库和弹性池的数量，以及 SQL 托管实例中的实例和实例数据库，Azure SQL Analytics 接收的是诊断遥测数据。

![“Azure SQL Analytics”磁贴](./media/azure-sql/azure-sql-sol-tile-02.png)

Azure SQL Analytics 提供了两个单独的视图，一个用于监视 SQL 数据库，另一个用于监视 SQL 托管实例。

若要查看 SQL 数据库的 Azure SQL Analytics 监视仪表板，请单击磁贴的上半部分。 若要查看托管实例和托管实例数据库的 Azure SQL Analytics 监视仪表板，请单击磁贴的下半部分。

### <a name="viewing-azure-sql-analytics-data"></a>查看 Azure SQL Analytics 数据

该仪表板包含通过不同透视监视的所有数据库的概览。 要使不同透视起效，必须允许将有关 SQL 资源的适当指标或日志流式传输到 Log Analytics 工作区。

如果某些指标或日志未流式传输到 Azure Monitor，则不会使用监视信息填充 Azure SQL Analytics 中的磁贴。

### <a name="sql-database-view"></a>SQL 数据库视图

选择数据库的 Azure SQL Analytics 磁贴后，会显示监视仪表板。

![显示监视仪表板的屏幕截图。](./media/azure-sql/azure-sql-sol-overview.png)

选择任意磁贴，打开特定透视的向下钻取报告。 选择透视后，可看到向下钻取报表。

![显示向下钻取报表的屏幕截图。](./media/azure-sql/azure-sql-sol-metrics.png)

此视图中的每个透视都提供了订阅、服务器、弹性池和数据库级别的汇总。 此外，每个透视在右侧都显示了特定于透视的报表。 从列表选择订阅、服务器、池或数据库会继续向下钻取。

### <a name="sql-managed-instance-view"></a>SQL 托管实例视图

选择数据库的 Azure SQL Analytics 磁贴后，会显示监视仪表板。

![Azure SQL Analytics 概述](./media/azure-sql/azure-sql-sol-overview-mi.png)

选择任意磁贴，打开特定透视的向下钻取报告。 选择透视后，可看到向下钻取报表。

选择“SQL 托管实例”视图会显示有关实例利用率及实例数据库的详细信息，以及有关在整个托管实例中执行的查询的遥测数据。

![Azure SQL Analytics 超时](./media/azure-sql/azure-sql-sol-metrics-mi.png)

### <a name="intelligent-insights-report"></a>智能见解报告

使用 Azure SQL 数据库 [Intelligent Insights](../../azure-sql/database/intelligent-insights-overview.md)，可以了解所有 Azure SQL 数据库的性能状况。 可通过见解透视，对收集的所有智能见解进行可视化和访问。

![Azure SQL Analytics 见解](./media/azure-sql/azure-sql-sol-insights.png)

### <a name="elastic-pools-and-database-reports"></a>弹性池和数据库报表

弹性池和数据库都有其特定的报表，这些报表显示特定时间内对该资源收集的所有数据。

![Azure SQL Analytics 数据库](./media/azure-sql/azure-sql-sol-database.png)

![Azure SQL 弹性池](./media/azure-sql/azure-sql-sol-pool.png)

### <a name="query-reports"></a>查询报表

通过查询持续时间和查询等待透视，可使用查询报表关联任何查询的性能。 此报表比较不同数据库之间的查询性能，从而可轻松查找良好执行所选查询的数据库以及查询缓慢的数据库。

![Azure SQL Analytics 查询](./media/azure-sql/azure-sql-sol-queries.png)

## <a name="permissions"></a>权限

若要使用 Azure SQL Analytics，至少需要为用户授予 Azure 中的“读者”角色的权限。 但是，此角色不允许用户查看查询文本，或执行任何自动优化操作。 在 Azure 中，可以最大程度地使用 Azure SQL Analytics 的更自由角色包括“所有者”、“参与者”、“SQL DB 参与者”或“SQL Server 参与者”。 可能还需要考虑在门户中创建一个自定义角色，该角色只拥有 Azure SQL Analytics 的特定使用权限，而无权管理其他资源。

### <a name="creating-a-custom-role-in-portal"></a>在门户中创建自定义角色

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

认识到某些组织会在 Azure 中强制实施严格的权限控制，请查看以下 PowerShell 脚本，以便在 Azure 门户中创建“SQL Analytics 监视操作员”自定义角色，该角色拥有最大程度地使用 Azure SQL Analytics 所需的最低读取和写入权限。

将以下脚本中的“{SubscriptionId}”替换为自己的 Azure 订阅 ID，并在以 Azure 中“所有者”或“参与者”角色的身份登录后执行该脚本。

   ```powershell
    Connect-AzAccount
    Select-AzSubscription {SubscriptionId}
    $role = Get-AzRoleDefinition -Name Reader
    $role.Name = "SQL Analytics Monitoring Operator"
    $role.Description = "Lets you monitor database performance with Azure SQL Analytics as a reader. Does not allow change of resources."
    $role.IsCustom = $true
    $role.Actions.Add("Microsoft.SQL/servers/databases/read");
    $role.Actions.Add("Microsoft.SQL/servers/databases/topQueries/queryText/*");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/read");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/write");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/recommendedActions/read");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/recommendedActions/write");
    $role.Actions.Add("Microsoft.Sql/servers/databases/automaticTuning/read");
    $role.Actions.Add("Microsoft.Sql/servers/databases/automaticTuning/write");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/read");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/write");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/recommendedActions/read");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/recommendedActions/write");
    $role.Actions.Add("Microsoft.Resources/deployments/write");
    $role.AssignableScopes = "/subscriptions/{SubscriptionId}"
    New-AzRoleDefinition $role
   ```

创建新角色后，请将此角色分配给你需要向其授予自定义权限，使其能够使用 Azure SQL Analytics 的每个用户。

## <a name="analyze-data-and-create-alerts"></a>分析数据和创建警报

Azure SQL Analytics 中的数据分析基于自定义查询和报告的 [Log Analytics 语言](../logs/get-started-queries.md)。 在[可用的指标和日志](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md#metrics-and-logs-available)中查看从数据库资源收集的、用于自定义查询的数据的说明。

若要在 Azure SQL Analytics 中实现自动化警报，必须编写一个可在满足条件时触发警报的 Log Analytics 查询。 查看有关 Log Analytics 查询的以下几个示例，可以基于这些示例在 Azure SQL Analytics 中设置警报。

### <a name="creating-alerts-for-azure-sql-database"></a>针对 Azure SQL 数据库创建警报

可以使用来自 Azure SQL 数据库资源的数据轻松[创建警报](../alerts/alerts-metric.md)。 以下是部分有用的可用于日志警报的[日志查询](../logs/log-query-overview.md)：

#### <a name="high-cpu"></a>CPU 使用率过高

```
AzureMetrics
| where ResourceProvider=="MICROSOFT.SQL"
| where ResourceId contains "/DATABASES/"
| where MetricName=="cpu_percent"
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

> [!NOTE]
>
> - 设置此警报的前提要求是受监视的数据库将基本指标流式传输到 Azure SQL Analytics。
> - 若要改为获取高 DTU 结果，请将 MetricName 值 cpu_percent 替换为 dtu_consumption_percent。

#### <a name="high-cpu-on-elastic-pools"></a>弹性池的高 CPU

```
AzureMetrics
| where ResourceProvider=="MICROSOFT.SQL"
| where ResourceId contains "/ELASTICPOOLS/"
| where MetricName=="cpu_percent"
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

> [!NOTE]
>
> - 设置此警报的前提要求是受监视的数据库将基本指标流式传输到 Azure SQL Analytics。
> - 若要改为获取高 DTU 结果，请将 MetricName 值 cpu_percent 替换为 dtu_consumption_percent。

#### <a name="storage-in-average-above-95-in-the-last-1-hr"></a>存储在过去 1小时的平均值超过 95%

```
let time_range = 1h;
let storage_threshold = 95;
AzureMetrics
| where ResourceId contains "/DATABASES/"
| where MetricName == "storage_percent"
| summarize max_storage = max(Average) by ResourceId, bin(TimeGenerated, time_range)
| where max_storage > storage_threshold
| distinct ResourceId
```

> [!NOTE]
>
> - 设置此警报的前提要求是受监视的数据库将基本指标流式传输到 Azure SQL Analytics。
> - 此查询要求将警报规则设置为当存在来自查询的结果时（> 0 个结果，表示某些数据库上存在此条件）触发。 输出是在所定义的 time_range 内高于 storage_threshold 的数据库资源的列表。
> - 输出是在所定义的 time_range 内高于 storage_threshold 的数据库资源的列表。

#### <a name="alert-on-intelligent-insights"></a>Intelligent insights 上的警报

> [!IMPORTANT]
> 如果数据库正常运行，且未生成智能见解，则此查询将失败并出现错误消息：无法解析名为“rootCauseAnalysis_s”的标量表达式。 对于数据库不存在任何智能见解的所有情况，都会执行此行为。

```
let alert_run_interval = 1h;
let insights_string = "hitting its CPU limits";
AzureDiagnostics
| where Category == "SQLInsights" and status_s == "Active"
| where TimeGenerated > ago(alert_run_interval)
| where rootCauseAnalysis_s contains insights_string
| distinct ResourceId
```

> [!NOTE]
>
> - 设置此警报的前提要求是受监视的数据库将 SQLInsights 诊断日志流式传输到 Azure SQL Analytics。
> - 此查询要求将警报规则设置为以与 alert_run_interval 相同的频率运行以避免重复的结果。 此规则应当设置为当存在来自查询的结果时（> 0 个结果）触发。
> - 请自定义 alert_run_interval 来指定时间范围以检查在配置为将 SQLInsights 日志流式传输到 Azure SQL Analytics 的数据库上是否发生了此条件。
> - 请自定义 insights_string 来捕获 Insights 根本原因分析文本的输出。 这是可以从现有见解使用的 Azure SQL Analytics 的 UI 中显示的同一文本。 另外，还可以使用下面的查询来查看在你的订阅上生成的所有见解的文本。 可以使用查询的输出来获取用于在 Insights 上设置警报的不同字符串。

```
AzureDiagnostics
| where Category == "SQLInsights" and status_s == "Active"
| distinct rootCauseAnalysis_s
```

### <a name="creating-alerts-for-sql-managed-instance"></a>针对 SQL 托管实例创建警报

#### <a name="storage-is-above-90"></a>存储超过90%

```
let storage_percentage_threshold = 90;
AzureDiagnostics
| where Category =="ResourceUsageStats"
| summarize (TimeGenerated, calculated_storage_percentage) = arg_max(TimeGenerated, todouble(storage_space_used_mb_s) *100 / todouble (reserved_storage_mb_s))
   by ResourceId
| where calculated_storage_percentage > storage_percentage_threshold
```

> [!NOTE]
>
> - 设置此警报的先决条件是，已在 Azure SQL Analytics 中为受监视的托管实例启用了 ResourceUsageStats 日志的流式传输。
> - 此查询要求将警报规则设置为当存在来自查询的结果时（> 0 个结果，表示托管实例满足该条件）触发。 输出是托管实例上的存储消耗量百分比。

#### <a name="cpu-average-consumption-is-above-95-in-the-last-1-hr"></a>CPU 平均消耗在过去 1 小时内超过 95%

```
let cpu_percentage_threshold = 95;
let time_threshold = ago(1h);
AzureDiagnostics
| where Category == "ResourceUsageStats" and TimeGenerated > time_threshold
| summarize avg_cpu = max(todouble(avg_cpu_percent_s)) by ResourceId
| where avg_cpu > cpu_percentage_threshold
```

> [!NOTE]
>
> - 设置此警报的先决条件是，已在 Azure SQL Analytics 中为受监视的托管实例启用了 ResourceUsageStats 日志的流式传输。
> - 此查询要求将警报规则设置为当存在来自查询的结果时（> 0 个结果，表示托管实例满足该条件）触发。 输出是在定义的期间内在托管实例上消耗的 CPU 的平均利用率百分比。

### <a name="pricing"></a>定价

虽然 Azure SQL Analytics 可以免费使用，但超出每月分配的免费数据引入单位的诊断遥测数据消耗量仍适用，请参阅 [Log Analytics 定价](https://azure.microsoft.com/pricing/details/monitor)。 提供的免费数据引入单位每月可免费监控多个数据库。 与空闲数据相比，工作负载较重的活动数据库越多，将引入的数据越多。 选择 Azure SQL Analytics 导航菜单上的 OMS 工作区，然后选择使用情况和预估成本，即可轻松监视 Azure SQL Analytics 中的数据引入消耗量。

## <a name="next-steps"></a>后续步骤

- 使用 [Azure Monitor 中的日志查询](../logs/log-query-overview.md)查看详细的 Azure SQL 数据。
- [创建自己的仪表板](../visualize/tutorial-logs-dashboards.md)，显示 Azure SQL 数据。
- 发生特定 Azure SQL 事件时[创建警报](../alerts/alerts-overview.md)。