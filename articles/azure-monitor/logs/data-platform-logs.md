---
title: Azure Monitor 日志
description: 了解 Azure Monitor 日志的基础知识，这些日志用于对监视数据进行高级分析。
documentationcenter: ''
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 10/22/2020
ms.author: bwren
ms.openlocfilehash: 616d1f5cf4fc2806d56c34335e66418452c7684b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121735793"
---
# <a name="azure-monitor-logs-overview"></a>Azure Monitor 日志概览
Azure Monitor 日志是 Azure Monitor 的一项功能，用于从[受监视的资源](../monitor-reference.md)收集并整理日志和性能数据。 来自多个源的数据可以合并到单个工作区中。 这些源包括：

- 来自 Azure 服务的[平台日志](../essentials/platform-logs-overview.md)。
- 来自[虚拟机代理](../agents/agents-overview.md)的日志和性能数据。
- 来自[应用程序](../app/app-insights-overview.md)的使用情况和性能数据。 

然后可以使用能够快速分析数百万条记录的复杂查询语言来分析数据。 

你可以执行检索特定记录集的简单查询，也可以执行复杂的数据分析来识别监视数据中的关键模式。 通过 Log Analytics 以交互方式使用日志查询及其结果，在警报规则中使用它们来主动接收问题通知，或者在工作簿或仪表板中直观显示其结果。

> [!NOTE]
> Azure Monitor 日志是支持 Azure Monitor 的数据平台的一半。 另一半则是将数值数据存储在时序数据库中的 [Azure Monitor 指标](../essentials/data-platform-metrics.md)。 数值数据比 Azure Monitor 日志中的数据更轻量。 Azure Monitor 指标可以支持准实时场景，因此有助于发出问题警报和快速检测问题。 
>
> Azure Monitor 指标只能存储特定结构的数值数据，而 Azure Monitor 日志可以存储具有自己结构的各种数据类型。 你还可使用日志查询对 Azure Monitor 日志数据执行复杂的分析，只是无法分析 Azure Monitor 指标数据。

## <a name="what-can-you-do-with-azure-monitor-logs"></a>可以对 Azure Monitor 日志执行哪些操作？
下表介绍了 Azure Monitor 日志的部分使用方式：

|  | 说明 |
|:---|:---|
| **分析** | 使用 Azure 门户中的 [Log Analytics](./log-analytics-tutorial.md) 编写[日志查询](./log-query-overview.md)，并使用功能强大的分析引擎以交互方式分析日志数据。 |
| **Alert** | 配置[日志警报规则](../alerts/alerts-log.md)，以便在查询结果与特定的结果匹配时发送通知或执行[自动化操作](../alerts/action-groups.md)。 |
| **可视化** | 将以表格或图表形式呈现的查询结果固定到 [Azure 仪表板](../../azure-portal/azure-portal-dashboards.md)。<br>创建一个[工作簿](../visualize/workbooks-overview.md)以与交互式报表中的多组数据合并。 <br>将查询结果导出到 [Power BI](../visualize/powerbi.md)，以使用不同的可视化效果并与 Azure 外部的用户共享。<br>将查询结果导出到 [Grafana](../visualize/grafana-plugin.md) 以使用其仪表板并与其他数据源合并。|
| **获取见解** | 支持为特定应用程序和服务提供自定义监视体验的[见解](../monitor-reference.md#insights-and-core-solutions)。  |
| **检索** | 使用 [Azure CLI](/cli/azure/monitor/log-analytics) 从命令行访问日志查询结果。<br>使用 [PowerShell cmdlet](/powershell/module/az.operationalinsights) 从命令行访问日志查询结果。<br>使用 [REST API](https://dev.loganalytics.io/) 从自定义应用程序访问日志查询结果。 |
| **导出** | 配置[自动导出](./logs-data-export.md)，将日志数据自动导出到 Azure 存储帐户或 Azure 事件中心。<br>构建一个工作流来检索日志数据，并使用 [Azure 逻辑应用](./logicapp-flow-connector.md)将其复制到外部位置。 |

![显示 Azure Monitor 日志概述的关系图。](media/data-platform-logs/logs-overview.png)

## <a name="data-collection"></a>数据收集
创建 Log Analytics 工作区后，必须配置源以发送其数据。 系统不自动收集任何数据。 

此配置将因数据源而异。 例如：

- [创建诊断设置](../essentials/diagnostic-settings.md)，将资源日志从 Azure 资源发送到工作区。 
- [启用 VM 见解](../vm/vminsights-enable-overview.md)以从虚拟机收集数据。 
- [在工作区上配置数据源](../agents/data-sources.md)以收集更多事件和性能数据。

如需了解可配置为向 Azure Monitor 日志发送数据的数据源的完整列表，请参阅 [Azure Monitor 监视哪些内容？](../monitor-reference.md)。

## <a name="log-analytics-and-workspaces"></a>Log Analytics 和工作区
Log Analytics 是 Azure 门户中的一种工具。 可用于编辑和运行日志查询并以交互方式分析其结果。 然后，你可以使用这些查询来支持 Azure Monitor 中的其他功能，例如日志查询警报和工作簿。 通过 Azure Monitor 菜单中的“日志”选项或者从 Azure 门户中的其他大部分服务访问 Log Analytics。

有关 Log Analytics 的说明，请参阅 [Azure Monitor 中的 Log Analytics 概述](./log-analytics-overview.md)。 若要演练如何使用 Log Analytics 功能创建简单的日志查询并分析结果，请参阅 [Log Analytics 教程](./log-analytics-tutorial.md)。

Azure Monitor 日志将收集的数据存储在一个或多个 [Log Analytics 工作区](./design-logs-deployment.md)中。 工作区定义：

- 数据的地理位置。
- 定义哪些用户可以访问数据的访问权限。
- 配置设置，例如定价层和数据保留。  

必须创建至少一个工作区才能使用 Azure Monitor 日志。 一个工作区对所有监视数据可能已经足够，也可以根据需要选择创建多个工作区。 例如，你可以对生产数据使用一个工作区，对测试数据使用另一个工作区。 

若要创建新工作区，请参阅[在 Azure 门户中创建 Log Analytics 工作区](./quick-create-workspace.md)。 有关创建多个工作区的注意事项，请参阅[设计 Azure Monitor 日志部署](design-logs-deployment.md)。

## <a name="log-queries"></a>日志查询
可通过日志查询从 Log Analytics 工作区检索数据，该查询是对处理数据和返回结果的只读请求。 日志查询以 [Kusto 查询语言 (KQL)](/azure/data-explorer/kusto/query/) 编写。 KQL 也是 Azure 数据资源管理器所使用的查询语言。 

你可以在 Log Analytics 中编写日志查询，以交互方式分析其结果，在警报规则中使用它们来主动接收问题通知，或将其结果包含在工作簿或仪表板中。 Insights 包括预生成的查询，以支持其视图和工作簿。

有关使用日志查询的区域列表以及教程和其他入门文档的参考资料，请参阅 [Azure Monitor 中的日志查询](./log-query-overview.md)。

![显示 Log Analytics 中的查询的屏幕截图。](media/data-platform-logs/log-analytics.png)

## <a name="data-structure"></a>数据结构
日志查询从 Log Analytics 工作区检索其数据。 每个工作区都包含多个表，这些表被组织成具有多行数据的单独列。 每个表由一组唯一的列定义。 数据源提供的数据行共享这些列。 

[![显示 Azure Monitor 日志结构的关系图。](media/data-platform-logs/logs-structure.png)](media/data-platform-logs/logs-structure.png#lightbox)

来自 Application Insights 的日志数据也存储在 Azure Monitor 日志中，但根据应用程序的配置方式不同，存储的方式也不同： 

- 对于基于工作区的应用程序，数据存储在 Log Analytics 工作区中的一组标准表中。 数据类型包括应用程序请求、异常和页面视图。 多个应用程序可以使用同一个工作区。 

- 对于经典应用程序，数据不会存储在 Log Analytics 工作区中。 它使用相同的查询语言，并且你可以在 Azure 门户中使用相同的 Log Analytics 工具创建和运行查询。 经典应用程序的数据项是彼此分开存储的。 尽管表名和列名不同，但其常规结构与基于工作区的应用程序相同。 

有关基于工作区的应用程序和经典应用程序的架构的详细比较，请参阅[基于工作区的资源更改](../app/apm-tables.md)。

> [!NOTE]
> 经典 Application Insights 体验为资源查询、工作簿和基于日志的警报提供向后兼容性。 若要根据[新的基于工作区的表结构/架构](../app/apm-tables.md)进行查询或查看，必须先转到 Log Analytics 工作区。 在预览版期间，从 Application Insights 窗格中选择“日志”即可访问经典 Application Insights 查询体验。 有关详细信息，请参阅[查询范围](./scope.md)。

[![显示 Application Insights 的 Azure Monitor 日志结构的关系图。](media/data-platform-logs/logs-structure-ai.png)](media/data-platform-logs/logs-structure-ai.png#lightbox)

## <a name="relationship-to-azure-data-explorer"></a>与 Azure 数据资源管理器的关系
Azure Monitor 日志基于 Azure 数据资源管理器。 Log Analytics 工作区大致等效于 Azure 数据资源管理器中的数据库。 表的结构相同，两者均使用 KQL。 

在 Azure 门户中使用 Log Analytics 来处理 Azure Monitor 查询的体验与使用 Azure 数据资源管理器 Web UI 的体验类似。 你甚至可以[将来自 Log Analytics 工作区的数据包含在 Azure 数据资源管理器查询中](/azure/data-explorer/query-monitor-data)。 

## <a name="next-steps"></a>后续步骤

- 了解[日志查询](./log-query-overview.md)，以从 Log Analytics 工作区检索和分析数据。
- 了解 [Azure Monitor 中的指标](../essentials/data-platform-metrics.md)。
- 了解适用于 Azure 中各种资源的[监视数据](../agents/data-sources.md)。
