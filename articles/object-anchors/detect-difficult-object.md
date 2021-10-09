---
title: 如何检测困难对象
description: 描述可配置为检测困难对象的机制。
author: rgarcia
manager: vrivera
ms.author: rgarcia
ms.date: 09/08/2021
ms.topic: troubleshooting
ms.service: azure-object-anchors
ms.openlocfilehash: cbdf29aa7bb9c342e08b64af0745c689910b26bd
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124791966"
---
# <a name="how-to-detect-a-difficult-object"></a>如何检测困难对象

有时，对象可能会变得更加难以检测到。 例如：

- 由于对象靠墙而导致大量表面积无法访问时
- 对象太大并且在该对象周围行走所花费的时间过长时
- 当设备传感器未检测到对象的表面时

## <a name="adjusting-object-query-values"></a>调整对象查询值

Azure Object Anchors SDK 提供的一些机制可在以下情况下可能有所帮助：

- `ObjectQuery.MinSurfaceCoverage` 属性。 表示一个对象实例被视为真正而必须达到的最低表面覆盖比率。 此属性的允许范围是 0 到 1.0（表示 0% 到 100%）。 默认设置因对象而异（表面积越大，所需的最小覆盖范围越小）。 在大多数情况下都会正常工作。 但遇到困难对象时，建议减小此属性的值，这样检测对象时需要的表面覆盖率更低。

- `ObjectQuery.MaxScaleChange` 属性。 如果原始模型没有被检测对象的 `1:1` 比例，则可以调整此设置。 此属性的允许范围是 0 到 1.0（表示 0% 到 100%）。 默认设置为 0，这将禁用比例估算，因此需要 `1:1` 比例映射。 例如，如果将此属性设置为 10%，则将启用比例估算，并且在模型比例与对象不是 `1:1` 匹配的情况下允许一定的灵活性。

- `ObjectQuery.ExpectedMaxVerticalOrientationInDegrees` 属性。 表示对象的向上方向与重心之间的最大角度（以度为单位）。 范围从 0 到 180。 换句话说，此属性表示对象相对于原始模型的倾斜度。 默认设置为 3 度，可以增大，这样可在对象倾斜度与原始模型不匹配的情况下提供更大的灵活性。

- `ObjectQuery.IsExpectedToBeStandingOnGroundPlane` 属性。 这是一个布尔值，表示对象是否应位于地面级别。 默认值为 false。 可以切换为 true，可在对象处于地面级别的情况下加快检测的速度。

- `ObjectQuery.SearchAreas` 属性。 表示要查找对象的区域的集合。 提供密集的搜索区域，同时仍涵盖全部或大部分对象，从而提高检测速度和准确性。 您也可选取：

  - 定向的边界框（使用 `ObjectSearchArea.FromOriented`）。
  - 视图字段（使用 `ObjectSearchArea.FromFieldOfView`）。
  - 位置（使用 `ObjectSearchArea.FromLocation`）。
  - 球体（使用 `ObjectSearchArea.FromSphere`）。

有关详细信息，请参阅适用于 [Unity](/dotnet/api/microsoft.azure.objectanchors.objectquery) 或 [HoloLens C++/WinRT](/cpp/api/object-anchors/winrt/objectquery) 的 `ObjectQuery` 类。

## <a name="next-steps"></a>后续步骤

在此故障排除指南中，你了解了如何排查难以检测对象的检测问题。
下面是一些相关文章：

> [!div class="nextstepaction"]
> [排查物体检测问题](./troubleshoot/object-detection.md)
