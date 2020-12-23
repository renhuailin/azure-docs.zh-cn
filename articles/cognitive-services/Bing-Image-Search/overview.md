---
title: 什么是必应图像搜索 API？
titleSuffix: Azure Cognitive Services
description: 必应图像搜索 API 允许你在应用程序中使用必应的认知图像搜索功能。 使用此 API 发送用户搜索查询时，可以获取并显示与必应图像类似的相关高质量图像。
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 1446AD8B-A685-4F5F-B4AA-74C8E9A40BE9
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: overview
ms.date: 12/18/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: c52098d86efe60ee524735e6158add5260a0757f
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2020
ms.locfileid: "96338225"
---
# <a name="what-is-the-bing-image-search-api"></a>什么是必应图像搜索 API？

> [!WARNING]
> 必应搜索 API 将从认知服务迁移到必应搜索服务。 从 2020 年 10 月 30 日开始，必应搜索的任何新实例都需按照[此处](/bing/search-apis/bing-web-search/create-bing-search-service-resource)所述的过程进行预配。
> 使用认知服务进行预配的必应搜索 API 将在未来三年或在企业协议结束前（以先发生者为准）得到支持。
> 有关迁移说明，请参阅[必应搜索服务](/bing/search-apis/bing-web-search/create-bing-search-service-resource)。

必应图像搜索 API 允许你在应用程序中使用必应的图像搜索功能。 通过向 API 发送搜索查询，可以获取与 [bing.com/images](https://www.bing.com/images) 类似的高质量图像。

虽然必应图像搜索 API 提供仅限图像的搜索结果，但是你可以组合或使用其他可用的[必应搜索 API](../bing-web-search/bing-api-comparison.md) 在 Web 上查找多种类型的内容。

## <a name="bing-image-search-features"></a>必应图像搜索功能

| Feature                                                                                                                                                                                 | 说明                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [实时建议搜索词](./concepts/bing-image-search-sending-queries.md) | 在用户键入时通过[必应自动建议 API](../bing-autosuggest/get-suggested-search-terms.md) 显示建议的搜索词改善应用体验。 |
| [筛选和限制图像结果](./concepts/bing-image-search-get-images.md)                       | 通过编辑查询参数筛选必应返回的图像。                                                                                                       |
| [缩略图的裁剪、重设大小和显示](../bing-web-search/resize-and-crop-thumbnails.md)                                                | 编辑和显示必应图像搜索返回的图像的缩略图预览。                                                                                      |
| [用户搜索查询的分段和扩展](./concepts/bing-image-search-sending-queries.md)               | 通过包括和显示必应建议的查询搜索词，扩展搜索功能。                                                                    |
| [获取热门图像](trending-images.md)                                                                     | 自定义一个搜罗全世界热门图像的搜索。                                                                                                          |

## <a name="workflow"></a>工作流

必应图像搜索 API 是一项 RESTful Web 服务，可以轻松地通过任何编程语言调用，只要该语言能够发出 HTTP 请求和分析 JSON 即可。 可以通过 [REST API](./quickstarts/csharp.md) 或 [SDK](./quickstarts/client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp) 使用此服务。

1. 创建可以访问必应搜索 API 的[认知服务 API 帐户](../cognitive-services-apis-create-account.md)。 如果没有 Azure 订阅，可以免费[创建一个帐户](https://azure.microsoft.com/free/cognitive-services/)。
2. 使用有效的[搜索查询](./concepts/bing-image-search-sending-queries.md)向 API 发送请求。
3. 通过分析返回的 JSON 消息处理 API 响应。

## <a name="next-steps"></a>后续步骤

首先，请尝试必应搜索 API [交互式演示](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)。
此演示介绍如何快速自定义搜索查询并在 Web 中搜索图像。

若要完成第一个 API 请求的快速入门，可以学习：

* 使用 REST API [向必应发送搜索查询](./quickstarts/csharp.md)，或者
* 使用 SDK [请求和筛选](./quickstarts/client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp)必应返回的图像。

## <a name="see-also"></a>另请参阅

* 必应搜索 API 的[定价详细信息](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)。 

* [必应图像搜索 API v7](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) 参考部分包含有关 API 的终结点、标头、API 响应和查询参数的信息。

* [必应使用和显示要求](../bing-web-search/use-display-requirements.md)指定了允许用户如何使用通过必应搜索 API 获得的内容和信息。

* [使用必应图像搜索 API 从 Web 获取图像](./concepts/bing-image-search-get-images.md)一文介绍了如何在 Web 中搜索和获取图像。

* [发送和使用搜索查询](./concepts/bing-image-search-sending-queries.md)一文介绍了如何完成搜索查询的发出、自定义和分段。

* 请访问[必应搜索 API 中心页](../bing-web-search/overview.md)，浏览其他可用的 API。