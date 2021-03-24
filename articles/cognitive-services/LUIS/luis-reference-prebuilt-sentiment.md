---
title: 情绪分析 - LUIS
titleSuffix: Azure Cognitive Services
description: 如果配置了情绪分析，LUIS json 响应会包含情绪分析内容。
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 07/01/2020
ms.openlocfilehash: 2e8e5a127741625fde7910aaabd421836148fc35
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "95018661"
---
# <a name="sentiment-analysis"></a>情绪分析
如果配置了情绪分析，LUIS json 响应会包含情绪分析内容。 请在[文本分析](../text-analytics/index.yml)文档中详细了解情绪分析。

LUIS 使用文本分析 V2。 

## <a name="resolution-for-sentiment"></a>情绪解决方法

情绪数据是一个介于 0 到 1 之间的分数，指示数据的正面情绪（分数接近 1）或负面情绪（分数接近 0）。

#### <a name="english-language"></a>[英语](#tab/english)

当区域性为 `en-us` 时，响应为：

```JSON
"sentimentAnalysis": {
  "label": "positive",
  "score": 0.9163064
}
```

#### <a name="other-languages"></a>[其他语言](#tab/other-languages)

对于所有其他区域性，响应为：

```JSON
"sentimentAnalysis": {
  "score": 0.9163064
}
```
* * *

## <a name="next-steps"></a>后续步骤

详细了解 [V3 预测终结点](luis-migration-api-v3.md)。