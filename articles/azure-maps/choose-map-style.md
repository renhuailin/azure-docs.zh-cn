---
title: 更改 Azure Maps Web Map Control 的样式
description: 了解如何更改地图的样式和选项。 请参阅如何在 Azure Maps 中为地图添加样式选取器控件，以便用户可以切换使用不同的样式。
author: anastasia-ms
ms.author: v-stharr
ms.date: 04/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
ms.custom: devx-track-js
ms.openlocfilehash: 9ea39da9b509d89ee431e9aa992e7312c86558d0
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121747761"
---
# <a name="change-the-style-of-the-map"></a>更改地图的样式

地图控件支持多种不同的地图[样式选项](/javascript/api/azure-maps-control/atlas.styleoptions)和[基本地图样式](supported-map-styles.md)。 在初始化地图控件时，可以设置所有样式。 或者，也可以使用地图控件的 `setStyle` 函数来设置样式。 本文介绍如何使用这些样式选项来自定义地图的外观。 此外，还将学习如何在地图中实施样式选取器控件。 样式选取器控件允许用户在不同的基本样式之间切换。

## <a name="set-map-style-options"></a>设置地图样式选项

可以在 web 控件初始化期间设置样式选项。 或者，可以通过调用地图控件的 `setStyle` 函数来更新样式选项。 要查看所有可用的样式选项，请参阅[样式选项](/javascript/api/azure-maps-control/atlas.styleoptions)。

```javascript
//Set the style options when creating the map.
var map = new atlas.Map('map', {
    renderWorldCopies: false,
    showBuildingModels: false,
    showLogo: true,
    showFeedbackLink: true,
    style: 'road'

    //Additional map options.
};

//Update the style options at anytime using `setStyle` function.
map.setStyle({
    renderWorldCopies: true,
    showBuildingModels: true,
    showLogo: false,
    showFeedbackLink: false
});
```

以下工具显示不同样式选项如何更改地图的呈现方式。 若要查看三维建筑物，请缩放到主要城市。

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="地图样式选项" src="https://codepen.io/azuremaps/embed/eYNMjPb?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
请参阅画笔 <a href='https://codepen.io/azuremaps/pen/eYNMjPb'>通过 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 在 <a href='https://codepen.io'>CodePen</a> 上设置地图样式选项</a>。
</iframe>

## <a name="set-a-base-map-style"></a>设置基本地图样式

还可以使用 Web SDK 中提供的一种[基本地图样式](supported-map-styles.md)来初始化地图控件。 然后，可以使用 `setStyle` 函数以不同的地图样式来更新基本样式。

### <a name="set-a-base-map-style-on-initialization"></a>在初始化时设置基本地图样式

在初始化过程中可以设置地图控件的基本样式。 在以下代码中，地图控件的 `style` 选项设置为[`grayscale_dark`基本地图样式](supported-map-styles.md#grayscale_dark)。  

```javascript
var map = new atlas.Map('map', {
    style: 'grayscale_dark',

    //Additional map options
);
```

<br/>

<iframe height='500' scrolling='no' title='设置地图加载的样式' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅画笔 <a href='https://codepen.io/azuremaps/pen/WKOQRq/'>通过 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 在 <a href='https://codepen.io'>CodePen</a> 上设置地图加载的样式</a>。
</iframe>

### <a name="update-the-base-map-style"></a>更新基地图样式

可以使用 `setStyle` 函数更新基本地图样式，并将 `style` 选项设置为更改为其他基本地图样式或添加其他样式选项。

```javascript
map.setStyle({ style: 'satellite' });
```

在以下代码中，在加载地图实例之后，地图样式将使用 [setStyle](/javascript/api/azure-maps-control/atlas.map#setstyle-styleoptions-) 函数从 `grayscale_dark` 更新为 `satellite`。

<br/>

<iframe height='500' scrolling='no' title='更新样式' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅画笔 <a href='https://codepen.io/azuremaps/pen/yqXYzY/'>通过 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 在 <a href='https://codepen.io'>CodePen</a> 上更新样式</a>。
</iframe>

## <a name="add-the-style-picker-control"></a>添加样式选取器控件

样式选取器控件提供了一个带有飞出面板的易用按钮，最终用户可以使用该按钮在基本样式之间切换。

样式选择器具有两个不同的布局选项：`icon` 和 `list`。 此外，样式选取器支持选择两个不同的样式选取器控件 `style` 选项：`light` 和 `dark`。 在此示例中，样式选取器使用 `icon` 布局，并以图标形式显示基本地图样式的选择列表。 样式控件选取器包括以下基本样式集：`["road", "grayscale_light", "grayscale_dark", "night", "road_shaded_relief"]`。 有关样式选取器控件选项的详细信息，请参阅[样式控件选项](/javascript/api/azure-maps-control/atlas.stylecontroloptions)。

下图显示了以 `icon` 布局显示的样式选取器控件。

:::image type="content" source="./media/choose-map-style/style-picker-icon-layout.png" alt-text="样式选取器图标布局":::

下图显示了以 `list` 布局显示的样式选取器控件。

:::image type="content" source="./media/choose-map-style/style-picker-list-layout.png" alt-text="样式选取器列表布局":::

> [!IMPORTANT]
> 默认情况下，样式选取器控件会列出 Azure Maps S0 定价层下可用的所有样式。 如果要减少此列表中的样式数量，可将希望在列表中显示的样式数组传递到样式选取器的 `mapStyle` 选项中。 如果使用的是 Gen 1 (S1) 或 Gen 2 定价层并想要显示所有可用样式，请将样式选取器的 `mapStyles` 选项设置为 `"all"`。

以下代码演示了如何替代默认 `mapStyles` 基本样式列表。 在此示例中，我们要设置 `mapStyles` 选项以列出想要通过样式选取器控件显示的基本样式。

<br/>

<iframe height='500' scrolling='no' title='添加样式选取器' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅画笔 <a href='https://codepen.io/azuremaps/pen/OwgyvG/'>通过 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 在 <a href='https://codepen.io'>CodePen</a> 上添加样式选取器</a>。
</iframe>

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [Map](/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [StyleOptions](/javascript/api/azure-maps-control/atlas.styleoptions)

> [!div class="nextstepaction"]
> [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol)

> [!div class="nextstepaction"]
> [StyleControlOptions](/javascript/api/azure-maps-control/atlas.stylecontroloptions)

有关可向地图添加的更多代码示例，请参阅以下文章：

> [!div class="nextstepaction"]
> [添加地图控件](map-add-controls.md)

> [!div class="nextstepaction"]
> [添加符号层](map-add-pin.md)

> [!div class="nextstepaction"]
> [添加气泡层](map-add-bubble-layer.md)
