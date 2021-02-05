---
title: 监视 Azure IoT 中心
description: 从此处开始了解如何监视 Azure IoT 中心
author: robinsh
ms.author: robinsh
ms.topic: conceptual
ms.service: iot-hub
ms.custom: subject-monitoring
ms.date: 11/06/2020
ms.openlocfilehash: db85594ad4fef37ec7411f7d5791fdb5279ec504
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2021
ms.locfileid: "99581646"
---
# <a name="monitoring-azure-iot-hub"></a>监视 Azure IoT 中心

当你的关键应用程序和业务流程依赖于 Azure 资源时，你需要监视这些资源的可用性、性能和操作。 本文介绍了 Azure IoT 中心生成的监视数据，以及如何使用 Azure Monitor 的功能在此数据的基础上进行分析和发出警报。

## <a name="monitor-overview"></a>Monitor 概述

每个 IoT 中心的 Azure 门户中的“概述”页面都包括提供某些使用指标的图表，例如使用的消息数以及连接到 IoT 中心的设备数。

:::image type="content" source="media/monitor-iot-hub/overview-portal.png" alt-text="IoT 中心“概述”页上的默认指标图表。":::

请注意，消息计数值可能会延迟 1 分钟。由于与 IoT 中心服务基础结构有关的原因，此值在刷新时有时可能会在较高和较低的值之间来回切换。 此计数器只有上一分钟应计的值会不正确。

“概述”窗格中提供的信息很有用，但只代表提供给 IoT 中心的少量监视数据。 某些监视数据会在你创建 IoT 中心后立即自动收集，并可供用于分析。 你可以使用某些配置启用其他数据收集类型。

## <a name="what-is-azure-monitor"></a>说明是 Azure Monitor？

Azure IoT 中心使用 [Azure Monitor](../azure-monitor/overview.md) 创建监视数据。Azure Monitor 是 Azure 中的一个全堆栈监视服务，提供用于监视 Azure 资源以及其他云中的资源和本地资源的整套功能。

一开始可以阅读[使用 Azure Monitor 监视 Azure 资源](../azure-monitor/insights/monitor-azure-resource.md)一文，其中介绍了以下概念：

- 说明是 Azure Monitor？
- 与监视相关的成本
- 监视 Azure 中收集的数据
- 配置数据收集
- Azure 中用于分析监视数据并就其发出警报的标准工具

本文的以下各部分介绍了为 Azure IoT 中心收集的特定数据，并提供了有关使用 Azure 工具配置数据收集和分析此数据的示例。

## <a name="monitoring-data"></a>监视数据

Azure IoT 中心收集的监视数据与[监视来自 Azure 资源的数据](../azure-monitor/insights/monitor-azure-resource.md#monitoring-data)中所述的其他 Azure 资源的类型相同。

有关 Azure IoT 中心创建的指标和日志的详细信息，请参阅 [Azure IoT 中心监视数据参考](monitor-iot-hub-reference.md)。

> [!IMPORTANT]
> IoT 中心服务使用 Azure Monitor 资源日志发出的事件不保证可靠或有序。 某些事件可能会丢失或未按顺序传送。 资源日志不一定是实时的，可能需要几分钟的时间才能将事件记录到所选的目标。

## <a name="collection-and-routing"></a>收集和路由

平台指标和活动日志会自动收集和存储，但你可以使用诊断设置将其路由到其他位置。

在创建诊断设置并将其路由到一个或多个位置之前，不会收集和存储资源日志。

指标和日志可路由到多个位置，包括：
- Azure Monitor 日志存储（使用关联的 Log Analytics 工作区）。 可以在其中使用 Log Analytics 对它们进行分析。
- 用于存档和脱机分析的 Azure 存储 
- 事件中心终结点，外部应用程序（例如第三方 SIEM 工具）可以从中读取它们。

在 Azure 门户的 IoT 中心左窗格的“监视”下选择“诊断设置”，然后选择“添加诊断设置”，以便创建作用域为 IoT 中心发出的日志和平台指标的诊断设置。

以下屏幕截图显示了用于将资源日志类型“连接操作”和所有平台指标路由到 Log Analytics 工作区的诊断设置。

:::image type="content" source="media/monitor-iot-hub/diagnostic-setting-portal.png" alt-text="IoT 中心的“诊断设置”窗格。":::

有关使用 Azure 门户、CLI 或 PowerShell 创建诊断设置的详细过程，请参阅[创建诊断设置以收集 Azure 中的平台日志和指标](../azure-monitor/platform/diagnostic-settings.md)。 创建诊断设置时，请指定要收集的日志类别。 [Azure IoT 中心监视数据参考中的“资源日志”](monitor-iot-hub-reference.md#resource-logs)下列出了 Azure IoT 中心的类别。

将 IoT 中心平台指标路由到其他位置时，请注意：

- 以下平台指标不可通过诊断设置导出：“已连接设备数(预览)”和“设备总数(预览)” 。

- 多维指标（例如，某些[路由指标](monitor-iot-hub-reference.md#routing-metrics)）当前导出为跨维度值聚合的平展单维指标。 有关更多详细信息，请参阅[将平台指标导出到其他位置](../azure-monitor/platform/metrics-supported.md#exporting-platform-metrics-to-other-locations)。

## <a name="analyzing-metrics"></a>分析指标

可以从“Azure Monitor”菜单中打开“指标”，使用指标资源管理器根据来自其他 Azure 服务的指标分析 Azure IoT 中心的指标 。 有关使用此工具的详细信息，请参阅 [Azure 指标资源管理器入门](../azure-monitor/platform/metrics-getting-started.md)。

在 Azure 门户的 IoT 中心左窗格的“监视”下选择“指标”以打开指标资源管理器。默认情况下，打开的指标资源管理器的范围为 IoT 中心发出的平台指标：

:::image type="content" source="media/monitor-iot-hub/metrics-portal.png" alt-text="IoT 中心的指标资源管理器页。":::

有关为 Azure IoT 中心收集的平台指标的列表，请参阅 [Azure IoT 中心监视数据参考中的“指标”](monitor-iot-hub-reference.md#metrics)。 有关为所有 Azure 服务收集的平台指标的列表，请参阅 [Azure Monitor 支持的指标](../azure-monitor/platform/metrics-supported.md)。

对于以计数单位收集的 IoT 中心平台指标，某些聚合可能不可用或无法使用。 若要了解详细信息，请参阅 [Azure IoT 中心监视数据参考中的“支持的聚合”](monitor-iot-hub-reference.md#supported-aggregations)。

某些 IoT 中心指标（例如[路由指标](monitor-iot-hub-reference.md#routing-metrics)）是多维的。 对于这些指标，你可以基于某个维度对图表应用[筛选器](../azure-monitor/platform/metrics-charts.md#filters)和[拆分](../azure-monitor/platform/metrics-charts.md#apply-splitting)。

## <a name="analyzing-logs"></a>分析日志

Azure Monitor 日志中的数据以表形式存储，每个表具有自己独有的属性集。 这些表中的数据与某个 Log Analytics 工作区相关联，并且可以在 Log Analytics 中进行查询。 若要详细了解 Azure Monitor 日志，请参阅 Azure Monitor 文档中的 [Azure Monitor 日志概述](../azure-monitor/platform/data-platform-logs.md)。 

若要将数据路由到 Azure Monitor 日志，你必须创建一个诊断设置，以便将资源日志或平台指标发送到 Log Analytics 工作区。 若要了解详细信息，请参阅[收集和路由](#collection-and-routing)。

在 Azure 门户的 IoT 中心左窗格的“监视”下选择“日志”以执行 Log Analytics 查询。默认情况下，查询范围为 IoT 中心的 Log Analytics 日志中收集的日志和指标。

:::image type="content" source="media/monitor-iot-hub/logs-portal.png" alt-text="IoT 中心的“日志”页。":::

有关 Azure Monitor 日志使用的可供 Log Analytics 查询的表的列表，请参阅 [Azure IoT 中心监视数据参考中的“Azure Monitor 日志表”](monitor-iot-hub-reference.md#azure-monitor-logs-tables)。

Azure Monitor 中的所有资源日志都具有后跟服务特定字段的相同字段。 [Azure Monitor 资源日志架构](../azure-monitor/platform/resource-logs-schema.md#top-level-common-schema)概述了常见架构。 可以在 [Azure IoT 中心监视数据参考中的“资源日志”](monitor-iot-hub-reference.md#resource-logs)中找到为 Azure IoT 中心收集的资源日志的架构和类别。

[活动日志](../azure-monitor/platform/activity-log.md)是 Azure 中的一种平台日志，可用于深入了解订阅级别事件。 你可以单独查看它或将它路由到 Azure Monitor 日志，然后便可以在其中使用 Log Analytics 执行复杂得多的查询。  

将 IoT 中心平台指标路由到 Azure Monitor 日志时，请注意：

- 以下平台指标不可通过诊断设置导出：“已连接设备数(预览)”和“设备总数(预览)” 。

- 多维指标（例如，某些[路由指标](monitor-iot-hub-reference.md#routing-metrics)）当前导出为跨维度值聚合的平展单维指标。 有关更多详细信息，请参阅[将平台指标导出到其他位置](../azure-monitor/platform/metrics-supported.md#exporting-platform-metrics-to-other-locations)。

有关 IoT 中心的一些常见查询，请参阅[示例 Kusto 查询](#sample-kusto-queries)。 有关使用 Log Analytics 查询的详细信息，请参阅 [Azure Monitor 中的日志查询概述](../azure-monitor/log-query/log-query-overview.md)。

### <a name="sdk-version-in-iot-hub-logs"></a>IoT 中心日志中的 SDK 版本

IoT 中心资源日志中的一些操作在其 `properties` 对象中返回 `sdkVersion` 属性。 对于这些操作，当设备或后端应用使用 Azure IoT SDK 之一时，此属性包含有关所使用的 SDK、SDK 版本和运行 SDK 的平台的信息。 下面的示例演示了在使用 Node.js 设备 SDK 时，为 [`deviceConnect`](monitor-iot-hub-reference.md#connections) 操作发出的 `sdkVersion` 属性：`"azure-iot-device/1.17.1 (node v10.16.0; Windows_NT 10.0.18363; x64)"`。 下面是为 .NET (C#) SDK 发出的值的示例：`".NET/1.21.2 (.NET Framework 4.8.4200.0; Microsoft Windows 10.0.17763 WindowsProduct:0x00000004; X86)"`。

下表显示了用于不同 Azure IoT SDK 的 SDK 名称：

| SdkVersion 属性中的 SDK 名称 | 语言 |
|----------|----------|
| .NET | .NET (C#) |
| microsoft.azure.devices | .NET (C#) 服务 SDK |
| microsoft.azure.devices.client | .NET (C#) 设备 SDK |
| iothubclient | C 或 Python v1（已弃用）设备 SDK |
| iothubserviceclient | C 或 Python v1（已弃用）服务 SDK |
| azure-iot-device-iothub-py | Python 设备 SDK |
| azure-iot-device | Node.js 设备 SDK |
| azure-iothub | Node.js 服务 SDK |
| com.microsoft.azure.iothub-java-client | Java 设备 SDK |
| com.microsoft.azure.iothub.service.sdk | Java 服务 SDK |
| com.microsoft.azure.sdk.iot.iot-device-client | Java 设备 SDK |
| com.microsoft.azure.sdk.iot.iot-service-client | Java 服务 SDK |
| C | Embedded C |
| C + (OSSimplified = Azure RTOS) | Azure RTOS |

对 IoT 中心资源日志执行查询时，可以提取 SDK 版本属性。 例如，下面的查询从连接操作返回的属性中提取 SDK 版本属性（和设备 ID）。 这两个属性将与操作时间以及设备所连接到的 IoT 中心的资源 ID 一起写入到结果中。

```kusto
// SDK version of devices
// List of devices and their SDK versions that connect to IoT Hub
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
| where Category == "Connections"
| extend parsed_json = parse_json(properties_s) 
| extend SDKVersion = tostring(parsed_json.sdkVersion) , DeviceId = tostring(parsed_json.deviceId)
| distinct DeviceId, SDKVersion, TimeGenerated, _ResourceId
```

### <a name="sample-kusto-queries"></a>示例 Kusto 查询

> [!IMPORTANT]
> 在 IoT 中心菜单中选择“日志”时，Log Analytics 随即打开，其查询范围设置为当前 IoT 中心。 这意味着日志查询只包含来自该资源的数据。 如果希望运行的查询包含其他 IoT 中心或其他 Azure 服务的数据，请从“Azure Monitor”菜单中选择“日志”。 有关详细信息，请参阅 [Azure Monitor Log Analytics 中的日志查询范围和时间范围](../azure-monitor/log-query/scope.md)。

下面是可用来帮助你监视 IoT 中心的查询。

- 连接性错误：确定设备连接错误。

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Category == "Connections" and Level == "Error"
    ```

- 限制错误：确定因发出的请求最多而导致限制错误的设备。

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where ResultType == "429001"
    | extend DeviceId = tostring(parse_json(properties_s).deviceId)
    | summarize count() by DeviceId, Category, _ResourceId
    | order by count_ desc
    ```

- 死终结点：按报告问题的次数以及原因来确定死的或不正常的终结点。

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Category == "Routes" and OperationName in ("endpointDead", "endpointUnhealthy")
    | extend parsed_json = parse_json(properties_s)
    | extend Endpoint = tostring(parsed_json.endpointName), Reason = tostring(parsed_json.details) 
    | summarize count() by Endpoint, OperationName, Reason, _ResourceId
    | order by count_ desc
    ```

- 错误摘要：所有操作的按类型的错误计数。

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Level == "Error"
    | summarize count() by ResultType, ResultDescription, Category, _ResourceId
    ```

- 最近连接的设备：IoT 中心在指定时间段内看到其已连接的设备的列表。

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Category == "Connections" and OperationName == "deviceConnect"
    | extend DeviceId = tostring(parse_json(properties_s).deviceId)
    | summarize max(TimeGenerated) by DeviceId, _ResourceId
    ```

- 设备的 SDK 版本：用于设备连接或设备到云孪生操作的设备及其 SDK 版本的列表。

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Category == "Connections" or Category == "D2CTwinOperations"
    | extend parsed_json = parse_json(properties_s)
    | extend SDKVersion = tostring(parsed_json.sdkVersion) , DeviceId = tostring(parsed_json.deviceId)
    | distinct DeviceId, SDKVersion, TimeGenerated, _ResourceId
    ```

### <a name="read-logs-from-azure-event-hubs"></a>从 Azure 事件中心读取日志

通过诊断设置来设置事件日志记录后，可以创建应用程序以读出日志，从而可以根据日志中的信息采取措施。 以下示例代码从事件中心检索日志：

```csharp
class Program
{ 
    static string connectionString = "{your AMS eventhub endpoint connection string}";
    static string monitoringEndpointName = "{your AMS event hub endpoint name}";
    static EventHubClient eventHubClient;
    //This is the Diagnostic Settings schema
    class AzureMonitorDiagnosticLog
    {
        string time { get; set; }
        string resourceId { get; set; }
        string operationName { get; set; }
        string category { get; set; }
        string level { get; set; }
        string resultType { get; set; }
        string resultDescription { get; set; }
        string durationMs { get; set; }
        string callerIpAddress { get; set; }
        string correlationId { get; set; }
        string identity { get; set; }
        string location { get; set; }
        Dictionary<string, string> properties { get; set; }
    };

    static void Main(string[] args)
    {
        Console.WriteLine("Monitoring. Press Enter key to exit.\n");
        eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, monitoringEndpointName);
        var d2cPartitions = eventHubClient.GetRuntimeInformationAsync().PartitionIds;
        CancellationTokenSource cts = new CancellationTokenSource();
        var tasks = new List<Task>();
        foreach (string partition in d2cPartitions)
        {
            tasks.Add(ReceiveMessagesFromDeviceAsync(partition, cts.Token));
        }
        Console.ReadLine();
        Console.WriteLine("Exiting...");
        cts.Cancel();
        Task.WaitAll(tasks.ToArray());
    }

    private static async Task ReceiveMessagesFromDeviceAsync(string partition, CancellationToken ct)
    {
        var eventHubReceiver = eventHubClient.GetDefaultConsumerGroup().CreateReceiver(partition, DateTime.UtcNow);
        while (true)
        {
            if (ct.IsCancellationRequested)
            {
                await eventHubReceiver.CloseAsync();
                break;
            }
            EventData eventData = await eventHubReceiver.ReceiveAsync(new TimeSpan(0,0,10));
            if (eventData != null)
            {
                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                Console.WriteLine("Message received. Partition: {0} Data: '{1}'", partition, data);
                var deserializer = new JavaScriptSerializer();
                //deserialize json data to azure monitor object
                AzureMonitorDiagnosticLog message = new JavaScriptSerializer().Deserialize<AzureMonitorDiagnosticLog>(result);
            }
        }
    }
}
```

## <a name="alerts"></a>警报

在监视数据中发现重要情况时，Azure Monitor 警报会主动通知你。 有了警报，你就可以在客户注意到你的系统中的问题之前确定和解决它们。 可以在[指标](../azure-monitor/platform/alerts-metric-overview.md)、[日志](../azure-monitor/platform/alerts-unified-log.md)和[活动日志](../azure-monitor/platform/activity-log-alerts.md)上设置警报。 不同类型的警报各有优缺点。

当基于平台指标创建警报规则时，请注意，对于以计数单位收集的 IoT 中心平台指标，某些聚合可能不可用或无法使用。 若要了解详细信息，请参阅 [Azure IoT 中心监视数据参考中的“支持的聚合”](monitor-iot-hub-reference.md#supported-aggregations)。

## <a name="monitor-per-device-disconnects-with-event-grid"></a>使用事件网格监视每个设备的连接断开问题

Azure Monitor 提供“连接的设备数”指标，可用于监视连接到 IoT 中心的设备数，并在连接的设备数低于阈值时触发警报。 尽管在某些情况下，仅使用该指标就足以达到目的，但 [Azure 事件网格](../event-grid/index.yml)还提供了一个低延迟、每设备监视解决方案，可用于跟踪关键设备和基础结构的设备连接。

借助事件网格，可以订阅 IoT 中心 [DeviceConnected 和 DeviceDisconnected 事件](iot-hub-event-grid.md#event-types)，以触发警报和监视设备连接状态 。 与 Azure Monitor 相比，事件网格提供的事件延迟要低得多，并且你可以监视每台设备，而不是已连接的设备总数。 这些因素使事件网格成为监视关键设备和基础结构的连接的首选方法。 强烈建议在生产环境中使用事件网格监视设备连接。

有关通过事件网格和 Azure Monitor 监视设备连接的更多详细信息，请参阅[监视、诊断和排查 Azure IoT 中心的连接断开问题](iot-hub-troubleshoot-connectivity.md)。

## <a name="next-steps"></a>后续步骤

- 有关 [服务名称] 创建的指标、日志和其他重要值的参考信息，请参阅 [Azure IoT 中心监视数据参考](monitor-iot-hub-reference.md)。

- 有关监视 Azure 资源的详细信息，请参阅[使用 Azure Monitor 监视 Azure 资源](../azure-monitor/insights/monitor-azure-resource.md)。
