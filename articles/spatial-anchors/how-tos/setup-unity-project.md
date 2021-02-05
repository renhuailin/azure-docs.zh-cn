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
ms.openlocfilehash: 56532c17c91e6c703a6acd7990bbae47cd248165
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576559"
---
# <a name="configuring-azure-spatial-anchors-in-a-unity-project"></a>在 Unity 项目中配置 Azure 空间锚

本指南将演示如何在 Unity 项目中开始处理 Azure 空间锚点 SDK。

## <a name="project-requirements"></a>项目要求

[!INCLUDE [Unity Project Requirements](../../../includes/spatial-anchors-unity-project-requirements.md)]

## <a name="configuring-a-project"></a>配置项目

在 Unity 项目中包含 Azure 空间锚点 SDK 之前，请务必通过 Unity 包管理器安装 [所需](#project-requirements) 的包。

### <a name="download-asa-packages"></a>下载 ASA 包
[!INCLUDE [Download Unity Packages](../../../includes/spatial-anchors-unity-download-packages.md)]

### <a name="import-asa-packages"></a>导入 ASA 包
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
