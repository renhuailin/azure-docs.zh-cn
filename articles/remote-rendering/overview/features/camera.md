---
title: 照相机
description: 介绍相机设置和用例
author: christophermanthei
ms.author: chmant
ms.date: 03/07/2020
ms.topic: article
ms.openlocfilehash: dbe86313054706af974ccb324a39e942e9b5ca44
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "99594123"
---
# <a name="camera"></a>照相机

为确保本地和远程渲染的内容可以无缝合成，各种相机属性需要在服务器和客户端之间保持同步。 最值得注意的是相机转换和投影。 例如，本地内容渲染必须与渲染最新远程帧使用相同的相机转换和投影。

![本地和远程相机](./media/camera.png)

在上图中，相比服务器发送的远程帧，本地相机已移动。 因此，本地内容需要从与远程帧相同的角度渲染，最后将生成的图像重新投影到本地相机空间。有关详情，请参阅[后期阶段重新投影](late-stage-reprojection.md)。

远程和本地渲染之间的延迟意味着，应用这些设置的任何更改都带有延迟。 因此，任何新值都不能立即在同一帧中使用。

在相机设置中可以设置近平面和远平面的距离。 在 HoloLens 2 上，剩余的转换和投影数据由硬件定义。 在使用[桌面模拟](../../concepts/graphics-bindings.md)时，可通过输入 `Update` 函数来设置它们。

在使用更改的值渲染的第一个远程帧到达后，就可以在本地使用它们。 在 HoloLens 2 上，用于渲染的数据就像在任何其他全息应用程序中一样，由 *HolographicFrame* 提供。 在[桌面模拟](../../concepts/graphics-bindings.md)上，可通过输出 `Update` 函数来检索它们。

## <a name="camera-settings"></a>相机设置

在相机设置中可以更改以下属性：

**近平面和远平面：**

为确保不能设置无效范围，NearPlane 和 FarPlane 属性都是只读状态，并使用单独的函数 SetNearAndFarPlane 来更改范围。 这些数据将发送到帧末尾的服务器。 设置这些值时，NearPlane 必须小于 FarPlane。 否则将会出错。

> [!IMPORTANT]
> 在 Unity 中更改主相机的近平面和远平面时，系统会自动处理此操作。

**EnableDepth**：

有时，禁用远程映像的深度缓冲区写入有利于进行调试。 禁用深度还会使服务器停止发送深度数据，从而减少带宽。

> [!TIP]
> 在 Unity 中会提供一个名为 **EnableDepthComponent** 的调试组件，可用于在编辑器 UI 中切换此功能。

**InverseDepth**：

> [!NOTE]
> 只有 `EnableDepth` 设置为 `true` 时，此设置才比较重要。 否则，此设置不会产生任何影响。

深度缓冲区通常在 [0;1] 的浮点范围内记录 z 值，0 表示近平面深度，1 表示远平面深度。 此外，还可以反转此范围并在 [1;0] 范围内记录深度值，即近平面深度为 1，远平面深度为 0。 通常，后者可改善非线性 z 范围内的浮点精度分布。

> [!WARNING]
> 常用方法是对相机对象反转近平面和远平面的值。 对于 Azure 远程渲染，在 `CameraSettings` 上尝试此操作会失败出错。

Azure 远程渲染 API 需要知道本地渲染器的深度缓冲区约定，以便将远程深度正确编写至本地深度缓冲区。 如果深度缓冲区范围为 [0;1]，则将此标志保留为 `false`。 如果使用范围为 [1;0] 的反转深度缓冲区，请将 `InverseDepth` 标志设置为 `true`。

> [!NOTE]
> 对于 Unity，`RenderingConnection` 已应用正确设置，因此无需手动干预。

更改相机设置的方式如下所示：

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

* [C# CameraSettings](/dotnet/api/microsoft.azure.remoterendering.camerasettings)
* [C++ CameraSettings](/cpp/api/remote-rendering/camerasettings)
* [C# GraphicsBindingSimD3d11.Update 函数](/dotnet/api/microsoft.azure.remoterendering.graphicsbindingsimd3d11.update)
* [C++ GraphicsBindingSimD3d11::Update 函数](/cpp/api/remote-rendering/graphicsbindingsimd3d11#update)

## <a name="next-steps"></a>后续步骤

* [图形绑定](../../concepts/graphics-bindings.md)
* [后期阶段重新投影](late-stage-reprojection.md)