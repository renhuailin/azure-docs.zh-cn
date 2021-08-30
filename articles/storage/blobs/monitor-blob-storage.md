---
title: 监视 Azure Blob 存储 | Microsoft Docs
description: 了解如何监视 Azure Blob 存储的性能和可用性。 监视 Azure Blob 存储数据、了解配置以及分析指标和日志数据。
author: normesta
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 10/26/2020
ms.author: normesta
ms.reviewer: fryu
ms.custom: subject-monitoring, devx-track-csharp, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: f38149e2259dbb6724a81e8139f46bd65a0edff0
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121749319"
---
# <a name="monitoring-azure-blob-storage"></a>监视 Azure Blob 存储

如果你有依赖 Azure 资源的关键应用程序和业务流程，则需要监视这些资源的可用性、性能和操作。 本文介绍 Azure Blob 存储生成的监视数据，以及如何使用 Azure Monitor 的各种功能分析这些数据的相关警报。

> [!NOTE]
> Azure Monitor 中的 Azure 存储日志目前为公共预览版，可在所有公有云区域中进行预览测试。 此预览版支持 blob（包括 Azure Data Lake Storage Gen2）、文件、队列和表日志。 此功能适用于使用 Azure 资源管理器部署模型创建的所有存储帐户。 请参阅[存储帐户概述](../common/storage-account-overview.md)。

## <a name="monitor-overview"></a>Monitor 概述

每种 Blob 存储资源的 Azure 门户“概述”页都包含资源使用情况（例如请求和每小时计费）的简要视图。 这些信息非常有用，但只提供少量监视数据。 创建资源后，其中的某些数据会自动收集，并可供分析。 你可以使用某些配置启用其他数据收集类型。

## <a name="what-is-azure-monitor"></a>说明是 Azure Monitor？
Azure Blob 存储使用 [Azure Monitor](../../azure-monitor/overview.md) 来创建监视数据，Azure Monitor 是 Azure 中的一项完整堆栈监视服务。 Azure Monitor 提供了一整套用于监视 Azure 资源以及其他云和本地资源的功能。 

可先阅读文章[使用 Azure Monitor 监视 Azure 资源](../../azure-monitor/essentials/monitor-azure-resource.md)，其中介绍了以下内容：

- 说明是 Azure Monitor？
- 与监视相关的成本
- 监视 Azure 中收集的数据
- 配置数据收集
- Azure 中用于分析监视数据并就其发出警报的标准工具

本文中的以下各部分将介绍从 Azure 存储收集的特定数据。 其中的示例演示了如何配置数据收集并通过 Azure 工具分析这些数据。

## <a name="monitoring-data"></a>监视数据

Azure Blob 存储会收集与其他 Azure 资源类型相同的监视数据，如[监视 Azure 资源中的数据](../../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data)中所述。 

有关 Azure Blob 存储创建的指标和日志指标的详细信息，请参阅 [Azure Blob 存储监视数据参考](monitor-blob-storage-reference.md)。

Azure Monitor 中的指标和日志仅支持 Azure 资源管理器存储帐户。 Azure Monitor 不支持经典存储帐户。 如果要使用经典存储帐户上的指标或日志，则需要迁移到 Azure 资源管理器存储帐户。 请参阅[迁移到 Azure 资源管理器](../../virtual-machines/migration-classic-resource-manager-overview.md)。

如果需要，可以继续使用经典指标和日志。 实际上，经典指标和日志可与 Azure Monitor 中的指标和日志同时使用。 在 Azure 存储终止旧指标和日志的服务之前，支持范围保持不变。

## <a name="collection-and-routing"></a>收集和路由

平台指标和活动日志会自动收集，但你可以使用诊断设置将其路由到其他位置。 

若要收集资源日志，必须创建诊断设置。 创建设置时，选择“blob”作为要为其启用日志的存储类型。 然后，指定需要收集日志的以下操作类别之一。 

| 类别 | 说明 |
|:---|:---|
| StorageRead | 对象上的读取操作。 |
| StorageWrite | 对象上的写入操作。 |
| StorageDelete | 对象上的删除操作。 |

> [!NOTE]
> Data Lake Storage Gen2 不会显示为存储类型。 这是因为 Data Lake Storage Gen2 是适用于 Blob 存储的一组功能。 

## <a name="creating-a-diagnostic-setting"></a>创建诊断设置

可使用 Azure 门户、PowerShell、Azure CLI、Azure 资源管理器模板或 Azure Policy 创建诊断设置。 

有关一般指南，请参阅[创建诊断设置以收集 Azure 中的平台日志和指标](../../azure-monitor/essentials/diagnostic-settings.md)。

> [!NOTE]
> Azure Monitor 中的 Azure 存储日志目前为公共预览版，可在所有公有云区域中进行预览测试。 此预览版支持 blob（包括 Azure Data Lake Storage Gen2）、文件、队列和表日志。 此功能适用于使用 Azure 资源管理器部署模型创建的所有存储帐户。 请参阅[存储帐户概述](../common/storage-account-overview.md)。

### <a name="azure-portal"></a>[Azure 门户](#tab/azure-portal)

1. 登录到 Azure 门户。

2. 导航到自己的存储帐户。

3. 在“监视”部分，单击“诊断设置(预览)”。

   > [!div class="mx-imgBorder"]
   > ![门户 - 诊断日志](media/monitor-blob-storage/diagnostic-logs-settings-pane.png)   

4. 选择“blob”作为要为其启用日志的存储类型。

5. 单击“添加诊断设置”。

   > [!div class="mx-imgBorder"]
   > ![门户 - 资源日志 - 添加诊断设置](media/monitor-blob-storage/diagnostic-logs-settings-pane-2.png)

   此时将显示“诊断设置”页。

   > [!div class="mx-imgBorder"]
   > ![资源日志页](media/monitor-blob-storage/diagnostic-logs-page.png)

6. 在该页的“名称”字段中，输入此资源日志设置的名称。 然后，选择要记录的操作（读取、写入和删除操作），以及要将日志发送到的位置。

#### <a name="archive-logs-to-a-storage-account"></a>将日志存档到存储帐户

如果你选择将日志存档到存储帐户，则需要为发送到存储帐户的日志量付费。 有关具体定价，请参阅 [Azure Monitor 定价](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)页的“平台日志”部分。

1. 选中“存档到存储帐户”复选框，然后单击“配置”按钮。

   > [!div class="mx-imgBorder"]   
   > ![“诊断设置”页的存档存储](media/monitor-blob-storage/diagnostic-logs-settings-pane-archive-storage.png)

2. 在“存储帐户”下拉列表中，选择要将日志存档到的存储帐户，单击“确定”按钮，然后单击“保存”按钮。

   [!INCLUDE [no retention policy](../../../includes/azure-storage-logs-retention-policy.md)]

   > [!NOTE]
   > 在你选择存储帐户作为导出目标之前，请参阅[存档 Azure 资源日志](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)，了解存储帐户的先决条件。

#### <a name="stream-logs-to-azure-event-hubs"></a>将日志流式传输到 Azure 事件中心

如果你选择将日志流式传输到事件中心，则需要为发送到事件中心的日志量付费。 有关具体定价，请参阅 [Azure Monitor 定价](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)页的“平台日志”部分。

1. 选中“流式传输到事件中心”复选框，然后单击“配置”按钮。

2. 在“选择事件中心”窗格中，选择要将日志流式传输到的事件中心的命名空间、名称和策略名称。 

   > [!div class="mx-imgBorder"]
   > ![“诊断设置”页的事件中心](media/monitor-blob-storage/diagnostic-logs-settings-pane-event-hub.png)

3. 单击“确定”按钮，然后单击“保存”按钮。

#### <a name="send-logs-to-azure-log-analytics"></a>将日志发送到 Azure Log Analytics

1. 选中“发送到 Log Analytics”复选框，选择 Log Analytics 工作区，然后单击“保存”按钮。

   > [!div class="mx-imgBorder"]   
   > ![诊断设置页日志分析](media/monitor-blob-storage/diagnostic-logs-settings-pane-log-analytics.png)

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

将此代码段中的 `<storage-service-resource--id>` 占位符替换为 blob 服务的资源 ID。 通过打开存储帐户的“终结点”页，可在 Azure 门户中找到资源 ID。

Category 参数的值可以为 `StorageRead`、`StorageWrite` 和 `StorageDelete`。

[!INCLUDE [no retention policy](../../../includes/azure-storage-logs-retention-policy.md)]

下面是一个示例：

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/blobServices/default -StorageAccountId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount -Enabled $true -Category StorageWrite,StorageDelete`

有关每个参数的说明，请参阅[通过 Azure PowerShell 存档 Azure 资源日志](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)。

#### <a name="stream-logs-to-an-event-hub"></a>将日志流式传输到事件中心

如果你选择将日志流式传输到事件中心，则需要为发送到事件中心的日志量付费。 有关具体定价，请参阅 [Azure Monitor 定价](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)页的“平台日志”部分。

使用 [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) PowerShell cmdlet 和 `EventHubAuthorizationRuleId` 参数来启用日志。

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -EventHubAuthorizationRuleId <event-hub-namespace-and-key-name> -Enabled $true -Category <operations-to-log> -RetentionEnabled <retention-bool> -RetentionInDays <number-of-days>
```

下面是一个示例：

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/blobServices/default -EventHubAuthorizationRuleId /subscriptions/20884142-a14v3-4234-5450-08b10c09f4/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhubnamespace/authorizationrules/RootManageSharedAccessKey -Enabled $true -Category StorageDelete`

有关每个参数的说明，请参阅[通过 PowerShell cmdlet 将数据流式传输到事件中心](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs)。

#### <a name="send-logs-to-log-analytics"></a>将日志发送到 Log Analytics

使用 [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) PowerShell cmdlet 和 `WorkspaceId` 参数来启用日志。

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -WorkspaceId <log-analytics-workspace-resource-id> -Enabled $true -Category <operations-to-log> -RetentionEnabled <retention-bool> -RetentionInDays <number-of-days>
```

下面是一个示例：

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/blobServices/default -WorkspaceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.OperationalInsights/workspaces/my-analytic-workspace -Enabled $true -Category StorageDelete`

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
az monitor diagnostic-settings create --name <setting-name> --storage-account <storage-account-name> --resource <storage-service-resource-id> --resource-group <resource-group> --logs '[{"category": <operations>, "enabled": true }]'
```

将此代码片段中的 `<storage-service-resource--id>` 占位符替换为 Blob 存储服务的资源 ID。 通过打开存储帐户的“终结点”页，可在 Azure 门户中找到资源 ID。

category 参数的值可以为 `StorageRead`、`StorageWrite` 和 `StorageDelete`。

[!INCLUDE [no retention policy](../../../includes/azure-storage-logs-retention-policy.md)]

下面是一个示例：

`az monitor diagnostic-settings create --name setting1 --storage-account mystorageaccount --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/blobServices/default --resource-group myresourcegroup --logs '[{"category": StorageWrite}]'`

有关每个参数的说明，请参阅[通过 Azure CLI 存档资源日志](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)。

#### <a name="stream-logs-to-an-event-hub"></a>将日志流式传输到事件中心

如果你选择将日志流式传输到事件中心，则需要为发送到事件中心的日志量付费。 有关具体定价，请参阅 [Azure Monitor 定价](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)页的“平台日志”部分。

使用 [az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create) 命令启用日志。

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --event-hub <event-hub-name> --event-hub-rule <event-hub-namespace-and-key-name> --resource <storage-account-resource-id> --logs '[{"category": <operations>, "enabled": true "retentionPolicy": {"days": <number-days>, "enabled": <retention-bool}}]'
```

下面是一个示例：

`az monitor diagnostic-settings create --name setting1 --event-hub myeventhub --event-hub-rule /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhubnamespace/authorizationrules/RootManageSharedAccessKey --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/blobServices/default --logs '[{"category": StorageDelete, "enabled": true }]'`

有关每个参数的说明，请参阅[通过 Azure CLI 将数据流式传输到事件中心](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs)。

#### <a name="send-logs-to-log-analytics"></a>将日志发送到 Log Analytics

使用 [az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create) 命令启用日志。

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --workspace <log-analytics-workspace-resource-id> --resource <storage-account-resource-id> --logs '[{"category": <category name>, "enabled": true "retentionPolicy": {"days": <days>, "enabled": <retention-bool}}]'
```

下面是一个示例：

`az monitor diagnostic-settings create --name setting1 --workspace /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.OperationalInsights/workspaces/my-analytic-workspace --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/blobServices/default --logs '[{"category": StorageDelete, "enabled": true ]'`

 有关详细信息，请参阅[在 Azure Monitor 中将 Azure 资源日志流式传输到 Log Analytics 工作区](../../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace)。

### <a name="template"></a>[模板](#tab/template)

若要查看创建诊断设置的 Azure 资源管理器模板，请参阅 [Azure 存储的诊断设置](../../azure-monitor/essentials/resource-manager-diagnostic-settings.md#diagnostic-setting-for-azure-storage)。

### <a name="azure-policy"></a>[Azure Policy](#tab/policy)

可使用策略定义来创建诊断设置。 这样，就可确保为创建或更新的每个帐户创建一个诊断设置。 请参阅 [Azure 存储的 Azure Policy 内置定义](../common/policy-reference.md)。

---

## <a name="analyzing-metrics"></a>分析指标

你可以使用指标资源管理器通过其他 Azure 服务中的指标分析 Azure 存储的指标。 从 Azure Monitor 菜单中选择“指标”，可打开指标资源管理器 。 有关使用此工具的详细信息，请参阅 [Azure 指标资源管理器入门](../../azure-monitor/essentials/metrics-getting-started.md)。 

以下示例演示了如何查看帐户级别的事务。

![在 Azure 门户中访问指标的屏幕截图](./media/monitor-blob-storage/access-metrics-portal.png)

对于支持维度的指标，可使用所需的维度值筛选指标。 以下示例演示了如何通过选择“API 名称”维度的值，在特定操作上查看帐户级别的“事务” 。

![在 Azure 门户中访问包含维度的指标的屏幕截图](./media/monitor-blob-storage/access-metrics-portal-with-dimension.png)

有关 Azure 存储支持的维度的完整列表，请参阅[指标维度](monitor-blob-storage-reference.md#metrics-dimensions)。

Azure Blob 存储的指标位于以下命名空间： 

- Microsoft.Storage/storageAccounts
- Microsoft.Storage/storageAccounts/blobServices

有关所有 Azure Monitor 支持指标（包括 Azure Blob 存储）的列表，请参阅 [Azure Monitor 支持的指标](../../azure-monitor/essentials/metrics-supported.md)。


### <a name="accessing-metrics"></a>访问指标

> [!TIP]
> 若要查看 Azure CLI 或 .NET 示例，请选择此处列出的相应选项卡。

### <a name="net-sdk"></a>[.NET SDK](#tab/azure-portal)

Azure Monitor 提供 [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/)，用于读取指标定义和值。 [示例代码](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/)演示如何通过不同的参数来使用 SDK。 对于存储指标，需使用 `0.18.0-preview` 或更高版本。
 
在这些示例中，将 `<resource-ID>` 占位符替换为整个存储帐户或 Blob 存储的资源 ID。 你可以在 Azure 门户中存储帐户的“终结点”页上找到这些资源 ID。

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
        // Resource ID for blob storage
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/blobServices/default";
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

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

#### <a name="list-the-metric-definition"></a>列出指标定义

你可以列出存储帐户或 Blob 存储服务的指标定义。 请使用 [Get-AzMetricDefinition](/powershell/module/az.monitor/get-azmetricdefinition) cmdlet。

在此示例中，将 `<resource-ID>` 占位符替换为整个存储帐户的资源 ID 或 Blob 存储服务的资源 ID。  你可以在 Azure 门户中存储帐户的“终结点”页上找到这些资源 ID。

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetricDefinition -ResourceId $resourceId
```

#### <a name="reading-metric-values"></a>读取指标值

可以读取存储帐户或 Blob 存储服务的帐户级别指标值。 使用 [Get-AzMetric](/powershell/module/Az.Monitor/Get-AzMetric) cmdlet。

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetric -ResourceId $resourceId -MetricNames "UsedCapacity" -TimeGrain 01:00:00
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

#### <a name="list-the-account-level-metric-definition"></a>列出帐户级指标定义

你可以列出存储帐户或 Blob 存储服务的指标定义。 使用 [az monitor metrics list-definitions](/cli/azure/monitor/metrics#az_monitor_metrics_list_definitions) 命令。
 
在此示例中，将 `<resource-ID>` 占位符替换为整个存储帐户的资源 ID 或 Blob 存储服务的资源 ID。 你可以在 Azure 门户中存储帐户的“终结点”页上找到这些资源 ID。

```azurecli-interactive
   az monitor metrics list-definitions --resource <resource-ID>
```

#### <a name="read-account-level-metric-values"></a>读取帐户级指标值

可以读取存储帐户或 Blob 存储服务的帐户级指标值。 请使用 [az monitor metrics list](/cli/azure/monitor/metrics#az_monitor_metrics_list) 命令。

```azurecli-interactive
   az monitor metrics list --resource <resource-ID> --metric "UsedCapacity" --interval PT1H
```
### <a name="template"></a>[模板](#tab/template)

不适用。

### <a name="azure-policy"></a>[Azure Policy](#tab/policy)

不适用。

---

## <a name="analyzing-logs"></a>分析日志

你可以将资源日志作为存储帐户中的 blob 以及事件数据进行访问，也可以通过 Log Analytics 查询访问资源日志。

要详细了解这些日志中显示的字段的参考，请参阅 [Azure Blob 存储监视数据参考](monitor-blob-storage-reference.md)。

> [!NOTE]
> Azure Monitor 中的 Azure 存储日志目前为公共预览版，可在所有公有云区域中进行预览测试。 此预览版为常规用途 v1 和常规用途 v2 存储帐户中的 Blob（包括 Azure Data Lake Storage Gen2）、文件、队列、表和高级存储帐户启用日志。 经典存储帐户不受支持。

仅在针对服务终结点发出请求时才会创建日志条目。 例如，如果存储帐户的 Blob 终结点中存在活动，而表或队列终结点中没有该活动，则仅创建与 Blob 服务有关的日志。 Azure 存储日志包含有关成功和失败的存储服务请求的详细信息。 可以使用该信息监视各个请求和诊断存储服务问题。 将最大程度地记录请求。

### <a name="log-authenticated-requests"></a>记录经过身份验证的请求

 将记录以下类型的已经过身份验证的请求：

- 成功的请求
- 失败的请求，包括超时、限制、网络、授权和其他错误
- 使用共享访问签名 (SAS) 或 OAuth 的请求，包括失败和成功的请求
- 对分析数据（$logs 容器中的经典日志数据和 $metric 表中的类指标数据）的请求 

不会记录 Blob 存储服务本身发出的请求，如创建或删除日志。 若要查看所记录数据的完整列表，请参阅[存储记录的操作和状态消息](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)和[存储日志格式](monitor-blob-storage-reference.md)。

### <a name="log-anonymous-requests"></a>记录匿名请求

 记录以下类型的匿名请求：

- 成功的请求
- 服务器错误
- 客户端和服务器的超时错误
- 失败的 GET 请求，错误代码为 304（未修改）

不会记录所有其他失败的匿名请求。 若要查看所记录数据的完整列表，请参阅[存储记录的操作和状态消息](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)和[存储日志格式](monitor-blob-storage-reference.md)。

### <a name="accessing-logs-in-a-storage-account"></a>访问存储帐户中的日志

日志显示为存储到目标存储帐户中的容器的 blob。 数据作为按行分隔的 JSON 有效负载进行收集并存储在单个 blob 中。 Blob 的名称遵循以下命名约定：

`https://<destination-storage-account>.blob.core.windows.net/insights-logs-<storage-operation>/resourceId=/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<source-storage-account>/blobServices/default/y=<year>/m=<month>/d=<day>/h=<hour>/m=<minute>/PT1H.json`

下面是一个示例：

`https://mylogstorageaccount.blob.core.windows.net/insights-logs-storagewrite/resourceId=/subscriptions/`<br>`208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/blobServices/default/y=2019/m=07/d=30/h=23/m=12/PT1H.json`

### <a name="accessing-logs-in-an-event-hub"></a>访问事件中心内的日志

发送到事件中心的日志并没有存储为文件，但你可以验证事件中心是否收到了日志信息。 在 Azure 门户中，请转到事件中心，然后验证“传入消息”计数是否大于零。 

![审核日志](media/monitor-blob-storage/event-hub-log.png)

你可以使用安全信息和事件管理以及监视工具来访问和读取发送到事件中心的日志数据。 有关详细信息，请参阅[可对发送到事件中心的监视数据执行什么操作？](../../azure-monitor/essentials/stream-monitoring-data-event-hubs.md)。

### <a name="accessing-logs-in-a-log-analytics-workspace"></a>访问 Log Analytics 工作区中的日志

你可以使用 Azure Monitor 日志查询来访问发送到 Log Analytics 工作区的日志。

有关详细信息，请参阅 [Azure Monitor 中的 Log Analytics 入门](../../azure-monitor/logs/log-analytics-tutorial.md)。

数据存储在 StorageBlobLog 表中。 Data Lake Storage Gen2 的日志不会出现在专用表中。 这是因为 Data Lake Storage Gen2 不是服务。 这是你可以在存储帐户中启用的一组功能。 如果启用了这些功能，日志将继续出现在 StorageBlobLog 表中。 

#### <a name="sample-kusto-queries"></a>示例 Kusto 查询

你可在“日志搜索”栏中输入下面这些查询，以帮助监视 Blob 存储。 这些查询使用[新语言](../../azure-monitor/logs/log-query-overview.md)。

> [!IMPORTANT]
> 从存储帐户资源组菜单中选择“日志”时，会打开 Log Analytics 并将查询范围设置为当前资源组。 这意味着日志查询只包含来自该资源组的数据。 如果要运行的查询包含来自他资源或其他 Azure 服务的数据，请从“Azure Monitor”菜单中选择“日志”。 有关详细信息，请参阅 [Azure Monitor Log Analytics 中的日志查询范围和时间范围](../../azure-monitor/logs/scope.md)。

使用以下查询可帮助你监视 Azure 存储帐户：

* 列出最近三天内 10 个最常见的错误。

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by StatusText
    | top 10 by count_ desc
    ```
* 列出最近三天内导致大部分错误的前 10 个操作。

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by OperationName
    | top 10 by count_ desc
    ```
* 列出最近三天内端到端延迟最长的前 10 个操作。

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d)
    | top 10 by DurationMs desc
    | project TimeGenerated, OperationName, DurationMs, ServerLatencyMs, ClientLatencyMs = DurationMs - ServerLatencyMs
    ```
* 列出最近三天内导致服务器端限制错误的所有操作。

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText contains "ServerBusy"
    | project TimeGenerated, OperationName, StatusCode, StatusText
    ```
* 列出最近三天内使用匿名访问的所有请求。

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and AuthenticationType == "Anonymous"
    | project TimeGenerated, OperationName, AuthenticationType, Uri
    ```
* 创建最近三天内使用的操作的饼图。
    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d)
    | summarize count() by OperationName
    | sort by count_ desc 
    | render piechart
    ```
## <a name="faq"></a>常见问题解答

**Azure 存储是否支持托管磁盘或非托管磁盘的指标？**

否。 Azure 计算支持磁盘上的指标。 有关详细信息，请参阅 [托管和非托管磁盘的每个磁盘指标](https://azure.microsoft.com/blog/per-disk-metrics-managed-disks/)。

## <a name="next-steps"></a>后续步骤

- 如需查看 Azure Blob 存储创建的日志和指标的参考，请参阅 [Azure Blob 存储监视数据参考](monitor-blob-storage-reference.md)。
- 要了解如何监视 Azure 资源，请参阅[使用 Azure Monitor 监视 Azure 资源](../../azure-monitor/essentials/monitor-azure-resource.md)。
- 要详细了解指标迁移，请参阅 [Azure 存储指标迁移](../common/storage-metrics-migration.md)。
- 有关通用方案和最佳做法，请参阅[监视 Azure Blob 存储的最佳做法](blob-storage-monitoring-scenarios.md)。
