---
author: aahill
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 07/15/2021
ms.author: aahi
ms.openlocfilehash: c66a17c1591f9a0070af1f6fc6b0ec6127292f6b
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114594581"
---
<a name="HOLTop"></a>

# <a name="version-31"></a>[版本 3.1](#tab/version-3-1)

[v3.1 参考文档](/python/api/azure-ai-textanalytics/azure.ai.textanalytics?preserve-view=true&view=azure-python-preview) | [v3.1 库源代码](https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/textanalytics/azure-ai-textanalytics) | [v3.1 包 (PiPy)](https://pypi.org/project/azure-ai-textanalytics/5.1.0/) | [v3.1 示例](https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/textanalytics/azure-ai-textanalytics/samples)

# <a name="version-30"></a>[版本 3.0](#tab/version-3)

[v3 参考文档](/python/api/azure-ai-textanalytics/azure.ai.textanalytics) | [v3 库源代码](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics) | [v3 包(PiPy)](https://pypi.org/project/azure-ai-textanalytics/) | [v3 示例](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/samples)

---

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/cognitive-services)
* [Python 3.x](https://www.python.org/)
* 你有了 Azure 订阅后，<a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="创建文本分析资源"  target="_blank">将在 Azure 门户中创建文本分析资源 </a>，以获取你的密钥和终结点。 部署后，单击“转到资源”。
    * 你需要从创建的资源获取密钥和终结点，以便将应用程序连接到文本分析 API。 你稍后会在快速入门中将密钥和终结点粘贴到下方的代码中。
    * 可以使用免费定价层 (`F0`) 试用该服务，然后再升级到付费层进行生产。
* 若要使用“分析”功能，需要标准 (S) 定价层的“文本分析”资源。

## <a name="setting-up"></a>设置

### <a name="install-the-client-library"></a>安装客户端库

在安装 Python 后，可以通过以下命令安装客户端库：

# <a name="version-31"></a>[版本 3.1](#tab/version-3-1)

```console
pip install azure-ai-textanalytics==5.1.0
```

> [!TIP]
> 想要立即查看整个快速入门代码文件？ 可以[在 GitHub 上](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/TextAnalytics/python-v3-client-library.py)找到它，其中包含此快速入门中的代码示例。 

# <a name="version-30"></a>[版本 3.0](#tab/version-3)

```console
pip install --upgrade azure-ai-textanalytics
```

> [!TIP]
> 想要立即查看整个快速入门代码文件？ 可以[在 GitHub 上](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/TextAnalytics/python-v3-client-library.py)找到它，其中包含此快速入门中的代码示例。 


---

### <a name="create-a-new-python-application"></a>创建新的 Python 应用程序

创建一个新的 Python 文件，为资源的 Azure 终结点和订阅密钥创建变量。

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```python
key = "<paste-your-text-analytics-key-here>"
endpoint = "<paste-your-text-analytics-endpoint-here>"
```


## <a name="object-model"></a>对象模型

# <a name="version-31"></a>[版本 3.1](#tab/version-3-1)

文本分析客户端是向 Azure 进行身份验证的 `TextAnalyticsClient` 对象。 该客户端提供了几种方法来分析文本。 

将处理文本以 `documents` 列表的形式发送到 API 时，该列表可以是字符串列表、类似于字典的表示形式列表或 `TextDocumentInput/DetectLanguageInput` 列表。 `dict-like` 对象包含 `id`、`text` 和 `language/country_hint` 的组合。 `text` 属性存储要以源 `country_hint` 分析的文本，而 `id` 则可以是任何值。 

响应对象是一个列表，其中包含每个文档的分析信息。 

# <a name="version-30"></a>[版本 3.0](#tab/version-3)

文本分析客户端是一个 `TextAnalyticsClient` 对象，它使用你的密钥向 Azure 进行身份验证。 该客户端提供了几种方法来成批分析文本。 

当成批处理时，文本将以 `documents` 的列表的形式发送到 API，该项是包含 `id`、`text` 和 `language` 属性的组合的 `dictionary` 对象，具体取决于所用的方法。 `text` 属性存储要以源 `language` 分析的文本，而 `id` 则可以是任何值。 

响应对象是一个列表，其中包含每个文档的已分析信息。 

---

## <a name="code-examples"></a>代码示例

这些代码片段展示了如何使用适用于 Python 的文本分析客户端库执行以下任务：

# <a name="version-31"></a>[版本 3.1](#tab/version-3-1)

* [对客户端进行身份验证](#authenticate-the-client)
* [情绪分析](#sentiment-analysis)
* [观点挖掘](#opinion-mining)
* [语言检测](#language-detection)
* [命名实体识别](#named-entity-recognition-ner) 
* [个人身份信息识别](#personally-identifiable-information-pii-recognition) 
* [实体链接](#entity-linking)
* [关键短语提取](#key-phrase-extraction)


# <a name="version-30"></a>[版本 3.0](#tab/version-3)

* [对客户端进行身份验证](#authenticate-the-client)
* [情绪分析](#sentiment-analysis)
* [语言检测](#language-detection)
* [命名实体识别](#named-entity-recognition-ner) 
* [实体链接](#entity-linking)
* [关键短语提取](#key-phrase-extraction)

---

## <a name="authenticate-the-client"></a>验证客户端

# <a name="version-31"></a>[版本 3.1](#tab/version-3-1)

创建一个函数，以便通过上面创建的 `key` 和 `endpoint` 来实例化 `TextAnalyticsClient` 对象。 然后创建一个新客户端。 

```python
from azure.ai.textanalytics import TextAnalyticsClient
from azure.core.credentials import AzureKeyCredential

def authenticate_client():
    ta_credential = AzureKeyCredential(key)
    text_analytics_client = TextAnalyticsClient(
            endpoint=endpoint, 
            credential=ta_credential)
    return text_analytics_client

client = authenticate_client()
```

# <a name="version-30"></a>[版本 3.0](#tab/version-3)

创建一个函数，以便通过上面创建的 `key` 和 `endpoint` 来实例化 `TextAnalyticsClient` 对象。 然后创建一个新客户端。 请注意，应定义 `api_version=TextAnalyticsApiVersion.V3_0` 以便使用版本 3.0。

```python
# use this code if you're using SDK version is 5.0.0
from azure.ai.textanalytics import TextAnalyticsClient
from azure.core.credentials import AzureKeyCredential

def authenticate_client():
    ta_credential = AzureKeyCredential(key)
    text_analytics_client = TextAnalyticsClient(
            endpoint=endpoint, 
            credential=ta_credential) 
    return text_analytics_client

client = authenticate_client()
```

如果使用 `pip install azure-ai-textanalytics --pre` 安装了 v5.1.0 版的客户端库，可使用客户端的 `api_version` 参数指定 v3.0 版的文本分析 API。 如果客户端是 v5.1.0 或更高版本，请仅使用以下 `authenticate_client()` 方法。

```python
# Only use the following code sample if you're using v5.1.0 of the client library, 
# and are looking to specify v3.0 of the Text Analytics API for your client
from azure.ai.textanalytics import TextAnalyticsClient, TextAnalyticsApiVersion
from azure.core.credentials import AzureKeyCredential
def authenticate_client():
   ta_credential = AzureKeyCredential(key)
   text_analytics_client = TextAnalyticsClient(
     endpoint=endpoint,
     credential=ta_credential,
     api_version=TextAnalyticsApiVersion.V3_0
   )
   
client = authenticate_client()
```

--- 

## <a name="sentiment-analysis"></a>情绪分析

# <a name="version-31"></a>[版本 3.1](#tab/version-3-1)

创建一个名为 `sentiment_analysis_example()` 的新函数，该函数采用客户端作为参数，然后调用 `analyze_sentiment()` 函数。 返回的响应对象将包含整个输入文档的情绪标签和分数，以及每个句子的情绪分析。


```python
def sentiment_analysis_example(client):

    documents = ["I had the best day of my life. I wish you were there with me."]
    response = client.analyze_sentiment(documents=documents)[0]
    print("Document Sentiment: {}".format(response.sentiment))
    print("Overall scores: positive={0:.2f}; neutral={1:.2f}; negative={2:.2f} \n".format(
        response.confidence_scores.positive,
        response.confidence_scores.neutral,
        response.confidence_scores.negative,
    ))
    for idx, sentence in enumerate(response.sentences):
        print("Sentence: {}".format(sentence.text))
        print("Sentence {} sentiment: {}".format(idx+1, sentence.sentiment))
        print("Sentence score:\nPositive={0:.2f}\nNeutral={1:.2f}\nNegative={2:.2f}\n".format(
            sentence.confidence_scores.positive,
            sentence.confidence_scores.neutral,
            sentence.confidence_scores.negative,
        ))
          
sentiment_analysis_example(client)
```

### <a name="output"></a>输出

```console
Document Sentiment: positive
Overall scores: positive=1.00; neutral=0.00; negative=0.00 

Sentence: I had the best day of my life.
Sentence 1 sentiment: positive
Sentence score:
Positive=1.00
Neutral=0.00
Negative=0.00

Sentence: I wish you were there with me.
Sentence 2 sentiment: neutral
Sentence score:
Positive=0.21
Neutral=0.77
Negative=0.02
```


# <a name="version-30"></a>[版本 3.0](#tab/version-3)

创建一个名为 `sentiment_analysis_example()` 的新函数，该函数采用客户端作为参数，然后调用 `analyze_sentiment()` 函数。 返回的响应对象将包含整个输入文档的情绪标签和分数，以及每个句子的情绪分析。


```python
def sentiment_analysis_example(client):

    documents = ["I had the best day of my life. I wish you were there with me."]
    response = client.analyze_sentiment(documents = documents)[0]
    print("Document Sentiment: {}".format(response.sentiment))
    print("Overall scores: positive={0:.2f}; neutral={1:.2f}; negative={2:.2f} \n".format(
        response.confidence_scores.positive,
        response.confidence_scores.neutral,
        response.confidence_scores.negative,
    ))
    for idx, sentence in enumerate(response.sentences):
        print("Sentence: {}".format(sentence.text))
        print("Sentence {} sentiment: {}".format(idx+1, sentence.sentiment))
        print("Sentence score:\nPositive={0:.2f}\nNeutral={1:.2f}\nNegative={2:.2f}\n".format(
            sentence.confidence_scores.positive,
            sentence.confidence_scores.neutral,
            sentence.confidence_scores.negative,
        ))
          
sentiment_analysis_example(client)
```

### <a name="output"></a>输出

```console
Document Sentiment: positive
Overall scores: positive=1.00; neutral=0.00; negative=0.00 

Sentence: I had the best day of my life.
Sentence 1 sentiment: positive
Sentence score:
Positive=1.00
Neutral=0.00
Negative=0.00

Sentence: I wish you were there with me.
Sentence 2 sentiment: neutral
Sentence score:
Positive=0.21
Neutral=0.77
Negative=0.02
```

---

## <a name="opinion-mining"></a>观点挖掘

# <a name="version-31"></a>[版本 3.1](#tab/version-3-1)


若要使用观点挖掘进行情绪分析，请创建一个名为 `sentiment_analysis_with_opinion_mining_example()` 的新函数（采用客户端作为参数），然后使用选项标志 `show_opinion_mining=True` 调用 `analyze_sentiment()` 函数。 返回的响应对象不仅包含整个输入文档的情绪标签和分数以及每个句子的情绪分析，还包含角度和观点级情绪分析。


```python
def sentiment_analysis_with_opinion_mining_example(client):

    documents = [
        "The food and service were unacceptable, but the concierge were nice"
    ]

    result = client.analyze_sentiment(documents, show_opinion_mining=True)
    doc_result = [doc for doc in result if not doc.is_error]

    positive_reviews = [doc for doc in doc_result if doc.sentiment == "positive"]
    negative_reviews = [doc for doc in doc_result if doc.sentiment == "negative"]

    positive_mined_opinions = []
    mixed_mined_opinions = []
    negative_mined_opinions = []

    for document in doc_result:
        print("Document Sentiment: {}".format(document.sentiment))
        print("Overall scores: positive={0:.2f}; neutral={1:.2f}; negative={2:.2f} \n".format(
            document.confidence_scores.positive,
            document.confidence_scores.neutral,
            document.confidence_scores.negative,
        ))
        for sentence in document.sentences:
            print("Sentence: {}".format(sentence.text))
            print("Sentence sentiment: {}".format(sentence.sentiment))
            print("Sentence score:\nPositive={0:.2f}\nNeutral={1:.2f}\nNegative={2:.2f}\n".format(
                sentence.confidence_scores.positive,
                sentence.confidence_scores.neutral,
                sentence.confidence_scores.negative,
            ))
            for mined_opinion in sentence.mined_opinions:
                target = mined_opinion.target
                print("......'{}' target '{}'".format(target.sentiment, target.text))
                print("......Target score:\n......Positive={0:.2f}\n......Negative={1:.2f}\n".format(
                    target.confidence_scores.positive,
                    target.confidence_scores.negative,
                ))
                for assessment in mined_opinion.assessments:
                    print("......'{}' assessment '{}'".format(assessment.sentiment, assessment.text))
                    print("......Assessment score:\n......Positive={0:.2f}\n......Negative={1:.2f}\n".format(
                        assessment.confidence_scores.positive,
                        assessment.confidence_scores.negative,
                    ))
            print("\n")
        print("\n")
          
sentiment_analysis_with_opinion_mining_example(client)
```

### <a name="output"></a>输出

```console
Document Sentiment: positive
Overall scores: positive=0.84; neutral=0.00; negative=0.16

Sentence: The food and service were unacceptable, but the concierge were nice
Sentence sentiment: positive
Sentence score:
Positive=0.84
Neutral=0.00
Negative=0.16

......'negative' target 'food'
......Target score:
......Positive=0.01
......Negative=0.99

......'negative' assessment 'unacceptable'
......Assessment score:
......Positive=0.01
......Negative=0.99

......'negative' target 'service'
......Target score:
......Positive=0.01
......Negative=0.99

......'negative' assessment 'unacceptable'
......Assessment score:
......Positive=0.01
......Negative=0.99

......'positive' target 'concierge'
......Target score:
......Positive=1.00
......Negative=0.00

......'positive' assessment 'nice'
......Assessment score:
......Positive=1.00
......Negative=0.00





Press any key to continue . . .

```

# <a name="version-30"></a>[版本 3.0](#tab/version-3)

此功能在版本 3.0 中不可用。

---

## <a name="language-detection"></a>语言检测

# <a name="version-31"></a>[版本 3.1](#tab/version-3-1)

创建一个名为 `language_detection_example()` 的新函数，该函数采用客户端作为参数，然后调用 `detect_language()` 函数。 如果成功，则返回的响应对象将在 `primary_language` 中包含检测到的语言，否则将包含 `error`。

> [!Tip]
> 在某些情况下，可能很难根据输入区分语言。 可以使用 `country_hint` 参数指定 2 个字母的国家/地区代码。 默认情况下，API 使用“US”作为默认的 countryHint，要删除此行为，可以通过将此值设置为空字符串 `country_hint : ""` 来重置此参数。 

```python
def language_detection_example(client):
    try:
        documents = ["Ce document est rédigé en Français."]
        response = client.detect_language(documents = documents, country_hint = 'us')[0]
        print("Language: ", response.primary_language.name)

    except Exception as err:
        print("Encountered exception. {}".format(err))
language_detection_example(client)
```


### <a name="output"></a>输出

```console
Language:  French
```

# <a name="version-30"></a>[版本 3.0](#tab/version-3)

创建一个名为 `language_detection_example()` 的新函数，该函数采用客户端作为参数，然后调用 `detect_language()` 函数。 如果成功，则返回的响应对象将在 `primary_language` 中包含检测到的语言，否则将包含 `error`。

> [!Tip]
> 在某些情况下，可能很难根据输入区分语言。 可以使用 `country_hint` 参数指定 2 个字母的国家/地区代码。 默认情况下，API 使用“US”作为默认的 countryHint，要删除此行为，可以通过将此值设置为空字符串 `country_hint : ""` 来重置此参数。 

```python
def language_detection_example(client):
    try:
        documents = ["Ce document est rédigé en Français."]
        response = client.detect_language(documents = documents, country_hint = 'us')[0]
        print("Language: ", response.primary_language.name)

    except Exception as err:
        print("Encountered exception. {}".format(err))
language_detection_example(client)
```


### <a name="output"></a>输出

```console
Language:  French
```


---

## <a name="named-entity-recognition-ner"></a>命名实体识别 (NER)

# <a name="version-31"></a>[版本 3.1](#tab/version-3-1)

> [!NOTE]
> 在版本 `3.1` 中： 
> * 实体链接是一个独立于 NER 的请求。

创建一个名为 `entity_recognition_example` 的新函数，该函数采用客户端作为参数，然后调用 `recognize_entities()` 函数并循环访问结果。 如果成功，则返回的响应对象将在 `entity` 中包含检测到的实体列表，否则将包含 `error`。 对于检测到的每个实体，输出其类别和子类别（如果存在）。

```python
def entity_recognition_example(client):

    try:
        documents = ["I had a wonderful trip to Seattle last week."]
        result = client.recognize_entities(documents = documents)[0]

        print("Named Entities:\n")
        for entity in result.entities:
            print("\tText: \t", entity.text, "\tCategory: \t", entity.category, "\tSubCategory: \t", entity.subcategory,
                    "\n\tConfidence Score: \t", round(entity.confidence_score, 2), "\tLength: \t", entity.length, "\tOffset: \t", entity.offset, "\n")

    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_recognition_example(client)
```

### <a name="output"></a>输出

```console
Named Entities:

        Text:    trip   Category:        Event  SubCategory:     None
        Confidence Score:        0.61   Length:          4      Offset:          18

        Text:    Seattle        Category:        Location       SubCategory:     GPE
        Confidence Score:        0.82   Length:          7      Offset:          26

        Text:    last week      Category:        DateTime       SubCategory:     DateRange
        Confidence Score:        0.8    Length:          9      Offset:          34
```

## <a name="personally-identifiable-information-pii-recognition"></a>个人身份信息识别 (PII) 识别

创建一个名为 `pii_recognition_example` 的新函数，该函数采用客户端作为参数，然后调用 `recognize_pii_entities()` 函数并循环访问结果。 如果成功，则返回的响应对象将在 `entity` 中包含检测到的实体列表，否则将包含 `error`。 对于检测到的每个实体，输出其类别和子类别（如果存在）。

```python
def pii_recognition_example(client):
    documents = [
        "The employee's SSN is 859-98-0987.",
        "The employee's phone number is 555-555-5555."
    ]
    response = client.recognize_pii_entities(documents, language="en")
    result = [doc for doc in response if not doc.is_error]
    for doc in result:
        print("Redacted Text: {}".format(doc.redacted_text))
        for entity in doc.entities:
            print("Entity: {}".format(entity.text))
            print("\tCategory: {}".format(entity.category))
            print("\tConfidence Score: {}".format(entity.confidence_score))
            print("\tOffset: {}".format(entity.offset))
            print("\tLength: {}".format(entity.length))
pii_recognition_example(client)
```

### <a name="output"></a>输出

```console
Redacted Text: The employee's SSN is ***********.
Entity: 859-98-0987
        Category: U.S. Social Security Number (SSN)
        Confidence Score: 0.65
        Offset: 22
        Length: 11
Redacted Text: The employee's phone number is ************.
Entity: 555-555-5555
        Category: Phone Number
        Confidence Score: 0.8
        Offset: 31
        Length: 12
```

# <a name="version-30"></a>[版本 3.0](#tab/version-3)

创建一个名为 `entity_recognition_example` 的新函数，该函数采用客户端作为参数，然后调用 `recognize_entities()` 函数并循环访问结果。 如果成功，则返回的响应对象将在 `entity` 中包含检测到的实体列表，否则将包含 `error`。 对于检测到的每个实体，输出其类别和子类别（如果存在）。

```python
def entity_recognition_example(client):

    try:
        documents = ["I had a wonderful trip to Seattle last week."]
        result = client.recognize_entities(documents = documents)[0]

        print("Named Entities:\n")
        for entity in result.entities:
            print("\tText: \t", entity.text, "\tCategory: \t", entity.category, "\tSubCategory: \t", entity.subcategory,
                    "\n\tConfidence Score: \t", round(entity.confidence_score, 2), "\n")

    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_recognition_example(client)
```

### <a name="output"></a>输出

```console
Named Entities:

        Text:    trip   Category:        Event  SubCategory:     None
        Confidence Score:        0.61

        Text:    Seattle        Category:        Location       SubCategory:     GPE
        Confidence Score:        0.82

        Text:    last week      Category:        DateTime       SubCategory:     DateRange
        Confidence Score:        0.8
```


---

## <a name="entity-linking"></a>实体链接

# <a name="version-31"></a>[版本 3.1](#tab/version-3-1)

创建一个名为 `entity_linking_example()` 的新函数，该函数采用客户端作为参数，然后调用 `recognize_linked_entities()` 函数并循环访问结果。 如果成功，则返回的响应对象将在 `entities` 中包含检测到的实体列表，否则将包含 `error`。 由于链接实体是唯一标识的，因此同一实体的实例将以分组形式出现在 `entity` 对象下，显示为 `match` 对象的列表。

```python
def entity_linking_example(client):

    try:
        documents = ["""Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, 
        to develop and sell BASIC interpreters for the Altair 8800. 
        During his career at Microsoft, Gates held the positions of chairman,
        chief executive officer, president and chief software architect, 
        while also being the largest individual shareholder until May 2014."""]
        result = client.recognize_linked_entities(documents = documents)[0]

        print("Linked Entities:\n")
        for entity in result.entities:
            print("\tName: ", entity.name, "\tId: ", entity.data_source_entity_id, "\tUrl: ", entity.url,
            "\n\tData Source: ", entity.data_source)
            print("\tMatches:")
            for match in entity.matches:
                print("\t\tText:", match.text)
                print("\t\tConfidence Score: {0:.2f}".format(match.confidence_score))
                print("\t\tOffset: {}".format(match.offset))
                print("\t\tLength: {}".format(match.length))
            
    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_linking_example(client)
```

### <a name="output"></a>输出

```console
Linked Entities:

        Name:  Microsoft        Id:  Microsoft  Url:  https://en.wikipedia.org/wiki/Microsoft
        Data Source:  Wikipedia
        Matches:
                Text: Microsoft
                Confidence Score: 0.55
                Offset: 0
                Length: 9
                Text: Microsoft
                Confidence Score: 0.55
                Offset: 168
                Length: 9
        Name:  Bill Gates       Id:  Bill Gates         Url:  https://en.wikipedia.org/wiki/Bill_Gates
        Data Source:  Wikipedia
        Matches:
                Text: Bill Gates
                Confidence Score: 0.63
                Offset: 25
                Length: 10
                Text: Gates
                Confidence Score: 0.63
                Offset: 179
                Length: 5
        Name:  Paul Allen       Id:  Paul Allen         Url:  https://en.wikipedia.org/wiki/Paul_Allen
        Data Source:  Wikipedia
        Matches:
                Text: Paul Allen
                Confidence Score: 0.60
                Offset: 40
                Length: 10
        Name:  April 4  Id:  April 4    Url:  https://en.wikipedia.org/wiki/April_4
        Data Source:  Wikipedia
        Matches:
                Text: April 4
                Confidence Score: 0.32
                Offset: 54
                Length: 7
        Name:  BASIC    Id:  BASIC      Url:  https://en.wikipedia.org/wiki/BASIC
        Data Source:  Wikipedia
        Matches:
                Text: BASIC
                Confidence Score: 0.33
                Offset: 98
                Length: 5
        Name:  Altair 8800      Id:  Altair 8800        Url:  https://en.wikipedia.org/wiki/Altair_8800
        Data Source:  Wikipedia
        Matches:
                Text: Altair 8800
                Confidence Score: 0.88
                Offset: 125
                Length: 11
```

# <a name="version-30"></a>[版本 3.0](#tab/version-3)

创建一个名为 `entity_linking_example()` 的新函数，该函数采用客户端作为参数，然后调用 `recognize_linked_entities()` 函数并循环访问结果。 如果成功，则返回的响应对象将在 `entities` 中包含检测到的实体列表，否则将包含 `error`。 由于链接实体是唯一标识的，因此同一实体的实例将以分组形式出现在 `entity` 对象下，显示为 `match` 对象的列表。

```python
def entity_linking_example(client):

    try:
        documents = ["""Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, 
        to develop and sell BASIC interpreters for the Altair 8800. 
        During his career at Microsoft, Gates held the positions of chairman,
        chief executive officer, president and chief software architect, 
        while also being the largest individual shareholder until May 2014."""]
        result = client.recognize_linked_entities(documents = documents)[0]

        print("Linked Entities:\n")
        for entity in result.entities:
            print("\tName: ", entity.name, "\tId: ", entity.data_source_entity_id, "\tUrl: ", entity.url,
            "\n\tData Source: ", entity.data_source)
            print("\tMatches:")
            for match in entity.matches:
                print("\t\tText:", match.text)
                print("\t\tConfidence Score: {0:.2f}".format(match.confidence_score))
            
    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_linking_example(client)
```

### <a name="output"></a>输出

```console
Linked Entities:

        Name:  Altair 8800      Id:  Altair 8800        Url:  https://en.wikipedia.org/wiki/Altair_8800
        Data Source:  Wikipedia
        Matches:
                Text: Altair 8800
                Confidence Score: 0.88
        Name:  Bill Gates       Id:  Bill Gates         Url:  https://en.wikipedia.org/wiki/Bill_Gates
        Data Source:  Wikipedia
        Matches:
                Text: Bill Gates
                Confidence Score: 0.63
                Text: Gates
                Confidence Score: 0.63
        Name:  Paul Allen       Id:  Paul Allen         Url:  https://en.wikipedia.org/wiki/Paul_Allen
        Data Source:  Wikipedia
        Matches:
                Text: Paul Allen
                Confidence Score: 0.60
        Name:  Microsoft        Id:  Microsoft  Url:  https://en.wikipedia.org/wiki/Microsoft
        Data Source:  Wikipedia
        Matches:
                Text: Microsoft
                Confidence Score: 0.55
                Text: Microsoft
                Confidence Score: 0.55
        Name:  April 4  Id:  April 4    Url:  https://en.wikipedia.org/wiki/April_4
        Data Source:  Wikipedia
        Matches:
                Text: April 4
                Confidence Score: 0.32
        Name:  BASIC    Id:  BASIC      Url:  https://en.wikipedia.org/wiki/BASIC
        Data Source:  Wikipedia
        Matches:
                Text: BASIC
                Confidence Score: 0.33
```

---

## <a name="key-phrase-extraction"></a>关键短语提取

# <a name="version-31"></a>[版本 3.1](#tab/version-3-1)

创建一个名为 `key_phrase_extraction_example()` 的新函数，该函数采用客户端作为参数，然后调用 `extract_key_phrases()` 函数。 如果成功，结果将包含 `key_phrases` 中检测到的关键短语列表，如果失败，则将包含 `error`。 输出任何检测到的关键短语。

```python
def key_phrase_extraction_example(client):

    try:
        documents = ["My cat might need to see a veterinarian."]

        response = client.extract_key_phrases(documents = documents)[0]

        if not response.is_error:
            print("\tKey Phrases:")
            for phrase in response.key_phrases:
                print("\t\t", phrase)
        else:
            print(response.id, response.error)

    except Exception as err:
        print("Encountered exception. {}".format(err))
        
key_phrase_extraction_example(client)
```


### <a name="output"></a>输出

```console
    Key Phrases:
         cat
         veterinarian
```

# <a name="version-30"></a>[版本 3.0](#tab/version-3)

创建一个名为 `key_phrase_extraction_example()` 的新函数，该函数采用客户端作为参数，然后调用 `extract_key_phrases()` 函数。 如果成功，结果将包含 `key_phrases` 中检测到的关键短语列表，如果失败，则将包含 `error`。 输出任何检测到的关键短语。

```python
def key_phrase_extraction_example(client):

    try:
        documents = ["My cat might need to see a veterinarian."]

        response = client.extract_key_phrases(documents = documents)[0]

        if not response.is_error:
            print("\tKey Phrases:")
            for phrase in response.key_phrases:
                print("\t\t", phrase)
        else:
            print(response.id, response.error)

    except Exception as err:
        print("Encountered exception. {}".format(err))
        
key_phrase_extraction_example(client)
```


### <a name="output"></a>输出

```console
    Key Phrases:
         cat
         veterinarian
```


---

## <a name="extract-health-entities"></a>提取健康状况实体

可以使用文本分析来执行异步请求，以从文本中提取健康状况实体。 下面的示例演示了基本示例。 可以[在 GitHub 上](https://github.com/Azure/azure-sdk-for-python/blob/main/sdk/textanalytics/azure-ai-textanalytics/samples/sample_analyze_healthcare_entities.py)找到更高级的示例。

[!INCLUDE [health operation pricing](../health-operation-pricing-caution.md)]

# <a name="version-31"></a>[版本 3.1](#tab/version-3-1)

``` python
def health_example(client):
    documents = [
        """
        Patient needs to take 50 mg of ibuprofen.
        """
    ]

    poller = client.begin_analyze_healthcare_entities(documents)
    result = poller.result()

    docs = [doc for doc in result if not doc.is_error]

    for idx, doc in enumerate(docs):
        for entity in doc.entities:
            print("Entity: {}".format(entity.text))
            print("...Normalized Text: {}".format(entity.normalized_text))
            print("...Category: {}".format(entity.category))
            print("...Subcategory: {}".format(entity.subcategory))
            print("...Offset: {}".format(entity.offset))
            print("...Confidence score: {}".format(entity.confidence_score))
        for relation in doc.entity_relations:
            print("Relation of type: {} has the following roles".format(relation.relation_type))
            for role in relation.roles:
                print("...Role '{}' with entity '{}'".format(role.name, role.entity.text))
        print("------------------------------------------")
health_example(client)
```

### <a name="output"></a>输出

```console
Entity: 50 mg
...Normalized Text: None
...Category: Dosage
...Subcategory: None
...Offset: 31
...Confidence score: 1.0
Entity: ibuprofen
...Normalized Text: ibuprofen
...Category: MedicationName
...Subcategory: None
...Offset: 40
...Confidence score: 1.0
Relation of type: DosageOfMedication has the following roles
...Role 'Dosage' with entity '50 mg'
...Role 'Medication' with entity 'ibuprofen'
```

# <a name="version-30"></a>[版本 3.0](#tab/version-3)

此功能在版本 3.0 中不可用。

---


## <a name="use-the-api-asynchronously-with-the-analyze-operation"></a>使用“分析”操作异步使用 API

# <a name="version-31"></a>[版本 3.1](#tab/version-3-1)

可以使用“分析”操作执行异步批处理请求实现以下目的：NER、关键短语提取、情绪分析和 PII 检测。 下面的示例演示了有关一个操作的基本示例。 可以[在 GitHub 上](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/textanalytics/azure-ai-textanalytics/samples/sample_analyze_actions.py)找到更高级的示例。

[!INCLUDE [Analyze operation pricing](../analyze-operation-pricing-caution.md)]

创建一个名为 `analyze_batch_example()` 的新函数，该函数采用客户端作为参数，然后调用 `begin_analyze_actions()` 函数。 结果将是一个长期操作，将轮询该操作以获得结果。

```python
from azure.ai.textanalytics import (
    RecognizeEntitiesAction,
    ExtractKeyPhrasesAction
)

def analyze_batch_example(client):
        documents = [
            "Microsoft was founded by Bill Gates and Paul Allen."
        ]

        poller = client.begin_analyze_actions(
            documents,
            display_name="Sample Text Analysis",
            actions=[RecognizeEntitiesAction(), ExtractKeyPhrasesAction()]
        )

        result = poller.result()
        action_results = [action_result for action_result in list(result)]
        first_action_result = action_results[0][0]
        print("Results of Entities Recognition action:")

        for entity in first_action_result.entities:
            print("Entity: {}".format(entity.text))
            print("...Category: {}".format(entity.category))
            print("...Confidence Score: {}".format(entity.confidence_score))
            print("...Offset: {}".format(entity.offset))
            print("...Length: {}".format(entity.length))
        print("------------------------------------------")

        second_action_result = action_results[0][1]
        print("Results of Key Phrase Extraction action:")
        
        for key_phrase in second_action_result.key_phrases:
            print("Key Phrase: {}\n".format(key_phrase))
        print("------------------------------------------")

analyze_batch_example(client)
```

### <a name="output"></a>输出

```console
Results of Entities Recognition action:
Entity: Microsoft
...Category: Organization
...Confidence Score: 1.0
...Offset: 0
...Length: 9
Entity: Bill Gates
...Category: Person
...Confidence Score: 1.0
...Offset: 25
...Length: 10
Entity: Paul Allen
...Category: Person
...Confidence Score: 1.0
...Offset: 40
...Length: 10
------------------------------------------
Results of Key Phrase Extraction action:
Key Phrase: Bill Gates

Key Phrase: Paul Allen

Key Phrase: Microsoft

------------------------------------------
```

# <a name="version-30"></a>[版本 3.0](#tab/version-3)

此功能在版本 3.0 中不可用。

---
