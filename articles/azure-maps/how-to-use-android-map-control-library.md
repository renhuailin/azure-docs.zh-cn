---
title: Azure Maps Android SDK 入门
description: 熟悉 Android SDK Microsoft Azure 映射。 请参阅如何在 Android Studio 中创建项目、安装 SDK 和创建交互式地图。
author: anastasia-ms
ms.author: v-stharr
ms.date: 11/18/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 1da003bb8d285dbedde87cbc6cd4708fda2dc38b
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96531238"
---
# <a name="getting-started-with-azure-maps-android-sdk"></a>Azure Maps Android SDK 入门

Azure Maps Android SDK 是适用于 Android 的矢量地图库。 本文将引导你完成安装 Azure Maps Android SDK 和加载地图的过程。

## <a name="prerequisites"></a>先决条件

### <a name="create-an-azure-maps-account"></a>创建 Azure Maps 帐户

1. [创建 Azure Maps 帐户](quick-demo-map-app.md#create-an-azure-maps-account)
2. [获取主订阅密钥](quick-demo-map-app.md#get-the-primary-key-for-your-account)（亦称为“主密钥”或“订阅密钥”）。
有关 Azure Maps 中身份验证的详细信息，请参阅[在 Azure Maps 中管理身份验证](./how-to-manage-authentication.md)。
3. [下载并安装 Google 的 Android Studio](https://developer.android.com/studio/)。

## <a name="create-a-project-in-android-studio"></a>在 Android Studio 中创建项目

请完成以下步骤创建 Android Studio 项目：

1. 启动 Android Studio。
2. 单击 " **+ 创建新项目**"。
3. 在 " **手机和平板电脑** " 选项卡上，单击 " **空活动**"。 单击“下一步”  。
4. 在“配置项目”下，选择 `API 21: Android 5.0.0 (Lollipop)` 作为最低要求的 SDK。
5. 选择 `Java` 作为语言。
6. 接受项目的默认值 `Name` 。 单击“完成”。

安装 Android Studio 和创建新项目时如需更多帮助，请参阅 [Android Studio 文档](https://developer.android.com/studio/intro/)。

![在 Android Studio 中创建项目 ](./media/how-to-use-android-map-control-library/form-factor-android.png)

## <a name="set-up-a-device"></a>设置设备

若要在开发过程中测试应用程序，可以使用 Android 手机或 Android 模拟器。

若要详细了解如何设置 AVD (Android 虚拟设备) ，请参阅 [Android Studio 文档](https://developer.android.com/studio/run/managing-avds)。

## <a name="install-the-azure-maps-android-sdk"></a>安装 Azure Maps Android SDK

生成应用程序的下一步是安装 Azure Maps Android SDK。

请完成以下步骤来安装该 SDK：

1. 在 "项目" 选项卡中，展开 " **Gradle 脚本**"。 打开 **gradle (项目： My_Application)**，并将以下代码添加到 " **所有项目**" `repositories`  部分：

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. 打开 **gradle (Module： My_Application)**。

3. 请确保此部分中的 **minSdkVersion** `defaultConfig` 为 API 21 或更高版本。

4. 将以下代码添加到 Android 节：

    ```
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
    ```

5. 将以下代码添加到 `dependencies` 部分：

    ```
    implementation "com.microsoft.azure.maps:mapcontrol:0.6"
    ```

6. 在主工具栏上单击 " **文件** "，然后选择 "将 **项目与 Gradle 文件同步**"。

7. 打开 `res > layout > activity_main.xml`。 单击 `Code` 右上角的 "查看"。 在元素内添加以下 XML `<androidx.constraintlayout.widget.ConstraintLayout>` 。
    
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
            />
    </FrameLayout>
    ```

8. 在该 `java > com.example.myapplication > MainActivity.java` 文件中，你将需要：

    * 添加 Azure Maps SDK 的导入。
    * 设置 Azure Maps 身份验证信息。
    * 获取 **onCreate** 方法中的 map 控件实例。

    若要避免为每个应用程序视图添加身份验证信息，我们将通过调用来全局设置身份验证信息 `AzureMaps.setSubscriptionKey` 。 `AzureMaps.setAadProperties`如果要使用 Azure Active Directory 进行身份验证，则还可以调用。

    地图控件重写 MainActivity 类的以下生命周期方法。 这些方法负责管理 Android 的 OpenGL 生命周期。

    * onCreate(Bundle)
    * onStart()
    * onResume()
    * onPause()
    * onStop()
    * onDestroy()
    * onSaveInstanceState(Bundle)
    * onLowMemory()

    `MainActivity.java`按如下所示编辑文件：

    ```java
    package com.example.myapplication;

    //For older versions use: import android.support.v7.app.AppCompatActivity; 
    import androidx.appcompat.app.AppCompatActivity;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import com.microsoft.azure.maps.mapcontrol.layer.SymbolLayer;
    import com.microsoft.azure.maps.mapcontrol.options.MapStyle;
    import com.microsoft.azure.maps.mapcontrol.source.DataSource;

    public class MainActivity extends AppCompatActivity {

        static {
            AzureMaps.setSubscriptionKey("<Your Azure Maps subscription key>");
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
        }
    }
    ```

>[!WARNING]
>Android Studio 可能未导入所需的类。  因此，代码将具有一些无法解析的引用。 若要导入所需的类，只需将鼠标悬停在每个未解析的引用上，然后按 `Alt + Enter` (选项 + 返回 Mac) 。

Android Studio 将花费几秒钟时间来生成应用程序。 生成完成后，可在 Android 仿真设备中测试应用程序。 应会看到如下所示的地图：

:::image type="content" source="./media/how-to-use-android-map-control-library/android-map.png" border="true" alt-text="Android 应用程序中的 Azure Maps":::

## <a name="localizing-the-map"></a>本地化地图

Azure Maps Android SDK 提供了三种不同的方式来设置地图的语言和区域设置。

1. 通过对 AzureMaps 类调用静态方法来设置语言和区域设置。

    ```Java
    static {
        //Set your Azure Maps Key.
        AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");

        //Set the language to be used by Azure Maps.
        AzureMaps.setLanguage("fr-FR");

        //Set the regional view.
        AzureMaps.setLanguage("Auto");
    
    }
    ```

2. 定义地图控件 XML 中的语言和区域设置。

    ```XML
    <com.microsoft.azure.maps.mapcontrol.MapControl
        android:id="@+id/myMap"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:mapcontrol_language="fr-FR"
        app:mapcontrol_view="Auto"
        />
    ```

3. 通过在地图控件上调用方法来设置语言和区域设置。 此选项允许您在运行时更改设置。

    ```Java
    mapControl.onReady(map -> {
        map.setStyle(StyleOptions.language("fr-FR"));
        map.setStyle(StyleOptions.view("Auto"));
    
    });
    ```

下面是将语言设置为的 Azure Maps 的示例 `fr-FR` 。

:::image type="content" source="./media/how-to-use-android-map-control-library/android-localization.png" border="true" alt-text="Azure Maps，显示法语标签的地图图像":::

[此文档](supported-languages.md)提供了支持的语言和区域视图的完整列表。

## <a name="navigating-the-map"></a>在地图中导航

可通过多种不同的方式缩放、平移、旋转地图及调整其俯仰角。 下面详细说明了在地图中导航的所有不同方式。

**缩放地图**

- 使用双指触摸地图，收拢双指可以缩小，分开双指可以放大。
- 双击地图可将地图放大一个级别。
- 用两根手指双击可缩小地图一级。
- 点击两次；在第二次点击时，用手指按住地图并向上拖动可以放大，向下拖动可以缩小。

**平移地图**

- 触摸地图并朝任意方向拖动。

**旋转地图**

- 用双指触摸地图并旋转。

**调整地图俯仰角**

- 用双指触摸地图，并同时向上或向下拖动双指。

## <a name="next-steps"></a>后续步骤

了解如何在地图上添加叠加数据：

> [!div class="nextstepaction"]
> [在 Android 地图中添加符号层](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [在 Android 地图中添加形状](./how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [更改 Android 地图中的地图样式](./set-android-map-styles.md)