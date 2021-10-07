---
title: 必应见解的示例 - 必应视觉搜索
titleSuffix: Azure Cognitive Services
description: 本文包含有关必应视觉搜索可能如何使用以及如何在 Bing.com 上显示图像见解的示例。
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: 51248cb5e37e6f109873340cca74c46e4af6a721
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128669657"
---
# <a name="examples-of-bing-insights-usage"></a>必应见解用法示例

> [!WARNING]
> 必应搜索 API 将从认知服务迁移到必应搜索服务。 从 2020 年 10 月 30 日开始，必应搜索的任何新实例都需按照[此处](/bing/search-apis/bing-web-search/create-bing-search-service-resource)所述的过程进行预配。
> 使用认知服务进行预配的必应搜索 API 将在未来三年或在企业协议结束前（以先发生者为准）得到支持。
> 有关迁移说明，请参阅[必应搜索服务](/bing/search-apis/bing-web-search/create-bing-search-service-resource)。

本文包含有关必应可能如何使用以及如何在 Bing.com 上显示图像见解的示例。

## <a name="pagesincluding-insight-example"></a>PagesIncluding 见解示例

下面显示指向第一个网页的链接，并允许用户展开和折叠包含图像的其他网页列表：

![包含扩展页面](./media/pages-including.PNG)

## <a name="shoppingsources-insight-example"></a>ShoppingSources 见解示例

下面介绍了必应如何显示图像中产品的购物源：

![购物源](./media/shopping-sources.PNG)

## <a name="visualsearch-insight-example"></a>VisualSearch 见解示例

下面介绍了必应如何显示视觉上相似的图像（请参阅示例中的相关图像）：

![视觉上相似的图像](./media/similar-images.PNG)

## <a name="recipes-insight-example"></a>Recipes 见解示例

以下介绍了必应如何为图像中的食物显示食谱。 此示例使用户可以知道有可用的食谱：

![包含食谱和页面](./media/recipes-pages-including.PNG)

 当用户展开列表时，提供指向食谱的链接：

![包含展开的食谱页面](./media/expanded-recipes-pages-including.PNG)

## <a name="relatedsearches-insight-example"></a>RelatedSearches 见解示例

以下介绍了必应如何显示他人所进行的图像相关搜索。 如果用户单击图像，将针对相关查询转到 Bing.com/images 搜索结果页面。

![图像的相关搜索](./media/bordered-related-searches.PNG)

## <a name="entity-insight-example"></a>Entity 见解示例

以下介绍了必应如何显示图像中实体（人物、位置或事物）的相关信息。 如果用户单击实体链接，将针对实体转到 Bing.com 搜索结果页面：

![图像中显示的实体](./media/entity.PNG)

## <a name="displaying-other-insights-that-the-user-might-explore"></a>显示用户可能浏览的其他见解

以下介绍了必应如何显示用户可能浏览的图像的相关信息。

![浏览有关图像的其他见解](./media/apple-pie-more-tags.PNG)

## <a name="bounding-boxes-and-hot-spots"></a>边框和热点

非默认标记包括标识图像（标记应用于的图像）中感兴趣区域的边框。 如果边框未标识整个图像，使用边框在图像上创建热点。 用户可以单击热点以获取与热点（或矩形）下内容相关的信息。 例如，如果图像是一个高级时装图像，结果可能包含图像中配饰（如钱包、珠宝、围巾等）的标记（和边界框）。 下面的示例显示图像中太阳镜的热点矩形：

![边框和热点](./media/click-to-search.PNG)

## <a name="next-steps"></a>后续步骤

若要了解如何使用第一个请求，请参阅快速入门：

* [C#](quickstarts/csharp.md)

* [Java](quickstarts/java.md)

* [node.js](quickstarts/nodejs.md)

* [Python](quickstarts/python.md)