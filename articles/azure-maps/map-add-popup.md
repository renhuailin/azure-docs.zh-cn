---
title: 向地图上的点添加弹出窗口 | Microsoft Azure Maps
description: 了解 Azure Maps 中的弹出窗口、弹出窗口模板和弹出窗口事件。 请参阅如何在地图上向某个点添加弹出窗口，以及如何重用和自定义弹出窗口。
author: anastasia-ms
ms.author: v-stharr
ms.date: 02/27/2020
ms.topic: conceptual
ms.service: azure-maps
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 80672ca6a4c8837bcc75bde2a673bb45058ebcf9
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123425133"
---
# <a name="add-a-popup-to-the-map"></a>向地图添加弹出窗口

本文展示了如何向地图上的点添加弹出窗口。

## <a name="understand-the-code"></a>了解代码

以下代码使用符号层向地图添加一个具有 `name` 和 `description` 属性的点特征。 创建了 [Popup 类](/javascript/api/azure-maps-control/atlas.popup)实例，但未显示。 向符号层中添加鼠标事件，以触发弹出窗口打开和关闭。 悬停标记符号时，将使用标记位置更新弹出窗口的 `position` 属性，并使用用于包装要悬停的点特征的 `name` 和 `description` 属性的一些 HTML 来更新 `content` 选项。 然后，使用 `open` 函数在地图上显示弹出窗口。

```javascript
//Define an HTML template for a custom popup content laypout.
var popupTemplate = '<div class="customInfobox"><div class="name">{name}</div>{description}</div>';

//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

dataSource.add(new atlas.data.Feature(new atlas.data.Point([-122.1333, 47.63]), {
  name: 'Microsoft Building 41', 
  description: '15571 NE 31st St, Redmond, WA 98052'
}));

//Create a layer to render point data.
var symbolLayer = new atlas.layer.SymbolLayer(dataSource);

//Add the polygon and line the symbol layer to the map.
map.layers.add(symbolLayer);

//Create a popup but leave it closed so we can update it and display it later.
popup = new atlas.Popup({
  pixelOffset: [0, -18],
  closeButton: false
});

//Add a hover event to the symbol layer.
map.events.add('mouseover', symbolLayer, function (e) {
  //Make sure that the point exists.
  if (e.shapes && e.shapes.length > 0) {
    var content, coordinate;
    var properties = e.shapes[0].getProperties();
    content = popupTemplate.replace(/{name}/g, properties.name).replace(/{description}/g, properties.description);
    coordinate = e.shapes[0].getCoordinates();

    popup.setOptions({
      //Update the content of the popup.
      content: content,

      //Update the popup's position with the symbol's coordinate.
      position: coordinate

    });
    //Open the popup.
    popup.open(map);
  }
});

map.events.add('mouseleave', symbolLayer, function (){
  popup.close();
});
```

下面是上述功能的完整运行代码示例。

<br/>

<iframe height='500' scrolling='no' title='使用 Azure Maps 添加弹出窗口' src='//codepen.io/azuremaps/embed/MPRPvz/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/MPRPvz/'>Add a pop up using Azure Maps</a>（使用 Azure Maps 添加弹出窗口）。
</iframe>

## <a name="reusing-a-popup-with-multiple-points"></a>重复使用具有多个点的弹出窗口

在某些情况下，最好的方法是创建一个弹出窗口并重用它。 例如，可能有大量点，并且希望一次只显示一个弹出窗口。 通过重用弹出窗口，应用程序创建的 DOM 元素的数量将显著减少，以提供更好的性能。 以下示例将创建 3 点特征。 单击其中任何一个特征时，都会显示一个弹出窗口，其中包含该点特征的内容。

<br/>

<iframe height='500' scrolling='no' title='重复使用具有多个固定项的弹出窗口' src='//codepen.io/azuremaps/embed/rQbjvK/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true'>请参阅 <a href='https://codepen.io'>CodePen</a> 上 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 的<a href='https://codepen.io/azuremaps/pen/rQbjvK/'>重复使用具有多个固定项的弹出窗口</a> Pen。
</iframe>

## <a name="customizing-a-popup"></a>自定义弹出窗口

默认情况下，弹出窗口的背景色为白色，底部有一个指针箭头，右上角有一个“关闭”按钮。 以下示例使用弹出窗口的 `fillColor` 选项将背景色更改为黑色。 将 `CloseButton` 选项设置为 false，将删除“关闭”按钮。 弹出窗口的 HTML 内容从弹出窗口边缘填充 10 像素。 文本变为白色，因此在黑色背景色上的显示效果较好。  

<br/>

<iframe height="500" scrolling="no" title="自定义弹出窗口" src="//codepen.io/azuremaps/embed/ymKgdg/?height=500&theme-id=0&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
请参阅 <a href='https://codepen.io'>CodePen</a> 上 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/ymKgdg/'>Customized Popup</a>（自定义弹出窗口）。
</iframe>

## <a name="add-popup-templates-to-the-map"></a>向地图中添加弹出窗口模板

弹出窗口模板可用于轻松为弹出窗口创建数据驱动的布局。 以下各部分介绍了如何使用各种弹出窗口模板来使用特征属性生成格式化内容。

> [!NOTE]
> 默认情况下，使用弹出窗口模板呈现的所有内容都将作为安全特征在 iframe 内进行沙盒处理。 但存在一些限制：
>
> - 所有脚本、窗体、指针锁定和顶部导航功能都处于禁用状态。 单击时，允许链接在新选项卡中打开。 
> - 不支持 iframe `srcdoc` 参数的旧版本浏览器会受到限制，仅能呈现少量内容。
> 
> 如果信任加载到弹出窗口中的数据，并且可能希望加载到弹出窗口的脚本可以访问应用程序，则可以通过将弹出窗口模板的 `sandboxContent` 选项设置为 false 来禁用此设置。 

### <a name="string-template"></a>String 模板

String 模板会将占位符替换为特征属性的值。 无需为此特征属性赋予类型为 String 的值。 例如，`value1` 将保留一个整数。 然后，将这些值传递给 `popupTemplate` 的内容属性。 

`numberFormat` 选项指定要显示的数量的格式。 如果未指定 `numberFormat`，则代码将使用弹出窗口模板日期格式。 `numberFormat` 选项使用 [Number.toLocaleString](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString) 函数对数字进行格式设置。 若要设置较大的数的格式，请考虑对 `numberFormat` 选项使用 [NumberFormat.format](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/NumberFormat/format) 中的函数。 例如，以下代码片段使用 `maximumFractionDigits` 将小数位数限制为两位。

> [!Note]
> String 模板仅可以使用一种方法呈现图像。 首先，String 模板需要包含一个图像标记。 传递给图像标记的值应为指向图像的 URL。 其次，String 模板需要将 `HyperLinkFormatOptions` 中的 `isImage` 设置为 true。 `isImage` 选项指定超链接用于图像，超链接将加载到图像标记中。 单击超链接时，将打开该图像。

```javascript
var templateOptions = {
  content: 'This template uses a string template with placeholders.<br/><br/> - Value 1 = {value1}<br/> - Value 2 = {value2/subValue}<br/> - Array value [2] = {arrayValue/2}',
  numberFormat: {
    maximumFractionDigits: 2
  }
};

var feature = new atlas.data.Feature(new atlas.data.Point([0, 0]), {
    title: 'Template 1 - String template',
    value1: 1.2345678,
    value2: {
        subValue: 'Pizza'
    },
    arrayValue: [3, 4, 5, 6]
});

var popup = new atlas.Popup({
  content: atlas.PopupTemplate.applyTemplate(feature.properties, templateOptions),
  position: feature.geometry.coordinates
});
```

### <a name="propertyinfo-template"></a>PropertyInfo 模板

PropertyInfo 模板显示特征的可用属性。 `label` 选项指定要显示给用户的文本。 如果未指定 `label`，则将显示超链接。 如果超链接为图像，则将显示分配给“alt”标记的值。 `dateFormat` 指定日期格式，如果未指定日期格式，则日期将呈现为字符串。 `hyperlinkFormat` 选项呈现可单击的链接，同样，`email` 选项可用于呈现可单击的电子邮件地址。

在 PropertyInfo 模板向最终用户显示这些属性之前，它会以递归方式检查是否确实为该特征定义了属性。 它还会忽略显示样式和标题属性。 例如，它不会显示 `color`、`size`、`anchor`、`strokeOpacity` 和 `visibility`。 因此，在后端完成属性路径检查后，PropertyInfo 模板使用表格式显示内容。

```javascript
var templateOptions = {
  content: [
    {
        propertyPath: 'createDate',
        label: 'Created Date'
    },
    {
        propertyPath: 'dateNumber',
        label: 'Formatted date from number',
        dateFormat: {
          weekday: 'long',
          year: 'numeric',
          month: 'long',
          day: 'numeric',
          timeZone: 'UTC',
          timeZoneName: 'short'
        }
    },
    {
        propertyPath: 'url',
        label: 'Code samples',
        hideLabel: true,
        hyperlinkFormat: {
          lable: 'Go to code samples!',
          target: '_blank'
        }
    },
    {
        propertyPath: 'email',
        label: 'Email us',
        hideLabel: true,
        hyperlinkFormat: {
          target: '_blank',
          scheme: 'mailto:'
        }
    }
  ]
};

var feature = new atlas.data.Feature(new atlas.data.Point([0, 0]), {
    title: 'Template 2 - PropertyInfo',
    createDate: new Date(),
    dateNumber: 1569880860542,
    url: 'https://aka.ms/AzureMapsSamples',
    email: 'info@microsoft.com'
}),

var popup = new atlas.Popup({
  content: atlas.PopupTemplate.applyTemplate(feature.properties, templateOptions),
  position: feature.geometry.coordinates
});
```

### <a name="multiple-content-templates"></a>多个内容模板

特征还可以结合使用 String 模板和 PropertyInfo 模板来显示内容。 在这种情况下，String 模板在白色背景色上呈现占位符值。  而且，PropertyInfo 模板将在表中呈现全宽度图像。 此示例中的属性与我们在前面的示例中介绍的属性类似。

```javascript
var templateOptions = {
  content: [
    'This template has two pieces of content; a string template with placeholders and a array of property info which renders a full width image.<br/><br/> - Value 1 = {value1}<br/> - Value 2 = {value2/subValue}<br/> - Array value [2] = {arrayValue/2}',
    [{
      propertyPath: 'imageLink',
      label: 'Image',
      hideImageLabel: true,
      hyperlinkFormat: {
        isImage: true
      }
    }]
  ],
  numberFormat: {
    maximumFractionDigits: 2
  }
};

var feature = new atlas.data.Feature(new atlas.data.Point([0, 0]), {
    title: 'Template 3 - Multiple content template',
    value1: 1.2345678,
    value2: {
    subValue: 'Pizza'
    },
    arrayValue: [3, 4, 5, 6],
    imageLink: 'https://azuremapscodesamples.azurewebsites.net/common/images/Pike_Market.jpg'
});

var popup = new atlas.Popup({
  content: atlas.PopupTemplate.applyTemplate(feature.properties, templateOptions),
  position: feature.geometry.coordinates
});
```

### <a name="points-without-a-defined-template"></a>没有定义的模板的点

如果没有将弹出窗口模板定义为 String 模板、PropertyInfo 模板或二者的组合，则将使用默认设置。 如果 `title` 和 `description` 是分配的唯一属性，则弹出窗口模板会显示白色背景色，右上角有一个“关闭”按钮。 在中小型屏幕上，它会在底部显示一个箭头。 默认设置在表中显示除 `title` 和 `description` 之外的所有其他属性。 即使回退到默认设置，也仍可通过编程方式操作弹出窗口模板。 例如，用户可以关闭超链接检测，而默认设置仍适用于其他属性。

在 CodePen 中单击地图上的点。 以下每个弹出窗口模板在地图上都有一个对应的点：String 模板、PropertyInfo 模板和多个内容模板。 还可以使用三个点来显示模板如何使用默认设置进行呈现。

<br/>

<iframe height='500' scrolling='no' title='PopupTemplates' src='//codepen.io/azuremaps/embed/dyovrzL/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true'>请参阅 <a href='https://codepen.io'>CodePen</a> 上 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/dyovrzL/'>PopupTemplates</a>。
</iframe>

## <a name="reuse-popup-template"></a>重复使用弹出窗口模板

与重复使用弹出窗口类似，你可以重复使用弹出窗口模板。 当你想要同时为多个点仅显示一个弹出窗口模板时，此方法非常有用。 通过重用弹出窗口模板，应用程序创建的 DOM 元素的数量会减少，从而提高应用程序的性能。 以下示例对三个点使用同一个弹出窗口模板。 单击其中任何一个特征时，都会显示一个弹出窗口，其中包含该点特征的内容。

<br/>

<iframe height='500' scrolling='no' title='ReusePopupTemplate' src='//codepen.io/azuremaps/embed/WNvjxGw/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true'>请参阅 <a href='https://codepen.io'>CodePen</a> 上 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/WNvjxGw/'>ReusePopupTemplate</a>。
</iframe>

## <a name="popup-events"></a>弹出窗口事件

可以打开、关闭和拖动弹出窗口。 Popup 类提供事件以帮助开发人员响应这些事件。 以下示例重点介绍当用户打开、关闭或拖动弹出窗口时触发的事件。 

<br/>

<iframe height="500" scrolling="no" title="弹出窗口事件" src="//codepen.io/azuremaps/embed/BXrpvB/?height=500&theme-id=0&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
请参阅 <a href='https://codepen.io'>CodePen</a> 上 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/BXrpvB/'>Popup events</a>（弹出窗口事件）。
</iframe>

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [Popup](/javascript/api/azure-maps-control/atlas.popup)

> [!div class="nextstepaction"]
> [PopupOptions](/javascript/api/azure-maps-control/atlas.popupoptions)

> [!div class="nextstepaction"]
> [PopupTemplate](/javascript/api/azure-maps-control/atlas.popuptemplate)

有关完整代码示例，请参阅以下精彩文章：

> [!div class="nextstepaction"]
> [添加符号层](./map-add-pin.md)

> [!div class="nextstepaction"]
> [添加 HTML 标记](./map-add-custom-html.md)

> [!div class="nextstepaction"]
> [添加线条层](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [添加多边形层](map-add-shape.md)