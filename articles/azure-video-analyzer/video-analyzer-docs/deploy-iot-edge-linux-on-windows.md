---
title: 部署到 IoT Edge for Linux on Windows - Azure
description: 本文提供了有关如何部署到 IoT Edge for Linux on Windows 设备的指导。
ms.topic: how-to
ms.date: 05/25/2021
ms.openlocfilehash: 2907318f7d1c49c4aea247880a9880e724b46ca6
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2021
ms.locfileid: "110385114"
---
# <a name="deploy-to-an-iot-edge-for-linux-on-windows-eflow-device"></a>部署到 IoT Edge for Linux on Windows (EFLOW) 设备

本文介绍如何在具有 [IoT Edge for Linux on Windows (EFLOW)](../../iot-edge/iot-edge-for-linux-on-windows.md) 的边缘设备上部署 Azure 视频分析器。 完成本文档中的步骤后，你将能够运行[管道](pipeline.md)，该管道可检测视频中的移动并将此类事件发送到云中的 IoT 中心。 然后，你可以切换出高级方案的管道，并将 Azure 视频分析器功能引入基于 Windows 的 IoT Edge 设备。

## <a name="prerequisites"></a>先决条件 

* 具有活动订阅的 Azure 帐户。 如果没有帐户，可[免费创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 开发计算机上的 [Visual Studio Code](https://code.visualstudio.com/)。 请确保具有 [Azure IoT Tools 扩展](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)。
* 请阅读[什么是 EFLOW](../../iot-edge/iot-edge-for-linux-on-windows.md)。

## <a name="deployment-steps"></a>部署步骤

下方内容描述了文档的总体流程，只需 5 个简单的步骤即可在具有 EFLOW 的 Windows 设备上运行 Azure 视频分析器：

![IoT Edge for Linux on Windows (EFLOW) 示意图](./media/deploy-iot-edge-linux-on-windows/eflow.png)

1. 在 Windows 设备上[安装 EFLOW](../../iot-edge/how-to-install-iot-edge-on-windows.md)。 

    1. 如果使用的是 Windows 电脑，则在 [Windows Admin Center](/windows-server/manage/windows-admin-center/overview) 起始页的连接列表下，你会看到一个本地主机连接，该连接表示运行 Windows Admin Center 的电脑。 
    1. 你管理的任何其他服务器、电脑或群集也会显示在此处。
    1. 你可以使用 Windows Admin Center 在本地设备或远程托管设备上安装和管理 Azure EFLOW。 在本指南中，本地主机连接充当用于部署 Azure IoT Edge for Linux on Windows 的目标设备。 因此，你会看到 localhost 也被列为 IoT Edge 设备。

    ![部署步骤 - Windows Admin Center](./media/deploy-iot-edge-linux-on-windows/windows-admin-center.png) 
1. 单击 IoT Edge 设备与之连接，应会显示“概述”和“命令 Shell”选项卡。在“命令 shell”选项卡中，可以向边缘设备发出命令。

    ![部署步骤 - Azure IoT Edge 管理器](./media/deploy-iot-edge-linux-on-windows/azure-iot-edge-manager.png)
1. 转到命令外壳，然后键入以下命令：

    `bash -c "$(curl -sL https://aka.ms/ava-edge/prep_device)"`

    Azure 视频分析器模块使用非特权本地用户帐户在边缘设备上运行。 此外，它还需要某些本地文件夹来存储应用程序配置数据。 最后，对于本操作指南，我们将利用 [RTSP 模拟器](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555)，该模拟器将视频源实时中继到 AVA 模块进行分析。 此模拟器使用输入目录中的预录制视频文件作为输入。 

    上面使用的准备设备脚本会自动执行这些任务，因此，你可以运行一个命令，并通过无缝创建的特权拥有所有相关的输入和配置文件夹、视频输入文件以及用户帐户。 成功完成该命令后，应会显示边缘设备上创建的以下文件夹。 

    * `/home/localedgeuser/samples`
    * `/home/localedgeuser/samples/input`
    * `/var/lib/videoanalyzer`
    * `/var/media`

    请注意，/home/localedgeuser/samples/input 文件夹中的视频文件 (*.mkv) 会用作要分析的输入文件。 
1. 现在，你已设置边缘设备，将其注册到中心，并通过创建正确的文件夹结构成功运行，下一步是设置以下额外的 Azure 资源并部署 AVA 模块。 

    * 存储帐户
    * Azure 媒体服务帐户

    [![部署到 Azure](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)

    在模板中，当系统询问你是否需要边缘设备时，请选择“使用现有边缘设备”选项，因为你之前已创建设备和 IoT 中心。 在后续步骤中，系统还会提示你输入 IoT 中心名称和 IoT Edge 设备 ID。  
    
    ![使用现有设备](./media/deploy-iot-edge-linux-on-windows/use-existing-device.png) 

    完成后，可以重新登录到 IoT Edge 设备命令外壳并运行以下命令。

    **`sudo iotedge list`**

    应会看到边缘设备上已部署且正在运行以下四个模块。 请注意，资源创建脚本会将 AVA 模块与 IoT Edge 模块（edgeAgent 和 edgeHub）和 RTSP 模拟器模块一起部署，以提供模拟的 RTSP 视频源。
    
    ![已部署模块](./media/vscode-common-screenshots/avaedge-module.png)
1. 部署和设置模块后，即可在 EFLOW 上运行第一个 AVA 管道。 可以按如下所示运行简单的移动检测管道，并通过执行以下步骤来可视化结果：

    ![基于移动检测的视频分析器](./media/get-started-detect-motion-emit-events/motion-detection.svg)

    1. [配置](get-started-detect-motion-emit-events.md#prepare-to-monitor-the-modules) Azure IoT Tools 扩展。
    1. 设置 pipelineTopology，实例化 livePipeline，并通过这些[直接方法调用](get-started-detect-motion-emit-events.md#use-direct-method-calls)将其激活。
    1. 在中心上[观察结果](get-started-detect-motion-emit-events.md#observe-results)。
    1. 调用[清除方法](get-started-detect-motion-emit-events.md#deactivate-the-live-pipeline)。
    1. 不再需要资源时，即可将其删除。

        > [!IMPORTANT]
        > 未删除的资源仍可处于活动状态，并且会产生 Azure 费用。 请确保删除不打算使用的资源。
        
## <a name="next-steps"></a>后续步骤

* 尝试移动检测以及在云中录制相关视频。 按照[检测移动并录制视频剪辑](detect-motion-record-video-edge-devices.md)快速入门中的步骤进行操作。
* 运行[实时视频上的 AI](analyze-live-video-use-your-model-http.md#overview)（可以跳过先决条件设置，因为它已在上面完成）
* 使用 [VS Code 扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.live-video-analytics-edge)来查看其他管道。
* 使用支持 RTSP 的 [IP 相机](https://en.wikipedia.org/wiki/IP_camera)，而不是使用 RTSP 模拟器。 可以在 [ONVIF 一致性](https://www.onvif.org/conformant-products/)产品页上找到支持 RTSP 的 IP 相机。 查找符合配置文件 G、S 或 T 的设备。
