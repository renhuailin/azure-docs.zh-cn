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
ms.date: 08/24/2021
ms.author: alexeyo
ms.openlocfilehash: c7c140b1ff2d3d2151ad8ce01e3ad8f64660543d
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122777889"
---
# <a name="speech-services-in-sovereign-clouds"></a>主权云中的语音服务

## <a name="azure-government-united-states"></a>Azure 政府（美国）

仅适用于美国政府机构及其合作伙伴。 有关 Azure 政府的详细信息，请访问[此处](../../azure-government/documentation-government-welcome.md)和[此处](../../azure-government/compare-azure-government-global-azure.md)。

- **Azure 门户：**
  - [https://portal.azure.us/](https://portal.azure.us/)
- **区域：**
  - US Gov 亚利桑那州
  - US Gov 弗吉尼亚州
- **可用定价层：**
  - 免费层 (F0) 和标准层 (S0)。 有关更多详细信息，请参阅[此处](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)
- **支持的功能：**
  - 语音转文本
    - 自定义语音（声学模型 (AM) 和语言模型 (LM) 适应）
      - [Speech Studio](https://speech.azure.us/)
  - 文本转语音
    - 标准语音
    - 神经语音
  - 语音翻译器
- 不受支持的功能：
  - 自定义语音
- **支持的语言：**
  - 请在[此处](language-support.md)查看支持的语言的列表

### <a name="endpoint-information"></a>终结点信息

本部分中有用于[语音 SDK](speech-sdk.md)、[语音转文本 REST API](rest-speech-to-text.md) 和[文本转语音 REST API](rest-text-to-speech.md) 的语音服务终结点的相关信息。

#### <a name="speech-services-rest-api"></a>语音服务 REST API

Azure 政府中的语音服务 REST API 终结点采用以下格式：

|  REST API 类型/操作 | 终结点格式 |
|--|--|
| 访问令牌 | `https://<REGION_IDENTIFIER>.api.cognitive.microsoft.us/sts/v1.0/issueToken`
| [语音转文本 REST API v3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) | `https://<REGION_IDENTIFIER>.api.cognitive.microsoft.us/<URL_PATH>` |
| [适用于短音频的语音转文本 REST API](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) | `https://<REGION_IDENTIFIER>.stt.speech.azure.us/<URL_PATH>` |
| [文本转语音 REST API](rest-text-to-speech.md) | `https://<REGION_IDENTIFIER>.tts.speech.azure.us/<URL_PATH>` |

将 `<REGION_IDENTIFIER>` 替换为与下表中的订阅区域匹配的标识符：

|                     | 区域标识符 |
|--|--|
| **US Gov 亚利桑那州**  | `usgovarizona` |
| **US Gov 弗吉尼亚州** | `usgovvirginia` |

#### <a name="speech-sdk"></a>语音 SDK

对于主权云中的语音 SDK，你需要使用[语音 CLI](spx-overview.md) 的 `SpeechConfig` 类或 `--host` 选项的“从主机”实例化。 （也可使用“从端点”实例化和 `--endpoint` 语音 CLI 选项）。

应按如下所示将 `SpeechConfig` 类进行实例化：
```csharp
var config = SpeechConfig.FromHost(usGovHost, subscriptionKey);
```
应按以下方式使用语音 CLI （请注意 `--host` 选项）：
```dos
spx recognize --host "usGovHost" --file myaudio.wav
```
将 `subscriptionKey` 替换为语音资源密钥。 将 `usGovHost` 替换为匹配所需服务产品/服务以及此表中订阅区域的表达式：

|  区域/服务产品 | 主机表达式 |
|--|--|
| **US Gov 亚利桑那州** | |
| 语音转文本 | `wss://usgovarizona.stt.speech.azure.us` |
| 文本到语音转换 | `https://usgovarizona.tts.speech.azure.us` |
| **US Gov 弗吉尼亚州** | |
| 语音转文本 | `wss://usgovvirginia.stt.speech.azure.us` |
| 文本到语音转换 | `https://usgovvirginia.tts.speech.azure.us` |


## <a name="azure-china"></a>Azure 中国

可供在中国开展业务的组织使用。 有关 Azure 中国的详细信息，请查看[此处](/azure/china/overview-operations)。 


- **Azure 门户：**
  - [https://portal.azure.cn/](https://portal.azure.cn/)
- **区域：**
  - 中国东部 2
  - 中国北部 2
- **可用定价层：**
  - 免费层 (F0) 和标准层 (S0)。 有关更多详细信息，请参阅[此处](https://www.azure.cn/pricing/details/cognitive-services/index.html)
- **支持的功能：**
  - 语音转文本
    - 自定义语音（声学模型 (AM) 和语言模型 (LM) 适应）
      - [Speech Studio](https://speech.azure.cn/)
  - 文本转语音
    - 标准语音
    - 神经语音
  - 语音翻译器
- 不受支持的功能：
  - 自定义语音
- **支持的语言：**
  - 请在[此处](language-support.md)查看支持的语言的列表

### <a name="endpoint-information"></a>终结点信息

本部分中有用于[语音 SDK](speech-sdk.md)、[语音转文本 REST API](rest-speech-to-text.md) 和[文本转语音 REST API](rest-text-to-speech.md) 的语音服务终结点的相关信息。

#### <a name="speech-services-rest-api"></a>语音服务 REST API

Azure 中国中的语音服务 REST API 终结点采用以下格式：

|  REST API 类型/操作 | 终结点格式 |
|--|--|
| 访问令牌 | `https://<REGION_IDENTIFIER>.api.cognitive.azure.cn/sts/v1.0/issueToken`
| [语音转文本 REST API v3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) | `https://<REGION_IDENTIFIER>.api.cognitive.azure.cn/<URL_PATH>` |
| [适用于短音频的语音转文本 REST API](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) | `https://<REGION_IDENTIFIER>.stt.speech.azure.cn/<URL_PATH>` |
| [文本转语音 REST API](rest-text-to-speech.md) | `https://<REGION_IDENTIFIER>.tts.speech.azure.cn/<URL_PATH>` |

将 `<REGION_IDENTIFIER>` 替换为与下表中的订阅区域匹配的标识符：

|                     | 区域标识符 |
|--|--|
| **中国东部 2**  | `chinaeast2` |
| **中国北部 2**  | `chinanorth2` |

#### <a name="speech-sdk"></a>语音 SDK

对于主权云中的语音 SDK，你需要使用[语音 CLI](spx-overview.md) 的 `SpeechConfig` 类或 `--host` 选项的“从主机”实例化。 （也可使用“从端点”实例化和 `--endpoint` 语音 CLI 选项）。

应按如下所示将 `SpeechConfig` 类进行实例化：
```csharp
var config = SpeechConfig.FromHost(azCnHost, subscriptionKey);
```
应按以下方式使用语音 CLI （请注意 `--host` 选项）：
```dos
spx recognize --host "azCnHost" --file myaudio.wav
```
将 `subscriptionKey` 替换为语音资源密钥。 将 `azCnHost` 替换为匹配所需服务产品/服务以及此表中订阅区域的表达式：

|  区域/服务产品 | 主机表达式 |
|--|--|
| **中国东部 2** | |
| 语音转文本 | `wss://chinaeast2.stt.speech.azure.cn` |
| 文本到语音转换 | `https://chinaeast2.tts.speech.azure.cn` |
| **中国北部 2** | |
| 语音转文本 | `wss://chinanorth2.stt.speech.azure.cn` |
| 文本到语音转换 | `https://chinanorth2.tts.speech.azure.cn` |