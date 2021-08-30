---
title: 使用 Azure 门户将 Log Analytics 工作区移到另一个 Azure 区域
description: 使用 Azure 资源管理器模板，通过 Azure 门户将 Log Analytics 工作区从一个 Azure 区域移到另一个 Azure 区域。
author: yossiy
ms.topic: how-to
ms.date: 08/17/2021
ms.author: yossiy
ms.openlocfilehash: 0292559afefd983127980c036f4936433831f1e5
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122326292"
---
# <a name="move-log-analytics-workspace-to-another-region-using-the-azure-portal"></a>使用 Azure 门户将 Log Analytics 工作区移到另一个区域

在很多情况下，你会希望将现有 Log Analytics 工作区从一个区域移到另一个区域。 例如，Log Analytics 近期在承载着你的大部分资源的某个区域可用，你希望工作区离得更近一些，节省出口费用。 你可能还希望将工作区移到新添加的区域，以满足数据主权要求。

Log Analytics 工作区不能从一个区域移到另一个区域。 但是，你可以使用 Azure 资源管理器模板导出工作区资源和相关资源。 然后，你可以将资源暂存在另一区域，方法是：将工作区导出到某个模板，根据目标区域的情况修改参数，然后将该模板部署到新区域。 有关资源管理器和模板的详细信息，请参阅[快速入门：使用 Azure 门户创建和部署 Azure 资源管理器模板](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)。 工作区环境可能很复杂，包括连接的源、托管解决方案、链接服务、警报和查询包。 并非所有资源都可以在资源管理器模板中导出。在移动工作区时，某些资源需要单独的配置。

## <a name="prerequisites"></a>必备条件

- 若要将工作区配置导出到某个可部署到另一个区域的模板，你需要具有 [Log Analytics 参与者](../../role-based-access-control/built-in-roles.md#log-analytics-contributor)或[监视参与者](../../role-based-access-control/built-in-roles.md#monitoring-contributor)角色或权限更高的角色。

- 查明当前关联到你的工作区的所有资源，包括：
  - 连接的代理 -- 进入工作区中的“日志”，查询 [Heartbeat](../insights/solution-agenthealth.md#heartbeat-records) 表来列出连接的代理。
    ```kusto
    Heartbeat
    | summarize by Computer, Category, OSType, _ResourceId
    ```
  - 安装的解决方案 -- 单击工作区导航窗格中的“解决方案”，查看已安装解决方案的列表
  - 数据收集器 API -- 通过[数据收集器 API](../logs/data-collector-api.md) 到达的数据存储在自定义日志表中。 单击工作区导航窗格中的“日志”，然后单击架构窗格中的“自定义日志”，查看自定义日志表的列表
  - 链接服务 -- 工作区可能有链接到从属资源（例如自动化帐户、存储帐户、专用群集）的链接服务。 请从你的工作区中删除链接服务。 应当在目标工作区中手动重新配置这些项
  - 警报 -- 单击工作区导航窗格中的“警报”，然后单击工具栏中的“管理警报规则”以列出警报。 在 2019 年 6 月 1 日之后创建的工作区中的警报或[从旧的 Log Analytics 警报 API 升级到 scheduledQueryRules API](../alerts/alerts-log-api-switch.md) 的工作区中的警报可以包括在模板中。 你可以[检查 scheduledQueryRules API 是否用于你的工作区中的警报](../alerts/alerts-log-api-switch.md#check-switching-status-of-workspace)。 也可以在目标工作区中手动配置警报
  - 查询包 -- 一个工作区可以与多个查询包相关联。 若要查明工作区中的查询包，请单击工作区导航窗格中的“日志”，单击左窗格中的“查询”，然后单击搜索框右侧的省略号以显示更多设置 - 将在右侧打开一个包含所选查询包的对话框。  如果查询包与要移动的工作区位于同一资源组中，可以在此迁移中包括它
- 验证你的 Azure 订阅是否允许在目标区域中创建 Log Analytics 工作区

## <a name="prepare-and-move"></a>准备并移动
以下步骤展示了如何使用资源管理器模板为移动操作准备工作区和资源，并使用门户将其移到目标区域。 并非所有资源都可以通过模板导出，在目标区域中创建工作区后，需要单独配置这些资源。

### <a name="export-the-template-and-deploy-from-the-portal"></a>通过门户导出模板并进行部署

1. 登录到 [Azure 门户](https://portal.azure.com)，然后转到“资源组”
2. 找到包含你的工作区的资源组并单击它
3. 若要查看警报资源，请选中“显示隐藏类型”复选框
4. 单击“类型”筛选器并选择“Log Analytics 工作区”、“解决方案”、“SavedSearches”，根据情况选择“microsoft.insights/scheduledqueryrules”和“defaultQueryPack”，然后单击“应用”
5. 选择要移动的工作区、解决方案、警报、保存的搜索和查询包，然后单击工具栏中的“导出模板”
    
    > [!NOTE]
    > Sentinel 无法通过模板导出，你需要[将 Sentinel 加入](../../sentinel/quickstart-onboard.md)到目标工作区。
   
6. 单击工具栏中的“部署”，以编辑和准备用于部署的模板
7. 单击工具栏中的“编辑参数”，以在联机编辑器中打开 parameters.json 文件 
8. 若要编辑参数，请更改 parameters 下的 value 属性

示例参数文件：

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

9. 在编辑器中单击“保存”
10. 单击工具栏中的“编辑模板”，以在联机编辑器中打开 template.json 文件
11. 若要编辑将在其中部署 Log Analytics 工作区的目标区域，请在联机编辑器中的 resources 下更改 location 属性。  若要获取区域位置代码，请参阅 [Azure 位置](https://azure.microsoft.com/global-infrastructure/locations/)。 区域的代码是不带空格的区域名称，美国中部 = centralus 
12. 如果模板中存在链接服务资源 `microsoft.operationalinsights/workspaces/linkedservices`，请将其删除。 应当在目标工作区中手动重新配置这些项

示例模板，包括工作区、保存的搜索、解决方案、警报和查询包：

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

13. 在联机编辑器中单击“保存”
14. 单击“订阅”以选择将在其中部署目标工作区的订阅
16. 单击“资源组”以选择将在其中部署目标工作区的资源组。 可以单击“新建”，为目标工作区创建一个新的资源组
17. 验证“区域”是否设置为你要将 NSG 部署到其中的目标位置
18. 单击“查看 + 创建”按钮来验证模板
19. 单击“创建”，将工作区和所选资源部署到目标区域
20. 工作区（包括所选资源）现在部署在目标区域，你可以完成工作区中的剩余配置，使功能与原始工作区相匹配
    - 连接代理 -- 使用包括 DCR 在内的任意可用选项在虚拟机和虚拟机规模集上配置所需的代理，并指定新的目标工作区作为目标
    - 安装解决方案 -- 某些解决方案（例如 [Azure Sentinel](../../sentinel/quickstart-onboard.md)）需要特定的加入过程，并且未包括在模板中。 应当单独将它们加入到新工作区中
    - 数据收集器 API -- 配置数据收集器 API 实例以将数据发送到目标工作区
    - 警报规则 -- 当警报未在模板中导出时，需要在目标工作区中手动配置它们

## <a name="discard"></a>弃用

如果要丢弃源工作区，请删除导出的资源或包含它们的资源组。 为此，请在 Azure 门户中选择目标资源组 - 如果为此部署创建了新的资源组，请在“概述”页面中的工具栏上单击“删除资源组”。 如果模板已部署到现有资源组，请选择已使用模板部署的资源，然后单击工具栏中的“删除”。

## <a name="clean-up"></a>清理

建议在验证期间保留原始资源组（包括工作区），直至实现目标工作区功能。 如果要丢弃原始位置中的资源，请在 Azure 门户中选择原始资源组，然后选择要删除的资源，并单击工具栏中的“删除”。

## <a name="next-steps"></a>后续步骤

在本教程中，你已将一个 Log Analytics 工作区和关联的资源从一个区域移到了另一个区域，并清理了源资源。 若要详细了解如何在区域之间移动资源，以及如何在 Azure 中进行灾难恢复，请参阅：

- [将资源移到新资源组或订阅中](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [将 Azure VM 移到另一区域](../../site-recovery/azure-to-azure-tutorial-migrate.md)