---
title: 快速入门：使用 Unity 和 MRTK 创建 HoloLens 应用
description: 此快速入门介绍如何使用 MRTK 和 Object Anchors 生成 HoloLens Unity 应用。
author: craigktreasure
manager: virivera
services: azure-object-anchors
ms.author: crtreasu
ms.date: 08/02/2021
ms.topic: quickstart
ms.service: azure-object-anchors
ms.openlocfilehash: b047c293a14dcfde5c8de2f56fb5c2abeab61c69
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122254302"
---
# <a name="quickstart-create-a-hololens-app-with-azure-object-anchors-in-unity-with-mrtk"></a>快速入门：通过 MRTK 在 Unity 中创建具有 Azure Object Anchors 的 HoloLens 应用

此快速入门介绍如何创建使用 [Azure Object Anchors](../overview.md) 的 Unity HoloLens 应用。 Azure Object Anchors 是一项托管的云服务，它将 3D 资产转换为 AI 模型，以便为 HoloLens 启用对象感知混合现实体验。 完成本教程后，你将拥有一个使用 Unity 生成的 HoloLens 应用，该应用可检测到物理世界中的物体。

将了解如何执行以下操作：

> [!div class="checklist"]
> * 准备 Unity 生成设置。
> * 导出 HoloLens Visual Studio 项目。
> * 在 HoloLens 2 设备上部署并运行应用。

[!INCLUDE [Unity quickstart prerequisites](../../../includes/object-anchors-quickstart-unity-prerequisites.md)]

[!INCLUDE [Create Account](../../../includes/object-anchors-get-started-create-account.md)]

[!INCLUDE [Unity device setup](../../../includes/object-anchors-quickstart-unity-device-setup.md)]

[!INCLUDE [Unity upload your model](../../../includes/object-anchors-quickstart-unity-upload-model.md)]

## <a name="open-the-sample-project"></a>打开示例项目

[!INCLUDE [Clone Sample Repo](../../../includes/object-anchors-clone-sample-repository.md)]

[!INCLUDE [Download Unity Package](../../../includes/object-anchors-quickstart-unity-download-package.md)]

在 Unity 中，打开 `quickstarts/apps/unity/mrtk` 项目。

[!INCLUDE [Import Unity Package](../../../includes/object-anchors-quickstart-unity-import-package.md)]

[!INCLUDE [Configure Account](../../../includes/object-anchors-get-started-configure-account.md)]

[!INCLUDE [Unity build sample scene 1](../../../includes/object-anchors-quickstart-unity-build-sample-scene-1.md)]

当“TMP 导入程序”对话框提示导入 TextMesh Pro 资源时，请选择“导入 TMP Essentials”以执行此操作。
:::image type="content" source="./media/textmesh-pro-importer-dialog.png" alt-text="导入 TextMesh Pro 资源":::

[!INCLUDE [Unity build sample scene 2](../../../includes/object-anchors-quickstart-unity-build-sample-scene-2.md)]

[!INCLUDE [Unity build and deploy](../../../includes/object-anchors-quickstart-unity-build-deploy.md)]

 在 Unity 初始屏幕后，应该会显示一个白色的边框。 你可以用手移动、缩放或旋转该边框。 放置边框以包围要检测的物体。

打开<a href="https://microsoft.github.io/MixedRealityToolkit-Unity/Documentation/README_HandMenu.html" target="_blank">手动菜单</a>，选择“锁定 SearchArea”以防止边框进一步移动。 选择“开始搜索”以启动物体检测。 检测到物体时，将在物体上呈现一个网格。 屏幕上将显示检测到的实例的详细信息，例如更新的时间戳和表面覆盖率。 选择“停止搜索”停止跟踪，将删除检测到的所有实例。

#### <a name="the-app-menus"></a>应用菜单

还可以使用<a href="https://microsoft.github.io/MixedRealityToolkit-Unity/Documentation/README_HandMenu.html" target="_blank">手动菜单</a>执行其他操作。

##### <a name="primary-menu"></a>主菜单

* **开始搜索/停止搜索** – 开始或停止物体检测过程。
* **切换空间映射** – 显示/隐藏空间映射呈现。 此选项可用于调试扫描是否完成。
* **跟踪器设置** – 切换跟踪器设置菜单的激活状态。
* **搜索区域设置** – 切换搜索区域设置菜单的激活状态。
* **开始跟踪** – 捕获诊断数据并将其保存到设备。 有关更多详细信息，请参阅“调试检测问题和捕获诊断”部分。
* 上传跟踪 – 将诊断数据上传到 Object Anchors 服务。    

    :::image type="content" source="./media/mrtk-hand-menu-primary.png" alt-text="Unity 主手动菜单":::

##### <a name="tracker-settings-menu"></a>跟踪器设置菜单

* **高准确度** – 一种试验性功能，用于获取更准确的姿势。 启用此选项，物体检测期间将需要更多系统资源。 在此模式下，物体网格将显示为粉红色。 再次选择此按钮以切换回正常跟踪模式。
* **宽松的垂直对齐** – 启用后，允许以非垂直角度检测物体。 对检测坡道上的物体很有用。
* **允许缩放更改** – 允许跟踪器根据环境信息更改检测到的物体的大小。
* **覆盖率滑块** – 调整跟踪器检测物体时必须匹配的表面点比例。  值越低，跟踪器越能更好地检测 HoloLens 传感器难以检测到的物体，例如深色物体或高反射性物体。 值较高，错误检测的频率越低。

    :::image type="content" source="./media/mrtk-hand-menu-tracker.png" alt-text="Unity 跟踪器手动菜单":::

##### <a name="search-area-settings-menu"></a>搜索区域设置菜单

* **锁定搜索区域** – 锁定区域边框以防止意外地手动移动。
* **自动调整搜索区域** – 使搜索区域可以在物体检测期间自动重新定位。
* **循环网格** – 循环显示搜索区域内已加载的网格。  此选项可帮助用户对齐搜索框以应对难以检测的物体。

    :::image type="content" source="./media/mrtk-hand-menu-search-area.png" alt-text="Unity 搜索区域手动菜单":::

[!INCLUDE [Unity troubleshooting](../../../includes/object-anchors-quickstart-unity-troubleshooting.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [快速入门：深度 MRTK 演练](in-depth-mrtk-walkthrough.md)

> [!div class="nextstepaction"]
> [概念：SDK 概述](../concepts/sdk-overview.md)

> [!div class="nextstepaction"]
> [常见问题](../faq.md)

> [!div class="nextstepaction"]
> [转换 SDK](/dotnet/api/overview/azure/mixedreality.objectanchors.conversion-readme-pre)
