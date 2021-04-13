---
title: 分层状态替代
description: 解释分层状态替代组件的概念。
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 851a87885ac765c829e8c2be9fd1205e22906ca9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "94445144"
---
# <a name="hierarchical-state-override"></a>分层状态替代

在许多情况下，需要动态更改某个[模型](../../concepts/models.md)的部件的外观，例如隐藏子图或将部件切换为透明渲染。 更改所涉及的每个部分的材料是不切实际的，因为它需要遍历整个场景图，并管理每个节点上的材料克隆和分配。

若要以尽可能少的开销完成此用例，请使用 `HierarchicalStateOverrideComponent`。 该组件在场景图的任意分支上实现分层状态更新。 这意味着，可以在场景图中的任何级别上定义状态，并且它会在层次结构中传递，直到它被新状态覆盖或应用于叶对象。

举例说明，试想汽车的模型，你希望将整个汽车切换为透明的，除了内部引擎部分。 此用例仅涉及组件的两个实例：

* 第一个组件分配给模型的根节点，并为整个汽车启用透明渲染。
* 第二个组件分配给引擎的根节点，并通过显式关闭透视模式再次替代该状态。

## <a name="features"></a>功能

可以替代的一组固定状态为：

* **`Hidden`** ：场景图中的相应网格将隐藏或显示。
* **`Tint color`** ：渲染的对象可以使用其各自的浅色和淡化权重进行颜色淡化。 下图显示了对轮缘进行颜色淡化。
  
  ![用于将对象变为绿色的浅色](./media/color-tint.png)

* **`See-through`** ：为了特定目的（例如，为了显示对象的内部部件）以半透明方式呈现几何结构。 下图显示了整个汽车以透视模式呈现，红色制动钳除外：

  ![用于使选定对象透明的透明模式](./media/see-through.png)

  > [!IMPORTANT]
  > 仅当使用“TileBasedComposition”[渲染模式](../../concepts/rendering-modes.md)时，透视效果才起作用。

* **`Shell`** ：几何结构将呈现为透明且降低饱和度的外壳。 此模式允许淡出场景中不重要的部分，同时仍保留形状感和相对位置感。 若要更改外壳渲染的外观，请使用 [ShellRenderingSettings](shell-effect.md) 状态。 请查看下图，了解整个都是外壳渲染的汽车模型（蓝色弹簧除外）：

  ![用于淡出特定对象的外壳模式](./media/shell.png)

  > [!IMPORTANT]
  > 仅当使用“TileBasedComposition”[渲染模式](../../concepts/rendering-modes.md)时，外壳效果才起作用。

* **`Selected`** ：几何结构呈现有[选择轮廓](outlines.md)。

  ![用于突出显示所选部件的轮廓选项](./media/selection-outline.png)

* **`DisableCollision`** ：几何结构将不受[空间查询](spatial-queries.md)的影响。 **`Hidden`** 标志不会影响冲突状态标志，因此，这两个标志通常一起设置。

* **`UseCutPlaneFilterMask`** ：使用单个筛选器位掩码来控制剪切平面选择。 此标志确定是应使用单个筛选器掩码还是应从其父级继承。 筛选器位掩码本身通过 `CutPlaneFilterMask` 属性设置。 有关筛选工作原理的详细信息，请参阅[“选择性剪切平面”段落](cut-planes.md#selective-cut-planes)。 请参见以下示例，其中仅剪切轮胎和轮辋，而场景的其余部分不受影响。
![选择性剪切平面](./media/selective-cut-planes-hierarchical-override.png)


> [!TIP]
> 作为关闭完整子图的可见性和空间查询的替代方法，可以切换游戏对象的 `enabled` 状态。 如果层次结构被禁用，则此项将优先于任何 `HierarchicalStateOverrideComponent`。

## <a name="hierarchical-overrides"></a>分层替代

`HierarchicalStateOverrideComponent` 可以附加在对象层次结构的多个级别上。 由于一个实体上只能有每个类型的一个组件，因此每个 `HierarchicalStateOverrideComponent` 管理隐藏、透视、选定、颜色淡化和冲突等状态。

因此每个状态都可以设置为以下之一：

* `ForceOn` - 该状态会为此节点之上和之下的所有网格启用
* `ForceOff` - 该状态会为此节点之上和之下的所有网格禁用
* `InheritFromParent` - 该状态不受此替代组件影响

可以直接更改状态，也可以通过 `SetState` 函数更改状态：

```cs
HierarchicalStateOverrideComponent component = ...;

// set one state directly
component.HiddenState = HierarchicalEnableState.ForceOn;

// set a state with the SetState function
component.SetState(HierarchicalStates.SeeThrough, HierarchicalEnableState.InheritFromParent);

// set multiple states at once with the SetState function
component.SetState(HierarchicalStates.Hidden | HierarchicalStates.DisableCollision, HierarchicalEnableState.ForceOff);
```

```cpp
ApiHandle<HierarchicalStateOverrideComponent> component = ...;

// set one state directly
component->SetHiddenState(HierarchicalEnableState::ForceOn);

// or: set a state with the SetState function
component->SetState(HierarchicalStates::SeeThrough, HierarchicalEnableState::InheritFromParent);

// set multiple states at once with the SetState function
component->SetState(
    (HierarchicalStates)((int32_t)HierarchicalStates::Hidden | (int32_t)HierarchicalStates::DisableCollision), HierarchicalEnableState::ForceOff);

```

### <a name="tint-color"></a>浅色

`tint color` 替代略有特殊，因为它既有开/关/继承状态，又有浅色属性。 浅色的 Alpha 部分定义淡化效果的权重：如果设置为 0.0，则不会显示浅色，如果设置为 1.0，则将以纯浅色呈现对象。 对于中间值，最终颜色将与浅色混合。 可以在每帧的基础上更改浅色，以实现彩色动画。

## <a name="performance-considerations"></a>性能注意事项

`HierarchicalStateOverrideComponent` 本身的实例不会增加很多运行时开销。 但是，保持较低的活动组件数量始终是一个好习惯。 例如，在实现突出显示所选对象的选择系统时，建议在删除突出显示时删除该组件。 无相关功能的组件会迅速累积。

透明渲染比标准渲染在服务器 GPU 上增加了更多工作负载。 如果场景图的大型部分切换到 *see-through*，且几何结构多个层次可见，则可能会成为性能瓶颈。 具有[选择轮廓](../../overview/features/outlines.md#performance)的对象和[外壳渲染](../../overview/features/shell-effect.md#performance)也是如此。 

## <a name="api-documentation"></a>API 文档

* [C# HierarchicalStateOverrideComponent 类](/dotnet/api/microsoft.azure.remoterendering.hierarchicalstateoverridecomponent)
* [C++ HierarchicalStateOverrideComponent 类](/cpp/api/remote-rendering/hierarchicalstateoverridecomponent)

## <a name="next-steps"></a>后续步骤

* [轮廓](../../overview/features/outlines.md)
* [渲染模式](../../concepts/rendering-modes.md)
* [空间查询](../../overview/features/spatial-queries.md)