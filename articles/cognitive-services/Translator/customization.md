---
title: 翻译自定义 - 翻译器
titleSuffix: Azure Cognitive Services
description: 通过 Microsoft Translator Hub 使用首选的术语和样式构建自己的机器翻译系统。
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: lajanuar
ms.openlocfilehash: 68ae3735fc1f58ebbf8d7afd6a098e7520880058
ms.sourcegitcommit: 92dd25772f209d7d3f34582ccb8985e1a099fe62
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2021
ms.locfileid: "114228749"
---
# <a name="customize-your-text-translations"></a>自定义文本翻译

自定义翻译器是 Translator 服务的一项功能，它允许用户在使用翻译器（仅限版本 3）翻译文本时自定义 Microsoft 翻译工具的高级神经网络机器翻译。

与[认知服务语音](../speech-service/index.yml)一起使用时，该功能还可用于自定义语音翻译。

## <a name="custom-translator"></a>自定义翻译

使用自定义翻译，可以构建神经翻译系统，以了解自己的业务和行业中使用的术语。 然后，自定义的翻译系统将集成到现有的应用程序、工作流和网站中。

### <a name="how-does-it-work"></a>它是如何工作的？

使用以前翻译过的文档（传单、网页、文档等）构建一个翻译系统，以反映特定于领域的术语和样式，这比标准翻译系统更好。 用户可以上传 TMX、XLIFF、TXT、DOCX 和 XLSX 文档。  

该系统还接受在文档级别并行但在句子级别尚未对齐的数据。 如果用户可以访问相同内容的多种语言版（但每种语言的内容都位于单独的文档中），自定义翻译将能够跨文档自动匹配句子。  该系统还可以使用一种或两种语言的单语数据来补充并行训练数据以改进翻译。

然后，通过使用类别参数对翻译器进行常规调用，即可使用自定义的系统。

考虑到训练数据的适当类型和数量，通过使用自定义翻译，期望在翻译质量上获得 5 到 10 分或甚至更多 BLEU 分数的情况并不罕见。

有关基于可用数据的各种自定义级别的更多详细信息，请参阅[自定义翻译用户指南](./custom-translator/overview.md)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用自定义翻译设置自定义语言系统](./custom-translator/overview.md)
