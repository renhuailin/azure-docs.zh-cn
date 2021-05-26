---
title: 将元数据与 GenerateAnswer API 配合使用 - QnA Maker
titleSuffix: Azure Cognitive Services
description: 通过 QnA Maker，可将键值对形式的元数据添加到问/答对。 可以筛选用户查询的结果，以及存储可用于后续对话的其他信息。
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 0daa44f0d901dc14541785f764d5ccf8f96fc737
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2021
ms.locfileid: "110376667"
---
# <a name="get-an-answer-with-the-generateanswer-api"></a>使用 GenerateAnswer API 获取答案

若要获取用户问题的预测答案，请使用 GenerateAnswer API。 发布知识库时，可以在“发布”页上查看有关如何使用此 API 的信息。 还可以将 API 配置为基于元数据标记筛选答案，并使用测试查询字符串参数测试终结点中的知识库。

<a name="generateanswer-api"></a>

## <a name="get-answer-predictions-with-the-generateanswer-api"></a>使用 GenerateAnswer API 获取答案预测

使用机器人或应用程序中的 [GenerateAnswer API](/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer)，能以某个用户问题对知识库进行查询，从问答对中获取匹配度最高的结果。

<a name="generateanswer-endpoint"></a>

## <a name="publish-to-get-generateanswer-endpoint"></a>发布以获取 GenerateAnswer 终结点

发布知识库后，从 [QnA Maker 门户](https://www.qnamaker.ai)或使用 [API](/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) 都可以获取 GenerateAnswer 终结点的详细信息。

获取终结点详细信息：
1. 登录到 [https://www.qnamaker.ai](https://www.qnamaker.ai)。
1. 在“我的知识库”中，选择知识库的“查看代码”。
    ![我的知识库的屏幕截图](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png)
1. 获取 GenerateAnswer 终结点的详细信息。

    # <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)

    ![终结点详细信息的屏幕截图](../media/qnamaker-how-to-metadata-usage/view-code.png)

    # <a name="custom-question-answering-preview-release"></a>[自定义问答（预览版）](#tab/v2)

    ![终结点详细信息托管的屏幕截图](../media/qnamaker-how-to-metadata-usage/view-code-managed.png)

    ---

还可以在知识库的“设置”选项卡中获取终结点详细信息。

<a name="generateanswer-request"></a>

## <a name="generateanswer-request-configuration"></a>GenerateAnswer 请求配置

通过 HTTP POST 请求调用 GenerateAnswer。 有关演示如何调用 GenerateAnswer 的示例代码，请参阅[快速入门](../quickstarts/quickstart-sdk.md#generate-an-answer-from-the-knowledge-base)。

POST 请求使用：

* 必需的 [URI 参数](/rest/api/cognitiveservices/qnamakerruntime/runtime/train#uri-parameters)
* 必需的标头属性 `Authorization`（出于安全性）
* 必需的[正文属性](/rest/api/cognitiveservices/qnamakerruntime/runtime/train#feedbackrecorddto)。

GenerateAnswer URL 采用以下格式：

```
https://{QnA-Maker-endpoint}/knowledgebases/{knowledge-base-ID}/generateAnswer
```

请记住，使用字符串 `EndpointKey` 的值和尾随空格，以及在“设置”页上找到的终结点密钥设置 `Authorization` 的 HTTP 标头属性。

示例 JSON 正文如下所示：

```json
{
    "question": "qna maker and luis",
    "top": 6,
    "isTest": true,
    "scoreThreshold": 30,
    "rankerType": "" // values: QuestionOnly
    "strictFilters": [
    {
        "name": "category",
        "value": "api"
    }],
    "userId": "sd53lsY="
}
```

了解有关 [rankerType](../concepts/best-practices.md#choosing-ranker-type) 的详细信息。

以前的 JSON 仅请求阈值分数不低于 30% 的答案。

<a name="generateanswer-response"></a>

## <a name="generateanswer-response-properties"></a>GenerateAnswer 响应属性

[响应](/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#successful-query)是一个 JSON 对象，其中包括显示答案所需的所有信息，以及对话中的下一轮次（如果可用）。

```json
{
    "answers": [
        {
            "score": 38.54820341616869,
            "Id": 20,
            "answer": "There is no direct integration of LUIS with QnA Maker. But, in your bot code, you can use LUIS and QnA Maker together. [View a sample bot](https://github.com/Microsoft/BotBuilder-CognitiveServices/tree/master/Node/samples/QnAMaker/QnAWithLUIS)",
            "source": "Custom Editorial",
            "questions": [
                "How can I integrate LUIS with QnA Maker?"
            ],
            "metadata": [
                {
                    "name": "category",
                    "value": "api"
                }
            ]
        }
    ]
}
```

之前的 JSON 响应以评分为 38.5% 的答案做出响应。

## <a name="match-questions-only-by-text"></a>仅按文本匹配问题

默认情况下，QnA Maker 搜索问题和答案。 如果只想要搜索问题，则在 GenerateAnswer 请求的 POST 正文中使用 `RankerType=QuestionOnly` 来获取答案。

可以使用 `isTest=false` 搜索已发布的知识库，或使用 `isTest=true` 搜索测试知识库。

```json
{
  "question": "Hi",
  "top": 30,
  "isTest": true,
  "RankerType":"QuestionOnly"
}

```
## <a name="use-qna-maker-with-a-bot-in-c"></a>在 C# 中将 QnA Maker 与机器人配合使用

机器人框架使用 [getAnswer API](/dotnet/api/microsoft.bot.builder.ai.qna.qnamaker.getanswersasync#Microsoft_Bot_Builder_AI_QnA_QnAMaker_GetAnswersAsync_Microsoft_Bot_Builder_ITurnContext_Microsoft_Bot_Builder_AI_QnA_QnAMakerOptions_System_Collections_Generic_Dictionary_System_String_System_String__System_Collections_Generic_Dictionary_System_String_System_Double__) 提供对 QnA Maker 的属性的访问权限：

```csharp
using Microsoft.Bot.Builder.AI.QnA;
var metadata = new Microsoft.Bot.Builder.AI.QnA.Metadata();
var qnaOptions = new QnAMakerOptions();

metadata.Name = Constants.MetadataName.Intent;
metadata.Value = topIntent;
qnaOptions.StrictFilters = new Microsoft.Bot.Builder.AI.QnA.Metadata[] { metadata };
qnaOptions.Top = Constants.DefaultTop;
qnaOptions.ScoreThreshold = 0.3F;
var response = await _services.QnAServices[QnAMakerKey].GetAnswersAsync(turnContext, qnaOptions);
```

以前的 JSON 仅请求阈值分数不低于 30% 的答案。

## <a name="use-qna-maker-with-a-bot-in-nodejs"></a>在 Node.js 中将 QnA Maker 与机器人配合使用

机器人框架使用 [getAnswer API](/javascript/api/botbuilder-ai/qnamaker?preserve-view=true&view=botbuilder-ts-latest#generateanswer-string---undefined--number--number-) 提供对 QnA Maker 的属性的访问权限：

```javascript
const { QnAMaker } = require('botbuilder-ai');
this.qnaMaker = new QnAMaker(endpoint);

// Default QnAMakerOptions
var qnaMakerOptions = {
    ScoreThreshold: 0.30,
    Top: 3
};
var qnaResults = await this.qnaMaker.getAnswers(stepContext.context, qnaMakerOptions);
```

以前的 JSON 仅请求阈值分数不低于 30% 的答案。

## <a name="get-precise-answers-with-generateanswer-api"></a>使用 GenerateAnswer API 获取精确答案

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)

我们仅提供 QnA Maker 托管版本中的精确答案功能。

# <a name="custom-question-answering-preview-release"></a>[自定义问答（预览版）](#tab/v2)

用户可以在使用带自定义问答功能的文本分析资源时启用[精确答案](../reference-precise-answering.md)。 answerSpanRequest 参数必须进行相同的更新。

```json
{
    "question": "How long it takes to charge surface pro 4?",
    "top": 3,
    "answerSpanRequest": {
        "enable": true,
        "topAnswersWithSpan": 1
    }
}
```

同样，用户可以通过不设置 answerSpanRequest 参数来选择禁用精确答案。

```json
{
    "question": "How long it takes to charge surface pro 4?",
    "top": 3
}
```
### <a name="bot-settings"></a>机器人设置

如果要为机器人服务配置精确答案设置，请导航到机器人的应用服务资源。 然后，必须通过添加以下设置来更新配置。

- EnablePreciseAnswer
- DisplayPreciseAnswerOnly

|显示器配置|EnablePreciseAnswer|DisplayPreciseAnswerOnly|
|:--|--|--|
|仅限精确答案|true|true|
|仅限长答案|false|false|
|长答案和精确答案|true|false|

---

## <a name="common-http-errors"></a>常见 HTTP 错误

|代码|说明|
|:--|--|
|2xx|成功|
|400|请求的参数不正确，这意味着所需的参数缺失、格式错误或太大|
|400|请求的正文不正确，这意味着 JSON 缺失、格式错误或太大|
|401|密钥无效|
|403|禁止 - 没有正确的权限|
|404|知识库不存在|
|410|此 API 已弃用，不再可用|

## <a name="next-steps"></a>后续步骤

“发布”页还提供了使用 Postman 或 cURL [生成答案](../Quickstarts/get-answer-from-knowledge-base-using-url-tool.md)的信息。

> [!div class="nextstepaction"]
> [获取有关知识库的分析](../how-to/get-analytics-knowledge-base.md)
> [!div class="nextstepaction"]
> [置信度分数](../Concepts/confidence-score.md)
