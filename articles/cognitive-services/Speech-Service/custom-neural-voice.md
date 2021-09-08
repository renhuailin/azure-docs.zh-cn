---
title: 自定义神经语音概述 - 语音服务
titleSuffix: Azure Cognitive Services
description: 自定义神经语音是一种文本转语音功能，使你可以通过提供自己的音频数据作为示例，为应用程序创建独一无二的自定义合成声音。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/18/2021
ms.author: pafarley
ms.openlocfilehash: 9f0af5c38bc41ab6ae3926d41564682257137575
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2021
ms.locfileid: "123541038"
---
# <a name="what-is-custom-neural-voice"></a>什么是自定义神经语音？

自定义神经语音是一种文本转语音 (TTS) 功能，使你可以为应用程序创建独一无二的自定义合成声音。 利用自定义神经语音，你可以通过提供自己的音频样本作为训练数据来生成高度自然的语音。 自定义神经语音基于神经 TTS 技术和多语言、多说话者通用模型，可用于创建具有丰富说话风格或跨语言自适应的合成语音。 自定义神经语音逼真的自然语音可以表示品牌、使计算机拟人化并使用户能够与应用程序对话交互。 请参阅自定义神经语音支持的[语言](language-support.md#customization)以及跨语言功能。

> [!NOTE]
> 自定义神经语音功能需要注册，对该功能进行的访问会基于 Microsoft 的资格和使用条件受到限制。 希望使用此功能的客户需要通过 [引入窗体](https://aka.ms/customneural)注册其用例。

## <a name="the-basics-of-custom-neural-voice"></a>自定义神经语音的基础知识

用于自定义神经声音的底层神经 TTS 技术包括三个主要组件：文本分析器、神经声学模型和神经语音编码器。 为了从文本生成自然合成语音，文本会首先输入到文本分析器中，后者以音素序列的形式提供输出。 音素是一种基本声音单位，可区分特定语言中的不同字词。 音素序列定义文本中提供的字词的发音。 

接下来，音素序列会进入神经声学模型，以预测定义语音信号的声学特性，例如音色、说话风格、速度、语调和重音模式。 最后，神经语音编解码其会将声学特性转换为可听见的波形，以便生成合成语音。

![自定义神经语音的简介图像。](./media/custom-voice/cnv-intro.png)

神经 TTS 语音模型基于人类语音的录制样本，使用深度神经网络进行训练。 在本[博客](https://techcommunity.microsoft.com/t5/azure-ai/neural-text-to-speech-extends-support-to-15-more-languages-with/ba-p/1505911)中，我们介绍神经 TTS 如何使用先进的神经语音合成模型。 该博客还介绍了如何使用少于 2 小时的语音数据（或少于 2,000 条录制言语）使通用基础模型适应目标说话者的语音，并另外将语音转换为另一种语言或风格。 若要了解如何训练神经语音编码器，请参阅该[博客文章](https://techcommunity.microsoft.com/t5/azure-ai/azure-neural-tts-upgraded-with-hifinet-achieving-higher-audio/ba-p/1847860)。

利用自定义神经语言，可以调整神经 TTS 引擎以更好地适应你的方案。 若要创建自定义神经声音，请使用 [Speech Studio](https://speech.microsoft.com/customvoice) 上传录制的音频和相应的脚本，训练模型，然后将语音部署到自定义终结点。 自定义神经语音可使用用户提供的文本来实时将文本转换为语音，或者使用文本输入来脱机生成音频内容。 这可以通过 [REST API](./rest-text-to-speech.md)、[语音 SDK](./get-started-text-to-speech.md) 或 [Web 门户](https://speech.microsoft.com/audiocontentcreation)来实现。

## <a name="get-started"></a>入门

* 若要开始使用自定义神经语音并创建项目，请参阅[开始使用自定义神经语音](how-to-custom-voice.md)。
* 若要准备和上传音频数据，请参阅[准备训练数据](how-to-custom-voice-prepare-data.md)。
* 若要训练和部署模型，请参阅[创建和使用语音模型](how-to-custom-voice-create-voice.md)。

## <a name="terms-and-definitions"></a>术语和定义

| **条款**      | **定义**                                                                                                                                                                                                                                                                                                                                                                                       |
|---------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 语音模型   | 一种文本转语音模型，可模仿目标说话人的独特声音特征。 语音模型也称为语音字体或合成语音  。 语音模型是一组二进制格式的参数，不可供人阅读，并且不包含音频录制内容。 无法对其进行反向工程以派生或构造人类语音的音频。 |
| 配音员  | 个人或目标说话人，其声音被录制并用于创建旨在听起来像配音员语音的语音模型。                                                                                                                                                                                                                                                   |
| 标准 TTS  | TTS 的标准（或“传统”）方法，将口语分解为注音片段，以便可以使用经典编程或统计方法重新混合和匹配它们。                                                                                                                                                                                                    |
| 神经 TTS    | 神经 TTS 使用深度神经网络合成语音，这些网络“学习”注音在自然人类语音中的组合方式，而不是使用过程编程或统计方法。 除了目标配音员的录制内容外，神经 TTS 还使用通过来自许多不同说话人的语音录制内容构建的源库/基础模型。          |
| 训练数据 | 自定义神经语音训练数据集，其中包含配音员的音频录制内容以及关联文本停录。                                                                                                                                                                                                                                                               |
| 角色       | 角色描述你希望此语音成为的人员。 优秀的角色设计会向所有语音创建告知是选择已创建的可用语音模型，还是通过强制转换和录制新配音员从头开始。                                                                                                |
| 脚本        | 脚本是一种文本文件，其中包含要由配音员讲述的言语。 （术语“言语”包括完整的句子和较短的短语。）                                                                                                                                                                                                                               |

## <a name="responsible-use-of-ai"></a>负责使用 AI

若要了解如何负责任地使用自定义神经语音，请参阅[透明度备注](/legal/cognitive-services/speech-service/custom-neural-voice/transparency-note-custom-neural-voice?context=/azure/cognitive-services/speech-service/context/context)。 Microsoft 的透明度备注旨在帮助你了解我们 AI 技术的工作原理、系统所有者可能做出的影响系统性能和行为的选项，以及考虑整个系统（包括技术、人员和环境）的重要性。

## <a name="next-steps"></a>后续步骤

* [自定义神经语音入门](how-to-custom-voice.md)
