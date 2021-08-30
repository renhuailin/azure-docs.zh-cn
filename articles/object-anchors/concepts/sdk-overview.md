---
title: 运行时 SDK 概述
description: 熟悉 Object Anchors 运行时 SDK。
author: craigktreasure
manager: rgarcia
services: azure-object-anchors
ms.author: crtreasu
ms.date: 07/23/2021
ms.topic: conceptual
ms.service: azure-object-anchors
ms.openlocfilehash: bc197af9be61a98be1cb22c7ff9fd0e97c31fcbe
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2021
ms.locfileid: "114710745"
---
# <a name="runtime-sdk-overview"></a>运行时 SDK 概述

本部分简要概述了 Object Anchors 运行时 SDK，该 SDK 用来检测那些使用 Object Anchors 模型的对象。 你将了解对象的表示方式以及各种组件的用途。

下面描述的所有类型都可在以下某个命名空间中找到：Microsoft.Azure.ObjectAnchors、Microsoft.Azure.ObjectAnchors.Diagnostics 和 Microsoft.Azure.ObjectAnchors.SpatialGraph  。

## <a name="types"></a>类型

### <a name="objectmodel"></a>ObjectModel

[ObjectModel](/dotnet/api/microsoft.azure.objectanchors.objectmodel) 表示物理对象的几何体，并对进行检测和姿态估计所需的参数编码。 必须使用 [Object Anchors 服务](../quickstarts/get-started-model-conversion.md)来创建它。 然后，应用程序可以使用 Object Anchors API 加载生成的模型文件，并查询嵌入该模型中的网格以进行可视化。

### <a name="objectsearcharea"></a>ObjectSearchArea

[ObjectSearchArea](/dotnet/api/microsoft.azure.objectanchors.objectsearcharea) 指定用于查找一个或多个对象的空间。 它是由空间图节点 ID 以及空间图节点 ID 在坐标系中表示的空间边界定义的。 Object Anchors 运行时 SDK 支持四种类型的边界，即“视野”、“边界框”、“范围”和“位置”。

### <a name="accountinformation"></a>AccountInformation

[AccountInformation](/dotnet/api/microsoft.azure.objectanchors.accountinformation) 存储 Azure Object Anchors 帐户的 ID、密钥和域。

### <a name="objectanchorssession"></a>ObjectAnchorsSession

[ObjectAnchorsSession](/dotnet/api/microsoft.azure.objectanchors.objectanchorssession) 表示用于创建 ObjectObserver 实例的 Azure Object Anchors 会话，该实例用于检测物理世界中的物体。

### <a name="objectobserver"></a>ObjectObserver

[ObjectObserver](/dotnet/api/microsoft.azure.objectanchors.objectobserver) 会加载对象模型，检测其实例，并在 HoloLens 坐标系统中报告每个实例的 6-DoF 姿态。

尽管任何对象模型或实例都是从观察器创建的，但其生存期是独立的。 应用程序可以处置观察器，然后继续使用对象模型或实例。

### <a name="objectquery"></a>ObjectQuery

[ObjectQuery](/dotnet/api/microsoft.azure.objectanchors.objectquery) 告诉对象观察器如何查找给定模型的对象。 它提供以下可优化的参数，你可以从对象模型中检索这些参数的默认值。

#### <a name="minsurfacecoverage"></a>MinSurfaceCoverage

[MinSurfaceCoverage](/dotnet/api/microsoft.azure.objectanchors.objectquery.minsurfacecoverage) 属性指示值为多少时将实例视为检测到的实例。

对于每个候选对象，观察器会计算转换后的对象模型与场景之间的重叠曲面的比率，仅当覆盖率高于给定阈值时才将该候选对象报告给应用程序。

#### <a name="isexpectedtobestandingongroundplane"></a>IsExpectedToBeStandingOnGroundPlane

[IsExpectedToBeStandingOnGroundPlane](/dotnet/api/microsoft.azure.objectanchors.objectquery.isexpectedtobestandingongroundplane) 属性指示目标对象是否应位于地平面上。

地平面是搜索区域中最低的水平地面。 它对可能的对象姿态提供良好的约束。 启用此标志会引导观察器在有限的空间内估计姿态，并且可以提高准确度。 如果模型不应位于地平面上，则此参数会被忽略。

#### <a name="expectedmaxverticalorientationindegrees"></a>ExpectedMaxVerticalOrientationInDegrees

[ExpectedMaxVerticalOrientationInDegrees](/dotnet/api/microsoft.azure.objectanchors.objectquery.expectedmaxverticalorientationindegrees) 属性指示对象实例的向上方向与重力之间的预期最大角度（以度为单位）。

此参数为某个估计姿态的向上方向提供另一个约束。 例如，如果对象是直立的，则此参数可以为 0。 Object Anchors 不应该检测与模型不同的对象。 如果模型是直立的，则不会检测平放的实例。 将对平放布局使用新模型。
同一规则适用于接合处。

#### <a name="maxscalechange"></a>MaxScaleChange

[MaxScaleChange](/dotnet/api/microsoft.azure.objectanchors.objectquery.maxscalechange) 属性表示涉及空间映射的最大对象比例变化（在 0 ~ 1 范围内）。 估计的比例应用于以原点为中心和轴对齐的转换后的对象顶点。 估计的比例可能不是 CAD 模型与其物理表示形式之间的实际比例，而是一些允许应用用来尽量按照物理对象上的空间映射来渲染对象模型的值。

#### <a name="searchareas"></a>SearchAreas

[SearchAreas](/dotnet/api/microsoft.azure.objectanchors.objectquery.searchareas) 属性指示要在其中查找对象的空间边界数组。

观察器会在查询所指定的所有搜索区域的联合空间中查找对象。 在此版本中，我们将最多返回一个具有最高置信度的对象来降低延迟。

### <a name="objectinstance"></a>ObjectInstance

[ObjectInstance](/dotnet/api/microsoft.azure.objectanchors.objectinstance) 表示给定模型的实例在 HoloLens 坐标系中可能会处于的假设位置。 每个实例都有一个 `SurfaceCoverage` 属性，用于指示估计的姿态有多准确。

实例通过调用 `ObjectObserver.DetectAsync` 方法创建，然后在活动时在后台自动进行更新。 应用程序可以侦听特定实例上的状态更改事件，或者更改跟踪模式以暂停/继续更新。 当跟踪丢失时，系统会自动将实例从观察器中删除。

### <a name="objectdiagnosticssession"></a>ObjectDiagnosticsSession

[ObjectDiagnosticSession](/dotnet/api/microsoft.azure.objectanchors.diagnostics.objectdiagnosticssession) 记录诊断，并将数据写入存档。

诊断存档包括场景点云、观察器的状态，以及有关模型的信息。 此信息可用于确定可能的运行时问题。 有关详细信息，请参阅[常见问题解答](../faq.md)。

## <a name="runtime-sdk-usage-and-details"></a>运行时 SDK 用法和详细信息

本部分应当提供有关如何使用运行时 SDK 的基本信息。 它应该为你提供足够的上下文来浏览示例应用程序，以全面了解如何使用 Object Anchors。

### <a name="initialization"></a>初始化

在使用 Object Anchors 之前，应用程序需要调用 `ObjectObserver.IsSupported()` API 来确定设备是否支持它。 如果 `ObjectObserver.IsSupported()` API 返回了 `false`，请检查应用程序是否已启用 spatialPerception 功能，并/或升级到最新的 HoloLens OS。

```cs
using Microsoft.Azure.ObjectAnchors;

if (!ObjectObserver.IsSupported())
{
    // Handle the error
}

// This call should grant the access we need.
ObjectObserverAccessStatus status = await ObjectObserver.RequestAccessAsync();
if (status != ObjectObserverAccessStatus.Allowed)
{
    // Handle the error
}
```

接下来，应用程序将创建一个对象观察器并加载由 [Object Anchors 模型转换服务](../quickstarts/get-started-model-conversion.md)生成的必要模型。

```cs
using Microsoft.Azure.ObjectAnchors;

// Note that you need to provide the Id, Key and Domain for your Azure Object
// Anchors account.
Guid accountId = new Guid("[your account id]");
string accountKey = "[your account key]";
string accountDomain = "[your account domain]";

AccountInformation accountInformation = new AccountInformation(accountId, accountKey, accountDomain);
ObjectAnchorsSession session = new ObjectAnchorsSession(accountInformation);
ObjectObserver observer = session.CreateObjectObserver();

// Load a model into a byte array. The model could be a file, an embedded
// resource, or a network stream.
byte[] modelAsBytes;
ObjectModel model = await observer.LoadObjectModelAsync(modelAsBytes);

// Note that after a model is loaded, its vertices and normals are transformed
// into a centered coordinate system for the ease of computing the object pose.
// The rigid transform can be retrieved through the `OriginToCenterTransform`
// property.
```

应用程序会创建一个查询，用于检测某个空间内该模型的实例。

```cs
using Microsoft.Azure.ObjectAnchors;
using Microsoft.Azure.ObjectAnchors.SpatialGraph;
using Microsoft.Azure.ObjectAnchors.Unity;
using UnityEngine;

// Get the coordinate system.
SpatialGraphCoordinateSystem? coordinateSystem = null;

#if WINDOWS_UWP
SpatialCoordinateSystem worldOrigin = ObjectAnchorsWorldManager.WorldOrigin;
if (worldOrigin != null)
{
    coordinateSystem = await Task.Run(() => worldOrigin.TryToSpatialGraph());
}
#endif

if (!coordinateSystem.HasValue)
{
    Debug.LogError("no coordinate system?");
    return;
}

// Get the search area.
SpatialFieldOfView fieldOfView = new SpatialFieldOfView
{
    Position = Camera.main.transform.position.ToSystem(),
    Orientation = Camera.main.transform.rotation.ToSystem(),
    FarDistance = 4.0f, // Far distance in meters of object search frustum.
    HorizontalFieldOfViewInDegrees = 75.0f, // Horizontal field of view in
                                            // degrees of object search frustum.
    AspectRatio = 1.0f // Aspect ratio (horizontal / vertical) of object search
                       // frustum.
};

ObjectSearchArea searchArea = ObjectSearchArea.FromFieldOfView(coordinateSystem.Value, fieldOfView);

// Optionally change the parameters, otherwise use the default values embedded
// in the model.
ObjectQuery query = new ObjectQuery(model);
query.MinSurfaceCoverage = 0.2f;
query.ExpectedMaxVerticalOrientationInDegrees = 1.5f;
query.MaxScaleChange = 0.1f;
query.SearchAreas.Add(searchArea);

// Detection could take a while, so we run it in a background thread.
IList<ObjectInstance> detectedObjects = await observer.DetectAsync(query);
```

默认情况下，观察器会自动跟踪检测到的每个实例。 我们还可以选择通过更改这些实例的跟踪模式或侦听其状态更改事件来操作这些实例。

```cs
using Microsoft.Azure.ObjectAnchors;

foreach (ObjectInstance instance in detectedObjects)
{
    // Supported modes:
    // "LowLatencyCoarsePosition"    - Consumes less CPU cycles thus fast to
    //                                 update the state.
    // "HighLatencyAccuratePosition" - (Not yet implemented) Consumes more CPU
    //                                 cycles thus potentially taking longer
    //                                 time to update the state.
    // "Paused"                      - Stops to update the state until mode
    //                                 changed to low or high.
    instance.Mode = ObjectInstanceTrackingMode.LowLatencyCoarsePosition;

    // Listen to state changed event on this instance.
    instance.Changed += InstanceChangedHandler;

    // Optionally dispose an instance if not interested in it.
    // instance.Dispose();
}
```

在状态更改事件中，我们可以查询最新状态，或在实例丢失跟踪的情况下处置实例。

```cs
using Microsoft.Azure.ObjectAnchors;

var InstanceChangedHandler = new Windows.Foundation.TypedEventHandler<ObjectInstance, ObjectInstanceChangedEventArgs>((sender, args) =>
{
    // Try to query the current instance state.
    ObjectInstanceState? state = sender.TryGetCurrentState();

    if (state.HasValue)
    {
        // Process latest state via state.Value.
        // An object pose includes scale, rotation and translation, applied in
        // the same order to the object model in the centered coordinate system.
    }
    else
    {
        // This object instance is lost for tracking, and will never be recovered.
        // The caller can detach the Changed event handler from this instance
        // and dispose it.
        sender.Dispose();
    }
});
```

此外，应用程序还可以选择记录一个或多个诊断会话，以便进行脱机调试。

```cs
using Microsoft.Azure.ObjectAnchors;
using Microsoft.Azure.ObjectAnchors.Diagnostics;

string diagnosticsFolderPath = Windows.Storage.ApplicationData.Current.TemporaryFolder.Path;
const uint maxSessionSizeInMegaBytes = uint.MaxValue;

// Recording starts on the creation of a diagnostics session.
ObjectDiagnosticsSession diagnostics = new ObjectDiagnosticsSession(observer, maxSessionSizeInMegaBytes);

// Wait for the observer to do a job.

// Application can report some **pseudo ground-truth** pose for an instance
// acquired from other means.
diagnostics.ReportActualInstanceLocation(instance, coordinateSystem, Vector3.Zero, Quaternion.Identity);

// Close a session and write the diagnostics into an archive at specified location.
await diagnostics.CloseAsync(System.IO.Path.Combine(diagnosticsFolderPath, "diagnostics.zip"));
```

最后，我们需处置所有对象以释放资源。

```cs
using Microsoft.Azure.ObjectAnchors;

foreach (ObjectInstance instance in activeInstances)
{
    instance.Changed -= InstanceChangedHandler;
    instance.Dispose();
}

model.Dispose();
observer.Dispose();
```