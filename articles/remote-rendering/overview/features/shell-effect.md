---
title: Shell 呈现
description: 介绍如何使用 Shell 渲染效果
author: jumeder
ms.author: jumeder
ms.date: 10/23/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 7af95cba807cea340438a7de30f096758d0369ad
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594157"
---
# <a name="shell-rendering"></a>Shell 呈现

[层次结构状态替代组件](../../overview/features/override-hierarchical-state.md)的 shell 状态为透明效果。 与 [查看](../../overview/features/override-hierarchical-state.md) 呈现相比，只显示最顶层的对象，与不透明的呈现类似。 此外，当对象呈现为 shell 时，这些对象的正常外观也会改变。 这种效果适用于这样的用例：用户应从非重要部分进行可视化的指导，同时仍保持对整个场景的空间感知。

可以通过全局状态配置 shell 呈现对象的外观 `ShellRenderingSettings` 。 使用 shell 呈现的所有对象都将使用相同的设置。 每个对象参数不存在。

## <a name="shellrenderingsettings-parameters"></a>ShellRenderingSettings 参数

类 `ShellRenderingSettings` 包含与全局 shell 呈现属性相关的设置：

| 参数      | 类型    | 说明                                             |
|----------------|---------|---------------------------------------------------------|
| `Desaturation` | float   | 要应用于通常的最终对象颜色的 desaturation 量，范围为 0 (no desaturation) 为 1 (full desaturation)  |
| `Opacity`      | float   | Shell 呈现对象的不透明度，范围 0 (不可见) 为 1 (完全不透明)  |

另请参阅下表，了解在应用于整个场景时的参数效果的示例：

|                | 0 | 0.25 | 0.5 | 0.75 | 1.0 | 
|----------------|:-:|:----:|:---:|:----:|:---:|
| **Desaturation** | ![Desaturation-0。0](./media/shell-desaturation-00.png) | ![Desaturation-0.25](./media/shell-desaturation-025.png) | ![Desaturation-0。5](./media/shell-desaturation-05.png) | ![Desaturation-0.75](./media/shell-desaturation-075.png) | ![Desaturation-1。0](./media/shell-desaturation-10.png) |
| **不透明度**      | ![不透明度-0。0](./media/shell-opacity-00.png) | ![不透明度-0.25](./media/shell-opacity-025.png) | ![不透明度-0。5](./media/shell-opacity-05.png) | ![不透明度-0.75](./media/shell-opacity-075.png) | ![不透明度-1。0](./media/shell-opacity-10.png) |

Shell 效果应用于将以其他方式呈现场景的最终不透明颜色。 包含 [淡色层次状态重写](../../overview/features/override-hierarchical-state.md)的。

## <a name="example"></a>示例

以下代码通过 API 显示状态的示例用法 `ShellRenderingSettings` ：

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

与标准的不透明呈现相比，shell 呈现功能具有较小的常量开销。 比使用对象上的透明材料或 [透过](../../overview/features/override-hierarchical-state.md) 渲染要快得多。 如果仅部分场景切换到 shell 渲染，则性能可能会更高。 出现这种情况的原因可能是，另外显示了需要呈现的对象。 就这一点而言，性能的行为类似于 " [剪切平面](../../overview/features/cut-planes.md) " 功能。

## <a name="next-steps"></a>后续步骤

* [分层状态重写组件](../../overview/features/override-hierarchical-state.md)