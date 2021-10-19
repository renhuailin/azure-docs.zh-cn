---
author: dominicbetts
ms.author: dobett
ms.service: iot-develop
ms.topic: include
ms.date: 03/31/2021
ms.openlocfilehash: ba19a222f3485fa15bd754f130a9b72c82334a00
ms.sourcegitcommit: ee5d9cdaf691f578f2e390101bf5350859d85c67
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2021
ms.locfileid: "129740861"
---
[![浏览代码](../articles/iot-central/core/media/common/browse-code.svg)](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples/javascript/pnp)

## <a name="prerequisites"></a>先决条件

若要完成本文中的步骤，需要准备好以下资源：

[!INCLUDE [iot-central-prerequisites-basic](iot-central-prerequisites-basic.md)]

- 安装了 [Node.js](https://nodejs.org/) 6 或更高版本的开发计算机。 若要检查版本，可以在命令行中运行 `node --version`。 本教程中的说明假设在 Windows 命令提示符下运行 **node** 命令。 但是，可以在许多其他的操作系统上使用 Node.js。

- [适用于 Node.js 的 Microsoft Azure IoT SDK](https://github.com/Azure/azure-iot-sdk-node) GitHub 存储库（包含示例代码）的本地副本。 使用此链接下载存储库副本：[下载 ZIP](https://github.com/Azure/azure-iot-sdk-node/archive/master.zip)。 然后将该文件解压缩到本地计算机上的适当位置。

## <a name="review-the-code"></a>查看代码

在之前下载的 Microsoft Azure IoT SDK for Node.js 的副本中，使用文本编辑器打开 azure-iot-sdk-node/device/samples/javascript/pnp/pnpTemperatureController.js 文件。

当你运行连接到 IoT Central 的示例时，它将使用设备预配服务 (DPS) 来注册设备并生成连接字符串。 该示例从命令行环境中检索其所需的 DPS 连接信息。

`main` 方法：

* 在打开连接之前，创建 `client` 对象并设置 `dtmi:com:example:TemperatureController;2` 模型 ID。 IoT Central 使用模型 ID 识别或生成此设备的设备模板。 若要了解详细信息，请参阅[将设备与设备模板关联](../articles/iot-central/core/concepts-get-connected.md#associate-a-device-with-a-device-template)。
* 为三个命令创建命令处理程序。
* 为每个恒温器组件启动一个循环，每隔 5 秒发送一次温度遥测数据。
* 启动默认组件的循环，每隔 6 秒发送一次工作集大小遥测数据。
* 为每个恒温器组件发送 `maxTempSinceLastReboot` 属性。
* 发送设备信息属性。
* 为三个组件创建可写属性处理程序。

```javascript
async function main() {
  // ...

  // fromConnectionString must specify a transport, coming from any transport package.
  const client = Client.fromConnectionString(deviceConnectionString, Protocol);
  console.log('Connecting using connection string: ' + deviceConnectionString);
  let resultTwin;

  try {
    // Add the modelId here
    await client.setOptions(modelIdObject);
    await client.open();
    console.log('Enabling the commands on the client');
    client.onDeviceMethod(commandNameGetMaxMinReport1, commandHandler);
    client.onDeviceMethod(commandNameGetMaxMinReport2, commandHandler);
    client.onDeviceMethod(commandNameReboot, commandHandler);

    // Send Telemetry after some interval
    let index1 = 0;
    let index2 = 0;
    let index3 = 0;
    intervalToken1 = setInterval(() => {
      const data = JSON.stringify(thermostat1.updateSensor().getCurrentTemperatureObject());
      sendTelemetry(client, data, index1, thermostat1ComponentName).catch((err) => console.log('error ', err.toString()));
      index1 += 1;
    }, 5000);

    intervalToken2 = setInterval(() => {
      const data = JSON.stringify(thermostat2.updateSensor().getCurrentTemperatureObject());
      sendTelemetry(client, data, index2, thermostat2ComponentName).catch((err) => console.log('error ', err.toString()));
      index2 += 1;
    }, 5500);


    intervalToken3 = setInterval(() => {
      const data = JSON.stringify({ workingset: 1 + (Math.random() * 90) });
      sendTelemetry(client, data, index3, null).catch((err) => console.log('error ', err.toString()));
      index3 += 1;
    }, 6000);

    // attach a standard input exit listener
    exitListener(client);

    try {
      resultTwin = await client.getTwin();
      // Only report readable properties
      const patchRoot = helperCreateReportedPropertiesPatch({ serialNumber: serialNumber }, null);
      const patchThermostat1Info = helperCreateReportedPropertiesPatch({
        maxTempSinceLastReboot: thermostat1.getMaxTemperatureValue(),
      }, thermostat1ComponentName);

      const patchThermostat2Info = helperCreateReportedPropertiesPatch({
        maxTempSinceLastReboot: thermostat2.getMaxTemperatureValue(),
      }, thermostat2ComponentName);

      const patchDeviceInfo = helperCreateReportedPropertiesPatch({
        manufacturer: 'Contoso Device Corporation',
        model: 'Contoso 47-turbo',
        swVersion: '10.89',
        osName: 'Contoso_OS',
        processorArchitecture: 'Contoso_x86',
        processorManufacturer: 'Contoso Industries',
        totalStorage: 65000,
        totalMemory: 640,
      }, deviceInfoComponentName);

      // the below things can only happen once the twin is there
      updateComponentReportedProperties(resultTwin, patchRoot, null);
      updateComponentReportedProperties(resultTwin, patchThermostat1Info, thermostat1ComponentName);
      updateComponentReportedProperties(resultTwin, patchThermostat2Info, thermostat2ComponentName);
      updateComponentReportedProperties(resultTwin, patchDeviceInfo, deviceInfoComponentName);
      desiredPropertyPatchListener(resultTwin, [thermostat1ComponentName, thermostat2ComponentName, deviceInfoComponentName]);
    } catch (err) {
      console.error('could not retrieve twin or report twin properties\n' + err.toString());
    }
  } catch (err) {
    console.error('could not connect Plug and Play client or could not attach interval function for telemetry\n' + err.toString());
  }
}
```

`provisionDevice` 函数显示设备如何使用 DPS 注册并连接到 IoT Central。 负载包括 IoT Central 用来[将设备与设备模板关联](../articles/iot-central/core/concepts-get-connected.md#associate-a-device-with-a-device-template)的模型 ID：

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
    console.log('registration succeeded');
    console.log('assigned hub=' + result.assignedHub);
    console.log('deviceId=' + result.deviceId);
    console.log('payload=' + JSON.stringify(result.payload));
  } catch (err) {
    console.error("error registering device: " + err.toString());
  }
}
```

`sendTelemetry` 函数显示设备如何将温度遥测发送到 IoT Central。 对于来自组件的遥测数据，它使用组件名称添加一个名为 `$.sub` 的属性：

```javascript
async function sendTelemetry(deviceClient, data, index, componentName) {
  if (!!(componentName)) {
    console.log('Sending telemetry message %d from component: %s ', index, componentName);
  } else {
    console.log('Sending telemetry message %d from root interface', index);
  }
  const msg = new Message(data);
  if (!!(componentName)) {
    msg.properties.add(messageSubjectProperty, componentName);
  }
  msg.contentType = 'application/json';
  msg.contentEncoding = 'utf-8';
  await deviceClient.sendEvent(msg);
}
```

`main` 方法使用调用的帮助器方法 `helperCreateReportedPropertiesPatch` 来创建属性更新消息。 此方法采用一个可选参数来指定发送属性的组件：

```javascript
const helperCreateReportedPropertiesPatch = (propertiesToReport, componentName) => {
  let patch;
  if (!!(componentName)) {
    patch = { };
    propertiesToReport.__t = 'c';
    patch[componentName] = propertiesToReport;
  } else {
    patch = { };
    patch = propertiesToReport;
  }
  if (!!(componentName)) {
    console.log('The following properties will be updated for component: ' + componentName);
  } else {
    console.log('The following properties will be updated for root interface.');
  }
  console.log(patch);
  return patch;
};
```

`main` 方法使用以下方法来处理对 IoT Central 中可写属性的更新。 注意方法如何生成包含版本和状态代码的响应：

```javascript
const desiredPropertyPatchListener = (deviceTwin, componentNames) => {
  deviceTwin.on('properties.desired', (delta) => {
    console.log('Received an update for device with value: ' + JSON.stringify(delta));
    Object.entries(delta).forEach(([key, values]) => {
      const version = delta.$version;
      if (!!(componentNames) && componentNames.includes(key)) { // then it is a component we are expecting
        const componentName = key;
        const patchForComponents = { [componentName]: {} };
        Object.entries(values).forEach(([propertyName, propertyValue]) => {
          if (propertyName !== '__t' && propertyName !== '$version') {
            console.log('Will update property: ' + propertyName + ' to value: ' + propertyValue + ' of component: ' + componentName);
            const propertyContent = { value: propertyValue };
            propertyContent.ac = 200;
            propertyContent.ad = 'Successfully executed patch';
            propertyContent.av = version;
            patchForComponents[componentName][propertyName] = propertyContent;
          }
        });
        updateComponentReportedProperties(deviceTwin, patchForComponents, componentName);
      }
      else if  (key !== '$version') { // individual property for root
        const patchForRoot = { };
        console.log('Will update property: ' + key + ' to value: ' + values + ' for root');
        const propertyContent = { value: values };
        propertyContent.ac = 200;
        propertyContent.ad = 'Successfully executed patch';
        propertyContent.av = version;
        patchForRoot[key] = propertyContent;
        updateComponentReportedProperties(deviceTwin, patchForRoot, null);
      }
    });
  });
};
```

`main` 方法使用以下方法来处理来自 IoT Central的 命令：

```javascript
const commandHandler = async (request, response) => {
  helperLogCommandRequest(request);
  switch (request.methodName) {
  case commandNameGetMaxMinReport1: {
    await sendCommandResponse(request, response, 200, thermostat1.getMaxMinReportObject());
    break;
  }
  case commandNameGetMaxMinReport2: {
    await sendCommandResponse(request, response, 200, thermostat2.getMaxMinReportObject());
    break;
  }
  case commandNameReboot: {
    await sendCommandResponse(request, response, 200, 'reboot response');
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
    console.log('Response to method: ' + request.methodName + ' sent successfully.' );
  } catch (err) {
    console.error('An error ocurred when sending a method response:\n' + err.toString());
  }
};
```

## <a name="get-connection-information"></a>获取连接信息

[!INCLUDE [iot-central-connection-configuration](iot-central-connection-configuration.md)]

## <a name="run-the-code"></a>运行代码

若要运行示例应用程序，请打开命令行环境并导航到包含 pnpTemperatureController.js 示例文件的 azure-iot-sdk-node/device/samples/pnp 文件夹。 

[!INCLUDE [iot-central-connection-environment](iot-central-connection-environment.md)]

安装所需的包：

```cmd/sh
npm install
```

运行示例：

```cmd/sh
node pnpTemperatureController.js
```

以下输出显示设备注册并连接到 IoT Central。 该示例先发送两个恒温器组件的“`maxTempSinceLastReboot`”属性，然后才开始发送遥测数据：

```output
registration succeeded
assigned hub=iotc-....azure-devices.net
deviceId=sample-device-01
payload=undefined
Connecting using connection string: HostName=iotc-....azure-devices.net;DeviceId=sample-device-01;SharedAccessKey=qdv...IpAo=
Enabling the commands on the client
Please enter q or Q to exit sample.
The following properties will be updated for root interface.
{ serialNumber: 'alwinexlepaho8329' }
The following properties will be updated for component: thermostat1
{ thermostat1: { maxTempSinceLastReboot: 1.5902294191855972, __t: 'c' } }
The following properties will be updated for component: thermostat2
{ thermostat2: { maxTempSinceLastReboot: 16.181771928614545, __t: 'c' } }
The following properties will be updated for component: deviceInformation
{ deviceInformation:
   { manufacturer: 'Contoso Device Corporation',
     model: 'Contoso 47-turbo',
     swVersion: '10.89',
     osName: 'Contoso_OS',
     processorArchitecture: 'Contoso_x86',
     processorManufacturer: 'Contoso Industries',
     totalStorage: 65000,
     totalMemory: 640,
     __t: 'c' } }
executed sample
Received an update for device with value: {"$version":1}
Properties have been reported for component: thermostat1
Properties have been reported for component: thermostat2
Properties have been reported for component: deviceInformation
Properties have been reported for root interface.
Sending telemetry message 0 from component: thermostat1 
Sending telemetry message 0 from component: thermostat2 
Sending telemetry message 0 from root interface
```

[!INCLUDE [iot-central-monitor-thermostat](iot-central-monitor-thermostat.md)]

你可以看到设备如何响应命令和属性更新。 此 `getMaxMinReport` 命令在 `thermostat2` 组件中，而 `reboot` 命令在默认组件中。 系统为“thermostat2”组件设置了 `targetTemperature` 可写属性：

```output
Received command request for command name: thermostat2*getMaxMinReport
The command request payload is:
2021-03-26T06:00:00.000Z
Response to method: thermostat2*getMaxMinReport sent successfully.

...

Received command request for command name: reboot
The command request payload is:
10
Response to method: reboot sent successfully.

...

Received an update for device with value: {"thermostat2":{"targetTemperature":76,"__t":"c"},"$version":2}
Will update property: targetTemperature to value: 76 of component: thermostat2
Properties have been reported for component: thermostat2
```
