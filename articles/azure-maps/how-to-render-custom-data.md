---
title: 在 Microsoft Azure Maps 中的栅格地图上呈现自定义数据
description: 了解如何将图钉、标签和几何形状添加到栅格地图。 为此，请参阅“如何在 Azure Maps 中使用静态图像服务”。
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/02/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: e02da9993804ec170ba89b08ee1b46e01a1adac4
ms.sourcegitcommit: 285d5c48a03fcda7c27828236edb079f39aaaebf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2021
ms.locfileid: "113232351"
---
# <a name="render-custom-data-on-a-raster-map"></a>在栅格地图上呈现自定义数据

本文介绍如何使用具有图像合成功能的[静态图像服务](/rest/api/maps/render/getmapimage)。 图像合成功能支持检索包含自定义数据的静态栅格图块。

下面是自定义数据的示例：

- 自定义图钉
- 标签
- 几何覆盖图层

> [!Tip]
> 若要在网页上显示简单的地图，使用 Azure Maps Web SDK 通常比使用静态图像服务更经济高效。 Web SDK 使用地图图块。除非用户平移和缩放地图，否则每次加载地图时，它们通常只生成事务的一小部分。 Azure Maps Web SDK 提供了禁用平移和缩放的选项。 此外，Azure Maps Web SDK 还提供一组数据可视化选项，比静态地图 Web 服务提供的更丰富。  

## <a name="prerequisites"></a>先决条件

1. [创建 Azure Maps 帐户](quick-demo-map-app.md#create-an-azure-maps-account)
2. [获取主订阅密钥](quick-demo-map-app.md#get-the-primary-key-for-your-account)（亦称为“主密钥”或“订阅密钥”）。

本文使用 [Postman](https://www.postman.com/) 应用程序，但你也可以选择其他 API 开发环境。

我们将使用 Azure Maps [数据服务 API](/rest/api/maps/data) 来存储和呈现覆盖图层。

## <a name="render-pushpins-with-labels-and-a-custom-image"></a>呈现具有标签和自定义图像的图钉

> [!Note]
> 本部分的过程需要第 1 代和第 2 代定价层中的 Azure Maps 帐户。
Azure Maps 帐户第 1 代标准 S0 层仅支持 `pins` 参数的单个实例。 它允许你呈现在 URL 请求中指定的具有自定义图像的图钉（最多五个）。

### <a name="get-static-image-with-custom-pins-and-labels"></a>获取具有自定义图钉和标签的静态图像

若要获取具有自定义图钉和标签的静态图像，请执行以下操作：

1. 在 Postman 应用中，选择“新建”。

2. 在“新建”窗口中，选择“HTTP 请求” 。

3. 输入请求的请求名称，例如“GET Static Image”。


4. 选择“GET”HTTP 方法。


5. 输入以下 URL（请将 `{subscription-key}` 替换为你的主订阅密钥）：

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.98,%2040.77&pins=custom%7Cla15+50%7Cls12%7Clc003b61%7C%7C%27CentralPark%27-73.9657974+40.781971%7C%7Chttps%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FAzureMapsCodeSamples%2Fmaster%2FAzureMapsCodeSamples%2FCommon%2Fimages%2Ficons%2Fylw-pushpin.png
    ```

6. 选择“发送”。 

7. 服务将返回以下图像：

    :::image type="content" source="./media/how-to-render-custom-data/render-pins.png" alt-text="具有标签的自定义图钉。":::

## <a name="upload-pins-and-path-data"></a>上传图钉和路径数据

> [!Note]
> 本部分的过程需要 Gen 1 (S1) 或 Gen 2 定价层中的 Azure Maps 帐户。

在本部分，我们将路径和图钉数据上传到 Azure Maps 数据存储。

若要上传图钉和路径数据，请执行以下操作：

1. 在 Postman 应用中，选择“新建”。

2. 在“新建”窗口中，选择“HTTP 请求” 。

3. 输入请求的请求名称，例如“POST Path and Pin Data”。

4. 选择“POST”HTTP 方法。

5. 输入以下 URL（请将 `{subscription-key}` 替换为你的主订阅密钥）：

    ```HTTP
    https://us.atlas.microsoft.com/mapData?subscription-key={subscription-key}&api-version=2.0&dataFormat=geojson
    ```

6. 选择“正文”选项卡。

7. 在下拉列表中，选择“原始”和“JSON”。

8. 复制以下 JSON 数据（要上传的数据），然后将其粘贴到“正文”窗口中：
  
    ```JSON
    {
      "type": "FeatureCollection",
      "features": [
        {
          "type": "Feature",
          "properties": {},
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -73.98235,
                  40.76799
                ],
                [
                  -73.95785,
                  40.80044
                ],
                [
                  -73.94928,
                  40.7968
                ],
                [
                  -73.97317,
                  40.76437
                ],
                [
                  -73.98235,
                  40.76799
                ]
              ]
            ]
          }
        },
        {
          "type": "Feature",
          "properties": {},
          "geometry": {
            "type": "LineString",
            "coordinates": [
              [
                -73.97624731063843,
                40.76560773817073
              ],
              [
                -73.97914409637451,
                40.766826609362575
              ],
              [
                -73.98513078689575,
                40.7585866048861
              ]
            ]
          }
        }
      ]
    }
    ```

9. 选择“发送”。 

10. 在响应窗口中，选择“头”选项卡。

11. 复制“Operation-Location”键的值，即 `status URL`。 在下一部分，我们将使用 `status URL` 检查上传请求的状态。 `status URL` 采用以下格式：

   ```HTTP
   https://us.atlas.microsoft.com/mapData/operations/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx?api-version=2.0
   ```

>[!TIP]
>若要获取你自己的路径和图钉位置信息，请使用[数据上传 API](/rest/api/maps/data-v2/upload-preview)。

### <a name="check-pins-and-path-data-upload-status"></a>检查图钉和路径数据上传状态

若要检查数据上传的状态并检索其唯一 ID (`udid`)，请执行以下操作：

1. 在 Postman 应用中，选择“新建”。

2. 在“新建”窗口中，选择“HTTP 请求” 。

3. 输入请求的请求名称，例如“GET Data Upload Status”。

4. 选择“GET”HTTP 方法。

5. 输入在[上传图钉和路径数据](#upload-pins-and-path-data)中复制的 `status URL`。 请求应类似于以下 URL（请将 `{subscription-key}` 替换为你的主订阅密钥）：

   ```HTTP
     https://us.atlas.microsoft.com/mapData/operations/{statusUrl}?api-version=2.0&subscription-key={subscription-key}
   ```

6. 选择“发送”。 

7. 在响应窗口中，选择“头”选项卡。

8. 复制“Resource-Location”键的值，即 `resource location URL`。 `resource location URL` 包含绘图包资源的唯一标识符 (`udid`)。

    :::image type="content" source="./media/how-to-render-custom-data/resource-location-url.png" alt-text="复制资源位置 URL。":::

### <a name="render-uploaded-features-on-the-map"></a>在地图上呈现上传的特征

若要在地图上呈现上传的图钉和路径数据，请执行以下操作：

1. 在 Postman 应用中，选择“新建”。

2. 在“新建”窗口中，选择“HTTP 请求” 。

3. 输入请求的请求名称，例如“GET Data Upload Status”。

4. 选择“GET”HTTP 方法。

5. 输入以下[呈现服务](/rest/api/maps/render/get-map-image) URL（请将 `{subscription-key}` 替换为你的主订阅密钥，将 `udid` 替换为上传的数据的 `udid`）：

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.96682739257812%2C40.78119135317995&pins=default|la-35+50|ls12|lc003C62|co9B2F15||'Times Square'-73.98516297340393 40.758781646381024|'Central Park'-73.96682739257812 40.78119135317995&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.30||udid-{udId}
    ```

6. 服务将返回以下图像：

    :::image type="content" source="./media/how-to-render-custom-data/uploaded-path.png" alt-text="在静态地图图像中呈现上传的数据。":::

## <a name="render-a-polygon-with-color-and-opacity"></a>使用颜色和不透明度呈现多边形

> [!Note]
> 本部分的过程需要 Gen 1 (S1) 或 Gen 2 定价层中的 Azure Maps 帐户。

可以使用带有[路径参数](/rest/api/maps/render/getmapimage#uri-parameters)的样式修改器来修改多边形的外观。

若要呈现具有颜色和不透明度设置的多边形，请执行以下操作：

1. 在 Postman 应用中，选择“新建”。

2. 在“新建”窗口中，选择“HTTP 请求” 。

3. 输入请求的请求名称，例如“GET Polygon”。

4. 选择“GET”HTTP 方法。

5. 输入以下[呈现服务](/rest/api/maps/render/get-map-image) URL（请将 `{subscription-key}` 替换为你的主订阅密钥）：
  
    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&sku=S1&zoom=14&height=500&Width=500&center=-74.040701, 40.698666&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.50||-74.03995513916016 40.70090237454063|-74.04082417488098 40.70028420372218|-74.04113531112671 40.70049568385827|-74.04298067092896 40.69899904076542|-74.04271245002747 40.69879568992435|-74.04367804527283 40.6980961582905|-74.04364585876465 40.698055487620714|-74.04368877410889 40.698022951066996|-74.04168248176573 40.696444909137|-74.03901100158691 40.69837271818651|-74.03824925422668 40.69837271818651|-74.03809905052185 40.69903971085914|-74.03771281242369 40.699340668780984|-74.03940796852112 40.70058515602143|-74.03948307037354 40.70052821920425|-74.03995513916016 40.70090237454063
    &subscription-key={subscription-key}
    ```

6. 服务将返回以下图像：

    :::image type="content" source="./media/how-to-render-custom-data/opaque-polygon.png" alt-text="呈现不透明的多边形。":::

## <a name="render-a-circle-and-pushpins-with-custom-labels"></a>呈现具有自定义标签的圆和图钉

> [!Note]
> 本部分的过程需要 Gen 1 (S1) 或 Gen 2 定价层中的 Azure Maps 帐户。

可以通过添加样式修饰符来修改图钉的外观。 例如，若要使图钉及其标签变得更大或更小，请使用 `sc`“比例样式”修饰符。 此修饰符采用大于零的值。 值为 1 即为标准比例。 大于 1 的值将使图钉变大，而小于 1 的值将使图钉变小。 有关样式修饰符的详细信息，请参阅[静态图像服务路径参数](/rest/api/maps/render/getmapimage#uri-parameters)。

若要呈现具有自定义标签的圆和图钉，请执行以下操作：

1. 在 Postman 应用中，选择“新建”。

2. 在“新建”窗口中，选择“HTTP 请求” 。

3. 输入请求的请求名称，例如“GET Polygon”。

4. 选择“GET”HTTP 方法。

5. 输入以下[呈现服务](/rest/api/maps/render/get-map-image) URL（请将 `{subscription-key}` 替换为你的主订阅密钥）：

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co002D62||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={subscription-key}
    ```

6. 选择“发送”。 

7. 服务将返回以下图像：

    :::image type="content" source="./media/how-to-render-custom-data/circle-custom-pins.png" alt-text="呈现具有自定义图钉的圆。":::

8. 现在，我们通过修改 `co` 样式修饰符来更改图钉的颜色。 查看参数 `pins` 的值 (`pins=default|la15+50|al0.66|lc003C62|co002D62|`) 时，会看到当前颜色为 `#002D62`。 若要将颜色更改为 `#41d42a`，请将 `#002D62` 替换为 `#41d42a`。  `pins` 参数现在为 `pins=default|la15+50|al0.66|lc003C62|co41D42A|`。 请求类似于以下 URL：

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co41D42A||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={subscription-key}
    ```

9. 选择“发送”。 

10. 服务将返回以下图像：

    :::image type="content" source="./media/how-to-render-custom-data/circle-updated-pins.png" alt-text="呈现具有已更新的图钉的圆。":::

同样，你可以更改、添加和删除其他样式修饰符。

## <a name="next-steps"></a>后续步骤

* 浏览 [Azure Maps 获取地图图像 API](/rest/api/maps/render/getmapimage) 文档。
* 若要详细了解 Azure Maps 数据服务，请参阅[服务文档](/rest/api/maps/data)。
