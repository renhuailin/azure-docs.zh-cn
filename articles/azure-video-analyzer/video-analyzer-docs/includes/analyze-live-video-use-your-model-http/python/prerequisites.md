---
author: Juliako
ms.service: azure-video-analyzer
ms.topic: include
ms.date: 04/07/2021
ms.author: juliako
ms.openlocfilehash: 60f8968a3377479457cbe0faceae28bc83835453
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113735134"
---
* 包含活动订阅的 Azure 帐户。 如果没有帐户，可[免费创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

    > [!NOTE]
    > 你将需要一个至少具有参与者角色的 Azure 订阅。 如果没有正确的权限，请联系帐户管理员，让其授予适当的权限。
    * 包含以下扩展的 [Visual Studio Code](https://code.visualstudio.com/)：
        * [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

        [!INCLUDE [install-docker-prompt](../../common-includes/install-docker-prompt.md)]
        * [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
    * [Python 3](https://www.python.org/downloads/)（3.6.9 或更高版本）、[Pip 3](https://pip.pypa.io/en/stable/installing/) 和可选的 [venv](https://docs.python.org/3/library/venv.html)。
* 阅读[检测运动并发出事件](../../../detect-motion-emit-events-quickstart.md)快速入门
## <a name="set-up-azure-resources"></a>设置 Azure 资源

[![部署到 Azure](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)  
[!INCLUDE [resources](../../../includes/common-includes/azure-resources.md)]

## <a name="overview"></a>概述
在本快速入门中，你将使用视频分析器来检测车辆和人员等对象。 将关联的推理事件发布到 IoT Edge 中心。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./../../../media/analyze-live-video-use-your-model-http/overview.png" alt-text="将关联的推理事件发布到 IoT Edge 中心":::

上图显示本快速入门中信号的流动方式。 [Edge 模块](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555)模拟托管实时流协议 (RTSP) 服务器的 IP 相机。 [RTSP 源节点](./../../../pipeline.md#rtsp-source)从该服务器拉取视频源，并将视频帧发送到 [HTTP 扩展处理器节点](./../../../pipeline.md#http-extension-processor)。

HTTP 扩展节点扮演代理的角色。 它对 samplingOptions 字段设置的传入视频帧采样，并将视频帧转换为指定的图像类型。 然后，它将图像通过 REST 转发到另一个 Edge 模块，该模块在 HTTP 终结点后运行一个 AI 模型。 在此示例中，使用 YOLOv3 模型构建该 Edge 模块，该模型能够检测多种类型的对象。 HTTP 扩展处理器节点收集检测结果并将事件发布到 [IoT 中心消息接收器节点](./../../../pipeline.md#iot-hub-message-sink)。 然后该节点将这些事件发送到 [IoT Edge 中心](../../../../../iot-fundamentals/iot-glossary.md?view=iotedge-2018-06&preserve-view=true#iot-edge-hub)。

在本快速入门中，请执行以下操作：

* 创建和部署 livePipeline。
* 解释结果。
* 清理资源。
## <a name="set-up-your-development-environment"></a>设置开发环境
[!INCLUDE [setup development environment](./../../../includes/set-up-dev-environment/csharp/csharp-set-up-dev-env.md)]
