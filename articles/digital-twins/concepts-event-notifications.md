---
title: 事件通知
titleSuffix: Azure Digital Twins
description: 了解如何解释不同的事件类型及其不同的通知消息。
author: baanders
ms.author: baanders
ms.date: 4/8/2021
ms.topic: conceptual
ms.service: digital-twins
ms.custom: contperf-fy21q4
ms.openlocfilehash: d008888968f05641786cdfcb73afac1d540b7596
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122769865"
---
# <a name="event-notifications"></a>事件通知

Azure 数字孪生中不同的事件会生成 **通知**，解决方案后端可以通过这些通知了解何时发生了不同的操作。 然后，这些通知将[路由](concepts-route-events.md)到 Azure 数字孪生内部和外部的、可以使用此信息来采取措施的不同位置。

可以生成多种类型的通知，通知消息可能会根据生成通知的事件类型而显示不同的内容。 本文提供有关不同消息类型的详细信息及其可能显示的内容。

以下图表显示了不同的通知类型：

[!INCLUDE [digital-twins-notifications.md](../../includes/digital-twins-notifications.md)]

## <a name="notification-structure"></a>通知结构

一般情况下，通知由两个部分构成：头和正文。 

### <a name="event-notification-headers"></a>事件通知头

通知消息头用键值对表示。 根据所用的协议（MQTT、AMQP 或 HTTP），将以不同的方式序列化消息头。 本部分介绍有关通知消息头的一般信息（不考虑所选的具体协议和序列化是什么）。

某些通知遵守 [CloudEvents](https://cloudevents.io/) 标准。 下面描述了 CloudEvents 遵从性。
* 从设备发出的通知继续遵守现有的通知规范
* 由 IoT 中心处理和发出的通知继续遵守现有的通知规范，但 IoT 中心选择支持 CloudEvents（例如通过事件网格）时除外
* 从具有[模型](concepts-models.md)的[数字孪生](concepts-twins-graph.md)发出的通知遵守 CloudEvents
* 由 Azure 数字孪生处理和发出的通知遵守 CloudEvents

服务必须在所有通知中添加一个序号以指示通知的顺序，或者以其他某种方式保持通知自身的顺序。 

Azure 数字孪生向事件网格发出的通知将按照 CloudEvents 架构或 EventGridEvent 架构自动设置格式，具体取决于事件网格主题中定义的架构类型。 

头的扩展属性将添加为有效负载内部的事件网格架构的属性。 

### <a name="event-notification-bodies"></a>事件通知正文

此处以 JSON 格式描述通知消息正文。 可以根据消息正文所需的序列化（例如，使用 JSON、CBOR、Protobuf 等），以不同的方式将消息正文序列化。

正文包含的字段集将因通知类型而异。

以下部分更详细地介绍 IoT 中心和 Azure 数字孪生（或其他 Azure IoT 服务）发出的不同类型的通知。 在其中可以了解触发每种通知类型的因素，以及每种类型的通知正文包含的字段集。

## <a name="digital-twin-change-notifications"></a>数字孪生更改通知

更新数字孪生时会触发 **数字孪生更改通知**，例如：
* 属性值或元数据发生更改时。
* 数字孪生或组件元数据发生更改时。 这种情况的一个例子是更改数字孪生的模型。

### <a name="properties"></a>属性

下面是数字孪生更改通知正文中的字段。

| 名称    | 值 |
| --- | --- |
| `id` | 通知的标识符，例如一个 UUID，或服务维护的计数器。 `source` + `id` 对于每个相异事件都是唯一的 |
| `source` | IoT 中心或 Azure 数字孪生实例的名称，例如 *myhub.azure-devices.net* 或 *mydigitaltwins.westus2.azuredigitaltwins.net* |
| `data` | 描述对孪生体进行的更新的 JSON 修补文档。 有关详细信息，请参阅下面的[正文详细信息](#body-details)。 |
| `specversion` | *1.0*<br>消息遵守此版本的 [CloudEvents 规范](https://github.com/cloudevents/spec)。 |
| `type` | `Microsoft.DigitalTwins.Twin.Update` |
| `datacontenttype` | `application/json` |
| `subject` | 数字孪生的 ID |
| `time` | 在数字孪生上发生该操作时的时间戳 |
| `traceparent` | 事件的 W3C 跟踪上下文 |

### <a name="body-details"></a>正文详细信息

在消息中，`data` 字段包含 JSON 修补文档，该文档包含对数字孪生体的更新。

例如，假设已使用以下补丁更新了数字孪生。

:::code language="json" source="~/digital-twins-docs-samples/models/patch-component-2.json":::

相应通知（如果由像更新某个数字孪生体的 Azure 数字孪生这样的服务同步执行）中的数据将包含如下所示的正文：

```json
{
    "modelId": "dtmi:example:com:floor4;2",
    "patch": [
      {
        "value": 40,
        "path": "/Temperature",
        "op": "replace"
      },
      {
        "value": 30,
        "path": "/comp1/prop1",
        "op": "add"
      }
    ]
  }
```

此数据将出现在生命周期通知消息的 `data` 字段中。

## <a name="digital-twin-lifecycle-notifications"></a>数字孪生体生命周期通知

无论[数字孪生](concepts-twins-graph.md)是否代表 [Azure 数字孪生中的 IoT 中心设备](how-to-ingest-iot-hub-data.md)，数字孪生都会发出通知。 数字孪生之所以执行此操作是因为生命周期通知，生命周期通知与数字孪生体本身有关。

出现以下情况时会触发生命周期通知：
* 创建了数字孪生
* 删除了数字孪生

### <a name="properties"></a>属性

下面是生命周期通知正文中的字段。

| 名称 | 值 |
| --- | --- |
| `id` | 通知的标识符，例如一个 UUID，或服务维护的计数器。 `source` + `id` 对于每个相异事件都是唯一的。 |
| `source` | IoT 中心或 Azure 数字孪生实例的名称，例如 *myhub.azure-devices.net* 或 *mydigitaltwins.westus2.azuredigitaltwins.net* |
| `data` | 遇到生命周期事件的孪生体数据。 有关详细信息，请参阅下面的[正文详细信息](#body-details-1)。 |
| `specversion` | *1.0*<br>消息遵守此版本的 [CloudEvents 规范](https://github.com/cloudevents/spec)。 |
| `type` | `Microsoft.DigitalTwins.Twin.Create`<br>`Microsoft.DigitalTwins.Twin.Delete` |
| `datacontenttype` | `application/json` |
| `subject` | 数字孪生的 ID |
| `time` | 在孪生上发生该操作时的时间戳 |
| `traceparent` | 事件的 W3C 跟踪上下文 |

### <a name="body-details"></a>正文详细信息

下面是生命周期通知消息的示例： 

```json
{
  "specversion": "1.0",
  "id": "d047e992-dddc-4a5a-b0af-fa79832235f8",
  "type": "Microsoft.DigitalTwins.Twin.Create",
  "source": "contoso-adt.api.wus2.digitaltwins.azure.net",
  "data": {
    "$dtId": "floor1",
    "$etag": "W/\"e398dbf4-8214-4483-9d52-880b61e491ec\"",
    "$metadata": {
      "$model": "dtmi:example:Floor;1"
    }
  },
  "subject": "floor1",
  "time": "2020-06-23T19:03:48.9700792Z",
  "datacontenttype": "application/json",
  "traceparent": "00-18f4e34b3e4a784aadf5913917537e7d-691a71e0a220d642-01"
}
```

在消息中，`data` 字段包含受影响数字孪生体的数据（以 JSON 格式表示）。 `data` 字段的架构是 *数字孪生资源 7.1*。

对于创建事件，`data` 有效负载将反映创建资源后的孪生状态，因此，它应包含系统生成的所有元素，就如同一个 `GET` 调用一样。

下面是包含组件但不包含顶级属性的某个 [IoT 即插即用](../iot-develop/overview-iot-plug-and-play.md)设备的数据示例。 应该省略对设备没有意义的属性（例如报告的属性）。 以下 JSON 对象是将出现在生命周期通知消息的 `data` 字段中的信息：

```json
{
  "$dtId": "device-digitaltwin-01",
  "$etag": "W/\"e59ce8f5-03c0-4356-aea9-249ecbdc07f9\"",
  "thermostat": {
    "temperature": 80,
    "humidity": 45,
    "$metadata": {
      "$model": "dtmi:com:contoso:Thermostat;1",
      "temperature": {
        "desiredValue": 85,
        "desiredVersion": 3,
        "ackVersion": 2,
        "ackCode": 200,
        "ackDescription": "OK"
      },
      "humidity": {
        "desiredValue": 40,
        "desiredVersion": 1,
        "ackVersion": 1,
        "ackCode": 200,
        "ackDescription": "OK"
      }
    }
  },
  "$metadata": {
    "$model": "dtmi:com:contoso:Thermostat_X500;1",
  }
}
```

下面是数字孪生体数据的另一个示例。 此示例基于[模型](concepts-models.md)，且不支持组件：

```json
{
  "$dtId": "logical-digitaltwin-01",
  "$etag": "W/\"e59ce8f5-03c0-4356-aea9-249ecbdc07f9\"",
  "avgTemperature": 70,
  "comfortIndex": 85,
  "$metadata": {
    "$model": "dtmi:com:contoso:Building;1",
    "avgTemperature": {
      "desiredValue": 72,
      "desiredVersion": 5,
      "ackVersion": 4,
      "ackCode": 200,
      "ackDescription": "OK"
    },
    "comfortIndex": {
      "desiredValue": 90,
      "desiredVersion": 1,
      "ackVersion": 3,
      "ackCode": 200,
      "ackDescription": "OK"
    }
  }
}
```

## <a name="digital-twin-relationship-change-notifications"></a>数字孪生关系更改通知

创建、更新或删除数字孪生的任何关系时，都会触发 **关系更改通知**。 

### <a name="properties"></a>属性

下面是关系更改通知正文中的字段。

| 名称    | 值 |
| --- | --- |
| `id` | 通知的标识符，例如一个 UUID，或服务维护的计数器。 `source` + `id` 对于每个相异事件都是唯一的 |
| `source` | Azure 数字孪生实例的名称，例如 *mydigitaltwins.westus2.azuredigitaltwins.net* |
| `data` | 已更改的关系的有效负载。 有关详细信息，请参阅下面的[正文详细信息](#body-details-2)。 |
| `specversion` | *1.0*<br>消息遵守此版本的 [CloudEvents 规范](https://github.com/cloudevents/spec)。 |
| `type` | `Microsoft.DigitalTwins.Relationship.Create`<br>`Microsoft.DigitalTwins.Relationship.Update`<br>`Microsoft.DigitalTwins.Relationship.Delete` |
| `datacontenttype` | `application/json` |
| `subject` | 关系的 ID，例如 `<twin-ID>/relationships/<relationshipID>` |
| `time` | 在关系上发生该操作时的时间戳 |
| `traceparent` | 事件的 W3C 跟踪上下文 |

### <a name="body-details"></a>正文详细信息

在消息中，`data` 字段包含关系的有效负载（以 JSON 格式表示）。 其所用格式与通过 [DigitalTwins API](/rest/api/digital-twins/dataplane/twins) 对关系发出的 `GET` 请求的格式相同。 

下面是更新关系通知的数据示例。 “更新关系”是指关系的属性已更改，因此数据显示更新后的属性及其新值。 以下 JSON 对象是将出现在数字孪生体关系通知消息的 `data` 字段中的信息：

```json
{
    "modelId": "dtmi:example:Floor;1",
    "patch": [
      {
        "value": "user3",
        "path": "/ownershipUser",
        "op": "replace"
      }
    ]
  }
```

下面是创建或删除关系通知的数据示例。 对于 `Relationship.Delete`，正文与 `GET` 请求相同，它会在执行删除之前获得最新状态。

```json
{
    "$relationshipId": "device_to_device",
    "$etag": "W/\"72479873-0083-41a8-83e2-caedb932d881\"",
    "$relationshipName": "Connected",
    "$targetId": "device2",
    "connectionType": "WIFI"
}
```

## <a name="digital-twin-telemetry-messages"></a>数字孪生体遥测消息

在 Azure 数字孪生中从用于收集和发送度量的连接设备接收遥测消息。

### <a name="properties"></a>属性

下面是遥测消息正文中的字段。

| 名称    | 值 |
| --- | --- |
| `id` | 通知的标识符，客户在调用遥测 API 时提供该标识符。 |
| `source` | 遥测事件发送到的孪生体的完全限定名称。 使用以下格式：`<your-Digital-Twin-instance>.api.<your-region>.digitaltwins.azure.net/<twin-ID>`。 |
| `specversion` | *1.0*<br>消息遵守此版本的 [CloudEvents 规范](https://github.com/cloudevents/spec)。 |
| `type` | `microsoft.iot.telemetry` |
| `data` | 已发送到孪生体的遥测消息。 有效负载未修改，可能不符合已发送遥测的孪生体的架构。 |
| `dataschema` | 数据架构是孪生体或发出遥测的组件的模型 ID。 例如，`dtmi:example:com:floor4;2`。 |
| `datacontenttype` | `application/json` |
| `traceparent` | 事件的 W3C 跟踪上下文。 |

### <a name="body-details"></a>正文详细信息

正文包含遥测度量以及有关设备的某些上下文信息。

以下是示例遥测消息正文： 

```json
{
  "specversion": "1.0",
  "id": "df5a5992-817b-4e8a-b12c-e0b18d4bf8fb",
  "type": "microsoft.iot.telemetry",
  "source": "contoso-adt.api.wus2.digitaltwins.azure.net/digitaltwins/room1",
  "data": {
    "Temperature": 10
  },
  "dataschema": "dtmi:example:com:floor4;2",
  "datacontenttype": "application/json",
  "traceparent": "00-7e3081c6d3edfb4eaf7d3244b2036baa-23d762f4d9f81741-01"
}
```

## <a name="next-steps"></a>后续步骤

了解如何使用终结点和路由将事件传递给不同的目标：
* [事件路由](concepts-route-events.md)
