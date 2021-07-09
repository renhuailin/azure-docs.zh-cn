---
title: 关键字识别 - 语音服务
titleSuffix: Azure Cognitive Services
description: 概述使用语音软件开发工具包 (SDK) 开发的关键字识别的特性、功能和限制。
services: cognitive-services
author: hasyashah
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/30/2021
ms.author: hasshah
ms.custom: devx-track-csharp
ms.openlocfilehash: 25ab2fb283428f494ca52d769622df4b7a7a7ad1
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2021
ms.locfileid: "110116556"
---
# <a name="keyword-recognition"></a>关键字识别

关键字识别是指语音技术，可识别一段给定音频流中是否存在单词或短语。 它通常称为关键字辨识。 关键字识别最常见的用例是通过语音激活虚拟助理。 例如，“Hey Cortana”是 Cortana 助理的关键字。 识别出关键字后，会执行特定于方案的操作。对于虚拟助理方案，常见的操作是对关键字后面的音频进行语音识别。

通常，虚拟助理始终会侦听。 关键字识别充当用户的隐私边界。 关键字要求充当入口，防止不相关的用户音频从本地设备传输到云。

为了平衡准确度、延迟和计算复杂性，关键字识别以一个多阶段系统的形式实现。 对于第一个阶段之外的所有阶段，只有在此阶段之前的阶段已识别出意向关键字时，才会处理音频。

当前系统设计为具有跨边缘和云的多个阶段：

![跨边缘和云的多个关键字识别阶段。](media/custom-keyword/keyword-recognition-multi-stage.png)

关键字识别的准确度通过以下指标进行衡量：
* **正确接受率 (CA)** - 衡量最终用户说出某个关键字时系统识别该关键字的能力。 这也称为真正率。 
* **错误接受率 (FA)** - 衡量系统筛选出音频中不属于最终用户说出的关键字的能力。 这也称为假正率。

目标是最大程度地提高正确接受率，同时最大程度地降低错误接受率。 当前系统旨在检测前面有短时间沉默的关键字或短语。 不支持检测句子或话语中间的关键字。

## <a name="custom-keyword-for-on-device-models"></a>设备模型的自定义关键字

使用 [Speech Studio 上的自定义关键字门户](https://speech.microsoft.com/customkeyword)，可以通过指定任何字词或短语来生成在边缘执行的关键字识别模型。 可以通过选择适当的发音来进一步对关键字模型进行个性化设置。

### <a name="pricing"></a>定价

使用自定义关键字生成模型（包括基本模型和高级模型）不会产生费用。 通过语音 SDK 在设备上运行模型也不会产生费用。

### <a name="types-of-models"></a>模型类型

通过自定义关键字，可以针对任何关键字生成两种类型的设备模型：

| 模型类型 | 说明 |
| ---------- | ----------- |
| 基本 | 最适合用于演示或快速原型制作。 模型是使用常见的基本模型生成的，最多需要 15 分钟才能准备就绪。 模型可能没有最佳准确度这一特性。 |
| 高级 | 最适合用于产品集成。 模型是使用模拟训练数据对常见基本模型进行调整生成的，提高了准确度。 模型最多需要 48 小时便可准备就绪。 |

这两种模型类型都不需要上传训练数据。 自定义关键字可完全处理数据生成和模型训练。

### <a name="pronunciations"></a>发音

创建新模型时，自定义关键字会自动生成所提供关键字的可能发音。 你可以听取每个发音，并选择最能代表你期望最终用户说出关键字的所有发音。 不应选择所有其他发音。

为确保最佳准确度，请务必谨慎选择发音。 例如，选择超过所需的发音可能导致错误接受率较高。 选择的发音太少，未包含所有预期发音，这可能导致正确接受率较低。

### <a name="testing-models"></a>测试模型

使用自定义关键字生成设备模型后，可直接在门户上对其进行测试。 通过门户，你可以直接在浏览器中讲话，并获得关键字识别结果。

## <a name="keyword-verification"></a>关键字验证

关键字验证是一项云服务，它使用在 Azure 上运行的可靠模型来减少设备模型产生的错误接受的影响。 关键字验证无需任何优化或训练即可处理关键字。 增量模型更新会持续部署到服务以提高准确度和降低延迟，对客户端应用程序完全透明。

### <a name="pricing"></a>定价

关键字验证始终与语音转文本结合使用，除了语音转文本的费用之外，使用关键字验证不产生任何费用。

### <a name="keyword-verification-and-speech-to-text"></a>关键字验证和语音转文本

使用关键字验证时，始终将它与语音转文本结合使用。 这两个服务并行运行。 这意味着会将音频发送给这两个服务以同时进行处理。

![关键字验证和语音转文本的并行处理。](media/custom-keyword/keyword-verification-parallel-processing.png)

并行运行关键字验证和语音转文本具有以下优势：
* **语音转文本结果不存在额外延迟** - 并行执行意味着关键字验证不会增加任何延迟，并且客户端会快速接收语音转文本结果。 如果关键字验证确定音频中不存在关键字，则语音转文本处理将终止，这可防止进行不必要的语音转文本处理。 但是，网络和云模型处理会增加用户感受到的语音激活延迟。 有关详细信息，请参阅[建议和指南](keyword-recognition-guidelines.md)。
* **语音转文本结果中强制生成的关键字前缀** - 语音转文本处理将确保发送给客户端的结果中的关键字都带有前缀。 这可提高关键字后面的语音的语音转文本结果准确度。
* **增加语音转文本超时** - 由于在音频开始时选择了预期存在的关键字，因此语音转文本将允许在关键字后面停顿最多 5 秒，然后再确定语音结束和终止语音转文本处理。 这可确保分段命令 (\<keyword> \<pause> \<command>) 和链接命令 (\<keyword> \<command>) 都可正确处理最终用户体验 。 

### <a name="keyword-verification-responses-and-latency-considerations"></a>关键字验证响应和延迟注意事项

对于向服务发出的每个请求，关键字验证都将返回以下两个响应之一：“已接受”或“已拒绝”。 延迟处理能力取决于关键字的长度和预期包含关键字的音频段的长度。 处理延迟不包括客户端与 Azure 语音服务之间的网络开销。

| 关键字验证响应 | 说明 |
| ----------------------------- | ----------- |
| 已接受 | 表示服务认为关键字存在于作为请求的一部分提供的音频流中。 |
| 已拒绝 | 表示服务认为关键字不存在于作为请求的一部分提供的音频流中。 |

在被拒绝的情况中，通常会产生较高的延迟，因为服务处理的音频比接受的案例更多。 默认情况下，关键字验证将处理最多两秒的音频以搜索关键字。 如果确定关键字不在这两秒钟的内容里，则该服务将超时并向客户端发送拒绝响应。

### <a name="using-keyword-verification-with-on-device-models-from-custom-keyword"></a>在设备端模型使用自定义关键字的关键字验证

语音 SDK 有助于无缝使用结合使用自定义关键字与关键字验证和语音转文本生成的设备端模型。 它以透明方式处理以下操作：
* 基于设备端模型的结果对关键字验证和语音识别进行音频控制。
* 将关键字发送到关键字验证服务。
* 将任何其他元数据发送到云，以协调端到端方案。 

无需显式指定任何配置参数。 将从自定义关键字生成的设备端模型中自动提取所有必要的信息。

以下带有链接的示例和教程演示了如何使用语音 SDK：
 * [GitHub 上的语音助理示例](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant)
 * [教程：通过 Azure 机器人服务使用 C# 语音 SDK 使助手支持语音服务](./tutorial-voice-enable-your-bot-speech-sdk.md)
 * [教程：创建使用简单语音命令的自定义命令应用程序](./how-to-develop-custom-commands-application.md)

## <a name="speech-sdk-integration-and-scenarios"></a>语音 SDK 集成和方案

语音 SDK 有助于轻松使用通过自定义关键字和关键字验证服务生成的个性化设备端关键字识别模型。 为了确保满足产品需求，SDK 支持以下两种方案：

| 方案 | 说明 | 示例 |
| -------- | ----------- | ------- |
| 使用语音转文本的端到端关键字识别 | 最适合于使用由自定义关键字与 Azure 语音的关键字验证和语音转文本服务结合生成的自定义设备端关键字模型的产品。 这是最常见的情况。 | <ul><li>[语音助理示例代码。](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant)</li><li>[教程：通过 Azure 机器人服务使用 C# 语音 SDK 使助手支持语音服务。](./tutorial-voice-enable-your-bot-speech-sdk.md)</li><li>[教程：使用简单的语音命令创建自定义命令应用程序。](./how-to-develop-custom-commands-application.md)</li></ul> |
| 脱机关键字识别 | 最适用于没有网络连接的产品，这些产品将使用自定义关键字生成的自定义设备端关键字模型。 | <ul><li>[Windows UWP 上的 C# 示例。](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/uwp/keyword-recognizer)</li><li>[Android 上的 Java 示例。](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android/keyword-recognizer)</li></ul>

## <a name="next-steps"></a>后续步骤

* [阅读快速入门，使用自定义关键字生成设备端关键字识别模型。](custom-keyword-basics.md)
* [详细了解语音助理。](voice-assistants.md)
