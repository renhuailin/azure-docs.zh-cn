---
title: 快速入门：适用于 C# 的文本分析客户端库 | Microsoft Docs
description: 适用于 C# 的文本分析客户端库入门
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 07/15/2021
ms.author: aahi
ms.reviewer: assafi
ms.openlocfilehash: 817f282f167277c90ecfc19e11dd9312640c8bbf
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2021
ms.locfileid: "122442532"
---
<a name="HOLTop"></a>

# <a name="version-31"></a>[版本 3.1](#tab/version-3-1)

[v3.1 参考文档](/dotnet/api/azure.ai.textanalytics?preserve-view=true&view=azure-dotnet-preview) | [v3.1 库源代码](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics) | [v3.1 包 (NuGet)](https://www.nuget.org/packages/Azure.AI.TextAnalytics/5.1.0) | [v3.1 示例](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics/samples)

# <a name="version-30"></a>[版本 3.0](#tab/version-3)

[v3 参考文档](/dotnet/api/azure.ai.textanalytics) | [v3 库源代码](https://github.com/Azure/azure-sdk-for-net/tree/Azure.AI.TextAnalytics_5.0.0/sdk/textanalytics/Azure.AI.TextAnalytics) | [v3 包(NuGet)](https://www.nuget.org/packages/Azure.AI.TextAnalytics) | [v3 示例](https://github.com/Azure/azure-sdk-for-net/tree/Azure.AI.TextAnalytics_5.0.0/sdk/textanalytics/Azure.AI.TextAnalytics/samples)

---

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/cognitive-services)
* [Visual Studio IDE](https://visualstudio.microsoft.com/vs/)
* [!INCLUDE [contributor-requirement](../../../includes/quickstarts/contributor-requirement.md)]
* 你有了 Azure 订阅后，<a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="创建文本分析资源"  target="_blank">将在 Azure 门户中创建文本分析资源 </a>，以获取你的密钥和终结点。  部署后，单击“转到资源”。
    * 你需要从创建的资源获取密钥和终结点，以便将应用程序连接到文本分析 API。 你稍后会在快速入门中将密钥和终结点粘贴到下方的代码中。
    * 可以使用免费定价层 (`F0`) 试用该服务，然后再升级到付费层进行生产。
* 若要使用“分析”功能，需要标准 (S) 定价层的“文本分析”资源。

## <a name="setting-up"></a>设置

### <a name="create-a-new-net-core-application"></a>创建新的 .NET Core 应用程序

使用 Visual Studio IDE 创建新的 .NET Core 控制台应用。 这会创建包含单个 C# 源文件的“Hello World”项目：*program.cs*。

# <a name="version-31"></a>[版本 3.1](#tab/version-3-1)

右键单击 **解决方案资源管理器** 中的解决方案，然后选择“管理 NuGet 包”，以便安装客户端库。 在打开的包管理器中选择“浏览”，搜索 `Azure.AI.TextAnalytics`。 选择版本 `5.1.0`，然后选择“安装”。 也可使用[包管理器控制台](/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package)。

# <a name="version-30"></a>[版本 3.0](#tab/version-3)

右键单击 **解决方案资源管理器** 中的解决方案，然后选择“管理 NuGet 包”，以便安装客户端库。 在打开的包管理器中选择“浏览”，搜索 `Azure.AI.TextAnalytics`。 选择版本 `5.0.0`，然后选择“安装”。 也可使用[包管理器控制台](/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package)。


> [!TIP]
> 想要立即查看整个快速入门代码文件？ 可以[在 GitHub 上](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/TextAnalytics/program.cs)找到它，其中包含此快速入门中的代码示例。 

---

# <a name="version-31"></a>[版本 3.1](#tab/version-3-1)

打开 *program.cs* 文件并添加以下 `using` 指令：

```csharp
using Azure;
using System;
using System.Globalization;
using Azure.AI.TextAnalytics;
```

在应用程序的 `Program` 类中，为资源的密钥和终结点创建变量。

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly AzureKeyCredential credentials = new AzureKeyCredential("<replace-with-your-text-analytics-key-here>");
private static readonly Uri endpoint = new Uri("<replace-with-your-text-analytics-endpoint-here>");
```

替换应用程序的 `Main` 方法。 稍后将定义此处调用的方法。

```csharp
static void Main(string[] args)
{
    var client = new TextAnalyticsClient(endpoint, credentials);
    // You will implement these methods later in the quickstart.
    SentimentAnalysisExample(client);
    SentimentAnalysisWithOpinionMiningExample(client);
    LanguageDetectionExample(client);
    EntityRecognitionExample(client);
    EntityLinkingExample(client);
    RecognizePIIExample(client);
    KeyPhraseExtractionExample(client);

    Console.Write("Press any key to exit.");
    Console.ReadKey();
}
```

# <a name="version-30"></a>[版本 3.0](#tab/version-3)

打开 *program.cs* 文件并添加以下 `using` 指令：

```csharp
using Azure;
using System;
using System.Globalization;
using Azure.AI.TextAnalytics;
```

在应用程序的 `Program` 类中，为资源的密钥和终结点创建变量。

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly AzureKeyCredential credentials = new AzureKeyCredential("<replace-with-your-text-analytics-key-here>");
private static readonly Uri endpoint = new Uri("<replace-with-your-text-analytics-endpoint-here>");
```

替换应用程序的 `Main` 方法。 稍后将定义此处调用的方法。

```csharp
static void Main(string[] args)
{
    var client = new TextAnalyticsClient(endpoint, credentials);
    // You will implement these methods later in the quickstart.
    SentimentAnalysisExample(client);
    LanguageDetectionExample(client);
    EntityRecognitionExample(client);
    EntityLinkingExample(client);
    KeyPhraseExtractionExample(client);

    Console.Write("Press any key to exit.");
    Console.ReadKey();
}
```

---

## <a name="object-model"></a>对象模型

文本分析客户端是一个 `TextAnalyticsClient` 对象，该对象使用你的密钥在 Azure 中进行身份验证，并提供用于接受文本（单个字符串或批）的函数。 可以同步方式或异步方式将文本发送到 API。 响应对象包含发送的每个文档的分析信息。 

如果使用的是服务的 `3.x` 版本，则可使用可选的 `TextAnalyticsClientOptions` 实例，通过各种默认设置（例如默认语言或国家/地区提示）来初始化客户端。 还可以使用 Azure Active Directory 令牌进行身份验证。 

[!INCLUDE [text-analytics-character-limits](../character-limits.md)]

## <a name="code-examples"></a>代码示例

* [情绪分析](#sentiment-analysis)
* [观点挖掘](#opinion-mining)
* [语言检测](#language-detection)
* [命名实体识别](#named-entity-recognition-ner)
* [实体链接](#entity-linking)
* [关键短语提取](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>验证客户端

# <a name="version-31"></a>[版本 3.1](#tab/version-3-1)

请确保先前的 main 方法使用终结点和凭据创建新的客户端对象。

```csharp
var client = new TextAnalyticsClient(endpoint, credentials);
```

# <a name="version-30"></a>[版本 3.0](#tab/version-3)

请确保先前的 main 方法使用终结点和凭据创建新的客户端对象。

```csharp
var client = new TextAnalyticsClient(endpoint, credentials);
```

---

## <a name="sentiment-analysis"></a>情绪分析

# <a name="version-31"></a>[版本 3.1](#tab/version-3-1)

创建一个名为 `SentimentAnalysisExample()` 的新函数，该函数接受你之前创建的客户端，并调用其 `AnalyzeSentiment()` 函数。 如果成功，则返回的 `Response<DocumentSentiment>` 对象将包含整个输入文档的情绪标签和分数，以及每个句子的情绪分析。 如果发生错误，则会引发 `RequestFailedException`。

```csharp
static void SentimentAnalysisExample(TextAnalyticsClient client)
{
    string inputText = "I had the best day of my life. I wish you were there with me.";
    DocumentSentiment documentSentiment = client.AnalyzeSentiment(inputText);
    Console.WriteLine($"Document sentiment: {documentSentiment.Sentiment}\n");

    foreach (var sentence in documentSentiment.Sentences)
    {
        Console.WriteLine($"\tText: \"{sentence.Text}\"");
        Console.WriteLine($"\tSentence sentiment: {sentence.Sentiment}");
        Console.WriteLine($"\tPositive score: {sentence.ConfidenceScores.Positive:0.00}");
        Console.WriteLine($"\tNegative score: {sentence.ConfidenceScores.Negative:0.00}");
        Console.WriteLine($"\tNeutral score: {sentence.ConfidenceScores.Neutral:0.00}\n");
    }
}
```

### <a name="output"></a>输出

```console
Document sentiment: Positive

        Text: "I had the best day of my life."
        Sentence sentiment: Positive
        Positive score: 1.00
        Negative score: 0.00
        Neutral score: 0.00

        Text: "I wish you were there with me."
        Sentence sentiment: Neutral
        Positive score: 0.21
        Negative score: 0.02
        Neutral score: 0.77
```

## <a name="opinion-mining"></a>观点挖掘

创建一个名为 `SentimentAnalysisWithOpinionMiningExample()` 的新函数，该函数接受你之前创建的客户端，并使用 `AnalyzeSentimentOptions` 包中的 `IncludeOpinionMining` 选项调用其 `AnalyzeSentimentBatch()` 函数。 返回的 `AnalyzeSentimentResultCollection` 对象将包含表示 `Response<DocumentSentiment>` 的 `AnalyzeSentimentResult` 的集合。 `SentimentAnalysis()` 和 `SentimentAnalysisWithOpinionMiningExample()` 的区别在于后者的每个句子都包含 `SentenceOpinion`（表明了所分析的角度和相关评价）。 如果发生错误，则会引发 `RequestFailedException`。

```csharp
static void SentimentAnalysisWithOpinionMiningExample(TextAnalyticsClient client)
{
    var documents = new List<string>
    {
        "The food and service were unacceptable, but the concierge were nice."
    };

    AnalyzeSentimentResultCollection reviews = client.AnalyzeSentimentBatch(documents, options: new AnalyzeSentimentOptions()
    {
        IncludeOpinionMining = true
    });

    foreach (AnalyzeSentimentResult review in reviews)
    {
        Console.WriteLine($"Document sentiment: {review.DocumentSentiment.Sentiment}\n");
        Console.WriteLine($"\tPositive score: {review.DocumentSentiment.ConfidenceScores.Positive:0.00}");
        Console.WriteLine($"\tNegative score: {review.DocumentSentiment.ConfidenceScores.Negative:0.00}");
        Console.WriteLine($"\tNeutral score: {review.DocumentSentiment.ConfidenceScores.Neutral:0.00}\n");
        foreach (SentenceSentiment sentence in review.DocumentSentiment.Sentences)
        {
            Console.WriteLine($"\tText: \"{sentence.Text}\"");
            Console.WriteLine($"\tSentence sentiment: {sentence.Sentiment}");
            Console.WriteLine($"\tSentence positive score: {sentence.ConfidenceScores.Positive:0.00}");
            Console.WriteLine($"\tSentence negative score: {sentence.ConfidenceScores.Negative:0.00}");
            Console.WriteLine($"\tSentence neutral score: {sentence.ConfidenceScores.Neutral:0.00}\n");

            foreach (SentenceOpinion sentenceOpinion in sentence.Opinions)
            {
                Console.WriteLine($"\tTarget: {sentenceOpinion.Target.Text}, Value: {sentenceOpinion.Target.Sentiment}");
                Console.WriteLine($"\tTarget positive score: {sentenceOpinion.Target.ConfidenceScores.Positive:0.00}");
                Console.WriteLine($"\tTarget negative score: {sentenceOpinion.Target.ConfidenceScores.Negative:0.00}");
                foreach (AssessmentSentiment assessment in sentenceOpinion.Assessments)
                {
                    Console.WriteLine($"\t\tRelated Assessment: {assessment.Text}, Value: {assessment.Sentiment}");
                    Console.WriteLine($"\t\tRelated Assessment positive score: {assessment.ConfidenceScores.Positive:0.00}");
                    Console.WriteLine($"\t\tRelated Assessment negative score: {assessment.ConfidenceScores.Negative:0.00}");
                }
            }
        }
        Console.WriteLine($"\n");
    }
}
```

### <a name="output"></a>输出

```console
Document sentiment: Positive

        Positive score: 0.84
        Negative score: 0.16
        Neutral score: 0.00

        Text: "The food and service were unacceptable, but the concierge were nice."
        Sentence sentiment: Positive
        Sentence positive score: 0.84
        Sentence negative score: 0.16
        Sentence neutral score: 0.00

        Target: food, Value: Negative
        Target positive score: 0.01
        Target negative score: 0.99
                Related Assessment: unacceptable, Value: Negative
                Related Assessment positive score: 0.01
                Related Assessment negative score: 0.99
        Target: service, Value: Negative
        Target positive score: 0.01
        Target negative score: 0.99
                Related Assessment: unacceptable, Value: Negative
                Related Assessment positive score: 0.01
                Related Assessment negative score: 0.99
        Target: concierge, Value: Positive
        Target positive score: 1.00
        Target negative score: 0.00
                Related Assessment: nice, Value: Positive
                Related Assessment positive score: 1.00
                Related Assessment negative score: 0.00

Press any key to exit.
```

# <a name="version-30"></a>[版本 3.0](#tab/version-3)

创建一个名为 `SentimentAnalysisExample()` 的新函数，该函数接受你之前创建的客户端，并调用其 `AnalyzeSentiment()` 函数。 如果成功，则返回的 `Response<DocumentSentiment>` 对象将包含整个输入文档的情绪标签和分数，以及每个句子的情绪分析。 如果发生错误，则会引发 `RequestFailedException`。

```csharp
static void SentimentAnalysisExample(TextAnalyticsClient client)
{
    string inputText = "I had the best day of my life. I wish you were there with me.";
    DocumentSentiment documentSentiment = client.AnalyzeSentiment(inputText);
    Console.WriteLine($"Document sentiment: {documentSentiment.Sentiment}\n");

    foreach (var sentence in documentSentiment.Sentences)
    {
        Console.WriteLine($"\tText: \"{sentence.Text}\"");
        Console.WriteLine($"\tSentence sentiment: {sentence.Sentiment}");
        Console.WriteLine($"\tPositive score: {sentence.ConfidenceScores.Positive:0.00}");
        Console.WriteLine($"\tNegative score: {sentence.ConfidenceScores.Negative:0.00}");
        Console.WriteLine($"\tNeutral score: {sentence.ConfidenceScores.Neutral:0.00}\n");
    }
}
```

### <a name="output"></a>输出

```console
Document sentiment: Positive

        Text: "I had the best day of my life."
        Sentence sentiment: Positive
        Positive score: 1.00
        Negative score: 0.00
        Neutral score: 0.00

        Text: "I wish you were there with me."
        Sentence sentiment: Neutral
        Positive score: 0.21
        Negative score: 0.02
        Neutral score: 0.77
```

---

## <a name="language-detection"></a>语言检测

# <a name="version-31"></a>[版本 3.1](#tab/version-3-1)


创建一个名为 `LanguageDetectionExample()` 的新函数，该函数接受你之前创建的客户端并调用其 `DetectLanguage()` 函数。 返回的 `Response<DetectedLanguage>` 对象会包含检测到的语言及其名称和 ISO-6391 代码。 如果发生错误，则会引发 `RequestFailedException`。

> [!Tip]
> 在某些情况下，可能很难根据输入区分语言。 可以使用 `countryHint` 参数指定 2 个字母的国家/地区代码。 默认情况下，API 使用“US”作为默认的 countryHint，要删除此行为，可以通过将此值设置为空字符串 `countryHint = ""` 来重置此参数。 若要设置不同的默认值，请设置 `TextAnalyticsClientOptions.DefaultCountryHint` 属性，然后在客户端初始化期间传递它。

```csharp
static void LanguageDetectionExample(TextAnalyticsClient client)
{
    DetectedLanguage detectedLanguage = client.DetectLanguage("Ce document est rédigé en Français.");
    Console.WriteLine("Language:");
    Console.WriteLine($"\t{detectedLanguage.Name},\tISO-6391: {detectedLanguage.Iso6391Name}\n");
}
```

### <a name="output"></a>输出

```console
Language:
        French, ISO-6391: fr
```

# <a name="version-30"></a>[版本 3.0](#tab/version-3)


创建一个名为 `LanguageDetectionExample()` 的新函数，该函数接受你之前创建的客户端并调用其 `DetectLanguage()` 函数。 返回的 `Response<DetectedLanguage>` 对象会包含检测到的语言及其名称和 ISO-6391 代码。 如果发生错误，则会引发 `RequestFailedException`。

> [!Tip]
> 在某些情况下，可能很难根据输入区分语言。 可以使用 `countryHint` 参数指定 2 个字母的国家/地区代码。 默认情况下，API 使用“US”作为默认的 countryHint，要删除此行为，可以通过将此值设置为空字符串 `countryHint = ""` 来重置此参数。 若要设置不同的默认值，请设置 `TextAnalyticsClientOptions.DefaultCountryHint` 属性，然后在客户端初始化期间传递它。

```csharp
static void LanguageDetectionExample(TextAnalyticsClient client)
{
    DetectedLanguage detectedLanguage = client.DetectLanguage("Ce document est rédigé en Français.");
    Console.WriteLine("Language:");
    Console.WriteLine($"\t{detectedLanguage.Name},\tISO-6391: {detectedLanguage.Iso6391Name}\n");
}
```

### <a name="output"></a>输出

```console
Language:
        French, ISO-6391: fr
```


---

## <a name="named-entity-recognition-ner"></a>命名实体识别 (NER)

# <a name="version-31"></a>[版本 3.1](#tab/version-3-1)


创建一个名为 `EntityRecognitionExample()` 的新函数，该函数接受你之前创建的客户端，调用其 `RecognizeEntities()` 函数并循环访问结果。 返回的 `Response<CategorizedEntityCollection>` 对象将包含检测到的实体 `CategorizedEntity` 的集合。 如果发生错误，则会引发 `RequestFailedException`。

```csharp
static void EntityRecognitionExample(TextAnalyticsClient client)
{
    var response = client.RecognizeEntities("I had a wonderful trip to Seattle last week.");
    Console.WriteLine("Named Entities:");
    foreach (var entity in response.Value)
    {
        Console.WriteLine($"\tText: {entity.Text},\tCategory: {entity.Category},\tSub-Category: {entity.SubCategory}");
        Console.WriteLine($"\t\tScore: {entity.ConfidenceScore:F2},\tLength: {entity.Length},\tOffset: {entity.Offset}\n");
    }
}
```

### <a name="output"></a>输出

```console
Named Entities:
        Text: trip,     Category: Event,        Sub-Category:
                Score: 0.61,    Length: 4,      Offset: 18

        Text: Seattle,  Category: Location,     Sub-Category: GPE
                Score: 0.82,    Length: 7,      Offset: 26

        Text: last week,        Category: DateTime,     Sub-Category: DateRange
                Score: 0.80,    Length: 9,      Offset: 34
```

## <a name="personally-identifiable-information-pii-recognition"></a>个人身份信息识别 (PII) 识别

创建一个名为 `RecognizePIIExample()` 的新函数，该函数接受你之前创建的客户端，调用其 `RecognizePiiEntities()` 函数并循环访问结果。 返回的 `PiiEntityCollection` 表示检测到的 PII 实体的列表。 如果发生错误，则会引发 `RequestFailedException`。

```csharp
static void RecognizePIIExample(TextAnalyticsClient client)
{
    string document = "A developer with SSN 859-98-0987 whose phone number is 800-102-1100 is building tools with our APIs.";

    PiiEntityCollection entities = client.RecognizePiiEntities(document).Value;

    Console.WriteLine($"Redacted Text: {entities.RedactedText}");
    if (entities.Count > 0)
    {
        Console.WriteLine($"Recognized {entities.Count} PII entit{(entities.Count > 1 ? "ies" : "y")}:");
        foreach (PiiEntity entity in entities)
        {
            Console.WriteLine($"Text: {entity.Text}, Category: {entity.Category}, SubCategory: {entity.SubCategory}, Confidence score: {entity.ConfidenceScore}");
        }
    }
    else
    {
        Console.WriteLine("No entities were found.");
    }
}
```

### <a name="output"></a>输出

```console
Redacted Text: A developer with SSN *********** whose phone number is ************ is building tools with our APIs.
Recognized 2 PII entities:
Text: 859-98-0987, Category: U.S. Social Security Number (SSN), SubCategory: , Confidence score: 0.65
Text: 800-102-1100, Category: Phone Number, SubCategory: , Confidence score: 0.8
```

# <a name="version-30"></a>[版本 3.0](#tab/version-3)

创建一个名为 `EntityRecognitionExample()` 的新函数，该函数接受你之前创建的客户端，调用其 `RecognizeEntities()` 函数并循环访问结果。 返回的 `Response<IReadOnlyCollection<CategorizedEntity>>` 对象将包含检测到的实体的列表。 如果发生错误，则会引发 `RequestFailedException`。

```csharp
static void EntityRecognitionExample(TextAnalyticsClient client)
{
    var response = client.RecognizeEntities("I had a wonderful trip to Seattle last week.");
    Console.WriteLine("Named Entities:");
    foreach (var entity in response.Value)
    {
        Console.WriteLine($"\tText: {entity.Text},\tCategory: {entity.Category},\tSub-Category: {entity.SubCategory}");
        Console.WriteLine($"\t\tScore: {entity.ConfidenceScore:F2}\n");
    }
}
```

### <a name="output"></a>输出

```console
Named Entities:
        Text: trip,     Category: Event,        Sub-Category:
                Score: 0.61

        Text: Seattle,  Category: Location,     Sub-Category: GPE
                Score: 0.82

        Text: last week,        Category: DateTime,     Sub-Category: DateRange
                Score: 0.80
```

--- 

## <a name="entity-linking"></a>实体链接

# <a name="version-31"></a>[版本 3.1](#tab/version-3-1)

创建一个名为 `EntityLinkingExample()` 的新函数，该函数接受你之前创建的客户端，调用其 `RecognizeLinkedEntities()` 函数并循环访问结果。 返回的 `Response<LinkedEntityCollection>` 对象将包含检测到的实体 `LinkedEntity` 的集合。 如果发生错误，则会引发 `RequestFailedException`。 由于链接实体是唯一标识的，因此同一实体的实例将以分组形式出现在 `LinkedEntity` 对象下，显示为 `LinkedEntityMatch` 对象的列表。

```csharp
static void EntityLinkingExample(TextAnalyticsClient client)
{
    var response = client.RecognizeLinkedEntities(
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, " +
        "to develop and sell BASIC interpreters for the Altair 8800. " +
        "During his career at Microsoft, Gates held the positions of chairman, " +
        "chief executive officer, president and chief software architect, " +
        "while also being the largest individual shareholder until May 2014.");
    Console.WriteLine("Linked Entities:");
    foreach (var entity in response.Value)
    {
        Console.WriteLine($"\tName: {entity.Name},\tID: {entity.DataSourceEntityId},\tURL: {entity.Url}\tData Source: {entity.DataSource}");
        Console.WriteLine("\tMatches:");
        foreach (var match in entity.Matches)
        {
            Console.WriteLine($"\t\tText: {match.Text}");
            Console.WriteLine($"\t\tScore: {match.ConfidenceScore:F2}");
            Console.WriteLine($"\t\tLength: {match.Length}");
            Console.WriteLine($"\t\tOffset: {match.Offset}\n");
        }
    }
}
```

### <a name="output"></a>输出

```console
Linked Entities:
        Name: Microsoft,        ID: Microsoft,  URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft
                Score: 0.55
                Length: 9
                Offset: 0

                Text: Microsoft
                Score: 0.55
                Length: 9
                Offset: 150

        Name: Bill Gates,       ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates
                Score: 0.63
                Length: 10
                Offset: 25

                Text: Gates
                Score: 0.63
                Length: 5
                Offset: 161

        Name: Paul Allen,       ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen
                Score: 0.60
                Length: 10
                Offset: 40

        Name: April 4,  ID: April 4,    URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4
                Score: 0.32
                Length: 7
                Offset: 54

        Name: BASIC,    ID: BASIC,      URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC
                Score: 0.33
                Length: 5
                Offset: 89

        Name: Altair 8800,      ID: Altair 8800,        URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800
                Score: 0.88
                Length: 11
                Offset: 116
```

# <a name="version-30"></a>[版本 3.0](#tab/version-3)

创建一个名为 `EntityLinkingExample()` 的新函数，该函数接受你之前创建的客户端，调用其 `RecognizeLinkedEntities()` 函数并循环访问结果。 返回的 `Response<IReadOnlyCollection<LinkedEntity>>` 表示检测到的实体的列表。 如果发生错误，则会引发 `RequestFailedException`。 由于链接实体是唯一标识的，因此同一实体的实例将以分组形式出现在 `LinkedEntity` 对象下，显示为 `LinkedEntityMatch` 对象的列表。

```csharp
static void EntityLinkingExample(TextAnalyticsClient client)
{
    var response = client.RecognizeLinkedEntities(
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, " +
        "to develop and sell BASIC interpreters for the Altair 8800. " +
        "During his career at Microsoft, Gates held the positions of chairman, " +
        "chief executive officer, president and chief software architect, " +
        "while also being the largest individual shareholder until May 2014.");
    Console.WriteLine("Linked Entities:");
    foreach (var entity in response.Value)
    {
        Console.WriteLine($"\tName: {entity.Name},\tID: {entity.DataSourceEntityId},\tURL: {entity.Url}\tData Source: {entity.DataSource}");
        Console.WriteLine("\tMatches:");
        foreach (var match in entity.Matches)
        {
            Console.WriteLine($"\t\tText: {match.Text}");
            Console.WriteLine($"\t\tScore: {match.ConfidenceScore:F2}\n");
        }
    }
}
```

### <a name="output"></a>输出

```console
Linked Entities:
        Name: Altair 8800,      ID: Altair 8800,        URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800
                Score: 0.88

        Name: Bill Gates,       ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates
                Score: 0.63

                Text: Gates
                Score: 0.63

        Name: Paul Allen,       ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen
                Score: 0.60

        Name: Microsoft,        ID: Microsoft,  URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft
                Score: 0.55

                Text: Microsoft
                Score: 0.55

        Name: April 4,  ID: April 4,    URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4
                Score: 0.32

        Name: BASIC,    ID: BASIC,      URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC
                Score: 0.33
```

---

## <a name="key-phrase-extraction"></a>关键短语提取

# <a name="version-31"></a>[版本 3.1](#tab/version-3-1)

创建一个名为 `KeyPhraseExtractionExample()` 的新函数，该函数接受你之前创建的客户端，并调用其 `ExtractKeyPhrases()` 函数。 返回的 `<Response<KeyPhraseCollection>` 对象将包含检测到的关键短语的列表。 如果发生错误，则会引发 `RequestFailedException`。

```csharp
static void KeyPhraseExtractionExample(TextAnalyticsClient client)
{
    var response = client.ExtractKeyPhrases("My cat might need to see a veterinarian.");

    // Printing key phrases
    Console.WriteLine("Key phrases:");

    foreach (string keyphrase in response.Value)
    {
        Console.WriteLine($"\t{keyphrase}");
    }
}
```

### <a name="output"></a>输出

```console
Key phrases:
    cat
    veterinarian
```

# <a name="version-30"></a>[版本 3.0](#tab/version-3)

创建一个名为 `KeyPhraseExtractionExample()` 的新函数，该函数接受你之前创建的客户端，并调用其 `ExtractKeyPhrases()` 函数。 返回的 `<Response<IReadOnlyCollection<string>>` 对象将包含检测到的关键短语的列表。 如果发生错误，则会引发 `RequestFailedException`。

```csharp
static void KeyPhraseExtractionExample(TextAnalyticsClient client)
{
    var response = client.ExtractKeyPhrases("My cat might need to see a veterinarian.");

    // Printing key phrases
    Console.WriteLine("Key phrases:");

    foreach (string keyphrase in response.Value)
    {
        Console.WriteLine($"\t{keyphrase}");
    }
}
```

### <a name="output"></a>输出

```console
Key phrases:
    cat
    veterinarian
```

---


## <a name="extract-health-entities"></a>提取健康状况实体

[!INCLUDE [health operation pricing](../health-operation-pricing-caution.md)]

可以使用文本分析来执行异步请求，以从文本中提取健康状况实体。 下面的示例演示了基本示例。 可以[在 GitHub 上](https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/textanalytics/Azure.AI.TextAnalytics/samples/Sample7_AnalyzeHealthcareEntities.md)找到更高级的示例。

# <a name="version-31"></a>[版本 3.1](#tab/version-3-1)

```csharp
static async Task healthExample(TextAnalyticsClient client)
{
    string document = "Prescribed 100mg ibuprofen, taken twice daily.";

    List<string> batchInput = new List<string>()
    {
        document
    };
    AnalyzeHealthcareEntitiesOperation healthOperation = await client.StartAnalyzeHealthcareEntitiesAsync(batchInput);
    await healthOperation.WaitForCompletionAsync();

    await foreach (AnalyzeHealthcareEntitiesResultCollection documentsInPage in healthOperation.Value)
    {
        Console.WriteLine($"Results of Azure Text Analytics \"Healthcare Async\" Model, version: \"{documentsInPage.ModelVersion}\"");
        Console.WriteLine("");

        foreach (AnalyzeHealthcareEntitiesResult entitiesInDoc in documentsInPage)
        {
            if (!entitiesInDoc.HasError)
            {
                foreach (var entity in entitiesInDoc.Entities)
                {
                    // view recognized healthcare entities
                    Console.WriteLine($"  Entity: {entity.Text}");
                    Console.WriteLine($"  Category: {entity.Category}");
                    Console.WriteLine($"  Offset: {entity.Offset}");
                    Console.WriteLine($"  Length: {entity.Length}");
                    Console.WriteLine($"  NormalizedText: {entity.NormalizedText}");
                }
                Console.WriteLine($"  Found {entitiesInDoc.EntityRelations.Count} relations in the current document:");
                Console.WriteLine("");

                // view recognized healthcare relations
                foreach (HealthcareEntityRelation relations in entitiesInDoc.EntityRelations)
                {
                    Console.WriteLine($"    Relation: {relations.RelationType}");
                    Console.WriteLine($"    For this relation there are {relations.Roles.Count} roles");

                    // view relation roles
                    foreach (HealthcareEntityRelationRole role in relations.Roles)
                    {
                        Console.WriteLine($"      Role Name: {role.Name}");

                        Console.WriteLine($"      Associated Entity Text: {role.Entity.Text}");
                        Console.WriteLine($"      Associated Entity Category: {role.Entity.Category}");
                        Console.WriteLine("");
                    }
                    Console.WriteLine("");
                }
            }
            else
            {
                Console.WriteLine("  Error!");
                Console.WriteLine($"  Document error code: {entitiesInDoc.Error.ErrorCode}.");
                Console.WriteLine($"  Message: {entitiesInDoc.Error.Message}");
            }
            Console.WriteLine("");
        }
    }
}
```

```console
Results of Azure Text Analytics "Healthcare Async" Model, version: "2021-05-15"

  Entity: 100mg
  Category: Dosage
  Offset: 11
  Length: 5
  NormalizedText:
  Entity: ibuprofen
  Category: MedicationName
  Offset: 17
  Length: 9
  NormalizedText: ibuprofen
  Entity: twice daily
  Category: Frequency
  Offset: 34
  Length: 11
  NormalizedText:
  Found 2 relations in the current document:

    Relation: DosageOfMedication
    For this relation there are 2 roles
      Role Name: Dosage
      Associated Entity Text: 100mg
      Associated Entity Category: Dosage

      Role Name: Medication
      Associated Entity Text: ibuprofen
      Associated Entity Category: MedicationName


    Relation: FrequencyOfMedication
    For this relation there are 2 roles
      Role Name: Medication
      Associated Entity Text: ibuprofen
      Associated Entity Category: MedicationName

      Role Name: Frequency
      Associated Entity Text: twice daily
      Associated Entity Category: Frequency
```

# <a name="version-30"></a>[版本 3.0](#tab/version-3)

此功能在版本 3.0 中不可用。

---

## <a name="use-the-api-asynchronously-with-the-analyze-operation"></a>使用“分析”操作异步使用 API

# <a name="version-31"></a>[版本 3.1](#tab/version-3-1)

可以使用“分析”操作执行异步批处理请求实现以下目的：NER、关键短语提取、情绪分析和 PII 检测。 下面的示例演示了有关一个操作的基本示例。 可以[在 GitHub 上](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/textanalytics/Azure.AI.TextAnalytics/samples/Sample_AnalyzeActions.md)找到更高级的示例。

[!INCLUDE [Analyze operation pricing](../analyze-operation-pricing-caution.md)]

向 C# 文件添加以下 using 语句。

```csharp
using System.Threading.Tasks;
using System.Collections.Generic;
using System.Linq;
```

创建一个名为 `AnalyzeOperationExample()` 的新函数，该函数接受你之前创建的客户端，并调用其 `StartAnalyzeBatchActionsAsync()` 函数。 返回的操作将包含一个 `AnalyzeBatchActionsResult` 对象。 由于它是一个长期操作，因此请在 `operation.WaitForCompletionAsync()` 上使用 `await` 以便更新值。 `WaitForCompletionAsync()` 完成后，应会在 `operation.Value` 中更新集合。 如果发生错误，则会引发 `RequestFailedException`。


```csharp
static async Task AnalyzeOperationExample(TextAnalyticsClient client)
    {
        string inputText = "Microsoft was founded by Bill Gates and Paul Allen.";

        var batchDocuments = new List<string> { inputText };


        TextAnalyticsActions actions = new TextAnalyticsActions()
        {
            RecognizeEntitiesActions = new List<RecognizeEntitiesAction>() { new RecognizeEntitiesAction() },
            ExtractKeyPhrasesActions = new List<ExtractKeyPhrasesAction>() { new ExtractKeyPhrasesAction() },
            DisplayName = "Analyze Operation Quick Start Example"
        };

        AnalyzeActionsOperation operation = await client.StartAnalyzeActionsAsync(batchDocuments, actions);

        await operation.WaitForCompletionAsync();

        Console.WriteLine($"Status: {operation.Status}");
        Console.WriteLine($"Created On: {operation.CreatedOn}");
        Console.WriteLine($"Expires On: {operation.ExpiresOn}");
        Console.WriteLine($"Last modified: {operation.LastModified}");
        if (!string.IsNullOrEmpty(operation.DisplayName))
            Console.WriteLine($"Display name: {operation.DisplayName}");
        //Console.WriteLine($"Total actions: {operation.TotalActions}");
        Console.WriteLine($"  Succeeded actions: {operation.ActionsSucceeded}");
        Console.WriteLine($"  Failed actions: {operation.ActionsFailed}");
        Console.WriteLine($"  In progress actions: {operation.ActionsInProgress}");

        await foreach (AnalyzeActionsResult documentsInPage in operation.Value)
        {
            RecognizeEntitiesResultCollection entitiesResult = documentsInPage.RecognizeEntitiesResults.FirstOrDefault().DocumentsResults;
            ExtractKeyPhrasesResultCollection keyPhrasesResults = documentsInPage.ExtractKeyPhrasesResults.FirstOrDefault().DocumentsResults;

            Console.WriteLine("Recognized Entities");

            foreach (RecognizeEntitiesResult result in entitiesResult)
            {
                Console.WriteLine($"  Recognized the following {result.Entities.Count} entities:");

                foreach (CategorizedEntity entity in result.Entities)
                {
                    Console.WriteLine($"  Entity: {entity.Text}");
                    Console.WriteLine($"  Category: {entity.Category}");
                    Console.WriteLine($"  Offset: {entity.Offset}");
                    Console.WriteLine($"  Length: {entity.Length}");
                    Console.WriteLine($"  ConfidenceScore: {entity.ConfidenceScore}");
                    Console.WriteLine($"  SubCategory: {entity.SubCategory}");
                }
                Console.WriteLine("");
            }

            Console.WriteLine("Key Phrases");
            
            foreach (ExtractKeyPhrasesResult documentResults in keyPhrasesResults)
            {
                Console.WriteLine($"  Recognized the following {documentResults.KeyPhrases.Count} Keyphrases:");

                foreach (string keyphrase in documentResults.KeyPhrases)
                {
                    Console.WriteLine($"  {keyphrase}");
                }
                Console.WriteLine("");
            }
            
        }
    }
```

将此示例添加到应用程序后，请使用 `await` 在 `main()` 方法中调用。 由于分析操作是异步的，你需要将你的 `Main()` 方法更新为 `async Task` 类型。

```csharp
static async Task Main(string[] args)
{
    var client = new TextAnalyticsClient(endpoint, credentials);
    await AnalyzeOperationExample(client).ConfigureAwait(false);
}
```

### <a name="output"></a>输出

```console
Status: succeeded
Created On: 3/10/2021 2:25:01 AM +00:00
Expires On: 3/11/2021 2:25:01 AM +00:00
Last modified: 3/10/2021 2:25:05 AM +00:00
Display name: Analyze Operation Quick Start Example
Total actions: 1
  Succeeded actions: 1
  Failed actions: 0
  In progress actions: 0
Recognized Entities
    Recognized the following 3 entities:
    Entity: Microsoft
    Category: Organization
    Offset: 0
    ConfidenceScore: 0.83
    SubCategory: 
    Entity: Bill Gates
    Category: Person
    Offset: 25
    ConfidenceScore: 0.85
    SubCategory: 
    Entity: Paul Allen
    Category: Person
    Offset: 40
    ConfidenceScore: 0.9
    SubCategory: 
```

# <a name="version-30"></a>[版本 3.0](#tab/version-3)

此功能在版本 3.0 中不可用。

---
