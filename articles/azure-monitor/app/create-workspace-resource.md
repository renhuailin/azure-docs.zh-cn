---
title: 新建基于工作区的 Azure Monitor Application Insights 资源 | Microsoft Docs
description: 了解启用基于工作区的新 Azure Monitor Application Insights 资源所需的步骤。
ms.topic: conceptual
ms.date: 10/06/2020
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 21cf89c7d89e310f98fea4421076d298405b6fc3
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114464453"
---
# <a name="workspace-based-application-insights-resources"></a>基于工作区的 Application Insights 资源

基于工作区的资源支持 Application Insights 与 Log Analytics 之间的完全集成。 你现在可以选择将 Application Insights 遥测数据发送到一个公用 Log Analytics 工作区，这样就可以全权访问 Log Analytics 的所有功能，同时将应用程序、基础结构和平台日志置于单一的合并位置中。

这样还可以跨资源实现公用的 Azure 基于角色的访问控制 (Azure RBAC)，并且不再需要跨应用/工作区的查询。

> [!NOTE]
> 基于工作区的 Application Insights 资源的数据引入和保留操作通过数据所在的 Log Analytics 工作区计费。 [详细了解]( ./pricing.md#workspace-based-application-insights)基于工作区的 Application Insights 资源的计费。

## <a name="new-capabilities"></a>新功能

基于工作区的 Application Insights 使得你可以利用 Azure Monitor 和 Log Analytics 的最新功能，其中包括：

* [客户管理的密钥 (CMK)](../logs/customer-managed-keys.md) 为你的数据提供静态加密，并提供只有你有权访问的加密密钥。
* 通过 [Azure 专用链接](../logs/private-link-security.md)，可使用专用终结点将 Azure PaaS 服务安全地链接到你的虚拟网络。
* 借助[适用于 Profiler 和 Snapshot Debugger 的自带存储 (BYOS)](./profiler-bring-your-own-storage.md)，你可以完全控制与 Application Insights Profiler 和 Snapshot Debugger 相关联的所有数据的静态加密策略、生存期管理策略和网络访问。 
* 使用[承诺层级](../logs/manage-cost-storage.md#pricing-model)可以节省 30% 的成本（与即用即付价格相比）。 
* 可以通过 Log Analytics 流引入提高数据引入速度。

## <a name="create-workspace-based-resource"></a>创建基于工作区的资源

登录 [Azure 门户](https://portal.azure.com)，并创建 Application Insights 资源：

> [!div class="mx-imgBorder"]
> ![基于工作区的 Application Insights 资源](./media/create-workspace-resource/create-workspace-based.png)

如果你还没有现有的 Log Analytics 工作区，请[参阅 Log Analytics 工作区创建文档](../logs/quick-create-workspace.md)。

基于工作区的资源当前在所有商业区域和 Azure 政府中可用

创建资源后，可在“概览”窗格中看到相应的工作区信息：

![工作区名称](./media/create-workspace-resource/workspace-name.png)

单击蓝色链接文本会转到关联的 Log Analytics 工作区，你可以在其中利用新的统一工作区查询环境。

> [!NOTE]
> 我们仍然针对 Application Insights 体验中的 Application Insights 经典资源查询、工作簿和基于日志的警报提供完全的后向兼容性。 若要根据[新的基于工作区的表结构/架构](apm-tables.md)进行查询/查看，必须先导航到 Log Analytics 工作区。 从 Application Insights 窗格中选择“日志(分析)”即可访问经典 Application Insights 查询体验。

## <a name="copy-the-connection-string"></a>复制连接字符串

[连接字符串](./sdk-connection-string.md?tabs=net)用于标识要与遥测数据关联的资源。 它还允许你修改可供你的资源将其用作遥测目标的终结点。 你需要复制连接字符串，并将其添加到应用程序的代码或环境变量中。

## <a name="monitoring-configuration"></a>监视配置

创建基于工作区的 Application Insights 资源后，配置监视相对简单。

### <a name="code-based-application-monitoring"></a>基于代码的应用程序监视

对于基于代码的应用程序监视，只需安装合适的 Application Insights SDK，并将其指向新建资源的检测密钥或连接字符串。  

若要详细了解如何设置 Application Insights SDK 以进行基于代码的监视，请参阅特定于语言/框架的文档：

- [ASP.NET](./asp-net.md)
- [ASP.NET Core](./asp-net-core.md)
- [后台任务和新式控制台应用程序 (.NET/.NET Core)](./worker-service.md)
- [经典控制台应用程序 (.NET)](./console.md) 
- [Java](./java-in-process-agent.md)
- [JavaScript](./javascript.md)
- [Node.js](./nodejs.md)
- [Python](./opencensus-python.md)

### <a name="codeless-monitoring-and-visual-studio-resource-creation"></a>无代码监视和 Visual Studio 资源创建

对于 Azure Functions 和 Azure 应用服务等服务的无代码监视，还需要先创建基于工作区的 Application Insights 资源，然后在监视配置阶段指向该资源。

尽管这些服务提供了在其自己的资源创建过程中创建新 Application Insights 资源的选项，但通过这些 UI 选项创建的资源当前仅限于经典 Application Insights 体验。

这同样适用于 Visual Studio for ASP.NET 和 Visual Studio for ASP.NET Core 中的 Application Insights 资源创建体验。 你必须从 Visual Studio 监视启用 UI 中选择一个基于工作区的现有资源。 选择从 Visual Studio 中创建新资源时，只能创建经典 Application Insights 资源。

## <a name="creating-a-resource-automatically"></a>自动创建资源

### <a name="azure-cli"></a>Azure CLI

若要访问预览版 Application Insights Azure CLI 命令，首先需要运行以下命令：

```azurecli
 az extension add -n application-insights
```

如果不运行 `az extension add` 命令，则会看到一条错误消息，指出：`az : ERROR: az monitor: 'app-insights' is not in the 'az monitor' command group. See 'az monitor --help'.`

现在，可以运行以下命令来创建 Application Insights 资源：

```azurecli
az monitor app-insights component create --app
                                         --location
                                         --resource-group
                                         [--application-type]
                                         [--ingestion-access {Disabled, Enabled}]
                                         [--kind]
                                         [--only-show-errors]
                                         [--query-access {Disabled, Enabled}]
                                         [--tags]
                                         [--workspace]
```

#### <a name="example"></a>示例

```azurecli
az monitor app-insights component create --app demoApp --location eastus --kind web -g my_resource_group --workspace "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/test1234/providers/microsoft.operationalinsights/workspaces/test1234555"
```

有关此命令的完整 Azure CLI 文档，请参阅 [Azure CLI 文档](/cli/azure/monitor/app-insights/component#az_monitor_app_insights_component_create)。

### <a name="azure-powershell"></a>Azure PowerShell

PowerShell 命令 `New-AzApplicationInsights` 当前不支持创建基于工作区的 Application Insights 资源。 若要使用 PowerShell 创建基于工作区的资源，可以使用以下 Azure 资源管理器模板，并使用 PowerShell 进行部署。

### <a name="azure-resource-manager-templates"></a>Azure 资源管理器模板

#### <a name="template-file"></a>模板文件

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "string"
        },
        "type": {
            "type": "string"
        },
        "regionId": {
            "type": "string"
        },
        "tagsArray": {
            "type": "object"
        },
        "requestSource": {
            "type": "string"
        },
        "workspaceResourceId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('name')]",
            "type": "microsoft.insights/components",
            "location": "[parameters('regionId')]",
            "tags": "[parameters('tagsArray')]",
            "apiVersion": "2020-02-02-preview",
            "properties": {
                "ApplicationId": "[parameters('name')]",
                "Application_Type": "[parameters('type')]",
                "Flow_Type": "Redfield",
                "Request_Source": "[parameters('requestSource')]",
                "WorkspaceResourceId": "[parameters('workspaceResourceId')]"
            }
        }
    ]
}
```

#### <a name="parameters-file"></a>参数文件

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "type": {
            "value": "web"
        },
        "name": {
            "value": "customresourcename"
        },
        "regionId": {
            "value": "eastus"
        },
        "tagsArray": {
            "value": {}
        },
        "requestSource": {
            "value": "Custom"
        },
        "workspaceResourceId": {
            "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my_resource_group/providers/microsoft.operationalinsights/workspaces/myworkspacename"
        }
    }
}

```

## <a name="modifying-the-associated-workspace"></a>修改关联的工作区

创建基于工作区的 Application Insights 资源后，可以修改关联的 Log Analytics 工作区。

从“Application Insights 资源”窗格中，选择“属性” > “更改工作区” > “Log Analytics 工作区”  

## <a name="export-telemetry"></a>导出遥测

基于工作区的资源不支持旧版连续导出功能。 相反，请选择“诊断设置” > 从 Application Insights 资源中“添加诊断设置”。 可以选择所有表，或是要存档到存储帐户或流式传输到 Azure 事件中心的表的子集。

> [!NOTE]
> 遥测导出当前不额外收费。 此功能的定价信息会在 [Azure Monitor 定价页](https://azure.microsoft.com/pricing/details/monitor/)中提供。  开始计费之前，会发送通知。 如果你选择在通知期过后继续使用 <feature name>，系统会按照适用的费率计费。 
 

## <a name="next-steps"></a>后续步骤

* [探索指标](../essentials/metrics-charts.md)
* [编写分析查询](../logs/log-query-overview.md)
