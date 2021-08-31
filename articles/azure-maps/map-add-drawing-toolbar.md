---
title: 在地图中添加绘图工具的工具栏 | Microsoft Azure Maps
description: 如何使用 Azure Maps Web SDK 在地图中添加绘图工具栏
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
ms.custom: devx-track-js
ms.openlocfilehash: 0a548d516213b65406a2f47c27c9a1a82cfb6f4b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121751424"
---
# <a name="add-a-drawing-tools-toolbar-to-a-map"></a>在地图中添加绘图工具的工具栏

本文介绍如何使用“绘图工具”模块，并在地图上显示绘图工具栏。 [绘图工具栏](/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar)控件将绘图工具栏添加到地图上。 你将了解如何只用一种和所有的绘图工具来创建地图，以及如何在绘图管理器中定制绘图形状的渲染。

## <a name="add-drawing-toolbar"></a>添加绘图工具栏

下面的代码将创建一个绘图管理器实例，并在地图上显示该工具栏。

```javascript
//Create an instance of the drawing manager and display the drawing toolbar.
drawingManager = new atlas.drawing.DrawingManager(map, {
        toolbar: new atlas.control.DrawingToolbar({
            position: 'top-right',
            style: 'dark'
        })
    });
```

下面是上述功能的完整运行代码示例：

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="添加绘图工具栏" src="//codepen.io/azuremaps/embed/ZEzLeRg/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/ZEzLeRg/'>添加绘图工具栏</a>。
</iframe>


## <a name="limit-displayed-toolbar-options"></a>限制显示的工具栏选项

下面的代码将创建一个绘图管理器实例，并在地图上只显示一个多边形绘图工具的工具栏。 

```javascript
//Create an instance of the drawing manager and display the drawing toolbar with polygon drawing tool.
drawingManager = new atlas.drawing.DrawingManager(map, {
        toolbar: new atlas.control.DrawingToolbar({
            position: 'top-right',
            style: 'light',
            buttons: ["draw-polygon"]
        })
    });
```

下面是上述功能的完整运行代码示例：

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="添加多边形绘图工具" src="//codepen.io/azuremaps/embed/OJLWWMy/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/OJLWWMy/'>添加多边形绘图工具</a>。
</iframe>


## <a name="change-drawing-rendering-style"></a>更改绘图渲染样式

通过使用 `drawingManager.getLayers()` 和 `drawingManager.getPreviewLayers()` 函数检索绘图管理器的基础图层，然后在各个图层上设置选项，可以自定义绘制的形状样式。 编辑形状时，针对坐标显示的拖动手柄为 HTML 标记。 可以通过将 HTML 标记选项传递给绘图管理器的 `dragHandleStyle` 和 `secondaryDragHandleStyle` 选项来自定义拖动手柄的样式。  

下面的代码从绘图管理器中获取渲染层，并修改它们的选项以更改绘图的渲染样式。 在这种情况下，将用蓝色标记图标渲染点。 线条将为红色，四像素宽。 多边形将具有绿色填充色和橙色勾边。 然后将拖动手柄的样式更改为方形图标。 

```javascript
//Get rendering layers of drawing manager.
var layers = drawingManager.getLayers();

//Change the icon rendered for points.
layers.pointLayer.setOptions({
    iconOptions: {
        image: 'marker-blue'
    }
});

//Change the color and width of lines.
layers.lineLayer.setOptions({
    strokeColor: 'red',
    strokeWidth: 4
});

//Change fill color of polygons.
layers.polygonLayer.setOptions({
    fillColor: 'green'
});

//Change the color of polygon outlines.
layers.polygonOutlineLayer.setOptions({
    strokeColor: 'orange'
});


//Get preview rendering layers from the drawing manager and modify line styles to be dashed.
var previewLayers = drawingManager.getPreviewLayers();
previewLayers.lineLayer.setOptions({ strokeColor: 'red', strokeWidth: 4, strokeDashArray: [3,3] });
previewLayers.polygonOutlineLayer.setOptions({ strokeColor: 'orange', strokeDashArray: [3, 3] });

//Update the style of the drag handles that appear when editting.
drawingManager.setOptions({
    //Primary drag handle that represents coordinates in the shape.
    dragHandleStyle: {
        anchor: 'center',
        htmlContent: '<svg width="15" height="15" viewBox="0 0 15 15" xmlns="http://www.w3.org/2000/svg" style="cursor:pointer"><rect x="0" y="0" width="15" height="15" style="stroke:black;fill:white;stroke-width:4px;"/></svg>',
        draggable: true
    },

    //Secondary drag hanle that represents mid-point coordinates that users can grab to add new cooridnates in the middle of segments.
    secondaryDragHandleStyle: {
        anchor: 'center',
        htmlContent: '<svg width="10" height="10" viewBox="0 0 10 10" xmlns="http://www.w3.org/2000/svg" style="cursor:pointer"><rect x="0" y="0" width="10" height="10" style="stroke:white;fill:black;stroke-width:4px;"/></svg>',
        draggable: true
    }
});  
```

下面是上述功能的完整运行代码示例：

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="更改绘图渲染样式" src="//codepen.io/azuremaps/embed/OJLWpyj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/OJLWpyj/'>Change drawing rendering style</a>。
</iframe>

> [!NOTE]
> 在编辑模式下，可以旋转形状。 支持从 MultiPoint、LineString、MultiLineString、Polygon、MultiPolygon 和 Rectangle 几何图形进行旋转。 不能旋 Point 和 Circle 几何图形。 

## <a name="next-steps"></a>后续步骤

了解如何使用绘图工具模块的其他功能：

> [!div class="nextstepaction"]
> [获取形状数据](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [对绘图事件做出反应](drawing-tools-events.md)

> [!div class="nextstepaction"]
> [交互类型和键盘快捷键](drawing-tools-interactions-keyboard-shortcuts.md)

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [Map](/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [绘图工具栏](/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar)

> [!div class="nextstepaction"]
> [绘图管理器](/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager)