---
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/18/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: dd1552dda28291112a2412bdf956bc49a0b541d7
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2020
ms.locfileid: "95996374"
---
容器提供基于 websocket 的查询终结点 Api，可通过 [语音 SDK](../index.yml)进行访问。 默认情况下，语音 SDK 使用联机语音服务。 若要使用该容器，需要更改初始化方法。

> [!TIP]
> 使用带有容器的语音 SDK 时，无需提供 Azure 语音资源 [订阅密钥或身份验证持有者令牌](../rest-speech-to-text.md#authentication)。

请参下面的示例。

# <a name="c"></a>[C#](#tab/csharp)

请从使用此 Azure 云初始化调用：

```csharp
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

将此调用与容器 [主机](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost?preserve-view=true&view=azure-dotnet)一起使用：

```csharp
var config = SpeechConfig.FromHost(
    new Uri("ws://localhost:5000"));
```

# <a name="python"></a>[Python](#tab/python)

请从使用此 Azure 云初始化调用：

```python
speech_config = speechsdk.SpeechConfig(
    subscription=speech_key, region=service_region)
```

将此调用与容器 [终结点](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?preserve-view=true&view=azure-python)一起使用：

```python
speech_config = speechsdk.SpeechConfig(
    endpoint="ws://localhost:5000/speech/recognition/conversation/cognitiveservices/v1"
```

---