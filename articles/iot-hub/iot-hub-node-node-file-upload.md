---
title: 使用 Node 将文件从设备上传到 Azure IoT 中心 | Microsoft Docs
description: 如何使用用于 Node.js 的 Azure IoT 设备 SDK 从设备将文件上传到云中。 上传的文件存储在 Azure 存储 Blob 容器中。
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 06/28/2017
ms.custom: mqtt, devx-track-js
ms.openlocfilehash: b94cd4cc5c76495cb08f743b1a6849b6eb6c77b2
ms.sourcegitcommit: 190658142b592db528c631a672fdde4692872fd8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2021
ms.locfileid: "112005926"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-nodejs"></a>通过 IoT 中心将设备中的文件上传到云 (Node.js)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

本教程介绍如何：

* 安全提供具有 Azure blob URI 的设备，用于上传文件。

* 使用 IoT 中心文件上传通知触发处理应用后端中的文件。

[从设备将遥测数据发送到 IoT 中心](quickstart-send-telemetry-node.md)快速入门演示了 IoT 中心基本的设备到云的消息传送功能。 但是，在某些情况下，无法轻松地将设备发送的数据映射为 IoT 中心接受的相对较小的设备到云消息。 例如：

* 包含图像的大型文件
* 视频
* 以高频率采样的振动数据
* 某种形式的预处理数据。

通常使用 [Azure 数据工厂](../data-factory/introduction.md)或 [Hadoop](../hdinsight/index.yml) 堆栈等工具在云中批处理这些文件。 需要从设备上传文件时，仍可以使用 IoT 中心的安全性和可靠性。

在本教程结束时，会运行两个 Node.js 控制台应用：

* FileUpload.js，此应用使用 IoT 中心提供的 SAS URI 将文件上传到存储。

* **ReadFileUploadNotification.js**，它可以接收来自 IoT 中心的文件上传通知。

> [!NOTE]
> IoT 中心通过 Azure IoT 设备 SDK 来支持许多设备平台和语言（包括 C、.NET、Javascript、Python 和 Java）。 有关如何将设备连接到 Azure IoT 中心的分步说明，请参阅 [Azure IoT 开发人员中心]。

[!INCLUDE [iot-hub-include-x509-ca-signed-file-upload-support-note](../../includes/iot-hub-include-x509-ca-signed-file-upload-support-note.md)]

## <a name="prerequisites"></a>先决条件

* Node.js 版本 10.0.x 或更高版本。 [准备开发环境](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md)介绍了如何在 Windows 或 Linux 上安装本教程所用的 Node.js。

* 有效的 Azure 帐户。 （如果没有帐户，只需几分钟即可创建一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。）

* 确保已在防火墙中打开端口 8883。 本文中的设备示例使用 MQTT 协议，该协议通过端口 8883 进行通信。 在某些公司和教育网络环境中，此端口可能被阻止。 有关解决此问题的更多信息和方法，请参阅[连接到 IoT 中心(MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)。

## <a name="create-an-iot-hub"></a>创建 IoT 中心

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>从设备应用上传文件

在本部分，你将复制 GitHub 中的设备应用，以将文件上传到 IoT 中心。

1. GitHub 上为 Node.js 提供了两个文件上传示例，一个是基本示例，一个是更高级的示例。 在[此处](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/upload_to_blob.js)存储库中复制基本示例。 高级示例位于[此处](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/upload_to_blob_advanced.js)。   

2. 创建名为 ```fileupload``` 的空文件夹。  在 ```fileupload``` 文件夹的命令提示符处，使用以下命令创建 package.json 文件。  接受所有默认值：

    ```cmd/sh
    npm init
    ```

3. 在 ```fileupload``` 文件夹的命令提示符处，运行下述命令以安装 azure-iot-device 设备 SDK 包和 azure-iot-device-mqtt 包：

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

4. 使用文本编辑器，在 ```fileupload``` 文件夹中创建 FileUpload.js 文件，并将此基本示例复制到其中。

    ```javascript
    // Copyright (c) Microsoft. All rights reserved.
    // Licensed under the MIT license. See LICENSE file in the project root for full license information.

    'use strict';

    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var fs = require('fs');

    var deviceConnectionString = process.env.ConnectionString;
    var filePath = process.env.FilePath;

    var client = Client.fromConnectionString(deviceConnectionString, Protocol);
    fs.stat(filePath, function (err, fileStats) {
      var fileStream = fs.createReadStream(filePath);

      client.uploadToBlob('testblob.txt', fileStream, fileStats.size, function (err, result) {
        if (err) {
          console.error('error uploading file: ' + err.constructor.name + ': ' + err.message);
        } else {
          console.log('Upload successful - ' + result);
        }
        fileStream.destroy();
      });
    });
    ```

5. 为设备连接字符串和要上传文件的路径添加环境变量。

6. 保存并关闭 FileUpload.js 文件。

7. 将映像文件复制到 `fileupload` 文件夹，并指定一个名称，比如 `myimage.png`。 将文件路径放在 `FilePath` 环境变量中。

## <a name="get-the-iot-hub-connection-string"></a>获取 IoT 中心连接字符串

在本文中，你将创建一项后端服务，用于从你创建的 IoT 中心接收文件上传通知消息。 若要接收文件上传通知消息，服务需要服务连接权限。 默认情况下，每个 IoT 中心都使用名为“服务”的共享访问策略创建，该策略会授予此权限。

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="receive-a-file-upload-notification"></a>接收文件上传通知

本部分中的操作将会创建一个 Node.js 控制台应用，用于接收来自 IoT 中心的文件上传通知消息。

可以使用 IoT 中心的 **iothubowner** 的连接字符串完成本部分的操作。 可以在 [Azure 门户](https://portal.azure.com/)上的“共享访问策略”边栏选项卡中找到该连接字符串。

1. 创建名为 ```fileuploadnotification``` 的空文件夹。  在 ```fileuploadnotification``` 文件夹的命令提示符处，使用以下命令创建 package.json 文件。  接受所有默认值：

    ```cmd/sh
    npm init
    ```

2. 在 ```fileuploadnotification``` 文件夹中的命令提示符下，运行以下命令安装 **azure-iothub** SDK 包：

    ```cmd/sh
    npm install azure-iothub --save
    ```

3. 使用文本编辑器在 `fileuploadnotification` 文件夹中创建 **FileUploadNotification.js** 文件。

4. 在 **FileUploadNotification.js** 文件的开头添加以下 `require` 语句：

    ```javascript
    'use strict';

    var Client = require('azure-iothub').Client;
    ```

5. 添加 `iothubconnectionstring` 变量，并使用它创建一个客户端实例。  将 `{iothubconnectionstring}` 占位符值替换为先前在[获取 IoT 中心连接字符串](#get-the-iot-hub-connection-string)中复制的 IoT 中心连接字符串：

    ```javascript
    var connectionString = '{iothubconnectionstring}';
    ```

    > [!NOTE]
    > 为方便起见，代码中包含了连接字符串：这并不是建议的做法，你可能需要根据用例和体系结构，考虑使用更安全的方式来存储此机密。

6. 添加以下代码用于连接客户端：

    ```javascript
    var serviceClient = Client.fromConnectionString(connectionString);
    ```

7. 打开客户端，并使用 **getFileNotificationReceiver** 函数接收状态更新。

    ```javascript
    serviceClient.open(function (err) {
      if (err) {
        console.error('Could not connect: ' + err.message);
      } else {
        console.log('Service client connected');
        serviceClient.getFileNotificationReceiver(function receiveFileUploadNotification(err, receiver){
          if (err) {
            console.error('error getting the file notification receiver: ' + err.toString());
          } else {
            receiver.on('message', function (msg) {
              console.log('File upload from device:')
              console.log(msg.getData().toString('utf-8'));
            });
          }
        });
      }
    });
    ```

8. 保存并关闭 **FileUploadNotification.js** 文件。

## <a name="run-the-applications"></a>运行应用程序

现在，已准备就绪，可以运行应用程序了。

在 `fileuploadnotification` 文件夹中的命令提示符下运行以下命令：

```cmd/sh
node FileUploadNotification.js
```

在 `fileupload` 文件夹中的命令提示符下运行以下命令：

```cmd/sh
node FileUpload.js
```

以下屏幕截图显示来自 FileUpload 应用的输出  ：

![simulated-device 应用的输出](./media/iot-hub-node-node-file-upload/simulated-device.png)

以下屏幕截图显示 **FileUploadNotification** 应用的输出：

![read-file-upload-notification 应用的输出](./media/iot-hub-node-node-file-upload/read-file-upload-notification.png)

可以使用门户查看所配置的存储容器中上传的文件：

![上传的文件](./media/iot-hub-node-node-file-upload/uploaded-file.png)

## <a name="next-steps"></a>后续步骤

在本教程中，你已学习了如何使用 IoT 中心的文件上传功能来简化从设备进行的文件上传。 可以使用以下文章继续探索 IoT 中心功能和方案：

* [以编程方式创建 IoT 中心](iot-hub-rm-template-powershell.md)

* [C SDK 简介](iot-hub-device-sdk-c-intro.md)

* [Azure IoT SDK](iot-hub-devguide-sdks.md)
