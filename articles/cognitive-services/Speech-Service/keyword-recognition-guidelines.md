---
title: 关键字识别建议和准则 - 语音服务
titleSuffix: Azure Cognitive Services
description: 使用关键字识别时的建议和准则的概述。
services: cognitive-services
author: hasyashah
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/30/2021
ms.author: hasshah
ms.custom: devx-track-csharp
ms.openlocfilehash: 09322cb8d5497b2f6ea639955ebd7338e39f2418
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2021
ms.locfileid: "110116538"
---
# <a name="recommendations-and-guidelines-for-keyword-recognition"></a>关键字识别的建议和准则

本文概述了如何选择关键字来优化其准确性特性，以及如何使用关键字验证来设计用户体验。 

## <a name="choosing-an-effective-keyword"></a>选择有效的关键字

创建有效的关键字对于确保产品可一致、准确地进行响应至关重要。 选择关键字时请考虑以下准则。

> [!NOTE]
> 下面的示例采用英语，但准则适用于自定义关键字支持的所有语言。 有关所有支持语言的列表，请参阅[语言支持](language-support.md#custom-keyword-and-keyword-verification)。

- 说该词应该不超过两秒钟。
- 4 到 7 个音节的单词效果最好。 例如，“Hey, Computer”是一个很好的关键字。 而只是“Hey”则是一个糟糕的唤醒词。
- 关键字应遵循特定于最终用户的本地语言的常用发音规则。
- 遵循常见发音规则的独特或甚至虚构的单词可以减少误报。 例如，“computerama”可能是一个很好的关键字。
- 不要选择常用词。 例如，“eat”和“go”是人们在日常对话中经常说的词。 它们可能会导致产品的错误接受率高于预期。
- 避免使用可能有其他发音的关键字。 用户必须知道“正确”的发音才能使他们的产品进行语音激活。 例如，“509”的发音可以是“five zero nine”、“five oh nine”或“five hundred and nine”。 “R.E.I.” 发音可以是“r-e-i”或“ray”。 “Live”的发音可以是“/līv/”或“/liv/”。
- 不要使用特殊字符、符号或数字。 例如，“Go#”和“20 + cats”可能是问题关键字。 但是，“go sharp”或“twenty plus cats”可行。 你仍然可以在品牌中使用这些符号，并通过营销和文档来强化正确的发音。


## <a name="user-experience-recommendations-with-keyword-verification"></a>使用关键字验证时的用户体验建议

在使用[关键字验证](keyword-recognition-overview.md#keyword-verification)的多阶段关键字识别方案中，应用程序可以选择何时向最终用户通知检测到关键字。 对于呈现任何视觉或声音指示器，建议依赖于来自关键字验证服务的响应：

![优化准确性时的用户体验准则。](media/custom-keyword/keyword-verification-ux-accuracy.png)

这可确保准确性方面的最佳体验，以最大程度地减少错误接受的用户感知影响，但会产生额外的延迟。

对于需要延迟优化的应用程序，应用程序可以基于设备上的关键字识别向最终用户提供轻型和不引人注目的指示器。 例如，触发 LED 模式或触发图标。 如果关键字验证响应为关键字接受，则指示器可以继续存在，如果响应为关键字拒绝，则可以将其关闭：

![优化延迟时的用户体验准则。](media/custom-keyword/keyword-verification-ux-latency.png)

## <a name="next-steps"></a>后续步骤

* [获取语音 SDK。](speech-sdk.md)
* [详细了解语音助理。](voice-assistants.md)
