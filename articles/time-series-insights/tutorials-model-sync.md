---
title: Azure 数字孪生与时序见解之间的模型同步 | Microsoft Docs
description: 用于将 ADT 中的资产模型转换为 Azure TSI 中的资产模型的最佳做法和工具
ms.service: time-series-insights
services: time-series-insights
author: msrsrinivasan
ms.author: radhsrin
manager: diviso
ms.workload: big-data
ms.topic: conceptual
ms.date: 01/19/2021
ms.custom: dpalled
ms.openlocfilehash: 7e7c38001578caa5e23b6c0458f70171d8dcf630
ms.sourcegitcommit: e0ef8440877c65e7f92adf7729d25c459f1b7549
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2021
ms.locfileid: "113564493"
---
# <a name="model-synchronization-between-azure-digital-twins-and-time-series-insights-gen2"></a>Azure 数字孪生与时序见解第 2 代之间的模型同步

本文介绍了用于将 Azure 数字孪生 (ADT) 中的资产模型转换为 Azure 时序见解 (TSI) 中的资产模型的最佳做法和工具。  介绍如何将 Azure 数字孪生与 Azure 时序见解集成的系列教程分两个部分，本文是第 2 部分。 将 Azure 数字孪生与时序见解集成，可以存档和跟踪数字孪生的遥测和计算属性的历史记录。 此系列的教程面向将时序见解与 Azure 数字孪生集成的开发人员。 第 1 部分介绍了[如何建立数据管道，将 Azure 数字孪生中的实际时序数据引入时序见解](../digital-twins/how-to-integrate-time-series-insights.md)，而本文，即此教程系列的第 2 部分介绍了 Azure 数字孪生与时序见解之间的资产模型同步。 本教程介绍为时序 ID (TS ID) 选择和建立命名约定并在时序模型 (TSM) 中手动建立层次结构的最佳做法。

## <a name="choosing-a-time-series-id"></a>选择时序 ID

时序 ID 是用于标识时序见解中的资产的唯一标识符。 时序数据（字段中的遥测数据，为时间值对）使用 TSID 下列出的变量表示。 在 Azure 数字孪生中，孪生属性和遥测分别用于表示孪生的状态和孪生生成的度量。 在当前 TSM 设计中，TSID 必须唯一。 使用 Azure 数字孪生中的孪生的孪生 ID，或将其与属性或遥测名称结合使用，将始终在 TSM 中提供唯一的 TS ID。 在典型案例中，`<Twin ID>` 为 TSID，属性和遥测名称为 TSM 中的变量。 但是，在某些用例中，最好使用组合键格式（例如 `<Twin ID>+ <Delimiter of Choice> + <Name of the Property or Telemetry>`）平展时序见解中的资产层次结构。 我们来举例说明后一种案例。 将建筑中的一个房间建模为一个孪生，其孪生 ID 为 Room22。 其温度设置属性将转换为 TSID Room22_TempSetting，在 TSM 中温度度量将转换为 Room22_TempMea。

[![选择时序 ID](media/tutorials-model-sync/choosing-tsid.png)](media/tutorials-model-sync/choosing-tsid.png#lightbox)

## <a name="contextualizing-time-series"></a>情景化时序

时序见解中的数据情景化（大部分本质上为空间）通过资产层次结构实现，且使用同一方法通过时序见解资源管理器的树状视图对数据轻松导航。 时序类型和层次结构使用时序见解中的时序模型 (TSM) 定义。 TSM 中的类型有助于定义变量，而层次结构级别和实例字段值用于在时序见解资源管理器中构造树状视图。 有关 TSM 的详细信息，请参阅[联机时序见解文档](./concepts-model-overview.md)。

在 Azure 数字孪生中，资产间的连接使用孪生关系表示。 孪生关系只是连接的资产关系图。 但在时序见解中，资产之间的关系在本质上是分层的。 也就是说，资产共享父子类型关系，使用树状结构表示。 若要将关系信息从 Azure 数字孪生转换为时序见解层次结构，需要从 Azure 数字孪生中选择相关层次结构关系。 Azure 数字孪生使用开放标准建模语言：数字孪生定义语言 (DTDL)。 在 DTDL 中，模型使用名为 JSON-LD 的 JSON 变体进行描述。 有关规范的完整详细信息，请参阅 [DTDL 文档](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)。

[![资产间的连接](media/tutorials-model-sync/asset-connection.png)](media/tutorials-model-sync/asset-connection.png#lightbox)

## <a name="translating-graph-representation-in-azure-digital-twins-to-tree-structure-in-time-series-insights"></a>将 Azure 数字孪生中的图形表示转换为时序见解中的树状结构

本教程的以下部分将捕获一些核心方案，这些方案将 Azure 数字孪生中的图形结构手动转换为时序见解中的树状结构。

示例系统：本教程使用下面的示例来解释下文讨论的概念。 它是一个虚构的简单构建管理系统，包含一层楼和两个房间。 它有三个恒温器，其中每个房间有一个，第三个为楼层公用。 此外，它还有一个水流计，用于测量通过房间之间的管道连接从 Room21 流到 Room22 的水量。 查看孪生之间的空间关系，它具有两种类型的关系。

1. 最常见的层次结构关系。 例如，Building40 -> Floor01 -> FloorTS* -> Temperature
1. 不太常见的循环关系。 例如，从 Building40 开始，可以通过两个不同的路径跟踪 FlowMtr。

   1. Building40 -> Floor01 -> Room21 -> FlowMtr* -> Flow
   1. Building40 -> Floor01 -> Room22 -> FlowMtr* -> Flow  
      其中，“Flow”是测量 Room21 和 Room22 之间的水流量的实际遥测数据

> [!Note]
>
> `*` FloorTS 代表 FloorThermoStat，而 FlowMtr 代表 Flow Meter，通常选择为 TSID。 温度和流量是指在时序见解中称为变量的原始遥测数据。

鉴于时序见解中的当前限制，即一个资产无法在多个分支中表示，以下部分说明了时序见解中的层次结构和循环关系建模。

## <a name="case-1-hierarchical-parent-child-relationship"></a>案例 1：层次结构（父子）关系

这是孪生之间最常见的关系类型。 下图介绍了纯父子关系建模。 实例字段和 TSID 派生自孪生 ID，如下所示。 尽管可以使用时序见解资源管理器手动更新实例字段，但下面的“使用 API 更新实例字段”部分说明了如何使用 Azure 函数侦听 Azure 数字孪生中的模型更改，以及如何更新时序见解中的实例字段。

[![映射孪生 ID](media/tutorials-model-sync/mapping-twin-ids.png)](media/tutorials-model-sync/mapping-twin-ids.png#lightbox)

[![映射孪生 ID 2](media/tutorials-model-sync/mapping-twin-ids2.png)](media/tutorials-model-sync/mapping-twin-ids2.png#lightbox)

## <a name="case-2-circular-relationship"></a>案例 2：循环关系

### <a name="circular-relationship-in-azure-digital-twins-to-single-hierarchy-relationship-in-time-series-insights"></a>Azure 数字孪生中的循环关系与时序见解中的单一层次结构关系

假设 TSID 必须唯一，且仅可在一个层次结构中表示，此案例将表示“FlowMtr”，其中名为“Flow”的遥测数据刚好在孪生“Room21”下。 将来，如果时序见解可以在 TSM 中支持多个时序表示，遥测“Flow”将在“Room 21”和“Room 22”下表示

以下屏幕截图显示了如何将 Azure 数字孪生中的孪生 ID 手动映射到 TSM 中的实例字段，以及时序见解中生成的层次结构。

[![映射 Azure 数字孪生中的孪生 ID](media/tutorials-model-sync/mapping-twin-ids-adt.png)](media/tutorials-model-sync/mapping-twin-ids-adt.png#lightbox)

### <a name="circular-relationship-in-azure-digital-twins-to-multiple-hierarchies-in-time-series-insights-using-duplicates"></a>Azure 数字孪生中的循环关系与使用重复项的时序见解中的多个层次结构

本教程的第 1 部分介绍了客户端程序（Azure 函数）如何帮助将遥测数据从 IoT 中心或其他事件源传输到 Azure 数字孪生。 此方法建议使用相同的客户端程序对父孪生的相关属性进行更新。 在给定的示例中，从 IoT 中心读取 FlowMtr 遥测并更新 FlowMtr 中的属性 "Flow" 时，该程序还可以更新源的所有可能父孪生中的相应属性。 在我们的示例中，将为 Room21 的属性“outflowmea”（使用“outflow”关系标识）和 Room22 的属性“inflowmea”。  下面的屏幕截图显示时序见解资源管理器中的最终用户体验。 必须注意，使用此方法时数据有重复项。

[![时序见解资源管理器](media/tutorials-model-sync/tsi-explorer.png)](media/tutorials-model-sync/tsi-explorer.png#lightbox)

下面的代码片段显示了客户端应用程序如何使用 Azure 数字孪生 API 来浏览孪生关系。

> [!Note]
>
> 此代码段示例假设读者熟悉本教程的[第 1 部分](../digital-twins/tutorial-end-to-end.md#set-up-the-sample-function-app)，且此代码更改在“ProcessHubToDTEvents”函数内进行。

```csharp
if (propertyPath.Equals("/Flow"))
{
//Update the flow value property of the flow meter
await AdtUtilities.UpdateTwinProperty(client, twinId, "replace",
propertyPath, "double", propertyValue, log);

//also update the sending end flow
string parentIdOutflow = await AdtUtilities.FindParent(client, twinId,
"outflow", log);
if (parentIdOutflow != null)
await AdtUtilities.UpdateTwinProperty(client, parentIdOutflow, "replace", "outflow", "double", propertyValue, log);
else
log.LogInformation("Unable to find Parent with outflow
relationship for " + twinId );
//and receiving end flow value
string parentIdinflow = await AdtUtilities.FindParent(client, twinId,
"inflow", log);
if (parentIdinflow != null)

await AdtUtilities.UpdateTwinProperty(client, parentIdinflow,
"replace", "inflow", "double", propertyValue, log);
else
log.LogInformation("Unable to find Parent with inflow
relationship for " + twinId);
}
```

## <a name="updating-instance-fields-using-apis"></a>使用 API 更新实例字段

本教程的此部分介绍了如何侦听 Azure 数字孪生中的模型更改（如创建、删除孪生）或孪生之间的关系更改，以及使用时序见解模型 API 以编程方式更新实例字段和层次结构。 此更新时序见解模型方法通常通过 Azure 函数实现。 在 Azure 数字孪生中，事件通知（如孪生添加或删除）可以路由到下游服务（如事件中心），进而提供给 Azure 函数。 有关事件路由和筛选的详细信息，请参阅[此处](../digital-twins/how-to-manage-routes-portal.md)。  本部分的其余部分介绍如何在 Azure 函数中使用时序见解模型 API 更新时序见解模型，以响应 Azure 数字孪生中的孪生添加（一种模型更改类型）。

### <a name="receiving-and-identifying-twin-addition-event-notification"></a>接收和识别孪生添加事件通知

```csharp
[FunctionName("RouteEventsToTsi")]
public async Task Run([EventGridTrigger]EventGridEvent eventGridEvent)
{
    try
    {
        if (eventGridEvent != null && eventGridEvent.Data != null)
        {
            logger.LogInformation($"EventType: {eventGridEvent.EventType}");
            logger.LogInformation($"EventGridEvent: {JsonConvert.SerializeObject(eventGridEvent)}");

            //Shape event and Send event data to event hub and tsi
            await SendEventToEventHubAsync(eventGridEvent).ConfigureAwait(false);

            //If a new twin was created, update the newly created instance in TSI with info retrieved from ADT
            if (eventGridEvent.EventType == Constants.TwinCreateEventType)
            {
                //retrieve building, floor and room of value twin
                var twinInfo = await RetrieveTwinInfoAsync(eventGridEvent).ConfigureAwait(false);
                //Update Tsi instance with type(sensor type), hierarchy(space hierarchy) and instance fields(twin info retrieved above)
                var instance = await CreateInstanceToSendAsync(twinInfo).ConfigureAwait(false);
                var instanceToUpdate = new List<TimeSeriesInstance>() { instance };
                var response = await tsiClient.TimeSeriesInstances.ExecuteBatchAsync(new InstancesBatchRequest(update: instanceToUpdate)).ConfigureAwait(false);
            }
        }
    }
    catch (Exception ex)
    {
        logger.LogError($"Exception: {ex.Message}");
    }
}
```

### <a name="creating-time-series-insights-client-and-adding-instance-details"></a>创建时序见解客户端和添加实例详细信息

```csharp
private async Task<TimeSeriesInstance> CreateInstanceToSendAsync(Dictionary<string, string> twinInfo)
{
    try
    {
        tsiClient = await GetTSIClientAsync().ConfigureAwait(false);

        var timeSeriesId = new object[] { twinInfo[Constants.DtId] };
        var instances = await tsiClient.TimeSeriesInstances.ExecuteBatchAsync(
            new InstancesBatchRequest(
                get: new InstancesRequestBatchGetOrDelete(
                    new IList<object>[] { timeSeriesId }))).ConfigureAwait(false);
        var instance = instances.Get.First().Instance;

        if (instance != null)
        {
            instance = await AddHierarchyToInstanceAsync(instance).ConfigureAwait(false);
            instance = await AddTypeToInstanceAsync(instance, twinInfo[Constants.TwinType]).ConfigureAwait(false);

            instance.InstanceFields = new Dictionary<string, object>
                            {
                                { "Building", twinInfo[Constants.BuildingName] },
                                { "Floor", twinInfo[Constants.FloorName] },
                                { "Room", twinInfo[Constants.ParentName] }
                            };

            //If value twin is a sensor value twin, add sensor type instance field to diff from spatial value twin
            if (twinInfo[Constants.ParentType] == Constants.Sensor)
            {
                instance.InstanceFields.Add(Constants.SensorType, twinInfo[Constants.TwinType]);
            }
            return instance;
        }
        else
        {
            logger.LogError($"instance with id {twinInfo[Constants.DtId]} not found");
            return new TimeSeriesInstance();
        }
    }
    catch (Exception e)
    {
        logger.LogError(e.Message);
        throw;
    }
}
```

### <a name="applying-hierarchy-info-to-instance"></a>将层次结构信息应用到实例

```csharp
private async Task<TimeSeriesInstance> AddHierarchyToInstanceAsync(TimeSeriesInstance instance)
{
    if (instance.HierarchyIds == null)
    {
        TimeSeriesHierarchy spacesHierarchy = null;
        try
        {
            var hierarchy = await RunGetHierarchiesAsync(Constants.SpaceHierarchy).ConfigureAwait(false);
            spacesHierarchy = hierarchy.First(h => h.Name.Equals(Constants.SpaceHierarchy));
            instance.HierarchyIds = new List<Guid?>();
            instance.HierarchyIds.Add(spacesHierarchy.Id);
        }
        catch (Exception ex)
        {
            logger.LogWarning($"Hierarchy 'space hierarchy' not found, {ex}");
            throw;
        }
    }
    return instance;
}
```

## <a name="next-steps"></a>后续步骤

教程系列的第三部分将介绍如何使用时序见解 API 查询 Azure 数字孪生的历史数据。 此研究正在进行中，在准备就绪时将更新该部分。 同时，建议读者参阅[时序见解数据查询 API 文档](./concepts-query-overview.md)。
