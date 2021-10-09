---
title: 常见问题
description: 有关 Azure Object Anchors 服务的常见问题解答。
author: craigktreasure
manager: vriveras
ms.author: crtreasu
ms.date: 09/10/2021
ms.topic: overview
ms.service: azure-object-anchors
ms.openlocfilehash: 18069157b4c7f38216c01649a33ed5f999dc7577
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128638380"
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

**问：模型转换服务需要的重力方向和单位是什么？**

**答：** 重力方向是指向地球的向下矢量，度量单位表示模型的标度。 转换模型时，必须[确保重力方向和资产尺寸单位正确无误](./troubleshoot/object-detection.md#ensure-the-gravity-direction-and-asset-dimension-unit-are-correct)。

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

答：这取决于对象大小以及扫描进程。 若要获得更快的检测，请尝试遵循最佳做法进行全面扫描。
对于每个维度中 2 米以内的小型对象，可能会在几秒内检测到。 对于较大对象（如汽车），用户应绕着对象走一圈才能获得可靠检测，这意味着检测可能需要几十秒。

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

**答：** 这取决于对象的大小、材质、环境等。对于小型对象，估计姿态误差在 2 厘米内。 对于大型对象（如汽车），误差最多可达 2-8 厘米。

问：Object Anchors 是否可以处理移动对象？

答：不支持持续移动或动态对象。 在物理上将对象移动到新位置后，我们能够支持位于空间中全新位置的对象，但在移动该对象时无法对其进行跟踪。

问：Object Anchors 是否可以处理变形或接合？

答：可以处理部分，具体取决于对象形状或几何形状因变形或接合而改变的程度。 如果对象的几何形状变化幅度很大，用户可以为该配置创建另一个模型，然后将其用于检测。

**问：Object Anchors 可以同时检测多少个不同模型？**

**答：** 为确保最佳用户体验，我们目前支持一次检测三个模型，但不强制实施限制。

问：Object Anchors 是否可以检测到同一对象模型的多个实例？

**答：** 可以。为确保最佳用户体验，我们支持检测最多三个相同模型类型的实例，但不强制实施限制。 你可以在每个搜索区域检测一个对象实例。 通过调用 `ObjectQuery.SearchAreas.Add`，可以向查询添加更多搜索区域，以检测更多实例。 你可以对多个查询调用 `ObjectObserver.DetectAsync` 来检测多个模型。

问：如果 Object Anchors 运行时无法检测到我的对象，我该怎么办？

**答：** 有许多因素可能会导致无法正确检测对象：环境、模型转换配置、查询设置等。 详细了解如何[排查对象检测问题](./troubleshoot/object-detection.md)。

问：如何选择对象查询参数？

**答：** 这里有一些[常规指南](./troubleshoot/object-detection.md#adjust-object-query-values)，以及针对[难以检测的对象](./detect-difficult-object.md)的更详细的指南。

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

## <a name="next-steps"></a>后续步骤

本文介绍了一些常见问题的答案，让你可以在使用 Azure Object Anchors 时获得最佳结果。
下面是一些相关文章：

> [!div class="nextstepaction"]
> [最佳做法](./best-practices.md)

> [!div class="nextstepaction"]
> [排查物体检测问题](./troubleshoot/object-detection.md)
