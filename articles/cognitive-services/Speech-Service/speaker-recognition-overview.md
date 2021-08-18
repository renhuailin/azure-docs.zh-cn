---
title: 说话人识别概述 - 语音服务
titleSuffix: Azure Cognitive Services
description: 说话人识别提供一些算法，可使用语音生物测量，根据说话人独特的语音特征来验证和识别说话人。 说话人识别用于回答“谁在说话？”的问题。 本文概述了说话人识别服务的优势和功能。
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/02/2020
ms.author: lajanuar
ms.custom: cog-serv-seo-aug-2020
keywords: 说话人识别, 语音生物测量
ms.openlocfilehash: a7a47ec86ec7e59cdc78e997f30bd7d0749e4a86
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122070153"
---
# <a name="what-is-speaker-recognition-preview"></a>什么是说话人识别（预览版）？

说话人识别服务提供一些算法，可使用语音生物测量，根据说话人独特的语音特征来验证和识别说话人。 说话人识别用于回答“谁在说话？”的问题。 你为单个说话人提供音频训练数据，这会基于说话人语音的独有特征创建注册配置文件。 然后，你可以根据此配置文件交叉检查音频语音样本，以验证说话人是否是同一个人（说话人验证），或根据一组已注册的说话人配置文件交叉检查音频语音样本，查看它是否与该组中的任何配置文件匹配（说话人标识）。 与此相反，[说话人分割聚类](batch-transcription.md#speaker-separation-diarization)会在批处理操作中按说话人对音频段进行分组。

## <a name="speaker-verification"></a>说话人验证

说话人验证利用通行短语或自由格式的语音输入来简化验证已注册说话人身份的过程。 它可用于在各种解决方案（从呼叫中心的客户身份验证到无接触设施访问）中验证个人以便实现安全、无摩擦客户参与。

### <a name="how-does-speaker-verification-work"></a>说话人验证是如何工作的？

:::image type="content" source="media/speaker-recognition/speaker-rec.png" alt-text="说话人验证流程图。":::

说话人验证可以依赖于文本，也可以独立于文本。 依赖于文本的验证意味着说话人需要选择相同通行短语以在注册和验证阶段进行使用。 独立于文本的验证意味着说话人可以在注册和验证阶段以日常语言说话。

对于依赖于文本的验证，通过从一组预定义短语中说出通信短语来注册说话人的语音。 语音特征从音频录制中进行提取，以构成唯一的语音签名，同时还会识别所选通行短语。 语音签名和通行短语一起用于验证说话人。 

独立于文本的验证在注册期间或在要验证的音频样本中对说话人所说的内容没有限制，因为它仅提取语音特征以对相似性进行评分。 

这些 API 并不旨在用于确定音频是来自真实的个人还是已注册说话人的模仿内容/录制内容。 

## <a name="speaker-identification"></a>说话人识别

说话人识别用于在一组已注册说话人中确定未知说话人的身份。 说话人识别使你能够将语音归属于各个说话人，并从具有多个说话人的场景中解锁值，例如：

* 支持远程会议工作效率解决方案 
* 构建多用户设备个性化

### <a name="how-does-speaker-identification-work"></a>说话人识别是如何工作的？

说话人识别注册独立于文本，也就是说，对说话人在音频中所说的内容没有限制。 与说话人验证类似，在注册阶段，将为说话人录音，并提取语音特征来构成唯一的语音签名。 在标识阶段，输入语音样本会与已注册语音（每个请求中最多 50 个）的指定列表进行比较。

## <a name="data-security-and-privacy"></a>数据安全和隐私

说话人注册数据存储在受保护的系统中，其中包括用于注册的语音音频和语音签名特征。 仅当升级算法，并且需要再次提取特征时，才使用用于注册的语音音频。 在识别阶段，服务不会保留录音，也不会保留已发送到服务的提取语音特征。 

可控制保留数据的时间长度。 你可以通过 API 调用创建、更新和删除说话人的个人注册数据。 删除订阅后，与该订阅关联的所有说话人注册数据也会一并删除。 

与所有认知服务资源一样，使用说话人识别服务的开发人员必须了解 Microsoft 针对客户数据的政策。 你应确保已收到要进行说话人识别的用户提供的相应权限。 有关详细信息，请参阅 Microsoft 信任中心内的 [“认知服务”页](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/) 。 

## <a name="common-questions-and-solutions"></a>常见问题和解决方案

| 问题 | 解决方案 |
|---------|----------|
| 说话人识别可以用于哪些场景？ | 呼叫中心客户验证、基于语音的患者签入、会议听录、多用户设备个性化|
| 识别与验证之间有何区别？ | 识别是检测一组说话人中哪个成员正在说话的过程。 人验证是确认说话人与已知或已注册的语音匹配的操作。|
| 依赖于文本与独立于文本的验证之间有何区别？ | 依赖于文本的验证需要使用特定通行短语来进行注册和识别。 独立于文本的验证需要更长的语音样本进行注册，但可以说出任何内容（包括在识别期间）。|
| 支持哪些语言？ | 英语、法语、西班牙语、中文、德语、意大利语、日语和葡萄牙语 |
| 支持哪些 Azure 区域？ | 说话人识别是预览版服务，目前仅在美国西部区域可用。|
| 支持哪些格式的音频？ | 单声道 16 位，16kHz PCM 编码的 WAV |
| 接受和拒绝响应不准确，如何调整阈值 ？ | 由于最佳阈值随场景的不同而变化很大，因此 API 仅基于默认阈值 0.5 来决定是“接受”还是“拒绝”。 建议高级用户替代默认决策，基于自己的场景对结果进行微调。 |
| 是否可以多次注册一个说话人？ | 可以，对于依赖于文本的验证，最多可以将一个说话人注册 50 次。 对于独立于文本的验证或说话人识别，最多可使用 300 秒的音频进行注册。 |
| 哪些数据存储在 Azure 中？ | 注册音频存储在服务中，直到[删除](./get-started-speaker-recognition.md#deleting-voice-profile-enrollments)语音配置文件。 不保留或存储识别音频样本。 |

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> * 完成说话人识别[基础知识文章](./get-started-speaker-recognition.md)，详细了解可在应用程序中使用的常见设计模式。
> * 请参阅[视频教程](https://azure.microsoft.com/resources/videos/speaker-recognition-text-independent-verification-developer-tutorial/)，了解独立于文本的说话人验证。