---
title: 监视 Azure 防火墙日志和指标
description: 本文介绍如何启用和管理 Azure 防火墙日志和指标。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 08/03/2021
ms.author: victorh
ms.openlocfilehash: 8a86c3d9c81d332fdd1072f937d8e222d5a6436d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121746498"
---
# <a name="monitor-azure-firewall-logs-and-metrics"></a>监视 Azure 防火墙日志和指标

可以使用防火墙日志来监视 Azure 防火墙。 此外，可以使用活动日志来审核对 Azure 防火墙资源执行的操作。 使用指标，可以在门户中查看性能计数器。

可通过门户访问其中部分日志。 可将日志发送到 [Azure Monitor 日志](../azure-monitor/insights/azure-networking-analytics.md)、存储和事件中心，并使用 Azure Monitor 日志或其他工具（例如 Excel 和 Power BI）对其进行分析。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>先决条件

在开始之前，应阅读 [Azure 防火墙日志和指标](logs-and-metrics.md)，简要了解可用于 Azure 防火墙的诊断日志和指标。

## <a name="enable-diagnostic-logging-through-the-azure-portal"></a>通过 Azure 门户启用诊断日志记录

完成此过程以启用诊断日志记录后，可能需要经过几分钟的时间，数据才会显示在日志中。 如果一开始未看到任何内容，请在几分钟后重新查看。

1. 在 Azure 门户中，打开防火墙资源组并选择防火墙。
2. 在“监视”下，选择“诊断设置” 。

   对于 Azure 防火墙，有四种特定于服务的日志可用：

   * AzureFirewallApplicationRule
   * AzureFirewallNetworkRule
   * AzureFirewallDnsProxy


3. 选择“添加诊断设置”。 “诊断设置”  页提供用于诊断日志的设置。
5. 在此示例中，Azure Monitor 日志存储日志，因此请键入“防火墙日志分析”作为名称  。
6. 在“日志”下面，选择“AzureFirewallApplicationRule”、“AzureFirewallNetworkRule”和“AzureFirewallDnsProxy”以收集日志   。
7. 选择“发送到 Log Analytics”以配置工作区。
8. 选择订阅。
9. 选择“保存”。

## <a name="enable-diagnostic-logging-by-using-powershell"></a>使用 PowerShell 启用诊断日志记录

每个 Resource Manager 资源都会自动启用活动日志记录。 必须启用诊断日志记录才能开始收集通过这些日志提供的数据。

若要通过 PowerShell 启用诊断日志记录，请使用以下步骤：

1. 记下 Log Analytics 工作区资源 ID，其中存储了日志数据。 此值的形式为：`/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/microsoft.operationalinsights/workspaces/<workspace name>`。

   可以使用订阅中的任何工作区。 可使用 Azure 门户查找此信息。 此信息位于资源的“属性”页中。

2. 请注意防火墙的资源 ID。 此值的形式为：`/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/azureFirewalls/<Firewall name>`。

   可使用门户查找此信息。

3. 使用以下 PowerShell cmdlet 为所有日志和指标启用诊断日志记录：

   ```azurepowershell
      $diagSettings = @{
      Name = 'toLogAnalytics'
      ResourceId = '/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/azureFirewalls/<Firewall name>'
      WorkspaceId = '/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/microsoft.operationalinsights/workspaces/<workspace name>'
      Enabled = $true
      }
   Set-AzDiagnosticSetting  @diagSettings 
   ```

## <a name="enable-diagnostic-logging-by-using-the-azure-cli"></a>通过 Azure CLI 启用诊断日志记录

每个 Resource Manager 资源都会自动启用活动日志记录。 必须启用诊断日志记录才能开始收集通过这些日志提供的数据。

若要通过 Azure CLI 启用诊断日志记录，请使用以下步骤：

1. 记下 Log Analytics 工作区资源 ID，其中存储了日志数据。 此值的形式为：`/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/azureFirewalls/<Firewall name>`。

   可以使用订阅中的任何工作区。 可使用 Azure 门户查找此信息。 此信息位于资源的“属性”页中。

2. 请注意防火墙的资源 ID。 此值的形式为：`/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/azureFirewalls/<Firewall name>`。

   可使用门户查找此信息。

3. 使用以下 Azure CLI 命令为所有日志和指标启用诊断日志记录：

   ```azurecli
      az monitor diagnostic-settings create -n 'toLogAnalytics'
      --resource '/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/azureFirewalls/<Firewall name>'
      --workspace '/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/microsoft.operationalinsights/workspaces/<workspace name>'
      --logs '[{\"category\":\"AzureFirewallApplicationRule\",\"Enabled\":true}, {\"category\":\"AzureFirewallNetworkRule\",\"Enabled\":true}, {\"category\":\"AzureFirewallDnsProxy\",\"Enabled\":true}]' 
      --metrics '[{\"category\": \"AllMetrics\",\"enabled\": true}]'
   ```

## <a name="view-and-analyze-the-activity-log"></a>查看和分析活动日志

可使用以下任意方法查看和分析活动日志数据：

* **Azure 工具**：通过 Azure PowerShell、Azure CLI、Azure REST API 或 Azure 门户检索活动日志中的信息。 [使用 Resource Manager 的活动操作](../azure-resource-manager/management/view-activity-logs.md)一文中详细介绍了每种方法的分步说明。
* Power BI  ：如果尚无 [Power BI](https://powerbi.microsoft.com/pricing) 帐户，可免费试用。 使用[适用于 Power BI 的 Azure 活动日志内容包](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/)，可以借助预配置的仪表板（可直接使用或进行自定义）分析数据。
* **Azure Sentinel**：可以将 Azure 防火墙日志连接到 Azure Sentinel，以便查看工作簿中的日志数据，使用这些数据创建自定义警报，并通过整合这些数据来改进调查。 Azure Sentinel 中的 Azure 防火墙数据连接器目前为公共预览版。 有关详细信息，请参阅[从 Azure 防火墙连接数据](../sentinel/connect-azure-firewall.md)。

   有关概述，请参阅 Mohit Kumar 的以下视频：
   > [!VIDEO https://www.microsoft.com/videoplayer/embed/RWI4nn]


## <a name="view-and-analyze-the-network-and-application-rule-logs"></a>查看和分析网络与应用程序规则日志

[Azure 防火墙工作簿](firewall-workbook.md)为 Azure 防火墙数据分析提供了一个灵活的画布。 该画布可用于在 Azure 门户中创建丰富的视觉对象报表。 你可以利用跨 Azure 部署的多个防火墙，并将其组合成统一的交互式体验。

还可以连接到存储帐户并检索访问和性能日志的 JSON 日志条目。 下载 JSON 文件后，可以将其转换为 CSV 并在 Excel、Power BI 或任何其他数据可视化工具中查看。

> [!TIP]
> 如果熟悉 Visual Studio 和更改 C# 中的常量和变量值的基本概念，则可以使用 GitHub 提供的[日志转换器工具](https://github.com/Azure-Samples/networking-dotnet-log-converter)。

## <a name="view-metrics"></a>查看指标
浏览 Azure 防火墙。 在“监视”下，选择“指标”。  若要查看可用值，请选择“指标”下拉列表  。

## <a name="next-steps"></a>后续步骤

将防火墙配置为收集日志后，可以浏览 Azure Monitor 日志以查看数据。

- [使用 Azure 防火墙工作簿监视日志](firewall-workbook.md)

- [Azure Monitor 日志中的网络监视解决方案](../azure-monitor/insights/azure-networking-analytics.md)
