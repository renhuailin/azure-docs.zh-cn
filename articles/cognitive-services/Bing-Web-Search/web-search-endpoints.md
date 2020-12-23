---
title: Web 搜索终结点
titleSuffix: Azure Cognitive Services
description: 若要获取 web 搜索结果，请将 `GET` 请求发送到以下终结点。 标头和 URL 参数定义了更多规范。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: aahi
ms.openlocfilehash: c882e3e4d0cd6ba594a700f4fd53c14103a8d1d1
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/09/2020
ms.locfileid: "94381246"
---
# <a name="web-search-endpoint"></a>Web 搜索终结点

> [!WARNING]
> 必应搜索 API 将从认知服务迁移到必应搜索服务。 从 2020 年 10 月 30 日开始，必应搜索的任何新实例都需按照[此处](/bing/search-apis/bing-web-search/create-bing-search-service-resource)所述的过程进行预配。
> 使用认知服务进行预配的必应搜索 API 将在未来三年或在企业协议结束前（以先发生者为准）得到支持。
> 有关迁移说明，请参阅[必应搜索服务](/bing/search-apis/bing-web-search/create-bing-search-service-resource)。

“Web 搜索 API”返回网页、新闻、图像、视频和[实体](../bing-entities-search/overview.md)。 实体包含有关人员、地点或主题的摘要信息。

## <a name="endpoint"></a>终结点

若要使用必应 API 获取 Web 搜索结果，请向以下终结点发送 `GET` 请求。 标头和 URL 参数定义了更多规范。

终结点：返回与 `?q=""` 定义的用户搜索查询相关的 Web 结果。

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/search
```

终结点：有关标头、参数、市场代码、响应对象、错误等内容的详细信息，请参阅[必应 Web API v7](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference) 参考。

## <a name="response-json"></a>响应 JSON

对 Web 搜索请求的响应包括作为 JSON 对象的所有结果。 分析结果需要处理每种类型元素的过程。 有关示例，请参阅[教程](./tutorial-bing-web-search-single-page-app.md)和[源代码](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/Tutorials/Bing-Web-Search)。

## <a name="next-steps"></a>后续步骤

必应 API 支持根据其类型返回结果的搜索操作。 所有搜索终结点均将结果作为 JSON 响应对象返回。  所有终结点均支持按经度、纬度和搜索半径返回特定语言和位置的查询。

若要完整了解每个终结点支持的参数，请参阅每种类型对应的参考页面。
有关使用 Web 搜索 API 的基本请求的示例，请参阅[搜索 Web 快速入门](./overview.md)。