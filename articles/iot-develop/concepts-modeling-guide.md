---
title: 了解 IoT 即插即用设备模型 | Microsoft Docs
description: 了解用于 IoT 即插即用设备的数字孪生定义语言 (DTDL) 建模语言。 本文介绍基元数据类型和复杂数据类型、重用使用组件和继承的模式以及语义类型。 本文提供了有关为模型创作选择设备孪生模型标识符和工具支持的指导。
author: dominicbetts
ms.author: dobett
ms.date: 03/09/2021
ms.topic: conceptual
ms.service: iot-develop
services: iot-develop
ms.openlocfilehash: b79c72f6711519c87cb671ea5b7d72eaffabe7e2
ms.sourcegitcommit: 8669087bcbda39e3377296c54014ce7b58909746
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2021
ms.locfileid: "114406328"
---
# <a name="iot-plug-and-play-modeling-guide"></a>IoT 即插即用建模指南

IoT 即插即用的核心是设备 _模型_，该模型向支持 IoT 即插即用的应用程序介绍设备的功能。 该模型被构造为一组接口，这些接口定义以下内容：

- _属性_：表示设备或其他实体的只读或可写状态。 例如，设备序列号可以是只读属性，而调温器上的目标温度可以是可写属性。
- _遥测_ 字段：定义设备发出的数据，包括常规的传感器读数流、偶发性错误或信息消息。
- _命令_：描述可在设备上执行的功能或操作。 例如，命令可以重新启动网关，或使用远程相机拍照。

若要详细了解 IoT 即插即用如何使用设备模型，请参阅 [IoT 即插即用设备开发者指南](concepts-developer-guide-device.md)和 [IoT 即插即用服务开发者指南](concepts-developer-guide-service.md)。

若要定义模型，请使用数字孪生定义语言 (DTDL)。 DTDL 使用名为 [JSON-LD](https://json-ld.org/) 的 JSON 变量。 以下代码片段展示的恒温器设备模型具有以下特点：

- 具有唯一的模型 ID：`dtmi:com:example:Thermostat;1`。
- 发送温度遥测数据。
- 具有用于设置目标温度的可写属性。
- 具有报告自上次重新启动以来最大温度的只读属性。
- 响应请求某个时间段内的最大、最小和平均温度的命令。

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
    {
      "@type": [
        "Telemetry",
        "Temperature"
      ],
      "name": "temperature",
      "displayName": "Temperature",
      "description": "Temperature in degrees Celsius.",
      "schema": "double",
      "unit": "degreeCelsius"
    },
    {
      "@type": [
        "Property",
        "Temperature"
      ],
      "name": "targetTemperature",
      "schema": "double",
      "displayName": "Target Temperature",
      "description": "Allows to remotely specify the desired target temperature.",
      "unit": "degreeCelsius",
      "writable": true
    },
    {
      "@type": [
        "Property",
        "Temperature"
      ],
      "name": "maxTempSinceLastReboot",
      "schema": "double",
      "unit": "degreeCelsius",
      "displayName": "Max temperature since last reboot.",
      "description": "Returns the max temperature since last device reboot."
    },
    {
      "@type": "Command",
      "name": "getMaxMinReport",
      "displayName": "Get Max-Min report.",
      "description": "This command returns the max, min and average temperature from the specified time to the current time.",
      "request": {
        "name": "since",
        "displayName": "Since",
        "description": "Period to return the max-min report.",
        "schema": "dateTime"
      },
      "response": {
        "name": "tempReport",
        "displayName": "Temperature Report",
        "schema": {
          "@type": "Object",
          "fields": [
            {
              "name": "maxTemp",
              "displayName": "Max temperature",
              "schema": "double"
            },
            {
              "name": "minTemp",
              "displayName": "Min temperature",
              "schema": "double"
            },
            {
              "name": "avgTemp",
              "displayName": "Average Temperature",
              "schema": "double"
            },
            {
              "name": "startTime",
              "displayName": "Start Time",
              "schema": "dateTime"
            },
            {
              "name": "endTime",
              "displayName": "End Time",
              "schema": "dateTime"
            }
          ]
        }
      }
    }
  ]
}
```

恒温器模型有单个接口。 本文后面的示例说明了使用组件和继承的更复杂的模型。

本文介绍如何设计和创作自己的模型，并介绍数据类型、模型结构和工具等主题。

若要了解详细信息，请参阅[数字孪生定义语言 v2](https://github.com/Azure/opendigitaltwins-dtdl) 规范。

## <a name="model-structure"></a>模型结构

属性、遥测和命令将分组为接口。 本部分介绍如何使用组件和继承，以使用接口来描述简单和复杂的模型。

### <a name="model-ids"></a>模型 ID

每个接口都具有唯一的数字孪生模型标识符 (DTMI)。 复杂模型使用 DTMI 标识组件。 应用程序可以使用设备发送的 DTMI 在存储库中查找模型定义。

DTMI 应遵循 [IoT 即插即用模型存储库](https://github.com/Azure/iot-plugandplay-models)要求的命名约定：

- DTMI 前缀是 `dtmi:`。
- DTMI 后缀是模型的版本号，例如 `;2`。
- DTMI 的主体映射到存储模型的模型存储库中的文件夹和文件。 版本号是文件名的一部分。

例如，由 DTMI `dtmi:com:Example:Thermostat;2` 标识的模型存储在“DTMI/com/example/thermostat-2.js”文件中。

以下代码片段概述了具有唯一 DTMI 的接口定义：

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;2",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
    ...
  ]
}
```

### <a name="no-components"></a>无组件

简单的模型（例如前面所示的恒温器）不会使用嵌入组件或级联组件。 遥测、属性和命令在接口的 `contents` 节点中定义。

下面的示例说明了不使用组件的简单模型的一部分：

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
    {
      "@type": [
        "Telemetry",
        "Temperature"
      ],
      "name": "temperature",
      "displayName": "Temperature",
      "description": "Temperature in degrees Celsius.",
      "schema": "double",
      "unit": "degreeCelsius"
    },
    {
      "@type": [
        "Property",
...
```

Azure IoT 资源管理器和 IoT Central 设备模板设计器之类的工具将恒温器等独立接口标记为 _默认组件_。

以下屏幕截图展示模型在 Azure IoT 资源管理器工具中的显示方式：

:::image type="content" source="media/concepts-modeling-guide/default-component.png" alt-text="Azure IoT 资源管理器中的默认组件":::

以下屏幕截图展示模型在 IoT Central 设备模板设计器中显示为默认组件的方式。 选择“查看标识”以查看模型的 DTMI：

:::image type="content" source="media/concepts-modeling-guide/iot-central-model.png" alt-text="在 IoT Central 设计器中显示恒温器模型的屏幕截图":::

模型 ID 存储在设备孪生属性中，如以下屏幕截图所示：

:::image type="content" source="media/concepts-modeling-guide/twin-model-id.png" alt-text="数字孪生属性中的模型 ID":::

不带组件的 DTDL 模型对于使用一组遥测、属性和命令的设备或 IoT Edge 模块非常有用。 不使用组件的模型允许用户轻松地将现有设备或模块迁移为 IoT 即插即用设备或模块 - 创建 DTDL 模型来描述实际设备或模块，而无需定义任何组件。

> [!TIP]
> 模块可以是设备[模块](../iot-hub/iot-hub-devguide-module-twins.md)或 [IoT Edge 模块](../iot-edge/about-iot-edge.md)。

### <a name="reuse"></a>重用

可以通过两种方法重用接口定义。 在模型中使用多个组件来引用其他接口定义。 使用继承扩展现有的接口定义。

### <a name="multiple-components"></a>多个组件

可以通过组件将一个模型接口构建为其他接口的程序集。

例如，[恒温器](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json)接口定义为模型。 定义[温度控制器模型](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json)时，可以将此接口作为一个或多个组件合并。 在下面的示例中，这些组件称为 `thermostat1` 和 `thermostat2`。

对于包含多个组件的 DTDL 模型，有两个或两个以上的组件部分。 每个部分的 `@type` 均设置为 `Component`，并显式引用架构，如以下代码片段所示：

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:TemperatureController;1",
  "@type": "Interface",
  "displayName": "Temperature Controller",
  "description": "Device with two thermostats and remote reboot.",
  "contents": [
    {
      "@type": [
        "Telemetry",
        "DataSize"
      ],
      "name": "workingSet",
      "displayName": "Working Set",
      "description": "Current working set of the device memory in KiB.",
      "schema": "double",
      "unit": "kibibyte"
    },
    {
      "@type": "Property",
      "name": "serialNumber",
      "displayName": "Serial Number",
      "description": "Serial number of the device.",
      "schema": "string"
    },
    {
      "@type": "Command",
      "name": "reboot",
      "displayName": "Reboot",
      "description": "Reboots the device after waiting the number of seconds specified.",
      "request": {
        "name": "delay",
        "displayName": "Delay",
        "description": "Number of seconds to wait before rebooting the device.",
        "schema": "integer"
      }
    },
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat1",
      "displayName": "Thermostat One",
      "description": "Thermostat One of Two."
    },
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat2",
      "displayName": "Thermostat Two",
      "description": "Thermostat Two of Two."
    },
    {
      "@type": "Component",
      "schema": "dtmi:azure:DeviceManagement:DeviceInformation;1",
      "name": "deviceInformation",
      "displayName": "Device Information interface",
      "description": "Optional interface with basic device hardware information."
    }
  ]
}
```

此模型包含“内容”部分定义的三个组件 - 两个 `Thermostat` 组件和一个 `DeviceInformation` 组件。 内容部分还包括属性、遥测和命令定义。

以下屏幕截图显示了此模型在 IoT Central 中的显示方式。 温度控制器中的属性、遥测和命令定义显示在顶层“默认组件”中。 每个恒温器的属性、遥测和命令定义将显示在组件定义中：

:::image type="content" source="media/concepts-modeling-guide/temperature-controller.png" alt-text="显示 IoT Central 中温度控制器设备模板的屏幕截图。":::

:::image type="content" source="media/concepts-modeling-guide/temperature-controller-components.png" alt-text="显示 IoT Central 中温度控制器设备模板中的恒温器组件的屏幕截图。":::

若要了解如何编写与组件交互的设备代码，请参阅[IoT 即插即用设备开发者指南](concepts-developer-guide-device.md)。

若要了解如何编写与设备上的组件进行交互的服务代码，请参阅 [IoT 即插即用服务开发者指南](concepts-developer-guide-service.md)。

### <a name="inheritance"></a>继承

可以使用继承在基接口中重用功能，以扩展接口的功能。 例如，多个设备模型可以共享常用功能，如序列号：

:::image type="content" source="media/concepts-modeling-guide/inheritance.png" alt-text="设备模型中的继承示例，展示了一个恒温器和一个流控制器从基接口共享功能。" border="false":::

下面的代码片段展示了 DTML 模型使用 `extends` 关键字定义上图中显示的继承关系：

```json
[
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:example:Thermostat;1",
    "@type": "Interface",
    "contents": [
      {
        "@type": "Telemetry",
        "name": "temperature",
        "schema": "double",
        "unit": "degreeCelsius"
      },
      {
        "@type": "Property",
        "name": "targetTemperature",
        "schema": "double",
        "unit": "degreeCelsius",
        "writable": true
      }
    ],
    "extends": [
      "dtmi:com:example:baseDevice;1"
    ]
  },
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:example:baseDevice;1",
    "@type": "Interface",
    "contents": [
      {
        "@type": "Property",
        "name": "SerialNumber",
        "schema": "double",
        "writable": false
      }
    ]
  }
]
```

以下屏幕截图显示了此模型在 IoT Central 设备模板环境中：

:::image type="content" source="media/concepts-modeling-guide/iot-central-inheritance.png" alt-text="显示 IoT Central 中接口继承的屏幕截图":::

编写设备或服务端代码时，代码不需要执行任何特殊操作来处理继承的接口。 在此部分所示的示例中，设备代码会报告序列号，就像它是恒温器接口的一部分一样。

### <a name="tips"></a>提示

你可以在创建模型时合并组件和继承。 下图显示了从 `baseDevice` 接口继承的 `thermostat` 模型。 `baseDevice` 接口有一个组件，该组件本身继承自另一个接口：

:::image type="content" source="media/concepts-modeling-guide/inheritance-components.png" alt-text="显示使用组件和继承的模型的关系图。" border="false":::

以下代码片段显示了使用 `extends` 和 `component` 关键字的 DTML 模型，以定义上图中所示的继承关系和组件用法：

```json
[
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:example:Thermostat;1",
    "@type": "Interface",
    "contents": [
      {
        "@type": "Telemetry",
        "name": "temperature",
        "schema": "double",
        "unit": "degreeCelsius"
      },
      {
        "@type": "Property",
        "name": "targetTemperature",
        "schema": "double",
        "unit": "degreeCelsius",
        "writable": true
      }
    ],
    "extends": [
      "dtmi:com:example:baseDevice;1"
    ]
  },
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:example:baseDevice;1",
    "@type": "Interface",
    "contents": [
      {
        "@type": "Property",
        "name": "SerialNumber",
        "schema": "double",
        "writable": false
      },
      {
        "@type" : "Component",
        "schema": "dtmi:com:example:baseComponent;1",
        "name": "baseComponent"
      }
    ]
  }
]
```

## <a name="data-types"></a>数据类型

使用数据类型定义遥测、属性和命令参数。 数据类型可以是基元类型或复杂类型。 复杂数据类型使用基元类型或其他复杂类型。 复杂类型的最大深度为五个级别。

### <a name="primitive-types"></a>基元类型

下表显示了可以使用的基元类型集：

| 基元类型 | 说明 |
| --- | --- |
| `boolean` | 布尔值 |
| `date` | [RFC 3339 第 5.6 节](https://tools.ietf.org/html/rfc3339#section-5.6)中定义的完整日期 |
| `dateTime` | [RFC 3339](https://tools.ietf.org/html/rfc3339) 中定义的日期时间 |
| `double` | IEEE 8 字节浮点 |
| `duration` | ISO 8601 格式的持续时间 |
| `float` | IEEE 4 字节浮点 |
| `integer` | 4 字节带符号整数 |
| `long` | 8 字节带符号整数 |
| `string` | UTF8 字符串 |
| `time` | [RFC 3339 第 5.6 节](https://tools.ietf.org/html/rfc3339#section-5.6)中定义的完整时间 |

以下代码片段显示了在 `schema` 字段中使用 `double` 类型的示例遥测定义：

```json
{
  "@type": "Telemetry",
  "name": "temperature",
  "displayName": "Temperature",
  "schema": "double"
}
```

### <a name="complex-datatypes"></a>复杂数据类型

复杂数据类型是“数组”、“枚举”、“映射”、“对象”或地理空间类型之一。

#### <a name="arrays"></a>数组

数组是可索引的数据类型，其中的所有元素都是相同的类型。 元素类型可以是基元类型或复杂类型。

以下代码片段显示了在 `schema` 字段中使用 `Array` 类型的示例遥测定义。 数组元素为布尔值：

```json
{
  "@type": "Telemetry",
  "name": "ledState",
  "schema": {
    "@type": "Array",
    "elementSchema": "boolean"
  }
}
```

#### <a name="enumerations"></a>枚举

枚举描述具有一组映射到值的命名标签的类型。 这些值可以是整数或字符串，但标签始终为字符串。

以下代码片段显示了在 `schema` 字段中使用 `Enum` 类型的示例遥测定义。 枚举中的值是整数：

```json
{
  "@type": "Telemetry",
  "name": "state",
  "schema": {
    "@type": "Enum",
    "valueSchema": "integer",
    "enumValues": [
      {
        "name": "offline",
        "displayName": "Offline",
        "enumValue": 1
      },
      {
        "name": "online",
        "displayName": "Online",
        "enumValue": 2
      }
    ]
  }
}
```

#### <a name="maps"></a>Maps

映射是具有键值对的类型，其中的值都具有相同的类型。 映射中的键必须是字符串。 映射中的值可以是任何类型，包括其他复杂类型。

以下代码片段显示了在 `schema` 字段中使用 `Map` 类型的示例属性定义。 映射中的值是字符串：

```json
{
  "@type": "Property",
  "name": "modules",
  "writable": true,
  "schema": {
    "@type": "Map",
    "mapKey": {
      "name": "moduleName",
      "schema": "string"
    },
    "mapValue": {
      "name": "moduleState",
      "schema": "string"
    }
  }
}
```

### <a name="objects"></a>对象

对象类型由命名字段组成。 对象映射中的字段类型可以是基元类型或复杂类型。

以下代码片段显示了在 `schema` 字段中使用 `Object` 类型的示例遥测定义。 对象中的字段为 `dateTime`、`duration` 和 `string` 类型：

```json
{
  "@type": "Telemetry",
  "name": "monitor",
  "schema": {
    "@type": "Object",
    "fields": [
      {
        "name": "start",
        "schema": "dateTime"
      },
      {
        "name": "interval",
        "schema": "duration"
      },
      {
        "name": "status",
        "schema": "string"
      }
    ]
  }
}
```

#### <a name="geospatial-types"></a>地理空间类型

DTDL 提供了一组基于 [GeoJSON](https://geojson.org/) 的地理空间类型，用于建模地理数据结构：`point`、`multiPoint`、`lineString`、`multiLineString`、`polygon` 和 `multiPolygon`。 这些类型是数组、对象和枚举的预定义嵌套结构。

以下代码片段显示了在 `schema` 字段中使用 `point` 类型的示例遥测定义：

```json
{
  "@type": "Telemetry",
  "name": "location",
  "schema": "point"
}
```

由于地理空间类型基于数组，因此当前无法在属性定义中使用。

## <a name="semantic-types"></a>语义类型

属性的数据类型或遥测定义指定设备与服务交换的数据的格式。 语义类型提供了有关遥测和属性的信息，应用程序可以使用这些信息来确定处理或显示值的方式。 每个语义类型都有一个或多个关联的单位。 例如，摄氏温度和华氏温度语义类型为单位。 IoT Central 仪表板和分析可以使用语义类型信息来确定绘制遥测数据或属性值以及显示单位的方式。 若要了解如何使用模型分析程序读取语义类型，请参阅[了解数字孪生模型分析程序](concepts-model-parser.md)。

以下代码片段显示了一个包含语义类型信息的示例遥测定义。 语义类型 `Temperature` 将添加到 `@type` 数组和 `unit` 值，`degreeCelsius` 为语义类型的有效单位之一：

```json
{
  "@type": [
    "Telemetry",
    "Temperature"
  ],
  "name": "temperature",
  "schema": "double",
  "unit": "degreeCelsius"
}
```

## <a name="localization"></a>本地化

应用程序（如 IoT Central）使用模型中的信息来围绕与 IoT 即插即用设备交换的数据动态构建 UI。 例如，仪表板上的磁贴可以显示遥测、属性和命令的名称和说明。

模型中的可选 `description` 和 `displayName` 字段保留要在 UI 中使用的字符串。 这些字段可以保留应用程序呈现本地化 UI 的本地化字符串。

以下代码片段显示了包含本地化字符串的示例温度遥测定义：

```json
{
  "@type": [
    "Telemetry",
    "Temperature"
  ],
  "description": {
    "en": "Temperature in degrees Celsius.",
    "it": "Temperatura in gradi Celsius."
  },
  "displayName": {
    "en": "Temperature",
    "it": "Temperatura"
  },
  "name": "temperature",
  "schema": "double",
  "unit": "degreeCelsius"
}
```

添加本地化字符串是可选项。 下面的示例仅包含一种默认语言：

```json
{
  "@type": [
    "Telemetry",
    "Temperature"
  ],
  "description": "Temperature in degrees Celsius.",
  "displayName": "Temperature",
  "name": "temperature",
  "schema": "double",
  "unit": "degreeCelsius"
}
```

## <a name="lifecycle-and-tools"></a>生命周期和工具

设备模型的四个生命周期阶段是“创作”、“发布”、“使用”和“版本控制”：

### <a name="author"></a>作者

DTML 设备模型是可以在文本编辑器中创建的 JSON 文档。 但是，在 IoT Central 中可以使用设备模板 GUI 环境创建 DTML 模型。 在 IoT Central 中用户可以：

- 创建用于定义属性、遥测和命令的接口。
- 使用组件组装多个接口。
- 定义接口之间的继承关系。
- 导入和导出 DTML 模型文件。

若要了解详细信息，请参阅[在 Azure IoT Central 应用程序中定义新的设备类型](../iot-central/core/howto-set-up-template.md)。

VS Code 和 Visual Studio 2019 都有 DTDL 创作扩展。

若要安装适用于 VS Code 的 DTDL 扩展，请转到[适用于 Visual Studio Code 的 DTDL 编辑器](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl)。 还可以在 VS Code 的“扩展”视图中搜索“DTDL” 。

安装该扩展后，请使用它来帮助你在 VS Code 中创作 DTDL 模型文件：

- 该扩展在 DTDL 模型文件中提供语法验证，突出显示错误，如以下屏幕截图所示：

    :::image type="content" source="media/concepts-modeling-guide/model-validation.png" alt-text="VS Code 中的模型验证":::

- 编辑 DTDL 模型时，使用 IntelliSense 和自动完成功能：

    :::image type="content" source="media/concepts-modeling-guide/model-intellisense.png" alt-text="在 VS Code 中将 IntelliSense 用于 DTDL 模型":::

- 创建新的 DTDL 接口。 “DTDL: 创建接口”命令使用新接口创建一个 JSON 文件。 该接口包含示例遥测数据、属性和命令定义。

若要安装适用于 Visual Studio 2019 的 DTDL 扩展，请转到 [VS 2019 的 DTDL 语言支持](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16dtdllanguagesupport)。 你还可以在 Visual Studio 的“管理扩展”中搜索 DTDL。

安装该扩展后，请使用它来帮助你在 Visual Studio 中创作 DTDL 模型文件：

- 该扩展在 DTDL 模型文件中提供语法验证，突出显示错误，如以下屏幕截图所示：

    :::image type="content" source="media/concepts-modeling-guide/model-validation-2.png" alt-text="Visual Studio 中的模型验证":::

- 编辑 DTDL 模型时，使用 IntelliSense 和自动完成功能：

    :::image type="content" source="media/concepts-modeling-guide/model-intellisense-2.png" alt-text="在 Visual Studio 中将 IntelliSense 用于 DTDL 模型":::

### <a name="publish"></a>发布

若要使 DTML 模型可共享并可发现，请将其发布到设备模型存储库中。

在公共存储库中发布模型之前，你可以使用 `dmr-client` 工具来验证模型。

若要了解详细信息，请参阅[设备模型存储库](concepts-model-repository.md)。

### <a name="use"></a>用途

应用程序（如 IoT Central）使用设备模型。 在 IoT Central 中，模型是描述设备功能的设备模板的一部分。 IoT Central 使用设备模板动态构建设备 UI，包括仪表板和分析。

自定义解决方案可以使用[数字孪生模型分析程序](concepts-model-parser.md)来了解实现该模型的设备的功能。 若要了解详细信息，请参阅[在 IoT 解决方案中使用 IoT 即插即用模型](concepts-model-discovery.md)。

### <a name="version"></a>版本

为了确保使用模型的设备和服务器端解决方案继续正常工作，已发布的模型是不可变的。

DTMI 包括可用来创建模型的多个版本的版本号。 设备和服务器端解决方案可以使用设计规定的特定版本。

IoT Central 实现了更多设备模型版本控制规则。 如果在 IoT Central 中对设备模板及其模型进行版本控制，则可以将设备从以前的版本迁移到更高版本。 但是，在没有固件升级的情况下，已迁移设备不能使用新功能。 若要了解详细信息，请参阅[编辑设备模板](../iot-central/core/howto-edit-device-template.md)。

## <a name="limits-and-constraints"></a>限制和约束

下面的列表总结了有关模型的一些主要约束和限制：

- 目前，数组、映射和对象的最大深度为五个级别的深度。
- 不能在属性定义中使用数组。
- 可以将接口扩展到 10 个级别。
- 接口最多可以扩展到两个其他接口。
- 组件不能包含另一个组件。

## <a name="next-steps"></a>后续步骤

现在，你已了解设备建模，以下是一些其他资源：

- [数字孪生定义语言 v2 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)
- [模型存储库](./concepts-model-repository.md)
