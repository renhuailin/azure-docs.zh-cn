---
title: 支持的内置 Azure Maps 地图样式
description: 了解 Azure Maps 支持的内置地图样式，例如道路、blank_accessible、卫星、satellite_road_labels、road_shaded_relief 和夜间。
author: anastasia-ms
ms.author: v-stharr
ms.date: 04/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: aae17a1071949ea935d7094539d31e85a354da08
ms.sourcegitcommit: f6b76df4c22f1c605682418f3f2385131512508d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2021
ms.locfileid: "108326590"
---
# <a name="azure-maps-supported-built-in-map-styles"></a>Azure Maps 支持的内置地图样式

Azure Maps 支持几种不同的内置地图样式，如下所述。

>[!important]
>本部分的过程需要 Gen 1 或 Gen 2 定价层中的 Azure Maps 帐户。 有关定价层的详细信息，请查看[在 Azure Maps 中选择正确的定价层](choose-pricing-tier.md)。

## <a name="road"></a>道路

“道路”地图是显示道路的标准地图。 它还显示自然和人工功能，以及这些功能的标签。

![道路地图样式](./media/supported-map-styles/road.png)

**适用 API：**

* [地图图像](/rest/api/maps/render/getmapimage)
* [地图图块](/rest/api/maps/render/getmaptile)
* Web SDK 地图控件
* Android 地图控件
* Power BI 视觉对象

## <a name="blank-and-blank_accessible"></a>blank 和 blank_accessible

“blank”和“blank_accessible”地图样式提供空白画布用于可视化数据。 “blank_accessible”样式将继续提供屏幕阅读器更新和地图的位置详细信息，即使不显示基本地图。

> [!Note]
> 在 Web SDK 中，可以通过设置地图 DIV 元素的 CSS `background-color` 样式来更改地图的背景色。

**适用 API：**

* Web SDK 地图控件

## <a name="satellite"></a>satellite

**卫星** 样式是卫星和航拍图像的组合。

![卫星图块地图样式](./media/supported-map-styles/satellite.png)

**适用 API：**

* [卫星图块](/rest/api/maps/render/getmapimagerytilepreview)
* Web SDK 地图控件
* Android 地图控件
* Power BI 视觉对象

## <a name="satellite_road_labels"></a>satellite_road_labels

此地图样式是道路和标签叠加在卫星和航拍图像上的混合体。

![satellite_road_labels 地图样式](./media/supported-map-styles/satellite-road-labels.png)

**适用 API：**

* Web SDK 地图控件
* Android 地图控件
* Power BI 视觉对象

## <a name="grayscale_dark"></a>grayscale_dark

**灰度深色** 是道路地图样式的深色版本。

![gray_scale 地图样式](./media/supported-map-styles/grayscale-dark.png)

**适用 API：**

* [地图图像](/rest/api/maps/render/getmapimage)
* [地图图块](/rest/api/maps/render/getmaptile)
* Web SDK 地图控件
* Android 地图控件
* Power BI 视觉对象

## <a name="grayscale_light"></a>grayscale_light

**灰度浅色** 是道路地图样式的浅色版本。

![灰度浅色地图样式](./media/supported-map-styles/grayscale-light.png)

**适用 API：**
* Web SDK 地图控件
* Android 地图控件
* Power BI 视觉对象

## <a name="night"></a>夜间

夜间是包含彩色道路和符号的道路地图样式的深色版本。

![夜间地图样式](./media/supported-map-styles/night.png)

**适用 API：**

* Web SDK 地图控件
* Android 地图控件
* Power BI 视觉对象

## <a name="road_shaded_relief"></a>road_shaded_relief

道路地形阴影是 Azure Maps 主要样式，通过地球的轮廓完成。

![地形阴影地图样式](./media/supported-map-styles/shaded-relief.png)

**适用 API：**

* [地图图块](/rest/api/maps/render/getmaptile)
* Web SDK 地图控件
* Android 地图控件
* Power BI 视觉对象

## <a name="high_contrast_dark"></a>high_contrast_dark

“high_contrast_dark”是一种比其他样式对比度更高的深色地图样式。

![高对比度深色地图样式](./media/supported-map-styles/high-contrast-dark.png)

**适用 API：**

* Web SDK 地图控件
* Power BI 视觉对象

## <a name="next-steps"></a>后续步骤

了解如何在 Azure Maps 中设置地图样式：

[选择地图样式](./choose-map-style.md)