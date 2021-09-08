---
title: 使用 Azure Maps 创建地图 | Microsoft Azure Maps
description: 了解如何使用 Azure Maps Web SDK 将地图添加到网页。 了解动画、样式、照相机、服务和用户交互选项。
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: azure-maps
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 137381e3e08f9f2096467171e42033d2ffbd5b1b
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123432713"
---
# <a name="create-a-map"></a>创建地图

本文展示了如何创建地图并将地图制成动画。  

## <a name="loading-a-map"></a>加载地图

若要加载地图，请创建 [Map 类](/javascript/api/azure-maps-control/atlas.map)的新实例。 初始化地图时，传递 DIV 元素 ID 以呈现地图，并传递一组要在加载地图时使用的选项。 如果未在 `atlas` 命名空间上指定默认身份验证信息，则加载地图时需要在地图选项中指定此信息。 地图以异步方式加载多个资源以提高性能。 因此，在创建地图实例后，将 `ready` 或 `load` 事件附加到该地图，然后将与地图交互的任何其他代码添加到事件处理程序。 当地图加载的资源足够以编程方式交互时，就会触发 `ready` 事件。 在初始地图视图完全加载完成后触发 `load` 事件。 

<br/>

<iframe height="500" scrolling="no" title="基本地图负载" src="//codepen.io/azuremaps/embed/rXdBXx/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/rXdBXx/'>基础地图加载</a>。
</iframe>

> [!TIP]
> 可以在同一页上加载多个地图。 同一页上的多个地图可能使用相同或不同的身份验证和语言设置。

## <a name="show-a-single-copy-of-the-world"></a>显示单个世界副本

当在宽屏幕上缩小地图时，多个世界副本将水平显示。 此选项对于某些方案很有用，但对其他应用程序而言需要查看单一副本。 此行为通过将地图 `renderWorldCopies` 选项设置为 `false` 来实现。

<br/>

<iframe height="500" scrolling="no" title="renderWorldCopies = false" src="//codepen.io/azuremaps/embed/eqMYpZ/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/eqMYpZ/'>renderWorldCopies = false</a>。
</iframe>


## <a name="map-options"></a>地图选项

创建地图时，有几种不同类型的选项，可以传入这些选项来自定义地图的工作方式，如下所示。

- [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions) 和 [CameraBoundOptions](/javascript/api/azure-maps-control/atlas.cameraboundsoptions) 用于指定地图应显示的区域。
- [ServiceOptions](/javascript/api/azure-maps-control/atlas.serviceoptions) 用于指定地图与支持地图的服务进行交互的方式。
- [StyleOptions](/javascript/api/azure-maps-control/atlas.styleoptions) 用于指定地图的样式和呈现。
- [UserInteractionOptions](/javascript/api/azure-maps-control/atlas.userinteractionoptions) 用于指定当用户与地图进行交互时，地图的访问方式。 

使用 `setCamera`、 `setServiceOptions`、 `setStyle` 和 `setUserInteraction` 函数加载地图后，还可以更新这些选项。 

## <a name="controlling-the-map-camera"></a>控制地图相机

可以通过两种方法使用地图照相机来设置显示的地图区域。 可以在加载地图时设置相机选项。 或者，可以在地图加载后随时调用 `setCamera` 选项，以编程方式更新地图视图。  

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>设置相机

地图相机控制地图画布视区中显示的内容。 照相机选项可以在初始化或传递到地图 `setCamera` 函数时传递给地图选项。

```javascript
//Set the camera options when creating the map.
var map = new atlas.Map('map', {
    center: [-122.33, 47.6],
    zoom: 12

    //Additional map options.
};

//Update the map camera at anytime using setCamera function.
map.setCamera({
    center: [-110, 45],
    zoom: 5 
});
```

在下列代码中，创建了一个 [Map 对象](/javascript/api/azure-maps-control/atlas.map)并设置了中心和缩放选项。 中心和缩放级别等地图属性是 [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions) 的一部分。

<br/>

<iframe height='500' scrolling='no' title='通过 CameraOptions 创建地图' src='//codepen.io/azuremaps/embed/qxKBMN/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true'>请参阅 <a href='https://codepen.io'>CodePen</a> 上 Azure Location Based Services (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>通过  创建地图`CameraOptions` </a>。
</iframe>

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>设置相机边界

边界框可用于更新地图相机。 如果边界框用点数据计算，通常可以采取一种有用的方法，在相机选项中指定像素填充值，以考虑图标大小。 这有助于确保点不会落在地图视区的边缘。

```javascript
map.setCamera({
    bounds: [-122.4, 47.6, -122.3, 47.7],
    padding: 10
});
```

在下列代码中，通过 `new atlas.Map()` 构造了 [Map 对象](/javascript/api/azure-maps-control/atlas.map)。 可以通过 Map 类的 [setCamera](/javascript/api/azure-maps-control/atlas.map) 函数定义地图属性，例如 `CameraBoundsOptions`。 边界和填充属性是使用 `setCamera` 设置的。

<br/>

<iframe height='500' scrolling='no' title='通过 CameraBoundsOptions 创建地图' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true'>请参阅 <a href='https://codepen.io'>CodePen</a> 上 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>通过  创建地图`CameraBoundsOptions` </a>。
</iframe>

### <a name="animate-map-view"></a>将地图视图制成动画

设置地图的相机选项时，还可以设置[动画选项](/javascript/api/azure-maps-control/atlas.animationoptions)。 这些选项指定动画的类型和移动照相机所需的持续时间。

```javascript
map.setCamera({
    center: [-122.33, 47.6],
    zoom: 12,
    duration: 1000,
    type: 'fly'  
});
```

在下面的代码中，第一个代码块创建地图并设置了输入和缩放地图样式。 第二个代码块为动画按钮创建一个单击事件处理程序。 单击此按钮时，将调用 `setCamera` 函数并为 [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions) 和 [AnimationOptions](/javascript/api/azure-maps-control/atlas.animationoptions) 提供一些随机值。

<br/>

<iframe height='500' scrolling='no' title='将地图视图制成动画' src='//codepen.io/azuremaps/embed/WayvbO/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/WayvbO/'>Animate Map View</a>（将地图视图制成动画）。
</iframe>

## <a name="request-transforms"></a>请求转换

有时，能修改地图控件发出的 HTTP 请求会很有用。 例如：

- 向图块请求添加其他标头。 此操作通常针对密码保护的服务。
- 修改 URL 以通过代理服务运行请求。

地图的 [服务选项](/javascript/api/azure-maps-control/atlas.serviceoptions)具有 `transformRequest` ，可用于在地图提出请求前修改所有请求。 `transformRequest` 选项是一个函数，该函数接受两个参数：一个字符串 URL，及一个表示请求用途的资源类型字符串。 此函数必须返回 [RequestParameters](/javascript/api/azure-maps-control/atlas.requestparameters) 结果。

```JavaScript
transformRequest: (url: string, resourceType: string) => RequestParameters
```

下面的示例演示如何使用此函数将用户名和密码添加为请求的标头来将所有请求更改为大小 `https://example.com`。

```JavaScript
var map = new atlas.Map('myMap', {
    transformRequest: function (url, resourceType) {
        //Check to see if the request is to the specified endpoint.
        if (url.indexOf('https://examples.com') > -1) {
            //Add custom headers to the request.
            return {
                url: url,
                header: {
                    username: 'myUsername',
                    password: 'myPassword'
                }
            };
        }

        //Return the URL unchanged by default.
        return { url: url };
    },

    authOptions: {
        authType: 'subscriptionKey',
        subscriptionKey: '<Your Azure Maps Key>'
    }
});
```

## <a name="try-out-the-code"></a>试用代码

查看代码示例。 你可以在“JS”选项卡中编辑 JavaScript 代码，然后在“结果”选项卡上查看地图视图更改。还可以单击右上角的“在 CodePen 上编辑”，然后在 CodePen 中更改代码  。

<a id="relatedReference"></a>

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [Map](/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions)

> [!div class="nextstepaction"]
> [AnimationOptions](/javascript/api/azure-maps-control/atlas.animationoptions)

请参阅向应用添加功能的代码示例：

> [!div class="nextstepaction"]
> [更改地图的样式](choose-map-style.md)

> [!div class="nextstepaction"]
> [将控件添加到地图](map-add-controls.md)

> [!div class="nextstepaction"]
> [示例代码](/samples/browse/?products=azure-maps)