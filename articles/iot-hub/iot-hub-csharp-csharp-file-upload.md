---
title: 使用 .NET 将文件从设备上传到 Azure IoT 中心 | Microsoft Docs
description: 如何使用用于 .NET 的 Azure IoT 设备 SDK 从设备将文件上传到云中。 上传的文件存储在 Azure 存储 Blob 容器中。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 07/18/2021
ms.author: robinsh
ms.custom: mqtt, devx-track-csharp
ms.openlocfilehash: 41cf392fb4b50c06e6af1f20e0c53570589b4090
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778423"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-net"></a>通过 IoT 中心将设备中的文件上传到云 (.NET)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

本教程通过 .NET 文件上传示例介绍了如何使用 IoT 中心的文件上传功能。 

[将遥测数据从设备发送到 IoT 中心](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-csharp)快速入门和[使用 IoT 中心发送云到设备的消息](iot-hub-csharp-csharp-c2d.md)教程介绍了 IoT 中心提供的基本的设备到云和云到设备的消息传送功能。 [使用 IoT 中心配置消息路由](tutorial-routing.md)教程介绍了一种在 Microsoft Azure Blob 存储中可靠存储设备到云消息的方法。 但是，在某些情况下，无法轻松地将设备发送的数据映射为 IoT 中心接受的相对较小的设备到云消息。 例如：

* 包含图像的大型文件

* 视频

* 以高频率采样的振动数据

* 某种形式的预处理数据

通常使用 [Azure 数据工厂](../data-factory/introduction.md)或 [Hadoop](../hdinsight/index.yml) 堆栈等工具在云中批处理这些文件。 但是，当你需要从设备上传文件时，你仍然可以使用 IoT 中心的安全性和可靠性。 本教程将告诉你方法。

> [!NOTE]
> IoT 中心通过 Azure IoT 设备 SDK 来支持许多设备平台和语言（包括 C、Java、Python 和 JavaScript）。 有关如何将设备连接到 Azure IoT 中心的分步说明，请参阅 [Azure IoT 开发人员中心](https://azure.microsoft.com/develop/iot)。

[!INCLUDE [iot-hub-include-x509-ca-signed-file-upload-support-note](../../includes/iot-hub-include-x509-ca-signed-file-upload-support-note.md)]

## <a name="prerequisites"></a>先决条件

* Visual Studio Code

* 有效的 Azure 帐户。 如果没有帐户，只需花费几分钟就能创建一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。

* 本文中运行的示例应用程序是使用 C# 编写的。 对于 Azure IoT C# 示例，建议在开发计算机上使用 .NET Core SDK 3.1 或更高版本。

    可以从 [.NET](https://dotnet.microsoft.com/download) 为多个平台下载 .NET Core SDK。

    可以使用以下命令验证开发计算机上 .NET Core SDK 当前的版本：

    ```cmd/sh
    dotnet --version
    ```

* 从 [https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) 下载 Azure IoT C# 示例，并提取 ZIP 存档。

* 确保已在防火墙中打开端口 8883。 本文中的示例使用 MQTT 协议，该协议通过端口 8883 进行通信。 在某些公司和教育网络环境中，此端口可能被阻止。 有关解决此问题的更多信息和方法，请参阅[连接到 IoT 中心(MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)。

## <a name="create-an-iot-hub"></a>创建 IoT 中心

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>在 IoT 中心内注册新设备

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-include-associate-storage.md)]

## <a name="examine-the-application"></a>检查应用程序

在 Visual Studio Code 的 .NET 示例下载中打开 *azure-iot-samples-csharp-master\iot-hub\Samples\device\FileUploadSample* 文件夹。 该文件夹包含一个名为 parameters.cs 的文件。 如果打开该文件，你会看到参数 p 是必需的并且包含设备连接字符串。 注册设备时复制并保存了此连接字符串。 如果要更改传输协议，可以指定参数 t。 默认协议是 MQTT。 文件 program.cs 包含 main 函数。 FileUploadSample.cs 文件包含主要示例逻辑。 TestPayload.txt 是要上传到 Blob 容器中的文件。

## <a name="run-the-application"></a>运行应用程序

现即可运行应用程序。

1. 在 Visual Studio Code 中打开终端窗口。
1. 键入以下命令：
    ```cmd/sh
    dotnet restore
    dotnet run --p "{Your device connection string}"
    ```

输出应如下所示：

```cmd/sh
  Uploading file TestPayload.txt
  Getting SAS URI from IoT Hub to use when uploading the file...
  Successfully got SAS URI (https://contosostorage.blob.core.windows.net/contosocontainer/MyDevice%2FTestPayload.txt?sv=2018-03-28&sr=b&sig=x0G1Baf%2BAjR%2BTg3nW34zDNKs07p6dLzkxvZ3ZSmjIhw%3D&se=2021-05-04T16%3A40%3A52Z&sp=rw) from IoT Hub
  Uploading file TestPayload.txt using the Azure Storage SDK and the retrieved SAS URI for authentication
  Successfully uploaded the file to Azure Storage
  Notified IoT Hub that the file upload succeeded and that the SAS URI can be freed.
  Time to upload file: 00:00:01.5077954.
  Done.
```

## <a name="verify-the-file-upload"></a>验证文件上传

执行以下步骤，验证是否已将 TestPayload.txt 上传到容器：

1. 在存储帐户左侧窗格中的“数据存储”下，选择“容器” 。
1. 选择已将 TestPayload.txt 上传到其中的容器。
1. 选择与设备同名的文件夹。
1. 选择“TestPayload.txt”。
1. 下载该文件以在本地查看其内容。

## <a name="next-steps"></a>后续步骤

在本教程中，你已学习了如何使用 IoT 中心的文件上传功能来简化从设备进行的文件上传。 可以使用以下文章继续探索 IoT 中心功能和方案：

* [以编程方式创建 IoT 中心](iot-hub-rm-template-powershell.md)

* [C SDK 简介](iot-hub-device-sdk-c-intro.md)

* [Azure IoT SDK](iot-hub-devguide-sdks.md)

若要进一步探索 IoT 中心的功能，请参阅：

* [使用 Azure IoT Edge 将 AI 部署到边缘设备](../iot-edge/quickstart-linux.md)