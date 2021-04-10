---
title: 使用元数据进行上下文筛选
titleSuffix: Azure Cognitive Services
description: QnA Maker 按元数据筛选 QnA 对。
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 8f65ca9386963824f0cb740f587de83c9dec7f78
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "103017419"
---
# <a name="filter-responses-with-metadata"></a>使用元数据筛选响应

通过 QnA Maker，可将键值对形式的元数据添加到问答对。 然后可以使用此信息来筛选用户查询的结果，以及存储可用于后续对话的其他信息。

<a name="qna-entity"></a>

## <a name="store-questions-and-answers-with-a-qna-entity"></a>用 QnA 实体存储问答

务必要了解 QnA Maker 存储问答数据的方式。 下图显示了一个 QnA 实体：

![QnA 实体图示](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

每个 QnA 实体都有一个唯一的永久 ID。 可以使用该 ID 对特定 QnA 实体进行更新。

## <a name="use-metadata-to-filter-answers-by-custom-metadata-tags"></a>使用元数据按自定义元数据标记筛选答案

通过添加元数据，可以按这些元数据标记筛选答案。 从“视图选项”菜单添加“元数据”列。 通过选择元数据 + 图标来添加元数据对，将元数据添加到知识库。 此对包含一个键和一个值。

![添加元数据的屏幕截图](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

<a name="filter-results-with-strictfilters-for-metadata-tags"></a>

## <a name="filter-results-with-strictfilters-for-metadata-tags"></a>使用元数据标记的 strictFilters 来筛选结果

请考虑用户问题“这家酒店什么时候关门？”，其中的意向暗指餐馆“Paradise”。

由于只需要关于餐馆“Paradise”的结果，因此可以在 GenerateAnswer 调用中对元数据“餐馆名称”设置一个筛选器。 下面的示例对此进行了展示：

```json
{
    "question": "When does this hotel close?",
    "top": 1,
    "strictFilters": [ { "name": "restaurant", "value": "paradise"}]
}
```

### <a name="logical-and-by-default"></a>逻辑 AND（默认）

若要在查询中组合多个元数据筛选器，请将其他元数据筛选器添加到 `strictFilters` 属性的数组。 默认情况下，这些值按逻辑组合 (AND)。 逻辑组合要求所有筛选器都与 QnA 对匹配，以便在答案中返回该对。

这等效于将 `strictFiltersCompoundOperationType` 属性与 `AND` 值一起使用。

### <a name="logical-or-using-strictfilterscompoundoperationtype-property"></a>逻辑 OR 使用 strictFiltersCompoundOperationType 属性

合并多个元数据筛选器时，如果只关心一个或几个筛选器匹配，则使用 `strictFiltersCompoundOperationType` 属性和 `OR` 值。

这允许你的知识库在任何筛选器匹配但不返回没有元数据的答案时返回答案。

```json
{
    "question": "When do facilities in this hotel close?",
    "top": 1,
    "strictFilters": [
      { "name": "type","value": "restaurant"},
      { "name": "type", "value": "bar"},
      { "name": "type", "value": "poolbar"}
    ],
    "strictFiltersCompoundOperationType": "OR"
}
```

### <a name="metadata-examples-in-quickstarts"></a>快速入门中的元数据示例

有关元数据的详细信息，请参阅 QnA Maker 门户中的元数据快速入门：
* [创作 - 将元数据添加到 QnA 对](../quickstarts/add-question-metadata-portal.md#add-metadata-to-filter-the-answers)
* [查询预测 - 按元数据筛选应答](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md)

<a name="keep-context"></a>

## <a name="use-question-and-answer-results-to-keep-conversation-context"></a>使用问答结果来保留会话上下文

对 GenerateAnswer 的响应包含匹配的问答对相应的元数据信息。 可以在客户端应用程序中使用此信息来存储以前会话的上下文，以便在以后的对话中使用。

```json
{
    "answers": [
        {
            "questions": [
                "What is the closing time?"
            ],
            "answer": "10.30 PM",
            "score": 100,
            "id": 1,
            "source": "Editorial",
            "metadata": [
                {
                    "name": "restaurant",
                    "value": "paradise"
                },
                {
                    "name": "location",
                    "value": "secunderabad"
                }
            ]
        }
    ]
}
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [分析知识库](../How-to/get-analytics-knowledge-base.md)
