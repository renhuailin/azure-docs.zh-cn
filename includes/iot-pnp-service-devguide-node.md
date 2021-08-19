---
author: dominicbetts
ms.author: dobett
ms.service: iot-develop
ms.topic: include
ms.date: 10/20/2020
ms.openlocfilehash: 974dc0488e358581bc6c0ee1798c079026d75cca
ms.sourcegitcommit: 8669087bcbda39e3377296c54014ce7b58909746
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2021
ms.locfileid: "114405020"
---
还可以使用以下资源：

- [Node.js SDK 参考文档](/javascript/api/azure-iothub)
- [服务客户端示例](https://github.com/Azure/azure-iot-sdk-node/blob/master/service/samples/javascript/twin.js)
- [数字孪生示例](https://github.com/Azure/azure-iot-sdk-node/blob/master/service/samples/javascript/get_digital_twin.js)

## <a name="iot-hub-service-client-examples"></a>IoT 中心服务客户端示例

本部分介绍有关使用 IoT 中心服务客户端的 JavaScript 示例以及 Registry 类和 Client 类 。 使用 Registry 类通过设备孪生与设备状态交互。 还可以使用 Registry 类在 IoT 中心[查询设备注册](../articles/iot-hub/iot-hub-devguide-query-language.md)。 使用 Client 类在设备上调用命令。 设备的 [DTDL](../articles/iot-develop/concepts-digital-twin.md) 模型定义了设备实现的属性和命令。 在代码片段中，`deviceId` 变量保存注册到 IoT 中心的 IoT 即插即用设备的设备 ID。

### <a name="get-the-device-twin-and-model-id"></a>获取设备孪生和模型 ID

若要获取连接到 IoT 中心的 IoT 即插即用设备的设备孪生和模型 ID，请执行以下命令：

```javascript
var Registry = require('azure-iothub').Registry;

// ...

var registry = Registry.fromConnectionString(connectionString);
registry.getTwin(deviceId, function(err, twin) {
  if (err) {
    console.error(err.message);
  } else {
    console.log('Model Id: ' + twin.modelId);
    console.log(JSON.stringify(twin, null, 2));
  }
}
```

### <a name="update-device-twin"></a>更新设备孪生

下面的代码片段展示如何更新设备上的 `targetTemperature` 属性。 此示例演示为何需要以及如何在更新孪生之前获取孪生。 在设备的默认组件中定义该属性：

```javascript
var Registry = require('azure-iothub').Registry;
var registry = Registry.fromConnectionString(connectionString);

registry.getTwin(deviceId, function(err, twin) {
  if (err) {
    console.error(err.message);
  } else {
    var twinPatch = {
      properties: {
        desired: {
          targetTemperature: 42
        }
      }
    };
    twin.update(twinPatch, function(err, twin) {
      if (err) {
        console.error(err.message);
      } else {
        console.log(JSON.stringify(twin, null, 2));
      }
    }
  }
}
```

以下代码片段演示如何更新组件上的 `targetTemperature` 属性。 此示例演示为何需要以及如何在更新孪生之前获取孪生。 在 thermostat1 组件中定义该属性：

```javascript
var Registry = require('azure-iothub').Registry;
var registry = Registry.fromConnectionString(connectionString);

registry.getTwin(deviceId, function(err, twin) {
  if (err) {
    console.error(err.message);
  } else {
    var twinPatch = {
      properties: {
        desired: {
          thermostat1:
          {
            __t: "c",
            targetTemperature: 45
          }
        }
      }
    };
    twin.update(twinPatch, function(err, twin) {
      if (err) {
        console.error(err.message);
      } else {
        console.log(JSON.stringify(twin, null, 2));
      }
    }
  }
}
```

对于组件中的属性，属性补丁类似于以下示例：

```json
{
  "thermostat1":
  {
    "__t": "c",
    "targetTemperature": 20
  }
}
```

### <a name="call-command"></a>调用命令

以下代码片段演示如何调用默认组件中定义的 `getMaxMinReport` 命令：

```javascript
var Client = require('azure-iothub').Client;

// ...

var client = Client.fromConnectionString(connectionString);

var methodParams = {
  methodName: "getMaxMinReport",
  payload: new Date().getMinutes -2,
  responseTimeoutInSeconds: 15
};

client.invokeDeviceMethod(deviceId, methodParams, function (err, result) {
  if (err) {
    console.error('Failed to invoke method \'' + methodParams.methodName + '\': ' + err.message);
  } else {
    console.log(methodParams.methodName + ' on ' + deviceId + ':');
    console.log(JSON.stringify(result, null, 2));
  }
});
```

以下代码片段演示如何在组件上调用 `getMaxMinReport` 命令。 在 thermostat1 组件中定义该命令：

```javascript
var Client = require('azure-iothub').Client;

// ...

var client = Client.fromConnectionString(connectionString);

var methodParams = {
  methodName: "thermostat1*getMaxMinReport",
  payload: new Date().getMinutes -2,
  responseTimeoutInSeconds: 15
};

client.invokeDeviceMethod(deviceId, methodParams, function (err, result) {
  if (err) {
    console.error('Failed to invoke method \'' + methodParams.methodName + '\': ' + err.message);
  } else {
    console.log(methodParams.methodName + ' on ' + deviceId + ':');
    console.log(JSON.stringify(result, null, 2));
  }
});
```

## <a name="iot-hub-digital-twin-examples"></a>IoT 中心数字孪生体示例

使用 DigitalTwinClient 类通过数字孪生体与设备状态交互。 设备的 [DTDL](../articles/iot-develop/concepts-digital-twin.md) 模型定义了设备实现的属性和命令。

本部分演示有关使用数字孪生 API 的 JavaScript 示例。

`digitalTwinId` 变量保存注册到 IoT 中心的 IoT 即插即用设备的设备 ID。

### <a name="get-the-digital-twin-and-model-id"></a>获取数字孪生体和模型 ID

若要获取连接到 IoT 中心的 IoT 即插即用设备的数字孪生体和模型 ID，请执行以下命令：

```javascript
const IoTHubTokenCredentials = require('azure-iothub').IoTHubTokenCredentials;
const DigitalTwinClient = require('azure-iothub').DigitalTwinClient;
const { inspect } = require('util');

// ...

const credentials = new IoTHubTokenCredentials(connectionString);
const digitalTwinClient = new DigitalTwinClient(credentials);

const digitalTwin = await digitalTwinClient.getDigitalTwin(digitalTwinId);

console.log(inspect(digitalTwin));
console.log('Model Id: ' + inspect(digitalTwin.$metadata.$model));
```

### <a name="update-digital-twin"></a>更新数字孪生体

下面的代码片段展示如何更新设备上的 `targetTemperature` 属性。 在设备的默认组件中定义该属性：

```javascript
const IoTHubTokenCredentials = require('azure-iothub').IoTHubTokenCredentials;
const DigitalTwinClient = require('azure-iothub').DigitalTwinClient;

// ...

const credentials = new IoTHubTokenCredentials(connString);
const digitalTwinClient = new DigitalTwinClient(credentials);

const patch = [{
  op: 'add',
  path: '/targetTemperature',
  value: 42
}];
await digitalTwinClient.updateDigitalTwin(digitalTwinId, patch);
```

以下代码片段演示如何更新组件上的 `targetTemperature` 属性。 在 thermostat1 组件中定义该属性：

```javascript
const IoTHubTokenCredentials = require('azure-iothub').IoTHubTokenCredentials;
const DigitalTwinClient = require('azure-iothub').DigitalTwinClient;

// ...

const credentials = new IoTHubTokenCredentials(connString);
const digitalTwinClient = new DigitalTwinClient(credentials);

const patch = [{
  op: 'add',
  path: '/thermostat1/targetTemperature',
  value: 42
}];
await digitalTwinClient.updateDigitalTwin(digitalTwinId, patch);
```

### <a name="call-command"></a>调用命令

以下代码片段演示如何调用默认组件中定义的 `getMaxMinReport` 命令：

```javascript
const IoTHubTokenCredentials = require('azure-iothub').IoTHubTokenCredentials;
const DigitalTwinClient = require('azure-iothub').DigitalTwinClient;
const { inspect } = require('util');

// ...

const commandPayload = new Date().getMinutes -2;

const credentials = new IoTHubTokenCredentials(connectionString);
const digitalTwinClient = new DigitalTwinClient(credentials);

const options = {
  connectTimeoutInSeconds: 30,
  responseTimeoutInSeconds: 40
};
const commandResponse = await digitalTwinClient.invokeCommand(digitalTwinId, "getMaxMinReport", commandPayload, options);

console.log(inspect(commandResponse));
```

以下代码片段演示如何在组件上调用 `getMaxMinReport` 命令。 在 thermostat1 组件中定义该命令：

```javascript
const IoTHubTokenCredentials = require('azure-iothub').IoTHubTokenCredentials;
const DigitalTwinClient = require('azure-iothub').DigitalTwinClient;
const { inspect } = require('util');

// ...

const commandPayload = new Date().getMinutes -2;

const credentials = new IoTHubTokenCredentials(connectionString);
const digitalTwinClient = new DigitalTwinClient(credentials);

const options = {
  connectTimeoutInSeconds: 30,
  responseTimeoutInSeconds: 40
};
const commandResponse = await digitalTwinClient.invokeComponentCommand(digitalTwinId, "thermostat1", "getMaxMinReport", commandPayload, options);

console.log(inspect(commandResponse));
```

## <a name="read-device-telemetry"></a>读取设备遥测数据

IoT 即插即用设备将 DTDL 模型中定义的遥测数据发送到 IoT 中心。 默认情况下，IoT 中心会将遥测数据路由到一个可供你在其中使用遥测数据的事件中心终结点。 若要了解详细信息，请参阅[使用 IoT 中心消息路由将设备到云的消息发送到不同的终结点](../articles/iot-hub/iot-hub-devguide-messages-d2c.md)。

下面的代码片段演示如何从默认事件中心终结点读取遥测数据。 此代码片段中的代码取自 IoT 中心快速入门：[将设备的遥测数据发送到 IoT 中心并使用后端应用程序进行读取](../articles/iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-nodejs)：

```javascript
const { EventHubConsumerClient } = require("@azure/event-hubs");

var printError = function (err) {
  console.log(err.message);
};

var printMessages = function (messages) {
  for (const message of messages) {
    console.log("Telemetry received: ");
    console.log(JSON.stringify(message.body));
    console.log("Properties (set by device): ");
    console.log(JSON.stringify(message.properties));
    console.log("System properties (set by IoT Hub): ");
    console.log(JSON.stringify(message.systemProperties));
    console.log("");
  }
};

// ...

const clientOptions = {};

const consumerClient = new EventHubConsumerClient("$Default", connectionString, clientOptions);

consumerClient.subscribe({
  processEvents: printMessages,
  processError: printError,
});
```

前面代码的以下输出显示了由多组件 **TemperatureController** IoT 即插即用设备发送的温度遥测数据。 `dt-subject` 系统属性显示发送了遥测数据的组件的名称。 此示例中的两个组件是 DTDL 模型中定义的 `thermostat1` 和 `thermostat2`。 `dt-dataschema` 系统属性显示模型 ID：

```cmd/sh
Telemetry received:
{"temperature":68.77370855171125}
Properties (set by device):
undefined
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-connection-auth-method":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","iothub-connection-auth-generation-id":"637388034455888246","iothub-enqueuedtime":1603206669320,"iothub-message-source":"Telemetry","dt-subject":"thermostat1","dt-dataschema":"dtmi:com:example:TemperatureController;1","contentType":"application/json","contentEncoding":"utf-8"}

Telemetry received:
{"temperature":30.833394506549226}
Properties (set by device):
undefined
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-connection-auth-method":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","iothub-connection-auth-generation-id":"637388034455888246","iothub-enqueuedtime":1603206665835,"iothub-message-source":"Telemetry","dt-subject":"thermostat2","dt-dataschema":"dtmi:com:example:TemperatureController;1","contentType":"application/json","contentEncoding":"utf-8"}
```

## <a name="read-device-twin-change-notifications"></a>读取设备孪生更改通知

可以配置 IoT 中心，以便生成要路由到支持的终结点的设备孪生更改通知。 要了解详细信息，请参阅[使用 IoT 中心消息路由将设备到云的消息发送到不同的终结点 > 非遥测事件](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events)。

当 IoT 中心为无组件恒温器设备生成设备孪生更改通知时，前面 JavaScript 代码片段中显示的代码将生成以下输出。 应用程序属性 `iothub-message-schema` 和 `opType` 提供有关更改通知类型的信息：

```cmd/sh
Telemetry received:
{"version":4,"properties":{"reported":{"maxTempSinceLastReboot":42.1415152639582,"$metadata":{"$lastUpdated":"2020-10-21T10:01:40.1281138Z","maxTempSinceLastReboot":{"$lastUpdated":"2020-10-21T10:01:40.1281138Z"}},"$version":3}}}
Properties (set by device):
{"hubName":"my-pnp-hub","deviceId":"my-pnp-device","operationTimestamp":"2020-10-21T10:01:40.1281138+00:00","iothub-message-schema":"twinChangeNotification","opType":"updateTwin"}
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-enqueuedtime":1603274500282,"iothub-message-source":"twinChangeEvents","userId":{"type":"Buffer","data":[109,121,45,112,110,112,45,104,117,98]},"correlationId":"11ed82d13f50","contentType":"application/json","contentEncoding":"utf-8"}
```

当 IoT 中心为包含组件的设备生成设备孪生更改通知时，前面 JavaScript 代码片段中显示的代码将生成以下输出。 此示例显示当具有恒温器组件的温度传感器设备生成通知时所生成的输出。 应用程序属性 `iothub-message-schema` 和 `opType` 提供有关更改通知类型的信息：

```cmd/sh
Telemetry received:
{"version":4,"properties":{"reported":{"thermostat1":{"maxTempSinceLastReboot":3.5592971602417913,"__t":"c"},"$metadata":{"$lastUpdated":"2020-10-21T10:07:51.8284866Z","thermostat1":{"$lastUpdated":"2020-10-21T10:07:51.8284866Z","maxTempSinceLastReboot":{"$lastUpdated":"2020-10-21T10:07:51.8284866Z"},"__t":{"$lastUpdated":"2020-10-21T10:07:51.8284866Z"}}},"$version":3}}}
Properties (set by device):
{"hubName":"my-pnp-hub","deviceId":"my-pnp-device","operationTimestamp":"2020-10-21T10:07:51.8284866+00:00","iothub-message-schema":"twinChangeNotification","opType":"updateTwin"}
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-enqueuedtime":1603274871951,"iothub-message-source":"twinChangeEvents","userId":{"type":"Buffer","data":[109,121,45,112,110,112,45,104,117,98]},"correlationId":"11ee605b195f","contentType":"application/json","contentEncoding":"utf-8"}
```

## <a name="read-digital-twin-change-notifications"></a>读取数字孪生体更改通知

可以配置 IoT 中心，以便生成要路由到支持的终结点的数字孪生体更改通知。 要了解详细信息，请参阅[使用 IoT 中心消息路由将设备到云的消息发送到不同的终结点 > 非遥测事件](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events)。

当 IoT 中心为无组件恒温器设备生成数字孪生更改通知时，前面 JavaScript 代码片段中显示的代码将生成以下输出。 应用程序属性 `iothub-message-schema` 和 `opType` 提供有关更改通知类型的信息：

```cmd/sh
Telemetry received:
[{"op":"add","path":"/$metadata/maxTempSinceLastReboot","value":{"lastUpdateTime":"2020-10-21T10:01:40.1281138Z"}},{"op":"add","path":"/maxTempSinceLastReboot","value":42.1415152639582}]
Properties (set by device):
{"hubName":"my-pnp-hub","deviceId":"my-pnp-device","operationTimestamp":"2020-10-21T10:01:40.1281138+00:00","iothub-message-schema":"digitalTwinChangeNotification","opType":"updateTwin"}
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-enqueuedtime":1603274500282,"iothub-message-source":"digitalTwinChangeEvents","userId":{"type":"Buffer","data":[109,121,45,112,110,112,45,104,117,98]},"correlationId":"11ed82d13f50","contentType":"application/json-patch+json","contentEncoding":"utf-8"}
```

当 IoT 中心为包含组件的设备生成数字孪生更改通知时，前面 JavaScript 代码片段中显示的代码将生成以下输出。 此示例显示当具有恒温器组件的温度传感器设备生成通知时所生成的输出。 应用程序属性 `iothub-message-schema` 和 `opType` 提供有关更改通知类型的信息：

```cmd/sh
Telemetry received:
[{"op":"add","path":"/thermostat1","value":{"$metadata":{"maxTempSinceLastReboot":{"lastUpdateTime":"2020-10-21T10:07:51.8284866Z"}},"maxTempSinceLastReboot":3.5592971602417913}}]
Properties (set by device):
{"hubName":"my-pnp-hub","deviceId":"my-pnp-device","operationTimestamp":"2020-10-21T10:07:51.8284866+00:00","iothub-message-schema":"digitalTwinChangeNotification","opType":"updateTwin"}
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-enqueuedtime":1603274871951,"iothub-message-source":"digitalTwinChangeEvents","userId":{"type":"Buffer","data":[109,121,45,112,110,112,45,104,117,98]},"correlationId":"11ee605b195f","contentType":"application/json-patch+json","contentEncoding":"utf-8"}
```