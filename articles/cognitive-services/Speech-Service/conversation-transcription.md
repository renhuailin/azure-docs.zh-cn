---
title: 对话听录（预览版）- 语音服务
titleSuffix: Azure Cognitive Services
description: 对话听录是一种会议解决方案，它结合了识别、说话人 ID 和分割聚类来提供任何对话的听录。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/26/2021
ms.author: pafarley
ms.openlocfilehash: abef7e44ad2e15bda230d28e8dae74a3fd949f88
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129706328"
---
# <a name="what-is-conversation-transcription-preview"></a>什么是对话听录（预览版）？

对话听录是一种[语音转文本](speech-to-text.md)解决方案，它结合了语音识别、说话人识别以及到每个说话人的句子归属（也称为分割聚类），来提供任何对话的实时和/或异步听录。 对话听录可区分对话中的说话人，以确定谁说了什么以及说话的时间，并使开发者能够轻松地将语音转文本添加到执行多说话人分割聚类的应用程序。

## <a name="key-features"></a>主要功能

- 时间戳 - 每个说话人言语都具有一个时间戳，以便可以轻松地找到说出短语的时间。
- 可读口述文本 - 口述文本会自动添加格式设置和标点符号，以确保文本与所说的内容紧密匹配。
- 用户配置文件 - 通过收集用户语音样本并发送到签名生成服务来生成用户配置文件。
- 说话人识别 - 使用用户配置文件识别说话人，并向每个说话人分配说话人标识符。
- 多说话人分割聚类 - 通过使用每个说话人标识符合成音频流来确定谁说了些什么。
- 实时听录 – 在对话进行期间，提供谁在说了什么以及说话时间的实时口述文本。
- 异步听录 – 使用多声道音频流提供准确度更高的口述文本。

> [!NOTE]
> 虽然对话听录不会对房间中的说话人数量施加限制，但是它针对每个会话 2-10 个说话人进行了优化。

## <a name="get-started"></a>入门

请参阅实时对话听录[快速入门](how-to-use-conversation-transcription.md)以开始使用。

## <a name="use-cases"></a>用例

若要使会议对每个人（例如失聪和有听力障碍的参与者）都具有包容性，进行实时听录非常重要。 实时模式下的对话听录采用会议音频，并确定谁在说些什么，这使所有会议参与者都可按照口述文本参与会议而不会出现延迟。

### <a name="improved-efficiency"></a>提高了效率

会议参与者可以将精力集中在会议上，让对话听录来做笔记。 参与者可以使用口述文本积极参与会议并迅速跟进后续步骤，而无需在会议期间做笔记并可能遗漏某些内容。

## <a name="how-it-works"></a>工作原理

这是对话听录工作原理的简要概述。

![导入对话听录示意图](media/scenarios/conversation-transcription-service.png)

## <a name="expected-inputs"></a>预期输入

- 多声道音频流 – 有关规范和设计详细信息，请参阅 [Microsoft 语音设备 SDK 麦克风](./speech-devices-sdk-microphone.md)。 若要详细了解或购买开发工具包，请参阅[获取 Microsoft 语音设备 SDK](./get-speech-devices-sdk.md)。
- 用户语音样本 - 对话听录需要在对话之前提供用户配置文件，以进行说话人识别。 需要从每个用户收集音频录制内容，然后将录制内容发送到[签名生成服务](https://aka.ms/cts/signaturegenservice)，以验证音频并生成用户配置文件。

> [!NOTE]
> 为实现说话人识别，需要提供用户语音样本进行语音签名。 没有语音样本的说话人将识别为“未识别”。 启用 `DifferentiateGuestSpeakers` 属性后，仍可以对无法识别的说话人进行区分（请参阅下面的示例）。 听录输出随后会将说话人显示为“Guest_0”、“Guest_1”等，而不是将其识别为预先注册的特定说话人姓名。
> ```csharp
> config.SetProperty("DifferentiateGuestSpeakers", "true");
> ```


## <a name="real-time-vs-asynchronous"></a>实时与异步

对话听录提供三种听录模式：

### <a name="real-time"></a>实时

音频数据进行实时处理以返回说话人标识符 + 口述文本。 如果听录解决方案要求是向对话参与者提供正在进行的对话的实时口述文本视图，请选择此模式。 例如，生成应用程序以使失聪和有听力障碍的参与者更容易访问会议是实时听录的理想用例。

### <a name="asynchronous"></a>异步

音频数据进行批处理以返回说话人标识符和口述文本。 如果听录解决方案要求是提供更高准确度，而无需实时口述文本视图，请选择此模式。 例如，如果你要生成应用程序以使会议参与者可以轻松地弥补错过的会议，则使用异步听录模式获取高准确度的听录结果。

### <a name="real-time-plus-asynchronous"></a>实时加异步

音频数据进行实时处理以返回说话人标识符 + 口述文本，此外还会创建一个请求，以通过异步处理获取高准确度口述文本。 如果应用程序需要实时听录，不过也需要更高准确度的口述文本以便在进行对话或会议之后使用，请选择此模式。

## <a name="language-support"></a>语言支持

目前，对话听录在以下区域中支持[所有语音转文本语言](language-support.md#speech-to-text)： `centralus`、`eastasia`、`eastus` 和 `westeurope`。 如果需要其他区域设置支持，请联系[对话听录功能人员](mailto:CTSFeatureCrew@microsoft.com)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [实时听录对话](how-to-use-conversation-transcription.md)
