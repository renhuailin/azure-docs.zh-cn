---
title: 用于 Log Analytics 群集的资源管理器模板示例
description: 用于部署 Log Analytics 群集的 Azure 资源管理器模板示例。
ms.topic: sample
author: yossiy
ms.author: yossiy
ms.date: 09/12/2021
ms.openlocfilehash: 1e585db0e7e0cec05319c74419bb840200d1d86a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128659223"
---
# <a name="resource-manager-template-samples-for-log-analytics-clusters-in-azure-monitor"></a>用于 Azure Monitor 中的 Log Analytics 群集的资源管理器模板示例
本文包含用于在 Azure Monitor 中创建和配置 Log Analytics 群集的 [Azure 资源管理器模板](../../azure-resource-manager/templates/syntax.md)示例。 每个示例都包含模板文件和参数文件，其中包含要提供给模板的示例值。

[!INCLUDE [azure-monitor-samples](../../../includes/azure-monitor-resource-manager-samples.md)]


## <a name="template-references"></a>模板参考

- [Microsoft.operationalinsights 群集](/azure/templates/microsoft.operationalinsights/2020-03-01-preview/clusters) 

## <a name="create-a-log-analytics-cluster"></a>创建 Log Analytics 资源
下面的示例创建一个新的空 Log Analytics 群集。

### <a name="template-file"></a>模板文件

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "clusterName": {
      "type": "string",
      "metadata": {
        "description": "The name of the Log Analytics cluster."
      }
    },
    "CommitmentTier": {
      "type": "int",
      "allowedValues": [
        500,
        1000,
        2000,
        5000
      ],
      "defaultValue": 500,
      "metadata": {
        "description": "The Capacity Reservation value."
      }
  },
  "billingType": {
      "type": "string",
      "allowedValues": [
        "Cluster",
        "Workspaces"
      ],
      "defaultValue": "Cluster",
      "metadata": {
          "description": "The billing type settings. Can be 'Cluster' (default) or 'Workspaces' for proportional billing on workspaces."
      }
    }
  },
  "resources": [
    {
      "name": "[parameters('clusterName')]",
      "type": "Microsoft.OperationalInsights/clusters",
      "apiVersion": "2021-06-01",
      "location": "[resourceGroup().location]",
      "identity": {
        "type": "SystemAssigned"
      },
      "sku": {
        "name": "CapacityReservation",
        "capacity": "[parameters('CommitmentTier')]"
      },
      "properties":  {
        "billingType": "[parameters('billingType')]"
      }
    }
  ]
}
```

### <a name="parameter-file"></a>参数文件

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "clusterName": {
      "value": "MyCluster"
    },
    "CommitmentTier": {
      "value": 500
    },
    "billingType": {
      "value": "Cluster"
    }
  }
}
```

## <a name="update-a-log-analytics-cluster"></a>更新 Log Analytics 群集
下面的示例将 Log Analytics 群集更新为使用客户管理的密钥。

### <a name="template-file"></a>模板文件

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "clusterName": {
      "type": "string",
      "metadata": {
        "description": "The name of the Log Analytics cluster."
      }
    },
    "keyVaultUri": {
      "type": "string",
      "metadata": {
        "description": "The key identifier URI."
      }
    },
    "keyName": {
      "type": "string",
      "metadata": {
        "description": "The key name."
      }
    },
    "keyVersion": {
      "type": "string",
      "metadata": {
        "description": "The key version. When empty, latest key version is used."
      }
    }
  },
  "resources": [
    {
      "name": "[parameters('clusterName')]",
      "type": "Microsoft.OperationalInsights/clusters",
      "apiVersion": "2021-06-01",
      "location": "[resourceGroup().location]",
      "identity": {
        "type": "SystemAssigned"
      },
      "properties":  {
        "keyVaultProperties": {
        "keyVaultUri": "https://key-vault-name.vault.azure.net",
        "keyName": "key-name",
        "keyVersion": "current-version"
        }
      }  
    }
  ]
}
```

### <a name="parameter-file"></a>参数文件

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "clusterName": {
      "value": "MyCluster"
    },
    "keyVaultUri": {
      "value": "https://key-vault-name.vault.azure.net"
    },
    "keyName": {
      "value": "MyKeyName"
    },
    "keyVersion": {
      "value": ""
    }
  }
}
```

## <a name="next-steps"></a>后续步骤

* [获取 Azure Monitor 的其他示例模板](../resource-manager-samples.md)。
* [详细了解 Log Analytics 专用群集](./logs-dedicated-clusters.md)。
* [了解有关代理数据源的详细信息](../agents/agent-data-sources.md)。
