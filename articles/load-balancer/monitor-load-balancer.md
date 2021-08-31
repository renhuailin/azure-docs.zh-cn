---
title: 监视 Azure 负载均衡器
description: 从此处开始了解如何监视负载均衡器。
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: how-to
ms.custom: subject-monitoring
ms.date: 06/29/2021
ms.openlocfilehash: 62885e889c64c40c051f838178f9a96d4ac4a8ee
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114291174"
---
# <a name="monitoring-load-balancer"></a>监视负载均衡器

当你的关键应用程序和业务流程依赖于 Azure 资源时，你需要监视这些资源的可用性、性能和操作。 

本文介绍负载均衡器生成的监视数据。 负载均衡器使用 [Azure Monitor](../azure-monitor/overview.md)。 如果你不熟悉使用此功能的所有 Azure 服务通用的 Azure Monitor 功能，请参阅[使用 Azure Monitor 监视 Azure 资源](../azure-monitor/essentials/monitor-azure-resource.md)。

## <a name="load-balancer-insights"></a>负载均衡器见解

Azure 中的某些服务在 Azure 门户中具有一个特殊且醒目的预生成仪表板，可以从此仪表板着手监视服务。 这些特殊仪表板称为“见解”。

负载均衡器见解提供：

* 函数依赖关系视图
* 指标仪表板
* 概述选项卡
* “前端和后端可用性”选项卡
* “数据吞吐量”选项卡
* 流分发
* 连接监视器
* 指标定义

有关负载均衡器见解的详细信息，请参阅[使用 Insights 监视和配置 Azure 负载均衡器](./load-balancer-insights.md)

## <a name="monitoring-data"></a>监视数据 

负载均衡器收集与[监视 Azure 资源中的数据](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data)中所述的其他 Azure 资源相同的监视数据。 

有关负载均衡器创建的指标和日志指标的详细信息，请参阅[监视负载均衡器数据参考](monitor-load-balancer.md)。

负载均衡器通过以下内容提供其他监视数据：

* [运行状况探测](./load-balancer-custom-probe-overview.md)
* [资源运行状况](./load-balancer-standard-diagnostics.md#resource-health-status)
* [REST API](./load-balancer-query-metrics-rest-api.md)

## <a name="collection-and-routing"></a>收集和路由

平台指标和活动日志会自动收集和存储，但你可以使用诊断设置将其路由到其他位置。  

在创建诊断设置并将其路由到一个或多个位置之前，不会收集和存储资源日志。

## <a name="creating-a-diagnostic-setting"></a>创建诊断设置

可以使用 Azure 门户、PowerShell 或 Azure CLI 来创建诊断设置。


有关一般指南，请参阅[创建诊断设置以收集 Azure 中的平台日志和指标](../azure-monitor/essentials/diagnostic-settings.md)。

创建诊断设置时，请指定要收集的日志类别。 负载均衡器的类别为“AllMetrics”

### <a name="portal"></a>门户

1. 登录到 [Azure 门户](https://portal.azure.com)

2. 在门户顶部的搜索框中，输入“负载均衡器”。

3. 在搜索结果中选择“负载均衡器”。

4. 选择你的负载均衡器。 对于此示例，使用“myLoadBalancer”。

5. 在“myLoadBalancer”的“监视”部分中，选择“诊断设置”  。

6. 在“诊断设置”中，选择“+ 添加诊断设置” 。

7. 在“诊断设置”中，输入或选择以下信息。

    | 设置 | 值 |
    | ------- | ----- |
    | 诊断设置名称 | 为该诊断设置输入一个名称。 |
    | **类别详细信息** |   |
    | 指标 | 选择“AllMetrics”。 |

8. 选择“目标详细信息”。 一些目标选项为：
    * 发送到 Log Analytics
        * 选择“订阅”和“Log Analytics 工作区” 。
    * 存档到存储帐户
        * 选择“订阅”和“存储帐户” 。
    * 流式传输到事件中心
        * 选择“订阅”、“事件中心命名空间”、“事件中心名称（可选）”和“事件中心策略名称”   

9. 选择“保存”。

### <a name="powershell"></a>PowerShell

登录到 Azure PowerShell：

```azurepowershell
Connect-AzAccount 
```

#### <a name="log-analytics-workspace"></a>Log Analytics 工作区

若要为 Log Analytics 工作区启用诊断日志，请输入以下命令。 将括号中的值替换为自己的值：

```azurepowershell
## Place the load balancer in a variable. ##
$lbpara = @{
    ResourceGroupName = <your-resource-group-name>
    Name = <your-load-balancer-name>
}
$lb = Get-AzLoadBalancer @lbpara
    
## Place the workspace in a variable. ##
$wspara = @{
    ResourceGroupName = <your-resource-group-name>
    Name = <your-log-analytics-workspace-name>
}
$ws = Get-AzOperationalInsightsWorkspace @wspara
    
## Enable the diagnostic setting. ##
Set-AzDiagnosticSetting `
    -ResourceId $lb.id `
    -Name <your-diagnostic-setting-name> `
    -Enabled $true `
    -MetricCategory 'AllMetrics' `
    -WorkspaceId $ws.ResourceId
```

#### <a name="storage-account"></a>存储帐户

若要在存储帐户中启用诊断日志，请输入以下命令。 将括号中的值替换为自己的值：

```azurepowershell
## Place the load balancer in a variable. ##
$lbpara = @{
    ResourceGroupName = <your-resource-group-name>
    Name = <your-load-balancer-name>
}
$lb = Get-AzLoadBalancer @lbpara
    
## Place the storage account in a variable. ##
$storpara = @{
    ResourceGroupName = <your-resource-group-name>
    Name = <your-storage-account-name>
}
$storage = Get-AzStorageAccount @storpara
    
## Enable the diagnostic setting. ##
Set-AzDiagnosticSetting `
    -ResourceId $lb.id `
    -Name <your-diagnostic-setting-name> `
    -StorageAccountId $storage.id `
    -Enabled $true `
    -MetricCategory 'AllMetrics'
```

#### <a name="event-hub"></a>事件中心

若要为事件中心命名空间启用诊断日志，请输入以下命令。 将括号中的值替换为自己的值：

```azurepowershell
## Place the load balancer in a variable. ##
$lbpara = @{
    ResourceGroupName = <your-resource-group-name>
    Name = <your-load-balancer-name>
}
$lb = Get-AzLoadBalancer @lbpara
    
## Place the event hub in a variable. ##
$hubpara = @{
    ResourceGroupName = <your-resource-group-name>
    Name = <your-event-hub-name>
}
$eventhub = Get-AzEventHubNamespace @hubpara

## Place the event hub authorization rule in a variable. ##    
$hubrule = @{
    ResourceGroupName = 'myResourceGroup'
    Namespace = 'myeventhub8675'
}
$eventhubrule = Get-AzEventHubAuthorizationRule @hubrule

## Enable the diagnostic setting. ##
Set-AzDiagnosticSetting `
    -ResourceId $lb.Id `
    -Name 'myDiagSetting-event'`
    -EventHubName $eventhub.Name `
    -EventHubAuthorizationRuleId $eventhubrule.Id `
    -Enabled $true `
    -MetricCategory 'AllMetrics'
```
### <a name="azure-cli"></a>Azure CLI

登录到 Azure CLI：

```azurecli
az login
```

#### <a name="log-analytics-workspace"></a>Log Analytics 工作区

若要为 Log Analytics 工作区启用诊断日志，请输入以下命令。 将括号中的值替换为自己的值：

```azurecli
lbid=$(az network lb show \
    --name <your-load-balancer-name> \
    --resource-group <your-resource-group> \
    --query id \
    --output tsv)

wsid=$(az monitor log-analytics workspace show \
    --resource-group <your-resource-group> \
    --workspace-name <your-log-analytics-workspace-name> \
    --query id \
    --output tsv)
    
az monitor diagnostic-settings create \
    --name <your-diagnostic-setting-name> \
    --resource $lbid \
    --metrics '[{"category": "AllMetrics","enabled": true}]' \
    --workspace $wsid
```

#### <a name="storage-account"></a>存储帐户

若要在存储帐户中启用诊断日志，请输入以下命令。 将括号中的值替换为自己的值：

```azurecli
lbid=$(az network lb show \
    --name <your-load-balancer-name> \
    --resource-group <your-resource-group> \
    --query id \
    --output tsv)

storid=$(az storage account show \
        --name <your-storage-account-name> \
        --resource-group <your-resource-group> \
        --query id \
        --output tsv)
    
az monitor diagnostic-settings create \
    --name <your-diagnostic-setting-name> \
    --resource $lbid \
    --metrics '[{"category": "AllMetrics","enabled": true}]' \
    --storage-account $storid
```

#### <a name="event-hub"></a>事件中心

若要为事件中心命名空间启用诊断日志，请输入以下命令。 将括号中的值替换为自己的值：

```azurecli
lbid=$(az network lb show \
    --name <your-load-balancer-name> \
    --resource-group <your-resource-group> \
    --query id \
    --output tsv)

az monitor diagnostic-settings create \
    --name myDiagSetting-event \
    --resource $lbid \
    --metrics '[{"category": "AllMetrics","enabled": true}]' \
    --event-hub-rule /subscriptions/<your-subscription-id>/resourceGroups/<your-resource-group>/providers/Microsoft.EventHub/namespaces/<your-event-hub-namespace>/authorizationrules/RootManageSharedAccessKey
```

以下部分将讨论可以收集的指标和日志。

## <a name="analyzing-metrics"></a>分析指标

可以从“Azure Monitor”菜单中打开“指标”，使用指标资源管理器根据来自其他 Azure 服务的指标分析负载均衡器的指标 。 有关使用此工具的详细信息，请参阅 [Azure 指标资源管理器入门](../azure-monitor/essentials/metrics-getting-started.md)。 

有关为负载均衡器收集的平台指标列表，请参阅[监视负载均衡器数据参考指标](monitor-load-balancer-reference.md#metrics)  

若要参考，可以查看 [Azure Monitor 中所有受支持的资源指标](../azure-monitor/essentials/metrics-supported.md)列表。

## <a name="analyzing-logs"></a>分析日志

Azure Monitor 日志中的数据以表形式存储，每个表具有自己独有的属性集。  

[活动日志](../azure-monitor/essentials/activity-log.md)是一种平台日志，可用于深入了解订阅级别的事件。 你可以单独查看它或将它路由到 Azure Monitor 日志，然后便可以在其中使用 Log Analytics 执行复杂得多的查询。  

如需查看 Azure Monitor 日志使用并可通过 Log Analytics 查询的表列表，请参阅[监视负载均衡器数据参考](monitor-load-balancer-reference.md#azure-monitor-logs-tables)  

### <a name="sample-kusto-queries"></a>示例 Kusto 查询

> [!NOTE]
> Kusto 查询当前存在一个问题，阻止从负载均衡器日志中检索数据。


## <a name="alerts"></a>警报

在监视数据中发现重要情况时，Azure Monitor 警报会主动通知你。 有了警报，你就可以在客户注意到你的系统中的问题之前确定和解决它们。 可以在[指标](../azure-monitor/alerts/alerts-metric-overview.md)、[日志](../azure-monitor/alerts/alerts-unified-log.md)和[活动日志](../azure-monitor/alerts/activity-log-alerts.md)上设置警报。 不同类型的警报各有优缺点

如果要创建或运行在负载均衡器上运行的应用程序，[Azure Monitor Application Insights](../azure-monitor/overview.md#application-insights) 可能会提供其他类型的警报。


下表列出了常见和推荐使用的负载均衡器的警报规则。

| 警报类型 | 条件 | 说明  |
|:---|:---|:---|
| 由于 VM 不可用，负载均衡规则不可用 | 如果数据路径可用性按前端 IP 地址和前端端口（所有已知和未来值）拆分等于零且运行状况探测状态等于零，则触发警报 | 此警报确定任何已配置的负载均衡规则的数据路径可用性是否因已配置的运行状况探测正在探测关联后端池中的所有 VM 而无法为流量提供服务。 查看负载均衡器[故障排除指南](load-balancer-troubleshoot.md)，调查潜在根本原因。 |
| VM 可用性非常低 | 如果运行状况探测状态按后端 IP 和后端端口拆分等于用户定义的占总池大小的探测百分比（即探测到 25%），则触发警报 | 此警报确定可用的 VM 是否少于提供流量所需的 VM |
| 到 Internet 终结点的出站连接失败 | 如果按“连接状态 = 失败”筛选的 SNAT 连接计数大于零，则触发警报 | 当 SNAT 端口耗尽且 VM 无法启动出站连接时，将触发此警报。 |
| 接近 SNAT 耗尽 | 如果“已使用的 SNAT 端口”大于用户定义的数量，则触发警报 | 此警报需要始终分配相同数量的端口的静态出站配置。 当已使用端口达到已分配端口百分比时，则会触发此警报。 |

## <a name="next-steps"></a>后续步骤

- 有关负载均衡器创建的指标、日志和其他重要值的参考信息，请参阅[监视负载均衡器数据参考](monitor-load-balancer-reference.md)。
- 有关监视 Azure 资源的详细信息，请参阅[使用 Azure Monitor 监视 Azure 资源](../azure-monitor/essentials/monitor-azure-resource.md)。