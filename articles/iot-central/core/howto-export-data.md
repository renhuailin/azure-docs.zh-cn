---
title: 从 Azure IoT Central 中导出数据 | Microsoft Docs
description: 如何使用新的数据导出功能将 IoT 数据导出到 Azure 和自定义云目标。
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 06/04/2021
ms.topic: how-to
ms.service: iot-central
ms.custom: contperf-fy21q1, contperf-fy21q3
ms.openlocfilehash: 0435fece7394c0a1494e51581bce263cbf1e068a
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114461172"
---
# <a name="export-iot-data-to-cloud-destinations-using-data-export"></a>使用数据导出功能将 IoT 数据导出到云目标

本文介绍如何使用 Azure IoT Central 中的数据导出。 使用此功能，可以从 IoT Central 应用程序连续导出已筛选和扩充的 IoT 数据。 数据导出功能将更改准实时地推送到云解决方案的其他部分，以提供暖路径见解、分析和存储。

例如，你能够：

- 近实时连续导出 JSON 格式的遥测、属性更改、设备连接、设备生命周期和设备模板生命周期数据。
- 筛选数据流，以导出与自定义条件匹配的数据。
- 使用设备中的自定义值和属性值来扩充数据流。
- 将数据发送到 Azure 事件中心、Azure 服务总线、Azure Blob 存储和 Webhook 终结点等目标。

> [!Tip]
> 如果启用数据导出功能，则只能获取自启用此功能以后的数据。 目前，如果禁用数据导出功能，则暂时无法检索数据。 若要保留更多的历史数据，请尽早启用数据导出功能。

## <a name="prerequisites"></a>先决条件

若要使用数据导出功能，则必须有 [V3 应用程序](howto-faq.yml#how-do-i-get-information-about-my-application-)，且必须拥有[数据导出](howto-manage-users-roles.md)权限。

如果有 V2 应用程序，请参阅[将 V2 IoT Central 应用程序迁移到 V3](howto-migrate.md)。

## <a name="set-up-export-destination"></a>设置导出目标

在配置数据导出之前，导出目标必须已经存在。 以下目标类型目前可用：

- Azure 事件中心
- Azure 服务总线队列
- Azure 服务总线主题
- Azure Blob 存储
- Webhook

### <a name="create-an-event-hubs-destination"></a>创建事件中心目标

如果当前没有可导出到的事件中心命名空间，请执行以下步骤：

1. [在 Azure 门户中创建新的事件中心命名空间](https://ms.portal.azure.com/#create/Microsoft.EventHub)。 可以在 [Azure 事件中心文档](../../event-hubs/event-hubs-create.md)中进行详细的了解。

1. 在事件中心命名空间中创建事件中心。 转到命名空间，选择顶部的“+ 事件中心”，以便创建事件中心实例。

1. 生成在 IoT Central 中设置数据导出时使用的密钥：

    - 选择你创建的事件中心实例。
    - 依次选择“设置”>“共享访问策略”。
    - 新建一个密钥，或选择一个具有“发送”权限的现有密钥。
    - 复制主连接字符串或辅助连接字符串。 使用此连接字符串在 IoT Central 中新建目标。
    - 或者，可以为整个事件中心命名空间生成连接字符串：
        1. 在 Azure 门户中，转到你的事件中心命名空间。
        2. 在“设置”下，选择“共享访问策略”。
        3. 新建一个密钥，或选择一个具有“发送”权限的现有密钥。
        4. 复制主连接字符串或辅助连接字符串
        
### <a name="create-a-service-bus-queue-or-topic-destination"></a>创建服务总线队列或主题目标

如果当前没有可导出到的服务总线命名空间，请执行以下步骤：

1. [在 Azure 门户中创建新的服务总线命名空间](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5)。 可以在 [Azure 服务总线文档](../../service-bus-messaging/service-bus-create-namespace-portal.md)中进行详细的了解。

1. 若要创建要导出到的队列或主题，请转到你的服务总线命名空间，然后选择“+ 队列”或“+ 主题”。

1. 生成在 IoT Central 中设置数据导出时使用的密钥：

    - 选择你创建的队列或主题。
    - 选择“设置”/“共享访问策略”。
    - 新建一个密钥，或选择一个具有“发送”权限的现有密钥。
    - 复制主连接字符串或辅助连接字符串。 使用此连接字符串在 IoT Central 中新建目标。
    - 或者，可以为整个服务总线命名空间生成连接字符串：
        1. 在 Azure 门户中，转到“服务总线命名空间”。
        2. 在“设置”下，选择“共享访问策略”。
        3. 新建一个密钥，或选择一个具有“发送”权限的现有密钥。
        4. 复制主连接字符串或辅助连接字符串

### <a name="create-an-azure-blob-storage-destination"></a>创建 Azure Blob 存储目标

如果还没有可导出到的现有 Azure 存储帐户，请按以下步骤操作：

1. [在 Azure 门户中创建新的存储帐户](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)。 可以详细了解如何新建 [Azure Blob 存储帐户](../../storage/blobs/storage-quickstart-blobs-portal.md)或 [Azure Data Lake Storage v2 存储帐户](../../storage/common/storage-account-create.md)。 数据导出功能只能将数据写入到支持块 blob 的存储帐户中。 下表列出了已知的兼容存储帐户类型：

    |性能层|帐户类型|
    |-|-|
    |Standard|常规用途 V2|
    |Standard|常规用途 V1|
    |Standard|Blob 存储|
    |高级|块 blob 存储|

1. 若要在存储帐户中创建容器，请转到你的存储帐户。 在“Blob 服务”下选择“浏览 Blob”。 选择顶部的“+ 容器”以创建新容器。

1. 依次转到“设置”>“访问密钥”，为你的存储帐户生成连接字符串。 复制两个连接字符串中的一个。

### <a name="create-a-webhook-endpoint"></a>创建 Webhook 终结点

可以将数据导出到公开可用的 HTTP Webhook 终结点。 可以使用 [RequestBin](https://requestbin.net/) 创建测试 Webhook 终结点。 当达到请求限制时，RequestBin 会对请求进行限制：

1. 打开 [RequestBin](https://requestbin.net/)。
2. 创建一个新的 RequestBin 并复制 **Bin URL**。 在测试数据导出时，要用到此 URL。

## <a name="set-up-data-export"></a>设置数据导出

至此，你已经有了可以将数据导出到的目标；接下来，将在 IoT Central 应用程序中设置数据导出：

1. 登录到 IoT Central 应用程序。

1. 在左侧窗格中，选择“数据导出”。

    > [!Tip]
    > 如果在左侧窗格中没有看到“数据导出”，则表明你没有在应用中配置数据导出所需的权限。 请与管理员联系以设置数据导出。

1. 选择“+ 新建导出”。

1. 为新的导出输入显示名称，并确保数据导出功能处于“已启用”状态。

1. 选择要导出的数据的类型。 下表列出了支持的数据导出类型：

    | 数据类型 | 说明 | 数据格式 |
    | :------------- | :---------- | :----------- |
    |  遥测 | 准实时地从设备中导出遥测消息。 每个导出的消息都包含原始设备消息的完整内容（经过规范化处理）。   |  [遥测消息格式](#telemetry-format)   |
    | 属性更改 | 准实时地导出设备和云属性的更改。 对于只读设备属性，将导出对报告值的更改。 对于读写属性，报告值和所需值都会被导出。 | [属性更改消息格式](#property-changes-format) |
    | 设备连接 | 导出设备已连接和断开连接事件。 | [设备连接消息格式](#device-connectivity-changes-format) |
    | 设备生命周期 | 导出设备已注册、已删除、已预配、已启用、已禁用、displayNameChanged 和 deviceTemplateChanged 事件。 | [设备生命周期更改消息格式](#device-lifecycle-changes-format) |
    | 设备模板生命周期 | 导出发布的设备模板更改，包括已创建、已更新和已删除事件。 | [设备模板生命周期更改消息格式](#device-template-lifecycle-changes-format) | 

1. （可选）添加筛选器来减少导出的数据量。 对于每种数据导出类型，都有不同类型的筛选器可用：<a name="DataExportFilters"></a>
    
    | 数据类型 | 可用的过滤器| 
    |--------------|------------------|
    |遥测|<ul><li>按设备名称、设备 ID、设备模板以及设备是否模拟进行筛选</li><li>筛选流以仅包含符合筛选条件的遥测数据</li><li>筛选流以仅包含设备中其属性与筛选条件匹配的遥测数据</li><li>筛选流以仅包含消息属性符合筛选条件的遥测数据。 消息属性（也称为应用程序属性）将在每个遥测消息上的键值对包中发送，该消息由使用设备 SDK 的设备选择性发送 。 若要创建消息属性筛选器，请输入要查找的消息属性键，并指定条件。 只导出属性与指定筛选条件匹配的遥测消息。 [从 IoT 中心文档中详细了解应用程序属性](../../iot-hub/iot-hub-devguide-messages-construct.md) </li></ul>|
    |属性更改|<ul><li>按设备名称、设备 ID、设备模板以及设备是否模拟进行筛选</li><li>筛选流以仅包含符合筛选条件的属性更改</li></ul>|
    |设备连接|<ul><li>按设备名称、设备 ID、设备模板以及设备是否模拟进行筛选</li><li>筛选流以仅包含设备中其属性与筛选条件匹配的更改</li></ul>|
    |设备生命周期|<ul><li>按设备名称、设备 ID、设备模板以及设备是否预配、启用或模拟进行筛选</li><li>筛选流以仅包含设备中其属性与筛选条件匹配的更改</li></ul>|
    |设备模板生命周期|<ul><li>按设备模板进行筛选</li></ul>|
    
1. （可选）使用额外的键值对元数据来扩充导出的消息。 以下扩充可用于遥测、属性更改、设备连接和设备生命周期数据导出类型：<a name="DataExportEnrichmnents"></a>
    - 自定义字符串：向每条消息添加一个自定义静态字符串。 输入任意键，并输入任意字符串值。
    - 属性，将添加到每条消息中：
       - 设备元数据，例如设备名称、设备模板名称、是否启用、预配和模拟
       - 将当前设备报告的属性或云属性值添加到每条消息中。 如果导出的消息来自没有指定属性的设备，则导出的消息不会得到扩充。

1. 添加新目标或你已经创建的目标。 选择“新建一个”链接，然后添加以下信息：

    - 目标名称：目标在 IoT Central 中的显示名称。
    - 目标类型：选择目标类型。 如果还没有设置目标，请参阅[设置导出目标](#set-up-export-destination)。
    - 对于 Azure 事件中心、Azure 服务总线队列或主题，粘贴资源的连接字符串，并在必要时输入区分大小写的事件中心、队列或主题名称。
    - 对于 Azure Blob 存储，粘贴资源的连接字符串，并在必要时输入区分大小写的容器名称。
    - 对于 Webhook，粘贴 Webhook 终结点的回叫 URL。 可以视需要选择配置 Webhook 授权（OAuth 2.0 和授权令牌），并添加自定义头。 
        - 对于 OAuth 2.0，只支持客户端凭据流。 在保存目标后，IoT Central 会与 OAuth 提供程序通信来检索授权令牌。 对于发送到此目标的每条消息，此令牌都会附加到“Authorization”头。
        - 对于授权令牌，可以为发送到此目标的每条消息指定将直接附加到“Authorization”头的令牌值。
    - 选择“创建”  。

1. 选择“+ 目标”，然后从下拉列表中选择目标。 最多可以向一个导出添加五个目标。

1. 设置完导出后，选择“保存”。 几分钟后，数据就会出现在目标中。

## <a name="monitor-your-export"></a>监视导出

除了可以在 IoT Central 中查看导出的状态之外，还可以使用 [Azure Monitor](../../azure-monitor/overview.md) 查看要导出的数据量以及任何导出错误。 可以使用 REST API 或在 PowerShell 或 Azure CLI 中使用查询，在 Azure 门户的图表中访问导出和设备运行状况指标。 目前，可以在 Azure Monitor 中监视以下数据导出指标：

- 在应用筛选器之前传入导出的消息数。
- 通过筛选器的消息数。
- 已成功导出到目标的消息数。
- 遇到的错误数。

若要了解详细信息，请参阅[监视应用程序运行状况](howto-manage-iot-central-from-portal.md#monitor-application-health)。

## <a name="destinations"></a>Destinations

### <a name="azure-blob-storage-destination"></a>Azure Blob 存储目标

数据每分钟导出一次，每个文件包含自上次导出以来的一批更改。 导出的数据以 JSON 格式保存。 导出的数据在存储帐户中的默认路径为：

- 遥测：{container}/{app-id}/{partition_id}/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}
- 属性更改：{container}/{app-id}/{partition_id}/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}

若要在 Azure 门户中浏览导出的文件，请转到文件，然后选择“编辑 blob”。

### <a name="azure-event-hubs-and-azure-service-bus-destinations"></a>Azure 事件中心和 Azure 服务总线目标

数据导出是准实时的。 数据在消息正文中，以 JSON 格式编码为 UTF-8。

消息的注释或系统属性包中包含 `iotcentral-device-id`、`iotcentral-application-id`、`iotcentral-message-source` 和 `iotcentral-message-type` 字段，这些字段的值与消息正文中相应的字段相同。

### <a name="webhook-destination"></a>Webhook 目标

对于 Webhook 目标，数据导出也是准实时的。 消息正文中的数据采用与事件中心和服务总线相同的格式。

## <a name="telemetry-format"></a>遥测格式

每个导出的消息都包含设备在消息正文中发送的规范化形式的完整消息。 消息以 JSON 格式编码为 UTF-8。 每个消息中的信息包括：

- `applicationId`：IoT Central 应用程序的 ID。
- `messageSource`：消息源 - `telemetry`。
- `deviceId`：发送遥测消息的设备的 ID。
- `schema`：有效负载架构的名称和版本。
- `templateId`：与设备关联的设备模板的 ID。
- `enqueuedTime`：IoT Central 收到此消息的时间。
- `enrichments`：在导出中设置的任何扩充。
- `module`：发送此消息的 IoT Edge 模块。 仅当消息来自 IoT Edge 模块时才会显示此字段。
- `component`：发送此消息的组件。 仅当在消息中发送的功能在设备模板中建模为组件时，才会显示此字段
- `messageProperties`：设备随消息一起发送的附加属性。 这些属性有时亦称为“应用程序属性”。 [从 IoT 中心文档中了解详细信息](../../iot-hub/iot-hub-devguide-messages-construct.md)。

对于事件中心和服务总线，IoT Central 在从设备接收到消息后迅速导出新消息。 在每个消息的用户属性（亦称为“应用程序属性”）中，自动包含了 `iotcentral-device-id`、`iotcentral-application-id` 和 `iotcentral-message-source`。

对于 Blob 存储，消息被批处理并每分钟导出一次。

下面的示例展示了导出的遥测消息：

```json

{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "telemetry",
    "deviceId": "1vzb5ghlsg1",
    "schema": "default@v1",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:26:55.455Z",
    "telemetry": {
        "Activity": "running",
        "BloodPressure": {
            "Diastolic": 7,
            "Systolic": 71
        },
        "BodyTemperature": 98.73447010562934,
        "HeartRate": 88,
        "HeartRateVariability": 17,
        "RespiratoryRate": 13
    },
    "enrichments": {
      "userSpecifiedKey": "sampleValue"
    },
    "module": "VitalsModule",
    "component": "DeviceComponent",
    "messageProperties": {
      "messageProp": "value"
    }
}
```
### <a name="message-properties"></a>消息属性

除了遥测有效负载之外，遥测消息还有用于元数据的属性。 上面的代码片段展示了 `deviceId` 和 `enqueuedTime` 等系统消息的示例。 若要详细了解系统消息属性，请参阅 [D2C IoT 中心消息的系统属性](../../iot-hub/iot-hub-devguide-messages-construct.md#system-properties-of-d2c-iot-hub-messages)。

如果需要向遥测消息中添加自定义元数据，则可以向遥测消息添加属性。 例如，需要在设备创建消息时添加时间戳。

下面的代码片段展示了如何在设备上创建消息时将 `iothub-creation-time-utc` 属性添加到消息中：

> [!IMPORTANT]
> 此时间戳的格式必须为 UTC（不含时区信息）。 例如，`2021-04-21T11:30:16Z` 有效，`2021-04-21T11:30:16-07:00` 无效。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
async function sendTelemetry(deviceClient, index) {
  console.log('Sending telemetry message %d...', index);
  const msg = new Message(
    JSON.stringify(
      deviceTemperatureSensor.updateSensor().getCurrentTemperatureObject()
    )
  );
  msg.properties.add("iothub-creation-time-utc", new Date().toISOString());
  msg.contentType = 'application/json';
  msg.contentEncoding = 'utf-8';
  await deviceClient.sendEvent(msg);
}
```

# <a name="java"></a>[Java](#tab/java)

```java
private static void sendTemperatureTelemetry() {
  String telemetryName = "temperature";
  String telemetryPayload = String.format("{\"%s\": %f}", telemetryName, temperature);

  Message message = new Message(telemetryPayload);
  message.setContentEncoding(StandardCharsets.UTF_8.name());
  message.setContentTypeFinal("application/json");
  message.setProperty("iothub-creation-time-utc", Instant.now().toString());

  deviceClient.sendEventAsync(message, new MessageIotHubEventCallback(), message);
  log.debug("My Telemetry: Sent - {\"{}\": {}°C} with message Id {}.", telemetryName, temperature, message.getMessageId());
  temperatureReadings.put(new Date(), temperature);
}
```

# <a name="c"></a>[C#](#tab/csharp)

```csharp
private async Task SendTemperatureTelemetryAsync()
{
  const string telemetryName = "temperature";

  string telemetryPayload = $"{{ \"{telemetryName}\": {_temperature} }}";
  using var message = new Message(Encoding.UTF8.GetBytes(telemetryPayload))
  {
      ContentEncoding = "utf-8",
      ContentType = "application/json",
  };
  message.Properties.Add("iothub-creation-time-utc", DateTime.UtcNow.ToString("yyyy-MM-ddTHH:mm:ssZ"));
  await _deviceClient.SendEventAsync(message);
  _logger.LogDebug($"Telemetry: Sent - {{ \"{telemetryName}\": {_temperature}°C }}.");
}
```

# <a name="python"></a>[Python](#tab/python)

```python
async def send_telemetry_from_thermostat(device_client, telemetry_msg):
    msg = Message(json.dumps(telemetry_msg))
    msg.custom_properties["iothub-creation-time-utc"] = datetime.now(timezone.utc).isoformat()
    msg.content_encoding = "utf-8"
    msg.content_type = "application/json"
    print("Sent message")
    await device_client.send_message(msg)
```

---

下面的代码片段展示了导出到 Blob 存储的消息中的此属性：

```json
{
  "applicationId":"5782ed70-b703-4f13-bda3-1f5f0f5c678e",
  "messageSource":"telemetry",
  "deviceId":"sample-device-01",
  "schema":"default@v1",
  "templateId":"urn:modelDefinition:mkuyqxzgea:e14m1ukpn",
  "enqueuedTime":"2021-01-29T16:45:39.143Z",
  "telemetry":{
    "temperature":8.341033560421833
  },
  "messageProperties":{
    "iothub-creation-time-utc":"2021-01-29T16:45:39.021Z"
  },
  "enrichments":{}
}
```

## <a name="property-changes-format"></a>属性更改格式

每个消息或记录都表示对设备或云属性的更改。 导出的消息中的信息包括：

- `applicationId`：IoT Central 应用程序的 ID。
- `messageSource`：消息源 - `properties`。
- `messageType`：`cloudPropertyChange`、`devicePropertyDesiredChange` 或 `devicePropertyReportedChange`。
- `deviceId`：发送遥测消息的设备的 ID。
- `schema`：有效负载架构的名称和版本。
- `enqueuedTime`：IoT Central 检测到此更改的时间。
- `templateId`：与设备关联的设备模板的 ID。
- `properties`：已更改的属性的数组，包括已更改的属性和值的名称。 如果属性在组件或 IoT Edge 模块内建模，则包含组件和模块信息。
- `enrichments`：在导出中设置的任何扩充。
- 
对于事件中心和服务总线，IoT Central 将新消息数据准实时地导出到事件中心或服务总线队列/主题。 在每个消息的用户属性（亦称为“应用程序属性”）中，自动包含了 `iotcentral-device-id`、`iotcentral-application-id`、`iotcentral-message-source` 和 `iotcentral-message-type`。

对于 Blob 存储，消息被批处理并每分钟导出一次。

下面的示例展示了在 Azure Blob 存储中接收到的导出的属性更改消息。

```json
{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "properties",
    "messageType": "cloudPropertyChange",
    "deviceId": "18a985g1fta",
    "schema": "default@v1",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:37:32.942Z",
    "properties": [{
        "name": "MachineSerialNumber",
        "value": "abc",
        "module": "VitalsModule",
        "component": "DeviceComponent"
    }],
    "enrichments": {
        "userSpecifiedKey" : "sampleValue"
    }
}
```
## <a name="device-connectivity-changes-format"></a>设备连接更改格式

每条消息或记录表示单个设备遇到的连接事件。 导出的消息中的信息包括：

- `applicationId`：IoT Central 应用程序的 ID。
- `messageSource`：消息源 - `deviceConnectivity`。
- `messageType`：`connected` 或 `disconnected`。
- `deviceId`：已更改的设备的 ID。
- `schema`：有效负载架构的名称和版本。
- `templateId`：与设备关联的设备模板的 ID。
- `enqueuedTime`：在 IoT Central 中发生此更改的时间。
- `enrichments`：在导出中设置的任何扩充。

对于事件中心和服务总线，IoT Central 将新消息数据准实时地导出到事件中心或服务总线队列/主题。 在每个消息的用户属性（亦称为“应用程序属性”）中，自动包含了 `iotcentral-device-id`、`iotcentral-application-id`、`iotcentral-message-source` 和 `iotcentral-message-type`。

对于 Blob 存储，消息被批处理并每分钟导出一次。

以下示例演示了在 Azure Blob 存储中收到的已导出设备连接消息。

```json
{
  "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
  "messageSource": "deviceConnectivity",
  "messageType": "connected",
  "deviceId": "1vzb5ghlsg1",
  "schema": "default@v1",
  "templateId": "urn:qugj6vbw5:___qbj_27r",
  "enqueuedTime": "2021-04-05T22:26:55.455Z",
  "enrichments": {
    "userSpecifiedKey": "sampleValue"
  }
}

```
## <a name="device-lifecycle-changes-format"></a>设备生命周期更改格式

每条消息或记录表示对单个设备的一项更改。 导出的消息中的信息包括：

- `applicationId`：IoT Central 应用程序的 ID。
- `messageSource`：消息源 - `deviceLifecycle`。
- `messageType`：发生的更改类型。 `registered`、`deleted`、`provisioned`、`enabled`、`disabled`、`displayNameChanged` 和 `deviceTemplateChanged` 之一。
- `deviceId`：已更改的设备的 ID。
- `schema`：有效负载架构的名称和版本。
- `templateId`：与设备关联的设备模板的 ID。
- `enqueuedTime`：在 IoT Central 中发生此更改的时间。
- `enrichments`：在导出中设置的任何扩充。

对于事件中心和服务总线，IoT Central 将新消息数据准实时地导出到事件中心或服务总线队列/主题。 在每个消息的用户属性（亦称为“应用程序属性”）中，自动包含了 `iotcentral-device-id`、`iotcentral-application-id`、`iotcentral-message-source` 和 `iotcentral-message-type`。

对于 Blob 存储，消息被批处理并每分钟导出一次。

以下示例演示了在 Azure Blob 存储中收到的已导出设备生命周期消息。

```json
{
  "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
  "messageSource": "deviceLifecycle",
  "messageType": "registered",
  "deviceId": "1vzb5ghlsg1",
  "schema": "default@v1",
  "templateId": "urn:qugj6vbw5:___qbj_27r",
  "enqueuedTime": "2021-01-01T22:26:55.455Z",
  "enrichments": {
    "userSpecifiedKey": "sampleValue"
  }
}
```
## <a name="device-template-lifecycle-changes-format"></a>设备模板生命周期更改格式

每条消息或记录表示对单个已发布设备模板的一项更改。 导出的消息中的信息包括：

- `applicationId`：IoT Central 应用程序的 ID。
- `messageSource`：消息源 - `deviceTemplateLifecycle`。
- `messageType`：`created`、`updated` 或 `deleted`。
- `schema`：有效负载架构的名称和版本。
- `templateId`：与设备关联的设备模板的 ID。
- `enqueuedTime`：在 IoT Central 中发生此更改的时间。
- `enrichments`：在导出中设置的任何扩充。

对于事件中心和服务总线，IoT Central 将新消息数据准实时地导出到事件中心或服务总线队列/主题。 在每个消息的用户属性（亦称为“应用程序属性”）中，自动包含了 `iotcentral-device-id`、`iotcentral-application-id`、`iotcentral-message-source` 和 `iotcentral-message-type`。

对于 Blob 存储，消息被批处理并每分钟导出一次。

以下示例演示了在 Azure Blob 存储中收到的已导出设备生命周期消息。

```json
{
  "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
  "messageSource": "deviceTemplateLifecycle",
  "messageType": "created",
  "schema": "default@v1",
  "templateId": "urn:qugj6vbw5:___qbj_27r",
  "enqueuedTime": "2021-01-01T22:26:55.455Z",
  "enrichments": {
    "userSpecifiedKey": "sampleValue"
  }
}
```

## <a name="comparison-of-legacy-data-export-and-data-export"></a>新旧数据导出功能比较

下表列出了[旧数据导出](howto-export-data-legacy.md)功能与新数据导出功能的区别：

| 功能  | 旧数据导出 | 新数据导出 |
| :------------- | :---------- | :----------- |
| 可用数据类型 | 遥测、设备、设备模板 | 遥测、属性更改、设备连接性更改、设备生命周期更改、设备模板生命周期更改 |
| 筛选 | None | 取决于导出的数据类型。 对于遥测，按遥测、消息属性和属性值筛选 |
| 扩充 | None | 使用设备上的自定义字符串或属性值进行扩充 |
| Destinations | Azure 事件中心、Azure 服务总线队列和主题、Azure Blob 存储 | 与旧数据导出加上 Webhook 相同|
| 支持的应用程序版本 | V2、V3 | 仅 V3 |
| 明显限制 | 5 个导出/应用，1 个目标/导出 | 10 个导出与目标连接/应用 |

## <a name="next-steps"></a>后续步骤

至此，你已经知道如何使用新数据导出功能，建议下一步是了解[如何在 IoT Central 中使用分析](./howto-create-analytics.md)
