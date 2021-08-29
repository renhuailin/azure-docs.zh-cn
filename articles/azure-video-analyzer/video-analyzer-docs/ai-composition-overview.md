---
title: Azure 视频分析器中的 AI 组合
description: 本文简要概述了 Azure 视频分析器对三种 AI 组合的支持。 本主题还提供了每种 AI 组合的方案说明。
ms.topic: conceptual
ms.date: 08/11/2021
ms.openlocfilehash: 709bfe642c6427e131c95d64af6de0ee410cf261
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778316"
---
# <a name="ai-composition"></a>AI 组合

本文简要概述了 Azure 视频分析器对三种 AI 组合的支持。 

* [顺序程序](#sequential-ai-composition)
* [Parallel](#parallel-ai-composition)
* [合并式](#combined-ai-composition)

## <a name="sequential-ai-composition"></a>顺序式 AI 组合

AI 节点可以按顺序组合。 这样，下游节点就可以增强由某个上游节点生成的推理。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/ai-composition/sequential.svg" alt-text="顺序式 AI 组合":::
 
### <a name="key-aspects"></a>关键方面

* 管道扩展充当媒体传递节点，并且可进行配置，让外部 AI 服务器接收速率、格式和分辨率各不相同的帧。 另外，还可以指定配置，以使外部 AI 服务器可以接收所有帧或只接收已包含推理的帧。
* 推理会在它们经过不同的扩展节点时添加到帧，可以按顺序添加无数此类节点。
* 连续视频录制或基于事件的视频录制等其他方案可以与顺序式 AI 组合进行合并。

    
## <a name="parallel-ai-composition"></a>并行式 AI 组合

AI 节点还可以采用并行方式来组合，而不是按顺序来组合。 这样，就可以针对引入的视频流执行独立推理，节省边缘的引入带宽。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/ai-composition/parallel.svg" alt-text="并行式 AI 组合":::
 
### <a name="key-aspects"></a>关键方面

* 视频可以拆分为任意数量的并行分支，并且此类拆分可以在以下节点之后的任何点发生。
    
    * RTSP 源
    * 动作检测器
    * 管道扩展

## <a name="combined-ai-composition"></a>合并式 AI 组合

顺序式和并行式组合构造都可以合并，以开发复杂的可组合的 AI 管道。 之所以有可能这样操作，是因为 AVA 管道允许扩展节点连同其他受支持节点一起无限期地按顺序合并，以及/或者通过并行组合进行合并。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/ai-composition/complex.svg" alt-text="合并式 AI 组合":::
 


## <a name="next-steps"></a>后续步骤

[通过多个使用 AI 组合的 AI 模型来分析实时视频流](analyze-ai-composition.md)
