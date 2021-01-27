---
title: 猥亵筛选-翻译人员
titleSuffix: Azure Cognitive Services
description: 使用猥亵筛选来确定在 Azure 认知服务翻译人员的文本中翻译的猥亵级别。
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: lajanuar
ms.openlocfilehash: 9f389d669e69dbfa6ec6d4d0b4716d2367443f17
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896793"
---
# <a name="add-profanity-filtering-with-the-translator"></a>通过转换器添加猥亵语言筛选器

通常，Translator 服务在翻译中会保留源中存在的不雅内容。 不雅程度和使词语不雅的语境在不同的文化之间有所不同。 因此，在目标语言中的不雅程度可能会被放大或降低。

如果希望避免在翻译中看到不雅内容（即使源文本中存在不雅内容），可以使用 Translate() 方法中提供的不雅内容筛选选项。 使用该选项可以选择是要看到不雅内容被删除、标有相应标记还是不被执行任何操作。

转换 ( # A1 方法采用 "options" 参数，该参数包含新元素 "ProfanityAction"。 ProfanityAction 的接受值为 "NoAction"、"标记" 和 "已删除"。

## <a name="accepted-values-of-profanityaction-and-examples"></a>可接受的 ProfanityAction 值和示例
|ProfanityAction 值 | 操作 | 示例：源 - 日语 | 示例：目标 - 英语|
| :---|:---|:---|:---|
| NoAction | 默认。 与不设置此选项等效。 不雅内容从源传递到目标。 | 彼は変態です。 | He is a jerk. |
| Marked | 亵渎词由 XML 标记括起来 ... \<profanity> \</profanity> 。 | 彼は変態です。 | 他是 \<profanity> 机械 \</profanity> 。 |
| Deleted | 将不雅词语从输出中删除且不替换。 | 彼は。 | 他是一个。 |

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [使用你的翻译人员呼叫应用猥亵语言筛选](reference/v3-0-translate.md)
