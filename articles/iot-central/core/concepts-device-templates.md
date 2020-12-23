---
title: 什么是 Azure 中的设备模板 IoT Central |Microsoft Docs
description: Azure IoT Central 设备模板使你能够指定连接到应用程序的设备的行为。 设备模板指定设备必须实现的遥测、属性和命令。 设备模板还在 IoT Central （例如，操作员使用的窗体和仪表板）中定义设备的 UI。
author: dominicbetts
ms.author: dobett
ms.date: 11/05/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: 1a352849cb5bb8563a7e09500f081139a24e97ea
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/07/2020
ms.locfileid: "96750690"
---
# <a name="what-are-device-templates"></a>什么是设备模板？

_本文适用于设备开发人员和解决方案构建者。_

Azure IoT Central 中的设备模板是一个蓝图，用于定义连接到应用程序的设备类型的特征和行为。 例如，设备模板定义设备发送的遥测，以便 IoT Central 可以创建使用正确单位和数据类型的可视化效果。

解决方案生成器将设备模板添加到 IoT Central 的应用程序。 设备开发人员写入实现设备模板中定义的行为的设备代码。

设备模板包括以下部分：

- _设备型号_。 设备模板的此部分定义设备与应用程序交互的方式。 设备开发人员实现模型中定义的行为。
    - _默认组件_。 每个设备模型都具有默认组件。 默认组件的界面描述了特定于设备模型的功能。
    - _组件_。 除了描述设备功能的默认组件之外，设备模型还可以包含组件。 每个组件都有一个用于描述组件功能的接口。 组件接口可以在其他设备模型中重复使用。 例如，多个电话设备型号可能使用同一照相机接口。
    - _继承接口_。 设备模型包含一个或多个扩展默认组件功能的接口。
- _云属性_。 设备模板的此部分使解决方案开发人员可以指定任何要存储的设备元数据。 云属性永远不会与设备同步，而只存在于应用程序中。 云属性不影响设备开发人员为实现设备模型而编写的代码。
- _自定义_。 设备模板的此部分使解决方案开发人员可以重写设备模型中的部分定义。 如果解决方案开发人员想要优化应用程序处理值的方式（例如更改属性的显示名称或用于显示遥测值的颜色），则自定义项很有用。 自定义不会影响设备开发人员为实现设备模型而编写的代码。
- _视图_。 设备模板的此部分使解决方案开发人员可以定义可视化，以查看设备中的数据，以及用于管理和控制设备的窗体。 视图使用设备模型、云属性和自定义项。 视图不影响设备开发人员为实现设备模型而编写的代码。

## <a name="device-models"></a>设备模型

设备模型定义设备与 IoT Central 应用程序交互的方式。 设备开发人员必须确保设备实现设备模型中定义的行为，以便 IoT Central 能够监视和管理设备。 设备模型由一个或多个 _接口_ 组成，每个接口都可以定义 _遥测_ 类型、 _设备属性_ 和 _命令_ 的集合。 解决方案开发人员可以将定义设备模型的 JSON 文件导入设备模板，或使用 IoT Central 中的 web UI 来创建或编辑设备模型。 对使用 Web UI 进行的设备模型进行更改需要对 [设备模板进行版本控制](./howto-version-device-template.md)。

解决方案开发人员还可以导出包含设备模型的 JSON 文件。 设备开发人员可以使用此 JSON 文档来了解设备应该如何与 IoT Central 的应用程序进行通信。

定义设备模型的 JSON 文件使用 [数字克隆定义语言 (DTDL) V2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)。 IoT Central 要求 JSON 文件包含带有以内联方式定义的接口（而不是在单独的文件中）的设备模型。

典型的 IoT 设备由以下内容组成：

- 自定义部件，这是使您的设备独一无二的东西。
- 标准部件，是所有设备通用的部分。

这些部件在设备模型中称为 _接口_ 。 接口定义设备实现的每个部件的详细信息。 接口可跨设备型号重复使用。 在 DTDL 中，组件引用在单独的 DTDL 文件中定义的接口。

以下示例显示了温度控制器设备的设备型号的轮廓。 默认组件包含 `workingSet` 、和的定义 `serialNumber` `reboot` 。 设备模型还包括 `thermostat` 和 `deviceInformation` 接口：

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
        "Telemetry", "DataSize"
      ],
      "name": "workingSet",
      "displayName": "Working Set",
      "description": "Current working set of the device memory in KiB.",
      "schema": "double",
      "unit" : "kibibyte"
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
      "name": "thermostat",
      "displayName": "Thermostat",
      "description": "Thermostat One."
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

接口具有一些必需字段：

- `@id`：唯一 ID，采用简单统一资源名称的形式。
- `@type`：声明此对象是接口。
- `@context`：指定用于接口的 DTDL 版本。
- `contents`：列出组成设备的属性、遥测和命令。 这些功能可在多个接口中定义。

有一些可选字段可用于向功能模型中添加更多详细信息，例如显示名称和说明。

"实现" 部分的接口列表中的每个条目都有：

- `name`：接口的编程名称。
- `schema`：功能模型实现的接口。

## <a name="interfaces"></a>接口

DTDL 可让你描述设备的功能。 相关功能分组为接口。 接口描述设备的一部分实现的属性、遥测和命令：

- `Properties`. 属性是表示设备状态的数据字段。 使用属性来表示设备的持久状态，如冷却剂泵的关闭状态。 属性还可以表示基本设备属性，例如设备的固件版本。 你可以将属性声明为只读或可写。 只有设备可以更新只读属性的值。 操作员可以设置要发送到设备的可写属性的值。
- `Telemetry`. 遥测字段表示传感器的度量。 如果设备采用传感器度量，应发送包含传感器数据的遥测事件。
- `Commands`. 命令表示设备的用户可以在设备上执行的方法。 例如，使用 reset 命令或命令来打开或关闭风扇。

下面的示例演示恒温器接口定义：

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
      "displayName" : "Temperature",
      "description" : "Temperature in degrees Celsius.",
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
      "unit" : "degreeCelsius",
      "writable": true
    },
    {
      "@type": [
        "Property",
        "Temperature"
      ],
      "name": "maxTempSinceLastReboot",
      "schema": "double",
      "unit" : "degreeCelsius",
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
        "name" : "tempReport",
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
              "name" : "avgTemp",
              "displayName": "Average Temperature",
              "schema": "double"
            },
            {
              "name" : "startTime",
              "displayName": "Start Time",
              "schema": "dateTime"
            },
            {
              "name" : "endTime",
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

此示例显示了两个属性 (一个只读和一个可写) 、一个遥测类型和一个命令。 最小字段说明具有：

- `@type` 指定功能类型： `Telemetry` 、 `Property` 或 `Command` 。  在某些情况下，该类型包括一个语义类型，以使 IoT Central 可以做出有关如何处理该值的一些假设。
- `name` 对于遥测值。
- `schema` 指定遥测的数据类型或属性。 此值可以是基元类型，如 double、integer、boolean 或 string。 还支持复杂的对象类型和映射。

可选字段（如显示名称和说明）使你可以向界面和功能添加更多详细信息。

## <a name="properties"></a>属性

默认情况下，属性是只读的。 只读属性意味着设备将属性值更新报告给你的 IoT Central 应用程序。 IoT Central 应用程序无法设置只读属性的值。

还可以在接口上将属性标记为可写。 设备可以从你的 IoT Central 应用程序中接收可写属性的更新，以及向应用程序报告属性值更新。

设备无需连接即可设置属性值。 当设备下一次连接到应用程序时，将传输更新的值。 此行为同时适用于只读属性和可写属性。

不要使用属性从设备发送遥测数据。 例如，readonly 属性（如） `temperatureSetting=80` 应意味着设备温度已设置为80，并且设备尝试进入或停留在此温度上。

对于可写属性，设备应用程序会返回所需状态状态代码、版本和说明，以指示是否已收到并应用属性值。

## <a name="telemetry"></a>遥测

IoT Central 允许你查看仪表板和图表上的遥测，并使用规则在达到阈值时触发操作。 IoT Central 使用设备模型中的信息（如数据类型、单位和显示名称）来确定如何显示遥测值。

你可以使用 IoT Central 数据导出功能将遥测流式传输到其他目标（例如存储或事件中心）。

## <a name="commands"></a>命令

默认情况下，命令必须在30秒内执行，并且在命令到达时必须连接设备。 如果设备有时间响应或设备未连接，则该命令将失败。

命令可以包含请求参数并返回响应。

### <a name="offline-commands"></a>脱机命令

如果设备模板中命令的 "脱机" 选项启用 " **脱机** " 选项，则可以选择 "队列命令"。

脱机命令是从解决方案到设备的单向通知。 脱机命令可以有请求参数，但不返回响应。

> [!NOTE]
> 此选项仅在 IoT Central web UI 中可用。 如果从设备模板导出模型或接口，则不包含此设置。

## <a name="cloud-properties"></a>云属性

云属性是设备模板的一部分，但不是设备模型的一部分。 云属性使解决方案开发人员可以指定要存储在 IoT Central 应用程序中的任何设备元数据。 云属性不影响设备开发人员为实现设备模型而编写的代码。

解决方案开发人员可以将云属性添加到仪表板，并将视图添加到设备属性，使操作员能够管理连接到应用程序的设备。 解决方案开发人员还可以使用云属性作为规则定义的一部分，使阈值可编辑阈值。

## <a name="customizations"></a>自定义

自定义项是设备模板的一部分，但不是设备模型的一部分。 自定义使解决方案开发人员可以增强或重写设备模型中的一些定义。 例如，解决方案开发人员可以更改遥测类型或属性的显示名称。 解决方案开发人员还可以使用自定义项添加验证，如字符串设备属性的最小值或最大长度。

自定义可能会影响设备开发人员为实现设备模型而编写的代码。 例如，自定义可以设置遥测的最小和最大字符串长度或最小和最大数值。

## <a name="views"></a>视图

解决方案开发人员创建允许操作员监视和管理连接的设备的视图。 视图是设备模板的一部分，因此视图与特定设备类型相关联。 视图可以包括：

- 用于绘制遥测数据的图表。
- 用于显示只读设备属性的磁贴。
- 磁贴，使操作员可以编辑可写的设备属性。
- 用于使运算符编辑云属性的磁贴。
- 允许操作员调用命令的磁贴，包括需要有效负载的命令。
- 用于显示标签、图像或 markdown 文本的磁贴。

可以添加到视图中的遥测、属性和命令由设备模板中的设备模型、云属性和自定义项决定。

## <a name="next-steps"></a>后续步骤

作为设备开发人员，现在你已了解设备模板，接下来要做的后续步骤是阅读 [遥测、属性和命令有效负载](./concepts-telemetry-properties-commands.md) ，了解有关与 IoT Central 设备交换的数据的详细信息。

作为解决方案开发人员，建议的下一步是 [在 Azure IoT Central 应用程序中阅读定义新的 IoT 设备类型](./howto-set-up-template.md) ，以了解有关如何创建设备模板的详细信息。
