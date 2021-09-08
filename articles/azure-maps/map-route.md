---
title: 在地图上显示路线方向 | Microsoft Azure Maps
description: 在本文中，你将学习如何使用 Microsoft Azure Maps Web SDK 在地图上显示两个地点之间的路线。
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
ms.custom: codepen
ms.openlocfilehash: c877abf1b00f978a9ad0bdce51234308a5fab651
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123429705"
---
# <a name="show-directions-from-a-to-b"></a>显示从 A 到 B 的路线

本文展示了如何发出路线请求并在地图上显示路线。

可通过两种方法来执行此操作。 第一种方法是通过服务模块查询 [Azure Maps 路线 API](/rest/api/maps/route/getroutedirections)。 第二种方法是使用 [Fetch API](https://fetch.spec.whatwg.org/) 以向 [Azure Maps Route API](/rest/api/maps/route/getroutedirections) 进行搜索请求。 下面将讨论这两种方法。

## <a name="query-the-route-via-service-module"></a>通过服务模块查询路线

<iframe height='500' scrolling='no' title='在地图上显示从 A 到 B 的路线（服务模块）' src='//codepen.io/azuremaps/embed/RBZbep/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/RBZbep/'>在地图上显示从 A 到 B 的路线（服务模块）</a>。
</iframe>

在上面的代码中，第一个块构造一个地图对象，并通过设置身份验证机制来使用访问令牌。 有关说明，可以参阅[创建地图](./map-create.md)。

第二个代码块创建一个 `TokenCredential`，以使用访问令牌验证对 Azure Maps 的 HTTP 请求。 然后它将 `TokenCredential` 传递给 `atlas.service.MapsURL.newPipeline()`，并创建一个 [Pipeline](/javascript/api/azure-maps-rest/atlas.service.pipeline) 实例。 `routeURL` 表示 Azure Maps [Route](/rest/api/maps/route) 操作的 URL。

第二个代码块创建 [DataSource](/javascript/api/azure-maps-control/atlas.source.datasource) 对象并将其添加到地图。

第四个代码块创建起点和终[点](/javascript/api/azure-maps-control/atlas.data.point)对象，并将其添加到 dataSource 对象。

线条是 LineString 的一个[特征](/javascript/api/azure-maps-control/atlas.data.feature)。 [LineLayer](/javascript/api/azure-maps-control/atlas.layer.linelayer) 呈现 [DataSource](/javascript/api/azure-maps-control/atlas.source.datasource) 中包装的线条对象（作为地图中的线条）。 第四个代码块创建线条层并将其添加到地图。 请参阅 [LinestringLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions) 中介绍的线条层属性。

某个[符号层](/javascript/api/azure-maps-control/atlas.layer.symbollayer)使用文本或图标来呈现在 [DataSource](/javascript/api/azure-maps-control/atlas.source.datasource) 中包装的基于点的数据。 文本或图标在地图上渲染为符号。 第五个代码块创建符号层并将其添加到地图。

第六个代码块查询 Azure Maps 路线服务（[服务模块](how-to-use-services-module.md)的一部分）。 RouteURL 的 [getRouteDirections](/javascript/api/azure-maps-rest/atlas.service.routeurl#methods) 方法用于获取起点和终点之间的路线。 然后，系统会使用 `geojson.getFeatures()` 方法从响应中提取 GeoJSON 特性集合，并将其添加到数据源。 然后，它将响应呈现为地图上的路线。 有关向地图添加线条的详细信息，请参阅[在地图上添加线条](map-add-line-layer.md)。

最后一个代码块使用地图的 [setCamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) 属性设置地图的边界。

路线查询、数据源、符号、线层和摄影机边界是在[事件侦听器](/javascript/api/azure-maps-control/atlas.map#events)中创建的。 此代码结构可确保仅在地图完全加载后显示结果。

## <a name="query-the-route-via-fetch-api"></a>通过 Fetch API 查询路线

<iframe height='500' scrolling='no' title='在地图上显示从 A 到 B 的路线' src='//codepen.io/azuremaps/embed/zRyNmP/?height=469&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/zRyNmP/'>Show directions from A to B on a map</a>（在地图上显示从 A 到 B 的方向）。
</iframe>

在上面的代码中，第一个代码块构造一个地图对象，并通过设置身份验证机制来使用访问令牌。 有关说明，可以参阅[创建地图](./map-create.md)。

第二个代码块创建 [DataSource](/javascript/api/azure-maps-control/atlas.source.datasource) 对象并将其添加到地图。

第三个代码块创建路线的起点和终点。 然后，将其添加到数据源。 有关如何使用 [addPins](/javascript/api/azure-maps-control/atlas.map) 的说明，可以参阅[在地图上添加图钉](map-add-pin.md)。

[LineLayer](/javascript/api/azure-maps-control/atlas.layer.linelayer) 呈现 [DataSource](/javascript/api/azure-maps-control/atlas.source.datasource) 中包装的线条对象（作为地图中的线条）。 第四个代码块创建线条层并将其添加到地图。 请参阅 [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions) 中介绍的线条层属性。

某个[符号层](/javascript/api/azure-maps-control/atlas.layer.symbollayer)使用文本或图标来呈现作为符号包装在地图上 [DataSource](/javascript/api/azure-maps-control/atlas.source.datasource) 中的基于点的数据。 第五个代码块创建符号层并将其添加到地图。 请在 [SymbolLayerOptions](/javascript/api/azure-maps-control/atlas.symbollayeroptions) 中参阅符号层的属性。

下一个代码块从起点和终点之间创建 `SouthWest` 和 `NorthEast` 点，并使用地图的 [setCamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) 属性设置地图的边界。

最后一个代码块使用 [Fetch API](https://fetch.spec.whatwg.org/) 向 [Azure Maps Route API](/rest/api/maps/route/getroutedirections) 进行搜索请求。 然后分析响应。 如果响应成功，则将使用纬度和经度信息创建一条直线（通过连接这些点）。 然后，将行数据添加到数据源，以在地图上呈现路线。 有关说明，可以参阅[在地图上添加线条](map-add-line-layer.md)。

路线查询、数据源、符号、线层和摄影机边界是在[事件侦听器](/javascript/api/azure-maps-control/atlas.map#events)中创建的。 重申一下，我们想要确保结果在地图完全加载后显示。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [有关使用路由服务的最佳做法](how-to-use-best-practices-for-search.md)

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [Map](/javascript/api/azure-maps-control/atlas.map)

有关完整代码示例，请参阅以下文章：

> [!div class="nextstepaction"]
> [在地图上显示交通信息](./map-show-traffic.md)

> [!div class="nextstepaction"]
> [与地图交互 - 鼠标事件](./map-events.md)