---
title: 可视化 Azure Monitor 中的数据 | Microsoft 文档
description: 概述了将 Azure Monitor 中存储的指标和日志数据可视化时可使用的方法。
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 07/28/2021
ms.openlocfilehash: 4a98a44cd56691947536779103f55b4e713c74df
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121724607"
---
# <a name="visualizing-data-from-azure-monitor"></a>可视化 Azure Monitor 中的数据
本文概述了将 Azure Monitor 中存储的日志和指标数据可视化时可使用的方法。

图表和图形等可视化效果有助于你分析监视数据，从而深入了解问题并识别模式。 根据使用的工具，你还可以选择与组织内外的其他用户共享可视化效果。

## <a name="workbooks"></a>工作簿
[工作簿](./visualize/workbooks-overview.md)是交互式文档，可以让用户深入了解团队内部的数据、调查和协作。 例如，工作簿在故障排查指南和事件分析方面非常有用。

![该图显示了工作簿中若干页面的屏幕截图，包括“页面视图分析”、“使用情况”和“在页面上所花费的时间”。](media/visualizations/workbook.png)

### <a name="advantages"></a>优点
- 支持指标和日志。
- 支持启用交互式报表的参数，其中，选择表中的元素将以动态方式更新关联的图表和可视化效果。
- 类似于文档的流。
- 包含个人或共享工作簿的选项。
- 轻松、易协作的创作体验。
- 模板支持基于 GitHub 的公共模板库。


## <a name="azure-dashboards"></a>Azure 仪表板
[Azure 仪表板](../azure-portal/azure-portal-dashboards.md)是 Azure 的主要仪表板技术。 这种在 Azure 基础结构和服务上提供的单一管理平台，在实现快速识别重要问题方面尤其有用。

![屏幕截图显示了包含可自定义信息的 Azure 仪表板的示例。](media/visualizations/dashboard.png)

下面是有关如何创建仪表板的视频演练。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4AslH]

### <a name="advantages"></a>优点
- 深度集成到 Azure 中。 可视化效果可以固定到多个 Azure 页的仪表板，包括[指标资源管理器](essentials/metrics-charts.md)、[Log Analytics](logs/log-analytics-overview.md) 和 [Application Insights](app/app-insights-overview.md)。
- 支持指标和日志。
- 合并来自多个源的数据，包括[指标资源管理器](essentials/metrics-charts.md)、[日志查询](logs/log-query-overview.md)以及 [Application Insights](app/app-insights-overview.md) 中的[映射](app/app-map.md)和可用性的输出。
- 包含个人或共享仪表板的选项。 与 [Azure 基于角色的访问控制 (Azure RBAC)](../role-based-access-control/overview.md) 集成。
- 自动刷新。 指标刷新的时间范围至少为 5 分钟。 日志每小时刷新一次，同时可根据需要使用手动刷新选项，方法是单击给定可视化效果上的“刷新”图标或刷新整个仪表板。
- 带有时间戳和自定义参数的参数化指标仪表板。
- 灵活的布局选项。
- 全屏模式。


### <a name="limitations"></a>限制
- 对日志可视化效果具有有限控制，不支持数据表。 数据系列总数限制为 50，其他数据系列在“其他” Bucket 下进行分组。
- 对于日志图表，不支持自定义参数。
- 日志图表仅限于过去 30 天。
- 日志图表只能固定到共享仪表板。
- 没有与仪表板数据的交互操作。
- 上下文向下钻取受限。


## <a name="power-bi"></a>Power BI
[Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) 对于创建以业务为中心的仪表板和报表，以及用于分析长期 KPI 趋势的报表特别有用。 你可以[将日志查询结果导入 Power BI 数据集](visualize/powerbi.md)，以便利用其各项功能，例如合并不同来源的数据和在 Web 和移动设备上共享报表。

![Power BI](media/visualizations/power-bi.png)

### <a name="advantages"></a>优点
- 丰富的可视化效果。
- 广泛的交互性，包括放大和交叉筛选。
- 易于在整个组织内共享。
- 与来自多个数据源的其他数据集成。
- 结果缓存在多维数据集中，性能更佳。


### <a name="limitations"></a>限制
- 支持日志，但不支持指标。
- 无 Azure 集成。 无法通过 Azure 资源管理器管理仪表板和模型。
- 需要将查询结果导入到 Power BI 模型中进行配置。 结果大小和刷新存在限制。
- 每天的数据刷新次数限制为 8 次。


## <a name="grafana"></a>Grafana
[Grafana](https://grafana.com/) 是一个适用于操作仪表板的开放平台。 它尤其适用于检测、隔离和归类操作事件。 你可以将 [Grafana Azure Monitor 数据源插件](visualize/grafana-plugin.md)添加到 Azure 订阅中，使其可视化 Azure 指标数据。

![显示 Grafana 可视化效果的屏幕截图。](media/visualizations/grafana.png)

> [!IMPORTANT]
> Internet Explorer 浏览器和旧版 Microsoft Edge 浏览器与 Grafana 不兼容，必须使用基于 chromium 的浏览器，包括 Microsoft Edge。 请参阅 [Grafana 支持的浏览器](https://grafana.com/docs/grafana/latest/installation/requirements/#supported-web-browsers)。

### <a name="advantages"></a>优点
- 丰富的可视化效果。
- 丰富的数据源生态系统。
- 数据交互功能，包括放大。
- 支持参数。

### <a name="limitations"></a>限制
- 无 Azure 集成。 无法通过 Azure 资源管理器管理仪表板和模型。
- 支持额外 Grafana 基础结构的成本或用于 Grafana Cloud 的额外成本。

## <a name="azure-monitor-partners"></a>Azure Monitor 合作伙伴
某些 [Azure Monitor 合作伙伴](./partners.md)可能会提供可视化功能。 上一链接列出了 Microsoft 评估的合作伙伴。 

### <a name="advantages"></a>优点
- 可能会提供现成的可视化效果，节省时间

### <a name="limitations"></a>限制
- 可能会产生额外成本
- 研究和评估合作伙伴产品/服务的时间

## <a name="build-your-own-custom-application"></a>生成自己的自定义应用程序
你可以使用任何 REST 客户端通过其 API 访问 Azure Monitor 中的日志和指标数据，从而可以生成自己的自定义网站和应用程序。

### <a name="advantages"></a>优点
- 在 UI、可视化效果、交互性和功能方面具有完全的灵活性。
- 可将指标和日志数据与其他数据源相结合。

### <a name="disadvantages"></a>缺点
- 所需的工程工作量明显较大。

## <a name="azure-monitor-views"></a>Azure Monitor 视图

> [!IMPORTANT]
> 视图即将弃用。 有关将视图转换为工作簿的指导，请参阅 [Azure Monitor 视图设计器转换为工作簿的指南](visualize/view-designer-conversion-overview.md)。

借助 [Azure Monitor 中的视图](visualize/view-designer.md)，可以使用日志数据创建自定义可视化效果。 它们由[监视解决方案](insights/solutions.md)用于呈现所收集的数据。


![屏幕截图显示了容器监视解决方案磁贴，以及在选择磁贴时打开的详细 Azure Monitor 视图。](media/visualizations/view.png)

### <a name="advantages"></a>优点
- 为日志数据提供了丰富的可视化效果。
- 导出和导入视图可以将其传输到其他资源组和订阅。
- 通过工作区和监视解决方案集成到 Azure Monitor 管理模型中。
- 用于自定义参数的[筛选器](visualize/view-designer-filters.md)。
- 具有交互性，支持多级钻取（一个视图钻取到另一个视图）

### <a name="limitations"></a>限制
- 支持日志，但不支持指标。
- 无个人视图。 适用于有权限访问工作区的所有用户。
- 无自动刷新。
- 布局选项有限。
- 不支持跨多个工作区或 Application Insights 应用程序进行查询。
- 查询的响应大小限制为 8MB，查询的执行时间限制为 110 秒。

## <a name="next-steps"></a>后续步骤
- 了解[由 Azure Monitor 收集的数据](data-platform.md)。
- 了解 [Azure 仪表板](../azure-portal/azure-portal-dashboards.md)。
- 了解[指标资源管理器](essentials/metrics-getting-started.md)
- 了解[工作簿](./visualize/workbooks-overview.md)。
- 了解如何[将日志数据导入 Power BI](./visualize/powerbi.md)。
- 了解 [Grafana Azure Monitor 数据源插件](./visualize/grafana-plugin.md)。
- 了解 [Azure Monitor 中的视图](visualize/view-designer.md)。