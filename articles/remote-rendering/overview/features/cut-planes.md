---
title: 剪切平面
description: 介绍了什么是裁切平面，以及如何使用裁切平面
author: jakrams
ms.author: jakras
ms.date: 02/06/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: b3348e5a999b507aa0d286528970beb0e03f26cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "99594366"
---
# <a name="cut-planes"></a>剪切平面

裁切平面是一项视觉功能，它会剪裁虚拟平面一端上的像素，从而呈现[网格](../../concepts/meshes.md)的内部。
下图演示了该效果。 左侧显示了原始网格，在右侧可以看到网格内部：

![裁切平面](./media/cutplane-1.png)

## <a name="cutplanecomponent"></a>CutPlaneComponent

可以通过创建 *CutPlaneComponent* 在场景中添加裁切平面。 平面的位置和方向由组件的所有者[实体](../../concepts/entities.md)确定。

```cs
void CreateCutPlane(RenderingSession session, Entity ownerEntity)
{
    CutPlaneComponent cutPlane = (CutPlaneComponent)session.Connection.CreateComponent(ObjectType.CutPlaneComponent, ownerEntity);
    cutPlane.Normal = Axis.X; // normal points along the positive x-axis of the owner object's orientation
    cutPlane.FadeColor = new Color4Ub(255, 0, 0, 128); // fade to 50% red
    cutPlane.FadeLength = 0.05f; // gradient width: 5cm
}
```

```cpp
void CreateCutPlane(ApiHandle<RenderingSession> session, ApiHandle<Entity> ownerEntity)
{
    ApiHandle<CutPlaneComponent> cutPlane = session->Connection()->CreateComponent(ObjectType::CutPlaneComponent, ownerEntity)->as<CutPlaneComponent>();;
    cutPlane->SetNormal(Axis::X); // normal points along the positive x-axis of the owner object's orientation
    Color4Ub fadeColor;
    fadeColor.channels = { 255, 0, 0, 128 }; // fade to 50% red
    cutPlane->SetFadeColor(fadeColor);
    cutPlane->SetFadeLength(0.05f); // gradient width: 5cm
}
```

### <a name="cutplanecomponent-properties"></a>CutPlaneComponent 属性

裁切平面组件上公开了以下属性：

* `Enabled`：可以通过禁用此组件来暂时关闭裁切平面。 已禁用的裁切平面不会产生渲染开销，且不会计入全局裁切平面限制。

* `Normal`：指定要将哪个方向（+X、-X、+Y、-Y、+Z、-Z）用作平面法线。 此方向相对于所有者实体的方向。 请移动和旋转所有者实体以便精确定位。

* `FadeColor` 和 `FadeLength`：

  如果 *FadeColor* 的 alpha 值不为零，则靠近裁切平面的像素将会根据 FadeColor 的 RGB 部分淡化。 alpha 通道的强度决定了像素是根据淡化颜色完全淡化，还是仅部分淡化。 *FadeLength* 定义超过什么距离将进行这种淡化。

* `ObjectFilterMask`：一个筛选器位掩码，用于确定受剪切平面影响的几何结构。 有关详细信息，请参阅下一段。

### <a name="selective-cut-planes"></a>选择性剪切平面

可以配置单独的剪切平面，使其仅影响特定几何结构。 下图演示了这种设置在实际中可能呈现的状态：

![选择性剪切平面](./media/selective-cut-planes.png)

通过剪切平面端的位掩码与几何结构上设置的另一个位掩码之间的 **逻辑位掩码比较** 执行筛选。 如果掩码之间逻辑 `AND` 操作的结果并非零，则剪切平面将影响几何结构。

* 剪切平面组件上的位掩码通过其 `ObjectFilterMask` 属性设置
* 几何子层次结构上的位掩码通过 [HierarchicalStateOverrideComponent](override-hierarchical-state.md#features) 设置

示例：

| 剪切平面筛选器掩码 | 几何结构筛选器掩码  | 逻辑 `AND` 的结果 | 剪切平面会影响几何结构？  |
|--------------------|-------------------|-------------------|:----------------------------:|
| (0000 0001) == 1   | (0000 0001) == 1  | (0000 0001) == 1  | 是 |
| (1111 0000) == 240 | (0001 0001) == 17 | (0001 0000) == 16 | 是 |
| (0000 0001) == 1   | (0000 0010) == 2  | (0000 0000) == 0  | 否 |
| (0000 0011) == 3   | (0000 1000) == 8  | (0000 0000) == 0  | 否 |

>[!TIP]
> 将剪切平面的 `ObjectFilterMask` 设置为 0，意味着它不会影响任何几何结构，因为逻辑 `AND` 的结果永远不会为非 null。 渲染系统最初不会考虑这些平面，所以这是一种禁用单独剪切平面的轻型方法。 这些剪切平面也不影响 8 个活动平面的限制。

## <a name="limitations"></a>限制

* Azure 远程渲染 **最多支持 8 个活动的剪切平面**。 你可以创建更多的裁切平面组件，但如果尝试同时启用更多组件，它将忽略激活操作。 如果要切换应当影响场景的组件，请先禁用其他平面。
* 剪切平面只是一项视觉功能，不会影响[空间查询](spatial-queries.md)的结果。 如果你确实想要将光线投影到裁开的网格，可以调整光线的起点，使其位于裁切平面上。 这样，光线便只会落在可视部分上。

## <a name="performance-considerations"></a>性能注意事项

在渲染期间，每个活动的裁切平面会产生较小的成本。 请禁用或删除不再需要的裁切平面。

## <a name="api-documentation"></a>API 文档

* [C# CutPlaneComponent 类](/dotnet/api/microsoft.azure.remoterendering.cutplanecomponent)
* [C++ CutPlaneComponent 类](/cpp/api/remote-rendering/cutplanecomponent)

## <a name="next-steps"></a>后续步骤

* [单面渲染](single-sided-rendering.md)
* [空间查询](spatial-queries.md)