---
title: 最佳实践
description: 获得改进结果的推荐最佳做法
author: ariye
ms.author: crtreasu
ms.date: 09/10/2021
ms.topic: best-practice
ms.service: azure-object-anchors
ms.openlocfilehash: 119837282409719a194341467a5d8164a46431b9
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124777576"
---
# <a name="best-practices"></a>最佳实践

建议尝试其中的一些步骤以获得最佳结果。

## <a name="conversion"></a>转换

- 检查物理对象的尺寸。 Azure Object Anchors 最适用于其最小尺寸在建议的 1 米 - 10 米范围内的对象。
- 在 [MeshLab](https://www.meshlab.net/) 之类的软件中检查 3D 模型，以了解以下详细信息。
  - 确保 3D 模型具有三角形网格，并且外表面上的三角形朝外。 也就是说，顶点的方向应使法线在向外方向上遵循右手定则。
  - 确保为 3D 模型指定了相对于物理对象的正确缩放单位。 单位应为以下之一：厘米、分米、英尺、英寸、公里、米、毫米、码。
  - 确认对应于现实世界中对象的垂直方向的标称重力方向。 如果对象的向下垂直/重力为 -Y，则使用 (0, -1, 0) 或 (0, 0, -1) 表示 -Z，同样地，也可以表示任何其他方向 _*_ 。
  - 请确保 3D 模型采用受支持的格式之一进行编码：`.glb`、`.gltf`、`.ply`、`.fbx`、`.obj`。
- 我们的模型转换服务可能需要很长时间才能处理大型、高 LOD（详细程度）模型。 为了提高效率，可以对 3D 模型进行预处理以删除内部面。

## <a name="detection"></a>检测

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Mixed-Reality/Azure-Object-Anchors-Detection-and-Alignment-Best-Practices/player]

- 提供的运行时 SDK 需要用户提供的搜索区域来搜索和检测物理对象。 搜索区域可以是边界框、球体、视锥体或它们的任意组合。 若要避免错误检测，请设置一个足以涵盖对象的搜索区域。 使用提供的示例应用时，请站在距离最近的表面 2 米左右的位置启动应用。
- 在 HoloLens 2 设备上启动 Azure Object Anchors 应用之前，请使用“设置”应用删除所有全息影像。
  转到“系统” -> “全息影像”，然后选择“删除所有全息影像”以启动一个全新的地图。

  清除全息影像可确保在对象的当前位置正确检测对象，以防它们最近移动。
- 删除全息影像后，在启动应用之前，请佩戴 HoloLens 距离对象 1-2 米绕着对象走动来扫描对象。

  预先扫描对象和环境有助于清除从以前的对象和扫描创建的任何残留面。
  否则，应用可能会看到重影表面，导致 3D 模型和相关全息影像无法准确对齐。 预扫描对象还可以大幅减少 Azure Object Anchors 检测延迟，例如，从 30 秒减少到 5 秒。
- 对于深色和高反射率的对象，你可能需要从多个角度和多个距离在更近的范围内扫描对象。
- 提供的运行时 SDK 提供了几个参数，以允许用户微调检测，如我们的示例应用所示。 默认参数适用于大多数对象。 如果你发现需要针对特定对象调整它们，以下是一些建议：
  - 如果物理对象较大、较暗或发亮，请使用较低的表面覆盖率阈值。
  - 可以对大型物体（例如汽车）进行小范围的更改（例如 0.1）。
  - 当对象在斜坡上时，允许在对象的局部垂直方向和重力方向之间存在一定程度的偏差。

## <a name="next-steps"></a>后续步骤

在本指南中，你学习了一些最佳做法，以便在使用 Azure Object Anchors 来检测对象时获得最佳结果。 下面是一些相关文章：

> [!div class="nextstepaction"]
> [排查物体检测问题](./troubleshoot/object-detection.md)
