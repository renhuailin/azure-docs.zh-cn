---
title: 审核 Azure Sentinel 查询和活动 |Microsoft Docs
description: 本文介绍如何审核在 Azure Sentinel 中执行的查询和活动。
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.assetid: 9b4c8e38-c986-4223-aa24-a71b01cb15ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/03/2021
ms.author: bagol
ms.openlocfilehash: a8ea32d84da521c8a1af926c6cb5e26bc2738de2
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102054512"
---
# <a name="audit-azure-sentinel-queries-and-activities"></a>审核 Azure Sentinel 查询和活动

本文介绍如何查看 Azure Sentinel 工作区中执行的查询运行和活动的审核数据，如 (SOC) 工作区中的内部和外部符合性要求。

Azure Sentinel 提供对以下内容的访问：

- **AzureActivity** 表，提供有关 Azure Sentinel 中执行的所有操作（如编辑警报规则）的详细信息。 **AzureActivity** 表不记录特定的查询数据。 有关详细信息，请参阅 [通过 Azure 活动日志进行审核](#auditing-with-azure-activity-logs)。

- **LAQueryLogs** 表，提供有关查询 Log Analytics 运行的详细信息，包括从 Azure Sentinel 运行的查询。 有关详细信息，请参阅 [LAQueryLogs 的审核](#auditing-with-laquerylogs)。

> [!TIP]
> 除了本文中所述的手动查询，Azure Sentinel 还提供了一个内置的工作簿，可帮助您审核 SOC 环境中的活动。
>
> 在 "Azure Sentinel **工作簿** " 区域中，搜索 **工作区审核** 工作簿。



## <a name="auditing-with-azure-activity-logs"></a>通过 Azure 活动日志进行审核

Azure Sentinel 的审核日志将保留在 [Azure 活动日志](../azure-monitor/essentials/platform-logs-overview.md)中，其中， **AzureActivity** 表包含在 azure Sentinel 工作区中执行的所有操作。

使用 Azure Sentinel 审核 SOC 环境中的活动时，可以使用 **AzureActivity** 表。

**查询 AzureActivity 表**：

1. 连接 [Azure 活动](connect-azure-activity.md) 数据源，开始将审核事件流式传输到 " **日志** " 屏幕（称为 AzureActivity）中的新表中。

1. 然后，使用 KQL 查询数据，就像对任何其他表一样。

    **AzureActivity** 表包含来自多个服务（包括 Azure Sentinel）的数据。 若要仅在 Azure Sentinel 中筛选数据，请在下面的代码中启动查询：

    ```kql
     AzureActivity
    | where OperationNameValue startswith "MICROSOFT.SECURITYINSIGHTS"
    ```

    例如，若要找出上次编辑特定分析规则的用户，请使用以下查询 (替换 `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` 为要检查) 的规则的规则 ID：

    ```kusto
    AzureActivity
    | where OperationNameValue startswith "MICROSOFT.SECURITYINSIGHTS/ALERTRULES/WRITE"
    | where Properties contains "alertRules/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    | project Caller , TimeGenerated , Properties
    ```

向查询添加更多参数，以便进一步探索 **AzureActivities** 表，具体取决于需要报告的内容。 以下各节提供了使用 **AzureActivity** 表数据进行审核时要使用的其他示例查询。 

有关详细信息，请参阅 [Azure 活动日志中包含的 Azure Sentinel 数据](#azure-sentinel-data-included-in-azure-activity-logs)。

### <a name="find-all-actions-taken-by-a-specific-user-in-the-last-24-hours"></a>查找特定用户在过去24小时内执行的所有操作

以下 **AzureActivity** 表查询列出了在过去24小时内由特定 Azure AD 用户执行的所有操作。

```kql
AzureActivity
| where OperationNameValue contains "SecurityInsights"
| where Caller == "[AzureAD username]"
| where TimeGenerated > ago(1d)
```

### <a name="find-all-delete-operations"></a>查找所有删除操作

以下 **AzureActivity** 表查询列出了在 Azure Sentinel 工作区中执行的所有删除操作。

```kql
AzureActivity
| where OperationNameValue contains "SecurityInsights"
| where OperationName contains "Delete"
| where ActivityStatusValue contains "Succeeded"
| project TimeGenerated, Caller, OperationName
``` 


### <a name="azure-sentinel-data-included-in-azure-activity-logs"></a>Azure 活动日志中包含的 azure Sentinel 数据
 
Azure Sentinel 的审核日志将保留在 [Azure 活动日志](../azure-monitor/essentials/platform-logs-overview.md)中，并包括以下类型的信息：

|Operation  |信息类型  |
|---------|---------|
|**创建时间**     |警报规则 <br> 案例注释 <br>事件注释 <br>保存的搜索<br>Watchlists    <br>工作簿     |
|**已删除**     |警报规则 <br>书签 <br>数据连接器 <br>事故 <br>保存的搜索 <br>设置 <br>威胁情报报表 <br>Watchlists      <br>工作簿 <br>工作流  |
|**已更新**     |  警报规则<br>书签 <br> 案例 <br> 数据连接器 <br>事故 <br>事件注释 <br>威胁情报报表 <br> 工作簿 <br>工作流       |
|     |         |

你还可以使用 Azure 活动日志来检查用户的授权和许可证。 

例如，下表列出了在 Azure 活动日志中找到的包含日志数据的特定资源的所选操作。

|操作名称|    资源类型|
|----|----|
|创建或更新工作簿  |Microsoft Insights/工作簿|
|删除工作簿    |Microsoft Insights/工作簿|
|设置工作流   |Microsoft.Logic/workflows|
|删除工作流    |Microsoft.Logic/workflows|
|创建保存的搜索    |Microsoft.operationalinsights/工作区/savedSearches|
|删除保存的搜索    |Microsoft.operationalinsights/工作区/savedSearches|
|更新警报规则 |Microsoft.SecurityInsights/alertRules|
|删除警报规则 |Microsoft.SecurityInsights/alertRules|
|更新警报规则响应操作 |SecurityInsights/alertRules/actions|
|删除警报规则响应操作 |SecurityInsights/alertRules/actions|
|更新书签   |Microsoft.SecurityInsights/bookmarks|
|删除书签   |Microsoft.SecurityInsights/bookmarks|
|更新事例   |SecurityInsights/事例|
|更新案例调查  |SecurityInsights/案例/调查|
|创建案例注释   |SecurityInsights/案例/注释|
|更新数据连接器 |Microsoft.SecurityInsights/dataConnectors|
|删除数据连接器 |Microsoft.SecurityInsights/dataConnectors|
|更新设置    |Microsoft.SecurityInsights/settings|
| | |

有关详细信息，请参阅 [Azure 活动日志事件架构](/azure/azure-monitor/essentials/activity-log-schema)。


## <a name="auditing-with-laquerylogs"></a>用 LAQueryLogs 审核

**LAQueryLogs** 表提供了 Log Analytics 中的日志查询的详细信息。 由于 Log Analytics 用作 Azure Sentinel 的基础数据存储，因此可以将系统配置为收集 Azure Sentinel 工作区中的 LAQueryLogs 数据。

LAQueryLogs 数据包含如下所示的信息：

- 当查询运行时
- 谁在 Log Analytics 中运行查询
- 在 Log Analytics 中运行查询所使用的工具，如 Azure Sentinel
- 查询文本本身
- 每个查询运行的性能数据

> [!NOTE]
> - **LAQueryLogs** 表只包含已在 Azure Sentinel 的 "日志" 边栏选项卡中运行的查询。 它不包含按计划分析规则运行的查询（使用 **调查图** 或在 Azure Sentinel **搜寻** 页中）。
> - 查询运行的时间与在 **LAQueryLogs** 表中填充的数据之间可能会有短暂的延迟。 建议在 **LAQueryLogs** 表中查询审核数据，等待大约5分钟。
>


**查询 LAQueryLogs 表**：

1. 默认情况下，Log Analytics 工作区中未启用 **LAQueryLogs** 表。 若要在 Azure Sentinel 中审核时使用 **LAQueryLogs** 数据，请先在 Log Analytics 工作区的 **诊断设置** 区域启用 **LAQueryLogs** 。

    有关详细信息，请参阅 [Azure Monitor 日志中的审核查询](/azure/azure-monitor/logs/query-audit)。


1. 然后，使用 KQL 查询数据，就像对任何其他表一样。

    例如，下面的查询显示在过去一周内按天运行的查询数：

    ```kql
    LAQueryLogs
    | where TimeGenerated > ago(7d)
    | summarize events_count=count() by bin(TimeGenerated, 1d)
    ```

以下部分介绍了使用 Azure Sentinel 审核 SOC 环境中的活动时，在 **LAQueryLogs** 表上运行的更多示例查询。

### <a name="the-number-of-queries-run-where-the-response-wasnt-ok"></a>在响应不为 "确定" 的情况下运行的查询数

以下 **LAQueryLogs** 表查询显示了查询运行的次数，其中除 HTTP 响应为 **200 OK** 以外的任何内容。 例如，此编号将包括无法运行的查询。

```kql
LAQueryLogs
| where ResponseCode != 200 
| count 
```

### <a name="show-users-for-cpu-intensive-queries"></a>显示 CPU 密集型查询的用户

以下 **LAQueryLogs** 表查询根据使用的 cpu 和查询时间的长度列出运行最多 cpu 密集型查询的用户。

```kql
LAQueryLogs
|summarize arg_max(StatsCPUTimeMs, *) by AADClientId
| extend User = AADEmail, QueryRunTime = StatsCPUTimeMs
| project User, QueryRunTime, QueryText
| order by QueryRunTime desc
```

### <a name="show-users-who-ran-the-most-queries-in-the-past-week"></a>显示过去一周内运行最多查询的用户

以下 **LAQueryLogs** 表查询列出了最近一周内运行最多查询的用户。

```kql
LAQueryLogs
| where TimeGenerated > ago(7d)
| summarize events_count=count() by AADEmail
| extend UserPrincipalName = AADEmail, Queries = events_count
| join kind= leftouter (
    SigninLogs)
    on UserPrincipalName
| project UserDisplayName, UserPrincipalName, Queries
| summarize arg_max(Queries, *) by UserPrincipalName
| sort by Queries desc
```

## <a name="configuring-alerts-for-azure-sentinel-activities"></a>配置 Azure Sentinel 活动的警报

你可能想要使用 Azure Sentinel 审核资源来创建主动警报。

例如，如果 Azure Sentinel 工作区中有敏感的表，请使用以下查询在每次查询这些表时通知你：

```kql
LAQueryLogs
| where QueryText contains "[Name of sensitive table]"
| where TimeGenerated > ago(1d)
| extend User = AADEmail, Query = QueryText
| project User, Query
```


## <a name="next-steps"></a>后续步骤

在 Azure Sentinel 中，使用 **工作区审核** 工作簿审核 SOC 环境中的活动。

有关详细信息，请参阅 [教程：可视化和监视数据](tutorial-monitor-your-data.md)。
