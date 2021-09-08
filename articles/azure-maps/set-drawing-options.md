---
title: 绘图工具模块 | Microsoft Azure Maps
description: 本文介绍了如何使用 Microsoft Azure Maps Web SDK 设置绘图选项数据
author: anastasia-ms
ms.author: v-stharr
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-maps
ms.custom: devx-track-js
ms.openlocfilehash: 260aac69c33efa7ef006f882af1cfd2c851eecc2
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123437717"
---
# <a name="use-the-drawing-tools-module"></a>使用绘图工具模块

Azure Maps Web SDK 提供了一个 *绘图工具模块*。 使用此模块，可以轻松地使用输入设备（如鼠标或触摸屏）绘制和编辑地图上的形状。 此模块的核心类是[绘图管理器](/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager#setoptions-drawingmanageroptions-)。 绘图管理器提供了在地图上绘制和编辑形状所需的一切功能。 它可直接使用，并与自定义工具栏 UI 集成。 还可以使用内置的[绘图工具栏](/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar)类。

## <a name="loading-the-drawing-tools-module-in-a-webpage"></a>在网页中加载绘图工具模块

1. 创建一个新的 HTML 文件，并[照常实现该地图](./how-to-use-map-control.md)。
2. 加载 Azure Maps 绘图工具模块。 可以通过两种方法加载该模块：
    - 使用 Azure Maps 服务模块的全局托管 Azure 内容分发网络版本。 在文件的 `<head>` 元素中添加对 JavaScript 和 CSS 样式表的引用：

        ```html
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/drawing/1/atlas-drawing.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/drawing/1/atlas-drawing.min.js"></script>
        ```

    - 或者，可以通过使用 [azure-maps-drawing-tools](https://www.npmjs.com/package/azure-maps-drawing-tools) npm 包在本地加载 Azure Maps Web SDK 源代码的绘图工具模块，然后将其托管在你的应用中。 此程序包还包括了 TypeScript 定义。 使用此命令：

        > **npm install azure-maps-drawing-tools**

        然后，在文件的 `<head>` 元素中添加对 JavaScript 和 CSS 样式表的引用：

         ```html
        <link rel="stylesheet" href="node_modules/azure-maps-drawing-tools/dist/atlas-drawing.min.css" type="text/css" />
        <script src="node_modules/azure-maps-drawing-tools/dist/atlas-drawing.min.js"></script>
         ```

## <a name="use-the-drawing-manager-directly"></a>直接使用绘图管理器

在应用程序中加载“绘图工具”模块后，可以使用[绘图管理器](/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager#setoptions-drawingmanageroptions-)启用绘图和编辑功能。 可以在对其进行实例化或使用 `drawingManager.setOptions()` 函数时，为绘图管理器指定选项。

### <a name="set-the-drawing-mode"></a>设置绘制模式

下面的代码将创建一个绘图管理器实例，并设置“绘制 **模式**”选项。 

```javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon"
});
```

下面的代码是一个完整运行的示例，演示如何设置绘图管理器的绘制模式。 单击地图，开始绘制多边形。

<br/>

<iframe height="500" scrolling="no" title="绘制多边形" src="//codepen.io/azuremaps/embed/YzKVKRa/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/YzKVKRa/'>绘制多边形</a>。
</iframe>


### <a name="set-the-interaction-type"></a>设置交互类型

绘图管理器支持三种不同的方法来与地图进行交互以绘制形状。

* `click` -在单击鼠标或触摸时添加坐标。
* `freehand ` - 在地图上拖动鼠标或触摸时添加坐标。 
* `hybrid` -在单击或拖动鼠标或触摸时添加坐标。

下面的代码将启用多边形绘制模式，并设置绘图管理器应遵循的绘图交互的类型 `freehand`。 

```javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon",
    interactionType: "freehand"
});
```

 此代码示例实现了在地图上绘制多边形的功能。 只需按住鼠标左键并随意拖动即可。

<br/>

<iframe height="500" scrolling="no" title="自由手工绘图" src="//codepen.io/azuremaps/embed/ZEzKoaj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/ZEzKoaj/'>自由手工绘图</a>。
</iframe>


### <a name="customizing-drawing-options"></a>自定义绘图选项

前面的示例演示了如何在实例化绘图管理器时自定义绘图选项。 还可以使用 `drawingManager.setOptions()` 函数设置“绘图管理器”选项。 下面是一个工具，用于测试使用 setOptions 函数对绘图管理器的所有选项的自定义情况。

<br/>

<iframe height="685" title="自定义绘图管理器" src="//codepen.io/azuremaps/embed/LYPyrxR/?height=600&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/LYPyrxR/'>获取形状数据</a>。
</iframe>


### <a name="put-a-shape-into-edit-mode"></a>将形状置于编辑模式

通过将现有形状传入绘图管理器的 `edit` 函数，以编程方式将现有形状置于编辑模式。 如果形状是 GeoJSON 特征，则使用 `atls.Shape` 类将其包装，然后再将其传入。

若要以编程方式使形状退出编辑模式，请将绘图管理器模式设置为 `idle`。

```javascript
//If you are starting with a GeoJSON feature, wrap it with the atlas.Shape class.
var feature = { 
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [0,0]
        },
    "properties":  {}
};

var shape = new atlas.Shape(feature);

//Pass the shape into the edit function of the drawing manager.
drawingManager.edit(shape);

//Later, to programmatically take shape out of edit mode, set mode to idle. 
drawingManager.setOptions({ mode: 'idle' });
```

> [!NOTE]
> 将形状传入绘图管理器的 `edit` 函数时，形状将添加到由绘图管理器维护的数据源中。 如果此形状以前在另一个数据源中，则会从该数据源中删除它。

若要将形状添加到绘图管理器中供最终用户查看和编辑，但又不想以编程方式将其置于编辑模式，请从绘图管理器中检索数据源并将形状添加到其中。

```javascript
//The shape(s) you want to add to the drawing manager so 
var shape = new atlas.Shape(feature);

//Retrieve the data source from the drawing manager.
var source = drawingManager.getSource();

//Add your shape.
source.add(shape);

//Alternatively, load in a GeoJSON feed using the sources importDataFromUrl function.
source.importDataFromUrl('yourFeatures.json');
```

下表列出了不同类型的形状特征支持的编辑类型。

| 形状特征 | 编辑点 | 旋转 | 删除形状 |
|---------------|:-----------:|:------:|:------------:|
| 点         | ✓           |        | ✓           |
| LineString    | ✓           | ✓      | ✓           |
| Polygon       | ✓           | ✓      | ✓           |
| MultiPoint    |             | ✓      | ✓           |
| MultiLineString |           | ✓      | ✓           |
| MultiPolygon  |             | ✓      | ✓           |
| 圆形        | ✓           |        | ✓           |
| Rectangle     | ✓           | ✓      | ✓           |

## <a name="next-steps"></a>后续步骤

了解如何使用绘图工具模块的其他功能：

> [!div class="nextstepaction"]
> [添加绘图工具栏](map-add-drawing-toolbar.md)

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
> [绘图管理器](/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager)

> [!div class="nextstepaction"]
> [绘图工具栏](/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar)
