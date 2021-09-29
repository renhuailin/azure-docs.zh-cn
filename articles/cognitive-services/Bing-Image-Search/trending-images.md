---
title: 使用必应图像搜索 API 获取热门图像
titleSuffix: Azure Cognitive Services
description: 使用必应图像搜索 API 从 Web 搜索目前的热门图像。
services: cognitive-services
manager: nitinme
ms.assetid: EAB92D35-5C0B-4A0A-8F49-02DF7FAD44B4
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.custom: seodec2018
ms.openlocfilehash: fb8e27ea3d35a6a8c6f83090d97188d7473b5890
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128619792"
---
# <a name="get-trending-images-from-the-web"></a>从 Web 获取热门图像

> [!WARNING]
> 必应搜索 API 将从认知服务迁移到必应搜索服务。 从 2020 年 10 月 30 日开始，必应搜索的任何新实例都需按照[此处](/bing/search-apis/bing-web-search/create-bing-search-service-resource)所述的过程进行预配。
> 使用认知服务进行预配的必应搜索 API 将在未来三年或在企业协议结束前（以先发生者为准）得到支持。
> 有关迁移说明，请参阅[必应搜索服务](/bing/search-apis/bing-web-search/create-bing-search-service-resource)。

要获取今天的热门图像，请发送以下 GET 请求：  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/trending?mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

热门图像 API 目前仅支持以下市场：  

- en-US（英语，美国）  
- en-CA（英语，加拿大）  
- en-AU（英语，澳大利亚）  
- zh-CN（中文，中国）

响应包含 [TrendingImages](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#trendingimages) 对象，该对象按类别列出图像。 使用类别的 `title` 将图像分组到用户体验中。 可能每天更改类别。  

```json
{
    "_type" : "TrendingImages",  
    "categories" : [{  
        "title" : "Popular people searches",  
        "tiles" : [{  
            "query" : {  
                "text" : "Smith",  
                "displayText" : "Mr. Smith",  
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?q=smith&FORM=..."
            },  
            "image" : {  
                "id" : "C3C60AE779A054D5CD80D3CACF0F3",  
                "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?id=OIP.M2532...",  
                "contentUrl" : "http:\/\/www.contoso.com.au\/assets\/Uploads\/smith-SH01.jpg",  
                "thumbnail" : {  
                    "width" : 288,  
                    "height" : 300  
                }  
            }  
        },  
        . . .  
        ]  
    },  
    . . .  
    {  
        "title" : "Popular Halloween searches",  
        "tiles" : [{  
            "query" : {  
                "text" : "Halloween costumes for adults",  
                "displayText" : "Halloween costumes for adults",  
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?q=Halloween+costumes..."
            },  
            "image" : {  
                "id" : "0F3395F2983003F89DCEE711B55D7FA53E4",  
                "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OIP.Me429c...",  
                "contentUrl" : "http:\/\/images.domain.com\/products\/8179\/1-1\/adult-squirrel...",  
                "thumbnail" : {  
                    "width" : 336,  
                    "height" : 480  
                }  
            }  
        }]  
    }]  
}  
```  

每个磁贴包含用于获取相关图像的图像和选项。 要获取相关图像，你可以使用查询 `text` 来调用[图像搜索 API](./overview.md)，并自行显示相关图像。 或者，你可以使用 `webSearchUrl` 中的 URL 将用户转到必应图像搜索结果页面，其中包含相关图像。

如果调用图像搜索 API 来获取相关图像，请将 [id](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#id) 查询参数设置为 `id` 字段中的 ID。 指定 ID 可确保响应包含图像（它是响应中的第一个图像）及其相关图像。 另外，将 [q](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) 查询参数设置为 `query` 对象的 `text` 字段中的文本。

以下示例显示如何使用图像 ID 在先前的热门图像 API 响应中获取 Smith 先生的相关图像。

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=Smith&id=77FDE4A1C6529A23C7CF0EC073FAA64843E828F2&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```