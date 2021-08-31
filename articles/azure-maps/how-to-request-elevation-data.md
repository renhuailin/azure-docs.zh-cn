---
title: 使用 Azure Maps 高程服务请求高程数据
description: 了解如何使用 Azure Maps 高程服务请求高程数据。
author: anastasia-ms
ms.author: v-stharr
ms.date: 05/18/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
ms.custom: mvc
ms.openlocfilehash: d9e7595a6f3d84628df0c1d79f7936bbf09ea5ef
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121742628"
---
# <a name="request-elevation-data-using-the-azure-maps-elevation-service"></a>使用 Azure Maps 高程服务请求高程数据

Azure Maps [高程服务](/rest/api/maps/elevation)提供的 API 可查询地球表面上任何位置的高程数据。 可以请求沿路径、在定义的边界框内、或在特定坐标处采样的高程数据。 还有，可以使用[呈现 V2 - Get Map Tile API](/rest/api/maps/renderv2) 以磁贴格式检索高程数据。 磁贴以 GeoTIFF 光栅格式传送。 本文描述如何使用 Azure Maps 高程服务和获取地图图块 API 来请求高程数据。 可以使用 GeoJSON 和 GeoTiff 格式请求高程数据。

## <a name="prerequisites"></a>先决条件

1. [在第一代 (S1) 或第二代定价层中创建 Azure Maps 帐户](quick-demo-map-app.md#create-an-azure-maps-account)。
2. [获取主订阅密钥](quick-demo-map-app.md#get-the-primary-key-for-your-account)（亦称为“主密钥”或“订阅密钥”）。

有关 Azure Maps 中身份验证的详细信息，请参阅[在 Azure Maps 中管理身份验证](how-to-manage-authentication.md)。

本文使用 [Postman](https://www.postman.com/) 应用程序，但你也可以选择其他 API 开发环境。

## <a name="request-elevation-data-in-raster-tile-format"></a>以光栅图块格式请求高程数据

要以光栅图块格式请求高程数据，请使用 [呈现服务 V2 - Get Map Tile API](/rest/api/maps/renderv2)。 如果可以找到磁贴，API 会将磁贴作为 GeoTIFF 返回。 否则，API 返回 0。 所有光栅 DEM 图块都使用大地水准面（海平面）地球模式。 在此示例中，我们将请求山峰的高程数据。 珠穆朗玛峰。

>[!TIP]
>若要在世界地图上的特定区域检索图块，请在适当的缩放级别找到正确的图块。 另请注意，WorldDEM 覆盖了全球陆地，但未覆盖海洋。  有关详细信息，请参阅[缩放级别和磁贴网格](zoom-levels-and-tile-grid.md)。

要使用 Postman 应用以光栅图块格式请求高程数据，请执行以下步骤：

1. 在 Postman 应用中，选择“新建”。

2. 在“新建”窗口中，选择“HTTP 请求” 。

3. 在“请求名称”中，输入请求名称。

4. 在“生成器”选项卡中选择 GET HTTP 方法，然后输入以下 URL 来请求光栅图块 。

    ```http
    https://atlas.microsoft.com/map/tile?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=2.0&tilesetId=microsoft.dem&zoom=13&x=6074&y=3432
    ```

    >[!Important]
    >对于此请求和本文中提到的其他请求，请将 `{Azure-Maps-Primary-Subscription-key}` 替换为你的主订阅密钥。

5. 选择“发送”按钮  。

    应会收到包含 GeoTIFF 格式高程数据的光栅图块。 光栅磁贴原始数据中的每个像素的类型都为 `float`。 每个像素的值表示以米为单位的高程高度。

## <a name="request-elevation-data-in-geojson-format"></a>以 GeoJSON 格式请求高程数据

若要以 GeoJSON 格式请求高程数据，请使用高程服务 API。 本节介绍其中每个 API：

* [获取点数据](/rest/api/maps/elevation/getdataforpoints)
* [发布点数据](/rest/api/maps/elevation/postdataforpoints)
* [获取折线数据](/rest/api/maps/elevation/getdataforpolyline)
* [发布折线数据](/rest/api/maps/elevation/postdataforpolyline)
* [获取边界框数据](/rest/api/maps/elevation/getdataforboundingbox)

>[!IMPORTANT]
> 当无法返回任何数据时，所有 API 都返回 0。

### <a name="request-elevation-data-for-points"></a>请求点高程数据

在此示例中，我们将使用[获取点数据 API](/rest/api/maps/elevation/getdataforpoints) 来请求山峰的高程数据。 珠穆朗玛峰和扎姆朗山。 接下来，我们将使用 [发布点数据 API](/rest/api/maps/elevation/postdataforpoints) 来使用相同的两点请求高程数据。 URL 中的纬度和经度应使用 WGS84（世界大地坐标系统）十进制度。

 >[!IMPORTANT]
 >URL 字符长度限制为 2048，因此不可能在 URL GET 请求中将超过 100 个坐标作为管道分隔字符串进行传递。 如果打算将超过 100 个坐标作为管道分隔字符串传递，请使用发布点数据 API。

创建请求：

1. 在 Postman 应用中，再次选择“新建”。

2. 在“新建”窗口中，选择“HTTP 请求” 。

3. 在“请求名称”中，输入请求名称。

4. 在“生成器”选项卡上选择 GET HTTP 方法，然后输入以下 URL（将 `{Azure-Maps-Primary-Subscription-key}` 替换为主要订阅密钥） ：

    ```http
    https://atlas.microsoft.com/elevation/point/json?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&points=-73.998672,40.714728|150.644,-34.397
    ```

5. 选择“发送”按钮  。  将收到以下 JSON 响应：

    ```json
    {
    "data": [
        {
            "coordinate": {
                "latitude": 40.714728,
                "longitude": -73.998672
            },
            "elevationInMeter": 12.142355447638208
        },
        {
            "coordinate": {
                "latitude": -34.397,
                "longitude": 150.644
            },
            "elevationInMeter": 384.47041445517846
        }
    ]
    }
    ```

6. 现在，我们将调用[发布点数据 API](/rest/api/maps/elevation/postdataforpoints)，以获取相同两点的高程数据。 在“生成器”选项卡上选择 POST HTTP 方法，然后输入以下 URL（将 `{Azure-Maps-Primary-Subscription-key}` 替换为主要订阅密钥） ：

    ```http
    https://atlas.microsoft.com/elevation/point/json?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0
    ```

7. 在 POST 请求的“标头”字段中，将 `Content-Type` 设置为 `application/json` 。 

8. 在“主体”字段中，提供以下坐标点信息：

     ```json
    [
        {
            "lon": -73.998672,
            "lat": 40.714728
        },
        {
            "lon": 150.644,
            "lat": -34.397
        }
    ]
    ```

9. 选择“发送”。 

### <a name="request-elevation-data-samples-along-a-polyline"></a>沿折线请求高程数据示例

在此示例中，我们将使用[获取折线 API 数据](/rest/api/maps/elevation/getdataforpolyline)来请求沿山峰的坐标之间的直线均匀分布的五个高程数据示例。 珠穆朗玛峰和扎姆朗山。 两个坐标都必须以经度/纬度格式定义。 如果不为 `samples` 参数指定值，则示例数默认为 10。 最大示例数为 2,000。

接下来，我们将使用“获取折线 API 数据”来请求沿路径均匀分布的三个高程数据示例。 我们将通过传递三个经度/纬度坐标对来定义示例的精确位置。

最后，我们将使用[发布折线数据 API](/rest/api/maps/elevation/postdataforpolyline) 来请求相同三个均匀分布示例的高程数据。

URL 中的纬度和经度应使用 WGS84（世界大地坐标系统）十进制度。

 >[!IMPORTANT]
 >URL 字符长度限制为 2048，因此不可能在 URL GET 请求中将超过 100 个坐标作为管道分隔字符串进行传递。 如果打算将超过 100 个坐标作为管道分隔字符串传递，请使用发布点数据 API。

创建请求：

1. 在 Postman 应用中，选择“新建”。

2. 在“新建”窗口中，选择“HTTP 请求” 。

3. 在“请求名称”中，输入请求名称。

4. 在“生成器”选项卡上选择 GET HTTP 方法，然后输入以下 URL（将 `{Azure-Maps-Primary-Subscription-key}` 替换为主要订阅密钥） ：

   ```http
    https://atlas.microsoft.com/elevation/line/json?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}&lines=-73.998672,40.714728|150.644,-34.397&samples=5
    ```

5. 选择“发送”按钮  。  将收到以下 JSON 响应：

    ```JSON
    {
        "data": [
            {
                "coordinate": {
                    "latitude": 40.714728,
                    "longitude": -73.998672
                },
                "elevationInMeter": 12.14236
            },
            {
                "coordinate": {
                    "latitude": 21.936796000000001,
                    "longitude": -17.838003999999998
                },
                "elevationInMeter": 0.0
            },
            {
                "coordinate": {
                    "latitude": 3.1588640000000012,
                    "longitude": 38.322664000000003
                },
                "elevationInMeter": 598.66943
            },
            {
                "coordinate": {
                    "latitude": -15.619067999999999,
                    "longitude": 94.483332000000019
                },
                "elevationInMeter": 0.0
            },
            {
                "coordinate": {
                    "latitude": -34.397,
                    "longitude": 150.644
                },
                "elevationInMeter": 384.47041
            }
        ]
    }
    ```

6. 现在，我们将请求在珠穆朗玛峰、扎姆朗山和贾奴峰的坐标之间的路径上的三个高程数据示例。 在“Params”字段中，针对 `lines` 查询密钥值输入以下坐标数组。

    ```html
        86.9797222, 27.775|86.9252778, 27.9880556 | 88.0444444, 27.6822222
    ```

7. 将 `samples` 查询密钥值更改为 `3`。  下图显示了新值。

     :::image type="content" source="./media/how-to-request-elevation-data/get-elevation-samples.png" alt-text="检索三个高程数据示例。":::

8. 选择“发送”。  将收到以下 JSON 响应：

    ```json
    {
        "data": [
            {
                "coordinate": {
                    "latitude": 27.775,
                    "longitude": 86.9797222
                },
                "elevationInMeter": 7116.0348851572589
            },
            {
                "coordinate": {
                    "latitude": 27.737403546316028,
                    "longitude": 87.411180791156454
                },
                "elevationInMeter": 1798.6945512521534
            },
            {
                "coordinate": {
                    "latitude": 27.682222199999998,
                    "longitude": 88.0444444
                },
                "elevationInMeter": 7016.9372013588072
            }
        ]
    }
    ```

9. 现在，我们将调用[发布折线数据 API](/rest/api/maps/elevation/postdataforpolyline)，以获取相同三点的高程数据。  在“生成器”选项卡上选择 POST HTTP 方法，然后输入以下 URL（将 `{Azure-Maps-Primary-Subscription-key}` 替换为主要订阅密钥） ：

    ```http
    https://atlas.microsoft.com/elevation/line/json?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}&samples=5
    ```

10. 在 POST 请求的“标头”字段中，将 `Content-Type` 设置为 `application/json` 。 

11. 在“主体”字段中，提供以下坐标点信息。

     ```json
    [
        {
            "lon": 86.9797222,
            "lat": 27.775
        },
        {
            "lon": 86.9252778,
            "lat": 27.9880556
        },
        {
            "lon": 88.0444444,
            "lat": 27.6822222
        }
    ]
    ```

12. 选择“发送”。 

### <a name="request-elevation-data-by-bounding-box"></a>按边界框请求高程数据

现在，我们将使用[获取边界框数据](/rest/api/maps/elevation/getdataforboundingbox)来请求山峰附近的高程数据。 华盛顿州，雷尼尔山。 高程数据将按边界框中均匀分布的位置返回。 边界区域由 2 组纬度/经度坐标（南纬、西经 | 北纬、东经）定义，并分为行和列。 边界框边缘包含两行和两列。 按行和列相交创建的网格顶点返回高程。 在单个请求中，最多可以返回 2000 个高程。

在本示例中，我们将指定行 = 3，列 = 6。 响应返回 18 个高程值。 在下图中，高程值顺序从西南角开始，然后继续从西到东，从南到北。  高程点按其返回顺序进行编号。

:::image type="content" source="./media/how-to-request-elevation-data/bounding-box.png" border="false" alt-text="位于东北和东南角的边界框。":::

创建请求：

1. 在 Postman 应用中，选择“新建”。

2. 在“新建”窗口中，选择“HTTP 请求” 。

3. 在“请求名称”中，输入请求名称。

4. 在“生成器”选项卡上选择 GET HTTP 方法，然后输入以下 URL（将 `{Azure-Maps-Primary-Subscription-key}` 替换为主要订阅密钥） ：

    ```http
    https://atlas.microsoft.com/elevation/lattice/json?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&bounds=-121.66853362143818, 46.84646479863713,-121.65853362143818, 46.85646479863713&rows=2&columns=3
    ```

5. 选择“发送”。   响应返回 18 个高程数据示例，对应网格的每个顶点。

    ```json
    {
    "data": [
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66853362143819
            },
            "elevationInMeter": 2298.6581875651746
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66653362143819
            },
            "elevationInMeter": 2306.3980756609963
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66453362143818
            },
            "elevationInMeter": 2279.3385479564113
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66253362143819
            },
            "elevationInMeter": 2233.1549264690366
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66053362143818
            },
            "elevationInMeter": 2196.4485923541492
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.65853362143818
            },
            "elevationInMeter": 2133.1756767157253
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66853362143819
            },
            "elevationInMeter": 2345.3227848228803
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66653362143819
            },
            "elevationInMeter": 2292.2449195443587
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66453362143818
            },
            "elevationInMeter": 2270.5867788258074
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66253362143819
            },
            "elevationInMeter": 2296.8311427390604
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66053362143818
            },
            "elevationInMeter": 2266.0729430891065
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.65853362143818
            },
            "elevationInMeter": 2242.216346631234
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66853362143819
            },
            "elevationInMeter": 2378.460838833359
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66653362143819
            },
            "elevationInMeter": 2327.6761137260387
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66453362143818
            },
            "elevationInMeter": 2208.3782743402949
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66253362143819
            },
            "elevationInMeter": 2106.9526472760981
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66053362143818
            },
            "elevationInMeter": 2054.3270174034078
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.65853362143818
            },
            "elevationInMeter": 2030.6438331110671
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66853362143819
            },
            "elevationInMeter": 2318.753153399402
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66653362143819
            },
            "elevationInMeter": 2253.88875188271
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66453362143818
            },
            "elevationInMeter": 2136.6145845357587
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66253362143819
            },
            "elevationInMeter": 2073.6734467948486
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66053362143818
            },
            "elevationInMeter": 2042.994055784251
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.65853362143818
            },
            "elevationInMeter": 1988.3631481900356
        }
    ]
    }
    ```

## <a name="samples-use-elevation-service-apis-in-azure-maps-control"></a>示例：在 Azure Maps Control 中使用高程服务 API

### <a name="get-elevation-data-by-coordinate-position"></a>按坐标位置获取高程数据

以下示例网页介绍了如何使用地图控件显示坐标点的高程数据。 当用户拖动标记时，地图将在弹出窗口中显示高程数据。

<br/>

<iframe height="500" style="width:100%;" scrolling="no" title="获取位置高程" src="https://codepen.io/azuremaps/embed/c840b510e113ba7cb32809591d5f96a2?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps（<a href='https://codepen.io/azuremaps'>@azuremaps</a>）提供的触笔<a href='https://codepen.io/azuremaps/pen/c840b510e113ba7cb32809591d5f96a2'>获取位置高程</a>。
</iframe>

### <a name="get-elevation-data-by-bounding-box"></a>按边界框获取高程数据

以下示例网页介绍如何使用地图控件显示边界框内所包含的高程数据。 用户通过选择左上角的 `square` 图标来定义边界框，然后在地图上的任意位置绘制正方形。 然后，地图控件将根据位于右上角的键中指定的颜色，来呈现高程数据。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="按边界框获取高程" src="https://codepen.io/azuremaps/embed/619c888c70089c3350a3e95d499f3e48?height=500&theme-id=default&default-tab=js,result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps（<a href='https://codepen.io/azuremaps'>@azuremaps</a>）提供的触笔<a href='https://codepen.io/azuremaps/pen/619c888c70089c3350a3e95d499f3e48'>按边界框获取高程</a>。
</iframe>

### <a name="get-elevation-data-by-polyline-path"></a>按折线路径获取高程数据

以下示例网页介绍如何使用地图控件显示沿路径的高程数据。 用户通过选择左上角的 `Polyline` 图标来定义路径，然后在地图上绘制折线。 然后，地图控件将根据位于右上角的键中指定的颜色，来呈现高程数据。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="高程路径梯度" src="https://codepen.io/azuremaps/embed/7bee08e5cb13d05cb0a11636b60f14ca?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的触笔<a href='https://codepen.io/azuremaps/pen/7bee08e5cb13d05cb0a11636b60f14ca'>高程路径梯度</a>。
</iframe>


## <a name="next-steps"></a>后续步骤

要进一步了解 Azure Maps 高程 API，请参阅：

> [!div class="nextstepaction"]
> [高程 - 获取纬度经度坐标数据](/rest/api/maps/elevation/getdataforpoints)

> [!div class="nextstepaction"]
> [高程 - 获取边界框数据](/rest/api/maps/elevation/getdataforboundingbox)

> [!div class="nextstepaction"]
> [高程 - 获取折线数据](/rest/api/maps/elevation/getdataforpolyline)

> [!div class="nextstepaction"]
> [呈现 V2 – Get Map Tile](/rest/api/maps/renderv2)

有关 Azure Maps REST API 的完整列表，请参阅：

> [!div class="nextstepaction"]
> [Azure Maps REST API](/rest/api/maps/)
