---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/24/2020
ms.openlocfilehash: 4b19ae1b584a2d300e4144e79ef76245c71035cf
ms.sourcegitcommit: b8a175b6391cddd5a2c92575c311cc3e8c820018
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "96126080"
---
## <a name="prerequisites"></a>先决条件

若要完成本文中的步骤，需要以下各项：

* 使用“自定义应用程序”模板创建的 Azure IoT Central 应用程序。 有关详细信息，请参阅[创建应用程序快速入门](../articles/iot-central/core/quick-deploy-iot-central.md)。 应用程序必须在 2020 年 7 月 14 日或之后创建。
* 安装了 [Node.js](https://nodejs.org/) 6 或更高版本的开发计算机。 若要检查版本，可以在命令行中运行 `node --version`。 本教程中的说明假设在 Windows 命令提示符下运行 **node** 命令。 但是，可以在许多其他的操作系统上使用 Node.js。
* [适用于 Node.js 的 Microsoft Azure IoT SDK](https://github.com/Azure/azure-iot-sdk-node) GitHub 存储库（包含示例代码）的本地副本。 使用此链接下载存储库副本：[下载 ZIP](https://github.com/Azure/azure-iot-sdk-node/archive/master.zip)。 然后将该文件解压缩到本地计算机上的适当位置。

## <a name="review-the-code"></a>查看代码

在先前下载的适用于 Node.js 的 Microsoft Azure IoT SDK 的副本中，在文本编辑器中打开 azure-iot-sdk-node/device/samples/pnp/simple_thermostat.js 文件。

当你运行连接到 IoT Central 的示例时，它将使用设备预配服务 (DPS) 来注册设备并生成连接字符串。 该示例从命令行环境中检索其所需的 DPS 连接信息。

`main` 方法：

* 在打开连接之前，创建 `client` 对象并设置 `dtmi:com:example:Thermostat;1` 模型 ID。
* 创建命令处理程序。
* 启动循环，每 10 秒发送一次温度遥测。
* 将 `maxTempSinceLastReboot` 属性发送到 IoT Central。 IoT Central 会忽略 `serialNumber` 属性，因为它不是设备模型的一部分。
* 创建可写属性处理程序。

```javascript
async function main() {

  // ...

  // fromConnectionString must specify a transport, coming from any transport package.
  const client = Client.fromConnectionString(deviceConnectionString, Protocol);

  let resultTwin;
  try {
    // Add the modelId here
    await client.setOptions(modelIdObject);
    await client.open();

    client.onDeviceMethod(commandMaxMinReport, commandHandler);

    // Send Telemetry every 10 secs
    let index = 0;
    intervalToken = setInterval(() => {
      sendTelemetry(client, index).catch((err) => console.log('error', err.toString()));
      index += 1;
    }, telemetrySendInterval);

    // attach a standard input exit listener
    attachExitHandler(client);

    // Deal with twin
    try {
      resultTwin = await client.getTwin();
      const patchRoot = createReportPropPatch({ serialNumber: deviceSerialNum });
      const patchThermostat = createReportPropPatch({
        maxTempSinceLastReboot: deviceTemperatureSensor.getMaxTemperatureValue()
      });

      // the below things can only happen once the twin is there
      updateComponentReportedProperties(resultTwin, patchRoot);
      updateComponentReportedProperties(resultTwin, patchThermostat);

      // Setup the handler for desired properties
      desiredPropertyPatchHandler(resultTwin);

    } catch (err) {
      console.error('could not retrieve twin or report twin properties\n' + err.toString());
    }
  } catch (err) {
    console.error('could not connect Plug and Play client or could not attach interval function for telemetry\n' + err.toString());
  }
}
```

`provisionDevice` 函数显示设备如何使用 DPS 注册并连接到 IoT Central。 有效负载包括模型 ID：

```javascript
async function provisionDevice(payload) {
  var provSecurityClient = new SymmetricKeySecurityClient(registrationId, symmetricKey);
  var provisioningClient = ProvisioningDeviceClient.create(provisioningHost, idScope, new ProvProtocol(), provSecurityClient);

  if (!!(payload)) {
    provisioningClient.setProvisioningPayload(payload);
  }

  try {
    let result = await provisioningClient.register();
    deviceConnectionString = 'HostName=' + result.assignedHub + ';DeviceId=' + result.deviceId + ';SharedAccessKey=' + symmetricKey;
  } catch (err) {
    console.error("error registering device: " + err.toString());
  }
}
```

`sendTelemetry` 函数显示设备如何将温度遥测发送到 IoT Central。 `getCurrentTemperatureObject` 方法返回类似于 `{ temperature: 45.6 }` 的对象：

```javascript
async function sendTelemetry(deviceClient, index) {
  console.log('Sending telemetry message %d...', index);
  const msg = new Message(
    JSON.stringify(
      deviceTemperatureSensor.updateSensor().getCurrentTemperatureObject()
    )
  );
  msg.contentType = 'application/json';
  msg.contentEncoding = 'utf-8';
  await deviceClient.sendEvent(msg);
}
```

`main` 方法使用以下两种方法将 `maxTempSinceLastReboot` 属性发送到 IoT Central。 `main` 方法使用类似于 `{maxTempSinceLastReboot: 80.9}` 的对象调用 `createReportPropPatch`：

```javascript
const createReportPropPatch = (propertiesToReport) => {
  let patch;
  patch = { };
  patch = propertiesToReport;
  return patch;
};

const updateComponentReportedProperties = (deviceTwin, patch) => {
  deviceTwin.properties.reported.update(patch, function (err) {
    if (err) throw err;
    console.log('Properties have been reported for component');
  });
};
```

`main` 方法使用以下两种方法来处理对 IoT Central 中目标温度可写属性的更新。 注意 `propertyUpdateHandle` 如何生成包含版本和状态代码的响应：

```javascript
const desiredPropertyPatchHandler = (deviceTwin) => {
  deviceTwin.on('properties.desired', (delta) => {
    const versionProperty = delta.$version;

    Object.entries(delta).forEach(([propertyName, propertyValue]) => {
      if (propertyName !== '$version') {
        propertyUpdateHandler(deviceTwin, propertyName, null, propertyValue, versionProperty);
      }
    });
  });
};

const propertyUpdateHandler = (deviceTwin, propertyName, reportedValue, desiredValue, version) => {
  console.log('Received an update for property: ' + propertyName + ' with value: ' + JSON.stringify(desiredValue));
  const patch = createReportPropPatch(
    { [propertyName]:
      {
        'value': desiredValue,
        'ac': 200,
        'ad': 'Successfully executed patch for ' + propertyName,
        'av': version
      }
    });
  updateComponentReportedProperties(deviceTwin, patch);
  console.log('updated the property');
};
```

`main` 方法使用以下两种方法来处理对 `getMaxMinReport` 命令的调用。 `getMaxMinReportObject` 方法将报告生成为 JSON 对象：

```javascript
const commandHandler = async (request, response) => {
  switch (request.methodName) {
  case commandMaxMinReport: {
    console.log('MaxMinReport ' + request.payload);
    await sendCommandResponse(request, response, 200, deviceTemperatureSensor.getMaxMinReportObject());
    break;
  }
  default:
    await sendCommandResponse(request, response, 404, 'unknown method');
    break;
  }
};

const sendCommandResponse = async (request, response, status, payload) => {
  try {
    await response.send(status, payload);
    console.log('Response to method \'' + request.methodName +
              '\' sent successfully.' );
  } catch (err) {
    console.error('An error ocurred when sending a method response:\n' +
              err.toString());
  }
};
```

## <a name="get-connection-information"></a>获取连接信息

[!INCLUDE [iot-central-connection-configuration](iot-central-connection-configuration.md)]

## <a name="run-the-code"></a>运行代码

若要运行示例应用程序，请打开命令行环境并导航到包含 simple_thermostat.js 示例文件的 azure-iot-sdk-node/device/samples/pnp 文件夹 。

[!INCLUDE [iot-central-connection-environment](iot-central-connection-environment.md)]

安装所需的包：

```cmd/sh
npm install
```

运行示例：

```cmd/sh
node simple_thermostat.js
```

以下输出显示设备注册并连接到 IoT Central。 然后，示例会先发送 `maxTempSinceLastReboot` 属性，然后开始发送遥测：

```output
registration succeeded
assigned hub=iotc-.......azure-devices.net
deviceId=sample-device-01
payload=undefined
Connecting using connection string HostName=iotc-........azure-devices.net;DeviceId=sample-device-01;SharedAccessKey=Ci....=
Enabling the commands on the client
Please enter q or Q to exit sample.
The following properties will be updated for root interface:
{ maxTempSinceLastReboot: 55.20309427428496 }
Properties have been reported for component
Sending telemetry message 0...
Sending telemetry message 1...
Sending telemetry message 2...
Sending telemetry message 3...
```

[!INCLUDE [iot-central-monitor-thermostat](iot-central-monitor-thermostat.md)]

可以看到设备如何对命令和属性更新做出响应：

```output
MaxMinReport 2020-10-15T12:00:00.000Z
Response to method 'getMaxMinReport' sent successfully.

...

Received an update for property: targetTemperature with value: {"value":86.3}
The following properties will be updated for root interface:
{
  targetTemperature: {
    value: { value: 86.3 },
    ac: 200,
    ad: 'Successfully executed patch for targetTemperature',
    av: 2
  }
}
```
