---
title: 监视 Azure 容器注册表
description: 从此处开始了解如何使用 Azure Monitor 功能来监视 Azure 容器注册表
author: dlepow
ms.author: danlep
ms.topic: how-to
ms.custom: subject-monitoring
ms.service: container-registry
ms.date: 03/19/2021
ms.openlocfilehash: 1b7f8a13a7d372fe67e5ca7c235febfa52294432
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114294943"
---
# <a name="monitor-azure-container-registry"></a>监视 Azure 容器注册表

当你的关键应用程序和业务流程依赖于 Azure 资源时，你需要监视这些资源的可用性、性能和操作。 本文介绍了 Azure 容器注册表生成的监视数据，以及如何使用 Azure Monitor 的功能在这些数据的基础上进行分析和发出警报。

## <a name="monitor-overview"></a>Monitor 概述

每个注册表的 Azure 门户中的“概述”页面上均有一个显示最近资源使用情况和活动（例如推送和拉取操作）的简要视图。 这些高级别信息非常有用，但只显示少量监视数据。 

:::image type="content" source="media/monitor-service/metrics-overview.png" alt-text="注册表指标概述"::: 

## <a name="what-is-azure-monitor"></a>说明是 Azure Monitor？

Azure 容器注册表使用 [Azure Monitor](../azure-monitor/overview.md) 创建监视数据，Azure Monitor 是 Azure 中的一个完整堆栈监视服务，提供一组完整的功能来监视 Azure 资源以及其他云和本地的资源。

一开始可以阅读[使用 Azure Monitor 监视 Azure 资源](../azure-monitor/essentials/monitor-azure-resource.md)一文，其中介绍了以下概念：

- 说明是 Azure Monitor？
- 与监视相关的成本
- 监视 Azure 中收集的数据
- 配置数据收集
- Azure 中用于分析监视数据并就其发出警报的标准工具

本文的以下各部分介绍了为 Azure 容器注册表收集的特定数据，并提供了有关使用 Azure 工具配置数据收集和分析这些数据的示例。

## <a name="monitoring-data"></a>监视数据 

Azure 容器注册表收集与 [Azure 资源中的监视数据](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data)中所述的其他 Azure 资源相同的监视数据。 

有关 Azure 容器注册表创建的指标和日志的详细信息，请参阅 [Azure 容器注册表监视数据参考](monitor-service-reference.md)。

## <a name="collection-and-routing"></a>收集和路由

平台指标和活动日志会自动收集和存储，但你可以使用诊断设置将其路由到其他位置。  

在创建诊断设置并将其路由到一个或多个位置之前，不会收集和存储资源日志。

有关使用 Azure 门户、CLI 或 PowerShell 创建诊断设置的详细过程，请参阅[创建诊断设置以收集 Azure 中的平台日志和指标](../azure-monitor/essentials/diagnostic-settings.md)。 创建诊断设置时，请指定要收集的日志类别。 [Azure 容器注册表监视数据参考](monitor-service-reference.md#resource-logs)列出了 Azure 容器注册表的类别。

> [!TIP]
> 也可以通过导航到门户中的注册表来创建注册表诊断设置。 在菜单中，选择“监视”下的“诊断设置”。 

下图显示了为注册表启用诊断设置时的选项。

:::image type="content" source="media/monitor-service/diagnostic-settings.png" alt-text="容器注册表的诊断设置":::

以下部分将讨论可以收集的指标和日志。

## <a name="analyzing-metrics-preview"></a>分析指标（预览）

可以从 “Azure Monitor” 菜单中打开“指标”，使用指标资源管理器根据来自其他 Azure 服务的指标为 Azure 容器注册表分析指标。  有关使用此工具的详细信息，请参阅 [Azure 指标资源管理器入门](../azure-monitor/essentials/metrics-getting-started.md)。 

> [!TIP]
> 还可以导航到门户中的注册表，进入指标资源管理器。 在菜单中，选择“监视”下的“指标”（预览）。 

有关为 Azure 容器注册表收集的平台指标的列表，请参阅 [Azure 容器注册表监视数据参考指标](monitor-service-reference.md#metrics)  

若要参考，可以查看 [Azure Monitor 中所有受支持的资源指标](../azure-monitor/essentials/metrics-supported.md)列表。

### <a name="azure-cli"></a>Azure CLI

以下 Azure CLI 命令可用于获取有关指标 Azure 容器注册表的信息。

* [az acr show-usage](/cli/azure/acr/#az_acr_show_usage) - 显示 Azure 容器注册表使用的当前存储
* [az monitor metrics list-definitions](/cli/azure/monitor/metrics#az_monitor_metrics_list_definitions) - 列出指标定义和维度
* [az monitor metrics list](/cli/azure/monitor/metrics#az_monitor_metrics_list) - 检索指标值

### <a name="rest-api"></a>REST API 

可以使用此 Azure Monitor REST API 以编程方式获得有关 Azure 容器注册表指标的信息。

* [列出指标定义和维度](/rest/api/monitor/metricdefinitions/list)
* [检索指标值](/rest/api/monitor/metrics/list)

## <a name="analyzing-logs"></a>分析日志

Azure Monitor 日志中的数据以表形式存储，每个表具有自己独有的属性集。  

Azure Monitor 中的所有资源日志都具有后跟服务特定字段的相同字段。 [Azure Monitor 资源日志架构](../azure-monitor/essentials/resource-logs-schema.md#top-level-common-schema)概述了常见架构。 有关 Azure 容器注册表资源日志的架构，可参阅 [Azure 容器注册表数据参考](monitor-service-reference.md#schemas)。 

[活动日志](../azure-monitor/essentials/activity-log.md)是 Azure 中的一种平台日志，可用于深入了解订阅级别事件。 你可以单独查看它或将它路由到 Azure Monitor 日志，然后便可以在其中使用 Log Analytics 执行复杂得多的查询。  

有关为 Azure 容器注册表收集的资源日志类型列表，请参阅 [Azure 容器注册表监视数据参考](monitor-service-reference.md#resource-logs)。  

如需查看 Azure Monitor 日志使用并可通过 Log Analytics 查询的表列表，请参阅 [Azure 容器监视数据参考](monitor-service-reference.md#azure-monitor-logs-tables)。  

### <a name="sample-kusto-queries"></a>示例 Kusto 查询

> [!IMPORTANT]
> 在 “Azure 容器注册表”菜单中选择“日志”时，Log Analytics 随即打开，其查询范围设置为当前注册表。  这意味着日志查询只包含来自该资源的数据。 如果要运行的查询包含来自其他注册表或其他 Azure 服务的数据，请从 “Azure Monitor” 菜单中选择“日志”。 。 有关详细信息，请参阅 [Azure Monitor Log Analytics 中的日志查询范围和时间范围](/azure/azure-monitor/log-query/scope/)。

例如，以下查询从 **ContainerRegistryRepositoryEvents** 表中检索最近 24 小时的数据：

```Kusto
ContainerRegistryRepositoryEvents
| where TimeGenerated > ago(1d) 
```

下图显示了示例输出：

:::image type="content" source="media/monitor-service/azure-monitor-query.png" alt-text="查询日志数据":::

下面是可用来帮助你监视注册表资源的查询。 

### <a name="error-events-from-the-last-hour"></a>最近一小时内的错误事件

```Kusto
union Event, Syslog // Event table stores Windows event records, Syslog stores Linux records
| where TimeGenerated > ago(1h)
| where EventLevelName == "Error" // EventLevelName is used in the Event (Windows) records
    or SeverityLevel== "err" // SeverityLevel is used in Syslog (Linux) records
```

### <a name="100-most-recent-registry-events"></a>100 个最近的注册表事件

```Kusto
ContainerRegistryRepositoryEvents
| union ContainerRegistryLoginEvents
| top 100 by TimeGenerated
| project TimeGenerated, LoginServer, OperationName, Identity, Repository, DurationMs, Region , ResultType
```

### <a name="identity-of-user-or-object-that-deleted-repository"></a>删除了存储库的用户或对象的标识

```Kusto
ContainerRegistryRepositoryEvents
| where OperationName contains "Delete"
| project LoginServer, OperationName, Repository, Identity, CallerIpAddress
```

### <a name="identity-of-user-or-object-that-deleted-tag"></a>删除了标记的用户或对象的标识

```Kusto
ContainerRegistryRepositoryEvents
| where OperationName contains "Untag"
| project LoginServer, OperationName, Repository, Tag, Identity, CallerIpAddress
```

### <a name="repository-level-operation-failures"></a>存储库级操作失败

```kusto
ContainerRegistryRepositoryEvents 
| where ResultDescription contains "40"
| project TimeGenerated, OperationName, Repository, Tag, ResultDescription
```

### <a name="registry-authentication-failures"></a>注册表身份验证失败

```kusto
ContainerRegistryLoginEvents 
| where ResultDescription != "200"
| project TimeGenerated, Identity, CallerIpAddress, ResultDescription
```


## <a name="alerts"></a>警报

在监视数据中发现重要情况时，Azure Monitor 警报会主动通知你。 有了警报，你就可以在客户注意到你的系统中的问题之前确定和解决它们。 可以在[指标](../azure-monitor/alerts/alerts-metric-overview.md)、[日志](../azure-monitor/alerts/alerts-unified-log.md)和[活动日志](../azure-monitor/alerts/activity-log-alerts.md)上设置警报。 不同类型的警报各有优缺点。


<!-- only include next line if applications run on your service and work with App Insights. 

If you are creating or running an application which run on <*service*> [Azure Monitor Application Insights](../azure-monitor/overview.md#application-insights) may offer additional types of alerts.
-->

下表列出了常见和推荐使用的 Azure 容器注册表的警报规则。

<!-- Fill in the table with metric and log alerts that would be valuable for your service. Change the format as necessary to make it more readable -->
| 警报类型 | 条件 | 说明  |
|:---|:---|:---|
| 指标 | 信号：已使用存储<br/>运算符：大于<br/>聚合类型：Average<br/>阈值：5 GB|  如果使用的注册表存储超过指定值，则会发出警报。|
| | | |

### <a name="example-send-email-alert-when-registry-storage-used-exceeds-a-value"></a>示例：当使用的注册表存储超过特定值时，会发送电子邮件警报

1. 在 Azure 门户中导航到注册表。
1. 在“监视”下，选择“指标”（预览）。 
1. 在指标资源管理器的“指标”中，选择“使用的存储”。 
1. 选择“新建预警规则”。
1. 在“范围”中，确认要创建警报规则的注册表资源。
1. 在“条件”中，选择“添加条件”。 
    1. 在“信号名”中，选择“使用的存储”。 
    1. 在“图表周期”中，选择“过去 24 小时”。 
    1. 在“警报逻辑”的“阈值”中，选择一个值，如“5”。  在“单位”中，选择一个值，如“GB”。
    1. 接受其余设置的默认值，然后选择“完成”。
1. 在“操作”中，选择“添加操作组” > “+ 创建操作组”。  
    1. 输入操作组的详细信息。
    1. 在“通知”选项卡上，选择“电子邮件/短信/推送/语音”并输入收件人，例如 admin@contoso.com。  选择“查看 + 创建”。
1. 输入警报规则的名称和说明，然后选择严重级别。
1. 选择“创建预警规则”。

## <a name="next-steps"></a>后续步骤

- 有关 Azure 容器注册表创建的指标、日志和其他重要值的参考，请参阅 [Azure 容器注册表监视数据参考](monitor-service-reference.md)。
- 有关监视 Azure 资源的详细信息，请参阅[使用 Azure Monitor 监视 Azure 资源](../azure-monitor/essentials/monitor-azure-resource.md)。