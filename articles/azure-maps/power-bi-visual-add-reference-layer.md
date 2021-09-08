---
title: 将参考层添加到 Azure Maps Power BI 视觉对象 | Microsoft Azure Maps
description: 在本文中，你将了解如何在适用于 Power BI 的 Microsoft Azure Maps 视觉对象中使用参考层。
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: f1488584f025edc9e8b3e098b283efbb56b449d4
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123430425"
---
# <a name="add-a-reference-layer"></a>添加引用层

通过“参考层”功能，可以将辅助空间数据集上传到视觉对象，并将其叠加到地图上，以提供附加上下文。 此数据集由 Power BI 托管，并且必须为具有 `.json` 或 `.geojson` 文件扩展名的 [GeoJSON 文件](https://wikipedia.org/wiki/GeoJSON)。

若要将 GeoJSON 文件添加为参考层，请在“格式”窗格中，展开“参考层”部分，然后按“+ 添加本地文件”按钮   。

将 GeoJSON 文件添加到参考层后，该文件的名称将显示在“+ 添加本地文件”按钮的位置，旁边有一个 X 。 按“X”按钮，从视觉对象中删除数据，并从 Power BI 中删除 GeoJSON 文件。

下图显示了 [2016 年科罗拉多州的人口普查区](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Common/data/geojson)，按人口设置颜色。

> [!div class="mx-imgBorder"]
> ![显示 2016 年科罗拉多州的人口普查区的地图，按人口设置颜色，用作参考层](media/power-bi-visual/reference-layer-CO-census-tract.png)

下面是“参考层”部分提供的“格式”窗格中的所有设置。

| 设置              | 说明   |
|----------------------|---------------|
| 参考层数据 | 要作为地图内的附加层上传到视觉对象的 GeoJSON 数据文件。 “+ 添加本地文件”按钮将打开一个文件对话框，用户可以使用该对话框选择具有 `.json` 或 `.geojson` 文件扩展名的 GeoJSON 文件。 |

> [!NOTE]
> 在此 Azure Maps 视觉对象预览中，参考层只将前 5,000 个形状特征加载到地图中。 在将来的更新中会提高此限值。

## <a name="styling-data-in-a-reference-layer"></a>设置参考层中数据的样式

可以将属性添加到 GeoJSON 文件中的每个特征，以自定义其在地图上的样式。 此功能使用 Azure Maps Web SDK 中的简单数据层功能。 有关详细信息，请参阅有关[支持的样式属性](spatial-io-add-simple-data-layer.md#default-supported-style-properties)的文档。 在 Azure Maps 视觉对象中，不支持将自定义图标图像作为安全预防措施。

下面是 GeoJSON 点功能的一个示例，它将其显示的颜色设置为红色。

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.13284, 47.63699]
    },
    "properties": {
        "color": "red"
    }
}
```
## <a name="next-steps"></a>后续步骤

向地图添加更多上下文：

> [!div class="nextstepaction"]
> [添加图块层](power-bi-visual-add-tile-layer.md)

> [!div class="nextstepaction"]
> [显示实时流量](power-bi-visual-show-real-time-traffic.md)
