---
title: 为地图添加对齐网格 | Microsoft Azure Maps
description: 如何使用 Azure Maps Web SDK 为地图添加对齐网格
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/20/2021
ms.topic: conceptual
ms.service: azure-maps
ms.openlocfilehash: 3202461de662e0f789b6c3a6187dcfbe2fa58764
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123439607"
---
# <a name="add-a-snap-grid-to-the-map"></a>为地图添加对齐网格

利用对齐网格，可以更轻松地使用共享的边缘和节点以及更直的线条来绘制形状。 在地图上绘制建筑轮廓或网络路径时，将形状对齐到网格很有用。

对齐网格的分辨率以像素为单位。 网格是正方形的，并且是相对于最接近的整数缩放级别的。 网格在每个缩放级别相对于物理现实世界面积的 2 倍进行缩放。

## <a name="use-a-snap-grid"></a>使用对齐网格

使用 `atlas.drawing.SnapGridManager` 类创建对齐网格，并传入对管理器要连接到的地图的引用。 如果要使网格可见，请将 `showGrid` 选项设置为 `true`。 若要将某个形状对齐到网格，请将其传递到对齐网格管理器 `snapShape` 函数中。 如果要对齐一个位置数组，请将其传递到 `snapPositions` 函数中。

下面的示例在拖动一个 HTML 标记时将它对齐到网格。 绘制工具用于在 `drawingcomplete` 事件触发时将绘制的形状对齐到网格。

<br/>

<iframe height="500" scrolling="no" title="使用对齐网格" src="https://codepen.io/azuremaps/embed/rNmzvXO?default-tab=js%2Cresult" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
请参阅 <a href="https://codepen.io">CodePen</a> 上由 Azure Maps (<a href="https://codepen.io/azuremaps">@azuremaps</a>) 提供的 Pen <a href="https://codepen.io/azuremaps/pen/rNmzvXO"> 使用对齐网格</a>。
</iframe>


## <a name="snap-grid-options"></a>对齐网格选项

以下示例演示了可用于对齐网格管理器的不同自定义选项。 可以通过使用对齐网格管理器 `getGridLayer` 函数来检索基础线层，从而自定义网格线样式。

<br/>

<iframe height="700" scrolling="no" title="对齐网格选项" src="https://codepen.io/azuremaps/embed/RwVZJry?default-tab=result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
请参阅 <a href="https://codepen.io">CodePen</a> 上由 Azure Maps (<a href="https://codepen.io/azuremaps">@azuremaps</a>) 提供的 Pen <a href="https://codepen.io/azuremaps/pen/RwVZJry"> 对齐网格选项</a>。
</iframe>


## <a name="next-steps"></a>后续步骤

了解如何使用绘制工具模块的其他功能：

> [!div class="nextstepaction"]
> [获取形状数据](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [对绘图事件做出反应](drawing-tools-events.md)

> [!div class="nextstepaction"]
> [交互类型和键盘快捷键](drawing-tools-interactions-keyboard-shortcuts.md)