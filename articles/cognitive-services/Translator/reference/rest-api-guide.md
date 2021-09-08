---
title: 文本翻译 REST API 参考指南
titleSuffix: Azure Cognitive Services
description: 查看包含指向文本翻译 REST API 链接的列表。
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 08/10/2021
ms.author: lajanuar
ms.openlocfilehash: f3188f303d7e2945341267c9ea4598682e4d7964
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123426240"
---
# <a name="text-translation-rest-api"></a>文本翻译 REST API

文本翻译是 Azure Translator 服务的一项基于云的功能，属于 REST API 的 Azure 认知服务系列。 文本翻译 API 可将[受支持的语言和方言](../../language-support.md)中任一语言的文本转换为其中另一语言的文本。 可用语言如下表所示：

| 请求| 方法| 说明|
|---------|--------------|---------|
| [语言](v3-0-languages.md) | **GET** | 返回“翻译”、“音译”和“字典”方法当前支持的语言集  。 此请求不需要身份验证标头，且不需要翻译器资源就能查看支持的语言集。|
|[翻译](v3-0-translate.md) | **POST**| 将指定的源语言文本翻译为目标语言文本。|
|[直译](v3-0-transliterate.md) |  **POST** | 将源语言脚本或字母映射到目标语言脚本或字母表。
|[检测](v3-0-detect.md) | **POST** | 标识源语言。 |
|[breakSentence](v3-0-break-sentence.md) | **POST** | 返回表示源文本中的句子长度的整数数组。 |
| [字典/查找](v3-0-dictionary-lookup.md) | **POST** | 返回单字翻译的替换项。 |
| [字典/示例](v3-0-dictionary-lookup.md) | **POST** | 返回术语在上下文中的使用方式。 |

> [!div class="nextstepaction"]
> [在 Azure 门户中创建翻译器资源。](../translator-how-to-signup.md)

> [!div class="nextstepaction"]
> [快速入门：REST API 和编程语言](../quickstart-translator.md)
