---
title: Azure Maps Web SDK 中的图像模板 | Microsoft Azure Maps
description: 了解如何通过使用 Azure Maps Web SDK 将图像图标和填充了图案的多边形添加到地图。 查看可用的图像和填充图案模板。
author: anastasia-ms
ms.author: v-stharr
ms.date: 8/6/2019
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 520a8681ff72f6cbf0587baa501b5ece3d5369e7
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123437843"
---
# <a name="how-to-use-image-templates"></a>如何使用图像模板

可以在 Azure Maps Web SDK 中将图像与 HTML 标记和各种层配合使用：

 - 符号层可使用图像图标来呈现地图上的点。 还可以沿着线条路径呈现符号。
 - 可以使用填充图案图像来呈现多边形层。 
 - HTML 标记可以使用图像和其他 HTML 元素来呈现点。

为了确保层具有良好的性能，请在呈现前将图像加载到地图图像子画面资源。 在默认情况下，SymbolLayer 的 [IconOptions](/javascript/api/azure-maps-control/atlas.iconoptions) 会将几个带有一些颜色的标记图像预加载到地图图像子画面。 这些标记图像和其他标记图像会作为 SVG 模板提供。 它们可用于创建具有自定义比例的图像，或用作客户主色和辅色。 总共提供了 42 个图像模板：27 个符号图标和 15 个多边形填充图案。

可以通过使用 `map.imageSprite.createFromTemplate` 函数将图像模板添加到地图图像子画面资源。 此函数允许传入最多 5 个参数；

```javascript
createFromTemplate(id: string, templateName: string, color?: string, secondaryColor?: string, scale?: number): Promise<void>
```

`id` 是你创建的独一无二的标识符。 `id` 会在图像添加到地图图像子画面后被分配到该图像。 在层中使用此标识符来指定要呈现哪个图像资源。 `templateName` 指定要使用哪个图像模板。 `color` 选项设置图像的主色，`secondaryColor` 选项设置图像的辅色。 `scale` 选项会在将图像模板应用到图像子画面之前先对该模板进行缩放。 在图像应用于图像子画面时，它会转换为 PNG。 为了确保呈现清晰，最好在将图像模板添加到子画面之前先将该模板向上扩展，而不是在层中向上扩展。

此函数以异步方式将图像加载到图像子画面。 因此，它会返回一个承诺，你可以等待此函数完成。

下面的代码演示如何从某一个内置模板创建图像并将该图像用于符号层。

```javascript
map.imageSprite.createFromTemplate('myTemplatedIcon', 'marker-flat', 'teal', '#fff').then(function () {

    //Add a symbol layer that uses the custom created icon.
    map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
        iconOptions: {
            image: 'myTemplatedIcon'
        }
    }));
});
```

## <a name="use-an-image-template-with-a-symbol-layer"></a>将图像模板用于符号层

在将图像模板加载到地图图像子画面后，可以通过在 `iconOptions` 的 `image` 选项中引用图像资源 ID 将该图像模板呈现为符号层中的符号。

下面的示例使用带有青色主色和白色辅助色的 `marker-flat` 图像模板来呈现符号层。 

<br/>

<iframe height="500" scrolling="no" title="带有内置图标模板的符号层" src="//codepen.io/azuremaps/embed/VoQMPp/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
请参阅 <a href='https://codepen.io'>CodePen</a> 上的触笔<a href='https://codepen.io/azuremaps/pen/VoQMPp/'>带有内置图标模板的符号层</a>（由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供）。
</iframe>

## <a name="use-an-image-template-along-a-lines-path"></a>沿线条路径使用图像模板

在将图像模板加载到地图图像子画面后，可以将 LineString 添加到数据源，并配合 `lineSpacing` 选项使用符号层，并且可以在 `iconOptions` 的 `image` 选项中引用图像资源的 ID，通过这样的方式来沿线条的路径呈现该图像模板。 

下面的示例使用带有宝蓝色主色和白色辅色的 `car` 图像模板，在地图上呈现粉红色线条并使用符号层。 

<br/>

<iframe height="500" scrolling="no" title="带有内置图标模板的线条层" src="//codepen.io/azuremaps/embed/KOQvJe/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
请参阅 <a href='https://codepen.io'>CodePen</a> 上的触笔<a href='https://codepen.io/azuremaps/pen/KOQvJe/'>带有内置图标模板的线条层</a>（由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供）。
</iframe>

> [!TIP]
> 如果图像模板指向上方，请将符号层的 `rotation` 图标选项设置为 90（如果你希望它指向与线条相同的方向）。

## <a name="use-an-image-template-with-a-polygon-layer"></a>使用带有多边形层的图像模板

在将图像模板加载到地图图像子画面后，可以通过在多边形层的 `fillPattern` 选项中引用图像资源 ID 将该图像模板呈现为该层中的填充图案。

下面的示例使用带有红色主色和透明辅色的 `dot` 图像模板呈现多边形层。  

<br/>

<iframe height="500" scrolling="no" title="用内置图标模板填充多边形" src="//codepen.io/azuremaps/embed/WVMEmz/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
请参阅 <a href='https://codepen.io'>CodePen</a> 上的触笔 <a href='https://codepen.io/azuremaps/pen/WVMEmz/'>带有内置图标模板的填充多边形</a>（由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供）。
</iframe>

> [!TIP]
> 通过设置填充图案的辅色，可以更轻松地看到基础地图仍将提供主要图案。 

## <a name="use-an-image-template-with-an-html-marker"></a>使用带有 HTML 标记的图像模板

可以使用 `altas.getImageTemplate` 函数来检索图像模板，并且图像模板可以用作 HTML 标记的内容。 可以将模板传递到标记的 `htmlContent` 选项中，然后使用 `color`、`secondaryColor` 和 `text` 选项来自定义模板。

下面的示例使用具有红色主色、粉红色辅色和文本值“00”的 `marker-arrow` 模板。

<br/>

<iframe height="500" scrolling="no" title="带有内置图标模板的 HTML 标记" src="//codepen.io/azuremaps/embed/EqQvzq/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
请参阅 <a href='https://codepen.io'>CodePen</a> 上的触笔<a href='https://codepen.io/azuremaps/pen/EqQvzq/'>带有内置图标模板的 HTML 标记</a>（由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供）。
</iframe>


> [!TIP]
> 图像模板也可以在地图外部使用。 getImageTemplate 函数会返回具有占位符（`{color}`、`{secondaryColor}`、`{scale}`、`{text}`）的 SVG 字符串。 请替换这些占位符值，以创建有效的 SVG 字符串。 然后，可以将这个 SVG 字符串直接添加到 HTML DOM，或者也可以将它转换为数据 URI 并将它插入到图像标记中。 例如：
> ```JavaScript
> //Retrieve an SVG template and replace the placeholder values.
> var svg = atlas.getImageTemplate('marker').replace(/{color}/, 'red').replace(/{secondaryColor}/, 'white').replace(/{text}/, '').replace(/{scale}/, 1);
>
> //Convert to data URI for use in image tags.
> var dataUri = 'data:image/svg+xml;base64,' + btoa(svg);
> ```

## <a name="create-custom-reusable-templates"></a>创建自定义可重用模板

如果应用程序使用具有不同图标的相同图标，或者，如果要创建添加其他图像模板的模块，可以从 Azure Maps Web SDK 轻松地添加和检索这些图标。 请对 `atlas` 命名空间使用以下静态函数。

| 名称 | 返回类型 | 说明 | 
|-|-|-|
| `addImageTemplate(templateName: string, template: string, override: boolean)` | | 将自定义 SVG 图像模板添加到 atlas 命名空间。 |
|  `getImageTemplate(templateName: string, scale?: number)`| 字符串 | 按名称检索 SVG 模板。 |
| `getAllImageTemplateNames()` | string[] |  按名称检索 SVG 模板。 |

SVG 图像模板支持以下占位符值：

| 占位符 | 说明 |
|-|-|
| `{color}` | 主色。 | 
| `{secondaryColor}` | 辅色。 | 
| `{scale}` | SVG 图像在添加到地图图像子画面后，会转换为 PNG 图像。 此占位符可用于在模板转换之前对它进行缩放，确保它清晰呈现。 | 
| `{text}` | 在文本与 HTML 标记配合使用时用于呈现文本的位置。 |

下面的示例演示如何获取 SVG 模板，并将它添加到 Azure Maps Web SDK 作为可重用的图标模板。 

<br/>

<iframe height="500" scrolling="no" title="将自定义图标模板添加到 atlas 命名空间" src="//codepen.io/azuremaps/embed/NQyvEX/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
请参阅 <a href='https://codepen.io'>CodePen</a> 上的触笔<a href='https://codepen.io/azuremaps/pen/NQyvEX/'>将自定义图标模板添加到 atlas 命名空间</a>（由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供）。
</iframe>

## <a name="list-of-image-templates"></a>图像模板的列表

下表列出了 Azure Maps Web SDK 中所有目前可用的图像模板。 模板名称位于每个图像上方。 在默认情况下，主色为蓝色，辅色为白色。 为了更易于在白色背景上看清辅色，以下图像将辅色设置为黑色。

符号图标模板

:::row:::
   :::column span="":::
      marker
   :::column-end:::
   :::column span="":::
      粗标记
   :::column-end:::
   :::column span="":::
      圆形标记
   :::column-end:::
   :::column span="":::
      扁平标记
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![标记图标](./media/image-templates/marker.png)
   :::column-end:::
   :::column span="":::
      ![粗标记图标](./media/image-templates/marker-thick.png)
   :::column-end:::
   :::column span="":::
      ![圆形标记图标](./media/image-templates/marker-circle.png)
   :::column-end:::
   :::column span="":::
      ![扁平标记图标](./media/image-templates/marker-flat.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      正方形标记
   :::column-end:::
   :::column span="":::
      正方形标记群
   :::column-end:::
   :::column span="":::
      箭头标记
   :::column-end:::
   :::column span="":::
      球形图钉标记
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![正方形标记图标](./media/image-templates/marker-square.png)
   :::column-end:::
   :::column span="":::
      ![正方形标记群图标](./media/image-templates/marker-square-cluster.png)
   :::column-end:::
   :::column span="":::
      ![箭头标记图标](./media/image-templates/marker-arrow.png)
   :::column-end:::
   :::column span="":::
      ![球形图钉标记图标](./media/image-templates/marker-ball-pin.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      圆角正方形
   :::column-end:::
   :::column span="":::
      圆角正方形标记群
   :::column-end:::
   :::column span="":::
      flag
   :::column-end:::
   :::column span="":::
      旗形三角
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![圆角正方形标记图标](./media/image-templates/marker-square-rounded.png)
   :::column-end:::
   :::column span="":::
      ![圆角正方形标记群图标](./media/image-templates/marker-square-rounded-cluster.png)
   :::column-end:::
   :::column span="":::
      ![旗形图标](./media/image-templates/flag.png)
   :::column-end:::
   :::column span="":::
      ![旗形三角图标](./media/image-templates/flag-triangle.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      三角形
   :::column-end:::
   :::column span="":::
      粗三角
   :::column-end:::
   :::column span="":::
      向上三角箭头
   :::column-end:::
   :::column span="":::
      向左三角箭头
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![三角形图标](./media/image-templates/triangle.png)
   :::column-end:::
   :::column span="":::
      ![粗三角图标](./media/image-templates/triangle-thick.png)
   :::column-end:::
   :::column span="":::
      ![向上三角箭头图标](./media/image-templates/triangle-arrow-up.png)
   :::column-end:::
   :::column span="":::
      ![向左三角箭头图标](./media/image-templates/triangle-arrow-left.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      六边形
   :::column-end:::
   :::column span="":::
      粗六边形
   :::column-end:::
   :::column span="":::
      圆角六边形
   :::column-end:::
   :::column span="":::
      粗圆角六边形
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![六边形图标](./media/image-templates/hexagon.png)
   :::column-end:::
   :::column span="":::
      ![粗六边形图标](./media/image-templates/hexagon-thick.png)
   :::column-end:::
   :::column span="":::
      ![圆角六边形图标](./media/image-templates/hexagon-rounded.png)
   :::column-end:::
   :::column span="":::
      ![粗圆角六边形图标](./media/image-templates/hexagon-rounded-thick.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      固定
   :::column-end:::
   :::column span="":::
      圆形图钉
   :::column-end:::
   :::column span="":::
      圆角正方形
   :::column-end:::
   :::column span="":::
      粗圆角正方形
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![“固定”图标](./media/image-templates/pin.png)
   :::column-end:::
   :::column span="":::
      ![圆形图钉图标](./media/image-templates/pin-round.png)
   :::column-end:::
   :::column span="":::
      ![圆角正方形图标](./media/image-templates/rounded-square.png)
   :::column-end:::
   :::column span="":::
      ![粗圆角正方形图标](./media/image-templates/rounded-square-thick.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      向上箭头
   :::column-end:::
   :::column span="":::
      细向上箭头
   :::column-end:::
   :::column span="":::
      car
   :::column-end:::
   :::column span="":::
      &nbsp;
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![向上箭头图标](./media/image-templates/arrow-up.png)
   :::column-end:::
   :::column span="":::
      ![细向上箭头图标](./media/image-templates/arrow-up-thin.png)
   :::column-end:::
   :::column span="":::
      ![汽车图标](./media/image-templates/car.png)
   :::column-end:::
   :::column span="":::
      &nbsp;
   :::column-end:::
:::row-end:::


多边形填充图案模板

:::row:::
   :::column span="":::
      棋盘
   :::column-end:::
   :::column span="":::
      旋转棋盘
   :::column-end:::
   :::column span="":::
      圆形
   :::column-end:::
   :::column span="":::
      间距圆形
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![棋盘图标](./media/image-templates/checker.png)
   :::column-end:::
   :::column span="":::
      ![旋转棋盘图标](./media/image-templates/checker-rotated.png)
   :::column-end:::
   :::column span="":::
      ![圆形图标](./media/image-templates/circles.png)
   :::column-end:::
   :::column span="":::
      ![间距圆形图标](./media/image-templates/circles-spaced.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      向上斜线
   :::column-end:::
   :::column span="":::
      向下斜线
   :::column-end:::
   :::column span="":::
      向上斜条
   :::column-end:::
   :::column span="":::
      向下斜条
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![向上斜线图标](./media/image-templates/diagonal-lines-up.png)
   :::column-end:::
   :::column span="":::
      ![向下斜线图标](./media/image-templates/diagonal-lines-down.png)
   :::column-end:::
   :::column span="":::
      ![向上斜条图标](./media/image-templates/diagonal-stripes-up.png)
   :::column-end:::
   :::column span="":::
      ![向下斜条图标](./media/image-templates/diagonal-stripes-down.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      网格线
   :::column-end:::
   :::column span="":::
      旋转网格线
   :::column-end:::
   :::column span="":::
      旋转网格条
   :::column-end:::
   :::column span="":::
      x 填充
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![网格线图标](./media/image-templates/grid-lines.png)
   :::column-end:::
   :::column span="":::
      ![旋转网格线图标](./media/image-templates/rotated-grid-lines.png)
   :::column-end:::
   :::column span="":::
      ![旋转网格条图标](./media/image-templates/rotated-grid-stripes.png)
   :::column-end:::
   :::column span="":::
      ![x 填充图标](./media/image-templates/x-fill.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      波浪线
   :::column-end:::
   :::column span="":::
      垂直波浪线
   :::column-end:::
   :::column span="":::
      圆点
   :::column-end:::
   :::column span="":::
      &nbsp;
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![波浪线图标](./media/image-templates/zig-zag.png)
   :::column-end:::
   :::column span="":::
      ![垂直波浪线图标](./media/image-templates/zig-zag-vertical.png)
   :::column-end:::
   :::column span="":::
      ![圆点图标](./media/image-templates/dots.png)
   :::column-end:::
   :::column span="":::
      &nbsp;
   :::column-end:::
:::row-end:::
<br>

预加载的图像图标

地图使用 `marker`、`pin` 和 `pin-round` 模板将一组图标预加载到地图图像子画面。 下表列出了这些图标名称及其颜色值。

| 图标名称 | color | secondaryColor |
|-----------|-------|----------------|
| `marker-black` | `#231f20` | `#ffffff` |
| `marker-blue` | `#1a73aa` | `#ffffff` |
| `marker-darkblue` | `#003963` | `#ffffff` |
| `marker-red` | `#ef4c4c` | `#ffffff` |
| `marker-yellow` | `#f2c851` | `#ffffff` |
| `pin-blue` | `#2072b8` | `#ffffff` |
| `pin-darkblue` | `#003963` | `#ffffff` |
| `pin-red` | `#ef4c4c` | `#ffffff` |
| `pin-round-blue` | `#2072b8` | `#ffffff` |
| `pin-round-darkblue` | `#003963` | `#ffffff` |
| `pin-round-red` | `#ef4c4c` | `#ffffff` |


## <a name="try-it-now-tool"></a>立即试用工具

通过以下工具，可以通过多种方式呈现不同的内置图像模板，并自定义主色、辅色和缩放。

<br/>

<iframe height="500" scrolling="no" title="图标模板选项" src="//codepen.io/azuremaps/embed/NQyaaO/?height=500&theme-id=0&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
请参阅 <a href='https://codepen.io'>CodePen</a> 上的触笔<a href='https://codepen.io/azuremaps/pen/NQyaaO/'>图标模板选项</a>（由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供）。
</iframe>

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [ImageSpriteManager](/javascript/api/azure-maps-control/atlas.imagespritemanager)

> [!div class="nextstepaction"]
> [atlas 命名空间](/javascript/api/azure-maps-control/atlas#functions
)

请参阅以下文章，以获取更多可以使用图像模板的代码示例：

> [!div class="nextstepaction"]
> [添加符号层](map-add-pin.md)

> [!div class="nextstepaction"]
> [添加线条层](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [添加多边形层](map-add-shape.md)

> [!div class="nextstepaction"]
> [添加 HTML 标记](map-add-bubble-layer.md)