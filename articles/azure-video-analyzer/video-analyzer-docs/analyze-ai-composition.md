---
title: 借助 AI 合成功能，通过多个 AI 模型分析实时视频流
description: 本文就如何借助 Azure 视频分析器的 AI 合成功能，通过多个 AI 模型分析实时视频流，提供了相关指导。
ms.service: azure-video-analyzer
ms.topic: how-to
ms.date: 04/01/2021
ms.openlocfilehash: 03a2f0c597b3fe58b030ca2b03d9337b3322b444
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779823"
---
# <a name="analyze-live-video-streams-with-multiple-ai-models-using-ai-composition"></a>借助 AI 合成功能，通过多个 AI 模型分析实时视频流

某些客户方案要求使用多个 AI 模型分析视频。 此类模型可以[相互增强](ai-composition-overview.md#sequential-ai-composition)，或在[同一视频流上[独立并行工作](ai-composition-overview.md#parallel-ai-composition)，或是此类增强和独立并行模型的组合](ai-composition-overview.md#combined-ai-composition)亦可作用于同一视频流，以派生可操作见解。

Azure 视频分析器通过名为 [AI 合成](ai-composition-overview.md)的功能支持此类方案。 本指南展示了如何在同一视频流上以增强方式应用多个模型。 其并行使用 Tiny(Light) YOLO 和常规 YOLO 模型来检测感兴趣的对象。 Tiny YOLO 模型的计算更轻巧，但准确性略低于 YOLO 模型，因此被率先调用。 若检测到的对象通过了特定的置信度阈值，则不调用按顺序缓存的常规 YOLO 模型，以便有效地利用基础资源。

完成本指南中的步骤后，你将能够通过具有 AI 可组合性的管道运行模拟实时视频流，并将其扩展到特定方案。 下图显示了该管道的图形表示形式。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/ai-composition/motion-with-object-detection-using-ai-composition.svg" alt-text="AI 合成概述":::
 
## <a name="prerequisites"></a>先决条件

* 具有活动订阅的 Azure 帐户。 如果没有帐户，可[免费创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

    > [!NOTE]
    > 你将需要一个具有服务主体创建权限（所有者角色提供此权限）的 Azure 订阅。 如果你没有正确的权限，请联系帐户管理员，让其授予你适当的权限。
* 开发计算机上的 [Visual Studio Code](https://code.visualstudio.com/)。 请确保具有 [Azure IoT Tools 扩展](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)。
* 确保开发计算机连接到的网络允许出站流量采用高级消息队列协议 (AMQP) 流经端口 5671。 此设置使 Azure IoT Tools 可以与 Azure IoT 中心通信。
* 使用自己的 gRPC 模型快速入门，完成[分析实时视频](analyze-live-video-use-your-model-grpc.md)。 请不要跳过此操作，因为这是操作指南的严格要求。

> [!TIP]
> 安装 Azure IoT Tools 扩展时，系统可能会提示你安装 Docker。 可以忽略此提示。
>
> 如果在创建 Azure 资源时遇到问题，请查看[故障排除指南](troubleshoot.md#common-error-resolutions)来解决一些常见问题。

## <a name="review-the-video-sample"></a>观看示例视频

由于已完成先决条件部分中指定的快速入门，因此你已创建边缘设备。 此边缘设备将具有以下输入文件夹：/home/localedgeuser/samples/input，其中包含某些视频文件。 登录到 IoT Edge 设备，将目录更改为 /home/localedgeuser/samples/input/，并运行以下命令，以获取将用于本操作指南的输入文件。

wget https://lvamedia.blob.core.windows.net/public/co-final.mkv

此外，如有需要，请在提供 [VLC 媒体播放器](https://www.videolan.org/vlc/)的计算机上选择 Ctrl+N，然后将链接粘贴到[示例视频 (.mkv)](https://lvamedia.blob.core.windows.net/public/co-final.mkv)，开始播放视频。 你将看到汽车行驶在高速公路上的片段。

## <a name="create-and-deploy-the-pipeline"></a>创建和部署管道

与在先决条件中完成的快速入门中步骤类似，你可按照此处步骤操作，但需进行细微调整。

1. 请遵循刚刚完成的快速入门中[创建和部署管道](analyze-live-video-use-your-model-grpc.md#create-and-deploy-the-pipeline)部分的准则。 在继续执行这些步骤时，请务必进行以下调整。 这些步骤有助于确保使用直接方法调用正确的主体。

编辑 operations.json 文件：

* 将链接更改为管道拓扑：`"pipelineTopologyUrl" : "https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/ai-composition/topology.json"`
* 在 `livePipelineSet` 下，  
   1. 确保：`"topologyName" : "AIComposition"` 及 
   2. 将 `rtspUrl` 参数值更改为 `"rtsp://rtspsim:554/media/co-final.mkv"`。
    
* 在 `pipelineTopologyDelete` 下，编辑名称：`"name" : "AIComposition"`
    
2. 请遵循[生成和部署　IoT Edge 部署清单](analyze-live-video-use-your-model-grpc.md#generate-and-deploy-the-iot-edge-deployment-manifest)部分中的准则，但请改为使用以下部署清单：src/edge/deployment.composite.template.json

3. 请遵循[运行示例程序](analyze-live-video-use-your-model-grpc.md#run-the-sample-program)部分中的准则。

4. 有关结果详细信息，请参阅[结果解释](analyze-live-video-use-your-model-grpc.md#interpret-results)部分。 除了中心上的分析事件和诊断事件外，使用的拓扑还会在云端创建相关视频剪辑，该视频剪辑由基于 AI 信号的激活信号门触发。 此剪辑还附带了中心上的[操作事件](record-event-based-live-video.md#operational-events)，供下游工作流使用。 你可以登录　Azure 门户，[检查和播放](record-event-based-live-video.md#playing-back-the-recording)视频剪辑。

## <a name="clean-up"></a>清理

如果不打算继续使用此应用程序，请删除本快速入门中创建的资源。

## <a name="next-steps"></a>后续步骤

详细了解[诊断消息](monitor-log-edge.md)。
