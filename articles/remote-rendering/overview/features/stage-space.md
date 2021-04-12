---
title: 暂存空间
description: 介绍暂存空间设置和用例
author: christophermanthei
ms.author: chmant
ms.date: 03/07/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 4a739907ce0a3b0b6dfcb8791b51d0ea5e7e76e7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "99594004"
---
# <a name="stage-space"></a>暂存空间

在提供头部跟踪数据（如 HoloLens 2）的设备上运行 ARR 时，会将头部姿态发送到用户应用程序和服务器。 在其中定义头部转换的空间称为暂存空间。

若要对齐本地和远程内容，假定客户端和服务器上的暂存空间和世界空间相同。 如果用户决定在相机顶部添加其他转换，则还需要将其发送到服务器以正确对齐本地和远程内容。

移动暂存空间的典型原因是[世界锁定工具](https://microsoft.github.io/MixedReality-WorldLockingTools-Unity/README.html)或其他真实世界定位点技术以及移动 VR 虚拟字符所需。

## <a name="stage-space-settings"></a>暂存空间设置

> [!CAUTION]
> 试验：此功能为实验性功能，会随时间而更改。 因此，可能还需额外升级代码以更新到较新的客户端 SDK 版本。 在当前实现中，更改暂存空间源时，会在短时间内出现本地/远程内容不对齐的情况。
因此，该实现目前仅用于世界锁定，例如随时间推移而发生较少更改的定位点。

若要通知服务器会将其他转换应用于暂存空间，则需要发送按位置定义的源以及世界空间中的旋转。 可以通过“暂存空间设置”访问此设置。

> [!IMPORTANT]
> 在[桌面模拟](../../concepts/graphics-bindings.md)中，相机的世界空间位置由用户应用程序提供。 在这种情况下，必须跳过暂存空间源设置，因为相机转换已成倍增加。

```cs
void ChangeStageSpace(RenderingSession session)
{
    StageSpaceSettings settings = session.Connection.StageSpaceSettings;

    // Set position and rotation to the world-space transform of the stage space.
    settings.Position = new Double3(0, 0, 0);
    settings.Rotation = new Quaternion(0, 0, 0, 1);
}
```

```cpp
void ChangeStageSpace(ApiHandle<RenderingSession> session)
{
    ApiHandle<StageSpaceSettings> settings = session->Connection()->GetStageSpaceSettings();

    // Set position and rotation to the world-space transform of the stage space.
    settings->SetPosition({0, 0, 0});
    settings->SetRotation({0, 0, 0, 1});
}
```

## <a name="unity-stage-space-script"></a>Unity 暂存空间脚本

Unity 集成提供了一个名为 StageSpace 的脚本，该脚本可添加到相机的父 GameObject。 成功添加后，此脚本将用于设置暂存空间源。

## <a name="next-steps"></a>后续步骤

* [图形绑定](../../concepts/graphics-bindings.md)
* [后期阶段重新投影](late-stage-reprojection.md)
