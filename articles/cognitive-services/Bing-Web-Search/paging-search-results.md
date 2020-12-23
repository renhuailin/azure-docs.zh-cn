---
title: 如何逐页浏览搜索结果-必应搜索 API
titleSuffix: Azure Cognitive Services
description: 了解如何逐页浏览必应搜索 API 的搜索结果。
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 26CA595B-0866-43E8-93A2-F2B5E09D1F3B
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: aahi
ms.openlocfilehash: 670460759a9495de735da35ae9f3d8388e59e0e5
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350613"
---
# <a name="how-to-page-through-results-from-the-bing-search-apis"></a>如何逐页浏览必应搜索 API 的结果

> [!WARNING]
> 必应搜索 API 将从认知服务迁移到必应搜索服务。 从 2020 年 10 月 30 日开始，必应搜索的任何新实例都需按照[此处](/bing/search-apis/bing-web-search/create-bing-search-service-resource)所述的过程进行预配。
> 使用认知服务进行预配的必应搜索 API 将在未来三年或在企业协议结束前（以先发生者为准）得到支持。
> 有关迁移说明，请参阅[必应搜索服务](/bing/search-apis/bing-web-search/create-bing-search-service-resource)。

当你将调用发送到必应 Web、自定义、图像、新闻或视频搜索 Api 时，必应会返回可能与查询相关的结果总数的子集。 若要获取估计的可用结果总数，请访问应答对象的 `totalEstimatedMatches` 字段。 

例如： 

```json
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43CA...",
        "totalEstimatedMatches" : 262000,
        "value" : [...]
    }
}  
```

## <a name="paging-through-search-results"></a>在搜索结果中分页

若要逐页浏览可用结果，请 `count` `offset` 在发送请求时使用和查询参数。  

> [!NOTE]
>
> * 将必应视频、图像和新闻 Api 分页仅适用于常规视频 (`/video/search`) 、新闻 (`/news/search`) 和图像 (`/image/search`) 搜索。 不支持通过趋势主题和类别进行分页。  
> * 此 `TotalEstimatedMatches` 字段是对当前查询的搜索结果总数的估计值。 设置 `count` 和 `offset` 参数时，此估计值可能会更改。

| 参数 | 描述                                                                                                                                                                |
|-----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `count`   | 指定要在响应中返回的结果数。 请注意，的默认值为 `count` ，可能请求的最大结果数因 API 而异。 可以在 " [后续步骤](#next-steps)" 下的参考文档中找到这些值。 |
| `offset`  | 指定要跳过的结果数。 `offset` 从零开始，应小于 (`totalEstimatedMatches` - `count`)。                                           |

例如，如果想要每页显示15个结果，则应将设置 `count` 为15，将设置为 `offset` 0，以获取结果的第一页。 对于后续的每个 API 调用，将递增 `offset` 15。 下面的示例展示了如何从偏移 45 处开始请求获取 15 个网页。

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&count=15&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

如果使用默认 `count` 值，则只需 `offset` 在 API 调用中指定查询参数。  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

使用必应图像和视频 Api 时，可以使用 `nextOffset` 值来避免重复的搜索结果。 从 `Images` 或响应对象获取值 `Videos` ，并将其用于请求中的 `offset` 参数。  

> [!NOTE]
> 必应 Web 搜索 API 返回可包含网页、图像、视频和新闻的搜索结果。 当你从必应 Web 搜索 API 中翻到搜索结果 [时，你只会](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webpage)分页，而不是其他答案类型，如图像或新闻。 对象中的搜索结果 `WebPage` 可能还包括其他答案类型中显示的结果。
>
> 如果在 `responseFilter` 未指定任何筛选器值的情况下使用查询参数，请不要使用 `count` 和 `offset` 参数。 

## <a name="next-steps"></a>后续步骤

* [什么是必应 Web 搜索 Api？](bing-api-comparison.md)
* [必应 Web 搜索 API v7 参考](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)
* [Bing 自定义搜索 API v7 引用](/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference)
* [必应新闻搜索 API v7 引用](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference)
* [必应视频搜索 API v7 引用](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference)
* [必应图像搜索 API v7 引用](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)