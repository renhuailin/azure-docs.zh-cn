---
title: QnA Maker 托管现在已重命名为自定义问答
titleSuffix: Azure Cognitive Services
description: 本文包含有关 QnA Maker 功能变更的新闻。
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 05/11/2021
ms.custom: references_regions
ms.openlocfilehash: 6e8f9a26836be14d4952ba309933511635ff673a
ms.sourcegitcommit: e39ad7e8db27c97c8fb0d6afa322d4d135fd2066
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111982639"
---
# <a name="qna-maker-managed-is-now-renamed-to-custom-question-answering"></a>QnA Maker 托管现在已重命名为自定义问答

[QnA Maker 托管（预览版）](https://techcommunity.microsoft.com/t5/azure-ai/introducing-qna-maker-managed-now-in-public-preview/ba-p/1845575)于 2020 年 11 月推出，是一种免费的公共预览版产品/服务。 它引入了一些新功能，包括使用深度学习排名程序的增强型相关性、精确答案和端到端区域支持。 

QnA Maker 托管现已成为文本分析中的一项功能，并已重命名为自定义问答，这是为了整合认知服务中的语言产品/服务。  

## <a name="creating-a-new-custom-question-answering-service"></a>创建新的自定义问答服务

[创建文本分析资源](https://ms.portal.azure.com/?quickstart=true#create/Microsoft.CognitiveServicesTextAnalytics)，以使用问答和其他功能，例如实体识别、情绪分析，等等。  

现在，当你创建新的文本分析资源时，你可以选择想要包括的功能。 选择“自定义问答(预览版)”，继续创建你的资源。  

> [!div class="mx-imgBorder"]
> [ ![创建文本分析资源 UI 菜单的屏幕截图，其中选择了自定义问答功能]( ./media/select-feature.png) ]( ./media/select-feature.png#lightbox)


你不能再通过 QnA Maker 创建流来创建 QnA Maker 托管资源，系统会将你重定向到文本分析服务。 QnA Maker 稳定版本不会有任何变化。 

> [!div class="mx-imgBorder"]
> [ ![资源创建菜单的屏幕截图]( ./media/create-resource.png) ]( ./media/create-resource.png#lightbox)

## <a name="details"></a>详细信息

- 所有现有 QnA Maker 托管（预览版）资源将继续像以前一样工作。 目前不需要对这些资源执行任何操作。
- 自定义问答（预览版）的创建流是主要更改。 服务、门户、终结点、SDK 等仍保持原样。
- 自定义问答（预览版）继续作为免费公共预览版提供。 此功能仅作为文本分析标准资源的一部分提供。 请勿将文本分析资源的定价层更改为“免费”。
- 自定义问答（预览版）在以下区域提供：
    - 美国中南部
    - 北欧
    - 澳大利亚东部。

## <a name="next-steps"></a>后续步骤

* [QnA Maker 客户端库入门](./quickstarts/quickstart-sdk.md)
* [QnA Maker 门户入门](./quickstarts/create-publish-knowledge-base.md)

