---
title: Azure Monitor 中的监视解决方案 | Microsoft Docs
description: 获取有关不同问题领域的逻辑、可视化效果和数据采集规则的预打包集合的信息。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/16/2020
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: a9f1cb05a87af95272624a4a0406deac5bc0c411
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128594078"
---
# <a name="monitoring-solutions-in-azure-monitor"></a>Azure Monitor 中的监视解决方案

Azure Monitor 中的监视解决方案提供对 Azure 应用程序或服务的操作分析。 本文简要概述了 Azure 中的监视解决方案，并详细介绍如何使用和安装这些解决方案。 

可以在 Azure Monitor 中针对你使用的任何应用程序和服务添加监视解决方案。 这些解决方案通常是免费的，但收集数据可能会产生使用费。

## <a name="use-monitoring-solutions"></a>使用监视解决方案

Azure Monitor 中的“概述”页针对 Log Analytics 工作区中安装的每个解决方案显示一个磁贴。 若要打开此页，请在 [Azure 门户](https://ms.portal.azure.com)中转到“Azure Monitor”。 在“Insights”菜单上，选择“更多”以打开“Insights Hub”，然后选择“Log Analytics 工作区”。

[![显示用于打开 Insights Hub 的选项的屏幕截图。](media/solutions/insights-hub.png)](media/solutions/insights-hub.png#lightbox)


使用屏幕顶部的下拉框更改工作区或用于磁贴的时间范围。 选择解决方案的磁贴，以打开视图，其中包含它收集的数据的更详细分析。

[![显示 Azure 门户中的监视解决方案的统计信息的屏幕截图。](media/solutions/overview.png)](media/solutions/overview.png#lightbox)

监视解决方案可以包含多种类型的 Azure 资源。 可以像查看其他任何资源一样查看解决方案包含的任何资源。 例如，解决方案中包含的任何日志查询都列在[查询资源管理器](../logs/log-analytics-tutorial.md)中的“解决方案查询”下。 可在使用[日志查询](../logs/log-query-overview.md)执行临时分析时使用这些查询。

## <a name="list-installed-monitoring-solutions"></a>列出已安装的监视解决方案

### <a name="portal"></a>[Portal](#tab/portal)

若要列出订阅中安装的监视解决方案，请执行以下操作：

1. 转到 [Azure 门户](https://ms.portal.azure.com)。 搜索并选择“解决方案”。

   将列出所有工作区中安装的解决方案。 解决方案名称的后面是在其中安装该解决方案的工作区的名称。
1. 使用屏幕顶部的下拉框可按订阅或资源组进行筛选。

![显示列出的解决方案的屏幕截图。](media/solutions/list-solutions-all.png)

选择某个解决方案的名称以打开其摘要页。 此页显示解决方案中包含的所有视图，并提供解决方案本身及其工作区的选项。

![显示解决方案的摘要信息的屏幕截图。](media/solutions/solution-properties.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要列出订阅中安装的监视解决方案，请使用 [az monitor log-analytics solution list](/cli/azure/monitor/log-analytics/solution#az_monitor_log_analytics_solution_list) 命令。 在运行该命令之前，请遵循[安装监视解决方案](#install-a-monitoring-solution)中的先决条件。

```azurecli
# List all log-analytics solutions in the current subscription.
az monitor log-analytics solution list

# List all log-analytics solutions for a specific subscription
az monitor log-analytics solution list --subscription MySubscription

# List all log-analytics solutions in a resource group
az monitor log-analytics solution list --resource-group MyResourceGroup
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

若要列出订阅中安装的监视解决方案，请使用 [Get-AzMonitorLogAnalyticsSolution](/powershell/module/az.monitoringsolutions/get-azmonitorloganalyticssolution) cmdlet。 在运行该 cmdlet 之前，请遵循[安装监视解决方案](#install-a-monitoring-solution)中的先决条件。

```azurepowershell-interactive
# List all Log Analytics solutions in the current subscription
Get-AzMonitorLogAnalyticsSolution

# List all Log Analytics solutions for a specific subscription
Get-AzMonitorLogAnalyticsSolution -SubscriptionId 00000000-0000-0000-0000-000000000000

# List all Log Analytics solutions in a resource group
Get-AzMonitorLogAnalyticsSolution -ResourceGroupName MyResourceGroup
```

* * *

## <a name="install-a-monitoring-solution"></a>安装监视解决方案

### <a name="portal"></a>[Portal](#tab/portal)

[Azure 市场](https://azuremarketplace.microsoft.com)中提供了 Microsoft 和合作伙伴提供的监视解决方案。 可以搜索可用的解决方案，并使用以下过程进行安装。 安装解决方案时，必须选择要在其中安装该解决方案的 [Log Analytics 工作区](../logs/manage-access.md)，以及要将解决方案数据收集到的位置。

1. 在[订阅的解决方案列表](#list-installed-monitoring-solutions)中，选择“添加”。
1. 浏览或搜索解决方案。 还可以使用[此搜索链接](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/management-tools?page=1&subcategories=management-solutions)。
1. 查找所需的监视解决方案并阅读其说明。
1. 选择“创建”以启动安装进程。
1. 在出现提示时，指定 Log Analytics 工作区，并为解决方案提供任何所需的配置。

![显示 Azure 市场中的解决方案的屏幕截图。](media/solutions/install-solution.png)

### <a name="install-a-solution-from-the-community"></a>从社区安装解决方案

社区成员可以将管理解决方案提交到 Azure 快速入门模板。 可以直接安装这些解决方案，也可以下载模板以供今后安装。

1. 请遵循 [Log Analytics 工作区和自动化帐户](#log-analytics-workspace-and-automation-account)中所述的过程来链接工作区和帐户。
2. 转到 [Azure 快速入门模板](https://azure.microsoft.com/resources/templates/)。
3. 搜索感兴趣的解决方案。
4. 从结果中选择解决方案以查看其详细信息。
5. 选择“部署到 Azure”按钮。
6. 除了解决方案中任何参数的值，系统还会提示提供资源组和位置等信息。
7. 选择“购买”可安装解决方案。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="prepare-your-environment"></a>准备环境

1. 安装 Azure CLI。

   需要[安装 Azure CLI](/cli/azure/install-azure-cli)，然后才能运行 CLI 引用命令。 如果愿意，你还可以使用 Azure Cloud Shell 来完成本文中的步骤。 Azure Cloud Shell 是一种可以通过浏览器使用的交互式 shell 环境。 使用下列方法之一打开 Cloud Shell：

   - 转到 [Cloud Shell 网页](https://shell.azure.com)。

   - 在 [Azure 门户](https://portal.azure.com)的右上角菜单栏上，选择“Cloud Shell”按钮。 

1. 登录。

   如果使用的是 CLI 的本地安装，请使用 [az login](/cli/azure/reference-index#az_login) 命令登录。  遵循终端中显示的步骤完成身份验证过程。

    ```azurecli
    az login
    ```

1. 安装 `log-analytics-solution` 扩展。

   `log-analytics-solution` 命令是核心 Azure CLI 的实验性扩展。 在[使用 Azure CLI 的扩展](/cli/azure/azure-cli-extensions-overview?)中详细了解扩展参考。

   ```azurecli
   az extension add --name log-analytics-solution
   ```

   预期会出现以下警告。

   ```output
   The installed extension `log-analytics-solution` is experimental and not covered by customer support.  Please use with discretion.
   ```

### <a name="install-a-solution-with-the-azure-cli"></a>通过 Azure CLI 安装解决方案

安装解决方案时，必须选择要在其中安装该解决方案的 [Log Analytics 工作区](../logs/manage-access.md)，以及要将解决方案数据收集到的位置。  使用 Azure CLI，你可以通过 [az monitor log-analytics workspace](/cli/azure/monitor/log-analytics/workspace) 引用命令来管理工作区。  请遵循 [Log Analytics 工作区和自动化帐户](#log-analytics-workspace-and-automation-account)中所述的过程来链接工作区和帐户。

使用 [az monitor log-analytics solution create](/cli/azure/monitor/log-analytics/solution) 命令安装监视解决方案。  方括号中的参数是可选的。

```azurecli
az monitor log-analytics solution create --name
                                         --plan-product
                                         --plan-publisher
                                         --resource-group
                                         --workspace
                                         [--no-wait]
                                         [--tags]
```

下面是一个代码示例，用于为 OMSGallery/容器的计划产品创建 Log Analytics 解决方案。

```azurecli
az monitor log-analytics solution create --resource-group MyResourceGroup \
                                         --name Containers({SolutionName}) \
                                         --tags key=value \
                                         --plan-publisher Microsoft  \
                                         --plan-product "OMSGallery/Containers" \
                                         --workspace "/subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/ \
                                           Microsoft.OperationalInsights/workspaces/{WorkspaceName}"
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

### <a name="prepare-your-environment"></a>准备环境

1. 安装 Azure PowerShell 中的说明进行操作。

   在运行 Azure PowerShell 引用命令之前，需要[安装 Azure PowerShell](/powershell/azure/install-az-ps)。 如果愿意，你还可以使用 Azure Cloud Shell 来完成本文中的步骤。 Azure Cloud Shell 是一种可以通过浏览器使用的交互式 shell 环境。 使用下列方法之一打开 Cloud Shell：

   - 转到 [Cloud Shell 网页](https://shell.azure.com)。

   - 在 [Azure 门户](https://portal.azure.com)的右上角菜单栏上，选择“Cloud Shell”按钮。

   > [!IMPORTANT]
   > 在 Az.MonitoringSolutions PowerShell 模块处于预览版阶段时，必须使用 `Install-Module` cmdlet 来单独安装该模块。 此 PowerShell 模块正式发布后，它会包含在将来的 Az PowerShell 模块发行版中，并在 Azure Cloud Shell 中默认提供。

   ```azurepowershell-interactive
   Install-Module -Name Az.MonitoringSolutions
   ```

1. 登录。

   如果使用的是 PowerShell 的本地安装，请使用 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet 登录。 按照 PowerShell 中显示的步骤操作，完成身份验证过程。

   ```azurepowershell
   Connect-AzAccount
   ```

### <a name="install-a-solution-with-azure-powershell"></a>通过 Azure PowerShell 安装解决方案

安装解决方案时，必须选择要在其中安装该解决方案的 [Log Analytics 工作区](../logs/manage-access.md)，以及要将解决方案数据收集到的位置。 借助 Azure PowerShell，可使用 [Az.MonitoringSolutions](/powershell/module/az.monitoringsolutions) PowerShell 模块中的 cmdlet 管理工作区。 请遵循 [Log Analytics 工作区和自动化帐户](#log-analytics-workspace-and-automation-account)中所述的过程来链接工作区和帐户。

使用 [New-AzMonitorLogAnalyticsSolution](/powershell/module/az.monitoringsolutions/new-azmonitorloganalyticssolution) cmdlet 安装监视解决方案。 方括号中的参数是可选的。

```azurepowershell
New-AzMonitorLogAnalyticsSolution -ResourceGroupName <string> -Type <string> -Location <string>
-WorkspaceResourceId <string> [-SubscriptionId <string>] [-Tag <hashtable>]
[-DefaultProfile <psobject>] [-Break] [-HttpPipelineAppend <SendAsyncStep[]>]
[-HttpPipelinePrepend <SendAsyncStep[]>] [-Proxy <uri>] [-ProxyCredential <pscredential>]
[-ProxyUseDefaultCredentials] [-WhatIf] [-Confirm] [<CommonParameters>]
```

下面的示例为 Log Analytics 工作区创建监视解决方案。

```azurepowershell-interactive
$workspace = Get-AzOperationalInsightsWorkspace -ResourceGroupName MyResourceGroup -Name WorkspaceName
New-AzMonitorLogAnalyticsSolution -Type Containers -ResourceGroupName MyResourceGroup -Location $workspace.Location -WorkspaceResourceId $workspace.ResourceId
```

* * *

## <a name="log-analytics-workspace-and-automation-account"></a>Log Analytics 工作区和自动化帐户

所有监视解决方案都需要使用一个 [Log Analytics 工作区](../logs/manage-access.md)来存储收集的数据，以及托管日志搜索和视图。 某些解决方案还需要使用一个[自动化帐户](../../automation/automation-security-overview.md)来包含 Runbook 和相关资源。 工作区和帐户必须满足以下要求：

* 解决方案的每项安装只能使用一个 Log Analytics 工作区和一个自动化帐户。 可以在多个工作区中单独安装解决方案。
* 如果解决方案需要自动化帐户，则必须将 Log Analytics 工作区和自动化帐户相互链接。 一个 Log Analytics 工作区只能链接到一个自动化帐户，而一个自动化帐户也只能链接到一个 Log Analytics 工作区。

通过 Azure 市场安装解决方案时，系统会提示你提供一个工作区和自动化帐户。 如果工作区与自动化帐户之间尚未建立链接，则系统会创建这种链接。

若要验证 Log Analytics 工作区和自动化帐户之间的链接，请执行以下操作：

1. 在 Azure 门户中选择自动化帐户。
1. 滚动到菜单的“相关资源”部分，然后选择“链接的工作区”。
1. 如果工作区已链接到自动化帐户，此页面会列出它链接到的工作区。 如果你选择所列出工作区的名称，则会被重定向到此工作区的概述页面。

## <a name="remove-a-monitoring-solution"></a>删除监视解决方案

### <a name="portal"></a>[Portal](#tab/portal)

若要使用门户删除已安装的解决方案，请在[已安装的解决方案列表](#list-installed-monitoring-solutions)中找到它。 选择解决方案的名称以打开其摘要页，然后选择“删除”。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 删除已安装的解决方案，请使用 [az monitor log-analytics solution delete](/cli/azure/monitor/log-analytics/solution#az_monitor_log_analytics_solution_delete) 命令。

```azurecli
az monitor log-analytics solution delete --name
                                         --resource-group
                                         [--no-wait]
                                         [--yes]
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

若要使用 Azure PowerShell 删除已安装的解决方案，请使用 [Remove-AzMonitorLogAnalyticsSolution](/powershell/module/az.monitoringsolutions/remove-azmonitorloganalyticssolution) cmdlet。

```azurepowershell-interactive
Remove-AzMonitorLogAnalyticsSolution  -ResourceGroupName MyResourceGroup -Name WorkspaceName
```

* * *

## <a name="next-steps"></a>后续步骤

* 获取 [Microsoft 提供的监视解决方案的列表](../monitor-reference.md)。
* 了解如何[创建查询](../logs/log-query-overview.md)来分析监视解决方案收集的数据。
* 查看所有[适用于 Azure Monitor 的 Azure CLI 命令](/cli/azure/azure-cli-reference-for-monitor)。
