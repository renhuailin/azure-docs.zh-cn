---
title: 管理场
description: 介绍如何管理场
author: RiyazPishori
ms.topic: article
ms.date: 11/04/2019
ms.author: riyazp
ms.openlocfilehash: 70d7ae985a56842f2b02c5af5a7c7c1e589b2b56
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2021
ms.locfileid: "108759808"
---
# <a name="manage-farms"></a>管理场

可以在 Azure FarmBeats 中管理场。 本文提供了有关如何创建场、安装设备、传感器和无人机的信息，这些信息可帮助管理场。

## <a name="create-farms"></a>创建场

请使用以下步骤：

1. 登录到场加速器，会显示“场”页面。
    如果已在订阅中创建了场，则“场”页面会显示场列表。

    以下是示例映像：

    ![显示场页面的屏幕截图。](./media/create-farms-in-azure-farmbeats/create-farm-main-page-1.png)


2. 选择“创建场”并提供“名称”、“作物”和“地址”。   
3. 在“定义场边界”，（必填字段）选择“标记地图”或粘贴 GeoJSON 代码。  

下面是定义场边界的两种方法：

1. “标记地图”：使用地图控件工具绘制和标记场的边界。 若要标记边界，请参阅![显示用于在地图上绘制边界的铅笔图标的屏幕截图](./media/create-farms-in-azure-farmbeats/pencil-icon-1.png)，并标记精确的边界。

    ![显示地图上所绘制边界的屏幕截图。](./media/create-farms-in-azure-farmbeats/create-farm-mark-on-map-1.png)

2. “粘贴 GeoJson 代码”：GeoJSON 是一种使用 JavaScript 对象表示法 (JSON) 编码地理数据结构的格式。 此选项显示一个文本框，可以在其中输入 GeoJSON 字符串来标记场边界。 也可以从 GeoJSON.io 创建 GeoJSON 代码。
使用工具提示来帮助填写信息。

    ![突出显示了创建场屏幕上的粘贴 GeoJson 代码选项的屏幕截图。](./media/create-farms-in-azure-farmbeats/create-new-farm-1.png)

3.  选择“提交”以创建场。 会创建一个新场并显示在“场”页面中。

## <a name="view-farm"></a>查看场

场列表页面显示所创建场的列表。 选择一个场，以查看以下列表内容：

 - “设备计数” — 显示在场中部署的设备的数量和状态。
 - “地图” — 场中部署了设备的场地图。
 - “遥测数据” — 显示场中部署的传感器的遥测数据。
 - “最新精度地图” — 显示最新的卫星索引地图 (EVI、NDWI)、土潮湿热度地图和传感器放置地图。

## <a name="edit-farm"></a>编辑场

“场”页面显示所创建场的列表。

1.  选择一个场以查看和编辑该场。
2.  选择“编辑场”以编辑场信息。 在“场详细信息”窗口中，可以编辑“名称”、“作物”、“地址”和定义“场边界”字段。    

    ![FarmBeats 项目](./media/create-farms-in-azure-farmbeats/edit-farm-1.png)

3. 选择“提交”以保存所编辑的详细信息。

## <a name="delete-farm"></a>删除场

“场”页面显示所创建场的列表。 通过以下步骤删除场：

1.  从列表中选择一个场来删除场详细信息。
2.  选择“删除场”来删除场。

    ![显示删除场屏幕并突出显示删除按钮的屏幕截图。](./media/create-farms-in-azure-farmbeats/delete-farm-1.png)

    > [!NOTE]
    > 删除场时，不会删除与该场关联的设备和地图。 与设备和地图关联的任何场信息都不相关。 可以继续从 FarmBeats 服务查看设备、遥测数据和地图。


## <a name="next-steps"></a>后续步骤

现在，已经创建了场，接下来请了解如何让[传感器数据](get-sensor-data-from-sensor-partner.md)流入你的场。
