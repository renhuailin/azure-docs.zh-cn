---
title: HTTP 扩展协议 - Azure
description: Azure 视频分析器支持通过管道扩展节点增强其处理功能。 HTTP 扩展处理器支持使用 HTTP 协议的可扩展性方案，其中性能和/或最佳资源利用率不是主要问题。
ms.topic: reference
ms.date: 06/01/2021
ms.openlocfilehash: 0c87bf83692798fd416f9d416d1f2a217c99d40a
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2021
ms.locfileid: "114604852"
---
# <a name="use-the-http-extension-protocol"></a>使用 HTTP 扩展协议 

Azure 视频分析器支持通过[管道扩展](pipeline-extension.md)节点增强其处理功能。 HTTP 扩展处理器节点支持使用 HTTP 扩展协议的可扩展性方案，其中性能和/或最佳资源利用率不是主要问题。 本文介绍如何使用此协议在视频分析器和 HTTP REST 终结点之间发送消息，该终结点通常包装在 AI 推理服务器周围。

HTTP 协定在以下两个组件之间定义：

* HTTP 服务器
* 视频分析器模块充当 HTTP 客户端

## <a name="http-contract"></a>HTTP 协议

### <a name="request"></a>请求

从视频分析器模块到 HTTP 服务器的请求如下所示：

|密钥|值|
|---|---|
|POST|https://hostname/optional-path?optional-query|
|Accept|application/json|
|授权| 基本、摘要式和持有者（通过自定义标头支持）|
|Content-Type|  image/jpeg<br/>image/png<br/>image/bmp<br/>image/x-raw|
|Content-Length 正文长度（以字节为单位）   ||
|User-Agent |Azure 媒体服务|
|正文|  图像字节数，二进制编码为某个受支持的内容类型。|
```

### Example

```html
POST http://localhost:8080/inference HTTP/1.1
Host: localhost:8080
x-ms-client-request-id: d6050cd4-c9f2-42d3-9adc-53ba7e440f17
Content-Type: image/bmp
Content-Length: 519222

(Image Binary Content)
```

### <a name="response"></a>响应

从推理服务器到视频分析器模块的响应应如下所示：

|密钥|   值|
|---|----|
|状态代码|  200 正常 - 找到了推理结果<br/>204 无内容 - AI 未找到结果<br/>400 错误的请求 - 非预期<br/>500 内部服务器错误 - 非预期<br/>503 服务器繁忙 - 视频分析器将基于“Retry-After”标头（如果未预设标头，则基于默认时间量）回退。|
|Content-Type   |application/json|
|Content-Length |以字节为单位的正文长度。|
|正文|  具有单个“inferences”属性的 JSON 对象。|

### <a name="example"></a>示例

```html
HTTP/1.1 200 OK
Content-Type: application/json
Content-Length: 468
Server: Microsoft-HTTPAPI/2.0
Date: Fri, 17 Apr 2021 04:44:01 GMT

{
  "inferences": [
    {
      "type": "entity",
      "entity": {
        "tag": { "value": "car", "confidence": 0.9048132 },
        "box": { "l": 0.42681578, "t": 0.47660735, "w": 0.019501392, "h": 0.020954132 }
      }
    },
    {
      "type": "entity",
      "entity": {
        "tag": { "value": "car", "confidence": 0.8953932 },
        "box": { "l": 0.55083525, "t": 0.4843858, "w": 0.046550274, "h": 0.046502113 }
      }
    }    
  ]
}
```

建议使用有效的 JSON 文档返回响应，并采用按照[推理元数据架构对象模型](inference-metadata-schema.md)定义的预建立架构。 符合该架构将确保与视频分析器中其他组件的互操作性，例如，能够跟踪实时视频中的对象，以及能够在播放期间将推理元数据覆盖在视频上，如[此处](record-stream-inference-data-with-video.md)所演示。

如果你的模块返回的响应的内容类型不是“application/json”，则视频分析器会将消息编码为 base 64 内容，并将其序列化为不透明的 JSON 有效负载。

如果你的模块返回的响应的内容类型为“application/json”，但 JSON 架构不遵循上述推理元数据架构，则将通过管道转发消息有效负载，但互操作性会降低。

> [!NOTE]
> 如果你的推理服务器针对给定的图像未生成任何结果，则它必须返回响应正文为空的 HTTP 204 状态代码（无内容）。 视频分析器会将此内容视为空结果，不会在整个管道中转发该事件。

## <a name="next-steps"></a>后续步骤

[了解 gRPC 扩展协议](grpc-extension-protocol.md)


