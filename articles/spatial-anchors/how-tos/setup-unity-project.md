---
title: 安装适用于 Unity 的 Azure 空间锚
description: 配置 Unity 项目以使用 Azure 空间锚。
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 2/3/2021
ms.topic: how-to
ms.service: azure-spatial-anchors
ms.openlocfilehash: e058186d8848256bf97d99ee1b8b1ddae7d78383
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550617"
---
# <a name="configuring-azure-spatial-anchors-in-a-unity-project"></a>在 Unity 项目中配置 Azure 空间锚

本指南将演示如何在 Unity 项目中开始处理 Azure 空间锚点 SDK。

## <a name="requirements"></a>要求

Azure 空间定位点当前支持具有以下配置的 Unity 2019.4 (LTS) 。

* Azure 空间锚 2.4.0 + 支持 Unity 2019.4 with AR Foundation 3.1。

## <a name="configuring-a-project"></a>配置项目

### <a name="download-packages"></a>下载包
[!INCLUDE [Download Unity Packages](../../../includes/spatial-anchors-unity-download-packages.md)]

### <a name="import-packages"></a>导入程序包
[!INCLUDE [Import Unity Packages](../../../includes/spatial-anchors-unity-import-packages.md)]

### <a name="android-only-configure-the-maintemplategradle-file"></a>仅限 Android：配置 Maintemplate.json. gradle 文件

1. 转到“编辑” > “项目设置” > “播放器”  。
2. 在 "**播放器设置**" 的 **检查器面板** 中，选择 " **Android** " 图标。
3. 在 " **生成** " 部分下，选中 " **自定义主 Gradle 模板** " 复选框，以在处生成自定义 Gradle 模板 `Assets\Plugins\Android\mainTemplate.gradle` 。
4. 在文本编辑器中打开 `mainTemplate.gradle` 文件。
5. 在 `dependencies` 部分中，粘贴以下依赖项：

    ```gradle
    implementation('com.squareup.okhttp3:okhttp:[3.11.0]')
    implementation('com.microsoft.appcenter:appcenter-analytics:[1.10.0]')
    ```

完成所有操作后， `dependencies` 部分应如下所示：

[!code-gradle[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-unity-android-gradle-setup.md?range=9-13&highlight=3-4)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [如何：创建和定位 Unity 中的定位点](./create-locate-anchors-unity.md)
