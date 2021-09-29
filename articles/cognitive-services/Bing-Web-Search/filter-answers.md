---
title: 如何筛选搜索结果 - 必应 Web 搜索 API
titleSuffix: Azure Cognitive Services
description: 可以使用“responseFilter”查询参数筛选必应在响应中提供的结果类型（例如，图像、视频和新闻）。
services: cognitive-services
manager: nitinme
ms.assetid: 8B837DC2-70F1-41C7-9496-11EDFD1A888D
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/08/2019
ms.openlocfilehash: 8bc11b3f59246c736f8c099e97637c3605405924
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128625483"
---
# <a name="filtering-the-answers-that-the-search-response-includes"></a>筛选搜索响应包含的结果  

> [!WARNING]
> 必应搜索 API 将从认知服务迁移到必应搜索服务。 从 2020 年 10 月 30 日开始，必应搜索的任何新实例都需按照[此处](/bing/search-apis/bing-web-search/create-bing-search-service-resource)所述的过程进行预配。
> 使用认知服务进行预配的必应搜索 API 将在未来三年或在企业协议结束前（以先发生者为准）得到支持。
> 有关迁移说明，请参阅[必应搜索服务](/bing/search-apis/bing-web-search/create-bing-search-service-resource)。

查询 Web 时，必应会返回它为搜索找到的所有相关内容。 例如，如果搜索查询为“航行+小船”，则响应可能包含以下结果：

```json
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43C...",
        "totalEstimatedMatches" : 262000,
        "value" : [...]
    },
    "images" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images",
        "readLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=sail...",
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43CA5CA6464E5D...",
        "isFamilyFriendly" : true,
        "value" : [...]
    },
    "rankingResponse" : {
        "mainline" : {
            "items" : [...]
        }
    }
}    
```

## <a name="query-parameters"></a>查询参数

若要筛选必应返回的结果，请在调用 API 时使用以下查询参数。  

### <a name="responsefilter"></a>ResponseFilter

可以使用 [responseFilter](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#responsefilter) 查询参数（以逗号分隔的结果列表）筛选必应在响应中提供的结果类型（例如，图像、视频和新闻）。 如果必应找到相关内容，响应中会包含结果。 

若要从响应中排除特定结果（例如图像），请在结果类型之前添加 `-` 字符。 例如：

```
&responseFilter=-images,-videos
```

下面展示如何使用 `responseFilter` 来请求“航行小艇”的图像、视频和新闻。 对查询字符串进行编码时，逗号会更改为 %2C。  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&responseFilter=images%2Cvideos%2Cnews&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

以下展示对上一查询的响应。 因为必应未找到相关的视频和新闻结果，所以响应中不包含这些内容。

```json
{
    "_type" : "SearchResponse",
    "images" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images",
        "readLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=sail...",
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3AD78B183C56456C...",
        "isFamilyFriendly" : true,
        "value" : [...]
    },
    "rankingResponse" : {
        "mainline" : {
            "items" : [{
                "answerType" : "Images",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images"
                }
            }]
        }
    }
}
```

虽然必应在之前的响应中未返回视频和新闻结果，但这并不意味着不存在这些视频和新闻内容。 这只表明页面中未包含。 但如果[翻页](./paging-search-results.md)浏览更多结果，则后续页面中可能会包含这些内容。 此外，如果直接调用[视频搜索 API](../bing-video-search/overview.md) 和[新闻搜索 API](../bing-news-search/search-the-web.md) 终结点，则响应中很可能会包含结果。

不建议使用 `responseFilter` 从单个 API 获取结果。 如果需要来自单个必应 API 的内容，可直接调用该 API。 例如，如果希望只接收图像，可向图像搜索 API 终结点 `https://api.cognitive.microsoft.com/bing/v7.0/images/search` 或其他[图像](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#endpoints)终结点发送请求。 调用单个 API 非常重要，这不仅是出于性能原因，还因为特定于内容的 API 可提供更丰富的结果。 例如，可使用不适用于 Web 搜索 API 的筛选器来筛选结果。  

### <a name="site"></a>站点

若要从特定域获取搜索结果，请在查询字符串中包含 `site:` 查询参数。  

```
https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us
```

> [!NOTE]
> 如果使用 `site:` 查询运算符，则不管 [safeSearch](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#safesearch) 设置如何，仍有可能出现响应中包含成人内容的情况，具体取决于查询。 只有在知道网站内容且方案允许使用成人内容的情况下，才应使用 `site:`。

### <a name="freshness"></a>新鲜度

若要将 Web 响应结果限制为必应在特定时段发现的网页，请将 [freshness](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#freshness) 查询参数设置为以下值之一（不区分大小写）：

* `Day` - 返回必应在过去 24 小时内发现的网页
* `Week` - 返回必应在过去 7 天内发现的网页
* `Month` - 返回在过去 30 天内发现的网页

还可以将此参数设置为 `YYYY-MM-DD..YYYY-MM-DD` 格式的自定义日期范围。 

`https://<host>/bing/v7.0/search?q=ipad+updates&freshness=2019-02-01..2019-05-30`

若要将结果限制为单个日期，请将 freshness 参数设置为特定日期：

`https://<host>/bing/v7.0/search?q=ipad+updates&freshness=2019-02-04`

如果必应符合你的筛选条件的网页数少于你请求的网页数（或必应返回的默认数量），则结果可能包括指定时段以外的网页。

## <a name="limiting-the-number-of-answers-in-the-response"></a>限制响应中的检索结果数

必应可以在 JSON 响应中返回多种结果类型。 例如，如果查询“sailing+dinghies”，则必应可能会返回 `webpages`、`images`、`videos` 和 `relatedSearches`。

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailing dinghies"
    },
    "webPages" : {...},
    "images" : {...},
    "relatedSearches" : {...},
    "videos" : {...},
    "rankingResponse" : {...}
}
```

若要将必应返回的检索结果限为排名前两位的结果（网页和图片），可将 [answerCount](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#answercount) 查询参数设置为 2。

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&answerCount=2&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

响应仅包括 `webPages` 和 `images`。

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailing dinghies"
    },
    "webPages" : {...},
    "images" : {...},
    "rankingResponse" : {...}
}
```

如果将 `responseFilter` 查询参数添加到上一查询并将其设置为网页和新闻，则响应中只会包含网页，因为未对新闻进行排名。

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailing dinghies"
    },
    "webPages" : {...},
    "rankingResponse" : {...}
}
```

## <a name="promoting-answers-that-are-not-ranked"></a>提升未排名的检索结果

如果必应针对查询返回的首选检索结果是网页、图片、视频和 relatedSearches，则响应中会包含这些结果。 如果将 [answerCount](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#answercount) 设置为二 (2)，则必应会返回排名前两位的检索结果：网页和图片。 如果希望必应在响应中包含图片和视频，请指定[提升](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#promote)查询参数并将其设置为图片和视频。

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&answerCount=2&promote=images%2Cvideos&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

以下是对上述请求的响应。 必应返回排名前两位的检索结果（网页和图片），并将视频提升到检索结果中。

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailiing dinghies"
    },
    "webPages" : {...},
    "images" : {...},
    "videos" : {...},
    "rankingResponse" : {...}
}
```

如果将 `promote` 设置为新闻，则响应不会包含新闻检索结果，因为它不是经过排名的检索结果&mdash;，只能提升经过排名的检索结果。

要提升的检索结果不计入 `answerCount` 限制。 例如，如果经过排名的检索结果有新闻、图片和视频，且将 `answerCount` 设置为 1，将 `promote` 设置为新闻，则响应会包含新闻和图片。 或者，如果经过排名的检索结果有视频、图像和新闻，则响应会包含视频和新闻。

只有在指定了 `answerCount` 查询参数时，才可使用 `promote`。