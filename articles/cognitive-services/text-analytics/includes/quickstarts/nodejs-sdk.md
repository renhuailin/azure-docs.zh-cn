---
title: 快速入门：适用于 Node.js 的文本分析 v3 客户端库 | Microsoft Docs
description: 适用于 Node.js 的 v3 版文本分析客户端库入门。
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 07/15/2021
ms.author: aahi
ms.reviewer: sumeh, assafi
ms.custom: devx-track-js
ms.openlocfilehash: e58a42809933092499bbd7132c76c0a6e10f9971
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114593468"
---
<a name="HOLTop"></a>

# <a name="version-31"></a>[版本 3.1](#tab/version-3-1)

[v3 参考文档](/javascript/api/overview/azure/ai-text-analytics-readme?preserve-view=true&view=azure-node-preview) | [v3 库源代码](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics) | [v3 包(NPM)](https://www.npmjs.com/package/@azure/ai-text-analytics/v/5.1.0) | [v3 示例](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/textanalytics/ai-text-analytics/samples)


# <a name="version-30"></a>[版本 3.0](#tab/version-3)

[v3 参考文档](/javascript/api/overview/azure/ai-text-analytics-readme) | [v3 库源代码](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics) | [v3 包(NPM)](https://www.npmjs.com/package/@azure/ai-text-analytics) | [v3 示例](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples)


---

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/cognitive-services)
* 最新版本的 [Node.js](https://nodejs.org/)。
* 你有了 Azure 订阅后，<a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="创建文本分析资源"  target="_blank">将在 Azure 门户中创建文本分析资源 </a>，以获取你的密钥和终结点。 部署后，单击“转到资源”。
    * 你需要从创建的资源获取密钥和终结点，以便将应用程序连接到文本分析 API。 你稍后会在快速入门中将密钥和终结点粘贴到下方的代码中。
    * 可以使用免费定价层 (`F0`) 试用该服务，然后再升级到付费层进行生产。
* 若要使用“分析”功能，需要标准 (S) 定价层的“文本分析”资源。

## <a name="setting-up"></a>设置

### <a name="create-a-new-nodejs-application"></a>创建新的 Node.js 应用程序

在控制台窗口（例如 cmd、PowerShell 或 Bash）中，为应用创建一个新目录并导航到该目录。 

```console
mkdir myapp 

cd myapp
```

运行 `npm init` 命令以使用 `package.json` 文件创建一个 node 应用程序。 

```console
npm init
```
### <a name="install-the-client-library"></a>安装客户端库

# <a name="version-31"></a>[版本 3.1](#tab/version-3-1)

安装 `@azure/ai-text-analytics` NPM 包：

```console
npm install --save @azure/ai-text-analytics@5.1.0
```

> [!TIP]
> 想要立即查看整个快速入门代码文件？ 可以[在 GitHub 上](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/TextAnalytics/text-analytics-v3-client-library.js)找到它，其中包含此快速入门中的代码示例。 


# <a name="version-30"></a>[版本 3.0](#tab/version-3)

安装 `@azure/ai-text-analytics` NPM 包：

```console
npm install --save @azure/ai-text-analytics@5.0.0
```

> [!TIP]
> 想要立即查看整个快速入门代码文件？ 可以[在 GitHub 上](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/TextAnalytics/text-analytics-v3-client-library.js)找到它，其中包含此快速入门中的代码示例。 

---

应用的 `package.json` 文件将使用依赖项进行更新。
创建一个名为 `index.js` 的文件，并添加以下内容：

# <a name="version-31"></a>[版本 3.1](#tab/version-3-1)

```javascript
"use strict";

const { TextAnalyticsClient, AzureKeyCredential } = require("@azure/ai-text-analytics");
```

# <a name="version-30"></a>[版本 3.0](#tab/version-3)

```javascript
"use strict";

const { TextAnalyticsClient, AzureKeyCredential } = require("@azure/ai-text-analytics");
```

---

为资源的 Azure 终结点和密钥创建变量。

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```javascript
const key = '<paste-your-text-analytics-key-here>';
const endpoint = '<paste-your-text-analytics-endpoint-here>';
```

## <a name="object-model"></a>对象模型

文本分析客户端是一个 `TextAnalyticsClient` 对象，它使用你的密钥向 Azure 进行身份验证。 该客户端提供了几种方法来分析文本，文本可以是单个字符串，也可以是批处理。

文本将以 `documents` 的列表的形式发送到 API，该项是包含 `id`、`text` 和 `language` 属性的组合的 `dictionary` 对象，具体取决于所用的方法。 `text` 属性存储要以源 `language` 分析的文本，而 `id` 则可以是任何值。 

响应对象是一个列表，其中包含每个文档的分析信息。 

## <a name="code-examples"></a>代码示例

* [客户端身份验证](#authenticate-the-client)
* [情绪分析](#sentiment-analysis) 
* [观点挖掘](#opinion-mining)
* [语言检测](#language-detection)
* [命名实体识别](#named-entity-recognition-ner)
* [实体链接](#entity-linking)
* 个人身份信息
* [关键短语提取](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>验证客户端

# <a name="version-31"></a>[版本 3.1](#tab/version-3-1)

创建一个新的 `TextAnalyticsClient` 对象并使用你的密钥和终结点作为参数。

```javascript
const textAnalyticsClient = new TextAnalyticsClient(endpoint,  new AzureKeyCredential(key));
```

# <a name="version-30"></a>[版本 3.0](#tab/version-3)

创建一个新的 `TextAnalyticsClient` 对象并使用你的密钥和终结点作为参数。

```javascript
const textAnalyticsClient = new TextAnalyticsClient(endpoint,  new AzureKeyCredential(key));
```

---

## <a name="sentiment-analysis"></a>情绪分析

# <a name="version-31"></a>[版本 3.1](#tab/version-3-1)

创建一个字符串数组，使其包含要分析的文档。 调用客户端的 `analyzeSentiment()` 方法，并获取返回的 `SentimentBatchResult` 对象。 循环访问结果列表，输出每个文档的 ID、文档级别情绪以及置信度分数。 对于每个文档，结果都包含句子级别情绪以及偏移量、长度和置信度分数。

```javascript
async function sentimentAnalysis(client){

    const sentimentInput = [
        "I had the best day of my life. I wish you were there with me."
    ];
    const sentimentResult = await client.analyzeSentiment(sentimentInput);

    sentimentResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Sentiment: ${document.sentiment}`);
        console.log(`\tDocument Scores:`);
        console.log(`\t\tPositive: ${document.confidenceScores.positive.toFixed(2)} \tNegative: ${document.confidenceScores.negative.toFixed(2)} \tNeutral: ${document.confidenceScores.neutral.toFixed(2)}`);
        console.log(`\tSentences Sentiment(${document.sentences.length}):`);
        document.sentences.forEach(sentence => {
            console.log(`\t\tSentence sentiment: ${sentence.sentiment}`)
            console.log(`\t\tSentences Scores:`);
            console.log(`\t\tPositive: ${sentence.confidenceScores.positive.toFixed(2)} \tNegative: ${sentence.confidenceScores.negative.toFixed(2)} \tNeutral: ${sentence.confidenceScores.neutral.toFixed(2)}`);
        });
    });
}
sentimentAnalysis(textAnalyticsClient)
```

在控制台窗口中使用 `node index.js` 运行代码。

### <a name="output"></a>输出

```console
ID: 0
        Document Sentiment: positive
        Document Scores:
                Positive: 1.00  Negative: 0.00  Neutral: 0.00
        Sentences Sentiment(2):
                Sentence sentiment: positive
                Sentences Scores:
                Positive: 1.00  Negative: 0.00  Neutral: 0.00
                Sentence sentiment: neutral
                Sentences Scores:
                Positive: 0.21  Negative: 0.02  Neutral: 0.77
```

# <a name="version-30"></a>[版本 3.0](#tab/version-3)

创建一个字符串数组，使其包含要分析的文档。 调用客户端的 `analyzeSentiment()` 方法，并获取返回的 `SentimentBatchResult` 对象。 循环访问结果列表，输出每个文档的 ID、文档级别情绪以及置信度分数。 对于每个文档，结果都包含句子级别情绪以及偏移量、长度和置信度分数。

```javascript
async function sentimentAnalysis(client){

    const sentimentInput = [
        "I had the best day of my life. I wish you were there with me."
    ];
    const sentimentResult = await client.analyzeSentiment(sentimentInput);

    sentimentResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Sentiment: ${document.sentiment}`);
        console.log(`\tDocument Scores:`);
        console.log(`\t\tPositive: ${document.confidenceScores.positive.toFixed(2)} \tNegative: ${document.confidenceScores.negative.toFixed(2)} \tNeutral: ${document.confidenceScores.neutral.toFixed(2)}`);
        console.log(`\tSentences Sentiment(${document.sentences.length}):`);
        document.sentences.forEach(sentence => {
            console.log(`\t\tSentence sentiment: ${sentence.sentiment}`)
            console.log(`\t\tSentences Scores:`);
            console.log(`\t\tPositive: ${sentence.confidenceScores.positive.toFixed(2)} \tNegative: ${sentence.confidenceScores.negative.toFixed(2)} \tNeutral: ${sentence.confidenceScores.neutral.toFixed(2)}`);
        });
    });
}
sentimentAnalysis(textAnalyticsClient)
```

在控制台窗口中使用 `node index.js` 运行代码。

### <a name="output"></a>输出

```console
ID: 0
        Document Sentiment: positive
        Document Scores:
                Positive: 1.00  Negative: 0.00  Neutral: 0.00
        Sentences Sentiment(2):
                Sentence sentiment: positive
                Sentences Scores:
                Positive: 1.00  Negative: 0.00  Neutral: 0.00
                Sentence sentiment: neutral
                Sentences Scores:
                Positive: 0.21  Negative: 0.02  Neutral: 0.77
```

---

## <a name="opinion-mining"></a>观点挖掘

# <a name="version-31"></a>[版本 3.1](#tab/version-3-1)

若要使用观点挖掘进行情绪分析，请创建一个包含要分析的文档的字符串数组。 调用客户端的 `analyzeSentiment()` 方法（添加了选项标志 `includeOpinionMining: true`），并获取返回的 `SentimentBatchResult` 对象。 循环访问结果列表，输出每个文档的 ID、文档级别情绪以及置信度分数。 对于每个文档，结果不仅包含如上所述的句子级情绪，而且还包含角度和观点级情绪。

```javascript
async function sentimentAnalysisWithOpinionMining(client){

  const sentimentInput = [
    {
      text: "The food and service were unacceptable, but the concierge were nice",
      id: "0",
      language: "en"
    }
  ];
  const results = await client.analyzeSentiment(sentimentInput, { includeOpinionMining: true });

  for (let i = 0; i < results.length; i++) {
    const result = results[i];
    console.log(`- Document ${result.id}`);
    if (!result.error) {
      console.log(`\tDocument text: ${sentimentInput[i].text}`);
      console.log(`\tOverall Sentiment: ${result.sentiment}`);
      console.log("\tSentiment confidence scores:", result.confidenceScores);
      console.log("\tSentences");
      for (const { sentiment, confidenceScores, opinions } of result.sentences) {
        console.log(`\t- Sentence sentiment: ${sentiment}`);
        console.log("\t  Confidence scores:", confidenceScores);
        console.log("\t  Mined opinions");
        for (const { target, assessments } of opinions) {
          console.log(`\t\t- Target text: ${target.text}`);
          console.log(`\t\t  Target sentiment: ${target.sentiment}`);
          console.log("\t\t  Target confidence scores:", target.confidenceScores);
          console.log("\t\t  Target assessments");
          for (const { text, sentiment } of assessments) {
            console.log(`\t\t\t- Text: ${text}`);
            console.log(`\t\t\t  Sentiment: ${sentiment}`);
          }
        }
      }
    } else {
      console.error(`\tError: ${result.error}`);
    }
  }
}
sentimentAnalysisWithOpinionMining(textAnalyticsClient)
```

在控制台窗口中使用 `node index.js` 运行代码。

### <a name="output"></a>输出

```console
- Document 0
        Document text: The food and service were unacceptable, but the concierge were nice
        Overall Sentiment: positive
        Sentiment confidence scores: { positive: 0.84, neutral: 0, negative: 0.16 }
        Sentences
        - Sentence sentiment: positive
          Confidence scores: { positive: 0.84, neutral: 0, negative: 0.16 }
          Mined opinions
                - Target text: food
                  Target sentiment: negative
                  Target confidence scores: { positive: 0.01, negative: 0.99 }
                  Target assessments
                        - Text: unacceptable
                          Sentiment: negative
                - Target text: service
                  Target sentiment: negative
                  Target confidence scores: { positive: 0.01, negative: 0.99 }
                  Target assessments
                        - Text: unacceptable
                          Sentiment: negative
                - Target text: concierge
                  Target sentiment: positive
                  Target confidence scores: { positive: 1, negative: 0 }
                  Target assessments
                        - Text: nice
                          Sentiment: positive
```

# <a name="version-30"></a>[版本 3.0](#tab/version-3)

此功能在版本 3.0 中不可用。

---

## <a name="language-detection"></a>语言检测

# <a name="version-31"></a>[版本 3.1](#tab/version-3-1)

创建一个字符串数组，使其包含要分析的文档。 调用客户端的 `detectLanguage()` 方法，并获取返回的 `DetectLanguageResultCollection`。 然后循环访问结果，输出每个文档的 ID 以及各自的主要语言。

```javascript
async function languageDetection(client) {

    const languageInputArray = [
        "Ce document est rédigé en Français."
    ];
    const languageResult = await client.detectLanguage(languageInputArray);

    languageResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tPrimary Language ${document.primaryLanguage.name}`)
    });
}
languageDetection(textAnalyticsClient);
```

在控制台窗口中使用 `node index.js` 运行代码。

### <a name="output"></a>输出

```console
ID: 0
        Primary Language French
```

# <a name="version-30"></a>[版本 3.0](#tab/version-3)

创建一个字符串数组，使其包含要分析的文档。 调用客户端的 `detectLanguage()` 方法，并获取返回的 `DetectLanguageResultCollection`。 然后循环访问结果，输出每个文档的 ID 以及各自的主要语言。

```javascript
async function languageDetection(client) {

    const languageInputArray = [
        "Ce document est rédigé en Français."
    ];
    const languageResult = await client.detectLanguage(languageInputArray);

    languageResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tPrimary Language ${document.primaryLanguage.name}`)
    });
}
languageDetection(textAnalyticsClient);
```

在控制台窗口中使用 `node index.js` 运行代码。

### <a name="output"></a>输出

```console
ID: 0
        Primary Language French
```

---

## <a name="named-entity-recognition-ner"></a>命名实体识别 (NER)

# <a name="version-31"></a>[版本 3.1](#tab/version-3-1)

创建一个字符串数组，使其包含要分析的文档。 调用客户端的 `recognizeEntities()` 方法，并获取 `RecognizeEntitiesResult` 对象。 循环访问结果列表，并输出实体名称、类型、子类型、偏移量、长度和分数。

```javascript
async function entityRecognition(client){

    const entityInputs = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800",
        "La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle."
    ];
    const entityResults = await client.recognizeEntities(entityInputs);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.text} \tCategory: ${entity.category} \tSubcategory: ${entity.subCategory ? entity.subCategory : "N/A"}`);
            console.log(`\tScore: ${entity.confidenceScore}`);
        });
    });
}
entityRecognition(textAnalyticsClient);
```

在控制台窗口中使用 `node index.js` 运行代码。

### <a name="output"></a>输出

```console
Document ID: 0
        Name: Microsoft         Category: Organization  Subcategory: N/A
        Score: 0.29
        Name: Bill Gates        Category: Person        Subcategory: N/A
        Score: 0.78
        Name: Paul Allen        Category: Person        Subcategory: N/A
        Score: 0.82
        Name: April 4, 1975     Category: DateTime      Subcategory: Date
        Score: 0.8
        Name: 8800      Category: Quantity      Subcategory: Number
        Score: 0.8
Document ID: 1
        Name: 21        Category: Quantity      Subcategory: Number
        Score: 0.8
        Name: Seattle   Category: Location      Subcategory: GPE
        Score: 0.25
```

## <a name="personally-identifying-information-pii-recognition"></a>个人身份信息 (PII) 识别

创建一个字符串数组，使其包含要分析的文档。 调用客户端的 `recognizePiiEntities()` 方法，并获取 `RecognizePIIEntitiesResult` 对象。 循环访问结果列表，并输出实体名称、类型和分数。

```javascript
async function piiRecognition(client) {

    const documents = [
        "The employee's phone number is (555) 555-5555."
    ];

    const results = await client.recognizePiiEntities(documents, "en");
    for (const result of results) {
        if (result.error === undefined) {
            console.log("Redacted Text: ", result.redactedText);
            console.log(" -- Recognized PII entities for input", result.id, "--");
            for (const entity of result.entities) {
                console.log(entity.text, ":", entity.category, "(Score:", entity.confidenceScore, ")");
            }
        } else {
            console.error("Encountered an error:", result.error);
        }
    }
}
piiRecognition(textAnalyticsClient)
```

在控制台窗口中使用 `node index.js` 运行代码。

### <a name="output"></a>输出

```console
Redacted Text:  The employee's phone number is **************.
 -- Recognized PII entities for input 0 --
(555) 555-5555 : Phone Number (Score: 0.8 )
```

# <a name="version-30"></a>[版本 3.0](#tab/version-3)

创建一个字符串数组，使其包含要分析的文档。 调用客户端的 `recognizeEntities()` 方法，并获取 `RecognizeEntitiesResult` 对象。 循环访问结果列表，并输出实体名称、类型、子类型、偏移量、长度和分数。

```javascript
async function entityRecognition(client){

    const entityInputs = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800",
        "La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle."
    ];
    const entityResults = await client.recognizeEntities(entityInputs);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.text} \tCategory: ${entity.category} \tSubcategory: ${entity.subCategory ? entity.subCategory : "N/A"}`);
            console.log(`\tScore: ${entity.confidenceScore}`);
        });
    });
}
entityRecognition(textAnalyticsClient);
```

在控制台窗口中使用 `node index.js` 运行代码。

### <a name="output"></a>输出

```console
Document ID: 0
        Name: Microsoft         Category: Organization  Subcategory: N/A
        Score: 0.29
        Name: Bill Gates        Category: Person        Subcategory: N/A
        Score: 0.78
        Name: Paul Allen        Category: Person        Subcategory: N/A
        Score: 0.82
        Name: April 4, 1975     Category: DateTime      Subcategory: Date
        Score: 0.8
        Name: 8800      Category: Quantity      Subcategory: Number
        Score: 0.8
Document ID: 1
        Name: 21        Category: Quantity      Subcategory: Number
        Score: 0.8
        Name: Seattle   Category: Location      Subcategory: GPE
        Score: 0.25
```


---


## <a name="entity-linking"></a>实体链接

# <a name="version-31"></a>[版本 3.1](#tab/version-3-1)

创建一个字符串数组，使其包含要分析的文档。 调用客户端的 `recognizeLinkedEntities()` 方法，并获取 `RecognizeLinkedEntitiesResult` 对象。 循环访问结果列表，并输出实体名称、ID、数据源、URL 和匹配项。 `matches` 数组中的每个对象都将包含该匹配项的偏移量、长度和分数。

```javascript
async function linkedEntityRecognition(client){

    const linkedEntityInput = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800. During his career at Microsoft, Gates held the positions of chairman, chief executive officer, president and chief software architect, while also being the largest individual shareholder until May 2014."
    ];
    const entityResults = await client.recognizeLinkedEntities(linkedEntityInput);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.name} \tID: ${entity.dataSourceEntityId} \tURL: ${entity.url} \tData Source: ${entity.dataSource}`);
            console.log(`\tMatches:`)
            entity.matches.forEach(match => {
                console.log(`\t\tText: ${match.text} \tScore: ${match.confidenceScore.toFixed(2)}`);
        })
        });
    });
}
linkedEntityRecognition(textAnalyticsClient);
```

在控制台窗口中使用 `node index.js` 运行代码。

### <a name="output"></a>输出

```console
Document ID: 0
        Name: Altair 8800       ID: Altair 8800         URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800       Score: 0.88
        Name: Bill Gates        ID: Bill Gates  URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates        Score: 0.63
                Text: Gates     Score: 0.63
        Name: Paul Allen        ID: Paul Allen  URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen        Score: 0.60
        Name: Microsoft         ID: Microsoft   URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft         Score: 0.55
                Text: Microsoft         Score: 0.55
        Name: April 4   ID: April 4     URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4   Score: 0.32
        Name: BASIC     ID: BASIC       URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC     Score: 0.33
```

# <a name="version-30"></a>[版本 3.0](#tab/version-3)

创建一个字符串数组，使其包含要分析的文档。 调用客户端的 `recognizeLinkedEntities()` 方法，并获取 `RecognizeLinkedEntitiesResult` 对象。 循环访问结果列表，并输出实体名称、ID、数据源、URL 和匹配项。 `matches` 数组中的每个对象都将包含该匹配项的偏移量、长度和分数。

```javascript
async function linkedEntityRecognition(client){

    const linkedEntityInput = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800. During his career at Microsoft, Gates held the positions of chairman, chief executive officer, president and chief software architect, while also being the largest individual shareholder until May 2014."
    ];
    const entityResults = await client.recognizeLinkedEntities(linkedEntityInput);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.name} \tID: ${entity.dataSourceEntityId} \tURL: ${entity.url} \tData Source: ${entity.dataSource}`);
            console.log(`\tMatches:`)
            entity.matches.forEach(match => {
                console.log(`\t\tText: ${match.text} \tScore: ${match.confidenceScore.toFixed(2)}`);
        })
        });
    });
}
linkedEntityRecognition(textAnalyticsClient);
```

在控制台窗口中使用 `node index.js` 运行代码。

### <a name="output"></a>输出

```console
Document ID: 0
        Name: Altair 8800       ID: Altair 8800         URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800       Score: 0.88
        Name: Bill Gates        ID: Bill Gates  URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates        Score: 0.63
                Text: Gates     Score: 0.63
        Name: Paul Allen        ID: Paul Allen  URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen        Score: 0.60
        Name: Microsoft         ID: Microsoft   URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft         Score: 0.55
                Text: Microsoft         Score: 0.55
        Name: April 4   ID: April 4     URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4   Score: 0.32
        Name: BASIC     ID: BASIC       URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC     Score: 0.33
```

---

## <a name="key-phrase-extraction"></a>关键短语提取

# <a name="version-31"></a>[版本 3.1](#tab/version-3-1)

创建一个字符串数组，使其包含要分析的文档。 调用客户端的 `extractKeyPhrases()` 方法，并获取返回的 `ExtractKeyPhrasesResult` 对象。 循环访问结果，输出每个文档的 ID 以及任何检测到的密钥短语。

```javascript
async function keyPhraseExtraction(client){

    const keyPhrasesInput = [
        "My cat might need to see a veterinarian.",
    ];
    const keyPhraseResult = await client.extractKeyPhrases(keyPhrasesInput);
    
    keyPhraseResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Key Phrases: ${document.keyPhrases}`);
    });
}
keyPhraseExtraction(textAnalyticsClient);
```

在控制台窗口中使用 `node index.js` 运行代码。

### <a name="output"></a>输出

```console
ID: 0
        Document Key Phrases: cat,veterinarian
```

# <a name="version-30"></a>[版本 3.0](#tab/version-3)

创建一个字符串数组，使其包含要分析的文档。 调用客户端的 `extractKeyPhrases()` 方法，并获取返回的 `ExtractKeyPhrasesResult` 对象。 循环访问结果，输出每个文档的 ID 以及任何检测到的密钥短语。

```javascript
async function keyPhraseExtraction(client){

    const keyPhrasesInput = [
        "My cat might need to see a veterinarian.",
    ];
    const keyPhraseResult = await client.extractKeyPhrases(keyPhrasesInput);
    
    keyPhraseResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Key Phrases: ${document.keyPhrases}`);
    });
}
keyPhraseExtraction(textAnalyticsClient);
```

在控制台窗口中使用 `node index.js` 运行代码。

### <a name="output"></a>输出

```console
ID: 0
        Document Key Phrases: cat,veterinarian
```


---

## <a name="extract-health-entities"></a>提取健康状况实体

[!INCLUDE [health operation pricing](../health-operation-pricing-caution.md)]

可以使用文本分析来执行异步请求，以从文本中提取健康状况实体。 下面的示例演示了基本示例。 可以[在 GitHub 上](https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/textanalytics/ai-text-analytics/samples/v5/javascript/beginAnalyzeHealthcareEntities.js)找到更高级的示例。

# <a name="version-31"></a>[版本 3.1](#tab/version-3-1)

```javascript
async function healthExample(client) {
    console.log("== Recognize Healthcare Entities Sample ==");

    const documents = [
        "Prescribed 100mg ibuprofen, taken twice daily."
      ];
    const poller = await client.beginAnalyzeHealthcareEntities(documents, "en", {
      includeStatistics: true
    });
  
    poller.onProgress(() => {
      console.log(
        `Last time the operation was updated was on: ${poller.getOperationState().lastModifiedOn}`
      );
    });
    console.log(
      `The analyze healthcare entities operation was created on ${
        poller.getOperationState().createdOn
      }`
    );
    console.log(
      `The analyze healthcare entities operation results will expire on ${
        poller.getOperationState().expiresOn
      }`
    );
  
    const results = await poller.pollUntilDone();
  
    for await (const result of results) {
      console.log(`- Document ${result.id}`);
      if (!result.error) {
        console.log("\tRecognized Entities:");
        for (const entity of result.entities) {
          console.log(`\t- Entity "${entity.text}" of type ${entity.category}`);
        }
        if (result.entityRelations && (result.entityRelations.length > 0)) {
          console.log(`\tRecognized relations between entities:`);
          for (const relation of result.entityRelations) {
            console.log(
              `\t\t- Relation of type ${relation.relationType} found between the following entities:`
            );
            for (const role of relation.roles) {
              console.log(`\t\t\t- "${role.entity.text}" with the role ${role.name}`);
            }
          }
        }
      } else console.error("\tError:", result.error);
    }
  }
  
  healthExample(textAnalyticsClient).catch((err) => {
    console.error("The sample encountered an error:", err);
  });
```

### <a name="output"></a>输出

```console
- Document 0
    Recognized Entities:
    - Entity "100mg" of type Dosage
    - Entity "ibuprofen" of type MedicationName
    - Entity "twice daily" of type Frequency
    Recognized relations between entities:
        - Relation of type DosageOfMedication found between the following entities:   
                - "100mg" with the role Dosage
                - "ibuprofen" with the role Medication
        - Relation of type FrequencyOfMedication found between the following entities:
                - "ibuprofen" with the role Medication
                - "twice daily" with the role Frequency
```

# <a name="version-30"></a>[版本 3.0](#tab/version-3)

此功能在版本 3.0 中不可用。

---


## <a name="use-the-api-asynchronously-with-the-analyze-operation"></a>使用“分析”操作异步使用 API

# <a name="version-31"></a>[版本 3.1](#tab/version-3-1)

可以使用“分析”操作执行异步批处理请求实现以下目的：NER、关键短语提取、情绪分析和 PII 检测。 下面的示例演示了有关一个操作的基本示例。 可在 GitHub 上找到更多适用于 [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/textanalytics/ai-text-analytics/samples/v5/javascript/beginAnalyzeActions.js) 和 [TypeScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/textanalytics/ai-text-analytics/samples/v5/typescript/src/beginAnalyzeActions.ts) 的高级示例。

[!INCLUDE [Analyze Batch Action pricing](../analyze-operation-pricing-caution.md)]

创建名为 `analyze_example()` 的新函数，它将调用 `beginAnalyze()` 函数。 结果将是一个长期操作，将轮询该操作以获得结果。

```javascript
async function analyze_example(client) {
    const documents = [
        "Microsoft was founded by Bill Gates and Paul Allen.",
    ];

    const actions = {
        recognizeEntitiesActions: [{ modelVersion: "latest" }],
        extractKeyPhrasesActions: [{ modelVersion: "latest" }]
    };
    const poller = await client.beginAnalyzeActions(documents, actions, "en");

    console.log(
        `The analyze batch actions operation was created on ${poller.getOperationState().createdOn}`
    );
    console.log(
        `The analyze batch actions operation results will expire on ${poller.getOperationState().expiresOn
        }`
    );
    const resultPages = await poller.pollUntilDone();
    for await (const page of resultPages) {
        const entitiesAction = page.recognizeEntitiesResults[0];
        if (!entitiesAction.error) {
            for (const doc of entitiesAction.results) {
                console.log(`- Document ${doc.id}`);
                if (!doc.error) {
                    console.log("\tEntities:");
                    for (const entity of doc.entities) {
                        console.log(`\t- Entity ${entity.text} of type ${entity.category}`);
                    }
                } else {
                    console.error("\tError:", doc.error);
                }
            }
        }
    }
    for await (const page of resultPages) {
        const keyPhrasesAction = page.extractKeyPhrasesResults[0];
        if (!keyPhrasesAction.error) {
            for (const doc of keyPhrasesAction.results) {
                console.log(`- Document ${doc.id}`);
                if (!doc.error) {
                    console.log("\tKey phrases:");
                    for (const phrase of doc.keyPhrases) {
                        console.log(`\t- ${phrase}`);
                    }
                } else {
                    console.error("\tError:", doc.error);
                }
            }
        }
    }
}
analyze_example(textAnalyticsClient)
```

### <a name="output"></a>Output

```console
The analyze batch actions operation was created on Fri Jun 18 2021 12:34:52 GMT-0700 (Pacific Daylight Time)
The analyze batch actions operation results will expire on Sat Jun 19 2021 12:34:52 GMT-0700 (Pacific Daylight Time)
- Document 0
        Entities:
        - Entity Microsoft of type Organization
        - Entity Bill Gates of type Person
        - Entity Paul Allen of type Person
- Document 0
        Key phrases:
        - Bill Gates
        - Paul Allen
        - Microsoft
```

还可以使用“分析”操作来执行 NER、关键短语提取、情绪分析和检测 PII。 请参阅 GitHub 上的 [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/textanalytics/ai-text-analytics/samples/v5/javascript/beginAnalyzeActions.js) 和 [TypeScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples/v5/typescript/src) 分析示例。

# <a name="version-30"></a>[版本 3.0](#tab/version-3)

此功能在版本 3.0 中不可用。

---

在快速入门文件中使用 `node` 命令运行应用程序。

```console
node index.js
```
