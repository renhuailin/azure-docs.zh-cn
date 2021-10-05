---
title: 排查物体检测问题
description: 排查在使用 Azure Object Anchors 检测对象时出现的问题。
author: craigktreasure
manager: rgarcia
ms.author: crtreasu
ms.date: 09/10/2021
ms.topic: troubleshooting
ms.service: azure-object-anchors
ms.openlocfilehash: b70babbf3f105903cc7b14d175908b972bde9158
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128601988"
---
# <a name="troubleshooting-object-detection"></a>排查物体检测问题

本文假设已将 3D 模型转换为 Azure Object Anchors 检测模型，并已成功将该模型加载到应用程序中。

## <a name="troubleshooting-steps"></a>疑难解答步骤

* 确保检测的模型在支持的（1-10 米）大小范围内，以获得最佳体验。
* 通过添加一些海报，确保房间具有足够纹理。
* [如下所述](#remove-holograms-to-reset-the-map)删除当前的全息影像以重置映射。
* 更全面地扫描对象。
* 提供一个紧密的边界框作为搜索区域，包括对象的全部或大部分。
* 清除空间映射缓存并重新扫描对象。
* 确保在模型转换期间使用了正确的重力方向和资产尺寸，[如下所述](#ensure-the-gravity-direction-and-asset-dimension-unit-are-correct)。
* [如下所述](#visually-inspect-the-detection-models-mesh)以目视方式检查检测模型。
* [如下所述](#adjust-object-query-values)调整模型查询值。
* [如下所述](#capture-diagnostics)捕获诊断。

### <a name="remove-holograms-to-reset-the-map"></a>删除全息影像以重置映射

如果发现检测的对象存在以下任何问题，删除并重置映射可以解决该问题：
* 方向反转
* 姿势错误
* 模型倾斜

若要删除全息影像并重置映射，请打开“设置”应用并转到“系统” -> “全息影像”  。 然后选择“删除所有全息影像”以开始使用一个全新的映射。

如果最近移动了对象，清除全息影像可确保在对象的当前位置正确检测对象。

佩戴 HoloLens 在环境中四处走动，以重新扫描环境。 绕着你要检测的任何对象在 1-2 米范围内走动几次。

### <a name="ensure-the-gravity-direction-and-asset-dimension-unit-are-correct"></a>确保重力方向和资产尺寸单位正确

使用 Object Anchors 转换 SDK（参阅[此文](../quickstarts/get-started-model-conversion.md)）提交要转换的 3D 模型时，需要为 3D 模型输入正确的重力方向 (`Gravity`) 和度量单位 (`AssetDimensionUnit`)。 如果这些值不正确，则 Object Anchors 不太可能会正确检测对象。

重力方向是指向地球的向下矢量。 对于 CAD 模型，引力方向通常与向上方向相反。 例如，在许多情况下，+Z 表示向上，其中 -Z 或 `Vector3(0.0, 0.0, -1.0)` 表示重力方向。 确定重力时，还应考虑到模型在运行时是在哪个方向被看到的。 如果尝试在现实世界的一个平面上检测椅子，重力可能是 `Vector3(0.0, 0.0, -1.0)`。 但是，如果椅子位于 45 度的斜面上，则重力可能是 `Vector3(0.0, -Sqrt(2)/2, -Sqrt(2)/2)`。

可以使用 3D 渲染工具（例如 [MeshLab](http://www.meshlab.net/)）确定重力方向。

度量单位表示模型的标度。 可以使用 **Microsoft.Azure.ObjectAnchors.Conversion.AssetLengthUnit** 枚举找到支持的单位。

还可以按照[此处](../visualize-converted-model.md)的说明在 Unity 中可视化检测模型，以便以目视方式检查重力方向和标度看起来是否正确。

### <a name="visually-inspect-the-detection-models-mesh"></a>以目视方式检查检测模型的网格

有时，以目视方式检查检测模型的网格会很有帮助，这样可以看到任何方向、标度或特征问题。 按照[此处](../visualize-converted-model.md)的说明在 Unity 中可视化转换后的模型。

### <a name="adjust-object-query-values"></a>调整对象查询值

* 提供严格的搜索区域，理想情况下涵盖完整的对象以提高检测速度和准确性。
* 默认的 `ObjectQuery.MinSurfaceCoverage` 值通常已足够，但你可以使用较小的值来加快检测速度。
* 如果预计对象是直立的，请为 `ObjectQuery.ExpectedMaxVerticalOrientationInDegrees` 使用较小值。
* 应用应始终使用 `1:1` 对象模型进行检测。 估计的数值范围应接近 1，理想情况下误差不超过 1%。 应用可以将 `ObjectQuery.MaxScaleChange` 设置为 `0` 或 `0.1`，以禁用或启用小数位数估算，并定性评估实例姿态。
* 有关详细信息，请参阅[如何检测难以检测的对象](../detect-difficult-object.md)。

### <a name="capture-diagnostics"></a>捕获诊断

应用程序可以使用 [ObjectDiagnosticsSession](../concepts/sdk-overview.md#objectdiagnosticssession) 对象来捕获和保存诊断存档。

[使用 MRTK 的 Unity 示例应用](../quickstarts/get-started-unity-hololens-mrtk.md)将诊断数据写入 TempState 文件夹。 可以通过打开<a href="/windows/mixed-reality/mrtk-unity/features/ux-building-blocks/hand-menu" target="_blank">手形菜单</a>，选择“开始跟踪”并重现检测尝试来启动诊断会话，然后选择“停止跟踪”来保存诊断存档 。 然后，可以使用 [Windows 设备门户](/windows/mixed-reality/develop/platform-capabilities-and-apis/using-the-windows-device-portal)从应用的 TempState 文件夹检索诊断存档。

然后可与我们共享诊断存档，使我们能够帮助调试问题。

## <a name="next-steps"></a>后续步骤

在本故障排除指南中，你已了解如何排查在使用 Azure Object Anchors 检测物理对象时出现的问题。
下面是一些相关文章：

> [!div class="nextstepaction"]
> [如何检测困难对象](../detect-difficult-object.md)

> [!div class="nextstepaction"]
> [如何可视化 Object Anchors 模型](../visualize-converted-model.md)
