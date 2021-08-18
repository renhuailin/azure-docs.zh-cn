---
title: 开始使用自定义神经语音 - 语音服务
titleSuffix: Azure Cognitive Services
description: 自定义神经语音是一组在线工具，可用于为自有品牌创建可识别的独一无二的语音。 只需准备好几个音频文件和关联的听录内容即可完全入门。
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/18/2021
ms.author: lajanuar
ms.openlocfilehash: 155c62791426ceafde70c173370d4056a6b8956c
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122068975"
---
# <a name="get-started-with-custom-neural-voice"></a>自定义神经语音入门

[自定义神经语音](https://aka.ms/customvoice)是一组在线工具，可用于为自有品牌创建可识别的独一无二的语音。 只需准备好几个音频文件和关联的听录内容即可完全入门。 请遵循以下链接开始创建自定义文本转语音体验。 请参阅自定义神经语音支持的[语言](language-support.md#customization)和[区域](regions.md#custom-voices)。

> [!NOTE]
> 在 Microsoft 对于设计负责任 AI 的承诺中，我们限制了自定义神经语音的使用。 仅当我们评审你的应用程序，并且你承诺在遵守我们的负责任的 AI 原则的条件下使用该应用程序后，你才会获得对该技术的访问权限。 详细了解我们的[限制访问策略](/legal/cognitive-services/speech-service/custom-neural-voice/limited-access-custom-neural-voice?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext)和[在此处应用](https://aka.ms/customneural)。 
 
## <a name="set-up-your-azure-account"></a>设置 Azure 帐户

需要有语音服务订阅才可使用自定义神经语音。 请遵照这些说明在 Azure 中创建语音服务订阅。 如果你没有 Azure 帐户，可以注册一个新帐户。  

创建 Azure 帐户和语音服务订阅后，需要登录到 Speech Studio 并连接订阅。

1. 从 Azure 门户获取语音服务订阅密钥。
2. 登录到 [Speech Studio](https://speech.microsoft.com)，然后单击“自定义语音”。
3. 选择你的订阅并创建语音项目。
4. 若要切换到另一个语音订阅，请使用顶部导航栏中的齿轮图标。

> [!NOTE]
> 必须先在 Azure 中创建 F0 或 S0 语音服务密钥才能使用该服务。 自定义神经语音仅支持 S0 层。 

## <a name="create-a-project"></a>创建一个项目

数据、模型、测试和终结点等内容在 Speech Studio 中组织成“项目”。 每个项目特定于某个国家/语言，以及要创建的语音的性别。 例如，对于呼叫中心的使用美式英语 (en-US) 的聊天机器人，可以创建女性语音项目。

若要创建第一个项目，请选择“文本转语音/自定义语音”选项卡，然后单击“创建项目” 。 遵照向导中的说明创建项目。 创建项目后，你将看到四个选项卡：“设置配音员”、“准备训练数据”、“训练模型”和“部署模型”   。 使用[后续步骤](#next-steps)中提供的链接，了解如何使用每个选项卡。

## <a name="tips-for-creating-a-custom-neural-voice"></a>创建自定义神经语音的提示

要创建优秀的自定义语音，就需要在从语音设计和数据准备到将语音模型部署到系统的每个步骤中，仔细进行质量控制。 下面是为你的组织创建自定义神经语音时需要执行的一些重要步骤。 

### <a name="persona-design"></a>角色设计

首先，在角色简介文档中定义语音特征和语音背后的性格，用其设计一个用于表示品牌的语音角色。 这将有助于指导自定义语音模型的创建过程，包括定义脚本、选择配音员、训练和语音优化。

### <a name="script-selection"></a>脚本选择
 
请谨慎选择表示语音用户方案的录制内容脚本。 例如，如果要创建客户服务机器人，可使用机器人会话中的短语作为录制内容的脚本。 在脚本中包括不同的语句类型，包括陈述句、疑问句、感叹句等。

### <a name="preparing-training-data"></a>准备训练数据

建议在专业的录音棚中捕获录制内容，以获得较高的信噪比。 语音模型的质量很大程度取决于你的训练数据。 需要一致的音量、语速、音调和一致的语音表达风格。

准备好录制内容后，按照[准备训练数据](how-to-custom-voice-prepare-data.md)操作，以准备格式正确的训练数据。

### <a name="training"></a>培训

准备好训练数据后，转到 [Speech Studio](https://aka.ms/custom-voice) 创建自定义神经语音。 至少需要选择 300 条言语才能创建自定义神经语音。 上传后，系统会自动执行一系列数据质量检查。 若要生成高质量的语音模型，应修复错误并再次提交。

### <a name="testing"></a>测试

为语音模型准备测试脚本，其中应涵盖应用的不同用例。 建议在训练数据集内部和外部使用脚本，以便可以更广泛地测试不同内容的质量。

### <a name="tuning-and-adjustment"></a>优化和调整

经过训练的语音模型的风格和特征取决于用于训练的配音员的录音风格和质量。 但在向语音模型发出 API 调用以生成合成语音时，可使用 [SSML（语音合成标记语言）](./speech-synthesis-markup.md?tabs=csharp)进行几项调整。 SSML 是标记语言，用于与 TTS 服务通信以将文本转换为音频。 调整包括改变音调、语速、声调和发音纠正。  如果语音模型采用了多种风格，则 SSML 还可用于切换风格。

## <a name="migrate-to-custom-neural-voice"></a>迁移到自定义神经语音

自定义语音的标准/非神经训练层（统计参数、连续）已弃用。 该公告已发送至 2021 年 2 月 28 日之前的所有现有语音订阅。 在弃用期间（2021 年 3 月 1 日至 2024 年 2 月 29 日），现有标准层用户可以继续使用其创建的非神经网络模型。 所有新用户/新的语音资源都应移至神经网络层/自定义神经语音。 2024 年 2 月 29 日之后，将不再支持所有标准/非神经网络自定义语音。 

如果使用的是非神经网络/标准自定义语音，请立即按照以下步骤迁移到自定义神经语音。 迁移到自定义神经语音有助于开发更逼真的语音，以提供甚至更自然的聊天界面，并以负责任的方式让客户和最终用户从最新的文本转语音技术中受益。 

1. 详细了解我们的[限制访问策略](/legal/cognitive-services/speech-service/custom-neural-voice/limited-access-custom-neural-voice?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext)和[在此处应用](https://aka.ms/customneural)。 请注意，Microsoft 全权决定自定义神经语音服务的访问权限，决策依据为我们的资格标准。 仅当我们评审客户的应用程序，并且他们承诺在遵守我们的[负责任的 AI 原则](https://microsoft.com/ai/responsible-ai)和[行为准则](/legal/cognitive-services/speech-service/tts-code-of-conduct?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext)的条件下使用该应用程序后，他们才会获得对该技术的访问权限。 
2. 当你的应用程序获得批准后，你将获得对神经网络训练功能的访问权限。 请确保使用在应用程序中提供的同一 Azure 订阅登录到 [Speech Studio](https://speech.microsoft.com)。 
    > [!IMPORTANT]
    > 为了保护配音员并防止使用未经授权的录制内容或未经配音员的确认而对语音模型进行训练，我们要求客户上传一份录音声明，表明他们同意。 准备录制脚本时，请确保包含以下句子。 “我[说出你的名字和姓氏]知道，[说出公司名称]将使用我的声音录音来创建和使用我的声音的合成版本。”
    > 这句话必须作为口头同意文件上传到“设置配音员”标签中。 这句话将用于验证训练数据集中的录制内容是否由作出同意的同一人完成。
3. 创建自定义神经语音模型后，将语音模型部署到新的终结点。 若要使用神经语音模型创建新的自定义语音终结点，请转到“文本转语音”>“自定义语音”>“部署模型”。 选择“部署模型”，并输入自定义终结点的“名称”和“说明”  。 然后选择要与此终结点关联的自定义神经语音模型，并确认部署。  
4. 如果已使用新模型创建新的终结点，请更新应用中的代码。 

## <a name="next-steps"></a>后续步骤

- [准备自定义神经语音数据](how-to-custom-voice-prepare-data.md)
- [训练和部署自定义神经语音](how-to-custom-voice-create-voice.md)
- [如何录制语音示例](record-custom-voice-samples.md)