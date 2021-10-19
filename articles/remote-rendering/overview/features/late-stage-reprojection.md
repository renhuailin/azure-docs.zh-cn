---
title: 后期阶段重新投影
description: 介绍后期重投影及其使用方法。
author: sebastianpick
ms.author: sepick
ms.date: 02/04/2020
ms.topic: article
ms.openlocfilehash: 377e35b3107195841f49ecc8b1e5fb18fe38ed87
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2021
ms.locfileid: "129658531"
---
# <a name="late-stage-reprojection"></a>后期阶段重新投影

后期重投影 (LSR) 是一项硬件功能，用于在用户移动时帮助稳定全息图。

当你在静态模型周围移动时，静态模型在视觉上应保持其位置。 如果它们看起来不稳定，则可能暗示存在 LSR 问题。 请注意，其他动态转换（例如动画或爆炸视图）可能会掩盖此行为。

可以在两种不同的 LSR 模式（即“平面 LSR”或“深度 LSR”）之间进行选择。 哪一种模式处于活动状态取决于客户端应用程序是否提交深度缓冲区。

尽管两种 LSR 模式有不同的限制，但它们都能提高全息图稳定性。 首先尝试使用深度 LSR，因为在大多数情况下，理论上来说它可以提供更好的结果。

## <a name="choose-lsr-mode-in-unity"></a>在 Unity 中选择 LSR 模式

在 Unity 编辑器中，转到 :::no-loc text="File > Build Settings":::。 选择左下角的 *:::no-loc text="Player Settings":::* ，然后在 *:::no-loc text="Player > XR Settings > Virtual Reality SDKs > Windows Mixed Reality":::* 下检查是否选中了 **:::no-loc text="Enable Depth Buffer Sharing":::** ：

![“深度缓冲区共享已启用”标志](./media/unity-depth-buffer-sharing-enabled.png)

如果已选中，则你的应用将使用深度 LSR，否则将使用平面 LSR。

使用 OpenXR 时，应始终提交深度缓冲区。 可在 :::no-loc text="XR Plug-in Management > OpenXR"::: 中查找设置。 然后，可通过 OpenXR 插件中的扩展更改重新投影模式：

```cs
using Microsoft.MixedReality.OpenXR;

public class OverrideReprojection : MonoBehaviour
{
    void OnEnable()
    {
        RenderPipelineManager.endCameraRendering += RenderPipelineManager_endCameraRendering;
    }
    void OnDisable()
    {
        RenderPipelineManager.endCameraRendering -= RenderPipelineManager_endCameraRendering;
    }

    // When using the Universal Render Pipeline, OnPostRender has to be called manually.
    private void RenderPipelineManager_endCameraRendering(ScriptableRenderContext context, Camera camera)
    {
        OnPostRender();
    }

    // Called directly when using Unity's legacy renderer.
    private void OnPostRender()
    {
        ReprojectionSettings reprojectionSettings = default;
        reprojectionSettings.ReprojectionMode = ReprojectionMode.PlanarManual; // Or your favorite reprojection mode.
        
        // In case of PlanarManual you also need to provide a focus point here.
        reprojectionSettings.ReprojectionPlaneOverridePosition = ...;
        reprojectionSettings.ReprojectionPlaneOverrideNormal = ...;
        reprojectionSettings.ReprojectionPlaneOverrideVelocity = ...;

        foreach (ViewConfiguration viewConfiguration in ViewConfiguration.EnabledViewConfigurations)
        {
            if (viewConfiguration.IsActive && viewConfiguration.SupportedReprojectionModes.Contains(reprojectionSettings.ReprojectionMode))
            {
                viewConfiguration.SetReprojectionSettings(reprojectionSettings);
            }
        }
    }
}
```

## <a name="depth-lsr"></a>深度 LSR

要使深度 LSR 正常工作，客户端应用程序必须提供一个有效的深度缓冲区，其中包含要在 LSR 期间考虑的所有相关几何体。

深度 LSR 会尝试根据提供的深度缓冲区的内容来稳定视频帧。 因此，没有呈现给它的内容（例如透明对象）无法通过 LSR 进行调整，可能会显示不稳定和重新投影伪影。

若要降低透明对象的重新投影不稳定性，可以强制执行深度缓冲区写入。 至于[有色](color-materials.md)材料和 [PBR](pbr-materials.md) 材料，请参阅材料标志 TransparencyWritesDepth。 但请注意，当启用此标志时，透明/不透明对象交互的视觉对象质量可能会受到影响。

## <a name="planar-lsr"></a>平面 LSR

平面 LSR 没有单像素深度信息，而深度 LSR 有此信息。 它会改为根据你必须为每个帧提供的一个平面来重新投影所有内容。

对于靠近所提供平面的对象，平面 LSR 重新投影的效果最佳。 对象离得越远，看起来就越不稳定。 深度 LSR 更适合重新投影不同深度的对象，而平面 LSR 可能更适合与平面对齐的内容。

### <a name="configure-planar-lsr-in-unity"></a>在 Unity 中配置平面 LSR

平面参数源自所谓的焦点。 使用 WMR 时，必须通过 `UnityEngine.XR.WSA.HolographicSettings.SetFocusPointForFrame` 设置每一帧的焦点。 有关详细信息，请参阅 [Unity 焦点 API](/windows/mixed-reality/focus-point-in-unity)。 对于 OpenXR，需要通过上一部分中所示的 `ReprojectionSettings` 设置焦点。
如果你未设置焦点，系统会为你选择一个回退。 但是，自动回退通常会导致不理想的结果。

你可以自己计算焦点，虽然也可以基于远程渲染主机算出的焦点进行操作。 请调用 `RemoteManagerUnity.CurrentSession.GraphicsBinding.GetRemoteFocusPoint` 来获取该焦点。

通常情况下，客户端和主机都会呈现另一方不知道的内容，例如客户端上的 UI 元素。 因此，可以将远程焦点与本地算出的焦点结合使用。

在两个连续的帧中算出的焦点可能大不相同。 直接按原样使用它们可能会导致全息图看起来像是在跳跃。 若要防止此行为，建议在上一个焦点和当前焦点之间进行插值操作。

## <a name="next-steps"></a>后续步骤

* [服务器端性能查询](performance-queries.md)