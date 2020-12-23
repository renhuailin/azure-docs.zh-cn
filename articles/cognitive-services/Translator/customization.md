---
title: 翻译自定义-转换器
titleSuffix: Azure Cognitive Services
description: 通过 Microsoft Translator Hub 使用首选的术语和样式构建自己的机器翻译系统。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: swmachan
ms.openlocfilehash: 9c4410cb2b9550bc111da96204ada15313867fb1
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2020
ms.locfileid: "95238082"
---
# <a name="customize-your-text-translations"></a>自定义文本翻译

自定义转换器是 Translator 服务的一项功能，它允许用户在使用转换器 (仅) 版本3翻译文本时自定义 Microsoft Translator 的高级神经计算机翻译。

与[认知服务语音](../speech-service/index.yml)一起使用时，该功能还可用于自定义语音翻译。

## <a name="custom-translator"></a>自定义翻译

使用自定义翻译，可以构建神经翻译系统，以了解自己的业务和行业中使用的术语。 然后，自定义的翻译系统将集成到现有的应用程序、工作流和网站中。

### <a name="how-does-it-work"></a>工作原理

使用之前翻译的文档 (leaflets、网页、文档等 ) 生成一个翻译系统，该系统反映了域特定术语和样式，比标准翻译系统更好。 用户可以上传 TMX、XLIFF、TXT、DOCX 和 XLSX 文档。  

该系统还接受在文档级别并行但在句子级别尚未对齐的数据。 如果用户可以访问相同内容的多种语言版（但每种语言的内容都位于单独的文档中），自定义翻译将能够跨文档自动匹配句子。  该系统还可以使用一种或两种语言的单语数据来补充并行训练数据以改进翻译。

然后，使用 category 参数通过对转换器的常规调用来提供自定义系统。

考虑到训练数据的适当类型和数量，通过使用自定义翻译，期望在翻译质量上获得 5 到 10 分或甚至更多 BLEU 分数的情况并不罕见。

有关基于可用数据的各种自定义级别的更多详细信息，请参阅[自定义翻译用户指南](./custom-translator/overview.md)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用自定义翻译设置自定义语言系统](./custom-translator/overview.md)
