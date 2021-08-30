---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 05/06/2019
ms.author: wolfma
ms.openlocfilehash: 95f69f04ec1aa0fb0c5588647709d8d938ad2a53
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114441323"
---
### <a name="neural-and-standard-voices"></a>神经和标准语音

使用此表按区域/终结点确定神经和标准语音的可用性：

| 区域 | 端点 |
|--------|----------|
| 澳大利亚东部 | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/v1` |
| 巴西南部 | `https://brazilsouth.tts.speech.microsoft.com/cognitiveservices/v1` |
| 加拿大中部 | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/v1` |
| 美国中部 | `https://centralus.tts.speech.microsoft.com/cognitiveservices/v1` |
| 东亚 | `https://eastasia.tts.speech.microsoft.com/cognitiveservices/v1` |
| 美国东部 | `https://eastus.tts.speech.microsoft.com/cognitiveservices/v1` |
| 美国东部 2 | `https://eastus2.tts.speech.microsoft.com/cognitiveservices/v1` |
| 法国中部 | `https://francecentral.tts.speech.microsoft.com/cognitiveservices/v1` |
| 印度中部 | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/v1` |
| 日本东部 | `https://japaneast.tts.speech.microsoft.com/cognitiveservices/v1` |
| 日本西部 | `https://japanwest.tts.speech.microsoft.com/cognitiveservices/v1` |
| 韩国中部 | `https://koreacentral.tts.speech.microsoft.com/cognitiveservices/v1` |
| 美国中北部 | `https://northcentralus.tts.speech.microsoft.com/cognitiveservices/v1` |
| 北欧 | `https://northeurope.tts.speech.microsoft.com/cognitiveservices/v1` |
| 美国中南部 | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/v1` |
| 东南亚 | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/v1` |
| 英国南部 | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/v1` |
| 美国中西部 | `https://westcentralus.tts.speech.microsoft.com/cognitiveservices/v1` |
| 西欧 | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/v1` |
| 美国西部 | `https://westus.tts.speech.microsoft.com/cognitiveservices/v1` |
| 美国西部 2 | `https://westus2.tts.speech.microsoft.com/cognitiveservices/v1` |

> [!TIP]
> [预览版语音](../articles/cognitive-services/Speech-Service/language-support.md#neural-voices-in-preview)只在以下 3 个区域提供：美国东部、西欧和东南亚。

### <a name="custom-voices"></a>自定义语音

如果已经创建了自定义语音字体，请使用已创建的终结点。 还可以使用下面列出的终结点，并将 `{deploymentId}` 替换为语音模型的部署 ID。

| 区域 | 端点 |
|--------|----------|
| 澳大利亚东部 | `https://australiaeast.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 巴西南部 | `https://brazilsouth.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 加拿大中部 | `https://canadacentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 美国中部 | `https://centralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 东亚 | `https://eastasia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 美国东部 | `https://eastus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 美国东部 2 | `https://eastus2.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 法国中部 | `https://francecentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 印度中部 | `https://centralindia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 日本东部 | `https://japaneast.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 日本西部 | `https://japanwest.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 韩国中部 | `https://koreacentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 美国中北部 | `https://northcentralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 北欧 | `https://northeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 美国中南部 | `https://southcentralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 东南亚 | `https://southeastasia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 英国南部 | `https://uksouth.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 西欧 | `https://westeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 美国西部 | `https://westus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 美国西部 2 | `https://westus2.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |

### <a name="custom-neural-voice"></a>自定义神经语音

下表详细说明了对自定义神经语音功能的区域支持。

| 功能 | 支持的区域 |
|---|---|
| 语音模型托管 | 美国东部、美国西部 2、美国中南部、东南亚、英国南部、西欧、澳大利亚东部 |
| 实时字符数 | 美国东部、美国西部 2、美国中南部、东南亚、英国南部、西欧、澳大利亚东部 |
| 长音频字符数 | 美国东部、西欧、东南亚、英国南部、印度中部 |
| 自定义神经训练 | 美国东部、东南亚、英国南部 |

### <a name="long-audio-api"></a>长音频 API

长音频 API 可用于具有单独终结点的多个区域。

| 区域 | 端点 |
|--------|----------|
| 美国东部 | `https://eastus.customvoice.api.speech.microsoft.com` |
| 印度中部 | `https://centralindia.customvoice.api.speech.microsoft.com` |
| 东南亚 | `https://southeastasia.customvoice.api.speech.microsoft.com` |
| 英国南部 | `https://uksouth.customvoice.api.speech.microsoft.com` |
| 西欧 | `https://westeurope.customvoice.api.speech.microsoft.com` |
