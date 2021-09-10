---
title: 图形绑定
description: 图形绑定设置和用例
author: florianborn71
manager: jlyons
services: azure-remote-rendering
titleSuffix: Azure Remote Rendering
ms.author: flborn
ms.date: 12/11/2019
ms.topic: conceptual
ms.service: azure-remote-rendering
ms.custom: devx-track-csharp
ms.openlocfilehash: 4c9bfc12a1af68e56aacea5b73bd2b4ddc6f857c
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123221597"
---
# <a name="graphics-binding"></a>图形绑定

要在自定义应用程序中使用 Azure 远程渲染，需要将其集成到应用程序的渲染管道中。 此集成可通过图形绑定完成。

设置后，可通过图形绑定访问影响渲染图像的各种函数。 这些函数可以分为两类：始终可用的常规函数和仅与所选 `Microsoft.Azure.RemoteRendering.GraphicsApiType` 相关的特定函数。

## <a name="graphics-binding-in-unity"></a>Unity 中的图形绑定

在 Unity 中，整个绑定由传递到 `RemoteManagerUnity.InitializeManager` 中的 `RemoteUnityClientInit` 结构处理。 要设置图形模式，必须将 `GraphicsApiType` 字段设置为所选绑定。 该字段将根据是否存在 XRDevice 自动填充。 该行为可手动替代为以下行为：

* HoloLens 2：根据活动 Unity XR 插件使用 [OpenXR](#openxr) 或 [Windows 混合现实](#windows-mixed-reality)图形绑定。
* **平面 UWP 桌面应用**：始终使用 [模拟](#simulation)。
* **Unity 编辑器**：始终使用 [模拟](#simulation)，除非连接了 WMR VR 耳机，在这种情况下，将禁用 ARR，以便调试应用程序的非 ARR 相关部分。 另请参阅[全息远程处理](../how-tos/unity/holographic-remoting.md)。

Unity 的唯一其他相关部分是访问[基本绑定](#access)，可跳过下面的所有其他部分。

## <a name="graphics-binding-setup-in-custom-applications"></a>自定义应用程序中的图形绑定设置

要选择图形绑定，请执行以下两个步骤：首先，初始化程序时，必须以静态方式初始化图形绑定：

```cs
RemoteRenderingInitialization managerInit = new RemoteRenderingInitialization();
managerInit.GraphicsApi = GraphicsApiType.OpenXrD3D11;
managerInit.ConnectionType = ConnectionType.General;
managerInit.Right = ///...
RemoteManagerStatic.StartupRemoteRendering(managerInit);
```

```cpp
RemoteRenderingInitialization managerInit;
managerInit.GraphicsApi = GraphicsApiType::OpenXrD3D11;
managerInit.ConnectionType = ConnectionType::General;
managerInit.Right = ///...
StartupRemoteRendering(managerInit); // static function in namespace Microsoft::Azure::RemoteRendering

```
在访问任何其他远程渲染 API 之前，都必须先进行上述调用。
同样，在销毁所有其他远程渲染对象之后，应调用相应的 de-init 函数 `RemoteManagerStatic.ShutdownRemoteRendering();`。
对于 WMR，还需要在调用任何全息 API 之前调用 `StartupRemoteRendering`。 对于 OpenXR，同样适用于任何 OpenXR 相关的 API。

## <a name="span-idaccessaccessing-graphics-binding"></a><span id="access">访问图形绑定

设置客户端后，可以使用 `RenderingSession.GraphicsBinding` getter 访问基本图形绑定。 例如，可以按如下所示检索最后一帧的统计信息：

```cs
RenderingSession currentSession = ...;
if (currentSession.GraphicsBinding != null)
{
    FrameStatistics frameStatistics;
    if (currentSession.GraphicsBinding.GetLastFrameStatistics(out frameStatistics) == Result.Success)
    {
        ...
    }
}
```

```cpp
ApiHandle<RenderingSession> currentSession = ...;
if (ApiHandle<GraphicsBinding> binding = currentSession->GetGraphicsBinding())
{
    FrameStatistics frameStatistics;
    if (binding->GetLastFrameStatistics(&frameStatistics) == Result::Success)
    {
        ...
    }
}
```

## <a name="graphic-apis"></a>图形 API

目前可以选择三个图形 API：`OpenXrD3D11`、`WmrD3D11` 和 `SimD3D11`。 还有第四个图形 API：`Headless`，但客户端尚不支持。

### <a name="openxr"></a>OpenXR

`GraphicsApiType.OpenXrD3D11` 是要在 HoloLens 2 上运行的默认绑定。 它将创建 `GraphicsBindingOpenXrD3d11` 绑定。 在此模式下，Azure 远程渲染会创建一个 OpenXR API 层，用于将自身集成到 OpenXR 运行时中。

要访问派生的图形绑定，必须强制转换基本 `GraphicsBinding`。
若要使用 OpenXR 绑定，需要完成三个任务：

#### <a name="package-custom-openxr-layer-json"></a>打包自定义 OpenXR 层 JSON

若要将远程渲染与 OpenXR 结合使用，需要激活自定义 OpenXR API 层。 这是通过调用上一部分中提到的 `StartupRemoteRendering` 来完成的。 但是，作为先决条件，`XrApiLayer_msft_holographic_remoting.json` 需要与应用程序一起打包，以便我们加载它。 如果将“Microsoft.Azure.RemoteRendering.Cpp”NuGet 包添加到项目中，则会自动执行此操作。

#### <a name="inform-remote-rendering-of-the-used-xr-space"></a>通知所用 XR 空间的远程渲染

为了使远程和本地渲染的内容保持一致，需要这样做。

```cs
RenderingSession currentSession = ...;
ulong space = ...; // XrSpace cast to ulong
GraphicsBindingOpenXrD3d11 openXrBinding = (currentSession.GraphicsBinding as GraphicsBindingOpenXrD3d11);
if (openXrBinding.UpdateAppSpace(space) == Result.Success)
{
    ...
}
```

```cpp
ApiHandle<RenderingSession> currentSession = ...;
XrSpace space = ...;
ApiHandle<GraphicsBindingOpenXrD3d11> openXrBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingOpenXrD3d11>();
#ifdef _M_ARM64
    if (openXrBinding->UpdateAppSpace(reinterpret_cast<uint64_t>(space)) == Result::Success)
#else
    if (openXrBinding->UpdateAppSpace(space) == Result::Success)
#endif
{
    ...
}
```

上面的 `XrSpace` 是由应用程序使用的，它定义了表示 API 中坐标的世界空间坐标系统。

#### <a name="render-remote-image-openxr"></a>渲染远程图像 (OpenXR)

在每个帧开始时，需要将远程帧渲染到后台缓冲区中。 可通过调用 `BlitRemoteFrame` 来完成此操作，这会将双眼的颜色和深度信息填充到当前绑定的渲染目标。 因此，在将完整的后台缓冲区绑定为渲染目标之后执行此操作非常重要。

> [!WARNING]
> 在将远程图像以调用 blit 参数的方式渲染到后台缓冲区之后，应该使用单通道立体渲染技术（例如使用 **SV_RenderTargetArrayIndex**）来渲染本地内容。 其他立体渲染技术（例如，在单独的通道中渲染每只眼睛）可能会导致严重的性能下降或图形伪影，应避免使用。

```cs
RenderingSession currentSession = ...;
GraphicsBindingOpenXrD3d11 openXrBinding = (currentSession.GraphicsBinding as GraphicsBindingOpenXrD3d11);
openXrBinding.BlitRemoteFrame();
```

```cpp
ApiHandle<RenderingSession> currentSession = ...;
ApiHandle<GraphicsBindingOpenXrD3d11> openXrBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingOpenXrD3d11>();
openXrBinding->BlitRemoteFrame();
```

### <a name="windows-mixed-reality"></a>Windows Mixed Reality

`GraphicsApiType.WmrD3D11` 是以前使用的图形绑定，在 HoloLens 2 上运行。 它将创建 `GraphicsBindingWmrD3d11` 绑定。 在此模式下，Azure 远程渲染直接连接到全息 API。

要访问派生的图形绑定，必须强制转换基本 `GraphicsBinding`。
要使用 WMR 绑定，需要执行以下两项操作：

#### <a name="inform-remote-rendering-of-the-used-coordinate-system"></a>通知所用坐标系统的远程渲染

为了使远程和本地渲染的内容保持一致，需要这样做。

```cs
RenderingSession currentSession = ...;
IntPtr ptr = ...; // native pointer to ISpatialCoordinateSystem
GraphicsBindingWmrD3d11 wmrBinding = (currentSession.GraphicsBinding as GraphicsBindingWmrD3d11);
if (wmrBinding.UpdateUserCoordinateSystem(ptr) == Result.Success)
{
    ...
}
```

```cpp
ApiHandle<RenderingSession> currentSession = ...;
void* ptr = ...; // native pointer to ISpatialCoordinateSystem
ApiHandle<GraphicsBindingWmrD3d11> wmrBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingWmrD3d11>();
if (wmrBinding->UpdateUserCoordinateSystem(ptr) == Result::Success)
{
    ...
}
```

上面的 `ptr` 必须是一个指向本机 `ABI::Windows::Perception::Spatial::ISpatialCoordinateSystem` 对象的指针，该对象定义了表示 API 中坐标的世界空间坐标系统。

#### <a name="render-remote-image-wmr"></a>渲染远程图像 (WMR)

上述 OpenXR 案例中的注意事项在这里同样适用。 API 调用如下所示：

```cs
RenderingSession currentSession = ...;
GraphicsBindingWmrD3d11 wmrBinding = (currentSession.GraphicsBinding as GraphicsBindingWmrD3d11);
wmrBinding.BlitRemoteFrame();
```

```cpp
ApiHandle<RenderingSession> currentSession = ...;
ApiHandle<GraphicsBindingWmrD3d11> wmrBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingWmrD3d11>();
wmrBinding->BlitRemoteFrame();
```

### <a name="simulation"></a>模拟

`GraphicsApiType.SimD3D11` 是模拟绑定，如果选择，它将创建 `GraphicsBindingSimD3d11` 图形绑定。 此界面用于模拟头部运动，例如，在桌面应用程序中，以及渲染单视场图像时。

若要实现模拟绑定，必须了解本地相机与远程帧之间的差异（如[相机](../overview/features/camera.md)页面所述）。

需要两个相机：

* **本地相机**：此相机表示由应用程序逻辑驱动的当前相机位置。
* **代理相机**：此相机与服务器发送的当前远程帧匹配。 由于在客户端请求某个帧与该帧到达之间有一个时间延迟，因此在移动上远程帧总是比本地相机慢一点。

此处的基本方法是使用代理相机将远程图像和本地内容渲染成一个屏幕外目标。 然后，代理图像会重新投影到本地相机空间，[后期重投影](../overview/features/late-stage-reprojection.md)中进一步介绍了此内容。

`GraphicsApiType.SimD3D11` 还支持立体渲染，在下面进行 `InitSimulation` 设置调用期间需要启用此功能。 设置过程要稍微复杂一点，其工作方式如下：

#### <a name="create-proxy-render-target"></a>设置代理渲染目标

需要使用 `GraphicsBindingSimD3d11.Update` 函数提供的代理照相机数据，将远程和本地内容渲染到名为“代理”的屏幕外颜色/深度渲染目标。

代理必须与后台缓冲区的分辨率匹配，并且应当采用 DXGI_FORMAT_R8G8B8A8_UNORM 或 DXGI_FORMAT_B8G8R8A8_UNORM 格式。 在进行立体渲染的情况下，颜色代理纹理和深度代理纹理（如果使用深度）都需要有两个阵列层而不是一个。 会话准备就绪后，需要先调用 `GraphicsBindingSimD3d11.InitSimulation` 才可连接到该会话：

```cs
RenderingSession currentSession = ...;
IntPtr d3dDevice = ...; // native pointer to ID3D11Device
IntPtr color = ...; // native pointer to ID3D11Texture2D
IntPtr depth = ...; // native pointer to ID3D11Texture2D
float refreshRate = 60.0f; // Monitor refresh rate up to 60hz.
bool flipBlitRemoteFrameTextureVertically = false;
bool flipReprojectTextureVertically = false;
bool stereoscopicRendering = false;
GraphicsBindingSimD3d11 simBinding = (currentSession.GraphicsBinding as GraphicsBindingSimD3d11);
simBinding.InitSimulation(d3dDevice, depth, color, refreshRate, flipBlitRemoteFrameTextureVertically, flipReprojectTextureVertically, stereoscopicRendering);
```

```cpp
ApiHandle<RenderingSession> currentSession = ...;
void* d3dDevice = ...; // native pointer to ID3D11Device
void* color = ...; // native pointer to ID3D11Texture2D
void* depth = ...; // native pointer to ID3D11Texture2D
float refreshRate = 60.0f; // Monitor refresh rate up to 60hz.
bool flipBlitRemoteFrameTextureVertically = false;
bool flipReprojectTextureVertically = false;
bool stereoscopicRendering = false;
ApiHandle<GraphicsBindingSimD3d11> simBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingSimD3d11>();
simBinding->InitSimulation(d3dDevice, depth, color, refreshRate, flipBlitRemoteFrameTextureVertically, flipReprojectTextureVertically, stereoscopicRendering);
```

需要为 init 函数提供指向本机 d3d 设备以及代理渲染目标的颜色和深度纹理的指针。 初始化后，可以多次调用 `RenderingSession.ConnectAsync` 和 `Disconnect`，但切换到不同会话时，需要先在旧会话上调用 `GraphicsBindingSimD3d11.DeinitSimulation`，然后才能在另一个会话上调用 `GraphicsBindingSimD3d11.InitSimulation`。

#### <a name="render-loop-update"></a>渲染循环更新

渲染循环更新包含多个步骤：

1. 在对每个帧进行任何渲染之前，将使用发送到服务器进行渲染的最新照相机转换调用 `GraphicsBindingSimD3d11.Update`。 同时，应将返回的代理转换应用于代理照相机，以渲染到代理渲染目标中。
如果返回的代理更新 `SimulationUpdate.frameId` 为 NULL，则还没有远程数据。 在这种情况下，应使用最新照相机数据直接将任何本地内容渲染到后台缓冲区（而不是渲染到代理渲染目标中），并跳过接下来的两个步骤。
1. 现在，应用程序应绑定代理渲染目标并调用 `GraphicsBindingSimD3d11.BlitRemoteFrameToProxy`。 这会将远程颜色和深度信息填充到代理渲染目标。 现在可以使用代理照相机转换将任何本地内容渲染到代理上。
1. 接下来，需要将后台缓冲区绑定为渲染目标，并调用 `GraphicsBindingSimD3d11.ReprojectProxy`，此时可以显示后台缓冲区。

```cs
RenderingSession currentSession = ...;
GraphicsBindingSimD3d11 simBinding = (currentSession.GraphicsBinding as GraphicsBindingSimD3d11);
SimulationUpdateParameters updateParameters = new SimulationUpdateParameters();
// Fill out camera data with current camera data
// (see "Simulation Update structures" section below)
...
SimulationUpdateResult updateResult = new SimulationUpdateResult();
simBinding.Update(updateParameters, out updateResult);
// Is the frame data valid?
if (updateResult.FrameId != 0)
{
    // Bind proxy render target
    simBinding.BlitRemoteFrameToProxy();
    // Use proxy camera data to render local content
    ...
    // Bind back buffer
    simBinding.ReprojectProxy();
}
else
{
    // Bind back buffer
    // Use current camera data to render local content
    ...
}
```

```cpp
ApiHandle<RenderingSession> currentSession;
ApiHandle<GraphicsBindingSimD3d11> simBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingSimD3d11>();

SimulationUpdateParameters updateParameters;
// Fill out camera data with current camera data
// (see "Simulation Update structures" section below)
...
SimulationUpdateResult updateResult;
simBinding->Update(updateParameters, &updateResult);
// Is the frame data valid?
if (updateResult.FrameId != 0)
{
    // Bind proxy render target
    simBinding->BlitRemoteFrameToProxy();
    // Use proxy camera data to render local content
    ...
    // Bind back buffer
    simBinding->ReprojectProxy();
}
else
{
    // Bind back buffer
    // Use current camera data to render local content
    ...
}
```

#### <a name="simulation-update-structures"></a>模拟更新结构

对于每一帧，上一部分的“渲染循环更新”要求你输入与本地相机对应的相机参数范围，并返回与下一可用帧的相机对应的相机参数集。 这两个集分别在 `SimulationUpdateParameters` 和 `SimulationUpdateResult` 结构中捕获：

```cs
public struct SimulationUpdateParameters
{
    public int FrameId;
    public StereoMatrix4x4 ViewTransform;
    public StereoCameraFov FieldOfView;
};

public struct SimulationUpdateResult
{
    public int FrameId;
    public float NearPlaneDistance;
    public float FarPlaneDistance;
    public StereoMatrix4x4 ViewTransform;
    public StereoCameraFov FieldOfView;
};
```

结构成员具有以下含义：

| 成员 | 说明 |
|--------|-------------|
| FrameId | 连续帧标识符。 是 SimulationUpdateParameters 输入所需，并且需要针对每个新帧连续递增。 如果尚无帧数据可用，则在 SimulationUpdateResult 中为 0。 |
| ViewTransform | 帧的相机视图变换矩阵的“左-右-立体”对。 对于单像渲染，只有 `Left` 成员有效。 |
| FieldOfView | [OpenXR 视野约定](https://www.khronos.org/registry/OpenXR/specs/1.0/html/xrspec.html#angles)中帧相机的视野的“左-右-立体”对。 对于单像渲染，只有 `Left` 成员有效。 |
| NearPlaneDistance | 用于当前远程帧的投影矩阵的近平面距离。 |
| FarPlaneDistance | 用于当前远程帧的投影矩阵的远平面距离。 |

立体对 `ViewTransform` 和 `FieldOfView` 允许在启用立体渲染的情况下设置两个 eye-camera 值。 否则，将忽略 `Right` 成员。 如你所见，仅以普通 4x4 变换矩阵的形式传递了相机的变换，而没有指定投影矩阵。 实际矩阵由 Azure 远程渲染使用指定的视野以及在 [CameraSettings API](../overview/features/camera.md) 上设置的当前近平面和远平面在内部计算。

由于你可以在运行时根据需要在 [CameraSettings](../overview/features/camera.md) 上更改近平面和远平面，并且服务将以异步方式应用这些设置，因此每个 SimulationUpdateResult 还包含在渲染相应帧期间使用的特定近平面和远平面。 你可以使用那些平面值来调整投影矩阵，以便渲染与远程帧渲染匹配的本地对象。

最后，尽管 **模拟更新** 调用需要 OpenXR 约定中的视野，但出于标准化和算法安全原因，你可以使用以下结构填充示例中所示的转换函数：

```cs
public SimulationUpdateParameters CreateSimulationUpdateParameters(int frameId, Matrix4x4 viewTransform, Matrix4x4 projectionMatrix)
{
    SimulationUpdateParameters parameters = default;
    parameters.FrameId = frameId;
    parameters.ViewTransform.Left = viewTransform;
    if (parameters.FieldOfView.Left.FromProjectionMatrix(projectionMatrix) != Result.Success)
    {
        // Invalid projection matrix
        throw new ArgumentException("Invalid projection settings");
    }
    return parameters;
}

public void GetCameraSettingsFromSimulationUpdateResult(SimulationUpdateResult result, out Matrix4x4 projectionMatrix, out Matrix4x4 viewTransform, out int frameId)
{
    projectionMatrix = default;
    viewTransform = default;
    frameId = 0;

    if (result.FrameId == 0)
    {
        // Invalid frame data
        return;
    }

    // Use the screenspace depth convention you expect for your projection matrix locally
    if (result.FieldOfView.Left.ToProjectionMatrix(result.NearPlaneDistance, result.FarPlaneDistance, DepthConvention.ZeroToOne, out projectionMatrix) != Result.Success)
    {
        // Invalid field-of-view
        return;
    }
    viewTransform = result.ViewTransform.Left;
    frameId = result.FrameId;
}
```

```cpp
SimulationUpdateParameters CreateSimulationUpdateParameters(uint32_t frameId, Matrix4x4 viewTransform, Matrix4x4 projectionMatrix)
{
    SimulationUpdateParameters parameters;
    parameters.FrameId = frameId;
    parameters.ViewTransform.Left = viewTransform;
    if (FovFromProjectionMatrix(projectionMatrix, parameters.FieldOfView.Left) != Result::Success)
    {
        // Invalid projection matrix
        return {};
    }
    return parameters;
}

void GetCameraSettingsFromSimulationUpdateResult(const SimulationUpdateResult& result, Matrix4x4& projectionMatrix, Matrix4x4& viewTransform, uint32_t& frameId)
{
    if (result.FrameId == 0)
    {
        // Invalid frame data
        return;
    }

    // Use the screenspace depth convention you expect for your projection matrix locally
    if (FovToProjectionMatrix(result.FieldOfView.Left, result.NearPlaneDistance, result.FarPlaneDistance, DepthConvention::ZeroToOne, projectionMatrix) != Result::Success)
    {
        // Invalid field-of-view
        return;
    }
    viewTransform = result.ViewTransform.Left;
    frameId = result.FrameId;
}
```

这些转换函数允许在视野规范和普通 4x4 透视投影矩阵之间快速切换，具体取决于你对本地渲染的需求。 这些转换函数包含验证逻辑，在输入投影矩阵或输入视野无效的情况下会返回错误，不设置有效结果。

## <a name="api-documentation"></a>API 文档

* [C# RemoteManagerStatic.StartupRemoteRendering()](/dotnet/api/microsoft.azure.remoterendering.remotemanagerstatic.startupremoterendering)
* [C# GraphicsBinding 类](/dotnet/api/microsoft.azure.remoterendering.graphicsbinding)
* [C# GraphicsBindingWmrD3d11 类](/dotnet/api/microsoft.azure.remoterendering.graphicsbindingwmrd3d11)
* [C# GraphicsBindingSimD3d11 类](/dotnet/api/microsoft.azure.remoterendering.graphicsbindingsimd3d11)
* [C++ RemoteRenderingInitialization 结构](/cpp/api/remote-rendering/remoterenderinginitialization)
* [C++ GraphicsBinding 类](/cpp/api/remote-rendering/graphicsbinding)
* [C++ GraphicsBindingWmrD3d11 类](/cpp/api/remote-rendering/graphicsbindingwmrd3d11)
* [C++ GraphicsBindingSimD3d11 类](/cpp/api/remote-rendering/graphicsbindingsimd3d11)

## <a name="next-steps"></a>后续步骤

* [摄像头](../overview/features/camera.md)
* [后期阶段重新投影](../overview/features/late-stage-reprojection.md)
* [教程：查看远程渲染的模型](../tutorials/unity/view-remote-models/view-remote-models.md)