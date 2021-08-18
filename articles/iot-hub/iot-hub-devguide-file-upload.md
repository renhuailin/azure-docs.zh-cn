---
title: 了解 Azure IoT 中心文件上传 | Microsoft Docs
description: 开发人员指南 - 使用 IoT 中心的文件上传功能，可将文件从设备上传到 Azure 存储 blob 容器。
author: robinsh
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/07/2018
ms.custom:
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: dd7adc3a1fdb45255bd13ee279d66e64bdf4c1d1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121728779"
---
# <a name="upload-files-with-iot-hub"></a>使用 IoT 中心上传文件

在许多情况下，我们无法轻松地将设备发送的数据映射为 IoT 中心容易接受的相对较小的设备到云消息。 例如：
* 大型图像文件
* 视频文件
* 以高频率采样的振动数据
* 某种形式的预处理数据

当你需要从设备上传此类文件时，仍可以借助 IoT 中心的安全性和可靠性。 IoT 中心本身不中转消息，但是 IoT 中心充当关联 Azure 存储帐户的调度程序。 设备请求来自 IoT 中心的存储令牌，该令牌特定于设备要上传的文件。 设备使用 SAS URI 将文件上传到存储空间，上传完成后，设备将完成通知发送到 IoT 中心。 IoT 中心检查文件上传是否已完成。

[!INCLUDE [iot-hub-include-x509-ca-signed-file-upload-support-note](../../includes/iot-hub-include-x509-ca-signed-file-upload-support-note.md)]

### <a name="when-to-use"></a>何时使用

使用文件上传，发送间歇性连接的设备上传的媒体文件和大型遥测批文件（或者是压缩后的文件，以节省带宽）。 如果在使用报告属性、设备到云消息或文件上传方面有任何疑问，请参阅[设备到云通信指南](iot-hub-devguide-d2c-guidance.md)。

## <a name="associate-an-azure-storage-account-with-iot-hub"></a>将 Azure 存储帐户与 IoT 中心相关联

你需要拥有与 IoT 中心关联的 Azure 存储帐户。 

若要了解如何使用门户创建 Azure 存储帐户，请参阅[创建存储帐户](../storage/common/storage-account-create.md)。 

此外，你也可以使用 [IoT 中心资源提供程序 REST API](/rest/api/iothub/iothubresource) 以编程方式创建一个存储帐户。 

将 Azure 存储帐户与 IoT 中心相关联时，IoT 中心会生成一个 SAS URI。 设备可以使用此 SAS URI 安全地将文件上传到 Blob 容器。

## <a name="create-a-container"></a>创建容器

 通过门户创建 Blob 容器的步骤：

1. 在存储帐户左侧窗格中的“数据存储”下，选择“容器”。
1. 在“容器”边栏选项卡中，选择“+ 容器”。
1. 在随即打开的“新建容器”窗格中，为容器提供一个名称，然后选择“创建”。

创建容器后，按照[使用 Azure 门户配置文件上传](iot-hub-configure-file-upload.md)中的说明操作。 确保有一个 Blob 容器与你的 IoT 中心关联并且文件通知已启用。

你还可以使用 [IoT 中心资源提供程序 REST API](/rest/api/iothub/iothubresource) 为你的 IoT 中心创建一个与存储关联的容器。

## <a name="file-upload-using-an-sdk"></a>使用 SDK 上传文件

以下操作指南逐步介绍了多种 SDK 语言的文件上传过程。 这些指南展示了如何使用 Azure 门户将存储帐户与 IoT 中心相关联。 它们还包含代码片段或参考示例，指导你完成上传过程。

* [.NET](iot-hub-csharp-csharp-file-upload.md)
* [Java](iot-hub-java-java-file-upload.md)
* [Node.js](iot-hub-node-node-file-upload.md)
* [Python](iot-hub-python-python-file-upload.md)

> [!NOTE]
> [Azure IoT SDK](iot-hub-devguide-sdks.md) 自动处理检索共享访问签名 URI、上传文件和通知 IoT 中心已完成上传等操作。 如果防火墙阻止对 Blob 存储终结点的访问，但却允许访问 IoT 中心终结点，那么文件上传过程将失败，并针对 IoT C# 设备 SDK 显示以下错误：
>
> `---> System.Net.Http.HttpRequestException: A connection attempt failed because the connected party did not properly respond after a period of time, or established connection failed because connected host has failed to respond`
>
> 若要使文件上传功能正常运行，设备必须可以同时访问 IoT 中心终结点和 Blob 存储终结点。
> 


## <a name="initialize-a-file-upload-rest"></a>初始化文件上传 (REST)

你可以使用 REST API（而不是其中一个 SDK）来上传文件。 IoT 中心有一个终结点，专供设备在上传文件时请求用于存储的 SAS URI。 为了启动文件上传过程，设备会使用以下 JSON 正文向 `{iot hub}.azure-devices.net/devices/{deviceId}/files` 发送 POST 请求：

```json
{
    "blobName": "{name of the file for which a SAS URI will be generated}"
}
```

IoT 中心返回以下数据，供设备用来上传文件：

```json
{
    "correlationId": "somecorrelationid",
    "hostName": "yourstorageaccount.blob.core.windows.net",
    "containerName": "testcontainer",
    "blobName": "test-device1/image.jpg",
    "sasToken": "1234asdfSAStoken"
}
```

### <a name="deprecated-initialize-a-file-upload-with-a-get"></a>已弃用：使用 GET 初始化文件上传

> [!NOTE]
> 本部分介绍已弃用的功能，这些功能用于从 IoT 中心接收 SAS URI。 使用前面所述的 POST 方法。

IoT 中心有两个 REST 终结点支持文件上传，一个用于获取存储的 SAS URI，另一个用于通知 IoT 中心已完成上传。 设备通过在 `{iot hub}.azure-devices.net/devices/{deviceId}/files/{filename}` 向 IoT 中心发送 GET 来启动文件上传过程。 IoT 中心返回：

* 特定于要上传的文件的 SAS URI。

* 上传完成后要使用的相关 ID。

## <a name="notify-iot-hub-of-a-completed-file-upload-rest"></a>通知 IoT 中心已完成文件上传 (REST)

设备使用 Azure 存储 SDK 将文件上传到存储。 上传完成后，设备会使用以下 JSON 正文向 `{iot hub}.azure-devices.net/devices/{deviceId}/files/notifications` 发送 POST 请求：

```json
{
    "correlationId": "{correlation ID received from the initial request}",
    "isSuccess": bool,
    "statusCode": XXX,
    "statusDescription": "Description of status"
}
```

`isSuccess` 的值为布尔值，指示文件是否已成功上传。 `statusCode` 的状态代码表示将文件上传到存储时的状态，`statusDescription` 对应于 `statusCode`。

## <a name="reference-topics"></a>参考主题：

以下参考主题详细介绍了如何从设备上传文件。

### <a name="file-upload-notifications"></a>文件上传通知

（可选）当设备通知 IoT 中心某个上传完成后，IoT 中心将生成一条通知消息。 此消息包含文件的名称和存储位置。

如 [终结点](iot-hub-devguide-endpoints.md)中所述，IoT 中心通过面向服务的终结点 (**/messages/servicebound/fileuploadnotifications**) 以消息的形式传递文件上传通知。 文件上传通知的接收语义与云到设备消息的接收语义相同，并且具有相同的[消息生命周期](iot-hub-devguide-messages-c2d.md#the-cloud-to-device-message-life-cycle)。 从文件上传通知终结点检索到的每条消息都是具有以下属性的 JSON 记录：

| 属性 | 说明 |
| --- | --- |
| EnqueuedTimeUtc |指示通知创建时间的时间戳。 |
| DeviceId |上传文件的设备的 **DeviceId**。 |
| BlobUri |已上传文件的 URI。 |
| BlobName |已上传文件的名称。 |
| LastUpdatedTime |指示文件更新时间的时间戳。 |
| BlobSizeInBytes |已上传文件的大小。 |

**示例**。 此示例显示文件上传通知消息的正文。

```json
{
    "deviceId":"mydevice",
    "blobUri":"https://{storage account}.blob.core.windows.net/{container name}/mydevice/myfile.jpg",
    "blobName":"mydevice/myfile.jpg",
    "lastUpdatedTime":"2016-06-01T21:22:41+00:00",
    "blobSizeInBytes":1234,
    "enqueuedTimeUtc":"2016-06-01T21:22:43.7996883Z"
}
```

### <a name="file-upload-notification-configuration-options"></a>文件上传通知配置选项

每个 IoT 中心都具有针对文件上传通知的以下配置选项：

| 属性 | 说明 | 范围和默认值 |
| --- | --- | --- |
| **enableFileUploadNotifications** |控制是否将文件上传通知写入文件通知终结点。 |布尔型。 默认值：True。 |
| **fileNotifications.ttlAsIso8601** |文件上传通知的默认 TTL。 |ISO_8601 间隔上限为 48 小时（下限为 1 分钟）。 默认值：1 小时。 |
| **fileNotifications.lockDuration** |文件上传通知队列的锁定持续时间。 |5 到 300 秒（最小为 5 秒）。 默认值：60 秒。 |
| **fileNotifications.maxDeliveryCount** |文件上传通知队列的最大传递计数。 |1 到 100。 默认值：100。 |

可以使用 Azure 门户、Azure CLI 或 PowerShell 在 IoT 中心设置这些属性。 若要了解如何操作，请参阅[配置文件上传](iot-hub-configure-file-upload.md)下的主题。

## <a name="additional-reference-material"></a>其他参考资料

IoT 中心开发人员指南中的其他参考主题包括：

* [IoT 中心终结点](iot-hub-devguide-endpoints.md)介绍了用于运行时和管理操作各种 IoT 中心终结点。

* [限制和配额](iot-hub-devguide-quotas-throttling.md)介绍了适用于 IoT 中心服务的配额和限制行为。

* [Azure IoT 设备和服务 SDK](iot-hub-devguide-sdks.md) 列出了开发与 IoT 中心交互的设备和服务应用时可使用的各种语言 SDK。

* [IoT 中心查询语言](iot-hub-devguide-query-language.md)介绍了可用来从 IoT 中心检索设备孪生和作业相关信息的查询语言。

* [IoT 中心 MQTT 支持](iot-hub-mqtt-support.md)提供了有关 IoT 中心对 MQTT 协议的支持的详细信息。

## <a name="next-steps"></a>后续步骤

现在，你已了解了如何使用 IoT 中心从设备上传文件，接下来可以根据兴趣查看以下 IoT 中心开发人员指南主题：

* [管理 IoT 中心中的设备标识](iot-hub-devguide-identity-registry.md)

* [控制 IoT 中心的访问权限](iot-hub-devguide-security.md)

* [使用设备孪生来同步状态和配置](iot-hub-devguide-device-twins.md)

* [在设备上调用直接方法](iot-hub-devguide-direct-methods.md)

* [在多台设备上计划作业](iot-hub-devguide-jobs.md)

要尝试本文中介绍的一些概念，请参阅以下 IoT 中心教程：

* [如何通过 IoT 中心将文件从设备上传到云中](iot-hub-csharp-csharp-file-upload.md)
