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
ms.openlocfilehash: 07e79dbde142400677901ee02903144f9a42cd6b
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/23/2020
ms.locfileid: "97740695"
---
# <a name="visualize-data-from-azure-time-series-insights-in-power-bi"></a>在 Power BI 中可视化来自 Azure 时序见解的数据

Azure 时序见解是可在云中存储、管理、查询和可视化时序数据的平台。 [Power BI](https://powerbi.microsoft.com) 是一个业务分析工具，它提供丰富的可视化功能让你在组织中共享见解和结果。 现在，这两项服务都可以集成，使你可以通过强大的数据可视化功能和 Power BI 的轻松共享功能增强 Azure 时序见解的强大分析能力。

将了解如何执行以下操作：

* 使用本机 Azure 时序见解连接器将 Azure 时序见解连接到 Power BI
* 在 Power BI 中创建带有时序数据的视觉对象
* 将报表发布到 Power BI，然后与组织中的其他人共享


## <a name="prerequisites"></a>先决条件

* 注册一个[免费的 Azure 订阅](https://azure.microsoft.com/free/)（如果还没有）。
* 下载并安装最新版本的 [Power BI Desktop](https://powerbi.microsoft.com/downloads/)。
* 具有或创建 [Azure 时序见解 Gen2 环境](./how-to-provision-manage.md)

请查看 [环境访问策略](./concepts-access-policies.md) ，并确保对 Azure 时序见解 Gen2 环境具有直接访问权限或来宾访问权限。 

> [!IMPORTANT]
> * 下载并安装最新版本的 [Power BI Desktop](https://powerbi.microsoft.com/downloads/)。 若要按照本文中的步骤进行操作，请确保至少安装了12月 2020 (2.88.321.0) 版本的 Power BI Desktop。 

## <a name="connect-data-from-azure-time-series-insights-to-power-bi"></a>将数据从 Azure 时序见解连接到 Power BI

### <a name="1-export-data-into-power-bi-desktop"></a>1. 将数据导出到 Power BI 桌面

开始操作：

1. 打开 Azure 时序见解 Gen2 资源管理器并组织数据。 这是将导出到 Power BI 的数据。
1. 创建满意的视图后，导航到“更多操作”下拉菜单，然后选择“连接到 Power BI” 。

    [![Azure 时序见解 Gen2 资源管理器导出](media/how-to-connect-power-bi/export-from-explorer.jpg)](media/how-to-connect-power-bi/export-from-explorer.jpg#lightbox)

1. 设置导出参数：

   * **数据格式**：选择是要将 **聚合数据** 还是 **原始事件** 导出到 Power BI。 

       > [!NOTE]
       > * 如果导出原始事件，则可以稍后在 Power BI 中聚合该数据。 但是，如果您导出了聚合数据，则无法还原到 Power BI 中的原始数据。 
       > * 原始事件级别数据的事件计数限制为 250,000 个。

   * **时间范围**：选择是要查看 **固定** 时间范围还是 Power BI 中的 **最新** 数据。 选择固定时间范围意味着您已绘制的搜索范围中的数据将导出到 Power BI。 选择最新的时间范围意味着 Power BI 将获取所选搜索范围的最新数据 (例如，如果你绘制任意1小时的数据，然后选择 "最新" 设置，Power BI 连接器将始终查询最新1小时的数据。 ) 
  
   * **存储类型**：选择是否要对 " **热存储** " 或 " **冷存储**" 运行所选的查询。 

    > [!TIP]
    > * Azure 时序见解资源管理器将根据你选择导出的数据自动选择建议的参数。 

1. 配置设置后，请选择 " **将查询复制到剪贴板**"。

    [![Azure 时序见解资源管理器导出模式](media/how-to-connect-power-bi/choose-explorer-parameters.jpg)](media/how-to-connect-power-bi/choose-explorer-parameters.jpg#lightbox)

2. 启动 Power BI Desktop。
   
3. 在 Power BI Desktop 中的“主页”选项卡上，选择左上角的“获取数据”，然后选择“更多”。

    [![在 Power BI 中获取数据](media/how-to-connect-power-bi/get-data-power-bi.jpg)](media/how-to-connect-power-bi/get-data-power-bi.jpg#lightbox)

4. 搜索“Azure 时序见解”，依次选择“Azure 时序见解(Beta)”、“连接”。  

    [![将 Power BI 连接到 Azure 时序见解](media/how-to-connect-power-bi/select-tsi-connector.jpg)](media/how-to-connect-power-bi/select-tsi-connector.jpg#lightbox)

    或者，导航到“Azure”选项卡，并依次选择“Azure 时序见解(Beta)”、“连接”。

5. 将从 Azure 时序见解资源管理器复制的查询粘贴到 **自定义查询** 字段中，然后按 **"确定"**。

    [![粘贴自定义查询，然后选择 "确定"](media/how-to-connect-power-bi/custom-query-load.png)](media/how-to-connect-power-bi/custom-query-load.png#lightbox)  

6.  随即会加载数据表。 按“加载”以载入 Power BI。 如果要对数据进行任何转换，现在可以通过单击 " **转换数据**" 来执行此操作。 你还可以在加载数据后对其进行转换。

    [![查看表中的数据，并选择 "加载"](media/how-to-connect-power-bi/review-the-loaded-data-table.png)](media/how-to-connect-power-bi/review-the-loaded-data-table.png#lightbox)  

## <a name="create-a-report-with-visuals"></a>使用视觉对象创建报表

将数据导入 Power BI 后，接下来请生成包含视觉对象的报表。

1. 在窗口的左侧，确保已选择“报表”视图。

    [![屏幕截图显示“报表”视图图标。](media/how-to-connect-power-bi/select-the-report-view.png)](media/how-to-connect-power-bi/select-the-report-view.png#lightbox)

1. 在“可视化效果”列中，选择所需的视觉对象。 例如，选择“折线图”。 这会在画布中添加一个空白的折线图。

1.  在 " **字段** " 列表中，选择 " **_Timestamp** "，然后将其拖动到 " **轴** " 字段以沿 X 轴显示时间。 确保切换到“_时间戳”，作为“轴”的值（默认为“数据层次结构”）  。

    [![选择 _Timestamp](media/how-to-connect-power-bi/select-timestamp.png)](media/how-to-connect-power-bi/select-timestamp.png#lightbox)

1.  同样，在 " **字段** " 列表中，选择要绘制的变量，然后将其拖到 " **值** " 字段以沿 Y 轴显示值。 选择时序 ID 值并将其拖到 " **图例** " 字段，以便在图表中创建多个行，每个时序 id 一个行。 这会显示类似于 Azure 时序见解资源管理器提供的视图！ 

    [![创建基本折线图](media/how-to-connect-power-bi/power-bi-line-chart.png)](media/how-to-connect-power-bi/power-bi-line-chart.png#lightbox)

1. 若要将另一个图表添加到画布，请选择画布上折线图外部的任意位置，并重复上述过程。

    [![创建要共享的其他图表](media/how-to-connect-power-bi/power-bi-additional-charts.png)](media/how-to-connect-power-bi/power-bi-additional-charts.png#lightbox)

创建报表后，可以将其发布到 Power BI Reporting Services 并与组织中的其他人共享。

## <a name="advanced-editing"></a>高级编辑
如果已在 Power BI 中加载了一个数据集，但想要修改查询（例如日期/时间或环境 ID 参数），可以通过 Power BI 的高级编辑器功能实现此目的。 请参阅 [Power BI 文档](https://docs.microsoft.com/power-bi/desktop-query-overview) ，以了解有关如何使用 **Power Query 编辑器** 进行更改的详细信息。 

## <a name="next-steps"></a>后续步骤

* 详细了解 [Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-query-overview)。

* 了解如何 [查询](concepts-query-overview.md) Azure 时序见解 Gen2 中的数据。
