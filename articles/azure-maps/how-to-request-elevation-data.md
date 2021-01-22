---
title: '使用 Azure Maps 提升服务 (预览版请求提升数据) '
description: 了解如何使用 Azure Maps 提升服务 (预览版) 请求提升数据。
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: d14eda84144105bf2e04f1238284bc58a91c4c03
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2021
ms.locfileid: "98684049"
---
# <a name="request-elevation-data-using-the-azure-maps-elevation-service-preview"></a>使用 Azure Maps 提升服务 (预览版请求提升数据) 

> [!IMPORTANT]
> Azure Maps 提升服务目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Azure Maps [提升服务](/rest/api/maps/elevation) 提供了 api，用于查询地球表面任意位置的提升数据。 您可以请求沿着路径、在定义的范围框中或在特定坐标内进行抽样提升数据。 此外，还可以使用 [呈现 V2-获取地图磁贴 API](/rest/api/maps/renderv2) 来检索以磁贴格式的提升数据。 磁贴以 GeoTIFF 光栅格式提供。 本文介绍如何使用 Azure Maps 提升服务和获取地图磁贴 API 来请求提升数据。 可以通过 GeoJSON 和 GeoTiff 格式请求提升数据。

## <a name="prerequisites"></a>先决条件

1. [在 S1 定价层中创建 Azure Maps 帐户](quick-demo-map-app.md#create-an-azure-maps-account)
2. [获取主订阅密钥](quick-demo-map-app.md#get-the-primary-key-for-your-account)（亦称为“主密钥”或“订阅密钥”）。

有关 Azure Maps 中的身份验证的详细信息，请 [在 Azure Maps 中管理身份验证](how-to-manage-authentication.md)。

本文使用的是 [Postman](https://www.postman.com/) 应用程序，但你可以选择不同的 API 开发环境。

## <a name="request-elevation-data-in-raster-tiled-format"></a>以光栅平铺格式请求提升数据

若要以光栅磁贴格式请求提升数据，请使用 [呈现 V2-获取地图磁贴 API](/rest/api/maps/renderv2)。 如果可以找到磁贴，API 会将磁贴作为 GeoTIFF 返回。 否则，API 将返回0。 所有光栅 DEM 磁贴都 (海平面) 地球模式使用 geoid。 在此示例中，我们将为 Mt 请求提升数据。 他.

>[!TIP]
>若要在世界地图上的特定区域检索磁贴，需要在适当的缩放级别找到正确的磁贴。 另请注意，WorldDEM 涵盖整个全局 landmass，但不涵盖海洋。  有关详细信息，请参阅[缩放级别和磁贴网格](zoom-levels-and-tile-grid.md)。

1. 打开 Postman 应用。 在 Postman 应用顶部附近，选择“新建”。 在“新建”窗口中，选择“集合”。  命名集合，然后选择“创建”按钮。

2. 若要创建请求，请再次选择“新建”。 在“新建”窗口中，选择“请求”。 在“请求名称”中，输入请求名称。 选择在上一步中创建的集合，然后选择“保存”。

3. 在 "生成器" 选项卡中选择 " **获取** HTTP" 方法，然后输入以下 URL 请求 "光栅" 磁贴。 对于此请求和本文中提到的其他请求，请将 `{Azure-Maps-Primary-Subscription-key}` 替换为你的主订阅密钥。

    ```http
    https://atlas.microsoft.com/map/tile?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=2.0&tilesetId=microsoft.dem&zoom=13&x=6074&y=3432
    ```

4. 单击“发送”按钮。 你应收到包含 GeoTIFF 格式的提升数据的光栅磁贴。 光栅图块的原始数据中的每个像素的类型为 `float` 。 每个像素的值表示以米为单位的提升高度。

## <a name="request-elevation-data-in-geojson-format"></a>以 GeoJSON 格式请求提升数据

使用提升服务 (预览) Api 以 GeoJSON 格式请求提升数据。 本部分将显示三个 Api 中的每一个：

* [获取点数据](/rest/api/maps/elevation/getdataforpoints)
* [为点发布数据](/rest/api/maps/elevation/postdataforpoints)
* [获取折线的数据](/rest/api/maps/elevation/getdataforpolyline)
* [为折线发布数据](/rest/api/maps/elevation/postdataforpolyline)
* [获取边界框的数据](/rest/api/maps/elevation/getdataforboundingbox)

>[!IMPORTANT]
> 当无法返回任何数据时，所有 Api 都将返回 `0` 。

### <a name="request-elevation-data-for-points"></a>请求提升点数据

在此示例中，我们将使用 " [获取点数据" API](/rest/api/maps/elevation/getdataforpoints) 在 Mt 请求提升数据。 他和 Chamlang 山上。 接下来，我们将使用 [点的 Post 数据 API](/rest/api/maps/elevation/postdataforpoints) 请求使用相同的两点来请求提升数据。 URL 中的纬度和经度应处于 WGS84 (世界测量 System) 小数度。

 >[!IMPORTANT]
 >由于 URL 字符长度限制为2048，因此不能将超过100个坐标作为 URL GET 请求中的管道分隔字符串传递。 如果打算将超过100的坐标作为管道分隔字符串传递，请使用 POST 数据来表示点。

1. 若要创建请求，请再次选择“新建”。 在“新建”窗口中，选择“请求”。 在“请求名称”中，输入请求名称。 选择在上一步中创建的集合，然后选择“保存”。

2. 在 "生成器" 选项卡中选择 " **获取** HTTP" 方法，然后输入以下 URL。 对于此请求和本文中提到的其他请求，请将 `{Azure-Maps-Primary-Subscription-key}` 替换为你的主订阅密钥。

    ```http
    https://atlas.microsoft.com/elevation/point/json?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&points=-73.998672,40.714728|150.644,-34.397
    ```

3. 单击“发送”按钮。  你会收到以下 JSON 响应：

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

4. 现在，我们将为 [点 API 调用 Post 数据](/rest/api/maps/elevation/postdataforpoints) ，以获取相同两点的提升数据。 在 "生成器" 选项卡中选择 **POST** HTTP 方法，然后输入以下 URL。 对于此请求和本文中提到的其他请求，请将 `{Azure-Maps-Primary-Subscription-key}` 替换为你的主订阅密钥。

    ```http
    https://atlas.microsoft.com/elevation/point/json?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0
    ```

5. 在 POST 请求的“头”中，将“`Content-Type`”设置为“`application/json`”。 在 **正文** 中提供以下坐标点信息。 完成后，单击“发送”。

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

### <a name="request-elevation-data-samples-along-a-polyline"></a>按折线请求提升数据示例

在此示例中，我们将使用 " [获取折线的数据](/rest/api/maps/elevation/getdataforpolyline) " 来请求5个均匀分布的样本，沿 Mt 坐标之间的直线。 他和 Chamlang 山上。 这两个坐标都必须以长/Lat 格式定义。 如果未指定参数的值，则 `samples` 示例数默认为10。 最大样本数为2000。

接下来，我们将使用 "获取折线数据" 来请求三个均匀间距的数据沿着路径进行提升。 我们将通过传递三个长/Lat 坐标对来定义示例的确切位置。

最后，我们将使用用于 [折线 API 的 Post 数据](/rest/api/maps/elevation/postdataforpolyline) 在相同的三个相同的示例中请求提升数据。

URL 中的纬度和经度应处于 WGS84 (世界测量 System) 小数度。

 >[!IMPORTANT]
 >由于 URL 字符长度限制为2048，因此不能将超过100个坐标作为 URL GET 请求中的管道分隔字符串传递。 如果打算将超过100的坐标作为管道分隔字符串传递，请使用 POST 数据来表示点。

1. 选择“新建”。 在“新建”窗口中，选择“请求”。 在“请求名称”中输入名称，然后选择一个集合。 单击“ **保存**”。

2. 在 "生成器" 选项卡中选择 " **获取** HTTP" 方法，然后输入以下 URL。 对于此请求和本文中提到的其他请求，请将 `{Azure-Maps-Primary-Subscription-key}` 替换为你的主订阅密钥。

   ```http
    https://atlas.microsoft.com/elevation/line/json?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}&lines=-73.998672,40.714728|150.644,-34.397&samples=5
    ```

3. 单击“发送”按钮。  你会收到以下 JSON 响应：

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

4. 现在，我们将在 Mount 他、Chamlang 和 Jannu 山上的坐标之间，通过路径请求三个提升数据示例。 在 " **Params** " 部分中，复制查询键的值的以下坐标数组 `lines` 。

    ```html
        86.9797222, 27.775|86.9252778, 27.9880556 | 88.0444444, 27.6822222
    ```

5. 将 `samples` 查询密钥值改为 `3` 。  下图显示了新值。

     :::image type="content" source="./media/how-to-request-elevation-data/get-elevation-samples.png" alt-text="检索三个提升数据示例。":::

6. 单击蓝色的 " **发送** " 按钮。 你会收到以下 JSON 响应：

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

7. 现在，我们将为 [折线 API 调用 Post 数据](/rest/api/maps/elevation/postdataforpolyline) ，以获取同三个点的提升数据。 在 "生成器" 选项卡中选择 **POST** HTTP 方法，然后输入以下 URL。 对于此请求和本文中提到的其他请求，请将 `{Azure-Maps-Primary-Subscription-key}` 替换为你的主订阅密钥。

    ```http
    https://atlas.microsoft.com/elevation/line/json?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}&samples=5
    ```

8. 在 POST 请求的“头”中，将“`Content-Type`”设置为“`application/json`”。 在 **正文** 中提供以下坐标点信息。 完成后，单击“发送”。

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

### <a name="request-elevation-data-by-bounding-box"></a>请求按边界框提升数据

现在，我们将使用 " [获取数据" 边界框](/rest/api/maps/elevation/getdataforboundingbox) 来请求在 Mt 附近进行提升。 Rainier，WA。 提升数据将返回到边界框中等距的位置。  (2) 一组纬度/长坐标 (南部纬度、西经度 |"北部纬度"、"东部经度) " 分为行和列。 边界框帐户的两个 (2) 的边缘和两个 (2) 列。 为在行和列相交时创建的网格顶点返回提升。 在单个请求中，最多可以返回2000个提升。

在此示例中，我们将指定 rows = 3，列 = 6。 在响应中返回18个提升值。 在下面的关系图中，提升值是从西南角开始排序的，然后将 "西" 和 "东南" 继续到 "中"。  提升点按其返回顺序进行编号。

:::image type="content" source="./media/how-to-request-elevation-data/bounding-box.png" border="false" alt-text="外框坐标，位于 NE 和 SE 角。":::

1. 选择“新建”。 在“新建”窗口中，选择“请求”。 在“请求名称”中输入名称，然后选择一个集合。 单击“ **保存**”。

2. 在 "生成器" 选项卡中选择 " **获取** HTTP" 方法，然后输入以下 URL。 对于此请求和本文中提到的其他请求，请将 `{Azure-Maps-Primary-Subscription-key}` 替换为你的主订阅密钥。

    ```http
    https://atlas.microsoft.com/elevation/lattice/json?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&bounds=-121.66853362143818, 46.84646479863713,-121.65853362143818, 46.85646479863713&rows=2&columns=3
    ```

3. 单击蓝色的 " **发送** " 按钮。 18对于网格的每个顶点都有一个提升数据示例，在响应中返回。

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

## <a name="samples-use-elevation-service-preview-apis-in-azure-maps-control"></a>示例：使用提升服务 (预览) Azure Maps 控件中的 Api

### <a name="get-elevation-data-by-coordinate-position"></a>按坐标位置获取提升数据

下面的示例网页演示了如何使用地图控件在某个坐标点显示仰角数据。 当用户拖动标记时，地图将在弹出窗口中显示提升数据。

<br/>

<iframe height="500" style="width:100%;" scrolling="no" title="获取提升位置" src="https://codepen.io/azuremaps/embed/c840b510e113ba7cb32809591d5f96a2?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
请参阅 CodePen 上的 "通过 Azure Maps () 按<a href='https://codepen.io/azuremaps/pen/c840b510e113ba7cb32809591d5f96a2'>位置获取提升</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'></a>。
</iframe>

### <a name="get-elevation-data-by-bounding-box"></a>按边界框获取提升数据

下面的示例网页演示了如何使用地图控件来显示包含在边界框中的提升数据。 用户通过单击左上角的图标来定义边界框 `square` ，并在地图上的任意位置绘制正方形。 然后，地图控件将根据位于右上角的键中指定的颜色，来呈现提升数据。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="通过边界框提升" src="https://codepen.io/azuremaps/embed/619c888c70089c3350a3e95d499f3e48?height=500&theme-id=default&default-tab=js,result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
Azure Maps 通过 CodePen 上的 () ，按<a href='https://codepen.io/azuremaps/pen/619c888c70089c3350a3e95d499f3e48'>边界框</a>查看笔提升 <a href='https://codepen.io/azuremaps'>@azuremaps</a> 。 <a href='https://codepen.io'></a>
</iframe>

### <a name="get-elevation-data-by-polyline-path"></a>按折线路径获取提升数据

下面的示例网页演示了如何使用地图控件沿路径显示提升数据。 用户通过单击 `Polyline` 左上角的图标，并在地图上绘制折线来定义路径。 然后，地图控件将在右上角的键中指定的颜色呈现提升数据。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="提升路径渐变" src="https://codepen.io/azuremaps/embed/7bee08e5cb13d05cb0a11636b60f14ca?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
请参阅 CodePen 上的 () ，查看笔<a href='https://codepen.io/azuremaps/pen/7bee08e5cb13d05cb0a11636b60f14ca'>提升路径 Azure Maps 梯度</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> 。 <a href='https://codepen.io'></a>
</iframe>


## <a name="next-steps"></a>后续步骤

若要进一步了解 Azure Maps 提升 (预览版) Api，请参阅：

> [!div class="nextstepaction"]
> [提升 (预览) -获取 Lat 长坐标的数据](/rest/api/maps/elevation/getdataforpoints)

> [!div class="nextstepaction"]
> [提升 (预览) -获取边界框的数据](/rest/api/maps/elevation/getdataforboundingbox)

> [!div class="nextstepaction"]
> [提升 (预览) -获取折线数据](/rest/api/maps/elevation/getdataforpolyline)

> [!div class="nextstepaction"]
> [Render V2 –获取地图磁贴](/rest/api/maps/renderv2)

有关 Azure Maps REST API 的完整列表，请参阅：

> [!div class="nextstepaction"]
> [Azure Maps REST API](/rest/api/maps/)