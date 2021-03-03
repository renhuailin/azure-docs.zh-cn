---
title: 更正拼写错误的单词-LUIS
titleSuffix: Azure Cognitive Services
description: 通过将必应拼写检查 API V7 添加到 LUIS 终结点查询来更正表述中拼写错误的字词。
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 01/12/2021
ms.openlocfilehash: 509d1dc0b94bdfa9be5185df0bad793f7702eb26
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101731028"
---
# <a name="correct-misspelled-words-with-bing-resource"></a>更正包含必应资源的拼写错误的单词

V3 预测 API 现支持[必应拼写检查 API](/bing/search-apis/bing-spell-check/overview)。 通过在请求的标头中包含必应搜索资源的键，向应用程序添加拼写检查。 如果已拥有必应资源，可以使用现有 Bing 资源，或 [创建新](https://portal.azure.com/#create/Microsoft.BingSearch) 资源来使用此功能。 

拼写错误的查询的预测输出示例：

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

若要在 Azure 门户中创建必应搜索资源，请按照以下说明操作：

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 选择左上角的“创建资源”。

3. 在搜索框中，输入 `Bing Search V7` 并选择服务。

4. 右侧随即出现一个包含“法律声明”等信息的信息面板。 选择“创建”开始创建订阅。

> [!div class="mx-imgBorder"]
> ![必应拼写检查 API V7 资源](./media/luis-tutorial-bing-spellcheck/bing-search-resource-portal.png)

5. 在下一个面板中，输入服务设置。 等待服务创建过程完成。

6. 创建资源后，请切换到左侧的 " **密钥" 和 "终结点** " 边栏选项卡。 

7. 复制一个要添加到预测请求标头的键。 只需要两个密钥中的一个。

<!--
## Using the key in LUIS test panel
There are two places in LUIS to use the key. The first is in the [test panel](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel). The key isn't saved into LUIS but instead is a session variable. You need to set the key every time you want the test panel to apply the Bing Spell Check API v7 service to the utterance. See [instructions](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel) in the test panel for setting the key.
-->
## <a name="enable-spell-check-from-ui"></a>通过 UI 启用拼写检查 
可以使用 [Luis 门户](https://www.luis.ai)为示例查询启用拼写检查。 在屏幕顶部选择 " **管理** "，并在左侧导航栏中选择 " **Azure 资源** "。 将预测资源关联到应用程序后，可以从页面底部选择 " **更改查询参数** "，并将资源键粘贴到 " **启用拼写检查** " 字段中。
    
   > [!div class="mx-imgBorder"]
   > ![启用拼写检查](./media/luis-tutorial-bing-spellcheck/spellcheck-query-params.png)


## <a name="adding-the-key-to-the-endpoint-url"></a>将密钥添加到终结点 URL
对于要对其应用拼写更正的每个查询，终结点查询需要在查询标头参数中传递必应拼写检查资源键。 可使用调用 LUIS 的聊天机器人或直接调用 LUIS 终结点 API。 无论调用终结点的方式如何，每个调用都必须在标头的请求中包含所需的信息，以便正确更正拼写更正。 必须将值与有关（ **必应）** 设置为键值。

|标头键|标头值|
|--|--|
|`mkt-bing-spell-check-key`|在资源的 " **密钥" 和 "终结点** " 边栏选项卡中找到密钥|

## <a name="send-misspelled-utterance-to-luis"></a>将拼写错误的表述发送到 LUIS
1. 在将发送的预测查询中添加一个拼写错误的查询文本，例如 "mountainn 的距离是多少？"。 在英语中，含有一个 `n` 的 `mountain` 才是正确的拼写。

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

如果不想使用必应搜索 API v7 服务，则需要添加正确且不正确的拼写。

两种解决方案是：

* 标签示例最谈话，其中包含所有不同的拼写，以便 LUIS 可以了解正确的拼写和打字错误。 使用此选项比使用拼写检查器需要更多标记操作。
* 创建一个短语列表，其中包含该词的所有变体。 利用此解决方案，无需在示例最谈话中标记词变体。


> [!div class="nextstepaction"]
> [详细了解示例陈述](./luis-how-to-add-entities.md)