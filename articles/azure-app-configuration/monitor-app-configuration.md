---
title: 监视 Azure 应用程序配置
description: 从此处开始了解如何监视应用程序配置
services: azure-app-configuration
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 05/05/2021
ms.openlocfilehash: 7edddaac52c569bf29c1be7e173ce966adb0c17b
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111969739"
---
# <a name="monitoring-app-configuration"></a>监视应用程序配置
当你的关键应用程序和业务流程依赖于 Azure 资源时，你需要监视这些资源的可用性、性能和操作。 

本文介绍 Azure 应用程序配置生成的监视数据。 应用程序配置使用 [Azure Monitor](../azure-monitor/overview.md)。 如果你不熟悉使用此功能的所有 Azure 服务通用的 Azure Monitor 功能，请参阅[使用 Azure Monitor 监视 Azure 资源](../azure-monitor/essentials/monitor-azure-resource.md)。

## <a name="monitoring-overview-page-in-azure-portal"></a>Azure 门户中的监视概述页面
Azure 门户中的“概述”页面包括对资源使用情况的简介，例如请求总数、已限制请求数和每个配置存储的请求持续时间。 这些信息非常有用，但只显示少量监视数据。 创建资源后，其中的某些监视数据会自动收集，并可供分析。 你可以使用某些配置启用其他数据收集类型。

> [!div class="mx-imgBorder"]
> ![在“概述”页面上进行监视](./media/monitoring-overview-page.png)

## <a name="monitoring-data"></a>监视数据 
应用程序配置收集与[监视 Azure 资源中的数据](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data)中所述的其他 Azure 资源相同的监视数据。 有关 Azure 应用程序配置创建的指标和日志指标的详细信息，请参阅[监视 Azure 应用程序配置数据参考](./monitor-app-configuration-reference.md)。

## <a name="collection-and-routing"></a>收集和路由
平台指标和活动日志会自动收集并存储，但可以使用诊断设置将其路由到其他位置。

在创建诊断设置并将其路由到一个或多个位置之前，不会收集和存储资源日志。 例如，若要在 Azure Monitor 中近实时查看配置存储的日志和指标，请在 Log Analytics 工作区中收集资源日志。 如果还没有工作区，请创建一个 [Log Analytics 工作区](../azure-monitor/logs/quick-create-workspace.md)，并按照以下步骤创建和启用诊断设置。 

 #### <a name="portal"></a>[门户](#tab/portal)

1. 登录到 Azure 门户。

1. 导航到 Azure 应用程序配置存储。

1. 在“监视”部分，选择“诊断设置”，然后选择“+添加诊断设置”。 
    > [!div class="mx-imgBorder"]
    > ![添加诊断设置](./media/diagnostic-settings-add.png)

1. 在“诊断设置”页面中，输入设置的名称，然后选择“HttpRequest”，并选择发送日志的目标。 若要将其发送到 Log Analytics 工作区，请选择“发送到 Log Analytics 工作区”。
 
    > [!div class="mx-imgBorder"]
    > ![诊断设置的详细信息](./media/monitoring-diagnostic-settings-details.png)

1. 输入 **订阅** 和 **Log Analytics 工作区** 的名称。 
1. 选择“保存”并验证“诊断设置”页面现在是否列出新的诊断设置。 
    

 ### <a name="azure-cli"></a>[Azure CLI](#tab/cli)
    
1. 打开 Azure Cloud Shell，或者，如果已在本地安装 Azure CLI，请打开命令控制台应用程序，如 Windows PowerShell。

1. 如果你的标识关联到多个订阅，请将活动订阅设置为要为其启用日志的存储帐户的订阅。

    ```Azure CLI
    az account set --subscription <your-subscription-id>
    ```

1. 使用 az monitor [diagnostic-settings create 命令](/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az_monitor_diagnostic_settings_create&preserve-view=true)启用日志。

    ```Azure CLI
    az monitor diagnostic-settings create --name <setting-name> --workspace <log-analytics-workspace-resource-id> --resource <app-configuration-resource-id> --logs '[{"category": <category name>, "enabled": true "retentionPolicy": {"days": <days>, "enabled": <retention-bool}}]'
    ```

 ### <a name="powershell"></a>[PowerShell](#tab/PowerShell)
    
1. 打开 Windows PowerShell 命令窗口，使用 Connect-AzAccount 命令登录到 Azure 订阅。 然后，按照屏幕指令进行操作。

    ```PowerShell
    Connect-AzAccount
    ```

1. 将活动订阅设置为要为其启用日志的 Azure 应用程序配置帐户的订阅。

    ```PowerShell
    Set-AzContext -SubscriptionId <subscription-id>
    ```
    
1. 若要为 Log Analytics 工作区启用日志，请使用 [Set-AzDiagnosticSetting PowerShell](/previous-versions/azure/mt631625(v=azure.100)?redirectedfrom=MSDN) cmdlet。 

    ```PowerShell
    Set-AzDiagnosticSetting -ResourceId <app-configuration-resource-id> -WorkspaceId <log-analytics-workspace-resource-id> -Enabled $true
    ```
1. 验证是否正确设置了诊断设置并启用了日志类别。 

    ```PowerShell
    Get-AzureRmDiagnosticSetting -ResourceId <app-configuration-resource-id> 
    ```
---
有关使用 Azure 门户、CLI 或 PowerShell 创建诊断设置的更多信息，请参阅[创建诊断设置以收集 Azure 中的平台日志和指标](../azure-monitor/essentials/diagnostic-settings.md)。 

创建诊断设置时，请指定要收集的日志类别。 有关 Azure 应用程序配置的日志类别的详细信息，请参阅 [Azure 应用程序配置监视数据参考](./monitor-app-configuration-reference.md#resourcelogs)。

## <a name="analyzing-metrics"></a>分析指标

可以从“Azure Monitor”菜单中打开“指标”，使用指标资源管理器根据来自其他 Azure 服务的指标为应用程序配置分析指标。 有关使用此工具的详细信息，请参阅 [Azure 指标资源管理器入门](../azure-monitor/essentials/metrics-getting-started.md)。 对于 Azure 应用程序配置，将收集以下指标： 

* Http 传入请求计数 
* Http 传入请求持续时间 
* 受限制的 Http 请求计数（Http 状态代码 429 响应）

在门户中，导航到“指标”部分，并选择要分析的“Metric Namespaces（指标命名空间）”和“指标”。 此屏幕截图显示了在选择配置存储的 Http 传入请求计数时的指标视图。

> [!div class="mx-imgBorder"]
> ![如何使用应用程序配置指标](./media/monitoring-analyze-metrics.png)

如需查看为 Azure 应用程序配置收集的平台指标列表，请参阅[监视 Azure 应用程序配置数据参考指标](./monitor-app-configuration-reference.md#metrics)。 还可以参阅 [Azure Monitor 中所有受支持的资源指标](../azure-monitor/essentials/metrics-supported.md)列表。

## <a name="analyzing-logs"></a>分析日志
Azure Monitor 日志中的数据以表形式存储，每个表具有自己独有的属性集。 [Azure Monitor 资源日志架构](../azure-monitor/essentials/resource-logs-schema.md#top-level-common-schema)概述了常见架构。 

[活动日志](../azure-monitor/essentials/activity-log.md)是 Azure 中的一种平台日志，可用于深入了解订阅级别事件。 你可以单独查看它或将它路由到 Azure Monitor 日志，然后便可以在其中使用 Log Analytics 执行复杂得多的查询。  
如需查看为 Azure 应用程序配置收集的资源日志类型的列表，请参阅[监视 Azure 应用程序配置数据参考](./monitor-app-configuration-reference.md#resourcelogs)。 如需查看 Azure Monitor 日志使用并可通过 Log Analytics 查询的表列表，请参阅[监视应用程序配置数据参考](./monitor-app-configuration-reference.md#azuremonitorlogstables)  

>[!IMPORTANT]
> 从“应用程序配置”菜单中选择“日志”时，会打开 Log Analytics 并将查询范围设置为当前的应用程序配置资源。 这意味着日志查询只包含来自该资源的数据。 


如果要运行的查询包含来自他配置或其他 Azure 服务的数据，请从“Azure Monitor”菜单中选择“日志”。 有关详细信息，请参阅 [Azure Monitor Log Analytics 中的日志查询范围和时间范围](/azure/azure-monitor/log-query/scope/)。

在门户中，导航到“日志”部分，然后导航到查询编辑器。 在左侧的“表”选项卡下，选择“AACHttpRequest”以查看配置存储的日志。 在编辑器中输入 Kusto 查询，结果将显示在下方。  

> [!div class="mx-imgBorder"]
> ![在我们的日志中编写 kusto 查询](./media/monitoring-writing-queries.png)

以下是可用于帮助监视 Azure 应用程序配置资源的示例查询。 



* 列出过去三天内的所有 Http 请求 
    ```Kusto
       AACHttpRequest
        | where TimeGenerated > ago(3d)
    ```

* 列出过去三天内的所有受限制的请求（因为请求过多而返回 Http 状态代码 429） 
    ```Kusto
       AACHttpRequest
        | where TimeGenerated > ago(3d)
        | where StatusCode == "429"
    ```

* 按 IP 地址列出在过去三天内发送的请求数 
    ```Kusto
       AACHttpRequest
        | where TimeGenerated > ago(3d)
        | summarize requestCount= count() by ClientIPAddress
        | order by requestCount desc 
    ```

* 为在过去三天内接收的状态代码类型创建饼图
    ```Kusto
       AACHttpRequest
        | where TimeGenerated > ago(3d)
        | summarize requestCount=count() by StatusCode
        | order by requestCount desc 
        | render piechart 
    ```

* 列出过去 14 天内每天发送的请求数
    ```Kusto
    AACHttpRequest
        | where TimeGenerated > ago(14d)
        | extend Day = startofday(TimeGenerated)
        | summarize requestcount=count() by Day
        | order by Day desc  
    ```

## <a name="alerts"></a>警报

在监视数据中发现重要情况时，Azure Monitor 警报会主动通知你。 有了警报，你就可以在客户注意到你的系统中的问题之前确定和解决它们。 可以在[指标](../azure-monitor/alerts/alerts-metric-overview.md)、[日志](../azure-monitor/alerts/alerts-unified-log.md)和[活动日志](../azure-monitor/alerts/activity-log-alerts.md)上设置警报。 不同类型的警报各有优缺点。
下表列出了常见和推荐使用的 Azure 应用程序配置的警报规则。

| 警报类型 | 条件 | 说明  |
|:---|:---|:---|
|Http 请求的速率限制 | 状态代码 = 429  | 配置存储已超过[每小时请求配额](./faq.yml#are-there-any-limits-on-the-number-of-requests-made-to-app-configuration)。 升级到标准存储，或遵循[最佳做法](./howto-best-practices.md#reduce-requests-made-to-app-configuration)来优化使用情况。 |



## <a name="next-steps"></a>后续步骤

* 有关 Azure 应用程序配置创建的指标、日志和其他重要值的参考信息，请参阅[监视 Azure 应用程序配置数据参考](./monitor-app-configuration-reference.md)。

* 有关监视 Azure 资源的详细信息，请参阅[使用 Azure Monitor 监视 Azure 资源](../azure-monitor/essentials/monitor-azure-resource.md)。