---
title: 开发和部署 gRPC 推理服务器-Azure
description: 本文提供了有关如何开发和部署 gRPC 推理服务器的指导。
ms.topic: how-to
ms.date: 12/02/2020
ms.openlocfilehash: 3f732a7432dacebeeefddd1822fec7d95dfbaa97
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/14/2020
ms.locfileid: "97425747"
---
# <a name="how-to-guide--develop-and-deploy-a-grpc-inference-server"></a>操作指南-开发和部署 gRPC 推理服务器

## <a name="overview"></a>概述

本文介绍了如何在 gRPC 推理服务器中包装 AI 模型 () ，以便可通过图形扩展 (LVA) 将其与实时视频分析集成。 

## <a name="suggested-pre-reading"></a>建议的读前准备

* [Media Graph 扩展](media-graph-extension-concept.md)
* [gRPC 扩展协议](grpc-extension-protocol.md)
* [推理元数据架构](inference-metadata-schema.md)
* [GRPC 简介](https://www.grpc.io/docs/what-is-grpc/introduction/)
* [proto3 语言指南](https://developers.google.com/protocol-buffers/docs/proto3)

## <a name="prerequisites"></a>先决条件

* X86-64 或 ARM64 设备，运行其中一个 [受支持的 Linux 操作系统](https://docs.microsoft.com/azure/iot-edge/support#operating-systems) 或 Windows 计算机。
* 在计算机上[安装 Docker](https://docs.docker.com/desktop/#download-and-install) 。
* 安装 [IoT Edge 运行时](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge?tabs=linux)。

## <a name="grpc-implementation-steps"></a>gRPC 实现步骤

若要创建 gRPC 推理服务器并使用实时视频分析将其实现为扩展，请执行以下步骤：

### <a name="setup"></a>设置：

执行必要的步骤来 [部署实时视频分析模块并在 IoT Edge 设备上工作](deploy-iot-edge-device.md)。

### <a name="high-level-implementation-steps"></a>高级别实现步骤：

1. 选择 gRPC 支持的多种语言之一： c #、c + +、Dart、中转、Java、Node、目标-C、PHP、Python、Ruby。
1. 实现将使用 [proto3 文件](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc)与实时视频分析通信的 gRPC 服务器。

    :::image type="content" source="./media/develop-deploy-grpc-inference-srv-how-to/inference-srv-container-process.png" alt-text="将使用 proto3 文件与实时视频分析通信的 gRPC 服务器":::

    在此服务中：
    1. 处理服务器和客户端之间的会话说明消息交换。
    1. 处理 [媒体示例消息](https://github.com/Azure/live-video-analytics/blob/master/contracts/grpc/extension.proto) 并返回结果。

        1. 调用推断引擎，该引擎使用定型模型对传入消息进行推断。
        1. 从引擎接收推断结果，将其打包回 media 样本，并使用 [推断](https://github.com/Azure/live-video-analytics/blob/master/contracts/grpc/inferencing.proto) 文件提交回实时视频分析。

            或者，将任何媒体转换函数调用到媒体示例。
1. 部署 gRPC 服务器实现。 可以通过两种方法执行此操作：

    1. 部署为与实时视频分析模块共存的 IoT 模块
    1. 将作为 IoT 模块部署到可在本地或云)  (的网络可访问的节点，这些节点可以与实时视频分析模块交换数据。
1. 使用实时视频分析模块配置实时视频分析图形拓扑，并将其指向 gRPC 服务器。

### <a name="recommendation"></a>建议：

在同一节点上并置时，可以使用共享内存来获得最佳性能。 这要求你使用由编程语言/环境公开的 Linux 共享内存功能。

1. 打开 Linux 共享内存句柄。
1. 收到帧后，访问共享内存中的地址偏移量。
1. 确认帧处理完成，使实时视频分析可以回收其内存。

## <a name="create-a-grpc-inference-server"></a>创建 gRPC 推理服务器

现在，你将构建一个 IoT Edge 模块 (External AI) ，该模块通过使用 [protobuf](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc) 消息通过共享内存从实时视频分析接受视频帧，将帧归类为 "深色" 或 "浅色"，并使用 [推理元数据架构](inference-metadata-schema.md)将推理结果返回到实时视频分析中的 IoT 中心消息接收器。

:::image type="content" source="./media/develop-deploy-grpc-inference-srv-how-to/external-ai.png" alt-text=" (External AI 构建 IoT Edge 模块) ":::

此 gRPC 推理服务器是构建的 .NET Core 控制台应用程序，用于处理实时视频分析和自定义 AI 之间发送的 [protobuf](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc) 消息。 下面是实时视频分析和 gRPC 推理服务器之间的消息流：

1. 实时视频分析发送媒体流描述符 (参见 [extension](https://github.com/Azure/live-video-analytics/blob/master/contracts/grpc/extension.proto)) ，它定义将在 gRPC 流会话中通过视频帧发送到服务器的 [protobuf](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc) 消息的媒体流信息。 
1. 服务器验证并确认流描述符，并设置所需的数据传输方法。
1. 实时视频分析随即开始发送包含视频帧的 MediaSample 文件。
1. 服务器会在收到视频帧后对其进行分析，并使用你定义的图像处理器开始处理它们。
1. 服务器随后会在 [protobuf](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc) 消息可用时立即将其返回。 

    :::image type="content" source="./media/develop-deploy-grpc-inference-srv-how-to/grpc-external-srv.png" alt-text="创建 gRPC 推理服务器":::

视频帧可以通过 [共享内存](https://en.wikipedia.org/wiki/Shared_memory#:~:text=In%20computer%20science%2C%20shared%20memory,of%20passing%20data%20between%20programs.) 传输，也可以嵌入到 protobuf 消息中。 数据传输模式可以在 LVA graph 拓扑中进行配置，以确定如何传输帧。 为此，可配置 MediaGraphGrpcExtension 属性的 **dataTransfer** 元素，如下所示：

集成

```
"dataTransfer": {
              "mode": "Embedded"
            }
```

共享内存：

```
"dataTransfer": {
              "mode": "sharedMemory",
              "SharedMemorySizeMiB": "20"
            }
```

> [!NOTE]
> 当通过共享内存通信时，IpcMode 的值应设置为 "可 **共享** "，并在 gRPC 服务器模块中将 IpcMode 的值设置为 " **container： {CONTAINER_NAME}**"。 这些设置将在用于将模块部署到 Azure IoT 中心的部署清单文件中进行。 下面是设置 IoT Edge 模块时要使用的容器选项的示例。

实时视频分析模块：

```
{
    "HostConfig": {
        "LogConfig": {
            "Config": {
                "max-size": "10m",
                "max-file": "10"
            }
        },
        "IpcMode": "shareable"
    }
}
```

gRPC 扩展模块：

```
{
    "HostConfig": {
        "LogConfig": {
            "Config": {
                "max-size": "10m",
                "max-file": "10"
            }
        },
        "IpcMode": "container:lvaEdge"
    }
}
```

> [!NOTE]
> 确保可以访问 grpcExtension 中 "container： lvaEdge" 的共享内存区域。

## <a name="sample-grpc-server"></a>示例 gRPC 服务器

若要了解如何开发 gRPC 服务器的详细信息，请查看我们的代码示例。

1. 从 GitHub 链接克隆存储库 [https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp](https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp) 。
1. 启动 VSCode 并导航到/src/edge/modules/grpcExtension 文件夹。
1. 让我们对文件进行快速演练：

    1. **Program.cs**：这是应用程序的入口点。 它负责初始化和管理将充当主机的 gRPC 服务器。 在我们的示例中，从 gRPC 客户端侦听传入的 gRPC 消息的端口 (如) 上的实时视频分析）由 AppConfig.js上的中的 grpcBindings 配置元素指定。
    
        ```json    
        {
          "grpcBinding": "tcp://0.0.0.0:5001"
        }
        ```
    1. **Services\MediaGraphExtensionService.cs**：此类负责处理 [protobuf](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc) 消息。 它将读取消息中的帧，调用 ImageProcessor 并写入推理结果。
现在，我们已配置并初始化了 gRPC 服务器端口连接，接下来让我们看看如何处理传入的 gRPC 消息。

        * 建立 gRPC 会话后，gRPC 服务器将从客户端接收的第一条消息 (实时视频分析) 是在 [extension proto](https://github.com/Azure/live-video-analytics/blob/master/contracts/grpc/extension.proto) 文件中定义的 MediaStreamDescriptor。 

            ```
            message MediaStreamDescriptor {
              oneof stream_identifier {
                GraphIdentifier graph_identifier = 1;     // Media Stream graph identifier
                string extension_identifier = 2;          // Media Stream extension identifier
              }
              MediaDescriptor media_descriptor = 5;       // Session media information.
              // Additional data transfer properties. If none is set, it is assumed
              // that all content will be transferred through messages (embedded transfer).
              oneof data_transfer_properties {
                SharedMemoryBufferTransferProperties shared_memory_buffer_transfer_properties = 10;
              }
            }
            ```
        * 在我们的服务器实现中，方法 `ProcessMediaStreamDescriptor` 将验证视频文件的 MediaStreamDescriptor 的 MediaDescriptor 属性，然后将使用共享内存或使用嵌入帧传输) 模式 (来设置数据传输模式，具体取决于你在拓扑中指定的内容和使用的部署模板文件。 
        * 收到消息并成功设置数据传输模式后，gRPC 服务器会将 MediaStreamDescriptor 消息作为确认返回给客户端，从而在服务器和客户端之间建立连接。    
        * 实时视频分析收到确认后，会开始将媒体流传输到 gRPC 服务器。 在我们的服务器实现中，方法 `ProcessMediaStream` 将处理传入的 MediaStreamMessage。 MediaStreamMessage 也是在 [extension](https://github.com/Azure/live-video-analytics/blob/master/contracts/grpc/extension.proto)中定义的。

            ```
            message MediaStreamMessage {
              uint64 sequence_number = 1;       // Monotonically increasing directional message identifier starting from 1 when the gRPC connection is created
              uint64 ack_sequence_number = 2;   // 0 if this message is not referencing any sent message.
            
            
              // Possible payloads are strongly defined by the contract below
              oneof payload {
                MediaStreamDescriptor media_stream_descriptor = 5;
                MediaSample media_sample = 6;
              }
            }
            ```
        * 根据 Appconfig.js上的 batchSize 的值，我们的服务器将继续接收消息，并将视频帧存储在列表中。 达到 batchSize 限制后，该函数将调用函数或将处理该映像的文件。 在我们的示例中，方法调用名为 BatchImageProcessor.cs 的文件
    1. **Processors\BatchImageProcessor.cs**：此类负责)  (处理图像。 在此示例中，我们使用了图像分类模型。 对于要处理的每个映像，使用的算法如下所示：

        1. 转换字节数组中的图像以进行处理。 请参阅方法： `GetBytes(Bitmap image)`
        
            我们使用的示例处理器仅支持 JPG 编码图像帧和 None 作为像素格式。 如果自定义处理器支持另一种编码和/或格式，请更新 `IsMediaFormatSupported` processor 类的方法。
        1. 使用 [ColorMatrix 类](https://docs.microsoft.com/dotnet/api/system.drawing.imaging.colormatrix?redirectedfrom=MSDN&view=dotnet-plat-ext-3.1&preserve-view=true)，将图像转换为灰色缩放。 请参阅方法： `ToGrayScale(Image source)` 。
        1. 获取灰色缩放图像后，我们将计算灰色刻度字节的平均值。
        1. 如果平均值 < 127，则将图像归类为 "深色"，否则我们会将其分类为 "light"，置信度值为1.0。 请参阅方法： `ProcessImage(List<Image> images)` 。

    通过修改此类或添加新的类并实现方法，可以添加自己的处理器逻辑：

    ```
    IEnumerable<Inference> ProcessImage(List<Image> images) 
    ```

    添加新类后，必须更新 MediaGraphExtensionService.cs，使其实例化类，并对其调用 ProcessImage 方法来运行处理逻辑。 

## <a name="connect-with-live-video-analytics-module"></a>与实时视频分析模块连接

现在，你已创建了 gRPC 扩展模块，接下来我们将创建并部署 media graph 拓扑。

1. 使用 Visual Studio Code，按照[以下说明](https://docs.microsoft.com/azure/iot-edge/tutorial-develop-for-linux#build-and-push-your-solution)登录到 Docker。
1. 在 Visual Studio Code 中，转到 src/edge。 你可看到 .env 文件以及一些部署模板文件。

    部署模板是指边缘设备的部署清单。 它包含一些占位符值。 该 .env 文件包含这些变量的值。
    
    然后选择“生成并推送 IoT Edge 解决方案”。 对于此步骤，请使用 src/edge/deployment.grpc.template.js。
        
    :::image type="content" source="./media/develop-deploy-grpc-inference-srv-how-to/build-push-iot-edge-solution.png" alt-text="与实时视频分析模块连接":::
    
    此操作将生成 grpc 服务器模块，并将映像推送到 Azure 容器注册表。
    检查确认已在 .env 文件中定义 CONTAINER_REGISTRY_USERNAME_myacr 和 CONTAINER_REGISTRY_PASSWORD_myacr 环境变量。
1. 转到 src/cloud-to-device-console-app 文件夹。 你可在此处看到 appsettings.json 文件和一些其他文件：

    * c2d-console-app.csproj - Visual Studio Code 的项目文件。
    * operations.json - 希望程序运行的操作的列表。
    * Program.cs - 示例程序代码。 此代码：

        * 加载应用设置。
        * 调用 IoT Edge 模块上的实时视频分析公开的直接方法。 可以通过调用模块的[直接方法](direct-methods.md)来使用该模块分析实时视频流。
        * 暂停以检查“终端”窗口中程序的输出，并检查“输出”窗口中模块生成的事件 。
        * 调用直接方法以清理资源。
1. 编辑 operations.json 文件：

    * 将链接更改为图拓扑：

        * `"topologyUrl" : https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/grpcExtension/topology.json`
        * 在 GraphInstanceSet 下，编辑图形拓扑的名称以匹配前面链接中的值：<br/>`"topologyName": "InferencingWithGrpcExtension"`
        * 在 GraphTopologyDelete 下，编辑名称：<br/>`"name": "InferencingWithGrpcExtension"`

            拓扑 (例如， `https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/grpcExtension/topology.json`) 必须定义扩展地址：
    * 扩展地址参数

        ```
        {
            "name": "grpcExtensionAddress",
            "type": "String",
            "description": "gRPC LVA Extension Address",
            "default": "https://<REPLACE-WITH-IP-OR-CONTAINER-NAME>/score"
        },
        ```
    * 配置

        ```
        {
            "@apiVersion": "1.0",
            "name": "TopologyName",
            "properties": {
            "processors": [
              {
                "@type": "#Microsoft.Media.MediaGraphGrpcExtension",
                "name": "grpcExtension",
                "endpoint": {
                  "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
                  "url": "${grpcExtensionAddress}",
                  "credentials": {
                    "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
                    "username": "${grpcExtensionUserName}",
                    "password": "${grpcExtensionPassword}"
                  }
                },
                "dataTransfer": {
                        "mode": "sharedMemory",
                        "SharedMemorySizeMiB": "5"
                },
                "image": {
                  "scale": {
                    "mode": "${imageScaleMode}",
                    "width": "${frameWidth}",
                    "height": "${frameHeight}"
                  },
                  "format": {
                    "@type": "#Microsoft.Media.MediaGraphImageFormatEncoded",
                    "encoding": "${imageEncoding}",
                    "quality": "${imageQuality}"
                  }
                }
            ]
          }
        }
        ```
    
## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>生成并部署 IoT Edge 部署清单

部署清单定义要部署到边缘设备的模块以及这些模块的配置设置。 请按照以下步骤从模板文件生成清单，然后将其部署到边缘设备。
此步骤将在上的 src/Edge/config/deployment.grpc.amd64.js中创建 IoT Edge 部署清单。 右键单击该文件，然后选择 " **创建单个设备的部署**"。

:::image type="content" source="./media/develop-deploy-grpc-inference-srv-how-to/create-deployment-single-device.png" alt-text="生成并部署 IoT Edge 部署清单":::

接下来，Visual Studio Code 会要求你选择 IoT 中心设备。 选择 IoT Edge 设备（应为 lva-sample-device）。
在此阶段，将启动将边缘模块部署到 IoT Edge 设备的过程。 大约 30 秒后，在 Visual Studio Code 的左下部分刷新 Azure IoT 中心。 应该会看到，新模块已部署为 lvaExtension。

:::image type="content" source="./media/develop-deploy-grpc-inference-srv-how-to/devices.png" alt-text="已部署名为 lvaExtension 的新模块":::

## <a name="next-steps"></a>后续步骤

按照在模型快速入门中 [分析实时视频](use-your-model-quickstart.md)中提到的 "**准备监视事件**" 步骤运行示例并解释结果。 另外，请查看我们的示例 gRPC 拓扑： [gRPCExtension](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/grpcExtension/topology.json)、 [CVRWithGrpcExtension](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/cvr-with-grpcExtension/topology.json)、[EVRtoAssetsByGrpcExtension 和 [EVROnMotionPlusGrpcExtension](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/motion-with-grpcExtension/topology.json)。

