---
title: 在 Azure 视频分析器媒体版（以前称为视频索引器）中自定义语言模型 - Azure
titleSuffix: Azure Video Analyzer for Media
description: 本文概述了 Azure 视频分析器媒体版（以前称为视频索引器）中的语言模型是什么，以及如何自定义它。
services: azure-video-analyzer
author: anikaz
manager: johndeu
ms.topic: article
ms.subservice: azure-video-analyzer-media
ms.date: 05/15/2019
ms.author: kumud
ms.openlocfilehash: ca79d7d541d137a299fd61431df9410c6822be04
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/15/2021
ms.locfileid: "112121366"
---
# <a name="customize-a-language-model-with-video-analyzer-for-media"></a>使用视频分析器媒体版自定义语言模型

Azure 视频分析器媒体版（以前称为视频索引器）支持通过与 Microsoft [自定义语音识别服务](https://azure.microsoft.com/services/cognitive-services/custom-speech-service/)集成实现自动语音识别。 可通过上传自适应文本（这些文本来自引擎需要适应词汇的领域）来自定义语言模型。 训练模型后，自适应文本中出现的新字词获得了默认的发音，能够得到识别，并且语言模型将学习可能出现的新字词顺序。 自定义语言模型包括英语、西班牙语、法语、德语、意大利语、中文（简体）、日语、俄语、葡萄牙语、印地语和韩语。 

下面，我们将使用一个非常特殊的单词“Kubernetes”为例（其语境为 Azure Kubernetes 服务）。 对于视频分析器媒体版而言，这是一个新单词，被识别为“communities”。 你需要对模型进行训练，使它将该单词识别为“Kubernetes”。 在其他情况下该单词存在，但语言模型未预料到它会出现在某个特定的上下文中。 例如，非专用语言模型不会将“container service”这个由 2 个单词组成的序列识别为特定的词组。

你可以选择上传文本文件中的列表中没有上下文的字词。 我们将这种行为成为部分适应。 你还可以上传含与你的内容相关的文档或句子的文本文件，获得更好的适应效果。

可以使用视频分析器媒体版 API 或网站来创建和编辑自定义语言模型，如本主题[后续步骤](#next-steps)部分中的各主题所述。

## <a name="best-practices-for-custom-language-models"></a>自定义语言模型最佳做法

视频分析器媒体版的学习基于词组的概率，学习的最佳做法如下：

* 提供生活中会这样用的真实句子示例，且数量需足够多。
* 一行仅提供一个句子，不要提供多句话。 否则系统的学习会将句子间的概率纳入考虑。
* 可以将一个字作为一句话提升改字，但使用完整的句子，系统的学习效果会更好。
* 引入新的字或首字母缩略词时，尽可能提供大量完整句子用例，从而为系统提供尽量多的上下文。
* 尝试使用数个适应选项，看看它们的效果。
* 避免多次重复一模一样的句子。 多次重复可能会为其余输入带来偏差。
* 避免使用不常见的符号 (~, # @ % &)，因为未来可能会弃用它们。 它们所在的句子也会随之被弃用。
* 避免输入过大（例如数十万个句子），因为这样做会削弱提升效果。

## <a name="next-steps"></a>后续步骤

[使用 API 自定义语言模型](customize-language-model-with-api.md)

[使用网站自定义语言模型](customize-language-model-with-website.md)
