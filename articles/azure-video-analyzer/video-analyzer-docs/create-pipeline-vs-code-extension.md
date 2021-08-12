---
title: Azure 视频分析器 Visual Studio Code 扩展
description: 本快速入门将逐步介绍如何开始使用 Azure 视频分析器 Visual Studio Code 扩展。
ms.service: azure-video-analyzer
ms.topic: quickstart
ms.date: 06/01/2021
ms.openlocfilehash: dd342d1aaf8cf93bfcf518342315ad022e52f4f9
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2021
ms.locfileid: "114604014"
---
# <a name="quickstart-azure-video-analyzer-visual-studio-code-extension"></a>快速入门：Azure 视频分析器 Visual Studio Code 扩展

本快速入门旨在演示如何设置视频分析器 Visual Studio Code 扩展并将其连接到视频分析器 Edge 模块，然后部署示例管道拓扑。  它将使用“开始使用”快速入门中所用的管道。  

完成设置步骤后，你将能通过实时管道运行模拟的实时视频流，该管道可检测和报告该流中的任何运动。 下图显示了该管道的图形表示形式。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/get-started-detect-motion-emit-events/motion-detection.svg" alt-text="检测运动":::
 
 ## <a name="prerequisites"></a>先决条件
 
* 包含活动订阅的 Azure 帐户。 如果没有帐户，可免费[创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 包含以下扩展的 [Visual Studio Code](https://code.visualstudio.com/)：
    * [视频分析器](https://go.microsoft.com/fwlink/?linkid=2163332)

* 如果未完成[开始使用 - Azure 视频分析器](./get-started-detect-motion-emit-events.md)快速入门，请确保[设置 Azure 资源](#set-up-azure-resources)。    

## <a name="set-up-azure-resources"></a>设置 Azure 资源

[![部署到 Azure](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)

[!INCLUDE [resources](./includes/common-includes/azure-resources.md)]

## <a name="connect-the-azure-video-analyzer-visual-studio-code-extension-to-your-iot-hub"></a>将 Azure 视频分析器 Visual Studio Code 扩展连接到 IoT 中心

若要将扩展连接到 Edge 模块，首先需要检索连接字符串。 为此，请按以下步骤操作。

1.  转到 [Azure 门户](https://portal.azure.com)并选择 IoT 中心。
1.  在左侧的 `Settings` 下，选择 `Shared access policies`。
1.  选择策略名称 `iothubowner`。
1.  从右侧窗口中复制 `Primary connection string`。

有了连接字符串后，以下步骤会将扩展连接到 Edge 模块。

1.  在 Visual Studio Code 中，选择左侧的 `Azure Video Analyzer` 图标。
1.  单击“”`Enter Connection String`按钮。
1.  在顶部，从门户中粘贴连接字符串。
1.  选择设备，默认为 `avasample-iot-edge-device`。
1.  选择视频分析器模块，默认为 `avaedge`。

在左侧，你现可看到连接的设备与基础模块。  默认情况下，未部署管道拓扑。

## <a name="deploy-a-topology-and-live-pipeline"></a>部署拓扑和实时管道

管道拓扑是视频分析器用于定义工作如何发生的基本构建基块。  你可在[此处](./pipeline.md)详细了解管道拓扑。  在本部分中，你将部署一个管道拓扑（它是一个模板），然后创建拓扑或实时管道的实例。 实时管道连接到实际的视频流。

1.  在左侧的 `Modules` 下，右键单击 `Pipeline topologies` 并选择“创建管道拓扑”。
1.  在顶部的 `Try sample topologies` 下，选择 `Motion Detection` 下的 `Publish motion events to IoT Hub`。  系统提示时，单击“是”`Proceed`。
1.  单击右上方的 `Save`。

你现在应在左边的 `Pipeline topologies` 列表中看到一个标记为 `MotionDetection` 的条目。  这是一个管道拓扑，其中某些参数被定义为可在创建实时管道时馈送的变量。  接下来，创建一个实时管道。

1.  在左侧的 `Pipeline topologies` 下，右键单击 `MotionDetection` 并选择 `Create live pipeline`。
1.  对于 `Live pipeline name`，输入 `mdpipeline1`。
1.  在 `Parameters` 部分：
    - 对于“rtspPassword”，输入“testuser”。
    - 对于“rtspUrl”，输入“rtsp://rtspsim:554/media/camera-300s.mkv”。
    - 对于“rtspUserName”，输入"testpassword"。
1.  在右上方，单击“保存并激活”。

此时，边缘设备上将部署一个启动拓扑，实时管道将启动并运行。  如果根据“开始使用”快速入门安装了 Azure IoT 中心扩展，你可以在 Azure IoT 中心 Visual Studio Code 扩展中监视内置的事件终结点；请按照[观察结果](./get-started-detect-motion-emit-events.md#observe-results)部分所示进行监视。

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [prerequisites](./includes/common-includes/clean-up-resources.md)]

## <a name="next-steps"></a>后续步骤

* 详细了解 [Azure 视频分析 Visual Studio Code 扩展](./visual-studio-code-extension.md)的各种功能。
