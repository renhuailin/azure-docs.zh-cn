---
title: 可视化 Azure Monitor 中的数据 | Microsoft Docs
description: 获取可用于将 Azure Monitor 中存储的指标和日志数据可视化的方法的摘要。
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 07/28/2021
ms.openlocfilehash: 257ca33952283ba3989ce11a2d933af3feceb8ac
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129612344"
---
# <a name="visualize-data-from-azure-monitor"></a>可视化 Azure Monitor 中的数据
本文汇总了可用于将 Azure Monitor 中存储的指标和日志数据可视化的方法。

图表和图形等可视化效果有助于你分析监视数据，从而深入了解问题并识别模式。 根据使用的工具，你可能还可以选择与组织内外的其他用户共享可视化效果。

## <a name="workbooks"></a>工作簿
[工作簿](./visualize/workbooks-overview.md)是交互式文档，可以让用户深入了解团队内部的数据、调查和协作。 工作簿在提供故障排查指导和事后分析方面特别有用。

![该图显示了工作簿中三个页面的屏幕截图，包括“页面视图分析”、“使用情况”和“在页面上所花费的时间”。](media/visualizations/workbook.png)

工作簿的优点包括：

- 对指标和日志的支持。
- 对用于启用交互式报表的参数的支持。在这些报表中，选择表中的元素将以动态方式更新关联的图表和可视化效果。
- 类似于文档的流。
- 个人或共享工作簿的选项。
- 易用的协作式创作体验。
- 支持基于 GitHub 的公共模板库的模板。


## <a name="azure-dashboards"></a>Azure 仪表板
[Azure 仪表板](../azure-portal/azure-portal-dashboards.md)是 Azure 的主要仪表板技术。 仪表在提供 Azure 基础结构和服务的单一管理平台方面非常有用，使你可以快速识别重要问题。

![该屏幕截图显示包含可自定义信息的 Azure 仪表板示例。](media/visualizations/dashboard.png)

下面是有关如何创建仪表板的视频演练：

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4AslH]

仪表板的优点包括：

- 深度集成到 Azure 中。 可以从多个 Azure 页面（包括[指标资源管理器](essentials/metrics-charts.md)、[Log Analytics](logs/log-analytics-overview.md) 和 [Application Insights](app/app-insights-overview.md)）将可视化效果固定到仪表板。
- 对指标和日志的支持。
- 能够合并来自多个源（包括[指标资源管理器](essentials/metrics-charts.md)、[日志查询](logs/log-query-overview.md)和[映射](app/app-map.md)的输出以及 [Application Insights](app/app-insights-overview.md) 中的可用性）的数据。
- 通过与 [Azure 基于角色的访问控制 (Azure RBAC)](../role-based-access-control/overview.md) 的集成提供个人或共享仪表板的选项。
- 自动刷新。 指标刷新频率取决于时间范围（最小值为 5 分钟）。 日志每小时刷新一次。 可以按需手动刷新，方法是选择可视化效果上的“刷新”图标，或刷新整个仪表板。
- 带有时间戳和自定义参数的参数化指标仪表板。
- 灵活的布局选项。
- 全屏模式。


限制包括：

- 对日志可视化效果具有有限控制，不支持数据表。 数据系列总数限制为 50，其他数据系列在其他桶下进行分组。
- 对于日志图表，不支持自定义参数。
- 日志图表的时间段有限制（过去 30 天）。
- 必须将日志图表固定到共享仪表板。
- 没有与仪表板数据的交互操作。
- 上下文向下钻取受限。

## <a name="grafana"></a>Grafana
[Grafana](https://grafana.com/) 是一个开放平台，它在操作仪表板和可视化性能以及可用性数据方面非常出色。 它在检测、隔离和归类操作事件方面很有用。 Grafana 包含 Azure Monitor 的内置支持，并包含现成的 [Azure Monitor 插件](https://grafana.com/docs/grafana/latest/datasources/azuremonitor/)。 它支持对三项 Azure 服务中的数据进行可视化：
- Azure Monitor 指标中来自 Azure 资源中数据的数值时序数据。 
- Azure Monitor 日志中来自 Azure 资源的日志和性能数据，使你能够使用强大的 Kusto 查询语言 (KQL) 进行查询。
- Azure Resource Graph，以便跨订阅快速查询和识别 Azure 资源。

Grafana 适用于各种数据源，并允许创建仪表板和面板，将 Azure、本地或其他云中资源的监视数据相结合。 

![显示 Grafana 可视化效果的屏幕截图。](media/visualizations/grafana.png)

> [!IMPORTANT]
> Internet Explorer 11 仅在 Grafana v6.0 之前的版本中完全受支持。请参阅 [Grafana 支持的浏览器](https://grafana.com/docs/grafana/latest/installation/requirements/#supported-web-browsers)。

Grafana 的优点包括：

- 丰富的可视化效果。
- 丰富的数据源生态系统，包括对非 Azure 数据源的支持。
- 数据交互功能，包括放大。
- 允许将事件数据集成到关系图中的注释。
- 对参数的支持，包括 [Azure Monitor 变量](https://grafana.com/docs/grafana/latest/datasources/azuremonitor/template-variables/)。

限制包括：

- 无法通过 Azure 资源管理器管理仪表板和模型。
- 用于支持额外 Grafana 基础结构的成本或用于 Grafana Cloud 的额外成本。
## <a name="power-bi"></a>Power BI
[Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) 对于创建以业务为中心的仪表板和报表，以及创建用于分析长期 KPI 趋势的报表非常有用。 可以[将日志查询结果导入](visualize/powerbi.md)到 Power BI 数据集，然后利用其各项功能，例如合并不同来源的数据和在 Web 和移动设备上共享报表。

![该屏幕截图显示针对 IT 操作提供的示例 Power BI 报表。](media/visualizations/power-bi.png)

Power BI 的优点包括：

- 丰富的可视化效果。
- 广泛的交互性，包括放大和交叉筛选。
- 在整个组织中轻松共享。
- 与来自多个数据源的其他数据集成。
- 结果缓存在多维数据集中，性能更佳。

限制包括：

- 支持日志，但不支持指标。
- 无 Azure 集成。 无法通过 Azure 资源管理器管理仪表板和模型。
- 需要将查询结果导入到 Power BI 模型中进行配置。 
- 结果大小和刷新有限制。
- 数据刷新次数有限制（每天 8 次）。


## <a name="azure-monitor-partners"></a>Azure Monitor 合作伙伴
某些 Azure Monitor 合作伙伴提供可视化功能。 有关 Microsoft 已评估的合作伙伴列表，请参阅 [Azure Monitor 合作伙伴集成](./partners.md)。 

Azure Monitor 合作伙伴可以提供现成的可视化效果来节省你的时间。 

限制包括：

- 可能会产生额外的成本。
- 需要花费时间来研究和评估合作伙伴产品/服务。

## <a name="your-own-custom-application"></a>你自己的自定义应用程序
可以使用任何 REST 客户端通过 API 访问 Azure Monitor 中的指标和日志数据。 然后，可以生成自己的自定义网站和应用程序。

生成自定义应用程序的优点包括：

- 在 UI、可视化效果、交互性和功能方面具有完全的灵活性。
- 能够将指标和日志数据与其他数据源相结合。

一个重大的缺点是需要完成工程方面的工作。

## <a name="azure-monitor-views"></a>Azure Monitor 视图

> [!IMPORTANT]
> 视图即将弃用。 有关将视图转换为工作簿的指导，请参阅[转换指南](visualize/view-designer-conversion-overview.md)。

借助 [Azure Monitor 中的视图](visualize/view-designer.md)，可以使用日志数据创建自定义可视化效果。 [监视解决方案](insights/solutions.md)使用视图来呈现它们收集的数据。


![该屏幕截图显示了“容器监视解决方案”磁贴，以及在选择此磁贴时打开的详细 Azure Monitor 视图。](media/visualizations/view.png)

视图的优点包括：

- 为日志数据提供了丰富的可视化效果。
- 能够导出和导入视图，以将其传输到其他资源组和订阅。
- 通过工作区和监视解决方案集成到 Azure Monitor 管理模型中。
- 用于自定义参数的[筛选器](visualize/view-designer-filters.md)。
- 交互性，支持多级钻取（可从一个视图钻取到另一个视图）。

限制包括：

- 支持日志，但不支持指标。
- 无个人视图。 视图适用于有权访问工作区的所有用户。
- 无自动刷新。
- 布局选项有限。
- 不支持跨多个工作区或 Application Insights 应用程序进行查询。
- 查询响应大小限制为 8 MB，查询执行时间限制为 110 秒。

## <a name="next-steps"></a>后续步骤
- 了解 [Azure Monitor 收集的数据](data-platform.md)。
- 了解 [Azure 仪表板](../azure-portal/azure-portal-dashboards.md)。
- 了解[指标资源管理器](essentials/metrics-getting-started.md)。
- 了解[工作簿](./visualize/workbooks-overview.md)。
- 了解如何[将日志数据导入 Power BI](./visualize/powerbi.md)。
- 了解[适用于 Grafana 的 Azure Monitor 数据源插件](./visualize/grafana-plugin.md)。
- 了解 [Azure Monitor 中的视图](visualize/view-designer.md)。
