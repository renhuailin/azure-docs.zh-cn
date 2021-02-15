---
title: 从 Azure IoT Central 导出数据 |Microsoft Docs
description: 如何使用新的数据导出将 IoT 数据导出到 Azure 和自定义云目标。
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 01/27/2021
ms.topic: how-to
ms.service: iot-central
ms.custom: contperf-fy21q1, contperf-fy21q3
ms.openlocfilehash: d31673b8d789cff5de3ddce63b67a98854b7aabc
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100515897"
---
# <a name="export-iot-data-to-cloud-destinations-using-data-export"></a>使用数据导出将 IoT 数据导出到云目标

> [!Note]
> 本文介绍 IoT Central 中的数据导出功能。 有关旧数据导出功能的信息，请参阅 [使用数据导出将 IoT 数据导出到云目标 (旧) ](./howto-export-data-legacy.md)。

本文介绍如何使用 Azure 中的新数据导出功能 IoT Central。 使用此功能可以从您的 IoT Central 应用程序持续导出已筛选和增加的 IoT 数据。 数据导出会将接近实时的更改推送到云解决方案的其他部分，以获取热路径见解、分析和存储。

例如，可以：

- 以近乎实时的顺序连续导出 JSON 格式的遥测数据和属性更改。
- 筛选数据流以导出与自定义条件匹配的数据。
- 利用来自设备的自定义值和属性值丰富数据流。
- 将数据发送到 Azure 事件中心、Azure 服务总线、Azure Blob 存储和 webhook 终结点等目标。

> [!Tip]
> 当你打开数据导出时，你只会获得那一刻的数据。 当前，数据导出关闭时无法检索数据。 若要保留更多的历史数据，请及早打开数据导出。

## <a name="prerequisites"></a>先决条件

若要使用数据导出功能，您必须具有 [V3 应用程序](howto-get-app-info.md)，并且您必须具有 [数据导出](howto-manage-users-roles.md) 权限。

如果有 V2 应用程序，请参阅将 [V2 IoT Central 应用程序迁移到 V3](howto-migrate.md)。

## <a name="set-up-export-destination"></a>设置导出目标

导出目标必须存在，然后才能配置数据导出。 当前提供以下目标类型：

- Azure 事件中心
- Azure 服务总线队列
- Azure 服务总线主题
- Azure Blob 存储
- Webhook

### <a name="create-an-event-hubs-destination"></a>创建事件中心目标

如果当前没有可导出到的事件中心命名空间，请执行以下步骤：

1. [在 Azure 门户中创建新的事件中心命名空间](https://ms.portal.azure.com/#create/Microsoft.EventHub)。 可以在 [Azure 事件中心文档](../../event-hubs/event-hubs-create.md)中进行详细的了解。

1. 在事件中心命名空间中创建事件中心。 转到命名空间，选择顶部的“+ 事件中心”，以便创建事件中心实例。

1. 生成要在 IoT Central 中设置数据导出时使用的密钥：

    - 选择创建的事件中心实例。
    - 选择 " **设置" > 共享访问策略**"。
    - 创建新的密钥或选择具有 " **发送** " 权限的现有密钥。
    - 复制主连接字符串或辅助连接字符串。 使用此连接字符串在 IoT Central 中设置新的目标。
    - 或者，您可以为整个事件中心命名空间生成连接字符串：
        1. 在 Azure 门户中转到事件中心命名空间。
        2. 在 "**设置**" 下，选择 "**共享访问策略**"
        3. 创建新的密钥或选择具有 " **发送** " 权限的现有密钥。
        4. 复制主连接字符串或辅助连接字符串
        
### <a name="create-a-service-bus-queue-or-topic-destination"></a>创建服务总线队列或主题目标

如果当前没有可导出到的服务总线命名空间，请执行以下步骤：

1. [在 Azure 门户中创建新的服务总线命名空间](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5)。 可以在 [Azure 服务总线文档](../../service-bus-messaging/service-bus-create-namespace-portal.md)中进行详细的了解。

1. 若要创建要导出到的队列或主题，请使用服务总线命名空间，然后选择 " **+ 队列** " 或 " **+ 主题**"。

1. 生成要在 IoT Central 中设置数据导出时使用的密钥：

    - 选择创建的队列或主题。
    - 选择 " **设置"/"共享访问策略**"。
    - 创建新的密钥或选择具有 " **发送** " 权限的现有密钥。
    - 复制主连接字符串或辅助连接字符串。 使用此连接字符串在 IoT Central 中设置新的目标。
    - 或者，您可以为整个 Service Bus 命名空间生成连接字符串：
        1. 在 Azure 门户中，转到“服务总线命名空间”。
        2. 在 "**设置**" 下，选择 "**共享访问策略**"
        3. 创建新的密钥或选择具有 " **发送** " 权限的现有密钥。
        4. 复制主连接字符串或辅助连接字符串

### <a name="create-an-azure-blob-storage-destination"></a>创建 Azure Blob 存储目标

如果没有要导出到的现有 Azure 存储帐户，请执行以下步骤：

1. [在 Azure 门户中创建新的存储帐户](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)。 可以详细了解如何创建新的 [Azure Blob 存储帐户](../../storage/blobs/storage-quickstart-blobs-portal.md) 或 [Azure Data Lake Storage v2 存储帐户](../../storage/common/storage-account-create.md)。 数据导出只能将数据写入支持块 blob 的存储帐户。 以下列表显示了已知的兼容存储帐户类型：

    |性能层|帐户类型|
    |-|-|
    |标准|常规用途 V2|
    |标准|常规用途 V1|
    |标准|Blob 存储|
    |高级|块 Blob 存储|

1. 若要在存储帐户中创建容器，请使用存储帐户。 在“Blob 服务”下选择“浏览 Blob”。 选择顶部的“+ 容器”以创建新容器。

1. 转到 " **设置" > "访问密钥**"，为你的存储帐户生成一个连接字符串。 复制两个连接字符串中的一个。

### <a name="create-a-webhook-endpoint"></a>创建 webhook 终结点

可以将数据导出到公开可用的 HTTP webhook 终结点。 可以使用 [RequestBin](https://requestbin.net/)创建测试 webhook 终结点。 达到请求限制时，RequestBin 限制请求：

1. 打开 [RequestBin](https://requestbin.net/)。
2. 创建一个新的 RequestBin 并复制 **Bin URL**。 在测试数据导出时使用此 URL。

## <a name="set-up-data-export"></a>设置数据导出

现在，你已有将数据导出到的目标，请在 IoT Central 应用程序中设置数据导出：

1. 登录到 IoT Central 应用程序。

1. 在左窗格中，选择 " **数据导出**"。

    > [!Tip]
    > 如果左窗格中未显示 " **数据导出** "，则没有权限在应用中配置数据导出。 请与管理员联系以设置数据导出。

1. 选择 " **+ 新建导出**"。

1. 为新的导出输入显示名称，并确保 **已启用** 数据导出。

1. 选择要导出的数据类型。 下表列出了支持的数据导出类型：

    | 数据类型 | 说明 | 数据格式 |
    | :------------- | :---------- | :----------- |
    |  遥测 | 以近乎实时的速度从设备导出遥测消息。 每个导出的消息都包含原始设备消息的已规范化内容。   |  [遥测消息格式](#telemetry-format)   |
    | 属性更改 | 以近乎实时的速度将更改导出到设备和云属性。 对于只读设备属性，将导出对报告值所做的更改。 对于读写属性，将导出报告的值和所需的值。 | [属性更改消息格式](#property-changes-format) |

<a name="DataExportFilters"></a>
1. （可选）添加筛选器以减少导出的数据量。 每种数据导出类型都有不同类型的筛选器：

    若要筛选遥测，可以：

    - **筛选** 导出的流，使其仅包含与设备名称、设备 ID 和设备模板筛选条件匹配的设备中的遥测。
    - **筛选** 功能：如果在 " **名称** " 下拉列表中选择了遥测项，则导出的流只包含符合筛选条件的遥测。 如果在 " **名称** " 下拉列表中选择 "设备" 或 "云" 属性项，则导出的流只包含其属性与筛选条件相匹配的设备的遥测数据。
    - **消息属性筛选器**：使用设备 sdk 的设备可以在每个遥测消息上发送 *消息属性* 或 *应用程序属性* 。 属性是使用自定义标识符对消息进行标记的键值对包。 若要创建消息属性筛选器，请输入所需的消息属性键，并指定条件。 仅导出属性与指定筛选条件匹配的遥测消息。 支持以下字符串比较运算符：等于、不等于、包含、不包含、存在、不存在。 [详细了解 IoT 中心文档中的应用程序属性](../../iot-hub/iot-hub-devguide-messages-construct.md)。

    若要筛选属性更改，请使用 **功能筛选器**。 选择下拉列表中的属性项。 导出的流只包含所选属性的更改，这些更改符合筛选条件。

<a name="DataExportEnrichmnents"></a>
1. （可选）利用附加的键值对元数据丰富导出的消息。 以下根据适用于遥测和属性更改数据导出类型：

    - **自定义字符串**：向每个消息添加一个自定义静态字符串。 输入任意密钥，并输入任意字符串值。
    - **属性**：将当前设备报告的属性或云属性值添加到每条消息。 输入任意密钥，并选择 "设备" 或 "云" 属性。 如果导出的消息来自没有指定属性的设备，则导出的消息不会获取扩充。

1. 添加新的目标或添加已创建的目标。 选择 " **新建一个** " 链接，并添加以下信息：

    - **目标名称**： IoT Central 中目标的显示名称。
    - **目标类型**：选择目标的类型。 如果尚未设置目标，请参阅 [设置导出目标](#set-up-export-destination)。
    - 对于 Azure 事件中心、Azure 服务总线队列或主题，请粘贴资源的连接字符串，并根据需要输入区分大小写的事件中心、队列或主题名称。
    - 对于 Azure Blob 存储，请粘贴资源的连接字符串，并根据需要输入区分大小写的容器名称。
    - 对于 Webhook，请粘贴 webhook 终结点的回调 URL。 你可以选择配置 webhook 授权 (OAuth 2.0 和授权令牌) 并添加自定义标头。 
        - 对于 OAuth 2.0，仅支持客户端凭据流。 保存目标后，IoT Central 将与 OAuth 提供程序进行通信以检索授权令牌。 对于发送到此目标的每个消息，此标记将附加到 "Authorization" 标头。
        - 对于 "授权令牌"，可以为发送到此目标的每个消息指定将直接附加到 "Authorization" 标头的令牌值。
    - 选择“创建”。

1. 选择 " **+ 目标** "，然后从下拉列表中选择一个目标。 最多可以向单个导出添加5个目标。

1. 完成导出设置后，请选择 " **保存**"。 几分钟后，你的数据将显示在目标中。

## <a name="monitor-your-export"></a>监视导出

除了在 IoT Central 中查看导出状态外，还可以监视通过导出传输的数据量，并观察 Azure Monitor 数据平台中的导出错误。 可以在 PowerShell 或 Azure CLI 的 Azure 门户、REST API 或查询中访问有关导出和设备运行状况的指标。 目前，可以在 Azure Monitor 中监视这些数据导出指标：

1. 应用筛选器之前传入要导出的消息数
2. 通过筛选器传递的消息数
3. 已成功导出到目标的消息数
4. 遇到的错误数
 
[了解有关如何访问 IoT Central 度量值的详细信息。](howto-monitor-application-health.md)

## <a name="destinations"></a>Destinations

### <a name="azure-blob-storage-destination"></a>Azure Blob 存储目标

每分钟导出一次数据，每个文件包含自上一次导出以来发生的一批更改。 导出的数据以 JSON 格式保存。 存储帐户中导出数据的默认路径为：

- 遥测： _{container}/{app-id}/{partition_id}/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_
- 属性更改： _{container}/{app-id}/{partition_id}/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_

若要浏览 Azure 门户中的已导出文件，请导航到该文件，然后选择 " **编辑 blob**"。

### <a name="azure-event-hubs-and-azure-service-bus-destinations"></a>Azure 事件中心和 Azure 服务总线目标

数据将以近乎实时的速度导出。 数据在消息正文中，采用 JSON 格式编码为 UTF-8。

消息的批注或系统属性包包含 `iotcentral-device-id` 、 `iotcentral-application-id` 、 `iotcentral-message-source` 和字段，这些字段的 `iotcentral-message-type` 值与消息正文中相应的字段的值相同。

### <a name="webhook-destination"></a>Webhook 目标

对于 webhook 目标，数据也会以近乎实时的速度导出。 消息正文中的数据的格式与事件中心和服务总线的格式相同。

## <a name="telemetry-format"></a>遥测格式

每个导出的消息都包含在消息正文中发送的完整消息的规范化形式。 消息采用 JSON 格式，并编码为 UTF-8。 每条消息中的信息包括：

- `applicationId`： IoT Central 应用程序的 ID。
- `messageSource`：消息的源 `telemetry` 。
- `deviceId`：发送遥测消息的设备的 ID。
- `schema`：负载架构的名称和版本。
- `templateId`：与设备关联的设备模板的 ID。
- `enrichments`：导出时设置的任何根据。
- `messageProperties`：设备随消息一起发送的附加属性。 这些属性有时称为 *应用程序属性*。 [了解 IoT 中心文档中的详细信息](../../iot-hub/iot-hub-devguide-messages-construct.md)。

对于事件中心和服务总线，IoT Central 在接收到来自设备的消息之后迅速导出一条新消息。 在用户属性中 (也称为应用程序属性) 每条消息， `iotcentral-device-id` `iotcentral-application-id` `iotcentral-message-source` 自动包含、和。

对于 Blob 存储，将每分钟分批和导出一次消息。

下面的示例显示导出的遥测消息：

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
    "messageProperties": {
      "messageProp": "value"
    }
}
```
### <a name="message-properties"></a>消息属性

遥测消息除了包含遥测有效负载之外，还具有元数据的属性。 上一个代码段显示了系统消息的示例 `deviceId` ，例如和 `enqueuedTime` 。 若要了解有关系统消息属性的详细信息，请参阅 [D2C IoT 中心消息的系统属性](../../iot-hub/iot-hub-devguide-messages-construct.md#system-properties-of-d2c-iot-hub-messages)。

如果需要将自定义元数据添加到遥测消息，则可以向遥测消息添加属性。 例如，需要在设备创建消息时添加时间戳。

下面的代码片段演示了在 `iothub-creation-time-utc` 设备上创建该属性时如何将其添加到消息：

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

以下代码片段显示已导出到 Blob 存储的消息中的此属性：

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

每条消息或记录表示对设备或云属性进行的一项更改。 对于设备属性，只会将报告的值中的更改导出为单独的消息。 导出的消息中的信息包括：

- `applicationId`： IoT Central 应用程序的 ID。
- `messageSource`：消息的源 `properties` 。
- `messageType`： `cloudPropertyChange` 、 `devicePropertyDesiredChange` 或 `devicePropertyReportedChange` 。
- `deviceId`：发送遥测消息的设备的 ID。
- `schema`：负载架构的名称和版本。
- `templateId`：与设备关联的设备模板的 ID。
- `enrichments`：导出时设置的任何根据。

对于事件中心和服务总线，IoT Central 会以近乎实时的时间将新的消息数据导出到事件中心、服务总线队列或主题。 在用户属性中 (也称为应用程序属性) 每条消息， `iotcentral-device-id` `iotcentral-application-id` 自动包括、、 `iotcentral-message-source` 和 `iotcentral-message-type` 。

对于 Blob 存储，将每分钟分批和导出一次消息。

下面的示例演示了 Azure Blob 存储中接收到的已导出属性更改消息。

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
        "value": "abc"
    }],
    "enrichments": {
        "userSpecifiedKey" : "sampleValue"
    }
}
```

## <a name="comparison-of-legacy-data-export-and-data-export"></a>旧数据导出和数据导出的比较

下表显示了 [旧的数据导出](howto-export-data-legacy.md) 功能和新的数据导出功能之间的差异：

| 功能  | 旧数据导出 | 新数据导出 |
| :------------- | :---------- | :----------- |
| 可用数据类型 | 遥测、设备、设备模板 | 遥测，属性更改 |
| 筛选 | 无 | 取决于导出的数据类型。 对于遥测，按遥测、消息属性和属性值进行筛选 |
| 根据 | 无 | 使用自定义字符串或设备上的属性值丰富 |
| Destinations | Azure 事件中心、Azure 服务总线队列和主题、Azure Blob 存储 | 与旧数据导出和 webhook 相同|
| 支持的应用程序版本 | V2、V3 | 仅 V3 |
| 明显限制 | 每个应用导出5个，每个导出1个目标 | 10个导出-每个应用的目标连接 |

## <a name="next-steps"></a>后续步骤

了解如何使用新的数据导出后，建议下一步是了解 [如何在 IoT Central 中使用分析](./howto-create-analytics.md)
