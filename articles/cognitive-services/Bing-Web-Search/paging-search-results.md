---
title: 如何分页列出搜索结果 - 必应搜索 API
titleSuffix: Azure Cognitive Services
description: 了解如何通过必应搜索 API 分页列出搜索结果。
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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "96350613"
---
# <a name="how-to-page-through-results-from-the-bing-search-apis"></a>如何通过必应搜索 API 分页列出结果

> [!WARNING]
> 必应搜索 API 将从认知服务迁移到必应搜索服务。 从 2020 年 10 月 30 日开始，必应搜索的任何新实例都需按照[此处](/bing/search-apis/bing-web-search/create-bing-search-service-resource)所述的过程进行预配。
> 使用认知服务进行预配的必应搜索 API 将在未来三年或在企业协议结束前（以先发生者为准）得到支持。
> 有关迁移说明，请参阅[必应搜索服务](/bing/search-apis/bing-web-search/create-bing-search-service-resource)。

将调用发送到必应 Web、自定义、图像、资讯或视频搜索 API 时，必应会返回可能与该查询相关的结果总数的子集。 要获取估计的可访问结果总数，请访问答案对象的 `totalEstimatedMatches` 字段。 

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

## <a name="paging-through-search-results"></a>分页列出搜索结果

要分页列出可访问结果，请在发送请求时使用 `count` 和 `offset` 查询参数。  

> [!NOTE]
>
> * 使用必应视频、图像和资讯 API 分页仅适用于常规视频 (`/video/search`)、资讯 (`/news/search`) 和图像 (`/image/search`) 搜索。 不支持分页列出趋势主题和类别。  
> * `TotalEstimatedMatches` 字段是当前查询的搜索结果预估总数。 设置 `count` 和 `offset` 参数时，此估计值可能会更改。

| 参数 | 说明                                                                                                                                                                |
|-----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `count`   | 指定要在响应中返回的结果数。 请注意，`count` 的默认值和可请求的最大结果数因 API 而异。 可以在[后续步骤](#next-steps)下的参考文档中找到这些值。 |
| `offset`  | 指定要跳过的结果数。 `offset` 从零开始，应小于 (`totalEstimatedMatches` - `count`)。                                           |

例如，如果希望每页显示 15 个结果，可以将 `count` 设置为 15，并将 `offset` 设置为 0，即可获得第一页结果。 对于后续每个 API 调用，可以使 `offset` 以 15 递增。 下面的示例展示了如何从偏移 45 处开始请求获取 15 个网页。

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&count=15&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

如果使用默认 `count` 值，则只需在 API 调用中指定 `offset` 查询参数。  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

使用必应图像和视频 API 时，可以使用 `nextOffset` 值来避免重复的搜索结果。 从 `Images` 或 `Videos` 响应对象获取值，并将其用于请求中的 `offset` 参数。  

> [!NOTE]
> 必应 Web 搜索 API 返回的搜索结果可能包括网页、图像、视频和资讯。 使用必应 Web 搜索 API 分页列出搜索结果时，只能对[网页](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webpage)分页，而不能对图像或资讯等其他答案类型分页。 `WebPage` 对象中的搜索结果也可能包括其他答案类型中显示的结果。
>
> 如果使用 `responseFilter` 查询参数时未指定筛选器值，请不要使用 `count` 和 `offset` 参数。 

## <a name="next-steps"></a>后续步骤

* [什么是必应 Web 搜索 API？](bing-api-comparison.md)
* [必应 Web 搜索 API v7 参考](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)
* [必应自定义搜索 API v7 参考](/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference)
* [必应资讯搜索 API v7 参考](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference)
* [必应视频搜索API v7 参考](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference)
* [必应图像搜索 API v7 参考](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)