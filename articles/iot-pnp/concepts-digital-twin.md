---
title: 了解 IoT 即插即用数字孪生
description: 了解 IoT 即插即用如何使用数字孪生
author: prashmo
ms.author: prashmo
ms.date: 12/14/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 99c957e5bf6ffe69c94e109796590f5ab975c3cf
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/17/2020
ms.locfileid: "97656880"
---
# <a name="understand-iot-plug-and-play-digital-twins"></a>了解 IoT 即插即用数字孪生

IoT 即插即用设备实现 [数字孪生定义语言 (DTDL) ](https://github.com/Azure/opendigitaltwins-dtdl) 架构描述的模型。 模型描述特定设备可以具有的组件、属性、命令和遥测消息集。

IoT 即插即用使用 DTDL 版本2。 有关此版本的详细信息，请参阅 GitHub 上的 [数字孪生定义语言 (DTDL) 版本 2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) 规范。

> [!NOTE]
> DTDL 不适用于 IoT 即插即用。 其他 IoT 服务（如 [Azure 数字孪生](../digital-twins/overview.md)）使用它来表示大楼和能源网络等整个环境。

Azure IoT 服务 Sdk 包括可让服务与设备的数字克隆交互的 Api。 例如，服务可以从数字克隆读取设备属性，或使用数字克隆来调用设备上的命令。 若要了解详细信息，请参阅 [IoT 中心数字输出示例](concepts-developer-guide-service.md#iot-hub-digital-twin-examples)。

本文中的示例 IoT 即插即用设备实现了具有[恒温器](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json)组件的[温度控制器型号](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json)。

## <a name="device-twins-and-digital-twins"></a>设备孪生和数字孪生

此外，Azure IoT 中心还会为每个连接的设备保留一个 *设备* 克隆。 设备克隆类似于数字克隆，因为它是设备属性的表示形式。 Azure IoT 服务 Sdk 包括用于与设备孪生进行交互的 Api。

Iot 中心在 IoT 即插即用设备首次连接时，初始化数字克隆和设备克隆。

设备孪生是存储设备状态信息（包括元数据、配置和条件）的 JSON 文档。 若要了解详细信息，请参阅 [IoT 中心服务客户端示例](concepts-developer-guide-service.md#iot-hub-service-client-examples)。 设备和解决方案构建者都可以继续使用同一组设备克隆 Api 和 Sdk 来使用 IoT 即插即用约定来实现设备和解决方案。

数字硬克隆 Api 在组件、属性和命令等高级 DTDL 结构上运行。 数字克隆 Api 使解决方案构建者可以更轻松地创建 IoT 即插即用解决方案。

在设备克隆中，可写属性的状态将拆分到 *所需的属性* 和 *报告的属性* 部分。 所有只读属性都可以在报告的属性部分中使用。

在数字克隆中，有一个统一的属性视图。 给定属性的同步状态存储在相应的默认 "组件" `$metadata` 部分中。

### <a name="device-twin-json-example"></a>设备克隆 JSON 示例

以下代码片段显示了一个 IoT 即插即用设备克隆，格式为 JSON 对象：

```json
{
  "deviceId": "sample-device",
  "modelId": "dtmi:com:example:TemperatureController;1",
  "version": 15,
  "properties": {
    "desired": {
      "thermostat1": {
        "__t": "c",
        "targetTemperature": 21.8
      },
      "$metadata": {...},
      "$version": 4
    },
    "reported": {
      "serialNumber": "alwinexlepaho8329",
      "thermostat1": {
        "maxTempSinceLastReboot": 25.3,
        "__t": "c",
        "targetTemperature": {
          "value": 21.8,
          "ac": 200,
          "ad": "Successfully executed patch",
        }
      },
      "$metadata": {...},
      "$version": 11
    }
  }
}
```

### <a name="digital-twin-example"></a>数字输出示例

以下代码片段显示了格式设置为 JSON 对象的数字克隆：

```json
{
  "$dtId": "sample-device",
  "serialNumber": "alwinexlepaho8329",
  "thermostat1": {
    "maxTempSinceLastReboot": 25.3,
    "targetTemperature": 21.8,
    "$metadata": {
      "targetTemperature": {
        "desiredValue": 21.8,
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

下表描述了数字双子 JSON 对象中的字段：

| 字段名称 | 说明 |
| --- | --- |
| `$dtId` | 一个用户提供的字符串，表示设备数字克隆的 ID |
| `{propertyName}` | JSON 中属性的值 |
| `$metadata.$model` | 可有可无表示此数字输出的模型接口 ID |
| `$metadata.{propertyName}.desiredValue` | [仅用于可写属性]指定属性的所需值。 |
| `$metadata.{propertyName}.desiredVersion` | [仅用于可写属性]IoT 中心维护的所需值的版本|
| `$metadata.{propertyName}.ackVersion` | [仅用于可写属性的必需]由实现数字克隆的设备确认的版本，它必须大于或等于所需版本 |
| `$metadata.{propertyName}.ackCode` | [仅用于可写属性的必需] `ack` 实现数字克隆的设备应用返回的代码 |
| `$metadata.{propertyName}.ackDescription` | [可选，仅用于可写属性] `ack` 实现数字克隆的设备应用返回的说明 |
| `$metadata.{propertyName}.lastUpdateTime` | IoT 中心维护设备最后一次更新的时间戳。 时间戳以 UTC 格式编码，并采用 ISO8601 格式 YYYY-MM-YYYY-MM-DDTHH： MM： SS. mmmZ |
| `{componentName}` | 包含组件的属性值和元数据的 JSON 对象。 |
| `{componentName}.{propertyName}` | JSON 中组件的属性的值 |
| `{componentName}.$metadata` | 组件的元数据信息。 |

### <a name="properties"></a>属性

属性是表示实体的状态的数据字段 (类似于许多面向对象的编程语言) 中的属性。

#### <a name="read-only-property"></a>只读属性

DTDL 架构：

```json
{
    "@type": "Property",
    "name": "serialNumber",
    "displayName": "Serial Number",
    "description": "Serial number of the device.",
    "schema": "string"
}
```

在此示例中， `alwinexlepaho8329` 是 `serialNumber` 设备报告的只读属性的当前值。
以下代码片段显示属性的并行 JSON 表示形式 `serialNumber` ：

:::row:::
   :::column span="":::
      **设备孪生**

```json
"properties": {
  "reported": {
    "serialNumber": "alwinexlepaho8329"
  }
}
```

   :::column-end:::
   :::column span="":::
      **数字孪生体**

```json
"serialNumber": "alwinexlepaho8329"
```

   :::column-end:::
:::row-end:::

#### <a name="writable-property"></a>可写属性

下面的示例显示了默认组件中的可写属性。

DTDL:

```json
{
  "@type": "Property",
  "name": "fanSpeed",
  "displayName": "Fan Speed",
  "writable": true,
  "schema": "double"
}
```

:::row:::
   :::column span="":::
      **设备孪生**

```json
{
  "properties": {
    "desired": {
      "fanSpeed": 2.0,
    },
    "reported": {
      "fanSpeed": {
        "value": 3.0,
        "ac": 200,
        "av": 1,
        "ad": "Successfully executed patch version 1"
      }
    }
  },
}
```

   :::column-end:::
   :::column span="":::
      **数字孪生体**

```json
{
  "fanSpeed": 3.0,
  "$metadata": {
    "fanSpeed": {
      "desiredValue": 2.0,
      "desiredVersion": 2,
      "ackVersion": 1,
      "ackCode": 200,
      "ackDescription": "Successfully executed patch version 1",
      "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
    }
  }
}
```

   :::column-end:::
:::row-end:::

在此示例中， `3.0` 是设备报告的属性的当前值 `fanSpeed` 。 `2.0` 是解决方案设置的所需值。 根级别属性的所需值和同步状态在数字克隆的根级别内设置 `$metadata` 。 设备联机后，可以应用此更新，并向后报告更新的值。

### <a name="components"></a>组件

组件允许将模型接口构建为其他接口的程序集。
例如，可以将 [恒温器](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) 接口合并为组件，并将其合并 `thermostat1`  `thermostat2` 到 [温度控制器模型](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) 模型中。

在设备克隆中，组件由 `{ "__t": "c"}` 标记标识。 在数字克隆中，出现 `$metadata` 标记组件。

在此示例中， `thermostat1` 是一个具有两个属性的组件：

- `maxTempSinceLastReboot` 是一个只读属性。
- `targetTemperature` 设备已成功同步的可写属性。 这些属性的所需值和同步状态在组件的中 `$metadata` 。

以下代码片段显示了组件的并行 JSON 表示形式 `thermostat1` ：

:::row:::
   :::column span="":::
      **设备孪生**

```json
"properties": {
  "desired": {
    "thermostat1": {
      "__t": "c",
      "targetTemperature": 21.8
    },
    "$metadata": {
    },
    "$version": 4
  },
  "reported": {
    "thermostat1": {
      "maxTempSinceLastReboot": 25.3,
      "__t": "c",
      "targetTemperature": {
        "value": 21.8,
        "ac": 200,
        "ad": "Successfully executed patch",
        "av": 4
      }
    },
    "$metadata": {
    },
    "$version": 11
  }
}
```

   :::column-end:::
   :::column span="":::
      **数字孪生体**

```json
"thermostat1": {
  "maxTempSinceLastReboot": 25.3,
  "targetTemperature": 21.8,
  "$metadata": {
    "targetTemperature": {
      "desiredValue": 21.8,
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
}
```

   :::column-end:::
:::row-end:::

## <a name="digital-twin-apis"></a>数字克隆 Api

数字克隆 Api 包括 " **获取数字** 克隆"、" **更新数字** 克隆"、" **调用组件" 命令** 和 **调用命令** 操作来更多地管理数字克隆。 你可以直接使用 [REST api](/rest/api/iothub/service/digitaltwin) ，也可以通过 [服务 SDK](../iot-pnp/libraries-sdks.md)使用。

## <a name="digital-twin-change-events"></a>数字孪生体更改事件

启用数字孪生更改事件后，只要组件或属性的当前值或所需值发生更改，就会触发事件。 数字克隆更改事件以 [JSON 修补程序](http://jsonpatch.com/) 格式生成。 如果启用了克隆更改事件，则会以设备克隆格式生成相应的事件。

若要了解如何启用设备和数字克隆事件的路由，请参阅 [使用 IoT 中心消息路由将设备到云的消息发送到不同的终结点](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events)。 若要了解消息格式，请参阅 [创建和读取 IoT 中心消息](../iot-hub/iot-hub-devguide-messages-construct.md)。

例如，当由解决方案设置时，将触发以下数字输出更改事件 `targetTemperature` ：

```json
iothub-connection-device-id:sample-device
iothub-enqueuedtime:7/17/2020 6:11:04 AM
iothub-message-source:digitalTwinChangeEvents
correlation-id:275d463fa034
content-type:application/json-patch+json
content-encoding:utf-8
[
  {
    "op": "add",
    "path": "/thermostat1/$metadata/targetTemperature",
    "value": {
      "desiredValue": 21.8,
      "desiredVersion": 4
    }
  }
]
```

当设备报告以上所需更改已应用时，将触发以下数字输出更改事件：

```json
iothub-connection-device-id:sample-device
iothub-enqueuedtime:7/17/2020 6:11:05 AM
iothub-message-source:digitalTwinChangeEvents
correlation-id:275d464a2c80
content-type:application/json-patch+json
content-encoding:utf-8
[
  {
    "op": "add",
    "path": "/thermostat1/$metadata/targetTemperature/ackCode",
    "value": 200
  },
  {
    "op": "add",
    "path": "/thermostat1/$metadata/targetTemperature/ackDescription",
    "value": "Successfully executed patch"
  },
  {
    "op": "add",
    "path": "/thermostat1/$metadata/targetTemperature/ackVersion",
    "value": 4
  },
  {
    "op": "add",
    "path": "/thermostat1/$metadata/targetTemperature/lastUpdateTime",
    "value": "2020-07-17T06:11:04.9309159Z"
  },
  {
    "op": "add",
    "path": "/thermostat1/targetTemperature",
    "value": 21.8
  }
]
```

> [!NOTE]
> 同时启用设备和数字克隆更改通知时，会将克隆更改通知消息加倍。

## <a name="next-steps"></a>后续步骤

现在，你已了解数字孪生，下面是一些其他资源：

- [如何使用 IoT 即插即用数字克隆 Api](howto-manage-digital-twin.md)
- [与解决方案中的设备交互](quickstart-service.md)
- [IoT 数字克隆 REST API](/rest/api/iothub/service/digitaltwin)
- [Azure IoT 资源管理器](howto-use-iot-explorer.md)