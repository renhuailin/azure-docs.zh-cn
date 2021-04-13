---
title: 必应新闻搜索终结点
titleSuffix: Azure Cognitive Services
description: 本文提供新闻搜索 API 终结点的摘要；新闻、热门资讯和热门新闻。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 1/10/2019
ms.author: aahi
ms.openlocfilehash: 9c095a46b3a0526b23645c9cbb5e99eb8eda9067
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "94366394"
---
# <a name="bing-news-search-api-endpoints"></a>必应新闻搜索 API 终结点

> [!WARNING]
> 必应搜索 API 将从认知服务迁移到必应搜索服务。 从 2020 年 10 月 30 日开始，必应搜索的任何新实例都需按照[此处](/bing/search-apis/bing-web-search/create-bing-search-service-resource)所述的过程进行预配。
> 使用认知服务进行预配的必应搜索 API 将在未来三年或在企业协议结束前（以先发生者为准）得到支持。
> 有关迁移说明，请参阅[必应搜索服务](/bing/search-apis/bing-web-search/create-bing-search-service-resource)。

“新闻搜索 API”返回新闻文章、网页、图像、视频和[实体](../bing-entities-search/overview.md)。 实体包含有关人员、地点或主题的摘要信息。

## <a name="endpoints"></a>终结点

若要使用必应新闻搜索 API 获取新闻搜索结果，请向以下某个终结点发送 `GET` 请求。 标头和 URL 参数定义了更多规范。

### <a name="news-items-by-search-query"></a>搜索查询的新闻项

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search
```

基于搜索查询返回的新闻项。 如果搜索查询为空，API 将返回不同类别的热门资讯文章。 通过 URL 编码搜索术语发送查询并将其附加到 `q=""` 参数。 有关可用性，请参阅[支持的国家/地区和市场](language-support.md#supported-markets-for-news-search-endpoint)。

### <a name="top-news-items-by-category"></a>类别的热门新闻项

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news  
```

按类别返回热门新闻项。 可以使用 `category=business`、`category=sports` 或 `category=entertainment` 专门请求热门商业、体育或娱乐文章。  `category` 参数只能与 `/news` URL 结合使用。 指定类别有一些正式要求；请参阅[查询参数](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#query-parameters)文档中的 `category`。 通过 URL 编码搜索术语发送查询并将其附加到 `q=""` 参数。 有关可用性，请参阅[支持的国家/地区和市场](language-support.md#supported-markets-for-news-endpoint)。

### <a name="trending-news-topics"></a>热门新闻主题 

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/trendingtopics
```

返回当前社交网络上热门的新闻主题。 当包含 `/trendingtopics` 选项时，必应搜索会忽略其他几个参数，例如 `freshness` 和 `?q=""`。 有关可用性，请参阅[支持的国家/地区和市场](language-support.md#supported-markets-for-news-trending-endpoint)。

## <a name="next-steps"></a>后续步骤

有关标头、参数、市场代码、响应对象、错误等的详细信息，请参阅[必应新闻搜索 API v7](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference) 参考。

若要完整了解每个终结点支持的参数，请参阅每种类型对应的参考页面。
有关使用新闻搜索 API 的基本请求的示例，请参阅[必应新闻搜索快速入门](/azure/cognitive-services/bing-news-search)。