---
title: 什么是必应实体搜索 API？
titleSuffix: Azure Cognitive Services
description: 详细了解必应实体搜索 API 以及如何从搜索查询中提取和搜索实体和场所。
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: overview
ms.date: 12/18/2019
ms.openlocfilehash: 7cf29bd4c20d6acd2a5a69cf9f8bbfb705413a0a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128631146"
---
# <a name="what-is-bing-entity-search-api"></a>什么是必应实体搜索 API？

> [!WARNING]
> 必应搜索 API 将从认知服务迁移到必应搜索服务。 从 2020 年 10 月 30 日开始，必应搜索的任何新实例都需按照[此处](/bing/search-apis/bing-web-search/create-bing-search-service-resource)所述的过程进行预配。
> 使用认知服务进行预配的必应搜索 API 将在未来三年或在企业协议结束前（以先发生者为准）得到支持。
> 有关迁移说明，请参阅[必应搜索服务](/bing/search-apis/bing-web-search/create-bing-search-service-resource)。

必应实体搜索 API 向必应发送搜索查询，然后获得包含实体和场所的结果。 位置结果包括餐馆、酒店或其他当地企业。 如果查询指定了本地商业场所的名称或询问某类商业场所（例如，“我附近的餐馆”），则必应会返回场所。 如果查询指定知名的人物、场所（旅游景点、州、国家/地区等）或事物，则必应会返回实体。

|Feature  |说明  |
|---------|---------|
|[实时搜索建议](concepts/search-for-entities.md#suggest-search-terms-with-the-bing-autosuggest-api)     | 当用户键入搜索词时，以下拉列表的形式提供搜索建议。       | 
| [实体歧义消除](concepts/search-for-entities.md#the-bing-entity-search-api-response)  | 为具有多个可能含义的查询获取多个实体。 |
| [查找场所](concepts/search-for-entities.md#find-places) | 搜索并返回有关本地商家和实体的信息  |

## <a name="workflow"></a>工作流

必应实体搜索 API 是一项 RESTful Web 服务，可以轻松地通过任何编程语言调用，只要该语言能够发出 HTTP 请求和分析 JSON 即可。 可以通过 REST API 或 SDK 使用此服务。

1. 创建可以访问必应搜索 API 的[认知服务 API 帐户](../cognitive-services-apis-create-account.md)。 如果没有 Azure 订阅，可以免费[创建一个帐户](https://azure.microsoft.com/free/cognitive-services/)。
2. 使用有效的搜索查询向 API 发送请求。
3. 通过分析返回的 JSON 消息处理 API 响应。

## <a name="next-steps"></a>后续步骤

* 尝试使用必应实体搜索 API 的[交互式演示](https://azure.microsoft.com/services/cognitive-services/bing-entity-search-api/)。 
* 若要快速开始你的第一个请求，请尝试使用[快速入门](quickstarts/csharp.md)。
* [必应实体搜索 API v7](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference) 参考部分。
* [必应使用和显示要求](../bing-web-search/use-display-requirements.md)指定了允许用户如何使用通过必应搜索 API 获得的内容和信息。
* 请访问[必应搜索 API 中心页](../bing-web-search/overview.md)，浏览其他可用的 API。