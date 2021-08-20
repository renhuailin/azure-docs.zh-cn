---
title: 用于数据收集规则的资源管理器模板示例
description: 在 Azure Monitor 中的数据收集规则和虚拟机之间创建关联的示例 Azure 资源管理器模板。
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 11/17/2020
ms.openlocfilehash: 24bc9b0f92a82354f511f534eb9b0e86d59d336b
ms.sourcegitcommit: e1874bb73cb669ce1e5203ec0a3777024c23a486
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/16/2021
ms.locfileid: "112201259"
---
# <a name="resource-manager-template-samples-for-data-collection-rules-in-azure-monitor"></a>用于 Azure Monitor 中的数据收集规则的资源管理器模板示例
本文包含示例 [Azure 资源管理器模板](../../azure-resource-manager/templates/syntax.md)，用于在[数据收集规则](data-collection-rule-overview.md)和 [Azure Monitor 代理](./azure-monitor-agent-overview.md)之间创建关联。 每个示例都包含模板文件和参数文件，其中包含要提供给模板的示例值。

[!INCLUDE [azure-monitor-samples](../../../includes/azure-monitor-resource-manager-samples.md)]

## <a name="create-rule-sample"></a>创建规则（示例）

查看[模板格式](/azure/templates/microsoft.insights/datacollectionrules)

## <a name="create-association-with-azure-vm"></a>与 Azure VM 创建关联

下面的示例在 Azure 虚拟机和数据收集规则之间创建关联。

### <a name="template-file"></a>模板文件

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string",
            "metadata": {
                "description": "Name of the virtual machine."
            }
        },
        "associationName": {
            "type": "string",
            "metadata": {
                "description": "Name of the association."
            }
        },
        "dataCollectionRuleId": {
            "type": "string",
            "metadata": {
                "description": "Resource ID of the data collection rule."
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Compute/virtualMachines/providers/dataCollectionRuleAssociations",
            "name": "[concat(parameters('vmName'),'/microsoft.insights/', parameters('associationName'))]",
            "apiVersion": "2019-11-01-preview",
            "properties": {
                "description": "Association of data collection rule. Deleting this association will break the data collection for this virtual machine.",
                "dataCollectionRuleId": "[parameters('dataCollectionRuleId')]"
            }
        }
    ]
}
```

### <a name="parameter-file"></a>参数文件

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "vmName": {
        "value": "my-windows-vm"
      },
      "location": {
        "value": "eastus"
      }
  }
}
```

## <a name="create-association-with-azure-arc"></a>创建与 Azure Arc 的关联

下面的示例在启用了 Azure Arc 的服务器和数据收集规则之间创建关联。

### <a name="template-file"></a>模板文件

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string",
            "metadata": {
                "description": "Name of the virtual machine."
            }
        },
        "associationName": {
            "type": "string",
            "metadata": {
                "description": "Name of the association."
            }
        },
        "dataCollectionRuleId": {
            "type": "string",
            "metadata": {
                "description": "Resource ID of the data collection rule."
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.HybridCompute/machines/providers/dataCollectionRuleAssociations",
            "name": "[concat(parameters('vmName'),'/microsoft.insights/', parameters('associationName'))]",
            "apiVersion": "2019-11-01-preview",
            "properties": {
                "description": "Association of data collection rule. Deleting this association will break the data collection for this Arc server.",
                "dataCollectionRuleId": "[parameters('dataCollectionRuleId')]"
            }
        }
    ]
}
```

### <a name="parameter-file"></a>参数文件

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "vmName": {
        "value": "my-windows-vm"
      },
      "location": {
        "value": "eastus"
      }
  }
}
```


## <a name="next-steps"></a>后续步骤

* [获取 Azure Monitor 的其他示例模板](../resource-manager-samples.md)。
* [详细了解 Log Analytics 代理](./log-analytics-agent.md)。
* [详细了解诊断扩展](./diagnostics-extension-overview.md)。
