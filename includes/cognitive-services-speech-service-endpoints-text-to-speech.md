---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 05/06/2019
ms.author: wolfma
ms.openlocfilehash: 650ce0cc9586118b30593767c6a3ddb92f494ac3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "95972636"
---
### <a name="standard-and-neural-voices"></a>标准语音和神经语音

使用下表按区域/终结点确定标准语音和神经语音的可用性：

| 区域 | 端点 | 标准语音 | 神经语音 |
|--------|----------|-----------------|---------------|
| 澳大利亚东部 | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/v1` | 是 | 是 |
| Brazil South | `https://brazilsouth.tts.speech.microsoft.com/cognitiveservices/v1` | 是 | 否 |
| 加拿大中部 | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/v1` | 是 | 是 |
| 美国中部 | `https://centralus.tts.speech.microsoft.com/cognitiveservices/v1` | 是 | 否 |
| 东亚 | `https://eastasia.tts.speech.microsoft.com/cognitiveservices/v1` | 是 | 否 |
| 美国东部 | `https://eastus.tts.speech.microsoft.com/cognitiveservices/v1` | 是 | 是 |
| 美国东部 2 | `https://eastus2.tts.speech.microsoft.com/cognitiveservices/v1` | 是 | 否 |
| 法国中部 | `https://francecentral.tts.speech.microsoft.com/cognitiveservices/v1` | 是 | 否 |
| 印度中部 | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/v1` | 是 | 是 |
| Japan East | `https://japaneast.tts.speech.microsoft.com/cognitiveservices/v1` | 是 | 否 |
| 日本西部 | `https://japanwest.tts.speech.microsoft.com/cognitiveservices/v1` | 是 | 否 |
| 韩国中部 | `https://koreacentral.tts.speech.microsoft.com/cognitiveservices/v1` | 是 | 否 |
| 美国中北部 | `https://northcentralus.tts.speech.microsoft.com/cognitiveservices/v1` | 是 | 否 |
| 北欧 | `https://northeurope.tts.speech.microsoft.com/cognitiveservices/v1` | 是 | 否 |
| 美国中南部 | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/v1` | 是 | 是 |
| Southeast Asia | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/v1` | 是 | 是 |
| 英国南部 | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/v1` | 是 | 是 |
| 西欧 | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/v1` | 是 | 是 |
| 美国西部 | `https://westus.tts.speech.microsoft.com/cognitiveservices/v1` | 是 | 否 |
| 美国西部 2 | `https://westus2.tts.speech.microsoft.com/cognitiveservices/v1` | 是 | 是 |

> [!TIP]
> [预览版](../articles/cognitive-services/Speech-Service/language-support.md#neural-voices-in-preview) 中的语音仅在以下3个区域提供：美国东部、西欧和东南亚。

### <a name="custom-voices"></a>自定义语音

如果已经创建了自定义语音字体，请使用已创建的终结点。 还可以使用下面列出的终结点，并将 `{deploymentId}` 替换为语音模型的部署 ID。

| 区域 | 终结点 |
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
