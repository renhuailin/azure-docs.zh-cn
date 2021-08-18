---
title: 将 Azure Monitor Application Insights 经典资源迁移到基于工作区的资源 | Microsoft Docs
description: 了解将 Azure Monitor Application Insights 经典资源升级到新的基于工作区的模型需要执行的步骤。
ms.topic: conceptual
ms.date: 09/23/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3eea51b69bbb1138ac7c5418370759d1777b482c
ms.sourcegitcommit: 47ac63339ca645096bd3a1ac96b5192852fc7fb7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114361830"
---
# <a name="migrate-to-workspace-based-application-insights-resources"></a>迁移到基于工作区的 Application Insights 资源

本指南将指导你完成将经典 Application Insights 资源迁移到基于工作区的资源的过程。 基于工作区的资源支持 Application Insights 与 Log Analytics 之间的完全集成。 基于工作区的资源将 Application Insights 遥测数据发送到一个公用 Log Analytics 工作区，这样你就能够访问 [Azure Monitor 的最新功能](#new-capabilities)，同时将应用程序、基础结构和平台日志置于单一的合并位置中。

基于工作区的资源允许跨资源进行常用的 Azure 基于角色的访问控制 (Azure RBAC)，不再需要跨应用/工作区进行查询。

**基于工作区的资源当前在所有商业区域和 Azure 美国政府中可用**

## <a name="new-capabilities"></a>新功能

基于工作区的 Application Insights 使得你可以利用 Azure Monitor 和 Log Analytics 的所有最新功能，其中包括：

* [客户管理的密钥 (CMK)](../logs/customer-managed-keys.md) 为你的数据提供静态加密，并提供只有你有权访问的加密密钥。
* 通过 [Azure 专用链接](../logs/private-link-security.md)，可使用专用终结点将 Azure PaaS 服务安全地链接到你的虚拟网络。
* 借助[适用于 Profiler 和 Snapshot Debugger 的自带存储 (BYOS)](./profiler-bring-your-own-storage.md)，你可以完全控制与 Application Insights Profiler 和 Snapshot Debugger 相关联的所有数据的静态加密策略、生存期管理策略和网络访问。 
* 使用[承诺层级](../logs/manage-cost-storage.md#pricing-model)可以节省 30% 的成本（与即用即付价格相比）。 
* 可以通过 Log Analytics 流引入提高数据引入速度。

## <a name="migration-process"></a>迁移过程

迁移到基于工作区的资源时，不会将任何数据从经典资源的存储传输到新的基于工作区的存储。 而选择迁移则会将新数据的写入位置更改为 Log Analytics 工作区，同时保留对经典资源数据的访问权限。 

你的经典资源数据将持久保存，并受针对经典 Application Insights 资源的保留设置制约。 在迁移后引入的所有新数据会受关联的 Log Analytics 工作区的[保留设置](../logs/manage-cost-storage.md#change-the-data-retention-period)制约，该工作区还支持[按数据类型确定的不同保留设置](../logs/manage-cost-storage.md#retention-by-data-type)。
迁移过程是永久性的，无法撤消。 将资源迁移到基于工作区的 Application Insights 后，它将始终是基于工作区的资源。 但是，在迁移后，可以根据需要随时更改目标工作区。 

> [!NOTE]
> 基于工作区的 Application Insights 资源的数据引入和保留操作[通过数据所在的 Log Analytics 工作区计费](../logs/manage-cost-storage.md)。 如果在迁移之前选择了将引入经典 Application Insights 资源的数据保留 90 天以上，则数据保留将继续通过该 Application Insights 资源计费。 [详细了解]( ./pricing.md#workspace-based-application-insights)基于工作区的 Application Insights 资源的计费。

如果不需要迁移现有资源，但是想要创建新的基于工作区的 Application Insights 资源，请使用[基于工作区的资源创建指南](create-workspace-resource.md)。

## <a name="pre-requisites"></a>先决条件

- 一个 Log Analytics 工作区，且其访问控制模式需要设为“`use resource or workspace permissions`”设置。 

    - 基于工作区的 Application Insights 资源与设为专用“`workspace based permissions`”设置的工作区不兼容。 若要详细了解 Log Analytics 工作区访问控制，请参阅 [Log Analytics 配置访问控制模式指南](../logs/manage-access.md#configure-access-control-mode)

    - 如果你还没有现有的 Log Analytics 工作区，请[参阅 Log Analytics 工作区创建文档](../logs/quick-create-workspace.md)。
    
- 基于工作区的资源不支持连续导出，必须禁用此功能。
迁移完成后，可以使用[诊断设置](../essentials/diagnostic-settings.md)配置到存储帐户的数据存档或到 Azure 事件中心的流式传输。  

    > [!CAUTION]
    > 诊断设置使用与连续导出不同的导出格式/架构，迁移会破坏与流分析的任何现有集成。

- 在你的 Log Analytics 工作区的“常规” > “使用情况和预估成本” > “数据保留”下检查当前保留设置。   此设置会影响迁移 Application Insights 资源后新引入数据的存储时间。 如果你目前存储 Application Insights 数据的时间超过默认的 90 天，想要保留这个较长的保留期，则可能需要调整工作区保留设置。

## <a name="migrate-your-resource"></a>迁移资源

本部分将指导你完成将经典 Application Insights 资源迁移到新的基于工作区的资源类型的过程。

1. 在 Application Insights 资源的左侧菜单栏中的“配置”标题下选择“属性”。

    ![在红色框中突出显示的属性](./media/convert-classic-resource/properties.png)

2. 选择 `Migrate to Workspace-based`。
    
     ![“迁移资源”按钮](./media/convert-classic-resource/migrate.png)

3. 选择要用来存储将来引入的所有 Application Insights 遥测数据的 Log Analytics 工作区。

     ![迁移向导 UI，其中显示了用于选择目标工作区的选项](./media/convert-classic-resource/migration.png)
    

迁移资源后，可在“概述”窗格中看到相应的工作区信息：

![工作区名称](./media/create-workspace-resource/workspace-name.png)

单击蓝色链接文本会转到关联的 Log Analytics 工作区，你可以在其中利用新的统一工作区查询环境。

> [!NOTE]
> 迁移到基于工作区的 Application Insights 资源后，我们建议使用[工作区的每日上限](../logs/manage-cost-storage.md#manage-your-maximum-daily-data-volume)（而不是 Application Insights 中的上限）来限制引入和成本。

## <a name="understanding-log-queries"></a>了解日志查询

我们仍然针对 Application Insights 体验中的 Application Insights 经典资源查询、工作簿和基于日志的警报提供完全的后向兼容性。 

若要根据[新的基于工作区的表结构/架构](apm-tables.md)编写查询，必须先导航到 Log Analytics 工作区。 

从工作区中的 Log Analytics UI 直接进行查询时，你只会看到迁移后引入的数据。 若要在统一查询体验中查看经典 Application Insights 数据和迁移后引入的新数据，请从迁移后的 Application Insights 资源中使用“日志(分析)”查询视图。

## <a name="programmatic-resource-migration"></a>编程化资源迁移

### <a name="azure-cli"></a>Azure CLI

若要访问预览版 Application Insights Azure CLI 命令，首先需要运行以下命令：

```azurecli
 az extension add -n application-insights
```

如果不运行 `az extension add` 命令，则会看到一条错误消息，指出：`az : ERROR: az monitor: 'app-insights' is not in the 'az monitor' command group. See 'az monitor --help'.`

现在，可以运行以下命令来创建 Application Insights 资源：

```azurecli
az monitor app-insights component update --app
                                         --resource-group
                                         [--ingestion-access {Disabled, Enabled}]
                                         [--kind]
                                         [--query-access {Disabled, Enabled}]
                                         [--retention-time]
                                         [--workspace]
```

#### <a name="example"></a>示例

```azurecli
az monitor app-insights component update --app your-app-insights-resource-name -g your_resource_group --workspace "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/test1234/providers/microsoft.operationalinsights/workspaces/test1234555"
```

有关此命令的完整 Azure CLI 文档，请参阅 [Azure CLI 文档](/cli/azure/monitor/app-insights/component#az_monitor_app_insights_component_update)。

### <a name="azure-powershell"></a>Azure PowerShell

PowerShell 命令 `Update-AzApplicationInsights` 当前不支持将经典 Application Insights 资源迁移到基于工作区的资源。 若要使用 PowerShell 创建基于工作区的资源，可以使用以下 Azure 资源管理器模板，并使用 PowerShell 进行部署。

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

从 Application Insights 资源窗格中，选择“属性” > “更改工作区” > “Log Analytics 工作区”。  

## <a name="troubleshooting"></a>疑难解答

### <a name="access-mode"></a>访问模式

**错误消息：** 所选工作区配置为基于工作区的访问模式。某些 APM 功能可能会受影响。请选择其他工作区或在工作区设置中允许基于资源的访问权限。可以使用 CLI 解决此错误。 

为了使基于工作区的 Application Insights 资源正常运行，你需要将目标 Log Analytics 工作区的访问控制模式更改为资源或工作区权限设置。 此设置位于 Log Analytics 工作区 UI 中的“属性” > “访问控制模式”下。 有关详细说明，请参阅 [Log Analytics 配置访问控制模式指南](../logs/manage-access.md#configure-access-control-mode)。 如果访问控制模式设置为独占的“需要工作区权限”设置，则通过门户迁移体验进行的迁移会保持被阻止状态。

如果由于安全原因而无法为当前目标工作区更改访问控制模式，建议你创建新的用于迁移的 Log Analytics 工作区。 

### <a name="continuous-export"></a>连续导出

**错误消息：** 在继续操作之前，需要禁用连续导出。迁移后，请使用“诊断设置”进行导出。 

基于工作区的资源不支持旧版连续导出功能。 在迁移之前，需要禁用连续导出。

1. 在 Application Insights 资源视图的“配置”标题下，选择“连续导出”。

    ![“连续导出”菜单项](./media/convert-classic-resource/continuous-export.png)

2. 选择“禁用”。

    ![连续导出禁用按钮](./media/convert-classic-resource/disable.png)

- 选择“禁用”后，可以导航回迁移 UI。 如果“编辑连续导出”页提示你的设置不会保存，你可以针对此提示选择“确定”，因为它与禁用/启用连续导出无关。

- 将 Application Insights 资源成功迁移到基于工作区的资源后，可以使用“诊断设置”来替换连续导出过去提供的功能。 请从你的 Application Insights 资源中选择“诊断设置” > “添加诊断设置”。  可以选择所有表，或是要存档到存储帐户或流式传输到 Azure 事件中心的表的子集。 有关诊断设置的详细指南，请参阅 [Azure Monitor 诊断设置指南](../essentials/diagnostic-settings.md)。

### <a name="retention-settings"></a>保留设置

警告消息：你的自定义 Application Insights 保留设置不会应用于发送到工作区的数据。你需要单独重新配置此设置。

在迁移之前无需进行任何更改，但此消息提醒你当前的 Application Insights 保留设置未设置为默认的 90 天保留期。 此警告消息表示，你可能需要在迁移和开始引入新数据之前修改 Log Analytics 工作区的保留设置。 

你可以在 Log Analytics UI 的“常规” > “使用情况和预估成本” > “数据保留”下检查 Log Analytics 的当前保留设置。   此设置会影响迁移 Application Insights 资源后新引入数据的存储时间。

## <a name="next-steps"></a>后续步骤

* [探索指标](../essentials/metrics-charts.md)
* [编写分析查询](../logs/log-query-overview.md)
