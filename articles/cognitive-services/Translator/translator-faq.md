---
title: 常见问题解答 - 翻译器
titleSuffix: Azure Cognitive Services
description: 获取 Azure 认知服务中翻译器 API 的常见问题解答。
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/18/2021
ms.author: lajanuar
ms.openlocfilehash: db1d981305ee62267f72f3050475c88dbcd5cc54
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2021
ms.locfileid: "110116519"
---
# <a name="frequently-asked-questionstranslator-api"></a>常见问题解答 - 翻译器 API

## <a name="how-does-translator-count-characters"></a>翻译器如何计算字符数？

翻译器将 Unicode 中定义的每个码位计为一个字符。 每个翻译都计为单独的翻译，即使在单个 API 调用中发出翻译为多种语言的请求时也是如此。 响应的长度无关紧要，请求、字词、字节或句子的数目与字符数无关。

翻译器会计入以下输入：

* 在请求正文中传递到翻译器的文本。
  * `Text`（如果使用 [Translate](reference/v3-0-translate.md)、[Transliterate](reference/v3-0-transliterate.md) 和 [Dictionary Lookup](reference/v3-0-dictionary-lookup.md) 方法）
  * `Text` 和 `Translation`（如果使用 [Dictionary Examples](reference/v3-0-dictionary-examples.md) 方法）。

* 所有标记：请求正文文本字段内的 HTML、XML 标记等。 用于生成请求的 JSON 表示法（例如键“Text:”）不计入。
* 单个字母。
* 标点。
* 空格、制表符、标记或任何空白字符。
* 重复的翻译（即使之前已翻译相同的文本）。 即使内容未更改或源和目标语言相同，也会对提交到翻译函数的每个字符进行计数。

对于基于图形符号（例如书面中文汉字和日文汉字）的脚本，翻译器服务会对 Unicode 码位计数。 每个符号计为一个字符。 例外情况：Unicode 代理项对计为两个字符。

对 Detect 和 BreakSentence 方法的调用不计入字符消耗 。 但是，我们希望对 Detect 和 BreakSentence 方法的调用次数与其他计数函数的使用次数成合理的比例。 如果 Detect 或 BreakSentence 调用的数量是其他计数方法数量的 100 倍，Microsoft 保留限制使用 Detect 和 BreakSentence 方法的权利。

## <a name="where-can-i-see-my-monthly-usage"></a>在哪里可以看到每月使用情况？

可使用 [Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/)来估算成本。 还可以在 Azure 门户中的用户帐户中监视、查看和添加 Azure 服务的 Azure 警报：

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 导航到翻译器资源概述页面。
1. 为翻译器资源选择“订阅”。

    :::image type="content" source="media/azure-portal-overview.png" alt-text="Azure 门户中概述页面上订阅链接的屏幕截图。":::

2. 在左侧滑轨中，在“成本管理”下进行选择：

    :::image type="content" source="media/azure-portal-cost-management.png" alt-text="Azure 门户中成本管理资源链接的屏幕截图。":::

## <a name="is-attribution-required-when-using-translator"></a>使用翻译器时，是否需指定归属？

使用翻译器进行文本和语音翻译时，不需要指定归属。 建议告知用户，他们正在查看的内容是机器翻译内容。

如果存在归属，它必须符合[翻译器归属准则](https://www.microsoft.com/translator/business/attribution/)。

## <a name="is-translator-a-replacement-for-human-translator"></a>翻译器是否可以替代人工翻译？

否，作为重要的交流工具，两者都占有一席之地。 如果内容量、创建速度和预算限制使得无法使用人工翻译，则可以使用机器翻译。

我们的一些[语言服务提供商 (LSP)](https://www.microsoft.com/translator/business/partners/) 合作伙伴会在使用人工翻译之前先使用机器翻译作为第一遍翻译，这可以提高高达 50% 的生产效率。 有关 LSP 合作伙伴的列表，请访问翻译器合作伙伴页面。

---
> [!TIP]
> 如果未在本常见问题解答中找到问题的答案，请尝试在 [StackOverflow](https://stackoverflow.com/search?q=%5Bmicrosoft-cognitive%5D+or+%5Bmicrosoft-cognitive%5D+translator&s=34bf0ce2-b6b3-4355-86a6-d45a1121fe27) 上求助翻译器 API 社区。