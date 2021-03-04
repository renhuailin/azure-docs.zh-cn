---
title: 部署到 Windows 上的 Linux IoT Edge-Azure
description: 本文提供了有关如何部署到 Windows 设备上的 Linux IoT Edge 的指导。
ms.topic: how-to
ms.date: 02/18/2021
ms.openlocfilehash: b635070d6a4834da2c6d82cc768684b6cde9cd71
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102042175"
---
# <a name="deploy-to-an-iot-edge-for-linux-on-windows-eflow-device"></a>部署到 Windows 上的 IoT Edge Linux (EFLOW) 设备

本文介绍如何在 [Windows (EFLOW) 上的适用于 Linux IoT Edge ](https://docs.microsoft.com/azure/iot-edge/iot-edge-for-linux-on-windows?view=iotedge-2018-06)的边缘设备上部署实时视频分析。 完成本文档中的步骤后，你将能够运行一个 [media graph](media-graph-concept.md) 来检测视频中的动作，并将此类事件发送到云中的 IoT 中心。 然后，你可以在媒体图中切换到高级方案，并将实时视频分析功能带入基于 Windows 的 IoT Edge 设备上。

## <a name="prerequisites"></a>先决条件 

* 具有活动订阅的 Azure 帐户。 如果没有帐户，可[免费创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

    > [!NOTE]
    > 你将需要一个具有服务主体创建权限（所有者角色提供此权限）的 Azure 订阅。 如果你没有正确的权限，请与你的帐户管理员联系以授予你适当的权限。
* 开发计算机上的 [Visual Studio Code](https://code.visualstudio.com/)。 请确保具有 [Azure IoT Tools 扩展](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)。
* 请参阅 [什么是 EFLOW](https://aka.ms/AzEFLOW-docs)。

## <a name="deployment-steps"></a>部署步骤

下面描述了文档的整体流动，并在5个简单步骤中设置了，以便在具有 EFLOW 的 Windows 设备上运行实时视频分析：

:::image type="content" source="./media/deploy-iot-edge-linux-on-windows/eflow.png" alt-text="Windows (EFLOW 上适用于 Linux 的 IoT Edge) 示意图":::

1. 在 Windows 设备上[安装 EFLOW](https://aka.ms/AzEFLOW-install) 。 

    1. 如果你使用的是 Windows 电脑，则在 [Windows 管理中心](https://docs.microsoft.com/windows-server/manage/windows-admin-center/overview) 的 "开始" 页上，在 "连接" 列表中，你将看到一个本地主机连接，该连接表示运行 Windows 管理中心的 PC。 
    1. 你管理的任何其他服务器、电脑或群集也会显示在此处。
    1. 可以使用 Windows 管理中心在本地设备或远程托管设备上安装和管理 Azure EFLOW。 在本指南中，本地主机连接作为 Windows 上的 Linux Azure IoT Edge 的部署目标设备。 因此，你会看到 localhost 也作为 IoT Edge 设备列出。

    :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/windows-admin-center.png" alt-text="部署步骤-windows 管理中心":::
1. 单击 IoT Edge 设备连接到该设备，你应该会看到概述和命令外壳选项卡。可以在 "命令外壳" 选项卡中向边缘设备发出命令。
 
    :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/azure-iot-edge-manager.png" alt-text="部署步骤-Azure IoT Edge 管理器":::
1. 请在命令行界面中，键入以下命令：
    
    ```bash
    bash -c "$(curl -sL https://aka.ms/lva-edge/prep_device)"
    ```

    实时视频分析模块在边缘设备上运行，具有非特权 [本地用户帐户](deploy-iot-edge-device.md#create-and-use-local-user-account-for-deployment)。 此外，它还 [需要某些本地文件夹](deploy-iot-edge-device.md#granting-permissions-to-device-storage) 来存储应用程序配置数据。 最后，对于本操作方法指南，我们将利用一个 [RTSP 模拟器](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) ，将视频源实时中继到 LVA 模块进行分析。 此模拟器将输入目录中的预录制视频文件作为输入。 
    
    以上使用的 "准备设备" 脚本会自动执行这些任务，因此，你可以运行一个命令，并具有所有相关的输入和配置文件夹、视频输入文件以及具有无缝创建的特权的用户帐户。 成功完成该命令后，应会看到在边缘设备上创建了以下文件夹。 
    
    * `/home/lvaedgeuser/samples`
    * `/home/lvaedgeuser/samples/input`
    * `/var/lib/azuremediaservices`
    * `/var/media`
    
    请注意视频文件 ( "/home/lvaedgeuser/samples/input" 文件夹中的 .mkv) ，它充当要分析的输入文件。 
    
    :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/azure-iot-edge-manager-analysis.png" alt-text="分析":::
1. 安装边缘设备并将其注册到中心，并在创建正确的文件夹结构的情况下成功运行后，下一步是设置以下额外的 Azure 资源并部署 LVA 模块。 

    * 存储帐户
    * Azure 媒体服务帐户

    为此，我们建议使用 [实时视频分析资源安装脚本](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) 在 Azure 订阅中部署所需的资源。 为此，请执行下列步骤：

    1. 打开 [Azure Cloud Shell](https://ms.portal.azure.com/#cloudshell/)。

        :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/azure-resources-cloud-shell.png" alt-text="Cloud shell":::
    1. 如果你是第一次使用 Cloud Shell，系统将提示你选择一个订阅以创建存储帐户和 Microsoft Azure 文件存储共享。 选择“创建存储”，创建用于存储 Cloud Shell 会话信息的存储帐户。 此存储帐户不同于脚本将要创建的与 Azure 媒体服务帐户配合使用的帐户。
    1. 在 Cloud Shell 窗口左侧的下拉菜单中，选择“Bash”作为环境。

        :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/bash.png" alt-text="Bash 环境":::
    1. 运行以下命令。

        ```bash
        bash -c "$(curl -sL https://aka.ms/lva-edge/setup-resources-for-samples)"
        ```
        
        当系统提示选择你自己的边缘设备作为 IoT Edge 设备时，请确保选择 " **Y** "，因为你之前创建了设备和 IoT 中心。 系统还会提示输入 IoT 中心名称和 IoT Edge 设备 id。可以通过登录到 Azure 门户并单击 IoT 中心，然后转到 IoT 中心门户边栏选项卡上的 IoT Edge 选项来获取这两项。

        :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/iot-edge-devices.png" alt-text="请参阅 IoT Edge 设备":::

    完成后，可以重新登录 IoT Edge 设备命令行界面，并运行以下命令。
    
    `sudo iotedge list`
    
    你应看到以下四个模块已部署并在你的 edge 设备上运行。 请注意，资源创建脚本会将 LVA 模块与 IoT Edge 模块一起部署 (edgeAgent 和 edgeHub) ，并使用 RTSP 模拟器模块来提供模拟的 RTSP 视频源。
    
    :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/running.png" alt-text="查看状态":::
1. 部署和设置模块后，便可以在 EFLOW 上运行第一个 LVA 媒体图形了。 可以按如下所示运行简单的运动检测图形，并通过执行以下步骤来可视化结果：

    :::image type="content" source="./media/analyze-live-video/motion-detection.svg" alt-text="动作检测图形":::

    1. [配置](get-started-detect-motion-emit-events-quickstart.md#configure-the-azure-iot-tools-extension) Azure IoT 工具扩展。
    
        > [!Note]
        > 使用以下路径： `~/clouddrive/lva_byod/appsettings.json. - instead of ~/clouddrive/lva-sample/appsettings.json` 。
    1. 设置拓扑，实例化图形，并通过这些 [直接方法调用](get-started-detect-motion-emit-events-quickstart.md#use-direct-method-calls)激活它。
    1. [观察集线器上的结果](get-started-detect-motion-emit-events-quickstart.md#observe-results) 。
    1. 调用 [清理方法](get-started-detect-motion-emit-events-quickstart.md#invoke-graphinstancedeactivate)。
    1. 如果不再需要资源，请将其删除。

        > [!IMPORTANT]
        > 删除的资源仍可处于活动状态，并会产生 Azure 费用。
    
## <a name="next-steps"></a>后续步骤

* 尝试运动检测，并在云中录制相关视频。 按照检测动作中的步骤 [，将视频剪辑录制到媒体服务](detect-motion-record-video-clips-media-services-quickstart.md#review-the-sample-video) 快速入门。
* [在直播视频上运行 AI](use-your-model-quickstart.md#overview) (可以跳过先决条件设置，因为它已在上面完成) 
* 使用我们的 [VS Code 扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.live-video-analytics-edge) 来查看其他媒体关系图。
* 使用支持 RTSP 的 [IP 照相机](https://en.wikipedia.org/wiki/IP_camera)  ，而不是使用 rtsp 模拟器。 可以在 [ONVIF 一致性](https://www.onvif.org/conformant-products/)产品页上找到支持 RTSP 的 IP 相机。 查找符合配置文件 G、S 或 T 的设备。

