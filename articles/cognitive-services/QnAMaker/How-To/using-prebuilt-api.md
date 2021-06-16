---
title: QnA Maker 预生成 API
titleSuffix: Azure Cognitive Services
description: 使用预生成的 API 通过文本获取答案
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 05/05/2021
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: b7e505bfbb27aca479569ff6cddfa2686674ae94
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111954117"
---
# <a name="prebuilt-question-answering"></a>预生成的问答

预生成的问答为用户提供了通过一段文本回答问题的功能，不必创建知识库、维护问答对，也不必因为基础设施未充分利用而浪费成本。 此功能作为 API 提供，可用于满足问题和解答需求，无需了解有关 QnA Maker 或其他存储的详细信息。

如果给定了用户查询和文本块/段落，则 API 会返回一个答案，在有精确答案的情况下会返回精确答案。 

<a name="qna-entity"></a>


## <a name="example-usage-of-prebuilt-question-answering"></a>预生成的问答的示例用法

假设你有一个或多个你要从中获取给定问题的答案的文本块。 通常，你必须创建与文本块数目同样多的源。 但是，使用预生成的问答，你现在可以查询文本块而无需在知识库中定义内容源。 

可以使用预生成的 API 的其他场景包括：

* 你在为最终用户开发电子书阅读器应用，该应用可以突出显示文本、输入问题，以及针对突出显示的文本查找答案 
* 使用浏览器扩展后，用户可以根据浏览器页面当前显示的内容提出问题
* 医疗机器人从用户那里获取查询，然后根据其确定的与用户查询最相关的医疗内容来提供答案 

下面是示例请求的示例：

## <a name="sample-request"></a>示例请求
```
POST https://{Endpoint}/qnamaker/v5.0-preview.2/generateanswer
```

### <a name="sample-query-over-a-single-block-of-text"></a>基于单个文本块的示例查询

请求正文

```json
{
    "question": "How long it takes to charge surface pro 4?",
    "documents": [
        {
            "text": "### The basics #### Power and charging It takes two to four hours to charge the Surface Pro 4 battery fully from an empty state. It can take longer if you’re using your Surface for power-intensive activities like gaming or video streaming while you’re charging it. You can use the USB port on your Surface Pro 4 power supply to charge other devices, like a phone, while your Surface charges.",
            "id": "doc1"
        }
    ],
    "Language": "en"
}
```
## <a name="sample-response"></a>示例响应

在上述请求正文中，我们基于单个文本块进行查询。 收到的针对上述查询的示例响应如下所示：

```json
{
    "answers": [
        {
            "answer": "### The basics #### Power and charging It takes two to four hours to charge the Surface Pro 4 battery fully from an empty state. It can take longer if you’re using your Surface for power-intensive activities like gaming or video streaming while you’re charging it. You can use the USB port on your Surface Pro 4 power supply to charge other devices, like a phone, while your Surface charges.",
            "answerSpan": {
                "text": "two to four hours",
                "score": 0.0,
                "startIndex": 47,
                "endIndex": 64
            },
            "score": 0.9599020481109619,
            "id": "doc1",
            "answerStartIndex": 0,
            "answerEndIndex": 390
        },
        {
            "answer": "It can take longer if you’re using your Surface for power-intensive activities like gaming or video streaming while you’re charging it. You can use the USB port on your Surface Pro 4 power supply to charge other devices, like a phone, while your Surface charges.",
            "score": 0.06749606877565384,
            "id": "doc1",
            "answerStartIndex": 129,
            "answerEndIndex": 390
        },
        {
            "answer": "You can use the USB port on your Surface Pro 4 power supply to charge other devices, like a phone, while your Surface charges.",
            "score": 0.011389964260160923,
            "id": "doc1",
            "answerStartIndex": 265,
            "answerEndIndex": 390
        }
    ]
}
```
我们发现，在 API 响应中会收到多个答案。 每个答案都有特定的置信度评分，有助于了解答案的总体相关性。 用户可以利用此置信度评分显示查询的答案。

## <a name="prebuilt-api-limits"></a>预生成 API 限制 

访问[预生成 API 限制](../limits.md#prebuilt-question-answering-limits)文档 

## <a name="prebuilt-api-reference"></a>预生成 API 参考
请访问[预生成 API 参考](/rest/api/cognitiveservices-qnamaker/qnamaker5.0preview2/prebuilt/generateanswer)文档，了解调用 API 所需的输入和输出参数。