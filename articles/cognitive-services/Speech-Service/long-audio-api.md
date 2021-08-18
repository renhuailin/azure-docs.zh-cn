---
title: 长音频 API - 语音服务
titleSuffix: Azure Cognitive Services
description: 了解长音频 API 是如何为长格式文本转语音的异步合成而设计的。
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/11/2020
ms.author: lajanuar
ms.openlocfilehash: 62546d4f7a1cd93d787faee22132d967bb0def8d
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122067062"
---
# <a name="long-audio-api"></a>长音频 API

长音频 API 可为长格式文本转语音（例如有声读物、新闻文章和文档）提供异步合成音频。 此 API 不能实时返回合成音频。 你需要轮询响应，并将输出用作提供音频的服务。 与语音 SDK 使用的文本转语音 API 不同，长音频 API 可创建超过 10 分钟的合成音频， 因此成为发行商和音频内容平台批量创建有声读物等长音频内容的理想选择。

长音频 API 的更多好处：

* 服务返回的合成语音使用最佳的神经网络语音。
* 无需部署语音终结点。

> [!NOTE]
> 长音频 API 支持[公共神经语音](./language-support.md#neural-voices)和[自定义神经语音](./how-to-custom-voice.md)。

## <a name="workflow"></a>工作流

使用长音频 API 时，通常需提交要合成的文本文件，轮询状态，并在状态指示成功时下载音频输出。

此图高度概括了该工作流。

![长音频 API 工作流关系图](media/long-audio-api/long-audio-api-workflow.png)

## <a name="prepare-content-for-synthesis"></a>为合成准备内容

准备文本文件时，请确保：

* 文件是纯文本 (.txt) 或 SSML 文本 (.txt)。
* 文件已编码为[包含字节顺序标记 (BOM) 的 UTF-8 格式](https://www.w3.org/International/questions/qa-utf8-bom.en#bom)。
* 文件是单个文件，而不是 zip 文件。
* 文件包含 400 多个字符（对于纯文本），或 400 个[可计费字符](./text-to-speech.md#pricing-note)（对于 SSML 文本），并且少于 10,000 个段落。
  * 对于纯文本，通过点击 Enter/Return 来分隔每个段落。 请参阅[纯文本输入示例](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/en-US.txt)。
  * 对于 SSML 文本，每个 SSML 部分都被视为一个段落。 按不同段落分隔 SSML 部分。 请参阅 [SSML 文本输入示例](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/SSMLTextInputSample.txt)。

## <a name="sample-code"></a>示例代码

本页的其余部分将重点介绍 Python，但长音频 API 的示例代码可在 GitHub 上提供以下编程语言：

* [示例代码：Python](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/Python)
* [示例代码：C#](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/CSharp)
* [示例代码：Java](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/)

## <a name="python-example"></a>Python 示例

本部分包含的 Python 示例演示了长音频 API 的基本用法。 使用最喜欢的 IDE 或编辑器创建新的 Python 项目。 然后将以下代码片段复制到名为 `long_audio_synthesis_client.py` 的文件中。

```python
import json
import ntpath
import requests
```

这些库用于构造 HTTP 请求以及调用文本转语音长音频合成 REST API。

### <a name="get-a-list-of-supported-voices"></a>获取受支持语音列表

若要获取支持的语音列表，请将 GET 请求发送到 `https://<endpoint>/api/texttospeech/v3.0/longaudiosynthesis/voices`。

此代码可获取用于特定区域/终结点的完整语音列表。

```python
def get_voices():
    region = '<region>'
    key = '<your_key>'
    url = 'https://{}.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0/longaudiosynthesis/voices'.format(region)
    header = {
        'Ocp-Apim-Subscription-Key': key
    }

    response = requests.get(url, headers=header)
    print(response.text)

get_voices()
```

请替换以下值：

* 将 `<your_key>` 替换为语音服务订阅密钥。 [Azure 门户](https://aka.ms/azureportal)中资源的“概述”选项卡内提供了此信息。
* 将 `<region>` 替换为创建语音资源的区域（例如：`eastus` 或 `westus`）。 [Azure 门户](https://aka.ms/azureportal)中资源的“概述”选项卡内提供了此信息。

输出如下：

```json
{
  "values": [
    {
      "locale": "en-US",
      "voiceName": "en-US-AriaNeural",
      "description": "",
      "gender": "Female",
      "createdDateTime": "2020-05-21T05:57:39.123Z",
      "properties": {
        "publicAvailable": true
      }
    },
    {
      "id": "8fafd8cd-5f95-4a27-a0ce-59260f873141"
      "locale": "en-US",
      "voiceName": "my custom neural voice",
      "description": "",
      "gender": "Male",
      "createdDateTime": "2020-05-21T05:25:40.243Z",
      "properties": {
        "publicAvailable": false
      }
    }
  ]
}
```

如果 properties.publicAvailable 为 true，则语音是公共神经语音。 否则，它是自定义神经语音。

### <a name="convert-text-to-speech"></a>将文本转换为语音

在纯文本或 SSML 文本中准备输入文本文件，然后将以下代码添加到 `long_audio_synthesis_client.py`：

> [!NOTE]
> `concatenateResult` 是一个可选参数。 如果未设置此参数，则将按段落生成音频输出。 你还可以通过加入该参数，将音频连接成一个输出。 
> `outputFormat` 也是可选的。 默认情况下，音频输出设置为 `riff-16khz-16bit-mono-pcm`。 有关支持的音频输出格式的详细信息，请参阅[音频输出格式](#audio-output-formats)。

```python
def submit_synthesis():
    region = '<region>'
    key = '<your_key>'
    input_file_path = '<input_file_path>'
    locale = '<locale>'
    url = 'https://{}.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0/longaudiosynthesis'.format(region)
    header = {
        'Ocp-Apim-Subscription-Key': key
    }

    voice_identities = [
        {
            'voicename': '<voice_name>'
        }
    ]

    payload = {
        'displayname': 'long audio synthesis sample',
        'description': 'sample description',
        'locale': locale,
        'voices': json.dumps(voice_identities),
        'outputformat': 'riff-16khz-16bit-mono-pcm',
        'concatenateresult': True,
    }

    filename = ntpath.basename(input_file_path)
    files = {
        'script': (filename, open(input_file_path, 'rb'), 'text/plain')
    }

    response = requests.post(url, payload, headers=header, files=files)
    print('response.status_code: %d' % response.status_code)
    print(response.headers['Location'])

submit_synthesis()
```

请替换以下值：

* 将 `<your_key>` 替换为语音服务订阅密钥。 [Azure 门户](https://aka.ms/azureportal)中资源的“概述”选项卡内提供了此信息。
* 将 `<region>` 替换为创建语音资源的区域（例如：`eastus` 或 `westus`）。 [Azure 门户](https://aka.ms/azureportal)中资源的“概述”选项卡内提供了此信息。
* 将 `<input_file_path>` 替换为准备进行文本转语音的文本文件的路径。
* 将 `<locale>` 替换为所需的输出区域设置。 有关详细信息，请参阅[语言支持](language-support.md#neural-voices)。

使用之前调用 `/voices` 终结点所返回的其中一个声音。

* 如果使用的是公共神经语音，请将 `<voice_name>` 替换为所需的输出语音。
* 若要使用自定义神经语音，请将 `voice_identities` 变量替换为以下内容，并将 `<voice_id>` 替换为自定义神经语音的 `id`。
```Python
voice_identities = [
    {
        'id': '<voice_id>'
    }
]
```

输出如下：

```console
response.status_code: 202
https://<endpoint>/api/texttospeech/v3.0/longaudiosynthesis/<guid>
```

> [!NOTE]
> 如果有多个输入文件，则需要提交多个请求，并且需要考虑多项限制。
> * 对于每个 Azure 订阅帐户，客户端每秒最多可提交 5 个请求。 如果超出限制，服务器会返回 429 错误代码（请求过多）。 通过降低提交率可避免超出此限制。
> * 对于每个 Azure 订阅帐户，服务器最多可将 120 个请求排入队列。 如果队列超出此限制，服务器将返回 429 错误代码（请求过多）。 在提交其他请求之前，需等待服务器处理完排队的请求。

可以使用输出中的 URL 获取请求状态。

### <a name="get-details-about-a-submitted-request"></a>获取有关已提交请求的详细信息

若要获取已提交合成请求的状态，请将 GET 请求发送到上一步所返回的 URL 中。

```Python

def get_synthesis():
    url = '<url>'
    key = '<your_key>'
    header = {
        'Ocp-Apim-Subscription-Key': key
    }
    response = requests.get(url, headers=header)
    print(response.text)

get_synthesis()
```

输出如下所示：

```json
response.status_code: 200
{
  "models": [
    {
      "voiceName": "en-US-AriaNeural"
    }
  ],
  "properties": {
    "outputFormat": "riff-16khz-16bit-mono-pcm",
    "concatenateResult": false,
    "totalDuration": "PT5M57.252S",
    "billableCharacterCount": 3048
  },
  "id": "eb3d7a81-ee3e-4e9a-b725-713383e71677",
  "lastActionDateTime": "2021-01-14T11:12:27.240Z",
  "status": "Succeeded",
  "createdDateTime": "2021-01-14T11:11:02.557Z",
  "locale": "en-US",
  "displayName": "long audio synthesis sample",
  "description": "sample description"
}
```

`status` 属性从 `NotStarted` 状态变更为 `Running`，最后变更为 `Succeeded` 或 `Failed`。 可以使用循环轮询此 API，直到状态变为 `Succeeded` 或 `Failed`。

### <a name="download-audio-result"></a>下载音频结果

合成请求成功后，可以通过调用 GET `/files` API 来下载音频结果。

```python
def get_files():
    id = '<request_id>'
    region = '<region>'
    key = '<your_key>'
    url = 'https://{}.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0/longaudiosynthesis/{}/files'.format(region, id)
    header = {
        'Ocp-Apim-Subscription-Key': key
    }

    response = requests.get(url, headers=header)
    print('response.status_code: %d' % response.status_code)
    print(response.text)

get_files()
```

将 `<request_id>` 替换为要下载结果的请求的 ID。 可在上一步的响应中找到它。

输出如下所示：

```json
response.status_code: 200
{
  "values": [
    {
      "name": "2779f2aa-4e21-4d13-8afb-6b3104d6661a.txt",
      "kind": "LongAudioSynthesisScript",
      "properties": {
        "size": 4200
      },
      "createdDateTime": "2021-01-14T11:11:02.410Z",
      "links": {
        "contentUrl": "https://customvoice-usw.blob.core.windows.net/artifacts/input.txt?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    },
    {
      "name": "voicesynthesis_waves.zip",
      "kind": "LongAudioSynthesisResult",
      "properties": {
        "size": 9290000
      },
      "createdDateTime": "2021-01-14T11:12:27.226Z",
      "links": {
        "contentUrl": "https://customvoice-usw.blob.core.windows.net/artifacts/voicesynthesis_waves.zip?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    }
  ]
}
```
此示例输出包含两个文件的信息。 包含 `"kind": "LongAudioSynthesisScript"` 的一个文件是已提交的输入脚本。 包含 `"kind": "LongAudioSynthesisResult"` 的另一个文件是此请求的结果。

结果是 zip 文件，其中包含生成的音频输出文件，以及输入文本的副本。

可以从其 `links.contentUrl` 属性的 URL 中下载这两个文件。

### <a name="get-all-synthesis-requests"></a>获取所有合成请求

下面的代码列出了所有提交的请求：

```python
def get_synthesis():
    region = '<region>'
    key = '<your_key>'
    url = 'https://{}.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0/longaudiosynthesis/'.format(region)    
    header = {
        'Ocp-Apim-Subscription-Key': key
    }

    response = requests.get(url, headers=header)
    print('response.status_code: %d' % response.status_code)
    print(response.text)

get_synthesis()
```

输出如下所示：

```json
response.status_code: 200
{
  "values": [
    {
      "models": [
        {
          "id": "8fafd8cd-5f95-4a27-a0ce-59260f873141",
          "voiceName": "my custom neural voice"
        }
      ],
      "properties": {
        "outputFormat": "riff-16khz-16bit-mono-pcm",
        "concatenateResult": false,
        "totalDuration": "PT1S",
        "billableCharacterCount": 5
      },
      "id": "f9f0bb74-dfa5-423d-95e7-58a5e1479315",
      "lastActionDateTime": "2021-01-05T07:25:42.433Z",
      "status": "Succeeded",
      "createdDateTime": "2021-01-05T07:25:13.600Z",
      "locale": "en-US",
      "displayName": "Long Audio Synthesis",
      "description": "Long audio synthesis sample"
    },
    {
      "models": [
        {
          "voiceName": "en-US-AriaNeural"
        }
      ],
      "properties": {
        "outputFormat": "riff-16khz-16bit-mono-pcm",
        "concatenateResult": false,
        "totalDuration": "PT5M57.252S",
        "billableCharacterCount": 3048
      },
      "id": "eb3d7a81-ee3e-4e9a-b725-713383e71677",
      "lastActionDateTime": "2021-01-14T11:12:27.240Z",
      "status": "Succeeded",
      "createdDateTime": "2021-01-14T11:11:02.557Z",
      "locale": "en-US",
      "displayName": "long audio synthesis sample",
      "description": "sample description"
    }
  ]
}
```

`values` 属性列出合成请求。 此列表已分页，最大页大小为 100。 如果请求数超过 100，则提供 `"@nextLink"` 属性以获取分页列表的下一页。

```console
  "@nextLink": "https://<endpoint>/api/texttospeech/v3.0/longaudiosynthesis/?top=100&skip=100"
```

还可以通过在 URL 参数中提供 `skip` 和 `top` 来自定义页面大小和跳过数字。

### <a name="remove-previous-requests"></a>删除以前的请求

该服务最多为每个 Azure 订阅帐户保留 20,000 个请求。 如果请求数量超出此限制，需在提交新请求之前删除以前的请求。 如果不删除现有请求，则会收到错误通知。

以下代码演示如何删除特定的合成请求。

```python
def delete_synthesis():
    id = '<request_id>'
    region = '<region>'
    key = '<your_key>'
    url = 'https://{}.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0/longaudiosynthesis/{}/'.format(region, id)
    header = {
        'Ocp-Apim-Subscription-Key': key
    }

    response = requests.delete(url, headers=header)
    print('response.status_code: %d' % response.status_code)
```

如果成功删除该请求，则响应状态代码将为 HTTP 204（无内容）。

```console
response.status_code: 204
```

> [!NOTE]
> 无法移除或删除状态为 `NotStarted` 或 `Running` 的请求。

[GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Python/voiceclient.py) 上提供了完整的 `long_audio_synthesis_client.py`。

## <a name="http-status-codes"></a>HTTP 状态代码

下表详细介绍了 REST API 中的 HTTP 响应代码和消息。

| API | HTTP 状态代码 | 说明 | 解决方案 |
|-----|------------------|-------------|----------|
| 创建 | 400 | 此区域未启用语音合成。 | 使用受支持区域更改语音订阅密钥。 |
|        | 400 | 只有此区域的标准语音订阅才有效。 | 将语音订阅密钥更改为“标准”定价层。 |
|        | 400 | 超过 Azure 帐户的 20,000 个请求限制。 在提交新请求之前需删除一些请求。 | 服务器将为每个 Azure 帐户最多保留 20,000 个请求。 请在提交新请求之前删除一些请求。 |
|        | 400 | 此模型不能用于语音合成：{modelID}。 | 请确保 {modelID} 的状态正确。 |
|        | 400 | 请求区域与模型区域不一致：{modelID}。 | 请确保 {modelID} 区域与请求区域匹配。 |
|        | 400 | 语音合成仅支持使用包含字节顺序标记的 UTF-8 编码中的文本文件。 | 请确保输入文件使用包含字节顺序标记的 UTF-8 编码。 |
|        | 400 | 语音合成请求中只允许有效的 SSML 输入。 | 请确保输入 SSML 表达式正确。 |
|        | 400 | 在输入文件中找不到语音名称 {voiceName}。 | 输入 SSML 语音名称与模型 ID 不对齐。 |
|        | 400 | 输入文件中的段落数应小于 10,000。 | 请确保文件中的段落数小于 10,000。 |
|        | 400 | 输入文件应超过 400 个字符。 | 请确保输入文件超过 400 个字符。 |
|        | 404 | 找不到语音合成定义中声明的模型：{modelID}。 | 请确保 {modelID} 正确。 |
|        | 429 | 超出活动语音合成限制。 请耐心等待，直至一些请求处理完成。 | 对于每个 Azure 帐户，服务器最多可以运行 120 个请求并将其排入队列。 请耐心等待，避免在某些请求处理完成之前提交新请求。 |
| 全部       | 429 | 请求太多。 | 对于每个 Azure 帐户，客户端每秒最多可以向服务器提交 5 个请求。 请减少每秒请求数量。 |
| Delete    | 400 | 语音合成任务仍在使用中。 | 只能删除“已完成”或“已失败”的请求。 |
| GetByID   | 404 | 找不到指定的实体。 | 请确保合成 ID 正确。 |

## <a name="regions-and-endpoints"></a>区域和终结点

长音频 API 可用于具有单独终结点的多个区域。

| 区域 | 端点 |
|--------|----------|
| 美国东部 | `https://eastus.customvoice.api.speech.microsoft.com` |
| 印度中部 | `https://centralindia.customvoice.api.speech.microsoft.com` |
| 东南亚 | `https://southeastasia.customvoice.api.speech.microsoft.com` |
| 英国南部 | `https://uksouth.customvoice.api.speech.microsoft.com` |
| 西欧 | `https://westeurope.customvoice.api.speech.microsoft.com` |

## <a name="audio-output-formats"></a>音频输出格式

我们支持灵活的语音输入格式。 可以通过设置 `concatenateResult` 参数，为每个段落生成音频输出，或将多个音频输出连接成单个输出。 长音频 API 支持以下音频输出格式：

> [!NOTE]
> 默认音频格式为 riff-16khz-16bit-mono-pcm。

* riff-8khz-16bit-mono-pcm
* riff-16khz-16bit-mono-pcm
* riff-24khz-16bit-mono-pcm
* riff-48khz-16bit-mono-pcm
* audio-16khz-32kbitrate-mono-mp3
* audio-16khz-64kbitrate-mono-mp3
* audio-16khz-128kbitrate-mono-mp3
* audio-24khz-48kbitrate-mono-mp3
* audio-24khz-96kbitrate-mono-mp3
* audio-24khz-160kbitrate-mono-mp3
