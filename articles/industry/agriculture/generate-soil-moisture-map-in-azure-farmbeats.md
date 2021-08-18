---
title: 生成土壤湿度热度地图
description: 介绍如何在 Azure FarmBeats 中生成土壤湿度热度地图
author: RiyazPishori
ms.topic: article
ms.date: 11/04/2019
ms.author: riyazp
ms.openlocfilehash: d318f7aad20f4bdf8ccb8db53c20c2426216b4dd
ms.sourcegitcommit: 5a27d9ba530aee0e563a1b0159241078e8c7c1e4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2021
ms.locfileid: "112422491"
---
# <a name="generate-soil-moisture-heatmap"></a>生成土壤湿度热度地图

土壤湿度指的是土壤颗粒之间的空隙中的水含量。 借助土壤湿度热度地图，可以了解农场内任何深度的高分辨率湿度数据。 若要生成准确的可用土壤湿度热度地图，需要使用来自同一提供商的统一传感器部署。 不同提供商测量土壤湿度的方式不同，校准的方式也不同。 对于特定深度，将使用该深度部署的传感器生成热度地图。

本文介绍使用 Azure FarmBeats 加速器为你的农场生成土壤湿度热度地图的过程。 在本文中，你将学习如何：

- [创建农场](#create-a-farm)
- [将传感器分配到农场](#get-soil-moisture-sensor-data-from-partner)
- [生成土壤湿度热度地图](#generate-soil-moisture-heatmap)

## <a name="before-you-begin"></a>在开始之前

确保以下信息：  

- Azure 订阅。
- Azure FarmBeats 的运行实例。
- 农场最少需要三个可用土壤湿度传感器。

## <a name="create-a-farm"></a>创建场

农场是指准备为其创建土壤湿度热度地图的地理区域。 可以使用[农场 API](https://aka.ms/FarmBeatsDatahubSwagger) 或在 [FarmsBeats 加速器 UI](manage-farms-in-azure-farmbeats.md#create-farms) 中创建农场

## <a name="deploy-sensors"></a>部署传感器

用户应在农场中实地部署土壤湿度传感器。 你可以从我们任何已获批准的合作伙伴处购买土壤湿度传感器，包括 [Davis Instruments](https://www.davisinstruments.com/products/enviromonitor-gateway-us-lte) 和 [Teralytic](https://teralytic.com/)。 用户应与传感器提供商进行协作，以便在农场中完成实物设置。

## <a name="get-soil-moisture-sensor-data-from-partner"></a>从合作伙伴获取土壤湿度传感器数据

随着传感器开始将数据流式传输到合作伙伴的数据仪表板，数据会进入 Azure FarmBeats。 该步骤可以从合作伙伴的应用程序中完成。

例如，如果你已购买 Davis 传感器，将登录天气链接帐户，并提供所需的凭据以使数据流式传输到 Azure FarmBeats。 若要获取所需的凭据，请按照[获取传感器数据](get-sensor-data-from-sensor-partner.md#get-sensor-data-from-sensor-partners)中的说明进行操作。

输入凭据并在合作伙伴应用程序上选择“提交”后，可以将数据流入 Azure FarmBeats。

### <a name="assign-soil-moisture-sensors-to-the-farm"></a>将土壤湿度传感器分配到农场

将传感器帐户关联到 Azure FarmBeats 后，需要将土壤湿度传感器分配给相关农场。

1.  在主页中，从菜单中选择“农场”，随即会显示“农场”列表页。
2.  选择“MyFarm” > “添加设备”。
3.  将显示“添加设备”窗口。 为农场选择关联到土壤湿度传感器的任何设备。

    ![显示“添加设备”屏幕的屏幕截图。](./media/get-sensor-data-from-sensor-partner/add-devices-1.png)

4. 选择“添加设备”。     

## <a name="generate-soil-moisture-heatmap"></a>生成土壤湿度热度地图

此步骤将创建一个作业或长期操作，为你的农场生成土壤湿度热度地图。

1.  在主页上，从左侧导航菜单中转到“农场”以查看农场页。
2.  选择“MyFarm”。
3.  在“农场详细信息”页中，选择“生成精确地图”。
4.  从下拉菜单中选择“土壤湿度”。
5.  在“土壤湿度”窗口中选择“本周”。
6.  在“选择土壤湿度”“传感器度量值”中，输入要用于地图的度量值。
    若要查找传感器度量值，请在“传感器”中选择任何土壤湿度传感器。 在“传感器属性”中，使用“度量值名称”值。

    ![显示“土壤湿度”屏幕的屏幕截图。](./media/get-sensor-data-from-sensor-partner/soil-moisture-1.png)


7.  选择“生成地图”。
    将显示包含作业详细信息的确认消息。 有关详细信息，请参阅“作业”中的“作业状态”。

    >[!NOTE]
    > 作业大约需要 3 到 4 小时才能完成。

### <a name="download-the-soil-moisture-heatmap"></a>下载土壤湿度热度地图

请使用以下步骤：

1. 在“作业”页上，检查你在上一个过程中创建的作业的“作业状态”。
2. 当作业状态显示为“成功”时，请在菜单上选择“地图”。
3. 按创建日期搜索地图，日期格式为<soil-moisture_MyFarm_YYYY-MM-DD>。
4. 在“名称”列中选择一个地图，随即会显示一个弹出窗口，其中包含所选地图的预览。
5. 选择“下载”。 下载地图，并将其存储在计算机的本地文件夹中。

    ![FarmBeats 项目](./media/get-sensor-data-from-sensor-partner/download-soil-moisture-map-1.png)

## <a name="next-steps"></a>后续步骤

现在，你已成功生成了土壤湿度热度地图，接下来了解如何[生成传感器放置](generate-maps-in-azure-farmbeats.md#sensor-placement-map)和[引入历史遥测数据](ingest-historical-telemetry-data-in-azure-farmbeats.md)。 
