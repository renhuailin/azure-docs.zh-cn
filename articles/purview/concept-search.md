---
title: '了解 Azure 监控范围中的搜索功能 (预览) '
description: 本文介绍 Azure 监控范围如何通过搜索功能实现数据发现。
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/06/2020
ms.openlocfilehash: af8ec9e0aac38240c7da92edd614892ff65712e2
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2020
ms.locfileid: "96551703"
---
# <a name="understand-search-features-in-azure-purview"></a>了解 Azure 监控范围中的搜索功能

本文概述了 Azure 监控范围中的搜索体验。 搜索是监控范围的核心平台功能，可为组织中的数据发现和数据使用调控体验作动力。

## <a name="search"></a>搜索

监控范围搜索体验由托管搜索索引提供支持。 向监控范围注册数据源后，搜索服务会为其元数据编制索引，以方便发现。 索引提供搜索关联功能，并通过查询数百万个元数据资产完成搜索请求。 搜索可帮助你发现、了解和使用数据，充分利用数据。

监控范围中的搜索体验分为三个阶段：

1. "搜索" 框显示包含最近使用的关键字和资产的历史记录。
1. 开始键入击键时，搜索将建议匹配的关键字和资产。 
1. "搜索结果" 页将显示与输入关键字匹配的资产。

## <a name="reduce-the-time-to-discover-data"></a>缩短发现数据的时间

数据发现是数据分析或数据使用者的数据管理工作负载的第一步。 数据发现可能需要很长时间，因为您可能不知道所需数据的查找位置。 即使在查找数据后，您也可能怀疑是否可以信任数据并对其进行依赖关系。 

在 Azure 监控范围中搜索的目标是通过提供手势来加快数据发现过程，快速查找重要数据。

## <a name="recent-search-and-suggestions"></a>最近的搜索和建议

很多时候，您可能会同时处理多个项目。 为了更轻松地恢复以前的项目，监控范围搜索提供了查看最近的搜索关键字和建议的功能。 此外，还可以通过在 "搜索框" 下拉框中选择 " **全部查看** " 来管理最近的搜索历史记录。

## <a name="filters"></a>筛选器

筛选器 (也称为 *facet*) 旨在补充搜索。 筛选器显示在 "搜索结果" 页中。 "搜索结果" 页中的筛选器包括分类、敏感度标签、数据源和所有者。 用户可以在筛选器中选择特定值，以仅查看匹配的数据资产，并将搜索结果限制为匹配的资产。

## <a name="hit-highlighting"></a>突出显示

"搜索结果" 页中的匹配关键字将突出显示，以便更轻松地标识搜索返回数据资产的原因。 关键字匹配可以出现在多个字段中，如数据资产名称、说明和所有者。

即使启用了命中突出显示，在搜索中也不能直观地包含数据资产。 默认情况下，会搜索所有属性。 因此，可能会由于列级属性上的匹配而返回数据资产。 而且，因为多个用户可以用其自己的分类和说明对数据资产进行批注，因此并非所有元数据都显示在搜索结果列表中。

## <a name="sort"></a>排序

用户有两个选项可对搜索结果进行排序。 它们可以按资产名称或默认搜索相关性进行排序。

## <a name="search-relevance"></a>搜索相关性

"相关性" 是 "搜索结果" 页中的默认排序顺序。 搜索关联查找 (部分或全部) 包含 search 关键字的文档。 包含搜索关键字的多个实例的资产排名更高。 此外，自定义计分机制还会不断地进行调整，以改善搜索相关性。

## <a name="next-steps"></a>后续步骤

* [快速入门：在 Azure 门户中创建 Azure 监控范围帐户](create-catalog-portal.md)
* [快速入门：使用 Azure PowerShell/Azure CLI 创建 Azure 监控范围帐户](create-catalog-powershell.md)
* [快速入门：使用监控范围 Studio](use-purview-studio.md)
