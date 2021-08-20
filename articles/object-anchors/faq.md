---
title: 常见问题
description: 有关 Azure Object Anchors 服务的常见问题解答。
author: craigktreasure
manager: vriveras
ms.author: crtreasu
ms.date: 04/01/2020
ms.topic: overview
ms.service: azure-object-anchors
ms.openlocfilehash: cb64f2be26abc1d3ccaf80b90a85f279c7930c94
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2021
ms.locfileid: "114710730"
---
# <a name="frequently-asked-questions-about-azure-object-anchors"></a>有关 Azure Object Anchors 的常见问题解答

使用 Azure Object Anchors，应用程序可以使用 3D 模型在物理世界中检测对象，并估计其 6-DoF 姿态。

有关详细信息，请参阅 [Azure Object Anchors 概述](overview.md)。

## <a name="product-faq"></a>产品常见问题解答
问：对于应使用的对象有哪些建议？

答：建议将以下属性用于对象：

* 每个维度 1-10 米
* 非对称，在几何形状上有足够变化
* 颜色鲜艳的低反射率（无光面）
* 静止对象
* 没有接合或很少接合
* 清理背景，没有杂物或有些许杂物
* 扫描对象应与你训练的模型 1:1 匹配

**问：对于模型转换，可以处理的最大对象维度有哪些？**

答：CAD 模型的每个维度应小于 10 米。 有关详细信息，请参阅[资产要求](overview.md)。

**问：可以处理以进行转换的最大 CAD 模型大小是多少？**

A：模型文件大小应小于 150 MB。 有关详细信息，请参阅[资产要求](overview.md)。

问：支持的 CAD 格式有哪些？

答：当前支持 `fbx`、`ply`、`obj`、`glb` 和 `gltf` 文件类型。 有关详细信息，请参阅[资产要求](overview.md)。

**问：模型转换服务需要的重力方向和单位是什么？如何计算？**

答：引力方向是指向地球的向下矢量。 对于 CAD 模型，引力方向通常与向上方向相反。 例如，在许多情况下，+Z 表示向上，其中 -Z 或 `Vector3(0.0, 0.0, -1.0)` 表示重力方向。 确定重力时，不仅应考虑模型，还应考虑模型在运行时期间显示的方向。 如果尝试在现实世界的一个平面上检测椅子，重力可能是 `Vector3(0.0, 0.0, -1.0)`。 但是，如果椅子位于 45 度的斜面上，则重力可能是 `Vector3(0.0, -Sqrt(2)/2, -Sqrt(2)/2)`。

重力方向可以通过 3D 渲染工具进行推理，例如 [MeshLab](http://www.meshlab.net/)。

单位表示模型的度量单位。 可以使用 **Microsoft.Azure.ObjectAnchors.Conversion.AssetLengthUnit** 枚举找到支持的单位。

**问：转换 CAD 模型需要多长时间？**

答：对于 `ply` 模型，通常需要 3-15 分钟。 如果以其他格式提交模型，将需要等待 15-60 分钟，具体取决于文件大小。

**问：在模型转换失败后，如何恢复？**

**答：** 若要详细了解模型转换作业失败可能导致的不同错误代码以及处理每个错误代码的方式，请参阅 [转换错误代码页](.\model-conversion-error-codes.md)。

问：Object Anchors 支持哪些设备？

**答：** HoloLens 2。

问：我的 HoloLens 应运行哪些 OS 版本？

答：OS 版本 18363.720 或于 2020 年 3 月 12 日后发布的更高版本。

  有关更多详细信息，请参阅 [Windows 10 2020 年 3 月 12 日更新](https://support.microsoft.com/help/4551762)。

问：在 HoloLens 上检测对象需要多长时间？

答：这取决于对象大小以及扫描进程。 若要获得更快的检测，请尝试遵循最佳做法进行全面扫描。 对于每个维度中 2 米以内的小型对象，可能会在几秒内检测到。 对于较大对象（如汽车），用户应绕着对象走一圈才能获得可靠检测，这意味着检测可能需要几十秒。

问：在 HoloLens 应用程序中使用 Object Anchors 的最佳做法是什么？

**答:**

 1. 执行目视校准以获取准确的渲染。
 2. 确保房间有丰富的视觉纹理和良好采光。
 3. 如果可以，请保持对象静止，远离杂物。
 4. （可选）在 HoloLens 设备上清除[空间映射](/windows/mixed-reality/spatial-mapping)缓存。
 5. 绕着对象走一圈进行扫描。 确保观测到对象的大部分。
 6. 设置一个足以覆盖对象的搜索区域。
 7. 检测期间，对象应保持静止。
 8. 启动对象检测并根据预计的姿态可视化渲染。
 9. 一旦姿态稳定且精确，锁定检测对象或停止跟踪以延长电池续航时间。

**问：HoloLens Unity 应用程序应该使用哪个版本的混合现实工具包 (MRTK) 才能使用 Object Anchors Unity SDK？**

**答：** Azure Object Anchors Unity SDK 不依赖于混合现实工具包，这意味着你可以使用你想使用的任意版本。 有关详细信息，请参阅 [MRTK for Unity 介绍](/windows/mixed-reality/develop/unity/mrtk-getting-started)。

问：估计姿态的准确性如何？

答：这取决于对象的大小、材质、环境等。对于小型对象，估计姿态误差在 2 厘米内。 对于大型对象（如汽车），误差最多可达 2-8 厘米。

问：Object Anchors 是否可以处理移动对象？

答：不支持持续移动或动态对象。 在物理上将对象移动到新位置后，我们能够支持位于空间中全新位置的对象，但在移动该对象时无法对其进行跟踪。

问：Object Anchors 是否可以处理变形或接合？

答：可以处理部分，具体取决于对象形状或几何形状因变形或接合而改变的程度。 如果对象的几何形状变化幅度很大，用户可以为该配置创建另一个模型，然后将其用于检测。

问：Object Anchors 可以同时检测多少个不同对象？

答：目前支持一次检测单个对象模型。

问：Object Anchors 是否可以检测到同一对象模型的多个实例？

答：是的，可以检测到最多 3 个同一模型类型的对象。 应用程序可以通过不同的查询多次调用 `ObjectObserver.DetectAsync` 来检测同一模型的多个实例。

问：如果 Object Anchors 运行时无法检测到我的对象，我该怎么办？

**答:**

* 通过添加一些海报，确保房间具有足够纹理。
* 更全面地扫描对象。
* 按如下所述调整模型参数。
* 提供一个紧密的边界框作为搜索区域，包括对象的全部或大部分。
* 清除空间映射缓存并重新扫描对象。
* 捕获诊断并将数据发送给我们。
* 调整 `MinSurfaceCoverage` 类中的 `ObjectQuery` 属性。 有关详细信息，请参阅[如何检测难以检测的对象](detect-difficult-object.md)。

问：如何选择对象查询参数？

**答:**

* 提供严格的搜索区域，理想情况下涵盖完整的对象以提高检测速度和准确性。
* 对象模型中的默认 `ObjectQuery.MinSurfaceCoverage` 通常良好，否则请使用较小的值来快速检测。
* 如果预计对象是直立的，则使用 `ObjectQuery.ExpectedMaxVerticalOrientationInDegrees` 较小值。
* 应用应始终使用 `1:1` 对象模型进行检测。 估计的数值范围应接近 1，理想情况下误差不超过 1%。 应用可以将 `ObjectQuery.MaxScaleChange` 设置为 `0` 或 `0.1`，以禁用或启用小数位数估算，并定性评估实例姿态。
* 有关详细信息，请参阅[如何检测难以检测的对象](detect-difficult-object.md)。

问：如何从 HoloLens 获取 Object Anchors 诊断数据？

答：应用程序可以指定诊断存档位置。 Object Anchors 示例应用将诊断写入到 TempState 文件夹。

问：使用 Object Anchors Unity SDK 返回的姿态时，为什么源模型与物理对象不一致？

答：导入对象模型时，Unity 可能更改坐标系统。 例如，当从右手坐标系统转换成左手坐标系统时，Object Anchors Unity SDK 会反转 Z 轴，但 Unity 可能会对 X 轴或 Y 轴进行其他旋转。 开发人员可以通过可视化和比较坐标系统来确定这一额外旋转。

问：是否支持 2D？

答：由于我们是基于几何图形的，因此仅支持 3D。

问：是否可以区分不同颜色的相同模型？

答：由于我们的算法基于几何图形，因此在检测期间，同一模型的不同颜色不会有不同表现。

问：是否可以在没有 Internet 连接的情况下使用 Object Anchors？

**答:** 
* 模型转换和训练需要连接，因为此操作在云中进行。
* 运行时会话完全在设备上，无需连接，因为所有计算都在 HoloLens 2 上发生。

## <a name="privacy-faq"></a>隐私常见问题解答
问：Azure Object Anchors 如何存储数据？

答：我们仅存储系统元数据，它使用 Microsoft 管理的数据加密密钥进行静态加密。
