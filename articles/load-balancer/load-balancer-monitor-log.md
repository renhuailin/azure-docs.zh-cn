---
title: 监视公共负载均衡器的操作、事件和计数器
titleSuffix: Azure Load Balancer
description: 了解如何为 Azure 负载均衡器启用日志记录。
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/05/2020
ms.author: allensu
ms.openlocfilehash: fcfd3da30ef9ace723b4204f5924591b1e2717f8
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/15/2020
ms.locfileid: "97503159"
---
# <a name="azure-monitor-logs-for-azure-standard-load-balancer"></a>Azure 标准负载均衡器 Azure Monitor 日志

你可以使用不同类型的 Azure Monitor 日志来管理 Azure 标准负载均衡器并对其进行故障排除。 可以将日志流式传输到事件中心或 Log Analytics 工作区。 你可以从 Azure Blob 存储提取所有日志并在 Excel 和 Power BI 等工具中查看它们。 

日志类型为：

* **活动日志：** 可以查看提交到 Azure 订阅的所有活动及其状态。 有关详细信息，请参阅 [查看活动日志以监视对资源的操作](../azure-resource-manager/management/view-activity-logs.md)。 默认情况下，活动日志处于启用状态，可以在 Azure 门户中查看。 这些日志适用于 Azure 基本负载均衡器和标准负载均衡器。
* **标准负载均衡器度量值：** 你可以使用此日志来查询导出为标准负载均衡器的日志的指标。 这些日志仅可用于标准负载均衡器。

> [!IMPORTANT]
> 运行状况探测和负载均衡器警报事件日志当前不起作用，并已在 [Azure 负载均衡器的已知问题](whats-new.md#known-issues)中列出。 

> [!IMPORTANT]
> 日志仅适用于在 Azure 资源管理器部署模型中部署的 Azure 资源。 不能将日志用于经典部署模型中的资源。 有关部署模型的详细信息，请参阅[了解 Resource Manager 部署和经典部署](../azure-resource-manager/management/deployment-models.md)。

## <a name="enable-logging"></a>启用日志记录

每个 Resource Manager 资源都会自动启用活动日志记录。 需启用事件和运行状况探测日志记录才能开始收集通过这些日志提供的数据。 请使用以下步骤：

1. 登录到 [Azure 门户](https://portal.azure.com)。 如果用户还没有负载均衡器，请先 [创建负载均衡器](./quickstart-load-balancer-standard-public-portal.md) ，并继续。
1. 在门户中，选择 " **资源组**"。
2. 选择负载均衡器所在的 **\<resource-group-name>** 。
3. 选择负载均衡器。
4. 选择 "**活动日志**  >  **诊断设置**"。
5. 在“诊断设置”窗格中，在“诊断设置”下选择“添加诊断设置”。  
6. 在 "**诊断设置**" 创建窗格的 "**名称**" 框中，输入 **myLBDiagnostics** 。
7. “诊断设置”有三个选项。 您可以选择一个、两个或全部三个，并为每个条件配置您的要求：

   * **存档到存储帐户**。 此进程需要已创建好的存储帐户。 若要创建存储帐户，请参阅[创建存储帐户](../storage/common/storage-account-create.md?tabs=azure-portal)。
     1. 选中 " **存档到存储帐户** " 复选框。
     2. 选择“配置”，打开“选择存储帐户”窗格 。
     3. 在 " **订阅** " 下拉列表中，选择在其中创建存储帐户的订阅。
     4. 在 " **存储帐户** " 下拉列表中，选择存储帐户的名称。
     5. 选择“确定”  。

   * **流式传输到事件中心**。 此进程需要已创建好的事件中心。 若要创建事件中心，请参阅 [快速入门：使用 Azure 门户创建事件中心](../event-hubs/event-hubs-create.md)。
     1. 选择“流式传输到事件中心”复选框。
     2. 选择“配置”，打开“选择事件中心”窗格 。
     3. 在 " **订阅** " 下拉列表中，选择在其中创建事件中心的订阅。
     4. 在 " **选择事件中心命名空间** " 下拉列表中，选择命名空间。
     5. 在 " **选择事件中心策略名称** " 下拉列表中，选择名称。
     6. 选择“确定”  。

   * **发送到 Log Analytics**。 此进程需要已创建并配置好的 Log Analytics 工作区。 若要创建 Log Analytics 工作区，请参阅 [在 Azure 门户中创建 Log Analytics 工作区](../azure-monitor/learn/quick-create-workspace.md)。
     1. 选中“发送到 Log Analytics”复选框。
     2. 在 " **订阅** " 下拉列表中，选择 Log Analytics 工作区所在的订阅。
     3. 在 " **Log Analytics 工作区** " 下拉列表中，选择工作区。

8. 在 "**诊断设置**" 窗格的 "**指标**" 部分中，选中 " **AllMetrics** " 复选框。

9. 验证所有内容是否正确，然后选择 "**诊断设置**" 创建窗格顶部的 "**保存**"。

## <a name="view-and-analyze-the-activity-log"></a>查看和分析活动日志

默认生成活动日志。 可以按照 [本文中的说明](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log)将其配置为在订阅级别上导出。 若要了解这些日志的详细信息，请阅读[查看活动日志以监视对资源的操作](../azure-resource-manager/management/view-activity-logs.md)一文。

您可以使用以下任一方法来查看和分析活动日志数据：

* **Azure 工具：** 通过 Azure PowerShell、Azure CLI、Azure REST API 或 Azure 门户从活动日志中检索信息。 [资源管理器的审核操作](../azure-resource-manager/management/view-activity-logs.md)一文提供了每种方法的分步说明。
* **Power BI：** 如果还没有 [Power BI](https://powerbi.microsoft.com/pricing) 帐户，可以免费试用。 通过使用 [Power BI 的 Azure 审核日志集成](https://powerbi.microsoft.com/integrations/azure-audit-logs/)，你可以使用预配置的仪表板分析数据。 您也可以根据自己的需求自定义视图。

## <a name="view-and-analyze-metrics-as-logs"></a>以日志形式查看和分析指标
通过使用 Azure Monitor 中的导出功能，可以导出负载均衡器指标。 这些度量值将为每分钟采样间隔生成一个日志条目。

针对每个资源级别启用指标到日志的导出。 若要启用这些日志：

1. 中转到 " **诊断设置** " 窗格。
1. 按资源组筛选，然后选择要为其启用指标导出的负载均衡器实例。 
1. 当负载均衡器的 "诊断设置" 页启动时，选择 " **AllMetrics** " 以将合格的指标导出为日志。

有关指标出口限制，请参阅本文的 [限制](#limitations) 部分。

在标准负载均衡器的诊断设置中启用 **AllMetrics** 后，如果你使用的是事件中心或 Log Analytics 工作区，则这些日志将在 **AzureMonitor** 表中进行填充。 

如果要导出到存储，请连接到存储帐户并检索事件和运行状况探测日志的 JSON 日志条目。 下载 JSON 文件后，可以将其转换为 CSV，并在 Excel、Power BI 或任何其他数据可视化工具中查看它们。 

> [!TIP]
> 如果熟悉 Visual Studio 和更改 c # 中的常量和变量值的基本概念，则可以使用 GitHub 提供的 [日志转换器工具](https://github.com/Azure-Samples/networking-dotnet-log-converter) 。

## <a name="stream-to-an-event-hub"></a>流式传输到事件中心
将诊断信息流式传输到事件中心时，可以将其用于在 Azure Monitor 集成的合作伙伴 SIEM 工具中进行集中式日志分析。 有关详细信息，请参阅 [将 Azure 监视数据流式传输到事件中心](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration)。

## <a name="send-to-log-analytics"></a>发送到 Log Analytics
可以将 Azure 中资源的诊断信息直接发送到 Log Analytics 工作区。 在该工作区中，可以对信息运行复杂的查询，以便进行故障排除和分析。 有关详细信息，请参阅 [在 Azure Monitor 中的 Log Analytics 工作区中收集 Azure 资源日志](../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace)。

## <a name="limitations"></a>限制
用于 Azure 负载均衡器的指标到日志的导出功能具有以下限制：
* 作为日志导出时，当前通过内部名称显示度量值。 可以在下表中找到映射。
* 不保留指标的维数。 例如，使用 **DipAvailability** 等指标 (运行状况探测状态) ，你将无法按后端 IP 地址进行拆分或查看。
* 已使用的 SNAT 端口和分配的 SNAT 端口的指标目前不可用作日志导出。

## <a name="next-steps"></a>后续步骤
* [查看负载均衡器的可用指标](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics)
* [按照以下 Azure Monitor 说明创建和测试查询](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)
