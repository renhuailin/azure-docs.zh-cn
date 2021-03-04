---
title: 为 Android 地图创建数据源 |Microsoft Azure 映射
description: 了解如何为地图创建数据源。 了解 Azure Maps Android SDK 使用的数据源： GeoJSON 源和矢量图块。
author: rbrundritt
ms.author: richbrun
ms.date: 12/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: fc68dc25aad3671a55e5c11cbee094b4027e7070
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102047666"
---
# <a name="create-a-data-source-android-sdk"></a> (Android SDK 创建数据源) 

Azure Maps Android SDK 将数据存储在数据源中。 使用数据源优化用于查询和呈现的数据操作。 目前有两种类型的数据源：

- **GeoJSON source**：在本地管理 GeoJSON 格式的原始位置数据。 适用于小型到中型数据集， (的) 的数量为几十万个形状。
- **矢量图块源**：基于地图平铺系统，为当前地图视图加载格式为矢量图块的数据。 适用于大到大数据集 (数百万或数十亿个形状) 。

## <a name="geojson-data-source"></a>GeoJSON 数据源

Azure Maps 使用 GeoJSON 作为其主数据模型之一。 GeoJSON 是以 JSON 格式表示地理空间数据的开放地理空间标准方式。 Azure Maps Android SDK 中可用的 GeoJSON 类，可轻松创建和序列化 GeoJSON 数据。 在类中加载并存储 GeoJSON 数据 `DataSource` ，并使用层进行呈现。 下面的代码演示如何在 Azure Maps 中创建 GeoJSON 对象。

```java
/*
    Raw GeoJSON feature
    
    {
         "type": "Feature",
         "geometry": {
             "type": "Point",
             "coordinates": [-100, 45]
         },
         "properties": {
             "custom-property": "value"
         }
    }

*/

//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(-100, 45));

//Add a property to the feature.
feature.addStringProperty("custom-property", "value");

//Add the feature to the data source.
source.add(feature);
```

另外，还可以先将属性加载到 JsonObject 中，然后再将其创建，如下所示。

```java
//Create a JsonObject to store properties for the feature.
JsonObject properties = new JsonObject();
properties.addProperty("custom-property", "value");

Feature feature = Feature.fromGeometry(Point.fromLngLat(-100, 45), properties);
```

创建 GeoJSON 功能后，可以通过映射的属性将数据源添加到地图中 `sources` 。 下面的代码演示如何创建 `DataSource` ，将其添加到映射，并将功能添加到数据源。

```javascript
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Add GeoJSON feature to the data source.
source.add(feature);
```

下面的代码演示了几种创建 GeoJSON 功能、FeatureCollection 和几何图形的方式。

```java
//GeoJSON Point Geometry
Point point = Point.fromLngLat(LONGITUDE, LATITUDE);

//GeoJSON Point Geometry
LineString linestring = LineString.fromLngLats(PointList);

//GeoJSON Polygon Geometry
Polygon polygon = Polygon.fromLngLats(listOfPointList);
 
Polygon polygonFromOuterInner = Polygon.fromOuterInner(outerLineStringObject,innerLineStringObject);

//GeoJSON MultiPoint Geometry
MultiPoint multiPoint = MultiPoint.fromLngLats(PointList);

//GeoJSON MultiLineString Geometry
MultiLineString multiLineStringFromLngLat = MultiLineString.fromLngLats(listOfPointList);

MultiLineString multiLineString = MultiLineString.fromLineString(singleLineString);

//GeoJSON MultiPolygon Geometry
MultiPolygon multiPolygon = MultiPolygon.fromLngLats(listOflistOfPointList);

MultiPolygon multiPolygonFromPolygon = MultiPolygon.fromPolygon(polygon);

MultiPolygon multiPolygonFromPolygons = MultiPolygon.fromPolygons(PolygonList);

//GeoJSON Feature
Feature pointFeature = Feature.fromGeometry(Point.fromLngLat(LONGITUDE, LATITUDE));
 
//GeoJSON FeatureCollection 
FeatureCollection featureCollectionFromSingleFeature = FeatureCollection.fromFeature(pointFeature);
 
FeatureCollection featureCollection = FeatureCollection.fromFeatures(listOfFeatures);
```

### <a name="serialize-and-deserialize-geojson"></a>序列化和反序列化 GeoJSON

功能集合、功能和几何图形类都具有 `fromJson()` 和 `toJson()` 静态方法，有助于进行序列化。 通过方法传递的格式化有效 JSON 字符串 `fromJson()` 将创建 geometry 对象。 此 `fromJson()` 方法还意味着可以使用 Gson 或其他序列化/反序列化策略。 下面的代码演示如何获取字符串化 GeoJSON 功能并将其反序列化为功能类，然后将其序列化为 GeoJSON 字符串。

```java
//Take a stringified GeoJSON object.
String GeoJSON_STRING = "{"
    + "      \"type\": \"Feature\","            
    + "      \"geometry\": {"
    + "            \"type\": \"Point\""
    + "            \"coordinates\": [-100, 45]"
    + "      },"
    + "      \"properties\": {"
    + "            \"custom-property\": \"value\""
    + "      },"
    + "}";

//Deserialize the JSON string into a feature.
Feature feature = Feature.fromJson(GeoJSON_STRING);
 
//Serialize a feature collection to a string.
String featureString = feature.toJson();
```

### <a name="import-geojson-data-from-web-or-assets-folder"></a>从 Web 或资产文件夹导入 GeoJSON 数据

大多数 GeoJSON 文件都包含一个 FeatureCollection。 将 GeoJSON 文件作为字符串读取，并使用 `FeatureCollection.fromJson` 方法对其进行反序列化。

下面的代码是一个可重用的类，用于将 web 或本地资产文件夹中的数据作为字符串导入，并通过回调函数将其返回到 UI 线程。

```java
import android.content.Context;
import android.os.Handler;
import android.os.Looper;
import android.webkit.URLUtil;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.net.URL;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

import javax.net.ssl.HttpsURLConnection;

public class Utils {

    interface SimpleCallback {
        void notify(String result);
    }

    /**
     * Imports data from a web url or asset file name and returns it to a callback.
     * @param urlOrFileName A web url or asset file name that points to data to load.
     * @param context The context of the app.
     * @param callback The callback function to return the data to.
     */
    public static void importData(String urlOrFileName, Context context, SimpleCallback callback){
        importData(urlOrFileName, context, callback, null);
    }
    
    /**
     * Imports data from a web url or asset file name and returns it to a callback.
     * @param urlOrFileName A web url or asset file name that points to data to load.
     * @param context The context of the app.
     * @param callback The callback function to return the data to.
     * @param error A callback function to return errors to.
     */
    public static void importData(String urlOrFileName, Context context, SimpleCallback callback, SimpleCallback error){
        if(urlOrFileName != null && callback != null) {
            ExecutorService executor = Executors.newSingleThreadExecutor();
            Handler handler = new Handler(Looper.getMainLooper());

            executor.execute(() -> {
                String data = null;

                try {

                    if(URLUtil.isNetworkUrl(urlOrFileName)){
                        data = importFromWeb(urlOrFileName);
                    } else {
                        //Assume file is in assets folder.
                        data = importFromAssets(context, urlOrFileName);
                    }

                    final String result = data;

                    handler.post(() -> {
                        //Ensure the resulting data string is not null or empty.
                        if (result != null && !result.isEmpty()) {
                            callback.notify(result);
                        } else {
                            error.notify("No data imported.");
                        }
                    });
                } catch(Exception e) {
                    if(error != null){
                        error.notify(e.getMessage());
                    }
                }
            });
        }
    }

    /**
     * Imports data from an assets file as a string.
     * @param context The context of the app.
     * @param fileName The asset file name.
     * @return
     * @throws IOException
     */
    private static String importFromAssets(Context context, String fileName) throws IOException {
        InputStream stream = null;

        try {
            stream = context.getAssets().open(fileName);

            if(stream != null) {
                return readStreamAsString(stream);
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            // Close Stream and disconnect HTTPS connection.
            if (stream != null) {
                stream.close();
            }
        }

        return null;
    }

    /**
     * Imports data from the web as a string.
     * @param url URL to the data.
     * @return
     * @throws IOException
     */
    private static String importFromWeb(String url) throws IOException {
        InputStream stream = null;
        HttpsURLConnection connection = null;
        String result = null;

        try {
            connection = (HttpsURLConnection) new URL(url).openConnection();

            //For this use case, set HTTP method to GET.
            connection.setRequestMethod("GET");

            //Open communications link (network traffic occurs here).
            connection.connect();

            int responseCode = connection.getResponseCode();
            if (responseCode != HttpsURLConnection.HTTP_OK) {
                throw new IOException("HTTP error code: " + responseCode);
            }

            //Retrieve the response body as an InputStream.
            stream = connection.getInputStream();

            if (stream != null) {
                return readStreamAsString(stream);
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            // Close Stream and disconnect HTTPS connection.
            if (stream != null) {
                stream.close();
            }
            if (connection != null) {
                connection.disconnect();
            }
        }

        return result;
    }

    /**
     * Reads an input stream as a string.
     * @param stream Stream to convert.
     * @return
     * @throws IOException
     */
    private static String readStreamAsString(InputStream stream) throws IOException {
        //Convert the contents of an InputStream to a String.
        BufferedReader in = new BufferedReader(new InputStreamReader(stream, "UTF-8"));

        String inputLine;
        StringBuffer response = new StringBuffer();

        while ((inputLine = in.readLine()) != null) {
            response.append(inputLine);
        }

        in.close();

        return response.toString();
    }
}
```

下面的代码演示如何使用此实用工具将 GeoJSON 数据导入为字符串，并通过回调将其返回到 UI 线程。 在回调中，可以将字符串数据序列化为 GeoJSON 功能集合，并将其添加到数据源。 还可以更新 "地图" 照相机，将数据集中在一起。

```java
//Create a data source and add it to the map.
DataSource dataSource = new DataSource();
map.sources.add(dataSource);

//Import the geojson data and add it to the data source.
Utils.importData("URL_or_FilePath_to_GeoJSON_data",
    this,
    (String result) -> {
        //Parse the data as a GeoJSON Feature Collection.
        FeatureCollection fc = FeatureCollection.fromJson(result);

        //Add the feature collection to the data source.
        dataSource.add(fc);

        //Optionally, update the maps camera to focus in on the data.

        //Calculate the bounding box of all the data in the Feature Collection.
        BoundingBox bbox = MapMath.fromData(fc);

        //Update the maps camera so it is focused on the data.
        map.setCamera(
            bounds(bbox),
            padding(20));
    });
```

## <a name="vector-tile-source"></a>矢量磁贴源

矢量图块源介绍如何访问矢量图块层。 使用 `VectorTileSource` 类实例化矢量图块源。 矢量图块层类似于图块层，但它们不同。 图块层是光栅图像。 矢量图块层是压缩文件，采用 **PBF** 格式。 此压缩文件包含向量映射数据以及一个或多个层。 基于每个层的样式，可以在客户端上呈现并设计文件样式。 矢量磁贴中的数据包含点、线条和多边形格式的地理功能。 使用矢量图块层（而不是光栅图块层）有多种优点：

- 矢量磁贴的文件大小通常比等效的光栅磁贴小得多。 因此，使用的带宽更少。 这意味着延迟较低、更快的地图和更好的用户体验。
- 由于矢量磁贴是在客户端上呈现的，因此它们会适应其所显示的设备的分辨率。 因此，呈现的地图的定义更清晰，并显示 crystal 清晰标签。
- 更改向量图中的数据样式不需要再次下载数据，因为新样式可应用于客户端。 与此相反，更改光栅图块层的样式通常需要从服务器加载磁贴，然后应用新样式。
- 由于数据是以矢量形式传递的，因此，进行数据准备需要的服务器端处理就越少。 因此，可以更快地提供较新的数据。

Azure Maps 遵循 [Mapbox Vector 磁贴规范](https://github.com/mapbox/vector-tile-spec)，即开放标准。 Azure Maps 在平台中提供以下矢量图块服务：

- 路标磁贴[文档](/rest/api/maps/renderv2/getmaptilepreview)  |  [数据格式详细信息](https://developer.tomtom.com/maps-api/maps-api-documentation-vector/tile)
- 流量事件[文档](/rest/api/maps/traffic/gettrafficincidenttile)  |  [数据格式详细信息](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-incidents/vector-incident-tiles)
- 流量流[文档](/rest/api/maps/traffic/gettrafficflowtile)  |  [数据格式详细信息](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-flow/vector-flow-tiles)
- Azure Maps Creator 还允许通过[获取磁贴呈现 V2](/rest/api/maps/renderv2/getmaptilepreview)创建和访问自定义向量磁贴

> [!TIP]
> 使用 web SDK Azure Maps 呈现服务中的矢量或光栅图像磁贴时，可以 `atlas.microsoft.com` 将替换为占位符 `azmapsdomain.invalid` 。 此占位符将替换为映射所使用的相同域，并将自动附加相同的身份验证详细信息。 当使用 Azure Active Directory authentication 时，这大大简化了使用渲染服务进行的身份验证。

若要在地图上显示 vector 磁贴源中的数据，请将源连接到数据呈现层之一。 使用矢量源的所有层都必须 `sourceLayer` 在选项中指定一个值。 下面的代码将 Azure Maps 流量向量磁贴服务作为矢量图块源加载，然后使用线条层在地图上显示它。 此向量磁贴源在源层中具有一组称为 "流量流" 的数据。 此数据集中的行数据具有一个名为的属性，该属性 `traffic_level` 在此代码中用于选择颜色并缩放线条大小。

```java
//Formatted URL to the traffic flow vector tiles, with the maps subscription key appended to it.
String trafficFlowUrl = "https://azmapsdomain.invalid/traffic/flow/tile/pbf?api-version=1.0&style=relative&zoom={z}&x={x}&y={y}";

//Create a vector tile source and add it to the map.
VectorTileSource source = new VectorTileSource(
    tiles(new String[] { trafficFlowUrl }),
    maxSourceZoom(22)
);
map.sources.add(source);

//Create a layer for traffic flow lines.
LineLayer layer = new LineLayer(source,
    //The name of the data layer within the data source to pass into this rendering layer.
    sourceLayer("Traffic flow"),

    //Color the roads based on the traffic_level property.
    strokeColor(
        interpolate(
            linear(),
            get("traffic_level"),
            stop(0, color(Color.RED)),
            stop(0.33, color(Color.YELLOW)),
            stop(0.66, color(Color.GREEN))
        )
    ),

    //Scale the width of roads based on the traffic_level property.
    strokeWidth(
        interpolate(
            linear(),
            get("traffic_level"),
            stop(0, 6),
            stop(1,1)
        )
    )
);

//Add the traffic flow layer below the labels to make the map clearer.
map.layers.add(layer, "labels");
```

![具有显示流量流级别的彩色编码路段的地图](media/create-data-source-android-sdk/android-vector-tile-source-line-layer.png)

## <a name="connecting-a-data-source-to-a-layer"></a>将数据源连接到层

使用呈现层在地图上呈现数据。 一个或多个呈现层可以引用单个数据源。 以下呈现层需要数据源：

- [气泡图层](map-add-bubble-layer-android.md) -将点数据呈现为地图上的缩放圆圈。
- [符号层](how-to-add-symbol-to-android-map.md) -将点数据呈现为图标或文本。
- [热度地图层](map-add-heat-map-layer-android.md) -将点数据呈现为密度热度地图。
- [线条层](android-map-add-line-layer.md) -呈现线条并呈现多边形的轮廓。
- [多边形层](how-to-add-shapes-to-android-map.md) -使用纯色或图像图案填充多边形的面积。

下面的代码演示如何创建数据源，将其添加到地图中，然后将其连接到气泡图层。 然后，将 GeoJSON 点数据从远程位置导入到数据源中。

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a layer that defines how to render points in the data source and add it to the map.
BubbleLayer layer = new BubbleLayer(source);
map.layers.add(layer);

//Import the geojson data and add it to the data source.
Utils.importData("URL_or_FilePath_to_GeoJSON_data",
    this,
    (String result) -> {
        //Parse the data as a GeoJSON Feature Collection.
        FeatureCollection fc = FeatureCollection.fromJson(result);

        //Add the feature collection to the data source.
        dataSource.add(fc);

        //Optionally, update the maps camera to focus in on the data.

        //Calculate the bounding box of all the data in the Feature Collection.
        BoundingBox bbox = MapMath.fromData(fc);

        //Update the maps camera so it is focused on the data.
        map.setCamera(
            bounds(bbox),
            padding(20));
    });
```

还有其他呈现层不连接到这些数据源，但它们直接加载要呈现的数据。

- 图[块层](how-to-add-tile-layer-android-map.md)-superimposes 在地图的顶部。

## <a name="one-data-source-with-multiple-layers"></a>一个具有多个层的数据源

多个层可以连接到单个数据源。 在许多不同方案中，此选项很有用。 例如，假设用户绘制多边形的情况。 当用户向地图添加点时，应呈现并填充多边形区域。 如果将样式的线条添加到边框，则可以在用户绘制时更轻松地看到多边形的边缘。 若要方便地编辑多边形中的单个位置，可以在每个位置的上方添加一个句柄，如 pin 或标记。

![显示单个数据源呈现数据的多个层的地图](media/create-data-source-android-sdk/multiple-layers-one-datasource.png)

在大多数映射平台中，需要一个多边形对象、一个线条对象和一个用于多边形中每个位置的 pin。 修改多边形时，您需要手动更新线条和 pin，这很快就会变得复杂。

对于 Azure Maps，只需在数据源中使用一个多边形，如以下代码所示。

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a polygon and add it to the data source.
source.add(Polygon.fromLngLats(/* List of points */));

//Create a polygon layer to render the filled in area of the polygon.
PolygonLayer polygonLayer = new PolygonLayer(source,
    fillColor("rgba(255,165,0,0.2)")
);

//Create a line layer for greater control of rendering the outline of the polygon.
LineLayer lineLayer = new LineLayer(source,
    strokeColor("orange"),
    strokeWidth(2f)
);

//Create a bubble layer to render the vertices of the polygon as scaled circles.
BubbleLayer bubbleLayer = new BubbleLayer(source,
    bubbleColor("orange"),
    bubbleRadius(5f),
    bubbleStrokeColor("white"),
    bubbleStrokeWidth(2f)
);

//Add all layers to the map.
map.layers.add(new Layer[] { polygonLayer, lineLayer, bubbleLayer });
```

> [!TIP]
> 使用方法向地图添加层时 `map.layers.add` ，可以将现有层的 ID 或实例作为第二个参数传入。 这会告知映射将插入要添加到现有层下方的新层。 在除了中，若要传入层 ID，此方法还支持以下值。
>
> - `"labels"` -将新层插入到地图标签层之下。
> - `"transit"` -将新层插入到地图道路和中转层下方。

## <a name="next-steps"></a>后续步骤

有关可向地图添加的更多代码示例，请参阅以下文章：

> [!div class="nextstepaction"]
> [使用数据驱动样式表达式](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [添加符号层](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [添加气泡层](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [添加线条层](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [添加多边形层](how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [添加热度地图](map-add-heat-map-layer-android.md)

> [!div class="nextstepaction"]
> [Web SDK 代码示例](/samples/browse/?products=azure-maps)