---
title: 什么是必应自动建议？
titleSuffix: Azure Cognitive Services
description: 必应自动建议 API 根据搜索框中的部分查询字符串返回建议查询的列表。
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: overview
ms.date: 12/18/2019
ms.openlocfilehash: f4df9d3c8cb17561cb822b2bd1ac43396470af52
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128618754"
---
# <a name="what-is-bing-autosuggest"></a>什么是必应自动建议？

> [!WARNING]
> 必应搜索 API 将从认知服务迁移到必应搜索服务。 从 2020 年 10 月 30 日开始，必应搜索的任何新实例都需按照[此处](/bing/search-apis/bing-web-search/create-bing-search-service-resource)所述的过程进行预配。
> 使用认知服务进行预配的必应搜索 API 将在未来三年或在企业协议结束前（以先发生者为准）得到支持。
> 有关迁移说明，请参阅[必应搜索服务](/bing/search-apis/bing-web-search/create-bing-search-service-resource)。

如果应用程序向任何必应搜索 API 发送查询，可以使用必应自动建议 API 来改进用户的搜索体验。 必应自动建议 API 根据搜索框中的部分查询字符串返回建议查询的列表。 在搜索框中输入字符时，可以在下拉列表中显示建议。

## <a name="bing-autosuggest-api-features"></a>必应自动建议 API 功能

| Feature                                                                                                                                                                                 | 说明                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [实时建议搜索词](concepts/get-suggestions.md) | 通过在你键入时使用自动建议 API 显示建议的搜索词来改善应用体验。 |

## <a name="workflow"></a>工作流

必应自动建议 API 是一项 RESTful Web 服务，可以轻松地通过任何编程语言调用，只要该语言能够发出 HTTP 请求和分析 JSON 即可。

1. 创建可以访问必应搜索 API 的[认知服务 API 帐户](../cognitive-services-apis-create-account.md)。 如果没有 Azure 订阅，可以免费[创建一个帐户](https://azure.microsoft.com/free/cognitive-services/)。
2. 每次用户在应用程序的搜索框中键入新字符时，都会向此 API 发送请求。
3. 通过分析返回的 JSON 消息处理 API 响应。

通常，每次用户在应用程序的搜索框中键入新字符时，你都会调用此 API。 输入更多字符时，API 将返回更相关的建议搜索查询。 例如，API 可能为单个 `s` 返回的建议可能不如为 `sail` 返回的建议相关。

下面的示例显示了一个下拉搜索框，其中包含 Bing 自动建议 API 提供的建议查询词。

![自动建议下拉搜索框列表](./media/cognitive-services-bing-autosuggest-api/bing-autosuggest-drop-down-list.PNG)

当用户从下拉列表中选择建议时，你可以使用它开始通过必应搜索 API 之一进行搜索，或者直接转到必应搜索结果页。

## <a name="next-steps"></a>后续步骤

若要快速了解如何使用第一个请求，请参阅[创建第一个查询](quickstarts/csharp.md)。

使自己熟悉[必应自动建议 API v7](/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference) 参考文档。 此参考文档包含可以用来请求建议查询词的终结点、标头和查询参数的列表，以及响应对象的定义。

请访问[必应搜索 API 中心页](../bing-web-search/overview.md)，浏览其他可用的 API。


了解如何使用[必应 Web 搜索 API](../bing-web-search/overview.md) 搜索 Web，并浏览其他[必应搜索 API](../bing-web-search/index.yml)。

确保阅读[必应使用和显示要求](../bing-web-search/use-display-requirements.md)，以免违反关于搜索结果使用要求的任何规则。