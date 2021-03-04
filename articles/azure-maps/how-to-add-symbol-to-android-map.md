---
title: 向 Android maps 添加符号层 |Microsoft Azure 映射
description: 了解如何向地图添加标记。 查看一个示例，该示例使用 Azure Maps Android SDK 添加一个符号层，其中包含来自数据源的基于点的数据。
author: rbrundritt
ms.author: richbrun
ms.date: 12/08/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 1706b60a61bd3b507d9fbcf555e478b388f51168
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102047564"
---
# <a name="add-a-symbol-layer-android-sdk"></a>Android SDK 添加符号层 () 

本文说明如何使用 Azure Maps Android SDK 将数据源中的点数据呈现为地图上的符号层。 符号层将点呈现为地图上的图像和文本。

> [!TIP]
> 默认情况下，符号层将呈现数据源中所有几何图形的坐标。 若要将该层限制为仅呈现点几何特征，请将 `filter` 层的选项设置为 `eq(geometryType(), "Point")` 。 如果还想要包含 MultiPoint 功能，请将层的 `filter` 选项设置为 `any(eq(geometryType(), "Point"), eq(geometryType(), "MultiPoint"))` 。

## <a name="prerequisites"></a>先决条件

请确保完成 [快速入门：创建 Android 应用](quick-android-map.md) 文档中的步骤。 本文中的代码块可以插入到 maps `onReady` 事件处理程序中。

## <a name="add-a-symbol-layer"></a>添加符号层

你需要先执行几个步骤，然后才能将符号层添加到地图中。 首先，创建一个数据源，并将其添加到地图中。 创建符号层。 然后，将数据源传递到符号层，以从数据源中检索数据。 最后，将数据添加到数据源，以便呈现一些内容。

下面的代码演示了加载后应添加到映射的内容。 此示例使用符号层在地图上呈现一个点。

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a point and add it to the data source.
source.add(Point.fromLngLat(0, 0));

//Create a symbol layer to render icons and/or text at points on the map.
SymbolLayer layer = new SymbolLayer(source);

//Add the layer to the map.
map.layers.add(layer);
```

有三种不同类型的点数据可以添加到地图中：

- GeoJSON 点几何-此对象只包含一个点的坐标，而不包含其他任何内容。 `Point.fromLngLat`静态方法可用于轻松创建这些对象。
- GeoJSON MultiPoint geometry-此对象包含多个点的坐标，而不包含其他任何点。 将一个点数组传递到 `MultiPoint` 类，以创建这些对象。
- GeoJSON 功能-此对象包含任何 GeoJSON 几何和一组属性，其中包含与几何图形关联的元数据。

有关详细信息，请参阅创建 [数据源](create-data-source-android-sdk.md) 文档，并在地图上创建和添加数据。

下面的代码示例将创建一个 GeoJSON 点几何图形，并将其传递给 GeoJSON 功能，并将一个 `title` 值添加到其属性中。 此 `title` 属性显示为地图上符号图标上方的文本。

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(0, 0));

//Add a property to the feature.
feature.addStringProperty("title", "Hello World!");

//Add the feature to the data source.
source.add(feature);

//Create a symbol layer to render icons and/or text at points on the map.
SymbolLayer layer = new SymbolLayer(source, 
    //Get the title property of the feature and display it on the map.
    textField(get("title"))
);

//Add the layer to the map.
map.layers.add(layer);
```

以下屏幕截图显示了上面的代码呈现使用带有符号层的图标和文本标签的点功能。

![使用符号层呈现的使用符号层的地图，其中显示了点功能的图标和文本标签](media/how-to-add-symbol-to-android-map/android-map-pin.png)

> [!TIP]
> 默认情况下，符号层通过隐藏重叠的符号来优化符号的呈现。 放大时，隐藏的符号将变为可见。 若要禁用此功能并始终呈现所有符号，请将 `iconAllowOverlap` 和 `textAllowOverlap` 选项设置为 `true` 。

## <a name="add-a-custom-icon-to-a-symbol-layer"></a>将自定义图标添加到符号层

符号层是使用 WebGL 呈现的。 因此，所有资源（例如图标图像）必须载入 WebGL 上下文。 此示例演示如何将自定义图标添加到地图资源。 然后，此图标用于使用地图上的自定义符号呈现点数据。 符号层的 `textField` 属性要求指定一个表达式。 在这种情况下，我们想要呈现温度属性。 由于温度为数字，因此需要将其转换为字符串。 此外，我们还需要在其中追加 "° F"。 表达式可用于执行此串联; `concat(Expression.toString(get("temperature")), literal("°F"))`.

```java
//Load a custom icon image into the image sprite of the map.
map.images.add("my-custom-icon", R.drawable.showers);

//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(-73.985708, 40.75773));

//Add a property to the feature.
feature.addNumberProperty("temperature", 64);

//Add the feature to the data source.
source.add(feature);

//Create a symbol layer to render icons and/or text at points on the map.
SymbolLayer layer = new SymbolLayer(source,
    iconImage("my-custom-icon"),
    iconSize(0.5f),

    //Get the title property of the feature and display it on the map.
    textField(concat(Expression.toString(get("temperature")), literal("°F"))),
    textOffset(new Float[]{0f, -1.5f})
);
```

对于本示例，以下图像已加载到应用程序的 "图形" 文件夹中。

| ![Rain 淋浴的天气图标图像](media/how-to-add-symbol-to-android-map/showers.png)|
|:-----------------------------------------------------------------------:|
| showers.png                                                  |

以下屏幕截图显示了上面的代码呈现点功能，其中使用自定义图标和带符号层的带格式文本标签。

![使用符号层绘制的点映射，其中显示自定义图标和点功能的格式化文本标签](media/how-to-add-symbol-to-android-map/android-custom-symbol-layer.png)

> [!TIP]
> 如果只希望使用符号层呈现文本，则可以通过将 `iconImage` 图标选项的属性设置为来隐藏该图标 `"none"` 。

## <a name="modify-symbol-colors"></a>修改符号颜色

Azure Maps Android SDK 附带了一组默认标记图标的预定义颜色变体。 例如， `marker-red` 可以将传递到 `iconImage` 符号层的选项，以呈现该层中标记图标的红色版本。 

```java
SymbolLayer layer = new SymbolLayer(source,
    iconImage("marker-red")
);
```

下表列出了所有可用的内置图标映像名称。 所有这些标记均从可以重写的颜色资源中提取其颜色。 除了覆盖此标记的主要填充颜色。 但请注意，重写其中一个标记的颜色将应用于所有使用该图标图像的层。

| 图标图像名称 | 颜色资源名称 |
|-----------------|---------------------|
| `marker-default` | `mapcontrol_marker_default` |
| `marker-black` | `mapcontrol_marker_black` |
| `marker-blue` | `mapcontrol_marker_blue` |
| `marker-darkblue` | `mapcontrol_marker_darkblue` |
| `marker-red` | `mapcontrol_marker_red` |
| `marker-yellow` | `mapcontrol_marker_yellow` |

还可以使用颜色资源名称替代所有标记的边框颜色 `mapcontrol_marker_border` 。 可以通过在应用的文件中添加具有相同名称的颜色来重写这些标记的颜色 `colors.xml` 。 例如，以下 `colors.xml` 文件会使默认标记颜色为浅绿色。

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <color name="mapcontrol_marker_default">#00FF00</color>
</resources>
```

下面是默认标记矢量 XML 的修改版本，你可以修改该版本以创建默认标记的其他自定义版本。 可以将修改后的版本添加到 `drawable` 应用的文件夹，并使用将其添加到地图图像 sprite `map.images.add` ，然后将其与符号层一起使用。

```xml
<vector xmlns:android="http://schemas.android.com/apk/res/android"
    android:width="24.5dp"
    android:height="36.5dp"
    android:viewportWidth="24.5"
    android:viewportHeight="36.5">
    <path
        android:pathData="M12.25,0.25a12.2543,12.2543 0,0 0,-12 12.4937c0,6.4436 6.4879,12.1093 11.059,22.5641 0.5493,1.2563 1.3327,1.2563 1.882,0C17.7621,24.8529 24.25,19.1857 24.25,12.7437A12.2543,12.2543 0,0 0,12.25 0.25Z"
        android:strokeWidth="0.5"
        android:fillColor="@color/mapcontrol_marker_default"
        android:strokeColor="@color/mapcontrol_marker_border"/>
</vector>
```

## <a name="next-steps"></a>后续步骤

有关可向地图添加的更多代码示例，请参阅以下文章：

> [!div class="nextstepaction"]
> [创建数据源](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [添加气泡层](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [使用数据驱动样式表达式](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [显示功能信息](display-feature-information-android.md)
