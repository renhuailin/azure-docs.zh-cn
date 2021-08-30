---
title: 为 Android 地图创建数据源 | Microsoft Azure Maps
description: 了解如何为地图创建数据源。 了解 Azure Maps Android SDK 使用的数据源：GeoJSON 源和矢量图块。
author: rbrundritt
ms.author: richbrun
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 9ea96613425ec3802080277da9ac674af4e87c52
ms.sourcegitcommit: d9a2b122a6fb7c406e19e2af30a47643122c04da
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2021
ms.locfileid: "114669082"
---
# <a name="create-a-data-source-android-sdk"></a>创建数据源 (Android SDK)

Azure Maps Android SDK 将数据存储在数据源中。 使用数据源可优化用于查询和渲染的数据操作。 目前数据源有两种类型：

- **GeoJSON 源**：在本地管理 GeoJSON 格式的原始位置数据。 适合中小型数据集（数十万个形状以上）。
- **矢量图块源**：基于地图图块系统，为当前的地图视图加载矢量图块格式的数据。 适合大型乃至超大型数据集（数百万或数十亿个形状）。

## <a name="geojson-data-source"></a>GeoJSON 数据源

Azure Maps 使用 GeoJSON 作为其主数据模型之一。 GeoJSON 是一种开放式地理空间标准方法，用于以 JSON 格式表示地理空间数据。 通过 Azure Maps Android SDK 中可用的 GeoJSON 类，可轻松创建和序列化 GeoJSON 数据。 在 `DataSource` 类中加载并存储 GeoJSON 数据，并使用层呈现它。 以下代码演示了如何在 Azure Maps 中创建 GeoJSON 对象。

::: zone pivot="programming-language-java-android"

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

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
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
val feature = Feature.fromGeometry(Point.fromLngLat(-100, 45))

//Add a property to the feature.
feature.addStringProperty("custom-property", "value")

//Add the feature to the data source.
source.add(feature)
```

> [!TIP]
> 可以使用以下三种方法之一将 GeoJSON 数据添加到 `DataSource` 实例：`add`、`importDataFromUrl` 和 `setShapes`。 `setShapes` 方法提供了一种有效的方法来覆盖数据源中的所有数据。 如果依次调用 `clear` 和 `add` 方法来替换数据源中的所有数据，则会对该地图进行两次呈现调用。 `setShape` 方法只需对地图进行一次呈现调用，即可在数据源中清除并添加数据。

::: zone-end

另外，还可以先将属性加载到 JsonObject 中，然后在创建特征时将其传入特征，如下所示。

::: zone pivot="programming-language-java-android"

```java
//Create a JsonObject to store properties for the feature.
JsonObject properties = new JsonObject();
properties.addProperty("custom-property", "value");

Feature feature = Feature.fromGeometry(Point.fromLngLat(-100, 45), properties);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a JsonObject to store properties for the feature.
val properties = JsonObject()
properties.addProperty("custom-property", "value")

val feature = Feature.fromGeometry(Point.fromLngLat(-100, 45), properties)
```

::: zone-end

创建 GeoJSON 特征后，可以通过地图的 `sources` 属性将数据源添加到地图中。 下面的代码演示如何创建 `DataSource`，将其添加到地图，以及如何将特征添加到数据源。

```javascript
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Add GeoJSON feature to the data source.
source.add(feature);
```

下面的代码演示了几种创建 GeoJSON 特征、FeatureCollection 和几何图形的方式。

::: zone pivot="programming-language-java-android"

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

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//GeoJSON Point Geometry
val point = Point.fromLngLat(LONGITUDE, LATITUDE)

//GeoJSON Point Geometry
val linestring = LineString.fromLngLats(PointList)

//GeoJSON Polygon Geometry
val polygon = Polygon.fromLngLats(listOfPointList)

val polygonFromOuterInner = Polygon.fromOuterInner(outerLineStringObject, innerLineStringObject)

//GeoJSON MultiPoint Geometry
val multiPoint = MultiPoint.fromLngLats(PointList)

//GeoJSON MultiLineString Geometry
val multiLineStringFromLngLat = MultiLineString.fromLngLats(listOfPointList)

val multiLineString = MultiLineString.fromLineString(singleLineString)

//GeoJSON MultiPolygon Geometry
val multiPolygon = MultiPolygon.fromLngLats(listOflistOfPointList)

val multiPolygonFromPolygon = MultiPolygon.fromPolygon(polygon)

val multiPolygonFromPolygons = MultiPolygon.fromPolygons(PolygonList)

//GeoJSON Feature
val pointFeature = Feature.fromGeometry(Point.fromLngLat(LONGITUDE, LATITUDE))

//GeoJSON FeatureCollection 
val featureCollectionFromSingleFeature = FeatureCollection.fromFeature(pointFeature)

val featureCollection = FeatureCollection.fromFeatures(listOfFeatures)
```

::: zone-end

### <a name="serialize-and-deserialize-geojson"></a>对 GeoJSON 进行序列化和反序列化

特征集合、特征和几何图形类都具有 `fromJson()` 和 `toJson()` 静态方法，有助于进行序列化。 通过 `fromJson()` 方法传递的格式化有效 JSON 字符串将创建几何对象。 此 `fromJson()` 方法还意味着可以使用 Gson 或其他序列化/反序列化策略。 下面的代码演示如何获取字符串化 GeoJSON 特征并将其反序列化为特征类，然后将其序列化回 GeoJSON 字符串。

::: zone pivot="programming-language-java-android"

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

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Take a stringified GeoJSON object.
val GeoJSON_STRING = ("{"
        + "      \"type\": \"Feature\","
        + "      \"geometry\": {"
        + "            \"type\": \"Point\""
        + "            \"coordinates\": [-100, 45]"
        + "      },"
        + "      \"properties\": {"
        + "            \"custom-property\": \"value\""
        + "      },"
        + "}")

//Deserialize the JSON string into a feature.
val feature = Feature.fromJson(GeoJSON_STRING)

//Serialize a feature collection to a string.
val featureString = feature.toJson()
```

::: zone-end

### <a name="import-geojson-data-from-web-or-assets-folder"></a>从 Web 或资产文件夹导入 GeoJSON 数据

大多数 GeoJSON 文件都包含一个 FeatureCollection。 将 GeoJSON 文件作为字符串读取，并使用 `FeatureCollection.fromJson` 方法对其进行反序列化。

`DataSource` 类具有一个名为 `importDataFromUrl` 的内置方法，该方法可使用指向 Web 上或资产文件夹中的文件的 URL 加载到 GeoJSON 文件中。 在将数据源添加到地图之前，必须调用此方法。

zone_pivot_groups：azure-maps-android

::: zone pivot="programming-language-java-android"

``` java
//Create a data source and add it to the map.
DataSource source = new DataSource();

//Import the geojson data and add it to the data source.
source.importDataFromUrl("URL_or_FilePath_to_GeoJSON_data");

//Examples:
//source.importDataFromUrl("asset://sample_file.json");
//source.importDataFromUrl("https://example.com/sample_file.json");

//Add data source to the map.
map.sources.add(source);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
var source = new DataSource()

//Import the geojson data and add it to the data source.
source.importDataFromUrl("URL_or_FilePath_to_GeoJSON_data")

//Examples:
//source.importDataFromUrl("asset://sample_file.json")
//source.importDataFromUrl("https://example.com/sample_file.json")

//Add data source to the map.
map.sources.add(source)
```

::: zone-end

`importDataFromUrl` 方法提供了一种简单的方法来将 GeoJSON 源加载到数据源，但对加载数据的方式以及加载后发生的情况提供有限的控制。 下面的代码是一个可重用的类，用于将 Web 或资产文件夹中的数据导入，并通过回调函数将其返回到 UI 线程。 在回调中，你可添加额外的加载后逻辑来处理数据，将其添加到地图，计算其边界框和更新地图照相机。

::: zone pivot="programming-language-java-android"

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

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
import android.content.Context
import android.os.Handler
import android.os.Looper
import android.webkit.URLUtil
import java.net.URL
import java.util.concurrent.ExecutorService
import java.util.concurrent.Executors

class Utils {
    companion object {

        /**
            * Imports data from a web url or asset file name and returns it to a callback.
            * @param urlOrFileName A web url or asset file name that points to data to load.
            * @param context The context of the app.
            * @param callback The callback function to return the data to.
            */
        fun importData(urlOrFileName: String?, context: Context, callback: (String?) -> Unit) {
            importData(urlOrFileName, context, callback, null)
        }

        /**
            * Imports data from a web url or asset file name and returns it to a callback.
            * @param urlOrFileName A web url or asset file name that points to data to load.
            * @param context The context of the app.
            * @param callback The callback function to return the data to.
            * @param error A callback function to return errors to.
            */
        public fun importData(urlOrFileName: String?, context: Context, callback: (String?) -> Unit, error: ((String?) -> Unit)?) {
            if (urlOrFileName != null && callback != null) {
                val executor: ExecutorService = Executors.newSingleThreadExecutor()
                val handler = Handler(Looper.getMainLooper())
                executor.execute {
                    var data: String? = null
                    
                    try {
                        data = if (URLUtil.isNetworkUrl(urlOrFileName)) {
                            URL(urlOrFileName).readText()
                        } else { //Assume file is in assets folder.
                            context.assets.open(urlOrFileName).bufferedReader().use{
                                it.readText()
                            }
                        }

                        handler.post {
                            //Ensure the resulting data string is not null or empty.
                            if (data != null && !data.isEmpty()) {
                                callback(data)
                            } else {
                                error!!("No data imported.")
                            }
                        }
                    } catch (e: Exception) {
                        error!!(e.message)
                    }
                }
            }
        }
    }
}
```

::: zone-end

下面的代码演示如何使用此实用工具将 GeoJSON 数据导入为字符串，并通过回调将其返回到 UI 线程。 在回调中，可以将字符串数据序列化为 GeoJSON 特征集合，并将其添加到数据源。 还可选择性更新地图照相机，将焦点对准数据。

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Import the geojson data and add it to the data source.
Utils.importData("URL_or_FilePath_to_GeoJSON_data",
    this,
    (String result) -> {
        //Parse the data as a GeoJSON Feature Collection.
        FeatureCollection fc = FeatureCollection.fromJson(result);

        //Add the feature collection to the data source.
        source.add(fc);

        //Optionally, update the maps camera to focus in on the data.

        //Calculate the bounding box of all the data in the Feature Collection.
        BoundingBox bbox = MapMath.fromData(fc);

        //Update the maps camera so it is focused on the data.
        map.setCamera(
            bounds(bbox),
            padding(20));
    });
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Import the GeoJSON data and add it to the data source.
Utils.importData("SamplePoiDataSet.json", this) { 
    result: String? ->
        //Parse the data as a GeoJSON Feature Collection.
            val fc = FeatureCollection.fromJson(result!!)

        //Add the feature collection to the data source.
        source.add(fc)

        //Optionally, update the maps camera to focus in on the data.

        //Calculate the bounding box of all the data in the Feature Collection.
        val bbox = MapMath.fromData(fc);

        //Update the maps camera so it is focused on the data.
        map.setCamera(
            bounds(bbox),

            //Padding added to account for pixel size of rendered points.
            padding(20)
        )
    }
```

::: zone-end

### <a name="update-a-feature"></a>更新特征

`DataSource` 类可以轻松添加和删除特征。 更新特征的几何图形或属性需要在数据源中替换该特征。 可使用两种方法来更新特征：

1. 使用所需的更新创建新特征，并使用 `setShapes` 方法替换数据源中的所有特征。 当你希望更新数据源中的所有特征时，此方法非常有效。

::: zone pivot="programming-language-java-android"

``` java
DataSource source;

private void onReady(AzureMap map) {
    //Create a data source and add it to the map.
    source = new DataSource();
    map.sources.add(source);

    //Create a feature and add it to the data source.
    Feature myFeature = Feature.fromGeometry(Point.fromLngLat(0,0));
    myFeature.addStringProperty("Name", "Original value");

    source.add(myFeature);
}

private void updateFeature(){
    //Create a new replacement feature with an updated geometry and property value.
    Feature myNewFeature = Feature.fromGeometry(Point.fromLngLat(-10, 10));
    myNewFeature.addStringProperty("Name", "New value");

    //Replace all features to the data source with the new one.
    source.setShapes(myNewFeature);
}
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
var source: DataSource? = null

private fun onReady(map: AzureMap) {
    //Create a data source and add it to the map.
    source = DataSource()
    map.sources.add(source)

    //Create a feature and add it to the data source.
    val myFeature = Feature.fromGeometry(Point.fromLngLat(0.0, 0.0))
    myFeature.addStringProperty("Name", "Original value")
    source!!.add(myFeature)
}

private fun updateFeature() {
    //Create a new replacement feature with an updated geometry and property value.
    val myNewFeature = Feature.fromGeometry(Point.fromLngLat(-10.0, 10.0))
    myNewFeature.addStringProperty("Name", "New value")

    //Replace all features to the data source with the new one.
    source!!.setShapes(myNewFeature)
}
```

::: zone-end

2. 跟踪变量中的功能实例，并将其传递到数据源 `remove` 方法中以删除它。 使用所需更新创建新特征，更新变量引用并使用 `add` 方法将其添加到数据源。

::: zone pivot="programming-language-java-android"

``` java
DataSource source;
Feature myFeature;

private void onReady(AzureMap map) {
    //Create a data source and add it to the map.
    source = new DataSource();
    map.sources.add(source);

    //Create a feature and add it to the data source.
    myFeature = Feature.fromGeometry(Point.fromLngLat(0,0));
    myFeature.addStringProperty("Name", "Original value");

    source.add(myFeature);
}

private void updateFeature(){
    //Remove the feature instance from the data source.
    source.remove(myFeature);

    //Get properties from original feature.
    JsonObject props = myFeature.properties();

    //Update a property.
    props.addProperty("Name", "New value");

    //Create a new replacement feature with an updated geometry.
    myFeature = Feature.fromGeometry(Point.fromLngLat(-10, 10), props);

    //Re-add the feature to the data source.
    source.add(myFeature);
}
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
var source: DataSource? = null
var myFeature: Feature? = null

private fun onReady(map: AzureMap) {
    //Create a data source and add it to the map.
    source = DataSource()
    map.sources.add(source)

    //Create a feature and add it to the data source.
    myFeature = Feature.fromGeometry(Point.fromLngLat(0.0, 0.0))
    myFeature.addStringProperty("Name", "Original value")
    source!!.add(myFeature)
}

private fun updateFeature() {
    //Remove the feature instance from the data source.
    source!!.remove(myFeature)

    //Get properties from original feature.
    val props = myFeature!!.properties()

    //Update a property.
    props!!.addProperty("Name", "New value")

    //Create a new replacement feature with an updated geometry.
    myFeature = Feature.fromGeometry(Point.fromLngLat(-10.0, 10.0), props)

    //Re-add the feature to the data source.
    source!!.add(myFeature)
}
```

::: zone-end

> [!TIP]
> 如果有一些数据需要定期更新，而其他数据很少更改，最好将这些数据拆分到单独的数据源实例中。 当数据源中发生更新时，该更新会强制地图重画数据源中的所有特征。 拆分此数据后，当一个数据源中发生更新时，只有定期更新的特征才会重画，另一个数据源中的特征不需要重画。 这有助于提高性能。

## <a name="vector-tile-source"></a>矢量图块源

矢量图块源介绍如何访问矢量图块层。 使用 `VectorTileSource` 类实例化矢量图块源。 矢量图块层与图块层类似，但不同。 图块层是光栅图像。 矢量图块层是压缩文件，为 PBF 格式。 此压缩文件包含矢量地图数据以及一个或多个层。 根据每层的样式，可以在客户端渲染文件并设计文件样式。 矢量图块中的数据包含点、线和多边形形式的地理功能。 相比光栅图块层，使用矢量图块层有多种优势：

- 矢量图块的文件大小通常比等效的光栅图块小得多。 因此，使用的带宽更少。 这意味着延迟较低、地图较快和用户体验更好。
- 由于矢量图块在客户端渲染，所以它们会适应要显示它们的设备的分辨率。 因此，渲染的地图看起来更清晰，可清楚地看到标签。
- 更改矢量地图中的数据样式不需要重新下载数据，因为新样式可应用于客户端。 相反，更改光栅图块层的样式通常需要从服务器加载图块，然后再应用新样式。
- 由于数据以矢量形式传递，所以准备数据所需的服务器端处理更少。 因此，可以更快地提供较新的数据。

Azure Maps 遵循开放式标准 - [Mapbox 矢量图块规范](https://github.com/mapbox/vector-tile-spec)。 Azure Maps 在平台中提供以下矢量图块服务：

- 道路图块[文档](/rest/api/maps/render-v2/get-map-tile) | [数据格式详细信息](https://developer.tomtom.com/maps-api/maps-api-documentation-vector/tile)
- 交通事故[文档](/rest/api/maps/traffic/gettrafficincidenttile) | [数据格式详细信息](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-incidents/vector-incident-tiles)
- 交通流[文档](/rest/api/maps/traffic/gettrafficflowtile) | [数据格式详细信息](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-flow/vector-flow-tiles)
- 使用 Azure Maps Creator，还可以通过[渲染器 V2 - 获取地图图块 API](/rest/api/maps/render-v2/get-map-tile) 创建和访问自定义矢量图块

> [!TIP]
> 通过 Web SDK 使用 Azure Maps 渲染服务中的矢量或光栅图块时，可以使用占位符 `azmapsdomain.invalid` 替代 `atlas.microsoft.com`。 此占位符将替换为地图使用的相同域，还会自动附加相同的身份验证详细信息。 这样可大大简化使用 Azure Active Directory 身份验证时对渲染服务的身份验证。

要在地图上显示矢量图块源中的数据，请将该源连接到数据渲染层之一。 使用矢量源的所有层都必须在选项中指定一个 `sourceLayer` 值。 以下代码可将 Azure Maps 交通流矢量图块服务加载为矢量图块源，然后使用线条层在地图上进行显示。 该矢量图块源在源层中有一个数据集，称为“交通流”。 此数据集中的线条数据包含一个 `traffic_level` 属性，在此代码中用于选择颜色和缩放线条大小。

::: zone pivot="programming-language-java-android"

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

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Formatted URL to the traffic flow vector tiles, with the maps subscription key appended to it.
val trafficFlowUrl = "https://azmapsdomain.invalid/traffic/flow/tile/pbf?api-version=1.0&style=relative&zoom={z}&x={x}&y={y}"

//Create a vector tile source and add it to the map.
val source = VectorTileSource(
    tiles(arrayOf(trafficFlowUrl)),
    maxSourceZoom(22)
)
map.sources.add(source)

//Create a layer for traffic flow lines.
val layer = LineLayer(
    source,  //The name of the data layer within the data source to pass into this rendering layer.
    sourceLayer("Traffic flow"),  //Color the roads based on the traffic_level property.
    strokeColor(
        interpolate(
            linear(),
            get("traffic_level"),
            stop(0, color(Color.RED)),
            stop(0.33, color(Color.YELLOW)),
            stop(0.66, color(Color.GREEN))
        )
    ),  //Scale the width of roads based on the traffic_level property.
    strokeWidth(
        interpolate(
            linear(),
            get("traffic_level"),
            stop(0, 6),
            stop(1, 1)
        )
    )
)

//Add the traffic flow layer below the labels to make the map clearer.
map.layers.add(layer, "labels")
```

::: zone-end

![用色彩编码的道路线显示交通流情况的地图](media/create-data-source-android-sdk/android-vector-tile-source-line-layer.png)

## <a name="connecting-a-data-source-to-a-layer"></a>将数据源连接到层

使用渲染层在地图上渲染数据。 一个数据源可被一个或多个渲染层引用。 以下渲染层需要数据源：

- [气泡层](map-add-bubble-layer-android.md) - 将点数据渲染为地图上的缩放圆圈。
- [符号层](how-to-add-symbol-to-android-map.md) - 将点数据渲染为图标或文本。
- [热度地图层](map-add-heat-map-layer-android.md) - 将点数据渲染为密度热度地图。
- [线条层](android-map-add-line-layer.md) - 渲染线条和/或渲染多边形边框。
- [多边形层](how-to-add-shapes-to-android-map.md) - 使用纯色或图像图案填充多边形区域。

以下代码演示了如何创建数据源，将其添加到地图中，然后将其连接到气泡层。 然后，从远程位置向数据源中导入 GeoJSON 点数据。

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();

//Import the geojson data and add it to the data source.
source.importDataFromUrl("URL_or_FilePath_to_GeoJSON_data");

//Add data source to the map.
map.sources.add(source);

//Create a layer that defines how to render points in the data source and add it to the map.
BubbleLayer layer = new BubbleLayer(source);
map.layers.add(layer);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()

//Import the geojson data and add it to the data source.
source.importDataFromUrl("URL_or_FilePath_to_GeoJSON_data")

//Add data source to the map.
map.sources.add(source)
```

::: zone-end

还有一些渲染层不会连接到这些数据源，而是直接加载要渲染的数据。

- [图块层](how-to-add-tile-layer-android-map.md) - 将光栅图块层叠加到地图顶部。

## <a name="one-data-source-with-multiple-layers"></a>包含多个层的单个数据源

可将多个层连接到单个数据源。 在许多不同方案中，这种选择都很有用。 例如，假设用户绘制多边形的场景。 当用户向地图中添加点时，我们应渲染并填充多边形区域。 如果对多边形边框添加样式化的线条，用户在绘制时则可以更清晰地看到多边形的边缘。 若要方便地编辑多边形中的单个位置，可以在每个位置上添加图柄，如单边锁定或标记。

![地图，显示了从单个数据源渲染数据的多个层](media/create-data-source-android-sdk/multiple-layers-one-datasource.png)

大多数地图平台中都需要一个多边形对象、一个线条对象以及用于多边形中各个位置的单边锁定。 由于修改了多边形，因此你需要手动更新线条和单边锁定，这可能很快就变得复杂起来。

使用 Azure Maps，只需在数据源中创建一个多边形，如以下代码所示。

::: zone pivot="programming-language-java-android"

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

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create a polygon and add it to the data source.
source.add(Polygon.fromLngLats())

//Create a polygon layer to render the filled in area of the polygon.
val polygonLayer = PolygonLayer(
    source,
    fillColor("rgba(255,165,0,0.2)")
)

//Create a line layer for greater control of rendering the outline of the polygon.
val lineLayer = LineLayer(
    source,
    strokeColor("orange"),
    strokeWidth(2f)
)

//Create a bubble layer to render the vertices of the polygon as scaled circles.
val bubbleLayer = BubbleLayer(
    source,
    bubbleColor("orange"),
    bubbleRadius(5f),
    bubbleStrokeColor("white"),
    bubbleStrokeWidth(2f)
)

//Add all layers to the map.
map.layers.add(arrayOf<Layer>(polygonLayer, lineLayer, bubbleLayer))
```

::: zone-end

> [!TIP]
> 使用 `map.layers.add` 方法向地图中添加层时，可以将现有层的 ID 或实例作为第二个参数传递。 这会告知地图将要添加的新层插入到现有层下方。 除传递层 ID 之外，这种方法还支持以下值。
>
> - `"labels"` - 将新层插入到地图标签层之下。
> - `"transit"` - 将新层插入到地图道路和中转层之下。

## <a name="next-steps"></a>后续步骤

有关可向地图添加的更多代码示例，请参阅以下文章：

> [!div class="nextstepaction"]
> [使用数据驱动样式表达式](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [聚类点数据](clustering-point-data-android-sdk.md)

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
