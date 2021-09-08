---
title: 在 Android SDK 中聚类点数据 | Microsoft Azure Maps
description: 了解如何在地图上聚类点数据。 了解如何使用 Azure Maps Android SDK 来聚类数据，响应聚类鼠标事件以及显示聚类聚合。
author: anastasia-ms
ms.author: v-stharr
ms.date: 03/23/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: a6b9de675c32c893e872d23835c856a0fe6c49d3
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123429813"
---
# <a name="clustering-point-data-in-the-android-sdk"></a>在 Android SDK 中聚类点数据

在地图上可视化大量的数据点时，数据点将会彼此重叠。 重叠可能会导致地图不可读且难以使用。 聚类点数据是将相邻的点数据合并在一起，并在地图上将其作为单个聚类数据点进行呈现的过程。 当用户放大地图时，聚类的各个数据点将会分开。 处理大量数据点时，请使用聚类过程来改善用户体验。

</br>

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Clustering-point-data-in-Azure-Maps/player?format=ny]

## <a name="prerequisites"></a>先决条件

请务必完成[快速入门：创建 Android 应用](quick-android-map.md)文档中的步骤。 可以将本文中的代码块插入到地图的 `onReady` 事件处理程序中。

## <a name="enabling-clustering-on-a-data-source"></a>对数据源启用聚类

通过将 `cluster` 选项设置为 `true`，在 `DataSource` 类中启用聚类。 设置 `clusterRadius` 以选择附近的点，并将它们合并为聚类。 `clusterRadius` 的值以像素为单位。 使用 `clusterMaxZoom` 指定禁用聚类逻辑的缩放级别。 下面的示例演示如何在数据源中启用聚类。

::: zone pivot="programming-language-java-android"

``` java
//Create a data source and enable clustering.
DataSource source = new DataSource(
    //Tell the data source to cluster point data.
    cluster(true),

    //The radius in pixels to cluster points together.
    clusterRadius(45),

    //The maximum zoom level in which clustering occurs.
    //If you zoom in more than this, all points are rendered as symbols.
    clusterMaxZoom(15)
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
 //Create a data source and enable clustering.
val source = DataSource( 
    //Tell the data source to cluster point data.
    cluster(true),  

    //The radius in pixels to cluster points together.
    clusterRadius(45),  

    //The maximum zoom level in which clustering occurs.
    //If you zoom in more than this, all points are rendered as symbols.
    clusterMaxZoom(15)
)
```

::: zone-end

> [!CAUTION]
> 聚类仅适用于 `Point` 特征。 如果数据源包含其他几何类型的特征（如 `LineString` 或 `Polygon`），则会发生错误。

> [!TIP]
> 如果两个数据点在地面上紧挨着，则聚类可能永远不会分解，无论用户放大多少倍。 若要解决此情况，可以设置 `clusterMaxZoom` 选项以禁用聚类逻辑，只是显示所有内容。

`DataSource` 类还提供了以下与聚类有关的方法。

| 方法 | 返回类型 | 说明 |
|--------|-------------|-------------|
| `getClusterChildren(Feature clusterFeature)` | `FeatureCollection` | 在下一个缩放级别检索给定聚类的子级。 这些子级可以是形状和子聚类的组合。 子聚类是包含与 ClusteredProperties 匹配的属性的特征。 |
| `getClusterExpansionZoom(Feature clusterFeature)` | `int` | 计算聚类开始展开或分开的缩放级别。 |
| `getClusterLeaves(Feature clusterFeature, long limit, long offset)` | `FeatureCollection` | 检索聚类中的所有点。 设置 `limit` 可返回点的子集，使用 `offset` 可逐页呈现点。 |

## <a name="display-clusters-using-a-bubble-layer"></a>使用气泡层显示聚类

气泡层是呈现聚类点的好方法。 使用表达式可基于聚类中的点数来缩放半径并更改颜色。 如果使用气泡层显示聚类，则应使用单独的层来呈现非聚类数据点。

若要在气泡顶部显示聚类的大小，请将符号层与文本一起使用，而不要使用图标。

以下代码显示使用气泡层的聚类点，以及每个聚类中使用符号层的点数。 第二个符号层用于显示不在聚类中的单个点。

::: zone pivot="programming-language-java-android"

``` java
//Create a data source and add it to the map.
DataSource source = new DataSource(
    //Tell the data source to cluster point data.
    cluster(true),

    //The radius in pixels to cluster points together.
    clusterRadius(45),

    //The maximum zoom level in which clustering occurs.
    //If you zoom in more than this, all points are rendered as symbols.
    clusterMaxZoom(15)
);

//Import the geojson data and add it to the data source.
map.importDataFromUrl("https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson");

//Add data source to the map.
map.sources.add(source);

//Create a bubble layer for rendering clustered data points.
map.layers.add(new BubbleLayer(source,
    //Scale the size of the clustered bubble based on the number of points in the cluster.
    bubbleRadius(
        step(
            get("point_count"),
            20,             //Default of 20 pixel radius.
            stop(100, 30),  //If point_count >= 100, radius is 30 pixels.
            stop(750, 40)   //If point_count >= 750, radius is 40 pixels.
        )
    ),

    //Change the color of the cluster based on the value on the point_cluster property of the cluster.
    bubbleColor(
        step(
            toNumber(get("point_count")),
            color(Color.GREEN),              //Default to lime green.
            stop(100, color(Color.YELLOW)),  //If the point_count >= 100, color is yellow.
            stop(750, color(Color.RED))      //If the point_count >= 100, color is red.
        )
    ),

    bubbleStrokeWidth(0f),

    //Only rendered data points which have a point_count property, which clusters do.
    BubbleLayerOptions.filter(has("point_count"))
));

//Create a symbol layer to render the count of locations in a cluster.
map.layers.add(new SymbolLayer(source,
    iconImage("none"),                //Hide the icon image.
    textField(get("point_count")),    //Display the point count as text.
    textOffset(new Float[]{ 0f, 0.4f }),

    //Allow clustered points in this layer.
    SymbolLayerOptions.filter(has("point_count"))
));

//Create a layer to render the individual locations.
map.layers.add(new SymbolLayer(source,
    //Filter out clustered points from this layer.
    SymbolLayerOptions.filter(not(has("point_count")))
));
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource( 
    //Tell the data source to cluster point data.
    cluster(true),  

    //The radius in pixels to cluster points together.
    clusterRadius(45),  

    //The maximum zoom level in which clustering occurs.
    //If you zoom in more than this, all points are rendered as symbols.
    clusterMaxZoom(15)
)

//Import the geojson data and add it to the data source.
map.importDataFromUrl("https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson")

//Add data source to the map.
map.sources.add(source)

//Create a bubble layer for rendering clustered data points.
map.layers.add(
    BubbleLayer(
        source,  

        //Scale the size of the clustered bubble based on the number of points in the cluster.
        bubbleRadius(
            step(
                get("point_count"),
                20,  //Default of 20 pixel radius.
                stop(100, 30),  //If point_count >= 100, radius is 30 pixels.
                stop(750, 40) //If point_count >= 750, radius is 40 pixels.
            )
        ),  

        //Change the color of the cluster based on the value on the point_cluster property of the cluster.
        bubbleColor(
            step(
                toNumber(get("point_count")),
                color(Color.GREEN),  //Default to lime green.
                stop(100, color(Color.YELLOW)),  //If the point_count >= 100, color is yellow.
                stop(750, color(Color.RED)) //If the point_count >= 100, color is red.
            )
        ),
        bubbleStrokeWidth(0f),  

        //Only rendered data points which have a point_count property, which clusters do.
        BubbleLayerOptions.filter(has("point_count"))
    )
)

//Create a symbol layer to render the count of locations in a cluster.
map.layers.add(
    SymbolLayer(
        source,
        iconImage("none"),  //Hide the icon image.
        textField(get("point_count")),  //Display the point count as text.
        textOffset(arrayOf(0f, 0.4f)),  

        //Allow clustered points in this layer.
        SymbolLayerOptions.filter(has("point_count"))
    )
)

//Create a layer to render the individual locations.
map.layers.add(
    SymbolLayer(
        source,  

        //Filter out clustered points from this layer.
        SymbolLayerOptions.filter(not(has("point_count")))
    )
)
```

::: zone-end

下图显示了上述代码，显示气泡层中的聚类点特征，这些特征根据聚类中的点数进行缩放和着色。 使用符号层呈现非聚类点。

![地图聚类位置在放大地图时分解](media/clustering-point-data-android-sdk/android-cluster-bubble-layer.gif)

## <a name="display-clusters-using-a-symbol-layer"></a>使用符号层显示聚类

可视化数据点时，符号层会自动隐藏彼此重叠的符号，以确保获得更清晰的用户界面。 如果要在地图上显示数据点密度，则可能不需要此默认行为。 但是，可以更改这些设置。 若要显示所有符号，请将符号层的 `iconAllowOverlap` 选项设置为 `true`。

使用聚类显示数据点密度，同时保持整洁的用户界面。 下面的示例演示如何使用符号层添加自定义符号并表示聚类和各个数据点。

::: zone pivot="programming-language-java-android"

``` java
//Load all the custom image icons into the map resources.
map.images.add("earthquake_icon", R.drawable.earthquake_icon);
map.images.add("warning_triangle_icon", R.drawable.warning_triangle_icon);

//Create a data source and add it to the map.
DataSource source = new DataSource(
    //Tell the data source to cluster point data.
    cluster(true)
);

//Import the geojson data and add it to the data source.
map.importDataFromUrl("https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson");

//Add data source to the map.
map.sources.add(source);

//Create a symbol layer to render the clusters.
map.layers.add(new SymbolLayer(source,
    iconImage("warning_triangle_icon"),
    textField(get("point_count")),
    textOffset(new Float[]{ 0f, -0.4f }),

    //Allow clustered points in this layer.
    filter(has("point_count"))
));

//Create a layer to render the individual locations.
map.layers.add(new SymbolLayer(source,
    iconImage("earthquake_icon"),

    //Filter out clustered points from this layer.
    filter(not(has("point_count")))
));
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Load all the custom image icons into the map resources.
map.images.add("earthquake_icon", R.drawable.earthquake_icon)
map.images.add("warning_triangle_icon", R.drawable.warning_triangle_icon)

//Create a data source and add it to the map.
val source = DataSource( 
    //Tell the data source to cluster point data.
    cluster(true)
)

//Import the geojson data and add it to the data source.
map.importDataFromUrl("https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson")

//Add data source to the map.
map.sources.add(source)

//Create a symbol layer to render the clusters.
map.layers.add(
    SymbolLayer(
        source,
        iconImage("warning_triangle_icon"),
        textField(get("point_count")),
        textOffset(arrayOf(0f, -0.4f)),  

        //Allow clustered points in this layer.
        filter(has("point_count"))
    )
)

//Create a layer to render the individual locations.
map.layers.add(
    SymbolLayer(
        source,
        iconImage("earthquake_icon"),  

        //Filter out clustered points from this layer.
        filter(not(has("point_count")))
    )
)
```

::: zone-end

对于本示例，以下图像加载到应用的可绘制资源文件夹中。

| ![地震图标图像](media/clustering-point-data-android-sdk/earthquake-icon.png) | ![阵雨的天气图标图像](media/clustering-point-data-android-sdk/warning-triangle-icon.png) |
|:-----------------------:|:--------------------------:|
| earthquake_icon.png     | warning_triangle_icon.png  |

下图显示了上述代码，这些代码使用自定义图标呈现聚类点和非聚类点特征。

![使用符号层呈现聚类点地图](media/clustering-point-data-android-sdk/android-cluster-symbol-layer.gif)

## <a name="clustering-and-the-heat-maps-layer&quot;></a>聚类和热度地图层

热度地图是在地图上显示数据密度的好方法。 此可视化方法可以自行处理大量数据点。 如果数据点进行了聚类，并且聚类大小用作热度地图的权重，则热度地图可以处理更多的数据。 若要实现此选项，请将热度地图层的 `heatmapWeight` 选项设置为 `get(&quot;point_count")`。 当聚类半径较小时，热度地图看起来几乎与使用非聚类数据点的热度地图完全相同，但性能会更好。 但是，聚类半径越小，热度地图便越精确，不过性能优势便越少。

::: zone pivot="programming-language-java-android"

``` java
//Create a data source and add it to the map.
DataSource source = new DataSource(
    //Tell the data source to cluster point data.
    cluster(true),

    //The radius in pixels to cluster points together.
    clusterRadius(10)
);

//Import the geojson data and add it to the data source.
map.importDataFromUrl("https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson");

//Add data source to the map.
map.sources.add(source);

//Create a heat map and add it to the map.
map.layers.add(new HeatMapLayer(source,
    //Set the weight to the point_count property of the data points.
    heatmapWeight(get("point_count")),

    //Optionally adjust the radius of each heat point.
    heatmapRadius(20f)
), "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource( 
    //Tell the data source to cluster point data.
    cluster(true),  

    //The radius in pixels to cluster points together.
    clusterRadius(10)
)

//Import the geojson data and add it to the data source.
map.importDataFromUrl("https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson")

//Add data source to the map.
map.sources.add(source)

//Create a heat map and add it to the map.
map.layers.add(
    HeatMapLayer(
        source,  
        
        //Set the weight to the point_count property of the data points.
        heatmapWeight(get("point_count")),  

        //Optionally adjust the radius of each heat point.
        heatmapRadius(20f)
    ), "labels"
)
```

::: zone-end

下图显示了上述显示热度地图的代码，该地图通过使用聚类点特征和聚类计数作为热度地图中的权重进行优化。

![使用聚类点作为权重进行优化的热度地图](media/clustering-point-data-android-sdk/android-cluster-heat-map.gif)

## <a name="mouse-events-on-clustered-data-points"></a>聚类数据点上的鼠标事件

当鼠标事件在包含聚类数据点的层上发生时，聚类数据点会作为 GeoJSON 点特征对象返回给事件。 此点特征具有以下属性：

| 属性名称             | 类型    | 说明   |
|---------------------------|---------|---------------|
| `cluster`                 | boolean | 指示特征是否表示聚类。 |
| `point_count`             | 数字  | 聚类包含的点数。  |
| `point_count`             | 数字  | 聚类包含的点数。  |
| `point_count_abbreviated` | 字符串  | 用于缩写过长的 `point_count` 值的字符串。 （例如，将 4,000 缩写为 4K）  |

此示例采用一个呈现聚类点并添加单击事件的气泡层。 当单击事件触发时，代码会计算地图，并将它缩放到下一个缩放级别，聚类将在该缩放级别分解。 此功能使用 `DataSource` 类的 `getClusterExpansionZoom` 方法和所单击的聚类数据点的 `cluster_id` 属性来实现。

::: zone pivot="programming-language-java-android"

``` java
//Create a data source and add it to the map.
DataSource source = new DataSource(
    //Tell the data source to cluster point data.
    cluster(true),

    //The radius in pixels to cluster points together.
    clusterRadius(45),

    //The maximum zoom level in which clustering occurs.
    //If you zoom in more than this, all points are rendered as symbols.
    clusterMaxZoom(15)
);

//Import the geojson data and add it to the data source.
source.importDataFromUrl("https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson");

//Add data source to the map.
map.sources.add(source);

//Create a bubble layer for rendering clustered data points.
BubbleLayer clusterBubbleLayer = new BubbleLayer(source,
    //Scale the size of the clustered bubble based on the number of points in the cluster.
    bubbleRadius(
        step(
            get("point_count"),
            20f,    //Default of 20 pixel radius.
            stop(100, 30),   //If point_count >= 100, radius is 30 pixels.
            stop(750, 40)    //If point_count >= 750, radius is 40 pixels.
        )
    ),

    //Change the color of the cluster based on the value on the point_cluster property of the cluster.
    bubbleColor(
        step(
            get("point_count"),
            color(Color.GREEN),  //Default to green.
            stop(100, color(Color.YELLOW)), //If the point_count >= 100, color is yellow.
            stop(750, color(Color.RED))     //If the point_count >= 100, color is red.
        )
    ),

    bubbleStrokeWidth(0f),

    //Only rendered data points which have a point_count property, which clusters do.
    BubbleLayerOptions.filter(has("point_count"))   
);

//Add the clusterBubbleLayer and two additional layers to the map.
map.layers.add(clusterBubbleLayer);

//Create a symbol layer to render the count of locations in a cluster.
map.layers.add(new SymbolLayer(source,
    //Hide the icon image.
    iconImage("none"),   

    //Display the 'point_count_abbreviated' property value.
    textField(get("point_count_abbreviated")), 
    
    //Offset the text position so that it's centered nicely.
    textOffset(new Float[] { 0f, 0.4f }),

    //Only rendered data points which have a point_count property, which clusters do.
    SymbolLayerOptions.filter(has("point_count")) 
));

//Create a layer to render the individual locations.
map.layers.add(new SymbolLayer(source,
    //Filter out clustered points from this layer.
    SymbolLayerOptions.filter(not(has("point_count"))) 
));

//Add a click event to the cluster layer so we can zoom in when a user clicks a cluster.
map.events.add((OnFeatureClick) (features) -> {
    if(features.size() > 0) {
        //Get the clustered point from the event.
        Feature cluster = features.get(0);

        //Get the cluster expansion zoom level. This is the zoom level at which the cluster starts to break apart.
        int expansionZoom = source.getClusterExpansionZoom(cluster);

        //Update the map camera to be centered over the cluster.
        map.setCamera(
            //Center the map over the cluster points location.
            center((Point)cluster.geometry()),

            //Zoom to the clusters expansion zoom level.
            zoom(expansionZoom),

            //Animate the movement of the camera to the new position.
            animationType(AnimationType.EASE),
            animationDuration(200)
        );
    }

    //Return true indicating if event should be consumed and not passed further to other listeners registered afterwards, false otherwise.
    return true;
}, clusterBubbleLayer);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource( //Tell the data source to cluster point data.
    //The radius in pixels to cluster points together.
    cluster(true),  

    //The maximum zoom level in which clustering occurs.
    clusterRadius(45),  

    //If you zoom in more than this, all points are rendered as symbols.
    clusterMaxZoom(15)
)

//Import the geojson data and add it to the data source.
source.importDataFromUrl("https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson")

//Add data source to the map.
map.sources.add(source)

//Create a bubble layer for rendering clustered data points.
val clusterBubbleLayer = BubbleLayer(
    source,  

    //Scale the size of the clustered bubble based on the number of points in the cluster.
    bubbleRadius(
        step(
            get("point_count"),
            20f,  //Default of 20 pixel radius.
            stop(100, 30),  //If point_count >= 100, radius is 30 pixels.
            stop(750, 40) //If point_count >= 750, radius is 40 pixels.
        )
    ),  
    
    //Change the color of the cluster based on the value on the point_cluster property of the cluster.
    bubbleColor(
        step(
            get("point_count"),
            color(Color.GREEN),  //Default to green.
            stop(
                100,
                color(Color.YELLOW)
            ),  //If the point_count >= 100, color is yellow.
            stop(750, color(Color.RED)) //If the point_count >= 100, color is red.
        )
    ),

    bubbleStrokeWidth(0f),  

    //Only rendered data points which have a point_count property, which clusters do.
    BubbleLayerOptions.filter(has("point_count"))
)

//Add the clusterBubbleLayer and two additional layers to the map.
map.layers.add(clusterBubbleLayer)

//Create a symbol layer to render the count of locations in a cluster.
map.layers.add(
    SymbolLayer(
        source,  

        //Hide the icon image.
        iconImage("none"),  

        //Display the 'point_count_abbreviated' property value.
        textField(get("point_count_abbreviated")),  

        //Offset the text position so that it's centered nicely.
        textOffset(
            arrayOf(
                0f,
                0.4f
            )
        ),  

        //Only rendered data points which have a point_count property, which clusters do.
        SymbolLayerOptions.filter(has("point_count"))
    )
)

//Create a layer to render the individual locations.
map.layers.add(
    SymbolLayer(
        source,  

        //Filter out clustered points from this layer.
        SymbolLayerOptions.filter(not(has("point_count")))
    )
)

//Add a click event to the cluster layer so we can zoom in when a user clicks a cluster.
map.events.add(OnFeatureClick { features: List<Feature?>? -> 
    if (features.size() > 0) {
        //Get the clustered point from the event.
        val cluster: Feature = features.get(0)

        //Get the cluster expansion zoom level. This is the zoom level at which the cluster starts to break apart.
        val expansionZoom: Int = source.getClusterExpansionZoom(cluster)

        //Update the map camera to be centered over the cluster.
        map.setCamera( 

            //Center the map over the cluster points location.
            center(cluster.geometry() as Point?),  

            //Zoom to the clusters expansion zoom level.
            zoom(expansionZoom),  

            //Animate the movement of the camera to the new position.
            animationType(AnimationType.EASE),
            animationDuration(200)
        )
    }
    true
}, clusterBubbleLayer)
```

::: zone-end

下图显示了上述在地图上显示聚类点的代码，单击地图时，可放大到聚类开始分解和扩展的下一缩放级别。

![单击时放大和分解的聚类特征地图](media/clustering-point-data-android-sdk/android-cluster-expansion.gif)

## <a name="display-cluster-area"></a>显示聚类区域

聚类表示的点数据会分散在某个区域。 在此示例中，当鼠标悬停在聚类上方时，会发生两种主要行为。 首先，该聚类中包含的各个数据点将用于计算凸包。 然后，凸包会显示在地图上以显示一个区域。  凸包是一种多边形，用于包装一组点（如松紧带），可以使用 `atlas.math.getConvexHull` 方法进行计算。 可以使用 `getClusterLeaves` 方法从数据源中检索包含在聚类中的所有点。

::: zone pivot="programming-language-java-android"

``` java
//Create a data source and add it to the map.
DataSource source = new DataSource(
    //Tell the data source to cluster point data.
    cluster(true)
);

//Import the geojson data and add it to the data source.
source.importDataFromUrl("https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson");

//Add data source to the map.
map.sources.add(source);

//Create a data source for the convex hull polygon. Since this will be updated frequently it is more efficient to separate this into its own data source.
DataSource polygonDataSource = new DataSource();

//Add data source to the map.
map.sources.add(polygonDataSource);

//Add a polygon layer and a line layer to display the convex hull.
map.layers.add(new PolygonLayer(polygonDataSource));
map.layers.add(new LineLayer(polygonDataSource));

//Create a symbol layer to render the clusters.
SymbolLayer clusterLayer = new SymbolLayer(source,
    iconImage("marker-red"),
    textField(get("point_count_abbreviated")),
    textOffset(new Float[] { 0f, -1.2f }),
    textColor(Color.WHITE),
    textSize(14f),

    //Only rendered data points which have a point_count property, which clusters do.
    SymbolLayerOptions.filter(has("point_count"))
);
map.layers.add(clusterLayer);

//Create a layer to render the individual locations.
map.layers.add(new SymbolLayer(source,
    //Filter out clustered points from this layer.
    SymbolLayerOptions.filter(not(has("point_count")))
));

//Add a click event to the layer so we can calculate the convex hull of all the points within a cluster.
map.events.add((OnFeatureClick) (features) -> {
    if(features.size() > 0) {
        //Get the clustered point from the event.
        Feature cluster = features.get(0);

        //Get all points in the cluster. Set the offset to 0 and the max long value to return all points.
        FeatureCollection leaves = source.getClusterLeaves(cluster, Long.MAX_VALUE, 0);

        //Get the point features from the feature collection.
        List<Feature> childFeatures = leaves.features();

        //When only two points in a cluster. Render a line.
        if(childFeatures.size() == 2){
            //Extract the geometry points from the child features.
            List<Point> points = new ArrayList();

            childFeatures.forEach(f -> {
                points.add((Point)f.geometry());
            });
            
            //Create a line from the points.
            polygonDataSource.setShapes(LineString.fromLngLats(points));
        } else {
            Polygon hullPolygon = MapMath.getConvexHull(leaves);

            //Overwrite all data in the polygon data source with the newly calculated convex hull polygon.
            polygonDataSource.setShapes(hullPolygon);
        }
    }

    //Return true indicating if event should be consumed and not passed further to other listeners registered afterwards, false otherwise.
    return true;
}, clusterLayer);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource( 
    //Tell the data source to cluster point data.
    cluster(true)
)

//Import the geojson data and add it to the data source.
source.importDataFromUrl("https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson")

//Add data source to the map.
map.sources.add(source)

//Create a data source for the convex hull polygon. Since this will be updated frequently it is more efficient to separate this into its own data source.
val polygonDataSource = DataSource()

//Add data source to the map.
map.sources.add(polygonDataSource)

//Add a polygon layer and a line layer to display the convex hull.
map.layers.add(PolygonLayer(polygonDataSource))
map.layers.add(LineLayer(polygonDataSource))

//Create a symbol layer to render the clusters.
val clusterLayer = SymbolLayer(
    source,
    iconImage("marker-red"),
    textField(get("point_count_abbreviated")),
    textOffset(arrayOf(0f, -1.2f)),
    textColor(Color.WHITE),
    textSize(14f),  

    //Only rendered data points which have a point_count property, which clusters do.
    SymbolLayerOptions.filter(has("point_count"))
)
map.layers.add(clusterLayer)

//Create a layer to render the individual locations.
map.layers.add(
    SymbolLayer(
        source,  

        //Filter out clustered points from this layer.
        SymbolLayerOptions.filter(not(has("point_count")))
    )
)

//Add a click event to the layer so we can calculate the convex hull of all the points within a cluster.
map.events.add(OnFeatureClick { features: List<Feature?>? -> 
    if (features.size() > 0) {
        //Get the clustered point from the event.
        val cluster: Feature = features.get(0)

        //Get all points in the cluster. Set the offset to 0 and the max long value to return all points.
        val leaves: FeatureCollection = source.getClusterLeaves(cluster, Long.MAX_VALUE, 0)

        //Get the point features from the feature collection.
        val childFeatures = leaves.features()

        //When only two points in a cluster. Render a line.
        if (childFeatures!!.size == 2) {
            //Extract the geometry points from the child features.
            val points: MutableList<Point?> = ArrayList()
            childFeatures!!.forEach(Consumer { f: Feature ->
                points.add(
                    f.geometry() as Point?
                )
            })

            //Create a line from the points.
            polygonDataSource.setShapes(LineString.fromLngLats(points))
        } else {
            val hullPolygon: Polygon = MapMath.getConvexHull(leaves)

            //Overwrite all data in the polygon data source with the newly calculated convex hull polygon.
            polygonDataSource.setShapes(hullPolygon)
        }
    }
    true
}, clusterLayer)
```

::: zone-end

下图显示了上述代码，该代码显示单击聚类内所有点的区域。

![该地图显示单击聚类中所有点的凸包多边形](media/clustering-point-data-android-sdk/android-cluster-leaves-convex-hull.gif)

## <a name="aggregating-data-in-clusters"></a>聚合聚类中的数据

通常使用符号以及聚类中的点数来表示聚类。 但有时需要使用其他指标自定义聚类样式。 使用聚类属性，可以创建自定义属性，这相当于基于聚类中每个点的属性进行计算。 可以在 `DataSource` 的 `clusterProperties` 选项中定义聚类属性。

以下代码基于聚类中每个数据点的实体类型属性计算计数。 当用户单击某个聚类时，将显示一个弹出窗口，其中包含有关该聚类的其他信息。

::: zone pivot="programming-language-java-android"

``` java
//An array of all entity type property names in features of the data set.
String[] entityTypes = new String[] { "Gas Station", "Grocery Store", "Restaurant", "School" };

//Create a popup and add it to the map.
Popup popup = new Popup();
map.popups.add(popup);

//Close the popup initially.
popup.close();

//Create a data source and add it to the map.
source = new DataSource(
    //Tell the data source to cluster point data.
    cluster(true),

    //The radius in pixels to cluster points together.
    clusterRadius(50),

    //Calculate counts for each entity type in a cluster as custom aggregate properties.
    clusterProperties(new ClusterProperty[]{
        new ClusterProperty("Gas Station", sum(accumulated(), get("Gas Station")), switchCase(eq(get("EntityType"), literal("Gas Station")), literal(1), literal(0))),
        new ClusterProperty("Grocery Store", sum(accumulated(), get("Grocery Store")), switchCase(eq(get("EntityType"), literal("Grocery Store")), literal(1), literal(0))),
        new ClusterProperty("Restaurant", sum(accumulated(), get("Restaurant")), switchCase(eq(get("EntityType"), literal("Restaurant")), literal(1), literal(0))),
        new ClusterProperty("School", sum(accumulated(), get("School")), switchCase(eq(get("EntityType"), literal("School")), literal(1), literal(0)))
    })
);

//Import the geojson data and add it to the data source.
source.importDataFromUrl("https://azuremapscodesamples.azurewebsites.net/Common/data/geojson/SamplePoiDataSet.json");

//Add data source to the map.
map.sources.add(source);

//Create a bubble layer for rendering clustered data points.
BubbleLayer clusterBubbleLayer = new BubbleLayer(source,
    bubbleRadius(20f),
    bubbleColor("purple"),
    bubbleStrokeWidth(0f),

    //Only rendered data points which have a point_count property, which clusters do.
    BubbleLayerOptions.filter(has("point_count"))
);

//Add the clusterBubbleLayer and two additional layers to the map.
map.layers.add(clusterBubbleLayer);

//Create a symbol layer to render the count of locations in a cluster.
map.layers.add(new SymbolLayer(source,
    //Hide the icon image.
    iconImage("none"),

    //Display the 'point_count_abbreviated' property value.
    textField(get("point_count_abbreviated")),

    textColor(Color.WHITE),
    textOffset(new Float[] { 0f, 0.4f }),

    //Only rendered data points which have a point_count property, which clusters do.
    SymbolLayerOptions.filter(has("point_count"))
));

//Create a layer to render the individual locations.
map.layers.add(new SymbolLayer(source,
    //Filter out clustered points from this layer.
    SymbolLayerOptions.filter(not(has("point_count")))
));

//Add a click event to the cluster layer and display the aggregate details of the cluster.
map.events.add((OnFeatureClick) (features) -> {
    if(features.size() > 0) {
        //Get the clustered point from the event.
        Feature cluster = features.get(0);

        //Create a number formatter that removes decimal places.
        NumberFormat nf = DecimalFormat.getInstance();
        nf.setMaximumFractionDigits(0);

        //Create the popup's content.
        StringBuilder sb = new StringBuilder();

        sb.append("Cluster size: ");
        sb.append(nf.format(cluster.getNumberProperty("point_count")));
        sb.append(" entities\n");

        for(int i = 0; i < entityTypes.length; i++) {
            sb.append("\n");

            //Get the entity type name.
            sb.append(entityTypes[i]);
            sb.append(": ");

            //Get the aggregated entity type count from the properties of the cluster by name.
            sb.append(nf.format(cluster.getNumberProperty(entityTypes[i])));
        }

        //Retrieve the custom layout for the popup.
        View customView = LayoutInflater.from(this).inflate(R.layout.popup_text, null);

        //Access the text view within the custom view and set the text to the title property of the feature.
        TextView tv = customView.findViewById(R.id.message);
        tv.setText(sb.toString());

        //Get the position of the cluster.
        Position pos = MapMath.getPosition((Point)cluster.geometry());

        //Set the options on the popup.
        popup.setOptions(
            //Set the popups position.
            position(pos),

            //Set the anchor point of the popup content.
            anchor(AnchorType.BOTTOM),

            //Set the content of the popup.
            content(customView)
        );

        //Open the popup.
        popup.open();
    }

    //Return a boolean indicating if event should be consumed or continue bubble up.
    return true;
}, clusterBubbleLayer);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//An array of all entity type property names in features of the data set.
val entityTypes = arrayOf("Gas Station", "Grocery Store", "Restaurant", "School")

//Create a popup and add it to the map.
val popup = Popup()
map.popups.add(popup)

//Close the popup initially.
popup.close()

//Create a data source and add it to the map.
val source = DataSource( 
    //Tell the data source to cluster point data.
    cluster(true),  

    //The radius in pixels to cluster points together.
    clusterRadius(50),  

    //Calculate counts for each entity type in a cluster as custom aggregate properties.
    clusterProperties(
        arrayOf<ClusterProperty>(
            ClusterProperty("Gas Station", sum(accumulated(), get("Gas Station")), switchCase(eq(get("EntityType"), literal("Gas Station")), literal(1), literal(0))),
            ClusterProperty("Grocery Store", sum(accumulated(), get("Grocery Store")), switchCase(eq(get("EntityType"), literal("Grocery Store")), literal(1), literal(0))),
            ClusterProperty("Restaurant", sum(accumulated(), get("Restaurant")), switchCase(eq(get("EntityType"), literal("Restaurant")), literal(1), literal(0))),
            ClusterProperty("School", sum(accumulated(), get("School")), switchCase(eq(get("EntityType"), literal("School")), literal(1), literal(0)))
        )
    )
)

//Import the geojson data and add it to the data source.
source.importDataFromUrl("https://azuremapscodesamples.azurewebsites.net/Common/data/geojson/SamplePoiDataSet.json")

//Add data source to the map.
map.sources.add(source)

//Create a bubble layer for rendering clustered data points.
val clusterBubbleLayer = BubbleLayer(
    source,
    bubbleRadius(20f),
    bubbleColor("purple"),
    bubbleStrokeWidth(0f),  

    //Only rendered data points which have a point_count property, which clusters do.
    BubbleLayerOptions.filter(has("point_count"))
)

//Add the clusterBubbleLayer and two additional layers to the map.
map.layers.add(clusterBubbleLayer)

//Create a symbol layer to render the count of locations in a cluster.
map.layers.add(
    SymbolLayer(
        source,  

        //Hide the icon image.
        iconImage("none"),  

        //Display the 'point_count_abbreviated' property value.
        textField(get("point_count_abbreviated")),

        textColor(Color.WHITE),
        textOffset(arrayOf(0f, 0.4f)),  

        //Only rendered data points which have a point_count property, which clusters do.
        SymbolLayerOptions.filter(has("point_count"))
    )
)

//Create a layer to render the individual locations.
map.layers.add(
    SymbolLayer(
        source,  

        //Filter out clustered points from this layer.
        SymbolLayerOptions.filter(not(has("point_count")))
    )
)

//Add a click event to the cluster layer and display the aggregate details of the cluster.
map.events.add(OnFeatureClick { features: List<Feature> ->
    if (features.size > 0) {
        //Get the clustered point from the event.
        val cluster = features[0]

        //Create a number formatter that removes decimal places.
        val nf: NumberFormat = DecimalFormat.getInstance()
        nf.setMaximumFractionDigits(0)

        //Create the popup's content.
        val sb = StringBuilder()

        sb.append("Cluster size: ")
        sb.append(nf.format(cluster.getNumberProperty("point_count")))
        sb.append(" entities\n")

        for (i in entityTypes.indices) {
            sb.append("\n")

            //Get the entity type name.
            sb.append(entityTypes[i])
            sb.append(": ")

            //Get the aggregated entity type count from the properties of the cluster by name.
            sb.append(nf.format(cluster.getNumberProperty(entityTypes[i])))
        }

        //Retrieve the custom layout for the popup.
        val customView: View = LayoutInflater.from(this).inflate(R.layout.popup_text, null)

        //Access the text view within the custom view and set the text to the title property of the feature.
        val tv: TextView = customView.findViewById(R.id.message)
        tv.text = sb.toString()

        //Get the position of the cluster.
        val pos: Position = MapMath.getPosition(cluster.geometry() as Point?)

        //Set the options on the popup.
        popup.setOptions( 
            //Set the popups position.
            position(pos),  

            //Set the anchor point of the popup content.
            anchor(AnchorType.BOTTOM),  

            //Set the content of the popup.
            content(customView)
        )

        //Open the popup.
        popup.open()
    }

    //Return a boolean indicating if event should be consumed or continue bubble up.
    true
} as OnFeatureClick, clusterBubbleLayer)
```

::: zone-end

弹出窗口遵循[显示弹出窗口](display-feature-information-android.md?#display-a-popup)文档中列出的步骤。

下图显示了上述代码，该代码显示一个弹出窗口，其中包含单击聚类点中所有点的每个实体值类型的聚合计数。

![该地图显示一个弹出窗口，其中包含聚类中所有点的实体类型的聚合计数](media/clustering-point-data-android-sdk/android-cluster-aggregates.gif)

## <a name="next-steps"></a>后续步骤

要将更多数据添加到地图，请执行以下操作：

> [!div class="nextstepaction"]
> [创建数据源](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [添加符号层](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [添加气泡层](map-add-bubble-layer-android.md)
