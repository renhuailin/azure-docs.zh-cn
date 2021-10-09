---
title: 了解 Azure Purview 中的搜索功能
description: 本文介绍 Azure Purview 如何通过搜索功能实现数据发现。
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 09/27/2021
ms.openlocfilehash: 6bc9029a4b75206b5de99622729415b1aa36d4a2
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129207519"
---
# <a name="understand-search-features-in-azure-purview"></a>了解 Azure Purview 中的搜索功能

本文概述了 Azure Purview 中的搜索体验。 搜索是 Purview 的一项核心平台功能，它支持组织中的数据发现和数据使用管理体验。

## <a name="search"></a>搜索

Purview 搜索体验由托管搜索索引提供支持。 向 Purview 注册数据源后，数据的元数据由搜索服务进行索引以便于发现。 索引提供搜索相关性功能，并通过查询数百万个元数据资产来完成搜索请求。 搜索可帮助你发现、了解和使用数据来从中获得最大价值。

Purview 中的搜索体验分为 3 个阶段：

1. 搜索框显示包含最近使用的关键字和资产的历史记录。
1. 开始键入击键时，搜索会建议匹配的关键字和资产。 
1. 搜索结果页面上显示与输入的关键字匹配的资产。

## <a name="reduce-the-time-to-discover-data"></a>更快发现数据

对数据使用者来说，数据发现是数据分析或数据管理工作负载的第一步。 数据发现可能很耗时，因为你可能不知道在哪里查找你需要的数据。 甚至在找到数据后，你也可能不确定是否能信任该数据以及是否能与其建立依赖关系。 

Azure Purview 中的搜索目标是提供手势来快速找到重要数据，从而加快数据发现的过程。

## <a name="recent-search-and-suggestions"></a>最近使用的搜索和建议

很多时候，你可能在同时处理多个项目。 为了更轻松地恢复之前的项目，Purview 搜索让你能够查看最近使用的搜索关键字和建议。 此外，你可从搜索框下拉列表中选择“全部查看”来管理近期搜索历史记录。

## <a name="filters"></a>筛选器

筛选器（也称为“Facet”）旨在对搜索进行补充。 筛选器显示在搜索结果页中。 搜索结果页中的筛选器包括分类、敏感度标签、数据源和所有者。 用户可在筛选器中选择特定值来仅查看匹配的数据资产，并且将搜索结果限制为匹配的资产。

## <a name="hit-highlighting"></a>突出显示

搜索结果页中的匹配关键字突出显示，以便更轻松地确定搜索为何返回某项数据资产。 关键字匹配可出现在多个字段中，例如数据资产名称、说明和所有者。

即使启用搜索词突出显示，搜索中包括某项数据资产的原因也并非总是那么明确。 默认会搜索所有属性。 因此，返回某项数据资产的原因可能是列级别属性上存在匹配。 而且由于多名用户可使用自己的分类和说明对数据资产进行批注，因此并非所有元数据都显示在搜索结果列表中。

## <a name="sort"></a>排序

用户有两个选项来对搜索结果进行排序。 它们可按资产名称或默认搜索相关性进行排序。

## <a name="search-relevance"></a>搜索相关性

相关性是搜索结果页中的默认排序顺序。 搜索相关性会查找包含搜索关键字的文档（全部或部分）。 包含搜索关键字的许多实例的资产排名更高。 此外，自定义评分机制会不断调整来提高搜索相关性。

## <a name="next-steps"></a>后续步骤

* [快速入门：在 Azure 门户中创建 Azure Purview 帐户](create-catalog-portal.md)
* [快速入门：使用 Azure PowerShell/Azure CLI 创建 Azure Purview 帐户](create-catalog-powershell.md)
* [使用 Purview Studio](use-purview-studio.md)
