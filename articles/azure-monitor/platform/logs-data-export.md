---
title: Azure Monitor 中的 Log Analytics 工作区数据导出功能（预览版）
description: 使用 Log Analytics 数据导出功能，可以在收集 Log Analytics 工作区中所选表的数据时，将数据持续导出到 Azure 存储帐户或 Azure 事件中心。
ms.subservice: logs
ms.topic: conceptual
ms.custom: references_regions, devx-track-azurecli
author: bwren
ms.author: bwren
ms.date: 10/14/2020
ms.openlocfilehash: 4155cda1e1de6f15aefa6d5fc960988eba15068d
ms.sourcegitcommit: 287c20509c4cf21d20eea4619bbef0746a5cd46e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/14/2020
ms.locfileid: "97371962"
---
# <a name="log-analytics-workspace-data-export-in-azure-monitor-preview"></a>Azure Monitor 中的 Log Analytics 工作区数据导出功能（预览版）
使用 Azure Monitor 中的 Log Analytics 工作区数据导出功能，可以在收集 Log Analytics 工作区中所选表的数据时，将数据持续导出到 Azure 存储帐户或 Azure 事件中心。 本文提供了有关此功能的详细信息以及在工作区中配置数据导出的步骤。

## <a name="overview"></a>概述
为 Log Analytics 工作区配置数据导出后，发送到工作区中所选表的任何新数据每小时都会自动导出到你的存储帐户，或准实时导出到你的事件中心。

![数据导出概述](media/logs-data-export/data-export-overview.png)

包含的表中的所有数据导出时不会经过筛选。 例如，为 SecurityEvent 表配置数据导出规则时，发送到 SecurityEvent 表的所有数据将从配置时开始导出 。


## <a name="other-export-options"></a>其他导出选项
Log Analytics 工作区数据导出会持续从 Log Analytics 工作区导出数据。 针对特定场景导出数据的其他选项包括：

- 使用逻辑应用从日志查询进行计划性导出。 这类似于数据导出功能，但你可向 Azure 存储发送经过筛选或聚合的数据。 不过，此方法受限于[日志查询限制](../service-limits.md#log-analytics-workspaces)。请参阅[使用逻辑应用将 Log Analytics 工作区中的数据存档到 Azure 存储](logs-export-logic-app.md)。
- 使用逻辑应用一次性导出。 请参阅[适用于逻辑应用和 Power Automate 的 Azure Monitor 日志连接器](logicapp-flow-connector.md)。
- 使用 PowerShell 脚本一次性导出到本地计算机。 请参阅 [Invoke-AzOperationalInsightsQueryExport](https://www.powershellgallery.com/packages/Invoke-AzOperationalInsightsQueryExport)。


## <a name="current-limitations"></a>当前限制

- 当前只能使用 CLI 或 REST 请求执行配置， 无法使用 Azure 门户或 PowerShell。
- CLI 和 REST 中的 ```--export-all-tables``` 选项不受支持，将被删除。 你应在导出规则中显式提供表的列表。
- 受支持的表当前仅限于下面[受支持的表](#supported-tables)部分中指定的那些。 如果数据导出规则包含不受支持的表，操作不会失败，但不会导出该表的任何数据。 如果数据导出规则包含不存在的表，操作会失败并出现错误 ```Table <tableName> does not exist in the workspace.```
- Log Analytics 工作区可以位于除以下区域外的任何区域：
  - 瑞士北部
  - 瑞士西部
  - Azure 政府区域
- 目标存储帐户或事件中心必须与 Log Analytics 工作区位于同一区域。
- 对于存储帐户，要导出的表的名称不能超过 60 个字符，而对于事件中心，不能超过 47 个字符。 名称较长的表将不会被导出。

> [!NOTE]
> Log Analytics 数据导出将数据作为追加 Blob 写入，该功能当前在 Azure Data Lake Storage Gen2 中为预览版。 配置以此存储为导出位置的导出之前，需要提出支持请求。 对于此请求，请使用以下详细信息。
> - 问题类型：技术
> - 订阅：你的订阅
> - 服务：Data Lake Storage Gen2
> - 资源：你的资源名称
> - 摘要：请求订阅注册以接受来自 Log Analytics 数据导出的数据。
> - 问题类型：连接
> - 问题子类型：连接性问题

## <a name="data-completeness"></a>数据完整性
如果目标不可用，数据导出会继续重新尝试发送数据，最多持续 30 分钟。 如果 30 分钟后仍不可用，数据将被放弃，直到目标变为可用。

## <a name="cost"></a>成本
数据导出功能当前不收取额外费用。 数据导出的定价将在以后公布，在开始计费之前将提供相关通知。 如果你选择在通知期后继续使用数据导出，则将按照适用的费率缴费。

## <a name="export-destinations"></a>导出目标

### <a name="storage-account"></a>存储帐户
数据以每小时为频率发送到存储帐户。 “数据导出配置”为存储帐户中的每个表创建一个容器，其名称为 am- 后跟表的名称。 例如，表 SecurityEvent 将发送到名为 am-SecurityEvent 的容器中 。

存储帐户 Blob 路径为 WorkspaceResourceId=/subscriptions/subscription-id/resourcegroups/\<resource-group\>/providers/microsoft.operationalinsights/workspaces/\<workspace\>/y=\<four-digit numeric year\>/m=\<two-digit numeric month\>/d=\<two-digit numeric day\>/h=\<two-digit 24-hour clock hour\>/m=00/PT1H.json。 由于追加 Blob 在存储中的写入限制为 50K，如果追加的数量很大，则导出的 Blob 的数量可能会增加。 在这种情况下，Blob 的命名模式将为 PT1H_#.json，其中 # 是增量 Blob 计数。

存储帐户数据格式为 [JSON 行](./resource-logs-blob-format.md)。 这意味着每个记录将由换行符分隔，JSON 记录之间没有外部记录数组和逗号。 

[![存储示例数据](media/logs-data-export/storage-data.png)](media/logs-data-export/storage-data.png#lightbox)

当基于时间的保留策略启用了 allowProtectedAppendWrites 设置时，Log Analytics 数据导出可以将追加 Blob 写入不可变存储帐户。 该设置允许将新块写入追加 Blob，同时维持不可变性保护和合规性。 请参阅[允许受保护的追加 Blob 写入](../../storage/blobs/storage-blob-immutable-storage.md#allow-protected-append-blobs-writes)。

### <a name="event-hub"></a>事件中心
数据到达 Azure Monitor 时，将准实时地发送到事件中心。 将为导出的每个数据类型创建一个事件中心，其名称为 am- 后跟表的名称。 例如，表 SecurityEvent 将发送到名为 am-SecurityEvent 的事件中心中 。 如果要将导出的数据传递到特定事件中心，或者有一个表的名称超过了 47 个字符的限制，则可提供自己的事件中心名称并将定义的表的所有数据导出到该事件中心。

注意事项：
1. "基本" 事件中心 sku 支持较低的事件大小限制，工作区中的某些日志可能会超出此 [限制](../../event-hubs/event-hubs-quotas.md#basic-vs-standard-tiers) 并被删除。 建议使用 "标准" 或 "专用" 事件中心作为导出目标。
2. 导出的数据量通常会随时间的推移而增加，需要扩大事件中心的规模，以适应更高的传输速率并避免出现限制情况和数据延迟。 应使用事件中心的自动膨胀功能来自动进行纵向扩展和增加吞吐量单位数，以满足使用量需求。 有关详细信息，请参阅[自动增加 Azure 事件中心吞吐量单位](../../event-hubs/event-hubs-auto-inflate.md)。

## <a name="prerequisites"></a>先决条件
以下是在配置 Log Analytics 数据导出之前必须完成的先决条件。

- 存储帐户或事件中心必须已创建，并且必须与 Log Analytics 工作区位于同一区域。 如果需要将数据复制到其他存储帐户，可使用任何 [Azure 存储冗余选项](../../storage/common/storage-redundancy.md)。  
- 存储帐户必须是 StorageV1 或 StorageV2。 不支持经典存储  
- 如果你已将存储帐户配置为允许从所选网络进行访问，则需要在存储帐户设置中添加一个例外来允许 Azure Monitor 写入存储。

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
数据导出规则定义要为一组表导出到单个目标的数据。 可为每个目标创建一个规则。


# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

空值

# <a name="powershell"></a>[PowerShell](#tab/powershell)

空值

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

使用以下 CLI 命令查看工作区中的表。 它可帮助复制所需的表并将其包含在数据导出规则中。

```azurecli
az monitor log-analytics workspace table list -resource-group resourceGroupName --workspace-name workspaceName --query [].name --output table
```

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

使用以下命令，使用 CLI 创建到特定事件中心的数据导出规则。 所有表都导出到提供的事件中心名称。 

```azurecli
$eventHubResourceId = '/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.EventHub/namespaces/namespaces-name/eventHubName/eventhub-name'
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
---

## <a name="view-data-export-rule-configuration"></a>查看数据导出规则配置

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

空值

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
---

## <a name="disable-an-export-rule"></a>禁用导出规则

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

空值

# <a name="powershell"></a>[PowerShell](#tab/powershell)

空值

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

如果在特定时间段（例如执行测试时）不需要保留数据，可以禁用导出规则来停止导出。 使用以下命令通过 CLI 禁用数据导出规则。

```azurecli
az monitor log-analytics workspace data-export update --resource-group resourceGroupName --workspace-name workspaceName --name ruleName --enable false
```

# <a name="rest"></a>[REST](#tab/rest)

使用以下请求通过 REST API 禁用数据导出规则。 该请求应使用持有者令牌授权。

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
---

## <a name="delete-an-export-rule"></a>删除导出规则

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

空值

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
---

## <a name="view-all-data-export-rules-in-a-workspace"></a>查看工作区中的所有数据导出规则

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

空值

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
---

## <a name="unsupported-tables"></a>不受支持的表
如果数据导出规则包含不受支持的表，配置不会失败，但不会导出该表的任何数据。 如果该表在之后得到支持，则将在那时导出其数据。

如果数据导出规则包含不存在的表，它将失败，并出现错误 " <tableName> 工作区中不存在表"。


## <a name="supported-tables"></a>受支持的表
受支持的表当前仅限于下面指定的那些表。 除非指定限制，否则将导出表中的所有数据。 如果添加了对其他表的支持，此列表将会更新。


| 表 | 限制 |
|:---|:---|
| AADDomainServicesAccountLogon | |
| AADDomainServicesAccountManagement | |
| AADDomainServicesDirectoryServiceAccess | |
| AADDomainServicesLogonLogoff | |
| AADDomainServicesPolicyChange | |
| AADDomainServicesPrivilegeUse | |
| AADManagedIdentitySignInLogs | |
| AADNonInteractiveUserSignInLogs | |
| AADProvisioningLogs | |
| AADServicePrincipalSignInLogs | |
| ADAssessmentRecommendation | |
| ADFActivityRun | |
| ADFPipelineRun | |
| ADFTriggerRun | |
| ADReplicationResult | |
| ADSecurityAssessmentRecommendation | |
| ADTDigitalTwinsOperation | |
| ADTEventRoutesOperation | |
| ADTModelsOperation | |
| ADTQueryOperation | |
| ADXCommand | |
| ADXQuery | |
| AegDeliveryFailureLogs | |
| AegPublishFailureLogs | |
| 警报 |部分支持。 此表中的某些数据是通过存储帐户引入的。 当前不导出此数据。 |
| 异常 | |
| ApiManagementGatewayLogs | |
| AppCenterError | |
| AppPlatformSystemLogs | |
| AppServiceAppLogs | |
| AppServiceAuditLogs | |
| AppServiceConsoleLogs | |
| AppServiceFileAuditLogs | |
| AppServiceHTTPLogs | |
| AppServiceIPSecAuditLogs | |
| AppServicePlatformLogs | |
| AuditLogs | |
| AutoscaleEvaluationsLog | |
| AutoscaleScaleActionsLog | |
| AWSCloudTrail | |
| AzureAssessmentRecommendation | |
| AzureDevOpsAuditing | |
| BehaviorAnalytics | |
| BlockchainApplicationLog | |
| BlockchainProxyLog | |
| BlockchainProxyLog | |
| CommonSecurityLog | |
| CommonSecurityLog | |
| ComputerGroup | |
| ConfigurationData | 部分支持。 某些数据是通过不支持导出的内部服务引入的。 当前不导出此数据。 |
| ContainerImageInventory | |
| ContainerInventory | |
| ContainerLog | |
| ContainerNodeInventory | |
| ContainerServiceLog | |
| CoreAzureBackup | |
| DatabricksAccounts | |
| DatabricksClusters | |
| DatabricksDBFS | |
| DatabricksInstancePools | |
| DatabricksJobs | |
| DatabricksNotebook | |
| DatabricksSecrets | |
| DatabricksSQLPermissions | |
| DatabricksSSH | |
| DatabricksWorkspace | |
| DnsEvents | |
| DnsInventory | |
| Dynamics365Activity | |
| 事件 | 部分支持。 此表中的某些数据是通过存储帐户引入的。 当前不导出此数据。 |
| ExchangeAssessmentRecommendation | |
| FailedIngestion | |
| FunctionAppLogs | |
| HDInsightAmbariClusterAlerts | |
| HDInsightAmbariSystemMetrics | |
| HDInsightGatewayAuditLogs | |
| HDInsightHadoopAndYarnLogs | |
| HDInsightHadoopAndYarnMetrics | |
| HDInsightHBaseLogs | |
| HDInsightHBaseMetrics | |
| HDInsightHiveAndLLAPLogsSample | |
| HDInsightKafkaLogs | |
| HDInsightKafkaMetrics | |
| HDInsightOozieLogs | |
| HDInsightSecurityLogs | |
| HDInsightSparkApplicationEvents | |
| HDInsightSparkBlockManagerEvents | |
| HDInsightSparkEnvironmentEvents | |
| HDInsightSparkEventsLog | |
| HDInsightSparkExecutorEvents | |
| HDInsightSparkExtraEvents | |
| HDInsightSparkJobEvents | |
| HDInsightSparkLogs | |
| HDInsightSparkSQLExecutionEvents | |
| HDInsightSparkStageEvents | |
| HDInsightSparkStageTaskAccumulables | |
| HDInsightSparkTaskEvents | |
| HDInsightStormLogs | |
| HDInsightStormMetrics | |
| HDInsightStormTopologyMetrics | |
| 检测信号 | |
| HuntingBookmark | |
| InsightsMetrics | 部分支持。 某些数据是通过不支持导出的内部服务引入的。 当前导出中缺少此部分。 |
| IntuneAuditLogs | |
| IntuneDeviceComplianceOrg | |
| IntuneOperationalLogs | |
| KubeEvents | |
| KubeHealth | |
| KubeMonAgentEvents | |
| KubeNodeInventory | |
| KubePodInventory | |
| KubeServices | |
| KubeServices | |
| LAQueryLogs | |
| McasShadowItReporting | |
| MicrosoftAzureBastionAuditLogs | |
| MicrosoftDataShareReceivedSnapshotLog | |
| MicrosoftDataShareSentSnapshotLog | |
| MicrosoftDataShareShareLog | |
| MicrosoftHealthcareApisAuditLogs | |
| NWConnectionMonitorDestinationListenerResult | |
| NWConnectionMonitorDNSResult | |
| NWConnectionMonitorPathResult | |
| NWConnectionMonitorPathResult | |
| NWConnectionMonitorTestResult | |
| NWConnectionMonitorTestResult | |
| OfficeActivity | 部分支持。 某些数据通过 Webhook 从 Office 365 引入到 Log Analytics。 当前不导出此数据。 |
| 操作 | 部分支持。 某些数据是通过不支持导出的内部服务引入的。 当前不导出此数据。 |
| 性能 | 部分支持。 当前仅支持 Windows 性能数据。 当前未导出 Linux 性能数据。 |
| ProtectionStatus | |
| SCCMAssessmentRecommendation | |
| SCOMAssessmentRecommendation | |
| SecurityAlert | |
| SecurityBaseline | |
| SecurityBaselineSummary | |
| SecurityDetection | |
| SecurityEvent | |
| SecurityIncident | |
| SecurityIoTRawEvent | |
| SecurityNestedRecommendation | |
| SecurityRecommendation | |
| SfBAssessmentRecommendation | |
| SfBOnlineAssessmentRecommendation | |
| SharePointOnlineAssessmentRecommendation | |
| SignalRServiceDiagnosticLogs | |
| SigninLogs | |
| SPAssessmentRecommendation | |
| SQLAssessmentRecommendation | |
| SucceededIngestion | |
| SynapseGatewayEvents | |
| SynapseRBACEvents | |
| Syslog | 部分支持。 此表中的某些数据是通过存储帐户引入的。 当前不导出此数据。 |
| ThreatIntelligenceIndicator | |
| 更新 | 部分支持。 某些数据是通过不支持导出的内部服务引入的。 当前不导出此数据。 |
| UpdateRunProgress | |
| UpdateSummary | |
| 使用情况 | |
| UserAccessAnalytics | |
| UserPeerAnalytics | |
| Watchlist | |
| WindowsEvent | |
| WindowsFirewall | |
| WireData | 部分支持。 某些数据是通过不支持导出的内部服务引入的。 当前不导出此数据。 |
| WorkloadMonitoringPerf | |
| WVDAgentHealthStatus | |
| WVDCheckpoints | |
| WVDConnections | |
| WVDErrors | |
| WVDFeeds | |
| WVDManagement | |


## <a name="next-steps"></a>后续步骤

- [从 Azure 数据资源管理器查询导出的数据](azure-data-explorer-query-storage.md)。