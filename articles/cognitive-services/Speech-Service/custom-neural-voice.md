---
title: 自定义神经语音概述-语音服务
titleSuffix: Azure Cognitive Services
description: 自定义的神经语音是一项文本到语音功能，允许您通过提供自己的音频数据作为示例，为您的应用程序创建一种自定义的合成声音。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/01/2020
ms.author: trbye
ms.openlocfilehash: 36885e4673b83d1db7972f03c4a6309f766206c5
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101713093"
---
# <a name="what-is-custom-neural-voice"></a>什么是自定义的神经声音？

自定义的神经语音是一种 [文本到语音](./text-to-speech.md) (TTS) 功能，它允许您通过提供自己的音频数据作为示例，为您的应用程序创建一种自定义的综合声音。 文本到语音的工作原理是使用机器学习模型将文本转换为合成语音，这听起来像是所选的声音。 使用 [REST API](./rest-text-to-speech.md)，你可以使应用程序与 [预建](./language-support.md#neural-voices) 声音或通过自定义的神经语音功能开发的 [自定义语音](./how-to-custom-voice-prepare-data.md) 模型进行交流。 自定义的神经声音基于神经 TTS 技术，该技术创建自然声音，与人类的语音进行比较时经常无法区分。
自定义的神经语音的现实声音和自然声音可以代表品牌、personify 的计算机，并允许用户以自然方式与应用程序 conversationally 交互。

> [!NOTE]
> 自定义的神经语音功能需要注册，并且基于 Microsoft 的资格和使用条件限制对其的访问权限。 希望使用此功能的客户需要通过 " [进气窗体](https://aka.ms/customneural)" 注册其用例。

## <a name="the-basics-of-custom-neural-voice"></a>自定义神经语音的基础知识

用于自定义神经声音的底层神经 TTS 技术包括三个主要组件：文本分析器、神经声音模型和神经 Vocoder。 若要从文本生成自然合成语音，请首先将文本输入到文本分析器，它以音素序列的形式提供输出。 音素是一种基本的声音单位，用特定语言区分不同的单词。 音素序列定义文本中提供的单词的发音。 

接下来，音素序列进入神经声音模型，以预测定义语音信号的声音特征，例如 timbre、讲话风格、速度、intonations 和压力模式。 最后，神经 Vocoder 将声音功能转换为可听见的波浪，以便生成综合语音。

![自定义的神经声音简介图像。](./media/custom-voice/cnv-intro.png)

神经 TTS 语音模型使用基于人为语音的记录样本的深层神经网络进行训练。 在本 [博客](https://techcommunity.microsoft.com/t5/azure-ai/neural-text-to-speech-extends-support-to-15-more-languages-with/ba-p/1505911)中，我们将介绍神经 TTS 如何与先进的神经语音合成模型结合使用。 该博客还介绍了如何调整通用基准模型，使其不超过2小时的语音数据 (或少于2000个记录的最谈话) 从目标扬声器，并了解目标演讲者的语音。 若要了解如何训练神经 vocoder，请参阅 [博客文章](https://techcommunity.microsoft.com/t5/azure-ai/azure-neural-tts-upgraded-with-hifinet-achieving-higher-audio/ba-p/1847860)。

利用自定义的神经声音自定义功能，您可以调整神经 TTS 引擎以更好地适应您的用户方案。 若要创建自定义的神经声音，请使用 [Speech Studio](https://speech.microsoft.com/customvoice) 上传录制的音频和相应的脚本、训练模型，并将语音部署到自定义终结点。 根据用例，自定义的神经语音可用于实时将文本转换为语音 (例如，在智能虚拟助手) 或以脱机方式生成音频内容 (例如，在音频书籍中使用，或者在电子学习应用程序中使用用户提供的文本输入) 。 可以通过 [REST API](./rest-text-to-speech.md)、 [语音 SDK](./get-started-text-to-speech.md?pivots=programming-language-csharp&tabs=script%2cwindowsinstall)或 [web 门户](https://speech.microsoft.com/audiocontentcreation)获取此内容。

## <a name="terms-and-definitions"></a>术语和定义

| **条款**      | **定义**                                                                                                                                                                                                                                                                                                                                                                                       |
|---------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 语音模型   | 一种文本到语音模型，可模仿目标发言人的独特沉默特征。 *语音模型* 也称为 *语音字体* 或 *综合声音*。 语音模型是一组二进制格式的参数，这些参数不能人工阅读，并且不包含音频录音。 不能对其进行反向工程以派生或构造人类语音的音频。 |
| 配音员  | 录制和使用声音的个人或目标扬声器，用于创建语音模型，使其听起来像语音语音。                                                                                                                                                                                                                                                   |
| 标准 TTS  | TTS 的标准（或 "传统"）方法，该方法将口头语言分解为拼音代码段，以便可以使用传统编程或统计方法 remixed 和匹配它们。                                                                                                                                                                                                    |
| 神经 TTS    | 使用 deep 神经网络的神经 TTS 合成语音，该网络具有在自然人类语音中进行拼音的方式，而不是使用过程编程或统计方法。 除了目标语音人才录音外，神经 TTS 还使用从许多不同发言人使用声音录制构建的源库/基本模型。          |
| 训练数据 | 自定义的神经语音训练数据集，其中包含语音的音频录音以及关联的文本转录。                                                                                                                                                                                                                                                               |
| 增添       | 角色描述你希望此语音使用的用户。 优秀的角色设计将通知所有语音创建，无论是选择已创建的可用语音模型，还是通过强制转换和录制新的语音模型从头开始。                                                                                                |
| Script        | 脚本是一种文本文件，其中包含要由语音人才讲述的最谈话。  (术语 "*最谈话*" 包含完整的句子和更短的短语。 )                                                                                                                                                                                                                                |

## <a name="responsible-use-of-ai"></a>负责使用 AI

若要了解如何使用自定义的神经 Voice，请参阅 [透明度注释](/legal/cognitive-services/speech-service/custom-neural-voice/transparency-note-custom-neural-voice?context=/azure/cognitive-services/speech-service/context/context)。 Microsoft 的透明度注释旨在帮助你了解 AI 技术的工作原理、系统所有者可做出的影响系统性能和行为的选项，以及考虑整个系统（包括技术、人员和环境）的重要性。

## <a name="next-steps"></a>后续步骤

* [自定义语音入门](how-to-custom-voice.md)
* [创建和使用自定义语音端点](how-to-custom-voice-create-voice.md)