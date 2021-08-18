---
title: Azure 视频分析器管道
description: 使用 Azure 视频分析器管道可以定义应从何处捕获输入数据、应如何处理这些数据以及应将结果传送到何处。 管道由连接的用于实现所需数据流的节点组成。
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: 5e874cc71752fa6690a4ca3fc9f3cd33665dc381
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2021
ms.locfileid: "114602249"
---
# <a name="pipeline"></a>管道

使用 Azure 视频分析器管道可以定义应从何处捕获输入数据、应如何处理这些数据以及应将结果传送到何处。 管道由连接的用于实现所需数据流的节点组成。 下图提供了管道的图形表示形式。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/pipeline/pipeline-representation.svg" alt-text="管道表示形式":::

管道支持不同类型的节点

* 源节点可用于将数据捕获到管道中。 数据是指音频、视频和/或元数据。
* 处理器节点可用于在管道中处理媒体。
* 接收器节点可用于将结果传送到管道外部的服务和应用。

## <a name="suggested-pre-reading"></a>建议的读前准备

* [概述](overview.md)
* [术语](terminology.md)

## <a name="pipeline-topologies"></a>管道拓扑

使用管道拓扑可以描述应如何根据自定义需求，通过一组互连的节点处理和分析实时视频。 可以通过选择要包含在拓扑中的节点、节点连接方式以及值占位符形式的参数，来为不同的方案创建不同的拓扑。 管道是特定管道拓扑的单个实例。 管道是实际处理媒体的位置。 可以通过管道拓扑中声明的用户定义参数，将管道关联到单个摄像头（以及其他方面）。

例如，如果你要从多个 IP 摄像头录制视频，可以定义由 RTSP 源节点和视频接收器节点组成的管道拓扑。 RTSP 源节点可以使用 RTSP URL、用户名和密码作为参数。 视频接收器节点可以使用视频名称作为参数。 从同一拓扑创建多个管道（每个摄像头有一个管道）时，可以提供这些参数的值。

## <a name="pipeline-states"></a>管道状态

管道的生命周期如下图所示。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/pipeline/pipeline-activation.svg" alt-text="管道的生命周期":::

首先创建管道拓扑。 定义拓扑后，可以通过提供参数值来创建管道。 成功创建管道后，该管道处于“非活动”状态。 激活时，管道将依次进入“正在激活”和“活动”状态。 

在管道进入“活动”状态后，数据（实时视频）将开始流经该管道。 停用时，处于活动状态的管道将依次进入“正在停用”和“非活动”状态。 只能删除非活动管道。

可以通过为拓扑中的参数提供不同的值，从单个拓扑创建多个管道。 删除所有管道后，可以删除拓扑。

> [!NOTE]
> 管道可以在没有数据流经它时处于活动状态（例如，输入视频源脱机）。 当管道处于活动状态时，将对你的 Azure 订阅计费。

## <a name="sources-processors-and-sinks"></a>源、处理器和接收器

视频分析器边缘模块支持管道中以下类型的节点：

### <a name="sources"></a>源

#### <a name="rtsp-source"></a>RTSP 源

使用 RTSP 源节点可以从 RTSP 服务器捕获媒体。 RTSP 协议定义如何在服务器（例如 IP 摄像头）与客户端之间建立和控制媒体会话。 管道中的 [RTSP](https://tools.ietf.org/html/rfc2326) 源节点充当客户端，可以与 RTSP 服务器建立会话。 许多设备（例如大多数 [IP 照相机](https://en.wikipedia.org/wiki/IP_camera)）有内置的 RTSP 服务器。 [ONVIF](https://www.onvif.org/) 强制要求在 [Profile G、S 和 T](https://www.onvif.org/wp-content/uploads/2019/12/ONVIF_Profile_Feature_overview_v2-3.pdf) 兼容设备的定义中支持 RTSP。 RTSP 源节点要求你指定 RTSP URL 以及凭据才能启用经过身份验证的连接。

#### <a name="iot-hub-message-source"></a>IoT 中心消息源

与其他 [IoT Edge 模块](../../iot-fundamentals/iot-glossary.md?view=iotedge-2020-11&preserve-view=true#iot-edge-device)一样，Azure 视频分析器模块可以通过 [IoT Edge 中心](../../iot-fundamentals/iot-glossary.md?view=iotedge-2020-11&preserve-view=true#iot-edge-hub)接收消息。 消息可以发送自其他模块或在 Edge 设备上运行的应用，或者发送自云。 此类消息可传送（路由）到视频分析器模块上的[命名输入](../../iot-edge/module-composition.md?view=iotedge-2020-11&preserve-view=true#sink)。 使用 IoT 中心消息源节点可将此类消息引入管道。 然后，可以在管道中使用消息来激活信号门（参阅下面的[信号门](#signal-gate-processor)）。

例如，你可能有一个在门打开时生成消息的 IoT Edge 模块。 来自该模块的消息可以路由到 IoT Edge 中心，然后可以从该中心路由到管道的 IoT 中心消息源。 在管道中，消息可以从 IoT 中心消息源传递到信号门处理器，然后，该处理器可以启用将 RTSP 源中的视频录制到文件。

### <a name="processors"></a>Processors

#### <a name="motion-detection-processor"></a>运动检测处理器

通过运动检测处理器节点，你可以在实时视频中检测运动。 它会检查传入的视频帧并确定视频中是否有移动。 如果检测到运动，它会将视频帧传递到管道中的下一个节点，并发出事件。 运动检测处理器节点（连同其他节点）可用于在检测到运动时触发传入视频的录制。

#### <a name="http-extension-processor"></a>HTTP 扩展处理器

使用 HTTP 扩展处理器节点可以将管道扩展到你自己的 IoT Edge 模块。 此节点采用解码的视频帧作为输入，并将此类帧中继到模块公开的 HTTP REST 终结点，在该终结点中可以使用 AI 模型分析帧并返回推理结果。 此外，此节点还具有内置的图像格式化程序，用于在视频帧中继到 HTTP 终结点之前对它们进行缩放和编码。 缩放程序提供了选项，用于保留、填充或拉伸图像纵横比。 图像编码器支持 JPEG、PNG、BMP 和 RAW 格式。 请在[此处](pipeline-extension.md#http-extension-processor)详细了解处理器。

#### <a name="grpc-extension-processor"></a>gRPC 扩展处理器

此 gRPC 扩展处理器节点以解码的视频帧作为输入，并将此类帧中继到模块公开的 [gRPC](terminology.md#grpc) 终结点。 节点支持使用[共享内存](https://en.wikipedia.org/wiki/Shared_memory)传输数据，或将帧直接嵌入 gRPC 消息的正文中。 与 HTTP 扩展进程一样，此节点也具有内置的图像格式化程序，用于在将视频帧中继到 gRPC 终结点之前对它们进行缩放和编码。 请在[此处](pipeline-extension.md#grpc-extension-processor)详细了解处理器。

#### <a name="cognitive-services-extension-processor"></a>认知服务扩展处理器

使用认知服务扩展处理器节点可将管道扩展到[空间分析](https://azure.microsoft.com/services/cognitive-services/computer-vision/) IoT Edge 模块。 此节点采用解码的视频帧作为输入，并将此类帧中继到公开的 [gRPC](pipeline-extension.md#grpc-extension-processor) 终结点，在该终结点中可以使用空间分析技能分析帧并返回推理结果。 请在[此处](pipeline-extension.md#cognitive-services-extension-processor)详细了解处理器。

#### <a name="signal-gate-processor"></a>信号门处理器

通过信号门处理器节点，你可以有条件地将媒体从一个节点转发到另一个节点。 信号门处理器节点必须紧跟在视频接收器或文件接收器之后。 示例用例是将信号门处理器节点插入 RTSP 源节点和视频接收器节点之间，并使用运动检测器处理器节点的输出触发门。 使用此类管道时，只会在检测到运动时才录制视频。 还可使用 HTTP 或 gRPC 扩展节点（而不是运动检测处理器节点）的输出来触发门，从而在检测到有趣的事物时启用视频录制。

#### <a name="object-tracker-processor"></a>对象跟踪器处理器

使用对象跟踪器处理器节点可以跟踪在上游 HTTP 或 gRPC 扩展处理器节点中检测到的对象。 如果你需要检测每个帧中的对象，但边缘设备没有所需的计算能力，无法将 AI 模型应用于每个帧，那么使用此节点就会带来很多便利。 如果你只能每隔 10 帧运行计算机视觉模型，则对象跟踪器可从某个此类帧中提取结果，然后使用[光学流](https://en.wikipedia.org/wiki/Optical_flow)技术为第 2 到第 9 帧生成结果，直到再次对下一帧应用该模型。 使用此节点时，需要在计算能力与准确度之间进行权衡取舍。 应用 AI 模型的帧越接近，准确度越高。 但是，这意味着要更频繁地应用 AI 模型，因此也相当于所需的计算能力更高。 对象跟踪器处理器节点的一个常见用途是检测对象何时跨线。

#### <a name="line-crossing-processor"></a>跨线处理器

使用越线处理器节点可以检测对象何时跨越你定义的线条。 除此之外，它还会保留跨线的对象数（从激活管道时算起）。 此节点必须在对象跟踪器处理器节点的下游使用。

### <a name="sinks"></a>接收器

#### <a name="video-sink"></a>视频接收器

使用视频接收器节点可将视频和关联的元数据保存到视频分析器云资源。 视频可以连续或间断录制，具体取决于事件。 与云的连接断开时，视频接收器节点可以在边缘设备上缓存视频；连接恢复后，将继续上传。 可以查看[连续视频录制](continuous-video-recording.md)一文，了解有关如何配置此节点的属性的详细信息。

#### <a name="file-sink"></a>文件接收器

使用文件接收器节点可将视频写入边缘设备本地文件系统上的位置。 管道中只能有一个文件接收器节点，并且该节点必须位于信号门处理器节点的下游。 这会将输出文件的持续时间限制为信号门处理器节点属性中指定的值。 为确保边缘设备不会耗尽磁盘空间，还可以设置一个最大大小，供视频分析器边缘模块用来缓存数据。

> [!NOTE]
> 如果缓存已满，则视频分析器边缘模块将开始删除最旧的数据，将其替换为新数据。

#### <a name="iot-hub-message-sink"></a>IoT 中心消息接收器

通过 IoT 中心消息接收器节点，你可以将事件发布到 IoT Edge 中心。 IoT Edge 中心可配置为将数据路由到边缘设备上的其他模块或应用，或路由到云中的 IoT 中心（根据部署清单中指定的路由）。 IoT 中心消息接收器节点可以接受来自上游处理器节点（例如运动检测处理器节点）的事件，或通过 HTTP 扩展处理器节点接受来自外部推理服务的事件。

## <a name="rules-on-the-use-of-nodes"></a>使用节点的规则

有关如何在管道中使用不同节点的其他规则，请参阅[管道的限制](quotas-limitations.md#limitations-on-pipeline-topologies)。

## <a name="scenarios"></a>方案

使用上面定义的源、处理器和接收器组合，可以为涉及实时视频分析的各种方案构建管道。 示例方案包括：

* [连续视频录制](continuous-video-recording.md) 
* [基于事件的视频录制](event-based-video-recording-concept.md) 
* [在不录制视频的情况下进行视频分析](analyze-live-video-without-recording.md) 

## <a name="next-steps"></a>后续步骤

若要了解如何对实时视频源运行运动检测，请参阅[快速入门：入门 - Azure 视频分析器](get-started-detect-motion-emit-events.md)。

