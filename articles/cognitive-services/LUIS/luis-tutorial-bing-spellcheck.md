---
title: 更正拼写错误的单词 - LUIS
titleSuffix: Azure Cognitive Services
description: 通过将必应拼写检查 API V7 添加到 LUIS 终结点查询来更正表述中拼写错误的字词。
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 01/12/2021
ms.openlocfilehash: 904a29278ea3efdabe6ed72892d2e14ccbf2e0e3
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124740753"
---
# <a name="correct-misspelled-words-with-bing-resource"></a>使用必应资源更正拼写错误的单词

V3 预测 API 现支持[必应拼写检查 API](/bing/search-apis/bing-spell-check/overview)。 在请求的标头中将密钥添加到必应搜索资源，来将拼写检查添加到应用程序中。 可以使用现有必应资源（如果已经有一个自己的必应资源），或者[创建一个新的](https://portal.azure.com/#create/Microsoft.BingSearch)必应资源来使用此功能。 

有关拼写错误的查询的预测输出示例：

```json
{
  "query": "bouk me a fliht to kayro",
  "prediction": {
    "alteredQuery": "book me a flight to cairo",
    "topIntent": "book a flight",
    "intents": {
      "book a flight": {
        "score": 0.9480589
      }
      "None": {
        "score": 0.0332136229
      }
    },
    "entities": {}
  }
}
```

在 LUIS 用户话语预测之前纠正拼写错误。 可以在响应中查看对原始话语所做的任何更改，包括拼写方面的更改。

## <a name="create-bing-search-resource"></a>创建必应搜索资源

若要在 Azure 门户中创建必应搜索资源，请按照以下说明：

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 选择左上角的“创建资源”。

3. 在搜索框中，输入 `Bing Search V7` 并选择服务。

4. 右侧随即出现一个包含“法律声明”等信息的信息面板。 选择“创建”开始创建订阅。

> [!div class="mx-imgBorder"]
> ![必应拼写检查 API V7 资源](./media/luis-tutorial-bing-spellcheck/bing-search-resource-portal.png)

5. 在下一个面板中，输入服务设置。 等待服务创建过程完成。

6. 创建资源后，转到左侧的“密钥和终结点”边栏选项卡。 

7. 将其中一个要添加的密钥复制到预测请求的标头中。 只需要两个密钥中的一个即可。

<!--
## Using the key in LUIS test panel
There are two places in LUIS to use the key. The first is in the [test panel](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel). The key isn't saved into LUIS but instead is a session variable. You need to set the key every time you want the test panel to apply the Bing Spell Check API v7 service to the utterance. See [instructions](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel) in the test panel for setting the key.
-->


## <a name="adding-the-key-to-the-endpoint-url"></a>将密钥添加到终结点 URL
对于每个要应用拼写更正的查询，终结点查询需要具有在查询标头参数中传递的必应拼写资源密钥。 可使用调用 LUIS 的聊天机器人或直接调用 LUIS 终结点 API。 无论如何调用终结点，每个调用都必须包含标头请求中拼写更正所需的信息，以确保正常工作。 必须使用 mkt-bing-spell-check-key 将值设置为密钥值。

|标头密钥|标头值|
|--|--|
|`mkt-bing-spell-check-key`|资源的“密钥和终结点”标头选项卡中找到的密钥|

## <a name="send-misspelled-utterance-to-luis"></a>将拼写错误的表述发送到 LUIS
1. 在你将发送（例如，“这座山有多远？”）的预测查询中添加拼写错误的言语。 在英语中，含有一个 `n` 的 `mountain` 才是正确的拼写。

2. LUIS 对 `How far is the mountain?` 的响应是一个 JSON 结果。 如果必应拼写检查 API v7 检测到拼写错误，LUIS 应用的 JSON 响应中的 `query` 字段包含原始查询，而 `alteredQuery` 字段包含更正后发送到 LUIS 的查询。

```json
{
  "query": "How far is the mountainn?",
  "alteredQuery": "How far is the mountain?",
  "topScoringIntent": {
    "intent": "Concierge",
    "score": 0.183866
  },
  "entities": []
}
```

## <a name="ignore-spelling-mistakes"></a>忽略拼写错误

如果不想使用必应搜索 API v7 服务，需要添加正确和错误的拼写。

两个解决方案如下：

* 标签示例言语拥有所有不同拼写，这样 LUIS 可以学习正确的拼写和错误的拼写。 使用此选项比使用拼写检查器需要更多标记操作。
* 创建一个包含词语的所有差异的短语列表。 通过该解决办法，不需要在示例言语中标记词语差异。


> [!div class="nextstepaction"]
> [详细了解示例陈述](./luis-how-to-add-entities.md)
