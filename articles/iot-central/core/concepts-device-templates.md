---
title: 什么是 Azure IoT Central 中的设备模板 |Microsoft Docs
description: Azure IoT Central 设备模板使你能够指定连接到应用程序的设备的行为。 设备模板用于指定设备必须实现的遥测、属性和命令。 设备模板还定义 IoT Central 中设备的 UI（例如，操作员使用的窗体和视图）。
author: dominicbetts
ms.author: dobett
ms.date: 08/24/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: d0e5f563e61cec96e67dd998e969a34a8da3615f
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2021
ms.locfileid: "123481229"
---
# <a name="what-are-device-templates"></a>什么是设备模板？

Azure IoT Central 中的设备模板是一个蓝图，用于定义可连接到应用程序的某种设备的特征和行为。 例如，设备模板定义设备发送的遥测数据，以便 IoT Central 使用正确单位和数据类型创建可视化效果。

解决方案构建者将设备模板添加到 IoT Central 应用程序。 设备开发者编写实现设备模板中定义的行为的设备代码。

设备模板包含以下各部分：

- _设备模型_。 设备模板的此部分定义设备与应用程序交互的方式。 设备开发者实现模型中定义的行为。
    - 根组件。 每个设备模型都有根组件。 根组件的接口描述了特定于设备模型的功能。
    - _组件_。 除了描述设备功能的根组件之外，设备模型还可能包含其他组件。 每个组件都有一个用于描述组件功能的接口。 组件接口可以在其他设备模型中重复使用。 例如，多个电话设备模型可能使用同一照相机接口。
    - _继承接口_。 设备模型包含一个或多个可扩展根组件功能的接口。
- _云属性_。 设备模板的此部分使解决方案开发者可以指定任何要存储的设备元数据。 云属性永远不会与设备同步，而只存在于应用程序中。 云属性不影响设备开发者为实现设备模型而编写的代码。
- _自定义_。 设备模板的此部分使解决方案开发者可以替代设备模型中的部分定义。 如果解决方案开发者想要优化应用程序处理值的方式（例如更改属性的显示名称或用于显示遥测值的颜色），则自定义功能很有用。 自定义不会影响设备开发者为实现设备模型而编写的代码。
- _视图_。 设备模板的此部分使解决方案开发者可以定义可视化，以查看设备中的数据，以及用于管理和控制设备的窗体。 视图使用设备模型、云属性和自定义。 视图不影响设备开发者为实现设备模型而编写的代码。

## <a name="device-models"></a>设备模型

设备模型定义设备与 IoT Central 应用程序交互的方式。 设备开发者必须确保设备实现设备模型中定义的行为，以便 IoT Central 能够监视和管理设备。 设备模型由一个或多个“接口”组成，每个接口都可以定义“遥测”类型、“设备属性”和“命令”的集合。 解决方案开发者可以将定义设备模型的 JSON 文件导入设备模板，或使用 IoT Central 中的 web UI 来创建或编辑设备模型。

若要详细了解如何编辑设备模型，请参阅[编辑现有设备模板](howto-edit-device-template.md)

解决方案开发者还可以导出包含设备模型的 JSON 文件。 设备开发者可以使用此 JSON 文档来了解设备应该如何与 IoT Central 的应用程序进行通信。

定义设备模型的 JSON 文件使用[数字孪生体定义语言 (DTDL) V2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)。 IoT Central 要求 JSON 文件包含带有以内联方式（而不是在单独的文件中）定义接口的设备模型。 若要了解详细信息，请参阅 [IoT 即插即用建模指南](../../iot-develop/concepts-modeling-guide.md)。

典型的 IoT 设备由以下内容组成：

- 自定义部件，这是使你的设备独一无二的内容。
- 标准部件，是所有设备通用的内容。

这些部件在设备模型中称为“接口”。 接口定义设备实现的每个部件的详细信息。 接口可跨设备模型重复使用。 在 DTDL 中，组件引用另一个接口，该接口可能是在单独的 DTDL 文件中或该文件的一个单独的部分中定义的。

以下示例概述了[温度控制器设备](https://github.com/Azure/iot-plugandplay-models/blob/main/dtmi/com/example/temperaturecontroller-2.json)的设备模型。 根组件包含对 `workingSet`、`serialNumber` 和 `reboot` 的定义。 设备模型还包括两个 `thermostat` 组件和一个 `deviceInformation` 组件。 为了简洁起见，已删除了这三个组件的内容：

```json
[
  {
    "@context": [
      "dtmi:iotcentral:context;2",
      "dtmi:dtdl:context;2"
    ],
    "@id": "dtmi:com:example:TemperatureController;2",
    "@type": "Interface",
    "contents": [
      {
        "@type": [
          "Telemetry",
          "DataSize"
        ],
        "description": {
          "en": "Current working set of the device memory in KiB."
        },
        "displayName": {
          "en": "Working Set"
        },
        "name": "workingSet",
        "schema": "double",
        "unit": "kibibit"
      },
      {
        "@type": "Property",
        "displayName": {
          "en": "Serial Number"
        },
        "name": "serialNumber",
        "schema": "string",
        "writable": false
      },
      {
        "@type": "Command",
        "commandType": "synchronous",
        "description": {
          "en": "Reboots the device after waiting the number of seconds specified."
        },
        "displayName": {
          "en": "Reboot"
        },
        "name": "reboot",
        "request": {
          "@type": "CommandPayload",
          "description": {
            "en": "Number of seconds to wait before rebooting the device."
          },
          "displayName": {
            "en": "Delay"
          },
          "name": "delay",
          "schema": "integer"
        }
      },
      {
        "@type": "Component",
        "displayName": {
          "en": "thermostat1"
        },
        "name": "thermostat1",
        "schema": "dtmi:com:example:Thermostat;2"
      },
      {
        "@type": "Component",
        "displayName": {
          "en": "thermostat2"
        },
        "name": "thermostat2",
        "schema": "dtmi:com:example:Thermostat;2"
      },
      {
        "@type": "Component",
        "displayName": {
          "en": "DeviceInfo"
        },
        "name": "deviceInformation",
        "schema": "dtmi:azure:DeviceManagement:DeviceInformation;1"
      }
    ],
    "displayName": {
      "en": "Temperature Controller"
    }
  },
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:example:Thermostat;2",
    "@type": "Interface",
    "displayName": "Thermostat",
    "description": "Reports current temperature and provides desired temperature control.",
    "contents": [
      ...
    ]
  },
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:azure:DeviceManagement:DeviceInformation;1",
    "@type": "Interface",
    "displayName": "Device Information",
    "contents": [
      ...
    ]
  }
]
```

接口具有一些必填字段：

- `@id`：唯一 ID，采用简单统一资源名称的形式。
- `@type`：声明此对象是接口。
- `@context`：指定用于接口的 DTDL 版本。
- `contents`：列出组成设备的属性、遥测和命令。 这些功能可在多个接口中定义。

有一些可选字段可用于向功能模型中添加更多详细信息，例如显示名称和说明。

“实现”部分的接口列表中的每个条目都有：

- `name`：接口的编程名称。
- `schema`：功能模型实现的接口。

## <a name="interfaces"></a>接口

DTDL 用于描述设备的功能。 相关功能分组为接口。 接口描述设备实现的部分属性、遥测和命令：

- `Properties`. 属性是表示设备状态的数据字段。 使用属性来表示设备的持久状态，如冷却泵的开关状态。 属性还可以表示基本设备属性，例如设备的固件版本。 你可以将属性声明为只读或可写。 只有设备可以更新只读属性的值。 操作员可以设置要发送到设备的可写属性的值。
- `Telemetry`. 遥测字段表示传感器的度量值。 如果设备采用传感器度量，应发送包含传感器数据的遥测事件。
- `Commands`. 命令表示设备用户可以对设备执行的任务。 例如，使用重置命令或打开或关闭风扇的命令。

以下示例说明了恒温器接口定义：

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;2",
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

此示例展示了两个属性（一个只读属性和一个可写属性）、一个遥测类型和一个命令。 最小字段说明包括：

- `@type` 指定功能类型：`Telemetry`、`Property` 或 `Command`。  在某些情况下，该类型包括一个语义类型，以使 IoT Central 可以做出有关如何处理该值的一些假设。
- `name` 为遥测值。
- `schema` 指定遥测的数据类型或属性。 此值可以是基元类型，如 double、integer、boolean 或 string。 还支持复杂的对象类型和映射。

可选字段（如显示名称和说明）允许用户向接口和功能添加更多详细信息。

## <a name="properties"></a>属性

默认情况下，属性是只读的。 只读属性意味着设备将属性值更新报告给你的 IoT Central 应用程序。 IoT Central 应用程序无法设置只读属性的值。

还可以将接口上的属性标记为可写。 设备可以从你的 IoT Central 应用程序中接收可写属性的更新，以及向应用程序报告属性值更新。

设备无需连接即可设置属性值。 当设备下一次连接到应用程序时，将传输更新的值。 此行为同时适用于只读属性和可写属性。

不要使用属性从设备发送遥测数据。 例如，一种只读属性（例如 `temperatureSetting=80`）意味着设备温度已设置为 80，并且设备尝试达到或停留在此温度。

对于可写属性，设备应用程序会返回所需状态的状态代码、版本和说明，以指示是否已收到并应用属性值。

## <a name="telemetry"></a>遥测

使用 IoT Central，可以在设备视图和图表中查看遥测数据，并通过规则在达到阈值时触发操作。 IoT Central 使用设备模型中的信息（如数据类型、单位和显示名称）来确定如何显示遥测值。 还可以在应用程序和个人仪表板上显示遥测值。

你可以使用 IoT Central 数据导出功能将遥测数据流式传输到其他目标（例如存储或事件中心）。

## <a name="commands"></a>命令

默认情况下，命令必须在 30 秒内执行，并且设备在命令到达时必须处于已连接状态。 如果设备未按时响应或设备未连接，则该命令将失败。

命令可以包含请求参数并返回响应。

### <a name="offline-commands"></a>脱机命令

如果设备当前脱机，则可通过为设备模板中的命令启用“脱机时排队”选项，选择排队命令。

脱机命令是从解决方案到设备的单向通知。 脱机命令可以有请求参数，但不返回响应。

> [!NOTE]
> 此选项仅在 IoT Central web UI 中可用。 如果从设备模板导出模型或接口，则不包含此设置。

## <a name="cloud-properties"></a>云属性

云属性是设备模板的一部分，但不是设备模型的一部分。 云属性使解决方案开发者可以指定要存储在 IoT Central 应用程序中的任何设备元数据。 云属性不影响设备开发者为实现设备模型而编写的代码。

解决方案开发者可以将云属性与设备属性一起添加到设备视图和窗体中，使操作员能够管理连接到应用程序的设备。 解决方案开发者还可以使用云属性作为规则定义的一部分，使操作员可以编辑阈值。

## <a name="customizations"></a>自定义

自定义是设备模板的一部分，但不是设备模型的一部分。 解决方案开发者可利用自定义增强或替代设备模型中的某些定义。 例如，解决方案开发者可以更改遥测类型或属性的显示名称。 解决方案开发者还可以使用自定义添加验证，如字符串设备属性的最小或最大长度。

自定义可能会影响设备开发者为实现设备模型而编写的代码。 例如，自定义可以设置遥测数据的最小和最大字符串长度或最小和最大数值。

## <a name="views"></a>视图

解决方案开发者创建视图，以支持操作员监视和管理连接的设备。 视图是设备模板的一部分，因此视图与特定设备类型相关联。 视图可以包括：

- 绘制遥测数据的图表。
- 显示只读设备属性的磁贴。
- 允许操作员编辑可写设备属性的磁贴。
- 允许操作员编辑云属性的磁贴。
- 允许操作员调用命令的磁贴，包括需要有效负载的命令。
- 显示标签、图像或 markdown 文本的磁贴。

设备模板中的设备模型、云属性和自定义决定了哪些遥测、属性和命令可以添加到视图。

## <a name="next-steps"></a>后续步骤

现在，你已经了解了设备模板，建议的后续步骤是阅读[遥测、属性和命令有效负载](./concepts-telemetry-properties-commands.md)，详细了解设备与 IoT Central 交换的数据。
