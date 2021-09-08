---
title: 从地图上的形状获取数据 | Microsoft Azure Maps
description: 本文介如何使用 Microsoft Azure Maps Web SDK 获取在地图上绘制的形状数据。
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
ms.custom: devx-track-js
ms.openlocfilehash: ae9683140eb1a3faf685bb3ffe2ee299313eabe3
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123434999"
---
# <a name="get-shape-data"></a>获取形状数据

本文介绍如何获取在地图上绘制的形状数据。 我们在[绘图管理器](/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager#getsource--)中使用 drawingManager.getSource() 函数。 当你想提取绘制形状的 geojson 数据并在其他地方使用时，可以使用多种方案。  


## <a name="get-data-from-drawn-shape"></a>从绘制形状获取数据

以下函数获取绘制形状的源数据，并将其输出到屏幕上。 

```javascript
function getDrawnShapes() {
    var source = drawingManager.getSource();

    document.getElementById('CodeOutput').value = JSON.stringify(source.toJson(), null, '    ');
}
```

下面是完整的运行代码示例，你可以在其中绘制形状以测试功能：

<br/>

<iframe height="686" title="获取形状数据" src="//codepen.io/azuremaps/embed/xxKgBVz/?height=265&theme-id=0&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/xxKgBVz/'>获取形状数据</a>。
</iframe>


## <a name="next-steps"></a>后续步骤

了解如何使用绘图工具模块的其他功能：

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