---
author: fvneerden
ms.service: azure-video-analyzer
ms.topic: include
ms.date: 05/05/2021
ms.author: faneerde
ms.openlocfilehash: ac9945502459345deb60794821a48293c0b4c346
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2021
ms.locfileid: "110385134"
---
部署清单定义要部署到边缘设备的模块。 它还定义了这些模块的配置设置。

请按照以下步骤从模板文件生成清单，然后将其部署到边缘设备。

1.  打开 Visual Studio Code。
1.  在“AZURE IOT 中心”窗格旁，选择“更多操作”图标以设置 IoT 中心连接字符串 。 可以从 src/cloud-to-device-console-app/appsettings.json 文件复制该字符串。

    ![设置 IoT 连接字符串](../../../media/vscode-common-screenshots/set-connection-string.png)

    > [!NOTE]
    > 系统可能会要求你提供 IoT 中心的内置终结点信息。 若要获取此信息，请在 Azure 门户中导航到 IoT 中心，然后在左侧导航窗格中查找“内置终结点”选项。 单击此处，在“与事件中心兼容的终结点”部分下查找“与事件中心兼容的终结点” 。 复制并使用框中的文本。 终结点将如下所示： <br/>`Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>`

1.  右键单击“src/edge/deployment.template.json”并选择“生成 IoT Edge 部署清单”。

    ![生成 IoT Edge 部署清单](../../../media/quickstarts/generate-iot-edge-deployment-manifest.png)

    此操作应在 src/edge/config 文件夹中创建一个名为“deployment.amd64.json”的清单文件 。

1.  右键单击“src/edge/config/deployment.amd64.json”，选择“为单个设备创建部署”，然后选择边缘设备的名称 。

    ![创建单个设备的部署](../../../media/quickstarts/create-deployment-single-device.png)

1.  如果系统提示你选择 IoT 中心设备，请从下拉菜单中选择“avasample-iot-edge-device”。
1.  大约 30 秒后，在该窗口的左下角刷新 Azure IoT 中心。 边缘设备现在显示以下已部署的模块：

    - Azure 视频分析器（模块名称为 `avaedge`）
    - 实时流式处理协议 (RTSP) 模拟器（模块名称为 `rtspsim`）
