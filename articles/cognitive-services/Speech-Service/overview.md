---
title: 什么是语音服务？
titleSuffix: Azure Cognitive Services
description: 语音服务在单个 Azure 订阅中统合了语音转文本、文本转语音以及语音翻译功能。 使用语音 SDK、语音设备 SDK 或 REST API 在应用程序、工具和设备中添加语音。
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 11/23/2020
ms.author: nitinme
ms.openlocfilehash: 8e173f8f11f45fc61ee412c7241a5f9297c807bb
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2021
ms.locfileid: "114708223"
---
# <a name="what-is-the-speech-service"></a>什么是语音服务？

语音服务在单个 Azure 订阅中统合了语音转文本、文本转语音以及语音翻译功能。 使用[语音 CLI](spx-overview.md)、[语音 SDK](./speech-sdk.md)、[语音设备 SDK](./speech-devices-sdk-quickstart.md?pivots=platform-android)、[Speech Studio](speech-studio-overview.md) 或 [REST API](#reference-docs) 可以轻松在应用程序、工具和设备中启用语音。

> [!IMPORTANT]
> 语音服务已替代必应语音 API 和语音翻译。 有关迁移说明，请参阅迁移部分。

以下功能是语音服务的一部分。 请使用下表中的链接详细了解每项功能的常见用例或浏览 API 参考信息。

| 服务 | 功能 | 说明 | SDK 中 IsInRole 中的声明 | REST |
|---------|---------|-------------|-----|------|
| [语音转文本](speech-to-text.md) | 实时语音转文本 | 语音转文本可将音频流或本地文件实时转录或翻译为文本，应用程序、工具或设备可以使用或显示这些文本。 结合[语言理解 (LUIS)](../luis/index.yml) 使用语音转文本可以从听录的语音中派生用户意向，以及处理语音命令。 | [是](./speech-sdk.md) | [是](#reference-docs) |
| | [批量语音转文本](batch-transcription.md) | 批量语音转文本支持对 Azure Blob 存储中存储的大量语音音频数据进行异步语音到文本转录。 除了将语音音频转换为文本，批量语音转文本还允许进行分割聚类和情感分析。 | 否 | [是](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0) |
| | [多设备对话](multi-device-conversation.md) | 在对话中连接多个设备或客户端以发送基于语音或文本的消息，并轻松支持听录和翻译| 是 | 否 |
| | [对话听录](./conversation-transcription.md) | 启用实时语音识别、说话人识别和分割聚类。 它非常适合用于听录能够区分说话人的面对面会谈场景。 | 是 | 否 |
| | [创建自定义语音识别模型](#customize-your-speech-experience) | 如果使用语音转文本在独特的环境中进行识别和听录，则可以创建并训练自定义的声学、语言和发音模型，以解决环境干扰或行业特定的词汇。 | 否 | [是](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0) |
| | [发音评估](./how-to-pronunciation-assessment.md) | 发音评估可以评估语音发音，并为说话人提供有关讲话音频准确度和流利度的反馈。 通过发音评估，语言学习者可以练习、获得即时反馈并改进其发音，因此能够自信地讲话和演示。 | [是](./how-to-pronunciation-assessment.md) | [是](./rest-speech-to-text.md#pronunciation-assessment-parameters) |
| [文本转语音](text-to-speech.md) | 文本转语音 | 文本转语音可使用[语音合成标记语言 (SSML)](speech-synthesis-markup.md) 将输入文本转换为类似人类的合成语音。 使用神经语音，这是由深度神经网络提供支持的类人语音。 请参阅[语言支持](language-support.md)。 | [是](./speech-sdk.md) | [是](#reference-docs) |
| | [创建自定义语音](#customize-your-speech-experience) | 创建专属于品牌或产品的自定义语音字体。 | 否 | [是](#reference-docs) |
| [语音翻译](speech-translation.md) | 语音翻译 | 使用语音翻译可在应用程序、工具和设备中实现实时的多语言语音翻译。 进行语音转语音和语音转文本翻译时可以使用此服务。 | [是](./speech-sdk.md) | 否 |
| [语音助手](voice-assistants.md) | 语音助手 | 语音助手使用语音服务为开发人员助力，使他们可为其应用程序和体验创建自然的、类似于人类的对话界面。 语音助理服务在设备与助理实现之间提供快速可靠的交互。该实现使用 Bot Framework 的 Direct Line Speech 通道或集成的自定义命令服务来完成任务。 | [是](voice-assistants.md) | 否 |
| [说话人识别](speaker-recognition-overview.md) | 说话人验证和标识 | 说话人识别服务提供根据其独特的语音特征来验证和识别说话人的算法。 说话人识别用于回答“谁在说话？”的问题。 | 是 | [是](/rest/api/speakerrecognition/) |


[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

## <a name="try-the-speech-service-for-free"></a>免费试用语音服务

若要执行以下步骤，需要一个 Microsoft 帐户和一个 Azure 帐户。 如果没有 Microsoft 帐户，可以在 [Microsoft 帐户门户](https://account.microsoft.com/account)上注册一个免费帐户。 选择“Microsoft 登录”，然后，当系统要求登录时，选择“创建 Microsoft 帐户” 。 按步骤创建并验证新的 Microsoft 帐户。

具有 Azure 帐户后，请转到 [Azure 注册页面](https://azure.microsoft.com/free/ai/)，选择“免费开始使用”，然后使用 Microsoft 帐户创建新的 Azure 帐户。 以下是[如何注册 Azure 免费帐户](https://www.youtube.com/watch?v=GWT2R1C_uUU)的视频。

> [!NOTE]
> 注册 Azure 免费帐户时，该帐户附带 200 美元的服务额度，可用于支付长达 30 天的付费语音服务订阅。 当额度用尽或 30 天期限已过，将禁用 Azure 服务。 若要继续使用 Azure 服务，必须升级帐户。 有关详细信息，请参阅[如何升级 Azure 免费帐户](../../cost-management-billing/manage/upgrade-azure-subscription.md)。 
>
> 语音服务有两个服务层：免费 (f0) 和订阅 (s0)，它们有不同的限制和优点。 如果使用免费的低流量语音服务层级，即使是在免费试用帐户或服务额度过期之后，也仍可以保留此免费订阅。 有关详细信息，请参阅[认知服务定价 - 语音服务](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)。

### <a name="create-the-azure-resource"></a>创建 Azure 资源

若要将语音服务资源（免费层或付费层）添加到 Azure 帐户，请执行以下步骤：

1. 使用你的 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com/)。

1. 选择门户左上角的“创建资源”。  如果未看到“创建资源”，可通过选择屏幕左上角的折叠菜单找到它。

1. 在“新建”窗口中的搜索框内键入“语音”，然后按 ENTER。 

1. 在搜索结果中，选择“语音”。 
   
   :::image type="content" source="media/index/speech-search.png" alt-text="在 Azure 门户中创建语音资源。":::

1. 选择“创建”，然后： 

   - 为新资源指定唯一的名称。 名称有助于区分绑定到同一服务的多个订阅。
   - 选择新资源关联的 Azure 订阅，以确定计费方式。 以下是在 Azure 门户中[如何创建 Azure 订阅](../../cost-management-billing/manage/create-subscription.md#create-a-subscription-in-the-azure-portal)的介绍。
   - 选择将使用资源的[区域](regions.md)。 Azure 是一个全球性云平台，在世界各地的许多区域都可以使用。 若要获得最佳性能，请选择离你最近或应用程序运行的区域。 语音服务的可用性因地区而异。 请确保在受支持的区域中创建资源。 请参阅[语音服务的区域支持](./regions.md#speech-to-text-text-to-speech-and-translation).
   - 选择免费 (F0) 或付费 (S0) 定价层。 请选择“查看全部定价详细信息”或参阅[语音服务定价](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)，来获取每个层的定价和用量配额的完整信息。 有关资源的限制，请参阅 [Azure 认知服务限制](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-cognitive-services-limits)。
   - 为此“语音”订阅创建新的资源组或将订阅分配到现有资源组。 资源组有助于使多种 Azure 订阅保持有序状态。
   - 选择“创建”  。 系统随后会将你转到部署概述，并显示部署进度消息。  
<!--
> [!NOTE]
> You can create an unlimited number of standard-tier subscriptions in one or multiple regions. However, you can create only one free-tier subscription. Model deployments on the free tier that remain unused for 7 days will be decommissioned automatically.
-->
部署新的语音资源需要花费片刻时间。 

### <a name="find-keys-and-locationregion"></a>查找密钥和位置/区域

若要查找已完成部署的密钥和位置/区域，请按照下列步骤操作：

1. 使用你的 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com/)。

2. 选择“所有资源”，然后选择你的认知服务资源的名称。

3. 在左侧窗格中的“资源管理”下，选择“密钥和终结点” 。

每个订阅有两个密钥；可在应用程序中使用任意一个密钥。 若要将密钥复制/粘贴到代码编辑器或其他区域，请选择每个密钥旁边的复制按钮，切换窗口以将剪贴板内容粘贴到所需区域。

此外，请复制 `LOCATION` 值，这是你用于 SDK 调用的区域 ID（例如 `westus`、`westeurope`）。

> [!IMPORTANT]
> 这些订阅密钥用于访问认知服务 API。 不要共享你的密钥。 安全存储密钥 - 例如，使用 Azure Key Vault。 此外，我们建议定期重新生成这些密钥。 发出 API 调用只需一个密钥。 重新生成第一个密钥时，可以使用第二个密钥来持续访问服务。

## <a name="complete-a-quickstart"></a>完成快速入门

我们提供了适用于大多数流行编程语言的快速入门，旨在让你了解基本设计模式并帮助你在 10 分钟以内运行代码。 请参阅以下列表，了解每项功能的快速入门。

* [语音转文本快速入门](get-started-speech-to-text.md)
* [文本转语音快速入门](get-started-text-to-speech.md)
* [语音翻译快速入门](./get-started-speech-translation.md)
* [意向识别快速入门](./get-started-intent-recognition.md)
* [说话人辨识快速入门](./get-started-speaker-recognition.md)

在你有机会开始使用语音服务后，请尝试一下我们的教程，了解如何处理各种情况。

- [教程：使用适用于 C# 的语音 SDK 和 LUIS 从语音中识别意向](how-to-recognize-intents-from-speech-csharp.md)
- [教程：使用语音 SDK、C# 为机器人启用语音](tutorial-voice-enable-your-bot-speech-sdk.md)
- [教程：生成 Flask 应用以翻译文本、分析情绪以及将翻译后的文本合成为语音 - REST](../translator/tutorial-build-flask-app-translation-synthesis.md?bc=%2fazure%2fcognitive-services%2fspeech-service%2fbreadcrumb%2ftoc.json%252c%2fen-us%2fazure%2fbread%2ftoc.json&toc=%2fazure%2fcognitive-services%2fspeech-service%2ftoc.json%252c%2fen-us%2fazure%2fcognitive-services%2fspeech-service%2ftoc.json)

## <a name="get-sample-code"></a>获取示例代码

GitHub 上提供了语音服务的示例代码。 这些示例涵盖了常见方案，例如，从文件或流中读取音频、连续和单次识别，以及使用自定义模型。 使用以下链接查看 SDK 和 REST 示例：

- [语音转文本、文本转语音和语音翻译示例 (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [批量听录示例 (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
- [文本转语音示例 (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)
- [语音助手示例 (SDK)](https://aka.ms/csspeech/samples)

## <a name="customize-your-speech-experience"></a>自定义语音体验

语音服务能够很好地与内置模型配合工作，但是，你可能想要根据自己的产品或环境，进一步自定义和优化体验。 自定义选项的范围从声学模型优化，到专属于自有品牌的语音字体。

其他产品提供了针对特定用途（如卫生保健或保险）而优化的语音模型，但可供所有人平等地使用。 Azure 语音的自定义功能将成为你的独特竞争优势部分，而其他任何用户或客户都无法使用。 换句话说，你的模型是私人的，仅针对你的用例进行自定义调整。

| 语音服务 | 平台 | 说明 |
| -------------- | -------- | ----------- |
| 语音转文本 | [自定义语音识别](./custom-speech-overview.md) | 根据需要和可用数据自定义语音识别模型。 克服语音识别障碍，如说话风格、词汇和背景噪音。 |
| 文本转语音 | [自定义语音](https://aka.ms/customvoice) | 使用可用语音数据为文本转语音应用生成可识别的独一无二的语音。 可以通过调整一组语音参数来进一步微调语音输出。 |

## <a name="deploy-on-premises-using-docker-containers"></a>使用 Docker 容器进行本地部署

[使用语音服务容器](speech-container-howto.md)在本地部署 API 功能。 借助这些 Docker 容器，你能够将服务进一步引入数据，以满足合规性、安全性或其他操作目的。 语音服务提供以下容器：

* 标准语音转文本
* 自定义语音转文本
* 标准文本转语音
* 神经文本转语音
* 自定义文本转语音（预览版）
* 语音语言识别（预览版）

## <a name="reference-docs"></a>参考文档

- [语音 SDK](./speech-sdk.md)
- [语音设备 SDK](speech-devices-sdk.md)
- [REST API：语音转文本](rest-speech-to-text.md)
- [REST API：文本转语音](rest-text-to-speech.md)
- [REST API：批量听录和自定义](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)


## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [开始使用语音转文本](./get-started-speech-to-text.md)
> [开始使用文本转语音](get-started-text-to-speech.md)