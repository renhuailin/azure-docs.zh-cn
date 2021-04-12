---
title: Shell 呈现
description: 介绍如何使用 Shell 渲染效果
author: jumeder
ms.author: jumeder
ms.date: 10/23/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 7af95cba807cea340438a7de30f096758d0369ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "99594157"
---
# <a name="shell-rendering"></a>Shell 呈现

[分层状态替代组件](../../overview/features/override-hierarchical-state.md)的 shell 状态为透明效果。 与 [see-through](../../overview/features/override-hierarchical-state.md) 渲染相比，它只显示对象的最上面一层，与不透明渲染类似。 此外，采用 shell 渲染时，对象的正常外观也会改变。 这种效果适用于这样的用例：用户应获得可视化指引来专注于重要部分，但同时仍保持对整个场景的空间感知。

可以通过 `ShellRenderingSettings` 全局状态配置 shell 渲染对象的外观。 使用 shell 渲染的所有对象都将使用相同的设置。 所有对象使用相同参数。

## <a name="shellrenderingsettings-parameters"></a>ShellRenderingSettings 参数

类 `ShellRenderingSettings` 包含与全局 shell 渲染属性相关的设置：

| 参数      | 类型    | 说明                                             |
|----------------|---------|---------------------------------------------------------|
| `Desaturation` | FLOAT   | 要应用于常用最终对象颜色的去饱和度，范围为 0（零去饱和）到 1（全去饱和） |
| `Opacity`      | FLOAT   | Shell 渲染对象的不透明度，范围为 0（不可见）到 1（完全不透明） |

另请参阅下表，了解参数在应用于整个场景时的效果示例：

|                | 0 | 0.25 | 0.5 | 0.75 | 1.0 | 
|----------------|:-:|:----:|:---:|:----:|:---:|
| **Desaturation** | ![Desaturation-0.0](./media/shell-desaturation-00.png) | ![Desaturation-0.25](./media/shell-desaturation-025.png) | ![Desaturation-0.5](./media/shell-desaturation-05.png) | ![Desaturation-0.75](./media/shell-desaturation-075.png) | ![Desaturation-1.0](./media/shell-desaturation-10.png) |
| **不透明度**      | ![Opacity-0.0](./media/shell-opacity-00.png) | ![Opacity-0.25](./media/shell-opacity-025.png) | ![Opacity-0.5](./media/shell-opacity-05.png) | ![Opacity-0.75](./media/shell-opacity-075.png) | ![Opacity-1.0](./media/shell-opacity-10.png) |

在最终不透明颜色上应用 Shell 效果，场景将渲染为其他效果。 这包含[浅色分层状态替代](../../overview/features/override-hierarchical-state.md)。

## <a name="example"></a>示例

以下代码显示通过 API 的 `ShellRenderingSettings` 状态的示例用法：

```cs
void SetShellSettings(RenderingSession session)
{
    ShellRenderingSettings shellRenderingSettings = session.Connection.ShellRenderingSettings;
    shellRenderingSettings.Desaturation = 0.5f;
    shellRenderingSettings.Opacity = 0.1f;
}
```

```cpp
void SetShellSettings(ApiHandle<RenderingSession> session)
{
    ApiHandle<ShellRenderingSettings> shellRenderingSettings = session->Connection()->GetShellRenderingSettings();
    shellRenderingSettings->SetDesaturation(0.5f);
    shellRenderingSettings->SetOpacity(0.1f);
}
```

## <a name="performance"></a>性能

与标准不透明渲染相比，shell 渲染功能的固定开销较小。 它比在对象上使用透明材料或 [see-through](../../overview/features/override-hierarchical-state.md) 渲染要快得多。 如果仅将部分场景切换到 shell 渲染，则性能可能会大打折扣。 出现这种情况的原因是，需要渲染其他显现出来的对象。 就此而言，其性能与[剪切平面](../../overview/features/cut-planes.md)功能类似。

## <a name="next-steps"></a>后续步骤

* [分层状态重写组件](../../overview/features/override-hierarchical-state.md)