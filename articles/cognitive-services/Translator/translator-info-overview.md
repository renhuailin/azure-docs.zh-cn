---
title: Microsoft Translator 服务
titlesuffix: Azure Cognitive Services
description: 将“翻译”集成到应用程序、网站、工具和其他解决方案中，提供多语言用户体验。
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.topic: overview
ms.subservice: translator-text
ms.date: 02/15/2021
ms.author: lajanuar
ms.custom: cog-serv-seo-aug-2020
keywords: translator, 文本翻译, 机器翻译, 翻译服务
ms.openlocfilehash: 12f6d22f263747a8c43b2d98e6ade1de78aea1ce
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/17/2021
ms.locfileid: "100556252"
---
# <a name="what-is-the-translator-service"></a>什么是 Translator 服务？

Translator 是一种基于云的机器翻译服务，是用于构建智能应用的 [Azure 认知服务](../../index.yml?panel=ai&pivot=products)系列认知 API 的一部分。 “翻译”可以轻松地集成到应用程序、网站、工具和解决方案中。 通过该服务可添加[超过 70 种语言](./language-support.md)的多语言用户体验。 可以在任何操作系统的任何硬件平台上使用该服务进行文本转换。

## <a name="about-microsoft-translator"></a>关于 Microsoft Translator

“翻译”为许多 Microsoft 产品和服务提供支持，并已在全球数千家企业的应用程序和工作流中使用。

由 Translator 提供支持的语音翻译也可通过 [Azure 语音服务](../speech-service/index.yml)获取。 它将语音翻译 API 和自定义语音服务的功能组合成了一项统一的可完全自定义的服务。 

## <a name="language-support"></a>语言支持

Translator 为文本翻译、音译、语言检测和字典提供多语言支持。 请参阅[语言支持](language-support.md)以获取完整的列表，或者通过 [REST API](./reference/v3-0-languages.md) 以编程方式访问列表。  

## <a name="microsoft-translator-neural-machine-translation"></a>Microsoft Translator 神经机器翻译

神经机器翻译 (NMT) 是采用 AI 的高质量机器翻译的新标准。 它代替的是旧式统计机器翻译 (SMT) 技术，该技术在 2010-2020 中期的几年中达到了质量顶峰。

与 SMT 相比，NMT 不仅能够从原始翻译质量评分的立场提供更好的翻译，而且听起来更流畅、更类似于人类。 这种流畅性的主要原因在于 NMT 使用一个句子的完整语境来翻译单词。 SMT 仅考虑每个单词前面和后面几个单词的直接语境。

NMT 模型是该 API 的核心，对最终用户不可见。 唯一明显的区别是改进的翻译质量，尤其是针对中文、日语和阿拉伯语等语言。

详细了解 [NMT 的工作原理](https://www.microsoft.com/en-us/translator/mt.aspx#nnt)。

## <a name="improve-translations-with-custom-translator"></a>通过自定义翻译器改进翻译

 自定义翻译器是翻译服务的扩展，可以与“翻译”配合用于自定义神经翻译系统，并改进特定术语和样式的翻译。

使用自定义翻译器，可以构建翻译系统来处理自己的业务或行业中使用的术语。 可以使用类别参数通过常规“翻译”服务将自定义翻译系统轻松集成到现有应用程序、工作流、网站和设备中。

详细了解[自定义翻译器](customization.md)。

## <a name="next-steps"></a>后续步骤

- [创建“翻译”服务](./translator-how-to-signup.md)以获取访问密钥和终结点。
- 请试用[快速入门](quickstart-translator.md)以快速调用 Translator 服务。
- [API 参考](./reference/v3-0-reference.md)提供了 API 的技术文档。
- [定价详细信息](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)