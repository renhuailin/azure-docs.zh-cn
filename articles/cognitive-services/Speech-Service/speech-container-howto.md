---
title: 为语音服务 API 安装并运行 Docker 容器
titleSuffix: Azure Cognitive Services
description: 使用用于语音服务的 Docker 容器在本地执行语音识别、听录、生成等操作。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/01/2021
ms.author: aahi
ms.custom: cog-serv-seo-aug-2020
keywords: 本地, Docker, 容器
ms.openlocfilehash: a1f82384d7191c65293c85bdc3ef70347c6646f5
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124799028"
---
# <a name="install-and-run-docker-containers-for-the-speech-service-apis"></a>为语音服务 API 安装并运行 Docker 容器 

通过容器，你可以在自己的环境中运行一些语音服务 API。 容器非常适合用于满足特定的安全性和数据管理要求。 本文介绍如何下载、安装以及运行语音容器。

通过语音容器，客户能够生成语音应用程序体系结构，该体系结构针对强大的云功能和边缘位置进行了优化。 可以使用多个容器，它们的[定价](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)与基于云的 Azure 语音服务相同。

> [!IMPORTANT]
> 我们已在 2021 年 8 月 31 日停用了标准语音合成语音和文本转语音容器。 请考虑迁移应用程序，以改用神经文本转语音容器。 有关更新应用程序的详细信息，请[查看这些步骤](./text-to-speech.md#migrate-to-neural-voice)。

| 容器 | 功能 | 最晚 | 版本状态 |
|--|--|--|--|
| 语音转文本 | 使用中间结果分析情绪并听录连续实时语音或批量音频录制内容。  | 2.14.0 | 正式版 |
| 自定义语音转文本 | 通过[自定义语音识别门户](https://speech.microsoft.com/customspeech)中的自定义模型，使用中间结果将连续实时语音或批量音频录制内容听录成文本。 | 2.14.0 | 正式版 |
| 文本转语音 | 使用纯文本输入或语音合成标记语言 (SSML) 将文本转换为自然声音。 | 1.15.0 | 正式版 |
| 语音语言识别 | 检测音频文件中讲述的语言。 | 1.3.0 | 预览 |
| 神经文本转语音 | 使用深度神经网络技术将文本转换为自然语音，使合成语音变得更自然。 | 1.9.0 | 正式版 |

## <a name="prerequisites"></a>先决条件

> [!IMPORTANT]
> * 若要使用语音容器，必须提交在线请求并获得批准。 有关详细信息，请参阅下面的“请求批准运行容器”部分。 
> * 正式版容器满足 Microsoft 的稳定性和支持要求。 预览版容器仍在开发中。

使用语音服务容器之前，必须满足以下先决条件。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/cognitive-services/)。

* 在主机上安装 [Docker](https://docs.docker.com/)。 必须将 Docker 配置为允许容器连接 Azure 并向其发送账单数据。 
    * 在 Windows 上，还必须将 Docker 配置为支持 Linux 容器。
    * 应基本了解 [Docker 概念](https://docs.docker.com/get-started/overview/)。 
* 使用免费 (F0) 或标准 (S) [定价层](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)<a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices"  title="创建语音服务资源"  target="_blank">语音服务资源</a>。

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]


## <a name="host-computer-requirements-and-recommendations"></a>主机要求和建议

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>容器要求和建议

下表显示了每个语音容器的最小和建议资源分配。

| 容器 | 最小值 | 建议 |
|-----------|---------|-------------|
| 语音转文本 | 2 核心，2 GB 内存 | 4 核心，4-GB 内存 |
| 自定义语音转文本 | 2 核心，2 GB 内存 | 4 核心，4-GB 内存 |
| 文本转语音 | 单核，2-GB 内存 | 2 核心，3-GB 内存 |
| 语音语言识别 | 1 核心，1-GB 内存 | 1 核心，1-GB 内存 |
| 神经文本转语音 | 6 核心，12-GB 内存 | 8 核心，16-GB 内存 |

* 每个核心必须至少为 2.6 千兆赫 (GHz) 或更快。

核心和内存对应于 `--cpus` 和 `--memory` 设置，用作 `docker run` 命令的一部分。

> [!NOTE]
> 最小和建议值基于 Docker 限制，而不是基于主机资源。 例如，大型语言模型的文本转语音容器内存映射部分；建议将整个文件装入内存，这需要额外的 4-6 GB。 另外，由于模型要在内存中分页，因此首次运行任一容器可能需要更长的时间。

### <a name="advanced-vector-extension-support"></a>高级矢量扩展支持

主机是运行 docker 容器的计算机。 主机必须支持[高级矢量扩展](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) (AVX2)。 可使用以下命令检查 Linux 主机是否提供 AVX2 支持：

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```
> [!WARNING]
> 需要主计算机来支持 AVX2。 如果没有 AVX2 支持，容器将无法正常运行。

## <a name="request-approval-to-the-run-the-container"></a>请求批准运行容器

填写并提交[请求表单](https://aka.ms/csgate)以请求访问容器。 

[!INCLUDE [Request access to public preview](../../../includes/cognitive-services-containers-request-access.md)]


## <a name="get-the-container-image-with-docker-pull"></a>使用 `docker pull` 获取容器映像

以下容器注册表中提供了语音服务的容器映像。

# <a name="speech-to-text"></a>[语音转文本](#tab/stt)

| 容器 | 存储库 |
|-----------|------------|
| 语音转文本 | `mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text:latest` |

# <a name="custom-speech-to-text"></a>[自定义语音转文本](#tab/cstt)

| 容器 | 存储库 |
|-----------|------------|
| 自定义语音转文本 | `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text:latest` |

# <a name="text-to-speech"></a>[文本转语音](#tab/tts)

| 容器 | 存储库 |
|-----------|------------|
| 文本转语音 | `mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech:latest` |

# <a name="neural-text-to-speech"></a>[神经文本转语音](#tab/ntts)

| 容器 | 存储库 |
|-----------|------------|
| 神经文本转语音 | `mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech:latest` |

# <a name="speech-language-identification"></a>[语音语言识别](#tab/lid)

> [!TIP]
> 为了获取最有用的结果，我们建议将语音语言识别容器与语音转文本或自定义语音转文本容器配合使用。 

| 容器 | 存储库 |
|-----------|------------|
| 语音语言识别 | `mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection:latest` |

***

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-speech-containers"></a>语音容器的 Docker pull

# <a name="speech-to-text"></a>[语音转文本](#tab/stt)

#### <a name="docker-pull-for-the-speech-to-text-container"></a>语音转文本容器的 Docker pull

使用 [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) 命令从 Microsoft 容器注册表下载容器映像。

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text:latest
```

> [!IMPORTANT]
> `latest` 标记拉取 `en-US` 区域设置。 有关其他区域设置，请参阅[语音转文本区域设置](#speech-to-text-locales)。

#### <a name="speech-to-text-locales"></a>语音转文本区域设置

除 `latest` 以外的所有标记均采用以下格式并区分大小写：

```
<major>.<minor>.<patch>-<platform>-<locale>-<prerelease>
```

以下标记是格式示例：

```
2.6.0-amd64-en-us
```

有关 **语音转文本** 容器支持的所有区域设置，请参阅 [语音转文本映像标记](../containers/container-image-tags.md#speech-to-text)。

# <a name="custom-speech-to-text"></a>[自定义语音转文本](#tab/cstt)

#### <a name="docker-pull-for-the-custom-speech-to-text-container"></a>自定义语音转文本容器的 Docker pull

使用 [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) 命令从 Microsoft 容器注册表下载容器映像。

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text:latest
```

> [!NOTE]
> 自定义语音识别容器的 `locale` 和 `voice` 由容器引入的自定义模型确定。

# <a name="text-to-speech"></a>[文本转语音](#tab/tts)

#### <a name="docker-pull-for-the-text-to-speech-container"></a>文本转语音容器的 Docker pull

使用 [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) 命令从 Microsoft 容器注册表下载容器映像。

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech:latest
```

> [!IMPORTANT]
> `latest` 标记提取 `en-US` 区域设置和 `ariarus` 语音。 有关其他区域设置，请参阅[文本转语音区域设置](#text-to-speech-locales)。

#### <a name="text-to-speech-locales"></a>文本转语音区域设置

除 `latest` 以外的所有标记均采用以下格式并区分大小写：

```
<major>.<minor>.<patch>-<platform>-<locale>-<voice>-<prerelease>
```

以下标记是格式示例：

```
1.8.0-amd64-en-us-ariarus
```

有关 **文本转语音** 容器支持的所有区域设置和对应语音，请参阅 [文本转语音映像标记](../containers/container-image-tags.md#text-to-speech)。

> [!IMPORTANT]
> 构造文本转语音 HTTP POST 时，[语音合成标记语言 (SSML)](speech-synthesis-markup.md) 消息需要一个具有 `name` 特性的 `voice` 元素。 值是对应的容器区域设置和语音，也称为[“短名称”](language-support.md#standard-voices)。 例如，`latest` 标记具有语音名称 `en-US-AriaRUS`。

# <a name="neural-text-to-speech"></a>[神经文本转语音](#tab/ntts)

#### <a name="docker-pull-for-the-neural-text-to-speech-container"></a>神经文本转语音容器的 Docker pull

使用 [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) 命令从 Microsoft 容器注册表下载容器映像。

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech:latest
```

> [!IMPORTANT]
> `latest` 标记提取 `en-US` 区域设置和 `arianeural` 语音。 有关其他区域设置，请参阅[神经文本转语音区域设置](#neural-text-to-speech-locales)。

#### <a name="neural-text-to-speech-locales"></a>神经文本转语音区域设置

除 `latest` 以外的所有标记均采用以下格式并区分大小写：

```
<major>.<minor>.<patch>-<platform>-<locale>-<voice>
```

以下标记是格式示例：

```
1.3.0-amd64-en-us-arianeural
```

有关 **神经文本转语音** 容器支持的所有区域设置和对应语音，请参阅 [神经文本转语音映像标记](../containers/container-image-tags.md#neural-text-to-speech)。

> [!IMPORTANT]
> 构造神经文本转语音 HTTP POST 时，[语音合成标记语言 (SSML)](speech-synthesis-markup.md) 消息需要一个具有 `name` 特性的 `voice` 元素。 值是对应的容器区域设置和语音，也称为[“短名称”](language-support.md#neural-voices)。 例如，`latest` 标记具有语音名称 `en-US-AriaNeural`。

# <a name="speech-language-identification"></a>[语音语言识别](#tab/lid)

#### <a name="docker-pull-for-the-speech-language-identification-container"></a>语音语言识别容器的 Docker pull

使用 [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) 命令从 Microsoft 容器注册表下载容器映像。

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection:latest
```

***

## <a name="how-to-use-the-container"></a>如何使用容器

一旦容器位于[主计算机](#host-computer-requirements-and-recommendations)上，请通过以下过程使用容器。

1. 使用所需的计费设置[运行容器](#run-the-container-with-docker-run)。 提供 `docker run` 命令的多个[示例](speech-container-configuration.md#example-docker-run-commands)。
1. [查询容器的预测终结点](#query-the-containers-prediction-endpoint)。

## <a name="run-the-container-with-docker-run"></a>通过 `docker run` 运行容器

使用 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令运行容器。 有关如何获取 `{Endpoint_URI}` 和 `{API_Key}` 值的详细信息，请参阅[收集所需的参数](#gathering-required-parameters)。 我们还提供了 `docker run` 命令的其他[示例](speech-container-configuration.md#example-docker-run-commands)。

# <a name="speech-to-text"></a>[语音转文本](#tab/stt)

若要运行标准语音转文本容器，请执行以下 `docker run` 命令。

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

此命令：

* 运行容器映像中的某个语音转文本容器。
* 分配 4 个 CPU 核心和 4 千兆字节 (GB) 内存。
* 公开 TCP 端口 5000，并为容器分配伪 TTY。
* 退出后自动删除容器。 容器映像在主计算机上仍然可用。

> [!NOTE]
> 容器支持使用 GStreamer 为语音 SDK 提供压缩音频输入。
> 若要在容器中安装 GStreamer，请按照[在语音 SDK 中使用编解码器压缩的音频输入](how-to-use-codec-compressed-audio-input-streams.md)中适用于 GStreamer 的 Linux 说明操作。

#### <a name="diarization-on-the-speech-to-text-output"></a>对语音转文本输出进行分割聚类
默认已启用分割聚类。 若要在响应中获取分割聚类，请使用 `diarize_speech_config.set_service_property`。

1. 将短语输出格式设置为 `Detailed`。
2. 设置分割聚类的模式。 支持的模式为 `Identity` 和 `Anonymous`。
```python
diarize_speech_config.set_service_property(
    name='speechcontext-PhraseOutput.Format',
    value='Detailed',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)

diarize_speech_config.set_service_property(
    name='speechcontext-phraseDetection.speakerDiarization.mode',
    value='Identity',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```
> [!NOTE]
> “标识”模式返回 `"SpeakerId": "Customer"` 或 `"SpeakerId": "Agent"`。
> “匿名”模式返回 `"SpeakerId": "Speaker 1"` 或 `"SpeakerId": "Speaker 2"`


#### <a name="analyze-sentiment-on-the-speech-to-text-output"></a>分析语音转文本输出中的情绪 
从语音转文本容器 v2.6.0 开始，应使用 TextAnalytics 3.0 API 终结点，而不要使用预览版。 例如
* `https://westus2.api.cognitive.microsoft.com/text/analytics/v3.0/sentiment`
* `https://localhost:5000/text/analytics/v3.0/sentiment`

> [!NOTE]
> 文本分析 `v3.0` API 不与文本分析 `v3.0-preview.1` 后向兼容。 若要获得最新的情绪功能支持，请使用语音转文本容器映像 `v2.6.0` 和文本分析 `v3.0`。

从语音转文本容器 v2.2.0 开始，可以对输出调用[情绪分析 v3 API](../text-analytics/how-tos/text-analytics-how-to-sentiment-analysis.md)。 若要调用情绪分析，需要一个文本分析 API 资源终结点。 例如： 
* `https://westus2.api.cognitive.microsoft.com/text/analytics/v3.0-preview.1/sentiment`
* `https://localhost:5000/text/analytics/v3.0-preview.1/sentiment`

如果你要访问云中的文本分析终结点，需要一个密钥。 如果你在本地运行文本分析，则可能不需要提供此密钥。

密钥和终结点将作为自变量传递到语音容器，如以下示例中所示。

```bash
docker run -it --rm -p 5000:5000 \
mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text:latest \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
CloudAI:SentimentAnalysisSettings:TextAnalyticsHost={TEXT_ANALYTICS_HOST} \
CloudAI:SentimentAnalysisSettings:SentimentAnalysisApiKey={SENTIMENT_APIKEY}
```

此命令：

* 执行的步骤与前面的命令相同。
* 存储文本分析 API 终结点和密钥用于发送情绪分析请求。 

#### <a name="phraselist-v2-on-the-speech-to-text-output"></a>语音转文本输出中的短语列表 v2 

从语音转文本容器 v2.6.0 开始，你可以获取包含你自己的短语（整个句子或中间的短语）的输出。 例如，获取以下句子中的 *the tall man*：

* “This is a sentence **the tall man** this is another sentence.”

若要配置短语列表，需要在发出调用时添加你自己的短语。 例如：

```python
    phrase="the tall man"
    recognizer = speechsdk.SpeechRecognizer(
        speech_config=dict_speech_config,
        audio_config=audio_config)
    phrase_list_grammer = speechsdk.PhraseListGrammar.from_recognizer(recognizer)
    phrase_list_grammer.addPhrase(phrase)
    
    dict_speech_config.set_service_property(
        name='setflight',
        value='xonlineinterp',
        channel=speechsdk.ServicePropertyChannel.UriQueryParameter
    )
```

如果你要添加多个短语，请对每个短语调用 `.addPhrase()` 以将其添加到短语列表。 


# <a name="custom-speech-to-text"></a>[自定义语音转文本](#tab/cstt)

自定义语音转文本容器依赖于自定义语音识别模型。 必须已使用[自定义语音识别门户](https://speech.microsoft.com/customspeech)[训练](how-to-custom-speech-train-model.md)了该自定义模型。

必须使用自定义语音识别 **模型 ID** 来运行容器。 可在自定义语音识别门户的“训练”页上找到此 ID。 在自定义语音识别门户中，导航到“训练”页并选择模型。
<br>

![自定义语音识别训练页](media/custom-speech/custom-speech-model-training.png)

获取 **模型 ID**，用作 `docker run` 命令的 `ModelId` 参数的自变量。
<br>

![自定义语音识别模型详细信息](media/custom-speech/custom-speech-model-details.png)

下表列出了各个 `docker run` 参数及其对应的说明：

| 参数 | 说明 |
|---------|---------|
| `{VOLUME_MOUNT}` | 主计算机的[卷装载点](https://docs.docker.com/storage/volumes/)，Docker 使用它来持久保存自定义模型。 例如 *C:\CustomSpeech*，其中的 C 驱动器位于主机上。 |
| `{MODEL_ID}` | 自定义语音识别门户的“训练”页中的自定义语音识别 **模型 ID**。 |
| `{ENDPOINT_URI}` | 必须使用该终结点进行计量和计费。 有关详细信息，请参阅[收集必需的参数](#gathering-required-parameters)。 |
| `{API_KEY}` | API 密钥是必需的。 有关详细信息，请参阅[收集必需的参数](#gathering-required-parameters)。 |

若要运行自定义语音转文本容器，请执行以下 `docker run` 命令：

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

此命令：

* 运行容器映像中的某个自定义语音转文本容器。
* 分配 4 个 CPU 核心和 4 千兆字节 (GB) 内存。
* 从卷输入装载点（例如 *C:\CustomSpeech*）加载自定义语音转文本模型。
* 公开 TCP 端口 5000，并为容器分配伪 TTY。
* 根据给定的 `ModelId` 来下载模型（如果在卷装载中找不到）。
* 如果先前已下载自定义模型，则会忽略 `ModelId`。
* 退出后自动删除容器。 容器映像在主计算机上仍然可用。


#### <a name="base-model-download-on-the-custom-speech-to-text-container"></a>自定义语音转文本容器上的基础模型下载  
从自定义语音转文本容器 v2.6.0 开始，你可以使用选项 `BaseModelLocale=<locale>` 获取可用的基础模型信息。 此选项将提供你的计费帐户下该区域设置中的可用基础模型列表。 例如：

```bash
docker run --rm -it \
mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text \
BaseModelLocale={LOCALE} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

此命令：

* 运行容器映像中的某个自定义语音转文本容器。
* 检查并返回目标区域设置的可用基础模型。

输出将提供基础模型列表，其中包含区域设置、模型 ID 和创建日期时间等信息。 你可以使用模型 ID 来下载并使用你喜欢的特定基础模型。 例如：
```
Checking available base model for en-us
2020/10/30 21:54:20 [Info] Searching available base models for en-us
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2016-11-04T08:23:42Z, Id: a3d8aab9-6f36-44cd-9904-b37389ce2bfa
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2016-11-04T12:01:02Z, Id: cc7826ac-5355-471d-9bc6-a54673d06e45
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2017-08-17T12:00:00Z, Id: a1f8db59-40ff-4f0e-b011-37629c3a1a53
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2018-04-16T11:55:00Z, Id: c7a69da3-27de-4a4b-ab75-b6716f6321e5
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2018-09-21T15:18:43Z, Id: da494a53-0dad-4158-b15f-8f9daca7a412
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2018-10-19T11:28:54Z, Id: 84ec130b-d047-44bf-a46d-58c1ac292ca7
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2018-11-26T07:59:09Z, Id: ee5c100f-152f-4ae5-9e9d-014af3c01c56
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2018-11-26T09:21:55Z, Id: d04959a6-71da-4913-9997-836793e3c115
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2019-01-11T10:04:19Z, Id: 488e5f23-8bc5-46f8-9ad8-ea9a49a8efda
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2019-02-18T14:37:57Z, Id: 0207b3e6-92a8-4363-8c0e-361114cdd719
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2019-03-03T17:34:10Z, Id: 198d9b79-2950-4609-b6ec-f52254074a05
2020/10/30 21:54:21 [Fatal] Please run this tool again and assign --modelId '<one above base model id>'. If no model id listed above, it means currently there is no available base model for en-us
```

#### <a name="custom-pronunciation-on-the-custom-speech-to-text-container"></a>自定义语音转文本容器上的自定义发音 
从自定义语音转文本容器 v2.5.0 开始，你可以在输出中获取自定义发音结果。 你只需在自定义模型中设置自己的自定义发音规则，然后将该模型装载到自定义语音转文本容器即可。


# <a name="text-to-speech"></a>[文本转语音](#tab/tts)

若要运行标准文本转语音容器，请执行以下 `docker run` 命令。

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

此命令：

* 运行容器映像中的某个标准文本转语音容器。
* 分配 1 个 CPU 核心和 2 千兆字节 (GB) 内存。
* 公开 TCP 端口 5000，并为容器分配伪 TTY。
* 退出后自动删除容器。 容器映像在主计算机上仍然可用。

# <a name="neural-text-to-speech"></a>[神经文本转语音](#tab/ntts)

若要运行神经文本转语音容器，请执行以下 `docker run` 命令。

```bash
docker run --rm -it -p 5000:5000 --memory 12g --cpus 6 \
mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

此命令：

* 运行容器映像中的某个神经文本转语音容器。
* 分配 6 个 CPU 核心和 12 千兆字节 (GB) 内存。
* 公开 TCP 端口 5000，并为容器分配伪 TTY。
* 退出后自动删除容器。 容器映像在主计算机上仍然可用。

# <a name="speech-language-identification"></a>[语音语言识别](#tab/lid)

若要运行语音语言识别容器，请执行以下 `docker run` 命令。

```bash
docker run --rm -it -p 5003:5003 --memory 1g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

此命令： 

* 运行容器映像中的某个语音语言检测容器。 目前，运行此映像无需付费。
* 分配 1 个 CPU 核心和 1 千兆字节 (GB) 内存。
* 公开 TCP 端口 5003，并为容器分配伪 TTY。
* 退出后自动删除容器。 容器映像在主计算机上仍然可用。

如果你要结合语音转文本容器运行此容器，可以使用此 [Docker 映像](https://hub.docker.com/r/antsu/on-prem-client)。 启动这两个容器后，使用此 Docker Run 命令执行 `speech-to-text-with-languagedetection-client`。

```Docker
docker run --rm -v ${HOME}:/root -ti antsu/on-prem-client:latest ./speech-to-text-with-languagedetection-client ./audio/LanguageDetection_en-us.wav --host localhost --lport 5003 --sport 5000
```

> [!NOTE]
> 增加并发调用数可能会影响可靠性和延迟。 要进行语言识别，我们建议使用 1 个 CPU 和 1GB 内存最多发出 4 个并发调用。 对于具有 2 个 CPU 和 2GB 内存的主机，建议最多发出 6 个并发调用。

***

> [!IMPORTANT]
> 必须指定 `Eula`、`Billing` 和 `ApiKey` 选项运行容器；否则，该容器不会启动。  有关详细信息，请参阅[计费](#billing)。

## <a name="query-the-containers-prediction-endpoint"></a>查询容器的预测终结点

> [!NOTE]
> 如果你要运行多个容器，请使用唯一端口号。

| 容器 | SDK 主机 URL | 协议 |
|--|--|--|
| 标准语音转文本和自定义语音转文本 | `ws://localhost:5000` | WS |
| 文本转语音（包括标准和神经）、语音语言识别 | `http://localhost:5000` | HTTP |

有关使用 WSS 和 HTTPS 协议的详细信息，请参阅[容器安全性](../cognitive-services-container-support.md#azure-cognitive-services-container-security)。

### <a name="speech-to-text-standard-and-custom"></a>语音转文本（标准和自定义）

[!INCLUDE [Query Speech-to-text container endpoint](includes/speech-to-text-container-query-endpoint.md)]

#### <a name="analyze-sentiment"></a>分析情绪

如果[向容器](#analyze-sentiment-on-the-speech-to-text-output)提供了文本分析 API 凭据，则可以使用语音 SDK 通过情绪分析发送语音识别请求。 可将 API 响应配置为使用简单或详细格式。 
> [!NOTE]
> 现已确定语音服务 Python SDK v1.13 中的情绪分析存在一个问题。 如果你使用的是语音服务 Python SDK 中的情绪分析，请使用 v1.12.x 或更低版本。

# <a name="simple-format"></a>[简单格式](#tab/simple-format)

若要将语音客户端配置为使用简单格式，请添加 `"Sentiment"` 作为 `Simple.Extensions` 的值。 如果你要选择特定的文本分析模型版本，请替换 `speechcontext-phraseDetection.sentimentAnalysis.modelversion` 属性配置中的 `'latest'`。

```python
speech_config.set_service_property(
    name='speechcontext-PhraseOutput.Simple.Extensions',
    value='["Sentiment"]',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
speech_config.set_service_property(
    name='speechcontext-phraseDetection.sentimentAnalysis.modelversion',
    value='latest',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

`Simple.Extensions` 在响应的根层中返回情绪分析结果。

```json
{
   "DisplayText":"What's the weather like?",
   "Duration":13000000,
   "Id":"6098574b79434bd4849fee7e0a50f22e",
   "Offset":4700000,
   "RecognitionStatus":"Success",
   "Sentiment":{
      "Negative":0.03,
      "Neutral":0.79,
      "Positive":0.18
   }
}
```

# <a name="detailed-format"></a>[详细格式](#tab/detailed-format)

若要将语音客户端配置为使用详细格式，请添加 `"Sentiment"` 作为 `Detailed.Extensions` 和/或 `Detailed.Options` 的值。 如果你要选择特定的文本分析模型版本，请替换 `speechcontext-phraseDetection.sentimentAnalysis.modelversion` 属性配置中的 `'latest'`。

```python
speech_config.set_service_property(
    name='speechcontext-PhraseOutput.Detailed.Options',
    value='["Sentiment"]',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
speech_config.set_service_property(
    name='speechcontext-PhraseOutput.Detailed.Extensions',
    value='["Sentiment"]',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
speech_config.set_service_property(
    name='speechcontext-phraseDetection.sentimentAnalysis.modelversion',
    value='latest',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

`Detailed.Extensions` 在响应的根层中提供情绪分析结果。 `Detailed.Options` 在响应的 `NBest` 层中提供结果。 这两个选项可以单独使用，也可以一起使用。

```json
{
   "DisplayText":"What's the weather like?",
   "Duration":13000000,
   "Id":"6a2aac009b9743d8a47794f3e81f7963",
   "NBest":[
      {
         "Confidence":0.973695,
         "Display":"What's the weather like?",
         "ITN":"what's the weather like",
         "Lexical":"what's the weather like",
         "MaskedITN":"What's the weather like",
         "Sentiment":{
            "Negative":0.03,
            "Neutral":0.79,
            "Positive":0.18
         }
      },
      {
         "Confidence":0.9164971,
         "Display":"What is the weather like?",
         "ITN":"what is the weather like",
         "Lexical":"what is the weather like",
         "MaskedITN":"What is the weather like",
         "Sentiment":{
            "Negative":0.02,
            "Neutral":0.88,
            "Positive":0.1
         }
      }
   ],
   "Offset":4700000,
   "RecognitionStatus":"Success",
   "Sentiment":{
      "Negative":0.03,
      "Neutral":0.79,
      "Positive":0.18
   }
}
```

---

如果你要完全禁用情绪分析，请将 `false` 值添加到 `sentimentanalysis.enabled`。

```python
speech_config.set_service_property(
    name='speechcontext-phraseDetection.sentimentanalysis.enabled',
    value='false',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

### <a name="text-to-speech-standard-and-neural"></a>文本转语音（标准和神经）

[!INCLUDE [Query Text-to-speech container endpoint](includes/text-to-speech-container-query-endpoint.md)]

### <a name="run-multiple-containers-on-the-same-host"></a>在同一主机上运行多个容器

若要使用公开端口运行多个容器，请确保在运行每个容器时使用不同的公开端口。 例如，在端口 5000 上运行第一个容器，在端口 5001 上运行第二个容器。

可以让此容器和其他 Azure 认知服务容器一起运行在该主机上。 此外，还可以让同一认知服务容器的多个容器一起运行。

[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>停止容器

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>故障排除

在启动或运行容器时，你可能会遇到问题。 请使用输出[装载点](speech-container-configuration.md#mount-settings)并启用日志记录。 这可以让容器生成有助于排查问题的日志文件。

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>计费

语音容器使用 Azure 帐户中的“语音”资源向 Azure 发送计费信息。

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

有关这些选项的详细信息，请参阅[配置容器](speech-container-configuration.md)。

## <a name="summary"></a>摘要

本文已介绍语音容器的概念，及其下载、安装和运行工作流。 综上所述：

* 语音服务为 Docker 提供四个 Linux 容器，其中封装了各种功能：
  * *语音转文本*
  * *自定义语音转文本*
  * *文本转语音*
  * *自定义文本转语音*
  * *神经文本转语音*
  * *语音语言识别*
* 可以从 Azure 中的容器注册表下载容器映像。
* 容器映像在 Docker 中运行。
* 无论是使用 REST API（仅适用于文本转语音）还是 SDK（语音转文本或文本转语音），都要指定容器的主机 URI。 
* 实例化容器时，必须提供计费信息。

> [!IMPORTANT]
>  如果未连接到 Azure 进行计量，则无法授权并运行认知服务容器。 客户需要始终让容器向计量服务传送账单信息。 认知服务容器不会将客户数据（例如，正在分析的图像或文本）发送给 Microsoft。

## <a name="next-steps"></a>后续步骤

* 查看[配置容器](speech-container-configuration.md)了解配置设置。
* 了解如何[将语音服务容器与 Kubernetes 和 Helm 配合使用](speech-container-howto-on-premises.md)
* 使用其他[认知服务容器](../cognitive-services-container-support.md)