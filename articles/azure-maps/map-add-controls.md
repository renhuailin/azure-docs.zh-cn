---
title: 向地图添加控件 | Microsoft Azure Maps
description: 如何在 Microsoft Azure Maps 中向地图添加缩放控件、间距控件、旋转控件和样式选取器。
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
ms.custom: devx-track-js
ms.openlocfilehash: e2029135497efb08bfa2c0d435690c112dea17ed
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123438311"
---
# <a name="add-controls-to-a-map"></a>向地图添加控件

本文展示了如何向地图添加控件。 你还将学习如何创建带有所有控件和[样式选取器](./choose-map-style.md)的地图。

## <a name="add-zoom-control"></a>添加缩放控件

缩放控件添加了用于放大和缩小地图的按钮。下面的代码示例创建了 [ZoomControl](/javascript/api/azure-maps-control/atlas.control.zoomcontrol) 类的实例，并将其添加到地图的右下角。

```javascript
//Construct a zoom control and add it to the map.
map.controls.add(new atlas.control.ZoomControl(), {
    position: 'bottom-right'
});
```

下面是上述功能的完整运行代码示例。

<br/>

<iframe height='500' scrolling='no' title='添加缩放控件' src='//codepen.io/azuremaps/embed/WKOQyN/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 发布的 Pen <a href='https://codepen.io/azuremaps/pen/WKOQyN/'>添加缩放控件</a>。
</iframe>

## <a name="add-pitch-control"></a>添加绕 X 轴旋转控件

间距控件向地图添加了用于相对于水平线倾斜间距的按钮。 下面的代码示例创建了 [PitchControl](/javascript/api/azure-maps-control/atlas.control.pitchcontrol) 类的实例。 它将 PitchControl 添加到地图的右上角。

```javascript
//Construct a pitch control and add it to the map.
map.controls.add(new atlas.control.PitchControl(), {
    position: 'top-right'
});
```

下面是上述功能的完整运行代码示例。

<br/>

<iframe height='500' scrolling='no' title='添加绕 X 轴旋转控件' src='//codepen.io/azuremaps/embed/xJrwaP/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 发布的 Pen <a href='https://codepen.io/azuremaps/pen/xJrwaP/'>添加绕 X 轴旋转控件</a>。
</iframe>

## <a name="add-compass-control"></a>添加指南针控件

罗盘控件添加了用于旋转地图的按钮。 下面的代码示例创建了 [CompassControl](/javascript/api/azure-maps-control/atlas.control.compasscontrol) 类的实例，并将其添加到地图的左下角。

```javascript
//Construct a compass control and add it to the map.
map.controls.add(new atlas.control.CompassControl(), {
    position: 'bottom-left'
});
```

下面是上述功能的完整运行代码示例。

<br/>

<iframe height='500' scrolling='no' title='添加旋转控件' src='//codepen.io/azuremaps/embed/GBEoRb/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 发布的 Pen <a href='https://codepen.io/azuremaps/pen/GBEoRb/'>添加旋转控件</a>。
</iframe>

## <a name="a-map-with-all-controls"></a>包含所有控件的地图

可以将多个控件放入一个数组并同时添加到地图中，然后放置在地图的相同区域，以简化开发。 下面的代码使用这种方法将标准导航控件添加到地图中。

```javascript
map.controls.add([
    new atlas.control.ZoomControl(),
    new atlas.control.CompassControl(),
    new atlas.control.PitchControl(),
    new atlas.control.StyleControl()
], {
    position: "top-right"
});
```

下面的代码示例将缩放控件、罗盘控件、间距控件和样式选取器控件添加到地图的右上角。 注意它们是如何自动堆叠的。 控件对象在脚本中的顺序决定了它们在地图上的显示顺序。 若要更改地图上控件的顺序，则可以更改它们在数组中的顺序。

<br/>

<iframe height='500' scrolling='no' title='包含所有控件的地图' src='//codepen.io/azuremaps/embed/qyjbOM/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 发布的 Pen <a href='https://codepen.io/azuremaps/pen/qyjbOM/'>包含所有控件的地图</a>。
</iframe>

样式选取器控件由 [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol) 类定义。 若要详细了解如何使用样式选取器控件，请参阅[选择地图样式](choose-map-style.md)。

## <a name="customize-controls"></a>自定义控件

下面是用来测试自定义控件的各种选项的工具。

<br/>

<iframe height="700" scrolling="no" title="导航控件选项" src="//codepen.io/azuremaps/embed/LwBZMx/?height=700&theme-id=0&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
请参阅 <a href='https://codepen.io'>CodePen</a> 上的 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) <a href='https://codepen.io/azuremaps/pen/LwBZMx/'>导航控件选项</a>文章。
</iframe>

若要创建自定义导航控件，请创建一个从 `atlas.Control` 类扩展的类，或创建一个 HTML 元素并将其放置在地图 div 的上方。 让此 UI 控件调用地图的 `setCamera` 函数来移动地图。

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [罗盘控件](/javascript/api/azure-maps-control/atlas.control.compasscontrol)

> [!div class="nextstepaction"]
> [PitchControl](/javascript/api/azure-maps-control/atlas.control.pitchcontrol) 

> [!div class="nextstepaction"]
> [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol) 

> [!div class="nextstepaction"]
> [ZoomControl](/javascript/api/azure-maps-control/atlas.control.zoomcontrol) 

有关完整代码，请参阅以下文章：

> [!div class="nextstepaction"]
> [添加图钉](./map-add-pin.md)

> [!div class="nextstepaction"]
> [添加弹出项](./map-add-popup.md)

> [!div class="nextstepaction"]
> [添加线条层](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [添加多边形层](map-add-shape.md)

> [!div class="nextstepaction"]
> [添加气泡层](map-add-bubble-layer.md)
