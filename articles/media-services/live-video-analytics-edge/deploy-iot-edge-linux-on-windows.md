---
title: 使用实时视频分析部署到 IoT Edge for Linux on Windows - Azure
description: 本文提供有关如何使用实时视频分析部署到 IoT Edge for Linux on Windows 设备的指导。
ms.topic: how-to
ms.date: 02/18/2021
ms.openlocfilehash: c504ecf2bb7405ee49d6e9ee94f069336924f5c0
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2021
ms.locfileid: "110378088"
---
# <a name="deploy-to-an-iot-edge-for-linux-on-windows-eflow-device-with-live-video-analytics"></a>使用实时视频分析部署到 IoT Edge for Linux on Windows (EFLOW) 设备

[!INCLUDE [redirect to Azure Video Analyzer](./includes/redirect-video-analyzer.md)]

本文介绍如何在具有 [IoT Edge for Linux on Windows (EFLOW)](../../iot-edge/iot-edge-for-linux-on-windows.md) 的边缘设备上部署实时视频分析。 完成本文档中的步骤后，你将能够运行[媒体图](media-graph-concept.md)，该媒体图可检测视频中的移动并将此类事件发送到云中的 IoT 中心。 然后，你可以切换出高级方案的媒体图，并将实时视频分析功能引入基于 Windows 的 IoT Edge 设备。

## <a name="prerequisites"></a>先决条件 

* 具有活动订阅的 Azure 帐户。 如果没有帐户，可[免费创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

    > [!NOTE]
    > 你将需要一个具有服务主体创建权限（所有者角色提供此权限）的 Azure 订阅。 如果你没有正确的权限，请联系帐户管理员，让其授予你适当的权限。
* 开发计算机上的 [Visual Studio Code](https://code.visualstudio.com/)。 请确保具有 [Azure IoT Tools 扩展](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)。
* 请阅读[什么是 EFLOW](../../iot-edge/iot-edge-for-linux-on-windows.md)。

## <a name="deployment-steps"></a>部署步骤

下面描述了文档的整体流，只需 5 个简单步骤即可在具有 EFLOW 的 Windows 设备上运行实时视频分析：

:::image type="content" source="./media/deploy-iot-edge-linux-on-windows/eflow.png" alt-text="IoT Edge for Linux on Windows (EFLOW) 示意图":::

1. 在 Windows 设备上[安装 EFLOW](../../iot-edge/how-to-install-iot-edge-on-windows.md)。 

    1. 如果使用的是 Windows 电脑，则在 [Windows Admin Center](/windows-server/manage/windows-admin-center/overview) 起始页的连接列表下，你会看到一个本地主机连接，该连接表示运行 Windows Admin Center 的电脑。 
    1. 你管理的任何其他服务器、电脑或群集也会显示在此处。
    1. 你可以使用 Windows Admin Center 在本地设备或远程托管设备上安装和管理 Azure EFLOW。 在本指南中，本地主机连接充当用于部署 Azure IoT Edge for Linux on Windows 的目标设备。 因此，你会看到 localhost 也被列为 IoT Edge 设备。

    :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/windows-admin-center.png" alt-text="部署步骤 - Windows Admin Center":::
1. 单击 IoT Edge 设备与之连接，应会显示“概述”和“命令 Shell”选项卡。在“命令 shell”选项卡中，可以向边缘设备发出命令。
 
    :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/azure-iot-edge-manager.png" alt-text="部署步骤 - Azure IoT Edge 管理器":::
1. 转到命令外壳，然后键入以下命令：
    
    ```bash
    bash -c "$(curl -sL https://aka.ms/lva-edge/prep_device)"
    ```

    实时视频分析模块使用非特权[本地用户帐户](deploy-iot-edge-device.md#create-and-use-local-user-account-for-deployment)在边缘设备上运行。 此外，它还[需要某些本地文件夹](deploy-iot-edge-device.md#granting-permissions-to-device-storage)来存储应用程序配置数据。 最后，对于本操作指南，我们将利用 [RTSP 模拟器](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555)，该模拟器将视频源实时中继到 LVA 模块进行分析。 此模拟器使用输入目录中的预录制视频文件作为输入。 
    
    上面使用的准备设备脚本会自动执行这些任务，因此，你可以运行一个命令，并通过无缝创建的特权拥有所有相关的输入和配置文件夹、视频输入文件以及用户帐户。 成功完成该命令后，应会显示边缘设备上创建的以下文件夹。 
    
    * `/home/lvaedgeuser/samples`
    * `/home/lvaedgeuser/samples/input`
    * `/var/lib/azuremediaservices`
    * `/var/media`
    
    请注意 /home/lvaedgeuser/samples/input 文件夹中的视频文件 (*.mkv)，这些文件充当要分析的输入文件。 
    
    :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/azure-iot-edge-manager-analysis.png" alt-text="分析":::
1. 现在，你已设置边缘设备，将其注册到中心，并通过创建正确的文件夹结构成功运行，下一步是设置以下额外的 Azure 资源并部署 LVA 模块。 

    * 存储帐户
    * Azure 媒体服务帐户

    在本快速入门中，我们建议你使用[实时视频分析资源设置脚本](https://github.com/Azure/live-video-analytics/tree/master/edge/setup)在 Azure 订阅中部署所需资源。 为此，请执行下列步骤：

    1. 打开 [Azure Cloud Shell](https://ms.portal.azure.com/#cloudshell/)。

        :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/azure-resources-cloud-shell.png" alt-text="Cloud Shell":::
    1. 如果你是第一次使用 Cloud Shell，系统将提示你选择一个订阅以创建存储帐户和 Microsoft Azure 文件存储共享。 选择“创建存储”，创建用于存储 Cloud Shell 会话信息的存储帐户。 此存储帐户不同于脚本将要创建的与 Azure 媒体服务帐户配合使用的帐户。
    1. 在 Cloud Shell 窗口左侧的下拉菜单中，选择“Bash”作为环境。

        :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/bash.png" alt-text="Bash 环境":::
    1. 运行以下命令。

        ```bash
        bash -c "$(curl -sL https://aka.ms/lva-edge/setup-resources-for-samples)"
        ```
        
        当系统提示你选择自己的边缘设备作为 IoT Edge 设备时，请确保选择“Y”，因为你之前创建了设备和 IoT 中心。 系统还会提示输入 IoT 中心名称和 IoT Edge 设备 ID。要获取这两项信息，可登录 Azure 门户并单击 IoT 中心，然后转到 IoT 中心门户边栏选项卡上的 IoT Edge 选项。

        :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/iot-edge-devices.png" alt-text="查看 IoT Edge 设备":::

    完成后，可以重新登录到 IoT Edge 设备命令外壳并运行以下命令。
    
    `sudo iotedge list`
    
    应会看到边缘设备上已部署且正在运行以下四个模块。 请注意，资源创建脚本会将 LVA 模块、IoT Edge 模块（edgeAgent 和 edgeHub）与 RTSP 模拟器模块一起部署来提供模拟的 RTSP 视频源。
    
    :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/running.png" alt-text="查看状态":::
1. 部署和设置模块后，就可以在 EFLOW 上运行第一个 LVA 媒体图了。 可以按如下所示运行简单的移动检测图，并通过执行以下步骤来可视化结果：

    :::image type="content" source="./media/analyze-live-video/motion-detection.svg" alt-text="移动检测图":::

    1. [配置](get-started-detect-motion-emit-events-quickstart.md#configure-the-azure-iot-tools-extension) Azure IoT Tools 扩展。
    
        > [!Note]
        > 使用以下路径：`~/clouddrive/lva_byod/appsettings.json. - instead of ~/clouddrive/lva-sample/appsettings.json`。
    1. 设置拓扑，实例化图形，并通过这些[直接方法调用](get-started-detect-motion-emit-events-quickstart.md#use-direct-method-calls)激活它。
    1. 在中心上[观察结果](get-started-detect-motion-emit-events-quickstart.md#observe-results)。
    1. 调用[清除方法](get-started-detect-motion-emit-events-quickstart.md#invoke-graphinstancedeactivate)。
    1. 不再需要资源时，即可将其删除。

        > [!IMPORTANT]
        > 未删除的资源仍可处于活动状态，并且会产生 Azure 费用。
    
## <a name="next-steps"></a>后续步骤

* 尝试移动检测以及在云中录制相关视频。 按照[检测移动并将视频剪辑录制到媒体服务](detect-motion-record-video-clips-media-services-quickstart.md#review-the-sample-video)快速入门中的步骤进行操作。
* 运行[实时视频上的 AI](use-your-model-quickstart.md#overview)（可以跳过先决条件设置，因为它已在上面完成）
* 使用 [VS Code 扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.live-video-analytics-edge)来查看其他媒体图。
* 使用支持 RTSP 的 [IP 相机](https://en.wikipedia.org/wiki/IP_camera)，而不是使用 RTSP 模拟器。 可以在 [ONVIF 一致性](https://www.onvif.org/conformant-products/)产品页上找到支持 RTSP 的 IP 相机。 查找符合配置文件 G、S 或 T 的设备。
