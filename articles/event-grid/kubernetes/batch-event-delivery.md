---
title: Kubernetes 上的 Azure 事件网格 - 批量事件传送
description: 本文介绍如何将一批事件传送至目标。
author: jfggdl
ms.subservice: kubernetes
ms.author: jafernan
ms.date: 05/25/2021
ms.topic: conceptual
ms.openlocfilehash: 46ae19af49b827af857f5f224ee5f0013d620a43
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2021
ms.locfileid: "112414586"
---
# <a name="event-grid-on-kubernetes---batch-event-delivery"></a>Kubernetes 上的事件网格 - 批量事件传送 
具有 Azure Arc 的 Kubernetes 上的事件网格支持在单个传送请求中传送多个事件。 利用此功能，无需产生每个请求的 HTTP 开销，即可增加总体传送吞吐量。 批量事件传送默认关闭，并且可以使用事件订阅配置来打开。 

[!INCLUDE [event-grid-preview-feature-note.md](../includes/event-grid-preview-feature-note.md)]

> [!WARNING]
> 即使事件处理程序代码可能需要为每个批处理请求执行更多操作，处理每个传送请求所允许的最长持续时间也不变。 传送超时默认为 60 秒。

## <a name="batch-event-delivery-policy"></a>批量事件传送策略
可以通过调整以下两项设置，为每个事件订阅自定义 Kubernetes 上的事件网格的批处理事件传送行为：

- **每批最大事件数**
    
    此设置用于设置可添加到批处理传送请求的事件数的上限。
- 首选批大小 (KB)
    
    此配置项用于进一步控制可通过每个传送请求发送的最大 KB 数。

## <a name="batch-event-delivery-behavior"></a>批量事件传送行为   

- **全或无**

    Kubernetes 上的事件网格使用“全或无”语义运行。 它不支持批量事件传送部分成功。 事件处理程序应注意，只为每批请求它们可在 60 秒内合理处理的尽可能多的事件。
- **乐观批处理**

    批处理策略设置对批处理行为的限制并不严格，并尽力得到遵守。 如果事件处理率较低，则会发现批大小小于每批请求的最大事件数。
- 批量传送默认设置为关闭

    默认情况下，Kubernetes 上的事件网格仅向每个传送请求添加一个事件。 打开批量事件传送的方法是在事件订阅有效负载中设置前文提到的其中一项设置。
- **默认值**

    创建事件订阅时，不必同时指定设置（每批最大事件数和近似批大小 (KB)）。 如果仅设置了一项设置，则 Kubernetes 上的事件网格将使用默认值（此行为可配置）。 

## <a name="example"></a>示例
以下示例显示了如何在终结点属性中设置 `maxEventsPerBatch` 和 `preferredBatchSizeInKilobytes` 以启用批处理。 

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
             {
                "endpointUrl": "<your_webhook_url>",
                "maxEventsPerBatch": 10,
                "preferredBatchSizeInKilobytes": 64
             }
        },
    }
}
```

## <a name="next-steps"></a>后续步骤
若要了解 Azure Arc for Kubernetes 上的事件网格支持的目标和处理程序，请参阅 [Kubernetes 上的事件网格 - 事件处理程序](event-handlers.md)。