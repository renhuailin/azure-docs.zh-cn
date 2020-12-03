---
title: 使用 Azure Maps 设置地图样式 Android SDK
description: 了解设置地图样式的两种方法。 请参阅如何使用布局文件或活动类中的 Microsoft Azure 映射 Android SDK 来调整样式。
author: anastasia-ms
ms.author: v-stharr
ms.date: 11/18/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 8c7689fb87575ac6e150f793b43f35e8bf6adc83
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96532461"
---
# <a name="set-map-style-using-azure-maps-android-sdk"></a>使用 Azure Maps Android SDK 设置地图样式

本文介绍如何使用 Azure Maps Android SDK 设置地图样式。 Azure Maps 有六个不同的地图样式可供选择。 有关支持的地图样式的详细信息，请参阅 [Azure Maps 中支持的地图样式](./supported-map-styles.md)。

## <a name="prerequisites"></a>先决条件

1. [创建 Azure Maps 帐户](quick-demo-map-app.md#create-an-azure-maps-account)
2. [获取主订阅密钥](quick-demo-map-app.md#get-the-primary-key-for-your-account)（亦称为“主密钥”或“订阅密钥”）。
3. 下载并安装 [Azure Maps Android SDK](./how-to-use-android-map-control-library.md)。


## <a name="set-map-style-in-the-layout"></a>在布局中设置地图样式

您可以在活动类的布局文件中设置地图样式。 编辑，如下所示 `res > layout > activity_main.xml` ：

```XML
<FrameLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    >

    <com.microsoft.azure.maps.mapcontrol.MapControl
        android:id="@+id/mapcontrol"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:mapcontrol_centerLat="47.602806"
        app:mapcontrol_centerLng="-122.329330"
        app:mapcontrol_zoom="12"
        app:mapcontrol_style="grayscale_dark"
        />

</FrameLayout>
```

`mapcontrol_style`上述属性将地图样式设置为 **grayscale_dark**。

:::image type="content" source="./media/set-android-map-styles/grayscale-dark.png" border="true" alt-text="Azure Maps，地图图像显示样式为 grayscale_dark":::

## <a name="set-map-style-in-the-mainactivity-class"></a>在 MainActivity 类中设置地图样式

还可以在 MainActivity 类中设置地图样式。 打开该 `java > com.example.myapplication > MainActivity.java` 文件，并将以下代码片段复制到 **onCreate ( # B1** 方法。 此代码会将地图样式设置为 **satellite_road_labels**。

>[!WARNING]
>Android Studio 可能未导入所需的类。  因此，代码将具有一些无法解析的引用。 若要导入所需的类，只需将鼠标悬停在每个未解析的引用上，然后按 `Alt + Enter` (选项 + 返回 Mac) 。

```Java
mapControl.onReady(map -> {

    //Set the camera of the map.
    map.setCamera(center(47.64, -122.33), zoom(14));

    //Set the style of the map.
    map.setStyle((style(SATELLITE_ROAD_LABELS)));
       
});
```

:::image type="content" source="./media/set-android-map-styles/satellite-road-labels.png" border="true" alt-text="Azure Maps，地图图像显示样式为 satellite_road_labels":::