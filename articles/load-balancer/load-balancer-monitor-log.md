---
title: 监视公共基本负载均衡器的操作、事件和计数器
titleSuffix: Azure Load Balancer
description: 了解如何为 Azure 负载均衡器启用日志记录
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
ms.openlocfilehash: 6742723e24df83ac8112e224f1999f116ab82c94
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2020
ms.locfileid: "96572773"
---
# <a name="azure-monitor-logs-for-the-standard-azure-load-balancer"></a>标准 Azure 负载均衡器 Azure Monitor 日志

可以在 Azure 中使用不同类型的日志对标准负载均衡器进行管理和故障排除。 可以将日志流式传输到事件中心或 Log Analytics 工作区。 所有日志都可从 Azure Blob 存储提取并在 Excel 和 Power BI 等各种工具中查看。  可从下表了解有关各种类型日志的详细信息。

* **活动日志：** 可以使用 [查看活动日志以监视对资源的操作](../azure-resource-manager/management/view-activity-logs.md)，查看提交到 Azure 订阅的所有活动及其状态。 活动日志默认情况下启用，并且可以在 Azure 门户中查看。 这些日志适用于基本和标准负载均衡器。
* **标准负载均衡器度量值：** 你可以使用此日志来查询导出为标准 Azure 负载均衡器的日志的指标。 这些日志仅适用于标准负载均衡器。

> [!IMPORTANT]
> **运行状况探测和负载均衡器警报事件日志当前不起作用，并已在 [Azure 负载均衡器的已知问题](whats-new.md#known-issues)中列出。** 

> [!IMPORTANT]
> 日志仅适用于在资源管理器部署模型中部署的资源。 不能将日志用于经典部署模型中的资源。 有关部署模型的详细信息，请参阅[了解 Resource Manager 部署和经典部署](../azure-resource-manager/management/deployment-models.md)。

## <a name="enable-logging"></a>启用日志记录

每个 Resource Manager 资源都会自动启用活动日志记录。 需启用事件和运行状况探测日志记录才能开始收集通过这些日志提供的数据。 使用以下步骤启用日志记录。

登录到 [Azure 门户](https://portal.azure.com)。 如果用户还没有负载均衡器，请先 [创建负载均衡器](./quickstart-load-balancer-standard-public-portal.md) ，并继续。

1. 在门户中，单击“资源组”。
2. 选择负载均衡器所在的 **\<resource-group-name>** 。
3. 选择负载均衡器。
4. 选择 "**活动日志**  >  **诊断设置**"。
5. 在“诊断设置”窗格中，在“诊断设置”下选择“添加诊断设置”。  
6. 在“诊断设置”创建窗格中，在“名称”字段中输入“myLBDiagnostics”  。
7. “诊断设置”有三个选项。  可以选择一个、两个或全部三个，并根据要求对各选项进行配置：
   * **存档到存储帐户**
   * **流式传输到事件中心**
   * **发送到 Log Analytics**

    ### <a name="archive-to-a-storage-account"></a>存档到存储帐户
    此进程需要已创建好的存储帐户。  若要创建存储帐户，请参阅[创建存储帐户](../storage/common/storage-account-create.md?tabs=azure-portal)

    1. 选中“存档到存储帐户”旁的复选框。
    2. 选择“配置”，打开“选择存储帐户”窗格 。
    3. 在下拉框中，选择在其中创建了存储帐户的“订阅”。
    4. 在下拉框中，在“存储帐户”下选择存储帐户的名称。
    5. 选择“确定”。

    ### <a name="stream-to-an-event-hub"></a>流式传输到事件中心
    此进程需要已创建好的事件中心。  若要创建事件中心，请参阅[快速入门：使用 Azure 门户创建事件中心](../event-hubs/event-hubs-create.md)

    1. 选中“流式传输到事件中心”旁的复选框
    2. 选择“配置”，打开“选择事件中心”窗格 。
    3. 在下拉框中，选择在其中创建了事件中心的“订阅”。
    4. 在下拉框中，选择“事件中心命名空间”。
    5. 在下拉框中，选择“事件中心策略名称”。
    6. 选择“确定”。

    ### <a name="send-to-log-analytics"></a>发送到 Log Analytics
    此进程需要已创建并配置好的 Log Analytics 工作区。  若要创建 Log Analytics 工作区，请参阅[在 Azure 门户中创建 Log Analytics 工作区](../azure-monitor/learn/quick-create-workspace.md)

    1. 选择“发送到 Log Analytics”旁的复选框。
    2. 在下拉框中，选择 Log Analytics 工作区所在的“订阅”。
    3. 在下拉框中选择“Log Analytics 工作区”。


8.  在 "**诊断设置**" 窗格的 "**指标**" 部分下，选中 " **AllMetrics** " 旁边的复选框。

9. 检查确认所有内容都正确，然后单击创建“诊断设置”窗格顶部的“保存” 。

## <a name="activity-log"></a>活动日志

默认生成活动日志。 可以 [按照本文中的说明](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log)将其配置为在订阅级别上导出。 若要了解这些日志的详细信息，请阅读[查看活动日志以监视对资源的操作](../azure-resource-manager/management/view-activity-logs.md)一文。

### <a name="view-and-analyze-the-activity-log"></a>查看和分析活动日志

可使用以下任意方法查看和分析活动日志数据：

* **Azure 工具：** 通过 Azure PowerShell、Azure 命令行接口 (CLI)、Azure REST API 或 Azure 门户检索活动日志中的信息。 [使用 Resource Manager 审核操作](../azure-resource-manager/management/view-activity-logs.md)一文中详细介绍了每种方法的分步说明。
* **Power BI：** 如果还没有 [Power BI](https://powerbi.microsoft.com/pricing) 帐户，可以免费试用。 使用 [适用于 Power BI 的 Azure 审核日志集成](https://powerbi.microsoft.com/integrations/azure-audit-logs/)，你可以使用预配置的仪表板分析数据，也可以自定义视图以满足你的要求。

## <a name="metrics-as-logs"></a>作为日志的指标
使用指标来记录 Azure Monitor 提供的导出功能，你可以导出负载均衡器指标。 这些指标将为每分钟采样间隔生成一个日志条目。

针对每个资源级别启用了日志导出指标。 可以通过转到 "诊断设置" 边栏选项卡，按资源组筛选，然后选择要为其启用指标导出的负载均衡器来启用这些日志。 当 "负载均衡器诊断设置" 页启动时，选择 "AllMetrics" 以将合格的指标导出为日志。

有关指标出口限制，请参阅本文的 " [限制](#limitations) " 部分。

### <a name="view-and-analyze-metrics-as-logs"></a>以日志形式查看和分析指标
在标准负载均衡器的诊断设置中启用 AllMetrics 后，如果使用事件中心或 Log Analytics 工作区，则这些日志将填充到 AzureMonitor 表中。 如果导出到存储，请连接到存储帐户并检索事件和运行状况探测日志的 JSON 日志条目。 下载 JSON 文件后，可以将它们转换为 CSV 并在 Excel、Power BI 或任何其他数据可视化工具中查看。 

> [!TIP]
> 如果熟悉 Visual Studio 和更改 C# 中的常量和变量值的基本概念，则可以使用 GitHub 提供的[日志转换器工具](https://github.com/Azure-Samples/networking-dotnet-log-converter)。

## <a name="stream-to-an-event-hub"></a>流式传输到事件中心
将诊断信息流式传输到事件中心后，可以通过 Azure Monitor 集成将其用于第三方 SIEM 工具中的集中式日志分析。 有关详细信息，请参阅[将 Azure 监视数据流式传输到事件中心](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration)

## <a name="send-to-log-analytics"></a>发送到 Log Analytics
Azure 中的资源可以将其诊断信息直接发送到 Log Analytics 工作区，在此工作区中，可以针对信息运行复杂的查询以进行故障排除和分析。  有关详细信息，请参阅[在 Azure Monitor 的 Log Analytics 工作区中收集 Azure 资源日志](../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace)

## <a name="limitations"></a>限制
使用度量值来记录负载均衡器的导出功能时，当前存在以下限制：
* 作为日志导出时，当前使用内部名称显示指标。你可以在下表中找到映射
* 不保留指标的维数。 例如，使用 DipAvailability (运行状况探测状态) 的指标，将无法按后端 IP 地址进行拆分或查看
* 使用的 SNAT 端口和分配的 SNAT 端口指标当前不可用作日志导出

## <a name="next-steps"></a>后续步骤
* [查看负载均衡器的可用指标](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics)
* [按照 Azure Monitor 说明创建和测试查询](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)
* 使用下面的链接提供有关本文或负载均衡器功能的反馈
