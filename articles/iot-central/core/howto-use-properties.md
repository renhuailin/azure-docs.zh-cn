---
title: 使用 Azure IoT Central 解决方案中的属性
description: 了解如何使用 Azure IoT Central 解决方案中的只读和可写属性。
author: dominicbetts
ms.author: dobett
ms.date: 11/06/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: a0dd00dffaa5112ce04723a12e8b8634f6149690
ms.sourcegitcommit: b5508e1b38758472cecdd876a2118aedf8089fec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2021
ms.locfileid: "113588032"
---
# <a name="use-properties-in-an-azure-iot-central-solution"></a>使用 Azure IoT Central 解决方案中的属性

本操作指南介绍了如何使用 Azure IoT Central 应用程序的设备模板中定义的设备属性。

属性表示时间点值。 例如，设备可使用属性来报告它尝试达到的目标温度。 默认情况下，设备属性在 IoT Central 中是只读的。 使用可写属性，你可以在设备与 Azure IoT Central 应用程序之间同步状态。

你还可以在 Azure IoT Central 应用程序中定义云属性。 云属性值永远不会与设备进行交换，并且不在本文的讨论范围内。

## <a name="define-your-properties"></a>定义属性

属性是表示设备状态的数据字段。 使用属性来表示设备的持久状态，例如设备的开/关状态。 属性还可以表示基本设备属性，例如设备的软件版本。 请将属性声明为只读属性或可写属性。

以下屏幕截图显示了 Azure IoT Central 应用程序中的属性定义。

:::image type="content" source="media/howto-use-properties/property-definition.png" alt-text="屏幕截图，显示了 Azure IoT Central 应用程序中的属性定义。":::

下表显示了属性功能的配置设置。

| 字段           | 说明                                                                                                                                                                                                                        |
|-----------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 显示名称    | “仪表板磁贴”和“设备”窗体上使用的属性值的显示名称。                                                                                                                                                              |
| 名称            | 属性的名称。 Azure IoT Central 会根据显示名称生成此字段的值，但你可以视需要选择自己的值。 此字段必须为字母数字。  设备代码使用此“名称”值。           |
| 功能类型 | 属性。                                                                                                                                                                                                                          |
| 语义类型   | 属性的语义类型，如温度、状态或事件。 选择的语义类型将决定以下哪些字段可用。                                                                       |
| 架构          | 属性数据类型，如 double、string 或 vector。 可用的选项取决于语义类型。 架构不可用于事件和状态语义类型。                                               |
| 可写       | 如果属性不是可写的，则设备可向 Azure IoT Central 报告属性值。 如果属性是可写的，则设备可向 Azure IoT Central 报告属性值。 然后，Azure IoT Central 可以将属性更新发送到设备。 |
| 严重性        | 仅适用于事件语义类型。 严重性为“错误”、“信息”或“警告”。                                                                                                                         |
| 状态值    | 仅适用于状态语义类型。 定义可能的状态值，其中每个状态值都具有显示名称、名称、枚举类型和值。                                                                                   |
| 计价单位            | 属性值的单位，如“mph”、“%”或“&deg;C”  。                                                                                                                                                              |
| 显示单位    | “仪表板磁贴”和“设备”窗体上使用的显示单位。                                                                                                                                                                                    |
| 评论         | 有关属性功能的任何注释。                                                                                                                                                                                        |
| 说明     | 属性功能的说明。                                                                                                                                                                                          |

还可以在设备模板的接口中定义属性，如下所示：

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

* `@type` 指定功能类型：`Property`。 上一示例还显示了这两个属性的语义类型 `Temperature`。
* 属性的 `name`。
* `schema` 指定属性的数据类型。 此值可以是基元类型，例如 double、integer、Boolean 或 string。 还支持复杂的对象类型和映射。
* `writable` 默认情况下，属性是只读的。 你可以使用此字段将属性标记为可写。

可选字段（例如显示名称和说明）允许你向接口和功能添加更多详细信息。

创建属性时，你可以指定复杂架构类型，例如 Object 和 Enum。

![屏幕截图显示了如何添加功能。](./media/howto-use-properties/property.png)

当选择复杂架构（例如 Object）时，你还需要定义对象。

:::image type="content" source="media/howto-use-properties/object.png" alt-text="屏幕截图显示了如何定义对象":::

下面的代码显示了 Object 属性类型的定义。 此对象包含两个字段，其类型分别为 string 和 integer。

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

默认情况下，属性是只读的。 使用只读属性，设备可以将属性值更新报告给 Azure IoT Central 应用程序。 Azure IoT Central 应用程序无法设置只读属性的值。

Azure IoT Central 使用设备孪生在设备与 Azure IoT Central 应用程序之间同步属性值。 设备属性值使用设备孪生报告属性。 有关详细信息，请参阅[设备孪生](../../iot-hub/tutorial-device-twins.md)。

设备模型中的以下代码片段显示了一个只读属性类型的定义：

``` json
{
  "name": "model",
  "displayName": "Device model",
  "schema": "string",
  "comment": "Device model name or ID. Ex. Surface Book 2."
}
```

属性更新由设备以 JSON 有效负载形式发送。 有关详细信息，请参阅[有效负载](./concepts-telemetry-properties-commands.md)。

可以使用 Azure IoT 设备 SDK 将属性更新发送到 Azure IoT Central 应用程序。

通过使用以下函数，可以将设备孪生属性发送到 Azure IoT Central 应用程序：

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

为了简单起见，本文使用 Node.js。 有关其他语言示例，请参阅[创建客户端应用程序并将其连接到 Azure IoT Central 应用程序](tutorial-connect-device.md)教程。

Azure IoT Central 应用程序中的以下视图显示了你可以查看的属性。 该视图自动使“设备型号”属性成为只读设备属性。

:::image type="content" source="media/howto-use-properties/read-only.png" alt-text="屏幕截图显示了一个只读属性的视图":::

## <a name="implement-writable-properties"></a>实现可写属性

可写属性由操作员在 Azure IoT Central 应用程序中的窗体上设置。 Azure IoT Central 将属性发送到设备。 Azure IoT Central 要求设备进行确认。

设备模型中的以下代码片段显示了一个可写属性类型的定义：

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

若要定义和处理设备会对其进行响应的可写属性，可以使用以下代码：

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

响应消息应当包含 `ac` 和 `av` 字段。 `ad` 字段为可选。 有关示例，请参阅以下代码片段：

* `ac` 是一个数值字段，它使用下表中的值。
* `av` 是发送到设备的版本号。
* `ad` 是一个选项字符串说明。

| 值 | Label | 说明 |
| ----- | ----- | ----------- |
| `'ac': 200` | 已完成 | 属性更改操作已成功完成。 |
| `'ac': 202` 或 `'ac': 201` | 挂起的 | 属性更改操作已挂起或正在进行。 |
| `'ac': 4xx` | 错误 | 所请求的属性更改无效或有错误。 |
| `'ac': 5xx` | 错误 | 设备在处理所请求的更改时遇到意外错误。 |

有关设备孪生的详细信息，请参阅[从后端服务配置设备](../../iot-hub/tutorial-device-twins.md)。

当操作员在 Azure IoT Central 应用程序中设置可写属性时，该应用程序会使用设备孪生所需属性将值发送到设备。 然后，设备使用设备孪生报告的属性做出响应。 Azure IoT Central 在收到报告的属性值时会将属性视图的状态更新为“已接受”。

以下视图显示了可写属性。 当你输入值并选择“保存”后，初始状态为“挂起”。 当设备接受更改后，状态将更改为“已接受”。

![屏幕截图显示了“挂起”状态。](./media/howto-use-properties/status-pending.png)

![屏幕截图显示了“已接受”属性。](./media/howto-use-properties/accepted.png)

## <a name="next-steps"></a>后续步骤

现在，你已了解如何使用 Azure IoT Central 应用程序中的属性，请参阅：

* [Payloads](concepts-telemetry-properties-commands.md)
* [创建客户端应用程序并将其连接到 Azure IoT Central 应用程序](tutorial-connect-device.md)