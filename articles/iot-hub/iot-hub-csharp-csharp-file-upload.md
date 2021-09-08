---
title: 使用 .NET 将文件从设备上传到 Azure IoT 中心 | Microsoft Docs
description: 如何使用用于 .NET 的 Azure IoT 设备 SDK 从设备将文件上传到云中。 上传的文件存储在 Azure 存储 Blob 容器中。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/24/2021
ms.author: robinsh
ms.custom: mqtt, devx-track-csharp
ms.openlocfilehash: cc568964c3f961cad63591ea14885fb23eea03da
ms.sourcegitcommit: ef448159e4a9a95231b75a8203ca6734746cd861
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123187599"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-net"></a>通过 IoT 中心将设备中的文件上传到云 (.NET)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

本教程演示如何将 IoT 中心的文件上传功能与 Azure IoT .NET 设备和服务 SDK 结合使用。

[将遥测数据从设备发送到 IoT 中心](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-csharp)快速入门和[使用 IoT 中心发送云到设备的消息](iot-hub-csharp-csharp-c2d.md)教程介绍了 IoT 中心提供的基本的设备到云和云到设备的消息传送功能。 [使用 IoT 中心配置消息路由](tutorial-routing.md)教程介绍了一种在 Microsoft Azure Blob 存储中可靠存储设备到云消息的方法。 但是，在某些情况下，无法轻松地将设备发送的数据映射为 IoT 中心接受的相对较小的设备到云消息。 例如：

* 包含图像的大型文件

* 视频

* 以高频率采样的振动数据

* 某种形式的预处理数据

通常使用 [Azure 数据工厂](../data-factory/introduction.md)或 [Hadoop](../hdinsight/index.yml) 堆栈等工具在云中批处理这些文件。 但是，当你需要从设备上传文件时，你仍然可以使用 IoT 中心的安全性和可靠性。 本教程将告诉你方法。

在本教程结束时，会运行 2 个 .NET 控制台应用：

* FileUploadSample。 该设备应用使用 IoT 中心提供的 SAS URI 将文件上传到存储。 你需要从你在先决条件中下载的 Azure IoT C# 示例存储库运行此应用。

* ReadFileUploadNotification。 此服务应用可接收来自 IoT 中心的文件上传通知。 你将创建此应用。

> [!NOTE]
> IoT 中心通过 Azure IoT 设备 SDK 来支持许多设备平台和语言（包括 C、Java、Python 和 JavaScript）。 有关如何将设备连接到 Azure IoT 中心的分步说明，请参阅 [Azure IoT 开发人员中心](https://azure.microsoft.com/develop/iot)。

[!INCLUDE [iot-hub-include-x509-ca-signed-file-upload-support-note](../../includes/iot-hub-include-x509-ca-signed-file-upload-support-note.md)]

## <a name="prerequisites"></a>先决条件

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

## <a name="upload-file-from-a-device-app"></a>从设备应用上传文件

在本文中，你将使用之前从 Azure IoT C# 示例存储库中下载的示例作为设备应用。 可以使用 Visual Studio、Visual Studio Code 或所选的文本编辑器打开下面的文件。  

该示例位于你在其中提取 Azure IoT C# 示例的文件夹下的 azure-iot-samples-csharp-master\iot-hub\Samples\device\FileUploadSample 中。

检查 FileUpLoadSample.cs 中的代码。 此文件包含主要示例逻辑。 创建 IoT 中心设备客户端后，它将遵循由三部分组成的标准过程，用于从设备上传文件：

1. 此代码调用设备客户端上的 GetFileUploadSasUriAsync 方法，以从 IoT 中心获取 SAS URI：

    ```csharp
    var fileUploadSasUriRequest = new FileUploadSasUriRequest
    {
        BlobName = fileName
    };

    // Lines removed for clarity

    FileUploadSasUriResponse sasUri = await _deviceClient.GetFileUploadSasUriAsync(fileUploadSasUriRequest);
    Uri uploadUri = sasUri.GetBlobUri();
    ```

1. 此代码使用 SAS URI 将文件上传到 Azure 存储。 在此示例中，它使用 SAS URI 创建 Azure 存储块 Blob 客户端，并上传文件：

    ```csharp
    var blockBlobClient = new BlockBlobClient(uploadUri);
    await blockBlobClient.UploadAsync(fileStreamSource, new BlobUploadOptions());
    ```

1. 此代码通知 IoT 中心已完成上传。 这会告知 IoT 中心它可以发布与上传相关联的资源 (SAS URI)。 如果启用了文件上传通知，IoT 中心会将通知消息发送到后端服务。

    ```csharp
    var successfulFileUploadCompletionNotification = new FileUploadCompletionNotification
    {
        // Mandatory. Must be the same value as the correlation id returned in the sas uri response
        CorrelationId = sasUri.CorrelationId,
    
        // Mandatory. Will be present when service client receives this file upload notification
        IsSuccess = true,
    
        // Optional, user defined status code. Will be present when service client receives this file upload notification
        StatusCode = 200,
    
        // Optional, user-defined status description. Will be present when service client receives this file upload notification
        StatusDescription = "Success"
    };
    
    await _deviceClient.CompleteFileUploadAsync(successfulFileUploadCompletionNotification);
    ```

如果检查 parameter.cs 文件，将看到：

- 该示例要求传递参数 p，该参数采用设备连接字符串。 

- 默认情况下，设备示例使用 MQTT 协议来与 IoT 中心通信。 可以使用参数 t 更改此传输协议。 请注意，不管该选项如何，Azure Blob 客户端始终使用 HTTPS 作为协议来上传文件 Azure 存储。

## <a name="get-the-iot-hub-connection-string"></a>获取 IoT 中心连接字符串

在本文中，你将创建一项后端服务，用于从你的 IoT 中心接收文件上传通知消息。 若要接收文件上传通知消息，服务需要服务连接权限。 默认情况下，每个 IoT 中心都使用名为“服务”的共享访问策略创建，该策略会授予此权限。

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="receive-a-file-upload-notification"></a>接收文件上传通知

本部分中的操作将创建一个 C# 控制台应用，用于接收来自 IoT 中心的文件上传通知消息。

1. 打开一个命令窗口并转到你想要在其中创建项目的文件夹。 创建名为 ReadFileUploadNotifications 的文件夹，并将目录更改到该文件夹。

    ```cmd/sh
    mkdir ReadFileUploadNotification
    cd ReadFileUploadNotification
    ```

1. 运行以下命令来创建 C# 控制台项目。 运行命令后，文件夹将包含 Program.cs 文件和 ReadFileUploadNotification.csproj 文件 。

    ```cmd/sh
    dotnet new console --language c#
    ```

1. 运行以下命令来将 Microsoft.Azure.Devices 包添加到项目文件。 此包是 Azure IoT .NET 服务 SDK。

    ```cmd/sh
    dotnet add package Microsoft.Azure.Devices
    ```

1. 打开 Program.cs 文件，并在文件的顶部添加以下语句：

    ```csharp
    using Microsoft.Azure.Devices;
    ```
1. 将以下字段添加到 Program 类。 将 `{iot hub connection string}` 占位符值替换为以前在[获取 IoT 中心连接字符串](#get-the-iot-hub-connection-string)中复制的 IoT 中心连接字符串：

    ```csharp
    static ServiceClient serviceClient;
    static string connectionString = "{iot hub connection string}";
    ```

1. 将以下方法添加到 **Program** 类：

    ```csharp
    private async static void ReceiveFileUploadNotificationAsync()
    {
        var notificationReceiver = serviceClient.GetFileNotificationReceiver();
        Console.WriteLine("\nReceiving file upload notification from service");
        while (true)
        {
            var fileUploadNotification = await notificationReceiver.ReceiveAsync();
            if (fileUploadNotification == null) continue;
            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("Received file upload notification: {0}", 
              string.Join(", ", fileUploadNotification.BlobName));
            Console.ResetColor();
            await notificationReceiver.CompleteAsync(fileUploadNotification);
        }
    }
    ```

    请注意，此接收模式与用于从设备应用接收云到设备消息的模式相同。

1. 最后，将 Main 方法中的行替换为以下行：

    ```csharp
    Console.WriteLine("Receive file upload notifications\n");
    serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
    ReceiveFileUploadNotificationAsync();
    Console.WriteLine("Press Enter to exit\n");
    Console.ReadLine();
    ```
    
## <a name="run-the-applications"></a>运行应用程序

现在已准备就绪，可以运行应用程序了。

1. 首先，运行服务应用，以接收来自 IoT 中心的文件上传通知。 在 ReadFileUploadNotification 文件夹中的命令提示符下，运行以下命令： 

    ```cmd/sh
    dotnet restore
    dotnet run
    ```
    
    应用启动并等待来自 IoT 中心的文件上传通知：

    ```cmd/sh
    Receive file upload notifications


    Receiving file upload notification from service
    Press Enter to exit
    ```



1. 接下来，运行设备应用以将文件上传到 Azure 存储。 打开新命令提示符，并将文件夹更改为你在其中扩展 Azure IoT C# 示例的文件夹下的 azure-iot-samples-csharp-master\iot-hub\Samples\device\FileUploadSample。 运行以下命令。 将第二个命令中的 `{Your device connection string}` 占位符值替换为之前在[在 IoT 中心内注册新设备](#register-a-new-device-in-the-iot-hub)中复制的设备连接字符串。

    ```cmd/sh
    dotnet restore
    dotnet run --p "{Your device connection string}"
    ```
    
    上传完成后，设备应用会有以下输出：
    
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
    
1. 请注意，服务应用显示它已收到文件上传通知：

    ```cmd/sh
    Receive file upload notifications
    
    
    Receiving file upload notification from service
    Press Enter to exit
    
    Received file upload notification: myDeviceId/TestPayload.txt
    ```

## <a name="verify-the-file-upload"></a>验证文件上传

可以使用门户查看所配置的存储容器中上传的文件：

1. 在 Azure 门户中导航到存储帐户。
1. 在存储帐户的左侧窗格中，选择“容器”。
1. 选择已将文件上传到其中的容器。
1. 选择与设备同名的文件夹。
1. 选择已将文件上传到其中的 Blob。 在本文中，它是名为 TestPayload.txt 的 Blob。  

    :::image type="content" source="./media/iot-hub-csharp-csharp-file-upload/view-uploaded-file.png" alt-text="屏幕截图显示在 Azure 门户中选择已上传的文件。":::

1. 在打开的页面上查看 Blob 属性。 可以选择“下载”，以将该文件下载到本地并查看其内容。

## <a name="next-steps"></a>后续步骤

在本教程中，你已学习了如何使用 IoT 中心的文件上传功能来简化从设备进行的文件上传。 可以继续通过以下文章了解此功能：

* [IoT 中心文件上传的概述](iot-hub-devguide-file-upload.md)

* [配置 IoT 中心文件上传](iot-hub-configure-file-upload.md)

* [Azure Blob 存储文档](/azure/storage/blobs/storage-blobs-introduction)

* [Azure Blob 存储 API 参考](/azure/storage/blobs/reference)

* [Azure IoT SDK](iot-hub-devguide-sdks.md)