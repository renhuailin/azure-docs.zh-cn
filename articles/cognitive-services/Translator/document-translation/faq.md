---
title: 常见问题解答：文档翻译
titleSuffix: Azure Cognitive Services
description: 从 Azure 认知服务获取有关翻译服务中文档翻译的常见问题解答。
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 07/15/2021
ms.author: lajanuar
ms.openlocfilehash: 18ce8c8032ca56ff0fa4dc039e862686c5a33d88
ms.sourcegitcommit: 47ac63339ca645096bd3a1ac96b5192852fc7fb7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114363545"
---
# <a name="document-translation-faq"></a>文档翻译的常见问题解答

本文包含有关文档翻译的常见问题解答。

|常见问题|
|:--|
|**何时应在请求中指定文档的源语言？**<br/>如果源文档中内容的语言已知，建议在请求中指定源语言，以获得更好的翻译。 如果文档具有多种语言的内容或语言未知，则不要在请求中指定源语言。 文档翻译自动标识每个文本段的语言并翻译。|
|**布局、结构和格式的保留程度如何？**<br/>将文本从源语言翻译为目标语言时，翻译后的文本的总长度可能与源不同。  这可能会导致跨页面重排文本。 源语言和目标语言可能无法使用相同的字体。 通常，在目标语言中应用相同的字体样式，以保留更靠近源的格式。|
|**文档中嵌入在图像中的文本会翻译吗？**<br/>不能。 文档中嵌入在图像中的文本不能翻译。|
|**文档翻译是否翻译扫描的文档内容？**<br/>不能。 文档翻译不能翻译扫描的文档内容。|



