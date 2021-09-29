---
title: 审核 Azure Sentinel 查询和活动 | Microsoft Docs
description: 本文介绍了如何审核在 Azure Sentinel 中执行的查询和活动。
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
ms.openlocfilehash: 4378065a8d1a91f9051c04dbb71a90e2a4ab862c
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124777386"
---
# <a name="audit-azure-sentinel-queries-and-activities"></a>审核 Azure Sentinel 查询和活动

本文介绍了如何按照特定要求（例如，安全运营中心 (SOC) 工作区中的内部和外部合规性要求）来查看在 Azure Sentinel 工作区中运行的查询和执行的活动的审核数据。

Azure Sentinel 提供对下列项的访问：

- AzureActivity 表，提供在 Azure Sentinel 中执行的所有操作（例如编辑警报规则）的相关详细信息。 AzureActivity 表不记录特定的查询数据。 有关详细信息，请参阅[通过 Azure 活动日志进行审核](#auditing-with-azure-activity-logs)。

- LAQueryLogs 表，提供在 Log Analytics 中运行的查询（包括从 Azure Sentinel 运行的查询）的相关详细信息。 有关详细信息，请参阅[通过 LAQueryLogs 进行审核](#auditing-with-laquerylogs)。

> [!TIP]
> 除了本文所述的手动查询之外，Azure Sentinel 还提供了一个内置的工作簿，用于帮助你审核 SOC 环境中的活动。
>
> 在 Azure Sentinel“工作簿”区域中，搜索“工作区审核”工作簿。



## <a name="auditing-with-azure-activity-logs"></a>通过 Azure 活动日志进行审核

Azure Sentinel 的审核日志是在 [Azure 活动日志](../azure-monitor/essentials/platform-logs-overview.md)中维护的，其中，AzureActivity 表包含在 Azure Sentinel 工作区中执行的所有操作。

使用 Azure Sentinel 审核 SOC 环境中的活动时，可以使用 AzureActivity 表。

若要查询 AzureActivity 表，请执行以下操作：

1. 连接 [Azure 活动](./data-connectors-reference.md#azure-activity)数据源，开始将审核事件流式传输到“日志”屏幕中的一个名为 AzureActivity 的新表。

1. 然后，使用 KQL 查询数据，就像对任何其他表进行查询一样。

    AzureActivity 表包含来自许多服务（包括 Azure Sentinel）的数据。 若只筛选来自 Azure Sentinel 的数据，请通过以下代码启动查询：

    ```kql
     AzureActivity
    | where OperationNameValue startswith "MICROSOFT.SECURITYINSIGHTS"
    ```

    例如，若要查明谁是最后编辑特定分析规则的用户，请使用以下查询（将 `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` 替换为你要检查的规则的规则 ID）：

    ```kusto
    AzureActivity
    | where OperationNameValue startswith "MICROSOFT.SECURITYINSIGHTS/ALERTRULES/WRITE"
    | where Properties contains "alertRules/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    | project Caller , TimeGenerated , Properties
    ```

向查询添加更多参数，以便进一步探索 AzureActivities 表，具体取决于你需要报告的内容。 以下各部分提供了使用 AzureActivity 表数据进行审核时要使用的其他示例查询。 

有关详细信息，请参阅 [Azure 活动日志中包含的 Azure Sentinel 数据](#azure-sentinel-data-included-in-azure-activity-logs)。

### <a name="find-all-actions-taken-by-a-specific-user-in-the-last-24-hours"></a>查找特定用户在过去 24 小时内执行的所有操作

以下 AzureActivity 表查询列出在过去 24 小时内由特定 Azure AD 用户执行的所有操作。

```kql
AzureActivity
| where OperationNameValue contains "SecurityInsights"
| where Caller == "[AzureAD username]"
| where TimeGenerated > ago(1d)
```

### <a name="find-all-delete-operations"></a>查找所有删除操作

以下 AzureActivity 表查询列出在 Azure Sentinel 工作区中执行的所有删除操作。

```kql
AzureActivity
| where OperationNameValue contains "SecurityInsights"
| where OperationName contains "Delete"
| where ActivityStatusValue contains "Succeeded"
| project TimeGenerated, Caller, OperationName
``` 


### <a name="azure-sentinel-data-included-in-azure-activity-logs"></a>Azure 活动日志中包含的 Azure Sentinel 数据
 
Azure Sentinel 的审核日志在 [Azure 活动日志](../azure-monitor/essentials/platform-logs-overview.md)中维护，包括以下类型的信息：

|操作  |信息类型  |
|---------|---------|
|**创建时间**     |警报规则 <br> 案例注释 <br>事件注释 <br>保存的搜索<br>播放列表    <br>工作簿     |
|**已删除**     |警报规则 <br>书签 <br>数据连接器 <br>事故 <br>保存的搜索 <br>设置 <br>威胁情报报告 <br>播放列表      <br>工作簿 <br>工作流  |
|**已更新**     |  警报规则<br>书签 <br> 案例 <br> 数据连接器 <br>事故 <br>事件注释 <br>威胁情报报告 <br> 工作簿 <br>工作流       |
|     |         |

你还可以使用 Azure 活动日志来检查用户授权和许可证。 

例如，下表列出了在 Azure 活动日志中找到的选定操作以及从中拉取日志数据的特定资源。

|操作名称|    资源类型|
|----|----|
|创建或更新工作簿  |Microsoft.Insights/workbooks|
|删除工作簿    |Microsoft.Insights/workbooks|
|设置工作流   |Microsoft.Logic/workflows|
|删除工作流    |Microsoft.Logic/workflows|
|创建保存的搜索    |Microsoft.OperationalInsights/workspaces/savedSearches|
|删除已保存的搜索    |Microsoft.OperationalInsights/workspaces/savedSearches|
|更新警报规则 |Microsoft.SecurityInsights/alertRules|
|删除警报规则 |Microsoft.SecurityInsights/alertRules|
|更新警报规则响应操作 |Microsoft.SecurityInsights/alertRules/actions|
|删除警报规则响应操作 |Microsoft.SecurityInsights/alertRules/actions|
|更新书签   |Microsoft.SecurityInsights/bookmarks|
|删除书签   |Microsoft.SecurityInsights/bookmarks|
|更新案例   |Microsoft.SecurityInsights/Cases|
|更新案例调查  |Microsoft.SecurityInsights/Cases/investigations|
|创建案例注释   |Microsoft.SecurityInsights/Cases/comments|
|更新数据连接器 |Microsoft.SecurityInsights/dataConnectors|
|删除数据连接器 |Microsoft.SecurityInsights/dataConnectors|
|更新设置    |Microsoft.SecurityInsights/settings|
| | |

有关详细信息，请参阅 [Azure 活动日志事件架构](../azure-monitor/essentials/activity-log-schema.md)。


## <a name="auditing-with-laquerylogs"></a>通过 LAQueryLogs 进行审核

LAQueryLogs 表提供了在 Log Analytics 中运行的日志查询的详细信息。 由于 Log Analytics 用作 Azure Sentinel 的基础数据存储，因此你可以对系统进行配置以在 Azure Sentinel 工作区中收集 LAQueryLogs 数据。

LAQueryLogs 数据包含如下信息：

- 查询运行时间
- 谁在 Log Analytics 中运行了查询
- 在 Log Analytics 中运行查询时所使用的工具，例如 Azure Sentinel
- 查询文本本身
- 每个查询运行的性能数据

> [!NOTE]
> - LAQueryLogs 表仅包含已在 Azure Sentinel 的“日志”边栏选项卡中运行的查询。 它不包含通过调查图或 Azure Sentinel 的“搜寻”页按照计划的分析规则运行的查询。
> - 从运行查询到在 LAQueryLogs 表中填充数据之间可能会有一个短暂的延迟。 建议等待大约 5 分钟，然后再在 LAQueryLogs 表中查询审核数据。
>


若要查询 LAQueryLogs 表，请执行以下操作：

1. 默认情况下，Log Analytics 工作区中未启用 LAQueryLogs 表。 在 Azure Sentinel 中进行审核时，若要使用 LAQueryLogs 数据，请先在 Log Analytics 工作区的“诊断设置”区域中启用 LAQueryLogs。

    有关详细信息，请参阅[在 Azure Monitor 日志中审核查询](../azure-monitor/logs/query-audit.md)。


1. 然后，使用 KQL 查询数据，就像对任何其他表进行查询一样。

    例如，下面的查询显示过去一周内每天运行的查询数：

    ```kql
    LAQueryLogs
    | where TimeGenerated > ago(7d)
    | summarize events_count=count() by bin(TimeGenerated, 1d)
    ```

以下各部分介绍了使用 Azure Sentinel 审核 SOC 环境中的活动时，要对 LAQueryLogs 表运行的更多示例查询。

### <a name="the-number-of-queries-run-where-the-response-wasnt-ok"></a>响应不是“正常”的已运行查询数

以下 LAQueryLogs 表查询显示收到的 HTTP 响应不是“200 正常”的已运行查询的数目。 例如，此数目将包括无法运行的查询。

```kql
LAQueryLogs
| where ResponseCode != 200 
| count 
```

### <a name="show-users-for-cpu-intensive-queries"></a>显示 CPU 密集型查询的用户

以下 LAQueryLogs 表查询根据使用的 CPU 和查询时间长度列出运行的 CPU 密集型查询最多的用户。

```kql
LAQueryLogs
|summarize arg_max(StatsCPUTimeMs, *) by AADClientId
| extend User = AADEmail, QueryRunTime = StatsCPUTimeMs
| project User, QueryRunTime, QueryText
| order by QueryRunTime desc
```

### <a name="show-users-who-ran-the-most-queries-in-the-past-week"></a>显示过去一周内运行查询最多的用户

以下 LAQueryLogs 表查询列出最近一周内运行查询最多的用户。

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

你可能需要使用 Azure Sentinel 审核资源来创建主动警报。

例如，如果你的 Azure Sentinel 工作区中有敏感的表，请使用以下查询在这些表每次被查询时通知你：

```kql
LAQueryLogs
| where QueryText contains "[Name of sensitive table]"
| where TimeGenerated > ago(1d)
| extend User = AADEmail, Query = QueryText
| project User, Query
```


## <a name="monitor-azure-sentinel-with-workbooks-rules-and-playbooks"></a>使用工作簿、规则和 playbook 监视 Azure Sentinel

使用 Azure Sentinel 自己的功能来监视 Azure Sentinel 中发生的事件和操作。

- 使用工作簿进行监视。 已生成以下工作簿来监视工作区活动：

    - 工作区审核。 包括的信息涉及环境中的哪些用户正在执行操作、他们执行了哪些操作，等等。
    - 分析效率。 了解在使用哪些分析规则、哪些 MITRE 技巧最受青睐，以及从规则中生成哪些事件。
    - 安全操作效率。 提供 SOC 团队绩效、已打开事件、已关闭事件等方面的指标。 此工作簿可用于显示团队绩效，并突出显示任何可能缺少的需要注意的方面。
    - 数据收集运行状况监视。 帮助监视是否有已停滞的或已停止的引入。 

    有关详细信息，请参阅[常用的 Azure Sentinel 工作簿](top-workbooks.md)。

- 监视引入延迟。  如果担心引入延迟，请[在分析规则中设置一个变量](https://techcommunity.microsoft.com/t5/azure-sentinel/handling-ingestion-delay-in-azure-sentinel-scheduled-alert-rules/ba-p/2052851)来表示延迟。 

    例如，以下分析规则有助于确保结果不包含重复项，并且在运行规则时日志不会缺失：

    ```kusto
    let ingestion_delay= 2min;let rule_look_back = 5min;CommonSecurityLog| where TimeGenerated >= ago(ingestion_delay + rule_look_back)| where ingestion_time() > (rule_look_back)
    -   Calculating ingestion delay
    CommonSecurityLog| extend delay = ingestion_time() - TimeGenerated| summarize percentiles(delay,95,99) by DeviceVendor, DeviceProduct
    ```

    有关详细信息，请参阅[在 Azure Sentinel 中使用自动化规则自动处理事件](automate-incident-handling-with-automation-rules.md)。

- 使用[连接器运行状况推送通知解决方案](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Send-ConnectorHealthStatus) playbook 监视数据连接器运行状况，看是否有已停滞的或已停止的引入，并在连接器停止收集数据或计算机停止报告时发送通知。

## <a name="next-steps"></a>后续步骤

在 Azure Sentinel 中，使用“工作区审核”工作簿审核 SOC 环境中的活动。

有关详细信息，请参阅[可视化和监视数据](monitor-your-data.md)。