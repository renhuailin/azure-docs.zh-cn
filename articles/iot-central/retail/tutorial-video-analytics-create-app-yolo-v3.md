---
title: 教程 - 在 Azure IoT Central 中创建视频分析 - 对象和运动检测应用程序 (YOLO v3)
description: 本教程演示如何在 IoT Central 中创建视频分析应用程序。 你将创建该应用程序，对其进行自定义，然后将其连接到其他 Azure 服务。 本教程使用 YOLO v3 实时对象检测系统。
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
author: KishorIoT
ms.author: nandab
ms.date: 09/01/2021
ms.openlocfilehash: 1517a2312751574e3d590c0fc27636c994e8c12c
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2021
ms.locfileid: "123479915"
---
# <a name="tutorial-create-a-video-analytics---object-and-motion-detection-application-in-azure-iot-central-yolo-v3"></a>教程：在 Azure IoT Central 中创建视频分析 - 对象和运动检测应用程序 (YOLO v3)

了解如何使用 IoT Central 的“视频分析 - 对象和运动检测”应用程序模板、Azure IoT Edge 设备、Azure 媒体服务以及 YOLO v3 实时对象和运动检测系统创建视频分析应用程序。 该解决方案使用零售商店来说明如何满足监视监控摄像头的常见业务需求， 并在视频源中使用自动对象检测来快速识别和查找感兴趣的事件。

> [!TIP]
> 若要使用 OpenVINO&trade; 而不是 YOLO v3 进行对象和运动检测，请参阅[教程：在 Azure IoT Central 中创建视频分析 - 对象和运动检测应用程序 (OpenVINO&trade;)](tutorial-video-analytics-create-app-openvino.md)。

[!INCLUDE [iot-central-video-analytics-part1](../../../includes/iot-central-video-analytics-part1.md)]

- [Scratchpad.txt](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/Scratchpad.txt) - 此文件可帮助你在浏览这些教程时，记录所需的各种配置选项。
- [deployment.amd64.json](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/deploymentManifests/deployment.amd64.json)
- [LvaEdgeGatewayDcm.json](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/LvaEdgeGatewayDcm.json)
- [state.json](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/state.json) - 当你打算在第二个教程中使用 Intel NUC 设备时，只需下载此文件。

> [!NOTE]
> GitHub 存储库还包括 LvaEdgeGatewayModule 和 lvaYolov3 IoT Edge 模块的源代码 。 有关使用源代码的详细信息，请参阅[生成 LVA Gateway 模块](tutorial-video-analytics-build-module.md)。

[!INCLUDE [iot-central-video-analytics-part2](../../../includes/iot-central-video-analytics-part2.md)]

## <a name="edit-the-deployment-manifest"></a>编辑部署清单

使用部署清单部署 IoT Edge 模块。 在 IoT Central 中，可以将清单作为设备模板导入，然后让 IoT Central 管理模块部署。

若要准备部署清单，请执行以下操作：

1. 使用文本编辑器打开保存在 lva-configuration 文件夹中的 deployment.amd64.json 文件 。

1. 找到 `LvaEdgeGatewayModule` 设置，并确保映像名称如以下代码片段所示：

    ```json
    "LvaEdgeGatewayModule": {
        "settings": {
            "image": "mcr.microsoft.com/lva-utilities/lva-edge-iotc-gateway:1.0-amd64",
    ```

1. 在 `LvaEdgeGatewayModule` 部分的 `env` 节点中添加媒体服务帐户的名称。 你已在 scratchpad.txt 文件中记下了该媒体服务帐户名称：

    ```json
    "env": {
        "lvaEdgeModuleId": {
            "value": "lvaEdge"
        },
        "amsAccountName": {
            "value": "<YOUR_AZURE_MEDIA_SERVICES_ACCOUNT_NAME>"
        }
    }
    ```

1. 该模板不会在 IoT Central 中公开这些属性，因此需要将媒体服务配置值添加到部署清单。 找到 `lvaEdge` 模块，并将占位符替换为在创建媒体服务帐户时在 scratchpad.txt 文件中记下的值。

    `azureMediaServicesArmId` 是在创建媒体服务帐户时在 scratchpad.txt 文件中记下的资源 ID。

    下表显示了应在部署清单中使用的 scratchpad.txt 文件中的“连接到媒体服务 API (JSON)”的值：

    | 部署清单       | 面板  |
    | ------------------------- | ----------- |
    | aadTenantId               | AadTenantId |
    | aadServicePrincipalAppId  | AadClientId |
    | aadServicePrincipalSecret | AadSecret   |

    > [!CAUTION]
    > 使用上表以确保在部署清单中添加了正确的值，否则设备将无法工作。

    ```json
    {
        "lvaEdge":{
        "properties.desired": {
            "applicationDataDirectory": "/var/lib/azuremediaservices",
            "azureMediaServicesArmId": "[Resource ID from scratchpad]",
            "aadTenantId": "[AADTenantID from scratchpad]",
            "aadServicePrincipalAppId": "[AadClientId from scratchpad]",
            "aadServicePrincipalSecret": "[AadSecret from scratchpad]",
            "aadEndpoint": "https://login.microsoftonline.com",
            "aadResourceId": "https://management.core.windows.net/",
            "armEndpoint": "https://management.azure.com/",
            "diagnosticsEventsOutputName": "AmsDiagnostics",
            "operationalMetricsOutputName": "AmsOperational",
            "logCategories": "Application,Event",
            "AllowUnsecuredEndpoints": "true",
            "TelemetryOptOut": false
            }
        }
    }
    ```

1. 保存更改。

本教程将解决方案配置为使用 YOLO v3 模块进行对象和运动检测。 以下代码片段显示了模块的配置：

```json
"lvaYolov3": {
    "settings": {
        "image": "mcr.microsoft.com/lva-utilities/yolov3-onnx:1.0"
    },
    "type": "docker",
    "status": "running",
    "restartPolicy": "always",
    "version": "1.0"
}
```

[!INCLUDE [iot-central-video-analytics-part3](../../../includes/iot-central-video-analytics-part3.md)]

### <a name="replace-the-manifest"></a>替换清单

在“LVA Edge 网关 v2”页上，选择“+ 替换清单” 。

:::image type="content" source="./media/tutorial-video-analytics-create-app-yolo-v3/replace-manifest.png" alt-text="替换清单":::

导航到 lva-configuration 文件夹，然后选择之前编辑的 deployment.amd64.json 。 选择“上传”。 验证完成后，选择“替换”。

[!INCLUDE [iot-central-video-analytics-part4](../../../includes/iot-central-video-analytics-part4.md)]

## <a name="clean-up-resources"></a>清理资源

如果应用程序使用完毕，可以删除创建的所有资源，如下所示：

1. 在 IoT Central 应用程序中，请导航至“管理”部分中的“你的应用程序”页面 。 然后选择“删除”。
1. 在 Azure 门户中，删除“lva-rg”资源组。
1. 在本地计算机上，停止“amp-viewer”Docker 容器。

## <a name="next-steps"></a>后续步骤

现在你已使用“视频分析 - 对象和运动检测”应用程序模板创建了一个 IoT Central 应用程序，为网关设备创建了一个设备模板，并向应用程序中添加了一个网关设备。

如果要使用运行云 VM 的 IoT Edge 模块和模拟视频流试用视频分析 - 对象和运动检测应用程序，请：

> [!div class="nextstepaction"]
> [创建用于视频分析的 IoT Edge 实例 (Linux VM)](tutorial-video-analytics-iot-edge-vm.md)

如果要使用运行真实设备的 IoT Edge 模块和真实 ONVIF 相机试用视频分析 - 对象和运动检测应用程序，请：

> [!div class="nextstepaction"]
> [创建用于视频分析的 IoT Edge 实例 (Intel NUC)](tutorial-video-analytics-iot-edge-nuc.md)
