---
title: IoT 即插即用约定 | Microsoft Docs
description: 描述 IoT 即插即用在设备发送遥测和属性以及处理命令和属性更新时预期设备使用的约定。
author: rido-min
ms.author: rmpablos
ms.date: 07/10/2020
ms.topic: conceptual
ms.service: iot-develop
services: iot-develop
ms.openlocfilehash: fc8992e8e602f4a92d870328b6da14dde06af087
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128670816"
---
# <a name="iot-plug-and-play-conventions"></a>IoT 即插即用约定

IoT 即插即用设备在与 IoT 中心交换消息时应遵循一组约定。 IoT 即插即用设备使用 MQTT 协议与 IoT 中心通信。

设备可以包含[模块](../iot-hub/iot-hub-devguide-module-twins.md)，或在由 IoT Edge 运行时托管的 [IoT Edge 模块](../iot-edge/about-iot-edge.md)中实现。

介绍 IoT 即插即用设备使用[数字孪生定义语言 v2 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl) 模型实现的遥测、属性和命令。 本文中引用了两种类型的模型：

- **无组件** - 没有组件的模型。 此模型将遥测、属性和命令声明为主接口的内容部分中的顶级属性。 在 Azure IoT 资源管理器工具中，此模型显示为单个默认组件。
- **多组件** - 由两个或多个接口组成的模型。 主接口显示为默认组件，包含遥测、属性和命令。 一个或多个接口声明为包含其他遥测、属性和命令的组件。

有关详细信息，请参阅 [IoT 即插即用建模指南](concepts-modeling-guide.md)。

## <a name="identify-the-model"></a>识别模型

为了宣布实现的模型，IoT 即插即用设备或模块通过将 `model-id` 添加到 `USERNAME` 字段，在 MQTT 连接数据包中包含模型 ID。

若要标识设备或模块实现的模型，服务可以从以下项获取模型 ID：

- 设备孪生 `modelId` 字段。
- 数字孪生体 `$metadata.$model` 字段。
- 数字孪生体更改通知。

## <a name="telemetry"></a>遥测技术

从无组件设备发送的遥测不需要任何额外的元数据。 系统添加 `dt-dataschema` 属性。

从多组件设备发送的遥测必须添加 `$.sub` 为消息属性。 系统添加 `dt-subject` 和 `dt-dataschema` 属性。

## <a name="read-only-properties"></a>只读属性

### <a name="sample-no-component-read-only-property"></a>无组件只读属性示例

设备或模块可以发送遵循 DTDL v2 规则的任何有效 JSON。

DTDL：

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:example: Thermostat;1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Property",
      "name": "temperature",
      "schema": "double"
    }
  ]
}
```

报告的属性有效负载示例：

```json
"reported" :
{
  "temperature" : 21.3
}
```

### <a name="sample-multiple-components-read-only-property"></a>多组件只读属性示例

设备或模块必须添加 `{"__t": "c"}` 标记以指示元素引用组件。

引用组件的 DTDL：

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:TemperatureController;1",
  "@type": "Interface",
  "displayName": "Temperature Controller",
  "contents": [
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat1"
    }
  ]
}
```

定义组件的 DTDL：

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Property",
      "name": "temperature",
      "schema": "double"
    }
  ]
}
```

报告的属性有效负载示例：

```json
"reported": {
  "thermostat1": {
    "__t": "c",
    "temperature": 21.3
  }
}
```

## <a name="writable-properties"></a>可写属性

设备或模块应通过发送报告的属性来确认已接收到属性。 报告的属性应包括：

- `value` - 属性的实际值（通常是接收到的值，但设备可能决定报告不同的值）。
- `ac` - 使用 HTTP 状态代码的确认代码。
- `av` - 引用所需属性的 `$version` 的确认版本。 可在所需的属性 JSON 有效负载中找到该值。
- `ad` - 可选确认说明。

设备启动时，应该请求设备孪生，并检查是否有可写属性更新。 如果在设备处于脱机状态时增加了可写属性的版本，则设备应发送报告的属性响应，以确认接收到更新。

设备首次启动时，如果没有从中心接收到初始所需属性，则可以为报告的属性发送初始值。 在这种情况下，设备应将 `av` 设置为 `1`。 例如：

```json
"reported": {
  "targetTemperature": {
    "value": 20.0,
    "ac": 200,
    "av": 1,
    "ad": "initialize"
  }
}
```

设备可以使用报告的属性向中心提供其他信息。 例如，设备可能会使用一系列正在进行的消息进行响应，如：

```json
"reported": {
  "targetTemperature": {
    "value": 35.0,
    "ac": 202,
    "av": 3,
    "ad": "In-progress - reporting current temperature"
  }
}
```

设备达到目标温度时会发送以下消息：

```json
"reported": {
  "targetTemperature": {
    "value": 20.0,
    "ac": 200,
    "av": 3,
    "ad": "Reached target temperature"
  }
}
```

设备可能会报告如下错误：

```json
"reported": {
  "targetTemperature": {
    "value": 120.0,
    "ac": 500,
    "av": 3,
    "ad": "Target temperature out of range. Valid range is 10 to 99."
  }
}
```

### <a name="object-type"></a>对象类型

如果将可写属性定义为对象，则服务必须将完整的对象发送到设备。 设备应通过将足够的信息发送回服务来确认更新，以便服务了解设备对更新的处理方式。 此响应可能包括：

- 整个对象。
- 仅设备更新的字段。
- 字段的子集。

对于大型对象，请考虑最大程度地减小确认中包含的对象的大小。

以下示例显示了定义为 `Object`（使用四个字段）的可写属性：

DTDL：

```json
{
  "@type": "Property",
  "name": "samplingRange",
  "schema": {
    "@type": "Object",
    "fields": [
      {
        "name": "startTime",
        "schema": "dateTime"
      },
      {
        "name": "lastTime",
        "schema": "dateTime"
      },
      {
        "name": "count",
        "schema": "integer"
      },
      {
        "name": "errorCount",
        "schema": "integer"
      }
    ]
  },
  "displayName": "Sampling range"
  "writable": true
}
```

若要更新此可写属性，请从服务发送一个完整的对象，如下所示：

```json
{
  "samplingRange": {
    "startTime": "2021-08-17T12:53:00.000Z",
    "lastTime": "2021-08-17T14:54:00.000Z",
    "count": 100,
    "errorCount": 5
  }
}
```

设备使用一个确认进行响应，如下所示：

```json
{
  "samplingRange": {
    "ac": 200,
    "av": 5,
    "ad": "Weighing status updated",
    "value": {
      "startTime": "2021-08-17T12:53:00.000Z",
      "lastTime": "2021-08-17T14:54:00.000Z",
      "count": 100,
      "errorCount": 5
    }
  }
}
```

### <a name="sample-no-component-writable-property"></a>无组件可写属性示例

设备在单个有效负载中接收多个所需属性时，可以跨多个有效负载发送报告的属性响应，或将多个响应合并为单个有效负载。

设备或模块可以发送遵循 DTDL v2 规则的任何有效 JSON：

DTDL：

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:example: Thermostat;1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Property",
      "name": "targetTemperature",
      "schema": "double",
      "writable": true
    },
    {
      "@type": "Property",
      "name": "targetHumidity",
      "schema": "double",
      "writable": true
    }
  ]
}
```

所需的属性有效负载示例：

```json
"desired" :
{
  "targetTemperature" : 21.3,
  "targetHumidity" : 80,
  "$version" : 3
}
```

报告的属性第一个有效负载示例：

```json
"reported": {
  "targetTemperature": {
    "value": 21.3,
    "ac": 200,
    "av": 3,
    "ad": "complete"
  }
}
```

报告的属性第二个有效负载示例：

```json
"reported": {
  "targetHumidity": {
    "value": 80,
    "ac": 200,
    "av": 3,
    "ad": "complete"
  }
}
```

> [!NOTE]
> 可以选择将这两个报告的属性有效负载组合成单个有效负载。

### <a name="sample-multiple-components-writable-property"></a>多组件可写属性示例

设备或模块必须添加 `{"__t": "c"}` 标记以指示元素引用组件。

发送标记仅用于更新组件中定义的属性。 对默认组件中定义的属性的更新不包括标记，请参阅[无组件可写属性示例](#sample-no-component-writable-property)

设备在单个有效负载中接收多个报告的属性时，可以跨多个有效负载发送报告的属性响应，或将多个响应合并为单个有效负载。

设备或模块应通过发送报告的属性来确认已接收到属性：

引用组件的 DTDL：

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:TemperatureController;1",
  "@type": "Interface",
  "displayName": "Temperature Controller",
  "contents": [
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat1"
    }
  ]
}
```

定义组件的 DTDL：

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Property",
      "name": "targetTemperature",
      "schema": "double",
      "writable": true
    }
  ]
}
```

所需的属性有效负载示例：

```json
"desired": {
  "thermostat1": {
    "__t": "c",
    "targetTemperature": 21.3,
    "targetHumidity": 80,
    "$version" : 3
  }
}
```

报告的属性第一个有效负载示例：

```json
"reported": {
  "thermostat1": {
    "__t": "c",
    "targetTemperature": {
      "value": 23,
      "ac": 200,
      "av": 3,
      "ad": "complete"
    }
  }
}
```

报告的属性第二个有效负载示例：

```json
"reported": {
  "thermostat1": {
    "__t": "c",
    "targetHumidity": {
      "value": 80,
      "ac": 200,
      "av": 3,
      "ad": "complete"
    }
  }
}
```

> [!NOTE]
> 可以选择将这两个报告的属性有效负载组合成单个有效负载。

## <a name="commands"></a>命令

无组件接口使用不带前缀的命令名称。

在设备或模块上，多组件接口使用以下格式的命令名称：`componentName*commandName`。

## <a name="next-steps"></a>后续步骤

现在，你已经了解了 IoT 即插即用约定，下面是一些额外的资源：

- [数字孪生定义语言 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)
- [C 设备 SDK](/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST API](/rest/api/iothub/device)
- [IoT 即插即用建模指南](concepts-modeling-guide.md)
