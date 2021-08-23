---
title: 教程 - 迁移 Android 应用 | Microsoft Azure Maps
description: 有关如何将 Android 应用从 Google Maps 迁移到 Microsoft Azure Maps 的教程
author: rbrundritt
ms.author: richbrun
ms.date: 02/26/2021
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 95f24173f584cd6ac6e97ccae053706ff5b8f2cf
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "113091458"
---
# <a name="tutorial-migrate-an-android-app-from-google-maps"></a>教程：从 Google Maps 迁移 Android 应用

Azure Maps Android SDK 有一个类似于 Web SDK 的 API 接口。 如果你已使用其中的某个 SDK 进行开发，可以应用很多相同的概念、最佳做法和体系结构。 在本教程中，您将学习如何执行以下操作：

> [!div class="checklist"]
> * 加载地图
> * 将地图本地化
> * 添加标记、折线和多边形。
> * 叠加图块层
> * 显示交通情况数据

所有示例都是以 Java 提供的，但是，也可以结合 Azure Maps Android SDK 使用 Kotlin。

有关使用 Azure Maps Android SDK 进行开发的详细信息，请参阅 [Azure Maps Android SDK 的操作指南](how-to-use-android-map-control-library.md)。

## <a name="prerequisites"></a>先决条件

1. 通过登录到 [Azure 门户](https://portal.azure.com)来创建 Azure Maps 帐户。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。
2. [创建 Azure Maps 帐户](quick-demo-map-app.md#create-an-azure-maps-account)
3. [获取主订阅密钥](quick-demo-map-app.md#get-the-primary-key-for-your-account)（亦称为“主密钥”或“订阅密钥”）。 有关 Azure Maps 中身份验证的详细信息，请参阅[在 Azure Maps 中管理身份验证](how-to-manage-authentication.md)。

## <a name="load-a-map"></a>加载地图

使用 Google Maps 或 Azure Maps 在 Android 应用中加载地图涉及到类似的步骤。 使用上述任一 SDK 时，必须：

* 获取用于访问其中一个平台的 API 密钥或订阅密钥。
* 将一些 XML 添加到活动中，以指定要在何处呈现地图，以及如何布局地图。
* 将包含地图视图的活动中的所有生命周期方法重写为地图类中的相应方法。 具体而言，必须替代以下方法：
  * `onCreate(Bundle)`
  * `onStart()`
  * `onResume()`
  * `onPause()`
  * `onStop()`
  * `onDestroy()`
  * `onSaveInstanceState(Bundle)`
  * `onLowMemory()`
* 在尝试访问和设定地图之前，请等待地图准备就绪。

### <a name="before-google-maps"></a>前者：Google Maps

若要使用适用于 Android 的 Google Maps SDK 显示地图，需执行以下步骤：

1. 确保已安装 Google Play 服务。
2. 将 Google Maps 服务的依赖项添加到模块的 gradle.build 文件：

    `implementation 'com.google.android.gms:play-services-maps:17.0.0'`

3. 将 Google Maps API 密钥添加到 **google\_maps\_api.xml** 文件的 application 节中：

    ```xml
    <meta-data android:name="com.google.android.geo.API_KEY" android:value="YOUR_GOOGLE_MAPS_KEY"/>
    ```

4. 将一个地图片段添加到主活动：

    ```xml
    <com.google.android.gms.maps.MapView
            android:id="@+id/myMap"
            android:layout_width="match_parent"
            android:layout_height="match_parent"/>
    ```

::: zone pivot="programming-language-java-android"

5. 在 **MainActivity.java** 文件中，需要导入 Google Maps SDK。 将包含地图视图的活动中的所有生命周期方法转发到地图类中的相应方法。 使用 `getMapAsync(OnMapReadyCallback)` 方法从地图片段中检索 `MapView` 实例。 `MapView` 将自动初始化地图系统和视图。 按如下所示编辑 **MainActivity.java** 文件：

    ```java
    import com.google.android.gms.maps.GoogleMap;
    import com.google.android.gms.maps.MapView;
    import com.google.android.gms.maps.OnMapReadyCallback;
 
    import android.support.v7.app.AppCompatActivity;
    import android.os.Bundle;
    
    public class MainActivity extends AppCompatActivity implements OnMapReadyCallback {
    
        MapView mapView;
    
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
    
            mapView = findViewById(R.id.myMap);
    
            mapView.onCreate(savedInstanceState);
            mapView.getMapAsync(this);
        }
    
        @Override
    
        public void onMapReady(GoogleMap map) {
            //Add your post map load code here.
        }
    
        @Override
        public void onResume() {
            super.onResume();
            mapView.onResume();
        }
    
        @Override
        protected void onStart(){
            super.onStart();
            mapView.onStart();
        }
    
        @Override
        public void onPause() {
            super.onPause();
            mapView.onPause();
        }
    
        @Override
        public void onStop() {
            super.onStop();
            mapView.onStop();
        }
    
        @Override
        public void onLowMemory() {
            super.onLowMemory();
            mapView.onLowMemory();
        }
    
        @Override
        protected void onDestroy() {
            super.onDestroy();
            mapView.onDestroy();
        }
    
        @Override
        protected void onSaveInstanceState(Bundle outState) {
            super.onSaveInstanceState(outState);
            mapView.onSaveInstanceState(outState);
        }
    }
    ```

::: zone-end

::: zone pivot="programming-language-kotlin"

5. 在 MainActivity.kt 文件中，需要导入 Google Maps SDK。 将包含地图视图的活动中的所有生命周期方法转发到地图类中的相应方法。 使用 `getMapAsync(OnMapReadyCallback)` 方法从地图片段中检索 `MapView` 实例。 `MapView` 将自动初始化地图系统和视图。 按如下所示编辑 MainActivity.kt 文件：

    ```kotlin
    import com.google.android.gms.maps.GoogleMap;
    import com.google.android.gms.maps.MapView;
    import com.google.android.gms.maps.OnMapReadyCallback;
 
    import androidx.appcompat.app.AppCompatActivity
    import android.os.Bundle

    class MainActivity : AppCompatActivity() implements OnMapReadyCallback {
    
        var mapView: MapView? = null
    
        override fun onCreate(savedInstanceState: Bundle?) {
            super.onCreate(savedInstanceState)
            setContentView(R.layout.activity_main)
    
            mapView = findViewById(R.id.myMap)
    
            mapView?.onCreate(savedInstanceState)
            mapView?.getMapAsync(this)
        }

        public fun onMapReady(GoogleMap map) {
            //Add your post map load code here.
        }
    
        public override fun onStart() {
            super.onStart()
            mapView?.onStart()
        }
    
        public override fun onResume() {
            super.onResume()
            mapView?.onResume()
        }
    
        public override fun onPause() {
            mapView?.onPause()
            super.onPause()
        }
    
        public override fun onStop() {
            mapView?.onStop()
            super.onStop()
        }
    
        override fun onLowMemory() {
            mapView?.onLowMemory()
            super.onLowMemory()
        }
    
        override fun onDestroy() {
            mapView?.onDestroy()
            super.onDestroy()
        }
    
        override fun onSaveInstanceState(outState: Bundle) {
            super.onSaveInstanceState(outState)
            mapView?.onSaveInstanceState(outState)
        }
    }
    ```

::: zone-end

运行应用程序时，将按下图所示加载地图控件。

![简单 Google Maps](media/migrate-google-maps-android-app/simple-google-maps.png)

### <a name="after-azure-maps"></a>后者：Azure Maps

若要使用适用于 Android 的 Azure Maps SDK 显示地图，需执行以下步骤：

1. 打开顶级 **build.gradle** 文件，将以下代码添加到 **所有项目** 块节中：

    ```gradel
    maven {
        url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. 更新 **app/build.gradle** 并在其中添加以下代码：

    1. 确保项目的 **minSdkVersion** 设置为 API 21 或更高版本。

    2. 将以下代码添加到 Android 节：

        ```gradel
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```

    3. 更新 dependencies 块。 为最新 Azure Maps Android SDK 添加新的实现依赖项行：

        ```gradel
        implementation "com.azure.android:azure-maps-control:1.0.0"
        ```

        > [!Note]
        > 可将版本号设置为“0+”，使代码始终指向最新版本。

    4. 在工具栏中转到“文件”，然后单击“将项目与 Gradle 文件同步”。 

3. 将一个地图片段添加到 main 活动 (resources pwd\> layout \> activity\_main.xml)：

    ```XML
    <?xml version="1.0" encoding="utf-8"?>
    <FrameLayout
        xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        >

        <com.azure.android.maps.control.MapControl
            android:id="@+id/mapcontrol"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            />
    </FrameLayout>
    ```

::: zone pivot="programming-language-java-android"

4. 在 **MainActivity.java** 文件中，需要：

    * 导入 Azure Maps SDK
    * 设置 Azure Maps 身份验证信息
    * 在 **onCreate** 方法中获取地图控件实例

     使用 `setSubscriptionKey` 或 `setAadProperties` 方法在 `AzureMaps` 类中设置身份验证信息。 此全局更新可确保将身份验证信息添加到每个视图。

    地图控件包含自身的生命周期方法用于管理 Android 的 OpenGL 生命周期。 必须直接从包含的活动调用这些方法。 若要正确调用地图控件的生命周期方法，必须在包含地图控件的活动中重写以下生命周期方法。 调用相应的地图控件方法。

    * `onCreate(Bundle)`
    * `onStart()`
    * `onResume()`
    * `onPause()`
    * `onStop()`
    * `onDestroy()`
    * `onSaveInstanceState(Bundle)`
    * `onLowMemory()`

    按如下所示编辑 **MainActivity.java** 文件：

    ```java
    package com.example.myapplication;
    
    import androidx.appcompat.app.AppCompatActivity;
    import com.azure.android.maps.control.AzureMaps;
    import com.azure.android.maps.control.MapControl;
    import com.azure.android.maps.control.layer.SymbolLayer;
    import com.azure.android.maps.control.options.MapStyle;
    import com.azure.android.maps.control.source.DataSource;
    
    public class MainActivity extends AppCompatActivity {
    
    static {
        AzureMaps.setSubscriptionKey("<Your Azure Maps subscription key>");

        //Alternatively use Azure Active Directory authenticate.
        //AzureMaps.setAadProperties("<Your aad clientId>", "<Your aad AppId>", "<Your aad Tenant>");
    }

    MapControl mapControl;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        mapControl = findViewById(R.id.mapcontrol);

        mapControl.onCreate(savedInstanceState);

        //Wait until the map resources are ready.
        mapControl.onReady(map -> {
            //Add your post map load code here.

        });
    }

    @Override
    public void onResume() {
        super.onResume();
        mapControl.onResume();
    }

    @Override
    protected void onStart(){
        super.onStart();
        mapControl.onStart();
    }

    @Override
    public void onPause() {
        super.onPause();
        mapControl.onPause();
    }

    @Override
    public void onStop() {
        super.onStop();
        mapControl.onStop();
    }

    @Override
    public void onLowMemory() {
        super.onLowMemory();
        mapControl.onLowMemory();
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        mapControl.onDestroy();
    }

    @Override
    protected void onSaveInstanceState(Bundle outState) {
        super.onSaveInstanceState(outState);
        mapControl.onSaveInstanceState(outState);
    }}
    ```

::: zone-end

::: zone pivot="programming-language-kotlin"

4. 在 MainActivity.kt 文件中，需要：

    * 导入 Azure Maps SDK
    * 设置 Azure Maps 身份验证信息
    * 在 **onCreate** 方法中获取地图控件实例

     使用 `setSubscriptionKey` 或 `setAadProperties` 方法在 `AzureMaps` 类中设置身份验证信息。 此全局更新可确保将身份验证信息添加到每个视图。

    地图控件包含自身的生命周期方法用于管理 Android 的 OpenGL 生命周期。 必须直接从包含的活动调用这些方法。 若要正确调用地图控件的生命周期方法，必须在包含地图控件的活动中重写以下生命周期方法。 调用相应的地图控件方法。

    * `onCreate(Bundle)`
    * `onStart()`
    * `onResume()`
    * `onPause()`
    * `onStop()`
    * `onDestroy()`
    * `onSaveInstanceState(Bundle)`
    * `onLowMemory()`

    按如下所示编辑 MainActivity.kt 文件：

    ```kotlin
    package com.example.myapplication;

    import androidx.appcompat.app.AppCompatActivity
    import android.os.Bundle
    import com.azure.android.maps.control.AzureMap
    import com.azure.android.maps.control.AzureMaps
    import com.azure.android.maps.control.MapControl
    import com.azure.android.maps.control.events.OnReady
    
    class MainActivity : AppCompatActivity() {
    
        companion object {
            init {
                AzureMaps.setSubscriptionKey("<Your Azure Maps subscription key>");
    
                //Alternatively use Azure Active Directory authenticate.
                //AzureMaps.setAadProperties("<Your aad clientId>", "<Your aad AppId>", "<Your aad Tenant>");
            }
        }
    
        var mapControl: MapControl? = null
    
        override fun onCreate(savedInstanceState: Bundle?) {
            super.onCreate(savedInstanceState)
            setContentView(R.layout.activity_main)
    
            mapControl = findViewById(R.id.mapcontrol)
    
            mapControl?.onCreate(savedInstanceState)
    
            //Wait until the map resources are ready.
            mapControl?.onReady(OnReady { map: AzureMap -> })
        }
    
        public override fun onStart() {
            super.onStart()
            mapControl?.onStart()
        }
    
        public override fun onResume() {
            super.onResume()
            mapControl?.onResume()
        }
    
        public override fun onPause() {
            mapControl?.onPause()
            super.onPause()
        }
    
        public override fun onStop() {
            mapControl?.onStop()
            super.onStop()
        }
    
        override fun onLowMemory() {
            mapControl?.onLowMemory()
            super.onLowMemory()
        }
    
        override fun onDestroy() {
            mapControl?.onDestroy()
            super.onDestroy()
        }
    
        override fun onSaveInstanceState(outState: Bundle) {
            super.onSaveInstanceState(outState)
            mapControl?.onSaveInstanceState(outState)
        }
    }
    ```

::: zone-end

如果运行应用程序，将按下图中所示加载地图控件。

![简单 Azure Maps](media/migrate-google-maps-android-app/simple-azure-maps.png)

请注意，Azure Maps 控件支持更大程度的缩小操作，并不仅提供世界视图。

> [!TIP]
> 如果在 Windows 计算机上使用 Android 仿真器，则地图可能无法呈现，因为与 OpenGL 和软件加速图形渲染之间存在冲突。 某些用户已通过以下方法解决了此问题。 打开 AVD 管理器，并选择要编辑的虚拟设备。 在“验证配置”面板中向下滚动。 在“仿真性能”部分，将“图形”选项设置为“硬件”。  

## <a name="localizing-the-map"></a>本地化地图

如果受众遍布多个国家/地区或讲不同的语言，则本地化就非常重要。

### <a name="before-google-maps"></a>前者：Google Maps

将以下代码添加到 `onCreate` 方法，以设置地图的语言。 必须在设置地图的上下文视图之前添加该代码。 “fr”语言代码将语言限制为法语。

::: zone pivot="programming-language-java-android"

```java
String languageToLoad = "fr";
Locale locale = new Locale(languageToLoad);
Locale.setDefault(locale);

Configuration config = new Configuration();
config.locale = locale;

getBaseContext().getResources().updateConfiguration(config,
        getBaseContext().getResources().getDisplayMetrics());
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val languageToLoad = "fr"
val locale = Locale(languageToLoad)
Locale.setDefault(locale)

val config = Configuration()
config.locale = locale

baseContext.resources.updateConfiguration(
    config,
    baseContext.resources.displayMetrics
)
```

::: zone-end

下面是将语言设置为“fr”的 Google Maps 示例。

![Google Maps 本地化](media/migrate-google-maps-android-app/google-maps-localization.png)

### <a name="after-azure-maps&quot;></a>后者：Azure Maps

Azure Maps 提供三种不同的方式来设置地图的语言和区域视图。 第一个选项是将语言和区域视图信息直接传递到 `AzureMaps` 类。 此选项全局使用静态 `setLanguage` 和 `setView` 方法。 即，在载入到应用的所有 Azure Maps 控件中设置默认的语言和区域视图。 此示例使用“fr-FR”语言代码设置法语。

::: zone pivot="programming-language-java-android"

```java
static {
    //Set your Azure Maps Key.
    AzureMaps.setSubscriptionKey(&quot;<Your Azure Maps Key>");

    //Set the language to be used by Azure Maps.
    AzureMaps.setLanguage("fr-FR");

    //Set the regional view to be used by Azure Maps.
    AzureMaps.setView("Auto");
}
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
companion object {
    init {
            //Set your Azure Maps Key.
        AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");
    
        //Set the language to be used by Azure Maps.
        AzureMaps.setLanguage("fr-FR");
    
        //Set the regional view to be used by Azure Maps.
        AzureMaps.setView("Auto");
    }
}
```

::: zone-end

第二个选项是将语言和视图信息传递到地图控件 XML 代码中。

```xml
<com.azure.android.maps.control.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:azure_maps_language="fr-FR"
    app:azure_maps_view="Auto"
    />
```

第三个选项是使用地图 `setStyle` 方法来设定语言和区域地图视图。 每次执行代码时，此选项都会更新语言和区域视图。

::: zone pivot="programming-language-java-android"

```java
mapControl.onReady(map -> {
    map.setStyle(
        language("fr-FR"),
        view("Auto")
    );
});
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
mapControl!!.onReady { map: AzureMap ->
    map.setStyle(
        language("fr-FR"),
        view("Auto")
    )
}
```

::: zone-end

下面是将语言设置为“fr-FR”的 Azure Maps 示例。

![Azure Maps 本地化](media/migrate-google-maps-android-app/azure-maps-localization.png)

查看[支持的语言](supported-languages.md)完整列表。

## <a name="setting-the-map-view"></a>设置地图视图

可以通过调用相应的方法，以编程方式将 Azure Maps 和 Google Maps 中的动态地图移到新的地理位置。 让我们在地图中显示卫星航拍图像，将地图中心点置于某个坐标位置，并更改缩放级别。 此示例使用纬度：35.0272，经度：-111.0225，缩放级别：15。

### <a name="before-google-maps"></a>前者：Google Maps

可以使用 `moveCamera` 方法以编程方式移动 Google Maps 地图控件的相机。 `moveCamera` 方法允许指定地图的中心点和缩放级别。 `setMapType` 方法更改要显示的地图类型。

::: zone pivot="programming-language-java-android"

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.moveCamera(CameraUpdateFactory.newLatLngZoom(new LatLng(35.0272, -111.0225), 15));
    mapView.setMapType(GoogleMap.MAP_TYPE_SATELLITE);
}
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
public override fun onMapReady(googleMap: GoogleMap) {
    mapView = googleMap

    mapView.moveCamera(CameraUpdateFactory.newLatLngZoom(LatLng(35.0272, -111.0225), 15))
    mapView.setMapType(GoogleMap.MAP_TYPE_SATELLITE)
}
```

::: zone-end

![Google Maps - 设置视图](media/migrate-google-maps-android-app/google-maps-set-view.png)

> [!NOTE]
> Google Maps 使用尺寸为 256 像素的图块，而 Azure Maps 则使用更大的 512 像素图块。 在加载相同的地图区域时，与 Google Maps 相比，这可以减少 Azure Maps 所需的网络请求数。 若要实现与 Google Maps 中地图相同的可视区域，在使用 Azure Maps 时，需要将 Google Maps 中使用的缩放级别减 1。

### <a name="after-azure-maps"></a>后者：Azure Maps

如前所述，若要在 Azure Maps 中实现相同的可视区域，请将 Google Maps 中使用的缩放级别减 1。 本例中使用的缩放级别为 14。

可以在地图控件上的 XML 特性中设置初始地图视图。

```xml
<com.azure.android.maps.control.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:azure_maps_cameraLat="35.0272"
    app:azure_maps_cameraLng="-111.0225"
    app:azure_maps_zoom="14"
    app:azure_maps_style="satellite"
    />
```

可以使用地图 `setCamera` 和 `setStyle` 方法设定地图视图。

::: zone pivot="programming-language-java-android"

```java
mapControl.onReady(map -> {
    //Set the camera of the map.
    map.setCamera(center(Point.fromLngLat(-111.0225, 35.0272)), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE));
});
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
mapControl!!.onReady { map: AzureMap ->
    //Set the camera of the map.
    map.setCamera(center(Point.fromLngLat(-111.0225, 35.0272)), zoom(14))

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE))
}
```

::: zone-end

![Azure Maps - 设置视图](media/migrate-google-maps-android-app/azure-maps-set-view.png)

**其他资源：**

* [支持的地图样式](supported-map-styles.md)

## <a name="adding-a-marker"></a>添加标记

通常使用地图上的图像呈现点数据。 这些图像称为标记、图钉或符号。 以下示例在地图上的以下坐标以标记形式呈现点数据：纬度：51.5，经度：-0.2。

### <a name="before-google-maps"></a>前者：Google Maps

在 Google Maps 中，标记是使用地图 `addMarker` 方法添加的。

::: zone pivot="programming-language-java-android"

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.addMarker(new MarkerOptions().position(new LatLng(47.64, -122.33)));
}
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
public override fun onMapReady(googleMap: GoogleMap) {
    mapView = googleMap

    mapView.addMarker(MarkerOptions().position(LatLng(47.64, -122.33)))
}
```

::: zone-end

![Google Maps 标记](media/migrate-google-maps-android-app/google-maps-marker.png)

### <a name="after-azure-maps"></a>后者：Azure Maps

在 Azure Maps 中，通过先将数据添加到数据源，在地图上呈现点数据。 然后将该数据源连接到符号层。 数据源优化地图控件中空间数据的管理。 符号层指定如何以图像或文本的形式呈现点数据。

::: zone pivot="programming-language-java-android"

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource source = new DataSource();
    map.sources.add(source);

    //Create a point feature and add it to the data source.
    source.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));

    //Create a symbol layer and add it to the map.
    map.layers.add(new SymbolLayer(source));
});
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
mapControl!!.onReady { map: AzureMap ->
    //Create a data source and add it to the map.
    val source = new DataSource()
    map.sources.add(source)

    //Create a point feature and add it to the data source.
    source.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)))

    //Create a symbol layer and add it to the map.
    map.layers.add(SymbolLayer(source))
}
```

::: zone-end

![Azure Maps 标记](media/migrate-google-maps-android-app/azure-maps-marker.png)

## <a name="adding-a-custom-marker"></a>添加自定义标记

可使用自定义图像来表示地图上的点。 以下示例中的地图使用一个自定义图像显示某个点。 该点的位置为纬度：51.5，经度：-0.2。 定位点将标记位置偏移，使图钉图标的点与地图上的正确位置对齐。

![黄色图钉图像](media/migrate-google-maps-web-app/yellow-pushpin.png)<br/>
yellow-pushpin.png

在这两个示例中，以上图像将添加到应用资源的 drawable 文件夹中。

### <a name="before-google-maps"></a>前者：Google Maps

在 Google Maps 中，可将自定义图像用于标记。 使用标记的 `icon` 选项加载自定义图像。 若要使图像的点与坐标对齐，请使用 `anchor` 选项。 定位点相对于图像的尺寸。 在本例中，定位点的宽度为 0.2 个单位，高度为 1 个单位。

::: zone pivot="programming-language-java-android"

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.addMarker(new MarkerOptions().position(new LatLng(47.64, -122.33))
    .icon(BitmapDescriptorFactory.fromResource(R.drawable.yellow-pushpin))
    .anchor(0.2f, 1f));
}
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
public override fun onMapReady(googleMap: GoogleMap) {
    mapView = googleMap;

    mapView.addMarker(MarkerOptions().position(LatLng(47.64, -122.33))
    .icon(BitmapDescriptorFactory.fromResource(R.drawable.yellow-pushpin))
    .anchor(0.2f, 1f))
}
```

::: zone-end

![Google Maps 自定义标记](media/migrate-google-maps-android-app/google-maps-custom-marker.png)

### <a name="after-azure-maps"></a>后者：Azure Maps

Azure Maps 中的符号层也支持自定义图像，但需要先将图像加载到地图资源，并为其分配唯一的 ID。 然后，符号层需要引用此 ID。 使用 `iconOffset` 选项来偏移符号，使之与图像上的正确点对齐。 图标偏移量以像素为单位。 默认情况下，偏移量相对于图像的中下点，但可以使用 `iconAnchor` 选项调整此偏移量值。 此示例将 `iconAnchor` 选项设置为 `"center"`。 它使用图标偏移量将图像向右移动 5 个像素，向上移动 15 个像素，以便与图钉图像的点对齐。

::: zone pivot="programming-language-java-android"

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource source = new DataSource();
    map.sources.add(source);

    //Create a point feature and add it to the data source.
    source.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));

    //Load the custom image icon into the map resources.
    map.images.add("my-yellow-pin", R.drawable.yellow_pushpin);

    //Create a symbol that uses the custom image icon and add it to the map.
    map.layers.add(new SymbolLayer(source,
        iconImage("my-yellow-pin"),
        iconAnchor(AnchorType.CENTER),
        iconOffset(new Float[]{5f, -15f})));
});
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
mapControl!!.onReady { map: AzureMap ->
    //Create a data source and add it to the map.
    val source = DataSource()
    map.sources.add(source)

    //Create a point feature and add it to the data source.
    source.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)))

    //Load the custom image icon into the map resources.
    map.images.add("my-yellow-pin", R.drawable.yellow_pushpin)

    //Create a symbol that uses the custom image icon and add it to the map.
    map.layers.add(SymbolLayer(source,
        iconImage("my-yellow-pin"),
        iconAnchor(AnchorType.CENTER),
        iconOffset(arrayOf(0f, -1.5f))))
}
```

::: zone-end

![Azure Maps 自定义标记](media/migrate-google-maps-android-app/azure-maps-custom-marker.png)

## <a name="adding-a-polyline"></a>添加折线

折线用于表示地图上的线条或路径。 以下示例演示如何在地图上创建虚线折线。

### <a name="before-google-maps"></a>前者：Google Maps

Google Maps 使用 `PolylineOptions` 类呈现折线。 使用 `addPolyline` 方法将折线添加到地图中。 使用 `color` 选项设置笔划颜色。 使用 `width` 选项设置笔划宽度。 使用 `pattern` 选项添加笔划虚线数组。

::: zone pivot="programming-language-java-android"

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the polyline.
    PolylineOptions lineOptions = new PolylineOptions()
        .add(new LatLng(46, -123))
        .add(new LatLng(49, -122))
        .add(new LatLng(46, -121))
        .color(Color.RED)
        .width(10f)
        .pattern(Arrays.<PatternItem>asList(
                new Dash(30f), new Gap(30f)));

    //Add the polyline to the map.
    Polyline polyline = mapView.addPolyline(lineOptions);
}
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
public override fun onMapReady(googleMap: GoogleMap) {
    mapView = googleMap

    //Create the options for the polyline.
    val lineOptions = new PolylineOptions()
        .add(new LatLng(46, -123))
        .add(new LatLng(49, -122))
        .add(new LatLng(46, -121))
        .color(Color.RED)
        .width(10f)
        .pattern(Arrays.<PatternItem>asList(
                new Dash(30f), new Gap(30f)))

    //Add the polyline to the map.
    val polyline = mapView.addPolyline(lineOptions)
}
```

::: zone-end

![Google Maps 折线](media/migrate-google-maps-android-app/google-maps-polyline.png)

### <a name="after-azure-maps"></a>后者：Azure Maps

在 Azure Maps 中，折线称为 `LineString` 或 `MultiLineString` 对象。 将这些对象添加到数据源，并使用线条层来呈现。 使用 `strokeWidth` 选项设置笔划宽度。 使用 `strokeDashArray` 选项添加笔划虚线数组。

Azure Maps Web SDK 中的笔划宽度和虚线数组“像素”单位与 Google Maps 服务中相同。 这两个 SDK 允许使用相同的值来生成相同的结果。

::: zone pivot="programming-language-java-android"

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource source = new DataSource();
    map.sources.add(source);

    //Create an array of points.
    List<Point> points = Arrays.asList(
        Point.fromLngLat(-123, 46),
        Point.fromLngLat(-122, 49),
        Point.fromLngLat(-121, 46));

    //Create a LineString feature and add it to the data source.
    source.add(Feature.fromGeometry(LineString.fromLngLats(points)));

    //Create a line layer and add it to the map.
    map.layers.add(new LineLayer(source,
        strokeColor("red"),
        strokeWidth(4f),
        strokeDashArray(new Float[]{3f, 3f})));
});
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
mapControl!!.onReady { map: AzureMap ->
    //Create a data source and add it to the map.
    val source = DataSource()
    map.sources.add(source)

    //Create an array of points.
    val points = Arrays.asList(
        Point.fromLngLat(-123, 46),
        Point.fromLngLat(-122, 49),
        Point.fromLngLat(-121, 46))

    //Create a LineString feature and add it to the data source.
    source.add(Feature.fromGeometry(LineString.fromLngLats(points)))

    //Create a line layer and add it to the map.
    map.layers.add(LineLayer(source,
        strokeColor("red"),
        strokeWidth(4f),
        strokeDashArray(new Float[]{3f, 3f})))
}
```

::: zone-end

![Azure Maps 折线](media/migrate-google-maps-android-app/azure-maps-polyline.png)

## <a name="adding-a-polygon"></a>添加多边形

多边形用于表示地图上的某个区域。 下面的示例演示如何创建多边形。 该多边形基于地图的中点坐标构成一个三角形。

### <a name="before-google-maps"></a>前者：Google Maps

Google Maps 使用 `PolygonOptions` 类呈现多边形。 使用 `addPolygon` 方法将多边形添加到地图中。 分别使用 `fillColor` 和 `strokeColor` 选项设置填充颜色和笔划颜色。 使用 `strokeWidth` 选项设置笔划宽度。

::: zone pivot="programming-language-java-android"

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the polygon.
    PolygonOptions polygonOptions = new PolygonOptions()
            .add(new LatLng(46, -123))
            .add(new LatLng(49, -122))
            .add(new LatLng(46, -121))
            .add(new LatLng(46, -123))  //Close the polygon.
            .fillColor(Color.argb(128, 0, 128, 0))
            .strokeColor(Color.RED)
            .strokeWidth(5f);

    //Add the polygon to the map.
    Polygon polygon = mapView.addPolygon(polygonOptions);
}
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
public override fun onMapReady(googleMap: GoogleMap) {
    mapView = googleMap;

    //Create the options for the polygon.
    val polygonOptions = PolygonOptions()
        .add(new LatLng(46, -123))
        .add(new LatLng(49, -122))
        .add(new LatLng(46, -121))
        .add(new LatLng(46, -123))  //Close the polygon.
        .fillColor(Color.argb(128, 0, 128, 0))
        .strokeColor(Color.RED)
        .strokeWidth(5f)

    //valAdd the polygon to the map.
    Polygon polygon = mapView.addPolygon(polygonOptions)
}
```

::: zone-end

![Google Maps 多边形](media/migrate-google-maps-android-app/google-maps-polygon.png)

### <a name="after-azure-maps"></a>后者：Azure Maps

在 Azure Maps 中，将 `Polygon` 和 `MultiPolygon` 对象添加到数据源，并使用层在地图上呈现这些对象。 在多边形层中呈现多边形的区域。 使用线条层呈现多边形的轮廓。 使用 `strokeColor` 和 `strokeWidth` 选项设置笔划颜色和宽度。

Azure Maps Web SDK 中的笔划宽度和虚线数组“像素”单位与 Google Maps 中的相应单位保持一致。 这两个 SDK 接受相同的值并生成相同的结果。

::: zone pivot="programming-language-java-android"

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource source = new DataSource();
    map.sources.add(source);

    //Create an array of point arrays to create polygon rings.
    List<List<Point>> rings = Arrays.asList(Arrays.asList(
        Point.fromLngLat(-123, 46),
        Point.fromLngLat(-122, 49),
        Point.fromLngLat(-121, 46),
        Point.fromLngLat(-123, 46)));

    //Create a Polygon feature and add it to the data source.
    source.add(Feature.fromGeometry(Polygon.fromLngLats(rings)));

    //Add a polygon layer for rendering the polygon area.
    map.layers.add(new PolygonLayer(source,
        fillColor("green"),
        fillOpacity(0.5f)));

    //Add a line layer for rendering the polygon outline.
    map.layers.add(new LineLayer(source,
        strokeColor("red"),
        strokeWidth(2f)));
});
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
mapControl!!.onReady { map: AzureMap ->
    //Create a data source and add it to the map.
    val source = DataSource()
    map.sources.add(source)

    //Create an array of point arrays to create polygon rings.
    val rings = Arrays.asList(Arrays.asList(
        Point.fromLngLat(-123, 46),
        Point.fromLngLat(-122, 49),
        Point.fromLngLat(-121, 46),
        Point.fromLngLat(-123, 46)))

    //Create a Polygon feature and add it to the data source.
    source.add(Feature.fromGeometry(Polygon.fromLngLats(rings)))

    //Add a polygon layer for rendering the polygon area.
    map.layers.add(PolygonLayer(source,
        fillColor("green"),
        fillOpacity(0.5f)))

    //Add a line layer for rendering the polygon outline.
    map.layers.add(LineLayer(source,
        strokeColor("red"),
        strokeWidth(2f)))
}
```

::: zone-end

![Azure Maps 多边形](media/migrate-google-maps-android-app/azure-maps-polygon.png)

## <a name="overlay-a-tile-layer"></a>叠加图块层

 使用图块层可以叠加层图像，这些图像已分解成与地图图块系统相符的较小图块式图像。 这是叠加层图像或大型数据集的常用方法。 图块层在 Google Maps 中称为图像叠加层。

以下示例叠加爱荷华州立大学环境气象网提供的气象雷达图块层。 图块大小为 256 像素。

### <a name="before-google-maps"></a>前者：Google Maps

在 Google Maps 中，可在地图的顶层叠加图块层。 使用 `TileOverlayOptions` 类。 使用 `addTileLayer` 方法将图块层添加到地图中。 若要使图块半透明，请将 `transparency` 选项设置为 0.2，即 20% 的透明度。

::: zone pivot="programming-language-java-android"

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the tile layer.
    TileOverlayOptions tileLayer = new TileOverlayOptions()
        .tileProvider(new UrlTileProvider(256, 256) {
            @Override
            public URL getTileUrl(int x, int y, int zoom) {

                try {
                    //Define the URL pattern for the tile images.
                    return new URL(String.format("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/%d/%d/%d.png", zoom, x, y));
                }catch (MalformedURLException e) {
                    throw new AssertionError(e);
                }
            }
        }).transparency(0.2f);

    //Add the tile layer to the map.
    mapView.addTileOverlay(tileLayer);
}
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
public override fun onMapReady(googleMap: GoogleMap) {
    mapView = googleMap
    //Create the options for the tile layer.
    val tileLayer: TileOverlayOptions = TileOverlayOptions()
        .tileProvider(object : UrlTileProvider(256, 256) {
            fun getTileUrl(x: Int, y: Int, zoom: Int): URL? {
                return try { //Define the URL pattern for the tile images.
                    URL(
                        String.format(
                            "https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/%d/%d/%d.png",
                            zoom,
                            x,
                            y
                        )
                    )
                } catch (e: MalformedURLException) {
                    throw AssertionError(e)
                }
            }
        }).transparency(0.2f)
    //Add the tile layer to the map.
    mapView.addTileOverlay(tileLayer)
}
```

::: zone-end

![Google Maps 图块层](media/migrate-google-maps-android-app/google-maps-tile-layer.png)

### <a name="after-azure-maps&quot;></a>后者：Azure Maps

可以像添加任何其他层一样将图块层添加到地图中。 指定包含 x 轴、y 轴和缩放比例占位符的带格式 URL；`{x}`、`{y}`、`{z}` 分别用于告知层要在何处访问图块。 此外，Azure Maps 中的图块层支持 `{quadkey}`、`{bbox-epsg-3857}` 和 `{subdomain}` 占位符。 若要使图块层半透明，请使用不透明度值 0.8。 尽管不透明度和透明度相似，但两者应使用反值。 若要在这两个选项之间进行转换，请用数字 1 减去其值。

> [!TIP]
> 在 Azure Maps 中，可以方便地在其他层（包括基础地图层）下面呈现层。 另外，通常情况下，最好是在地图标签下面呈现图块层，便于阅读。 `map.layers.add` 方法采用另一个参数，该参数是要在其中插入新层的层的 ID。 若要在地图标签下面插入图块层，可使用以下代码：`map.layers.add(myTileLayer, &quot;labels");`

::: zone pivot="programming-language-java-android"

```java
mapControl.onReady(map -> {
    //Add a tile layer to the map, below the map labels.
    map.layers.add(new TileLayer(
        tileUrl("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png"),
        opacity(0.8f),
        tileSize(256)
    ), "labels");
});
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
mapControl!!.onReady { map: AzureMap ->
    //Add a tile layer to the map, below the map labels.
    map.layers.add(TileLayer(
        tileUrl("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png"),
        opacity(0.8f),
        tileSize(256)
    ), "labels")
}
```

::: zone-end

![Azure Maps 图块层](media/migrate-google-maps-android-app/azure-maps-tile-layer.png)

## <a name="show-traffic"></a>显示交通情况

Azure Maps 和 Google Maps 都提供用于叠加交通状况数据的选项。

### <a name="before-google-maps"></a>前者：Google Maps

在 Google Maps 中，可以通过将 true 传递给地图的 `setTrafficEnabled` 方法，将交通流量数据叠加到地图的顶层。

::: zone pivot="programming-language-java-android"

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.setTrafficEnabled(true);
}
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
public override fun onMapReady(googleMap: GoogleMap) {
    mapView = googleMap

    mapView.setTrafficEnabled(true)
}
```

::: zone-end

![Google Maps 交通状况](media/migrate-google-maps-android-app/google-maps-traffic.png)

### <a name="after-azure-maps"></a>后者：Azure Maps

Azure Maps 提供用于显示交通状况的多种不同选项。 交通事件（例如道路封闭和事故）可以作为图标显示在地图上。 交通流量和带颜色编码的道路可以叠加在地图上。 可以修改颜色，以相对于公布的限速、正常的预期延误时间或绝对延误时间显示。 Azure Maps 中的事件数据每隔一分钟更新一次，交通流量数据每隔两分钟更新一次。

::: zone pivot="programming-language-java-android"

```java
mapControl.onReady(map -> {
    map.setTraffic(
        incidents(true),
        flow(TrafficFlow.RELATIVE));
});
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
mapControl!!.onReady { map: AzureMap ->
    map.setTraffic(
        incidents(true),
        flow(TrafficFlow.RELATIVE))
}
```

::: zone-end

![Azure Maps 交通状况](media/migrate-google-maps-android-app/azure-maps-traffic.png)

## <a name="clean-up-resources"></a>清理资源

没有要清理的资源。

## <a name="next-steps"></a>后续步骤

详细了解 Azure Maps Android SDK：

> [!div class="nextstepaction"]
> [Azure Maps Android SDK 入门](how-to-use-android-map-control-library.md)
