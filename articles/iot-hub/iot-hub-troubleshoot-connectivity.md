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
ms.openlocfilehash: 81558526500f3c01e975d9a963b4a6a98ec6d753
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96446404"
---
# <a name="monitor-diagnose-and-troubleshoot-disconnects-with-azure-iot-hub"></a>监视、诊断和排查 Azure IoT 中心的连接断开问题

由于存在许多可能的故障点，IoT 设备的连接问题有时很难排查。 应用程序逻辑、物理网络、协议、硬件、IoT 中心和其他云服务都可能导致问题。 检测和查明问题根源的能力至关重要。 但是，大规模的 IoT 解决方案可能有数千台设备，因此，手动检查各个设备是不切实际的。 IoT 中心集成了两个 Azure 服务，可帮助你：

* **Azure Monitor** 为了帮助你按比例检测、诊断和解决这些问题，请使用 IoT 中心通过 Azure Monitor 提供的监视功能。 这包括设置警报，以便在出现断开连接时触发通知和操作，并配置可用于发现导致断开连接的情况的日志。

* **Azure 事件网格** 对于关键基础结构和按设备断开连接，请使用 Azure 事件网格订阅 IoT 中心发出的设备连接和断开连接事件。

在这两种情况下，这些功能仅限于 IoT 中心可以观察到的内容，因此我们还建议遵循监视设备和其他 Azure 服务的最佳实践。

## <a name="event-grid-vs-azure-monitor"></a>事件网格与 Azure Monitor

事件网格提供低延迟的每设备监视解决方案，可用于跟踪关键设备和基础结构的设备连接。 Azure Monitor 提供了一个指标的 *已连接设备*，可用于监视连接到 IoT 中心的设备数，并在该数字低于静态阈值时触发警报。

决定是否针对特定方案使用事件网格或 Azure Monitor 时，请考虑以下事项：

* 警报延迟：通过事件网格更快速地传递 IoT 中心连接事件。 这使事件网格成为需要快速通知的方案的更好选择。

* 每设备通知：事件网格提供了跟踪单个设备的连接和断开连接的功能。 这使事件网格成为需要监视关键设备连接的方案的更好选择。

* 轻型安装： Azure Monitor 指标警报提供了一种轻型设置体验，无需与其他服务集成以通过电子邮件、短信、语音和其他通知传递通知。  在事件网格中，需要与其他 Azure 服务集成，以提供通知。 这两项服务可与其他服务集成，以触发更复杂的操作。

由于在生产环境中，它的每设备性能都非常低，因此强烈建议使用事件网格来监视连接。 当然，选择不是排他的，你可以同时使用 Azure Monitor 指标警报和事件网格。 无论你选择哪种跟踪断开连接，你都可能会使用 Azure Monitor 资源日志来帮助排查意外设备断开连接的原因。 以下各节将更详细地讨论每个选项。

## <a name="event-grid-monitor-device-connect-and-disconnect-events"></a>事件网格：监视设备连接和断开连接事件

若要在生产环境中监视设备连接和断开连接事件，建议订阅事件网格中的 [ **DeviceConnected** 和 **DeviceDisconnected** 事件](iot-hub-event-grid.md#event-types)以触发警报并监视设备连接状态。 与 Azure Monitor 相比，事件网格提供的事件延迟要低得多，并且可以按设备而不是连接的设备总数来监视。 这些因素使事件网格成为监视关键设备和基础结构的首选方法。

使用事件网格监视或触发有关设备断开连接的警报时，请确保以筛选出定期断开连接的方式进行生成，因为使用 Azure IoT Sdk 的设备需要续订 SAS 令牌。 若要了解详细信息，请参阅 [MQTT device disconnect With Azure IoT sdk](#mqtt-device-disconnect-behavior-with-azure-iot-sdks)。

浏览以下主题，了解有关通过事件网格监视设备连接事件的详细信息：

* 有关将事件网格用于 IoT 中心的概述，请参阅 [使用事件网格对 IoT 中心事件做出反应](iot-hub-event-grid.md)。 请特别注意 " [设备已连接" 和 "设备断开连接事件的限制](iot-hub-event-grid.md#limitations-for-device-connected-and-device-disconnected-events) " 部分。

* 有关订购设备连接事件的教程，请参阅 [使用 Azure Cosmos DB 对 Azure IoT 中心的设备连接事件](iot-hub-how-to-order-connection-state-events.md)进行排序。

* 有关发送电子邮件通知的教程，请参阅事件网格文档中的 [使用事件网格和逻辑应用发送有关 Azure IoT 中心事件的电子邮件通知](../event-grid/publish-iot-hub-events-to-logic-apps.md) 。

## <a name="azure-monitor-route-connection-events-to-logs"></a>Azure Monitor：将连接事件路由到日志

IoT 中心持续发出几类操作的资源日志。 不过，若要收集此日志数据，则需要创建一个诊断设置，以便将其路由到可对其进行分析或存档的目标。 一个这样的目标是通过 Log Analytics 工作区 Azure Monitor 日志 ([参阅定价](https://azure.microsoft.com/pricing/details/log-analytics/)) ，使用 Kusto 查询可以分析数据。

IoT 中心 [资源日志连接类别](monitor-iot-hub-reference.md#connections) 发出与设备连接有关的操作和错误。 以下屏幕截图显示了一个诊断设置，用于将这些日志路由到 Log Analytics 工作区：

:::image type="content" source="media/iot-hub-troubleshoot-connectivity/create-diagnostic-setting.png" alt-text="用于将连接日志发送到 Log Analytics 工作区的推荐设置。":::

建议在创建 IoT 中心后尽早创建诊断设置，因为尽管 IoT 中心始终发出资源日志，但在将它们路由到目标之前，不会 Azure Monitor 收集这些日志。

若要了解有关将日志路由到目标的详细信息，请参阅 [集合和路由](monitor-iot-hub.md#collection-and-routing)。 有关创建诊断设置的详细说明，请参阅 [使用度量和日志教程](tutorial-use-metrics-and-diags.md)。

## <a name="azure-monitor-set-up-metric-alerts-for-device-disconnect-at-scale"></a>Azure Monitor：设置指标警报以在大规模进行设备断开连接

你可以根据 IoT 中心发出的平台指标设置警报。 对于指标警报，你可以通知用户感兴趣的条件已发生，并触发可自动响应该条件的操作。

[*已连接的设备 (预览版)*](monitor-iot-hub-reference.md#device-metrics)指标会告诉你有多少设备连接到 IoT 中心。 如果此指标低于阈值，则可以创建要触发的警报：

:::image type="content" source="media/iot-hub-troubleshoot-connectivity/configure-alert-logic.png" alt-text="连接设备指标的警报逻辑设置。":::

您可以使用指标警报规则来监视设备的断开连接异常。 也就是说，当大量设备意外断开连接时。 如果检测到这种情况，可以查看日志以帮助解决问题。 监视关键设备的每个设备的断开连接和断开连接;但是，您必须使用事件网格。 事件网格还提供比 Azure 指标更实时的体验。

若要了解有关 IoT 中心的警报的详细信息，请参阅 [监视 Iot 中心中的警报](monitor-iot-hub.md#alerts)。 有关在 IoT 中心创建警报的演练，请参阅 [使用度量和日志教程](tutorial-use-metrics-and-diags.md)。 有关警报的更详细概述，请参阅 Azure Monitor 文档 [中 Microsoft Azure 的警报概述](../azure-monitor/platform/alerts-overview.md) 。

## <a name="azure-monitor-use-logs-to-resolve-connectivity-errors"></a>Azure Monitor：使用日志解决连接错误

检测到设备断开连接时，无论它是 Azure Monitor 指标警报还是事件网格中，你都可以使用日志来帮助解决此问题。 本部分介绍如何查找 Azure Monitor 日志中的常见问题。 以下步骤假定已创建了一个 [诊断设置](#azure-monitor-route-connection-events-to-logs) ，以便将 IoT 中心连接日志发送到 Log Analytics 工作区。

创建用于将 IoT 中心资源日志路由到 Azure Monitor 日志的诊断设置后，请按照以下步骤在 Azure 门户中查看日志。

1. 在 [Azure 门户](https://portal.azure.com)中导航到 IoT 中心。

1. 在 IoT 中心的左窗格中的 " **监视** " 下，选择 " **日志**"。

1. 若要隔离 IoT 中心的连接错误日志，请在查询编辑器中输入以下查询，然后选择 " **运行**"：

    ```kusto
    AzureDiagnostics
    | where ( ResourceType == "IOTHUBS" and Category == "Connections" and Level == "Error")
    ```

1. 如果返回了结果，请查看 `OperationName`、`ResultType`（错误代码）和 `ResultDescription`（错误消息），以获取有关错误的更多详细信息。

   ![错误日志示例](./media/iot-hub-troubleshoot-connectivity/diag-logs.png)

确定错误后，请按照问题解决指南获取有关最常见错误的帮助：

* [400027 ConnectionForcefullyClosedOnNewConnection](iot-hub-troubleshoot-error-400027-connectionforcefullyclosedonnewconnection.md)

* [404104 DeviceConnectionClosedRemotely](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md)

* [401003 IoTHubUnauthorized](iot-hub-troubleshoot-error-401003-iothubunauthorized.md)

* [409002 LinkCreationConflict](iot-hub-troubleshoot-error-409002-linkcreationconflict.md)

* [500001 ServerError](iot-hub-troubleshoot-error-500xxx-internal-errors.md)

* [500008 GenericTimeout](iot-hub-troubleshoot-error-500xxx-internal-errors.md)

## <a name="mqtt-device-disconnect-behavior-with-azure-iot-sdks"></a>Azure IoT Sdk 的 MQTT 设备断开连接行为

Azure IoT 设备 Sdk 从 IoT 中心断开连接，然后在通过 MQTT (和 MQTT over Websocket) 协议续订 SAS 令牌时重新连接。 在日志中，此信息显示为信息设备断开连接并连接事件有时会伴随错误事件。

默认情况下，所有 Sdk 的令牌生存期为60分钟;但是，它可以由 Sdk 中的开发人员进行更改。 下表总结了每个 Sdk 的令牌生命周期、令牌续订和令牌续订行为：

| SDK 中 IsInRole 中的声明 | 令牌生存期 | 令牌续订 | 续订行为 |
|-----|----------|---------------------|---------|
| .NET | 60分钟，可配置 | 85% 的生命周期，可配置 | SDK 在令牌生命周期加上10分钟的宽限期连接和断开连接。 日志中生成的信息性事件和错误。 |
| Java | 60分钟，可配置 | 85% 的生命周期，不可配置 | SDK 在令牌生命周期加上10分钟的宽限期连接和断开连接。 日志中生成的信息性事件和错误。 |
| Node.js | 60分钟，可配置 | 路宽 | SDK 在令牌续订时进行连接和断开连接。 日志中仅生成信息性事件。 |
| Python | 60分钟，不可配置 | -- | SDK 会在令牌生命周期内连接和断开连接。 |

以下屏幕截图显示了不同 Sdk Azure Monitor 日志中的令牌续订行为。 根据说明，令牌生命周期和续订阈值已更改为默认值。

* 使用1200秒的 .NET 设备 SDK (20 分钟) 令牌生命周期和续订量设置为使用90% 的生命周期。 每隔30分钟断开连接一次：

    :::image type="content" source="media/iot-hub-troubleshoot-connectivity/net-mqtt.png" alt-text="通过 .NET SDK Azure Monitor 日志中的 MQTT 上的令牌续订的错误行为。":::

* Java SDK 300 秒 (5 分钟) 令牌生命周期和默认85% 的期限续订。 每15分钟发生一次断开连接：

    :::image type="content" source="media/iot-hub-troubleshoot-connectivity/java-mqtt.png" alt-text="带 Java SDK Azure Monitor 日志中通过 MQTT 的令牌续订的错误行为。":::

* 使用300秒的 Node SDK (5 分钟) 令牌生命周期和令牌续订设置为3分钟。 令牌续订时出现断开连接。 而且，没有错误，只发出信息性连接/断开连接事件：

    :::image type="content" source="media/iot-hub-troubleshoot-connectivity/node-mqtt.png" alt-text="通过节点 SDK Azure Monitor 日志中通过 MQTT 的令牌续订的错误行为。":::

使用以下查询收集结果。 此查询从属性包提取 SDK 名称和版本;若要了解详细信息，请参阅 [IoT 中心日志中的 SDK 版本](monitor-iot-hub.md#sdk-version-in-iot-hub-logs)。

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
| where Category == "Connections"
| extend parsed_json = parse_json(properties_s)
| extend SDKVersion = tostring(parsed_json.sdkVersion) , DeviceId = tostring(parsed_json.deviceId) , Protocol =  tostring(parsed_json.protocol)
| distinct TimeGenerated, OperationName, Level, ResultType, ResultDescription, DeviceId, Protocol, SDKVersion

```

作为 IoT 解决方案开发人员或操作员，需要注意此行为，以便解释日志中的连接/断开连接事件和相关错误。 如果要更改设备的令牌生命周期或续订行为，请检查设备是否实现了设备克隆设置，或者如何实现此功能。

如果要监视与事件中心的设备连接，请确保以筛选出定期断开连接的方式进行生成，因为需要续订 SAS 令牌。例如，只要断开连接事件后跟某个时间范围内的 connect 事件，则不会触发基于断开连接的操作。

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