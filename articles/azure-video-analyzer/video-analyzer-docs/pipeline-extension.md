---
title: 管道扩展 - Azure 视频分析器
description: Azure 视频分析器支持通过管道扩展节点增强管道处理功能。 本文介绍管道扩展节点。
ms.service: azure-video-analyzer
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: 55cb6a265e74eb4209742f8fb0a8b6f34cb08254
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2021
ms.locfileid: "114604682"
---
# <a name="pipeline-extension"></a>管道扩展

Azure 视频分析器支持通过管道扩展节点增强管道处理功能。 你的分析扩展插件可以利用传统的图像处理技术或计算机视觉 AI 模型。 通过在管道流中包含扩展处理器节点，可实现管道扩展。 扩展处理器节点将视频帧中继到配置的终结点，并充当扩展的接口。 可建立到本地或远程终结点的连接，并且可以根据需要使用身份验证和 TLS 加密来保护该连接。 此外，管道扩展处理器节点允许在将视频帧提交到自定义扩展之前对其进行缩放和编码（可选）。

视频分析器支持以下管道扩展处理器：

* [HTTP 扩展处理器](pipeline.md#http-extension-processor) 
* [gRPC 扩展处理器](pipeline.md#grpc-extension-processor)
* [认知服务扩展处理器](pipeline.md#cognitive-services-extension-processor) 
    
管道扩展节点要求分析扩展插件以 JSON 格式返回结果。 理想情况下，结果应遵循[推理元数据架构对象模型](inference-metadata-schema.md)

## <a name="http-extension-processor"></a>HTTP 扩展处理器

HTTP 扩展处理器支持使用 [HTTP 协议](http-extension-protocol.md)的可扩展性方案，其中性能和/或最佳资源利用率不是主要问题。 可以通过 HTTP REST 终结点向管道公开自己的 AI。

在以下情况下使用 HTTP 扩展处理器节点：

* 你想与现有的 HTTP 推断系统实现更好的互操作性。
* 低性能数据传输是可接受的。
* 你想将简单的请求-响应接口用于视频分析器。

## <a name="grpc-extension-processor"></a>gRPC 扩展处理器

gRPC 扩展处理器使用基于 gRPC 的高性能[结构化协议](grpc-extension-protocol.md)实现可扩展性方案。 它非常适用于性能和/或最佳资源利用率优先的场景。 通过 gRPC 扩展处理器，你可以充分利用结构化数据定义。 gRPC 通过以下方式提供优秀的内容传输性能：

* [内置共享内存](https://en.wikipedia.org/wiki/Shared_memory)或
* 将内容直接嵌入到 gRPC 消息正文中。

gRPC 扩展处理器可用于发送属性以及交换推理消息。 因此，在下列情况下，请使用 gRPC 扩展处理器节点：

* 需要使用结构化的协定（例如请求和响应的结构化消息）
* 想要使用[协议缓冲区 (protobuf)](https://developers.google.com/protocol-buffers) 作为通信的基础消息交换格式。
* 希望在单流会话（而不是传统的请求-响应模型）中与 gRPC 服务器通信，它需要自定义请求处理程序来分析传入的请求并调用正确的实现函数。
* 需要在视频分析器和你的模块之间进行低延迟和高吞吐量的通信。

## <a name="cognitive-services-extension-processor"></a>认知服务扩展处理器

认知服务扩展处理器是自定义构建的扩展处理器，使视频分析器能够使用基于 gRPC 的高性能[结构化协议](grpc-extension-protocol.md)，与 [计算机视觉空间分析]../../cognitive-services/computer-vision/) 功能很好地配合工作。 

在下列情况下，请使用认知服务扩展处理器：

* 需要更好地与现有[空间分析操作](../../cognitive-services/computer-vision/intro-to-spatial-analysis-public-preview.md)进行互操作。
* 想要充分发挥 gRPC 协议的优势，以及由 Microsoft 构建并提供支持的 AI 的准确性和性能。
* 以低延迟和高吞吐量分析多个摄像头源。

## <a name="use-your-inferencing-model"></a>使用推理模型

管道扩展允许你在任何可用的推理运行时（例如 ONNX、TensorFlow、PyTorch 或你自己的 Docker 容器中的其他运行时）上运行所选推理模型。 推理自定义扩展应与视频分析器边缘模块一起部署，以获得最佳性能，然后将通过管道拓扑中包含的 HTTP 扩展处理器、gRPC 扩展处理器或认知服务扩展处理器进行调用。 另外，可以通过在管道扩展处理器的上游添加[运动检测器处理器](pipeline.md#motion-detection-processor)（可选），来限制对自定义扩展的调用频率。

下图描绘了大致数据流：

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/pipeline-extension/ai-inference-results.svg" alt-text="推理模型":::
 
## <a name="samples"></a>示例

可以使按照快速入门中所述来开始使用，这些快速入门介绍了视频分析器与预构建的扩展服务的配合工作，该服务使用 [HTTP 扩展处理器](pipeline.md#http-extension-processor)以低帧速率运行，或使用 [gRPC 扩展处理器](pipeline.md#grpc-extension-processor)以高帧速率运行。


## <a name="next-steps"></a>后续步骤 

概念：[基于事件的视频录制](event-based-video-recording-concept.md)

