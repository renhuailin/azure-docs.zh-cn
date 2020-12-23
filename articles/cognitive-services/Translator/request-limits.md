---
title: 请求限制 - 翻译器
titleSuffix: Azure Cognitive Services
description: 本文列出了翻译器的请求限制。 费用是根据字符数而不是请求频率产生的，每个请求限制为 5,000 个字符。 字符限制是基于订阅的，F0 限制为每小时 200 万个字符。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 08/06/2020
ms.author: swmachan
ms.openlocfilehash: 8841c55d8f276f048db53a531bd2e9218e498b34
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2020
ms.locfileid: "95016490"
---
# <a name="request-limits-for-translator"></a>翻译器的请求限制

本文提供了翻译器的限制。 服务包括翻译、音译、句子长度检测、语言检测和备用翻译。

## <a name="character-and-array-limits-per-request"></a>每个请求的字符和数组限制

每个翻译请求在你要翻译到的所有目标语言中都限制为 10,000 个字符。 例如，如果发送 3,000 个字符的翻译请求以翻译成 3 种不同的语言，则请求大小为 3000x3 = 9,000 个字符，这满足请求限制。 按字符收费，而不是按请求数收费。 建议发送较短的请求。

下表列出了翻译器的每个操作的数组元素和字符限制。

| Operation | 数组元素的最大大小 |    最大数组元素数 |    最大请求大小（字符数） |
|:----|:----|:----|:----|
| Translate | 10,000    | 100   | 10,000 |
| Transliterate | 5,000 | 10    | 5,000 |
| Detect | 50,000 | 100 |   50,000 |
| BreakSentence | 50,000    | 100 | 50,000 |
| 字典查找| 100 |  10  | 1,000 |
| 字典示例 | 100 个用于文本，100 个用于翻译（总共 200 个）| 10|   2,000 |

## <a name="character-limits-per-hour"></a>每小时的字符限制

每小时的字符限制取决于翻译器订阅层。 

每小时配额应在一小时内均匀使用。 例如，在 F0 层限制为每小时 200 万字符时，使用字符的速度不应超过大约 33,300 个字符/分钟滑动窗口（200 万个字符除以 60 分钟）。

如果你达到或超过这些限制，或在短时间内发送过大部分配额，则可能会收到超出配额响应。 对并发请求没有限制。

| 层 | 字符限制 |
|------|-----------------|
| F0 | 每小时 200 万个字符 |
| S1 | 每小时 4000 万个字符 |
| S2/C2 | 每小时 4000 万个字符 |
| S3/C3 | 每小时 1.2 亿个字符 |
| S4/C4 | 每小时 2 亿个字符 |

[多服务订阅](./reference/v3-0-reference.md#authentication)的限制与 S1 层相同。

这些限制仅限于 Microsoft 的标准翻译模型。 使用自定义转换器的自定义翻译模型的每秒限制为1800个字符。

## <a name="latency"></a>延迟

使用标准模型时，翻译器的最大延迟为 15 秒，而使用自定义模型时，则为 120 秒。 通常，对于 100 个字符以内的文本的响应  将在 150 毫秒到 300 毫秒内返回。 自定义翻译模型对于持续的请求速率具有相似的延迟特征，在请求速率为间歇性时可能会有更高的延迟。 响应时间因请求大小和语言对而异。 如果在此时间范围内你没有收到翻译或[错误响应](./reference/v3-0-reference.md#errors)，请检查代码、网络连接并重试。 

## <a name="sentence-length-limits"></a>句子长度限制

使用 [BreakSentence](./reference/v3-0-break-sentence.md) 函数时，句子长度限制为 275 个字符。 这些语言存在例外情况：

| 语言 | 代码 | 字符限制 |
|----------|------|-----------------|
| 中文 | zh | 166 |
| 德语 | de | 800 |
| 意大利语 | it | 800 |
| 日语 | ja | 166 |
| 葡萄牙语 | pt | 800 |
| 西班牙语 | es | 800 |
| 泰语 | th | 180 |

> [!NOTE]
> 此限制不适用于翻译。

## <a name="next-steps"></a>后续步骤

* [定价](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
* [区域可用性](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)
* [v3 翻译器参考](./reference/v3-0-reference.md)