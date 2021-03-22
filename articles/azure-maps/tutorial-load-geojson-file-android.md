---
title: 教程：将 GeoJSON 数据加载到 Azure Maps Android SDK 中 | Microsoft Azure Maps
description: 关于如何将 GeoJSON 数据文件加载到 Azure Maps Android 地图 SDK 的教程。
author: rbrundritt
ms.author: richbrun
ms.date: 12/10/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 8300a7c120ce816c8068a88fa69f4f978fa664ca
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102034500"
---
# <a name="tutorial-load-geojson-data-into-azure-maps-android-sdk"></a>教程：将 GeoJSON 数据加载到 Azure Maps Android SDK 中

本教程将指导你完成将位置数据的 GeoJSON 文件导入 Azure Maps Android SDK 的过程。 在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 将 Azure Maps 添加到 Android 应用程序。
> * 从本地文件或 Web 创建数据源并将其加载到 GeoJSON 文件中。
> * 在地图上显示数据。

## <a name="prerequisites"></a>先决条件

1. 完成[快速入门：创建 Android 应用](quick-android-map.md)。 本教程将扩展该快速入门中使用的代码。
2. 下载[示例兴趣点](https://raw.githubusercontent.com/Azure-Samples/AzureMapsCodeSamples/master/AzureMapsCodeSamples/Common/data/geojson/SamplePoiDataSet.json) GeoJSON 文件。

### <a name="import-geojson-data-from-web-or-assets-folder"></a>从 Web 或资产文件夹导入 GeoJSON 数据

大多数 GeoJSON 文件将所有数据包装在 `FeatureCollection` 中。 考虑到这一点，如果将 GeoJSON 文件作为字符串加载到应用程序中，则可以将其传递到功能集合的静态 `fromJson` 方法中，该方法会将字符串反序列化为可添加到地图中的 GeoJSON `FeatureCollection` 对象。

以下步骤显示如何将 GeoJSON 文件导入应用程序并将其反序列化为 GeoJSON `FeatureCollection` 对象。

1. 完成[快速入门：创建 Android 应用](quick-android-map.md)，因为以下步骤基于此应用程序构建。
2. 在 Android Studio 的项目面板中，右键单击“app”文件夹，然后转到 `New > Folder > Assets Folder`。
3. 将[示例兴趣点](https://raw.githubusercontent.com/Azure-Samples/AzureMapsCodeSamples/master/AzureMapsCodeSamples/Common/data/geojson/SamplePoiDataSet.json) GeoJSON 文件拖放到 assets 文件夹中。

::: zone pivot="programming-language-java-android"

4. 创建名为 Utils.java 的新文件，并将以下代码添加到该文件中。 此代码提供了名为 `importData` 的静态方法，该方法使用 URL 作为字符串从应用程序的 `assets` 文件夹或从 Web 异步导入文件，然后使用简单的回调方法将其返回给 UI 线程。

    ```java
    //Modify the package name as needed to align with your application.
    package com.example.myapplication;
    
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

5. 进入 MainActivity.java 文件，并在 `mapControl.onReady` 事件的回调中添加以下代码，该代码位于 `onCreate` 方法内。 此代码使用导入实用工具以字符串的形式读取 SamplePoiDataSet.json 文件，然后使用 `FeatureCollection` 类的静态 `fromJson` 方法将其反序列化为功能集合。 此代码还计算功能集合中所有数据的边界框区域，并使用此边界框区域来设置地图的摄像头以将焦点对准数据。

    ```java
    //Create a data source and add it to the map.
    DataSource source = new DataSource();
    map.sources.add(source);
    
    //Import the GeoJSON data and add it to the data source.
    Utils.importData("SamplePoiDataSet.json",
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

                //Padding added to account for pixel size of rendered points.
                padding(20)
            );
        });
    ```

6. 使用此代码将 GeoJSON 数据加载到数据源，我们现在需要指定该数据在地图上的显示方式。 点数据有多个不同的渲染层；[气泡层](map-add-bubble-layer-android.md)、[符号层](how-to-add-symbol-to-android-map.md)和[热度地图层](map-add-heat-map-layer-android.md)是最常用的层。 在用于导入数据的代码后面，添加以下代码，以在 `mapControl.onReady` 事件的回调的气泡层中呈现数据。

    ```java
    //Create a layer and add it to the map.
    BubbleLayer layer = new BubbleLayer(source);
    map.layers.add(layer);
    ```

::: zone-end

::: zone pivot="programming-language-kotlin"

4. 创建一个名为“Utils.kt”的新文件，并将以下代码添加到该文件中。 此代码提供了名为 `importData` 的静态方法，该方法使用 URL 作为字符串从应用程序的 `assets` 文件夹或从 Web 异步导入文件，然后使用简单的回调方法将其返回给 UI 线程。

    ```kotlin
    //Modify the package name as needed to align with your application.
    package com.example.myapplication;

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

5. 进入 MainActivity.kt 文件，并在 `mapControl.onReady` 事件的回调（位于 `onCreate` 方法内）中添加以下代码。 此代码使用导入实用工具以字符串的形式读取 SamplePoiDataSet.json 文件，然后使用 `FeatureCollection` 类的静态 `fromJson` 方法将其反序列化为功能集合。 此代码还计算功能集合中所有数据的边界框区域，并使用此边界框区域来设置地图的摄像头以将焦点对准数据。

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

6. 使用此代码将 GeoJSON 数据加载到数据源，我们现在需要指定该数据在地图上的显示方式。 点数据有多个不同的渲染层；[气泡层](map-add-bubble-layer-android.md)、[符号层](how-to-add-symbol-to-android-map.md)和[热度地图层](map-add-heat-map-layer-android.md)是最常用的层。 在用于导入数据的代码后面，添加以下代码，以在 `mapControl.onReady` 事件的回调的气泡层中呈现数据。

    ```kotlin
    //Create a layer and add it to the map.
    val layer = new BubbleLayer(source)
    map.layers.add(layer)
    ```

::: zone-end

7. 运行应用程序。 显示的地图将以美国为中心，其中圆圈覆盖了 GeoJSON 文件中的每个位置。

    ![显示 GeoJSON 文件中所含数据的美国地图](media/tutorial-load-geojson-file-android/android-import-geojson.png)

## <a name="clean-up-resources"></a>清理资源

请执行以下步骤以清理本教程中的资源：

1. 关闭 Android Studio 并删除你创建的应用程序。
2. 如果在外部设备上测试了应用程序，请从该设备上卸载它。

## <a name="next-steps"></a>后续步骤

若要查看更多代码示例和交互式编码体验，请参阅：

> [!div class="nextstepaction"]
> [使用数据驱动样式表达式](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [显示功能信息](display-feature-information-android.md)

> [!div class="nextstepaction"]
> [添加符号层](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [添加线条层](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [添加多边形层](how-to-add-shapes-to-android-map.md)
