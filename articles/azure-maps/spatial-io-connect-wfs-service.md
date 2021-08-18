---
title: 连接到 Web 地理要素服务 (WFS) 服务 |Microsoft Azur Maps
description: 了解如何连接到 WFS 服务，然后使用 Azure Maps web SDK 和空间 IO 模块查询 WFS 服务。
author: anastasia-ms
ms.author: v-stharr
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
ms.custom: devx-track-js
ms.openlocfilehash: f50685a2e25d33f7caa614480817ea0040191cf8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121747706"
---
# <a name="connect-to-a-wfs-service"></a>连接到 WFS 服务

Web 地理要素服务 (WFS) 是一项 web 服务，用于查询具有由开放地理空间信息联盟 (OGC) 定义的标准化 API 的空间数据。 空间 IO 模块中的 `WfsClient` 类使开发人员连接到 WFS 服务并查询该服务中的数据。

`WfsClient` 类支持下列功能：

- 支持的版本: `1.0.0`、`1.1.0`和`2.0.0`
- 支持的筛选器运算符：二进制比较、逻辑、数学、值和 `bbox`。
- 仅使用 `HTTP GET` 发出的请求。
- 支持的操作：

    | 操作 | 说明 |
    | :-- | :-- |
    | GetCapabilities | 使用有效的 WFS 操作和参数生成元数据文档 |
    | getFeature | 返回数据源中的选定功能 |
    | DescribeFeatureType | 返回支持的功能类型 |

## <a name="using-the-wfs-client"></a>使用 WFS 客户端

空间 IO 模块中的 `atlas.io.ogc.WfsClient` 类可以使得轻松地查询 WFS 服务并将响应转换为 GeoJSON 对象。 然后，可以将此 GeoJSON 对象用于其他映射目的。

下面的代码查询 WFS 服务，并在地图上呈现返回的功能。

<br/>

<iframe height='700' scrolling='no' title='简单 WFS 示例' src='//codepen.io/azuremaps/embed/MWwvVYY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/MWwvVYY/'>简单 WFS 示例</a>。
</iframe>

## <a name="supported-filters"></a>支持的筛选器

WFS 标准规范使用 OGC 筛选器。 WFS 客户端支持以下筛选器，假定调用的服务也支持这些筛选器。 自定义筛选器字符串可以传递到 `CustomFilter` 类中。

**逻辑运算符**

- `And`
- `Or`
- `Not`

**值运算符**

- `GmlObjectId`
- `ResourceId`

**数学运算符**

- `Add`
- `Sub`
- `Mul`
- `Div`

**比较运算符**

- `PropertyIsEqualTo`
- `PropertyIsNotEqualTo`
- `PropertyIsLessThan`
- `PropertyIsGreaterThan`
- `PropertyIsLessThanOrEqualTo`
- `PropertyIsGreaterThanOrEqualTo`
- `PropertyIsLike`
- `PropertyIsNull`
- `PropertyIsNil`
- `PropertyIsBetween`

下面的代码演示如何将不同筛选器与 WFS 客户端一起使用。

<br/>

<iframe height='500' scrolling='no' title= 'WFS 筛选器示例' src='//codepen.io/azuremaps/embed/NWqvYrV/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/NWqvYrV/'>WFS 筛选器示例</a>。
</iframe>

## <a name="wfs-service-explorer"></a>WFS 服务资源管理器

以下代码使用 WFS 客户端来浏览 WFS 服务。 选择服务中的属性类型层，并查看关联的图例。

<br/>

<iframe height='700' style='width: 100%;' scrolling='no' title= 'WFS 服务资源管理器' src='//codepen.io/azuremaps/embed/bGdrvmG/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/bGdrvmG/'>WFS 服务资源管理器</a>。
</iframe>

若要访问在未启用 CORS 的终结点上托管的 WFS 服务，可以将启用 CORS 的代理服务传递到 WFS 客户端的 `proxyService` 选项中，如下所示。 

```JavaScript
//Create the WFS client to access the service and use the proxy service settings
client = new atlas.io.ogc.WfsClient({
    url: url,
    proxyService: window.location.origin + '/YourCorsEnabledProxyService.ashx?url='
});
```

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [WfsClient](/JavaScript/api/azure-maps-spatial-io/atlas.io.ogc.wfsclient)

> [!div class="nextstepaction"]
> [WfsServiceOptions](/JavaScript/api/azure-maps-spatial-io/atlas.wfsserviceoptions)

有关可向地图添加的更多代码示例，请参阅以下文章：

> [!div class="nextstepaction"]
> [利用核心操作](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [支持的数据格式详细信息](spatial-io-supported-data-format-details.md)