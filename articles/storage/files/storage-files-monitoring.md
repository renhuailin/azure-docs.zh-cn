---
title: 监视 Azure 文件存储 | Microsoft Docs
description: 了解如何监视 Azure 文件存储的性能和可用性。 监视 Azure 文件存储数据，了解配置以及分析指标和日志数据。
author: normesta
services: storage
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 3/02/2021
ms.author: normesta
ms.reviewer: fryu
ms.custom: monitoring, devx-track-csharp, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 69724711421182b2a530a2411b6ecc31b95a1388
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124823591"
---
# <a name="monitoring-azure-files"></a>监视 Azure 文件存储

如果你有依赖 Azure 资源的关键应用程序和业务流程，则需要监视这些资源的可用性、性能和操作。 本文介绍 Azure 文件存储生成的监视数据，以及如何使用 Azure Monitor 的各种功能分析这些数据的相关警报。

## <a name="applies-to"></a>适用于
| 文件共享类型 | SMB | NFS |
|-|:-:|:-:|
| 标准文件共享 (GPv2)、LRS/ZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |
| 标准文件共享 (GPv2)、GRS/GZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |
| 高级文件共享 (FileStorage)、LRS/ZRS | ![是](../media/icons/yes-icon.png) | ![是](../media/icons/yes-icon.png) |

## <a name="monitor-overview"></a>Monitor 概述

每种 Azure 文件存储资源的 Azure 门户“概述”页都包含资源使用情况（例如请求和每小时计费）的简要视图。 这些信息非常有用，但只提供少量监视数据。 创建资源后，其中的某些数据会自动收集，并可供分析。 你可以使用某些配置启用其他数据收集类型。

## <a name="what-is-azure-monitor"></a>说明是 Azure Monitor？
Azure 文件存储使用 [Azure Monitor](../../azure-monitor/overview.md) 来创建监视数据，Azure Monitor 是 Azure 中的一项完整堆栈监视服务。 Azure Monitor 提供了一整套用于监视 Azure 资源以及其他云和本地资源的功能。 

可先阅读文章[使用 Azure Monitor 监视 Azure 资源](../../azure-monitor/essentials/monitor-azure-resource.md)，其中介绍了以下内容：

- 说明是 Azure Monitor？
- 与监视相关的成本
- 监视 Azure 中收集的数据
- 配置数据收集
- Azure 中用于分析监视数据并就其发出警报的标准工具

本文中的以下各部分将介绍从 Azure 文件存储收集的特定数据。 其中的示例演示了如何配置数据收集并通过 Azure 工具分析这些数据。

## <a name="monitoring-data"></a>监视数据

Azure 文件存储会收集与其他 Azure 资源类型相同的监视数据，如[监视 Azure 资源中的数据](../../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data)中所述。 

有关 Azure 文件存储创建的指标和日志指标的详细信息，请参阅 [Azure 文件存储监视数据参考](storage-files-monitoring-reference.md)。

Azure Monitor 中的指标和日志仅支持 Azure 资源管理器存储帐户。 Azure Monitor 不支持经典存储帐户。 如果要使用经典存储帐户上的指标或日志，则需要迁移到 Azure 资源管理器存储帐户。 请参阅[迁移到 Azure 资源管理器](../../virtual-machines/migration-classic-resource-manager-overview.md)。

## <a name="collection-and-routing"></a>收集和路由

平台指标和活动日志会自动收集，但你可以使用诊断设置将其路由到其他位置。 

要收集资源日志，必须创建诊断设置。 创建设置时，选择“文件”作为要为其启用日志的存储类型。 然后，指定需要收集日志的以下操作类别之一。 

| 类别 | 说明 |
|:---|:---|
| StorageRead | 对象上的读取操作。 |
| StorageWrite | 对象上的写入操作。 |
| StorageDelete | 对象上的删除操作。 |

若要获取记录的 SMB 和 REST 操作列表，请参阅[存储记录的操作和状态消息](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)和 [Azure 文件存储监视数据参考](storage-files-monitoring-reference.md)。

## <a name="creating-a-diagnostic-setting"></a>创建诊断设置

可以使用 Azure 门户、PowerShell、Azure CLI、Azure 资源管理器模板或 Azure Policy 创建诊断设置。

> [!NOTE]
> Azure Monitor 中的 Azure 存储日志目前为公共预览版，可在所有公有云和美国政府云区域中用于预览测试。 此预览版支持 blob（包括 Azure Data Lake Storage Gen2）、文件、队列和表日志。 此功能适用于使用 Azure 资源管理器部署模型创建的所有存储帐户。 请参阅[存储帐户概述](../common/storage-account-overview.md)。

有关一般指南，请参阅[创建诊断设置以收集 Azure 中的平台日志和指标](../../azure-monitor/essentials/diagnostic-settings.md)。

### <a name="azure-portal"></a>[Azure 门户](#tab/azure-portal)

1. 登录到 Azure 门户。

2. 导航到存储帐户。

3. 在“监视”部分，单击“诊断设置(预览)”。

   > [!div class="mx-imgBorder"]
   > ![门户 - 诊断日志](media/storage-files-monitoring/diagnostic-logs-settings-pane.png)   

4. 选择“文件”作为要为其启用日志的存储类型。

5. 单击“添加诊断设置”。

   > [!div class="mx-imgBorder"]
   > ![门户 - 资源日志 - 添加诊断设置](media/storage-files-monitoring/diagnostic-logs-settings-pane-2.png)

   此时将显示“诊断设置”页。

   > [!div class="mx-imgBorder"]
   > ![资源日志页](media/storage-files-monitoring/diagnostic-logs-page.png)

6. 在该页的“名称”字段中，输入此资源日志设置的名称。 然后，选择要记录的操作（读取、写入和删除操作），以及要将日志发送到的位置。

#### <a name="archive-logs-to-a-storage-account"></a>将日志存档到存储帐户

如果你选择将日志存档到存储帐户，则需要为发送到存储帐户的日志量付费。 有关具体定价，请参阅 [Azure Monitor 定价](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)页的“平台日志”部分。

1. 选中“存档到存储帐户”复选框，然后单击“配置”按钮。

   > [!div class="mx-imgBorder"]   
   > ![“诊断设置”页的存档存储](media/storage-files-monitoring/diagnostic-logs-settings-pane-archive-storage.png)

2. 在“存储帐户”下拉列表中，选择要将日志存档到的存储帐户，单击“确定”按钮，然后单击“保存”按钮。

   [!INCLUDE [no retention policy](../../../includes/azure-storage-logs-retention-policy.md)]

   > [!NOTE]
   > 在你选择存储帐户作为导出目标之前，请参阅[存档 Azure 资源日志](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)，了解存储帐户的先决条件。

#### <a name="stream-logs-to-azure-event-hubs"></a>将日志流式传输到 Azure 事件中心

如果你选择将日志流式传输到事件中心，则需要为发送到事件中心的日志量付费。 有关具体定价，请参阅 [Azure Monitor 定价](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)页的“平台日志”部分。

1. 选中“流式传输到事件中心”复选框，然后单击“配置”按钮。

2. 在“选择事件中心”窗格中，选择要将日志流式传输到的事件中心的命名空间、名称和策略名称。 

   > [!div class="mx-imgBorder"]
   > ![“诊断设置”页的事件中心](media/storage-files-monitoring/diagnostic-logs-settings-pane-event-hub.png)

3. 单击“确定”按钮，然后单击“保存”按钮。

#### <a name="send-logs-to-azure-log-analytics"></a>将日志发送到 Azure Log Analytics

1. 选中“发送到 Log Analytics”复选框，选择 Log Analytics 工作区，然后单击“保存”按钮。

   > [!div class="mx-imgBorder"]   
   > ![“诊断设置”页的 Log Analytic](media/storage-files-monitoring/diagnostic-logs-settings-pane-log-analytics.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. 打开 Windows PowerShell 命令窗口，使用 `Connect-AzAccount` 命令登录到 Azure 订阅。 然后，按照屏幕指令进行操作。

   ```powershell
   Connect-AzAccount
   ```

2. 将活动订阅设置为要为其启用日志记录的存储帐户的订阅。

   ```powershell
   Set-AzContext -SubscriptionId <subscription-id>
   ```

#### <a name="archive-logs-to-a-storage-account"></a>将日志存档到存储帐户

如果你选择将日志存档到存储帐户，则需要为发送到存储帐户的日志量付费。 有关具体定价，请参阅 [Azure Monitor 定价](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)页的“平台日志”部分。

使用 [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) PowerShell cmdlet 和 `StorageAccountId` 参数来启用日志。

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -StorageAccountId <storage-account-resource-id> -Enabled $true -Category <operations-to-log> 
```

将此代码片段中的 `<storage-service-resource--id>` 占位符替换为 Azure 文件存储服务的资源 ID。 通过打开存储帐户的“属性”页，可在 Azure 门户中找到资源 ID。

Category 参数的值可以为 `StorageRead`、`StorageWrite` 和 `StorageDelete`。

[!INCLUDE [no retention policy](../../../includes/azure-storage-logs-retention-policy.md)]

下面是一个示例：

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/fileServices/default -StorageAccountId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount -Enabled $true -Category StorageWrite,StorageDelete`

有关每个参数的说明，请参阅[通过 Azure PowerShell 存档 Azure 资源日志](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)。

#### <a name="stream-logs-to-an-event-hub"></a>将日志流式传输到事件中心

如果你选择将日志流式传输到事件中心，则需要为发送到事件中心的日志量付费。 有关具体定价，请参阅 [Azure Monitor 定价](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)页的“平台日志”部分。

使用 [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) PowerShell cmdlet 和 `EventHubAuthorizationRuleId` 参数来启用日志。

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -EventHubAuthorizationRuleId <event-hub-namespace-and-key-name> -Enabled $true -Category <operations-to-log> -RetentionEnabled <retention-bool> -RetentionInDays <number-of-days>
```

下面是一个示例：

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/fileServices/default -EventHubAuthorizationRuleId /subscriptions/20884142-a14v3-4234-5450-08b10c09f4/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhubnamespace/authorizationrules/RootManageSharedAccessKey -Enabled $true -Category StorageDelete`

有关每个参数的说明，请参阅[通过 PowerShell cmdlet 将数据流式传输到事件中心](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs)。

#### <a name="send-logs-to-log-analytics"></a>将日志发送到 Log Analytics

使用 [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) PowerShell cmdlet 和 `WorkspaceId` 参数来启用日志。

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -WorkspaceId <log-analytics-workspace-resource-id> -Enabled $true -Category <operations-to-log> -RetentionEnabled <retention-bool> -RetentionInDays <number-of-days>
```

下面是一个示例：

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/fileServices/default -WorkspaceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.OperationalInsights/workspaces/my-analytic-workspace -Enabled $true -Category StorageDelete`

有关详细信息，请参阅[在 Azure Monitor 中将 Azure 资源日志流式传输到 Log Analytics 工作区](../../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace)。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. 首先，打开 [Azure Cloud Shell](../../cloud-shell/overview.md)，或者，如果已在本地[安装](/cli/azure/install-azure-cli) Azure CLI，请打开命令控制台应用程序，如 Windows PowerShell。

2. 如果你的标识已关联到多个订阅，请将活动订阅设置为要为其启用日志的存储帐户的订阅。

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   将 `<subscription-id>` 占位符值替换为你的订阅 ID。

#### <a name="archive-logs-to-a-storage-account"></a>将日志存档到存储帐户

如果你选择将日志存档到存储帐户，则需要为发送到存储帐户的日志量付费。 有关具体定价，请参阅 [Azure Monitor 定价](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)页的“平台日志”部分。

使用 [az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create) 命令启用日志。

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --storage-account <storage-account-name> --resource <storage-service-resource-id> --resource-group <resource-group> --logs '[{"category": <operations>, "enabled": true}]'
```

将此代码片段中的 `<storage-service-resource--id>` 占位符替换为 Blob 存储服务的资源 ID。 通过打开存储帐户的“属性”页，可在 Azure 门户中找到资源 ID。

category 参数的值可以为 `StorageRead`、`StorageWrite` 和 `StorageDelete`。

[!INCLUDE [no retention policy](../../../includes/azure-storage-logs-retention-policy.md)]

下面是一个示例：

`az monitor diagnostic-settings create --name setting1 --storage-account mystorageaccount --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/fileServices/default --resource-group myresourcegroup --logs '[{"category": StorageWrite, "enabled": true}]'`

有关每个参数的说明，请参阅[通过 Azure CLI 存档资源日志](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)。

#### <a name="stream-logs-to-an-event-hub"></a>将日志流式传输到事件中心

如果你选择将日志流式传输到事件中心，则需要为发送到事件中心的日志量付费。 有关具体定价，请参阅 [Azure Monitor 定价](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)页的“平台日志”部分。

使用 [az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create) 命令启用日志。

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --event-hub <event-hub-name> --event-hub-rule <event-hub-namespace-and-key-name> --resource <storage-account-resource-id> --logs '[{"category": <operations>, "enabled": true "retentionPolicy": {"days": <number-days>, "enabled": <retention-bool}}]'
```

下面是一个示例：

`az monitor diagnostic-settings create --name setting1 --event-hub myeventhub --event-hub-rule /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhubnamespace/authorizationrules/RootManageSharedAccessKey --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/fileServices/default --logs '[{"category": StorageDelete, "enabled": true }]'`

有关每个参数的说明，请参阅[通过 Azure CLI 将数据流式传输到事件中心](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs)。

#### <a name="send-logs-to-log-analytics"></a>将日志发送到 Log Analytics

使用 [az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create) 命令启用日志。

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --workspace <log-analytics-workspace-resource-id> --resource <storage-account-resource-id> --logs '[{"category": <category name>, "enabled": true "retentionPolicy": {"days": <days>, "enabled": <retention-bool}}]'
```

下面是一个示例：

`az monitor diagnostic-settings create --name setting1 --workspace /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.OperationalInsights/workspaces/my-analytic-workspace --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/fileServices/default --logs '[{"category": StorageDelete, "enabled": true ]'`

 有关详细信息，请参阅[在 Azure Monitor 中将 Azure 资源日志流式传输到 Log Analytics 工作区](../../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace)。

### <a name="template"></a>[模板](#tab/template)

若要查看创建诊断设置的 Azure 资源管理器模板，请参阅 [Azure 存储的诊断设置](../../azure-monitor/essentials/resource-manager-diagnostic-settings.md#diagnostic-setting-for-azure-storage)。

### <a name="azure-policy"></a>[Azure Policy](#tab/policy)

可以使用策略定义创建诊断设置。 这样，就可以确保为创建或更新的每一个帐户创建诊断设置。 请参阅 [Azure 存储的 Azure Policy 内置定义](../common/policy-reference.md)。

---

## <a name="analyzing-metrics"></a>分析指标

你可以使用指标资源管理器通过其他 Azure 服务中的指标分析 Azure 存储的指标。 从 Azure Monitor 菜单中选择“指标”，可打开指标资源管理器 。 有关使用此工具的详细信息，请参阅 [Azure 指标资源管理器入门](../../azure-monitor/essentials/metrics-getting-started.md)。 

对于支持维度的指标，可使用所需的维度值筛选指标。  有关 Azure 存储支持的维度的完整列表，请参阅[指标维度](storage-files-monitoring-reference.md#metrics-dimensions)。 Azure 文件存储的指标位于以下命名空间： 

- Microsoft.Storage/storageAccounts
- Microsoft.Storage/storageAccounts/fileServices

有关所有 Azure Monitor 支持指标（包括 Azure 文件存储）的列表，请参阅 [Azure Monitor 支持的指标](../../azure-monitor/essentials/metrics-supported.md#microsoftstoragestorageaccountsfileservices)。

### <a name="accessing-metrics"></a>访问指标

> [!TIP]
> 若要查看 Azure CLI 或 .NET 示例，请选择此处列出的相应选项卡。

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

#### <a name="list-the-metric-definition"></a>列出指标定义

你可以列出存储帐户或 Azure 文件存储服务的指标定义。 请使用 [Get-AzMetricDefinition](/powershell/module/az.monitor/get-azmetricdefinition) cmdlet。

在本示例中，将 `<resource-ID>` 占位符替换为整个存储帐户的资源 ID 或 Azure 文件存储服务的资源 ID。  你可以在 Azure 门户中存储帐户的“属性”页上找到这些资源 ID。

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetricDefinition -ResourceId $resourceId
```

#### <a name="reading-metric-values"></a>读取指标值

你可以读取存储帐户或 Azure 文件存储服务的帐户级别指标值。 使用 [Get-AzMetric](/powershell/module/Az.Monitor/Get-AzMetric) cmdlet。

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetric -ResourceId $resourceId -MetricNames "UsedCapacity" -TimeGrain 01:00:00
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

#### <a name="list-the-account-level-metric-definition"></a>列出帐户级指标定义

你可以列出存储帐户或 Azure 文件存储服务的指标定义。 使用 [az monitor metrics list-definitions](/cli/azure/monitor/metrics#az_monitor_metrics_list_definitions) 命令。
 
在本示例中，将 `<resource-ID>` 占位符替换为整个存储帐户的资源 ID 或 Azure 文件存储服务的资源 ID。 你可以在 Azure 门户中存储帐户的“属性”页上找到这些资源 ID。

```azurecli-interactive
   az monitor metrics list-definitions --resource <resource-ID>
```

#### <a name="read-account-level-metric-values"></a>读取帐户级指标值

你可以读取存储帐户或 Azure 文件存储服务的指标值。 请使用 [az monitor metrics list](/cli/azure/monitor/metrics#az_monitor_metrics_list) 命令。

```azurecli-interactive
   az monitor metrics list --resource <resource-ID> --metric "UsedCapacity" --interval PT1H
```

### <a name="net-sdk"></a>[.NET SDK](#tab/azure-portal)

Azure Monitor 提供 [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/)，用于读取指标定义和值。 [示例代码](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/)演示如何通过不同的参数来使用 SDK。 对于存储指标，需使用 `0.18.0-preview` 或更高版本。
 
在这些示例中，将 `<resource-ID>` 占位符替换为整个存储帐户或 Azure 文件存储服务的资源 ID。 你可以在 Azure 门户中存储帐户的“属性”页上找到这些资源 ID。

将 `<subscription-ID>` 占位符值替换为你的订阅 ID。 要查看有关如何获取 `<tenant-ID>`、`<application-ID>` 和 `<AccessKey>` 值的指南，请参阅[使用门户创建可访问资源的 Azure AD 应用程序和服务主体](../../active-directory/develop/howto-create-service-principal-portal.md)。 

#### <a name="list-the-account-level-metric-definition"></a>列出帐户级指标定义

以下示例演示如何列出帐户级别的指标定义：

```csharp
    public static async Task ListStorageMetricDefinition()
    {
        var resourceId = "<resource-ID>";
        var subscriptionId = "<subscription-ID>";
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";


        MonitorManagementClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;
        IEnumerable<MetricDefinition> metricDefinitions = await readOnlyClient.MetricDefinitions.ListAsync(resourceUri: resourceId, cancellationToken: new CancellationToken());

        foreach (var metricDefinition in metricDefinitions)
        {
            // Enumrate metric definition:
            //    Id
            //    ResourceId
            //    Name
            //    Unit
            //    MetricAvailabilities
            //    PrimaryAggregationType
            //    Dimensions
            //    IsDimensionRequired
        }
    }

```

#### <a name="reading-account-level-metric-values"></a>读取帐户级别指标值

以下示例演示如何读取帐户级别的 `UsedCapacity` 数据：

```csharp
    public static async Task ReadStorageMetricValue()
    {
        var resourceId = "<resource-ID>";
        var subscriptionId = "<subscription-ID>";
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";

        MonitorClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToUniversalTime().ToString("o");
        string endDate = DateTime.Now.ToUniversalTime().ToString("o");
        string timeSpan = startDate + "/" + endDate;

        Response = await readOnlyClient.Metrics.ListAsync(
            resourceUri: resourceId,
            timespan: timeSpan,
            interval: System.TimeSpan.FromHours(1),
            metricnames: "UsedCapacity",

            aggregation: "Average",
            resultType: ResultType.Data,
            cancellationToken: CancellationToken.None);

        foreach (var metric in Response.Value)
        {
            // Enumrate metric value
            //    Id
            //    Name
            //    Type
            //    Unit
            //    Timeseries
            //        - Data
            //        - Metadatavalues
        }
    }

```

#### <a name="reading-multidimensional-metric-values"></a>读取多维指标值

对于多维指标，如果需要读取基于特定维度值的指标数据，则需定义元数据筛选器。

以下示例演示如何根据支持多维的指标读取指标数据：

```csharp
    public static async Task ReadStorageMetricValueTest()
    {
        // Resource ID for Azure Files
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/fileServices/default";
        var subscriptionId = "<subscription-ID}";
        // How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";

        MonitorManagementClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToUniversalTime().ToString("o");
        string endDate = DateTime.Now.ToUniversalTime().ToString("o");
        string timeSpan = startDate + "/" + endDate;
        // It's applicable to define meta data filter when a metric support dimension
        // More conditions can be added with the 'or' and 'and' operators, example: BlobType eq 'BlockBlob' or BlobType eq 'PageBlob'
        ODataQuery<MetadataValue> odataFilterMetrics = new ODataQuery<MetadataValue>(
            string.Format("BlobType eq '{0}'", "BlockBlob"));

        Response = readOnlyClient.Metrics.List(
                        resourceUri: resourceId,
                        timespan: timeSpan,
                        interval: System.TimeSpan.FromHours(1),
                        metricnames: "BlobCapacity",
                        odataQuery: odataFilterMetrics,
                        aggregation: "Average",
                        resultType: ResultType.Data);

        foreach (var metric in Response.Value)
        {
            //Enumrate metric value
            //    Id
            //    Name
            //    Type
            //    Unit
            //    Timeseries
            //        - Data
            //        - Metadatavalues
        }
    }

```

# <a name="template"></a>[模板](#tab/template)

不适用。

### <a name="azure-policy"></a>[Azure Policy](#tab/policy)

不适用。

---

## <a name="analyzing-logs"></a>分析日志

你可以将资源日志作为存储帐户中的 blob 以及事件数据进行访问，也可以通过 Log Analytics 查询访问资源日志。

若要获取记录的 SMB 和 REST 操作列表，请参阅[存储记录的操作和状态消息](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)和 [Azure 文件存储监视数据参考](storage-files-monitoring-reference.md)。

> [!NOTE]
> Azure Monitor 中的 Azure 存储日志目前为公共预览版，可在所有公有云和美国政府云区域中用于预览测试。 此预览版为常规用途 v1 和常规用途 v2 存储帐户中的 Blob（包括 Azure Data Lake Storage Gen2）、文件、队列、表和高级存储帐户启用日志。 经典存储帐户不受支持。

仅在针对服务终结点发出请求时才会创建日志条目。 例如，如果存储帐户的文件终结点中存在活动，而表或队列终结点中没有该活动，则仅创建与 Azure 文件存储服务有关的日志。 Azure 存储日志包含有关成功和失败的存储服务请求的详细信息。 可以使用该信息监视各个请求和诊断存储服务问题。 将最大程度地记录请求。

### <a name="log-authenticated-requests"></a>记录经过身份验证的请求

 将记录以下类型的已经过身份验证的请求：

- 成功的请求
- 失败的请求，包括超时、限制、网络、授权和其他错误
- 使用 Kerberos、NTLM 或共享访问签名 (SAS) 的请求，包括失败和成功的请求
- 对分析数据（$logs 容器中的经典日志数据和 $metric 表中的经典指标数据）的请求

不会记录 Azure 文件存储服务本身发出的请求，如创建或删除日志。 有关记录的 SMB 和 REST 请求的完整列表，请参阅[存储记录的操作和状态消息](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)和 [Azure 文件存储监视数据参考](storage-files-monitoring-reference.md)。

### <a name="accessing-logs-in-a-storage-account"></a>访问存储帐户中的日志

日志显示为存储到目标存储帐户中的容器的 blob。 数据作为按行分隔的 JSON 有效负载进行收集并存储在单个 blob 中。 Blob 的名称遵循以下命名约定：

`https://<destination-storage-account>.blob.core.windows.net/insights-logs-<storage-operation>/resourceId=/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<source-storage-account>/fileServices/default/y=<year>/m=<month>/d=<day>/h=<hour>/m=<minute>/PT1H.json`

下面是一个示例：

`https://mylogstorageaccount.blob.core.windows.net/insights-logs-storagewrite/resourceId=/subscriptions/`<br>`208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/fileServices/default/y=2019/m=07/d=30/h=23/m=12/PT1H.json`

### <a name="accessing-logs-in-an-event-hub"></a>访问事件中心内的日志

发送到事件中心的日志并没有存储为文件，但你可以验证事件中心是否收到了日志信息。 在 Azure 门户中，请转到事件中心，然后验证“传入消息”计数是否大于零。 

![审核日志](media/storage-files-monitoring/event-hub-log.png)

你可以使用安全信息和事件管理以及监视工具来访问和读取发送到事件中心的日志数据。 有关详细信息，请参阅[可对发送到事件中心的监视数据执行什么操作？](../../azure-monitor/essentials/stream-monitoring-data-event-hubs.md)。

### <a name="accessing-logs-in-a-log-analytics-workspace"></a>访问 Log Analytics 工作区中的日志

你可以使用 Azure Monitor 日志查询来访问发送到 Log Analytics 工作区的日志。 数据存储在 StorageFileLogs 表中。 

有关详细信息，请参阅 [Log Analytics 教程](../../azure-monitor/logs/log-analytics-tutorial.md)。

#### <a name="sample-kusto-queries"></a>示例 Kusto 查询

你可以在“日志搜索”栏中输入下面这些查询，以帮助监视 Azure 文件存储。 这些查询使用[新语言](../../azure-monitor/logs/log-query-overview.md)。

> [!IMPORTANT]
> 从存储帐户资源组菜单中选择“日志”时，会打开 Log Analytics 并将查询范围设置为当前资源组。 这意味着日志查询只包含来自该资源组的数据。 如果要运行的查询包含来自他资源或其他 Azure 服务的数据，请从“Azure Monitor”菜单中选择“日志”。 有关详细信息，请参阅 [Azure Monitor Log Analytics 中的日志查询范围和时间范围](../../azure-monitor/logs/scope.md)。

使用以下查询可帮助你监视 Azure 文件共享：

- 查看过去一周的 SMB 错误

```Kusto
StorageFileLogs
| where Protocol == "SMB" and TimeGenerated >= ago(7d) and StatusCode contains "-"
| sort by StatusCode
```
- 创建过去一周内的 SMB 操作的饼图

```Kusto
StorageFileLogs
| where Protocol == "SMB" and TimeGenerated >= ago(7d) 
| summarize count() by OperationName
| sort by count_ desc
| render piechart
```

- 查看过去一周的 REST 错误

```Kusto
StorageFileLogs
| where Protocol == "HTTPS" and TimeGenerated >= ago(7d) and StatusText !contains "Success"
| sort by StatusText asc
```

- 创建过去一周内的 REST 操作的饼图

```Kusto
StorageFileLogs
| where Protocol == "HTTPS" and TimeGenerated >= ago(7d) 
| summarize count() by OperationName
| sort by count_ desc
| render piechart
```

若要查看 Azure 文件存储的列名和说明列表，请参阅 [StorageFileLogs](/azure/azure-monitor/reference/tables/storagefilelogs)。

有关如何编写查询的详细信息，请参阅 [Log Analytics 教程](../../azure-monitor/logs/log-analytics-tutorial.md)。

## <a name="alerts"></a>警报

在监视数据中发现重要情况时，Azure Monitor 警报会主动通知你。 有了警报，你就可以在客户注意到你的系统中的问题之前确定和解决它们。 可以在[指标](../../azure-monitor/alerts/alerts-metric-overview.md)、[日志](../../azure-monitor/alerts/alerts-unified-log.md)和[活动日志](../../azure-monitor/alerts/activity-log-alerts.md)上设置警报。 

下表列出了一些要监视的示例场景和用于警报的适当指标：

| 场景 | 用于警报的指标 |
|-|-|
| 文件共享受到限制。 | 指标：Transactions<br>维度名称：响应类型 <br>维度名称：FileShare（仅限高级文件共享） |
| 文件共享大小为容量的 80%。 | 指标：文件容量<br>维度名称：FileShare（仅限高级文件共享） |
| 一天内的文件共享流出量已超过 500 GiB。 | 指标：流出量<br>维度名称：FileShare（仅限高级文件共享） |

### <a name="how-to-create-alerts-for-azure-files"></a>如何为 Azure 文件存储创建警报

1. 在 Azure 门户 中转到自己的存储帐户。 

2. 单击“警报”，然后单击“+ 新建警报规则”。

3. 单击“编辑资源”，选择“文件资源类型”，然后单击“完成”。 

4. 单击“添加条件”，并为警报提供以下信息： 

    - **指标**
    - **维度名称**
    - **警报逻辑**

5. 单击“添加操作组”，通过选择现有操作组或创建新的操作组，将一个操作组（电子邮件、短信等）添加到警报中。

6. 填写 **警报详细信息**，例如 **警报规则名称**、**说明** 和 **严重性**。

7. 单击“创建警报规则”以创建警报。

> [!NOTE]  
> 如果创建了警报，但警报导致过多的干扰，请调整阈值和警报逻辑。

### <a name="how-to-create-an-alert-if-a-file-share-is-throttled"></a>如何创建文件共享受到限制时的警报

1. 在 Azure 门户 中转到自己的存储帐户。
2. 在“监视”部分中单击“警报”，然后单击“+ 新建警报规则”。  
3. 单击“编辑资源”，为存储帐户选择“文件资源类型”，然后单击“完成”。 例如，如果存储帐户名称为 `contoso`，请选择 `contoso/file` 资源。
4. 单击“选择条件”以添加条件。
5. 你将看到存储帐户支持的信号列表，请选择“事务”指标。
6. 在“配置信号逻辑”边栏选项卡上，单击“维度名称”下拉列表，然后选择“响应类型”。
7. 单击“维度值”下拉列表，然后为你的文件共享选择适当的响应类型。

    对于标准文件共享，请选择以下响应类型：

    - SuccessWithShareIopsThrottling
    - SuccessWithThrottling
    - ClientShareIopsThrottlingError

    对于高级文件共享，请选择以下响应类型：

    - SuccessWithShareEgressThrottling
    - SuccessWithShareIngressThrottling
    - SuccessWithShareIopsThrottling
    - ClientShareEgressThrottlingError
    - ClientShareIngressThrottlingError
    - ClientShareIopsThrottlingError

   > [!NOTE]
   > 如果“维度值”下拉列表中未列出响应类型，这意味着资源未被限制。 若要添加维度值，请在“维度值”下拉列表旁边选择“添加自定义值”，输入响应类型类型（例如 **SuccessWithThrottling**），选择“确定”，然后重复上述步骤，为你的文件共享添加所有适用的响应类型。

8. 对于“高级文件共享”，请单击“维度名称”下拉列表，然后选择“文件共享”。 对于“标准文件共享”，请跳到“步骤 #10”。

   > [!NOTE]
   > 如果文件共享是标准文件共享，则“File Share”维度不会列出文件共享，因为每个共享指标对标准文件共享不可用。 如果存储帐户中的任何文件共享受到限制，则会触发标准文件共享的限制警报，并且警报不会识别哪个文件共享受到限制。 因为每共享指标不可用于标准文件共享，所以建议为每个存储帐户使用一个文件共享。

9. 单击“维度值”下拉列表，并选择要对其发出警报的文件共享。
10. 定义“警报参数”（阈值、运算符、聚合粒度和评估频率），然后单击“完成”。

    > [!TIP]
    > 如果你使用的是静态阈值，并且文件共享当前受到限制，则可通过指标图表来确定合理的阈值。 如果使用的是动态阈值，则指标图表将显示基于最新数据计算出的阈值。

11. 单击“添加操作组”，通过选择现有操作组或创建新的操作组，将一个操作组（电子邮件、短信等）添加到警报中。
12. 填写 **警报详细信息**，例如 **警报规则名称**、**说明** 和 **严重性**。
13. 单击“创建警报规则”以创建警报。

### <a name="how-to-create-an-alert-if-the-azure-file-share-size-is-80-of-capacity"></a>如何在 Azure 文件共享大小为容量的 80% 时创建警报

1. 在 Azure 门户 中转到自己的存储帐户。
2. 在“监视”部分中单击“警报”，然后单击“+ 新建警报规则”。
3. 单击“编辑资源”，为存储帐户选择“文件资源类型”，然后单击“完成”。 例如，如果存储帐户名称为 `contoso`，请选择 `contoso/file` 资源。
4. 单击“选择条件”以添加条件。
5. 你将看到存储帐户支持的信号列表，请选择“文件容量”指标。
6. 对于“高级文件共享”，请单击“维度名称”下拉列表，然后选择“文件共享”。 对于“标准文件共享”，请跳到“步骤 #8”。

   > [!NOTE]
   > 如果文件共享是标准文件共享，则“File Share”维度不会列出文件共享，因为每个共享指标对标准文件共享不可用。 标准文件共享的警报基于存储帐户中的所有文件共享。 因为每共享指标不可用于标准文件共享，所以建议为每个存储帐户使用一个文件共享。

7. 单击“维度值”下拉列表，并选择要对其发出警报的文件共享。
8. 输入“阈值”（以字节为单位）。 例如，如果文件共享大小为 100 TiB，并且你希望在文件共享大小为容量的 80% 时收到警报，则阈值（以字节为单位）为 87960930222080。
9. 定义其余的“警报参数”（聚合粒度和评估频率），然后单击“完成”。
10. 单击“添加操作组”，通过选择现有操作组或创建新的操作组，将一个操作组（电子邮件、短信等）添加到警报中。
11. 填写 **警报详细信息**，例如 **警报规则名称**、**说明** 和 **严重性**。
12. 单击“创建警报规则”以创建警报。

### <a name="how-to-create-an-alert-if-the-azure-file-share-egress-has-exceeded-500-gib-in-a-day"></a>如何在 Azure 文件共享流出量在一天内超过 500 GiB 时创建警报

1. 在 Azure 门户 中转到自己的存储帐户。
2. 在“监视”部分中单击“警报”，然后单击“+ 新建警报规则”。 
3. 单击“编辑资源”，为存储帐户选择“文件资源类型”，然后单击“完成”。 例如，如果存储帐户名称为“contoso”，则选择“contoso/文件”资源。
4. 单击“选择条件”以添加条件。
5. 你将看到存储帐户支持的信号列表，请选择“流出量”指标。
6. 对于“高级文件共享”，请单击“维度名称”下拉列表，然后选择“文件共享”。 对于“标准文件共享”，请跳到“步骤 #8”。

   > [!NOTE]
   > 如果文件共享是标准文件共享，则“File Share”维度不会列出文件共享，因为每个共享指标对标准文件共享不可用。 标准文件共享的警报基于存储帐户中的所有文件共享。 因为每共享指标不可用于标准文件共享，所以建议为每个存储帐户使用一个文件共享。

7. 单击“维度值”下拉列表，并选择要对其发出警报的文件共享。
8. 为“阈值”输入 536870912000 个字节。 
9. 单击“聚合粒度”下拉列表，然后选择“24 小时”。
10. 选择“评估频率”，并单击“完成”。
11. 单击“添加操作组”，通过选择现有操作组或创建新的操作组，将一个操作组（电子邮件、短信等）添加到警报中。
12. 填写 **警报详细信息**，例如 **警报规则名称**、**说明** 和 **严重性**。
13. 单击“创建警报规则”以创建警报。

## <a name="next-steps"></a>后续步骤

- [Azure 文件存储监视数据参考](storage-files-monitoring-reference.md)
- [使用 Azure Monitor 监视 Azure 资源](../../azure-monitor/essentials/monitor-azure-resource.md)
- [Azure 存储指标迁移](../common/storage-metrics-migration.md)
- [规划 Azure 文件部署](./storage-files-planning.md)
- [如何部署 Azure 文件](./storage-how-to-create-file-share.md)
- [排查 Windows 上的 Azure 文件问题](./storage-troubleshoot-windows-file-connection-problems.md)
- [排查 Linux 上的 Azure 文件问题](./storage-troubleshoot-linux-file-connection-problems.md)
