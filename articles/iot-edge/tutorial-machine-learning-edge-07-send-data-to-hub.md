---
title: 教程：通过透明网关发送设备数据 - Azure IoT Edge 上的机器学习
description: 本教程介绍如何使用开发计算机作为模拟的 IoT Edge 设备，通过配置为透明网关的设备将数据发送到 IoT 中心。
author: kgremban
ms.author: kgremban
ms.date: 6/30/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: devx-track-csharp
ms.openlocfilehash: fbf957dee80a50b8256925b751d3df5440acca96
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121740533"
---
# <a name="tutorial-send-data-via-transparent-gateway"></a>教程：通过透明网关发送数据

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

本文将再次使用开发 VM 作为模拟设备， 但不会直接将数据发送到 IoT 中心，而是通过该设备将数据发送到配置为透明网关的 IoT Edge 设备。

当模拟设备正在发送数据时，我们将监视 IoT Edge 设备的运行情况。 设备完成运行后，我们将查看存储帐户中的数据，以验证一切是否按预期方式工作。

此步骤通常由云或设备开发人员执行。

本教程的此部分介绍了如何：

> [!div class="checklist"]
>
> * 构建并运行叶设备。
> * 验证生成的数据是否存储在 Azure Blob 存储中。
> * 验证机器学习模型是否对设备数据进行了分类。

## <a name="prerequisites"></a>先决条件

有一系列教程介绍如何在 IoT Edge 上使用 Azure 机器学习，本文是其中的一篇。 这一系列中的每篇文章都环环相扣，后一篇以前一篇为基础。 如果你是直接转到本文的，请访问本系列的[第一篇文章](tutorial-machine-learning-edge-01-intro.md)。

## <a name="review-device-harness"></a>查看设备的装备

重复使用 [DeviceHarness 项目](tutorial-machine-learning-edge-03-generate-data.md)来模拟下游设备（或叶设备）。 连接到透明网关需要做好两项额外的准备：

* 注册证书，使下游 IoT 设备信任 IoT Edge 运行时使用的证书颁发机构。 在我们的事例中，下游设备为开发 VM。
* 将边缘网关的完全限定域名 (FQDN) 添加到设备连接字符串。

请查看代码，了解如何完成这两项操作。

1. 在开发计算机上打开 Visual Studio Code。

1. 使用“文件” > “打开文件夹...”打开 C:\\source\\IoTEdgeAndMlSample\\DeviceHarness。 

1. 查看 Program.cs 中的 InstallCertificate() 方法。

1. 请注意，如果代码找到了证书路径，则会调用 CertificateManager.InstallCACert 方法在计算机上安装证书。

1. 现在请查看 TurbofanDevice 类中的 GetIotHubDevice 方法。

1. 当用户使用“-g”选项指定网关的 FQDN 时，该值将作为 `gatewayFqdn` 变量传递到此方法，并附加到设备连接字符串。

   ```csharp
   connectionString = $"{connectionString};GatewayHostName={gatewayFqdn.ToLower()}";
   ```

## <a name="build-and-run-leaf-device"></a>生成并运行叶设备

1. 在 DeviceHarness 项目仍在 Visual Studio Code 中打开时，生成项目。 从“终端”菜单中，选择“运行生成任务”，然后选择“生成”  。

1. 通过导航到 Azure 门户中的 IoT Edge 设备 (Linux VM) 并从“概述”页复制“DNS 名称”值，找到边缘网关的完全限定域名 (FQDN)。

1. 启动 IoT 设备 (Linux VM)（如果尚未运行）。

1. 打开 Visual Studio Code 终端。 从“终端”菜单选择“新建终端”，并运行以下命令，将 `<edge_device_fqdn>` 替换为从 IoT Edge 设备 (Linux VM) 复制的 DNS 名称 ：

   ```cmd
   dotnet run -- --gateway-host-name "<edge_device_fqdn>" --certificate C:\edgecertificates\certs\azure-iot-test-only.root.ca.cert.pem --max-devices 1
   ```

1. 应用程序会尝试将证书安装到开发计算机。 安装证书时，请接受安全警告。

1. 出现 IoT 中心连接字符串的提示时，请在 Azure IoT 中心设备面板中单击省略号 (…)，然后选择“复制 IoT 中心连接字符串” 。 将值粘贴到终端中。

1. 此时会显示如下所示的输出：

   ```output
   Found existing device: Client_001
   Using device connection string: HostName=<your hub>.azure-devices.net;DeviceId=Client_001;SharedAccessKey=xxxxxxx; GatewayHostName=iotedge-xxxxxx.<region>.cloudapp.azure.com
   Device: 1 Message count: 50
   Device: 1 Message count: 100
   Device: 1 Message count: 150
   Device: 1 Message count: 200
   Device: 1 Message count: 250
   ```

   请注意“GatewayHostName”已添加到设备连接字符串，这会导致设备通过 IoT Edge 透明网关来与 IoT 中心通信。

## <a name="check-output"></a>检查输出

### <a name="iot-edge-device-output"></a>IoT Edge 设备输出

随时可以通过查看 IoT Edge 设备来观察 avroFileWriter 模块的输出。

1. 通过 SSH 连接到 IoT Edge 虚拟机。

1. 查找已写入到磁盘中的文件。

   ```bash
   find /data/avrofiles -type f
   ```

1. 该命令的输出如以下示例所示：

   ```output
   /data/avrofiles/2019/4/18/22/10.avro
   ```

   根据运行所花费的时间，可能会出现多个文件。

1. 请注意时间戳。 一旦上次修改时间超过 10 分钟（查看 avroFileWriter 模块中 uploader.py 内的 MODIFIED\_FILE\_TIMEOUT），avroFileWriter 模块就会将文件上传到云中。

1. 10 分钟过后，该模块应会上传文件。 如果上传成功，它会从磁盘中删除文件。

### <a name="azure-storage"></a>Azure 存储

通过查看预期要将数据路由到的存储帐户，来观察叶设备发送数据的结果。

1. 在开发计算机上打开 Visual Studio Code。

1. 在资源管理器窗口中的“AZURE 存储”窗格上，浏览树形结构以找到你的存储帐户。

1. 展开“Blob 容器”节点。

1. 根据我们在本教程的前面部分所做的工作，预期 **ruldata** 容器应该包含带有 RUL 的消息。 展开“ruldata”节点。

1. 你将看到一个或多个类似于 `<IoT Hub Name>/<partition>/<year>/<month>/<day>/<hour>/<minute>` 的 Blob 文件。

1. 右键单击某个文件并选择“下载 Blob”，以将文件保存到开发计算机。

1. 接下来，展开“uploadturbofanfiles”节点。 在前一篇文章中，我们已将此位置设置为 avroFileWriter 模块上传的文件的目标。

1. 右键单击文件并选择“下载 Blob”，以将其保存到开发计算机。

### <a name="read-avro-file-contents"></a>读取 Avro 文件内容

我们提供了一个简单的命令行实用工具用于读取 Avro 文件，以及返回该文件中的消息的 JSON 字符串。 在本部分，我们将安装并运行该实用工具。

1. 在 Visual Studio Code 中打开终端（选择“终端” > “新建终端”）。 

1. 安装 hubavroreader：

   ```cmd
   pip install c:\source\IoTEdgeAndMlSample\HubAvroReader
   ```

1. 使用 hubavroreader 读取从 **ruldata** 下载的 Avro 文件。

   ```cmd
   hubavroreader <avro file with ath> | more
   ```

1. 可以看到，消息正文符合我们的预期，其中包含设备 ID 和预测的 RUL。

   ```json
   {
       "Body": {
           "ConnectionDeviceId": "Client_001",
           "CorrelationId": "3d0bc256-b996-455c-8930-99d89d351987",
           "CycleTime": 1.0,
           "PredictedRul": 170.1723693909444
       },
       "EnqueuedTimeUtc": "<time>",
       "Properties": {
           "ConnectionDeviceId": "Client_001",
           "CorrelationId": "3d0bc256-b996-455c-8930-99d89d351987",
           "CreationTimeUtc": "01/01/0001 00:00:00",
           "EnqueuedTimeUtc": "01/01/0001 00:00:00"
       },
       "SystemProperties": {
           "connectionAuthMethod": "{\"scope\":\"module\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
           "connectionDeviceGenerationId": "636857841798304970",
           "connectionDeviceId": "aaTurbofanEdgeDevice",
           "connectionModuleId": "turbofanRouter",
           "contentEncoding": "utf-8",
           "contentType": "application/json",
           "correlationId": "3d0bc256-b996-455c-8930-99d89d351987",
           "enqueuedTime": "<time>",
           "iotHubName": "mledgeiotwalkthroughhub"
       }
   }
   ```

1. 运行同一命令并传递从 **uploadturbofanfiles** 下载的 Avro 文件。

1. 如我们所料，这些消息包含原始消息中的所有传感器数据和操作设置。 此数据可用于改进边缘设备上的 RUL 模型。

   ```json
   {
       "Body": {
           "CycleTime": 1.0,
           "OperationalSetting1": -0.0005000000237487257,
           "OperationalSetting2": 0.00039999998989515007,
           "OperationalSetting3": 100.0,
           "PredictedRul": 170.17236328125,
           "Sensor1": 518.6699829101562,
           "Sensor10": 1.2999999523162842,
           "Sensor11": 47.29999923706055,
           "Sensor12": 522.3099975585938,
           "Sensor13": 2388.010009765625,
           "Sensor14": 8145.31982421875,
           "Sensor15": 8.424599647521973,
           "Sensor16": 0.029999999329447746,
           "Sensor17": 391.0,
           "Sensor18": 2388.0,
           "Sensor19": 100.0,
           "Sensor2": 642.3599853515625,
           "Sensor20": 39.11000061035156,
           "Sensor21": 23.353700637817383,
           "Sensor3": 1583.22998046875,
           "Sensor4": 1396.8399658203125,
           "Sensor5": 14.619999885559082,
           "Sensor6": 21.610000610351562,
           "Sensor7": 553.969970703125,
           "Sensor8": 2387.9599609375,
           "Sensor9": 9062.169921875
       },
           "ConnectionDeviceId": "Client_001",
           "CorrelationId": "70df0c98-0958-4c8f-a422-77c2a599594f",
           "CreationTimeUtc": "0001-01-01T00:00:00+00:00",
           "EnqueuedTimeUtc": "<time>"
   }
   ```

## <a name="clean-up-resources"></a>清理资源

如果你打算继续学习本端到端教程中使用的资源，请等到学完之后再清理创建的资源。 否则，通过以下步骤删除它们：

1. 删除所创建的用于保存开发 VM、IoT Edge VM、IoT 中心、存储帐户和机器学习工作区服务的资源组（以及创建的资源：容器注册表、Application Insights、密钥保管库和存储帐户）。

1. 删除 [Azure Notebooks](https://notebooks.azure.com) 中的机器学习项目。

1. 如果在本地克隆了存储库，请关闭任何引用该本地存储库的 PowerShell 或 VS Code 窗口，然后删除存储库目录。

1. 如果在本地创建了证书，请删除文件夹 c:\\edgeCertificates。

## <a name="next-steps"></a>后续步骤

在本文中，我们已使用开发 VM 模拟了一个向 IoT Edge 设备发送传感器和操作数据的叶设备。 我们通过检查边缘设备的实时操作并查看上传到存储帐户的文件，验证了该设备上的模块可以路由、分类、持久保存和上传数据。

若要继续了解 IoT Edge 功能，请尝试下一教程：

> [!div class="nextstepaction"]
> [创建 IoT Edge 设备的层次结构](tutorial-nested-iot-edge.md?view=iotedge-2020-11&preserve-view=true)