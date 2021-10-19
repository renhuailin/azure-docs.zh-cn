---
title: Azure Monitor 中的 Log Analytics 工作区数据导出功能（预览版）
description: 使用 Log Analytics 数据导出功能，可以在收集 Log Analytics 工作区中所选表的数据时，将数据持续导出到 Azure 存储帐户或 Azure 事件中心。
ms.topic: conceptual
ms.custom: references_regions, devx-track-azurecli, devx-track-azurepowershell
author: bwren
ms.author: bwren
ms.date: 05/07/2021
ms.openlocfilehash: beb3d2374e89402795dab0480d840e291c391ec8
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2021
ms.locfileid: "129811565"
---
# <a name="log-analytics-workspace-data-export-in-azure-monitor-preview"></a>Azure Monitor 中的 Log Analytics 工作区数据导出功能（预览版）
使用 Azure Monitor 中的 Log Analytics 工作区数据导出功能，可以在收集 Log Analytics 工作区中所选表的数据时，将数据持续导出到 Azure 存储帐户或 Azure 事件中心。 本文提供了有关此功能的详细信息以及在工作区中配置数据导出的步骤。

## <a name="overview"></a>概述
为 Log Analytics 工作区配置数据导出后，发送到工作区中所选表的任何新数据都将以每小时追加 Blob 的方式自动导出到你的存储帐户，或准实时导出到你的事件中心。

![数据导出概述](media/logs-data-export/data-export-overview.png)

包含的表中的所有数据导出时不会经过筛选。 例如，为 SecurityEvent 表配置数据导出规则时，发送到 SecurityEvent 表的所有数据将从配置时开始导出 。


## <a name="other-export-options"></a>其他导出选项
Log Analytics 工作区数据导出会持续从 Log Analytics 工作区导出数据。 针对特定场景导出数据的其他选项包括：

- 使用逻辑应用从日志查询进行计划性导出。 这类似于数据导出功能，但你可向 Azure 存储发送经过筛选或聚合的数据。 不过，此方法受限于[日志查询限制](../service-limits.md#log-analytics-workspaces)，请参阅[使用逻辑应用将 Log Analytics 工作区中的数据存档到 Azure 存储](logs-export-logic-app.md)。
- 使用 PowerShell 脚本一次性导出到本地计算机。 请参阅 [Invoke-AzOperationalInsightsQueryExport](https://www.powershellgallery.com/packages/Invoke-AzOperationalInsightsQueryExport)。

## <a name="limitations"></a>限制

- 目前可以使用 CLI 或 REST 请求执行配置。 尚不支持 Azure 门户或 PowerShell。
- CLI 和 REST 中的 `--export-all-tables` 选项不受支持，将被删除。 你应在导出规则中显式提供表的列表。
- 受支持的表仅限于下面[受支持的表](#supported-tables)部分指定的那些表。 
- 不支持在导出内容中包含现有的自定义日志表。 在 2022 年 3 月提供的新自定义日志版本将受支持。
- 如果数据导出规则包含不受支持的表，操作不会失败，但在表受到支持之前，不会导出该表的任何数据。 
- 如果数据导出规则包含不存在的表，操作会失败并出现错误 `Table <tableName> does not exist in the workspace`。
- 可以在工作区中定义最多 10 个已启用的规则。 允许已禁用的其他规则。 
- 在工作区的所有导出规则中，目标必须唯一。
- 目标必须与 Log Analytics 工作区位于同一区域。
- 导出到存储帐户时，表名称的长度不能超过 60 个字符；导出到事件中心时，其长度不能超过 47 个字符。 名称较长的表将不会被导出。
- 数据导出今后将在所有区域中可用，但目前仅在以下区域中受支持： 
    - 澳大利亚中部
    - 澳大利亚东部
    - 澳大利亚东南部
    - 巴西南部
    - 加拿大中部
    - 印度中部
    - 美国中部
    - 东亚
    - 美国东部
    - 美国东部 2
    - 法国中部
    - 德国中西部
    - Japan East
    - 韩国中部
    - 美国中北部
    - 北欧
    - 南非北部
    - 美国中南部
    - Southeast Asia
    - 瑞士北部
    - 瑞士西部
    - 阿拉伯联合酋长国北部
    - 英国南部
    - 英国西部
    - 美国中西部
    - 西欧
    - 美国西部
    - 美国西部 2

## <a name="data-completeness"></a>数据完整性
如果目标不可用，数据导出会继续重新尝试发送数据，最多持续 30 分钟。 如果 30 分钟后仍不可用，数据将被放弃，直到目标变为可用。

## <a name="cost"></a>成本
当前，数据导出功能不收取额外费用。 数据导出功能的定价会在将来公布，在开始计费之前会提供一个通知期。 如果你选择在通知期后继续使用数据导出，则将按照适用的费率缴费。

## <a name="export-destinations"></a>导出目标

在工作区中创建导出规则之前，必须创建数据导出目标。 目标不必要与工作区位于同一个订阅中。 使用 Azure Lighthouse，还可以将数据发送到另一个 Azure Active Directory 租户中的目标。

### <a name="storage-account"></a>存储帐户

需要对工作区和目标拥有“写入”权限才能配置数据导出规则。 不应使用存储了其他非监视数据的现有存储帐户，以便能够更好地控制对数据的访问，并防止达到存储引入速率限制和网络限制。 

若要将数据发送到不可变存储，请按照[设置和管理 Blob 存储的不可变性策略](../../storage/blobs/immutable-policy-configure-version-scope.md)中所述为存储帐户设置不可变策略。 必须按照本文中的所有步骤操作，包括启用受保护的追加 blob 写入操作。

存储帐户必须为 StorageV1 或更高版本，并且与工作区位于同一区域。 如果需要将数据复制到其他区域中的其他存储帐户，可以使用任何 [Azure 存储冗余选项](../../storage/common/storage-redundancy.md#redundancy-in-a-secondary-region)，包括 GRS 和 GZRS。

数据会在其到达 Azure Monitor 时被发送到存储帐户并存储在每小时追加的 Blob 中。 导出规则设置将为存储帐户中的每个表创建一个容器，其名称为 am- 后接表名称。 例如，表 SecurityEvent 将发送到名为 am-SecurityEvent 的容器中 。

从 2021 年 10 月 15 日开始，Blob 将存储在 5 个 minutes 文件夹中，其路径结构如下：WorkspaceResourceId=/subscriptions/subscription-id/resourcegroups/\<resource-group\>/providers/microsoft.operationalinsights/workspaces/\<workspace\>/y=\<four-digit numeric year\>/m=\<two-digit numeric month\>/d=\<two-digit numeric day\>/h=\<two-digit 24-hour clock hour\>/m=\<two-digit 60-minute clock minute\>/PT05M.json。 由于追加 Blob 在存储中的写入限制为 50K，如果追加的数量很大，则导出的 Blob 的数量可能会增加。 在这种情况下，Blob 的命名模式为 PT05M_#.json*，其中 # 是增量 Blob 计数。

存储帐户数据格式位于 [JSON 行](../essentials/resource-logs-blob-format.md)中。 这意味着每个记录将由换行符分隔，JSON 记录之间没有外部记录数组和逗号。 

[![存储示例数据](media/logs-data-export/storage-data.png)](media/logs-data-export/storage-data.png#lightbox)

### <a name="event-hub"></a>事件中心

需要对工作区和目标拥有“写入”权限才能配置数据导出规则。 事件中心命名空间的共享访问策略定义了流式传输机制拥有的权限。 流式传输到事件中心需要 Manage、Send 和 Listen 权限。 若要更新导出规则，必须对该事件中心授权规则拥有 ListKey 权限。

事件中心命名空间需要与工作区位于同一区域。

数据到达 Azure Monitor 时，将发送到事件中心。 将为导出的每个数据类型创建一个事件中心，其名称为 am- 后跟表的名称。 例如，表 SecurityEvent 将发送到名为 am-SecurityEvent 的事件中心中 。 如果要将导出的数据传递到特定事件中心，或者有一个表的名称超过了 47 个字符的限制，则可提供自己的事件中心名称并将定义的表的所有数据导出到该事件中心。

> [!IMPORTANT]
> [每个“基本”和“标准”命名空间层支持 10 个事件中心](../../event-hubs/event-hubs-quotas.md#common-limits-for-all-tiers)。 如果导出的表格超过 10 个，则可在多个导出规则之间将表格拆分到不同的事件中心命名空间，或在导出规则中提供事件中心名称，并将所有表格导出到该事件中心。

事件中心命名空间注意事项：
1. “基本”事件中心 SKU 支持的事件大小[限制](../../event-hubs/event-hubs-quotas.md#basic-vs-standard-vs-premium-vs-dedicated-tiers)更低，工作区中的某些日志可能会因超过该限制而被删除。 建议使用“标准”或“专用”事件中心作为导出目标。
2. 导出的数据量通常会随时间的推移而增加，需要扩大事件中心的规模，以适应更高的传输速率并避免出现限制情况和数据延迟。 应使用事件中心的自动扩充功能来自动进行纵向扩展并增加吞吐量单位数，以满足使用需求。 有关详细信息，请参阅[自动增加 Azure 事件中心吞吐量单位](../../event-hubs/event-hubs-auto-inflate.md)。

> [!NOTE]
> 启用虚拟网络后，Azure Monitor 数据导出无法访问事件中心资源。 必须在事件中心启用“允许受信任的 Microsoft 服务绕过此防火墙”设置，以便授予 Azure Monitor 数据导出访问事件中心资源的权限。 

## <a name="enable-data-export"></a>启用数据导出
需要执行以下步骤才能启用 Log Analytics 数据导出。 有关每项的详细信息，请阅读以下各节。

- 注册资源提供程序。
- 允许受信任的 Microsoft 服务。
- 创建一个或多个数据导出规则，用于定义要导出的表及其目标。

### <a name="register-resource-provider"></a>注册资源提供程序
需要为你的订阅注册以下 Azure 资源提供程序才能启用 Log Analytics 数据导出。 

- Microsoft.Insights

可能已为大多数 Azure Monitor 用户注册此资源提供程序。 若要验证，请转到 Azure 门户中的“订阅”。 选择订阅，然后在菜单的“设置”部分中单击“资源提供程序” 。 找到 Microsoft.Insights。 如果其状态为“已注册”，表示已注册。 如果未注册，请单击“注册”进行注册。

还可使用任何可用的方法来注册资源提供程序，如 [Azure 资源提供程序和类型](../../azure-resource-manager/management/resource-providers-and-types.md)中所述。 下面是使用 PowerShell 的示例命令：

```PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.insights
```

### <a name="allow-trusted-microsoft-services"></a>允许受信任的 Microsoft 服务
如果你已将存储帐户配置为允许从所选网络进行访问，则需要添加一个例外来允许 Azure Monitor 写入帐户。 从存储帐户的“防火墙和虚拟网络”中，选择“允许受信任的 Microsoft 服务访问此存储帐户” 。

[![存储帐户防火墙和虚拟网络](media/logs-data-export/storage-account-vnet.png)](media/logs-data-export/storage-account-vnet.png#lightbox)

### <a name="create-or-update-data-export-rule"></a>创建或更新数据导出规则
数据导出规则定义导出数据的表和目标。 可以在工作区中启用 10 条规则，可以添加其他规则，但添加的规则会处于“禁用”状态。 目标在工作区中的所有导出规则中必须是唯一的。

数据导出目标存在某些限制，应监视这些目标，以最大程度地减少导出限制、失败和延迟的情况。 请参阅[存储帐户可伸缩性](../../storage/common/scalability-targets-standard-account.md#scale-targets-for-standard-storage-accounts)和[事件中心命名空间配额](../../event-hubs/event-hubs-quotas.md)。

#### <a name="recommendations-for-storage-account"></a>关于存储帐户的建议 

1. 使用单独的存储帐户进行导出
1. 使用以下设置来配置关于以下指标的警报： 
   - `Operator` 大于
   - `Aggregation type` 总数
   - `Aggregation granularity (period)` 5 分钟
   - `Frequency of evaluation` 每隔 5 分钟
  
    | 范围 | 指标命名空间 | 指标 | 聚合 | 阈值 |
    |:---|:---|:---|:---|:---|
    | 存储名称 | 帐户 | 流入量 | Sum | 最大存储流入速率的 80%。 例如：在美国西部，常规用途 v2 为 60Gbps |
  
1. 修正操作
    - 使用单独的事件中心命名空间进行导出
    - Azure 存储标准帐户支持根据请求提高入口上限。 要请求提高上限，请联系 [Azure 支持](https://azure.microsoft.com/support/faq/)
    - 在其他存储帐户之间拆分表

#### <a name="recommendations-for-event-hub"></a>关于事件中心的建议

1. 使用以下设置来配置关于以下指标的警报： 
   - `Operator` 大于
   - `Aggregation type` 总数
   - `Aggregation granularity (period)` 5 分钟
   - `Frequency of evaluation` 每隔 5 分钟
  
    | 范围 | 指标命名空间 | 指标 | 聚合 | 阈值 |
    |:---|:---|:---|:---|:---|
    | 命名空间-名称 | 事件中心标准指标 | 传入字节数 | Sum | 每 5 分钟最大流入量的 80%。 例如，每个 TU 为 1MB/秒 |
    | 命名空间-名称 | 事件中心标准指标 | 传入消息数 | Sum | 每 5 分钟最大事件数的 80%。 例如，每个 TU 为 1000/s |
    | 命名空间-名称 | 事件中心标准指标 | 限制请求 | Sum | 介于请求的 1% 到 5% |

1. 修正操作
   - 增加限制单位 (TU) 的数量
   - 在其他命名空间之间拆分表
   - 使用高级版事件中心层级提高吞吐量

导出规则应该包含你在你的工作区中拥有的表。 运行此查询可获取工作区中可用表的列表。

```kusto
find where TimeGenerated > ago(24h) | distinct Type
```

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

不可用

# <a name="powershell"></a>[PowerShell](#tab/powershell)

空值

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

使用以下命令通过 CLI 创建有关导出到存储帐户的数据导出规则。

```azurecli
$storageAccountResourceId = '/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name'
az monitor log-analytics workspace data-export create --resource-group resourceGroupName --workspace-name workspaceName --name ruleName --tables SecurityEvent Heartbeat --destination $storageAccountResourceId
```

使用以下命令通过 CLI 创建有关导出到事件中心的数据导出规则。 为每个表创建一个单独的事件中心。

```azurecli
$eventHubsNamespacesResourceId = '/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.EventHub/namespaces/namespaces-name'
az monitor log-analytics workspace data-export create --resource-group resourceGroupName --workspace-name workspaceName --name ruleName --tables SecurityEvent Heartbeat --destination $eventHubsNamespacesResourceId
```

通过 CLI 使用以下命令，以便创建有关将数据导出到特定事件中心的数据导出规则。 所有表都将导出到所提供的事件中心名称。 

```azurecli
$eventHubResourceId = '/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.EventHub/namespaces/namespaces-name/eventhubs/eventhub-name'
az monitor log-analytics workspace data-export create --resource-group resourceGroupName --workspace-name workspaceName --name ruleName --tables SecurityEvent Heartbeat --destination $eventHubResourceId
```

# <a name="rest"></a>[REST](#tab/rest)

使用以下请求通过 REST API 创建数据导出规则。 该请求应使用持有者令牌授权和内容类型 application/json。

```rest
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports/<data-export-name>?api-version=2020-08-01
```

请求的正文指定了表目标。 以下是一个针对存储帐户的 REST 请求的正文示例。

```json
{
    "properties": {
        "destination": {
            "resourceId": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name"
        },
        "tablenames": [
            "table1",
            "table2" 
        ],
        "enable": true
    }
}
```

以下是一个针对事件中心的 REST 请求的正文示例。

```json
{
    "properties": {
        "destination": {
            "resourceId": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/Microsoft.EventHub/namespaces/eventhub-namespaces-name"
        },
        "tablenames": [
            "table1",
            "table2"
        ],
        "enable": true
    }
}
```

以下是一个针对事件中心的 REST 请求的正文示例，其中提供了事件中心名称。 在这种情况下，所有导出的数据都将发送到此事件中心。

```json
{
    "properties": {
        "destination": {
            "resourceId": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/Microsoft.EventHub/namespaces/eventhub-namespaces-name",
            "metaData": {
                "EventHubName": "eventhub-name"
        },
        "tablenames": [
            "table1",
            "table2"
        ],
        "enable": true
    }
  }
}
```

# <a name="template"></a>[模板](#tab/json)

通过模板使用以下命令，以便创建有关将数据导出到存储帐户的数据导出规则。

```
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "defaultValue": "workspace-name",
            "type": "String"
        },
        "workspaceLocation": {
            "defaultValue": "workspace-region",
            "type": "string"
        },
        "storageAccountRuleName": {
            "defaultValue": "storage-account-rule-name",
            "type": "string"
        },
        "storageAccountResourceId": {
            "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "microsoft.operationalinsights/workspaces",
            "apiVersion": "2020-08-01",
            "name": "[parameters('workspaceName')]",
            "location": "[parameters('workspaceLocation')]",
            "resources": [
                {
                  "type": "microsoft.operationalinsights/workspaces/dataexports",
                  "apiVersion": "2020-08-01",
                  "name": "[concat(parameters('workspaceName'), '/' , parameters('storageAccountRuleName'))]",
                  "dependsOn": [
                      "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]"
                  ],
                  "properties": {
                      "destination": {
                          "resourceId": "[parameters('storageAccountResourceId')]"
                      },
                      "tableNames": [
                          "Heartbeat",
                          "InsightsMetrics",
                          "VMConnection",
                          "Usage"
                      ],
                      "enable": true
                  }
              }
            ]
        }
    ]
}
```

通过模板使用以下命令，以便创建有关将数据导出到事件中心的数据导出规则。 为每个表创建一个单独的事件中心。

```
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "defaultValue": "workspace-name",
            "type": "String"
        },
        "workspaceLocation": {
            "defaultValue": "workspace-region",
            "type": "string"
        },
        "eventhubRuleName": {
            "defaultValue": "event-hub-rule-name",
            "type": "string"
        },
        "namespacesResourceId": {
            "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/microsoft.eventhub/namespaces/namespaces-name",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "microsoft.operationalinsights/workspaces",
            "apiVersion": "2020-08-01",
            "name": "[parameters('workspaceName')]",
            "location": "[parameters('workspaceLocation')]",
            "resources": [
              {
                  "type": "microsoft.operationalinsights/workspaces/dataexports",
                  "apiVersion": "2020-08-01",
                  "name": "[concat(parameters('workspaceName'), '/', parameters('eventhubRuleName'))]",
                  "dependsOn": [
                      "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]"
                  ],
                  "properties": {
                      "destination": {
                          "resourceId": "[parameters('namespacesResourceId')]"
                      },
                      "tableNames": [
                          "Usage",
                          "Heartbeat"
                      ],
                      "enable": true
                  }
              }
            ]
        }
    ]
}
```

通过模板使用以下命令，以便创建有关将数据导出到特定事件中心的数据导出规则。 所有表都将导出到所提供的事件中心名称。

```
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "defaultValue": "workspace-name",
            "type": "String"
        },
        "workspaceLocation": {
            "defaultValue": "workspace-region",
            "type": "string"
        },
        "eventhubRuleName": {
            "defaultValue": "event-hub-rule-name",
            "type": "string"
        },
        "namespacesResourceId": {
            "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/microsoft.eventhub/namespaces/namespaces-name",
            "type": "String"
        },
        "eventhubName": {
            "defaultValue": "event-hub-name",
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "microsoft.operationalinsights/workspaces",
            "apiVersion": "2020-08-01",
            "name": "[parameters('workspaceName')]",
            "location": "[parameters('workspaceLocation')]",
            "resources": [
              {
                  "type": "microsoft.operationalinsights/workspaces/dataexports",
                  "apiVersion": "2020-08-01",
                  "name": "[concat(parameters('workspaceName'), '/', parameters('eventhubRuleName'))]",
                  "dependsOn": [
                      "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]"
                  ],
                  "properties": {
                      "destination": {
                          "resourceId": "[parameters('namespacesResourceId')]",
                          "metaData": {
                              "eventHubName": "[parameters('eventhubName')]"
                          }
                      },
                      "tableNames": [
                          "Usage",
                          "Heartbeat"
                      ],
                      "enable": true
                  }
              }
            ]
        }
    ]
}
```

---

## <a name="view-data-export-rule-configuration"></a>查看数据导出规则配置

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

不可用

# <a name="powershell"></a>[PowerShell](#tab/powershell)

空值

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

使用以下命令通过 CLI 查看数据导出规则的配置。

```azurecli
az monitor log-analytics workspace data-export show --resource-group resourceGroupName --workspace-name workspaceName --name ruleName
```

# <a name="rest"></a>[REST](#tab/rest)

使用以下请求通过 REST API 查看数据导出规则的配置。 该请求应使用持有者令牌授权。

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports/<data-export-name>?api-version=2020-08-01
```

# <a name="template"></a>[模板](#tab/json)

空值

---

## <a name="disable-an-export-rule"></a>禁用导出规则

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

不可用

# <a name="powershell"></a>[PowerShell](#tab/powershell)

空值

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

如果在特定时间段（例如执行测试时）不需要保留数据，可以禁用导出规则来停止导出。 使用以下命令通过 CLI 禁用数据导出规则。

```azurecli
az monitor log-analytics workspace data-export update --resource-group resourceGroupName --workspace-name workspaceName --name ruleName --tables SecurityEvent Heartbeat --enable false
```

# <a name="rest"></a>[REST](#tab/rest)

如果在特定时间段（例如执行测试时）不需要保留数据，可以禁用导出规则来停止导出。 使用以下请求通过 REST API 禁用数据导出规则。 该请求应使用持有者令牌授权。

```rest
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports/<data-export-name>?api-version=2020-08-01
Authorization: Bearer <token>
Content-type: application/json

{
    "properties": {
        "destination": {
            "resourceId": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name"
        },
        "tablenames": [
"table1",
    "table2" 
        ],
        "enable": false
    }
}
```

# <a name="template"></a>[模板](#tab/json)

如果在特定时间段（例如执行测试时）不需要保留数据，可以禁用导出规则来停止导出。 在模板中设置 ```"enable": false``` 以禁用数据导出。

---

## <a name="delete-an-export-rule"></a>删除导出规则

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

不可用

# <a name="powershell"></a>[PowerShell](#tab/powershell)

空值

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

使用以下命令通过 CLI 删除数据导出规则。

```azurecli
az monitor log-analytics workspace data-export delete --resource-group resourceGroupName --workspace-name workspaceName --name ruleName
```

# <a name="rest"></a>[REST](#tab/rest)

使用以下请求通过 REST API 删除数据导出规则。 该请求应使用持有者令牌授权。

```rest
DELETE https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports/<data-export-name>?api-version=2020-08-01
```

# <a name="template"></a>[模板](#tab/json)

空值

---

## <a name="view-all-data-export-rules-in-a-workspace"></a>查看工作区中的所有数据导出规则

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

不可用

# <a name="powershell"></a>[PowerShell](#tab/powershell)

空值

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

使用以下命令通过 CLI 查看工作区中的所有数据导出规则。

```azurecli
az monitor log-analytics workspace data-export list --resource-group resourceGroupName --workspace-name workspaceName
```

# <a name="rest"></a>[REST](#tab/rest)

使用以下请求通过 REST API 查看工作区中的所有数据导出规则。 该请求应使用持有者令牌授权。

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports?api-version=2020-08-01
```

# <a name="template"></a>[模板](#tab/json)

空值

---

## <a name="unsupported-tables"></a>不受支持的表
如果数据导出规则包含不受支持的表，配置不会失败，但不会导出该表的任何数据。 如果该表在之后得到支持，则将在那时导出其数据。

如果数据导出规则包含不存在的表，操作会失败并出现错误“工作区中不存在表 \<tableName\>”。


## <a name="supported-tables"></a>受支持的表
受支持的表当前仅限于下面指定的那些表。 除非指定限制，否则将导出表中的所有数据。 此列表已更新，添加了对其他表的支持。

| 表 | 限制 |
|:---|:---|
| AACAudit |  |
| AACHttpRequest |  |
| AADDomainServicesAccountLogon |  |
| AADDomainServicesAccountManagement |  |
| AADDomainServicesDirectoryServiceAccess |  |
| AADDomainServicesLogonLogoff |  |
| AADDomainServicesPolicyChange |  |
| AADDomainServicesPrivilegeUse |  |
| AADManagedIdentitySignInLogs |  |
| AADNonInteractiveUserSignInLogs |  |
| AADProvisioningLogs |  |
| AADRiskyUsers |  |
| AADServicePrincipalSignInLogs |  |
| AADUserRiskEvents |  |
| ABSBotRequests |  |
| ACSAuthIncomingOperations |  |
| ACSBillingUsage |  |
| ACSChatIncomingOperations |  |
| ACSSMSIncomingOperations |  |
| ADAssessmentRecommendation |  |
| ADFActivityRun |  |
| ADFPipelineRun |  |
| ADFSSignInLogs |  |
| ADFTriggerRun |  |
| ADPAudit |  |
| ADPDiagnostics |  |
| ADPRequests |  |
| ADReplicationResult |  |
| ADSecurityAssessmentRecommendation |  |
| ADTDigitalTwinsOperation |  |
| ADTEventRoutesOperation |  |
| ADTModelsOperation |  |
| ADTQueryOperation |  |
| ADXCommand |  |
| ADXQuery |  |
| AegDeliveryFailureLogs |  |
| AegPublishFailureLogs |  |
| AEWAuditLogs |  |
| 警报 |  |
| AmlOnlineEndpointConsoleLog |  |
| ApiManagementGatewayLogs |  |
| AppCenterError |  |
| AppPlatformSystemLogs |  |
| AppServiceAppLogs |  |
| AppServiceAuditLogs |  |
| AppServiceConsoleLogs |  |
| AppServiceFileAuditLogs |  |
| AppServiceHTTPLogs |  |
| AppServicePlatformLogs |  |
| AuditLogs |  |
| AutoscaleEvaluationsLog |  |
| AutoscaleScaleActionsLog |  |
| AWSCloudTrail |  |
| AWSGuardDuty |  |
| AWSVPCFlow |  |
| AzureAssessmentRecommendation |  |
| AzureDevOpsAuditing |  |
| BehaviorAnalytics |  |
| BlockchainApplicationLog |  |
| BlockchainProxyLog |  |
| CDBCassandraRequests |  |
| CDBControlPlaneRequests |  |
| CDBDataPlaneRequests |  |
| CDBGremlinRequests |  |
| CDBMongoRequests |  |
| CDBPartitionKeyRUConsumption |  |
| CDBPartitionKeyStatistics |  |
| CDBQueryRuntimeStatistics |  |
| CommonSecurityLog |  |
| ComputerGroup |  |
| ConfigurationData | 部分支持 - 某些数据是通过不支持导出的内部服务引入的。 当前导出中缺少此部分。 |
| ContainerImageInventory |  |
| ContainerInventory |  |
| ContainerLog |  |
| ContainerLogV2 |  |
| ContainerNodeInventory |  |
| ContainerServiceLog |  |
| CoreAzureBackup |  |
| DatabricksAccounts |  |
| DatabricksClusters |  |
| DatabricksDBFS |  |
| DatabricksInstancePools |  |
| DatabricksJobs |  |
| DatabricksNotebook |  |
| DatabricksSecrets |  |
| DatabricksSQLPermissions |  |
| DatabricksSSH |  |
| DatabricksWorkspace |  |
| DeviceNetworkInfo |  |
| DnsEvents |  |
| DnsInventory |  |
| DummyHydrationFact |  |
| Dynamics365Activity |  |
| EmailAttachmentInfo |  |
| EmailEvents |  |
| EmailPostDeliveryEvents |  |
| EmailUrlInfo |  |
| 事件 | 部分支持 - 导出完全支持来自 Log Analytics 代理 (MMA) 或 Azure Monitor 代理 (AMA) 的数据。 通过诊断扩展代理到达的数据是通过存储收集的，而导出不支持此路径。2 |
| ExchangeAssessmentRecommendation |  |
| FailedIngestion |  |
| FunctionAppLogs |  |
| HDInsightAmbariClusterAlerts |  |
| HDInsightAmbariSystemMetrics |  |
| HDInsightHadoopAndYarnLogs |  |
| HDInsightHadoopAndYarnMetrics |  |
| HDInsightHBaseLogs |  |
| HDInsightHBaseMetrics |  |
| HDInsightHiveAndLLAPLogs |  |
| HDInsightHiveAndLLAPMetrics |  |
| HDInsightHiveTezAppStats |  |
| HDInsightJupyterNotebookEvents |  |
| HDInsightKafkaLogs |  |
| HDInsightKafkaMetrics |  |
| HDInsightOozieLogs |  |
| HDInsightSecurityLogs |  |
| HDInsightSparkApplicationEvents |  |
| HDInsightSparkBlockManagerEvents |  |
| HDInsightSparkEnvironmentEvents |  |
| HDInsightSparkExecutorEvents |  |
| HDInsightSparkJobEvents |  |
| HDInsightSparkLogs |  |
| HDInsightSparkSQLExecutionEvents |  |
| HDInsightSparkStageEvents |  |
| HDInsightSparkStageTaskAccumulables |  |
| HDInsightSparkTaskEvents |  |
| 检测信号 |  |
| HuntingBookmark |  |
| InsightsMetrics | 部分支持 - 某些数据是通过不支持导出的内部服务引入的。 当前导出中缺少此部分。 |
| IntuneAuditLogs |  |
| IntuneDevices |  |
| IntuneOperationalLogs |  |
| KubeEvents |  |
| KubeHealth |  |
| KubeMonAgentEvents |  |
| KubeNodeInventory |  |
| KubePodInventory |  |
| KubeServices |  |
| LAQueryLogs |  |
| McasShadowItReporting |  |
| MCCEventLogs |  |
| MicrosoftAzureBastionAuditLogs |  |
| MicrosoftDataShareReceivedSnapshotLog |  |
| MicrosoftDataShareSentSnapshotLog |  |
| MicrosoftDataShareShareLog |  |
| MicrosoftHealthcareApisAuditLogs |  |
| NWConnectionMonitorPathResult |  |
| NWConnectionMonitorTestResult |  |
| OfficeActivity | 政府云中的部分支持 - 某些数据是通过 Webhook 从 O365 引入 LA 的。 当前导出中缺少此部分。 |
| Operation | 部分支持 - 某些数据是通过不支持导出的内部服务引入的。 当前导出中缺少此部分。 |
| 性能 | 部分支持 – 目前仅支持 Windows 性能数据。 当前导出中缺少 Linux 性能数据。 |
| PowerBIDatasetsWorkspace |  |
| PurviewScanStatusLogs |  |
| SCCMAssessmentRecommendation |  |
| SCOMAssessmentRecommendation |  |
| SecurityAlert |  |
| SecurityBaseline |  |
| SecurityBaselineSummary |  |
| SecurityCef |  |
| SecurityDetection |  |
| SecurityEvent | 部分支持 - 导出完全支持来自 Log Analytics 代理 (MMA) 或 Azure Monitor 代理 (AMA) 的数据。 通过诊断扩展代理到达的数据是通过存储收集的，而导出不支持此路径。2 |
| SecurityIncident |  |
| SecurityIoTRawEvent |  |
| SecurityNestedRecommendation |  |
| SecurityRecommendation |  |
| SentinelHealth |  |
| SfBAssessmentRecommendation |  |
| SfBOnlineAssessmentRecommendation |  |
| SharePointOnlineAssessmentRecommendation |  |
| SignalRServiceDiagnosticLogs |  |
| SigninLogs |  |
| SPAssessmentRecommendation |  |
| SQLAssessmentRecommendation |  |
| SQLSecurityAuditEvents |  |
| SucceededIngestion |  |
| SynapseBigDataPoolApplicationsEnded |  |
| SynapseBuiltinSqlPoolRequestsEnded |  |
| SynapseGatewayApiRequests |  |
| SynapseIntegrationActivityRuns |  |
| SynapseIntegrationPipelineRuns |  |
| SynapseIntegrationTriggerRuns |  |
| SynapseRbacOperations |  |
| SynapseSqlPoolDmsWorkers |  |
| SynapseSqlPoolExecRequests |  |
| SynapseSqlPoolRequestSteps |  |
| SynapseSqlPoolSqlRequests |  |
| SynapseSqlPoolWaits |  |
| Syslog | 部分支持 - 导出完全支持来自 Log Analytics 代理 (MMA) 或 Azure Monitor 代理 (AMA) 的数据。 通过诊断扩展代理到达的数据是通过存储收集的，而导出不支持此路径。2 |
| ThreatIntelligenceIndicator |  |
| 更新 | 部分支持 - 某些数据是通过不支持导出的内部服务引入的。 当前导出中缺少此部分。 |
| UpdateRunProgress |  |
| UpdateSummary |  |
| 使用情况 |  |
| UserAccessAnalytics |  |
| UserPeerAnalytics |  |
| Watchlist |  |
| WindowsEvent |  |
| WindowsFirewall |  |
| WireData | 部分支持 - 某些数据是通过不支持导出的内部服务引入的。 当前导出中缺少此部分。 |
| WorkloadDiagnosticLogs |  |
| WVDAgentHealthStatus |  |
| WVDCheckpoints |  |
| WVDConnections |  |
| WVDErrors |  |
| WVDFeeds |  |
| WVDManagement |  |

## <a name="next-steps"></a>后续步骤

- [从 Azure 数据资源管理器查询导出的数据](../logs/azure-data-explorer-query-storage.md)。
