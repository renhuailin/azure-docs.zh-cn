---
title: 在 Unity 中使用全息远程处理和远程渲染
description: 如何将全息远程处理与 Azure 远程渲染结合使用
author: christophermanthei
ms.author: chmant
ms.date: 03/23/2020
ms.topic: how-to
ms.openlocfilehash: bd69710b6e4404a76d3cca385b6c07ea7c1f3f5c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "92201811"
---
# <a name="use-holographic-remoting-and-remote-rendering-in-unity"></a>在 Unity 中使用全息远程处理和远程渲染

[全息远程处理](/windows/mixed-reality/holographic-remoting-player)和 Azure 远程渲染在一个应用程序中是互斥的。 正因如此，也无法使用 [Unity 播放模式](/windows/mixed-reality/unity-play-mode)。

每次运行 Unity 编辑器时，只能使用其中一个。 若要使用另一个，需先重新启动 Unity。

## <a name="use-unity-play-mode-to-preview-on-hololens-2"></a>使用 Unity 播放模式在 HoloLens 2 上预览

 仍可使用 Unity 播放模式，例如，用于测试应用程序 UI。 但是，绝不初始化 ARR，这一点非常重要。 否则，会出现故障。

## <a name="use-a-wmr-vr-headset-to-preview-on-desktop"></a>使用 WMR VR 头戴显示设备在桌面中预览

如果有 Windows 混合现实 VR 头戴显示设备，可将其用于在 Unity 内预览。 在这种情况下，可以初始化 ARR，但在使用 WMR 头戴显示设备时，将无法连接到会话。