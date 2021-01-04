---
title: 快速入门：适用于 Java 的文本分析 v3 客户端库 | Microsoft Docs
description: 适用于 Java 的 v3 版文本分析客户端库入门。
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 12/11/2020
ms.custom: devx-track-java
ms.author: aahi
ms.reviewer: tasharm, assafi, sumeh
ms.openlocfilehash: e68a46070f03154092d148552fffbb99ae82bcfd
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/16/2020
ms.locfileid: "97612697"
---
<a name="HOLTop"></a>

# <a name="version-31-preview"></a>[版本 3.1 预览](#tab/version-3-1)

[参考文档](/java/api/overview/azure/ai-textanalytics-readme?view=azure-java-stable) | [库源代码](https://github.com/Azure/azure-sdk-for-java/blob/azure-ai-textanalytics_5.1.0-beta.3/sdk/textanalytics/azure-ai-textanalytics) | [包](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/5.1.0-beta.3) | [示例](https://github.com/Azure/azure-sdk-for-java/tree/azure-ai-textanalytics_5.1.0-beta.3/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics)

# <a name="version-30"></a>[版本 3.0](#tab/version-3)

[参考文档](/java/api/overview/azure/ai-textanalytics-readme?view=azure-java-stable) | [库源代码](https://github.com/Azure/azure-sdk-for-java/blob/azure-ai-textanalytics_5.0.0/sdk/textanalytics/azure-ai-textanalytics) | [包](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/5.0.0) | [示例](https://github.com/Azure/azure-sdk-for-java/tree/azure-ai-textanalytics_5.0.0/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics)

# <a name="version-21"></a>[版本 2.1](#tab/version-2)

本文仅介绍此 API 的版本 3.x。

---

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/cognitive-services)
* [Java 开发工具包](https://www.oracle.com/technetwork/java/javase/downloads/index.html) (JDK) 版本 8 或更高版本
* 你有了 Azure 订阅后，<a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="创建文本分析资源"  target="_blank">将在 Azure 门户中创建文本分析资源 <span class="docon docon-navigate-external x-hidden-focus"></span></a>，以获取你的密钥和终结点。  部署后，单击“转到资源”。
    * 你需要从创建的资源获取密钥和终结点，以便将应用程序连接到文本分析 API。 你稍后会在快速入门中将密钥和终结点粘贴到下方的代码中。
    * 可以使用免费定价层 (`F0`) 试用该服务，然后再升级到付费层进行生产。
* 若要使用“分析”功能，需要标准 (S) 定价层的“文本分析”资源。

## <a name="setting-up"></a>设置

### <a name="add-the-client-library"></a>添加客户端库

# <a name="version-31-preview"></a>[版本 3.1 预览](#tab/version-3-1)

在首选 IDE 或开发环境中创建 Maven 项目。 然后在项目的 pom.xml 文件中，添加以下依赖项。 可联机找到[用于其他生成工具](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/5.1.0-beta.1)的实现语法。

```xml
<dependencies>
     <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-ai-textanalytics</artifactId>
        <version>5.1.0-beta.3</version>
    </dependency>
</dependencies>
```

# <a name="version-30"></a>[版本 3.0](#tab/version-3)

在首选 IDE 或开发环境中创建 Maven 项目。 然后在项目的 pom.xml 文件中，添加以下依赖项。 可联机找到[用于其他生成工具](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/5.0.0)的实现语法。

```xml
<dependencies>
     <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-ai-textanalytics</artifactId>
        <version>5.0.0</version>
    </dependency>
</dependencies>
```

> [!TIP]
> 想要立即查看整个快速入门代码文件？ 可以[在 GitHub 上](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/TextAnalytics/TextAnalyticsSamples.java)找到它，其中包含此快速入门中的代码示例。 

# <a name="version-21"></a>[版本 2.1](#tab/version-2)

本文仅介绍此 API 的版本 3.x。

---

创建名为 `TextAnalyticsSamples.java` 的 Java 文件。 打开 文件并添加以下 `import` 语句：

```java
import com.azure.core.credential.AzureKeyCredential;
import com.azure.ai.textanalytics.models.*;
import com.azure.ai.textanalytics.TextAnalyticsClientBuilder;
import com.azure.ai.textanalytics.TextAnalyticsClient;
```

在 java 文件中，添加一个新类并添加你的 Azure 资源的密钥和终结点，如下所示。

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```java
public class TextAnalyticsSamples {
    private static String KEY = "<replace-with-your-text-analytics-key-here>";
    private static String ENDPOINT = "<replace-with-your-text-analytics-endpoint-here>";
}
```

将以下 main 方法添加到该类。 稍后将定义此处调用的方法。

# <a name="version-31-preview"></a>[版本 3.1（预览）](#tab/version-3-1)

```java
public static void main(String[] args) {
    //You will create these methods later in the quickstart.
    TextAnalyticsClient client = authenticateClient(KEY, ENDPOINT);

    sentimentAnalysisWithOpinionMiningExample(client)
    detectLanguageExample(client);
    recognizeEntitiesExample(client);
    recognizeLinkedEntitiesExample(client);
    recognizePiiEntitiesExample(client);
    extractKeyPhrasesExample(client);
}
```

# <a name="version-30"></a>[版本 3.0](#tab/version-3)

```java
public static void main(String[] args) {
    //You will create these methods later in the quickstart.
    TextAnalyticsClient client = authenticateClient(KEY, ENDPOINT);

    sentimentAnalysisExample(client);
    detectLanguageExample(client);
    recognizeEntitiesExample(client);
    recognizeLinkedEntitiesExample(client);
    extractKeyPhrasesExample(client);
        AnalyzeOperationExample(client)
}
```

# <a name="version-21"></a>[版本 2.1](#tab/version-2)

本文仅介绍此 API 的版本 3.x。

---


## <a name="object-model"></a>对象模型

文本分析客户端是一个 `TextAnalyticsClient` 对象，该对象使用你的密钥在 Azure 中进行身份验证，并提供用于接受文本（单个字符串或批）的函数。 可以同步方式或异步方式将文本发送到 API。 响应对象包含发送的每个文档的分析信息。 

## <a name="code-examples"></a>代码示例

* [对客户端进行身份验证](#authenticate-the-client)
* [情绪分析](#sentiment-analysis) 
* [观点挖掘](#opinion-mining)
* [语言检测](#language-detection)
* [命名实体识别](#named-entity-recognition-ner)
* [实体链接](#entity-linking)
* [关键短语提取](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>验证客户端

使用文本分析资源的密钥和终结点创建用于实例化 `TextAnalyticsClient` 对象的方法。 此示例同样适用于该 API 的版本 3.0 和 3.1。

```java
static TextAnalyticsClient authenticateClient(String key, String endpoint) {
    return new TextAnalyticsClientBuilder()
        .credential(new AzureKeyCredential(key))
        .endpoint(endpoint)
        .buildClient();
}
```


在程序的 `main()` 方法中，调用身份验证方法来实例化客户端。

## <a name="sentiment-analysis"></a>情绪分析

# <a name="version-31-preview"></a>[版本 3.1 预览](#tab/version-3-1)

> [!NOTE]
> 在版本 `3.1` 中：
> * 情绪分析包括“观点挖掘分析”（可选标志）。 
> * “观点挖掘”包含角度和观点级情绪。 

创建一个名为 `sentimentAnalysisExample()` 的新函数，该函数接受你之前创建的客户端，并调用其 `analyzeSentiment()` 函数。 如果成功，则返回的 `AnalyzeSentimentResult` 对象将包含 `documentSentiment` 和 `sentenceSentiments`，否则将包含 `errorMessage`。 

```java
static void sentimentAnalysisExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "I had the best day of my life. I wish you were there with me.";

    DocumentSentiment documentSentiment = client.analyzeSentiment(text);
    System.out.printf(
        "Recognized document sentiment: %s, positive score: %s, neutral score: %s, negative score: %s.%n",
        documentSentiment.getSentiment(),
        documentSentiment.getConfidenceScores().getPositive(),
        documentSentiment.getConfidenceScores().getNeutral(),
        documentSentiment.getConfidenceScores().getNegative());

    for (SentenceSentiment sentenceSentiment : documentSentiment.getSentences()) {
        System.out.printf(
            "Recognized sentence sentiment: %s, positive score: %s, neutral score: %s, negative score: %s.%n",
            sentenceSentiment.getSentiment(),
            sentenceSentiment.getConfidenceScores().getPositive(),
            sentenceSentiment.getConfidenceScores().getNeutral(),
            sentenceSentiment.getConfidenceScores().getNegative());
        }
    }
}
```

### <a name="output"></a>输出

```console
Recognized document sentiment: positive, positive score: 1.0, neutral score: 0.0, negative score: 0.0.
Recognized sentence sentiment: positive, positive score: 1.0, neutral score: 0.0, negative score: 0.0.
Recognized sentence sentiment: neutral, positive score: 0.21, neutral score: 0.77, negative score: 0.02.
```

### <a name="opinion-mining"></a>观点挖掘

若要使用观点挖掘执行情绪分析，请创建一个名为 `sentimentAnalysisWithOpinionMiningExample()` 的新函数（该函数接受之前创建的客户端），并通过设置选项对象 `AnalyzeSentimentOptions` 调用其 `analyzeSentiment()` 函数。 如果成功，则返回的 `AnalyzeSentimentResult` 对象将包含 `documentSentiment` 和 `sentenceSentiments`，否则将包含 `errorMessage`。 


```java
static void sentimentAnalysisWithOpinionMiningExample(TextAnalyticsClient client)
{
    // The document that needs be analyzed.
    String document = "Bad atmosphere. Not close to plenty of restaurants, hotels, and transit! Staff are not friendly and helpful.";

    System.out.printf("Document = %s%n", document);

    AnalyzeSentimentOptions options = new AnalyzeSentimentOptions().setIncludeOpinionMining(true);
    final DocumentSentiment documentSentiment = client.analyzeSentiment(document, "en", options);
    SentimentConfidenceScores scores = documentSentiment.getConfidenceScores();
    System.out.printf(
            "Recognized document sentiment: %s, positive score: %f, neutral score: %f, negative score: %f.%n",
            documentSentiment.getSentiment(), scores.getPositive(), scores.getNeutral(), scores.getNegative());

    documentSentiment.getSentences().forEach(sentenceSentiment -> {
        SentimentConfidenceScores sentenceScores = sentenceSentiment.getConfidenceScores();
        System.out.printf("\tSentence sentiment: %s, positive score: %f, neutral score: %f, negative score: %f.%n",
                sentenceSentiment.getSentiment(), sentenceScores.getPositive(), sentenceScores.getNeutral(), sentenceScores.getNegative());
        sentenceSentiment.getMinedOpinions().forEach(minedOpinions -> {
            AspectSentiment aspectSentiment = minedOpinions.getAspect();
            System.out.printf("\t\tAspect sentiment: %s, aspect text: %s%n", aspectSentiment.getSentiment(),
                    aspectSentiment.getText());
            SentimentConfidenceScores aspectScores = aspectSentiment.getConfidenceScores();
            System.out.printf("\t\tAspect positive score: %f, negative score: %f.%n",
                    aspectScores.getPositive(), aspectScores.getNegative());
            for (OpinionSentiment opinionSentiment : minedOpinions.getOpinions()) {
                System.out.printf("\t\t\t'%s' opinion sentiment because of \"%s\". Is the opinion negated: %s.%n",
                        opinionSentiment.getSentiment(), opinionSentiment.getText(), opinionSentiment.isNegated());
                SentimentConfidenceScores opinionScores = opinionSentiment.getConfidenceScores();
                System.out.printf("\t\t\tOpinion positive score: %f, negative score: %f.%n",
                        opinionScores.getPositive(), opinionScores.getNegative());
            }
        });
    });
}
```

### <a name="output"></a>输出

```console
Document = Bad atmosphere. Not close to plenty of restaurants, hotels, and transit! Staff are not friendly and helpful.
Recognized document sentiment: negative, positive score: 0.010000, neutral score: 0.140000, negative score: 0.850000.
    Sentence sentiment: negative, positive score: 0.000000, neutral score: 0.000000, negative score: 1.000000.
        Aspect sentiment: negative, aspect text: atmosphere
        Aspect positive score: 0.010000, negative score: 0.990000.
            'negative' opinion sentiment because of "bad". Is the opinion negated: false.
            Opinion positive score: 0.010000, negative score: 0.990000.
    Sentence sentiment: negative, positive score: 0.020000, neutral score: 0.440000, negative score: 0.540000.
    Sentence sentiment: negative, positive score: 0.000000, neutral score: 0.000000, negative score: 1.000000.
        Aspect sentiment: negative, aspect text: Staff
        Aspect positive score: 0.000000, negative score: 1.000000.
            'negative' opinion sentiment because of "friendly". Is the opinion negated: true.
            Opinion positive score: 0.000000, negative score: 1.000000.
            'negative' opinion sentiment because of "helpful". Is the opinion negated: true.
            Opinion positive score: 0.000000, negative score: 1.000000.

Process finished with exit code 0
```

# <a name="version-30"></a>[版本 3.0](#tab/version-3)

创建一个名为 `sentimentAnalysisExample()` 的新函数，该函数接受你之前创建的客户端，并调用其 `analyzeSentiment()` 函数。 如果成功，则返回的 `AnalyzeSentimentResult` 对象将包含 `documentSentiment` 和 `sentenceSentiments`，否则将包含 `errorMessage`。 

```java
static void sentimentAnalysisExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "I had the best day of my life. I wish you were there with me.";

    DocumentSentiment documentSentiment = client.analyzeSentiment(text);
    System.out.printf(
        "Recognized document sentiment: %s, positive score: %s, neutral score: %s, negative score: %s.%n",
        documentSentiment.getSentiment(),
        documentSentiment.getConfidenceScores().getPositive(),
        documentSentiment.getConfidenceScores().getNeutral(),
        documentSentiment.getConfidenceScores().getNegative());

    for (SentenceSentiment sentenceSentiment : documentSentiment.getSentences()) {
        System.out.printf(
            "Recognized sentence sentiment: %s, positive score: %s, neutral score: %s, negative score: %s.%n",
            sentenceSentiment.getSentiment(),
            sentenceSentiment.getConfidenceScores().getPositive(),
            sentenceSentiment.getConfidenceScores().getNeutral(),
            sentenceSentiment.getConfidenceScores().getNegative());
        }
    }
}
```

### <a name="output"></a>输出

```console
Recognized document sentiment: positive, positive score: 1.0, neutral score: 0.0, negative score: 0.0.
Recognized sentence sentiment: positive, positive score: 1.0, neutral score: 0.0, negative score: 0.0.
Recognized sentence sentiment: neutral, positive score: 0.21, neutral score: 0.77, negative score: 0.02.
```

# <a name="version-21"></a>[版本 2.1](#tab/version-2)

本文仅介绍此 API 的版本 3.x。

---

## <a name="language-detection"></a>语言检测

创建一个名为 `detectLanguageExample()` 的新函数，该函数接受你之前创建的客户端并调用其 `detectLanguage()` 函数。 如果成功，则返回的 `DetectLanguageResult` 对象将包含检测到的主要语言和检测到的其他语言的列表，如果失败，则将包含 `errorMessage`。 此示例同样适用于该 API 的版本 3.0 和 3.1。

> [!Tip]
> 在某些情况下，可能很难根据输入区分语言。 可以使用 `countryHint` 参数指定 2 个字母的国家/地区代码。 默认情况下，API 使用“US”作为默认的 countryHint，要删除此行为，可以通过将此值设置为空字符串 `countryHint = ""` 来重置此参数。 若要设置不同的默认值，请设置 `TextAnalyticsClientOptions.DefaultCountryHint` 属性，然后在客户端初始化期间传递它。

```java
static void detectLanguageExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "Ce document est rédigé en Français.";

    DetectedLanguage detectedLanguage = client.detectLanguage(text);
    System.out.printf("Detected primary language: %s, ISO 6391 name: %s, score: %.2f.%n",
        detectedLanguage.getName(),
        detectedLanguage.getIso6391Name(),
        detectedLanguage.getConfidenceScore());
}
```

### <a name="output"></a>输出

```console
Detected primary language: French, ISO 6391 name: fr, score: 1.00.
```

## <a name="named-entity-recognition-ner"></a>命名实体识别 (NER)

# <a name="version-31-preview"></a>[版本 3.1 预览](#tab/version-3-1)

> [!NOTE]
> 在版本 `3.1` 中：
> * NER 包含单独用于检测个人信息的方法。 
> * 实体链接是一个独立于 NER 的请求。

创建一个名为 `recognizeEntitiesExample()` 的新函数，该函数接受你之前创建的客户端，并调用其 `recognizeEntities()` 函数。 如果成功，则返回的 `CategorizedEntityCollection` 对象将包含 `CategorizedEntity` 的列表，否则将包含 `errorMessage`。

```java
static void recognizeEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "I had a wonderful trip to Seattle last week.";

    for (CategorizedEntity entity : client.recognizeEntities(text)) {
        System.out.printf(
            "Recognized entity: %s, entity category: %s, entity sub-category: %s, score: %s, offset: %s, length: %s.%n",
            entity.getText(),
            entity.getCategory(),
            entity.getSubcategory(),
            entity.getConfidenceScore(),
            entity.getOffset(),
            entity.getLength());
    }
}
```

### <a name="output"></a>输出

```console
Recognized entity: trip, entity category: Event, entity sub-category: null, score: 0.61, offset: 8, length: 4.
Recognized entity: Seattle, entity category: Location, entity sub-category: GPE, score: 0.82, offset: 16, length: 7.
Recognized entity: last week, entity category: DateTime, entity sub-category: DateRange, score: 0.8, offset: 24, length: 9.
```

### <a name="entity-linking"></a>实体链接

创建一个名为 `recognizeLinkedEntitiesExample()` 的新函数，该函数接受你之前创建的客户端，并调用其 `recognizeLinkedEntities()` 函数。 如果成功，则返回的 `LinkedEntityCollection` 对象将包含 `LinkedEntity` 的列表，否则将包含 `errorMessage`。 由于链接实体是唯一标识的，因此同一实体的实例将以分组形式出现在 `LinkedEntity` 对象下，显示为 `LinkedEntityMatch` 对象的列表。


```java
static void recognizeLinkedEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, " +
        "to develop and sell BASIC interpreters for the Altair 8800. " +
        "During his career at Microsoft, Gates held the positions of chairman, " +
        "chief executive officer, president and chief software architect, " +
        "while also being the largest individual shareholder until May 2014.";

    System.out.printf("Linked Entities:%n");
    for (LinkedEntity linkedEntity : client.recognizeLinkedEntities(text)) {
        System.out.printf("Name: %s, ID: %s, URL: %s, Data Source: %s.%n",
            linkedEntity.getName(),
            linkedEntity.getDataSourceEntityId(),
            linkedEntity.getUrl(),
            linkedEntity.getDataSource());
        System.out.printf("Matches:%n");
        for (LinkedEntityMatch linkedEntityMatch : linkedEntity.getMatches()) {
            System.out.printf("Text: %s, Score: %.2f, Offset: %s, Length: %s%n",
            linkedEntityMatch.getText(),
            linkedEntityMatch.getConfidenceScore(),
            linkedEntityMatch.getOffset(),
            linkedEntityMatch.getLength());
        }
    }
}
```

### <a name="output"></a>输出

```console
Linked Entities:
Name: Microsoft, ID: Microsoft, URL: https://en.wikipedia.org/wiki/Microsoft, Data Source: Wikipedia.
Matches:
Text: Microsoft, Score: 0.55, Offset: 9, Length: 0
Text: Microsoft, Score: 0.55, Offset: 9, Length: 150
Name: Bill Gates, ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates, Data Source: Wikipedia.
Matches:
Text: Bill Gates, Score: 0.63, Offset: 10, Length: 25
Text: Gates, Score: 0.63, Offset: 5, Length: 161
Name: Paul Allen, ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen, Data Source: Wikipedia.
Matches:
Text: Paul Allen, Score: 0.60, Offset: 10, Length: 40
Name: April 4, ID: April 4, URL: https://en.wikipedia.org/wiki/April_4, Data Source: Wikipedia.
Matches:
Text: April 4, Score: 0.32, Offset: 7, Length: 54
Name: BASIC, ID: BASIC, URL: https://en.wikipedia.org/wiki/BASIC, Data Source: Wikipedia.
Matches:
Text: BASIC, Score: 0.33, Offset: 5, Length: 89
Name: Altair 8800, ID: Altair 8800, URL: https://en.wikipedia.org/wiki/Altair_8800, Data Source: Wikipedia.
Matches:
Text: Altair 8800, Score: 0.88, Offset: 11, Length: 116
```


### <a name="personally-identifiable-information-recognition"></a>个人身份信息识别

创建一个名为 `recognizePiiEntitiesExample()` 的新函数，该函数接受你之前创建的客户端，并调用其 `recognizePiiEntities()` 函数。 如果成功，则返回的 `PiiEntityCollection` 对象将包含 `PiiEntity` 的列表，否则将包含 `errorMessage`。 它还将包含已修订的文本，该文本由输入文本组成，其中所有可识别实体均替换为 `*****`。

```java
static void recognizePiiEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String document = "My SSN is 859-98-0987";
    PiiEntityCollection piiEntityCollection = client.recognizePiiEntities(document);
    System.out.printf("Redacted Text: %s%n", piiEntityCollection.getRedactedText());
    piiEntityCollection.forEach(entity -> System.out.printf(
        "Recognized Personally Identifiable Information entity: %s, entity category: %s, entity subcategory: %s,"
            + " confidence score: %f.%n",
        entity.getText(), entity.getCategory(), entity.getSubcategory(), entity.getConfidenceScore()));
}
```

### <a name="output"></a>输出

```console
Redacted Text: My SSN is ***********
Recognized Personally Identifiable Information entity: 859-98-0987, entity category: U.S. Social Security Number (SSN), entity subcategory: null, confidence score: 0.650000.
```

# <a name="version-30"></a>[版本 3.0](#tab/version-3)

> [!NOTE]
> 在版本 `3.0` 中：
> * NER 包含单独用于检测个人信息的方法。 
> * 实体链接是一个独立于 NER 的请求。

创建一个名为 `recognizeEntitiesExample()` 的新函数，该函数接受你之前创建的客户端，并调用其 `recognizeEntities()` 函数。 如果成功，则返回的 `CategorizedEntityCollection` 对象将包含 `CategorizedEntity` 的列表，否则将包含 `errorMessage`。

```java
static void recognizeEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "I had a wonderful trip to Seattle last week.";

    for (CategorizedEntity entity : client.recognizeEntities(text)) {
        System.out.printf(
            "Recognized entity: %s, entity category: %s, entity sub-category: %s, score: %s.%n",
            entity.getText(),
            entity.getCategory(),
            entity.getSubcategory(),
            entity.getConfidenceScore());
    }
}
```

### <a name="output"></a>输出

```console
Recognized entity: trip, entity category: Event, entity sub-category: null, score: 0.61.
Recognized entity: Seattle, entity category: Location, entity sub-category: GPE, score: 0.82.
Recognized entity: last week, entity category: DateTime, entity sub-category: DateRange, score: 0.8.
```

### <a name="entity-linking"></a>实体链接

创建一个名为 `recognizeLinkedEntitiesExample()` 的新函数，该函数接受你之前创建的客户端，并调用其 `recognizeLinkedEntities()` 函数。 如果成功，则返回的 `LinkedEntityCollection` 对象将包含 `LinkedEntity` 的列表，否则将包含 `errorMessage`。 由于链接实体是唯一标识的，因此同一实体的实例将以分组形式出现在 `LinkedEntity` 对象下，显示为 `LinkedEntityMatch` 对象的列表。

```java
static void recognizeLinkedEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, " +
        "to develop and sell BASIC interpreters for the Altair 8800. " +
        "During his career at Microsoft, Gates held the positions of chairman, " +
        "chief executive officer, president and chief software architect, " +
        "while also being the largest individual shareholder until May 2014.";

    System.out.printf("Linked Entities:%n");
    for (LinkedEntity linkedEntity : client.recognizeLinkedEntities(text)) {
        System.out.printf("Name: %s, ID: %s, URL: %s, Data Source: %s.%n",
            linkedEntity.getName(),
            linkedEntity.getDataSourceEntityId(),
            linkedEntity.getUrl(),
            linkedEntity.getDataSource());
        System.out.printf("Matches:%n");
        for (LinkedEntityMatch linkedEntityMatch : linkedEntity.getMatches()) {
            System.out.printf("Text: %s, Score: %.2f%n",
            linkedEntityMatch.getText(),
            linkedEntityMatch.getConfidenceScore());
        }
    }
}
```

### <a name="output"></a>输出

```console
Linked Entities:
Name: Altair 8800, ID: Altair 8800, URL: https://en.wikipedia.org/wiki/Altair_8800, Data Source: Wikipedia.
Matches:
Text: Altair 8800, Score: 0.88
Name: Bill Gates, ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates, Data Source: Wikipedia.
Matches:
Text: Bill Gates, Score: 0.63
Text: Gates, Score: 0.63
Name: Paul Allen, ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen, Data Source: Wikipedia.
Matches:
Text: Paul Allen, Score: 0.60
Name: Microsoft, ID: Microsoft, URL: https://en.wikipedia.org/wiki/Microsoft, Data Source: Wikipedia.
Matches:
Text: Microsoft, Score: 0.55
Text: Microsoft, Score: 0.55
Name: April 4, ID: April 4, URL: https://en.wikipedia.org/wiki/April_4, Data Source: Wikipedia.
Matches:
Text: April 4, Score: 0.32
Name: BASIC, ID: BASIC, URL: https://en.wikipedia.org/wiki/BASIC, Data Source: Wikipedia.
Matches:
Text: BASIC, Score: 0.33
```

# <a name="version-21"></a>[版本 2.1](#tab/version-2)

本文仅介绍此 API 的版本 3.x。

---

## <a name="key-phrase-extraction"></a>关键短语提取

创建一个名为 `extractKeyPhrasesExample()` 的新函数，该函数接受你之前创建的客户端，并调用其 `extractKeyPhrases()` 函数。 如果成功，则返回的 `ExtractKeyPhraseResult` 对象将包含关键短语的列表，否则将包含 `errorMessage`。 此示例同样适用于该 API 的版本 3.0 和 3.1。

```java
static void extractKeyPhrasesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "My cat might need to see a veterinarian.";

    System.out.printf("Recognized phrases: %n");
    for (String keyPhrase : client.extractKeyPhrases(text)) {
        System.out.printf("%s%n", keyPhrase);
    }
}
```

### <a name="output"></a>输出

```console
Recognized phrases: 
cat
veterinarian
```
---

## <a name="use-the-api-asynchronously-with-the-analyze-operation"></a>使用“分析”操作异步使用 API

# <a name="version-31-preview"></a>[版本 3.1 预览](#tab/version-3-1)

> [!CAUTION]
> 若要使用“分析”操作，必须使用标准 (S) 定价层的“文本分析”资源。  

创建名为 `analyzeOperationExample()` 的新函数，它将调用 `beginAnalyzeTasks()` 函数。 结果将是一个长期操作，将轮询该操作以获得结果。

```java
static void analyzeOperationExample(TextAnalyticsClient client)
{
        List<TextDocumentInput> documents = Arrays.asList(
                        new TextDocumentInput("0", "Microsoft was founded by Bill Gates and Paul Allen.")
                        );

        SyncPoller<TextAnalyticsOperationResult, PagedIterable<AnalyzeTasksResult>> syncPoller =
                        client.beginAnalyzeTasks(documents,
                                        new AnalyzeTasksOptions().setDisplayName("{tasks_display_name}")
                                                        .setEntitiesRecognitionTasks(Arrays.asList(new EntitiesTask())),
                                        Context.NONE);

        syncPoller.waitForCompletion();
        PagedIterable<AnalyzeTasksResult> result = syncPoller.getFinalResult();

        result.forEach(analyzeJobState -> {
                System.out.printf("Job Display Name: %s, Job ID: %s.%n", analyzeJobState.getDisplayName(),
                                analyzeJobState.getJobId());
                System.out.printf("Total tasks: %s, completed: %s, failed: %s, in progress: %s.%n",
                                analyzeJobState.getTotal(), analyzeJobState.getCompleted(), analyzeJobState.getFailed(),
                                analyzeJobState.getInProgress());

                List<RecognizeEntitiesResultCollection> entityRecognitionTasks =
                                analyzeJobState.getEntityRecognitionTasks();
                if (entityRecognitionTasks != null) {
                        entityRecognitionTasks.forEach(taskResult -> {
                                // Recognized entities for each of documents from a batch of documents
                                AtomicInteger counter = new AtomicInteger();
                                for (RecognizeEntitiesResult entitiesResult : taskResult) {
                                        System.out.printf("%n%s%n", documents.get(counter.getAndIncrement()));
                                        if (entitiesResult.isError()) {
                                                // Erroneous document
                                                System.out.printf("Cannot recognize entities. Error: %s%n",
                                                                entitiesResult.getError().getMessage());
                                        } else {
                                                // Valid document
                                                entitiesResult.getEntities().forEach(entity -> System.out.printf(
                                                                "Recognized entity: %s, entity category: %s, entity subcategory: %s, "
                                                                                + "confidence score: %f.%n",
                                                                entity.getText(), entity.getCategory(), entity.getSubcategory(),
                                                                entity.getConfidenceScore()));
                                        }
                                }
                        });
                }
        });
    }
```

将此示例添加到应用程序后，请在 `main()` 方法中调用它。

```java
analyzeOperationExample(client);
```

### <a name="output"></a>Output

```console
Job Display Name: {tasks_display_name}, Job ID: 84fd4db4-0734-47ec-b263-ac5451e83f2a_637432416000000000.
Total tasks: 1, completed: 1, failed: 0, in progress: 0.

Text = Microsoft was founded by Bill Gates and Paul Allen., Id = 0, Language = null
Recognized entity: Microsoft, entity category: Organization, entity subcategory: null, confidence score: 0.960000.
Recognized entity: Bill Gates, entity category: Person, entity subcategory: null, confidence score: 1.000000.
Recognized entity: Paul Allen, entity category: Person, entity subcategory: null, confidence score: 0.990000.
```

还可以使用“分析”操作来检测 PII 和关键短语提取。 请参阅 GitHub 上的[分析示例](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics/lro/AnalyzeTasksAsync.java)。

# <a name="version-30"></a>[版本 3.0](#tab/version-3)

此功能在版本 3.0 中不可用。

# <a name="version-21"></a>[版本 2.1](#tab/version-2)

此功能在版本 2.1 中不可用。

---
