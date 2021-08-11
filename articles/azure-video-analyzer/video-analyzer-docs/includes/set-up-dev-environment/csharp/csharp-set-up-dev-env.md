---
author: russell-cooks
ms.topic: include
ms.service: azure-video-analyzer
ms.date: 05/03/2021
ms.author: juliako
ms.openlocfilehash: 341730ee0ed64809cec3f60a0e28cb6367fa863f
ms.sourcegitcommit: cc099517b76bf4b5421944bd1bfdaa54153458a0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2021
ms.locfileid: "113559700"
---
### <a name="get-the-sample-code"></a>获取示例代码

1. 克隆 [AVA C# 示例存储库](https://github.com/Azure-Samples/video-analyzer-iot-edge-csharp)。
1. 启动 Visual Studio Code，然后打开下载的存储库所在的文件夹。
1. 在 Visual Studio Code 中，浏览到 src/cloud-to-device-console-app 文件夹，然后创建一个名为 appsettings.json 的文件。 该文件包含运行程序所需的设置。
1. 浏览到上述设置步骤中创建的存储帐户中的文件共享，并找到“deployment-output”文件共享下的“appsettings.json”文件。 单击该文件，然后点击“下载”按钮。 应在新的浏览器选项卡中打开内容，如下所示：

   ```
   {
       "IoThubConnectionString" : "HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX",
       "deviceId" : "avasample-iot-edge-device",
       "moduleId" : "avaedge"
   }
   ```

   借助 IoT 中心连接字符串，可以使用 Visual Studio Code 通过 Azure IoT 中心将命令发送到 Edge 模块。 将上述 JSON 复制到“src/cloud-to-device-console-app/appsettings.json”文件中。
1. 接下来，浏览到 src/edge 文件夹并创建一个名为 .env 的文件。 此文件包含 Visual Studio Code 用来将模块部署到边缘设备的属性。
1. 浏览到上述设置步骤中创建的存储帐户中的文件共享，并找到“deployment-output”文件共享下的“env.txt”文件。 单击该文件，然后点击“下载”按钮。 应在新的浏览器选项卡中打开内容，如下所示：

   ```
        SUBSCRIPTION_ID="<Subscription ID>"
        RESOURCE_GROUP="<Resource Group>"
        AVA_PROVISIONING_TOKEN="<Provisioning token>"
        VIDEO_INPUT_FOLDER_ON_DEVICE="/home/localedgeuser/samples/input"
        VIDEO_OUTPUT_FOLDER_ON_DEVICE="/var/media"
        APPDATA_FOLDER_ON_DEVICE="/var/lib/videoAnalyzer"
        CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"
        CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry password>"
   ```

   将 JSON 从“env.txt”文件复制到“src/edge/.env”文件中 。

### <a name="connect-to-the-iot-hub"></a>连接到 IoT 中心

1. 在 Visual Studio Code 中，通过选择左下角“Azure IoT 中心”窗格旁边的“更多操作”图标，设置 IoT 中心连接字符串 。 从 src/cloud-to-device-console-app/appsettings.json 文件复制该字符串。

    <!-- commenting out the image for now ![Set IoT Hub connection string]()./media/quickstarts/set-iotconnection-string.png-->
    [!INCLUDE [provide-builtin-endpoint](../../common-includes/provide-builtin-endpoint.md)]
1. 大约 30 秒后，在左下部分刷新 Azure IoT 中心。 你应会看到已部署以下模块的边缘设备 `avasample-iot-edge-device`：
    - Edge 中心（模块名称为“edgeHub”）
    - Edge 代理（模块名称为“edgeAgent”）
    - 视频分析器（模块名称为“avaedge”）
    - RTSP 模拟器（模块名称为“rtspsim”）

### <a name="prepare-to-monitor-the-modules"></a>准备监视模块

运行本快速入门或教程时，事件将发送到 IoT 中心。 若要查看这些事件，请执行以下步骤：

1. 在 Visual Studio Code 中打开“资源管理器”窗格，然后在左下角查找“Azure IoT 中心”。
1. 展开“设备”节点。
1. 右键单击 `avasample-iot-edge-device`，然后选择“开始监视内置事件终结点”。

    [!INCLUDE [provide-builtin-endpoint](../../common-includes/provide-builtin-endpoint.md)]
