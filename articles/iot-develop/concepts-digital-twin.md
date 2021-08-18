---
title: 了解 IoT 即插即用数字孪生
description: 了解 IoT 即插即用如何使用数字孪生体
author: prashmo
ms.author: prashmo
ms.date: 12/14/2020
ms.topic: conceptual
ms.service: iot-develop
services: iot-develop
ms.openlocfilehash: 5623192cd713f06be24ce6c3f78b91756868db86
ms.sourcegitcommit: 8669087bcbda39e3377296c54014ce7b58909746
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2021
ms.locfileid: "114406323"
---
# <a name="understand-iot-plug-and-play-digital-twins"></a>了解 IoT 即插即用数字孪生

IoT 即插即用设备实现[数字孪生体定义语言 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl) 架构描述的模型。 模型描述特定设备可能具有的组件、属性、命令和遥测消息集。

IoT 即插即用使用 DTDL 版本 2。 有关此版本的详细信息，请参阅 GitHub 上的[数字孪生定义体语言 (DTDL) - 版本 2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) 规范。

> [!NOTE]
> DTDL 并非 IoT 即插即用所独有。 其他 IoT 服务（例如 [Azure 数字孪生](../digital-twins/overview.md)）使用它来表示大楼、能源网络等整个环境。

Azure IoT 服务 SDK 包括可让服务与设备的数字孪生体进行交互的 API。 例如，服务可以从数字孪生体读取设备属性，或使用数字孪生体在设备上调用命令。 若要了解详细信息，请参阅 [IoT 中心数字孪生体示例](concepts-developer-guide-service.md#iot-hub-digital-twin-examples)。

本文中的示例 IoT 即插即用设备实现了具有[恒温器](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json)组件的[温度控制器模型](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json)。

## <a name="device-twins-and-digital-twins"></a>设备孪生和数字孪生体

除了数字孪生体之外，Azure IoT 中心还会为每个连接的设备维护一个设备孪生。 设备孪生类似于数字孪生体，因为它是设备属性的表示形式。 Azure IoT 服务 SDK 包括用于与设备孪生进行交互的 API。

在 IoT 即插即用设备首次连接时，IoT 中心会对数字孪生体和设备孪生进行初始化。

设备孪生是存储设备状态信息（包括元数据、配置和条件）的 JSON 文档。 若要了解详细信息，请参阅 [IoT 中心服务客户端示例](concepts-developer-guide-service.md#iot-hub-service-client-examples)。 设备和解决方案开发者都可以继续使用同一组设备孪生 API 和 SDK，以便使用 IoT 即插即用约定来实现设备和解决方案。

数字孪生体 API 在组件、属性、命令等高级 DTDL 构造上运行。 数字孪生体 API 使解决方案开发者可以更轻松地创建 IoT 即插即用解决方案。

在设备孪生中，可写属性的状态拆分到“所需的属性”和“报告的属性”部分中。 所有只读属性都显示在报告的属性部分中。

数字孪生体提供了一个统一视图，其中包含属性的当前状态和所需状态。 给定属性的同步状态存储在相应的默认组件 `$metadata` 部分。

### <a name="device-twin-json-example"></a>设备孪生 JSON 示例

以下代码片段显示了一个格式化为 JSON 对象的 IoT 即插即用设备孪生：

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

### <a name="digital-twin-example"></a>数字孪生体示例

以下代码片段显示了格式化为 JSON 对象的数字孪生体：

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

下表描述了数字孪生体 JSON 对象中的字段：

| 字段名称 | 说明 |
| --- | --- |
| `$dtId` | 用户提供的字符串，表示设备数字孪生体的 ID |
| `{propertyName}` | JSON 中的属性的值 |
| `$metadata.$model` | [可选] 将此数字孪生体特征化的模型接口的 ID |
| `$metadata.{propertyName}.desiredValue` | [仅适用于可写属性] 指定的属性的所需值 |
| `$metadata.{propertyName}.desiredVersion` | [仅适用于可写属性] IoT 中心维护的所需值的版本|
| `$metadata.{propertyName}.ackVersion` | [必需，仅适用于可写属性] 由实现数字孪生体的设备确认的版本，必须高于或等于所需版本 |
| `$metadata.{propertyName}.ackCode` | [必需，仅适用于可写属性] 实现数字孪生体的设备应用返回的 `ack` 代码 |
| `$metadata.{propertyName}.ackDescription` | [可选，仅适用于可写属性] 实现数字孪生体的设备应用返回的 `ack` 说明 |
| `$metadata.{propertyName}.lastUpdateTime` | IoT 中心维护设备上次更新属性的时间戳。 时间戳采用 UTC，以 ISO8601 格式 YYYY-MM-DDTHH:MM:SS.mmmZ 进行编码 |
| `{componentName}` | 一个包含组件的属性值和元数据的 JSON 对象。 |
| `{componentName}.{propertyName}` | JSON 中组件属性的值 |
| `{componentName}.$metadata` | 组件的元数据信息。 |

### <a name="properties"></a>属性

属性是表示实体状态的数据字段（类似于许多面向对象的编程语言中的属性）。

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

在此示例中，`alwinexlepaho8329` 是设备报告的 `serialNumber` 只读属性的当前值。
以下代码片段显示了 `serialNumber` 属性的并行 JSON 表示形式：

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

DTDL：

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

在此示例中，`3.0` 是设备报告的 `fanSpeed` 属性的当前值。 `2.0` 是解决方案设置的所需值。 根级别属性的所需值和同步状态是在数字孪生体的根级别 `$metadata` 内设置的。 当设备联机后，它可以应用此更新，并报告更新后的值。

### <a name="components"></a>组件

使用组件，可以将模型接口构建为其他接口的程序集。
例如，可以在[温度控制器](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json)模型中将[恒温器](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json)接口合并为组件 `thermostat1` 和 `thermostat2`。

在设备孪生中，组件通过 `{ "__t": "c"}` 标记进行标识。 在数字孪生体中，`$metadata` 用来标记组件。

在此示例中，`thermostat1` 是一个具有两个属性的组件：

- `maxTempSinceLastReboot` 是一个只读属性。
- `targetTemperature` 是已被设备成功同步的可写属性。 这些属性的所需值和同步状态在组件的 `$metadata` 中。

以下代码片段显示了 `thermostat1` 组件的并行 JSON 表示形式：

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

## <a name="digital-twin-apis"></a>数字孪生体 API

数字孪生体 API 包括“获取数字孪生体”、“更新数字孪生体”、“调用组件命令”和“调用命令”等用于管理数字孪生体的操作。 你可以直接使用 [REST API](/rest/api/iothub/service/digitaltwin)，也可以通过[服务 SDK](../iot-develop/libraries-sdks.md) 使用它。

## <a name="digital-twin-change-events"></a>数字孪生体更改事件

启用数字孪生更改事件后，只要组件或属性的当前值或所需值发生更改，就会触发事件。 数字孪生体更改事件是以 [JSON 补丁](http://jsonpatch.com/)格式生成的。 如果启用了孪生体更改事件，则会以设备孪生格式生成相应的事件。

若要了解如何为设备和数字孪生体事件启用路由，请参阅[使用 IoT 中心消息路由将设备到云消息发送到不同的终结点](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events)。 若要了解消息格式，请参阅[创建和读取 IoT 中心消息](../iot-hub/iot-hub-devguide-messages-construct.md)。

例如，当解决方案设置 `targetTemperature` 时，将触发以下数字孪生体更改事件：

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

当设备报告上述所需更改已得到应用时，会触发以下数字孪生体更改事件：

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
> 同时在设备和数字孪生体更改通知中进行启用时，孪生体更改通知消息数会翻倍。

## <a name="next-steps"></a>后续步骤

现在，你已了解数字孪生体，可以继续学习下面的其他资源了：

- [如何使用 IoT 即插即用数字孪生体 API](howto-manage-digital-twin.md)
- [与解决方案中的设备交互](tutorial-service.md)
- [IoT 数字孪生 REST API](/rest/api/iothub/service/digitaltwin)
- [Azure IoT 资源管理器](../iot-fundamentals/howto-use-iot-explorer.md)