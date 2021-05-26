---
title: 查询知识库 - QnA Maker
description: 必须发布知识库。 发布后，将使用 generateAnswer API 在运行时预测终结点上查询知识库。
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: 4d36e1feb0279eec638a3602c5188e8af1cb7c17
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2021
ms.locfileid: "110369296"
---
# <a name="query-the-knowledge-base-for-answers"></a>查询知识库以获取答案

必须发布知识库。 发布后，将使用 generateAnswer API 在运行时预测终结点上查询知识库。 查询包含问题文本和其他设置，以帮助 QnA Maker 选择答案的最佳匹配项。

## <a name="how-qna-maker-processes-a-user-query-to-select-the-best-answer"></a>QnA Maker 如何处理用户查询以选择最佳答案

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)

训练和[发布](../quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base)的 QnA Maker 知识库通过 [GenerateAnswer API](../how-to/metadata-generateanswer-usage.md) 从机器人或其他客户端应用程序接收用户查询。 下图说明了接收用户查询时的过程。

![用户查询的排名模型过程](../media/qnamaker-concepts-knowledgebase/ranker-v1.png)

### <a name="ranker-process"></a>排名程序过程

下表对这一过程进行了说明。

|步骤|目的|
|--|--|
|1|客户端应用程序将用户查询发送到 [GenerateAnswer API](../how-to/metadata-generateanswer-usage.md)。|
|2|QnA Maker 通过语言检测、拼写检查器和断字符对用户查询进行预处理。|
|3|采用此预处理可更改用户查询，以获得最佳搜索结果。|
|4|这个更改的查询会发送到 Azure 认知搜索索引，该索引会接收前 `top` 个结果。 如果这些结果中没有正确答案，请将 `top` 值略微增大。 `top` 值为 10 时通常对 90% 的查询有效。 Azure 搜索在此步骤中筛选[非索引字](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/STOPWORDS.md)。|
|5|QnA Maker 使用基于语法和语义的特征化来确定用户查询与提取的 QnA 结果之间的相似性。|
|6|机器学习的排名程序模型使用步骤 5 中的不同功能，以确定置信度分数和新的排名顺序。|
|7|新的结果将按排名顺序返回到客户端应用程序。|
|||

使用的功能包括但不限于字词级别语义、语料库中的术语级别重要性，以及深度学习的语义模型，以确定两个文本字符串之间的相似性和相关性。

# <a name="custom-question-answering-preview-release"></a>[自定义问答（预览版）](#tab/v2)

训练和[发布](../quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base)的 QnA Maker 知识库通过 [GenerateAnswer API](../how-to/metadata-generateanswer-usage.md) 从机器人或其他客户端应用程序接收用户查询。 下图说明了接收用户查询时的过程。

![用户查询的排名模型过程预览](../media/qnamaker-concepts-knowledgebase/ranker-v2.png)

### <a name="ranker-process"></a>排名程序过程

下表对这一过程进行了说明。

|步骤|目的|
|--|--|
|1|客户端应用程序将用户查询发送到 [GenerateAnswer API](../how-to/metadata-generateanswer-usage.md)。|
|2|QnA Maker 通过语言检测、拼写检查器和断字符对用户查询进行预处理。|
|3|采用此预处理可更改用户查询，以获得最佳搜索结果。|
|4|这个更改的查询会发送到 Azure 认知搜索索引，该索引会接收前 `top` 个结果。 如果这些结果中没有正确答案，请将 `top` 值略微增大。 `top` 值为 10 时通常对 90% 的查询有效。 Azure 搜索在此步骤中筛选[非索引字](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/STOPWORDS.md)。|
|5|QnA Maker 使用基于先进转换器的模型来确定用户查询与从 Azure 认知搜索中提取的候选 QnA 结果之间的相似性。 基于转换器的模型是一个深度学习多语言模型，它在水平方向上适用于所有语言，可确定置信度分数和新的排名顺序。|
|6|新的结果将按排名顺序返回到客户端应用程序。|
|||

排名顺序处理所有替代问题和答案以便为用户查询查找最匹配的 QnA 对。 用户可以灵活地将排名顺序配置为仅按问题顺序排名。 

---

## <a name="http-request-and-response-with-endpoint"></a>带有终结点的 HTTP 请求和响应
发布知识库时，服务创建基于 REST 的 HTTP 终结点，可将该终结点集成到应用程序（通常是聊天机器人）。

### <a name="the-user-query-request-to-generate-an-answer"></a>用户查询请求可生成答案

用户查询是最终用户员询问的知识库问题，如 `How do I add a collaborator to my app?`。 查询通常采用自然语言格式，或者为表示问题的几个关键字，如 `help with collaborators`。 查询将从客户端应用程序中的 HTTP 请求发送到知识库。

```json
{
    "question": "How do I add a collaborator to my app?",
    "top": 6,
    "isTest": true,
    "scoreThreshold": 20,
    "strictFilters": [
    {
        "name": "QuestionType",
        "value": "Support"
    }],
    "userId": "sd53lsY="
}
```
通过设置 [scoreThreshold](./confidence-score.md#choose-a-score-threshold)、[top](../how-to/improve-knowledge-base.md#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers) 和 [strictFilters](../how-to/query-knowledge-base-with-metadata.md) 等属性控制该响应。

使用[对话上下文](../how-to/query-knowledge-base-with-metadata.md)和[多回合功能](../how-to/multiturn-conversation.md)使会话继续进行，以细化问题和答案，从而找到最终的正确答案。

### <a name="the-response-from-a-call-to-generate-an-answer"></a>来自调用的响应可生成答案

HTTP 响应是基于给定用户查询的最佳匹配从知识库检索的答案。 响应包括答案和预测分数。 如果你要求就 `top` 属性得到多个匹配度最高的答案，你会获取多个匹配度最高的答案，每个答案均附有一个分数。

```json
{
    "answers": [
        {
            "questions": [
                "How do I add a collaborator to my app?",
                "What access control is provided for the app?",
                "How do I find user management and security?"
            ],
            "answer": "Use the Azure portal to add a collaborator using Access Control (IAM)",
            "score": 100,
            "id": 1,
            "source": "Editorial",
            "metadata": [
                {
                    "name": "QuestionType",
                    "value": "Support"
                },
                {
                    "name": "ToolDependency",
                    "value": "Azure Portal"
                }
            ]
        }
    ]
}
```


## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [置信度分数](./confidence-score.md)
