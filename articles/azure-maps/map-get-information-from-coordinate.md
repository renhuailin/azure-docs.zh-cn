---
title: 在地图上显示坐标信息 | Microsoft Azure Maps
description: 了解如何在用户选择坐标时在地图上显示有关地址的信息。
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
ms.custom: codepen, devx-track-js
ms.openlocfilehash: ed7e1cc5de7ba33b2d3ebf8b37d861e432809570
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123430497"
---
# <a name="get-information-from-a-coordinate"></a>从坐标获取信息

本文介绍如何进行反向地址搜索，以显示所单击的弹出位置的地址。

可通过两种方法进行反向地址搜索。 一种方法是通过服务模块查询 [Azure Maps 反向地址搜索 API](/rest/api/maps/search/getsearchaddressreverse)。 另一种方法是使用[提取 API](https://fetch.spec.whatwg.org/) 向 [Azure Maps 反向地址搜索 API](/rest/api/maps/search/getsearchaddressreverse) 发出查找地址的请求。 下面将研究这两种方法。

## <a name="make-a-reverse-search-request-via-service-module"></a>通过服务模块发出反向搜索请求

<iframe height='500' scrolling='no' title='从坐标获取信息（服务模块）' src='//codepen.io/azuremaps/embed/ejEYMZ/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/ejEYMZ/'>Get information from a coordinate (Service Module)</a>（从坐标获取信息[服务模块]）。
</iframe>

在上面的代码中，第一个块构造一个地图对象，并通过设置身份验证机制来使用访问令牌。 有关说明，可以参阅[创建地图](./map-create.md)。

第二个代码块创建一个 `TokenCredential`，以使用访问令牌验证对 Azure Maps 的 HTTP 请求。 然后它将 `TokenCredential` 传递给 `atlas.service.MapsURL.newPipeline()`，并创建一个 [Pipeline](/javascript/api/azure-maps-rest/atlas.service.pipeline) 实例。 `searchURL` 表示 Azure Maps [搜索](/rest/api/maps/search)操作的 URL。

第三个代码块将鼠标光标的样式更新为指针，并创建一个 [popup](/javascript/api/azure-maps-control/atlas.popup#open) 对象。 有关说明，可以参阅[在地图上添加弹出窗口](./map-add-popup.md)。

第四个代码块添加了鼠标单击[事件侦听器](/javascript/api/azure-maps-control/atlas.map#events)。 触发后，它将使用单击点坐标创建搜索查询。 然后，它使用 [getSearchAddressReverse](/javascript/api/azure-maps-rest/atlas.service.searchurl#searchaddressreverse-aborter--geojson-position--searchaddressreverseoptions-) 方法查询[获取搜索地址反向 API](/rest/api/maps/search/getsearchaddressreverse) 以获取坐标地址。 然后使用 `geojson.getFeatures()` 方法从响应中提取 GeoJSON 特性集合。

第五个代码块设置 HTML 弹出内容，以显示单击坐标位置的响应地址。

光标更改、弹出对象和单击事件均在地图的[加载事件侦听器](/javascript/api/azure-maps-control/atlas.map#events)中创建。 此代码结构可确保在检索坐标信息之前完全加载地图。

## <a name="make-a-reverse-search-request-via-fetch-api"></a>通过提取 API 发出反向搜索请求

单击地图，使用 fetch 针对该位置发出反向地理编码请求。

<iframe height='500' scrolling='no' title='从坐标获取信息' src='//codepen.io/azuremaps/embed/ddXzoB/?height=516&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/ddXzoB/'>Get information from a coordinate</a>（从坐标获取信息）。
</iframe>

在上面的代码中，第一个代码块构造一个地图对象，并通过设置身份验证机制来使用访问令牌。 有关说明，可以参阅[创建地图](./map-create.md)。

第二个代码块将鼠标光标的样式更新为指针。 它实例化 [popup](/javascript/api/azure-maps-control/atlas.popup#open) 对象。 有关说明，可以参阅[在地图上添加弹出窗口](./map-add-popup.md)。

第三个代码块针对鼠标单击添加事件侦听器。 单击鼠标时，它使用[提取 API](https://fetch.spec.whatwg.org/) 查询 [Azure Maps 反向地址搜索 API](/rest/api/maps/search/getsearchaddressreverse) 以获取单击的坐标的地址。 对于成功的响应，它会收集所单击位置的地址。 它使用 popup 类的 [setOptions](/javascript/api/azure-maps-control/atlas.popup#setoptions-popupoptions-) 函数定义弹出内容和位置。

光标更改、弹出对象和单击事件均在地图的[加载事件侦听器](/javascript/api/azure-maps-control/atlas.map#events)中创建。 此代码结构可确保在检索坐标信息之前完全加载地图。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [有关使用搜索服务的最佳做法](how-to-use-best-practices-for-search.md)

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [Map](/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [Popup](/javascript/api/azure-maps-control/atlas.popup)

有关完整代码示例，请参阅以下文章：

> [!div class="nextstepaction"]
> [显示从 A 到 B 的路线](./map-route.md)

> [!div class="nextstepaction"]
> [显示交通情况](./map-show-traffic.md)