---
title: 快速入门：深度 MRTK 演练
description: 本快速入门将深入探讨 Azure Object Anchors MRTK Unity 示例应用程序
author: RamonArguelles
manager: virivera
ms.author: rgarcia
ms.date: 07/12/2021
ms.topic: quickstart
ms.service: azure-object-anchors
ms.openlocfilehash: 515d44cc7cb40972c67424799163bc57deba9182
ms.sourcegitcommit: b59e0afdd98204d11b7f9b6a3e55f5a85d8afdec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114371576"
---
# <a name="quickstart-in-depth-mrtk-walkthrough"></a>快速入门：深度 MRTK 演练

本指南将深入介绍 [Azure Object Anchors MRTK Unity 示例应用程序](get-started-unity-hololens-mrtk.md)， 目的在于提供对示例设计的见解。 通过阅读本指南，开发者可以快速理解示例中的 Azure Object Anchors 概念。

## <a name="project-layout"></a>项目布局

为 Azure Object Anchors MRTK Unity 示例创建的资产存储在 `Assets\MixedReality.AzureObjectAnchors` 中。 子文件夹如下所示：

- **图标**
  - 包含面向用户的菜单中使用的一些自定义图标。
- **材料**
  - 包含用于曲面重建可视化效果的着色器和材料以及一个“仅深度”着色器，该着色器写入深度缓冲区，以帮助围绕文本实现全息影像稳定。
- 预制
  - 包含可重用的 Unity `GameObjects`。 具体而言，`TrackableObjectPrefab` 表示 Azure Object Anchors 在检测到对象时创建的对象。
- **Profiles**
  - 包含自定义的 MRTK 配置文件，用于描述启用应用程序时 MRTK 中所需的最低功能。
- 场景
  - 包含 `AOASampleTestScene`，它是本示例中的主要场景。
- **脚本**
  - 包含为示例编写的脚本。

## <a name="unity-scene"></a>Unity 场景

“混合现实播放空间” - 主要是样板 MRTK

- <a href="/windows/mixed-reality/develop/unity/mrtk-getting-started" target="_blank">介绍 MRTK for Unity</a>
- 相机上附加了一个 UI，用于详细说明 Azure Object Anchors 的整体状态（请参阅 `OverlayDebugText.cs`）。

对象混合现实播放空间 – 主要与 Azure Object Anchors 相关，但也与有些 MRTK 控件相关。 附加到父级的两个脚本 `TrackableObjectSearch` 和 `ObjectTracker` 表示与 Azure Object Anchors 的主接口。

- **菜单**
  - 主要是 MRTK 代码，但 UI 交互面向 Azure Object Anchors 功能。
  - 附加的 `TrackableObjectMenu` 脚本执行将 MRTK UI 事件路由到相应 Azure Object Anchors 调用的主要作业。
  - <a href="/windows/mixed-reality/design/hand-menu" target="_blank">MRTK 手动菜单</a>。
- WorkspaceBoundingBox
  - 包含与控制边界框关联的 MRTK 脚本。
  - 还包含一个 `ModelVis` 子对象，用于在检测之前可视化 Azure Object Anchors 模型，以便在复杂检测期间帮助对齐。

## <a name="menu-walkthrough"></a>菜单演练

在深入了解脚本之前，我们先浏览一下菜单项。 这样，我们就可以引用这些菜单项如何与脚本交互。

:::image type="content" source="./media/mrtk-menus.png" alt-text="mrtk 菜单":::

底部和右侧的子菜单不会自动显示，分别使用 `Search Area Settings` 和 `Tracker Settings` 切换。

### <a name="main-menu"></a>主菜单

- 开始搜索
  - 开始搜索指定搜索区域中的对象。
- 切换空间映射
  - 在搜索时显示空间映射之间循环，始终是显示空间映射和从不显示空间映射。
- 跟踪器设置/搜索区域设置
  - 切换其各自的子菜单。
- 启动跟踪/停止跟踪
  - 开始或结束诊断跟踪。
- 上传跟踪
  - 将诊断跟踪上传到 Microsoft 进行调试分析。

### <a name="tracker-settings"></a>跟踪器设置

- 高精度
  - 启用后，`ObjectInstanceTrackingMode` 设置为 `HighLatencyAccuratePosition`。
  - 禁用后，`ObjectInstanceTrackingMode` 设置为 `LowLatencyCoarsePosition`。
- **宽松垂直对齐**
  - 启用后，`AllowedVerticalOrientationInDegrees` 设置为 10 度。 此功能允许检测渐变的对象。
  - 禁用后，`AllowedVerticalOrientationInDegrees` 设置为 0 度。
- **允许更改比例**
  - 启用后，`MaxScaleChange` 设置为 0.1。 此功能允许 Azure Object Anchors 根据 HoloLens 的跟踪缩放调整来调整对象的比例。
  - 禁用后，`MaxScaleChange` 设置为 0。
- 覆盖率滑块
  - 调整对象检测所需的覆盖率，以考虑匹配项。 值越低，检测频率越高。 此功能可能很适合难以检测的对象，但也可能导致对象检测误报增加。

### <a name="search-area-settings"></a>搜索区域设置

- 锁定搜索区域
  - 启用后，将阻止用户更改搜索区域。
- 自动调整搜索区域
  - 启用后，允许脚本移动搜索区域以优化检测过程。
- 循环网格
  - 在可检测到 `.ou` 对象的网格与无网格之间循环。

## <a name="scripts"></a>脚本

- AutonomousSearchArea.cs
  - 此脚本附加到 `WorkspaceBoundingBox`。 该脚本尝试自动缩放并放置 `WorkspaceBoundingBox`。 启用 `Auto-Adjust Search Area` 时，它也会随之启用。
- ObjectAnchorsSubscription.cs
  - 此脚本包装上传诊断数据所需的信息。
- ObjectTracker.cs
  - 此脚本桥接 Unity 与 Azure Object Anchors SDK 的检测方面。
- ObjectTrackerDiagnostics.cs
  - 此脚本管理 Azure Object Anchors SDK 的诊断功能。
- OverlayDebugText.cs
  - 此脚本附加到主相机。 负责向用户显示示例和 Azure Object Anchors 的总体状态。
- PositionDebugInfo.cs
  - 此脚本只负责强制向用户显示附加到所检测到对象的调试文本。
- SearchAreaController.cs
  - 此脚本管理用于指示 Azure Object Anchors 搜索对象时所用位置的 `WorkspaceBoundingBox` 的状态。
- SearchAreaModelVisualization.cs
  - 此脚本可启用 `Search Area Settings` 下的 `Cycle Mesh` 功能。
- SpatialMappingController.cs
  - 此脚本根据与 `Main Menu` 下 `Toggle Spatial Mapping` 的交互管理何时应启用空间映射。
- TextToSpeech.cs
  - 此脚本可接受文本并会将文本转换为语音。
- TrackableObjectData.cs
  - 此脚本表示可跟踪的对象的数据。
- TrackableObjectDataLoader.cs
  - 此脚本执行加载 `.ou` 文件并将其转换为 `TrackableObjectData` 项的工作。
- TrackableObjectMenu.cs
  - 大多数用户交互会从 UI 流向此脚本，然后流向相应的脚本。 例如， `TrackableObjectMenu` 具有 `ToggleSpatialMapping`，后者会路由到 `SpatialMappingController`。
- TrackableObjectSearch.cs
  - 此脚本可对搜索区域执行一些轻型管理。 具体而言，用户进入空间的交互，以及将搜索框置于用户前面。 此外，它还可以在程序搜索时或检测到对象时避免呈现搜索框内部，以防止完全呈现遮挡对象。
- TrackedObject.cs
  - `TrackableObjectPrefab` 上的主脚本。 用于维护 Azure Object Anchors 所检测到对象的可视化状态。
- TrackedObjectData.cs
  - 此脚本包含有关 Azure Object Anchors 当前所跟踪对象的信息。

## <a name="prefabs"></a>预制

- TrackableObjectPrefab
  - AzureObjectAnchors 检测到对象时创建的预制件。 此预制件的子元素 `LogicalCenter` 表示对象的中心，是尝试将子内容附加至所检测到的对象时适当的开始位置。 示例显示的信息文本将附加到此逻辑中心。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [概念：SDK 概述](../concepts/sdk-overview.md)

> [!div class="nextstepaction"]
> [常见问题](../faq.md)

> [!div class="nextstepaction"]
> [转换 SDK](/dotnet/api/overview/azure/mixedreality.objectanchors.conversion-readme-pre)
