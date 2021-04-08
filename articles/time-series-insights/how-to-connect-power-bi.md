---
title: 将环境连接到 Power BI - Azure 时序见解 | Microsoft Docs
description: 了解如何将 Azure 时序见解连接到 Power BI，以便在整个组织中共享、绘制图表和显示数据。
author: shreyasharmamsft
ms.author: shresha
manager: dpalled
services: time-series-insights
ms.service: time-series-insights
ms.topic: conceptual
ms.date: 12/14/2020
ms.openlocfilehash: 38403eed56dc718afdfce13375dd2662beb13eb6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "100374161"
---
# <a name="visualize-data-from-azure-time-series-insights-in-power-bi"></a>在 Power BI 中可视化来自 Azure 时序见解的数据

Azure 时序见解是可在云中存储、管理、查询和可视化时序数据的平台。 [Power BI](https://powerbi.microsoft.com) 是一个业务分析工具，它提供丰富的可视化功能让你在组织中共享见解和结果。 现在，这两项服务都可以集成，使你可以通过 Power BI 的强大数据可视化和轻松共享功能来增强 Azure 时序见解的强大分析能力。

将了解如何执行以下操作：

* 使用本机 Azure 时序见解连接器将 Azure 时序见解连接到 Power BI
* 使用 Power BI 中的时序数据创建视觉对象
* 将报表发布到 Power BI，然后与组织中的其他人共享


## <a name="prerequisites"></a>先决条件

* 注册一个[免费的 Azure 订阅](https://azure.microsoft.com/free/)（如果还没有）。
* 下载并安装最新版本的 [Power BI Desktop](https://powerbi.microsoft.com/downloads/)。
* 具有或创建 [Azure 时序见解 Gen2 环境](./how-to-provision-manage.md)

请查看[环境访问策略](./concepts-access-policies.md)，并确保你具有对 Azure 时序见解 Gen2 环境进行直接访问或来宾访问的权限。 

> [!IMPORTANT]
> * 下载并安装最新版本的 [Power BI Desktop](https://powerbi.microsoft.com/downloads/)。 若要按照本文中的步骤进行操作，请确保至少安装了 2020 年 12 月 (2.88.321.0) 版本的 Power BI Desktop。 

## <a name="export-data-from-azure-time-series-insights-into-power-bi-desktop"></a>将数据从 Azure 时序见解导出至 Power BI Desktop 中

开始操作：

1. 打开 Azure 时序见解第 2 代资源管理器并策展数据。 这是将导出到 Power BI 的数据。
1. 创建满意的视图后，导航到“更多操作”下拉菜单，然后选择“连接到 Power BI” 。

    [![Azure 时序见解第 2 代资源管理器导出](media/how-to-connect-power-bi/export-from-explorer.jpg)](media/how-to-connect-power-bi/export-from-explorer.jpg#lightbox)

1. 设置导出参数：

   * **数据格式**：选择是要将“聚合数据”还是“原始事件”导出到 Power BI 。 

       > [!NOTE]
       > 如果导出原始事件，则可以稍后在 Power BI 中聚合该数据。 但是，如果导出聚合数据，则无法在 Power BI 中还原到原始数据。 原始事件级别数据的事件计数限制为 250,000 个。

   * **时间范围**：选择要在 Power BI 中查看“固定”时间范围数据还是“最新”时间范围数据 。 选择固定时间范围意味着你所绘制的搜索范围内的数据将导出到 Power BI。 选择最新时间范围意味着 Power BI 将获取所选搜索范围内的最新数据（例如，如果你绘制任意 1 小时的数据并选择“最新”设置，则 Power BI 连接器将始终查询最新 1 小时的数据。）
  
   * **存储类型**：选择是要对“暖存储”还是“冷存储”运行所选查询 。 如果选择范围同时跨越冷存储和暖存储，则默认情况下会将查询路由到冷存储，因为暖存储只包含最新的数据。 可以手动更改 storeType 参数，但建议采用默认设置以获得最佳体验。 

    > [!TIP] 
    > Azure 时序见解资源管理器将根据搜索范围以及你选择导出的数据视图自动选择建议的参数。 

1. 配置设置后，选择“将查询复制到剪贴板”。

    [![Azure 时序见解资源管理器导出模式](media/how-to-connect-power-bi/choose-explorer-parameters.jpg)](media/how-to-connect-power-bi/choose-explorer-parameters.jpg#lightbox)

1. 启动 Power BI Desktop。
   
1. 在 Power BI Desktop 中的“主页”选项卡上，选择左上角的“获取数据”，然后选择“更多”。

    [![在 Power BI 中获取数据](media/how-to-connect-power-bi/get-data-power-bi.jpg)](media/how-to-connect-power-bi/get-data-power-bi.jpg#lightbox)

1. 搜索“Azure 时序见解”，依次选择“Azure 时序见解(Beta)”、“连接”。  

    [![将 Power BI 连接到 Azure 时序见解](media/how-to-connect-power-bi/select-tsi-connector.jpg)](media/how-to-connect-power-bi/select-tsi-connector.jpg#lightbox)

    或者，导航到“Azure”选项卡，并依次选择“Azure 时序见解(Beta)”、“连接”。

1. 将从 Azure 时序见解资源管理器复制的查询粘贴到“自定义查询”字段中，然后按“确定” 。

    [![粘贴自定义查询并选择“确定”](media/how-to-connect-power-bi/custom-query-load.png)](media/how-to-connect-power-bi/custom-query-load.png#lightbox)  

1.  随即会加载数据表。 按“加载”以载入 Power BI。 如果要对数据进行任何转换，现在可以通过单击“转换数据”来执行此操作。 还可以在加载数据后对其进行转换。

    [![查看表中的数据并选择“加载”](media/how-to-connect-power-bi/review-the-loaded-data-table.png)](media/how-to-connect-power-bi/review-the-loaded-data-table.png#lightbox)  

## <a name="create-a-report-with-visuals"></a>使用视觉对象创建报表

将数据导入 Power BI 后，接下来请生成包含视觉对象的报表。

1. 在窗口的左侧，确保已选择“报表”视图。

    [![屏幕截图显示“报表”视图图标。](media/how-to-connect-power-bi/select-the-report-view.png)](media/how-to-connect-power-bi/select-the-report-view.png#lightbox)

1. 在“可视化效果”列中，选择所需的视觉对象。 例如，选择“折线图”。 这会在画布中添加一个空白的折线图。

1.  在“字段”列表中选择“时间戳(_T)”，然后将此时间戳拖放到“轴”字段以显示 X 轴上的时间  。 确保切换到“_时间戳”，作为“轴”的值（默认为“数据层次结构”）  。

    [![选择”时间戳(_T)”](media/how-to-connect-power-bi/select-timestamp.png)](media/how-to-connect-power-bi/select-timestamp.png#lightbox)

1.  同样，在“字段”列表中选择要绘制的变量，然后将此变量拖放到“值”字段以显示 Y 轴上的值 。 选择时序 ID 值并将其拖放到“图例”字段，以在图表中创建多行，每个时序 ID 一行。 呈现结果将类似于 Azure 时序见解资源管理器提供的视图！ 

    [![创建基本折线图](media/how-to-connect-power-bi/power-bi-line-chart.png)](media/how-to-connect-power-bi/power-bi-line-chart.png#lightbox)

1. 若要将另一个图表添加到画布，请选择画布上折线图外部的任意位置，并重复上述过程。

    [![创建要共享的其他图表](media/how-to-connect-power-bi/power-bi-additional-charts.png)](media/how-to-connect-power-bi/power-bi-additional-charts.png#lightbox)

创建报表后，可将其发布到 Power BI Reporting Services 并与组织中的其他人共享。

## <a name="advanced-editing"></a>高级编辑
如果已在 Power BI 中加载了一个数据集，但想要修改查询（例如日期/时间或环境 ID 参数），可以通过 Power BI 的高级编辑器功能实现此目的。 请参阅 [Power BI 文档](/power-bi/desktop-query-overview)，了解有关如何使用 Power Query 编辑器进行更改的详细信息。 

## <a name="next-steps"></a>后续步骤

* 详细了解 [Power BI Desktop](/power-bi/desktop-query-overview)。

* 了解 Azure 时序见解第 2 代中的[查询数据](concepts-query-overview.md)。