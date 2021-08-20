---
title: 教程：使用 Microsoft Azure Maps Creator 创建室内地图
description: 有关如何使用 Microsoft Azure Maps Creator 创建室内地图的教程
author: anastasia-ms
ms.author: v-stharr
ms.date: 5/19/2021
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 73e7073b67916983ea7bd37cd6adad4ced5633c2
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2021
ms.locfileid: "114730621"
---
# <a name="tutorial-use-creator-to-create-indoor-maps"></a>教程：使用 Creator 创建室内定位

本教程介绍了如何创建室内地图。 本教程介绍以下操作：

> [!div class="checklist"]
> * 上传室内地图绘图包。
> * 将绘图包转换为地图数据。
> * 通过地图数据创建数据集。
> * 通过数据集中的数据创建图块集。
> * 通过查询 Azure Maps Web 特征服务 (WFS) API 来了解地图特征。
> * 通过使用地图特征和数据集中的数据来创建特征状态集。
> * 更新特征状态集。

## <a name="prerequisites"></a>必备条件

1. [创建 Azure Maps 帐户](quick-demo-map-app.md#create-an-azure-maps-account)。
2. [获取主订阅密钥](quick-demo-map-app.md#get-the-primary-key-for-your-account)（亦称为“主密钥”或“订阅密钥”）。
3. [创建 Creator 资源](how-to-manage-creator.md)。
4. 下载[示例绘图包](https://github.com/Azure-Samples/am-creator-indoor-data-examples/blob/master/Sample%20-%20Contoso%20Drawing%20Package.zip)。

本教程使用 [Postman](https://www.postman.com/) 应用程序，但你也可以选择其他 API 开发环境。

>[!IMPORTANT]
> 本教程使用 `us.atlas.microsoft.com` 地理 URL。 如果 Creator 服务不是在美国创建的，则必须使用不同的地理 URL。  有关详细信息，请参阅[访问 Creator 服务](how-to-manage-creator.md#access-to-creator-services)。 若要查看区域到地理位置的映射，[请参阅 Creator 服务地理范围](creator-geographic-scope.md)。

## <a name="upload-a-drawing-package"></a>上传绘图包

使用[数据上传 API](/rest/api/maps/data-v2/upload-preview) 将绘图包上传到 Azure Maps 资源。

数据上传 API 是一项长期事务，用于实现 [Creator 长时间运行的操作 API V2](creator-long-running-operation-v2.md) 中定义的模式。

上传绘图包：

1. 在 Postman 应用中，选择“新建”。

2. 在“新建”窗口中，选择“HTTP 请求” 。

3. 在“请求名称”中输入请求名称，比如 POST Data Upload。

4. 选择“POST”HTTP 方法。

5. 在[数据上传 API](/rest/api/maps/data-v2/upload-preview) 中输入以下 URL。请求应类似于以下 URL（将 `{Azure-Maps-Primary-Subscription-key}` 替换为主订阅密钥）：

    ```http
    https://us.atlas.microsoft.com/mapData?api-version=2.0&dataFormat=dwgzippackage&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

6. 选择“标头”选项卡  。

7. 在“键”字段中，选择 `Content-Type`。 

8. 在“值”字段中，选择 `application/octet-stream`。

     :::image type="content" source="./media/tutorial-creator-indoor-maps/data-upload-header.png"alt-text="用于数据上传的“标头”选项卡信息。":::

9. 选择“正文”选项卡。

10. 在下拉列表中，选择“二进制”。

11. 选择“选择文件”，然后选择绘图包。

    :::image type="content" source="./media/tutorial-creator-indoor-maps/data-upload-body.png" alt-text="选择绘图包。":::

12. 选择 **Send**。

13. 在响应窗口中，选择“标头”选项卡。

14. 复制“Operation-Location”键的值，即 `status URL`。 我们将使用 `status URL` 检查绘图包上传的状态。

     :::image type="content" source="./media/tutorial-creator-indoor-maps/data-upload-response-header.png" alt-text="复制“位置”键中的状态 URL。":::

### <a name="check-the-drawing-package-upload-status"></a>检查绘图包上传状态

检查绘图包的状态并检索其唯一 ID (`udid`)：

1. 在 Postman 应用中，选择“新建”。

2. 在“新建”窗口中，选择“HTTP 请求” 。

3. 在“请求名称”中输入请求名称，比如 GET Data Upload Status。

4. 选择“GET”HTTP 方法。

5. 输入在[上传绘图包](#upload-a-drawing-package)中复制的 `status URL`。 请求应类似于以下 URL（将 `{Azure-Maps-Primary-Subscription-key}` 替换为主订阅密钥）：

    ```http
    https://us.atlas.microsoft.com/mapData/operations/<operationId>?api-version=2.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

6. 选择 **Send**。

7. 在响应窗口中，选择“标头”选项卡。

8. 复制“Resource-Location”键的值，即 `resource location URL`。 `resource location URL` 包含绘图包资源的唯一标识符 (`udid`)。

    :::image type="content" source="./media/tutorial-creator-indoor-maps/resource-location-url.png" alt-text="复制资源位置 URL。":::

### <a name="optional-retrieve-drawing-package-metadata"></a>（可选）检索绘图包元数据

可以从绘图包资源中检索元数据。 元数据包含资源位置 URL、创建日期、更新日期、大小和上传状态等信息。

检索内容元数据：

1. 在 Postman 应用中，选择“新建”。

2. 在“新建”窗口中，选择“HTTP 请求” 。

3. 在“请求名称”中输入请求名称，比如 GET Data Upload Metadata。

4. . 选择“GET”HTTP 方法。

5. 输入在[检查绘图包上传状态](#check-the-drawing-package-upload-status)中复制的 `resource Location URL`。 请求应类似于以下 URL（将 `{Azure-Maps-Primary-Subscription-key}` 替换为主订阅密钥）：

    ```http
   https://us.atlas.microsoft.com/mapData/metadata/{udid}?api-version=2.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

6. 选择 **Send**。

7. 在响应窗口中，选择“正文”选项卡。元数据应类似于以下 JSON 片段：

    ```json
    {
        "udid": "{udid}",
        "location": "https://us.atlas.microsoft.com/mapData/6ebf1ae1-2a66-760b-e28c-b9381fcff335?api-version=2.0",
        "created": "5/18/2021 8:10:32 PM +00:00",
        "updated": "5/18/2021 8:10:37 PM +00:00",
        "sizeInBytes": 946901,
        "uploadStatus": "Completed"
    }
    ```

## <a name="convert-a-drawing-package"></a>转换绘图包

至此，绘图包已上传，现在我们将使用已上传包的 `udid` 将此包转换为地图数据。 转换 API 使用一项长期事务，用于实现[此处](creator-long-running-operation-v2.md)定义的模式。

转换绘图包：

1. 在 Postman 应用中，选择“新建”。

2. 在“新建”窗口中，选择“HTTP 请求” 。

3. 在“请求名称”中输入请求名称，比如 POST Convert Drawing Package。

4. 选择“POST”HTTP 方法。

5. 在[转换服务](/rest/api/maps/v2/conversion/convert)中输入以下 URL（将 `{Azure-Maps-Primary-Subscription-key}` 替换为主订阅密钥，将 `udid` 替换为已上传包的 `udid`）：

    ```http
    https://us.atlas.microsoft.com/conversions?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=2.0&udid={udid}&inputType=DWG&outputOntology=facility-2.0
    ```

6. 选择 **Send**。

7. 在响应窗口中，选择“标头”选项卡。 

8. 复制“Operation-Location”键的值，即 `status URL`。 我们将使用 `status URL` 检查转换的状态。

    :::image type="content" source="./media/tutorial-creator-indoor-maps/data-convert-location-url.png" border="true" alt-text="复制绘图包“位置”键的值。":::

### <a name="check-the-drawing-package-conversion-status"></a>检查绘图包转换状态

转换操作完成后，它将返回 `conversionId`。 可以通过检查绘图包转换过程的状态来访问 `conversionId`。 然后可以使用 `conversionId` 访问转换后的数据。

检查转换过程的状态并检索 `conversionId`：

1. 在 Postman 应用中，选择“新建”。

2. 在“新建”窗口中，选择“HTTP 请求” 。

3. 在“请求名称”中输入请求名称，比如 GET Conversion Status。

4. 选择“GET”HTTP 方法：

5. 输入在[转换绘图包](#convert-a-drawing-package)中复制的 `status URL`。 请求应类似于以下 URL（将 `{Azure-Maps-Primary-Subscription-key}` 替换为主订阅密钥）：

    ```http
    https://us.atlas.microsoft.com/conversions/operations/<operationId>?api-version=2.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

6. 选择 **Send**。

7. 在响应窗口中，选择“标头”选项卡。 

8. 复制“Resource-Location”键的值，即 `resource location URL`。 `resource location URL` 包含唯一标识符 (`conversionId`)，其他 API 可以使用它来访问转换后的地图数据。

      :::image type="content" source="./media/tutorial-creator-indoor-maps/data-conversion-id.png" alt-text="复制转换 ID。":::

示例绘图包应该在没有错误或警告的情况下进行转换。 不过，如果你收到来自自己的绘图包的错误或警告，JSON 响应包含指向[绘图错误可视化工具](drawing-error-visualizer.md)的链接。 可使用绘图错误可视化工具检查错误和警告的详细信息。 若要获取有关解决转换错误和警告的建议，请参阅[绘图转换错误和警告](drawing-conversion-error-codes.md)。

下面的 JSON 片段显示了一个示例转换警告：

```json
{
    "operationId": "<operationId>",
    "created": "2021-05-19T18:24:28.7922905+00:00",
    "status": "Succeeded",
     "warning": {
        "code": "dwgConversionProblem",
        "details": [
            {
                "code": "warning",
                "details": [
                    {
                        "code": "manifestWarning",
                        "message": "Ignoring unexpected JSON property: unitProperties[0].nonWheelchairAccessible with value False"
                    }
                ]
            }
        ]
    },
    "properties": {
        "diagnosticPackageLocation": "https://atlas.microsoft.com/mapData/ce61c3c1-faa8-75b7-349f-d863f6523748?api-version=1.0"
    }
}
```

## <a name="create-a-dataset"></a>创建数据集

数据集是地图特征（如建筑物、楼层和房间等）的集合。 若要创建数据集，请使用[数据集创建 API](/rest/api/maps/v2/dataset/create)。 数据集创建 API 需要使用已转换绘图包的 `conversionId`，并返回已创建数据集的 `datasetId`。

创建数据集：

1. 在 Postman 应用中，选择“新建”。

2. 在“新建”窗口中，选择“HTTP 请求” 。

3. 在“请求名称”中输入请求名称，比如 POST Dataset Create。

4. 选择“POST”HTTP 方法。

5. 在[数据集 API](/rest/api/maps/v2/dataset) 中输入以下 URL。 请求应类似于以下 URL（将 `{Azure-Maps-Primary-Subscription-key}` 替换为主订阅密钥，并将 `{conversionId` 替换为在[检查绘图包转换状态](#check-the-drawing-package-conversion-status)中获取的 `conversionId`）：

    ```http
    https://us.atlas.microsoft.com/datasets?api-version=2.0&conversionId={conversionId}&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

6. 选择 **Send**。

7. 在响应窗口中，选择“标头”选项卡。 

8. 复制“Operation-Location”键的值，即 `status URL`。 我们将使用 `status URL` 检查数据集的状态。

    :::image type="content" source="./media/tutorial-creator-indoor-maps/data-dataset-location-url.png" border="true" alt-text="复制数据集的“位置”键值。":::

### <a name="check-the-dataset-creation-status"></a>检查数据集创建状态

检查数据集创建过程的状态并检索 `datasetId`：

1. 在 Postman 应用中，选择“新建”。

2. 在“新建”窗口中，选择“HTTP 请求” 。

3. 在“请求名称”中输入请求名称，比如 GET Dataset Status。

4. 选择“GET”HTTP 方法。

5. 输入在[创建数据集](#create-a-dataset)中复制的 `status URL`。 请求应类似于以下 URL（将 `{Azure-Maps-Primary-Subscription-key}` 替换为主订阅密钥）：

    ```http
    https://us.atlas.microsoft.com/datasets/operations/<operationId>?api-version=2.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

6. 选择 **Send**。

7. 在响应窗口中，选择“标头”选项卡。Resource-Location 键的值为 `resource location URL`。 `resource location URL` 包含数据集的唯一标识符 (`datasetId`)。 

8. 复制 `datasetId`，因为在本教程的下一部分会用到它。

    :::image type="content" source="./media/tutorial-creator-indoor-maps/dataset-id.png" alt-text="复制数据集 ID。":::

## <a name="create-a-tileset"></a>创建图块集

图块集是一组在定位中渲染的矢量图块。 图块集是通过现有数据集创建的。 不过，图块集独立于它所源自的数据集。 如果数据集被删除，图块集将继续存在。

创建图块集：

1. 在 Postman 应用中，选择“新建”。

2. 在“新建”窗口中，选择“HTTP 请求” 。

3. 在“请求名称”中输入请求名称，比如 POST Tileset Create。

4. 选择“POST”HTTP 方法。

5. 在[图块集 API](/rest/api/maps/v2/tileset) 中输入以下 URL。 请求应类似于以下 URL（将 `{Azure-Maps-Primary-Subscription-key}` 替换为主订阅密钥），并将 `{datasetId` 替换为在[检查数据集创建状态](#check-the-dataset-creation-status)中获取的 `datasetId`：

    ```http
    https://us.atlas.microsoft.com/tilesets?api-version=2.0&datasetID={datasetId}&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

6. 选择 **Send**。

7. 在响应窗口中，选择“标头”选项卡。 

8. 复制“Operation-Location”键的值，即 `status URL`。 我们将使用 `status URL` 检查图块集的状态。

    :::image type="content" source="./media/tutorial-creator-indoor-maps/data-tileset-location-url.png" border="true" alt-text="复制图块集状态 URL 的值。":::

### <a name="check-the-tileset-creation-status"></a>检查图块集创建状态

检查图块集创建过程的状态并检索 `tilesetId`：

1. 在 Postman 应用中，选择“新建”。

2. 在“新建”窗口中，选择“HTTP 请求” 。

3. 在“请求名称”中输入请求名称，比如 GET Tileset Status。

4. 选择“GET”HTTP 方法。

5. 输入在[创建图块集](#create-a-tileset)中复制的 `status URL`。 请求应类似于以下 URL（将 `{Azure-Maps-Primary-Subscription-key}` 替换为主订阅密钥）：

    ```http
    https://us.atlas.microsoft.com/tilesets/operations/<operationId>?api-version=2.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

6. 选择 **Send**。

7. 在响应窗口中，选择“标头”选项卡。Resource-Location 键的值为 `resource location URL`。  `resource location URL` 包含数据集的唯一标识符 (`tilesetId`)。

    :::image type="content" source="./media/tutorial-creator-indoor-maps/tileset-id.png" alt-text="复制图块集 ID。":::

## <a name="query-datasets-with-wfs-api"></a>使用 WFS API 查询数据集

可以使用 [WFS API](/rest/api/maps/v2/wfs) 查询数据集。 可以使用 WFS API 查询所有特征集合或特定集合。 在本教程的此部分中，我们将同时实现这两点。 首先，我们将查询所有集合，然后查询 `unit` 集合。

### <a name="query-for-feature-collections"></a>查询特征集合

查询数据集中的所有集合：

1. 在 Postman 应用中，选择“新建”。

2. 在“新建”窗口中，选择“HTTP 请求” 。

3. 在“请求名称”中输入请求名称，比如 GET Dataset Collections。

4. 选择“GET”HTTP 方法。

5. 在 [WFS API](/rest/api/maps/v2/wfs) 中输入以下 URL。 请求应类似于以下 URL（将 `{Azure-Maps-Primary-Subscription-key}` 替换为主订阅密钥），并将 `{datasetId` 替换为在[检查数据集创建状态](#check-the-dataset-creation-status)中获取的 `datasetId`：

    ```http
    https://us.atlas.microsoft.com/wfs/datasets/{datasetId}/collections?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=2.0
    ```

6. 选择 **Send**。

7. 响应正文以 GeoJSON 格式返回，并包含数据集中的所有集合。 为了简单起见，此部分的示例只显示 `unit` 集合。 若要查看包含所有集合的示例，请参阅 [WFS 描述集合 API](/rest/api/maps/v2/wfs/collection-description)。 若要详细了解任意集合，可以选择 `link` 元素中的任何一个 URL。

    ```json
    {
    "collections": [
        {
            "name": "unit",
            "description": "A physical and non-overlapping area which might be occupied and traversed by a navigating agent. Can be a hallway, a room, a courtyard, etc. It is surrounded by physical obstruction (wall), unless the is_open_area attribute is equal to true, and one must add openings where the obstruction shouldn't be there. If is_open_area attribute is equal to true, all the sides are assumed open to the surroundings and walls are to be added where needed. Walls for open areas are represented as a line_element or area_element with is_obstruction equal to true.",
            "links": [
                {
                    "href": "https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit/definition?api-version=1.0",
                    "rel": "describedBy",
                    "title": "Metadata catalogue for unit"
                },
                {
                    "href": "https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit/items?api-version=1.0",
                    "rel": "data",
                    "title": "unit"
                }
                {
                    "href": "https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit?api-version=1.0",
                    "rel": "self",
                    "title": "Metadata catalogue for unit"
                }
            ]
        },
    ```

### <a name="query-for-unit-feature-collection"></a>查询单元特征集合

在本部分中，我们将查询 [WFS API](/rest/api/maps/v2/wfs) 以获取 `unit` 特征集合。

查询数据集中的单元集合：

1. 在 Postman 应用中，选择“新建”。

2. 在“新建”窗口中，选择“HTTP 请求” 。

3. 在“请求名称”中输入请求名称，比如 GET Unit Collection。

4. 选择“GET”HTTP 方法。

5. 输入以下 URL（将 `{Azure-Maps-Primary-Subscription-key}` 替换为主订阅密钥，将 `{datasetId` 替换为在[检查数据集创建状态](#check-the-dataset-creation-status)中获得的 `datasetId`）：

    ```http
    https://us.atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit/items?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=2.0
    ```

6. 选择 **Send**。

7. 返回响应后，为 `unit` 特征之一复制特征 `id`。 在下面的示例中，特征 `id` 是“UNIT26”。 在本教程中，我们将在下一节中使用“UNIT26”作为特征 `id`。

     ```json
    {
        "type": "FeatureCollection",
        "features": [
            {
                "type": "Feature",
                "geometry": {
                    "type": "Polygon",
                    "coordinates": ["..."]
                },
                "properties": {
                    "original_id": "b7410920-8cb0-490b-ab23-b489fd35aed0",
                    "category_id": "CTG8",
                    "is_open_area": true,
                    "navigable_by": [
                        "pedestrian"
                    ],
                    "route_through_behavior": "allowed",
                    "level_id": "LVL14",
                    "occupants": [],
                    "address_id": "DIR1",
                    "name": "157"
                },
                "id": "UNIT26",
                "featureType": ""
            }, {"..."}
        ]
    }
    ```

## <a name="create-a-feature-stateset"></a>创建特征状态集

特征状态集定义支持它们的特定特征的动态属性和值。 在本节中，我们将创建一个状态集，为 occupancy 属性定义布尔值和相应的样式。

创建状态集：

1. 在 Postman 应用中，选择“新建”。

2. 在“新建”窗口中，选择“HTTP 请求” 。

3. 在“请求名称”中输入请求名称，比如 POST Create Stateset。

4. 选择“POST”HTTP 方法。

5. 在[状态集 API](/rest/api/maps/v2/feature-state/create-stateset) 中输入以下 URL。 请求应类似于以下 URL（将 `{Azure-Maps-Primary-Subscription-key}` 替换为主订阅密钥，将 `{datasetId` 替换为在[检查数据集创建状态](#check-the-dataset-creation-status)中获得的 `datasetId`）：

    ```http
    https://us.atlas.microsoft.com/featurestatesets?api-version=2.0&datasetId={datasetId}&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

6. 选择“标头”选项卡  。

7. 在“键”字段中，选择 `Content-Type`。 

8. 在“值”字段中，选择 `application/json`。

     :::image type="content" source="./media/tutorial-creator-indoor-maps/stateset-header.png"alt-text="用于创建状态集的“标头”选项卡信息。":::

9. 选择“正文”选项卡。

10. 在下拉列表中，选择“原始”和“JSON”。

11. 复制以下 JSON 样式，然后将其粘贴到“正文”窗口中：

    ```json
    {
       "styles":[
          {
             "keyname":"occupied",
             "type":"boolean",
             "rules":[
                {
                   "true":"#FF0000",
                   "false":"#00FF00"
                }
             ]
          }
       ]
    }
    ```

12. 选择 **Send**。

13. 响应成功返回后，复制响应正文中的 `statesetId`。 在下一节中，我们将使用 `statesetId` 更改特征 `id` 为“UNIT26”的单元的 `occupancy` 属性状态。

    :::image type="content" source="./media/tutorial-creator-indoor-maps/response-stateset-id.png"alt-text="状态集 ID 响应。":::

### <a name="update-a-feature-state"></a>更新特征状态

更新特征 `id` 为“UNIT26”的单元的 `occupied` 状态：

1. 在 Postman 应用中，选择“新建”。

2. 在“新建”窗口中，选择“HTTP 请求” 。

3. 在“请求名称”中输入请求名称，比如 PUT Set Stateset。

4. 选择“PUT”HTTP 方法。

5. 在[特征状态集 API](/rest/api/maps/v2/feature-state/create-stateset) 中输入以下 URL。 请求应类似于以下 URL（将 `{Azure-Maps-Primary-Subscription-key}` 替换为主订阅密钥，将 `{statesetId` 替换为在[创建特征状态集](#create-a-feature-stateset)中获得的 `statesetId`）：

    ```http
    https://us.atlas.microsoft.com/featurestatesets/{statesetId}/featureStates/UNIT26?api-version=2.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

6. 选择“标头”选项卡  。

7. 在“键”字段中，选择 `Content-Type`。 

8. 在“值”字段中，选择 `application/json`。

     :::image type="content" source="./media/tutorial-creator-indoor-maps/stateset-header.png"alt-text="用于创建状态集的“标头”选项卡信息。":::

9. 选择“正文”选项卡。

10. 在下拉列表中，选择“原始”和“JSON”。

11. 复制以下 JSON 样式，然后将其粘贴到“正文”窗口中：

    ```json
    {
        "states": [
            {
                "keyName": "occupied",
                "value": true,
                "eventTimestamp": "2020-11-14T17:10:20"
            }
        ]
    }
    ```

    >[!NOTE]
    > 只有当 POST 请求的时间戳晚于上一请求的时间戳时，才会保存更新。

12. 选择 **Send**。

13. 更新完成后，你就会收到 `200 OK` HTTP 状态代码。 如果你已为室内地图实现[动态样式](indoor-map-dynamic-styling.md)，更新将会在指定的时间戳显示在渲染的地图中。

可以使用[特征状态获取 API](/rest/api/maps/v2/feature-state/get-states)通过特征 `id` 检索特征的状态。 还可以使用[特征状态删除 API](/rest/api/maps/v2/feature-state/delete-stateset) 来删除状态集及其资源。

要详细了解本文中讨论的各种 Azure Maps Creator 服务，请参阅 [Creator 室内地图](creator-indoor-maps.md)。

## <a name="clean-up-resources"></a>清理资源

没有任何需要清理的资源。

## <a name="next-steps"></a>后续步骤

若要了解如何使用室内地图模块，请参阅

> [!div class="nextstepaction"]
> [使用室内定位模块](how-to-use-indoor-module.md)
