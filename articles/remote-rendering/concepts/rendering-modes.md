---
title: 渲染模式
description: 描述不同的服务器端渲染模式
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 2cf1872bcdd7b1bda74046198f5fc32be1069913
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "99594495"
---
# <a name="rendering-modes"></a>渲染模式

远程渲染提供两种主要操作模式：**TileBasedComposition** 模式和 **DepthBasedComposition** 模式。 这些模式决定了如何在服务器上的多个 GPU 之间分布工作负荷。 模式必须在连接时指定，在运行时无法更改。

这两种模式不仅有优点，也存在固有的功能限制，因此，如何选取最适合的模式取决于具体用例。

## <a name="modes"></a>模式

现在我们更详细地介绍这两种模式。

### <a name="tilebasedcomposition-mode"></a>“TileBasedComposition”模式

在 TileBasedComposition 模式下，每个涉及的 GPU 都会在屏幕上渲染特定的子矩形（图块）。 主 GPU 根据图块组合出最终图像，然后将最终图像作为视频帧发送到客户端。 相应地，所有 GPU 都需要使用同一组资源进行渲染，因此，已加载的资产需要能够容纳到一个 GPU 的内存中。

这种模式下的渲染质量会略好于 DepthBasedComposition 模式，因为 MSAA 可以为每个 GPU 处理整组几何图形。 下面的屏幕截图显示了抗锯齿对于两种边缘都可以同样正确地工作：

![TileBasedComposition 中的 MSAA](./media/service-render-mode-quality.png)

此外，在这种模式下，每个部分都可以切换到透明材料，或通过 [HierarchicalStateOverrideComponent](../overview/features/override-hierarchical-state.md) 切换到透明模式

### <a name="depthbasedcomposition-mode"></a>“DepthBasedComposition”模式

在 DepthBasedComposition 模式下，每个涉及的 GPU 都以全屏分辨率进行渲染，但只是渲染一部分网格。 主 GPU 上的最终图像构成会进行相应的处理，确保各部分根据其深度信息正确合并。 通常，内存有效负载是分布在 GPU 中的，因此允许渲染模型不容纳在一个 GPU 的内存中。

每个单一的 GPU 都会使用 MSAA 对本地内容进行抗锯齿处理。 但是，在不同的的 GPU 中的边缘之间，可能会存在固有的锯齿现象。 通过对最终图像进行后处理，这种影响会得到削弱，但 MSAA 质量仍比在 TileBasedComposition 模式下要差。

下图展示了 MSAA 项目：![DepthBasedComposition 中的 MSAA](./media/service-render-mode-balanced.png)

抗锯齿在雕像和窗帘之间的效果是正常的，因为这两个部分都在同一 GPU 上渲染。 另一方面，窗帘和墙壁之间的边缘会出现一些锯齿，因为这两个部分是由不同的 GPU 完成构图的。

此模式的最大限制是，几何图形部分无法动态切换为透明材料，透明模式也对 [HierarchicalStateOverrideComponent](../overview/features/override-hierarchical-state.md) 不起作用。 不过，其他状态替代功能（轮廓、颜色色调等）确实有效。 另外，在此模式下，在转换时标记为透明的材料确实可以正常工作。

### <a name="performance"></a>性能

这两种模式的性能特征都会因用例而异，很难分析出或提供一般性的建议。 如果上述限制（内存或透明度/透明）对你没有约束，建议对两种模式都进行试验，并使用不同的摄像机位来监视性能。

## <a name="setting-the-render-mode"></a>设置渲染模式

远程渲染服务器上使用的渲染模式是在 `RenderingSession.ConnectAsync` 期间通过 `RendererInitOptions` 指定的。

```cs
async void ExampleConnect(RenderingSession session)
{
    RendererInitOptions parameters = new RendererInitOptions();

    // Connect with one rendering mode
    parameters.RenderMode = ServiceRenderMode.TileBasedComposition;
    await session.ConnectAsync(parameters);

    session.Disconnect();

    // Wait until session.IsConnected == false

    // Reconnect with a different rendering mode
    parameters.RenderMode = ServiceRenderMode.DepthBasedComposition;
    await session.ConnectAsync(parameters);
}
```

## <a name="api-documentation"></a>API 文档

* [C# RenderingSession.ConnectAsync()](/dotnet/api/microsoft.azure.remoterendering.renderingsession.connectasync)
* [C# RendererInitOptions 结构](/dotnet/api/microsoft.azure.remoterendering.rendererinitoptions)
* [C++ RenderingSession::ConnectToConnectAsyncRuntime()](/cpp/api/remote-rendering/renderingsession#connectasync)
* [C++ RendererInitOptions 结构](/cpp/api/remote-rendering/rendererinitoptions)

## <a name="next-steps"></a>后续步骤

* [会话](../concepts/sessions.md)
* [分层状态重写组件](../overview/features/override-hierarchical-state.md)