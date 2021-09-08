---
title: Azure Maps Power BI 视觉对象入门 | Microsoft Azure Maps
description: 在本文中，你将了解如何使用 Microsoft Azure Maps Power BI 视觉对象。
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 9dda6433f5f16ec71b086f606927d9f27c83098b
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123430461"
---
# <a name="getting-started-with-the-azure-maps-power-bi-visual"></a>Azure Maps Power BI 视觉对象入门

<Token>适用于：![绿色复选标记。](media/power-bi-visual/yes.png) 面向使用者的 Power BI 服务![绿色复选标记。](media/power-bi-visual/yes.png) 面向设计者和开发人员的 Power BI 服务![绿色复选标记。](media/power-bi-visual/yes.png) Power BI Desktop ![X 表示不适用。](media/power-bi-visual/no.png) 需要专业版或高级版许可证</Token>

本文介绍如何使用 Microsoft Azure Maps Power BI 视觉对象。

> [!NOTE]
> 可以在 Power BI Desktop 和 Power BI 服务中创建以及查看这种视觉对象。 本文中的步骤和图示来自 Power BI Desktop。

Power BI 的 Azure Maps 视觉对象提供了一组丰富的数据可视化效果，适用于地图上的空间数据。 估计超过 80% 的业务数据具有位置上下文。 Azure Maps 视觉对象可用于深入了解此位置上下文与业务数据有何关系以及对业务数据有何影响。

![具有显示业务数据的 Azure Maps 视觉对象的 Power BI 桌面](media/power-bi-visual/azure-maps-visual-hero.png)

## <a name="what-is-sent-to-azure"></a>发送到 Azure 的数据

Azure Maps 视觉对象连接到在 Azure 中托管的云服务，以检索位置数据，例如用于创建地图可视化效果的地图图像和坐标。

- 有关地图所关注区域的详细信息将发送到 Azure，以检索呈现地图画布（也称为地图图块）所需的图像。
- “位置”、“纬度”和“经度”Bucket 中的数据可能会发送到 Azure，以检索地图坐标（此过程称为地理编码）。
- 如果在 Power BI 中启用了遥测选项，则可能会收集有关视觉对象运行状况的遥测数据（例如，崩溃报告）。

除了上述方案以外，不会将叠加在地图上的任何其他数据发送到 Azure Maps 服务器。 所有数据呈现都在客户端本地进行。

你或你的管理员可能需要更新防火墙，以允许访问使用以下 URL 的 Azure Maps 平台。

> `https://atlas.microsoft.com`

若要详细了解与 Azure Maps 视觉对象相关的隐私策略和使用条款，请参阅 [Microsoft Azure 法律信息](https://azure.microsoft.com/support/legal/)。

## <a name="azure-maps-visual-preview-behavior-and-requirements"></a>Azure Maps 视觉对象（预览版）的行为和要求

Azure Maps 视觉对象有一些注意事项和要求。 :

- 必须在 Power BI Desktop 中启用 Azure Maps 视觉对象（预览版）。 若要启用 Azure Maps 视觉对象，请选择“文件”&gt;“选项和设置”&gt;“选项”&gt;“预览版功能”，然后选中“Azure Maps 视觉对象”复选框。      如果在启用此设置后 Azure Maps 视觉对象不可用，则可能需要在管理门户中启用租户管理员切换。
- 数据集中的字段必须包含纬度和经度信息。 

## <a name="use-the-azure-maps-visual-preview"></a>使用 Azure Maps 视觉对象（预览版）

启用 Azure Maps 视觉对象后，从“可视化效果”窗格中选择“Azure 地图”图标。  

![“可视化效果”窗格中的“Azure Maps 视觉对象”按钮](media/power-bi-visual/azure-maps-in-visualizations-pane.png)

Power BI 将创建一个空的 Azure Maps 视觉对象设计画布。 在预览版中会显示另一个免责声明。

![在初始状态下加载了 Azure Maps 视觉对象的 Power BI Desktop](media/power-bi-visual/visual-initial-load.png)

执行以下步骤以加载 Azure Maps 视觉对象：

1. 在“字段”窗格中，将包含纬度和经度坐标信息的数据字段拖放到“纬度”和/或“经度”Bucket 中。   这是加载 Azure Maps 视觉对象所需的最少数据。

    > [!div class="mx-imgBorder"]
    > ![提供“纬度”字段和“经度”字段后在地图上将点显示为气泡的 Azure Maps 视觉对象](media/power-bi-visual/bubble-layer.png)

2. 若要基于分类为数据着色，请将分类字段拖放到“字段”窗格的“图例”Bucket 中。  在此示例中，我们使用“行政区域”（也称为州/省/自治区/直辖市）列。  

    > [!div class="mx-imgBorder"]
    > ![提供“图例”字段后在地图上将点显示为彩色气泡的 Azure Maps 视觉对象](media/power-bi-visual/bubble-layer-with-legend-color.png)

    > [!NOTE]
    > 目前，此预览版中不会显示用于 Power BI 的内置图例控件。

3. 若要对数据进行相对缩放，请将度量拖放到“字段”窗格的“大小”Bucket 中。  在此示例中，我们使用“销售额”列。  

    > [!div class="mx-imgBorder"]
    > ![提供“大小”字段后在地图上将点显示为缩放的彩色气泡的 Azure Maps 视觉对象。](media/power-bi-visual/bubble-layer-with-legend-color-and-size.png)

4. 使用“格式”窗格中的选项来自定义数据的呈现方式。 下图与上图是同一个地图，但气泡图层的“填充透明度”选项设置为 50% 且启用了“高对比度轮廓”选项。  

    > [!div class="mx-imgBorder"]
    > ![使用自定义样式在地图上将点显示为气泡的 Azure Maps 视觉对象](media/power-bi-visual/bubble-layer-styled.png)

## <a name="fields-pane-buckets"></a>字段窗格 Bucket

Azure Maps 视觉对象的“字段”窗格中提供以下数据 Bucket。

| 字段     | 说明  |
|-----------|--------------|
| 纬度  | 此字段用于指定数据点的纬度值。 纬度值应介于 -90 十进制度到 90 十进制度之间。  |
| 经度 | 此字段用于指定数据点的经度值。 经度值应介于 -180 十进制度到 180 十进制度之间。  |
| 图例    | 此字段用于对数据进行分类，并为每个类别中的数据点分配唯一的颜色。 填充此 Bucket 后，“数据颜色”部分会出现在“格式”窗格中，你可以在其中调整颜色。  |
| 大小      | 此度量用于对地图上的数据点进行相对大小调整。   |
| 工具提示  | 当鼠标悬停在形状上时会在工具提示中显示的其他数据字段。 |

## <a name="map-settings"></a>地图设置

“格式”窗格的“地图设置”部分提供了一些选项，用于自定义地图的显示方式以及对更新的反应方式。

| 设置             | 说明  |
|---------------------|--------------|
| 自动缩放           | 自动将地图缩放到通过视觉对象的“字段”窗格加载的数据。 随着数据发生更改，地图将相应地更新其位置。 当滑块处于“关”位置时，会显示默认地图视图的更多地图视图设置。 |
| 世界环绕          | 允许用户在水平方向无限地平移地图。 |
| 样式选取器        | 向地图添加一个按钮，使报表读者可以更改地图的样式。 |
| 导航控件 | 向地图添加一些按钮，提供另一种方法让报表读者缩放和旋转地图以及更改地图的俯仰角。 请参阅[此文档的“在地图中导航”部分](map-accessibility.md#navigating-the-map)，详细了解用户在地图中导航的各种不同方式。 |
| 地图样式           | 地图的样式。 有关详细信息，请参阅[支持的地图样式](supported-map-styles.md)文档。 |
| 选择控件   | 添加一个允许用户在不同模式之间进行选择的按钮，用于选择地图上的数据；圆形、矩形、多边形（套索），或者行程时间或距离。 绘制多边形时，若要完成绘制，请单击第一个点，或在最后一个点上双击地图，或按 `c` 键。 |

### <a name="map-view-settings"></a>地图视图设置

如果“自动缩放”滑块处于“关”位置，则会显示以下设置，并允许用户指定默认地图视图信息。 

| 设置          | 说明   |
|------------------|---------------|
| 缩放             | 地图的默认缩放级别。 可以是介于 0 到 22 之间的一个数字。 |
| 中心纬度  | 地图中心的默认纬度。 |
| 中心经度 | 地图中心的默认经度。 |
| 方位          | 地图的默认方向（以度为单位，0 度为北，90 度为东，180 度为南，270 度为西）。 可以是介于 0 到 360 之间的任意数字。 |
| 音调            | 地图的默认倾斜度（以度为单位），它介于 0 度到 60 度之间，其中 0 度表示在地图上垂直向下俯视。 |

## <a name="considerations-and-limitations"></a>注意事项和限制

Azure Maps 视觉对象在以下服务和应用程序中可用：

| 服务/应用                              | 是否支持 |
|------------------------------------------|--------------|
| Power BI Desktop                         | 是          |
| Power BI 服务 (app.powerbi.com)       | 是          |
| Power BI 移动应用程序             | 是          |
| Power BI 发布到 Web                  | 否           |
| Power BI Embedded                        | 否           |
| Power BI 服务嵌入 (PowerBI.com) | 是          |

Azure Maps 在哪些国家/地区可用？

以下国家/地区除外，Azure Maps 目前在所有其他国家/地区都可用：

- 中国
- 韩国
- Azure 政府 (GCC + GCC High)

有关为这种视觉对象提供支持的不同 Azure Maps 服务的覆盖范围详细信息，请参阅[地理覆盖范围信息](geographic-coverage.md)文档。

Azure Maps 视觉对象支持哪些 Web 浏览器？

有关信息，请参阅 [Azure Maps Web SDK 支持的浏览器](supported-browsers.md)文档。

可以将多少个数据点可视化？

这种视觉对象最多支持 30000 个数据点。

可以在这种视觉对象中使用地址或其他位置字符串吗？

这种视觉对象的初始预览版仅支持十进制度的纬度值和经度值。 将来的更新会添加对地址和其他位置字符串的支持。

## <a name="next-steps"></a>后续步骤

详细了解 Azure Maps Power BI 视觉对象：

> [!div class="nextstepaction"]
> [了解 Azure Maps Power BI 视觉对象中的图层](power-bi-visual-understanding-layers.md)

> [!div class="nextstepaction"]
> [在组织中管理 Azure Maps 视觉对象](power-bi-visual-manage-access.md)

自定义视觉对象：

> [!div class="nextstepaction"]
> [Power BI 中颜色格式设置的提示和技巧](/power-bi/visuals/service-tips-and-tricks-for-color-formatting)

> [!div class="nextstepaction"]
> [自定义可视化效果的标题、背景和图例](/power-bi/visuals/power-bi-visualization-customize-title-background-and-legend)
