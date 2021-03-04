---
title: 运行时 SDK 概述
description: 熟悉对象锚运行时 SDK。
author: craigktreasure
manager: vriveras
services: azure-object-anchors
ms.author: crtreasu
ms.date: 03/02/2021
ms.topic: conceptual
ms.service: azure-object-anchors
ms.openlocfilehash: 74663f05c5ff995a090c7cd35e4edf46a754da17
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102034602"
---
# <a name="runtime-sdk-overview"></a>运行时 SDK 概述

本部分提供了对象定位点 SDK 的高级概述，该 SDK 用于检测使用对象锚记模型的对象。 您将了解如何表示对象以及如何使用各种组件。

下面所述的所有类型都可以在 **MixedReality. ObjectAnchors** 命名空间中找到。

## <a name="types"></a>类型

### <a name="objectmodel"></a>ObjectModel

[System.collections.objectmodel.collection](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectmodel)表示物理对象的几何，并为检测和姿势估算的必要参数编码。 必须使用 [对象锚定服务](../quickstarts/get-started-model-conversion.md)来创建它。 然后，应用程序可以使用对象锚点 API 加载生成的模型文件，并在该模型中查询嵌入的网格以进行可视化。

### <a name="objectsearcharea"></a>ObjectSearchArea

[ObjectSearchArea](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectsearcharea)指定要查找一个或多个对象的空间。 它通过空间图形节点 id 和坐标系统中的空间边界定义，由空间图形节点 ID 表示。 对象锚运行时 SDK 支持四种类型的边界：即， **视图的字段**、 **边界框**、 **球** 和 **位置**。

### <a name="objectquery"></a>ObjectQuery

[ObjectQuery](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectquery)通知 **对象观察** 器如何查找给定模型的对象。 它提供以下可优化参数，其默认值可从对象模型中检索。

#### <a name="minsurfacecoverage"></a>MinSurfaceCoverage

[MinSurfaceCoverage](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectquery.minsurfacecoverage)属性指示要将实例视为已检测到的值。

对于每个候选对象， **观察** 者将计算已转换对象模型与场景之间重叠曲面的比率，然后仅当覆盖率比率高于给定阈值时，它才会将候选报告给应用程序。

#### <a name="isexpectedtobestandingongroundplane"></a>IsExpectedToBeStandingOnGroundPlane

[IsExpectedToBeStandingOnGroundPlane](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectquery.isexpectedtobestandingongroundplane)属性指示目标对象是否应在地面上显示。

地面是搜索区域中的最低水平地板。 它对可能的对象姿势提供良好的约束。 打开此标志将引导 **观察** 者估算有限空间中的姿势，并可以提高准确性。 如果模型不应在地面上，则将忽略此参数。

#### <a name="expectedmaxverticalorientationindegrees"></a>ExpectedMaxVerticalOrientationInDegrees

[ExpectedMaxVerticalOrientationInDegrees](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectquery.expectedmaxverticalorientationindegrees)属性以度为单位指示对象实例与重力的向上方向之间的最大角度。

此参数为估计的姿势的向上方向提供另一个约束。 例如，如果对象是右上方，则此参数可以为0。 对象锚不应检测不同于模型的对象。 如果模型是最新的，则不会检测向下布局的实例。 新模型将用于向下的布局。 相同的规则适用于 articulation。

#### <a name="maxscalechange"></a>MaxScaleChange

[MaxScaleChange](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectquery.maxscalechange)属性指示 0 ~ 1)  (中的最大对象缩放更改（与空间映射相关）。 估计的小数位数适用于居中的已转换对象顶点和轴对齐的顶点。 估计的刻度可能不是 CAD 模型及其物理表示形式之间的实际缩放，但允许应用将对象模型呈现到物理对象的空间映射附近的某些值。

#### <a name="searchareas"></a>SearchAreas

[SearchAreas](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectquery.searchareas)属性指示一个空间边界数组，其中查找)  (的对象。

**观察** 者将在查询中指定的所有搜索区域的联合空间中查找对象。 在此版本中，我们将最多返回一个具有最高置信度的对象来减少延迟。

### <a name="objectinstance"></a>ObjectInstance

[ObjectInstance](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectinstance)表示给定模型的实例可以位于 HoloLens 坐标系统中的假设位置。 每个实例都带有一个 `SurfaceCoverage` 属性，用于指示估计的姿势有多好。

实例是通过调用方法创建的 `ObjectObserver.DetectAsync` ，然后在后台自动更新。 应用程序可以侦听特定实例上的状态更改事件，或更改跟踪模式以暂停/继续更新。 跟踪丢失时，实例将自动从 **观察** 程序中删除。

### <a name="objectobserver"></a>ObjectObserver

[ObjectObserver](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.objectobserver)加载对象模型，检测其实例，并报告 DoF 的每个实例在 HoloLens 坐标系统中的姿势。

尽管从 **观察** 程序创建任何对象模型或实例，但它们的生存期是独立的。 应用程序可以释放观察程序并继续使用对象模型或实例。

### <a name="objectdiagnosticssession"></a>ObjectDiagnosticsSession

[ObjectDiagnosticSession](https://docs.microsoft.com/dotnet/api/microsoft.azure.objectanchors.diagnostics.objectdiagnosticssession)记录诊断，并将数据写入存档。

诊断存档包括场景点云、观察程序的状态和有关模型的信息。 此信息可用于识别可能的运行时问题。 有关详细信息，请参阅[常见问题解答](../faq.md)。

## <a name="runtime-sdk-usage-and-details"></a>运行时 SDK 使用情况和详细信息

本部分将提供有关如何使用运行时 SDK 的基本知识。 它应为您授予足够的上下文来浏览示例应用程序，以查看如何整体使用对象锚。

### <a name="initialization"></a>初始化

应用程序需要调用 `ObjectObserver.IsSupported()` API 来确定设备上是否支持对象锚。 如果 `ObjectObserver.IsSupported()` API 返回 `false` ，请检查应用程序是否已启用 **spatialPerception** 功能和/或升级到最新的 HoloLens OS。

```cs
if (!ObjectObserver.IsSupported())
{
    // Handle the error
}

// This call should grant the access we need.
var status = await ObjectObserver.RequestAccessAsync();
if(status != ObjectObserverStatus.Allowed)
{
    // Handle the error
}
```

接下来，该应用程序将创建一个对象观察器并加载由 [对象锚模型转换服务](../quickstarts/get-started-model-conversion.md)生成的必要模型。

```cs
var observer = new ObjectObserver();

byte[] modelAsBytes; // Load a model into a byte array. Model could be a file, an embedded resource, or a network stream.
var model = await observer.LoadObjectModelAsync(modelAsBytes);

// Note that after a model is loaded, its vertices and normals are transformed into a centered coordinate system for the 
// ease of computing the object pose. The rigid transform can be retrieved through the `OriginToCenterTransform` property.
```

应用程序创建一个查询，用于检测某个空间内该模型的实例。

```cs
var coordinateSystem = default(SpatialGraphCoordinateSystem);
var searchAreaAsBox = ObjectSearchArea.FromOrientedBox(coordinateSystem, default(SpatialOrientedBox));

// Optionally change the parameters, otherwise use the default values embedded in the model.
var query = new ObjectQuery(model);
query.MinSurfaceCoverage = 0.2f;
query.ExpectedMaxVerticalOrientationInDegrees = 1.5f;
query.MaxScaleChange = 0.1f;
query.SearchAreas.Add(searchAreaAsBox);

// Detection could take long, run in a background thread.
var detectedObjects = await observer.DetectAsync(query);
```

默认情况下， **观察** 程序将自动跟踪每个检测到的实例。 可以选择通过更改其跟踪模式或侦听其状态更改事件来处理这些实例。

```cs
foreach (var instance in detectedObjects)
{
    // Supported modes:
    // "LowLatencyCoarsePosition" - consumes less CPU cycles thus fast to update the state.
    // "HighLatencyAccuratePosition" - (not yet implemented) consumes more CPU cycles thus potentially taking longer time to update the state.
    // "Paused" - stops to update the state until mode changed to low or high.
    instance.Mode = ObjectInstanceTrackingMode.LowLatencyCoarsePosition;

    // Listen to state changed event on this instance.
    instance.Changed += InstanceChangedHandler;

    // Optionally dispose an instance if not interested in it.
    //instance.Dispose();
}
```

在状态更改事件中，可以查询最新状态，或在实例丢失跟踪的情况下释放实例。

```cs
var InstanceChangedHandler = new Windows.Foundation.TypedEventHandler<ObjectInstance, ObjectInstanceChangedEventArgs>((sender, args) =>
{
    // Try to query the current instance state.
    var state = sender.TryGetCurrentState();

    if (state.HasValue)
    {
        // Process latest state via state.Value.
        // An object pose includes scale, rotation and translation, applied in the same order
        // to the object model in the centered coordinate system.
    }
    else
    {
        // This object instance is lost for tracking, and will never be recovered.
        // The caller can detach the Changed event handler from this instance and dispose it.
        sender.Dispose();
    }
});
```

此外，应用程序可以选择记录一个或多个诊断会话以便进行脱机调试。

```cs
string diagnosticsFolderPath = Windows.Storage.ApplicationData.Current.TemporaryFolder.Path;
const uint maxSessionSizeInMegaBytes = uint.MaxValue;

// Recording starts on the creation of a diagnostics session.
var diagnostics = new ObjectDiagnosticsSession(observer, maxSessionSizeInMegaBytes);

// Wait for the observer to do a job.

// Application can report some **pseudo ground-truth** pose for an instance acquired from other means.
diagnostics.ReportActualInstanceLocation(instance, coordinateSystem, Vector3.Zero, Quaternion.Identity);

// Close a session and write the diagnostics into an archive at specified location.
await diagnostics.CloseAsync(System.IO.Path.Combine(diagnosticsFolderPath, "diagnostics.zip"));
```

最后，我们通过释放所有对象来释放资源。

```cs
foreach(var instance in activeInstances)
{
    instance.Changed -= InstanceChangedHandler;
    instance.Dispose();
}

model.Dispose();
observer.Dispose();
```
