---
title: 安装适用于 Unity 的 Azure 空间定位点
description: 配置 Unity 项目以使用 Azure 空间定位点
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 03/30/2021
ms.topic: how-to
ms.service: azure-spatial-anchors
ms.openlocfilehash: fe9160f22754c62888b2a61ce9751f596842604e
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2021
ms.locfileid: "106076680"
---
# <a name="configuring-azure-spatial-anchors-in-a-unity-project"></a>在 Unity 项目中配置 Azure 空间定位点

本指南介绍如何在 Unity 项目中开始使用 Azure 空间定位点 SDK。

## <a name="project-requirements"></a>项目要求

[!INCLUDE [Unity Project Requirements](../../../includes/spatial-anchors-unity-project-requirements.md)]

## <a name="configuring-a-project"></a>配置项目

在将 Azure 空间定位点 SDK 添加到 Unity 项目之前，请确保通过 Unity 包管理器安装[所需](#project-requirements)的包。

### <a name="download-asa-packages"></a>下载 ASA 包
[!INCLUDE [Download Unity Packages](../../../includes/spatial-anchors-unity-download-packages.md)]

### <a name="import-asa-packages"></a>导入 ASA 包
[!INCLUDE [Import Unity Packages](../../../includes/spatial-anchors-unity-import-packages.md)]

### <a name="android-only-configure-the-maintemplategradle-file"></a>仅限 Android：配置 mainTemplate.gradle 文件

1. 转到“编辑” > “项目设置” > “播放器”  。
2. 在“播放器设置”的“检查器面板”中，选择“Android”图标  。
3. 在“生成”部分下，选中“自定义主 Gradle 模板”复选框，以在 `Assets\Plugins\Android\mainTemplate.gradle` 处生成自定义 Gradle 模板。
4. 在文本编辑器中打开 `mainTemplate.gradle` 文件。
5. 在 `dependencies` 部分中，粘贴以下依赖关系：

    ```gradle
    implementation('com.squareup.okhttp3:okhttp:[3.11.0]')
    implementation('com.microsoft.appcenter:appcenter-analytics:[1.10.0]')
    ```

完成所有操作后，`dependencies` 部分应如下所示：

[!code-gradle[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-unity-android-gradle-setup.md?range=9-13&highlight=3-4)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [操作说明：在 Unity 中创建并找到定位点](./create-locate-anchors-unity.md)
