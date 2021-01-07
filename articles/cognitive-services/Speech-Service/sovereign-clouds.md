---
title: 主权云 - 语音服务
titleSuffix: Azure Cognitive Services
description: 了解如何使用主权云
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.custom: references_regions
ms.date: 12/26/2020
ms.author: alexeyo
ms.openlocfilehash: a1c3fcf868af76865eec9fa2be4f0fdb58074867
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/07/2021
ms.locfileid: "97964450"
---
# <a name="speech-services-in-sovereign-clouds"></a>主权云中的语音服务

## <a name="azure-government-united-states"></a>Azure 政府 (美国) 

仅适用于美国政府实体及其合作伙伴。 请 [在此处](../../azure-government/documentation-government-welcome.md) 和此处查看有关 Azure 政府的详细信息 [。](../../azure-government/compare-azure-government-global-azure.md)

- **Azure 门户：**
  - [https://portal.azure.us/](https://portal.azure.us/)
- **区域**
  - US Gov 亚利桑那州
  - US Gov 弗吉尼亚州
- **可用定价层：**
  - 免费 (F0) 和标准 (S0) 。 [在此处](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)查看更多详细信息
- **支持的功能：**
  - 语音转文本
    - 自定义语音 (声音模型 (AM) 和语言模型 (LM) 适配) 
      - [Speech Studio](https://speech.azure.us/)
  - 文本转语音
  - 语音翻译
- 不受支持的功能：
  - 神经语音
  - 自定义语音
- **支持的语言：**
  - 阿拉伯语 (ar-*)
  - 中文 (zh-*)
  - 英语 (en-*)
  - 法语 (fr-*)
  - 德语 (de-*)
  - 印地语 (高保真) 
  - 韩语 (ko-KR)
  - 俄语 (ru-RU)
  - 西班牙语 (es-*)

### <a name="endpoint-information"></a>终结点信息

本部分包含语音服务终结点信息，用于 [语音 SDK](speech-sdk.md)、 [语音到文本 REST API](rest-speech-to-text.md)和 [文本到语音转换 REST API](rest-text-to-speech.md)。

#### <a name="speech-services-rest-api"></a>语音服务 REST API

Azure 政府版中 REST API 终结点的语音服务具有以下格式：

|  REST API 类型/操作 | 终结点格式 |
|--|--|
| 访问令牌 | `https://<REGION_IDENTIFIER>.api.cognitive.microsoft.us/sts/v1.0/issueToken`
| [从语音到文本 REST API 3。0](rest-speech-to-text.md#speech-to-text-rest-api-v30) | `https://<REGION_IDENTIFIER>.api.cognitive.microsoft.us/<URL_PATH>` |
| [短音频的语音到文本 REST API](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) | `https://<REGION_IDENTIFIER>.stt.speech.azure.us/<URL_PATH>` |
| [文本转语音 REST API](rest-text-to-speech.md) | `https://<REGION_IDENTIFIER>.tts.speech.azure.us/<URL_PATH>` |

将 `<REGION_IDENTIFIER>` 替换为与下表中的订阅区域匹配的标识符：

|                     | 区域标识符 |
|--|--|
| **US Gov 亚利桑那州**  | `usgovarizona` |
| **US Gov 弗吉尼亚州** | `usgovvirginia` |

#### <a name="speech-sdk"></a>语音 SDK

对于主权云中的语音 SDK，需要使用 `SpeechConfig` `--host` [语音 CLI](spx-overview.md)的类或选项的 "从主机" 实例化。  (还可以) 使用 "from 终结点" 实例化和 `--endpoint` 语音 CLI 选项。

`SpeechConfig` 类应实例化，如下所示：
```csharp
var config = SpeechConfig.FromHost(usGovHost, subscriptionKey);
```
应使用语音 CLI，如以下 (记下 `--host`) 选项：
```dos
spx recognize --host "usGovHost" --file myaudio.wav
```
`subscriptionKey`将替换为语音资源密钥。 替换 `usGovHost` 为匹配所需服务产品的表达式和此表中订阅的区域：

|  区域/服务产品 | 主机表达式 |
|--|--|
| **US Gov 亚利桑那州** | |
| 语音转文本 | `wss://usgovarizona.stt.speech.azure.us` |
| 文本到语音转换 | `https://usgovarizona.tts.speech.azure.us` |
| **US Gov 弗吉尼亚州** | |
| 语音转文本 | `wss://usgovvirginia.stt.speech.azure.us` |
| 文本到语音转换 | `https://usgovvirginia.tts.speech.azure.us` |


## <a name="azure-china"></a>Azure 中国

可供在中国有业务的组织使用。 请参阅[此处的](/azure/china/overview-operations)Azure 中国的详细信息。 


- **Azure 门户：**
  - [https://portal.azure.cn/](https://portal.azure.cn/)
- **区域**
  - 中国东部 2
- **可用定价层：**
  - 免费 (F0) 和标准 (S0) 。 [在此处](https://www.azure.cn/pricing/details/cognitive-services/index.html)查看更多详细信息
- **支持的功能：**
  - 语音转文本
    - 自定义语音 (声音模型 (AM) 和语言模型 (LM) 适配) 
      - [Speech Studio](https://speech.azure.cn/)
  - 文本转语音
  - 语音翻译
- 不受支持的功能：
  - 神经语音
  - 自定义语音
- **支持的语言：**
  - 阿拉伯语 (ar-*)
  - 中文 (zh-*)
  - 英语 (en-*)
  - 法语 (fr-*)
  - 德语 (de-*)
  - 印地语 (高保真) 
  - 韩语 (ko-KR)
  - 俄语 (ru-RU)
  - 西班牙语 (es-*)

### <a name="endpoint-information"></a>终结点信息

本部分包含语音服务终结点信息，用于 [语音 SDK](speech-sdk.md)、 [语音到文本 REST API](rest-speech-to-text.md)和 [文本到语音转换 REST API](rest-text-to-speech.md)。

#### <a name="speech-services-rest-api"></a>语音服务 REST API

Azure 中国 REST API 的语音服务终结点采用以下格式：

|  REST API 类型/操作 | 终结点格式 |
|--|--|
| 访问令牌 | `https://<REGION_IDENTIFIER>.api.cognitive.azure.cn/sts/v1.0/issueToken`
| [从语音到文本 REST API 3。0](rest-speech-to-text.md#speech-to-text-rest-api-v30) | `https://<REGION_IDENTIFIER>.api.cognitive.azure.cn/<URL_PATH>` |
| [短音频的语音到文本 REST API](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) | `https://<REGION_IDENTIFIER>.stt.speech.azure.cn/<URL_PATH>` |
| [文本转语音 REST API](rest-text-to-speech.md) | `https://<REGION_IDENTIFIER>.tts.speech.azure.cn/<URL_PATH>` |

将 `<REGION_IDENTIFIER>` 替换为与下表中的订阅区域匹配的标识符：

|                     | 区域标识符 |
|--|--|
| **中国东部 2**  | `chinaeast2` |

#### <a name="speech-sdk"></a>语音 SDK

对于主权云中的语音 SDK，需要使用 `SpeechConfig` `--host` [语音 CLI](spx-overview.md)的类或选项的 "从主机" 实例化。  (还可以) 使用 "from 终结点" 实例化和 `--endpoint` 语音 CLI 选项。

`SpeechConfig` 类应实例化，如下所示：
```csharp
var config = SpeechConfig.FromHost(azCnHost, subscriptionKey);
```
应使用语音 CLI，如以下 (记下 `--host`) 选项：
```dos
spx recognize --host "azCnHost" --file myaudio.wav
```
`subscriptionKey`将替换为语音资源密钥。 替换 `azCnHost` 为匹配所需服务产品的表达式和此表中订阅的区域：

|  区域/服务产品 | 主机表达式 |
|--|--|
| **中国东部 2** | |
| 语音转文本 | `wss://chinaeast2.stt.speech.azure.cn` |
| 文本到语音转换 | `https://chinaeast2.tts.speech.azure.cn` |