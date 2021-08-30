---
title: 监视和排查 Azure IoT 中心的连接断开问题
description: 了解如何监视和排查 Azure IoT 中心设备连接的常见错误
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/06/2020
ms.author: jlian
ms.custom:
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
- 'Role: Technical Support'
- fasttrack-edit
- iot
ms.openlocfilehash: 22c1658740af7ef7eccbeca02c6f98485ec11222
ms.sourcegitcommit: 351279883100285f935d3ca9562e9a99d3744cbd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2021
ms.locfileid: "112378297"
---
# <a name="monitor-diagnose-and-troubleshoot-azure-iot-hub-disconnects"></a>监视、诊断和排查 Azure IoT 中心连接断开问题 

由于存在许多可能的故障点，IoT 设备的连接问题有时很难排查。 应用程序逻辑、物理网络、协议、硬件、IoT 中心和其他云服务都可能导致问题。 检测和查明问题根源的能力至关重要。 但是，大规模的 IoT 解决方案可能有数千台设备，因此，手动检查各个设备是不切实际的。 IoT 中心集成了两项 Azure 服务以提供帮助：

* Azure Monitor：使用 Azure Monitor 可以从 IoT 中心收集遥测数据并对其进行分析和处理。 为了帮助你大规模检测、诊断和排查这些问题，请使用 IoT 中心通过 Azure Monitor 提供的监视功能。 这包括设置警报以在连接断开时触发通知和操作，并包括配置日志以便用于查明导致连接断开的情况。

* **Azure 事件网格** - 对于关键基础结构和每设备断开连接，请使用 Azure 事件网格来订阅 IoT 中心发出的设备连接和断开连接事件。 借助 Azure 事件网格，可以使用以下任何事件处理程序：

  - Azure Functions
  - 逻辑应用
  - Azure 自动化
  - WebHook
  - 队列存储
  - 混合连接
  - 事件中心

## <a name="event-grid-vs-azure-monitor"></a>事件网格与Azure Monitor

事件网格提供了一个低延迟、每设备监视解决方案，可用于跟踪关键设备和基础结构的设备连接。 Azure Monitor 提供了一个指标“连接的设备数”，可用于监视连接到 IoT 中心的设备数，并在连接的设备数低于静态阈值时触发警报。

决定对特定方案使用事件网格还是 Azure Monitor 时，请考虑以下事项：

* 警报延迟：通过事件网格传递 IoT 中心连接事件的速度要快得多。 对于需要快速通知的方案，这使得事件网格成为更好的选择。

* 每设备通知：事件网格提供了跟踪各个设备的连接和断开连接的功能。 对于需要监视关键设备的连接的方案，这使得事件网格成为更好的选择。

* 轻型设置：Azure Monitor 指标警报提供了一种轻型设置体验，无需与其他服务集成即可通过电子邮件、短信、语音和其他通知传递通知。  使用事件网格，需要与其他 Azure 服务集成才能传递通知。 这两项服务都可与其他服务集成来触发更复杂的操作。

## <a name="event-grid-monitor-connect-and-disconnect-events"></a>事件网格：监视连接和断开连接事件

若要监视生产环境中的设备连接和断开连接事件，我们建议订阅事件网格中的 [DeviceConnected 和 DeviceDisconnected 事件](iot-hub-event-grid.md#event-types)以触发警报并监视设备连接状态 。 与 Azure Monitor 相比，事件网格的事件延迟要低得多，并且你可以监视每台设备。 这些因素使事件网格成为监视关键设备和基础结构的首选方法。

使用事件网格监视或触发有关设备断开连接的警报时，请确保在使用 Azure IoT SDK 的设备上以筛选掉定期断开连接（SAS 令牌续订所导致）的方式进行生成。 若要了解详细信息，请参阅 [Azure IoT SDK 的 MQTT 设备断开连接行为](#mqtt-device-disconnect-behavior-with-azure-iot-sdks)。

浏览以下主题，详细了解如何使用事件网格监视设备连接事件：

* 有关将事件网格与 IoT 中心结合使用的概述，请参阅[使用事件网格对 IoT 中心事件做出反应](iot-hub-event-grid.md)。 请特别注意[设备已连接和设备已断开连接事件的限制](iot-hub-event-grid.md#limitations-for-device-connected-and-device-disconnected-events)部分。

* 有关对设备连接事件进行排序的教程，请参阅[使用 Azure Cosmos DB 对来自 Azure IoT 中心的设备连接事件进行排序](iot-hub-how-to-order-connection-state-events.md)。

* 有关发送电子邮件通知的教程，请参阅事件网格文档中的[使用事件网格和逻辑应用发送有关 Azure IoT 中心事件的电子邮件通知](../event-grid/publish-iot-hub-events-to-logic-apps.md)。

## <a name="azure-monitor-route-connection-events-to-logs"></a>Azure Monitor：将连接事件路由到日志

IoT 中心持续发出多类操作的资源日志。 不过，若要收集此日志数据，需要创建一个诊断设置，以将日志路由到可对其进行分析或存档的目标。 其中一个目标是通过 Log Analytics 工作区的 Azure Monitor 日志（[请参阅定价](https://azure.microsoft.com/pricing/details/log-analytics/)），可在其中使用 Kusto 查询来分析数据。

IoT 中心[资源日志连接类别](monitor-iot-hub-reference.md#connections)发出与设备连接有关的操作和错误。 以下屏幕截图显示了将这些日志路由到 Log Analytics 工作区的诊断设置：

:::image type="content" source="media/iot-hub-troubleshoot-connectivity/create-diagnostic-setting.png" alt-text="将连接日志发送到 Log Analytics 工作区的推荐设置。":::

建议在创建 IoT 中心后尽早创建诊断设置，因为尽管 IoT 中心始终会发出资源日志，但在将日志路由到目标之前，Azure Monitor 不会收集这些日志。

若要详细了解如何将日志路由到目标，请参阅[路由](monitor-iot-hub.md#collection-and-routing)。 有关创建诊断设置的详细说明，请参阅[使用指标和日志教程](tutorial-use-metrics-and-diags.md)。

## <a name="azure-monitor-set-up-metric-alerts-for-device-disconnects"></a>Azure Monitor：针对设备断开连接设置指标警报

可根据 IoT 中心发出的平台指标设置警报。 通过指标警报，你可通知用户他们关注的情况已发生，还可触发可自动响应该情况的操作。

[连接的设备数（预览版）](monitor-iot-hub-reference.md#device-metrics)指标会告诉你有多少设备已连接到 IoT 中心。 可创建在此指标低于阈值时触发的警报：

:::image type="content" source="media/iot-hub-troubleshoot-connectivity/configure-alert-logic.png" alt-text="连接的设备数指标的警报逻辑设置。":::

可使用指标警报规则大规模地监视设备断开连接异常情况。 即，使用警报来确定何时有大量设备意外断开连接。 如果检测到这种情况，你可以查看日志以帮助排查问题。 但是，若要近实时地监视每台设备的断开连接和关键设备的断开连接，必须使用事件网格。

若要详细了解 IoT 中心的警报，请参阅 [Monitor IoT 中心中的警报](monitor-iot-hub.md#alerts)。 有关在 IoT 中心创建警报的演练，请参阅[使用指标和日志教程](tutorial-use-metrics-and-diags.md)。 有关警报的更详细概述，请参阅 Azure Monitor 文档中的 [Microsoft Azure 中的警报概述](../azure-monitor/alerts/alerts-overview.md)。

## <a name="azure-monitor-use-logs-to-resolve-connectivity-errors"></a>Azure Monitor：使用日志解决连接错误

使用 Azure Monitor 指标警报或事件网格检测到设备断开连接时，可以使用日志来帮助排查原因。 本部分介绍了如何在 Azure Monitor 日志中查找常见问题。 以下步骤假定你已创建可将 IoT 中心连接日志发送到 Log Analytics 工作区的[诊断设置](#azure-monitor-route-connection-events-to-logs)。

创建可将 IoT 中心资源日志发送到 Azure Monitor 日志的诊断设置后，请按照以下步骤在 Azure 门户中查看日志。

1. 在 [Azure 门户](https://portal.azure.com)中导航到 IoT 中心。

1. 在 IoT 中心左侧窗格中的“监视”下，选择“日志” 。

1. 若要隔离 IoT 中心的连接错误日志，请在查询编辑器中输入以下查询，然后选择“运行”：

    ```kusto
    AzureDiagnostics
    | where ( ResourceType == "IOTHUBS" and Category == "Connections" and Level == "Error")
    ```

1. 如果返回了结果，请查看 `OperationName`、`ResultType`（错误代码）和 `ResultDescription`（错误消息），以获取更多详细信息。

   ![错误日志示例](./media/iot-hub-troubleshoot-connectivity/diag-logs.png)

使用以下问题解决指南来帮助解决最常见的错误：

* [400027 ConnectionForcefullyClosedOnNewConnection](iot-hub-troubleshoot-error-400027-connectionforcefullyclosedonnewconnection.md)

* [404104 DeviceConnectionClosedRemotely](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md)

* [401003 IoTHubUnauthorized](iot-hub-troubleshoot-error-401003-iothubunauthorized.md)

* [409002 LinkCreationConflict](iot-hub-troubleshoot-error-409002-linkcreationconflict.md)

* [500001 ServerError](iot-hub-troubleshoot-error-500xxx-internal-errors.md)

* [500008 GenericTimeout](iot-hub-troubleshoot-error-500xxx-internal-errors.md)

## <a name="mqtt-device-disconnect-behavior-with-azure-iot-sdks"></a>Azure IoT SDK 的 MQTT 设备断开连接行为

Azure IoT 设备 SDK 与 IoT 中心断开连接，然后在通过 MQTT（以及基于 WebSocket 的 MQTT）协议续订 SAS 令牌时重新连接。 在日志中，这将显示为信息性设备断开连接和连接事件，有时还伴随错误事件。

默认情况下，所有 SDK 的令牌有效期为 60 分钟；但是在某些 SDK 中，其令牌有效期可由开发人员进行更改。 下表总结了各个 SDK 的令牌有效期、令牌续订和令牌续订行为：

| SDK | 令牌有效期 | 令牌续订 | 续订行为 |
|-----|----------|---------------------|---------|
| .NET | 60 分钟，可配置 | 85% 的有效期，可配置 | 在令牌有效期结束并加上 10 分钟宽限期后，SDK 将断开连接，再重新连接。 信息性事件和错误在日志中生成。 |
| Java | 60 分钟，可配置 | 85% 的有效期，不可配置 | 在令牌有效期结束并加上 10 分钟宽限期后，SDK 将断开连接，再重新连接。 信息性事件和错误在日志中生成。 |
| Node.js | 60 分钟，可配置 | 可配置 | 在令牌续订时，SDK 将断开连接，再重新连接。 日志中仅生成信息性事件。 |
| Python | 60 分钟，可配置 | 过期前的 120 秒 | 在令牌有效期结束时，SDK 将断开连接，再重新连接。 |

以下屏幕截图显示不同 SDK 的 Azure Monitor 日志中的令牌续订行为。 如前所述，令牌有效期和续订阈值已更改了默认值。

* .NET 设备 SDK 的令牌有效期为 1200 秒（20 分钟），其续订设置在 90% 的有效期时进行。 每隔 30 分钟断开连接一次：

    :::image type="content" source="media/iot-hub-troubleshoot-connectivity/net-mqtt.png" alt-text=".NET SDK 的 Azure Monitor 日志中通过 MQTT 的令牌续订的错误行为。":::

* Java SDK 的令牌有效期为 300 秒（5 分钟），并且续订默认在 85% 的有效期时进行。 每隔 15 分钟断开连接一次：

    :::image type="content" source="media/iot-hub-troubleshoot-connectivity/java-mqtt.png" alt-text="Java SDK 的 Azure Monitor 日志中通过 MQTT 的令牌续订的错误行为。":::

* Node SDK 的令牌有效期为 300 秒（5 分钟），并且令牌续订设置在 3 分钟时进行。 令牌续订时会断开连接。 而且，没有错误，只发出信息性连接/断开连接事件：

    :::image type="content" source="media/iot-hub-troubleshoot-connectivity/node-mqtt.png" alt-text="Node SDK 的 Azure Monitor 日志中通过 MQTT 的令牌续订的错误行为。":::

使用以下查询收集结果。 查询会从属性包中提取 SDK 名称和版本。 有关详细信息，请参阅 [IoT 中心日志中的 SDK 版本](monitor-iot-hub.md#sdk-version-in-iot-hub-logs)。

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
| where Category == "Connections"
| extend parsed_json = parse_json(properties_s)
| extend SDKVersion = tostring(parsed_json.sdkVersion) , DeviceId = tostring(parsed_json.deviceId) , Protocol =  tostring(parsed_json.protocol)
| distinct TimeGenerated, OperationName, Level, ResultType, ResultDescription, DeviceId, Protocol, SDKVersion

```

作为 IoT 解决方案开发人员或操作员，需要注意此行为，以便解释连接/断开连接事件以及日志中的相关错误。 如果要更改设备的令牌有效期或续订行为，请检查设备是否实现了设备孪生设置或实现此功能的设备方法。

如果使用事件中心监视设备连接，请确保以筛选出定期断开连接（SAS 令牌续订所导致）的方式进行生成。 例如，只要在发生断开连接事件后，接着在特定时间范围内发生连接事件，就不要基于断开连接触发操作。

> [!NOTE]
> IoT 中心仅支持每个设备一个活动 MQTT 连接。 代表相同设备 ID 的任何新 MQTT 连接都会导致 IoT 中心删除现有连接。
>
> 400027 ConnectionForcefullyClosedOnNewConnection 将记录到 IoT 中心日志中

## <a name="i-tried-the-steps-but-they-didnt-work"></a>我尝试了这些步骤，但没有奏效

如果前面的步骤没有帮助，可尝试以下操作：

* 如果你有权以物理方式或远程访问（例如通过 SSH）有问题的设备，请遵循[设备端故障排除指南](https://github.com/Azure/azure-iot-sdk-node/wiki/Troubleshooting-Guide-Devices)继续进行故障排除。

* 在 Azure 门户 > IoT 中心 > IoT 设备中验证设备是否已启用。

* 如果设备使用 MQTT 协议，请确认端口 8883 已打开。 有关详细信息，请参阅[连接到 IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)。

* 获取有关[适用于 Azure IoT 中心的 Microsoft 常见问题解答页面](/answers/topics/azure-iot-hub.html)、[堆栈溢出](https://stackoverflow.com/questions/tagged/azure-iot-hub)或 [Azure 支持](https://azure.microsoft.com/support/options/)的帮助。

如果本指南未能提供所需的帮助，请在下面的反馈部分中留言，以帮助我们改进文档。

## <a name="next-steps"></a>后续步骤

* 要了解有关解决暂时性问题的详细信息，请参阅[暂时性故障处理](/azure/architecture/best-practices/transient-faults)。

* 要了解有关 Azure IoT SDK 和管理重试的详细信息，请参阅[如何使用 Azure IoT Hub 设备 SDK 管理连接和可靠消息传递](iot-hub-reliability-features-in-sdks.md#connection-and-retry)。
