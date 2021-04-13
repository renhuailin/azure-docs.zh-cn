---
title: 防止翻译内容 - 翻译器
titleSuffix: Azure Cognitive Services
description: 使用翻译器防止翻译内容。 使用翻译器可以标记内容，以便不对其进行翻译。
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: lajanuar
ms.openlocfilehash: e89ff3f4c1f54d9e137d3dd51e325b908c826b03
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "98897470"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator"></a>如何使用翻译器防止翻译内容

使用翻译器可以标记内容，以便不对其进行翻译。 例如，你可能想要标记本地化后没有意义的代码、品牌名称或单词/短语。

## <a name="methods-for-preventing-translation"></a>阻止翻译的方法

1. 使用 `notranslate` 标记内容。 根据设计，仅当输入 textType 设置为 HTML 时，这才起作用

   示例：

   ```html
   <span class="notranslate">This will not be translated.</span>
   <span>This will be translated. </span>
   ```
   
   ```html
   <div class="notranslate">This will not be translated.</div>
   <div>This will be translated. </div>
   ```

2. 使用 `translate="no"` 标记内容。 这仅适用于以 HTML 形式设置输入 textType 的情况

   示例：

   ```html
   <span translate="no">This will not be translated.</span>
   <span>This will be translated. </span>
   ```
   
   ```html
   <div translate="no">This will not be translated.</div>
   <div>This will be translated. </div>
   ```
   
3. 使用[动态词典](dynamic-dictionary.md)给出特定翻译。

4. 不要将字符串传递到翻译器进行翻译。

5. 自定义翻译器：使用[自定义翻译器中的词典](custom-translator/what-is-dictionary.md)以 100% 的概率给出短语翻译。


## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [使用“翻译”操作来转换文本](reference/v3-0-translate.md)
