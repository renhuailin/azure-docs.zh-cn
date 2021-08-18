---
title: 语音合成标记语言 (SSML) - 语音服务
titleSuffix: Azure Cognitive Services
description: 使用语音合成标记语言来控制文本到语音转换中的发音和韵律。
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: lajanuar
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: f69ea6fcc48cf12b012e15ab7ff1fd2b569c0889
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122071527"
---
# <a name="improve-synthesis-with-speech-synthesis-markup-language-ssml"></a>通过语音合成标记语言 (SSML) 改善合成

语音合成标记语言 (SSML) 是一种基于 XML 的标记语言，可让开发人员指定如何使用文本转语音服务将输入文本转换为合成语音。 与纯文本相比，SSML 可让开发人员微调音节、发音、语速、音量以及文本转语音输出的其他属性。 SSML 可自动处理正常的停顿（例如，在句号后面暂停片刻），或者在以问号结尾的句子中使用正确的音调。

SSML 的语音服务实现基于万维网联合会的[语音合成标记语言版本 1.0](https://www.w3.org/TR/speech-synthesis)。

> [!IMPORTANT]
> 中文、日语和韩语字符按两个字符计费。 有关详细信息，请参阅[定价](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)。

## <a name="neural-and-custom-voices"></a>神经和自定义语音

使用类似于人的神经语音，或创建产品或品牌所特有的你自己的自定义语音。 有关支持的语言、区域设置和语音的完整列表，请参阅[语言支持](language-support.md)。 若要详细了解神经和自定义语音，请参阅[文本转语音概述](text-to-speech.md)。


> [!NOTE]
> 可以使用[“文本转语音”页面](https://azure.microsoft.com/services/cognitive-services/text-to-speech/#features)听到不同风格和音调的声音阅读示例文本。


## <a name="special-characters"></a>特殊字符

使用 SSML 时请注意，特殊字符（例如引号、撇号和括号）必须经过转义。 有关详细信息，请参阅[可扩展标记语言 (XML) 1.0：附录 D](https://www.w3.org/TR/xml/#sec-entexpand)。

## <a name="supported-ssml-elements"></a>支持的 SSML 元素

每个 SSML 文档是使用 SSML 元素（或标记）创建的。 这些元素用于调整音节、韵律、音量等。 以下部分详细说明了每个元素的用法，以及该元素是必需的还是可选的。

> [!IMPORTANT]
> 不要忘记将属性值括在双引号中。 适当格式的有效 XML 的标准要求将属性值括在双引号中。 例如，`<prosody volume="90">` 是适当格式的有效元素，而 `<prosody volume=90>` 则不是。 SSML 可能无法识别未括在引号中的属性值。

## <a name="create-an-ssml-document"></a>创建 SSML 文档

`speak` 是根元素，对于所有 SSML 文档都是 **必需的**。 `speak` 元素包含重要信息，例如版本、语言和标记词汇定义。

**语法**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="string"></speak>
```

**属性**

| 属性 | 说明 | 必需/可选 |
|-----------|-------------|---------------------|
| `version` | 指示用于解释文档标记的 SSML 规范的版本。 当前版本为 1.0。 | 必须 |
| `xml:lang` | 指定根文档的语言。 该值可以包含小写的双字母语言代码（例如 `en`），或者语言代码加上大写的国家/地区代码（例如 `en-US`）。 | 必须 |
| `xmlns` | 指定文档的 URI，用于定义 SSML 文档的标记词汇（元素类型和属性名称）。 当前 URI 为 http://www.w3.org/2001/10/synthesis 。 | 必须 |

## <a name="choose-a-voice-for-text-to-speech"></a>选择文本转语音所用的语音

`voice` 元素是必需的。 它用于指定文本转语音所用的语音。

**语法**

```xml
<voice name="string">
    This text will get converted into synthesized speech.
</voice>
```

**属性**

| 属性 | 说明 | 必需/可选 |
|-----------|-------------|---------------------|
| `name` | 标识用于文本转语音输出的语音。 有关支持的语音的完整列表，请参阅[语言支持](language-support.md#text-to-speech)。 | 必须 |

**示例**

> [!NOTE]
> 本示例使用 `en-US-JennyNeural` 语音。 有关支持的语音的完整列表，请参阅[语言支持](language-support.md#text-to-speech)。

```XML
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        This is the text that is spoken.
    </voice>
</speak>
```

## <a name="use-multiple-voices"></a>使用多个语音

在 `speak` 元素中，可为文本转语音输出指定多种语音。 这些语音可以采用不同的语言。 对于每种语音，必须将文本包装在 `voice` 元素中。

**属性**

| 属性 | 说明 | 必需/可选 |
|-----------|-------------|---------------------|
| `name` | 标识用于文本转语音输出的语音。 有关支持的语音的完整列表，请参阅[语言支持](language-support.md#text-to-speech)。 | 必须 |

**示例**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        Good morning!
    </voice>
    <voice name="en-US-GuyNeural">
        Good morning to you too Jenny!
    </voice>
</speak>
```

## <a name="adjust-speaking-styles"></a>调整讲话风格

默认情况下，对于神经语音，文本转语音服务使用中性讲话风格来合成文本。 你可以调整说话风格来表达不同的情感（例如愉悦、同情和冷静），或使用 `mstts:express-as` 元素针对不同场景（例如自定义服务、新闻广播和语音助手）优化语音。 这是语音服务特有的可选元素。

目前以下神经语音支持讲话风格调整：
* `en-US-AriaNeural`
* `en-US-JennyNeural`
* `en-US-GuyNeural`
* `pt-BR-FranciscaNeural`
* `zh-CN-XiaoxiaoNeural`
* `zh-CN-YunyangNeural`
* `zh-CN-YunyeNeural`
* `zh-CN-YunxiNeural`
* `zh-CN-XiaohanNeural`
* `zh-CN-XiaomoNeural`
* `zh-CN-XiaoxuanNeural`
* `zh-CN-XiaoruiNeural`

可进一步更改说话风格的强度，更好地适应你的使用场景。 可以使用 `styledegree` 指定更强或更柔和的风格，使语音更具表现力或更柔和。 目前，中文（普通话，简体）神经语音支持说话风格调整。

除了调整说话风格和风格程度，还可调整 `role` 参数，使语音模拟不同的年龄和性别。 例如，男性语音可以提高音调和改变语调来模拟女性语音，但语音名称不会更改。 目前，这些中文（普通话，简体）神经语音支持角色调整：
* `zh-CN-XiaomoNeural`
* `zh-CN-XiaoxuanNeural`

上述更改将在句子级别应用，风格和角色扮演因语音而异。 如果某种风格或角色扮演不受支持，则该服务将以默认的中性讲话方式返回语音。 通过[语音列表 API](rest-text-to-speech.md#get-a-list-of-voices) 或免代码[音频内容创建](https://aka.ms/audiocontentcreation)平台可查看每种语音支持的风格和角色。

**语法**

```xml
<mstts:express-as style="string"></mstts:express-as>
```
```xml
<mstts:express-as style="string" styledegree="value"></mstts:express-as>
```
```xml
<mstts:express-as role="string" style="string"></mstts:express-as>
```
> [!NOTE]
> 目前，`styledegree` 仅支持中文（普通话，简体）神经语音。 `role` 仅支持 zh-CN-XiaomoNeural 和 zh-CN-XiaoxuanNeural。

**属性**

| 属性 | 说明 | 必需/可选 |
|-----------|-------------|---------------------|
| `style` | 指定讲话风格。 目前，讲话风格特定于语音。 | 如果调整神经语音的讲话风格，则此属性是必需的。 如果使用 `mstts:express-as`，则必须提供风格。 如果提供无效的值，将忽略此元素。 |
| `styledegree` | 指定说话风格的强度。 接受的值：0.01 到 2（含边界值）。 默认值为 1，表示预定义的风格强度。 最小单位为 0.01，表示略倾向于目标风格。 值为 2 表示是默认风格强度的两倍。  | 可选（目前，`styledegree` 仅支持中文（普通话，简体）神经语音。）|
| `role` | 指定讲话角色扮演。 语音将充当不同的年龄和性别，但语音名称不会更改。  | 可选（`role` 仅支持 zh-CN-XiaomoNeural 和 zh-CN-XiaoxuanNeural。）|

参考下表来确定每种神经语音支持的讲话风格。

| 语音                   | Style                     | 说明                                                 |
|-------------------------|---------------------------|-------------------------------------------------------------|
| `en-US-AriaNeural`      | `style="newscast-formal"` | 以正式、自信和权威的语气发布新闻 |
|                         | `style="newscast-casual"` | 以通用、随意的语气发布一般新闻        |
|                         | `style="narration-professional"` | 以专业、客观的语气朗读内容        |
|                         | `style="customerservice"` | 以友好热情的语气为客户提供支持  |
|                         | `style="chat"`            | 表达轻松随意的语气                         |
|                         | `style="cheerful"`        | 表达积极愉快的语气                         |
|                         | `style="empathetic"`      | 表达关心和理解               |
| `en-US-JennyNeural`     | `style="customerservice"` | 以友好热情的语气为客户提供支持  |
|                         | `style="chat"`            | 表达轻松随意的语气                         |
|                         | `style="assistant"`       | 以热情而轻松的语气对数字助理讲话    |
|                         | `style="newscast"`        | 以通用、随意的语气发布一般新闻   |
| `en-US-GuyNeural`       | `style="newscast"`        | 以正式专业的语气叙述新闻 |
| `pt-BR-FranciscaNeural` | `style="calm"`            | 以沉着冷静的态度说话。 语气、音调、韵律与其他语音类型相比要统一得多。                                |
| `zh-CN-XiaoxiaoNeural`  | `style="newscast"`        | 以正式专业的语气叙述新闻 |
|                         | `style="customerservice"` | 以友好热情的语气为客户提供支持  |
|                         | `style="assistant"`       | 以热情而轻松的语气对数字助理讲话    |
|                         | `style="chat"`            | 以轻松、随意的语气闲聊           |
|                         | `style="calm"`            | 以沉着冷静的态度说话。 语气、音调、韵律与其他语音类型相比要统一得多。                                |
|                         | `style="cheerful"`        | 以较高的音调和音量表达欢快、热情的语气                         |
|                         | `style="sad"`             | 以较高的音调、较低的强度和较低的音量表达悲伤的语气。 这种情绪的常见特征是说话时呜咽或哭泣。            |
|                         | `style="angry"`           | 以较低的音调、较高的强度和较高的音量来表达恼怒的语气。 说话者处于愤怒、生气和被冒犯的状态。       |
|                         | `style="fearful"`         | 以较高的音调、较高的音量和较快的语速来表达恐惧、紧张的语气。 说话者处于紧张和不安的状态。                          |
|                         | `style="disgruntled"`     | 表达轻蔑和抱怨的语气。 这种情绪的语音表现出不悦和蔑视。              |
|                         | `style="serious"`         | 表达严肃和命令的语气。 说话者的声音通常比较僵硬，节奏也不那么轻松。          |
|                         | `style="affectionate"`    | 以较高的音调和音量表达温暖而亲切的语气。 说话者处于吸引听众注意力的状态。 说话者的“个性”往往是讨人喜欢的。          |
|                         | `style="gentle"`          | 以较低的音调和音量表达温和、礼貌和愉快的语气         |
|                         | `style="lyrical"`         | 以优美又带感伤的方式表达情感         |
| `zh-CN-YunyangNeural`   | `style="customerservice"` | 以友好热情的语气为客户提供支持  |
| `zh-CN-YunyeNeural`     | `style="calm"`            | 以沉着冷静的态度说话。 语气、音调、韵律与其他语音类型相比要统一得多。    |
|                         | `style="cheerful"`        | 以较高的音调和音量表达欢快、热情的语气                         |
|                         | `style="sad"`             | 以较高的音调、较低的强度和较低的音量表达悲伤的语气。 这种情绪的常见特征是说话时呜咽或哭泣。            |
|                         | `style="angry"`           | 以较低的音调、较高的强度和较高的音量来表达恼怒的语气。 说话者处于愤怒、生气和被冒犯的状态。       |
|                         | `style="fearful"`         | 以较高的音调、较高的音量和较快的语速来表达恐惧、紧张的语气。 说话者处于紧张和不安的状态。                          |
|                         | `style="disgruntled"`     | 表达轻蔑和抱怨的语气。 这种情绪的语音表现出不悦和蔑视。              |
|                         | `style="serious"`         | 表达严肃和命令的语气。 说话者的声音通常比较僵硬，节奏也不那么轻松。          |
|   `zh-CN-YunxiNeural`   | `style="assistant"`       | 以热情而轻松的语气对数字助理讲话    |
|                         | `style="cheerful"`        | 以较高的音调和音量表达欢快、热情的语气                         |
|                         | `style="sad"`             | 以较高的音调、较低的强度和较低的音量表达悲伤的语气。 这种情绪的常见特征是说话时呜咽或哭泣。            |
|                         | `style="angry"`           | 以较低的音调、较高的强度和较高的音量来表达恼怒的语气。 说话者处于愤怒、生气和被冒犯的状态。       |
|                         | `style="fearful"`         | 以较高的音调、较高的音量和较快的语速来表达恐惧、紧张的语气。 说话者处于紧张和不安的状态。                          |
|                         | `style="disgruntled"`     | 表达轻蔑和抱怨的语气。 这种情绪的语音表现出不悦和蔑视。              |
|                         | `style="serious"`         | 表达严肃和命令的语气。 说话者的声音通常比较僵硬，节奏也不那么轻松。    |
|                         | `style="depressed"`       | 调低音调和音量来表达忧郁、沮丧的语气    |
|                         | `style="embarrassed"`     | 在说话者感到不舒适时表达不确定、犹豫的语气   |
| `zh-CN-XiaohanNeural`   | `style="cheerful"`        | 以较高的音调和音量表达欢快、热情的语气                         |
|                         | `style="sad"`             | 以较高的音调、较低的强度和较低的音量表达悲伤的语气。 这种情绪的常见特征是说话时呜咽或哭泣。            |
|                         | `style="angry"`           | 以较低的音调、较高的强度和较高的音量来表达恼怒的语气。 说话者处于愤怒、生气和被冒犯的状态。       |
|                         | `style="fearful"`         | 以较高的音调、较高的音量和较快的语速来表达恐惧、紧张的语气。 说话者处于紧张和不安的状态。                          |
|                         | `style="disgruntled"`     | 表达轻蔑和抱怨的语气。 这种情绪的语音表现出不悦和蔑视。              |
|                         | `style="serious"`         | 表达严肃和命令的语气。 说话者的声音通常比较僵硬，节奏也不那么轻松。    |
|                         | `style="embarrassed"`     | 在说话者感到不舒适时表达不确定、犹豫的语气   |
|                         | `style="affectionate"`    | 以较高的音调和音量表达温暖而亲切的语气。 说话者处于吸引听众注意力的状态。 说话者的“个性”往往是讨人喜欢的。          |
|                         | `style="gentle"`          | 以较低的音调和音量表达温和、礼貌和愉快的语气         |
| `zh-CN-XiaomoNeural`    | `style="calm"`            | 以沉着冷静的态度说话。 语气、音调、韵律与其他语音类型相比要统一得多。                         |
|                         | `style="cheerful"`        | 以较高的音调和音量表达欢快、热情的语气                 |
|                         | `style="angry"`           | 以较低的音调、较高的强度和较高的音量来表达恼怒的语气。 说话者处于愤怒、生气和被冒犯的状态。       |
|                         | `style="fearful"`         | 以较高的音调、较高的音量和较快的语速来表达恐惧、紧张的语气。 说话者处于紧张和不安的状态。                       |
|                         | `style="disgruntled"`     | 表达轻蔑和抱怨的语气。 这种情绪的语音表现出不悦和蔑视。         |
|                         | `style="serious"`         | 表达严肃和命令的语气。 说话者的声音通常比较僵硬，节奏也不那么轻松。  |
|                         | `style="depressed"`       | 调低音调和音量来表达忧郁、沮丧的语气    |
|                         | `style="gentle"`          | 以较低的音调和音量表达温和、礼貌和愉快的语气         |
| `zh-CN-XiaoxuanNeural`  | `style="calm"`            | 以沉着冷静的态度说话。 语气、音调、韵律与其他语音类型相比要统一得多。                         |
|                         | `style="cheerful"`        | 以较高的音调和音量表达欢快、热情的语气                              |
|                         | `style="angry"`           | 以较低的音调、较高的强度和较高的音量来表达恼怒的语气。 说话者处于愤怒、生气和被冒犯的状态。       |
|                         | `style="fearful"`         | 以较高的音调、较高的音量和较快的语速来表达恐惧、紧张的语气。 说话者处于紧张和不安的状态。                       |
|                         | `style="disgruntled"`     | 表达轻蔑和抱怨的语气。 这种情绪的语音表现出不悦和蔑视。         |
|                         | `style="serious"`         | 表达严肃和命令的语气。 说话者的声音通常比较僵硬，节奏也不那么轻松。  |
|                         | `style="depressed"`       | 调低音调和音量来表达忧郁、沮丧的语气    |
|                         | `style="gentle"`          | 以较低的音调和音量表达温和、礼貌和愉快的语气         |
| `zh-CN-XiaoruiNeural`   | `style="sad"`             | 以较高的音调、较低的强度和较低的音量表达悲伤的语气。 这种情绪的常见特征是说话时呜咽或哭泣。         |
|                         | `style="angry"`           | 以较低的音调、较高的强度和较高的音量来表达恼怒的语气。 说话者处于愤怒、生气和被冒犯的状态。       |
|                         | `style="fearful"`         | 以较高的音调、较高的音量和较快的语速来表达恐惧、紧张的语气。 说话者处于紧张和不安的状态。                       |

使用此表检查支持的角色及其定义。

|角色                     | 说明                |
|-------------------------|----------------------------|
|`role="Girl"`            | 该语音模拟女孩。 |
|`role="Boy"`             | 该语音模拟男孩。 |
|`role="YoungAdultFemale"`| 该语音模拟年轻成年女性。|
|`role="YoungAdultMale"`  | 该语音模拟年轻成年男性。|
|`role="OlderAdultFemale"`| 该语音模拟年长的成年女性。|
|`role="OlderAdultMale"`  | 该语音模拟年长的成年男性。|
|`role="SeniorFemale"`    | 该语音模拟老年女性。|
|`role="SeniorMale"`      | 该语音模拟老年男性。|


**示例**

此 SSML 代码片段演示如何使用 `<mstts:express-as>` 元素将讲话风格更改为 `cheerful`。

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis"
       xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
    <voice name="en-US-AriaNeural">
        <mstts:express-as style="cheerful">
            That'd be just amazing!
        </mstts:express-as>
    </voice>
</speak>
```

此 SSML 片段说明了如何使用 `styledegree` 属性更改 XiaoxiaoNeural 说话风格的强度。
```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis"
       xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="zh-CN">
    <voice name="zh-CN-XiaoxiaoNeural">
        <mstts:express-as style="sad" styledegree="2">
            快走吧，路上一定要注意安全，早去早回。
        </mstts:express-as>
    </voice>
</speak>
```

此 SSML 片段说明了如何使用 `role` 属性来更改 XiaomoNeural 的角色扮演。
```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis"
       xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="zh-CN">
    <voice name="zh-CN-XiaomoNeural">
        女儿看见父亲走了进来，问道：
        <mstts:express-as role="YoungAdultFemale" style="calm">
            “您来的挺快的，怎么过来的？”
        </mstts:express-as>
        父亲放下手提包，说：
        <mstts:express-as role="OlderAdultMale" style="calm">
            “刚打车过来的，路上还挺顺畅。”
        </mstts:express-as>
    </voice>
</speak>
```

## <a name="adjust-speaking-languages"></a>调整讲话语言

可以调整神经语音的讲话语言。
使用 `<lang xml:lang>` 元素，使一种语音能够流利地讲不同的语言（例如英语、西班牙语和中文）。 这是语音服务特有的可选元素。 如果没有此元素，语音将讲其主要语言。
目前，以下神经语音支持讲话语言调整：`en-US-JennyMultilingualNeural`。 上述更改将在句子级别和单词级别应用。 如果不支持某语言，则服务不会返回音频流。

> [!NOTE]
> 当前，`<lang xml:lang>` 元素与 `prosody` 和 `break` 元素不兼容，你无法调整此元素中的暂停和韵律，如音调、调型、速率、持续时间和音量。

**语法**

```xml
<lang xml:lang="string"></lang>
```

**属性**

| 属性 | 说明 | 必需/可选 |
|-----------|-------------|---------------------|
| `lang` | 指定讲话语言。 目前，讲不同的语言是特定于语音的。 | 如果调整神经语音的讲话语言，则为必需项。 如果使用 `lang xml:lang`，则必须提供区域设置。 |

使用此表来确定每种神经语音支持哪些讲话语言。

| 语音                            | 区域设置语言           | 说明                                                 |
|----------------------------------|---------------------------|-------------------------------------------------------------|
| `en-US-JennyMultilingualNeural`  | `lang="en-US"`            | 讲英语（美国）区域设置，这是此语音的主要区域设置 |
|                                  | `lang="en-CA"`            | 讲英语（加拿大）区域设置语言                                  |
|                                  | `lang="en-AU"`            | 讲英语（澳大利亚）区域设置语言                                  |
|                                  | `lang="en-GB"`            | 讲英语（英国）区域设置语言                                  |
|                                  | `lang="de-DE"`            | 讲德语（德国）区域设置语言                                  |
|                                  | `lang="fr-FR"`            | 讲法语（法国）区域设置语言                                  |
|                                  | `lang="fr-CA"`            | 讲法语（加拿大）区域设置语言                                  |
|                                  | `lang="es-ES"`            | 讲西班牙语（西班牙）区域设置语言                                  |
|                                  | `lang="es-MX"`            | 讲西班牙语（墨西哥）区域设置语言                                  |
|                                  | `lang="zh-CN"`            | 讲中文（中国）区域设置语言                                  |
|                                  | `lang="ko-KR"`            | 讲韩语（韩国）区域设置语言                                  |
|                                  | `lang="ja-JP"`            | 讲日语（日本）区域设置语言                                  |
|                                  | `lang="it-IT"`            | 讲意大利语（意大利）区域设置语言                                  |
|                                  | `lang="pt-BR"`            | 讲葡萄牙语（葡萄牙）区域设置语言                                  |

**示例**

此 SSML 代码片段演示如何使用 `<lang xml:lang>` 将讲话语言更改为 `en-US`、`es-MX` 和 `de-DE`。

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis"
       xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
    <voice name="en-US-JennyMultilingualNeural">
        I am looking forward to the exciting things.
        <lang xml:lang="es-MX">
            Estoy deseando que lleguen las cosas emocionantes.
        </lang>
        <lang xml:lang="de-DE">
            Ich freue mich auf die spannenden Dinge.
        </lang>
    </voice>
</speak>
```

## <a name="add-or-remove-a-breakpause"></a>添加或删除中断/暂停

使用元素 `break` 可在单词之间插入暂停（或中断），或者防止文本转语音服务自动添加暂停。

> [!NOTE]
> 如果某个单词或短语的合成语音听起来不自然，可以使用此元素来重写该单词或短语的默认文本转语音 (TTS) 行为。 将 `strength` 设置为 `none` 可防止文本转语音服务自动插入的韵律中断。

**语法**

```xml
<break strength="string" />
<break time="string" />
```

**属性**

| 属性 | 说明 | 必需/可选 |
|-----------|-------------|---------------------|
| `strength` | 使用以下值之一指定暂停的相对持续时间：<ul><li>无</li><li>x-weak</li><li>weak</li><li>medium（默认值）</li><li>strong</li><li>x-strong</li></ul> | 可选 |
| `time` | 指定暂停的绝对持续时间，以秒或毫秒为单位；该值应设为小于 5000 毫秒。 例如，`2s` 和 `500ms` 是有效值 | 可选 |

| Strength                      | 说明 |
|-------------------------------|-------------|
| None，或者不提供任何值 | 0 毫秒        |
| x-weak                        | 250 毫秒      |
| weak                          | 500 毫秒      |
| 中                        | 750 毫秒      |
| strong                        | 1000 毫秒     |
| x-strong                      | 1250 毫秒     |

**示例**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaNeural">
        Welcome to Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.
    </voice>
</speak>
```
## <a name="add-silence"></a>添加静音

使用 `mstts:silence` 元素在文本前后，或者在两个相邻句子之间添加暂停。

> [!NOTE]
>`mstts:silence` 和 `break` 之间的区别在于，`break` 可添加到文本中的任何位置，但静音仅适合输入文本的开头或结尾，或者两个相邻句子的分界处。


**语法**

```xml
<mstts:silence  type="string"  value="string"/>
```

**属性**

| 属性 | 说明 | 必需/可选 |
|-----------|-------------|---------------------|
| `type` | 指定添加静音的位置： <ul><li>`Leading` - 在文本的开头 </li><li>`Tailing` - 在文本的结尾 </li><li>`Sentenceboundary` - 在相邻句子之间 </li></ul> | 必须 |
| `Value` | 指定暂停的绝对持续时间，以秒或毫秒为单位；该值应设为小于 5000 毫秒。 例如，`2s` 和 `500ms` 是有效值 | 必须 |

**示例** 在本例中，`mtts:silence` 用于在两个句子之间添加 200 毫秒的静音。
```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
<voice name="en-US-AriaNeural">
<mstts:silence  type="Sentenceboundary" value="200ms"/>
If we’re home schooling, the best we can do is roll with what each day brings and try to have fun along the way.
A good place to start is by trying out the slew of educational apps that are helping children stay happy and smash their schooling at the same time.
</voice>
</speak>
```

## <a name="specify-paragraphs-and-sentences"></a>指定段落和句子

`p` 和 `s` 元素分别用于表示段落和句子。 如果不指定这些元素，则文本转语音服务会自动确定 SSML 文档的结构。

`p` 元素可包含文本和以下元素：`audio`、`break`、`phoneme`、`prosody`、`say-as`、`sub`、`mstts:express-as` 和 `s`。

`s` 元素可包含文本和以下元素：`audio`、`break`、`phoneme`、`prosody`、`say-as`、`mstts:express-as` 和 `sub`。

**语法**

```XML
<p></p>
<s></s>
```

**示例**

```XML
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        <p>
            <s>Introducing the sentence element.</s>
            <s>Used to mark individual sentences.</s>
        </p>
        <p>
            Another simple paragraph.
            Sentence structure in this paragraph is not explicitly marked.
        </p>
    </voice>
</speak>
```

## <a name="use-phonemes-to-improve-pronunciation"></a>使用音素改善发音

`ph` 元素用于 SSML 文档中的发音。 `ph` 元素只能包含文本，而不能包含其他元素。 始终提供人类可读的语音作为回退。

音标由音素构成，而这些音素由字母、数字或字符（有时是它们的组合）构成。 每个音素描述独特的语音。 这与拉丁字母形成鲜明对比，在拉丁字母中，任何字母都可能代表多种发音。 想像一下单词“candy”和“cease”中字母“c”的不同发音，或者字母组合“th”在单词“thing”和“those”中的不同发音。

> [!NOTE]
> 目前，不支持下面 5 种语音的音素标记：et-EE-AnuNeural、ga-IE-OrlaNeural、lt-LT-OnaNeural、lv-LV-EveritaNeural 和 mt-MT-GarceNeural。

**语法**

```XML
<phoneme alphabet="string" ph="string"></phoneme>
```

**属性**

| 属性 | 说明 | 必需/可选 |
|-----------|-------------|---------------------|
| `alphabet` | 指定在 `ph` 属性中合成字符串发音时要使用的音标。 指定音标的字符串必须以小写字母指定。 下面是可以指定的可能音标。<ul><li>`ipa` &ndash; <a href="https://en.wikipedia.org/wiki/International_Phonetic_Alphabet" target="_blank">国际音标 </a></li><li>`sapi` &ndash; [语音服务音标](speech-ssml-phonetic-sets.md)</li><li>`ups` &ndash;<a href="https://documentation.help/Microsoft-Speech-Platform-SDK-11/17509a49-cae7-41f5-b61d-07beaae872ea.htm" target="_blank"> 通用音素集</a></li></ul><br>音标仅适用于元素中的 `phoneme`。 | 可选 |
| `ph` | 一个字符串，包含用于在 `phoneme` 元素中指定单词发音的音素。 如果指定的字符串包含无法识别的音素，则文本转语音 (TTS) 服务将拒绝整个 SSML 文档，并且不会生成文档中指定的任何语音输出。 | 如果使用音素，则此属性是必需的。 |

**示例**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        <phoneme alphabet="ipa" ph="təˈmeɪtoʊ"> tomato </phoneme>
    </voice>
</speak>
```

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        <phoneme alphabet="sapi" ph="iy eh n y uw eh s"> en-US </phoneme>
    </voice>
</speak>
```

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        <s>His name is Mike <phoneme alphabet="ups" ph="JH AU"> Zhou </phoneme></s>
    </voice>
</speak>
```

## <a name="use-custom-lexicon-to-improve-pronunciation"></a>使用自定义词典改善发音

有时文本转语音服务无法准确地根据字词发音。 例如，公司的名称、医学术语或表情符号。 开发人员可以使用 `phoneme` 和 `sub` 标记来定义采用 SSML 朗读单个实体的方式。 但是，如果需要定义朗读多个实体的方式，则可以使用 `lexicon` 标记创建自定义词典。

> [!NOTE]
> 自定义词典当前支持 UTF-8 编码。

> [!NOTE]
> 目前，不支持下面 5 种语音的自定义词典：et-EE-AnuNeural、ga-IE-OrlaNeural、lt-LT-OnaNeural、lv-LV-EveritaNeural 和 mt-MT-GarceNeural。


**语法**

```XML
<lexicon uri="string"/>
```

**属性**

| 属性 | 说明                               | 必需/可选 |
|-----------|-------------------------------------------|---------------------|
| `uri`     | 外部 PLS 文档的地址。 | 必需。           |

**使用情况**

若要定义朗读多个实体的方式，可以创建一个以 .xml 或 .pls 文件形式存储的自定义词典。 以下是示例 .xml 文件。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<lexicon version="1.0"
      xmlns="http://www.w3.org/2005/01/pronunciation-lexicon"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="http://www.w3.org/2005/01/pronunciation-lexicon
        http://www.w3.org/TR/2007/CR-pronunciation-lexicon-20071212/pls.xsd"
      alphabet="ipa" xml:lang="en-US">
  <lexeme>
    <grapheme>BTW</grapheme>
    <alias>By the way</alias>
  </lexeme>
  <lexeme>
    <grapheme> Benigni </grapheme>
    <phoneme> bɛˈniːnji</phoneme>
  </lexeme>
  <lexeme>
    <grapheme>😀</grapheme>
    <alias>test emoji</alias>
  </lexeme>
</lexicon>
```

`lexicon` 元素包含至少一个 `lexeme` 元素。 每个 `lexeme` 元素包含至少一个 `grapheme` 元素以及一个或多个 `grapheme`、`alias` 和 `phoneme` 元素。 `grapheme` 元素包含描述<a href="https://www.w3.org/TR/pronunciation-lexicon/#term-Orthography" target="_blank">拼字法 </a> 的文本。 `alias` 元素用于指示某个首字母缩写词或某个缩写词的发音。 `phoneme` 元素提供了描述 `lexeme` 发音方式的文本。 当 `alias` 与 `phoneme` 元素具有相同的 `grapheme` 元素时，`alias` 具有更高的优先级。

词典包含必需的 `xml:lang` 属性，用于指示要应用词典的区域设置。 根据设计，一个自定义词典仅限于一种区域设置，因此将其应用于另一种区域设置将无法正常工作。

需要特别注意的是，不能使用自定义词典直接设置短语的发音。 如果需要设置首字母缩略词或缩写词的发音，请首先提供 `alias`，再将 `phoneme` 与该 `alias` 关联。 例如：

```xml
  <lexeme>
    <grapheme>Scotland MV</grapheme>
    <alias>ScotlandMV</alias>
  </lexeme>
  <lexeme>
    <grapheme>ScotlandMV</grapheme>
    <phoneme>ˈskɒtlənd.ˈmiːdiəm.weɪv</phoneme>
  </lexeme>
```

也可以直接提供所需的`alias`作为首字母缩略词或缩写词。 例如：
```xml
  <lexeme>
    <grapheme>Scotland MV</grapheme>
    <alias>Scotland Media Wave</alias>
  </lexeme>
```

> [!IMPORTANT]
> 使用 IPA 时，`phoneme` 元素不能包含空格。

有关自定义词典文件的详细信息，请参阅 [Pronunciation Lexicon Specification (PLS) Version 1.0](https://www.w3.org/TR/pronunciation-lexicon/)（发音词典规范 (PLS) 版本 1.0）。

接下来，发布自定义词典文件。 虽然对此文件的存储位置没有限制，但建议使用 [Azure Blob 存储](../../storage/blobs/storage-quickstart-blobs-portal.md)。

发布自定义词典后，可以从 SSML 引用它。

> [!NOTE]
> `lexicon` 元素必须位于 `voice` 元素内部。

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis"
          xmlns:mstts="http://www.w3.org/2001/mstts"
          xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        <lexicon uri="http://www.example.com/customlexicon.xml"/>
        BTW, we will be there probably at 8:00 tomorrow morning.
        Could you help leave a message to Robert Benigni for me?
    </voice>
</speak>
```

使用此自定义词典时，“BTW”将读作“By the way”。 “Benigni”将通过提供的 IPA“bɛˈniːnji”朗读。

**限制**
- 文件大小：自定义词典文件大小的最大限制为 100KB。如果超过此大小，合成请求会失败。
- 词典缓存刷新：自定义词典在缓存时会将 URI 用作 TTS 服务上的密钥（在首次加载它时）。 不会在 15 分钟内重新加载具有相同 URI 的词典。因此，如果希望自定义词典更改生效，最多需要等待 15 分钟。

**语音服务语音集**

在上面的示例中，我们使用的是国际音标（也称为 IPA 语音集）。 我们建议开发人员使用 IPA，因为它是国际标准。 对于某些 IPA 字符，当使用 Unicode 表示时，它们具有“预构”和“分解”版本。 自定义词典仅支持分解的 Unicode。

考虑到 IPA 不容易记住，语音服务为七种语言（`en-US`、`fr-FR`、`de-DE`、`es-ES`、`ja-JP`、`zh-CN`和 `zh-TW`）定义语音集。

可以使用 `x-microsoft-sapi` 作为 `alphabet` 属性的值，且自定义词典如下所示：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<lexicon version="1.0"
      xmlns="http://www.w3.org/2005/01/pronunciation-lexicon"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="http://www.w3.org/2005/01/pronunciation-lexicon
        http://www.w3.org/TR/2007/CR-pronunciation-lexicon-20071212/pls.xsd"
      alphabet="x-microsoft-sapi" xml:lang="en-US">
  <lexeme>
    <grapheme>BTW</grapheme>
    <alias> By the way </alias>
  </lexeme>
  <lexeme>
    <grapheme> Benigni </grapheme>
    <phoneme> b eh 1 - n iy - n y iy </phoneme>
  </lexeme>
</lexicon>
```

有关详细的语音服务音标的更多信息，请参阅[语音服务语音集](speech-ssml-phonetic-sets.md)。

## <a name="adjust-prosody"></a>调整韵律

`prosody` 元素用于指定文本转语音输出的音节、调型、范围、速率、持续时间和音量的变化。 `prosody` 元素可包含文本和以下元素：`audio`、`break`、`p`、`phoneme`、`prosody`、`say-as`、`sub` 和 `s`。

由于韵律属性值可在较大范围内变化，因此，语音识别器会将分配的值解释为所选语音的建议实际韵律值。 文本转语音服务将限制或替代不支持的值。 例如，音节 1 MHz 或音量 120 就是不支持的值。

**语法**

```XML
<prosody pitch="value" contour="value" range="value" rate="value" duration="value" volume="value"></prosody>
```

**属性**

| 属性 | 说明 | 必需/可选 |
|-----------|-------------|---------------------|
| `pitch` | 指示文本的基线音节。 可将音调表述为：<ul><li>以某个数字后接“Hz”（赫兹）表示的绝对值。 例如 `<prosody pitch="600Hz">some text</prosody>`。</li><li>以前面带有“+”或“-”的数字，后接“Hz”或“st”（用于指定音节的变化量）表示的相对值。 例如 `<prosody pitch="+80Hz">some text</prosody>` 或 `<prosody pitch="-2st">some text</prosody>`。 “st”表示变化单位为半音，即，标准全音阶中的半调（半步）。</li><li>常量值：<ul><li>x-low</li><li>low</li><li>中</li><li>high</li><li>x-high</li><li>默认值</li></ul></li></ul> | 可选 |
| `contour` |调型现在同时支持神经语音和标准语音。 调型表示音节的变化。 这些变化以语音输出中指定时间处的目标数组形式表示。 每个目标由参数对的集定义。 例如： <br/><br/>`<prosody contour="(0%,+20Hz) (10%,-2st) (40%,+10Hz)">`<br/><br/>每参数集中的第一个值以文本持续时间百分比的形式指定音节变化的位置。 第二个值使用音节的相对值或枚举值指定音节的升高或降低量（请参阅 `pitch`）。 | 可选 |
| `range` | 表示文本音节范围的值。 可使用用于描述 `pitch` 的相同绝对值、相对值或枚举值表示 `range`。 | 可选 |
| `rate` | 指示文本的讲出速率。 可将 `rate` 表述为：<ul><li>以充当默认值倍数的数字表示的相对值。 例如，如果值为 *1*，则速率不会变化。 如果值为 *0.5*，则速率会减慢一半。 如果值为 *3*，则速率为三倍。</li><li>常量值：<ul><li>x-slow</li><li>slow</li><li>中</li><li>fast</li><li>x-fast</li><li>默认值</li></ul></li></ul> | 可选 |
| `duration` | 语音合成 (TTS) 服务读取文本时应该消逝的时长，以秒或毫秒为单位。 例如 *2s* 或 *1800ms*。 持续时间仅支持标准语音。| 可选 |
| `volume` | 指示语音的音量级别。 可将音量表示为：<ul><li>以从 0.0 到 100.0（从最安静到最大声）的数字表示的绝对值。 例如 75。 默认值为 100.0。</li><li>以前面带有“+”或“-”的数字表示的相对值，指定音量的变化量。 例如，+10 或 -5.5。</li><li>常量值：<ul><li>silent</li><li>x-soft</li><li>soft</li><li>中</li><li>loud</li><li>x-loud</li><li>默认值</li></ul></li></ul> | 可选 |

### <a name="change-speaking-rate"></a>更改语速

可以在单词或句子级别对神经语音和标准语音应用语速。

**示例**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-GuyNeural">
        <prosody rate="+30.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-volume"></a>更改音量

可以在单词或句子级别对标准语音应用音量变化。 只能在句子级别对神经语音应用音量变化。

**示例**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        <prosody volume="+20.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-pitch"></a>更改音高

可以在单词或句子级别对标准语音应用音节变化。 只能在句子级别对神经语音应用音节变化。

**示例**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaNeural">
        Welcome to <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody>
    </voice>
</speak>
```

### <a name="change-pitch-contour"></a>更改音高升降曲线

> [!IMPORTANT]
> 神经语音现在支持音节调型变化。

**示例**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaNeural">
        <prosody contour="(60%,-60%) (100%,+80%)" >
            Were you the only person in the room?
        </prosody>
    </voice>
</speak>
```
## <a name="say-as-element"></a>say-as 元素

`say-as` 是一个可选元素，指示元素文本的内容类型（例如数字或日期）。 它为语音合成引擎提供有关如何朗读文本的指导。

**语法**

```XML
<say-as interpret-as="string" format="digit string" detail="string"> <say-as>
```

**属性**

| 属性 | 说明 | 必需/可选 |
|-----------|-------------|---------------------|
| `interpret-as` | 指示元素文本的内容类型。 有关类型列表，请参阅下表。 | 必须 |
| `format` | 为可能具有不明确格式的内容类型提供有关元素文本的精确格式设置的其他信息。 SSML 为使用它们的内容类型定义格式（请参阅下表）。 | 可选 |
| `detail` | 指示要朗读的详细信息的级别。 例如，此属性可以要求语音合成引擎朗读标点符号。 没有为 `detail` 定义标准值。 | 可选 |

<!-- I don't understand the last sentence. Don't we know which one Cortana uses? -->

下面是 `interpret-as` 和 `format` 属性支持的内容类型。 仅当 `interpret-as` 设置为日期和时间时，才包括 `format` 属性。

| interpret-as | format | 解释 |
|--------------|--------|----------------|
| `address` | | 此文本朗读为地址。 语音合成引擎将以下内容：<br /><br />`I'm at <say-as interpret-as="address">150th CT NE, Redmond, WA</say-as>`<br /><br />朗读为：“I'm at 150th court north east redmond washington.” |
| `cardinal`, `number` | | 此文本朗读为基数。 语音合成引擎将以下内容：<br /><br />`There are <say-as interpret-as="cardinal">3</say-as> alternatives`<br /><br />朗读为“There are three alternatives.”。 |
| `characters`, `spell-out` | | 此文本朗读为各个字符（拼读出来）。 语音合成引擎将以下内容：<br /><br />`<say-as interpret-as="characters">test</say-as>`<br /><br />朗读为“T E S T.” |
| `date` | dmy, mdy, ymd, ydm, ym, my, md, dm, d, m, y | 此文本朗读为日期。 `format` 属性指定日期的格式 (*d=day, m=month, and y=year*)。 语音合成引擎将以下内容：<br /><br />`Today is <say-as interpret-as="date" format="mdy">10-19-2016</say-as>`<br /><br />朗读为“Today is October nineteenth two thousand sixteen.” |
| `digits`, `number_digit` | | 此文本朗读为个体数字的序列。 语音合成引擎将以下内容：<br /><br />`<say-as interpret-as="number_digit">123456789</say-as>`<br /><br />朗读为“1 2 3 4 5 6 7 8 9.” |
| `fraction` | | 此文本朗读为分数。 语音合成引擎将以下内容：<br /><br /> `<say-as interpret-as="fraction">3/8</say-as> of an inch`<br /><br />朗读为“three eighths of an inch.” |
| `ordinal` | | 此文本朗读为基数。 语音合成引擎将以下内容：<br /><br />`Select the <say-as interpret-as="ordinal">3rd</say-as> option`<br /><br />朗读为“Select the third option”。 |
| `telephone` | | 此文本朗读为电话号码。 `format` 属性可以包含表示国家/地区代码的数字。 例如，“1”表示美国，“39”表示意大利。 语音合成引擎可使用此信息来指导其电话号码的发音。 电话号码也可能包含国家/地区代码，如果是，则优先于 `format` 中的国家/地区代码。 语音合成引擎将以下内容：<br /><br />`The number is <say-as interpret-as="telephone" format="1">(888) 555-1212</say-as>`<br /><br />朗读为“My number is area code eight eight eight five five five one two one two.” |
| `time` | hms12, hms24 | 此文本朗读为时间。 `format` 属性指定时间是使用 12 小时制 (hms12) 还是 24 小时制 (hms24) 指定的。 请使用冒号分隔表示小时、分钟和秒的数字。 下面是有效的时间示例：12:35、1:14:32、08:15 和 02:50:45。 语音合成引擎将以下内容：<br /><br />`The train departs at <say-as interpret-as="time" format="hms12">4:00am</say-as>`<br /><br />朗读为“The train departs at four A M.” |
| `name` | | 此文本朗读为人名。 语音合成引擎将以下内容：<br /><br />`<say-as interpret-as="name">ED</say-as>`<br /><br />发音为 [æd]。 <br />在中文姓名中，某些字符出现在姓氏中时发音不同。 例如，语音合成引擎在以下示例中将“仇” <br /><br />`<say-as interpret-as="address">仇先生</say-as>`<br /><br /> 发音为 [qiú] 而非 [chóu]。 |

**使用情况**

`say-as` 元素只能包含文本。

**示例**

语音合成引擎将以下示例朗读为“Your first request was for one room on October nineteenth twenty ten with early arrival at twelve thirty five PM.”

```XML
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        <p>
        Your <say-as interpret-as="ordinal"> 1st </say-as> request was for <say-as interpret-as="cardinal"> 1 </say-as> room
        on <say-as interpret-as="date" format="mdy"> 10/19/2010 </say-as>, with early arrival at <say-as interpret-as="time" format="hms12"> 12:35pm </say-as>.
        </p>
    </voice>
</speak>
```

## <a name="add-recorded-audio"></a>添加录制的音频

`audio` 是一个可选元素，用于将 MP3 音频插入 SSML 文档。 如果音频文件不可用或不可播放，可在音频元素的正文中包含可讲述的纯文本或 SSML 标记。 此外，`audio` 元素可包含文本和以下元素：`audio`、`break`、`p`、`s`、`phoneme`、`prosody`、`say-as` 和 `sub`。

包含在 SSML 文档中的任何音频必须满足以下要求：

* MP3 必须托管在可通过 Internet 访问的 HTTPS 终结点上。 必须使用 HTTPS，托管 MP3 文件的域必须提供有效的受信任 TLS/SSL 证书。
* MP3 必须是有效的 MP3 文件 (MPEG v2)。
* 比特率必须是 48 kbps。
* 采样率必须是 16,000 Hz。
* 单个响应中所有文本和音频文件的总时间不能超过 90 秒。
* MP3 不得包含任何客户特定的信息或其他敏感信息。

**语法**

```xml
<audio src="string"/></audio>
```

**属性**

| 属性 | 说明                                   | 必需/可选                                        |
|-----------|-----------------------------------------------|------------------------------------------------------------|
| `src`     | 指定音频文件的位置/URL。 | 在 SSML 文档中使用音频元素时，此属性是必需的。 |

**示例**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        <p>
            <audio src="https://contoso.com/opinionprompt.wav"/>
            Thanks for offering your opinion. Please begin speaking after the beep.
            <audio src="https://contoso.com/beep.wav">
                Could not play the beep, please voice your opinion now.
            </audio>
        </p>
    </voice>
</speak>
```

## <a name="add-background-audio"></a>添加背景音频

使用 `mstts:backgroundaudio` 元素可将背景音频添加到 SSML 文档（或者使用文本转语音来混合音频文件）。 使用 `mstts:backgroundaudio` 可以在后台循环音频文件，在文本转语音的开头淡入，并在文本转语音的末尾淡出。

如果提供的背景音频短于文本转语音或淡出持续时间，则会循环该音频。 如果其长度超过文本转语音的持续时间，则它在完成淡出后将会停止。

每个 SSML 文档仅允许一个背景音频文件。 但是，可以在 `voice` 元素中散布 `audio` 标记，以将更多的音频添加到 SSML 文档。

**语法**

```XML
<mstts:backgroundaudio src="string" volume="string" fadein="string" fadeout="string"/>
```

**属性**

| 属性 | 说明 | 必需/可选 |
|-----------|-------------|---------------------|
| `src` | 指定背景音频文件的位置/URL。 | 如果在 SSML 文档中使用背景音频，则此属性是必需的。 |
| `volume` | 指定背景音频文件的音量。 **接受的值**：`0` 到 `100`（含）。 默认值为 `1`。 | 可选 |
| `fadein` | 指定背景音频淡入的持续时间，以毫秒为单位。 默认值为 `0`，即，不淡入。 **接受的值**：`0` 到 `10000`（含）。  | 可选 |
| `fadeout` | 指定背景音频淡出的持续时间，以毫秒为单位。 默认值为 `0`，即，不淡出。**接受的值**：`0` 到 `10000`（含）。  | 可选 |

**示例**

```xml
<speak version="1.0" xml:lang="en-US" xmlns:mstts="http://www.w3.org/2001/mstts">
    <mstts:backgroundaudio src="https://contoso.com/sample.wav" volume="0.7" fadein="3000" fadeout="4000"/>
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, JennyNeural)">
        The text provided in this document will be spoken over the background audio.
    </voice>
</speak>
```

## <a name="bookmark-element"></a>Bookmark 元素

使用 bookmark 元素可以在 SSML 中插入自定义标记，以获得音频流中每个标记的偏移量。
我们不会读出 bookmark 元素。
bookmark 元素可用于引用文本或标记序列中的特定位置。

> [!NOTE]
> 目前，`bookmark` 元素仅适用于 `en-US-AriaNeural` 语音。

**语法**

```xml
<bookmark mark="string"/>
```

**属性**

| 属性 | 说明                                   | 必需/可选                                        |
|-----------|-----------------------------------------------|------------------------------------------------------------|
|  `mark`   | 指定 `bookmark` 元素的引用文本。 | 必需。 |

**示例**

例如，你可能想知道每个与花相关的词的时间偏移量，如下所示

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaNeural">
        We are selling <bookmark mark='flower_1'/>roses and <bookmark mark='flower_2'/>daisies.
    </voice>
</speak>
```

### <a name="get-bookmark-using-speech-sdk"></a>使用语音 SDK 获取书签

可以订阅语音 SDK 中的 `BookmarkReached` 事件来获取书签偏移量。

> [!NOTE]
> `BookmarkReached` 事件仅从语音 SDK 1.16 版本开始提供。

`BookmarkReached` 事件在输出音频数据变为可用时引发，这样将会比播放到输出设备更快。

* `AudioOffset` 会报告输出音频在合成的开始处到 bookmark 元素之间经历的时间。 此时间以百纳秒单位 (HNS) 进行测量，10,000 HNS 相当于 1 毫秒。
* `Text` 是 bookmark 元素的引用文本（在 `mark` 属性中设置的字符串）。

# <a name="c"></a>[C#](#tab/csharp)

有关详细信息，请参阅 <a href="/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesizer.bookmarkreached" target="_blank"> `BookmarkReached` </a>。

```csharp
synthesizer.BookmarkReached += (s, e) =>
{
    // The unit of e.AudioOffset is tick (1 tick = 100 nanoseconds), divide by 10,000 to convert to milliseconds.
    Console.WriteLine($"Bookmark reached. Audio offset: " +
        $"{e.AudioOffset / 10000}ms, bookmark text: {e.Text}.");
};
```

对于上面的示例 SSML，`BookmarkReached` 事件会触发两次，控制台输出将如下所示
```text
Bookmark reached. Audio offset: 825ms, bookmark text: flower_1.
Bookmark reached. Audio offset: 1462.5ms, bookmark text: flower_2.
```

# <a name="c"></a>[C++](#tab/cpp)

有关详细信息，请参阅 <a href="/cpp/cognitive-services/speech/speechsynthesizer#bookmarkreached" target="_blank"> `BookmarkReached` </a>。

```cpp
synthesizer->BookmarkReached += [](const SpeechSynthesisBookmarkEventArgs& e)
{
    cout << "Bookmark reached. "
        // The unit of e.AudioOffset is tick (1 tick = 100 nanoseconds), divide by 10,000 to convert to milliseconds.
        << "Audio offset: " << e.AudioOffset / 10000 << "ms, "
        << "bookmark text: " << e.Text << "." << endl;
};
```

对于上面的示例 SSML，`BookmarkReached` 事件会触发两次，控制台输出将如下所示
```text
Bookmark reached. Audio offset: 825ms, bookmark text: flower_1.
Bookmark reached. Audio offset: 1462.5ms, bookmark text: flower_2.
```

# <a name="java"></a>[Java](#tab/java)

有关详细信息，请参阅 <a href="/java/api/com.microsoft.cognitiveservices.speech.speechsynthesizer.bookmarkReached#com_microsoft_cognitiveservices_speech_SpeechSynthesizer_BookmarkReached" target="_blank"> `BookmarkReached` </a>。

```java
synthesizer.BookmarkReached.addEventListener((o, e) -> {
    // The unit of e.AudioOffset is tick (1 tick = 100 nanoseconds), divide by 10,000 to convert to milliseconds.
    System.out.print("Bookmark reached. Audio offset: " + e.getAudioOffset() / 10000 + "ms, ");
    System.out.println("bookmark text: " + e.getText() + ".");
});
```

对于上面的示例 SSML，`BookmarkReached` 事件会触发两次，控制台输出将如下所示
```text
Bookmark reached. Audio offset: 825ms, bookmark text: flower_1.
Bookmark reached. Audio offset: 1462.5ms, bookmark text: flower_2.
```

# <a name="python"></a>[Python](#tab/python)

有关详细信息，请参阅 <a href="/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesizer#bookmark-reached" target="_blank"> `bookmark_reached` </a>。

```python
# The unit of evt.audio_offset is tick (1 tick = 100 nanoseconds), divide it by 10,000 to convert to milliseconds.
speech_synthesizer.bookmark_reached.connect(lambda evt: print(
    "Bookmark reached: {}, audio offset: {}ms, bookmark text: {}.".format(evt, evt.audio_offset / 10000, evt.text)))
```

对于上面的示例 SSML，`bookmark_reached` 事件会触发两次，控制台输出将如下所示
```text
Bookmark reached, audio offset: 825ms, bookmark text: flower_1.
Bookmark reached, audio offset: 1462.5ms, bookmark text: flower_2.
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

有关详细信息，请参阅 <a href="/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesizer#bookmarkReached" target="_blank"> `bookmarkReached`</a>。

```javascript
synthesizer.bookmarkReached = function (s, e) {
    window.console.log("(Bookmark reached), Audio offset: " + e.audioOffset / 10000 + "ms, bookmark text: " + e.text);
}
```

对于上面的示例 SSML，`bookmarkReached` 事件会触发两次，控制台输出将如下所示
```text
(Bookmark reached), Audio offset: 825ms, bookmark text: flower_1.
(Bookmark reached), Audio offset: 1462.5ms, bookmark text: flower_2.
```

# <a name="objective-c"></a>[Objective-C](#tab/objectivec)

有关详细信息，请参阅 <a href="/objectivec/cognitive-services/speech/spxspeechsynthesizer#addbookmarkreachedeventhandler" target="_blank"> `addBookmarkReachedEventHandler` </a>。

```objectivec
[synthesizer addBookmarkReachedEventHandler: ^ (SPXSpeechSynthesizer *synthesizer, SPXSpeechSynthesisBookmarkEventArgs *eventArgs) {
    // The unit of AudioOffset is tick (1 tick = 100 nanoseconds), divide by 10,000 to converted to milliseconds.
    NSLog(@"Bookmark reached. Audio offset: %fms, bookmark text: %@.", eventArgs.audioOffset/10000., eventArgs.text);
}];
```

对于上面的示例 SSML，`BookmarkReached` 事件会触发两次，控制台输出将如下所示
```text
Bookmark reached. Audio offset: 825ms, bookmark text: flower_1.
Bookmark reached. Audio offset: 1462.5ms, bookmark text: flower_2.
```

# <a name="swift"></a>[Swift](#tab/swift)

有关详细信息，请参阅 <a href="/objectivec/cognitive-services/speech/spxspeechsynthesizer" target="_blank"> `addBookmarkReachedEventHandler` </a>。

---

## <a name="next-steps"></a>后续步骤

* [语言支持：语音、区域设置、语言](language-support.md)
