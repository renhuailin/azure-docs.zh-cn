---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 10/09/2020
ms.author: v-jawe
ms.openlocfilehash: 0964872d03d6e321d25d51a18edbb4a6f0be8a4f
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2020
ms.locfileid: "94424717"
---
本快速入门介绍如何使用语音服务和 cURL 将语音转换为文本。

若要深入了解语音转文本的概念，请参阅[概述](../../../speech-to-text.md)一文。

## <a name="prerequisites"></a>先决条件

本文假定你有 Azure 帐户和语音服务订阅。 如果你没有帐户和订阅，[可以免费试用语音服务](../../../overview.md#try-the-speech-service-for-free)。

## <a name="convert-speech-to-text"></a>将语音转换为文本

请在命令提示符处运行以下命令。 需要将以下值插入到命令中。
- 语音服务订阅密钥。
- 你的语音服务区域。
- 输入的音频文件路径。 可以使用[语音转文本](../../../get-started-text-to-speech.md)来生成音频文件。

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speech-to-text.sh" id="request":::

应收到类似于下面的响应。

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speech-to-text.sh" id="response":::

有关详细信息，请参阅[语音转文本 REST API 参考](../../../rest-speech-to-text.md)。