---
title: 向 Azure IoT 中心发送设备遥测数据快速入门 (Node.js)
description: 在本快速入门中，你将使用适用于 Node.js 的 Azure IoT 中心设备 SDK 将遥测数据从设备发送到 IoT 中心。
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: node
ms.topic: quickstart
ms.date: 01/11/2021
ms.openlocfilehash: 97fcff4706d6da968c93426f85569fed9af95aac
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2021
ms.locfileid: "102197803"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-nodejs"></a>快速入门：将遥测数据从设备发送到 IoT 中心 (Node.js)

**适用于**：[设备应用程序开发](about-iot-develop.md#device-application-development)

本快速入门介绍一个基本的 IoT 设备应用程序开发工作流。 使用 Azure CLI 创建 Azure IoT 中心和模拟设备，然后使用 Azure IoT Node.js SDK 访问该设备，并将遥测数据发送到中心。

## <a name="prerequisites"></a>先决条件
- 如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- Azure CLI。 可以使用 Azure Cloud Shell（即在浏览器中运行的交互式 CLI Shell）运行本快速入门中的所有命令。 如果使用 Cloud Shell，则无需安装任何内容。 如果希望在本地使用 CLI，则本快速入门需要 Azure CLI 2.0.76 版或更高版本。 若要查找版本，请运行 az --version。 若要安装或升级，请参阅[安装 Azure CLI]( /cli/azure/install-azure-cli)。
- [Node.js 10+](https://nodejs.org)。 如果使用的是 Azure Cloud Shell，请勿更新已安装的 Node.js 版本。 Azure Cloud Shell 已安装最新版本的 Node.js。

    使用以下命令验证开发计算机上 Node.js 的当前版本：

    ```cmd/sh
        node --version
    ```

[!INCLUDE [iot-hub-include-create-hub-cli](../../includes/iot-hub-include-create-hub-cli.md)]

## <a name="use-the-nodejs-sdk-to-send-messages"></a>使用 Node.js SDK 发送消息
在本部分，你要使用 Node.js SDK 将消息从模拟设备发送到 IoT 中心。 

1. 打开新的终端窗口。 将使用此终端来安装 Node.js SDK 和处理 Node.js 示例代码。 现在应该打开了两个终端：刚刚打开的用于操作 Node.js 的终端，以及在前面部分中用于输入 Azure CLI 命令的 CLI shell。 

1. 将 [Azure IoT Node.js SDK 设备示例](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples)复制到本地计算机：

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-node
    ```

1. 导航到“azure-iot-sdk-node/device/samples”目录：

    ```console
    cd azure-iot-sdk-node/device/samples
    ```
1. 安装 Azure IoT Node.js SDK 和所需的依赖项：

    ```console
    npm install
    ```
    此命令按照设备示例目录下 *package.json* 文件中的指定安装适当的依赖项。

1. 将“设备连接字符串”设置为名为 `DEVICE_CONNECTION_STRING` 的环境变量。 该字符串是在上一部分创建模拟的 Node.js 设备后获取的字符串。 

    **Windows (cmd)**

    ```console
    set DEVICE_CONNECTION_STRING=<your connection string here>
    ```

    > [!NOTE]
    > 在 Windows CMD 中，请不要在连接字符串的两边加上引号。

    **Linux (bash)**

    ```bash
    export DEVICE_CONNECTION_STRING="<your connection string here>"
    ```

1. 在打开的 CLI shell 中，运行 [az iot hub monitor-events](/cli/azure/ext/azure-iot/iot/hub#ext-azure-iot-az-iot-hub-monitor-events) 命令以开始监视模拟 IoT 设备上的事件。  事件消息抵达后，会在终端中输出。

    ```azurecli
    az iot hub monitor-events --output table --hub-name {YourIoTHubName}
    ```

1. 在 Node.js 终端中，运行安装的示例文件 *simple_sample_device.js* 的代码。 此代码访问模拟的 IoT 设备并将消息发送到 IoT 中心。

    若要在终端中运行 Node.js 示例：
    ```console
    node ./simple_sample_device.js
    ```

    或者，可以在 JavaScript IDE 中运行示例中的 Node.js 代码：
    ```javascript
    'use strict';

    const Protocol = require('azure-iot-device-mqtt').Mqtt;
    // Uncomment one of these transports and then change it in fromConnectionString to test other transports
    // const Protocol = require('azure-iot-device-amqp').AmqpWs;
    // const Protocol = require('azure-iot-device-http').Http;
    // const Protocol = require('azure-iot-device-amqp').Amqp;
    // const Protocol = require('azure-iot-device-mqtt').MqttWs;
    const Client = require('azure-iot-device').Client;
    const Message = require('azure-iot-device').Message;

    // String containing Hostname, Device Id & Device Key in the following formats:
    //  "HostName=<iothub_host_name>;DeviceId=<device_id>;SharedAccessKey=<device_key>"
    const deviceConnectionString = process.env.DEVICE_CONNECTION_STRING;
    let sendInterval;

    function disconnectHandler () {
    clearInterval(sendInterval);
    client.open().catch((err) => {
        console.error(err.message);
    });
    }

    // The AMQP and HTTP transports have the notion of completing, rejecting or abandoning the message.
    // For example, this is only functional in AMQP and HTTP:
    // client.complete(msg, printResultFor('completed'));
    // If using MQTT calls to complete, reject, or abandon are no-ops.
    // When completing a message, the service that sent the C2D message is notified that the message has been processed.
    // When rejecting a message, the service that sent the C2D message is notified that the message won't be processed by the device. the method to use is client.reject(msg, callback).
    // When abandoning the message, IoT Hub will immediately try to resend it. The method to use is client.abandon(msg, callback).
    // MQTT is simpler: it accepts the message by default, and doesn't support rejecting or abandoning a message.
    function messageHandler (msg) {
    console.log('Id: ' + msg.messageId + ' Body: ' + msg.data);
    client.complete(msg, printResultFor('completed'));
    }

    function generateMessage () {
    const windSpeed = 10 + (Math.random() * 4); // range: [10, 14]
    const temperature = 20 + (Math.random() * 10); // range: [20, 30]
    const humidity = 60 + (Math.random() * 20); // range: [60, 80]
    const data = JSON.stringify({ deviceId: 'myFirstDevice', windSpeed: windSpeed, temperature: temperature, humidity: humidity });
    const message = new Message(data);
    message.properties.add('temperatureAlert', (temperature > 28) ? 'true' : 'false');
    return message;
    }

    function errorCallback (err) {
    console.error(err.message);
    }

    function connectCallback () {
    console.log('Client connected');
    // Create a message and send it to the IoT Hub every two seconds
    sendInterval = setInterval(() => {
        const message = generateMessage();
        console.log('Sending message: ' + message.getData());
        client.sendEvent(message, printResultFor('send'));
    }, 2000);

    }

    // fromConnectionString must specify a transport constructor, coming from any transport package.
    let client = Client.fromConnectionString(deviceConnectionString, Protocol);

    client.on('connect', connectCallback);
    client.on('error', errorCallback);
    client.on('disconnect', disconnectHandler);
    client.on('message', messageHandler);

    client.open()
    .catch(err => {
    console.error('Could not connect: ' + err.message);
    });

    // Helper function to print results in the console
    function printResultFor(op) {
    return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
    };
    }
    ```

当 Node.js 代码将模拟的遥测消息从设备发送到 IoT 中心时，该消息将显示于正在监视事件的 CLI shell 中：

```output
event:
  component: ''
  interface: ''
  module: ''
  origin: <your device name>
  payload: '{"deviceId":"myFirstDevice","windSpeed":11.853592092144627,"temperature":22.62484121157508,"humidity":66.17960805575937}'
```

设备现已安全建立连接，并在向 Azure IoT 中心发送遥测数据。

## <a name="clean-up-resources"></a>清理资源
如果不再需要本快速入门中创建的 Azure 资源，可以使用 Azure CLI 将其删除。

> [!IMPORTANT]
> 删除资源组的操作不可逆。 资源组以及包含在其中的所有资源将被永久删除。 请确保不会意外删除错误的资源组或资源。 

若要按名称删除资源组，请执行以下操作：
1. 运行 [az group delete](/cli/azure/group#az-group-delete) 命令。 此命令将删除创建的资源组、IoT 中心和设备注册。

    ```azurecli
    az group delete --name MyResourceGroup
    ```
1. 运行 [az group list](/cli/azure/group#az-group-list) 命令，确认资源组是否已删除。  

    ```azurecli
    az group list
    ```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已了解一个可将设备安全连接到云并发送设备到云的遥测数据的基本 Azure IoT 应用程序工作流。 你已使用 Azure CLI 创建了 IoT 中心和模拟设备，然后使用 Azure IoT Node.js SDK 访问了该设备，并将遥测数据发送到了中心。 

接下来，请通过应用程序示例了解 Azure IoT Node.js SDK。

- [更多 Node.js 示例](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples)：此目录包含 Node.js SDK 存储库中用于展示 IoT 中心方案的更多示例。