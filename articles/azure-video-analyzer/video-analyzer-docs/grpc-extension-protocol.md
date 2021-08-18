---
title: gRPC 扩展协议 - Azure
description: Azure 视频分析器支持通过管道扩展节点增强其处理功能。 gRPC 扩展处理器使用基于 gRPC 的高性能结构化协议实现可扩展性方案。
ms.topic: reference
ms.date: 06/01/2021
ms.openlocfilehash: 7d6d60b0f9f6473cf226b8cecff0440ff90f008b
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2021
ms.locfileid: "114605067"
---
# <a name="use-the-grpc-extension-protocol"></a>使用 gRPC 扩展协议 

Azure 视频分析器支持通过[管道扩展节点](pipeline-extension.md)增强其管道处理功能。 gRPC 扩展处理器使用[基于 gRPC 的高性能结构化协议](pipeline-extension.md#grpc-extension-processor)实现可扩展性方案。

在本文中，你将学习如何使用 gRPC 扩展协议在视频分析器模块与 gRPC 服务器之间发送消息，gRPC 服务器会处理这些消息并返回结果。 gRPC 是一种新式的开放源代码高性能 RPC 框架，可在任何环境中运行且支持跨平台和跨语言的通信。 gRPC 传输服务在以下两者之间使用 HTTP/2 双向流式传输：

* gRPC 客户端（视频分析器模块）和
* gRPC 服务器（自定义扩展）。

gRPC 会话是通过 TCP/TLS 端口从 gRPC 客户端到 gRPC 服务器的单一连接。
在单个会话中：客户端通过 gRPC 流会话将媒体流描述符（后跟视频帧）作为 [protobuf](https://github.com/Azure/video-analyzer/tree/main/contracts/grpc) 消息发送到服务器。 服务器验证流描述符、分析视频帧，并将推理结果作为 protobuf 消息返回。

强烈建议使用有效的 JSON 文档返回响应，并采用按照[推理元数据架构对象模型](inference-metadata-schema.md)定义的预建立架构。 这将更好地确保与其他组件和场景（例如通过推理元数据录制和播放视频）的互操作性。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/grpc-extension-protocol/grpc-external-srv.svg" alt-text="Azure 视频分析器模块" lightbox="./media/grpc-extension-protocol/grpc-external-srv.svg":::

## <a name="implementing-grpc-protocol"></a>实现 gRPC 协议

### <a name="creating-a-grpc-connection"></a>创建 gRPC 连接

自定义扩展插件必须实现以下 gRPC 服务：

```
service MediaGraphExtension
    {
        rpc ProcessMediaStream(stream MediaStreamMessage) returns (stream MediaStreamMessage);
    }
```

调用时，将打开双向流，以便消息在 gRPC 扩展和视频分析器实时管理之间流动。 每个参与方在此流中发送的第一条消息都将包含一个 MediaStreamDescriptor，它定义将在后面的 MediaSample 中发送的具体信息。

例如，扩展可发送该消息（此处以 JSON 表示），表示它会将 gRPC 消息中嵌入的 416x416 rgb24 编码的帧发送到自定义扩展。

```
 {
    "sequence_number": 1,
    "ack_sequence_number": 0,
    "media_stream_descriptor": 
    {
        "graph_identifier": 
        {
            "media_services_arm_id": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/videoanalyzers/{video-analyzer-account-name}",
            "graph_instance_name": "{live-pipeline-name}",
            "graph_node_name": "{grpc-extension-node-name}"
        },
        "media_descriptor": 
        {
            "timescale": 90000,
            "video_frame_sample_format": 
            {
                "encoding": "RAW",
                "pixel_format": "RGB24",
                "dimensions": 
                {
                    "width": 416,
                    "height": 416
                },
                "stride_bytes": 1248
            }
        }
    }
}
```

自定义扩展将发送以下消息作为响应，该消息表示它仅返回推理。

```
{
    "sequence_number": 1,
    "ack_sequence_number": 1,
    "media_stream_descriptor": 
    {
        "extension_identifier": "customExtensionName"    
    }
}
```

现在双方已交换媒体描述符，接下来视频分析器开始将帧传输到 gRPC 服务器。

> [!NOTE]
> 可以使用你选择的编程语言来实现 gRPC 服务器。

### <a name="sequence-numbers"></a>序列号

gRPC 扩展节点和自定义扩展都将保留一组各自分配给其消息的序列号。 这些序列号应从 1 开始单调递增。 如果未确认任何消息（发送第一条消息时可能会发生此情况），可忽略 ack_sequence_number。

完全处理相应的消息后，必须确认请求。 如果是共享内存传输，则此确认表示发送方可释放共享内存，而接收方将不再访问它。 在确认之前，发送方必须保留任何共享内存。

### <a name="reading-embedded-content"></a>读取嵌入式内容

可通过 content_bytes 字段直接从 MediaSample 消息中读取嵌入式内容。 将根据 MediaDescriptor 对数据进行编码。

### <a name="reading-content-from-shared-memory"></a>从共享内存读取内容

通过共享内存传输内容时，发送方会在首次建立会话时将 SharedMemoryBufferTransferProperties 包含在其 MediaStreamDescriptor 中。 此内容如下所示（以 JSON 表示）：

```
{
  "handle_name": "inference_client_share_memory_2146989006636459346"
  "length_bytes": 20971520
}
```

然后，接收方打开文件 /dev/shm/inference_client_share_memory_2146989006636459346。 发件人将发送 MediaSample 消息，其中包含引用此文件中特定位置的 ContentReference。

```
{
    "timestamp": 143598615750000,
    "content_reference": 
    {
        "address_offset": 519168,
        "length_bytes": 173056
    }
}
```

然后，接收方从文件中的此位置读取数据。

为了使视频分析器边缘模块能够通过共享内存通信，必须正确配置容器的 IPC 模式。 可采用多种方式完成此操作，但这里提供了一些建议的配置。

* 与在主机设备上运行的 gRPC 推理引擎通信时，应将 IPC 模式设置为“主机”。
* 与在另一个 IoT Edge 模块中运行的 gRPC 服务器通信时，应将视频分析器模块的 IPC 模式设置为 `shareable`，将自定义扩展的 IPC 模式设置为 `container:avaedge`，其中 `avaedge` 是视频分析器模块的名称。

下面是使用上述第一个选项时，设备孪生中可能呈现的内容。

```
"avaedge": 
{
  "version": "1.0",
  "type": "docker",
  "status": "running",
  "restartPolicy": "always",
  "settings": 
  {
    "image": "mcr.microsoft.com/media/video-analyzer:1",
    "createOptions": 
      "HostConfig": 
      {
        "IpcMode": "shareable"
      }
  }
}
```

有关 IPC 模式的详细信息，请参阅 [IPC 设置 (--ipc)](https://docs.docker.com/engine/reference/run/#ipc-settings---ipc)。

## <a name="video-analyzer-grpc-extension-contract-definitions"></a>视频分析器 gRPC 扩展协定定义

本部分定义可定义数据流的 gRPC 协定。

### <a name="protocol-messages"></a>协议消息

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/grpc-extension-protocol/contract-definitions.png" alt-text="视频分析器协议消息"  lightbox="./media/grpc-extension-protocol/contract-definitions.png":::

### <a name="client-authentication"></a>客户端身份验证

自定义扩展的实施者可验证传入的 gRPC 连接的真实性，以确保这些连接来自 gRPC 扩展节点。 该节点将在请求头中提供要验证的条目。

可使用用户名/密码凭据来完成此操作。 创建 gRPC 扩展节点时，将提供如下所示的凭据：

```
{
  "@type": " #Microsoft.VideoAnalyzer.GrpcExtension ",
  "name": "{moduleIdentifier}",
  "endpoint": 
  {
    "@type": " #Microsoft.VideoAnalyzer.UnsecuredEndpoint ",
    "url": "tcp://customExtension:8081",
    "credentials": 
    {
      "@type": "#Microsoft. VideoAnalyzer.UsernamePasswordCredentials",
      "username": "username",
      "password": "password"
    }
  }
  // Other fields omitted
}
```

发送 gRPC 请求时，以下标头将包含在请求元数据中，模仿 HTTP 基本身份验证。

```
x-ms-authentication: Basic (Base64 Encoded username:password)
```

## <a name="configuring-inference-server-for-each-live-pipeline-over-grpc-extension"></a>通过 gRPC 扩展为每个实时管道配置推理服务器

配置推理服务器时，无需为推理服务器中打包的每个 AI 模型公开节点。 相反，对于实时管道，可以使用 GrpcExtension 节点的 `extensionConfiguration` 属性，并定义选择 AI 模型的方式。 在执行期间，视频分析器会将这个字符串传递给推理服务器，推理服务器可以使用它来调用所需的 AI 模型。 此 `extensionConfiguration property` 是一个可选属性，并且特定于 gRPC 服务器的实现。 该属性的用法如下：

```
{
  "@type": "#Microsoft.VideoAnalyzer.GrpcExtension",
  "name": "{moduleIdentifier}",
  "endpoint": 
  {
    "@type": "#Microsoft.VideoAnalyzer.UnsecuredEndpoint",
    "url": "${grpcExtensionAddress}",
    "credentials": 
    {
      "@type": "#Microsoft.VideoAnalyzer.UsernamePasswordCredentials",
      "username": "${grpcExtensionUserName}",
      "password": "${grpcExtensionPassword}"
    }
  },
    // Optional server configuration string. This is server specific 
  "extensionConfiguration": "{Optional extension specific string}",
  "dataTransfer": 
  {
    "mode": "sharedMemory",
    "SharedMemorySizeMiB": "75"
  }
    //Other fields omitted
}
```

## <a name="using-grpc-over-tls"></a>通过 TLS 使用 gRPC

可通过 TLS 为用于推理的 gRPC 连接提供保护。 无法保证视频分析器和推理引擎之间的网络安全时，这非常有用。 TLS 会对嵌入 gRPC 消息中的任何内容进行加密，导致在以高速率传输帧时产生额外的 CPU 开销。

gRPC 不支持 `IgnoreHostname` 和 `IgnoreSignature` 验证选项，因此推理引擎提供的服务器证书必须包含与 gRPC 扩展节点的终结点 URL 中 IP 地址/主机名完全匹配的公用名称 (CN)。

## <a name="next-steps"></a>后续步骤

了解[推理元数据架构](inference-metadata-schema.md)
