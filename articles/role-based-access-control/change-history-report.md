---
title: 查看 Azure RBAC 更改的活动日志
description: 查看 Azure 基于角色的访问控制 (Azure RBAC) 在过去 90 天所做更改的活动日志。
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 03/01/2021
ms.author: rolyon
ms.custom: H1Hack27Feb2017, devx-track-azurepowershell
ms.openlocfilehash: 43ee281bc9696740c7909ef6b6758d4542808721
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124755220"
---
# <a name="view-activity-logs-for-azure-rbac-changes"></a>查看 Azure RBAC 更改的活动日志

有时需要了解 Azure 基于角色的访问控制 (Azure RBAC) 更改，如出于审核或故障排除目的。 只要有人更改了你订阅中的角色分配或角色定义，这些更改就会记录到 [Azure 活动日志](../azure-monitor/essentials/platform-logs-overview.md)中。 可以查看活动日志，了解在过去 90 天内发生的所有 Azure RBAC 更改。

## <a name="operations-that-are-logged"></a>记录的操作

以下是活动日志中记录的与 Azure RBAC 相关的操作：

- 创建角色分配
- 删除角色分配
- 创建或更新自定义角色定义
- 删除自定义角色定义

## <a name="azure-portal"></a>Azure 门户

最简单的入手方式就是使用 Azure 门户查看活动日志。 以下屏幕截图显示了活动日志中角色分配操作的示例。 它还包括一个用于将日志下载为 CSV 文件的选项。

![使用门户的活动日志 - 屏幕截图](./media/change-history-report/activity-log-portal.png)

若要获取详细信息，请单击某个条目以打开“摘要”窗格。 单击“JSON”选项卡获取详细日志。

![使用门户的活动日志（打开了“摘要”窗格）- 屏幕截图](./media/change-history-report/activity-log-summary-portal.png)

门户中的活动日志有多个筛选器。 下面是与 Azure RBAC 相关的筛选器：

| “筛选器” | 值 |
| --------- | --------- |
| 事件类别 | <ul><li>管理</li></ul> |
| Operation | <ul><li>创建角色分配</li><li>删除角色分配</li><li>创建或更新自定义角色定义</li><li>删除自定义角色定义</li></ul> |

有关活动日志的详细信息，请参阅 [Azure 活动日志](../azure-monitor/essentials/activity-log.md)。


## <a name="interpret-a-log-entry"></a>解析日志条目

“JSON”选项卡、Azure PowerShell 或 Azure CLI 中的日志输出可以包含很多信息。 下面是尝试解析日志条目时要查找的一些关键属性。 有关使用 Azure PowerShell 或 Azure CLI 来筛选日志输出的方法，请参阅以下部分。

> [!div class="mx-tableFixed"]
> | 属性 | 示例值 | 说明 |
> | --- | --- | --- |
> | authorization:action | Microsoft.Authorization/roleAssignments/write | 创建角色分配 |
> |  | Microsoft.Authorization/roleAssignments/delete | 删除角色分配 |
> |  | Microsoft.Authorization/roleDefinitions/write | 创建或更新角色定义 |
> |  | Microsoft.Authorization/roleDefinitions/delete | 删除角色定义 |
> | authorization:scope | /subscriptions/{subscriptionId}<br/>/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId} | 操作范围 |
> | caller | admin@example.com<br/>{objectId} | 谁启动了操作 |
> | eventTimestamp | 2021-03-01T22:07:41.126243Z | 操作发生的时间 |
> | status:value | Started<br/>已成功<br/>失败 | 操作状态 |

## <a name="azure-powershell"></a>Azure PowerShell

若要使用 Azure PowerShell 查看活动日志，请使用 [Get-AzLog](/powershell/module/Az.Monitor/Get-AzLog) 命令。

此命令列出过去 7 天内订阅中所有角色分配的更改：

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleAssignments/*'}
```

此命令列出过去 7 天内资源组中所有角色定义的更改：

```azurepowershell
Get-AzLog -ResourceGroupName pharma-sales -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleDefinitions/*'}
```

### <a name="filter-log-output"></a>筛选日志输出

日志输出可以包含许多信息。 此命令列出过去 7 天内订阅中所有角色分配和角色定义的更改，并筛选输出：

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/role*'} | Format-List Caller,EventTimestamp,{$_.Authorization.Action},Properties
```

下面显示了在创建角色分配时筛选日志输出的示例：

```azurepowershell
Caller                  : admin@example.com
EventTimestamp          : 3/1/2021 10:07:42 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          statusCode     : Created
                          serviceRequestId: {serviceRequestId}
                          eventCategory  : Administrative
                          entity         : /subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}
                          message        : Microsoft.Authorization/roleAssignments/write
                          hierarchy      : {tenantId}/{subscriptionId}

Caller                  : admin@example.com
EventTimestamp          : 3/1/2021 10:07:41 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          requestbody    : {"Id":"{roleAssignmentId}","Properties":{"PrincipalId":"{principalId}","PrincipalType":"User","RoleDefinitionId":"/providers/Microsoft.Authorization/roleDefinitions/fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64","Scope":"/subscriptions/
                          {subscriptionId}/resourceGroups/example-group"}}
                          eventCategory  : Administrative
                          entity         : /subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}
                          message        : Microsoft.Authorization/roleAssignments/write
                          hierarchy      : {tenantId}/{subscriptionId}

```

如果使用服务主体来创建角色分配，则 Caller 属性将是一个服务主体对象 ID。 可以使用 [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) 获取有关服务主体的信息。

```Example
Caller                  : {objectId}
EventTimestamp          : 3/1/2021 9:43:08 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              : 
                          statusCode     : Created
                          serviceRequestId: {serviceRequestId}
                          eventCategory  : Administrative
```

## <a name="azure-cli"></a>Azure CLI

若要使用 Azure CLI 查看活动日志，请使用 [az monitor activity-log list](/cli/azure/monitor/activity-log#az_monitor_activity_log_list) 命令。

此命令列出了资源组中从 3 月 1 日起 7 天的活动日志：

```azurecli
az monitor activity-log list --resource-group example-group --start-time 2021-03-01 --offset 7d
```

此命令列出了授权资源提供程序从 3 月 1 日起 7 天的活动日志：

```azurecli
az monitor activity-log list --namespace "Microsoft.Authorization" --start-time 2021-03-01 --offset 7d
```

### <a name="filter-log-output"></a>筛选日志输出

日志输出可以包含许多信息。 此命令列出后续 7 天订阅中所有角色分配和角色定义的更改，并筛选输出：

```azurecli
az monitor activity-log list --namespace "Microsoft.Authorization" --start-time 2021-03-01 --offset 7d --query '[].{authorization:authorization, caller:caller, eventTimestamp:eventTimestamp, properties:properties}'
```

下面显示了在创建角色分配时筛选日志输出的示例：

```azurecli
[
 {
    "authorization": {
      "action": "Microsoft.Authorization/roleAssignments/write",
      "role": null,
      "scope": "/subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}"
    },
    "caller": "admin@example.com",
    "eventTimestamp": "2021-03-01T22:07:42.456241+00:00",
    "properties": {
      "entity": "/subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
      "eventCategory": "Administrative",
      "hierarchy": "{tenantId}/{subscriptionId}",
      "message": "Microsoft.Authorization/roleAssignments/write",
      "serviceRequestId": "{serviceRequestId}",
      "statusCode": "Created"
    }
  },
  {
    "authorization": {
      "action": "Microsoft.Authorization/roleAssignments/write",
      "role": null,
      "scope": "/subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}"
    },
    "caller": "admin@example.com",
    "eventTimestamp": "2021-03-01T22:07:41.126243+00:00",
    "properties": {
      "entity": "/subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
      "eventCategory": "Administrative",
      "hierarchy": "{tenantId}/{subscriptionId}",
      "message": "Microsoft.Authorization/roleAssignments/write",
      "requestbody": "{\"Id\":\"{roleAssignmentId}\",\"Properties\":{\"PrincipalId\":\"{principalId}\",\"PrincipalType\":\"User\",\"RoleDefinitionId\":\"/providers/Microsoft.Authorization/roleDefinitions/fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64\",\"Scope\":\"/subscriptions/{subscriptionId}/resourceGroups/example-group\"}}"
    }
  }
]
```

## <a name="azure-monitor-logs"></a>Azure Monitor 日志

[Azure Monitor 日志](../azure-monitor/logs/log-query-overview.md)是另一种可用于收集并分析所有 Azure 资源的 Azure RBAC 更改的工具。 Azure Monitor 日志具有以下优点：

- 编写复杂查询和逻辑
- 与警报、Power BI 和其他工具集成
- 以更长的保持期保存数据
- 与其他日志（例如安全性、虚拟机和自定义日志）交叉引用

以下是开始使用的基本步骤：

1. [创建 Log Analytics 工作区](../azure-monitor/logs/quick-create-workspace.md)。

1. 为工作区[配置 Activity Log Analytics 解决方案](../azure-monitor/essentials/activity-log.md#activity-log-analytics-monitoring-solution)。

1. [查看活动日志](../azure-monitor/essentials/activity-log.md#activity-log-analytics-monitoring-solution)。 单击“日志”选项可以快速导航到 Activity Log Analytics 解决方案“概述”页面。

   ![门户中的 Azure Monitor 日志选项](./media/change-history-report/azure-log-analytics-option.png)

1. （可选）使用 [Azure Monitor Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md) 查询并查看日志。 有关详细信息，请参阅 [Azure Monitor 日志查询入门](../azure-monitor/logs/get-started-queries.md)。

以下查询返回由目标资源提供程序组织的新角色分配：

```Kusto
AzureActivity
| where TimeGenerated > ago(60d) and Authorization contains "Microsoft.Authorization/roleAssignments/write" and ActivityStatus == "Succeeded"
| parse ResourceId with * "/providers/" TargetResourceAuthProvider "/" *
| summarize count(), makeset(Caller) by TargetResourceAuthProvider
```

以下查询返回图表中显示的角色分配更改：

```Kusto
AzureActivity
| where TimeGenerated > ago(60d) and Authorization contains "Microsoft.Authorization/roleAssignments"
| summarize count() by bin(TimeGenerated, 1d), OperationName
| render timechart
```

![使用高级分析门户的活动日志 - 屏幕截图](./media/change-history-report/azure-log-analytics.png)

## <a name="next-steps"></a>后续步骤
* [查看活动日志以监视对资源的操作](../azure-monitor/essentials/activity-log.md)
* [使用 Azure 活动日志监视订阅活动](../azure-monitor/essentials/platform-logs-overview.md)
