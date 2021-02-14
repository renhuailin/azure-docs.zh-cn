---
title: 语言支持 - 语音服务
titleSuffix: Azure Cognitive Services
description: 语音服务支持多种语言，可用于语音到文本和文本到语音转换，以及语音翻译。 本文提供了按服务功能列出的语言支持的完整列表。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/07/2021
ms.author: trbye
ms.custom: references_regions
ms.openlocfilehash: 9dfd950303d1cb838d44ae672fed33dd4cd7b5ec
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100381896"
---
# <a name="language-and-voice-support-for-the-speech-service"></a>语音服务的语言和语音支持

语言支持因语音服务功能而异。 下表汇总了对[语音转文本](#speech-to-text)、[文本转语音](#text-to-speech)和[语音翻译](#speech-translation)服务产品的语言支持。

## <a name="speech-to-text"></a>语音转文本

Microsoft 语音 SDK 和 REST API 都支持以下语言（区域设置）。 

为了提高准确性，已为一部分语言提供了自定义功能，你可通过上传音频和人工标记的脚本或相关文本（语句）进行自定义。 支持自定义带有 **音频和人标记** 的声音的声音模型仅限于下面列出的特定基本模型。 其他基本模型和语言将只使用脚本文本来训练自定义模型，就像使用 **相关文本：句子** 一样。 若要了解有关自定义的详细信息，请参阅[自定义语音识别入门](./custom-speech-overview.md)。

<!--
To get the AM and ML bits:
https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetSupportedLocalesForModels

To get pronunciation bits:
https://cris.ai -> Click on Adaptation Data -> scroll down to section "Pronunciation Datasets" -> Click on Import -> Locale: the list of locales there correspond to the supported locales
-->

| 语言                 | 区域设置 (BCP-47) | 自定义  | [语言检测](how-to-automatic-language-detection.md) |
|------------------------------------|--------|---------------------------------------------------|-------------------------------|
| 阿拉伯语(巴林)，现代标准  | `ar-BH` | 语言模型                                   | 是                           | 
| 阿拉伯语（埃及）                     | `ar-EG` | 语言模型                                   | 是                          |
| 阿拉伯语（伊拉克）                      | `ar-IQ` | 语言模型                                   |                           |
| 阿拉伯语（以色列）                    | `ar-IL` | 语言模型                                   |                           |
| 阿拉伯语（约旦）                    | `ar-JO` | 语言模型                                   |                           |
| 阿拉伯语(科威特)                    | `ar-KW` | 语言模型                                   |                           |
| 阿拉伯语（黎巴嫩）                   | `ar-LB` | 语言模型                                   |                           |
| 阿拉伯语（阿曼）                      | `ar-OM` | 语言模型                                   |                           |
| 阿拉伯语（卡塔尔）                     | `ar-QA` | 语言模型                                   |                           |
| 阿拉伯语（沙特阿拉伯）              | `ar-SA` | 语言模型                                   | 是                          |
| 阿拉伯语（巴勒斯坦）        | `ar-PS` | 语言模型                                   |                           |
| 阿拉伯语（叙利亚）                     | `ar-SY` | 语言模型                                   | 是                          |
| 阿拉伯语（阿拉伯联合酋长国）      | `ar-AE` | 语言模型                                   |                           |
| 保加利亚语(保加利亚)               | `bg-BG` | 语言模型                                   |                           |
| 加泰罗尼亚语(西班牙)                    | `ca-ES` | 语言模型                                   | 是                          |
| 中文（粤语，繁体）   | `zh-HK` | 音响型号 (20201015) <br>语言模型                 |        是                   |
| 中文（普通话，简体）     | `zh-CN` | 音响型号 (20200910) <br>语言模型                 |     是                      |
| 中文(台湾普通话)       | `zh-TW` | 音响型号 (20190701，20201015) <br>语言模型                 |           是                |
| 克罗地亚语（克罗地亚）                 | `hr-HR` | 语言模型                                   |                           |
| 捷克语（捷克共和国）             | `cs-CZ` | 语言模型                                   |                           |
| 丹麦语（丹麦）                   | `da-DK` | 语言模型                                   | 是                          |
| 荷兰语（荷兰）                | `nl-NL` | 音响型号 (20201015) <br>语言模型                                   |    是                       |
| 英语（澳大利亚）                | `en-AU` | 音响型号 (20201019) <br>语言模型                 | 是                          |
| 英语（加拿大）                   | `en-CA` | 音响型号 (20201019) <br>语言模型                 | 是                          |
| 英语（香港）                | `en-HK` | 语言模型                                   |                           |
| 英语（印度）                    | `en-IN` | 音响型号 (20200923) <br>语言模型                 | 是                          |
| 英语（爱尔兰）                  | `en-IE` | 语言模型                                   |                           |
| 英语（新西兰）              | `en-NZ` | 音响型号 (20201019) <br>语言模型                 |  是                         |
| 英语（尼日利亚）                  | `en-NG` | 语言模型                                   |                           |
| 英语（菲律宾）              | `en-PH` | 语言模型                                   |                           |
| 英语（新加坡）                | `en-SG` | 语言模型                                   |                           |
| 英语（南非）             | `en-ZA` | 语言模型                                   |                           |
| 英语（英国）           | `en-GB` | 音响型号 (20201019) <br>语言模型<br>发音| 是                          |
| 英语（美国）            | `en-US` | 音响型号 (20201019) <br>语言模型<br>发音| 是                          |
| 爱沙尼亚语（爱沙尼亚）                  | `et-EE` | 语言模型                                   |                           |
| 芬兰语（芬兰）                  | `fi-FI` | 语言模型                                   |     是                      |
| 法语（加拿大）                    | `fr-CA` | 音响型号 (20201015) <br>语言模型                 |     是                      |
| 法语（法国）                    | `fr-FR` | 音响型号 (20201015) <br>语言模型<br>发音|      是                     |
| 德语（德国）                   | `de-DE` | 声音模型 (20190701、20200619、20201127) <br>语言模型<br>发音|  是                         |
| 希腊语(希腊)                     | `el-GR` | 语言模型                                   |                           |
| 古吉拉特语(印度)                  | `gu-IN` | 语言模型                                   |                           |
| 印地语（印度）                      | `hi-IN` | 音响型号 (20200701) <br>语言模型                 |     是                      |
| 匈牙利语(匈牙利)                | `hu-HU` | 语言模型                                   |                           |
| 爱尔兰语（爱尔兰）                     | `ga-IE` | 语言模型                                   |                           |
| 意大利语（意大利）                    | `it-IT` | 音响型号 (20201016) <br>语言模型<br>发音|      是                     |
| 日语（日本）                   | `ja-JP` | 语言模型                                   |      是                     |
| 韩语(韩国)                     | `ko-KR` | 音响型号 (20201015) <br>语言模型                 |      是                     |
| 拉脱维亚语(拉脱维亚)                   | `lv-LV` | 语言模型                                   |                           |
| 立陶宛语（立陶宛）             | `lt-LT` | 语言模型                                   |                           |
| 马耳他语（马耳他）                     | `mt-MT` | 语言模型                                   |                           |
| 马拉地语(印度)                    | `mr-IN` | 语言模型                                   |                           |
| 挪威语（博克马尔语，挪威）         | `nb-NO` | 语言模型                                   |     是                      |
| 波兰语（波兰）                    | `pl-PL` | 语言模型                                   |       是                    |
| 葡萄牙语（巴西）                | `pt-BR` | 音响型号 (20190620，20201015) <br>语言模型<br>发音|          是                 |
| 葡萄牙语(葡萄牙)              | `pt-PT` | 语言模型                                   |             是              |
| 罗马尼亚语(罗马尼亚)                 | `ro-RO` | 语言模型                                   |                           |
| 俄语（俄罗斯）                   | `ru-RU` | 音响型号 (20200907) <br>语言模型                 |                是           |
| 斯洛伐克语(斯洛伐克)                  | `sk-SK` | 语言模型                                   |                           |
| 斯洛文尼亚语（斯洛文尼亚）               | `sl-SI` | 语言模型                                   |                           |
| 西班牙语（阿根廷）                | `es-AR` | 语言模型                                   |                           |
| 西班牙语（玻利维亚）                  | `es-BO` | 语言模型                                   |                           |
| 西班牙语（智利）                    | `es-CL` | 语言模型                                   |                           |
| 西班牙语（哥伦比亚）                 | `es-CO` | 语言模型                                   |                           |
| 西班牙语（哥斯达黎加）               | `es-CR` | 语言模型                                   |                           |
| 西班牙语（古巴）                     | `es-CU` | 语言模型                                   |                           |
| 西班牙语（多米尼加共和国）       | `es-DO` | 语言模型                                   |                           |
| 西班牙语（厄瓜多尔）                  | `es-EC` | 语言模型                                   |                           |
| 西班牙语（萨尔瓦多）              | `es-SV` | 语言模型                                   |                           |
| 西班牙语（赤道几内亚）        | `es-GQ` | 语言模型                                   |                           |
| 西班牙语（危地马拉）                | `es-GT` | 语言模型                                   |                           |
| 西班牙语（洪都拉斯）                 | `es-HN` | 语言模型                                   |                           |
| 西班牙语（墨西哥）                   | `es-MX` | 音响型号 (20200907) <br>语言模型                 |    是                       |
| 西班牙（尼加拉瓜）                | `es-NI` | 语言模型                                   |                           |
| 西班牙语（巴拿马）                   | `es-PA` | 语言模型                                   |                           |
| 西班牙语（巴拉圭）                 | `es-PY` | 语言模型                                   |                           |
| 西班牙语（秘鲁）                     | `es-PE` | 语言模型                                   |                           |
| 西班牙语（波多黎各）              | `es-PR` | 语言模型                                   |                           |
| 西班牙语(西班牙)                    | `es-ES` | 音响型号 (20201015) <br>语言模型                 |  是                         |
| 西班牙语（乌拉圭）                  | `es-UY` | 语言模型                                   |                           |
| 西班牙语（美国）                      | `es-US` | 语言模型                                   |                           |
| 西班牙语（委内瑞拉）                | `es-VE` | 语言模型                                   |                           |
| 瑞典语（瑞典）                   | `sv-SE` | 语言模型                                   |   是                        |
| 泰米尔语（印度）                      | `ta-IN` | 语言模型                                   |                           |
| 泰卢固语（印度）                     | `te-IN` | 语言模型                                   |                           |
| 泰语（泰国）                    | `th-TH` | 语言模型                                   |      是                     |
| 土耳其语（土耳其）                   | `tr-TR` | 语言模型                                   |                           |

## <a name="text-to-speech"></a>文本转语音

Microsoft 语音 SDK 和 REST API 支持以下语音，其中的每种语音都支持特定语言和方言（按区域设置标识）。 还可以通过[语音/列表 API](rest-text-to-speech.md#get-a-list-of-voices) 获取每个特定区域/终结点支持的语言和语音的完整列表。 

> [!IMPORTANT]
> 标准语音、自定义语音和神经语音的定价各不相同。 有关其他信息，请访问[定价](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)页。

### <a name="neural-voices"></a>神经语音

神经文本到语音转换是由深度神经网络提供支持的新型语音合成。 使用神经语音时，几乎无法将合成的语音与人类录音区分开来。

使用神经语音可使得与聊天机器人和语音助手的交互更加自然且富有吸引力、将数字文本（如电子书）转换为有声读物以及增强车载导航系统。 随着类人的自然韵律和字词的清晰发音，用户在与 AI 系统交互时，神经语音显著减轻了听力疲劳。

> [!NOTE]
> 神经语音由使用 24 khz 采样率的样本创建。
> 合成时，所有声音都可以使用其他采样率进行上采样或下采样。


| 语言 | Locale | 性别 | 语音名称 | 风格支持 |
|---|---|---|---|---|
| 阿拉伯语（埃及） | `ar-EG` | Female | `ar-EG-SalmaNeural` | 常规 |
| 阿拉伯语（埃及） | `ar-EG` | 男 | `ar-EG-ShakirNeural` <sup>新建</sup> | 常规 |
| 阿拉伯语（沙特阿拉伯） | `ar-SA` | 女 | `ar-SA-ZariyahNeural` | 常规 |
| 阿拉伯语（沙特阿拉伯） | `ar-SA` | 男 | `ar-SA-HamedNeural` <sup>新建</sup> | 常规 |
| 保加利亚语(保加利亚) | `bg-BG` | 女 | `bg-BG-KalinaNeural` | 常规 |
| 保加利亚语(保加利亚) | `bg-BG` | 男 | `bg-BG-BorislavNeural` <sup>新建</sup> | 常规 |
| 加泰罗尼亚语(西班牙) | `ca-ES` | 女 | `ca-ES-AlbaNeural` | 常规 |
| 加泰罗尼亚语(西班牙) | `ca-ES` | Female | `ca-ES-JoanaNeural` <sup>新建</sup> | 常规 |
| 加泰罗尼亚语(西班牙) | `ca-ES` | 男 | `ca-ES-EnricNeural` <sup>新建</sup> | 常规 |
| 中文（粤语，繁体） | `zh-HK` | Female | `zh-HK-HiuGaaiNeural` | 常规 |
| 中文（粤语，繁体） | `zh-HK` | Female | `zh-HK-HiuMaanNeural` <sup>新建</sup> | 常规 |
| 中文(粤语，繁体) | `zh-HK` | 男 | `zh-HK-WanLungNeural` <sup>新建</sup> | 常规 |
| 中文（普通话，简体） | `zh-CN` | 女 | `zh-CN-XiaoxiaoNeural` | 常规，[使用 SSML](speech-synthesis-markup.md#adjust-speaking-styles) 提供多种语音风格  |
| 中文（普通话，简体） | `zh-CN` | 女 | `zh-CN-XiaoyouNeural` | 儿童语音，针对讲故事进行了优化 |
| 中文（普通话，简体） | `zh-CN` | 男 | `zh-CN-YunyangNeural` | 针对新闻阅读进行了优化，<br /> [使用 SSML](speech-synthesis-markup.md#adjust-speaking-styles) 提供多种语音风格 |
| 中文（普通话，简体） | `zh-CN` | 男 | `zh-CN-YunyeNeural` | 针对讲故事进行了优化  |
| 中文(台湾普通话) | `zh-TW` | Female | `zh-TW-HsiaoChenNeural` <sup>新建</sup> | 常规 |
| 中文(台湾普通话) | `zh-TW` | Female | `zh-TW-HsiaoYuNeural` | 常规 |
| 中文(台湾普通话) | `zh-TW` | 男 | `zh-TW-YunJheNeural` <sup>新建</sup> | 常规 |
| 克罗地亚语（克罗地亚） | `hr-HR` | 女 | `hr-HR-GabrijelaNeural` | 常规 |
| 克罗地亚语（克罗地亚） | `hr-HR` | 男 | `hr-HR-SreckoNeural` <sup>新建</sup> | 常规 |
| 捷克语（捷克） | `cs-CZ` | 女 | `cs-CZ-VlastaNeural` | 常规 |
| 捷克语（捷克） | `cs-CZ` | 男 | `cs-CZ-AntoninNeural` <sup>新建</sup> | 常规 |
| 丹麦语（丹麦） | `da-DK` | 女 | `da-DK-ChristelNeural` | 常规 |
| 丹麦语（丹麦） | `da-DK` | 男 | `da-DK-JeppeNeural` <sup>新建</sup> | 常规 |
| 荷兰语（荷兰） | `nl-NL` | 女 | `nl-NL-ColetteNeural` | 常规 |
| 荷兰语（荷兰） | `nl-NL` | Female | `nl-NL-FennaNeural` <sup>新建</sup> | 常规 |
| 荷兰语（荷兰） | `nl-NL` | 男 | `nl-NL-MaartenNeural` <sup>新建</sup> | 常规 |
| 英语（澳大利亚） | `en-AU` | Female | `en-AU-NatashaNeural` | 常规 |
| 英语（澳大利亚） | `en-AU` | 男 | `en-AU-WilliamNeural` | 常规 |
| 英语（加拿大） | `en-CA` | Female | `en-CA-ClaraNeural` | 常规 |
| 英语（加拿大） | `en-CA` | 男 | `en-CA-LiamNeural` <sup>新建</sup> | 常规 |
| 英语（印度） | `en-IN` | Female | `en-IN-NeerjaNeural` | 常规 |
| 英语（印度） | `en-IN` | 男 | `en-IN-PrabhatNeural` <sup>新建</sup> | 常规 |
| 英语（爱尔兰） | `en-IE` | 女 | `en-IE-EmilyNeural` | 常规 |
| 英语（爱尔兰） | `en-IE` | 男 | `en-IE-ConnorNeural` <sup>新建</sup> | 常规 |
| 英语（英国） | `en-GB` | 女 | `en-GB-LibbyNeural` | 常规 |
| 英语（英国） | `en-GB` | 女 | `en-GB-MiaNeural` | 常规 |
| 英语（英国） | `en-GB` | 男 | `en-GB-RyanNeural` | 常规 |
| 英语（美国） | `en-US` | 女 | `en-US-AriaNeural` | 常规，[使用 SSML](speech-synthesis-markup.md#adjust-speaking-styles) 提供多种语音风格  |
| 英语（美国） | `en-US` | 女 | `en-US-JennyNeural` | 常规 |
| 英语（美国） | `en-US` | 男 | `en-US-GuyNeural` | 常规 |
| 芬兰语（芬兰） | `fi-FI` | 女 | `fi-FI-NooraNeural` | 常规 |
| 芬兰语（芬兰） | `fi-FI` | Female | `fi-FI-SelmaNeural` <sup>新建</sup> | 常规 |
| 芬兰语（芬兰） | `fi-FI` | 男 | `fi-FI-HarriNeural` <sup>新建</sup> | 常规 |
| 法语（加拿大） | `fr-CA` | Female | `fr-CA-SylvieNeural` | 常规 |
| 法语（加拿大） | `fr-CA` | 男 | `fr-CA-JeanNeural` | 常规 |
| 法语（法国） | `fr-FR` | Female | `fr-FR-DeniseNeural` | 常规 |
| 法语（法国） | `fr-FR` | 男 | `fr-FR-HenriNeural` | 常规 |
| 法语（瑞士） | `fr-CH` | 女 | `fr-CH-ArianeNeural` | 常规 |
| 法语（瑞士） | `fr-CH` | 男 | `fr-CH-FabriceNeural` <sup>新建</sup> | 常规 |
| 德语（奥地利） | `de-AT` | 女 | `de-AT-IngridNeural` | 常规 |
| 德语（奥地利） | `de-AT` | 男 | `de-AT-JonasNeural` <sup>新建</sup> | 常规 |
| 德语（德国） | `de-DE` | Female | `de-DE-KatjaNeural` | 常规 |
| 德语（德国） | `de-DE` | 男 | `de-DE-ConradNeural` | 常规 |
| 德语（瑞士） | `de-CH` | 女 | `de-CH-LeniNeural` | 常规 |
| 德语（瑞士） | `de-CH` | 男 | `de-CH-JanNeural` <sup>新建</sup> | 常规 |
| 希腊语(希腊) | `el-GR` | 女 | `el-GR-AthinaNeural` | 常规 |
| 希腊语(希腊) | `el-GR` | 男 | `el-GR-NestorasNeural` <sup>新建</sup> | 常规 |
| 希伯来语（以色列） | `he-IL` | 女 | `he-IL-HilaNeural` | 常规 |
| 希伯来语（以色列） | `he-IL` | 男 | `he-IL-AvriNeural` <sup>新建</sup> | 常规 |
| 印地语（印度） | `hi-IN` | Female | `hi-IN-SwaraNeural` | 常规 |
| 印地语（印度） | `hi-IN` | 男 | `hi-IN-MadhurNeural` <sup>新建</sup> | 常规 |
| 匈牙利语(匈牙利) | `hu-HU` | 女 | `hu-HU-NoemiNeural` | 常规 |
| 匈牙利语(匈牙利) | `hu-HU` | 男 | `hu-HU-TamasNeural` <sup>新建</sup> | 常规 |
| 印度尼西亚语(印度尼西亚) | `id-ID` | Female | `id-ID-GadisNeural` <sup>新建</sup> | 常规 |
| 印度尼西亚语(印度尼西亚) | `id-ID` | 男 | `id-ID-ArdiNeural` | 常规 |
| 意大利语（意大利） | `it-IT` | Female | `it-IT-ElsaNeural` | 常规 |
| 意大利语（意大利） | `it-IT` | Female | `it-IT-IsabellaNeural` | 常规 |
| 意大利语（意大利） | `it-IT` | 男 | `it-IT-DiegoNeural` | 常规 |
| 日语（日本） | `ja-JP` | 女 | `ja-JP-NanamiNeural` | 常规 |
| 日语（日本） | `ja-JP` | 男 | `ja-JP-KeitaNeural` | 常规 |
| 韩语(韩国) | `ko-KR` | Female | `ko-KR-SunHiNeural` | 常规 |
| 韩语(韩国) | `ko-KR` | 男 | `ko-KR-InJoonNeural` | 常规 |
| 马来语（马来西亚） | `ms-MY` | 女 | `ms-MY-YasminNeural` | 常规 |
| 马来语（马来西亚） | `ms-MY` | 男 | `ms-MY-OsmanNeural` <sup>新建</sup> | 常规 |
| 挪威语（博克马尔语，挪威） | `nb-NO` | 女 | `nb-NO-IselinNeural` | 常规 |
| 挪威语（博克马尔语，挪威） | `nb-NO` | Female | `nb-NO-PernilleNeural` <sup>新建</sup> | 常规 |
| 挪威语（博克马尔语，挪威） | `nb-NO` | 男 | `nb-NO-FinnNeural` <sup>新建</sup> | 常规 |
| 波兰语（波兰） | `pl-PL` | Female | `pl-PL-AgnieszkaNeural` <sup>新建</sup> | 常规 |
| 波兰语（波兰） | `pl-PL` | 女 | `pl-PL-ZofiaNeural` | 常规 |
| 波兰语（波兰） | `pl-PL` | 男 | `pl-PL-MarekNeural` <sup>新建</sup> | 常规 |
| 葡萄牙语（巴西） | `pt-BR` | Female | `pt-BR-FranciscaNeural` | 常规，[使用 SSML](speech-synthesis-markup.md#adjust-speaking-styles) 提供多种语音风格  |
| 葡萄牙语（巴西） | `pt-BR` | 男 | `pt-BR-AntonioNeural` | 常规 |
| 葡萄牙语(葡萄牙) | `pt-PT` | Female | `pt-PT-FernandaNeural` | 常规 |
| 葡萄牙语(葡萄牙) | `pt-PT` | Female | `pt-PT-RaquelNeural` <sup>新建</sup> | 常规 |
| 葡萄牙语(葡萄牙) | `pt-PT` | 男 | `pt-PT-DuarteNeural` <sup>新建</sup> | 常规 |
| 罗马尼亚语(罗马尼亚) | `ro-RO` | 女 | `ro-RO-AlinaNeural` | 常规 |
| 罗马尼亚语(罗马尼亚) | `ro-RO` | 男 | `ro-RO-EmilNeural` <sup>新建</sup> | 常规 |
| 俄语（俄罗斯） | `ru-RU` | Female | `ru-RU-DariyaNeural` | 常规 |
| 俄语（俄罗斯） | `ru-RU` | Female | `ru-RU-SvetlanaNeural` <sup>新建</sup> | 常规 |
| 俄语（俄罗斯） | `ru-RU` | 男 | `ru-RU-DmitryNeural` <sup>新建</sup> | 常规 |
| 斯洛伐克语(斯洛伐克) | `sk-SK` | 女 | `sk-SK-ViktoriaNeural` | 常规 |
| 斯洛伐克语(斯洛伐克) | `sk-SK` | 男 | `sk-SK-LukasNeural` <sup>新建</sup> | 常规 |
| 斯洛文尼亚语(斯洛文尼亚) | `sl-SI` | 女 | `sl-SI-PetraNeural` | 常规 |
| 斯洛文尼亚语(斯洛文尼亚) | `sl-SI` | 男 | `sl-SI-RokNeural` <sup>新建</sup> | 常规 |
| 西班牙语（墨西哥） | `es-MX` | Female | `es-MX-DaliaNeural` | 常规 |
| 西班牙语（墨西哥） | `es-MX` | 男 | `es-MX-JorgeNeural` | 常规 |
| 西班牙语(西班牙) | `es-ES` | 女 | `es-ES-ElviraNeural` | 常规 |
| 西班牙语(西班牙) | `es-ES` | 男 | `es-ES-AlvaroNeural` | 常规 |
| 瑞典语（瑞典） | `sv-SE` | 女 | `sv-SE-HilleviNeural` | 常规 |
| 瑞典语（瑞典） | `sv-SE` | Female | `sv-SE-SofieNeural` <sup>新建</sup> | 常规 |
| 瑞典语（瑞典） | `sv-SE` | 男 | `sv-SE-MattiasNeural` <sup>新建</sup> | 常规 |
| 泰米尔语（印度） | `ta-IN` | 女 | `ta-IN-PallaviNeural` | 常规 |
| 泰米尔语（印度） | `ta-IN` | 男 | `ta-IN-ValluvarNeural` <sup>新建</sup> | 常规 |
| 泰卢固语（印度） | `te-IN` | Female | `te-IN-ShrutiNeural` | 常规 |
| 泰卢固语（印度） | `te-IN` | 男 | `te-IN-MohanNeural` <sup>新建</sup> | 常规 |
| 泰语（泰国） | `th-TH` | 女 | `th-TH-AcharaNeural` | 常规 |
| 泰语（泰国） | `th-TH` | 女 | `th-TH-PremwadeeNeural` | 常规 |
| 泰语（泰国） | `th-TH` | 男 | `th-TH-NiwatNeural` <sup>新建</sup> | 常规 |
| 土耳其语（土耳其） | `tr-TR` | Female | `tr-TR-EmelNeural` | 常规 |
| 土耳其语（土耳其） | `tr-TR` | 男 | `tr-TR-AhmetNeural` <sup>新建</sup> | 常规 |
| 越南语(越南) | `vi-VN` | 女 | `vi-VN-HoaiMyNeural` | 常规 |
| 越南语(越南) | `vi-VN` | 男 | `vi-VN-NamMinhNeural` <sup>新建</sup> | 常规 |

#### <a name="neural-voices-in-preview"></a>预览中的神经语音

以下神经声音在公共预览版中。 

| 语言                         | Locale  | 性别 | 语音名称                             | 风格支持 |
|----------------------------------|---------|--------|----------------------------------------|---------------|
| 中文（普通话，简体） | `zh-CN` | 女 | `zh-CN-XiaohanNeural` | 常规，[使用 SSML](speech-synthesis-markup.md#adjust-speaking-styles)提供多种样式 |
| 中文（普通话，简体） | `zh-CN` | 女 | `zh-CN-XiaomoNeural` | 一般，[使用 SSML](speech-synthesis-markup.md#adjust-speaking-styles)提供多种角色扮演和样式 |
| 中文（普通话，简体） | `zh-CN` | 女 | `zh-CN-XiaoruiNeural` | 高级语音，[使用 SSML](speech-synthesis-markup.md#adjust-speaking-styles)提供多种样式 |
| 中文（普通话，简体） | `zh-CN` | 女 | `zh-CN-XiaoxuanNeural` | 一般，[使用 SSML](speech-synthesis-markup.md#adjust-speaking-styles)提供多种角色扮演和样式 |
| 中文（普通话，简体） | `zh-CN` | 男   | `zh-CN-YunxiNeural` | 常规，[使用 SSML](speech-synthesis-markup.md#adjust-speaking-styles)提供多种样式 |
| 爱沙尼亚语(爱沙尼亚) | `et-EE` | 女 | `et-EE-AnuNeural` | 常规 |
| 爱沙尼亚语(爱沙尼亚) | `et-EE` | 男 | `et-EE-KertNeural` <sup>新建</sup> | 常规 |
| 爱尔兰语（爱尔兰） | `ga-IE` | 女 | `ga-IE-OrlaNeural` | 常规 |
| 爱尔兰语（爱尔兰） | `ga-IE` | 男 | `ga-IE-ColmNeural` <sup>新建</sup> | 常规 |
| 拉脱维亚语(拉脱维亚) | `lv-LV` | 女 | `lv-LV-EveritaNeural` | 常规 |
| 拉脱维亚语(拉脱维亚) | `lv-LV` | 男 | `lv-LV-NilsNeural` <sup>新建</sup> | 常规 |
| 立陶宛语(立陶宛) | `lt-LT` | 女 | `lt-LT-OnaNeural` | 常规 |
| 立陶宛语(立陶宛) | `lt-LT` | 男 | `lt-LT-LeonasNeural` <sup>新建</sup> | 常规 |
| 马耳他语（马耳他） | `mt-MT` | 女 | `mt-MT-GraceNeural` | 常规 |
| 马耳他语（马耳他） | `mt-MT` | 男 | `mt-MT-JosephNeural` <sup>新建</sup> | 常规 |

> [!IMPORTANT]
> 公开预览版中的语音仅适用于3个服务地区：美国东部、西欧和东南亚。

有关区域可用性的详细信息，请参阅[区域](regions.md#standard-and-neural-voices)。

若要了解如何配置和调整神经声音，如讲话风格，请参阅 [语音合成标记语言](speech-synthesis-markup.md#adjust-speaking-styles)。

> [!IMPORTANT]
> `en-US-JessaNeural` 语音已更改为 `en-US-AriaNeural`。 如果以前使用了“Jessa”，请转换为“Aria”。

> [!TIP]
> 可以继续在语音合成请求中使用完整的服务名称映射，如“Microsoft Server Speech Text to Speech Voice (en-US, AriaNeural)”。

### <a name="standard-voices"></a>标准语音

提供超过 75 种标准语音，涉及超过 45 种语言和区域设置，允许用户将文本转换为合成语音。 有关区域可用性的详细信息，请参阅[区域](regions.md#standard-and-neural-voices)。

> [!NOTE]
> 除了两个例外，标准语音从使用 16 khz 采样率的样本创建。
> en-US-AriaRUS 和 en-US-GuyRUS 语音也是从使用 24 khz 采样率的样本创建的。
> 合成时，所有声音都可以使用其他采样率进行上采样或下采样。

| 语言 | 区域设置 (BCP-47) | 性别 | 语音名称 |
|--|--|--|--|
| 阿拉伯语（阿拉伯） | `ar-EG` | Female | `ar-EG-Hoda`|
| 阿拉伯语（沙特阿拉伯） | `ar-SA` | 男 | `ar-SA-Naayf`|
| 保加利亚语(保加利亚) | `bg-BG` | 男 | `bg-BG-Ivan`|
| 加泰罗尼亚语(西班牙) | `ca-ES` | 女 | `ca-ES-HerenaRUS`|
| 中文(粤语，繁体) | `zh-HK` | 男 | `zh-HK-Danny`|
| 中文（粤语，繁体） | `zh-HK` | Female | `zh-HK-TracyRUS`|
| 中文（普通话，简体） | `zh-CN` | 女 | `zh-CN-HuihuiRUS`|
| 中文（普通话，简体） | `zh-CN` | 男 | `zh-CN-Kangkang`|
| 中文（普通话，简体） | `zh-CN` | 女 | `zh-CN-Yaoyao`|
| 中文(台湾普通话) |  `zh-TW` | Female | `zh-TW-HanHanRUS`|
| 中文(台湾普通话) |  `zh-TW` | Female | `zh-TW-Yating`|
| 中文(台湾普通话) |  `zh-TW` | 男 | `zh-TW-Zhiwei`|
| 克罗地亚语（克罗地亚） | `hr-HR` | 男 | `hr-HR-Matej`|
| 捷克语（捷克共和国） | `cs-CZ` | 男 | `cs-CZ-Jakub`|
| 丹麦语（丹麦） | `da-DK` | 女 | `da-DK-HelleRUS`|
| 荷兰语（荷兰） | `nl-NL` | Female | `nl-NL-HannaRUS`|
| 英语（澳大利亚） | `en-AU` | Female | `en-AU-Catherine`|
| 英语（澳大利亚） | `en-AU` | Female | `en-AU-HayleyRUS`|
| 英语（加拿大） | `en-CA` | Female | `en-CA-HeatherRUS`|
| 英语（加拿大） | `en-CA` | Female | `en-CA-Linda`|
| 英语（印度） | `en-IN` | Female | `en-IN-Heera`|
| 英语（印度） | `en-IN` | Female | `en-IN-PriyaRUS`|
| 英语（印度） | `en-IN` | 男 | `en-IN-Ravi`|
| 英语（爱尔兰） | `en-IE` | 男 | `en-IE-Sean`|
| 英语（英国） | `en-GB` | 男 | `en-GB-George`|
| 英语（英国） | `en-GB` | Female | `en-GB-HazelRUS`|
| 英语（英国） | `en-GB` | Female | `en-GB-Susan`|
| 英语（美国） | `en-US` | 男 | `en-US-BenjaminRUS`|
| 英语（美国） | `en-US` | 男 | `en-US-GuyRUS`|
| 英语（美国） | `en-US` | Female | `en-US-AriaRUS`|
| 英语（美国） | `en-US` | 女 | `en-US-ZiraRUS`|
| 芬兰语（芬兰） | `fi-FI` | 女 | `fi-FI-HeidiRUS`|
| 法语（加拿大） | `fr-CA` | Female | `fr-CA-Caroline`|
| 法语（加拿大） | `fr-CA` | Female | `fr-CA-HarmonieRUS`|
| 法语（法国） | `fr-FR` | Female | `fr-FR-HortenseRUS`|
| 法语（法国） | `fr-FR` | Female | `fr-FR-Julie`|
| 法语（法国） | `fr-FR` | 男 | `fr-FR-Paul`|
| 法语（瑞士） | `fr-CH` | 男 | `fr-CH-Guillaume`|
| 德语（奥地利） | `de-AT` | 男 | `de-AT-Michael`|
| 德语（德国） | `de-DE` | Female | `de-DE-HeddaRUS`|
| 德语（德国） | `de-DE` | 男 | `de-DE-Stefan`|
| 德语（瑞士） | `de-CH` | 男 | `de-CH-Karsten`|
| 希腊语(希腊) | `el-GR` | 男 | `el-GR-Stefanos`|
| 希伯来语（以色列） | `he-IL` | 男 | `he-IL-Asaf`|
| 印地语（印度） | `hi-IN` | 男 | `hi-IN-Hemant`|
| 印地语（印度） | `hi-IN` | Female | `hi-IN-Kalpana`|
| 匈牙利语(匈牙利) | `hu-HU` | 男 | `hu-HU-Szabolcs`|
| 印度尼西亚语(印度尼西亚) | `id-ID` | 男 | `id-ID-Andika`|
| 意大利语（意大利） | `it-IT` | 男 | `it-IT-Cosimo`|
| 意大利语（意大利） | `it-IT` | Female | `it-IT-LuciaRUS`|
| 日语（日本） | `ja-JP` | 女 | `ja-JP-Ayumi`|
| 日语（日本） | `ja-JP` | 女 | `ja-JP-HarukaRUS`|
| 日语（日本） | `ja-JP` | 男 | `ja-JP-Ichiro`|
| 韩语(韩国) | `ko-KR` | Female | `ko-KR-HeamiRUS`|
| 马来语（马来西亚） | `ms-MY` | 男 | `ms-MY-Rizwan`|
| 挪威语（博克马尔语，挪威） | `nb-NO` | 女 | `nb-NO-HuldaRUS`|
| 波兰语（波兰） | `pl-PL` | 女 | `pl-PL-PaulinaRUS`|
| 葡萄牙语（巴西） | `pt-BR` | 男 | `pt-BR-Daniel`|
| 葡萄牙语（巴西） | `pt-BR` | 女 | `pt-BR-HeloisaRUS`|
| 葡萄牙语(葡萄牙) | `pt-PT` | 女 | `pt-PT-HeliaRUS`|
| 罗马尼亚语（罗马尼亚） | `ro-RO` | 男 | `ro-RO-Andrei`|
| 俄语（俄罗斯） | `ru-RU` | Female | `ru-RU-EkaterinaRUS`|
| 俄语（俄罗斯） | `ru-RU` | Female | `ru-RU-Irina`|
| 俄语（俄罗斯） | `ru-RU` | 男 | `ru-RU-Pavel`|
| 斯洛伐克语（斯洛伐克） | `sk-SK` | 男 | `sk-SK-Filip`|
| 斯洛文尼亚语（斯洛文尼亚） | `sl-SI` | 男 | `sl-SI-Lado`|
| 西班牙语（墨西哥） | `es-MX` | Female | `es-MX-HildaRUS`|
| 西班牙语（墨西哥） | `es-MX` | 男 | `es-MX-Raul`|
| 西班牙语(西班牙) | `es-ES` | 女 | `es-ES-HelenaRUS`|
| 西班牙语(西班牙) | `es-ES` | 女 | `es-ES-Laura`|
| 西班牙语(西班牙) | `es-ES` | 男 | `es-ES-Pablo`|
| 瑞典语（瑞典） | `sv-SE` | 女 | `sv-SE-HedvigRUS`|
| 泰米尔语（印度） | `ta-IN` | 男 | `ta-IN-Valluvar`|
| 泰卢固语（印度） | `te-IN` | 女 | `te-IN-Chitra`|
| 泰语（泰国） | `th-TH` | 男 | `th-TH-Pattara`|
| 土耳其语（土耳其） | `tr-TR` | 女 | `tr-TR-SedaRUS`|
| 越南语(越南) | `vi-VN` | 男 | `vi-VN-An` |

> [!IMPORTANT]
> `en-US-Jessa` 语音已更改为 `en-US-Aria`。 如果以前使用了“Jessa”，请转换为“Aria”。

> [!TIP]
> 可以继续在语音合成请求中使用完整的服务名称映射，如“Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)”。

### <a name="customization"></a>自定义

自定义语音在标准层和神经层中提供。 对于这两个层，支持的语言不同。 

| 语言 | Locale | Standard | 神经 |
|--|--|--|--|
| 中文（普通话，简体） | `zh-CN` | 是 | 是 |
| 中文 (普通话，简体中文) ，英语双语 | `zh-CN` 双语 | 是 | 是 |
| 英语（澳大利亚） | `en-AU` | 否 | 是 |
| 英语（印度） | `en-IN` | 是 | 是 |
| 英语（英国） | `en-GB` | 是 | 是 |
| 英语（美国） | `en-US` | 是 | 是 |
| 法语（加拿大） | `fr-CA` | 否 | 是 |
| 法语（法国） | `fr-FR` | 是 | 是 |
| 德语（德国） | `de-DE` | 是 | 是 |
| 意大利语（意大利） | `it-IT` | 是 | 是 |
| 日语（日本） | `ja-JP` | 否 | 是 |
| 韩语(韩国) | `ko-KR` | 否 | 是 |
| 葡萄牙语（巴西） | `pt-BR` | 是 | 是 |
| 西班牙语（墨西哥） | `es-MX` | 是 | 是 |
| 西班牙语(西班牙) | `es-ES` | 否 | 是 |

选择与训练自定义语音模型所需的训练数据相匹配的正确区域设置。 例如，如果你的录音数据是以带英国口音的英语说出的，请选择 `en-GB`。

> [!NOTE]
> 除了中英双语模型之外，我们在自定义语音中不支持其他双语模型训练。 如果要训练一种也可以说英语的中文语音，请选择“中英双语”。 使用 standard 方法 Chinese-English 双语模型训练仅适用于北欧和美国中北部。 英国南部和美国东部提供自定义的神经语音培训。

## <a name="speech-translation"></a>语音翻译

**语音翻译** API 支持使用不同的语言进行语音转语音和语音转文本的翻译。 源语言必须始终来自“语音转文本”语言表。 可用的目标语言取决于翻译目标是语音还是文本。 可以将传入的语音翻译成 [60 种以上的语言](https://www.microsoft.com/translator/business/languages/)。 这些语言的子集可用于[语音合成](language-support.md#text-languages)。

### <a name="text-languages"></a>文本语言

| 文本语言           | 语言代码 |
|:------------------------|:-------------:|
| 南非荷兰语               | `af`          |
| 阿拉伯语                  | `ar`          |
| Bangla                  | `bn`          |
| 波斯尼亚语(拉丁语系)         | `bs`          |
| 保加利亚语               | `bg`          |
| 粤语(繁体) | `yue`         |
| 加泰罗尼亚语                 | `ca`          |
| 简体中文      | `zh-Hans`     |
| 中文(繁体)     | `zh-Hant`     |
| 克罗地亚语                | `hr`          |
| 捷克语                   | `cs`          |
| 丹麦语                  | `da`          |
| 荷兰语                   | `nl`          |
| 英语                 | `en`          |
| 爱沙尼亚语                | `et`          |
| 斐济语                  | `fj`          |
| 菲律宾语                | `fil`         |
| 芬兰语                 | `fi`          |
| 法语                  | `fr`          |
| 德语                  | `de`          |
| 希腊语                   | `el`          |
| 古吉拉特语                | `gu`          |
| 海地克里奥尔语          | `ht`          |
| 希伯来语                  | `he`          |
| Hindi                   | `hi`          |
| 白苗文               | `mww`         |
| 匈牙利语               | `hu`          |
| 印度尼西亚语              | `id`          |
| 爱尔兰语                   | `ga`          |
| 意大利语                 | `it`          |
| 日语                | `ja`          |
| 卡纳达语                 | `kn`          |
| 斯瓦希里语               | `sw`          |
| 克林贡语                 | `tlh-Latn`    |
| 克林贡语(plqaD)         | `tlh-Piqd`    |
| 韩语                  | `ko`          |
| 拉脱维亚语                 | `lv`          |
| 立陶宛语              | `lt`          |
| 马达加斯加语                | `mg`          |
| 马来语                   | `ms`          |
| 马拉雅拉姆语               | `ml`          |
| 马耳他语                 | `mt`          |
| 毛利语                   | `mi`          |
| 马拉地语                 | `mr`          |
| 挪威语               | `nb`          |
| 波斯语                 | `fa`          |
| 波兰语                  | `pl`          |
| 葡萄牙语（巴西）     | `pt-br`       |
| 葡萄牙语(葡萄牙)   | `pt-pt`       |
| 旁遮普语                 | `pa`          |
| 克雷塔罗奥托米语         | `otq`         |
| 罗马尼亚语                | `ro`          |
| 俄语                 | `ru`          |
| 萨摩亚语                  | `sm`          |
| 塞尔维亚语（西里尔）      | `sr-Cyrl`     |
| 塞尔维亚语（拉丁）         | `sr-Latn`     |
| 斯洛伐克语                  | `sk`          |
| 斯洛文尼亚语               | `sl`          |
| 西班牙语                 | `es`          |
| 瑞典语                 | `sv`          |
| 塔希提语                | `ty`          |
| 泰米尔语                   | `ta`          |
| 泰卢固语                  | `te`          |
| 泰语                    | `th`          |
| 汤加语                  | `to`          |
| 土耳其语                 | `tr`          |
| 乌克兰语               | `uk`          |
| 乌尔都语                    | `ur`          |
| 越南语              | `vi`          |
| 威尔士语                   | `cy`          |
| 尤卡坦玛雅语            | `yua`         |

## <a name="speaker-recognition"></a>说话人识别

请参阅下表，了解各种说话人识别 API 支持的语言。 请参阅[概述](speaker-recognition-overview.md)，了解有关说话人识别的其他信息。

| 语言 | 区域设置 (BCP-47) | 依赖于文本的验证 | 独立于文本的验证 | 独立于文本的识别 |
|----|----|----|----|----|
|英语（美国）  |  zh-CN  |  是  |  是  |  是 |
|中文(普通话，简体) | zh-CN     |     不适用 |     是 |     是|
|英语（澳大利亚）     | en-AU     | 不适用     | 是     | 是|
|英语（加拿大）     | en-CA     | 不适用 |     是 |     是|
|英语(英国)     | en-GB     | 不适用     | 是     | 是|
|法语（加拿大）     | fr-CA     | 不适用     | 是 |     是|
|法语（法国）     | fr-FR     | 不适用     | 是     | 是|
|德语（德国）     | de-DE     | 不适用     | 是     | 是|
|意大利语 | it-IT     |     不适用     | 是 |     是|
|日语     | ja-JP | 不适用     | 是     | 是|
|葡萄牙语（巴西） | pt-BR |     不适用 |     是 |     是|
|西班牙语(墨西哥)     | es-MX     | 不适用 |     是 |     是|
|西班牙语(西班牙)     | es-ES | 不适用     | 是 |     是|

## <a name="next-steps"></a>后续步骤

* [创建免费 Azure 帐户](https://azure.microsoft.com/free/cognitive-services/)
* [了解如何在 C# 中识别语音](./get-started-speech-to-text.md?pivots=programming-language-chsarp)