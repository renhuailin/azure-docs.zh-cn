---
title: 在 Android 地图上显示功能信息 | Microsoft Azure Maps
description: 了解如何在用户与地图功能交互时显示信息。 使用 Azure Maps Android SDK 显示 toast 消息和其他类型的消息。
author: anastasia-ms
ms.author: v-stharr
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: a8bd232ccba2adb3666ee7fd54983be2ed1f7296
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123426429"
---
# <a name="display-feature-information"></a>显示功能信息

空间数据通常使用点、线条和多边形来表示。 这种数据通常具有与之关联的元数据信息。 例如，点可能代表餐馆的位置，而有关该餐馆的元数据可能是餐馆的名称、地址和提供的美食种类。 可作为 GeoJSON 的属性 `Feature` 添加这一元数据。 下面的代码创建一个简单的点要素，其 `title` 属性的值为“Hello World!”

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64));

//Add a property to the feature.
feature.addStringProperty("title", "Hello World!");

//Create a point feature, pass in the metadata properties, and add it to the data source.
source.add(feature);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create a point feature.
val feature = Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64))

//Add a property to the feature.
feature.addStringProperty("title", "Hello World!")

//Create a point feature, pass in the metadata properties, and add it to the data source.
source.add(feature)
```

::: zone-end

有关创建数据并将其添加到地图的方法，请参见[创建数据源](create-data-source-android-sdk.md)文档。

当用户与地图上的功能交互时，可以使用事件来回应这些操作。 常见的情况是显示一条消息，其中包含用户与之交互的功能的元数据属性。 `OnFeatureClick` 事件是用于检测用户在地图上点击功能时所使用的主要事件。 还有一个是 `OnLongFeatureClick` 事件。 向地图中添加 `OnFeatureClick` 事件时，可以将该事件限制为单个层，方法是传入要将事件限制为的层的 ID。 如果未传入任何层 ID，则在地图上点击任意功能都将触发此事件，无论功能位于哪个层。 下面的代码创建一个符号层，用于在地图上呈现点数据，然后添加一个 `OnFeatureClick` 事件并将该事件限制为此符号层。

::: zone pivot="programming-language-java-android"

```java
//Create a symbol and add it to the map.
SymbolLayer layer = new SymbolLayer(source);
map.layers.add(layer);

//Add a feature click event to the map.
map.events.add((OnFeatureClick) (features) -> {
    //Retrieve the title property of the feature as a string.
    String msg = features.get(0).getStringProperty("title");

    //Do something with the message.

    //Return a boolean indicating if event should be consumed or continue bubble up.
    return false;
}, layer.getId());    //Limit this event to the symbol layer.
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a symbol and add it to the map.
val layer = SymbolLayer(source)
map.layers.add(layer)

//Add a feature click event to the map.
map.events.add(OnFeatureClick { features: List<Feature> ->
    //Retrieve the title property of the feature as a string.
    val msg = features[0].getStringProperty("title")

    //Do something with the message.

    //Return a boolean indicating if event should be consumed or continue bubble up.
    return false
}, layer.getId()) //Limit this event to the symbol layer.
```

::: zone-end

## <a name="display-a-toast-message"></a>显示 toast 消息

Toast 消息是向用户显示信息的最简单方法之一，在 Android 的所有版本中都提供。 Toast 消息不支持任何类型的用户输入，只是显示片刻。 如果你想要快速让用户了解他们点击的内容，则 toast 消息可能是一个不错的选择。 下面的代码演示如何在事件中使用 toast 消息 `OnFeatureClick`。

::: zone pivot="programming-language-java-android"

```java
//Add a feature click event to the map.
map.events.add((OnFeatureClick) (features) -> {
    //Retrieve the title property of the feature as a string.
    String msg = features.get(0).getStringProperty("title");

    //Display a toast message with the title information.
    Toast.makeText(this, msg, Toast.LENGTH_SHORT).show();

    //Return a boolean indicating if event should be consumed or continue bubble up.
    return false;
}, layer.getId());    //Limit this event to the symbol layer.
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Add a feature click event to the map.
map.events.add(OnFeatureClick { features: List<Feature> ->
    //Retrieve the title property of the feature as a string.
    val msg = features[0].getStringProperty("title")

    //Display a toast message with the title information.
    Toast.makeText(this, msg, Toast.LENGTH_SHORT).show()

    //Return a boolean indicating if event should be consumed or continue bubble up.
    return false
}, layer.getId()) //Limit this event to the symbol layer.
```

::: zone-end

![正在点击的功能的动画和正在显示的 toast 消息](media/display-feature-information-android/symbol-layer-click-toast-message.gif)

除了 toast 消息以外，还有很多其他方法可以呈现功能的元数据属性，例如：

- [Snackbar](https://developer.android.com/training/snackbar/showing.html) - `Snackbars` 小组件提供有关操作的轻量级反馈。 这些小组件会在移动设备的底部显示简短消息，在较大的设备上则显示在左下方。 `Snackbars` 显示在屏幕上的所有其他元素之上，一次只能显示一个。
- [对话框](https://developer.android.com/guide/topics/ui/dialogs) - 对话框是一个小窗口，它会提示用户做出决定或输入其他信息。 对话框并不填充屏幕，通常用于模式事件，这些事件要求用户先执行某个操作，然后才能继续。
- 向当前活动添加一个[片段](https://developer.android.com/guide/components/fragments)。
- 导航到另一个活动或视图。

## <a name="display-a-popup"></a>显示弹出窗口

Azure Maps Android SDK 提供了一个 `Popup` 类，使用户可以轻松创建定位到地图上某个位置的 UI 注释元素。 对于弹出窗口，必须将具有相对布局的视图传入弹出窗口的 `content` 选项。 下面是一个简单的布局示例，用于在白色背景上显示深色文本。

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:orientation="vertical"
    android:background="#ffffff"
    android:layout_margin="8dp"
    android:padding="10dp"

    android:layout_height="match_parent">

    <TextView
        android:id="@+id/message"
        android:layout_width="wrap_content"
        android:text=""
        android:textSize="18dp"
        android:textColor="#222"
        android:layout_height="wrap_content"
        android:width="200dp"/>

</RelativeLayout>
```

假设以上布局存储在应用的 `res -> layout` 文件夹中名为 `popup_text.xml` 的文件中，以下代码将创建一个弹出窗口，并将弹出窗口添加到地图中。 单击某个功能时，将使用 `popup_text.xml` 布局显示 `title` 属性，并且布局的底部中心定位在地图上的指定位置。

::: zone pivot="programming-language-java-android"

```java
//Create a popup and add it to the map.
Popup popup = new Popup();
map.popups.add(popup);

map.events.add((OnFeatureClick)(feature) -> {
    //Get the first feature and it's properties.
    Feature f = feature.get(0);
    JsonObject props = f.properties();

    //Retrieve the custom layout for the popup.
    View customView = LayoutInflater.from(this).inflate(R.layout.popup_text, null);

    //Access the text view within the custom view and set the text to the title property of the feature.
    TextView tv = customView.findViewById(R.id.message);
    tv.setText(props.get("title").getAsString());

    //Get the position of the clicked feature.
    Position pos = MapMath.getPosition((Point)cluster.geometry());

    //Set the options on the popup.
    popup.setOptions(
        //Set the popups position.
        position(pos),

        //Set the anchor point of the popup content.
        anchor(AnchorType.BOTTOM),

        //Set the content of the popup.
        content(customView)

        //Optionally, hide the close button of the popup.
        //, closeButton(false)
            
        //Optionally offset the popup by a specified number of pixels.
        //pixelOffset(new Pixel(10, 10))
    );

    //Open the popup.
    popup.open();

    //Return a boolean indicating if event should be consumed or continue bubble up.
    return false;
});
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a popup and add it to the map.
val popup = Popup()
map.popups.add(popup)

map.events.add(OnFeatureClick { feature: List<Feature> ->
    //Get the first feature and it's properties.
    val f = feature[0]
    val props = f.properties()

    //Retrieve the custom layout for the popup.
    val customView: View = LayoutInflater.from(this).inflate(R.layout.popup_text, null)

    //Access the text view within the custom view and set the text to the title property of the feature.
    val tv: TextView = customView.findViewById(R.id.message)
    tv.text = props!!["title"].asString

    //Get the position of the clicked feature.
    val pos = MapMath.getPosition(f.geometry() as Point?);

    //Set the options on the popup.
    popup.setOptions( 
        //Set the popups position.
        position(pos),  

        //Set the anchor point of the popup content.
        anchor(AnchorType.BOTTOM),  

        //Set the content of the popup.
        content(customView) 

        //Optionally, hide the close button of the popup.
        //, closeButton(false)
            
        //Optionally offset the popup by a specified number of pixels.
        //pixelOffset(Pixel(10, 10))
    )

    //Open the popup.
    popup.open()

    //Return a boolean indicating if event should be consumed or continue bubble up.
    false
})
```

::: zone-end

以下屏幕截图显示了单击功能时出现的弹出窗口，并在地图移动时固定在其在地图上的指定位置。

![动画演示，显示了弹出窗口，并且地图移动，而弹出窗口固定到地图上某个位置](./media/display-feature-information-android/android-popup.gif)

## <a name="next-steps"></a>后续步骤

要将更多数据添加到地图，请执行以下操作：

> [!div class="nextstepaction"]
> [响应地图事件](android-map-events.md)

> [!div class="nextstepaction"]
> [创建数据源](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [添加符号层](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [添加气泡层](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [添加线条层](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [添加多边形层](how-to-add-shapes-to-android-map.md)
