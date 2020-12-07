---
title: 使用 Azure IoT Central 解决方案中的属性
description: 了解如何在 Azure IoT Central 解决方案中使用只读和可写属性。
author: dominicbetts
ms.author: dobett
ms.date: 11/06/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: a517f7a796b6543c8d60f0d1ebdba16afa0bc4b7
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/07/2020
ms.locfileid: "96751421"
---
# <a name="use-properties-in-an-azure-iot-central-solution"></a>使用 Azure IoT Central 解决方案中的属性

本文介绍如何使用 Azure IoT Central 应用程序的设备模板中定义的设备属性。

属性表示时间点值。 例如，设备可使用属性来报告它尝试达到的目标温度。 默认情况下，设备属性在 IoT Central 中是只读的。 可写属性使你可以在设备和 Azure IoT Central 应用程序之间同步状态。

你还可以在 Azure IoT Central 应用程序中定义云属性。 云属性值永远不会与设备交换，并超出了本文的范围。

## <a name="define-your-properties"></a>定义属性

属性是表示设备状态的数据字段。 使用属性来表示设备的持久状态，例如设备的开/关状态。 属性还可以表示设备的基本设备属性，例如设备的软件版本。 可以将属性声明为只读或可写。

以下屏幕截图显示 Azure IoT Central 应用程序中的属性定义。

:::image type="content" source="media/howto-use-properties/property-definition.png" alt-text="屏幕截图，显示 Azure IoT Central 应用程序中的属性定义。":::

下表显示了属性功能的配置设置。

| 字段           | 说明                                                                                                                                                                                                                        |
|-----------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 显示名称    | 仪表板和窗体上使用的属性值的显示名称。                                                                                                                                                              |
| 名称            | 属性的名称。 Azure IoT Central 会根据显示名称生成此字段的值，但你可以根据需要选择自己的值。 此字段必须为字母数字。                                                 |
| 功能类型 | 属性。                                                                                                                                                                                                                          |
| 语义类型   | 属性的语义类型，如温度、状态或事件。 选择的语义类型将决定以下哪些字段可用。                                                                       |
| 架构          | 属性数据类型，如 double、string 或 vector。 可用的选项取决于语义类型。 架构不可用于事件和状态语义类型。                                               |
| 可写       | 如果该属性不是可写的，则设备可以将属性值报告给 Azure IoT Central。 如果该属性是可写的，则设备可以将属性值报告给 Azure IoT Central。 然后，Azure IoT Central 可以将属性更新发送到设备。 |
| 严重性        | 仅适用于事件语义类型。 严重性为“错误”、“信息”或“警告”。                                                                                                                         |
| 状态值    | 仅适用于状态语义类型。 定义可能的状态值，其中每个状态值都具有显示名称、名称、枚举类型和值。                                                                                   |
| 计价单位            | 属性值的单位，如 **mph**、 **%** 或 **&deg; C**。                                                                                                                                                              |
| 显示单位    | 仪表板和窗体上使用的显示单位。                                                                                                                                                                                    |
| 评论         | 有关属性功能的任何注释。                                                                                                                                                                                        |
| 说明     | 属性功能的说明。                                                                                                                                                                                          |

属性还可以在设备模板中的接口中定义，如下所示：

``` json
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
}
```

此示例显示了两个属性。 这些属性与 UI 中的属性定义相关：

* `@type` 指定功能类型： `Property` 。 前面的示例还显示了 `Temperature` 这两个属性的语义类型。
* `name` 属性的。
* `schema` 指定属性的数据类型。 此值可以是基元类型，如 double、integer、Boolean 或 string。 还支持复杂的对象类型和映射。
* `writable` 默认情况下，属性是只读的。 您可以使用此字段将属性标记为可写。

可选字段（如显示名称和说明）使你可以向界面和功能添加更多详细信息。

当您创建属性时，您可以指定复杂的架构类型（如 **对象** 和 **枚举**）。

![显示如何添加功能的屏幕截图。](./media/howto-use-properties/property.png)

选择复杂 **架构**（如 **对象**）时，还需要定义对象。

:::image type="content" source="media/howto-use-properties/object.png" alt-text="显示如何定义对象的屏幕截图":::

下面的代码演示对象属性类型的定义。 此对象包含两个字段，其类型为 string 和 integer。

``` json
{
  "@type": "Property",
  "displayName": {
    "en": "ObjectProperty"
  },
  "name": "ObjectProperty",
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

## <a name="implement-read-only-properties"></a>实现只读属性

默认情况下，属性是只读的。 只读属性允许将设备报表属性值更新为 Azure IoT Central 应用程序。 Azure IoT Central 应用程序无法设置只读属性的值。

Azure IoT Central 使用设备孪生在设备和 Azure IoT Central 应用程序之间同步属性值。 设备属性值使用设备孪生报告属性。 有关详细信息，请参阅 [设备孪生](../../iot-hub/tutorial-device-twins.md)。

设备模型中的以下代码片段显示了只读属性类型的定义：

``` json
{
  "name": "model",
  "displayName": "Device model",
  "schema": "string",
  "comment": "Device model name or ID. Ex. Surface Book 2."
}
```

属性更新作为 JSON 负载由设备发送。 有关详细信息，请参阅 [有效负载](./concepts-telemetry-properties-commands.md)。

可以使用 Azure IoT 设备 SDK 将属性更新发送到 Azure IoT Central 应用程序。

通过使用以下功能，可以将设备克隆属性发送到 Azure IoT Central 应用程序：

``` javascript
hubClient.getTwin((err, twin) => {
    const properties = {
        model: 'environmentalSensor1.0'
    };
    twin.properties.reported.update(properties, (err) => {
        console.log(err ? `error: ${err.toString()}` : `status: success` )
    });
});
```

为了简单起见，本文使用 Node.js。 有关设备应用程序示例的完整信息，请参阅以下 [创建客户端应用程序并将其连接到 Azure IoT Central 应用程序](tutorial-connect-device.md) 教程。

Azure IoT Central 应用程序中的以下视图显示了你可以查看的属性。 视图自动使 **设备模型** 属性成为 _只读设备属性_。

:::image type="content" source="media/howto-use-properties/read-only.png" alt-text="显示只读属性的视图的屏幕截图":::

## <a name="implement-writable-properties"></a>实现可写属性

可写属性由 Azure IoT Central 应用程序中的某个运算符在窗体上设置。 Azure IoT Central 会将属性发送到设备。 Azure IoT Central 需要设备的确认。

设备模型中的以下代码片段显示了可写属性类型的定义：

``` json
{
  "@type": "Property",
  "displayName": "Brightness Level",
  "description": "The brightness level for the light on the device. Can be specified as 1 (high), 2 (medium), 3 (low)",
  "name": "brightness",
  "writable": true,
  "schema": "long"
}
```

若要定义和处理设备响应的可写属性，可以使用以下代码：

``` javascript
hubClient.getTwin((err, twin) => {
    twin.on('properties.desired.brightness', function(desiredBrightness) {
        console.log( `Received setting: ${desiredBrightness.value}` );
        var patch = {
            brightness: {
                value: desiredBrightness.value,
                ad: 'success',
                ac: 200,
                av: desiredBrightness.$version
            }
        }
        twin.properties.reported.update(patch, (err) => {
            console.log(err ? `error: ${err.toString()}` : `status: success` )
        });
    });
});
```

响应消息应包含 `ac` 和 `av` 字段。 `ad` 字段为可选。 有关示例，请参阅以下代码片段：

* `ac` 使用下表中的值的数值字段。
* `av` 发送到设备的版本号。
* `ad` 是一个选项字符串说明。

| 值 | Label | 说明 |
| ----- | ----- | ----------- |
| `'ac': 200` | 已完成 | 属性更改操作已成功完成。 |
| `'ac': 202` 或 `'ac': 201` | 挂起的 | 属性更改操作已挂起或正在进行。 |
| `'ac': 4xx` | 错误 | 请求的属性更改无效或出现错误。 |
| `'ac': 5xx` | 错误 | 设备在处理请求的更改时遇到意外错误。 |

有关设备孪生的详细信息，请参阅 [通过后端服务配置设备](../../iot-hub/tutorial-device-twins.md)。

当操作员在 Azure IoT Central 应用程序中设置可写属性时，应用程序将使用设备克隆的所需属性将该值发送到设备。 然后，设备使用 "设备克隆" 报告属性进行响应。 当 Azure IoT Central 收到报告的属性值时，它会更新状态为 "已 **接受**" 的属性视图。

以下视图显示可写属性。 输入值并选择 " **保存**" 后，初始状态为 " **挂起**"。 设备接受更改后，状态将更改为 "已 **接受**"。

![显示挂起状态的屏幕截图。](./media/howto-use-properties/status-pending.png)

![显示接受的属性的屏幕截图。](./media/howto-use-properties/accepted.png)

## <a name="next-steps"></a>后续步骤

现在，你已了解如何使用 Azure IoT Central 应用程序中的属性，请参阅：

* [Payloads](concepts-telemetry-properties-commands.md)
* [创建客户端应用程序并将其连接到 Azure IoT Central 应用程序](tutorial-connect-device.md)