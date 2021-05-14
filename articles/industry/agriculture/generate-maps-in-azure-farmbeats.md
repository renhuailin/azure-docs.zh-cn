---
title: 生成地图
description: 本文介绍如何在 Azure FarmBeats 中生成地图。
author: RiyazPishori
ms.topic: article
ms.date: 11/04/2019
ms.author: riyazp
ms.openlocfilehash: 0b936ecbd80ba0b258cea8b043f4bef789b67a35
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2021
ms.locfileid: "108751294"
---
# <a name="generate-maps"></a>生成地图

使用 Azure FarmBeats，可以通过使用卫星图像和传感器数据输入来生成以下地图。 地图有助于查看农场的地理位置，并识别设备的适当位置。

  - “传感器放置地图”：提供有关要使用的传感器数量以及在农场中放置的位置的建议。
  - “卫星指数地图”：显示农场的植被指数和水分指数。
  - “土壤湿度热度地图”：通过融合卫星数据和传感器数据来显示土壤湿度分布。

## <a name="sensor-placement-map"></a>传感器放置地图

FarmBeats 传感器放置地图可帮助放置土壤湿度传感器。 地图输出包含传感器部署的一系列坐标。 这些传感器的输入与卫星图像一起使用，以生成土壤湿度热度地图。

此地图是将如随全年的多个日期推移所见的林冠分段而得到的。 即使是裸露的土壤和建筑物也是林冠的一部分。 可以删除该位置上不需要的传感器。 此地图用于指导，你可以根据自己的自定义知识略微改变位置和数量。 添加传感器不会使土壤湿度热度地图的结果退化，但如果传感器数量减少，就有可能有损坏的热度地图准确性。

## <a name="before-you-begin"></a>准备阶段

在尝试生成传感器放置地图之前，请满足以下先决条件：

- 农场大小必须大于一英亩。
- 对于所选日期范围，无云的 Sentinel 场景数必须大于 6。
- 至少六个无云的 Sentinel 场景必须具有大于或等于 0.3 的规范化差异植被指数 (NDVI)。

    > [!NOTE]
    > Sentinel 仅允许每个用户有两个并发线程。 因此，作业将排队等待，并且可能需要更长的时间才能完成。

### <a name="dependencies-on-sentinel"></a>Sentinel 上的依赖项

以下依赖项与 Sentinel 相关：

- 我们依赖于 Sentinel 性能来下载卫星图像。 检查 Sentinel 性能状态和维护[活动](https://scihub.copernicus.eu/twiki/do/view/SciHubNews/WebHome)。
- Sentinel 仅允许每个用户有两个并发的[下载线程](https://sentinels.copernicus.eu/web/sentinel/sentinel-data-access/typologies-and-services)。
- 精度地图生成受 [Sentinel 覆盖范围和重新访问频率]( https://sentinel.esa.int/web/sentinel/user-guides/sentinel-2-msi/revisit-coverage)的影响。

## <a name="create-a-sensor-placement-map"></a>创建传感器放置地图
本部分详细介绍了创建传感器放置地图的过程。

> [!NOTE]
> 你可以从“地图”页或从“农场详细信息”页上的“生成精度地图”下拉菜单启动传感器放置地图。  

请执行以下步骤。

1. 在主页上，从左侧导航菜单中转到“地图”。
2. 选择“创建地图”，然后从下拉菜单中选择“传感器放置”。 

    ![选择传感器放置](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-1.png)

3. 选择“传感器放置”后，出现“传感器放置”窗口。 

    ![“传感器放置”置窗口](./media/get-sensor-data-from-sensor-partner/sensor-placement-1.png)

4. 从“农场”下拉菜单中选择一个农场。
   若要搜索并选择农场，可以在下拉列表中滚动，或在文本框中输入农场的名称。
5. 若要生成上一年的地图，请选择“推荐”。
6. 若要生成自定义日期范围的地图，请选择“选择日期范围”选项。 输入要生成传感器放置地图的开始日期和结束日期。
7. 选择“生成地图”。
 此时会出现包含作业详细信息的确认消息。

  有关作业状态的信息，请参阅“查看作业”部分。 如果作业状态显示为“失败”，则会在“失败”状态的工具提示上出现详细的错误消息。  在这种情况下，请重复前面的步骤，然后重试。

  如果问题仍然存在，请参阅[故障排除](troubleshoot-azure-farmbeats.md)部分，或联系 [Azure FarmBeats 论坛以获得支持](https://aka.ms/FarmBeatsMSDN)获得相关日志支持。

### <a name="view-and-download-a-sensor-placement-map"></a>查看和下载传感器放置地图

请执行以下步骤。

1. 在主页上，从左侧导航菜单中转到“地图”。

    ![在左侧导航菜单中选择地图](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. 从窗口的左侧导航栏中选择“筛选器”。
  “筛选器”窗口显示搜索条件。

    ![筛选器窗口](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3. 从下拉菜单中选择“类型”、“日期”和“名称”值。   然后选择“应用”以搜索要查看的地图。
  创建作业的日期以格式 type_farmname_YYYY-MM-DD 显示。
4. 在页面末尾使用导航栏，滚动浏览可用地图的列表。
5. 选择想要查看的地图。 弹出窗口显示所选地图的预览。
6. 选择“下载”，然后下载传感器坐标的 GeoJSON 文件。

    ![传感器放置地图预览](./media/get-sensor-data-from-sensor-partner/download-sensor-placement-map-1.png)

## <a name="satellite-indices-map"></a>卫星指数地图

以下部分介绍了创建和查看卫星指数地图所涉及的过程。

> [!NOTE]
> 可以从“地图”页或从“农场详细信息”页上的“生成精度地图”下拉菜单启动卫星指数地图。  

FarmBeats 使你能够生成农场的 NDVI、增强的植被索引 (EVI)以及规范化差异水索引 (NDWI) 地图。 这些指数可帮助确定作物当前正在生长或过去生长的情况，以及地面的代表性水位。


> [!NOTE]
> 生成地图的日期需要一个 Sentinel 图像。


## <a name="create-a-satellite-indices-map"></a>创建卫星指数地图

请执行以下步骤。

1. 在主页上，从左侧导航菜单中转到“地图”。
2. 选择“创建地图”，然后从下拉菜单中选择“卫星指数”。 

    ![从下拉菜单中选择卫星指数](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-satellite-indices-1.png)

3. 选择“卫星指数”后，出现“卫星指数”窗口。 

    ![卫星指数窗口](./media/get-sensor-data-from-sensor-partner/satellitte-indices-1.png)

4. 从下拉菜单中选择一个农场。
   若要搜索并选择农场，可以在下拉列表中滚动，或输入农场的名称。   
5. 若要生成上一周的地图，请选择“本周”。
6. 若要生成自定义日期范围的地图，请选择“选择日期范围”选项。 输入要生成卫星指数地图的开始日期和结束日期。
7. 选择“生成地图”。
    此时会出现包含作业详细信息的确认消息。

    ![卫星指数地图确认消息](./media/get-sensor-data-from-sensor-partner/successful-satellitte-indices-1.png)

    有关作业状态的信息，请参阅“查看作业”部分。 如果作业状态显示为“失败”，则会在“失败”状态的工具提示上出现详细的错误消息。  在这种情况下，请重复前面的步骤，然后重试。

    如果问题仍然存在，请参阅[故障排除](troubleshoot-azure-farmbeats.md)部分，或联系 [Azure FarmBeats 论坛以获得支持](https://aka.ms/FarmBeatsMSDN)获得相关日志支持。

### <a name="view-and-download-a-map"></a>查看并下载地图

请执行以下步骤。

1. 在主页上，从左侧导航菜单中转到“地图”。

    ![选择地图](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. 从窗口的左侧导航栏中选择“筛选器”。 “筛选器”窗口显示搜索条件。

    ![筛选器窗口显示搜索条件](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3. 从下拉菜单中选择“类型”、“日期”和“名称”值。   然后选择“应用”以搜索要查看的地图。
  创建作业的日期以格式 type_farmname_YYYY-MM-DD 显示。

4. 在页面末尾使用导航栏，滚动浏览可用地图的列表。
5. 对于“农场名称”和“日期”的每个组合，可以使用以下三个地图： 
    - NDVI
    - EVI
    - NDWI
6. 选择想要查看的地图。 弹出窗口显示所选地图的预览。
7. 从下拉菜单中选择“下载”以选择下载格式。 地图下载并存储在计算机上的本地文件夹中。

    ![选择的卫星指数地图预览](./media/get-sensor-data-from-sensor-partner/download-satellite-indices-map-1.png)

## <a name="soil-moisture-heatmap"></a>土壤湿度热度地图

土壤湿度指的是土壤颗粒之间的空间中含有的水分。 借助土壤湿度热度地图，可以在农场内的任何深度处以高分辨率了解土壤湿度数据。 若要生成准确且可用的土壤湿度热度地图，需要对传感器进行统一部署。 所有传感器都必须来自同一个提供商。 不同提供商测量土壤湿度的方式不同，校准的方式也不同。 使用在该深度部署的传感器生成特定深度的热度地图。

### <a name="before-you-begin"></a>准备阶段

在尝试生成土壤湿度热度地图之前，请满足以下先决条件：

- 必须部署至少三个土壤湿度传感器。 在传感器部署并与农场关联之前，不要尝试创建土壤湿度热度地图。
- 生成土壤湿度热度地图会受到 Sentinel 的路径范围、云覆盖和云阴影的影响。 在从请求土壤湿度热度地图那天开始的过去的120天内，必须有至少一个可用的无云的 Sentinel 场景。
- 在农场中部署的至少一半传感器必须处于联机状态，并将数据流式传输到 datahub。
- 必须使用同一提供商的传感器度量值生成热度地图。


## <a name="create-a-soil-moisture-heatmap"></a>创建土壤湿度热度地图

请执行以下步骤。

1. 在主页上，从左侧导航菜单中转到“地图”以查看“地图”页。 
2. 选择“创建地图”，然后从下拉菜单中选择“土壤湿度”。 

    ![从下拉菜单中选择土壤湿度](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-soil-moisture-1.png)

3. 选择“土壤湿度”后，出现“土壤湿度”窗口。 

    ![土壤湿度窗口](./media/get-sensor-data-from-sensor-partner/soil-moisture-1.png)

4. 从“农场”下拉菜单中选择一个农场。
   若要搜索并选择农场，可以在下拉列表中滚动，或在 **选择农场** 下拉菜单中输入农场的名称。
5. 在“选择土壤湿度传感器度量值”下拉菜单上，选择要生成地图的土壤湿度传感器度量值（深度）。
若要找到传感器度量值，转到“传感器”，并选择任何土壤湿度传感器。 然后，在“传感器属性”部分下，使用“度量值名称”中的值。 
6. 若要生成“今天”或“本周”的地图，请选择其中一个选项。 
7. 若要生成自定义日期范围的地图，请选择“选择日期范围”选项。 输入要生成土壤湿度热度地图的开始日期和结束日期。
8. 选择“生成地图”。
 此时会出现包含作业详细信息的确认消息。

   ![土壤湿度地图确认消息](./media/get-sensor-data-from-sensor-partner/successful-soil-moisture-1.png)

    有关作业状态的信息，请参阅“查看作业”部分。 如果作业状态显示为“失败”，则会在“失败”状态的工具提示上出现详细的错误消息。  在这种情况下，请重复前面的步骤，然后重试。

    如果问题仍然存在，请参阅[故障排除](troubleshoot-azure-farmbeats.md)部分，或联系 [Azure FarmBeats 论坛以获得支持](https://aka.ms/FarmBeatsMSDN)获得相关日志支持。

### <a name="view-and-download-a-map"></a>查看并下载地图

请执行以下步骤。

1. 在主页上，从左侧导航菜单中转到“地图”。

    ![转到地图](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. 从窗口的左侧导航栏中选择“筛选器”。 此时将显示“筛选器”窗口，您可以在其中搜索地图。

    ![从左侧导航栏选择筛选器](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3.  从下拉菜单中选择“类型”、“日期”和“名称”值。   然后选择“应用”以搜索要查看的地图。 创建作业的日期以格式 type_farmname_YYYY-MM-DD 显示。
4. 选择表头旁边的“排序”按钮，根据“农场”、“日期”、“创建日期”、“作业 ID”和“作业类型”进行排序。     
5. 在页面末尾使用导航按钮，滚动浏览可用地图的列表。
6. 选择想要查看的地图。 弹出窗口显示所选地图的预览。
7. 从下拉菜单中选择“下载”以选择下载格式。 地图将下载并存储在指定的文件夹中。

    ![土壤湿度热度地图预览](./media/get-sensor-data-from-sensor-partner/download-soil-moisture-map-1.png)
