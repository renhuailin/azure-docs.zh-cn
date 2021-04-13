---
title: 通过自定义语音改进合成 - 语音服务
titleSuffix: Azure Cognitive Services
description: 自定义语音是一组在线工具，使用它可为自有品牌创建可识别的独一无二的语音。 只需准备好几个音频文件和关联的听录内容即可完全入门。 请遵循以下链接开始创建自定义语音转文本体验。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: trbye
ms.openlocfilehash: 99af0ee46e2827ac8a5749d58bb19cf85f96aa46
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "104577455"
---
# <a name="get-started-with-custom-voice"></a>自定义语音入门

[自定义语音](https://aka.ms/customvoice)是一组在线工具，使用它可为自有品牌创建可识别的独一无二的语音。 只需准备好几个音频文件和关联的听录内容即可完全入门。 请遵循以下链接开始创建自定义文本转语音体验。

## <a name="whats-in-custom-voice"></a>什么是自定义语音？

在开始使用自定义语音之前，需要一个 Azure 帐户和一个语音服务订阅。 创建帐户后，可以准备数据、训练和测试模型、评估语音质量，并最终部署自定义语音模型。

下图突出显示了使用[自定义语音门户](https://aka.ms/customvoice)创建自定义语音模型的步骤。 请使用链接了解详细信息。

![自定义语音体系结构图](media/custom-voice/custom-voice-diagram.png)

1. [订阅和创建项目](#set-up-your-azure-account) - 创建 Azure 帐户和语音服务订阅。 使用此统一订阅可以访问语音转文本、文本转语音、语音翻译和自定义语音门户。 然后，可以使用语音服务订阅创建第一个自定义语音项目。

2. [上传数据](how-to-custom-voice-create-voice.md#upload-your-datasets) - 使用自定义语音门户或自定义语音 API 上传数据（音频和文本）。 在门户中，可以调查和评估发音评分以及信噪比。 有关详细信息，请参阅[如何为自定义语音准备数据](how-to-custom-voice-prepare-data.md)。

3. [训练模型](how-to-custom-voice-create-voice.md#build-your-custom-voice-model) – 使用数据创建自定义的“文本转语音”语音模型。 可在不同的语言中训练模型。 训练并测试模型后，如果你对结果感到满意，则可以部署该模型。

4. [部署模型](how-to-custom-voice-create-voice.md#create-and-use-a-custom-voice-endpoint) - 为文本语音模型创建自定义终结点，并使用该终结点在产品、工具和应用程序中进行语音合成。

## <a name="custom-neural-voices"></a>自定义神经语音

自定义语音目前支持标准层和神经网络层。 通过自定义神经语音，用户能够生成更高质量的语音模型，同时需要的数据更少，它还提供有助于负责任地部署 AI 的度量值。 建议使用自定义神经语音开发更逼真的语音，以提供更自然的聊天界面，并以负责任的方式让客户和最终用户从最新的文本转语音技术中受益。 [详细了解自定义神经语音](/legal/cognitive-services/speech-service/custom-neural-voice/transparency-note-custom-neural-voice?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext)。 

> [!NOTE]
> 在 Microsoft 对于设计负责任 AI 的承诺中，我们限制了自定义神经语音的使用。 仅当我们评审你的应用程序，并且你承诺在遵守我们的负责任的 AI 原则的条件下使用该应用程序后，你才会获得对该技术的访问权限。 详细了解我们的[限制访问策略](/legal/cognitive-services/speech-service/custom-neural-voice/limited-access-custom-neural-voice?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext)和[在此处应用](https://aka.ms/customneural)。 自定义语音的标准版本和神经网络版本所支持的[语言](language-support.md#customization)和[区域](regions.md#custom-voices)是不同的。 开始之前，请检查详细信息。  

## <a name="set-up-your-azure-account"></a>设置 Azure 帐户

需要拥有语音服务订阅，才能使用“自定义语音识别”门户创建自定义模型。 请遵照这些说明在 Azure 中创建语音服务订阅。 如果你没有 Azure 帐户，可以注册一个新帐户。  

创建 Azure 帐户和语音服务订阅后，需要登录到自定义语音门户并连接订阅。

1. 从 Azure 门户获取语音服务订阅密钥。
2. 登录到[自定义语音门户](https://aka.ms/custom-voice)。
3. 选择你的订阅并创建语音项目。
4. 若要切换到另一个语音订阅，请使用顶部导航栏中的齿轮图标。

> [!NOTE]
> 必须先在 Azure 中创建 F0 或 S0 语音服务密钥才能使用该服务。 自定义神经语音仅支持 S0 层。 

## <a name="how-to-create-a-project"></a>如何创建项目

数据、模型、测试和终结点等内容在自定义语音门户中组织成 **项目**。 每个项目特定于某个国家/语言，以及要创建的语音的性别。 例如，对于呼叫中心的使用美式英语 (en-US) 的聊天机器人，可以创建女性语音项目。

若要创建第一个项目，请选择“文本转语音/自定义语音”选项卡，然后单击“新建项目”。  遵照向导中的说明创建项目。 创建项目后，将看到四个选项卡：“数据”、“训练”、“测试”和“部署”。    使用[后续步骤](#next-steps)中提供的链接了解如何使用每个选项卡。

> [!IMPORTANT]
> [“自定义语音”门户](https://aka.ms/custom-voice)最近已更新！ 如果以前已在 CRIS.ai 门户或使用 API 创建了数据、模型、测试并已发布了终结点，则需要在新门户中创建一个新项目以连接到这些旧实体。

## <a name="how-to-migrate-to-custom-neural-voice"></a>如何迁移到自定义神经语音

自定义语音的标准/非神经网络训练层（自适应、统计参数、连续）已弃用。 该公告已发送至 2021 年 2 月 28 日之前的所有现有语音订阅。 在弃用期间（2021 年 3 月 1 日至 2024 年 2 月 29 日），现有标准层用户可以继续使用其创建的非神经网络模型。 所有新用户/新的语音资源都应移至神经网络层/自定义神经语音。 2024 年 2 月 29 日之后，将不再支持所有标准/非神经网络自定义语音。 

如果使用的是非神经网络/标准自定义语音，请立即按照以下步骤迁移到自定义神经语音。 迁移到自定义神经语音有助于开发更逼真的语音，以提供甚至更自然的聊天界面，并以负责任的方式让客户和最终用户从最新的文本转语音技术中受益。 

1. 详细了解我们的[限制访问策略](/legal/cognitive-services/speech-service/custom-neural-voice/limited-access-custom-neural-voice?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext)和[在此处应用](https://aka.ms/customneural)。 请注意，Microsoft 全权决定自定义神经语音服务的访问权限，决策依据为我们的资格标准。 仅当我们评审客户的应用程序，并且他们承诺在遵守我们的[负责任的 AI 原则](https://microsoft.com/ai/responsible-ai)和[行为准则](/legal/cognitive-services/speech-service/tts-code-of-conduct?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext)的条件下使用该应用程序后，他们才会获得对该技术的访问权限。 
2. 当你的应用程序获得批准后，你将获得对神经网络训练功能的访问权限。 请确保使用在应用程序中提供的同一 Azure 订阅登录到[自定义语音门户](https://speech.microsoft.com/customvoice)。 
    > [!IMPORTANT]
    > 为了保护配音员并防止使用未经授权的录制内容或未经配音员的确认而对语音模型进行训练，我们要求客户上传一份录音声明，表明他/她的同意。 准备录制脚本时，请确保包含以下句子。 “我[说出你的名字和姓氏]知道，[说出公司名称]将使用我的声音录音来创建和使用我的声音的合成版本。”
    > 这句话必须作为口头同意文件上传到“配音员”标签中。 这句话将用于验证训练数据集中的录制内容是否由作出同意的同一人完成。
3. 创建自定义神经语音模型后，将语音模型部署到新的终结点。 若要使用神经语音模型创建新的自定义语音终结点，请转到“文本转语音”>“自定义语音”>“部署”。 选择“部署模型”，并输入自定义终结点的“名称”和“说明”  。 然后选择要与此终结点关联的自定义神经语音模型，并确认部署。  
4. 如果已使用新模型创建新的终结点，请更新应用中的代码。 

## <a name="next-steps"></a>后续步骤

- [准备自定义语音数据](how-to-custom-voice-prepare-data.md)
- [创建自定义语音](how-to-custom-voice-create-voice.md)
- [指南：录制语音样本](record-custom-voice-samples.md)
