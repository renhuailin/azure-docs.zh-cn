---
title: 推理元数据架构 - Azure
description: 在 Azure 视频分析器中，无论是使用基于 HTTP 的协定还是基于 gRPC 的协定，每个推理对象都采用本主题中所述的对象模型。
ms.topic: reference
ms.date: 06/01/2021
ms.openlocfilehash: bacf69638b3b9f9b8feb86030b1a8498eb69260a
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2021
ms.locfileid: "114601531"
---
# <a name="inference-metadata-schema"></a>推理元数据架构 

在 Azure 视频分析器中，无论是使用基于 HTTP 的协定还是基于 gRPC 的协定，每个推理对象都采用本主题中所述的对象模型。

## <a name="object-model"></a>对象模型

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/inference-metadata-schema/object-model.png" alt-text="Azure 视频分析器 Oot 模型" lightbox="./media/inference-metadata-schema/object-model.png":::

 
|类型定义|说明|
|---|---|
|标记|与结果关联的标记或标签。 除了标记，还可以获得与标记相关联的置信度。|
|特性|与结果相关的其他属性。 可添加从推理引擎收到的新属性以及可信度值。|
|特性列表|可选属性列表。|
|Rectangle|相对于图像左上角的矩形区域。 所需的属性为“长度”、“宽度”、“高度”和“上边缘与原点的距离”。|
|分类|通常适用于整个示例的分类器的标签。 借助于“标记”，可对结果进行分类。|
|实体|在示例中检测或识别到的实体。 当推理引擎检测到实体时，该实体会获得一个“标记”，将返回推断出的其他属性以及发现的实体周围的矩形框坐标。|
|事件|在示例上检测到的事件。 在示例中检测到事件时，将返回该事件的名称和该事件特定的属性。|
|移动|在示例上检测到的运动。 在示例中检测到运动时，将返回检测到运动所在的矩形边界框的坐标。|
|文本|返回与示例相关的文本以及该文本的开始和结束时间戳。|
|其他|返回其他通用有效负载信息。|

下面的示例包含单个事件和一些受支持的推理类型：

```
[ 
  // Light Detection 
  { 
    "type": "classification", 
    "subtype": "lightDetection", 
    "classification": { 
      "tag": { "value": "daylight", "confidence": 0.86 }, 
      "attributes": [ 
          { "name": "isBlackAndWhite", "value": "false", "confidence": 0.71 } 
      ] 
    } 
  }, 
 
  // Motion Detection 
  { 
    "type": "motion", 
    "subtype": "motionDetection", 
    "motion": 
    { 
      "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 } 
    } 
  }, 
 
  // Yolo V3 
  { 
    "type": "entity", 
    "subtype": "objectDetection",     
    "entity": 
    { 
      "tag": { "value": "dog", "confidence": 0.97 }, 
      "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 } 
    } 
  }, 
 
  // Vehicle Identification 
  { 
    "type": "entity", 
    "subtype": "vehicleIdentification",     
    "entity": 
    { 
      "tag": { "value": "007-SPY", "confidence": 0.82 }, 
      "attributes":[   
        { "name": "color", "value": "black", "confidence": 0.90 }, 
        { "name": "body", "value": "coupe", "confidence": 0.87 }, 
        { "name": "make", "value": "Aston Martin", "confidence": 0.35 }, 
        { "name": "model", "value": "DBS V12", "confidence": 0.33 } 
      ], 
      "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 } 
    } 
  }, 
 
  // People Identification 
  { 
    "type": "entity", 
    "subtype": "peopleIdentification",     
    "entity": 
    { 
      "tag": { "value":"Erwin Schrödinger", "confidence": 0.50 }, 
      "attributes":[   
        { "name": "age", "value": "73", "confidence": 0.87 }, 
        { "name": "glasses", "value": "yes", "confidence": 0.94 } 
      ], 
      "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 } 
    }, 
 
    // Open type coming from the gRPC Map 
    "extensions":  
    { 
      "vector": "e1xkaXNwbGF5c3R5bGUgaVxoYmFyIHtcZnJhYyB7ZH17ZHR9fVx2ZXJ0IFxQc2kgKHQpXHJhbmdsZSA9e1xoYXQge0h9fVx2ZXJ0IFxQc2kgKHQpXHJhbmdsZSB9KQ==", 
      "skeleton": "p1,p2,p3,p4" 
    } 
  }, 
 
  // Captions 
  {     
    "type": "text", 
    "subtype": "speechToText",   
    "text": 
    { 
      "value": "Humor 75%. Confirmed. Self-destruct sequence in T minus 10… 9…", 
      "language": "en-US", 
      "startTimestamp": 12000, 
      "endTimestamp": 13000 
    } 
]
```

## <a name="next-steps"></a>后续步骤

- [gRPC 数据协定](./grpc-extension-protocol.md)
- [HTTP 数据协定](./http-extension-protocol.md)
