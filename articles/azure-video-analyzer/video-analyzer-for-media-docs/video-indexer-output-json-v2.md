---
title: 了解 v2 API 生成的 Azure 视频分析器媒体版（以前称为视频索引器）输出 - Azure
titleSuffix: Azure Video Analyzer for Media
description: 本主题介绍 v2 API 生成的 Azure 视频分析器媒体版（以前称为视频索引器）输出。
services: azure-video-analyzer
author: Juliako
manager: femila
ms.topic: article
ms.subservice: azure-video-analyzer-media
ms.date: 11/16/2020
ms.author: juliako
ms.openlocfilehash: 11fcc36ec8ad6d5feb030d00be4fbf470237634f
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/15/2021
ms.locfileid: "112115264"
---
# <a name="examine-the-video-analyzer-for-media-output"></a>了解视频分析器媒体版输出

为视频编制索引后，Azure 视频分析器媒体版（以前称为视频索引器）将生成包含指定视频见解详细信息的 JSON 内容。 见解包括脚本、OCR、人脸、主题、块等。每种见解类型都包含时间范围实例，这些时间范围将显示见解何时出现在视频中。 

可以通过在[视频分析器媒体版](https://www.videoindexer.ai/)网站中的视频上按“播放”按钮，来直观查看视频的汇总见解。 

你还可以调用“获取视频索引”API 来使用 API，如果响应状态为“正常”，则会获得详细的 JSON 输出作为响应内容。

![洞察力](./media/video-indexer-output-json/video-indexer-summarized-insights.png)

本文将介绍视频分析器媒体版输出（JSON 内容）。 <br/>有关可供使用的功能和见解的信息，请参阅[视频分析器媒体版见解](video-indexer-overview.md#video-insights)。

> [!NOTE]
> 视频分析器媒体版中所有访问令牌的有效期为一小时。

## <a name="get-the-insights"></a>获取见解

### <a name="insightsoutput-produced-in-the-websiteportal"></a>网站/门户中生成的见解/输出

1. 浏览到[视频分析器媒体版](https://www.videoindexer.ai/)网站并登录。
1. 找到你要检查其输出的视频。
1. 按“播放”。
1. 选择“见解”选项卡（汇总见解）或“时间线”选项卡（允许筛选相关的见解） 。
1. 下载项目以及其中的内容。

有关详细信息，请参阅[查看和编辑视频见解](video-indexer-view-edit.md)。

## <a name="insightsoutput-produced-by-api"></a>API 生成的见解/输出

1. 若要检索 JSON 文件，请调用[获取视频索引 API](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Video-Index)
1. 如果你还对特定项目感兴趣，请调用[获取视频项目下载 URL API](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Video-Artifact-Download-Url)

    在 API 调用中，指定请求的项目类型（OCR、人脸、关键帧等）

## <a name="root-elements-of-the-insights"></a>见解的根元素

|名称|说明|
|---|---|
|accountId|播放列表的 VI 帐户 ID。|
|id|播放列表的 ID。|
|name|播放列表的名称。|
|description|播放列表的说明。|
|userName|创建播放列表的用户名。|
|created|播放列表的创建时间。|
|privacyMode|播放列表的隐私模式（私用/公共）。|
|state|播放列表的状态（已上传、正在处理、已处理、失败、已隔离）。|
|isOwned|指示播放列表是否由当前用户创建。|
|isEditable|指示当前用户是否有权编辑播放列表。|
|isBase|指示该播放列表是基础播放列表（视频），还是由其他视频构成的播放列表（派生）。|
|durationInSeconds|播放列表的总持续时间。|
|summarizedInsights|包含一个 [summarizedInsights](#summarizedinsights)。
|videos|构成播放列表的[视频](#videos)列表。<br/>如果此播放列表由其他视频的时间范围构成（派生），则此列表中的视频仅包含这些时间范围的数据。|

```json
{
  "accountId": "bca61527-1221-bca6-1527-a90000002000",
  "id": "abc3454321",
  "name": "My first video",
  "description": "I am trying VI",
  "userName": "Some name",
  "created": "2018/2/2 18:00:00.000",
  "privacyMode": "Private",
  "state": "Processed",
  "isOwned": true,
  "isEditable": false,
  "isBase": false,
  "durationInSeconds": 120, 
  "summarizedInsights" : { . . . }
  "videos": [{ . . . }]
}
```

## <a name="summarizedinsights"></a>summarizedInsights

本部分介绍见解的摘要。

|属性 | 说明|
|---|---|
|name|视频的名称。 例如 Azure Monitor。|
|id|视频的 ID。 例如 63c6d532ff。|
|privacyMode|可以细分为以下模式之一：“私用”、“公共”。 **公共** - 向你帐户中的任何人，以及具有视频链接的每个人显示该视频。 **私用** - 向你帐户中的每个人显示该视频。|
|duration|包含一个持续时间，用于描述见解发生的时间。 持续时间以秒为单位。|
|thumbnailVideoId|从其创建缩略图的视频的 ID。
|thumbnailId|视频的缩略图 ID。 若要获取实际缩略图，请调用 [Get-Thumbnail](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Video-Thumbnail) 并为其传递 thumbnailVideoId 和 thumbnailId。|
|faces/animatedCharacters|可以包含零个或多个人脸。 有关更多详细信息，请参阅 [faces/animatedCharacters](#facesanimatedcharacters)。|
|关键字|可以包含零个或多个关键字。 有关更详细的信息，请参阅 [keywords](#keywords)。|
|情绪|可以包含零个或多个情绪。 有关更详细的信息，请参阅 [sentiments](#sentiments)。|
|audioEffects| 可以包含零个或多个音效。 有关更详细的信息，请参阅 [audioEffects](#audioeffects-preview)。|
|标签| 可以包含零个或多个标签。 有关更详细的信息，请参阅 [labels](#labels)。|
|brands| 可以包含零个或多个品牌。 有关更详细的信息，请参阅 [brands](#brands)。|
|statistics | 有关更详细的信息，请参阅 [statistics](#statistics)。|
|情感| 可以包含零个或多个情感。 有关更详细的信息，请参阅 [emotions](#emotions)。|
|topics|可以包含零个或多个主题。 [主题](#topics)见解。|

## <a name="videos"></a>videos

|名称|说明|
|---|---|
|accountId|视频的 VI 帐户 ID。|
|id|视频的 ID。|
|name|视频的名称。
|state|视频的状态（已上传、正在处理、已处理、失败、已隔离）。|
|processingProgress|处理进度（例如 20%）。|
|failureCode|无法处理时显示的失败代码（例如“UnsupportedFileType”）。|
|failureMessage|无法处理时显示的失败消息。|
|externalId|视频的外部 ID（如果用户已指定）。|
|externalUrl|视频的外部 URL（如果用户已指定）。|
|metadata|视频的外部元数据（如果用户已指定）。|
|isAdult|指示视频是否已经过人工审查，并已标识为成人视频。|
|insights|见解对象。 有关详细信息，请参阅 [insights](#insights)。|
|thumbnailId|视频的缩略图 ID。 若要获取实际缩略图，请调用 [Get-Thumbnail](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Video-Thumbnail) 并为其传递视频 ID 和 thumbnailId。|
|publishedUrl|用于流式传输视频的 URL。|
|publishedUrlProxy|要从中流式传输视频的 URL（适用于 Apple 设备）。|
|viewToken|用于流式传输视频的短期查看令牌。|
|sourceLanguage|视频的源语言。|
|语言|视频的实际语言（翻译）。|
|indexingPreset|用于编制视频索引的预设。|
|streamingPreset|用于发布视频的预设。|
|linguisticModelId|用于转录视频的 CRIS 模型。|
|statistics | 有关详细信息，请参阅 [statistics](#statistics)。|

```json
{
    "videos": [{
        "accountId": "2cbbed36-1972-4506-9bc7-55367912df2d",
        "id": "142a356aa6",
        "state": "Processed",
        "privacyMode": "Private",
        "processingProgress": "100%",
        "failureCode": "General",
        "failureMessage": "",
        "externalId": null,
        "externalUrl": null,
        "metadata": null,
        "insights": {. . . },
        "thumbnailId": "89d7192c-1dab-4377-9872-473eac723845",
        "publishedUrl": "https://videvmediaservices.streaming.mediaservices.windows.net:443/d88a652d-334b-4a66-a294-3826402100cd/Xamarine.ism/manifest",
        "publishedProxyUrl": null,
        "viewToken": "Bearer=<token>",
        "sourceLanguage": "En-US",
        "language": "En-US",
        "indexingPreset": "Default",
        "linguisticModelId": "00000000-0000-0000-0000-000000000000"
    }],
}
```
### <a name="insights"></a>insights

每个见解（例如脚本行、人脸、品牌等）包含唯一元素（例如 face1、face2、face3）的列表，并且每个元素有自身的元数据及其实例（具有其他可选元数据的时间范围）的列表。

人脸可能具有 ID、名称、缩略图、其他元数据及其时态实例（例如：00:00:05 - 00:00:10、00:01:00 - 00:02:30 和 00:41:21 - 00:41:49）的列表。每个时态实例可以包含其他元数据。 例如，人脸的矩形坐标 (20,230,60,60)。

|版本|代码版本|
|---|---|
|sourceLanguage|视频的源语言（采用一种主要语言）。 格式为 [BCP-47](https://tools.ietf.org/html/bcp47) 字符串。|
|语言|见解语言（从源语言翻译）。 格式为 [BCP-47](https://tools.ietf.org/html/bcp47) 字符串。|
|脚本|[脚本](#transcript)见解。|
|ocr|[OCR](#ocr) 见解。|
|关键字|[关键字](#keywords)见解。|
|blocks|可以包含一个或多个[块](#blocks)|
|faces/animatedCharacters|[faces/animatedCharacters](#facesanimatedcharacters) 见解。|
|标签|[标签](#labels)见解。|
|截图|[截图](#shots)见解。|
|brands|[品牌](#brands)见解。|
|audioEffects|[audioEffects](#audioeffects-preview) 见解。|
|情绪|[情绪](#sentiments)见解。|
|visualContentModeration|[visualContentModeration](#visualcontentmoderation) 见解。|
|textualContentModeration|[textualContentModeration](#textualcontentmoderation) 见解。|
|情感| [情感](#emotions)见解。|
|topics|[主题](#topics)见解。|
|扬声器|[发言人](#speakers)见解。|

示例：

```json
{
  "version": "0.9.0.0",
  "sourceLanguage": "en-US",
  "language": "es-ES",
  "transcript": ...,
  "ocr": ...,
  "keywords": ...,
  "faces": ...,
  "labels": ...,
  "shots": ...,
  "brands": ...,
  "audioEffects": ...,
  "sentiments": ...,
  "visualContentModeration": ...,
  "textualContentModeration": ...
}
```

#### <a name="blocks"></a>blocks

属性 | 说明
---|---
id|块的 ID。|
instances|此块的时间范围列表。|

#### <a name="transcript"></a>脚本

|名称|说明|
|---|---|
|id|行 ID。|
|text|脚本本身。|
|confidence|脚本准确性置信度。|
|speakerId|发言人的 ID。|
|语言|脚本语言。 旨在支持每行语言不同的脚本。|
|instances|出现该行的时间范围列表。 如果实例是脚本，则只有 1 个实例。|

示例：

```json
"transcript":[
{
  "id":1,
  "text":"Well, good morning everyone and welcome to",
  "confidence":0.8839,
  "speakerId":1,
  "language":"en-US",
  "instances":[
     {
    "adjustedStart":"0:00:10.21",
    "adjustedEnd":"0:00:12.81",
    "start":"0:00:10.21",
    "end":"0:00:12.81"
     }
  ]
},
{
  "id":2,
  "text":"ignite 2016. Your mission at Microsoft is to empower every",
  "confidence":0.8944,
  "speakerId":2,
  "language":"en-US",
  "instances":[
     {
    "adjustedStart":"0:00:12.81",
    "adjustedEnd":"0:00:17.03",
    "start":"0:00:12.81",
    "end":"0:00:17.03"
     }
  ]
},
```

#### <a name="ocr"></a>ocr

|名称|说明|
|---|---|
|id|OCR 行 ID。|
|text|OCR 文本。|
|confidence|识别置信度。|
|语言|OCR 语言。|
|instances|出现此 OCR 的时间范围列表（同一 OCR 可重复多次出现）。|
|高度|OCR 矩形的高度|
|top|像素的最高位置|
|左侧| 像素的左侧位置|
|width|OCR 矩形的宽度|

```json
"ocr": [
    {
      "id": 0,
      "text": "LIVE FROM NEW YORK",
      "confidence": 675.971,
      "height": 35,
      "language": "en-US",
      "left": 31,
      "top": 97,
      "width": 400,      
      "instances": [
        {
          "start": "00:00:26",
          "end": "00:00:52"
        }
      ]
    }
  ],
```

#### <a name="keywords"></a>关键字

|名称|说明|
|---|---|
|id|关键字 ID。|
|text|关键字文本。|
|confidence|关键字的识别置信度。|
|语言|关键字语言（转换后）。|
|instances|出现此关键字的时间范围列表（一个关键字可重复多次出现）。|

```json
{
    id: 0,
    text: "technology",
    confidence: 1,
    language: "en-US",
    instances: [{
            adjustedStart: "0:05:15.782",
            adjustedEnd: "0:05:16.249",
            start: "0:05:15.782",
            end: "0:05:16.249"
    },
    {
            adjustedStart: "0:04:54.761",
            adjustedEnd: "0:04:55.228",
            start: "0:04:54.761",
            end: "0:04:55.228"
    }]
}
```

#### <a name="facesanimatedcharacters"></a>faces/animatedCharacters

用 `animatedCharacters` 元素替换 `faces`，以防使用动画角色模型对视频进行索引。 此操作通过使用自定义视觉中的自定义模型完成，视频分析器媒体版在关键帧上运行它。

如果出现人脸（非动画角色），视频分析器媒体版将对所有视频帧使用人脸 API 来检测人脸和名人。

|名称|说明|
|---|---|
|id|人脸 ID。|
|name|人脸名称。 可以为“Unknown #0”、公认的名人或经过客户培训的人员。|
|confidence|人脸识别置信度。|
|description|名人的说明。 |
|thumbnailId|该人脸的缩略图 ID。|
|knownPersonId|如果是已知人员，则为此人的内部 ID。|
|referenceId|如果是必应名人，则为此人的必应 ID。|
|referenceType|当前仅限必应。|
|title|如果是名人，则为此人的头衔（例如“Microsoft 的 CEO”）。|
|imageUrl|如果是名人，则为此人的图像 URL。|
|instances|在给定时间范围内出现该人脸的实例。 每个实例还具有一个 thumbnailsId。 |

```json
"faces": [{
    "id": 2002,
    "name": "Xam 007",
    "confidence": 0.93844,
    "description": null,
    "thumbnailId": "00000000-aee4-4be2-a4d5-d01817c07955",
    "knownPersonId": "8340004b-5cf5-4611-9cc4-3b13cca10634",
    "referenceId": null,
    "title": null,
    "imageUrl": null,
    "instances": [{
        "thumbnailsIds": ["00000000-9f68-4bb2-ab27-3b4d9f2d998e",
        "cef03f24-b0c7-4145-94d4-a84f81bb588c"],
        "adjustedStart": "00:00:07.2400000",
        "adjustedEnd": "00:00:45.6780000",
        "start": "00:00:07.2400000",
        "end": "00:00:45.6780000"
    },
    {
        "thumbnailsIds": ["00000000-51e5-4260-91a5-890fa05c68b0"],
        "adjustedStart": "00:10:23.9570000",
        "adjustedEnd": "00:10:39.2390000",
        "start": "00:10:23.9570000",
        "end": "00:10:39.2390000"
    }]
}]
```

#### <a name="labels"></a>标签

|名称|说明|
|---|---|
|id|标签 ID。|
|name|标签名称（例如“计算机”、“电视”）。|
|语言|标签名称语言（转换后）。 BCP-47|
|instances|出现此标签的时间范围列表（一个标签可重复多次出现）。 每个实例都有置信度字段。 |


```json
"labels": [
    {
      "id": 0,
      "name": "person",
      "language": "en-US",
      "instances": [
        {
          "confidence": 1.0,
          "start": "00: 00: 00.0000000",
          "end": "00: 00: 25.6000000"
        },
        {
          "confidence": 1.0,
          "start": "00: 01: 33.8670000",
          "end": "00: 01: 39.2000000"
        }
      ]
    },
    {
      "name": "indoor",
      "language": "en-US",
      "id": 1,
      "instances": [
        {
          "confidence": 1.0,
          "start": "00: 00: 06.4000000",
          "end": "00: 00: 07.4670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 09.6000000",
          "end": "00: 00: 10.6670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 11.7330000",
          "end": "00: 00: 20.2670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 21.3330000",
          "end": "00: 00: 25.6000000"
        }
      ]
    }
  ] 
```

#### <a name="scenes"></a>scenes

|名称|说明|
|---|---|
|id|场景 ID。|
|instances|此场景的时间范围列表（每个场景只能有 1 个实例）。|

```json
"scenes":[  
    {  
      "id":0,
      "instances":[  
          {  
            "start":"0:00:00",
            "end":"0:00:06.34",
            "duration":"0:00:06.34"
          }
      ]
    },
    {  
      "id":1,
      "instances":[  
          {  
            "start":"0:00:06.34",
            "end":"0:00:47.047",
            "duration":"0:00:40.707"
          }
      ]
    },

]
```

#### <a name="shots"></a>截图

|名称|说明|
|---|---|
|id|截图 ID。|
|keyFrames|截图内的关键帧列表（每个关键帧都有一个 ID 和实例时间范围列表）。 每个关键帧实例都有一个 thumbnailId 字段，该字段包含关键帧的缩略图 ID。|
|instances|此快照的时间范围列表（每个快照只能有 1 个实例）。|

```json
"shots":[  
    {  
      "id":0,
      "keyFrames":[  
          {  
            "id":0,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:00.209",
                  "end":"0:00:00.251",
                  "duration":"0:00:00.042"
                }
            ]
          },
          {  
            "id":1,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:04.755",
                  "end":"0:00:04.797",
                  "duration":"0:00:00.042"
                }
            ]
          }
      ],
      "instances":[  
          {  
            "start":"0:00:00",
            "end":"0:00:06.34",
            "duration":"0:00:06.34"
          }
      ]
    },

]
```

#### <a name="brands"></a>brands

在语音转文本脚本和/或视频 OCR 中检测到的企业和产品品牌名称。 这不包括品牌或徽标检测内容的视觉辨识形式。

|名称|说明|
|---|---|
|id|品牌 ID。|
|name|品牌名称。|
|referenceId | 品牌维基百科 URL 的后缀。 例如，“Target_Corporation”是 [https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation) 的后缀。
|referenceUrl | 品牌的维基百科 URL（如果存在）。 例如， [https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation) 。
|description|品牌说明。|
|标记|与此品牌关联的预定义标记的列表。|
|confidence|视频分析器媒体版品牌检测器的置信度值 (0-1)。|
|instances|此品牌的时间范围列表。 每个实例有一个 brandType，表示此品牌是出现在脚本还是 OCR 中。|

```json
"brands": [
{
    "id": 0,
    "name": "MicrosoftExcel",
    "referenceId": "Microsoft_Excel",
    "referenceUrl": "http: //en.wikipedia.org/wiki/Microsoft_Excel",
    "referenceType": "Wiki",
    "description": "Microsoft Excel is a sprea..",
    "tags": [],
    "confidence": 0.975,
    "instances": [
    {
        "brandType": "Transcript",
        "start": "00: 00: 31.3000000",
        "end": "00: 00: 39.0600000"
    }
    ]
},
{
    "id": 1,
    "name": "Microsoft",
    "referenceId": "Microsoft",
    "referenceUrl": "http: //en.wikipedia.org/wiki/Microsoft",
    "description": "Microsoft Corporation is...",
    "tags": [
    "competitors",
    "technology"
    ],
    "confidence": 1.0,
    "instances": [
    {
        "brandType": "Transcript",
        "start": "00: 01: 44",
        "end": "00: 01: 45.3670000"
    },
    {
        "brandType": "Ocr",
        "start": "00: 01: 54",
        "end": "00: 02: 45.3670000"
    }
    ]
}
]
```

#### <a name="statistics"></a>statistics

|名称|说明|
|---|---|
|CorrespondenceCount|视频中对应关系的数目。|
|SpeakerWordCount|每个发言人的单词数。|
|SpeakerNumberOfFragments|发言人在视频中的片段数量。|
|SpeakerLongestMonolog|发言人的最长独白。 如果发言人在独白中有沉默，则会将沉默期包含在内。 删除独白开头和结尾的沉默期。| 
|SpeakerTalkToListenRatio|计算方式为将发言人的独白时间（开头和结尾之间没有沉默期）除以视频总时间。 时间将四舍五入为三位小数。|

#### <a name="audioeffects-preview"></a>audioEffects（预览版）

|名称|说明
|---|---|
|id|音频效果 ID|
|type|音频效果类型|
|name| 用于编制 JSON 索引的语言中的音频效果类型。 |
|instances|出现此音频效果的时间范围列表。 每个实例都有置信度字段。|
|start + end| 原始视频中的时间范围。|
|adjustedStart + adjustedEnd|[时间范围和调整的时间范围](concepts-overview.md#time-range-vs-adjusted-time-range)|

```json
audioEffects: [{
 {
        id: 0,
        type: "Laughter",
        name: "Laughter",
        instances: [{
                confidence: 0.8815,
                adjustedStart: "0:00:10.2",
                adjustedEnd: "0:00:11.2",
                start: "0:00:10.2",
                end: "0:00:11.2"
            }, {
                confidence: 0.8554,
                adjustedStart: "0:00:48.26",
                adjustedEnd: "0:00:49.56",
                start: "0:00:48.26",
                end: "0:00:49.56"
            }, {
                confidence: 0.8492,
                adjustedStart: "0:00:59.66",
                adjustedEnd: "0:01:00.66",
                start: "0:00:59.66",
                end: "0:01:00.66"
            }
        ]
    }
],
```

#### <a name="sentiments"></a>情绪

情绪依据其 sentimentType 字段得出（积极/中立/消极）。 例如：0-0.1、0.1-0.2。

|名称|说明|
|---|---|
|id|情绪 ID。|
|averageScore |该情绪类型的所有实例的所有分数的均值 - 积极/中立/消极|
|instances|出现此情绪的时间范围列表。|
|sentimentType |类型可以是“Positive”、“Neutral”或“Negative”。|

```json
"sentiments": [
{
    "id": 0,
    "averageScore": 0.87,
    "sentimentType": "Positive",
    "instances": [
    {
        "start": "00:00:23",
        "end": "00:00:41"
    }
    ]
}, {
    "id": 1,
    "averageScore": 0.11,
    "sentimentType": "Positive",
    "instances": [
    {
        "start": "00:00:13",
        "end": "00:00:21"
    }
    ]
}
]
```

#### <a name="visualcontentmoderation"></a>visualContentModeration

visualContentModeration 块包含视频分析器媒体版找到的、可能具有成人内容的时间范围。 如果 visualContentModeration 为空，则表示未识别到成人内容。

被确定包含成人或不雅内容的视频可能仅可供私人观看。 用户可以选择请求人工审查内容，在这种情况下，IsAdult 属性将包含人工审查的结果。

|名称|说明|
|---|---|
|id|视觉内容审核 ID。|
|adultScore|成人内容评分（由内容审核员提供）。|
|racyScore|不雅内容评分（由内容审核员提供）。|
|instances|显示此视觉内容审核的时间范围列表。|

```json
"VisualContentModeration": [
{
    "id": 0,
    "adultScore": 0.00069,
    "racyScore": 0.91129,
    "instances": [
    {
        "start": "00:00:25.4840000",
        "end": "00:00:25.5260000"
    }
    ]
},
{
    "id": 1,
    "adultScore": 0.99231,
    "racyScore": 0.99912,
    "instances": [
    {
        "start": "00:00:35.5360000",
        "end": "00:00:35.5780000"
    }
    ]
}
] 
```

#### <a name="textualcontentmoderation"></a>textualContentModeration 

|名称|说明|
|---|---|
|id|文本内容审核 ID。|
|bannedWordsCount |受禁单词的数目。|
|bannedWordsRatio |占单词总数的比。|

#### <a name="emotions"></a>情感

视频分析器媒体版基于语音和音频提示识别情感。识别的情感可能是：快乐、悲伤、愤怒或恐惧。

|名称|说明|
|---|---|
|id|情感 ID。|
|type|基于语音和音频提示识别的瞬间情感。情感可能是：快乐、悲伤、愤怒或恐惧。|
|instances|出现该情感的时间范围列表。|

```json
"emotions": [{
    "id": 0,
    "type": "Fear",
    "instances": [{
      "adjustedStart": "0:00:39.47",
      "adjustedEnd": "0:00:45.56",
      "start": "0:00:39.47",
      "end": "0:00:45.56"
    },
    {
      "adjustedStart": "0:07:19.57",
      "adjustedEnd": "0:07:23.25",
      "start": "0:07:19.57",
      "end": "0:07:23.25"
    }]
  },
  {
    "id": 1,
    "type": "Anger",
    "instances": [{
      "adjustedStart": "0:03:55.99",
      "adjustedEnd": "0:04:05.06",
      "start": "0:03:55.99",
      "end": "0:04:05.06"
    },
    {
      "adjustedStart": "0:04:56.5",
      "adjustedEnd": "0:05:04.35",
      "start": "0:04:56.5",
      "end": "0:05:04.35"
    }]
  },
  {
    "id": 2,
    "type": "Joy",
    "instances": [{
      "adjustedStart": "0:12:23.68",
      "adjustedEnd": "0:12:34.76",
      "start": "0:12:23.68",
      "end": "0:12:34.76"
    },
    {
      "adjustedStart": "0:12:46.73",
      "adjustedEnd": "0:12:52.8",
      "start": "0:12:46.73",
      "end": "0:12:52.8"
    },
    {
      "adjustedStart": "0:30:11.29",
      "adjustedEnd": "0:30:16.43",
      "start": "0:30:11.29",
      "end": "0:30:16.43"
    },
    {
      "adjustedStart": "0:41:37.23",
      "adjustedEnd": "0:41:39.85",
      "start": "0:41:37.23",
      "end": "0:41:39.85"
    }]
  },
  {
    "id": 3,
    "type": "Sad",
    "instances": [{
      "adjustedStart": "0:13:38.67",
      "adjustedEnd": "0:13:41.3",
      "start": "0:13:38.67",
      "end": "0:13:41.3"
    },
    {
      "adjustedStart": "0:28:08.88",
      "adjustedEnd": "0:28:18.16",
      "start": "0:28:08.88",
      "end": "0:28:18.16"
    }]
  }
],
```

#### <a name="topics"></a>topics

视频分析器媒体版从脚本中推理主要主题。 在可能的情况下，会包括第二级 [IPTC](https://iptc.org/standards/media-topics/) 分类。 

|名称|说明|
|---|---|
|id|主题 ID。|
|name|主题名称，例如：“Pharmaceuticals”。|
|referenceId|反映主题层次结构的痕迹导航。 例如：“健康和福利 / 医疗和保健 / 药品”。|
|confidence|[0,1] 范围内的置信度评分。 评分越高，则置信度越高。|
|语言|主题中使用的语言。|
|iptcName|IPTC 媒体代码名称（如果已检测到）。|
|instances |目前，视频分析器媒体版不会按时间间隔编制主题的索引，因此，整个视频将用作间隔。|

```json
"topics": [{
    "id": 0,
    "name": "INTERNATIONAL RELATIONS",
    "referenceId": "POLITICS AND GOVERNMENT/FOREIGN POLICY/INTERNATIONAL RELATIONS",
    "referenceType": "VideoIndexer",
    "confidence": 1,
    "language": "en-US",
    "instances": [{
        "adjustedStart": "0:00:00",
        "adjustedEnd": "0:03:36.25",
        "start": "0:00:00",
        "end": "0:03:36.25"
    }]
}, {
    "id": 1,
    "name": "Politics and Government",
    "referenceType": "VideoIndexer",
    "iptcName": "Politics",
    "confidence": 0.9041,
    "language": "en-US",
    "instances": [{
        "adjustedStart": "0:00:00",
        "adjustedEnd": "0:03:36.25",
        "start": "0:00:00",
        "end": "0:03:36.25"
    }]
}]
. . .
```

#### <a name="speakers"></a>扬声器

|名称|说明|
|---|---|
|id|发言人 ID。|
|name|发言人姓名，格式为“发言人 #<number>”，例如“发言人 #1”。|
|instances |出现该发言人的时间范围列表。|

```json
"speakers":[
{
  "id":1,
  "name":"Speaker #1",
  "instances":[
     {
    "adjustedStart":"0:00:10.21",
    "adjustedEnd":"0:00:12.81",
    "start":"0:00:10.21",
    "end":"0:00:12.81"
     }
  ]
},
{
  "id":2,
  "name":"Speaker #2",
  "instances":[
     {
    "adjustedStart":"0:00:12.81",
    "adjustedEnd":"0:00:17.03",
    "start":"0:00:12.81",
    "end":"0:00:17.03"
     }
  ]
},
` ` `
```
## <a name="next-steps"></a>后续步骤

[视频分析器媒体版开发人员门户](https://api-portal.videoindexer.ai)

有关如何在应用程序中嵌入小组件的信息，请参阅[将视频分析器媒体版小组件嵌入应用程序](video-indexer-embed-widgets.md)。 

