---
title: 为用于 VM 的 Azure Monitor 配置 Log Analytics 工作区
description: 介绍如何创建和配置用于 VM 的 Azure Monitor 使用的 Log Analytics 工作区。
ms.subservice: ''
ms.topic: conceptual
ms.custom: references_regions
author: bwren
ms.author: bwren
ms.date: 12/22/2020
ms.openlocfilehash: b84f9cae848d53cf04e1b77810b347786e122c5b
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/17/2021
ms.locfileid: "100608687"
---
# <a name="configure-log-analytics-workspace-for-azure-monitor-for-vms"></a>为用于 VM 的 Azure Monitor 配置 Log Analytics 工作区
用于 VM 的 Azure Monitor 从 Azure Monitor 中的一个或多个 Log Analytics 工作区收集其数据。 在载入代理之前，必须创建和配置工作区。 本文介绍工作区的要求并对其进行配置以实现用于 VM 的 Azure Monitor。

## <a name="overview"></a>概述
单个订阅可以根据你的要求使用任意数量的工作区。 工作区唯一的要求是它位于受支持的位置，并使用 *VMInsights* 解决方案进行配置。

配置工作区后，你可以使用任何可用的选项在虚拟机和虚拟机规模集上安装所需的代理，并指定一个工作区来发送其数据。 用于 VM 的 Azure Monitor 将从其订阅中任何已配置的工作区收集数据。

> [!NOTE]
> 使用 Azure 门户在单个虚拟机或虚拟机规模集上启用用于 VM 的 Azure Monitor 时，可以选择现有的工作区或创建一个新的工作区。 如果尚未安装 *VMInsights* 解决方案，则将其安装在此工作区中。 然后，你可以将此工作区用于其他代理。


## <a name="create-log-analytics-workspace"></a>创建 Log Analytics 工作区

>[!NOTE]
>本节中介绍的信息也适用于 [服务映射解决方案](service-map.md)。 

从 " **Log Analytics 工作区** " 菜单访问 Azure 门户中 Log Analytics 工作区。

[![记录 Anlytics 工作区](media/vminsights-configure-workspace/log-analytics-workspaces.png)](media/vminsights-configure-workspace/log-analytics-workspaces.png#lightbox)

可以使用以下任何方法创建新的 Log Analytics 工作区。 有关确定应在环境中使用的工作区数量以及如何设计其访问策略的指南，请参阅 [设计 Azure Monitor 日志部署](../platform/design-logs-deployment.md) 。


* [Azure 门户](../../azure-monitor/learn/quick-create-workspace.md)
* [Azure CLI](../../azure-monitor/learn/quick-create-workspace-cli.md)
* [PowerShell](../platform/powershell-workspace-configuration.md)
* [Azure Resource Manager](../samples/resource-manager-workspace.md)

## <a name="supported-regions"></a>支持的区域
用于 VM 的 Azure Monitor 支持 [Log Analytics 支持](https://azure.microsoft.com/global-infrastructure/services/?products=monitor&regions=all) 的任何区域中的 Log Analytics 工作区，但以下情况除外：

- 德国中西部
- 韩国中部

>[!NOTE]
>可以在任何区域监视 Azure Vm。 Vm 本身并不限于 Log Analytics 工作区所支持的区域。

## <a name="azure-role-based-access-control"></a>Azure 基于角色的访问控制
若要启用和访问用于 VM 的 Azure Monitor 中的功能，您必须在工作区中具有 " [Log Analytics 参与者" 角色](../platform/manage-access.md#manage-access-using-azure-permissions) 。 若要查看性能、运行状况和映射数据，你必须具有 Azure VM 的 " [监视读取](../platform/roles-permissions-security.md#built-in-monitoring-roles) 者" 角色。 有关如何控制对 Log Analytics 工作区的访问的详细信息，请参阅[管理工作区](../platform/manage-access.md)。

## <a name="add-vminsights-solution-to-workspace"></a>将 VMInsights 解决方案添加到工作区
在 Log Analytics 工作区可用于用于 VM 的 Azure Monitor 之前，必须已安装 *VMInsights* 解决方案。 以下部分介绍了配置工作区的方法。

> [!NOTE]
> 将 *VMInsights* 解决方案添加到工作区时，所有连接到该工作区的现有虚拟机都将开始向 InsightsMetrics 发送数据。 在将 Dependency Agent 添加到连接到工作区的现有虚拟机之前，不会收集其他数据类型的数据。

### <a name="azure-portal"></a>Azure 门户
有三个选项可用于使用 Azure 门户配置现有工作区。 下面描述了每个项目。

若要配置单个工作区，请在 " **Azure Monitor** " 菜单中，选择 "**虚拟机**" 选项，选择 **其他载入选项**，然后 **配置工作区**。 选择订阅和工作区，然后单击 " **配置**"。

[![配置工作区](../vm/media/vminsights-enable-policy/configure-workspace.png)](../vm/media/vminsights-enable-policy/configure-workspace.png#lightbox)

若要配置多个工作区，请在 "**监视器**" 菜单的 "**虚拟机**" 菜单中选择 "**工作区配置**" 选项卡 Azure 门户。 设置筛选器值以显示现有工作区的列表。 选择要启用的每个工作区旁边的框，然后单击 " **配置选择**"。

[![工作区配置](../vm/media/vminsights-enable-policy/workspace-configuration.png)](../vm/media/vminsights-enable-policy/workspace-configuration.png#lightbox)


使用 Azure 门户在单个虚拟机或虚拟机规模集上启用用于 VM 的 Azure Monitor 时，可以选择现有的工作区或创建一个新的工作区。 如果尚未安装 *VMInsights* 解决方案，则将其安装在此工作区中。 然后，你可以将此工作区用于其他代理。

[![在门户中启用单个 VM](../vm/media/vminsights-enable-portal/enable-vminsights-vm-portal.png)](../vm/media/vminsights-enable-portal/enable-vminsights-vm-portal.png#lightbox)


### <a name="resource-manager-template"></a>资源管理器模板
适用于用于 VM 的 Azure Monitor 的 Azure 资源管理器模板在存档文件 () .zip 中提供，可 [从 GitHub](https://aka.ms/VmInsightsARMTemplates)存储库下载该文件。 这包括名为 **ConfigureWorkspace** 的模板，用于配置用于 VM 的 Azure Monitor 的 Log Analytics 工作区。 你可以使用以下任一标准方法（包括下面的 PowerShell 和 CLI 命令示例）部署此模板： 

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
- 请参阅用于将代理连接到用于 VM 的 Azure Monitor [用于 VM 的 Azure Monitor 的内置代理](vminsights-enable-overview.md) 。
- 请参阅将 [监视解决方案定位到 Azure Monitor (预览版) ](../insights/solution-targeting.md) ，以限制从解决方案发送到工作区的数据量。
