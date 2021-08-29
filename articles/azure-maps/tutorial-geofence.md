---
title: 教程：在 Microsoft Azure Map 上创建地理围栏并跟踪设备
description: 有关如何设置地理围栏的教程。 了解如何使用 Azure Maps 空间服务跟踪与地理围栏相关的设备
author: anastasia-ms
ms.author: v-stharr
ms.date: 7/06/2021
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
ms.custom: mvc
ms.openlocfilehash: f9b2c74f25d5f27385b604d53530edbdb57a91fd
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121750204"
---
# <a name="tutorial-set-up-a-geofence-by-using-azure-maps"></a>教程：使用 Azure Maps 设置地域隔离区

本教程将引导你完成创建和使用 Azure Maps 地理围栏服务的基本步骤。 

假设出现了下面这种情景：

*负责构造的站点管理员需要在设备进入和退出构造区域周界范围时对其进行跟踪。每当有设备退出或进入这些范围时，运营管理员会收到邮件通知。*

Azure Maps 提供许多服务来支持对进入和退出构造区域的设备的跟踪。 在本教程中，将：

> [!div class="checklist"]
> * 上传用于定义要监视的构造站点区域的[地理围栏 GeoJSON 数据](geofence-geojson.md)。 你将使用[数据上传 API](/rest/api/maps/data-v2/upload-preview) 将地理围栏作为多边形坐标上传到你的 Azure Maps 账户。
> * 设置两个[逻辑应用](../event-grid/handler-webhooks.md#logic-apps)，当设备进入或退出地理围栏区域，将触发应用并向构造站点的运营管理员发送邮件通知。
> * 使用 [Azure 事件网格](../event-grid/overview.md)订阅 Azure Maps 地理围栏进入和退出事件。 你将设置两个 Webhook 事件订阅，用于调用两个逻辑应用中定义的 HTTP 终结点。 然后，逻辑应用将发送有关设备移出或移入地理围栏的相应邮件通知。
> * 使用[搜索地理围栏 GET API](/rest/api/maps/spatial/getgeofence) 在有设备退出和进入地理围栏区域时接收通知。

## <a name="prerequisites"></a>先决条件

1. [创建 Azure Maps 帐户](quick-demo-map-app.md#create-an-azure-maps-account)。
2. [获取主订阅密钥](quick-demo-map-app.md#get-the-primary-key-for-your-account)（亦称为“主密钥”或“订阅密钥”）。

本教程使用 [Postman](https://www.postman.com/) 应用程序，但你也可以选择其他 API 开发环境。

## <a name="upload-geofencing-geojson-data"></a>上传地理围栏 GeoJSON 数据

在本教程中，你将上传包含 `FeatureCollection` 的地理围栏 GeoJSON 数据。 `FeatureCollection` 包含用于定义站点内多边形区域的两个地理围栏。 第一个地理围栏不设过期时间或时间限制。 第二个地理围栏只能在营业时间（太平洋时区上午 9:00 到下午 5:00）接受查询，且在 2022 年 1 月 1 日后将失效。 有关 GeoJSON 数据格式的详细信息，请参阅[为 GeoJSON 数据设置地理围栏](geofence-geojson.md)。

>[!TIP]
>可以随时更新地理围栏数据。 有关详细信息，请参阅[数据上传 API](/rest/api/maps/data-v2/upload-preview)。

若要上传地理围栏 GeoJSON 数据：

1. 在 Postman 应用中，选择“新建”。

2. 在“新建”窗口中，选择“HTTP 请求” 。

3. 在“请求名称”中输入请求名称，比如 POST GeoJSON Data Upload。

4. 选择“POST”HTTP 方法。

5. 输入以下 URL。 请求应类似于以下 URL（将 `{Azure-Maps-Primary-Subscription-key}` 替换为主订阅密钥）：

    ```HTTP
    https://us.atlas.microsoft.com/mapData?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=2.0&dataFormat=geojson
    ```

    URL 路径中的 `geojson` 参数表示正在上传的数据的数据格式。

6. 选择“正文”选项卡。

7. 在下拉列表中，选择“原始”和“JSON”。

8. 复制以下 GeoJSON 数据，然后将其粘贴到“正文”窗口中：

   ```JSON
   {
      "type": "FeatureCollection",
      "features": [
        {
          "type": "Feature",
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -122.13393688201903,
                  47.63829579223815
                ],
                [
                  -122.13389128446579,
                  47.63782047131512
                ],
                [
                  -122.13240802288054,
                  47.63783312249837
                ],
                [
                  -122.13238388299942,
                  47.63829037035086
                ],
                [
                  -122.13393688201903,
                  47.63829579223815
                ]
              ]
            ]
          },
          "properties": {
            "geometryId": "1"
          }
        },
        {
          "type": "Feature",
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -122.13374376296996,
                  47.63784758098976
                ],
                [
                  -122.13277012109755,
                  47.63784577367854
                ],
                [
                  -122.13314831256866,
                  47.6382813338708
                ],
                [
                  -122.1334782242775,
                  47.63827591198201
                ],
                [
                  -122.13374376296996,
                  47.63784758098976
                ]
              ]
            ]
          },
          "properties": {
            "geometryId": "2",
            "validityTime": {
            "expiredTime": "2022-01-01T00:00:00",
            "validityPeriod": [
                {
                  "startTime": "2020-07-15T16:00:00",
                  "endTime": "2020-07-15T24:00:00",
                  "recurrenceType": "Daily",
                  "recurrenceFrequency": 1,
                  "businessDayOnly": true
                }
              ]
            }
          }
        }
      ]
   }
   ```

9. 选择 **Send**。

10. 在响应窗口中，选择“标头”选项卡。

11. 复制“Operation-Location”键的值，即 `status URL`。 我们将使用 `status URL` 检查 GeoJSON 数据上传的状态。

    ```http
    https://us.atlas.microsoft.com/mapData/operations/<operationId>?api-version=2.0
    ```

### <a name="check-the-geojson-data-upload-status"></a>检查 GeoJSON 数据上传状态

若要检查 GeoJSON 数据的状态并检索其唯一 ID (`udid`)：

1. 选择“新建”。

2. 在“新建”窗口中，选择“HTTP 请求” 。

3. 在“请求名称”中输入请求名称，比如 GET Data Upload Status。

4. 选择“GET”HTTP 方法。

5. 输入在[上传地理围栏 GeoJSON 数据](#upload-geofencing-geojson-data)中复制的 `status URL`。 请求应类似于以下 URL（将 `{Azure-Maps-Primary-Subscription-key}` 替换为主订阅密钥）：

   ```HTTP
   https://us.atlas.microsoft.com/mapData/<operationId>?api-version=2.0&subscription-key={Subscription-key}
   ```

6. 选择 **Send**。

7. 在响应窗口中，选择“标头”选项卡。

8. 复制“Resource-Location”键的值，即 `resource location URL`。 `resource location URL` 包含已上传数据的唯一标识符 (`udid`)。 保存 `udid`，在本教程的最后一部分中需要用它查询获取地理围栏 API。

    :::image type="content" source="./media/tutorial-geofence/resource-location-url.png" alt-text="复制资源位置 URL。":::

### <a name="optional-retrieve-geojson-data-metadata"></a>（可选）检索 GeoJSON 数据元数据

可以从上传的数据中检索元数据。 元数据包含资源位置 URL、创建日期、更新日期、大小和上传状态等信息。

检索内容元数据：

1. 选择“新建”。

2. 在“新建”窗口中，选择“HTTP 请求” 。

3. 在“请求名称”中输入请求名称，比如 GET Data Upload Metadata。

4. 选择“GET”HTTP 方法。

5. 输入在[检查 GeoJSON 数据上传状态](#check-the-geojson-data-upload-status)中复制的 `resource Location URL`。 请求应类似于以下 URL（将 `{Azure-Maps-Primary-Subscription-key}` 替换为主订阅密钥）：

    ```http
    https://us.atlas.microsoft.com/mapData/metadata/{udid}?api-version=2.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

6. 在响应窗口中，选择“正文”选项卡。元数据应类似于以下 JSON 片段：

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

## <a name="create-workflows-in-azure-logic-apps"></a>在 Azure 逻辑应用中创建工作流

接下来，我们将创建两个用于触发电子邮件通知的[逻辑应用](../event-grid/handler-webhooks.md#logic-apps)终结点。 

若要创建逻辑应用：

1. 登录 [Azure 门户](https://portal.azure.com)。

2. 在 Azure 门户的左上角，选择“创建资源”。

3. 在“在市场中搜索”框中键入“逻辑应用”。

4. 从结果中选择“逻辑应用”。 然后选择“创建”。

5. 在“逻辑应用”页上，输入以下值：
    * 要用于此逻辑应用的“订阅”。
    * 此逻辑应用的“资源组”名称。 可以选择新建或使用现有的资源组 。
    * 你的逻辑应用的“逻辑应用名称”。 本例使用 `Equipment-Enter` 作为名称。

    考虑到本教程的目的，请将其余值保留为其默认设置。

    :::image type="content" source="./media/tutorial-geofence/logic-app-create.png" alt-text="创建逻辑应用的屏幕截图。":::

6. 选择“查看 + 创建”。 检查设置，然后选择“创建”。

7. 部署成功完成后，选择“转到资源”。

8. 在“逻辑应用设计器”中，向下滚动到“从常见触发器开始”部分 。 选择“收到 HTTP 请求时”。

     :::image type="content" source="./media/tutorial-geofence/logic-app-trigger.png" alt-text="创建逻辑应用 HTTP 触发器的屏幕截图。":::

9. 在逻辑应用设计器的右上角，选择“保存”。 随即自动生成“HTTP POST URL”。 保存 URL。 下一部分中需要用它来创建事件终结点。

    :::image type="content" source="./media/tutorial-geofence/logic-app-httprequest.png" alt-text="逻辑应用 HTTP 请求 URL 和 JSON 的屏幕截图。":::

10. 选择“+ 新步骤”。

11. 在搜索框中，键入“`outlook.com email`”。 在“操作”列表中，向下滚动并选择“发送电子邮件 (V2)” 。
  
    :::image type="content" source="./media/tutorial-geofence/logic-app-designer.png" alt-text="创建逻辑应用设计器的屏幕截图。":::

12. 登录到 Outlook 帐户。 确保选择“是”以允许逻辑应用访问该帐户。 填充用于发送电子邮件的字段。

    :::image type="content" source="./media/tutorial-geofence/logic-app-email.png" alt-text="创建逻辑应用发送电子邮件步骤的屏幕截图。":::

    >[!TIP]
    > 在电子邮件通知中检索 GeoJSON 响应数据（如 `geometryId` 或 `deviceId`）。 可以将逻辑应用配置为读取事件网格发送的数据。 有关如何配置逻辑应用以使用事件数据并将其传递到电子邮件通知的详细信息，请参阅[教程：使用事件网格和逻辑应用发送有关 Azure IoT 中心事件的电子邮件通知](../event-grid/publish-iot-hub-events-to-logic-apps.md)。

13. 在“逻辑应用设计器”的左上角，选择“保存” 。

14. 重复相同的过程以创建第二个逻辑应用，以便在设备退出构造站点时通知管理员。 将逻辑应用命名为 `Equipment-Exit`。

## <a name="create-azure-maps-events-subscriptions"></a>创建 Azure Maps 事件订阅

Azure Maps 支持[三种事件类型](../event-grid/event-schema-azure-maps.md)。 在本教程中，我们将创建以下两个事件的订阅：

* 地理围栏进入事件
* 地理围栏退出事件

若要创建地理围栏退出和进入事件订阅：

1. 在 Azure Maps 帐户中，选择“订阅”。

2. 选择你的订阅名称。

3. 在设置菜单中，选择“事件”。

    :::image type="content" source="./media/tutorial-geofence/events-tab.png" alt-text="转到 Azure Maps 帐户事件的屏幕截图。":::

4. 在事件页面中，选择“+ 事件订阅”。

    :::image type="content" source="./media/tutorial-geofence/create-event-subscription.png" alt-text="创建 Azure Maps 事件订阅的屏幕截图。":::

5. 在“创建事件订阅”页中输入以下值：
    * 事件订阅的“名称”。
    * “事件架构”应为“事件网格架构” 。
    * 此事件订阅的“系统主题名称”，在本例中为 `Contoso-Construction`。
    * 对于“事件类型筛选器”，请选择 `Geofence Entered` 作为事件类型。
    * 对于“终结点类型”，选择 `Web Hook`。
    * 对于“终结点”，复制在上一个部分创建的“逻辑应用进入”终结点的 HTTP POST URL。 如果忘记保存，可以直接返回到逻辑应用设计器并从 HTTP 触发器步骤复制它。

    :::image type="content" source="./media/tutorial-geofence/events-subscription.png" alt-text="Azure Maps 事件订阅详细信息的屏幕截图。":::

6. 选择“创建”。

7. 对地理围栏退出事件重复相同的过程。 请确保选择 `Geofence Exited` 作为事件类型。

## <a name="use-spatial-geofence-get-api"></a>使用空间地理围栏获取 API

接下来，我们将使用[空间地理围栏获取 API](/rest/api/maps/spatial/getgeofence) 在有设备进入或退出地理围栏时向运营管理员发送电子邮件通知。

每个设备都有一个 `deviceId`。 在本教程中，你将跟踪单个设备，其唯一 ID 为 `device_1`。

下图显示了一段时间内设备的五个位置，并从位于地理围栏外的“启动”位置开始。 考虑到本教程的目的，不定义“启动”位置，因为我们不会在该位置查询设备。

当使用某个设备位置（指示最初进入或退出地理围栏的位置）查询[空间地理围栏获取 API](/rest/api/maps/spatial/getgeofence) 时，事件网格将调用相应的逻辑应用终结点，并向运营管理员发送电子邮件通知。

以下每个部分都使用设备的 5 个不同位置坐标发出 API 请求。

![Azure Maps 中的地理围栏图](./media/tutorial-geofence/geofence.png)

### <a name="equipment-location-1-47638237-122132483"></a>设备位置 1 (47.638237,-122.132483)

1. 在 Postman 应用中，选择“新建”。

2. 在“新建”窗口中，选择“HTTP 请求” 。

3. 在“请求名称”中输入请求名称，比如 Location 1。

4. 选择“GET”HTTP 方法。

5. 输入以下 URL。 请求应类似于以下 URL（将 `{Azure-Maps-Primary-Subscription-key}` 替换为主订阅密钥，并将 `{udid}` 替换为在[上传地理围栏 GeoJSON 数据部分](#upload-geofencing-geojson-data)中保存的 `udid`）。

   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.638237&lon=-122.1324831&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

6. 选择 **Send**。

7. 响应应类似于以下 GeoJSON 片段：

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_1",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.638291,
          "nearestLon": -122.132483
        },
        {
          "deviceId": "device_1",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "2",
          "distance": 999.0,
          "nearestLat": 47.638053,
          "nearestLon": -122.13295
        }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": true
    }
    ```

在上述 GeoJSON 响应中，与主站点地理围栏的距离为负表示设备在地理围栏内。 与子场地地理围栏的距离为正表示设备在子场地地理围栏外。 因为这是第一次在主站点地理围栏内找到此设备，所以 `isEventPublished` 参数设置为 `true`。 运营管理员会收到一封电子邮件通知，指示设备已进入地理围栏。

### <a name="location-2-4763800-122132531"></a>位置 2 (47.63800,-122.132531)

1. 在 Postman 应用中，选择“新建”。

2. 在“新建”窗口中，选择“HTTP 请求” 。

3. 在“请求名称”中输入请求名称，比如 Location 2。

4. 选择“GET”HTTP 方法。

5. 输入以下 URL。 请求应类似于以下 URL（将 `{Azure-Maps-Primary-Subscription-key}` 替换为主订阅密钥，并将 `{udid}` 替换为在[上传地理围栏 GeoJSON 数据部分](#upload-geofencing-geojson-data)中保存的 `udid`）。

   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63800&lon=-122.132531&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

6. 选择 **Send**。

7. 响应应类似于以下 GeoJSON 片段：

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.637997,
          "nearestLon": -122.132399
        },
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "2",
          "distance": 999.0,
          "nearestLat": 47.63789,
          "nearestLon": -122.132809
        }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": false
    }
    ````

在上述 GeoJSON 响应中，设备留在了主站点地理围栏中，且未进入子站点地理围栏。 因此，将 `isEventPublished` 参数设置为 `false`，运营管理员不会收到任何电子邮件通知。

### <a name="location-3-4763810783315048-12213336020708084"></a>位置 3 (47.63810783315048,-122.13336020708084)

1. 在 Postman 应用中，选择“新建”。

2. 在“新建”窗口中，选择“HTTP 请求” 。

3. 在“请求名称”中输入请求名称，比如 Location 3。

4. 选择“GET”HTTP 方法。

5. 输入以下 URL。 请求应类似于以下 URL（将 `{Azure-Maps-Primary-Subscription-key}` 替换为主订阅密钥，并将 `{udid}` 替换为在[上传地理围栏 GeoJSON 数据部分](#upload-geofencing-geojson-data)中保存的 `udid`）。

    ```HTTP
      https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.63810783315048&lon=-122.13336020708084&searchBuffer=5&isAsync=True&mode=EnterAndExit
      ```

6. 选择 **Send**。

7. 响应应类似于以下 GeoJSON 片段：

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.638294,
          "nearestLon": -122.133359
        },
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "2",
          "distance": -999.0,
          "nearestLat": 47.638161,
          "nearestLon": -122.133549
        }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": true
    }
    ````

在上述 GeoJSON 响应中，设备仍位于主站点地理围栏内，但已进入子站点地理围栏。 因此，`isEventPublished` 参数设置为 `true`。 运营管理员收到一封电子邮件通知，指示设备已进入地理围栏。

>[!NOTE]
>如果设备在营业时间后移入子站点，则不会发布任何事件，运营管理员不会收到任何通知。  

### <a name="location-4-47637988-1221338344"></a>位置 4 (47.637988,-122.1338344)

1. 在 Postman 应用中，选择“新建”。

2. 在“新建”窗口中，选择“HTTP 请求” 。

3. 在“请求名称”中输入请求名称，比如 Location 4。

4. 选择“GET”HTTP 方法。

5. 输入以下 URL。 请求应类似于以下 URL（将 `{Azure-Maps-Primary-Subscription-key}` 替换为主订阅密钥，并将 `{udid}` 替换为在[上传地理围栏 GeoJSON 数据部分](#upload-geofencing-geojson-data)中保存的 `udid`）。

    ```HTTP
    https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.637988&userTime=2023-01-16&lon=-122.1338344&searchBuffer=5&isAsync=True&mode=EnterAndExit
    ```

6. 选择 **Send**。

7. 响应应类似于以下 GeoJSON 片段：

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.637985,
          "nearestLon": -122.133907
        }
      ],
      "expiredGeofenceGeometryId": [
        "2"
      ],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": false
    }
    ````

在上述 GeoJSON 响应中，设备仍留在主站点地理围栏中，但已退出子站点地理围栏。 但是，如果留意，会发现 `userTime` 值在 `expiredTime` 之后（如地理围栏数据中所定义）。 因此，将 `isEventPublished` 参数设置为 `false`，运营管理员不会收到任何电子邮件通知。

### <a name="location-5-4763799--122134505"></a>位置 5 (47.63799, -122.134505)

1. 在 Postman 应用中，选择“新建”。

2. 在“新建”窗口中，选择“HTTP 请求” 。

3. 在“请求名称”中输入请求名称，比如 Location 5。

4. 选择“GET”HTTP 方法。

5. 输入以下 URL。 请求应类似于以下 URL（将 `{Azure-Maps-Primary-Subscription-key}` 替换为主订阅密钥，并将 `{udid}` 替换为在[上传地理围栏 GeoJSON 数据部分](#upload-geofencing-geojson-data)中保存的 `udid`）。

    ```HTTP
    https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.63799&lon=-122.134505&searchBuffer=5&isAsync=True&mode=EnterAndExit
    ```

6. 选择 **Send**。

7. 响应应类似于以下 GeoJSON 片段：

    ```json
    {
      "geometries": [
      {
        "deviceId": "device_01",
        "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
        "geometryId": "1",
        "distance": -999.0,
        "nearestLat": 47.637985,
        "nearestLon": -122.133907
      },
      {
        "deviceId": "device_01",
        "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
        "geometryId": "2",
        "distance": 999.0,
        "nearestLat": 47.637945,
        "nearestLon": -122.133683
      }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": true
    }
    ````

在上述 GeoJSON 响应中，设备已退出主站点地理围栏。 因此，将 `isEventPublished` 参数设置为 `true`，运营管理员将收到指示设备已退出地理围栏的电子邮件通知。

还可以[使用事件网格和逻辑应用发送电子邮件通知](../event-grid/publish-iot-hub-events-to-logic-apps.md)，并使用 Azure Maps 检查[事件网格中支持的事件处理程序](../event-grid/event-handlers.md)。

## <a name="clean-up-resources"></a>清理资源

没有需要清理的资源。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [在 Azure 逻辑应用中处理内容类型](../logic-apps/logic-apps-content-type.md)
