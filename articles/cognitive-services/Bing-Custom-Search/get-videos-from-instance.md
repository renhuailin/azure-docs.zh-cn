---
title: 从自定义视图中获取视频 - 必应自定义搜索
titleSuffix: Azure Cognitive Services
description: 有关使用必应自定义搜索从 Web 的自定义视图获取视频的简要概述。
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 09/10/2018
ms.openlocfilehash: 4bd2c63bb6fc494cb93c4fc666150c0e1a5477f7
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128644042"
---
# <a name="get-videos-from-your-custom-view"></a>从自定义视图获取视频

> [!WARNING]
> 必应搜索 API 将从认知服务迁移到必应搜索服务。 从 2020 年 10 月 30 日开始，必应搜索的任何新实例都需按照[此处](/bing/search-apis/bing-web-search/create-bing-search-service-resource)所述的过程进行预配。
> 使用认知服务进行预配的必应搜索 API 将在未来三年或在企业协议结束前（以先发生者为准）得到支持。
> 有关迁移说明，请参阅[必应搜索服务](/bing/search-apis/bing-web-search/create-bing-search-service-resource)。

必应自定义视频搜索可以让你使用视频来丰富自定义搜索体验。 与 Web 结果类似，自定义搜索支持在实例的网站列表中搜索视频。 可以使用必应自定义视频搜索 API 或通过托管 UI 功能获取视频。 使用托管 UI 功能简单易行，并建议用于在短时间内启动和运行搜索体验。 有关配置托管 UI 以包含视频的信息，请参阅[配置托管 UI 体验](hosted-ui.md)。

如果想要更好地控制搜索结果的显示，可以使用必应自定义视频搜索 API。 由于调用该 API 与调用必应视频搜索 API 相似，因此请查看[必应视频搜索](../bing-video-search/overview.md)获取调用该 API 的示例。 但在执行此操作之前，应自行熟悉[自定义视频搜索 API 参考](/rest/api/cognitiveservices-bingsearch/bing-custom-videos-api-v7-reference)内容。 主要区别在于支持的查询参数（必须包含 customConfig 查询参数）和向其发送请求的终结点。

<!--
## Next steps

[Call your custom view](search-your-custom-view.md)
-->