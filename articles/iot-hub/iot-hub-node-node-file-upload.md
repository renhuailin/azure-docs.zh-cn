---
title: 使用 Node 将文件从设备上传到 Azure IoT 中心 | Microsoft Docs
description: 如何使用用于 Node.js 的 Azure IoT 设备 SDK 从设备将文件上传到云中。 上传的文件存储在 Azure 存储 Blob 容器中。
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 07/27/2021
ms.custom: mqtt, devx-track-js
ms.openlocfilehash: a95737cd9d15b0ee21249f0db8d1bbb96fb8a3b2
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129710082"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-nodejs"></a>通过 IoT 中心将设备中的文件上传到云 (Node.js)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

本教程介绍如何：

* 安全提供具有 Azure blob URI 的设备，用于上传文件。

* 使用 IoT 中心文件上传通知触发处理应用后端中的文件。

[从设备将遥测数据发送到 IoT 中心](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-nodejs)快速入门演示了 IoT 中心基本的设备到云的消息传送功能。 但是，在某些情况下，无法轻松地将设备发送的数据映射为 IoT 中心接受的相对较小的设备到云消息。 例如：

* 包含图像的大型文件
* 视频
* 以高频率采样的振动数据
* 某种形式的预处理数据。

通常使用 [Azure 数据工厂](../data-factory/introduction.md)或 [Hadoop](../hdinsight/index.yml) 堆栈等工具在云中批处理这些文件。 需要从设备上传文件时，仍可以使用 IoT 中心的安全性和可靠性。

在本文结束时，会运行两个 Node.js 控制台应用：

* **FileUpload.js**，它使用 IoT 中心提供的 SAS URI 将文件上传到存储。

* FileUploadNotification.js，它接收来自 IoT 中心的文件上传通知。

> [!NOTE]
> IoT 中心通过 Azure IoT 设备 SDK 来支持许多设备平台和语言（包括 C、Java、Python 和 JavaScript）。 有关如何将设备连接到 Azure IoT 中心的分步说明，请参阅 [Azure IoT 开发人员中心](https://azure.microsoft.com/develop/iot)。

[!INCLUDE [iot-hub-include-x509-ca-signed-file-upload-support-note](../../includes/iot-hub-include-x509-ca-signed-file-upload-support-note.md)]

## <a name="prerequisites"></a>先决条件

* Node.js 版本 10.0.x 或更高版本。 建议使用 LTS 版本。 可以从 [nodejs.org](https://nodejs.org) 下载 Node.js。

* 有效的 Azure 帐户。 （如果没有帐户，只需几分钟即可创建一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。）

* 确保已在防火墙中打开端口 8883。 本文中的设备示例使用 MQTT 协议，该协议通过端口 8883 进行通信。 在某些公司和教育网络环境中，此端口可能被阻止。 有关解决此问题的更多信息和方法，请参阅[连接到 IoT 中心(MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)。

## <a name="create-an-iot-hub"></a>创建 IoT 中心

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>在 IoT 中心内注册新设备

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-include-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>从设备应用上传文件

在本部分，我们创建将文件上传到 IoT 中心的设备应用。 该代码基于 [Azure IoT node.js SDK](https://github.com/Azure/azure-iot-sdk-node) 设备示例中的 [upload_to_blob_advanced.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/javascript/upload_to_blob_advanced.js) 示例中提供的代码。

1. 创建名为 `fileupload` 的空文件夹。  在 `fileupload` 文件夹的命令提示符处，使用以下命令创建 package.json 文件。  接受所有默认值：

    ```cmd/sh
    npm init
    ```

1. 在 `fileupload` 文件夹的命令提示符处运行以下命令，以安装 azure-iot-device 设备 SDK、azure-iot-device-mqtt 和 @azure/storage-blob 包  ：

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt @azure/storage-blob --save
    ```

1. 使用文本编辑器，在 `fileupload` 文件夹中创建一个 FileUpload.js 文件，并将以下代码复制到其中。

    ```javascript
    'use strict';

    const Client = require('azure-iot-device').Client;
    const Protocol = require('azure-iot-device-mqtt').Mqtt;
    const errors = require('azure-iot-common').errors;
    const path = require('path');

    const {
      AnonymousCredential,
      BlockBlobClient,
      newPipeline
    } = require('@azure/storage-blob');

    // make sure you set these environment variables prior to running the sample.
    const deviceConnectionString = process.env.DEVICE_CONNECTION_STRING;
    const localFilePath = process.env.PATH_TO_FILE;
    const storageBlobName = path.basename(localFilePath);

    async function uploadToBlob(localFilePath, client) {
      const blobInfo = await client.getBlobSharedAccessSignature(storageBlobName);
      if (!blobInfo) {
        throw new errors.ArgumentError('Invalid upload parameters');
      }

      const pipeline = newPipeline(new AnonymousCredential(), {
        retryOptions: { maxTries: 4 },
        telemetry: { value: 'HighLevelSample V1.0.0' }, // Customized telemetry string
        keepAliveOptions: { enable: false }
      });

      // Construct the blob URL to construct the blob client for file uploads
      const { hostName, containerName, blobName, sasToken } = blobInfo;
      const blobUrl = `https://${hostName}/${containerName}/${blobName}${sasToken}`;

      // Create the BlockBlobClient for file upload to the Blob Storage Blob
      const blobClient = new BlockBlobClient(blobUrl, pipeline);

      // Setup blank status notification arguments to be filled in on success/failure
      let isSuccess;
      let statusCode;
      let statusDescription;

      try {
        const uploadStatus = await blobClient.uploadFile(localFilePath);
        console.log('uploadStreamToBlockBlob success');

        // Save successful status notification arguments
        isSuccess = true;
        statusCode = uploadStatus._response.status;
        statusDescription = uploadStatus._response.bodyAsText;

        // Notify IoT Hub of upload to blob status (success)
        console.log('notifyBlobUploadStatus success');
      }
      catch (err) {
        isSuccess = false;
        statusCode = err.code;
        statusDescription = err.message;

        console.log('notifyBlobUploadStatus failed');
        console.log(err);
      }

      await client.notifyBlobUploadStatus(blobInfo.correlationId, isSuccess, statusCode, statusDescription);
    }

    // Create a client device from the connection string and upload the local file to blob storage.
    const deviceClient = Client.fromConnectionString(deviceConnectionString, Protocol);
    uploadToBlob(localFilePath, deviceClient)
      .catch((err) => {
        console.log(err);
      })
      .finally(() => {
        process.exit();
      });
    ```

1. 保存并关闭 FileUpload.js 文件。

1. 将图像文件复制到 `fileupload` 文件夹，并为其指定名称，例如 `myimage.png`。

1. 为设备连接字符串添加环境变量并添加要上传的文件的路径。 在[向 IoT 中心注册设备](#register-a-new-device-in-the-iot-hub)时，你收到了设备连接字符串。
    
    - 对于 Windows：

        ```cmd
        set DEVICE_CONNECTION_STRING={your device connection string}
        set PATH_TO_FILE={your image filepath}
        ```

    - 对于 Linux/Bash：

        ```bash
        export DEVICE_CONNECTION_STRING="{your device connection string}"
        export PATH_TO_FILE="{your image filepath}"
        ```

## <a name="get-the-iot-hub-connection-string"></a>获取 IoT 中心连接字符串

在本文中，你将创建一项后端服务，用于从你创建的 IoT 中心接收文件上传通知消息。 若要接收文件上传通知消息，服务需要服务连接权限。 默认情况下，每个 IoT 中心都使用名为“服务”的共享访问策略创建，该策略会授予此权限。

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="receive-a-file-upload-notification"></a>接收文件上传通知

本部分中的操作将会创建一个 Node.js 控制台应用，用于接收来自 IoT 中心的文件上传通知消息。

1. 创建名为 `fileuploadnotification` 的空文件夹。  在 `fileuploadnotification` 文件夹的命令提示符处，使用以下命令创建 package.json 文件。  接受所有默认值：

    ```cmd/sh
    npm init
    ```

1. 在 `fileuploadnotification` 文件夹中的命令提示符下，运行以下命令安装 **azure-iothub** SDK 包：

    ```cmd/sh
    npm install azure-iothub --save
    ```

1. 使用文本编辑器在 `fileuploadnotification` 文件夹中创建 **FileUploadNotification.js** 文件。

1. 在 **FileUploadNotification.js** 文件的开头添加以下 `require` 语句：

    ```javascript
    'use strict';

    const Client = require('azure-iothub').Client;
    ```

1. 从环境中读取 IoT 中心的连接字符串：

    ```javascript
    const connectionString = process.env.IOT_HUB_CONNECTION_STRING;
    ```

1. 添加以下代码，从连接字符串创建服务客户端：

    ```javascript
    const serviceClient = Client.fromConnectionString(connectionString);
    ```

1. 打开客户端，并使用 **getFileNotificationReceiver** 函数接收状态更新。

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

1. 保存并关闭 **FileUploadNotification.js** 文件。

1. 为 IoT 中心连接字符串添加环境变量。 你先前在[获取 IoT 中心连接字符串](#get-the-iot-hub-connection-string)中复制了此字符串。
    
    - 对于 Windows：

        ```cmd
        set IOT_HUB_CONNECTION_STRING={your iot hub connection string}
        ```

    - 对于 Linux/Bash：

        ```bash
        export IOT_HUB_CONNECTION_STRING="{your iot hub connection string}"
        ```

## <a name="run-the-applications"></a>运行应用程序

现在已准备就绪，可以运行应用程序了。

在 `fileuploadnotification` 文件夹中的命令提示符下运行以下命令：

```cmd/sh
node FileUploadNotification.js
```

在 `fileupload` 文件夹中的命令提示符下运行以下命令：

```cmd/sh
node FileUpload.js
```

以下输出来自上传完成后的 FileUpload 应用：

```output
uploadStreamToBlockBlob success
notifyBlobUploadStatus success
```

以下示例输出来自上传完成后的 FileUploadNotification 应用：

```output
Service client connected
File upload from device:
{"deviceId":"myDeviceId","blobUri":"https://{your storage account name}.blob.core.windows.net/device-upload-container/myDeviceId/image.png","blobName":"myDeviceId/image.png","lastUpdatedTime":"2021-07-23T23:27:06+00:00","blobSizeInBytes":26214,"enqueuedTimeUtc":"2021-07-23T23:27:07.2580791Z"}
```

## <a name="verify-the-file-upload"></a>验证文件上传

可以使用门户查看所配置的存储容器中上传的文件：

1. 在 Azure 门户中导航到存储帐户。
1. 在存储帐户的左侧窗格中，选择“容器”。
1. 选择已将文件上传到其中的容器。
1. 选择与设备同名的文件夹。
1. 选择已将文件上传到其中的 Blob。 在本文中，它是与你的文件同名的 Blob。  

    :::image type="content" source="./media/iot-hub-node-node-file-upload/view-uploaded-file.png" alt-text="在 Azure 门户中查看上传的文件的屏幕截图。":::

1. 在打开的页面上查看 Blob 属性。 可以选择“下载”，以将该文件下载到本地并查看其内容。

## <a name="next-steps"></a>后续步骤

在本教程中，你已学习了如何使用 IoT 中心的文件上传功能来简化从设备进行的文件上传。 可以使用以下文章继续探索 IoT 中心功能和方案：

* [以编程方式创建 IoT 中心](iot-hub-rm-template-powershell.md)

* [C SDK 简介](iot-hub-device-sdk-c-intro.md)

* [Azure IoT SDK](iot-hub-devguide-sdks.md)
