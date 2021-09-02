---
title: Azure Monitor 工作簿和 Azure 资源管理器模板
description: 使用通过 Azure 资源管理器模板部署的预生成和自定义参数化 Azure Monitor 工作簿简化复杂的报表
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/30/2020
ms.openlocfilehash: d48cefcae16829bc0a58cdb4f4a43f52d78f8460
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121751403"
---
# <a name="programmatically-manage-workbooks"></a>以编程方式管理工作簿

资源所有者可以选择通过资源管理器模板以编程方式创建和管理其工作簿。

在如下所述的场景中，此方法可能很有用：
* 连同资源部署一起部署特定于组织或域的分析报表。 例如，可为新的应用或虚拟机部署特定于组织的性能和故障工作簿。
* 使用工作簿为现有资源部署标准报表或仪表板。

将使用资源管理器模板中指定的内容，在所需的子组/资源组中创建工作簿。

可通过编程方式管理两种类型的工作簿资源：
* [工作簿模板](#azure-resource-manager-template-for-deploying-a-workbook-template)
* [工作簿实例](#azure-resource-manager-template-for-deploying-a-workbook-instance)

## <a name="azure-resource-manager-template-for-deploying-a-workbook-template"></a>用于部署工作簿模板的 Azure 资源管理器模板

1. 打开要以编程方式部署的工作簿。
2. 单击“编辑”工具栏项，将工作簿切换到编辑模式。
3. 使用工具栏上的 _</>_ 按钮打开“高级编辑器”。
4. 确保位于“库模板”选项卡上。

    ![“库模板”选项卡](./media/workbooks-automate/gallery-template.png)
1. 将库模板中的 JSON 复制到剪贴板。
2. 下面是一个示例 Azure 资源管理器模板，它将一个工作簿模板部署到 Azure Monitor 工作簿库。 粘贴复制的 JSON 并替代 `<PASTE-COPIED-WORKBOOK_TEMPLATE_HERE>`。 可在[此处](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Documentation/ARM-template-for-creating-workbook-template)找到用于创建工作簿模板的 Azure 资源管理器参考模板。

    ```json
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "resourceName": {
                "type": "string",
                "defaultValue": "my-workbook-template",
                "metadata": {
                    "description": "The unique name for this workbook template instance"
                }
            }
        },
        "resources": [
            {
                "name": "[parameters('resourceName')]",
                "type": "microsoft.insights/workbooktemplates",
                "location": "[resourceGroup().location]",
                "apiVersion": "2019-10-17-preview",
                "dependsOn": [],
                "properties": {
                    "galleries": [
                        {
                            "name": "A Workbook Template",
                            "category": "Deployed Templates",
                            "order": 100,
                            "type": "workbook",
                            "resourceType": "Azure Monitor"
                        }
                    ],
                    "templateData": <PASTE-COPIED-WORKBOOK_TEMPLATE_HERE>
                }
            }
        ]
    }
    ```
1. 在 `galleries` 对象中，使用你自己的值填充 `name` 和 `category` 键。 在下一部分详细了解[参数](#parameters)。
2. 使用 [Azure 门户](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)、[命令行界面](../../azure-resource-manager/templates/deploy-cli.md)、[PowerShell](../../azure-resource-manager/templates/deploy-powershell.md) 等部署此 Azure 资源管理器模板。
3. 打开 Azure 门户，导航到在 Azure 资源管理器模板中选择的工作簿库。 在示例模板中，导航到 Azure Monitor 工作簿库：
    1. 打开 Azure 门户并导航到 Azure Monitor
    2. 从目录中打开 `Workbooks`
    3. 在 `Deployed Templates` 类别下的库中找到你的模板（一个紫色的项）。

### <a name="parameters"></a>parameters

|parameters                |说明                                                                                             |
|:-------------------------|:-------------------------------------------------------------------------------------------------------|
| `name`                   | Azure 资源管理器中的工作簿模板资源的名称。                                  |
|`type`                    | 始终是 microsoft.insights/workbooktemplates                                                            |
| `location`               | 要在其中创建工作簿的 Azure 位置。                                               |
| `apiVersion`             | 2019-10-17 预览版                                                                                     |
| `type`                   | 始终是 microsoft.insights/workbooktemplates                                                            |
| `galleries`              | 要在其中显示此工作簿模板的库集。                                                |
| `gallery.name`           | 库中工作簿模板的易记名称。                                             |
| `gallery.category`       | 库中的要将该模板放入到其中的组。                                                     |
| `gallery.order`          | 一个编号，用于确定模板在库中某个类别中的显示顺序。 顺序越低，优先级越高。 |
| `gallery.resourceType`   | 与库对应的资源类型。 这通常是一个对应于资源的资源类型字符串（例如 microsoft.operationalinsights/workspaces）。 |
|`gallery.type`            | 称作工作簿类型，它是用于在资源类型中区分库的唯一键。 例如，Application Insights 具有对应于不同工作簿库的类型 `workbook` 和 `tsg`。 |

### <a name="galleries"></a>库

| 库                                        | 资源类型                                       | 工作簿类型 |
|:-----------------------------------------------|:----------------------------------------------------|:--------------|
| Azure Monitor 中的工作簿                     | `Azure Monitor`                                     | `workbook`    |
| Azure Monitor 中的 VM Insights                   | `Azure Monitor`                                     | `vm-insights` |
| Log Analytics 工作区中的工作簿           | `microsoft.operationalinsights/workspaces`          | `workbook`    |
| Application Insights 中的工作簿              | `microsoft.insights/components`                     | `workbook`    |
| Application Insights 中的故障排除指南 | `microsoft.insights/components`                     | `tsg`         |
| Application Insights 中的使用情况                  | `microsoft.insights/components`                     | `usage`       |
| Kubernetes 服务中的工作簿                | `Microsoft.ContainerService/managedClusters`        | `workbook`    |
| 资源组中的工作簿                   | `microsoft.resources/subscriptions/resourcegroups`  | `workbook`    |
| Azure Active Directory 中的工作簿            | `microsoft.aadiam/tenant`                           | `workbook`    |
| 虚拟机中的 VM Insights                | `microsoft.compute/virtualmachines`                 | `insights`    |
| 虚拟机规模集中的 VM Insights      | `microsoft.compute/virtualmachinescalesets`         | `insights`    |

## <a name="azure-resource-manager-template-for-deploying-a-workbook-instance"></a>用于部署工作簿实例的 Azure 资源管理器模板

1. 打开要以编程方式部署的工作簿。
2. 单击“编辑”工具栏项，将工作簿切换到编辑模式。
3. 使用工具栏上的 _</>_ 按钮打开“高级编辑器”。
4. 在编辑器中，将“模板类型”切换为“资源管理器模板”。 
5. 该资源管理器模板会显示在编辑器中。 复制内容并按原样使用，或将其与一个更大的也用于部署目标资源的模板合并。

    ![插图，显示如何从工作簿 UI 内部获取资源管理器模板](./media/workbooks-automate/programmatic-template.png)

## <a name="sample-azure-resource-manager-template"></a>示例 Azure 资源管理器模板
此模板演示如何部署一个显示“Hello World!”的简单工作簿
```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workbookDisplayName":  {             
            "type":"string",
            "defaultValue": "My Workbook",
            "metadata": {
                "description": "The friendly name for the workbook that is used in the Gallery or Saved List. Needs to be unique in the scope of the resource group and source" 
            }
        },
        "workbookType":  {             
            "type":"string",
            "defaultValue": "tsg",
            "metadata": {
                "description": "The gallery that the workbook will been shown under. Supported values include workbook, `tsg`, Azure Monitor, etc." 
            }
        },
        "workbookSourceId":  {             
            "type":"string",
            "defaultValue": "<insert-your-resource-id-here>",
            "metadata": {
                "description": "The id of resource instance to which the workbook will be associated" 
            }
        },
        "workbookId": {
            "type":"string",
            "defaultValue": "[newGuid()]",
            "metadata": {
                "description": "The unique guid for this workbook instance" 
            }
        }
    },    
    "resources": [
        {
            "name": "[parameters('workbookId')]",
            "type": "Microsoft.Insights/workbooks",
            "location": "[resourceGroup().location]",
            "kind": "shared",
            "apiVersion": "2018-06-17-preview",
            "dependsOn": [],
            "properties": {
                "displayName": "[parameters('workbookDisplayName')]",
                "serializedData": "{\"version\":\"Notebook/1.0\",\"items\":[{\"type\":1,\"content\":\"{\\\"json\\\":\\\"Hello World!\\\"}\",\"conditionalVisibility\":null}],\"isLocked\":false}",
                "version": "1.0",
                "sourceId": "[parameters('workbookSourceId')]",
                "category": "[parameters('workbookType')]"
            }
        }
    ],
    "outputs": {
        "workbookId": {
            "type": "string",
            "value": "[resourceId( 'Microsoft.Insights/workbooks', parameters('workbookId'))]"
        }
    }
}
```

### <a name="template-parameters"></a>模板参数

| 参数 | 说明 |
| :------------- |:-------------|
| `workbookDisplayName` | 在“库”或“保存的列表”中使用的工作簿的易记名称。 在资源组和源范围内需保持唯一 |
| `workbookType` | 工作簿将在其下显示的库。 支持的值包括 workbook、`tsg`、Azure Monitor 等。 |
| `workbookSourceId` | 将要与该工作簿关联的资源实例的 ID。 新工作簿会显示它与此资源实例相关 - 例如，它会显示在资源目录中的“工作簿”下。 如果你希望工作簿在 Azure Monitor 中的工作簿库内显示，请使用字符串 _Azure Monitor_ 而不是资源 ID。 |
| `workbookId` | 此工作簿实例的唯一 GUID。 使用 _[newGuid()]_ 自动创建新的 GUID。 |
| `kind` | 用于指定创建的工作簿是否为共享。 所有新工作簿都将使用共享值。 |
| `location` | 要在其中创建工作簿的 Azure 位置。 使用 _[resourceGroup().location]_ 在资源组所在的位置创建工作簿 |
| `serializedData` | 包含要在工作簿中使用的内容或有效负载。 从工作簿 UI 使用资源管理器模板来获取值 |

### <a name="workbook-types"></a>工作簿类型
工作簿类型指定新工作簿实例会在其下显示的工作簿库类型。 选项包括：

| 类型 | 库位置 |
| :------------- |:-------------|
| `workbook` | 大多数报表中使用的默认值，包括 Application Insights 的工作簿库、Azure Monitor 等。  |
| `tsg` | Application Insights 中的故障排除指南库 |
| `usage` | Application Insights 中“使用情况”下的“更多”库  |

### <a name="working-with-json-formatted-workbook-data-in-the-serializeddata-template-parameter"></a>在 serializedData 模板参数中使用 JSON 格式的工作簿数据

导出 Azure 工作簿的 Azure 资源管理器模板时，通常会在导出的 `serializedData` 模板参数中嵌入固定的资源链接。 其中包括可能比较敏感的值，如订阅 ID、资源组名称和其他类型的资源 ID。

下面的示例演示如何在不使用字符串操作的情况下对导出的工作簿 Azure 资源管理器模板进行自定义。 此示例中的模式用于处理从 Azure 门户导出的未修改数据。 在以编程方式管理工作簿时，这也是屏蔽任何嵌入的敏感值的一种最佳做法，因此在这里屏蔽了订阅 ID 和资源组。 未对原始传入的 `serializedData` 值进行任何其他修改。

```json
{
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workbookDisplayName": {
      "type": "string"
    },
    "workbookSourceId": {
      "type": "string",
      "defaultValue": "[resourceGroup().id]"
    },
    "workbookId": {
      "type": "string",
      "defaultValue": "[newGuid()]"
    }
  },
  "variables": {
    // serializedData from original exported Azure Resource Manager template
    "serializedData": "{\"version\":\"Notebook/1.0\",\"items\":[{\"type\":1,\"content\":{\"json\":\"Replace with Title\"},\"name\":\"text - 0\"},{\"type\":3,\"content\":{\"version\":\"KqlItem/1.0\",\"query\":\"{\\\"version\\\":\\\"ARMEndpoint/1.0\\\",\\\"data\\\":null,\\\"headers\\\":[],\\\"method\\\":\\\"GET\\\",\\\"path\\\":\\\"/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups\\\",\\\"urlParams\\\":[{\\\"key\\\":\\\"api-version\\\",\\\"value\\\":\\\"2019-06-01\\\"}],\\\"batchDisabled\\\":false,\\\"transformers\\\":[{\\\"type\\\":\\\"jsonpath\\\",\\\"settings\\\":{\\\"tablePath\\\":\\\"$..*\\\",\\\"columns\\\":[]}}]}\",\"size\":0,\"queryType\":12,\"visualization\":\"map\",\"tileSettings\":{\"showBorder\":false},\"graphSettings\":{\"type\":0},\"mapSettings\":{\"locInfo\":\"AzureLoc\",\"locInfoColumn\":\"location\",\"sizeSettings\":\"location\",\"sizeAggregation\":\"Count\",\"opacity\":0.5,\"legendAggregation\":\"Count\",\"itemColorSettings\":null}},\"name\":\"query - 1\"}],\"isLocked\":false,\"fallbackResourceIds\":[\"/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/XXXXXXX\"]}",

    // parse the original into a JSON object, so that it can be manipulated
    "parsedData": "[json(variables('serializedData'))]",

    // create new JSON objects that represent only the items/properties to be modified
    "updatedTitle": {
      "content":{
        "json": "[concat('Resource Group Regions in subscription \"', subscription().displayName, '\"')]"
      }
    },
    "updatedMap": {
      "content": {
        "path": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups')]"
      }
    },

    // the union function applies the updates to the original data
    "updatedItems": [
      "[union(variables('parsedData')['items'][0], variables('updatedTitle'))]",
      "[union(variables('parsedData')['items'][1], variables('updatedMap'))]"
    ],

    // copy to a new workbook object, with the updated items
    "updatedWorkbookData": {
      "version": "[variables('parsedData')['version']]",
      "items": "[variables('updatedItems')]",
      "isLocked": "[variables('parsedData')['isLocked']]",
      "fallbackResourceIds": ["[parameters('workbookSourceId')]"]
    },

    // convert back to an encoded string
    "reserializedData": "[string(variables('updatedWorkbookData'))]"
  },
  "resources": [
    {
      "name": "[parameters('workbookId')]",
      "type": "microsoft.insights/workbooks",
      "location": "[resourceGroup().location]",
      "apiVersion": "2018-06-17-preview",
      "dependsOn": [],
      "kind": "shared",
      "properties": {
        "displayName": "[parameters('workbookDisplayName')]",
        "serializedData": "[variables('reserializedData')]",
        "version": "1.0",
        "sourceId": "[parameters('workbookSourceId')]",
        "category": "workbook"
      }
    }
  ],
  "outputs": {
    "workbookId": {
      "type": "string",
      "value": "[resourceId( 'microsoft.insights/workbooks', parameters('workbookId'))]"
    }
  },
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#"
}
```

在此示例中，以下步骤有助于对导出的 Azure 资源管理器模板进行自定义：
1. 如上一部分所述，将工作簿作为 Azure 资源管理器模板导出
2. 在模板的 `variables` 部分中：
    1. 将 `serializedData` 值解析为 json 对象变量，该变量将创建一个 json 结构，其中包含表示工作簿内容的项数组。
    2. 创建新的 JSON 对象，这些对象只表示要修改的项/属性。
    3. 投影一组新的 JSON 内容项 (`updatedItems`)，同时使用 `union()` 函数修改原始 JSON 项。
    4. 创建一个新的工作簿对象 `updatedWorkbookData`，其中包含来自原始解析数据的 `updatedItems` 和 `version`/`isLocked` 数据，以及一组更正后的 `fallbackResourceIds`。
    5. 将新的 JSON 内容序列化回一个新的字符串变量 `reserializedData`。
3. 使用新的 `reserializedData` 属性代替原始的 `serializedData` 属性。
4. 使用更新后的 Azure 资源管理器模板部署新的工作簿资源。

### <a name="limitations"></a>限制
出于技术原因，此机制无法用于在 Application Insights 的“工作簿”库中创建工作簿实例。 我们正在努力解决此限制。 在此同时，我们建议使用故障排除指南库（workbookType：`tsg`）来部署与 Application Insights 相关的工作簿。

## <a name="next-steps"></a>后续步骤

了解如何使用工作簿来支持新的[存储见解体验](../../storage/common/storage-insights-overview.md?toc=%2fazure%2fazure-monitor%2ftoc.json)。