---
title: 将 Azure 视频分析器部署到 IoT Edge 设备 - Azure
description: 本文列出了可帮助你将 Azure 视频分析器部署到 IoT Edge 设备的步骤。 例如，如果你有权访问本地 Linux 计算机，那么你可能会执行此操作。
ms.topic: how-to
ms.date: 06/01/2021
ms.openlocfilehash: cf73e5d44e9c4c04a7c2f018e403da18e18ff17d
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2021
ms.locfileid: "114603901"
---
# <a name="deploy-azure-video-analyzer-to-an-iot-edge-device"></a>将 Azure 视频分析器部署到 IoT Edge 设备

本文介绍如何在此前未安装任何其他模块的 IoT Edge 设备上部署 Azure 视频分析器边缘模块。 完成本文中的步骤后，你便已创建一个视频分析器帐户，并将视频分析器模块以及用于模拟支持 RTSP 的 IP 摄像头的模块部署到 IoT Edge 设备。 此过程旨在与视频分析器的快速入门和教程配合使用。 如果打算部署视频分析器模块以用于生产，应查看[生产就绪情况与最佳做法](production-readiness.md)一文。

> [!NOTE]
> 本文中概述的过程将卸载 IoT Edge 设备上安装的边缘模块（如果有）。


## <a name="prerequisites"></a>必备条件

* 运行[支持的 Linux 操作系统](../../iot-edge/support.md#operating-systems)之一的 x86-64 或 ARM64 设备
* 具有有效订阅的 Azure 帐户
* [创建和设置 IoT 中心](../../iot-hub/iot-hub-create-through-portal.md)
* [注册 IoT Edge 设备](../../iot-edge/how-to-register-device.md)
* [在基于 Debian 的 Linux 系统上安装 Azure IoT Edge 运行时](../../iot-edge/how-to-install-iot-edge.md)


## <a name="create-resources-on-iot-edge-device"></a>在 IoT Edge 设备上创建资源

Azure 视频分析器模块应配置为使用非特权本地用户帐户在 IoT Edge 设备上运行。 该模块需要创建某些本地文件夹来存储应用程序配置数据。 对于本操作指南，我们将利用 [RTSP 模拟器](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555)，该模拟器将视频源实时中继到 AVA 模块进行分析。 此模拟器使用输入目录中的预录制视频文件作为输入。 以下脚本可使设备准备好与快速入门和教程配合使用。

https://aka.ms/ava/prepare-device

`bash -c "$(curl -sL https://aka.ms/ava-edge/prep_device)"`

上面使用的准备设备脚本会自动执行以下任务：创建输入文件夹和配置文件夹、下载视频输入文件以及创建具有正确特权的用户帐户。 成功完成该命令后，应会显示边缘设备上创建的以下文件夹。 

* `/home/localedgeuser/samples`
* `/home/localedgeuser/samples/input`
* `/var/lib/videoanalyzer`
* `/var/media`

    请注意，/home/localedgeuser/samples/input 文件夹中的视频文件（“*.mkv”）用于模拟实时视频。 

## <a name="creating-azure-resources-and-deploying-edge-modules"></a>创建 Azure 资源并部署边缘模块
下一步是创建所需的 Azure 资源（视频分析器帐户、存储帐户、用户分配的托管标识）、向视频分析器帐户注册视频分析器边缘模块，并将视频分析器边缘模块和 RTSP 模拟器模块部署到 IoT Edge 设备。

单击“部署到 Azure”按钮

> [!WARNING]
> 请勿对已安装边缘模块（例如 Percept DK）的 IoT Edge 设备使用此按钮。 Azure Stack Edge 也不支持此按钮。

[![部署到 Azure](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava/click-to-deploy/form)

1. 选择“订阅”
2. 选择首选区域
3. 选择 IoT 中心和 IoT Edge 设备所属的资源组
4. 在“需要边缘设备吗?”下拉菜单中，选择“使用现有边缘设备”选项 
5. 单击“下一步”
![初始部署窗体的屏幕截图](./media/deploy-iot-edge-device/project-details.png)

1. 选择 IoT Edge 设备连接到的现有 IoT 中心的名称
1. 单击“下一步”
![第二个部署窗体的屏幕截图](./media/deploy-iot-edge-device/iot-hub-name.png)

1. 在最后一页上，单击“创建”

创建 Azure 资源以及部署边缘模块可能需要一段时间。


### <a name="verify-your-deployment"></a>验证部署

创建部署后，在 Azure 门户中导航到 IoT 中心的 IoT Edge 设备页。

1. 选择用作部署目标的 IoT Edge 设备，以打开其详细信息。
2. 在设备详细信息中，验证模块是否已列为“在部署中指定”和“由设备报告”。

可能需要等待一段时间，模块才会在设备上启动并向 IoT 中心发回报告。 刷新页面以查看更新的状态。
状态代码：200 -OK 表示 [IoT Edge 运行时](../../iot-edge/iot-edge-runtime.md)状况良好并且运行正常。

![屏幕截图显示了 IoT Edge 运行时的状态值。](./media/deploy-iot-edge-device/status.png)

#### <a name="invoke-a-direct-method"></a>调用直接方法

接下来，让我们通过调用直接方法来测试示例。 阅读 [Azure 视频分析器的直接方法](direct-methods.md)，了解我们的 avaEdge 模块提供的所有直接方法。

1. 单击创建的边缘模块，此操作会将你转到其配置页面。  

    ![屏幕截图显示了边缘模块的配置页面。](./media/deploy-iot-edge-device/modules.png)
1. 单击“直接方法”菜单选项。

    > [!NOTE] 
    > 正如你在当前页面上看到的那样，你需要在连接字符串部分中添加一个值。 无需隐藏或更改“设置名称”部分中的任何内容。 可以让它处于公开状态。

    ![直接方法](./media/deploy-iot-edge-device/module-details.png)
1. 接下来，在“方法名称”框中输入“pipelineTopologyList”。
1. 接下来，复制以下 JSON 有效负载并将其粘贴到有效负载框中。
    
   ```
   {
       "@apiVersion": "1.0"
   }
   ```
1. 单击页面顶部的“调用方法”选项

    ![直接方法](./media/deploy-iot-edge-device/direct-method.png)
1. 你应该会在“结果”框中看到状态 200 消息

    ![状态 200 消息](./media/deploy-iot-edge-device/connection-timeout.png) 

## <a name="next-steps"></a>后续步骤

学习[快速入门：入门 - Azure 视频分析器](get-started-detect-motion-emit-events.md)

> [!TIP]
> 如果继续学习上述快速入门，当使用 Visual Studio Code 调用直接方法时，将使用根据本文说明添加到 IoT 中心的设备，而不是默认的 `avasample-iot-edge-device`。
