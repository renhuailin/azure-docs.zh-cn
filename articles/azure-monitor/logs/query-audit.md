---
title: Azure Monitor 日志查询中的审核查询
description: 日志查询审核日志的详细信息，这些审核日志提供有关在 Azure Monitor 中运行的日志查询的遥测。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/03/2020
ms.openlocfilehash: 28dfac7de8e73adf577b0a13e5fbd8740b1e3b06
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102047411"
---
# <a name="audit-queries-in-azure-monitor-logs-preview"></a>Azure Monitor 日志中的审核查询（预览版）
日志查询审核日志提供有关在 Azure Monitor 中运行的日志查询的遥测。 其中包括诸如运行查询的时间、运行查询的人、使用的工具、查询文本，以及描述查询执行的性能统计信息等。


## <a name="configure-query-auditing"></a>配置查询审核
通过 Log Analytics 工作区上的[诊断设置](../essentials/diagnostic-settings.md)启用查询审核。 这样你便可以将审核数据发送到当前工作区或订阅中的任何其他工作区，发送到 Azure 事件中心以在 Azure 外部发送，或发送到 Azure 存储进行存档。 

### <a name="azure-portal"></a>Azure 门户
在以下任一位置访问 Azure 门户中 Log Analytics 工作区的诊断设置：

- 从“Azure Monitor”菜单中，选择“诊断设置”，然后找到并选择工作区 。

    [![Azure Monitor 的的诊断设置](media/query-audit/diagnostic-setting-monitor.png) ](media/query-audit/diagnostic-setting-monitor.png#lightbox) 

- 从“Log Analytics 工作区”菜单中，选择“工作区”，然后选择“诊断设置” 。

    [![Log Analytics 工作区的诊断设置](media/query-audit/diagnostic-setting-workspace.png) ](media/query-audit/diagnostic-setting-workspace.png#lightbox) 

### <a name="resource-manager-template"></a>资源管理器模板
可以从 [Log Analytics 工作区的诊断设置](../essentials/resource-manager-diagnostic-settings.md#diagnostic-setting-for-log-analytics-workspace)中获取示例资源管理器模板。

## <a name="audit-data"></a>审核数据
每次运行查询时都会创建一个审核记录。 如果将数据发送到 Log Analytics 工作区，数据将存储在名为“LAQueryLogs”的表中。 下表介绍了审核数据每条记录中的属性。

| 字段 | 说明 |
|:---|:---|
| TimeGenerated         | 提交查询时的 UTC 时间。 |
| CorrelationId         | 用于标识查询的唯一 ID。 与 Microsoft 联系以寻求帮助时可用于故障排除方案。 |
| AADObjectId           | 启动查询的用户帐户的 Azure Active Directory ID。  |
| AADTenantId           | 启动查询的用户帐户的租户 ID。  |
| AADEmail              | 启动查询的用户帐户的租户电子邮件。  |
| AADClientId           | 用于启动查询的应用程序的 ID 和已解析名称。 |
| RequestClientApp      | 用于启动查询的应用程序的已解析名称。 |
| QueryTimeRangeStart   | 为查询选择的时间范围的开始日期。 在某些情况下，例如从 Log Analytics 启动查询，并且在查询中而不是在时间选取器指定时间范围时，可能不会填充此值。 |
| QueryTimeRangeEnd     | 为查询选择的时间范围的结束日期。 在某些情况下，例如从 Log Analytics 启动查询，并且在查询中而不是在时间选取器指定时间范围时，可能不会填充此值。  |
| QueryText             | 运行的查询的文本。 |
| RequestTarget         | API URL 用于提交查询。  |
| RequestContext        | 请求对其运行查询的资源列表。 最多包含三个字符串数组：工作区、应用程序和资源。 以订阅或资源组为目标的查询将显示为“资源”。 包括 RequestTarget 暗示的目标。<br>如果可以解析每个资源的资源 ID，则将包含它们。 如果访问资源时返回错误，则可能无法解析。 在这种情况下，将使用查询中的特定文本。<br>如果查询使用不明确的名称，例如多个订阅中存在的工作区名称，则将使用此不明确的名称。 |
| RequestContextFilters | 在查询调用中指定的筛选器组。 最多包含三个可能的字符串数组：<br>- ResourceTypes - 用于限制查询范围的资源类型<br>- Workspaces - 用于限制查询的工作区列表<br>- WorkspaceRegions - 用于限制查询的工作区区域列表 |
| ResponseCode          | 提交查询时返回的 HTTP 响应代码。 |
| ResponseDurationMs    | 返回响应的时间。  |
| ResponseRowCount     | 查询返回的总行数。 |
| StatsCPUTimeMs       | 用于计算、解析和数据获取的总计算时间。 仅当查询返回状态代码 200 时填充。 |
| StatsDataProcessedKB | 为处理查询而访问的数据量。 受目标表大小、所用时间跨度、已应用筛选器和已引用列数影响。 仅当查询返回状态代码 200 时填充。 |
| StatsDataProcessedStart | 处理查询时访问最旧数据的时间。 受查询显式时间跨度和应用的筛选器的影响。 由于数据分区，这可能比显式时间跨度要大。 仅当查询返回状态代码 200 时填充。 |
| StatsDataProcessedEnd  |处理查询时访问最新数据的时间。 受查询显式时间跨度和应用的筛选器的影响。 由于数据分区，这可能比显式时间跨度要大。 仅当查询返回状态代码 200 时填充。 |
| StatsWorkspaceCount | 查询访问的工作区数。 仅当查询返回状态代码 200 时填充。 |
| StatsRegionCount | 查询访问的区域数。 仅当查询返回状态代码 200 时填充。 |

## <a name="considerations"></a>注意事项

- 只有在用户上下文中执行查询时才会记录查询。 不会记录 Azure 中的服务到服务。 此排除包含的两个主要查询集是计费计算和自动警报执行。 对于警报，只有计划的警报查询本身不会被记录；警报创建屏幕中警报的初始执行是在用户上下文中执行的，并且可以用于审核目的。 
- 性能统计信息不可用于来自 Azure 数据资源管理器代理的查询。 仍将填充这些查询的所有其他数据。
- 字符串上的 h 提示[模糊字符串字面量](/azure/data-explorer/kusto/query/scalar-data-types/string#obfuscated-string-literals)将不会影响查询审核日志。 查询将完全按照提交的方式进行捕获，而不会混淆字符串。 应确保只有具有合规性权限的用户才能使用 Log Analytics 工作区中提供的各种 Kubernetes RBAC 或 Azure RBAC 模式来查看此数据。
- 对于包含来自多个工作区的数据的查询，只能在用户有权访问的工作区中捕获查询。

## <a name="costs"></a>成本  
Azure 诊断扩展不会产生费用，但可能会产生数据引入费用。 检查与要将数据收集到其中的目标相对应的 [Azure Monitor 定价](https://azure.microsoft.com/pricing/details/monitor/)。

## <a name="next-steps"></a>后续步骤

- 详细了解[诊断设置](../essentials/diagnostic-settings.md)。
- 详细了解如何[优化日志查询](query-optimization.md)。
