---
title: 支持的内置 Azure Maps 地图样式
description: 了解 Azure Maps 支持的内置地图样式，例如道路、blank_accessible、卫星、satellite_road_labels、road_shaded_relief 和夜间。
author: anastasia-ms
ms.author: v-stharr
ms.date: 04/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: 4032dec715fc12b4e8144e6d9eebd51faceaaaaf
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121735867"
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

![灰度浅色地图样式](./media/supported-map-styles/grayscale-light.jpg)

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
* Android 地图控件
* Power BI 视觉对象

## <a name="high_contrast_light"></a>high_contrast_light

“high_contrast_light”是一种浅色地图样式，对比度比其他样式更高。

![高对比度浅色地图样式](./media/supported-map-styles/high-contrast-light.jpg)

**适用 API：**

* Web SDK 地图控件
* Android 地图控件
* Power BI 视觉对象

## <a name="map-style-accessibility"></a>地图样式辅助功能

交互式 Azure Maps 地图控件使用地图样式中的矢量图块为屏幕阅读器提供支持，来描述地图显示的区域。 在颜色对比度方面，多种地图样式也设计为可完全访问。 下表提供了有关每个地图样式支持的辅助功能的详细信息。

| 地图样式  | 颜色对比度 | 屏幕阅读器 | 说明 |
|------------|----------------|---------------|-------|
| `blank` | 不适用 | 否 | 对于希望使用自己的图块作为基本地图，或希望在没有任何背景的情况下查看数据的开发人员而言，空白画布非常适用。 屏幕阅读器不会依赖矢量图块进行说明。  |
| `blank_accessible` | 不适用  | 是 | 在幕后，此地图样式继续加载用于呈现地图的矢量图块，但是使这些数据透明。 这样，数据仍将加载，并可用于为屏幕阅读器提供支持。 |
| `grayscale_dark` | 部分 | 是 | 此地图样式主要用于商业智能方案，但可用于覆盖彩色层，如天气雷达图像。 |
| `grayscale_light` | 部分 | 是 | 此地图样式主要用于商业智能方案。 |
| `high_contrast_dark` | 是 | 是 | 可完全访问的地图样式，供处于深色设置的高对比度模式下的用户使用。 地图加载时，将自动检测高对比度设置。 |
| `high_contrast_light` | 是 | 是 | 可完全访问的地图样式，供处于浅色设置的高对比度模式下的用户使用。 地图加载时，将自动检测高对比度设置。 |
| `night` | 部分 | 是 | 此样式为处于弱光环境的用户而设计，你不希望地图过于明亮，使他们产生视觉压迫感。 |
| `road` | 部分 | 是 | 这是 Azure Maps 中主要的彩色道路地图样式。 由于大量不同的颜色以及可能的重叠色彩组合，几乎不可能使其 100% 无障碍。 也就是说，此地图样式会经过常规的辅助功能测试，并根据需要进行改进，使标签更便于阅读。 |
| `road_shaded_relief` | 部分 | 是 | 这几乎与主要道路地图样式相同，但在背景中添加了一个图块层，当缩小到更高级别时，此图块层会添加山脉的地形阴影和地表覆盖着色。 |
| `satellite` | 空值 | 是 | 纯卫星和航拍图像，无标签或道路标线。 矢量图块在后台进行加载，为屏幕阅读器提供支持，在切换到 `satellite_with_roads` 时实现更流畅的转换。 |
| `satellite_with_roads` | 否 | 是 | 卫星和航拍图像，叠加了标签和道路线标。 在全球范围内，叠加数据和图像之间可能会出现无限数量的颜色组合。 在大多数常见场景下，应侧重于使标签可读，但在某些位置，与背景图像的颜色对比度可能会导致标签难以阅读。 |

## <a name="next-steps"></a>后续步骤

了解如何在 Azure Maps 中设置地图样式：

> [!div class="nextstepaction"]
> [选择地图样式](./choose-map-style.md)
