---
title: Android 地图控件入门 | Microsoft Azure Maps
description: 熟悉 Azure Maps Android SDK。 了解如何在 Android Studio 中创建项目、安装 SDK 和创建交互式地图。
author: anastasia-ms
ms.author: v-stharr
ms.date: 2/26/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 21442101f1cde2717f079aac550a5e8c587451f2
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123430641"
---
# <a name="getting-started-with-azure-maps-android-sdk"></a>Azure Maps Android SDK 入门

Azure Maps Android SDK 是适用于 Android 的矢量地图库。 本文将引导你完成安装 Azure Maps Android SDK 和加载地图的过程。

## <a name="prerequisites"></a>先决条件

请务必完成[快速入门：创建 Android 应用](quick-android-map.md)文档中的步骤。

## <a name="localizing-the-map"></a>本地化地图

Azure Maps Android SDK 提供三种不同的方式来设置地图的语言和区域视图。 以下代码演示如何将语言设置为法语（“fr-FR”），以及如何将区域视图设置为“Auto”。

第一种做法是全局使用静态的 `setLanguage` 和 `setView` 方法，将语言和视图区域信息传入 `AzureMaps` 类。 此代码会在载入到应用的所有 Azure Maps 控件中设置默认语言和区域视图。

::: zone pivot="programming-language-java-android"

```java
static {
    //Alternatively use Azure Active Directory authenticate.
    AzureMaps.setAadProperties("<Your aad clientId>", "<Your aad AppId>", "<Your aad Tenant>");

    //Set your Azure Maps Key.
    //AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");   

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
        //Alternatively use Azure Active Directory authenticate.
        AzureMaps.setAadProperties("<Your aad clientId>", "<Your aad AppId>", "<Your aad Tenant>");

        //Set your Azure Maps Key.
        //AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");
    
        //Set the language to be used by Azure Maps.
        AzureMaps.setLanguage("fr-FR");
    
        //Set the regional view to be used by Azure Maps.
        AzureMaps.setView("Auto");
    }
}
```

::: zone-end

第二种做法是将语言和视图信息传入地图控件 XML 中。

```XML
<com.azure.android.maps.control.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:azure_maps_language="fr-FR"
    app:azure_maps_view="Auto"
    />
```

第三种做法是使用地图 `setStyle` 方法以编程方式设置地图的语言和区域视图。 随时都可以使用此方法更改地图的语言和区域视图。

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
mapControl.onReady(OnReady { map: AzureMap ->
    map.setStyle(
        language("fr-FR"),
        view("Auto")
    )
})
```

::: zone-end

下面是将语言设置为“fr-FR”、将区域视图设置为“Auto”的 Azure Maps 示例。

![Azure Maps，显示法语标签的地图图像](media/how-to-use-android-map-control-library/android-localization.png)

[此文档](supported-languages.md)提供了支持的语言和区域视图的完整列表。

## <a name="navigating-the-map"></a>在地图中导航

可通过多种不同的方式缩放、平移、旋转地图及调整其俯仰角。 下面详细说明了在地图中导航的所有不同方式。

**缩放地图**

* 使用双指触摸地图，收拢双指可以缩小，分开双指可以放大。
* 双击地图可将地图放大一个级别。
* 用双指双击地图可将地图缩小一个级别。
* 点击两次；在第二次点击时，用手指按住地图并向上拖动可以放大，向下拖动可以缩小。

**平移地图**

* 触摸地图并朝任意方向拖动。

**旋转地图**

* 用双指触摸地图并旋转。

**调整地图俯仰角**

* 用双指触摸地图，并同时向上或向下拖动双指。

## <a name="azure-government-cloud-support"></a>Azure 政府版云支持

Azure Maps Android SDK 支持 Azure 政府版云。 Azure Maps Android SDK 可从同一 Maven 存储库访问。 需要完成以下任务才能连接到 Azure Maps 平台的 Azure 政府版云版本。

在指定 Azure Maps 身份验证详细信息的同一位置，添加以下代码行，指示地图使用 Azure Maps 政府版云域。

::: zone pivot="programming-language-java-android"

```java
AzureMaps.setDomain("atlas.azure.us");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
AzureMaps.setDomain("atlas.azure.us")
```

::: zone-end

对地图和服务进行身份验证时，请确保使用 Azure 政府版云平台中的 Azure Maps 身份验证详细信息。

## <a name="migrating-from-a-preview-version"></a>从预览版迁移

随着从预览版转变为正式发布版，Azure Maps Android SDK 中引入了一些中断性变更。 以下是关键详细信息：

* Maven 标识符已从 `"com.microsoft.azure.maps:mapcontrol:0.7"` 更改为 `"com.azure.android:azure-maps-control:1.0.0"`。 命名空间和主版本号已更改。
* 导入命名空间已从 `com.microsoft.azure.maps.mapcontrol` 更改为 `com.azure.android.maps.control`
* XML 选项、颜色资源和图像资源的资源名称的 `mapcontrol_` 文本已替换为 `azure_maps_`。

    **之前：**

    ```xml
    <com.microsoft.azure.maps.mapcontrol.MapControl
        android:id="@+id/myMap"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:mapcontrol_language="fr-FR"
        app:mapcontrol_view="Auto"
        app:mapcontrol_centerLat="47.602806"
        app:mapcontrol_centerLng="-122.329330"
        app:mapcontrol_zoom="12"
    />
    ```

    **之后：**

    ```xml
    <com.azure.android.maps.control.MapControl
        android:id="@+id/myMap"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:azure_maps_language="fr-FR"
        app:azure_maps_view="Auto"
        app:azure_maps_centerLat="47.602806"
        app:azure_maps_centerLng="-122.329330"
        app:azure_maps_zoom="12"
    />
    ```

## <a name="next-steps"></a>后续步骤

了解如何在地图上添加叠加数据：

> [!div class="nextstepaction"]
> [在 Azure Maps 中管理身份验证](how-to-manage-authentication.md)

> [!div class="nextstepaction"]
> [更改 Android 地图中的地图样式](set-android-map-styles.md)

> [!div class="nextstepaction"]
> [添加符号层](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [添加线条层](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [添加多边形层](how-to-add-shapes-to-android-map.md)
