---
title: Azure IoT Central 中的遥测、属性和命令有效负载 | Microsoft Docs
description: 通过 Azure IoT Central 设备模板，可以指定设备必须实现的遥测、属性和命令。 了解设备可以与 IoT Central 交换的数据格式。
author: dominicbetts
ms.author: dobett
ms.date: 08/25/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: 3c30662a9ca85265f43e78ad9dd3f33c70c167a5
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2021
ms.locfileid: "123479933"
---
# <a name="telemetry-property-and-command-payloads"></a>遥测、属性和命令有效负载

Azure IoT Central 中的设备模板是定义以下内容的蓝图：

* 设备发送到 IoT Central 的遥测。
* 设备与 IoT Central 同步的属性。
* IoT Central 在设备上调用的命令。

本文介绍设备为设备模板中定义的遥测、属性和命令发送和接收的 JSON 有效负载。

本文没有介绍每种类型的遥测、属性和命令有效负载，但示例演示了所有关键类型。

每个示例都显示了设备模型中的代码片段，该代码片段定义类型和示例 JSON 有效负载，说明设备与 IoT Central 应用程序的交互方式。

> [!NOTE]
> IoT Central 接受任何有效 JSON，但只在与设备模型中的定义相匹配时才用于实现可视化。 可以导出与定义不匹配的数据，请参阅[将 IoT 数据导出到 Azure 中的目标](howto-export-data.md)。

定义设备模型的 JSON 文件使用[数字孪生体定义语言 (DTDL) V2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)。

有关显示其中一些正在使用的有效负载的示例设备代码，请参阅[创建客户端应用程序并将其连接到 Azure IoT Central 应用程序](tutorial-connect-device.md)教程。

## <a name="view-raw-data"></a>查看原始数据

通过 IoT Central，可以查看设备发送到应用程序的原始数据。 此视图可用于排除发送自设备的有效负载的问题。 若要查看设备正在发送的原始数据：

1. 从“设备”页导航到设备。

1. 选择“原始数据”选项卡：

    :::image type="content" source="media/concepts-telemetry-properties-commands/raw-data.png" alt-text="“原始数据”视图":::

    在此视图中，你可以选择要显示的列，并设置要查看的时间范围。 “未建模数据”列显示设备中与设备模板中的任何属性或遥测定义不匹配的数据。

## <a name="telemetry"></a>遥测

### <a name="telemetry-in-components"></a>组件中的遥测

如果在组件中定义遥测，请使用设备模型中定义的组件名称添加名为 `$.sub` 的自定义消息属性。 若要了解详细信息，请参阅[教程：创建客户端应用程序并将其连接到 Azure IoT Central 应用程序](tutorial-connect-device.md)。

### <a name="primitive-types"></a>基元类型

本部分显示设备流式传输到 IoT Central 应用程序的原始遥测类型的示例。

设备模型中的以下代码片段显示 `boolean` 遥测类型的定义：

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "BooleanTelemetry"
  },
  "name": "BooleanTelemetry",
  "schema": "boolean"
}
```

设备客户端应该以 JSON 形式发送遥测，如下例所示：

```json
{ "BooleanTelemetry": true }
```

设备模型中的以下代码片段显示 `string` 遥测类型的定义：

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "StringTelemetry"
  },
  "name": "StringTelemetry",
  "schema": "string"
}
```

设备客户端应该以 JSON 形式发送遥测，如下例所示：

```json
{ "StringTelemetry": "A string value - could be a URL" }
```

设备模型中的以下代码片段显示 `integer` 遥测类型的定义：

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "IntegerTelemetry"
  },
  "name": "IntegerTelemetry",
  "schema": "integer"
}

```

设备客户端应该以 JSON 形式发送遥测，如下例所示：

```json
{ "IntegerTelemetry": 23 }
```

设备模型中的以下代码片段显示 `double` 遥测类型的定义：

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "DoubleTelemetry"
  },
  "name": "DoubleTelemetry",
  "schema": "double"
}
```

设备客户端应该以 JSON 形式发送遥测，如下例所示：

```json
{ "DoubleTelemetry": 56.78 }
```

设备模型中的以下代码片段显示 `dateTime` 遥测类型的定义：

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "DateTimeTelemetry"
  },
  "name": "DateTimeTelemetry",
  "schema": "dateTime"
}
```

设备客户端应该以 JSON 形式发送遥测，如下例所示。`DateTime` 类型必须是 ISO 8061 格式：

```json
{ "DateTimeTelemetry": "2020-08-30T19:16:13.853Z" }
```

设备模型中的以下代码片段显示 `duration` 遥测类型的定义：

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "DurationTelemetry"
  },
  "name": "DurationTelemetry",
  "schema": "duration"
}
```

设备客户端应该以 JSON 形式发送遥测，如下例所示。持续时间必须是 ISO 8601 格式：

```json
{ "DurationTelemetry": "PT10H24M6.169083011336625S" }
```

### <a name="complex-types"></a>复杂类型

本部分显示设备流式传输到 IoT Central 应用程序的复杂遥测类型的示例。

设备模型中的以下代码片段显示 `geopoint` 遥测类型的定义：

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "GeopointTelemetry"
  },
  "name": "GeopointTelemetry",
  "schema": "geopoint"
}
```

> [!NOTE]
> geopoint 架构类型不属于[数字孪生定义语言规范](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)。 IoT Central 目前支持 geopoint 架构类型和 location 语义类型，以便向后兼容 。

设备客户端应该以 JSON 形式发送遥测，如下所示。 IoT Central 在地图上以 pin 的形式显示值：

```json
{
  "GeopointTelemetry": {
    "lat": 47.64263,
    "lon": -122.13035,
    "alt": 0
  }
}
```

设备模型中的以下代码片段显示 `Enum` 遥测类型的定义：

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "EnumTelemetry"
  },
  "name": "EnumTelemetry",
  "schema": {
    "@type": "Enum",
    "displayName": {
      "en": "Enum"
    },
    "valueSchema": "integer",
    "enumValues": [
      {
        "displayName": {
          "en": "Item1"
        },
        "enumValue": 0,
        "name": "Item1"
      },
      {
        "displayName": {
          "en": "Item2"
        },
        "enumValue": 1,
        "name": "Item2"
      },
      {
        "displayName": {
          "en": "Item3"
        },
        "enumValue": 2,
        "name": "Item3"
      }
    ]
  }
}
```

设备客户端应该以 JSON 形式发送遥测，如下所示。 值可能为 `0`、`1` 和 `2`，它们在 IoT Central 中显示为 `Item1`、`Item2` 和 `Item3`：

```json
{ "EnumTelemetry": 1 }
```

设备模型中的以下代码片段显示 `Object` 遥测类型的定义。 此对象具有类型为 `dateTime`、`integer` 和 `Enum` 的三个字段：

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "ObjectTelemetry"
  },
  "name": "ObjectTelemetry",
  "schema": {
    "@type": "Object",
    "displayName": {
      "en": "Object"
    },
    "fields": [
      {
        "displayName": {
          "en": "Property1"
        },
        "name": "Property1",
        "schema": "dateTime"
      },
      {
        "displayName": {
          "en": "Property2"
        },
        "name": "Property2",
        "schema": "integer"
      },
      {
        "displayName": {
          "en": "Property3"
        },
        "name": "Property3",
        "schema": {
          "@type": "Enum",
          "displayName": {
            "en": "Enum"
          },
          "valueSchema": "integer",
          "enumValues": [
            {
              "displayName": {
                "en": "Item1"
              },
              "enumValue": 0,
              "name": "Item1"
            },
            {
              "displayName": {
                "en": "Item2"
              },
              "enumValue": 1,
              "name": "Item2"
            },
            {
              "displayName": {
                "en": "Item3"
              },
              "enumValue": 2,
              "name": "Item3"
            }
          ]
        }
      }
    ]
  }
}
```

设备客户端应该以 JSON 形式发送遥测，如下所示。 `DateTime` 类型必须符合 ISO 8061 标准。 `Property3` 值可能为 `0` 和 `1`，它们在 IoT Central 中显示为 `Item1`、`Item2` 和 `Item3`：

```json
{
  "ObjectTelemetry": {
      "Property1": "2020-09-09T03:36:46.195Z",
      "Property2": 37,
      "Property3": 2
  }
}
```

设备模型中的以下代码片段显示 `vector` 遥测类型的定义：

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "VectorTelemetry"
  },
  "name": "VectorTelemetry",
  "schema": "vector"
}
```

设备客户端应该以 JSON 形式发送遥测，如下例所示：

```json
{
  "VectorTelemetry": {
    "x": 74.72395045538597,
    "y": 74.72395045538597,
    "z": 74.72395045538597
  }
}
```

### <a name="event-and-state-types"></a>事件和状态类型

本部分显示设备发送到 IoT Central 应用程序的遥测事件和状态的示例。

设备模型中的以下代码片段显示 `integer` 事件类型的定义：

```json
{
  "@type": [
    "Telemetry",
    "Event"
  ],
  "displayName": {
    "en": "IntegerEvent"
  },
  "name": "IntegerEvent",
  "schema": "integer"
}
```

设备客户端应该以 JSON 形式发送事件数据，如下例所示：

```json
{ "IntegerEvent": 74 }
```

设备模型中的以下代码片段显示 `integer` 状态类型的定义：

```json
{
  "@type": [
    "Telemetry",
    "State"
  ],
  "displayName": {
    "en": "IntegerState"
  },
  "name": "IntegerState",
  "schema": {
    "@type": "Enum",
    "valueSchema": "integer",
    "enumValues": [
      {
        "displayName": {
          "en": "Level1"
        },
        "enumValue": 1,
        "name": "Level1"
      },
      {
        "displayName": {
          "en": "Level2"
        },
        "enumValue": 2,
        "name": "Level2"
      },
      {
        "displayName": {
          "en": "Level3"
        },
        "enumValue": 3,
        "name": "Level3"
      }
    ]
  }
}
```

设备客户端应该以 JSON 形式发送状态，如下例所示。 整数状态值可能为 `1`、`2` 和 `3`：

```json
{ "IntegerState": 2 }
```

## <a name="properties"></a>属性

> [!NOTE]
> 属性的有效负载格式适用于在 2020 年 07 月 14 日或之后创建的应用程序。

### <a name="properties-in-components"></a>组件中的属性

如果该属性是在组件中定义的，请将该属性包装在组件名称中。 以下示例将 `maxTempSinceLastReboot` 设置在 `thermostat2` 组件中。 标记 `__t` 指示此组件：

```json
{
  "thermostat2" : {  
    "__t" : "c",  
    "maxTempSinceLastReboot" : 38.7
    } 
}
```

若要了解详细信息，请参阅[教程：创建客户端应用程序并将其连接到 Azure IoT Central 应用程序](tutorial-connect-device.md)。

### <a name="primitive-types"></a>基元类型

本部分显示设备发送到 IoT Central 应用程序的原始属性类型的示例。

设备模型中的以下代码片段显示 `boolean` 属性类型的定义：

```json
{
  "@type": "Property",
  "displayName": {
    "en": "BooleanProperty"
  },
  "name": "BooleanProperty",
  "schema": "boolean",
  "writable": false
}
```

设备客户端应发送 JSON 有效负载，将其作为设备孪生中报告的属性，类似于以下示例：

```json
{ "BooleanProperty": false }
```

设备模型中的以下代码片段显示 `long` 属性类型的定义：

```json
{
  "@type": "Property",
  "displayName": {
    "en": "LongProperty"
  },
  "name": "LongProperty",
  "schema": "long",
  "writable": false
}
```

设备客户端应发送 JSON 有效负载，将其作为设备孪生中报告的属性，类似于以下示例：

```json
{ "LongProperty": 439 }
```

设备模型中的以下代码片段显示 `date` 属性类型的定义：

```json
{
  "@type": "Property",
  "displayName": {
    "en": "DateProperty"
  },
  "name": "DateProperty",
  "schema": "date",
  "writable": false
}
```

设备客户端应发送 JSON 有效负载，将其作为设备孪生中报告的属性，类似于以下示例。 `Date` 类型必须符合 ISO 8061 标准：

```json
{ "DateProperty": "2020-05-17" }
```

设备模型中的以下代码片段显示 `duration` 属性类型的定义：

```json
{
  "@type": "Property",
  "displayName": {
    "en": "DurationProperty"
  },
  "name": "DurationProperty",
  "schema": "duration",
  "writable": false
}
```

设备客户端应发送 JSON 有效负载，将其作为设备孪生中报告的属性，类似于以下示例 - 持续时间必须符合 ISO 8601 Duration 标准：

```json
{ "DurationProperty": "PT10H24M6.169083011336625S" }
```

设备模型中的以下代码片段显示 `float` 属性类型的定义：

```json
{
  "@type": "Property",
  "displayName": {
    "en": "FloatProperty"
  },
  "name": "FloatProperty",
  "schema": "float",
  "writable": false
}
```

设备客户端应发送 JSON 有效负载，将其作为设备孪生中报告的属性，类似于以下示例：

```json
{ "FloatProperty": 1.9 }
```

设备模型中的以下代码片段显示 `string` 属性类型的定义：

```json
{
  "@type": "Property",
  "displayName": {
    "en": "StringProperty"
  },
  "name": "StringProperty",
  "schema": "string",
  "writable": false
}
```

设备客户端应发送 JSON 有效负载，将其作为设备孪生中报告的属性，类似于以下示例：

```json
{ "StringProperty": "A string value - could be a URL" }
```

### <a name="complex-types"></a>复杂类型

本部分显示设备发送到 IoT Central 应用程序的复杂属性类型的示例。

设备模型中的以下代码片段显示 `geopoint` 属性类型的定义：

```json
{
  "@type": "Property",
  "displayName": {
    "en": "GeopointProperty"
  },
  "name": "GeopointProperty",
  "schema": "geopoint",
  "writable": false
}
```

> [!NOTE]
> geopoint 架构类型不属于[数字孪生定义语言规范](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)。 IoT Central 目前支持 geopoint 架构类型和 location 语义类型，以便向后兼容 。

设备客户端应发送 JSON 有效负载，将其作为设备孪生中报告的属性，类似于以下示例：

```json
{
  "GeopointProperty": {
    "lat": 47.64263,
    "lon": -122.13035,
    "alt": 0
  }
}
```

设备模型中的以下代码片段显示 `Enum` 属性类型的定义：

```json
{
  "@type": "Property",
  "displayName": {
    "en": "EnumProperty"
  },
  "name": "EnumProperty",
  "writable": false,
  "schema": {
    "@type": "Enum",
    "displayName": {
      "en": "Enum"
    },
    "valueSchema": "integer",
    "enumValues": [
      {
        "displayName": {
          "en": "Item1"
        },
        "enumValue": 0,
        "name": "Item1"
      },
      {
        "displayName": {
          "en": "Item2"
        },
        "enumValue": 1,
        "name": "Item2"
      },
      {
        "displayName": {
          "en": "Item3"
        },
        "enumValue": 2,
        "name": "Item3"
      }
    ]
  }
}
```

设备客户端应发送 JSON 有效负载，将其作为设备孪生中报告的属性，类似于以下示例。 值可能为 `0` 和 `1`，它们在 IoT Central 中显示为 `Item1`、`Item2` 和 `Item3`：

```json
{ "EnumProperty": 1 }
```

设备模型中的以下代码片段显示 `Object` 属性类型的定义。 此对象具有类型为 `string` 和 `integer` 的两个字段：

```json
{
  "@type": "Property",
  "displayName": {
    "en": "ObjectProperty"
  },
  "name": "ObjectProperty",
  "writable": false,
  "schema": {
    "@type": "Object",
    "displayName": {
      "en": "Object"
    },
    "fields": [
      {
        "displayName": {
          "en": "Field1"
        },
        "name": "Field1",
        "schema": "integer"
      },
      {
        "displayName": {
          "en": "Field2"
        },
        "name": "Field2",
        "schema": "string"
      }
    ]
  }
}
```

设备客户端应发送 JSON 有效负载，将其作为设备孪生中报告的属性，类似于以下示例：

```json
{
  "ObjectProperty": {
    "Field1": 37,
    "Field2": "A string value"
  }
}
```

设备模型中的以下代码片段显示 `vector` 属性类型的定义：

```json
{
  "@type": "Property",
  "displayName": {
    "en": "VectorProperty"
  },
  "name": "VectorProperty",
  "schema": "vector",
  "writable": false
}
```

设备客户端应发送 JSON 有效负载，将其作为设备孪生中报告的属性，类似于以下示例：

```json
{
  "VectorProperty": {
    "x": 74.72395045538597,
    "y": 74.72395045538597,
    "z": 74.72395045538597
  }
}
```

### <a name="writable-property-types"></a>可写属性类型

本部分介绍设备从 IoT Central 应用程序接收的可写属性类型的示例。

如果在组件中定义了可写属性，则所需属性消息包含组件名称。 以下示例展示了请求设备更新 `thermostat2` 组件中 `targetTemperature` 的消息。 标记 `__t` 指示此组件：

```json
{
  "thermostat2": {
    "targetTemperature": {
      "value": 57
    },
    "__t": "c"
  },
  "$version": 3
}
```

若要了解详细信息，请参阅[教程：创建客户端应用程序并将其连接到 Azure IoT Central 应用程序](tutorial-connect-device.md)。

IoT Central 要求设备对可写属性更新作出响应。 响应消息应当包含 `ac` 和 `av` 字段。 `ad` 字段为可选。 有关示例，请参阅以下代码片段。

`ac` 是一个数值字段，它使用下表中的值：

| 值 | Label | 说明 |
| ----- | ----- | ----------- |
| `'ac': 200` | 已完成 | 属性更改操作已成功完成。 |
| `'ac': 202` 或 `'ac': 201` | 挂起的 | 属性更改操作已挂起或正在进行 |
| `'ac': 4xx` | 错误 | 所请求的属性更改无效或有错误 |
| `'ac': 5xx` | 错误 | 设备在处理所请求的更改时遇到意外错误。 |

`av` 是发送到设备的版本号。

`ad` 是一个选项字符串说明。

设备模型中的以下代码片段显示了一个可写 `string` 属性类型的定义：

```json
{
  "@type": "Property",
  "displayName": {
    "en": "StringPropertyWritable"
  },
  "name": "StringPropertyWritable",
  "writable": true,
  "schema": "string"
}
```

设备从 IoT Central 接收以下有效负载：

```json
{  
  "StringPropertyWritable": "A string from IoT Central", "$version": 7
}
```

处理更新后，设备应将以下 JSON 有效负载发送到 IoT Central。 此消息包括接收自 IoT Central 的原始更新的版本号。 IoT Central 接收到此消息后，它会在 UI 中将属性标记为“已同步”：

```json
{
  "StringPropertyWritable": {
    "value": "A string from IoT Central",
    "ac": 200,
    "ad": "completed",
    "av": 7
  }
}
```

设备模型中的以下代码片段显示了一个可写 `Enum` 属性类型的定义：

```json
{
  "@type": "Property",
  "displayName": {
    "en": "EnumPropertyWritable"
  },
  "name": "EnumPropertyWritable",
  "writable": true,
  "schema": {
    "@type": "Enum",
    "displayName": {
      "en": "Enum"
    },
    "valueSchema": "integer",
    "enumValues": [
      {
        "displayName": {
          "en": "Item1"
        },
        "enumValue": 0,
        "name": "Item1"
      },
      {
        "displayName": {
          "en": "Item2"
        },
        "enumValue": 1,
        "name": "Item2"
      },
      {
        "displayName": {
          "en": "Item3"
        },
        "enumValue": 2,
        "name": "Item3"
      }
    ]
  }
}
```

设备从 IoT Central 接收以下有效负载：

```json
{  
  "EnumPropertyWritable":  1 , "$version": 10
}
```

处理更新后，设备应将以下 JSON 有效负载发送到 IoT Central。 此消息包括接收自 IoT Central 的原始更新的版本号。 IoT Central 接收到此消息后，它会在 UI 中将属性标记为“已同步”：

```json
{
  "EnumPropertyWritable": {
    "value": 1,
    "ac": 200,
    "ad": "completed",
    "av": 10
  }
}
```

## <a name="commands"></a>命令

如果在组件中定义了命令，则设备接收的命令的名称包括组件名称。 例如，如果命令名称为 `getMaxMinReport`，并且组件名称为 `thermostat2`，则设备会收到请求，执行名称为 `thermostat2*getMaxMinReport` 的命令。

设备模型中的以下代码片段显示没有参数并且不要求设备返回任何内容的命令的定义：

```json
{
  "@type": "Command",
  "displayName": {
    "en": "CommandBasic"
  },
  "name": "CommandBasic"
}
```

设备在请求中接收到一个空的有效负载，并且应在响应中返回一个空的有效负载，同时使用 `200` HTTP 响应代码指示成功。

设备模型中的以下代码片段显示具有整数参数并且要求设备返回整数值的命令的定义：

```json
{
  "@type": "Command",
  "request": {
    "@type": "CommandPayload",
    "displayName": {
      "en": "RequestParam"
    },
    "name": "RequestParam",
    "schema": "integer"
  },
  "response": {
    "@type": "CommandPayload",
    "displayName": {
      "en": "ResponseParam"
    },
    "name": "ResponseParam",
    "schema": "integer"
  },
  "displayName": {
    "en": "CommandSimple"
  },
  "name": "CommandSimple"
}
```

设备接收一个整数值作为请求有效负载。 设备应返回一个整数值作为响应有效负载，并返回 `200` HTTP 响应代码以指示成功。

设备模型中的以下代码片段显示具有对象参数并且要求设备返回对象的命令的定义。 此示例中，两个对象都有整数和字符串字段：

```json
{
  "@type": "Command",
  "request": {
    "@type": "CommandPayload",
    "displayName": {
      "en": "RequestParam"
    },
    "name": "RequestParam",
    "schema": {
      "@type": "Object",
      "displayName": {
        "en": "Object"
      },
      "fields": [
        {
          "displayName": {
            "en": "Field1"
          },
          "name": "Field1",
          "schema": "integer"
        },
        {
          "displayName": {
            "en": "Field2"
          },
          "name": "Field2",
          "schema": "string"
        }
      ]
    }
  },
  "response": {
    "@type": "CommandPayload",
    "displayName": {
      "en": "ResponseParam"
    },
    "name": "ResponseParam",
    "schema": {
      "@type": "Object",
      "displayName": {
        "en": "Object"
      },
      "fields": [
        {
          "displayName": {
            "en": "Field1"
          },
          "name": "Field1",
          "schema": "integer"
        },
        {
          "displayName": {
            "en": "Field2"
          },
          "name": "Field2",
          "schema": "string"
        }
      ]
    }
  },
  "displayName": {
    "en": "CommandComplex"
  },
  "name": "CommandComplex"
}
```

以下代码片段显示发送到设备的示例请求有效负载：

```json
{ "Field1": 56, "Field2": "A string value" }
```

以下代码片段显示发送自设备的示例响应有效负载。 使用 `200` HTTP 响应代码指示成功：

```json
{ "Field1": 87, "Field2": "Another string value" }
```

### <a name="long-running-commands"></a>长时间运行的命令

设备模型中的以下代码片段显示命令的定义。 该命令具有整数参数，并要求设备返回整数值：

```json
{
  "@type": "Command",
  "request": {
    "@type": "CommandPayload",
    "displayName": {
      "en": "RequestParam"
    },
    "name": "RequestParam",
    "schema": "integer"
  },
  "response": {
    "@type": "CommandPayload",
    "displayName": {
      "en": "ResponseParam"
    },
    "name": "ResponseParam",
    "schema": "integer"
  },
  "displayName": {
    "en": "LongRunningCommandSimple"
  },
  "name": "LongRunningCommandSimple"
}
```

设备接收一个整数值作为请求有效负载。 如果设备需要时间处理此命令，它应该返回一个空的响应有效负载，同时使用 `202` HTTP 响应代码指示设备已接受处理请求。

设备处理完请求后，它应该将类似于以下示例的属性发送到 IoT Central。 属性名称必须与命令名称相同：

```json
{
  "LongRunningCommandSimple": {
    "value": 87
  }
}
```

### <a name="offline-commands"></a>脱机命令

在 IoT Central Web UI 中，可以为命令选择“脱机时排队”选项。 脱机命令是在设备连接后从解决方案发送到设备的单向通知。 脱机命令可以有请求参数，但不返回响应。

如果从设备模板导出模型或接口，则不包含“脱机时排队”设置。 无法通过查看导出的模型或接口 JSON 来判断命令是否是脱机命令。

脱机命令使用 [IoT 中心云到设备的消息](../../iot-hub/iot-hub-devguide-messages-c2d.md)将命令和有效负载发送到设备。

设备接收的消息的有效负载是参数的原始值。 名为 `method-name` 的自定义属性存储 IoT Central 命令的名称。 下表显示了一些有效负载的示例：

| IoT Central 请求架构 | 设备接收的有效负载示例 |
| -------------------------- | ---------------------------------- |
| 无请求参数       | `@`                                |
| Double                     | `1.23`                             |
| 字符串                     | `sample string`                    |
| 对象                     | `{"StartTime":"2021-01-05T08:00:00.000Z","Bank":2}` |

设备模型中的以下代码片段显示命令的定义。 该命令具有一个对象参数，其中包含日期/时间字段和枚举：

```json
{
  "@type": "Command",
  "displayName": {
    "en": "Generate Diagnostics"
  },
  "name": "GenerateDiagnostics",
  "request": {
    "@type": "CommandPayload",
    "displayName": {
      "en": "Payload"
    },
    "name": "Payload",
    "schema": {
      "@type": "Object",
      "displayName": {
        "en": "Object"
      },
      "fields": [
        {
          "displayName": {
            "en": "StartTime"
          },
          "name": "StartTime",
          "schema": "dateTime"
        },
        {
          "displayName": {
            "en": "Bank"
          },
          "name": "Bank",
          "schema": {
            "@type": "Enum",
            "displayName": {
              "en": "Enum"
            },
            "enumValues": [
              {
                "displayName": {
                  "en": "Bank 1"
                },
                "enumValue": 1,
                "name": "Bank1"
              },
              {
                "displayName": {
                  "en": "Bank2"
                },
                "enumValue": 2,
                "name": "Bank2"
              },
              {
                "displayName": {
                  "en": "Bank3"
                },
                "enumValue": 3,
                "name": "Bank3"
              }
            ],
            "valueSchema": "integer"
          }
        }
      ]
    }
  }
}
```

如果为上一个代码片段中的命令启用设备模板 UI 中的“脱机时排队”选项，则设备接收的消息包括以下属性：

| 属性名称 | 示例值 |
| ---------- | ----- |
| `custom_properties` | `{'method-name': 'GenerateDiagnostics'}` |
| `data` | `{"StartTime":"2021-01-05T08:00:00.000Z","Bank":2}` |

## <a name="next-steps"></a>后续步骤

了解设备模板后，建议接下来阅读[连接到 Azure IoT Central](./concepts-get-connected.md)，详细了解如何使用 IoT Central 注册设备以及 IoT Central 如何保护设备连接。
