---
title: 配置 VM 见解的 Log Analytics 工作区
description: 介绍如何创建和配置 VM 见解使用的 Log Analytics 工作区。
ms.topic: conceptual
ms.custom: references_regions, devx-track-azurepowershell
author: bwren
ms.author: bwren
ms.date: 12/22/2020
ms.openlocfilehash: a5a65f99fec0bb0db245450ead9747776e194b46
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128614422"
---
# <a name="configure-log-analytics-workspace-for-vm-insights"></a>配置 VM 见解的 Log Analytics 工作区
VM 见解从 Azure Monitor 中的一个或多个 Log Analytics 工作区收集其数据。 在加入代理之前，必须创建并配置一个工作区。 本文介绍工作区的要求，以及如何为 VM 见解配置工作区。

## <a name="overview"></a>概述
单个订阅可以根据你的要求使用任意数量的工作区。 工作区的唯一要求是位于受支持的位置，并使用 VMInsights 解决方案进行配置。

配置工作区后，可以使用任何可用选项在虚拟机和虚拟机规模集上安装所需的代理，并为其指定一个工作区用于发送其数据。 VM 见解将从其订阅中任何已配置的工作区收集数据。

> [!NOTE]
> 使用 Azure 门户在单个虚拟机或虚拟机规模集上启用 VM 见解时，门户中会提供用于选择现有工作区或创建新工作区的选项。 将在此工作区中安装 VMInsights 解决方案（如果尚未安装）。 然后，可将此工作区用于其他代理。


## <a name="create-log-analytics-workspace"></a>创建 Log Analytics 工作区

>[!NOTE]
>本部分所述的信息也适用于[服务映射解决方案](service-map.md)。 

在 Azure 门户中通过“Log Analytics 工作区”菜单访问 Log Analytics 工作区。

[![Log Anlytics 工作区](media/vminsights-configure-workspace/log-analytics-workspaces.png)](media/vminsights-configure-workspace/log-analytics-workspaces.png#lightbox)

可使用以下任一方法创建新的 Log Analytics 工作区。 有关确定应在环境中使用的工作区数量以及如何设计其访问策略的指导，请参阅[设计 Azure Monitor 日志部署](../logs/design-logs-deployment.md)。


* [Azure 门户](../logs/quick-create-workspace.md)
* [Azure CLI](../logs/resource-manager-workspace.md)
* [PowerShell](../logs/powershell-workspace-configuration.md)
* [Azure Resource Manager](../logs/resource-manager-workspace.md)

## <a name="supported-regions"></a>支持的区域
VM 见解支持 [Log Analytics 所支持的任何区域](https://azure.microsoft.com/global-infrastructure/services/?products=monitor&regions=all)中的 Log Analytics 工作区。


>[!NOTE]
>可以监视任何区域中的 Azure VM。 VM 本身并不局限于 Log Analytics 工作区支持的区域。

## <a name="azure-role-based-access-control"></a>Azure 基于角色的访问控制
若要启用并访问 VM 见解中的功能，必须在工作区中拥有 [Log Analytics 参与者](../logs/manage-access.md#manage-access-using-azure-permissions)角色。 若要查看性能、运行状况，以及映射数据，必须拥有 Azure VM 的[监视读取者](../roles-permissions-security.md#built-in-monitoring-roles)角色。 有关如何控制对 Log Analytics 工作区的访问的详细信息，请参阅[管理工作区](../logs/manage-access.md)。

## <a name="add-vminsights-solution-to-workspace"></a>将 VMInsights 解决方案添加到工作区
必须先在 Log Analytics 工作区中安装 VMInsights 解决方案，然后才能将此工作区与 VM 见解配合使用。 以下部分介绍了配置工作区的方法。

> [!NOTE]
> 将 VMInsights 解决方案添加到工作区时，所有已连接到该工作区的现有虚拟机将开始向 InsightsMetrics 发送数据。 在将 Dependency Agent 添加到与工作区连接的现有虚拟机之前，不会收集其他数据类型的数据。

### <a name="azure-portal"></a>Azure 门户
在 Azure 门户中可以使用三个选项配置现有工作区。 下面描述了每个项目。

若要配置单个工作区，请在“Azure Monitor”菜单中转到“虚拟机”选项，选择“其他加入选项”，然后选择“配置工作区”   。 选择订阅和工作区，然后单击“配置”。

[![配置工作区](../vm/media/vminsights-enable-policy/configure-workspace.png)](../vm/media/vminsights-enable-policy/configure-workspace.png#lightbox)

若要配置多个工作区，请在 Azure 门户上的“监视器”菜单中，选择“虚拟机”菜单中的“工作区配置”选项卡。 设置筛选器值以显示现有工作区的列表。 选中要启用的每个工作区旁边的框，然后单击“配置选定项”。

[![工作区配置](../vm/media/vminsights-enable-policy/workspace-configuration.png)](../vm/media/vminsights-enable-policy/workspace-configuration.png#lightbox)


使用 Azure 门户在单个虚拟机或虚拟机规模集上启用 VM 见解时，门户中会提供用于选择现有工作区或创建新工作区的选项。 将在此工作区中安装 VMInsights 解决方案（如果尚未安装）。 然后，可将此工作区用于其他代理。

[![在门户中启用单个 VM](../vm/media/vminsights-enable-portal/enable-vminsights-vm-portal.png)](../vm/media/vminsights-enable-portal/enable-vminsights-vm-portal.png#lightbox)


### <a name="resource-manager-template"></a>资源管理器模板
适用于 VM 见解的 Azure 资源管理器模板在存档文件 (.zip) 中提供，该文件可以[从 GitHub 存储库下载](https://aka.ms/VmInsightsARMTemplates)。 其中包含一个名为 ConfigureWorkspace 的模板，用于配置 VM 见解的 Log Analytics 工作区。 可以使用任何标准方法部署此模板，这些方法包括以下 PowerShell 和 CLI 示例命令： 

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az deployment group create --name ConfigureWorkspace --resource-group my-resource-group --template-file CreateWorkspace.json  --parameters workspaceResourceId='/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace' workspaceLocation='eastus'

```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
New-AzResourceGroupDeployment -Name ConfigureWorkspace -ResourceGroupName my-resource-group -TemplateFile ConfigureWorkspace.json -workspaceResourceId /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace -location eastus
```

---



## <a name="next-steps"></a>后续步骤
- 参阅[将代理加入 VM 见解](vminsights-enable-overview.md)以将代理连接到 VM 见解。
- 参阅[在 Azure Monitor 中设定监视解决方案的目标（预览版）](../insights/solution-targeting.md)，以限制从解决方案发送到工作区的数据量。