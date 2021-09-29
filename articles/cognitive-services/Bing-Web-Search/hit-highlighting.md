---
title: 如何使用修饰标记来突出显示文本 | 必应 Web 搜索 API
titleSuffix: Azure Cognitive Services
description: 了解如何通过必应 Web 搜索 API 使用文本修饰及在搜索结果中点击突出显示。
services: cognitive-services
manager: nitinme
ms.assetid: 5365B568-EA55-4D97-8FBE-0AF60158D4D5
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/30/2019
ms.openlocfilehash: 47ac724cfe62aac91e862702f6af8f949b1ea047
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128676345"
---
# <a name="using-decoration-markers-to-highlight-text"></a>使用修饰标记来突出显示文本

> [!WARNING]
> 必应搜索 API 将从认知服务迁移到必应搜索服务。 从 2020 年 10 月 30 日开始，必应搜索的任何新实例都需按照[此处](/bing/search-apis/bing-web-search/create-bing-search-service-resource)所述的过程进行预配。
> 使用认知服务进行预配的必应搜索 API 将在未来三年或在企业协议结束前（以先发生者为准）得到支持。
> 有关迁移说明，请参阅[必应搜索服务](/bing/search-apis/bing-web-search/create-bing-search-service-resource)。

必应支持命中词突出显示，此功能会在某些结果的显示字符串中标记查询词（或必应认为相关的其他词）。 例如，网页结果的 `name`、`displayUrl` 和 `snippet` 字段可能包含标记的查询字词。 

默认情况下，必应不在显示字符串中包括突出显示标记。 若要启用这些标记，请在请求中包括 `textDecorations` 查询参数，并将其设置为 `true`。

## <a name="hit-highlighting-example"></a>命中词突出显示示例

下面的示例显示了 `Sailing Dinghy` 的 Web 结果。 必应使用 E000 和 E001 Unicode 字符标记了查询词的开头和结尾。
  
![命中词突出显示](./media/cognitive-services-bing-web-api/bing-hit-highlighting.png) 

在用户界面中显示结果之前，请将 Unicode 字符替换为适合显示格式的字符。

## <a name="marker-formatting"></a>标记格式设置

必应提供使用 Unicode 字符或 HTML 标记作为标记这一选项。 若要指定要使用的标记，请包括 [textFormat](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#textformat) 查询参数： 

| 值             | 记号笔                       |
|-------------------|------------------------------|
| `textFormat=Raw`  | Unicode 字符（默认值） |
| `textFormat=HTML` | HTML 字符              |

## <a name="additional-text-decorations"></a>其他文本修饰

必应可能返回几种不同的文本修饰。 例如，`Computation` 结果可能在 `expression` 字段中包含查询词 `log(2)` 的下标标记。

![计算标记](./media/cognitive-services-bing-web-api/bing-markers-computation.png) 

如果请求未指定修饰，则 `expression` 字段会包含 `log10(2)`。 

如果 `textDecorations` 为 `true`，必应会在结果的显示字符串中包括以下标记。 如果没有等效的 HTML 标记，则 HTML 表格单元为空。

|Unicode|HTML|说明
|-|-|-
|U+E000|\<b>|表示查询词的开头（命中词突出显示）
|U+E001|\</b>|表示查询词的结尾
|U+E002|\<i>|表示斜体内容的开头 
|U+E003|\</i>|表示斜体内容的结尾
|U+E004|\<br/>|表示换行
|U+E005||表示电话号码的开头
|U+E006||表示电话号码的结尾
|U+E007||表示地址的开头
|U+E008||表示地址的结尾
|U+E009|\&nbsp;|表示不间断的空格
|U+E00C|\<strong>|表示粗体内容的开头
|U+E00D|\</strong>|表示粗体内容的结尾
|U+E00E||表示其背景颜色应比其周围背景颜色浅的内容的开头
|U+E00F||表示其背景颜色应比其周围背景颜色浅的内容的结尾
|U+E010||表示其背景颜色应比其周围背景颜色深的内容的开头
|U+E011||表示其背景颜色应比其周围背景颜色深的内容的结尾
|U+E012|\<del>|表示应删除的内容的开头
|U+E013|\</del>|表示应删除的内容的结尾
|U+E016|\<sub>|表示下标内容的开头
|U+E017|\</sub>|表示下标内容的结尾
|U+E018|\<sup>|表示上标内容的开头
|U+E019|\</sup>|表示上标内容的结尾

## <a name="next-steps"></a>后续步骤

* [什么是必应 Web 搜索 API？](overview.md) 
* [调整和裁剪缩略图](resize-and-crop-thumbnails.md)