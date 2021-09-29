---
title: 使用 Azure 门户将 Log Analytics 工作区移到另一个 Azure 区域
description: 使用 Azure 资源管理器模板，通过 Azure 门户将 Log Analytics 工作区从一个 Azure 区域移到另一个 Azure 区域。
author: yossiy
ms.topic: how-to
ms.date: 08/17/2021
ms.author: yossiy
ms.openlocfilehash: cecfa72493545f40c2e7326fece88cb85f99974b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128565406"
---
# <a name="move-a-log-analytics-workspace-to-another-region-by-using-the-azure-portal"></a>使用 Azure 门户将 Log Analytics 工作区移到另一个区域

在很多情况下，你会希望将现有 Log Analytics 工作区从一个区域移到另一个区域。 例如，Log Analytics 近期会在承载着你的大部分资源的某个区域变得可用，你希望工作区离得更近一些，节省出口费用。 你可能还希望将工作区移到新添加的区域，以满足数据主权要求。

Log Analytics 工作区不能从一个区域移到另一个区域。 但是，你可以使用 Azure 资源管理器模板导出工作区资源和相关资源。 然后，你可以将资源暂存在另一区域，方法是：将工作区导出到某个模板，根据目标区域的情况修改参数，然后将该模板部署到新区域。 有关资源管理器和模板的详细信息，请参阅[快速入门：使用 Azure 门户创建和部署 Azure 资源管理器模板](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)。 

工作区环境可能很复杂，包括连接的源、托管解决方案、链接服务、警报和查询包。 并非所有资源都可以使用资源管理器模板导出。在移动工作区时，某些资源需要单独的配置。

## <a name="prerequisites"></a>先决条件

- 若要将工作区配置导出到某个可部署到另一个区域的模板，你需要具有 [Log Analytics 参与者](../../role-based-access-control/built-in-roles.md#log-analytics-contributor)或[监视参与者](../../role-based-access-control/built-in-roles.md#monitoring-contributor)角色或权限更高的角色。

- 查明当前关联到你的工作区的所有资源，包括：
  - 连接的代理：进入工作区中的“日志”，查询 [heartbeat](../insights/solution-agenthealth.md#azure-monitor-log-records) 表以列出连接的代理。
    ```kusto
    Heartbeat
    | summarize by Computer, Category, OSType, _ResourceId
    ```
  - 诊断设置：资源可以将日志发送到 Azure 诊断或工作区中的专用表。 进入工作区中的“日志”，对将数据发送到 `AzureDiagnostics` 表的资源运行以下查询：

    ```kusto
    AzureDiagnostics
    | where TimeGenerated > ago(12h)
    | summarize by  ResourceProvider , ResourceType, Resource
    | sort by ResourceProvider, ResourceType
    ```

    对将数据发送到专用表的资源运行此查询：

    ```kusto
    search *
    | where TimeGenerated > ago(12h)
    | where isnotnull(_ResourceId)
    | extend ResourceProvider = split(_ResourceId, '/')[6]
    | where ResourceProvider !in ('microsoft.compute', 'microsoft.security')
    | extend ResourceType = split(_ResourceId, '/')[7]
    | extend Resource = split(_ResourceId, '/')[8]
    | summarize by tostring(ResourceProvider) , tostring(ResourceType), tostring(Resource)
    | sort by ResourceProvider, ResourceType
    ```

  - 安装的解决方案：在工作区导航窗格中选择“解决方案”，查看安装的解决方案的列表。
  - 数据收集器 API：通过[数据收集器 API](../logs/data-collector-api.md) 到达的数据存储在自定义日志表中。 若要查看自定义日志表的列表，请在工作区导航窗格中选择“日志”，然后在架构窗格中选择“自定义日志” 。
  - 链接服务：工作区可能有链接到从属资源（例如 Azure 自动化帐户、存储帐户或专用群集）的链接服务。 请从你的工作区中删除链接服务。 在目标工作区中手动重新配置它们。
  - 警报：若要列出警报，请在工作区导航窗格中选择“警报”，然后选择工具栏中的“管理警报规则” 。 对于在 2019 年 6 月 1 日之后创建的工作区中的警报或[从 Log Analytics 警报 API 升级到 scheduledQueryRules API](../alerts/alerts-log-api-switch.md) 的工作区中的警报，可以将它们包括在模板中。 
  
     你可以[检查 scheduledQueryRules API 是否用于你的工作区中的警报](../alerts/alerts-log-api-switch.md#check-switching-status-of-workspace)。 也可以在目标工作区中手动配置警报。
  - 查询包：一个工作区可以与多个查询包相关联。 若要标识工作区中的查询包，请在工作区导航窗格中选择“日志”，在左侧窗格中选择“查询”，然后选择搜索框右边的省略号 。 右侧将打开一个包含所选查询包的对话框。 如果查询包与要移动的工作区位于同一资源组中，可以将其包含在此迁移中。
- 验证你的 Azure 订阅是否允许在目标区域中创建 Log Analytics 工作区。

## <a name="prepare-and-move"></a>准备并移动
以下过程展示了如何使用资源管理器模板为移动操作准备工作区和资源，然后使用门户将其移到目标区域。 按顺序执行这些过程。

> [!NOTE]
> 并非所有资源都可以通过模板导出。 在目标区域创建工作区后，需要单独配置这些资源。

### <a name="select-resource-groups-and-edit-parameters"></a>选择资源组并编辑参数

1. 登录到 [Azure 门户](https://portal.azure.com)，然后选择“资源组”。
1. 找到包含你的工作区的资源组并选择它。
1. 若要查看警报资源，请选中“显示隐藏类型”复选框。
1. 选择“类型”筛选器。 选择“Log Analytics 工作区”、“解决方案”、“SavedSearches”、“microsoft.insights/scheduledqueryrules”、“defaultQueryPack”，以及你拥有的其他与工作区相关的资源（例如自动化帐户）    。 然后，选择“应用”。
1. 选择工作区、解决方案、保存的搜索、警报、查询包和你拥有的其他与工作区相关的资源（例如自动化帐户）。 然后选择工具栏上的“导出模板”。
    
    > [!NOTE]
    > 无法使用模板导出 Azure Sentinel。 你需要[将 Sentinel 加入到目标工作区](../../sentinel/quickstart-onboard.md)。
   
1. 选择工具栏中的“部署”，以编辑和准备用于部署的模板。
1. 选择工具栏中的“编辑参数”，以在联机编辑器中打开 parameters.json 文件。
1. 若要编辑参数，请更改 `parameters` 下的 `value` 属性。 下面是一个示例：

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "workspaces_name": {
          "value": "my-workspace-name"
        },
        "workspaceResourceId": {
          "value": "/subscriptions/resource-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/workspaces/workspace-name"
        },
        "alertName": {
          "value": "my-alert-name"
        },
        "querypacks_name": {
          "value": "my-default-query-pack-name"
        }
      }
    }
    ```

1. 在编辑器中选择“保存”。

### <a name="edit-the-template"></a>编辑模板

1. 选择工具栏中的“编辑模板”，以在联机编辑器中打开 template.json 文件。
1. 若要编辑将在其中部署 Log Analytics 工作区的目标区域，请在联机编辑器中更改 `resources` 下的 `location` 属性。 

   若要获取区域位置代码，请参阅 [Azure 中的数据驻留](https://azure.microsoft.com/global-infrastructure/locations/)。 区域代码是不带空格的区域名称。 例如，“美国中部”应为 `centralus`。
1. 如果模板中存在链接服务资源 (`microsoft.operationalinsights/workspaces/linkedservices`)，请将其删除。 应在目标工作区中手动重新配置这些资源。

   以下示例模板包括工作区、保存的搜索、解决方案、警报和查询包：

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "workspaces_name": {
          "type": "String"
        },
        "workspaceResourceId": {
          "type": "String"
        },
        "alertName": {
          "type": "String"
        },
        "querypacks_name": {
          "type": "String"
        }
      },
      "variables": {},
      "resources": [
        {
          "type": "microsoft.operationalinsights/workspaces",
          "apiVersion": "2020-08-01",
          "name": "[parameters('workspaces_name')]",
          "location": "france central",
          "properties": {
            "sku": {
              "name": "pergb2018"
            },
            "retentionInDays": 30,
            "features": {
              "enableLogAccessUsingOnlyResourcePermissions": true
            },
            "workspaceCapping": {
              "dailyQuotaGb": -1
            },
            "publicNetworkAccessForIngestion": "Enabled",
            "publicNetworkAccessForQuery": "Enabled"
          }
        },
        {
          "type": "Microsoft.OperationalInsights/workspaces/savedSearches",
          "apiVersion": "2020-08-01",
          "name": "[concat(parameters('workspaces_name'), '/2b5112ec-5ad0-5eda-80e9-ad98b51d4aba')]",
          "dependsOn": [
            "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaces_name'))]"
          ],
          "properties": {
            "category": "VM Monitoring",
            "displayName": "List all versions of curl in use",
            "query": "VMProcess\n| where ExecutableName == \"curl\"\n| distinct ProductVersion",
            "tags": [],
            "version": 2
          }
        },
        {
          "type": "Microsoft.OperationsManagement/solutions",
          "apiVersion": "2015-11-01-preview",
          "name": "[concat('Updates(', parameters('workspaces_name'))]",
          "location": "france central",
          "dependsOn": [
            "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaces_name'))]"
          ],
          "plan": {
            "name": "[concat('Updates(', parameters('workspaces_name'))]",
            "promotionCode": "",
            "product": "OMSGallery/Updates",
            "publisher": "Microsoft"
          },
          "properties": {
            "workspaceResourceId": "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaces_name'))]",
            "containedResources": [
              "[concat(resourceId('microsoft.operationalinsights/workspaces', parameters('workspaces_name')), '/views/Updates(', parameters('workspaces_name'), ')')]"
            ]
          }
        }
        {
          "type": "Microsoft.OperationsManagement/solutions",
          "apiVersion": "2015-11-01-preview",
          "name": "[concat('VMInsights(', parameters('workspaces_name'))]",
          "location": "france central",
          "plan": {
            "name": "[concat('VMInsights(', parameters('workspaces_name'))]",
            "promotionCode": "",
            "product": "OMSGallery/VMInsights",
            "publisher": "Microsoft"
          },
          "properties": {
            "workspaceResourceId": "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaces_name'))]",
            "containedResources": [
              "[concat(resourceId('microsoft.operationalinsights/workspaces', parameters('workspaces_name')), '/views/VMInsights(', parameters('workspaces_name'), ')')]"
            ]
          }
        },
        {
          "type": "microsoft.insights/scheduledqueryrules",
          "apiVersion": "2021-02-01-preview",
          "name": "[parameters('alertName')]",
          "location": "france central",
          "properties": {
            "displayName": "[parameters('alertName')]",
            "severity": 3,
            "enabled": true,
            "evaluationFrequency": "PT5M",
            "scopes": [
              "[parameters('workspaceResourceId')]"
            ],
            "windowSize": "PT15M",
            "criteria": {
              "allOf": [
                {
                  "query": "Heartbeat | where computer == 'my computer name'",
                  "timeAggregation": "Count",
                  "operator": "LessThan",
                  "threshold": 14,
                  "failingPeriods": {
                    "numberOfEvaluationPeriods": 1,
                    "minFailingPeriodsToAlert": 1
                  }
                }
              ]
            },
            "autoMitigate": true,
            "actions": {}
          }
        },
        {
          "type": "Microsoft.OperationalInsights/querypacks",
          "apiVersion": "2019-09-01-preview",
          "name": "[parameters('querypacks_name')]",
          "location": "francecentral",
          "properties": {}
        },
        {
          "type": "Microsoft.OperationalInsights/querypacks/queries",
          "apiVersion": "2019-09-01-preview",
          "name": "[concat(parameters('querypacks_name'), '/00000000-0000-0000-0000-000000000000')]",
          "dependsOn": [
            "[resourceId('Microsoft.OperationalInsights/querypacks', parameters('querypacks_name'))]"
          ],
          "properties": {
            "displayName": "my-query-name",
            "body": "my-query-text",
            "related": {
              "categories": [],
              "resourceTypes": [
                  "microsoft.operationalinsights/workspaces"
              ]
            },
            "tags": {
              "labels": []
            }
          }
        }
      ]
    }
    ```

1. 在在线编辑器中选择“保存”。

### <a name="deploy-the-workspace"></a>部署工作区

1. 选择“订阅”以选择将在其中部署目标工作区的订阅。
1. 选择“资源组”以选择将在其中部署目标工作区的资源组。 可以选择“新建”，为目标工作区创建一个新的资源组。
1. 验证“区域”是否设置为要将网络安全组部署到的目标位置。
1. 选择“查看 + 创建”按钮来验证模板。
1. 选择“创建”，将工作区和所选资源部署到目标区域。
1. 工作区（包括所选资源）现已部署在目标区域中。 你可以完成工作区中的剩余配置，使功能与原始工作区相匹配。
   - 连接代理：使用任意可用选项（包括数据收集规则）在虚拟机和虚拟机规模集上配置所需的代理，并将新的目标工作区指定为目标。
   - 诊断设置：更新标识的资源中的诊断设置，并将目标工作区作为目标。
   - 安装解决方案：某些解决方案（例如 [Azure Sentinel](../../sentinel/quickstart-onboard.md)）需要特定的加入过程，并且未包括在模板中。 应单独将它们加入到新工作区中。
   - 配置数据收集器 API：配置数据收集器 API 实例以将数据发送到目标工作区。
   - 配置警报规则：如果未从模板导出警报，则需要在目标工作区中手动配置它们。
1. 确保新数据不会引入到原始工作区。 在原始工作区中运行以下查询，并确保迁移后未进行任何数据引入：

    ```kusto
    search *
    | where TimeGenerated > ago(12h)
    | summarize max(TimeGenerated) by Type
    ```

数据源连接到目标工作区后，引入的数据存储在目标工作区中。 旧数据保留在原始工作区中，并受保留策略的约束。 可以执行[跨工作区查询](./cross-workspace-query.md#performing-a-query-across-multiple-resources)。 如果为两个工作区分配了相同的名称，请使用工作区引用中的限定名称 (subscriptionName/resourceGroup/componentName)。

以下是跨具有相同名称的两个工作区的查询示例：

```kusto
union 
  workspace('subscription-name1/<resource-group-name1/<original-workspace-name>')Update, 
  workspace('subscription-name2/<resource-group-name2/<target-workspace-name>').Update, 
| where TimeGenerated >= ago(1h)
| where UpdateState == "Needed"
| summarize dcount(Computer) by Classification
```

## <a name="discard"></a>弃用

如果要丢弃源工作区，请删除导出的资源或包含这些资源的资源组：

1. 在 Azure 门户中选择目标资源组。
1. 在“概述”页面上：
   
   - 如果为此部署创建了新的资源组，请选择工具栏上的“删除资源组”以删除该资源组。
   - 如果模板已部署到现有资源组，请选择使用该模板部署的资源，然后选择工具栏上的“删除”以删除所选资源。

## <a name="clean-up"></a>清理

在将新数据引入新工作区时，原始工作区中的旧数据仍可用于查询，并受工作区中定义的保留策略的约束。 如果需要使用旧数据来[跨工作区查询](./cross-workspace-query.md#performing-a-query-across-multiple-resources)，建议保留原始工作区。 

如果不再需要访问原始工作区中的旧数据：

1. 在 Azure 门户中选择原始资源组。 
1. 选择要删除的任何资源，然后选择工具栏上的“删除”。

## <a name="next-steps"></a>后续步骤

在本文中，你已将一个 Log Analytics 工作区和关联的资源从一个区域移到了另一个区域，并清理了源资源。 若要详细了解如何在区域之间移动资源，以及如何在 Azure 中进行灾难恢复，请参阅：

- [将资源移到新资源组或订阅中](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [将 Azure VM 移到另一区域](../../site-recovery/azure-to-azure-tutorial-migrate.md)
