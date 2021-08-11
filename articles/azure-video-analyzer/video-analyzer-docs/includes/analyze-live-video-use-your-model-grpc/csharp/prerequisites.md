---
author: Juliako
ms.service: azure-video-analyzer
ms.topic: include
ms.date: 04/07/2021
ms.author: juliako
ms.openlocfilehash: 86fcb2282d6e0885b64c1385ae4f23bf76d6ec0d
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113735307"
---
* 包含活动订阅的 Azure 帐户。 如果没有帐户，可[免费创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

    > [!NOTE]
    > 你将需要一个至少具有参与者角色的 Azure 订阅。 如果没有正确的权限，请联系帐户管理员，让其授予适当的权限。
* 包含以下扩展的 [Visual Studio Code](https://code.visualstudio.com/)：
    * [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    
    [!INCLUDE [install-docker-prompt](../../common-includes/install-docker-prompt.md)]
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)。 

## <a name="set-up-azure-resources"></a>设置 Azure 资源

[![部署到 Azure](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)  
[!INCLUDE [resources](../../../includes/common-includes/azure-resources.md)]

## <a name="overview"></a>概述

> [!div class="mx-imgBorder"]
> :::image type="content" source="./../../../media/analyze-live-video-use-your-model-grpc/overview.png" alt-text="gRPC 概述":::
 
此图显示本快速入门中信号的流动方式。 [Edge 模块](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555)模拟托管实时流式处理协议 (RTSP) 服务器的 IP 相机。 [RTSP 源节点](./../../../pipeline.md#rtsp-source)从该服务器拉取视频源，并将视频帧发送到[运动检测处理器](./../../../pipeline.md#motion-detection-processor)节点。 该处理器将检测运动，并在检测到运动后将视频帧推送到 [gRPC 扩展处理器](./../../../pipeline.md#grpc-extension-processor)节点。

gRPC 扩展节点扮演代理的角色。 它将视频帧转换为指定的图像类型。 然后，它将图像通过 gRPC 中继到另一个 Edge 模块，该模块通过[共享内存](https://en.wikipedia.org/wiki/Shared_memory)在 gRPC 终结点后运行 AI 模型。 在此示例中，使用 [YOLOv3](https://github.com/Azure/video-analyzer/tree/main/edge-modules/extensions/yolo/yolov3) 模型构建该 Edge 模块，该模型能够检测多种类型的对象。 gRPC 扩展处理器节点收集检测结果并将事件发布到 [IoT 中心接收器](./../../../pipeline.md#iot-hub-message-sink)节点。 然后该节点将这些事件发送到 [IoT Edge 中心](../../../../../iot-fundamentals/iot-glossary.md?view=iotedge-2020-11&preserve-view=true#iot-edge-hub)。

在本快速入门中，请执行以下操作：

1. 创建和部署管道。
1. 解释结果。
1. 清理资源。

## <a name="set-up-your-development-environment"></a>设置开发环境
[!INCLUDE [setup development environment](./../../../includes/set-up-dev-environment/csharp/csharp-set-up-dev-env.md)]