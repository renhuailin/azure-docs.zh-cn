---
title: 什么是必应拼写检查 API？
titleSuffix: Azure Cognitive Services
description: 了解必应拼写检查 API，该 API 使用机器学习和统计性机器翻译进行上下文拼写检查。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: overview
ms.date: 12/19/2019
ms.author: aahi
ms.openlocfilehash: be864e04143a381a08dedb5693026f05c42c5421
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2020
ms.locfileid: "96349293"
---
# <a name="what-is-the-bing-spell-check-api"></a>什么是必应拼写检查 API？

> [!WARNING]
> 必应搜索 API 将从认知服务迁移到必应搜索服务。 从 2020 年 10 月 30 日开始，必应搜索的任何新实例都需按照[此处](/bing/search-apis/bing-web-search/create-bing-search-service-resource)所述的过程进行预配。
> 使用认知服务进行预配的必应搜索 API 将在未来三年或在企业协议结束前（以先发生者为准）得到支持。
> 有关迁移说明，请参阅[必应搜索服务](/bing/search-apis/bing-web-search/create-bing-search-service-resource)。

使用必应拼写检查 API 可对文本执行上下文语法和拼写检查。 虽然大多数拼写检查器都依赖于基于字典的规则集，但必应拼写检查器利用机器学习和统计性机器翻译来提供准确的上下文更正。 

## <a name="features"></a>功能

| 功能 | 说明 |
|---------|---------|
|多种拼写检查模式     | 多种拼写检查模式使用户可以获得专注于语法和/或拼写的更正。 |
|俚语和非正式语言识别     | 识别文本中常用的表达和非正式用语。         |
|区分相似词     | 找出发音相似但含义不同的词之间的正确用法（如，“see”和“sea”）        |
|品牌、标题、热门用法支持     | 新品牌、标题和其他常用表达出现后能够识别这些词 |

## <a name="workflow"></a>工作流

可以轻松地通过任何编程语言调用必应拼写检查 API，只要该语言能够发出 HTTP 请求和分析 JSON 响应即可。 此服务可以通过 REST API 或必应拼写检查 SDK 进行访问。 

1. 创建可以访问必应搜索 API 的[认知服务 API 帐户](../cognitive-services-apis-create-account.md)。 如果没有 Azure 订阅，可以创建一个免费帐户。 
2. 向必应 Web 搜索 API 发送请求。
3. 分析 JSON 响应

## <a name="next-steps"></a>后续步骤

首先，请试用必应拼写检查搜索 API [互动演示](https://azure.microsoft.com/services/cognitive-services/spell-check/)，了解如何快速检查各种文本。

做好调用 API 的准备后，请创建一个[认知服务 API 帐户](../../cognitive-services/cognitive-services-apis-create-account.md)。 如果没有 Azure 订阅，可以免费[创建一个帐户](https://azure.microsoft.com/free/cognitive-services/)。

还可以访问[必应搜索 API 中心页](../bing-web-search/overview.md)，浏览其他可用的 API。