---
title: 如何备份和恢复语音自定义资源
titleSuffix: Azure Cognitive Services
description: 了解如何使用自定义语音识别和自定义语音为服务中断做好准备。
services: cognitive-services
author: masakiitagaki
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/28/2021
ms.author: mitagaki
ms.openlocfilehash: 0f540025561b6e452371a74093133bf3e1183b1b
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124744127"
---
# <a name="back-up-and-recover-speech-customization-resources"></a>备份和恢复语音自定义资源

语音服务[可在各个区域中均可使用](./regions.md)。 服务订阅密钥将绑定到单个区域。 获取密钥时，请选择数据、模型和部署所在的特定区域。

数据集（包含客户创建的数据资产，如自定义语音模型和自定义语音字体）也仅在部署服务的区域中可用。 此类资产包括：

**自定义语音识别**
-   训练音频/文本数据
-   测试音频/文本数据
-   自定义语音模型
-   日志数据

**自定义语音**
-   训练音频/文本数据
-   测试音频/文本数据
-   自定义语音字体

有些客户会使用默认终结点听录音频或标准语音用于语音合成，另一些客户则会创建用于自定义的资产。

这些资产由存储库本身定期自动备份，因此当某个区域不可用时，也不会发生数据丢失。 但是，仍须采取措施来确保发生区域中断时的服务连续性。

## <a name="how-to-monitor-service-availability"></a>如何监视服务可用性

如果使用默认终结点，则应将客户端代码配置为监视错误，如果错误仍然存在，请准备好重定向到你选择的另一服务订阅区域。

请按照以下步骤将客户端配置为监视错误：

1.  在文档中[查找各区域的可用终结点列表](./rest-speech-to-text.md)。
2.  从列表中选择一个主要区域以及一个或多个次要/备份区域。
3. 从 Azure 门户为每个区域创建语音服务资源。
    -  如果已设置特定配额，还可以考虑在备份区域中设置相同的配额。 请参阅[语音服务配额和限制](./speech-services-quotas-and-limits.md)，了解详细信息。

4.  请注意，每个区域都有自己的 STS 令牌服务。 对于主要区域以及任何备份区域，客户端配置文件需要知道以下内容：
    -  区域语音服务终结点
    -  [区域订阅密钥和区域代码](./rest-speech-to-text.md)

5.  配置代码用于监视连接错误（常见错误为连接超时和服务不可用）。 以下是 C# 中的示例代码：[GitHub：添加示例用于显示切换区域的可能候选项](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/fa6428a0837779cbeae172688e0286625e340942/samples/csharp/sharedcontent/console/speech_recognition_samples.cs#L965)。

    1.  由于网络遇到暂时性错误，因此对于出现的个别连接问题，建议重试。
    2.  对于持续出现的问题，请将流量重定向到新的 STS 令牌服务和语音服务终结点。 （对于文本转语音，请参考示例代码：[GitHub：TTS 公共语音切换区域](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_synthesis_samples.cs#L880)）。

对于此使用类型，从区域性故障中恢复可以即时进行，而且成本非常低。 所需的全部操作是在客户端上开发此功能。 在假设没有音频流的备份的情况下，遭受的数据损失最小。

## <a name="custom-endpoint-recovery"></a>自定义终结点恢复

某一区域的数据资产、模型或部署在任何其他区域中不可见，也不可访问。

应按照用于默认终结点的相同步骤，同时在主要和次要区域中创建语音服务资源。

### <a name="custom-speech"></a>自定义语音识别

自定义语音识别服务不支持自动故障转移。 建议执行以下步骤，为在客户端代码中实现手动或自动故障转移做好准备。 这些步骤中将会复制次要区域中的自定义模型。 做好以下准备后，当主要区域发生故障时，客户端代码即可切换到次要区域。

1.  在某一主要区域中创建自定义模型。
2.  运行[模型复制 API](https://eastus2.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/CopyModelToSubscription)，将自定义模型复制到所有备用（次要）区域。
3.  转到 Speech Studio 以加载复制的模型，然后在次要区域中创建新的终结点。 若要了解如何部署新模型，请参阅[训练和部署自定义语音识别模型](./how-to-custom-speech-train-model.md)。
    -  如果已设置特定配额，还可以考虑在备份区域中设置相同的配额。 请参阅[语音服务配额和限制](./speech-services-quotas-and-limits.md)，了解详细信息。
4.  应按照用于默认终结点的相同步骤，将客户端配置为对持续性错误进行故障转移。

客户端代码可以监视主要区域中已部署模型的可用性，并在主要区域发生故障时将其音频流量重定向到次要区域。 如果不需要实时故障转移，仍可按照以下步骤为手动故障转移做好准备。

#### <a name="offline-failover"></a>脱机故障转移

如果不需要实时故障转移，可以决定导入数据，稍后在次要区域中创建和部署模型，同时请了解这些任务需要一些时间才能完成。

#### <a name="failover-time-requirements"></a>故障转移时间要求

本部分提供有关用时的一般指导。 以下是使用[代表性测试数据集](https://github.com/microsoft/Cognitive-Custom-Speech-Service)估计脱机故障转移用时的记录。

-   数据上传到新区域：15 分钟
-   声学/语言模型创建：6 小时（具体用时取决于数据量）
-   模型评估：30 分钟
-   终结点部署：10 分钟
-   模型复制 API 调用：10 分钟
-   客户端代码的重新配置和部署：具体用时取决于客户端系统

尽管如此，仍建议为具有实时要求的生产模型创建主要和次要区域的密钥。

### <a name="custom-voice"></a>自定义语音

自定义语音不支持自动故障转移。 可使用以下两个选项处理实时合成失败。

选项 1：故障转移到同一区域中的公共语音。

当自定义语音实时合成失败时，则故障转移到公共语音（客户端示例代码：[GitHub：将自定义语音故障转移到公共语音](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_synthesis_samples.cs#L899)）。

检查[可用的公共语音](./language-support.md#neural-voices)。 如果需要故障转移到其他语音或其他区域，还可以更改上面的示例代码。

选项 2：故障转移到另一区域中的自定义语音。

1.  在某一主要区域中创建和部署自定义语音。
2.  在 [Speech Studio](https://speech.microsoft.com) 中，将自定义语音模型复制到另一区域（次要区域）。
3.  转到 Speech Studio 并切换到次要区域中的语音资源。 加载复制的模型并创建新的终结点。
    -   语音模型部署通常在 3 分钟内完成。
    -   请注意：附加终结点需要额外收费。 [请在此处查看模型托管的定价](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)。

4.  将客户端配置为故障转移到次要区域。 请参阅 C# 中的示例代码：[GitHub：自定义语音故障转移到次要区域](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_synthesis_samples.cs#L920)。