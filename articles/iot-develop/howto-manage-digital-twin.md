---
title: 如何管理 IoT 即插即用数字孪生
description: 如何使用数字孪生 API 管理 IoT 即插即用设备
author: prashmo
ms.author: prashmo
ms.date: 12/17/2020
ms.topic: how-to
ms.service: iot-develop
services: iot-develop
ms.openlocfilehash: 7e1a743331aaf0b75574ae3b605b67fad8211a15
ms.sourcegitcommit: 8669087bcbda39e3377296c54014ce7b58909746
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2021
ms.locfileid: "114406374"
---
# <a name="manage-iot-plug-and-play-digital-twins"></a>管理 IoT 即插即用数字孪生

IoT 即插即用支持“获取数字孪生”和“更新数字孪生”操作以管理数字孪生。 可以使用 [REST API](/rest/api/iothub/service/digitaltwin)，也可以使用[服务 SDK](libraries-sdks.md) 之一。

撰写本文时，数字孪生 API 版本为 `2020-09-30`。

## <a name="update-a-digital-twin"></a>更新数字孪生

IoT 即插即用设备实现[数字孪生定义语言 v2 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl) 所述的模型。 解决方案开发人员可以使用更新数字孪生 API 来更新组件的状态和数字孪生的属性。

本文中的示例 IoT 即插即用设备实现了具有[恒温器](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json)组件的[温度控制器模型](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json)。

以下代码片段显示了对格式化为 JSON 对象的“获取数字孪生”请求的响应。 若要了解有关数字孪生格式的详细信息，请参阅[了解 IoT 即插即用数字孪生](./concepts-digital-twin.md#digital-twin-example)：

```json
{
    "$dtId": "sample-device",
    "serialNumber": "alwinexlepaho8329",
    "thermostat1": {
        "maxTempSinceLastReboot": 25.3,
        "targetTemperature": 20.4,
        "$metadata": {
            "targetTemperature": {
                "desiredValue": 20.4,
                "desiredVersion": 4,
                "ackVersion": 4,
                "ackCode": 200,
                "ackDescription": "Successfully executed patch",
                "lastUpdateTime": "2020-07-17T06:11:04.9309159Z"
            },
            "maxTempSinceLastReboot": {
                "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
            }
        }
    },
    "$metadata": {
        "$model": "dtmi:com:example:TemperatureController;1",
        "serialNumber": {
            "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
        }
    }
}
```

数字孪生允许使用 [JSON 修补程序](http://jsonpatch.com/)来更新整个组件或属性。

例如，可以更新 `targetTemperature` 属性，如下所示：

```json
[
    {
        "op": "add",
        "path": "/thermostat1/targetTemperature",
        "value": 21.4
    }
]
```

上一个更新在相应的组件级别 `$metadata` 中设置属性的预期值，如以下代码片段所示。 IoT 中心将更新属性的预期版本：

```json
"thermostat1": {
    "targetTemperature": 20.4,
    "$metadata": {
        "targetTemperature": {
            "desiredValue": 21.4,
            "desiredVersion": 5,
            "ackVersion": 4,
            "ackCode": 200,
            "ackDescription": "Successfully executed patch",
            "lastUpdateTime": "2020-07-17T06:11:04.9309159Z"
        }
    }
}
```

### <a name="add-replace-or-remove-a-component"></a>添加、替换或删除组件

组件级别操作需要值中有一个空对象 `$metadata` 标记。

添加或替换组件操作将设置所提供的所有属性的预期值。 它还将清除更新未提供的任何可写属性的预期值。

删除组件将清除存在的所有可写属性的预期值。 设备最终会同步此删除操作，并停止报告各个属性。 然后，将从数字孪生中删除该组件。

下面的 JSON 修补程序示例演示如何添加、替换或删除组件：

```json
[
    {
        "op": "add",
        "path": "/thermostat1",
        "value": {
            "targetTemperature": 21.4,
            "anotherWritableProperty": 42,
            "$metadata": {}
        }
    },
    {
        "op": "replace",
        "path": "/thermostat1",
        "value": {
            "targetTemperature": 21.4,
            "$metadata": {}
        }
    },
    {
        "op": "remove",
        "path": "/thermostat2"
    }
]
```

### <a name="add-replace-or-remove-a-property"></a>添加、替换或删除属性

添加或替换操作将设置属性的预期值。 设备可同步状态，并报告值更新以及 `ack` 代码、版本和说明。

删除属性将清除属性的预期值（如果已设置）。 然后，设备可以停止报告此属性，并将其从组件中删除。 如果此属性是组件中的最后一个属性，则也会删除该组件。

以下 JSON 修补程序示例演示如何在组件中添加、替换或删除属性：

```json
[
    {
        "op": "add",
        "path": "/thermostat1/targetTemperature",
        "value": 21.4
    },
    {
        "op": "replace",
        "path": "/thermostat1/anotherWritableProperty",
        "value": 42
    },
    {
        "op": "remove",
        "path": "/thermostat2/targetTemperature",
    }
]
```

### <a name="rules-for-setting-the-desired-value-of-a-digital-twin-property"></a>数字孪生属性的预期值的设置规则

**名称**

组件或属性的名称必须是有效的 DTDL v2 名称。

允许的字符为 a-z、A-Z、0-9（不得作为第一个字符）以及下划线（不得作为第一个或最后一个字符）。

名称长度可以为 1-64 个字符。

**属性值**

该值必须是有效的 [DTDL V2 属性](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#property)。

所有基元类型都受支持。 复杂类型支持枚举、映射和对象。 若要了解详细信息，请参阅 [DTDL V2 架构](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#schemas)。

属性不支持数组或具有数组的任何复杂架构。

复杂对象支持的最大深度为五级。

复杂对象内的所有字段名称都应该是有效的 DTDL v2 名称。

所有映射键都应该是有效的 DTDL v2 名称。

## <a name="troubleshoot-update-digital-twin-api-errors"></a>更新数字孪生 API 错误故障排除

数字孪生 API 引发以下一般错误消息：

`ErrorCode:ArgumentInvalid;'{propertyName}' exists within the device twin and is not digital twin conformant property. Please refer to aka.ms/dtpatch to update this to be conformant.`

如果看到此错误，请确保遵循[数字孪生属性的预期值的设置规则](#rules-for-setting-the-desired-value-of-a-digital-twin-property)来更新修补程序

更新组件时，请确保设置[空对象 $metadata 标记](#add-replace-or-remove-a-component)。

如果设备报告的值不符合 [IoT 即插即用约定](./concepts-convention.md#writable-properties)，更新可能会失败。

## <a name="next-steps"></a>后续步骤

现在，你已了解数字孪生，以下是一些其他资源：

- [与解决方案中的设备交互](tutorial-service.md)
- [IoT 数字孪生 REST API](/rest/api/iothub/service/digitaltwin)
- [Azure IoT 资源管理器](../iot-fundamentals/howto-use-iot-explorer.md)
