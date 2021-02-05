---
title: 照相机
description: 描述照相机设置和用例
author: christophermanthei
ms.author: chmant
ms.date: 03/07/2020
ms.topic: article
ms.openlocfilehash: dbe86313054706af974ccb324a39e942e9b5ca44
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594123"
---
# <a name="camera"></a>照相机

若要确保本地和远程呈现的内容可以无缝地合成，则各种相机属性需要在服务器和客户端之间保持同步。 最值得注意的是相机转换和投影。 例如，本地呈现的内容必须使用呈现最新远程帧所用的同一相机转换和投影。

![本地和远程照相机](./media/camera.png)

在上图中，本地相机已移动，与服务器发送的远程帧相比。 因此，需要从与远程帧相同的角度呈现本地内容，最终会将生成的图像 reprojected 到本地相机空间中，这会在 [后期阶段 reprojection](late-stage-reprojection.md)中详细说明。

远程和本地呈现之间的延迟意味着对这些设置进行的任何更改都将应用到延迟。 因此，不能将任何新值立即用于相同的帧。

可在相机设置上设置近和远平面距离。 在 HoloLens 2 上，剩余的转换和投影数据由硬件定义。 使用 [桌面模拟](../../concepts/graphics-bindings.md)时，会通过其功能的输入来设置它们 `Update` 。

在第一个远程帧到达后，就可以在本地使用更改的值。 在 HoloLens 2 上，用于呈现的数据是由 *HolographicFrame* 提供的，就像在任何其他全息应用程序中一样。 在 [桌面模拟](../../concepts/graphics-bindings.md)上，通过函数的输出来检索它们 `Update` 。

## <a name="camera-settings"></a>照相机设置

可以在 "相机设置" 上更改以下属性：

**近和远平面：**

若要确保不能设置无效的范围， **NearPlane** 和 **FarPlane** 属性是只读的，并且存在单独的函数 **SetNearAndFarPlane** 来更改范围。 此数据将发送到帧末尾的服务器。 设置这些值时， **NearPlane** 必须小于 **FarPlane**。 否则，将发生错误。

> [!IMPORTANT]
> 在 Unity 中，这是在更改靠近和远处的摄像机时自动处理的。

**EnableDepth**：

有时，出于调试目的禁用远程映像的深度缓冲区写入会很有帮助。 禁用深度还会使服务器停止发送深度数据，从而减少带宽。

> [!TIP]
> 在 Unity 中，提供了一个名为 **EnableDepthComponent** 的调试组件，可用于在编辑器 UI 中切换此功能。

**InverseDepth**：

> [!NOTE]
> 仅当设置为时，此设置才是重要的 `EnableDepth` `true` 。 否则，此设置不会产生任何影响。

深度缓冲区通常在 [0; 1] 的浮点范围内记录 z 值，0表示近平面深度，1表示最远的深度。 还可以反转此范围并记录范围 [1; 0] 中的深度值，也就是说，接近平面的深度为1，而远端平面的深度变为0。 通常，后者可改善跨非线性 z 范围的浮点精度分布。

> [!WARNING]
> 常见的方法是在照相机对象上反转近平面和 far 平面的值。 在上尝试此功能时，这会导致 Azure 远程呈现失败并出现错误 `CameraSettings` 。

Azure 远程呈现 API 需要知道本地呈现器的深度缓冲区约定，以便正确地将远程深度组合到本地深度缓冲区。 如果深度缓冲区范围为 [0; 1]，则将此标志保留为 `false` 。 如果使用带 [1; 0] 范围的反转深度缓冲区，请将标志设置 `InverseDepth` 为 `true` 。

> [!NOTE]
> 对于 Unity，正确的设置已被应用， `RenderingConnection` 因此无需手动干预。

可以按如下所示更改相机设置：

```cs
void ChangeCameraSetting(RenderingSession session)
{
    CameraSettings settings = session.Connection.CameraSettings;

    settings.SetNearAndFarPlane(0.1f, 20.0f);
    settings.EnableDepth = false;
    settings.InverseDepth = false;
}
```

```cpp
void ChangeCameraSetting(ApiHandle<RenderingSession> session)
{
    ApiHandle<CameraSettings> settings = session->Connection()->GetCameraSettings();

    settings->SetNearAndFarPlane(0.1f, 20.0f);
    settings->SetEnableDepth(false);
    settings->SetInverseDepth(false);
}
```

## <a name="api-documentation"></a>API 文档

* [C # CameraSettings](/dotnet/api/microsoft.azure.remoterendering.camerasettings)
* [C + + CameraSettings](/cpp/api/remote-rendering/camerasettings)
* [C # GraphicsBindingSimD3d11 函数](/dotnet/api/microsoft.azure.remoterendering.graphicsbindingsimd3d11.update)
* [C + + GraphicsBindingSimD3d11：： Update 函数](/cpp/api/remote-rendering/graphicsbindingsimd3d11#update)

## <a name="next-steps"></a>后续步骤

* [图形绑定](../../concepts/graphics-bindings.md)
* [后期阶段重新投影](late-stage-reprojection.md)