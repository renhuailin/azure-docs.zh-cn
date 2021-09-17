---
title: 创建诊断设置以将平台日志和指标发送到不同的目标
description: 通过使用某个诊断设置，将 Azure Monitor 平台指标和日志发送到 Azure Monitor 日志、Azure 存储或 Azure 事件中心。
author: bwren
ms.author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 06/09/2021
ms.openlocfilehash: 0a161c2341137abc047d81b408058ca56e192526
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2021
ms.locfileid: "122444832"
---
# <a name="create-diagnostic-settings-to-send-platform-logs-and-metrics-to-different-destinations"></a>创建诊断设置以将平台日志和指标发送到不同的目标
Azure 中的[平台日志](./platform-logs-overview.md)（包括 Azure 活动日志和资源日志）提供 Azure 资源及其所依赖的 Azure 平台的详细诊断和审核信息。 默认情况下会收集[平台指标](./data-platform-metrics.md)，它们通常存储在 Azure Monitor 指标数据库中。 本文详细介绍如何创建和配置诊断设置，以将平台指标和平台日志发送到不同的目标。

> [!IMPORTANT]
> 应先禁用任何旧的配置，然后再为活动日志创建诊断设置。 有关详细信息，请参阅[旧式收集方法](../essentials/activity-log.md#legacy-collection-methods)。

每个 Azure 资源都需有自身的诊断设置，其设置定义了以下条件：

- 发送到设置中所定义目标的日志和指标数据的类别。 不同资源类型的可用类别各不相同。
- 要将日志发送到的一个或多个目标。 当前目标包括 Log Analytics 工作区、事件中心和 Azure 存储。

一个诊断设置只能为每个目标定义一种类型。 若要将数据发送到多个特定的目标类型（例如，两个不同的 Log Analytics 工作区），请创建多个设置。 每个资源最多可以有 5 个诊断设置。

以下视频演示如何通过诊断设置来路由平台日志。
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4AvVO]

> [!NOTE]
> [平台指标](./metrics-supported.md)自动发送到 [Azure Monitor 指标](./data-platform-metrics.md)中。 使用诊断设置可将特定 Azure 服务的指标发送到 Azure Monitor 日志中，以使用具有特定限制的[日志查询](../logs/log-query-overview.md)结合其他监视数据进行分析。 
>  
>  
> 当前不支持通过诊断设置发送多维指标。 多维指标将按平展后的单维指标导出，并跨维值聚合。 例如：可以在每个节点级别浏览区块链上的“IOReadBytes”指标并为其绘制图表。 但是，当通过诊断设置导出时，导出的指标将表示为所有节点的所有读取字节数。 此外，由于内部限制，并非所有指标都可以导出到 Azure Monitor 日志/Log Analytics。 有关详细信息，请参阅[可导出指标的列表](./metrics-supported-export-diagnostic-settings.md)。 
>  
>  
> 若要解决特定指标的这些限制，建议你使用[指标 REST API](/rest/api/monitor/metrics/list) 手动提取它们并使用 [Azure Monitor 数据收集器 API](../logs/data-collector-api.md) 将其导入到 Azure Monitor 日志中。  


## <a name="destinations"></a>Destinations
平台日志和指标可以发送到下表中列出的目标。 

| 目标 | 说明 |
|:---|:---|
| [Log Analytics 工作区](../logs/design-logs-deployment.md) | 将日志和指标发送到 Log Analytics 工作区可以使用强大的日志查询结合 Azure Monitor 收集的其他监视数据对其进行分析，并利用其他 Azure Monitor 功能，例如警报和可视化。 |
| [事件中心](../../event-hubs/index.yml) | 向事件中心发送日志和指标可将数据流式传输到外部系统，例如第三方 SIEM 和其他日志分析解决方案。  |
| [Azure 存储帐户](../../storage/blobs/index.yml) | 将日志和指标存档到 Azure 存储帐户有助于审核、静态分析或备份。 与 Azure Monitor 日志和 Log Analytics 工作区相比，Azure 存储成本较低，并且日志可以无限期保留。  |


### <a name="destination-requirements"></a>目标要求

在创建诊断设置之前，必须已创建诊断设置的任何目标。 只要配置设置的用户同时拥有两个订阅的相应 Azure RBAC 访问权限，目标就不必位于发送日志的资源所在的订阅中。 借助 Azure Lighthouse，还可以将诊断设置发送到另一个 Azure Active Directory 租户所在的工作区。 下表提供了每个目标的独特要求，包括任何区域限制。

| 目标 | 要求 |
|:---|:---|
| Log Analytics 工作区 | 此工作区无需与要监视的资源在同一区域。|
| 事件中心 | 命名空间的共享访问策略定义流式处理机制具有的权限。 流式传输到事件中心需要“管理”、“发送”和“侦听”权限。 若要更新诊断设置，使之包括流式传输，则必须在事件中心授权规则中拥有 ListKey 权限。<br><br>如果资源是区域性的，则事件中心命名空间需要与要监视的资源位于同一区域中。 |
| Azure 存储帐户 | 不应使用其中存储了其他非监视数据的现有存储帐户，以便更好地控制数据所需的访问权限。 不过，如果要将活动日志和资源日志一同存档，则可以选择使用该存储帐户在一个中心位置保留所有监视数据。<br><br>若要将数据发送到不可变存储，请按照[为 Blob 存储设置和管理不可变策略](../../storage/blobs/immutable-policy-configure-version-scope.md)中所述为存储帐户设置不可变策略。 必须按照本文中的所有步骤操作，包括启用受保护的追加 blob 写入操作。<br><br>如果资源是区域性的，则存储帐户需要与要监视的资源位于同一区域中。 |

> [!NOTE]
> Azure Data Lake Storage Gen2 帐户目前不支持作为诊断设置的目标，即使它们可能在 Azure 门户中被列为有效选项。

> [!NOTE]
> 启用虚拟网络后，Azure Monitor（诊断设置）无法访问事件中心资源。 你必须启用事件中心的“允许受信任的 Microsoft 服务绕过此防火墙”设置，以便授予 Azure Monitor（诊断设置）服务访问事件中心资源的权限。 


## <a name="create-in-azure-portal"></a>在 Azure 门户中创建

可以在 Azure 门户中通过“Azure Monitor”菜单或资源菜单配置诊断设置。

1. 在 Azure 门户中配置诊断设置的位置取决于资源。

   - 对于单项资源，在资源菜单中的“监视器”下，单击“诊断设置”。 

        ![Azure 门户中资源菜单的“监视”部分的屏幕截图，其中突出显示了“诊断设置”。](media/diagnostic-settings/menu-resource.png)

   - 对于一项或多项资源，在 Azure Monitor 菜单中，单击“设置”下的“诊断设置”，然后单击相应资源。 

        ![Azure Monitor 菜单中“设置”部分的屏幕截图，其中突出显示了“诊断设置”。](media/diagnostic-settings/menu-monitor.png)

   - 对于活动日志，在“Azure Monitor”菜单中，单击“活动日志”，然后单击“诊断设置”。   请确保禁用活动日志的任何旧配置。 有关详细信息，请参阅[禁用现有设置](./activity-log.md#legacy-collection-methods)。

        ![Azure Monitor 菜单的屏幕截图，其中已选择“活动日志”，并且在“Monitor - 活动日志”菜单栏中突出显示了“诊断设置”。](media/diagnostic-settings/menu-activity-log.png)

2. 如果选定的资源上不存在任何设置，系统会提示创建设置。 单击“添加诊断设置”。

   ![添加诊断设置 - 没有现有的设置](media/diagnostic-settings/add-setting.png)

   如果资源上有现有的设置，则会看到已配置的设置列表。 单击“添加诊断设置”以添加新设置，或单击“编辑设置”以编辑现有设置。  每个设置最多只能包含一个目标类型。

   ![添加诊断设置 - 现有的设置](media/diagnostic-settings/edit-setting.png)

3. 为设置指定名称（如果未指定）。

    ![添加诊断设置](media/diagnostic-settings/setting-new-blank.png)

4. **类别详细信息(要路由的内容)** - 选中要发送到稍后指定的目标的每个数据类别对应的框。 每种 Azure 服务的类别列表各不相同。

     - **所有指标** - 将资源的平台指标路由到 Azure 日志存储，但采用日志格式。 这些指标平常只发送到 Azure Monitor 指标时序数据库。 将它们发送到 Azure Monitor 日志存储（可通过 Log Analytics 进行搜索）有助于你将它们集成到跨其他日志进行搜索的查询中。 此选项不一定适用于所有资源类型。 当受支持时，[Azure Monitor 支持的指标](./metrics-supported.md)会列出为具体资源类型收集的具体指标。

       > [!NOTE]
       > 请参阅本文前面部分介绍的将指标路由到 Azure Monitor 日志时的限制。  


     - **日志** - 列出根据资源类型提供的各种类别。 请选中你希望路由到目标的所有类别。

5. **目标详细信息** - 选中每个目标对应的框。 当选中每个框时，将显示用于添加其他信息的选项。

      ![发送到 Log Analytics 或事件中心](media/diagnostic-settings/send-to-log-analytics-event-hubs.png)

    1. **Log Analytics** - 输入订阅和工作区。  如果没有工作区，则需[在继续操作之前创建一个](../logs/quick-create-workspace.md)。

    1. **事件中心** - 指定以下条件：
       - 事件中心所属的订阅
       - 事件中心命名空间 - 如果还没有，则需[创建一个](../../event-hubs/event-hubs-create.md)
       - 要向其发送所有数据的事件中心的名称（可选）。 如果未指定名称，将为每个日志类别创建一个事件中心。 如果发送多个类别，可能需要指定一个名称来限制创建的事件中心数。 有关详细信息，请参阅 [Azure 事件中心配额和限制](../../event-hubs/event-hubs-quotas.md)。
       - 事件中心策略（可选）- 策略定义流式处理机制具有的权限。 有关详细信息，请参阅 [Event-hubs-features](../../event-hubs/event-hubs-features.md#publisher-policy)。

    1. **存储** - 选择订阅、存储帐户和保留策略。

        ![发送到存储](media/diagnostic-settings/storage-settings-new.png)

        > [!TIP]
        > 请考虑将保留策略设置为 0，并使用计划的作业手动从存储中删除数据，避免将来可能会造成的混乱。
        >
        > 首先，如果要使用存储进行存档，你通常希望数据保留 365 天以上。 其次，如果选择的保留策略大于 0，则在存储时会向日志附加一个到期日期。 日志一旦存储，则无法为其更改该日期。
        >
        > 例如，如果将 *WorkflowRuntime* 的保留策略设置为 180 天，24 小时后又将其设置为 365 天，则在前 24 小时内存储的日志将在 180 天后自动删除，而该类型的所有后续日志将在 365 天后自动删除。 后来进行的保留策略更改不会使前 24 小时的日志保留 365 天。

6. 单击“保存” 。

片刻之后，新设置会显示在此资源的设置列表中，生成新的事件数据后，日志会立即流式传输到指定的目标。 发出事件后可能需要长达 15 分钟的时间该事件才会[出现在 Log Analytics 工作区中](../logs/data-ingestion-time.md)。

## <a name="create-using-powershell"></a>使用 PowerShell 创建

在 [Azure PowerShell](../powershell-samples.md) 中使用 [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) cmdlet 创建诊断设置。 有关参数说明，请参阅此 cmdlet 的文档。

> [!IMPORTANT]
> 不能将此方法用于 Azure 活动日志。 请改为利用[使用资源管理器模板在 Azure Monitor 中创建诊断设置](./resource-manager-diagnostic-settings.md)，创建资源管理器模板并使用 PowerShell 进行部署。

以下示例 PowerShell cmdlet 使用所有三个目标创建诊断设置。

```powershell
Set-AzDiagnosticSetting -Name KeyVault-Diagnostics -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault -Category AuditEvent -MetricCategory AllMetrics -Enabled $true -StorageAccountId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount -WorkspaceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace  -EventHubAuthorizationRuleId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```

## <a name="create-using-azure-cli"></a>使用 Azure CLI 创建

在 [Azure CLI](/cli/azure/monitor) 中使用 [az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create) 命令创建诊断设置。 有关参数说明，请参阅此命令的文档。

> [!IMPORTANT]
> 不能将此方法用于 Azure 活动日志。 请改为按[使用资源管理器模板在 Azure Monitor 中创建诊断设置](./resource-manager-diagnostic-settings.md)中的说明操作，创建资源管理器模板并使用 CLI 进行部署。

以下示例 CLI 命令使用所有三个目标创建诊断设置。 语法根据你的客户端稍有不同。

# <a name="cmd"></a>[CMD](#tab/CMD)
```azurecli
az monitor diagnostic-settings create  ^
--name KeyVault-Diagnostics ^
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault ^
--logs    "[{""category"": ""AuditEvent"",""enabled"": true}]" ^
--metrics "[{""category"": ""AllMetrics"",""enabled"": true}]" ^
--storage-account /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount ^
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/microsoft.operationalinsights/workspaces/myworkspace ^
--event-hub-rule /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```
# <a name="powershell"></a>[PowerShell](#tab/PowerShell)
```azurecli
az monitor diagnostic-settings create  `
--name KeyVault-Diagnostics `
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault `
--logs    '[{""category"": ""AuditEvent"",""enabled"": true}]' `
--metrics '[{""category"": ""AllMetrics"",""enabled"": true}]' `
--storage-account /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount `
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/microsoft.operationalinsights/workspaces/myworkspace `
--event-hub-rule /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```
# <a name="bash"></a>[Bash](#tab/Bash)
```azurecli
az monitor diagnostic-settings create  \
--name KeyVault-Diagnostics \
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault \
--logs    '[{"category": "AuditEvent","enabled": true}]' \
--metrics '[{"category": "AllMetrics","enabled": true}]' \
--storage-account /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount \
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/microsoft.operationalinsights/workspaces/myworkspace \
--event-hub-rule /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```
---

## <a name="create-using-resource-manager-template"></a>使用资源管理器模板创建
若要使用资源管理器模板创建或更新诊断设置，请参阅 [Azure Monitor 中的诊断设置的资源管理器模板示例](./resource-manager-diagnostic-settings.md)。

## <a name="create-using-rest-api"></a>使用 REST API 创建
若要使用 [Azure Monitor REST API](/rest/api/monitor/) 创建或更新诊断设置，请参阅[诊断设置](/rest/api/monitor/diagnosticsettings)。

## <a name="create-using-azure-policy"></a>使用 Azure Policy 创建
由于需要为每个 Azure 资源创建诊断设置，因此在创建每个资源时，可以使用 Azure Policy 来自动创建诊断设置。 有关详细信息，请参阅[使用 Azure Policy 大规模部署 Azure Monitor](../deploy-scale.md)。

## <a name="error-metric-category-is-not-supported"></a>错误：不支持指标类别
部署诊断设置时，将收到以下错误消息：

   “不支持指标类别 ‘xxxx’”

例如： 

   “不支持指标类别 ‘ActionsFailed’”

之前部署成功的位置。 

在使用资源管理器模板、诊断设置 REST API、Azure CLI 或 Azure PowerShell 时，会出现此问题。 通过 Azure 门户创建的诊断设置不受影响，因为仅显示受支持的类别名称。

此问题由对基础 API 的最新更改引起。 除“AllMetrics”之外的指标类别不受支持，少量特定的 Azure 服务除外。 过去，部署诊断设置时，将忽略其他类别名称。 Azure Monitor 后端只是将这些类别重定向到“AllMetrics”。  从 2021 年 2 月开始，对后端进行了更新，以明确确认提供的指标类别是否准确。 此更改导致一些部署出现失败。

如果收到此错误，请更新部署，将任何指标类别名称替换为“AllMetrics”以解决此问题。 如果部署之前添加了多个类别，则应只保留具有“AllMetrics”引用的类别。 如果仍遇到问题，请通过 Azure 门户联系 Azure 支持。 

## <a name="error-setting-disappears-due-to-non-ascii-characters-in-resourceid"></a>错误：由于 resourceID 中包含非 ASCII 字符，设置消失

诊断设置不支持包含非 ASCII 字符的 resourceID（例如，Preproducción）。 由于无法重命名 Azure 中的资源，唯一的选择是创建不包含非 ASCII 字符的新资源。 如果这些字符在一个资源组中，可以将其下的资源移到新的资源组。 否则，你需要重新创建该资源。 

## <a name="next-steps"></a>后续步骤

- [详细了解 Azure 平台日志](./platform-logs-overview.md)
