---
title: 在 Azure Monitor 日志分析中使用查询
description: Azure Monitor 日志分析中的日志查询的概述，其中包括不同类型的查询和可以使用的示例查询。
ms.subservice: logs
ms.topic: article
author: bwren
ms.author: bwren
ms.date: 05/20/2021
ms.openlocfilehash: 004196117512282ee960ed275dd970b472cde918
ms.sourcegitcommit: c2f0d789f971e11205df9b4b4647816da6856f5b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122662315"
---
# <a name="using-queries-in-azure-monitor-log-analytics"></a>在 Azure Monitor 日志分析中使用查询
打开 Log Analytics 时，可以访问现有日志查询。 可以在不加修改的情况下运行这些查询，也可以将它们用作自己查询的起点。 可用查询包括由 Azure Monitor 提供的示例和由组织保存的查询。 本文介绍可用的查询，以及如何发现和使用它们。


## <a name="queries-interface"></a>查询接口
从查询接口中选择查询，这些查询可从 Log Analytics 中的两个不同位置获得。

### <a name="queries-dialog"></a>“查询”对话框

打开 Log Analytics 时，会自动显示“查询”对话框。 如果不希望自动显示此对话框，请关闭“始终显示查询”开关。

![“查询”屏幕](media/queries/query-start.png)


每个查询都由一张卡片表示。 你可以快速浏览查询来查找所需内容。 可以直接从对话框中运行查询，也可以选择将其加载到查询编辑器，以便进行修改。

还可以通过单击屏幕右上角的“查询”来访问它。

[![“查询”按钮](media/queries/queries-button.png)](media/queries/queries-button.png#lightbox)

### <a name="query-sidebar"></a>“查询”边栏

可以从 Log Analytics 左侧边栏中的“查询”窗格访问与对话框体验完全相同的功能。 可以将鼠标指针悬停在查询名称上来获取查询说明和其他功能。

[![“查询”边栏](media/queries/query-sidebar.png)](media/queries/query-sidebar.png#lightbox)

## <a name="finding-and-filtering-queries"></a>查找和筛选查询

本部分中的选项同时存在于对话框和边栏查询体验中，但用户界面略有不同。  


### <a name="group-by"></a>Group by

单击“分组依据”下拉列表，更改查询的分组。 分组值还用作活动目录。 单击屏幕左侧的某个值会将查询视图向右滚动到所单击的项。 如果组织已创建包含标记的查询包，则自定义标记将包含在此列表中。

[![“示例查询”屏幕中的分组依据](media/queries/example-query-groupby.png)](media/queries/example-query-groupby.png#lightbox)



### <a name="filter"></a>筛选器

你还可以根据前面提到的分组依据值筛选查询。 在“示例查询”对话框中，可以在顶部找到筛选器。

[![“示例查询”屏幕上的筛选器](media/queries/example-query-filter.png)](media/queries/example-query-filter.png#lightbox)

### <a name="combining-group-by-and-filter"></a>组合使用分组依据和筛选器

筛选器和分组依据功能设计为协同工作。 它们在如何排列查询方面提供了灵活性。 例如，如果使用具有多个资源的资源组，你可能希望筛选到特定的资源类型，并按主题排列结果查询。

## <a name="query-properties"></a>查询属性
每个查询都有多个属性，可帮助你进行分组和查找。 这些属性可用于排序和筛选，并且你可在[保存自己的查询](save-query.md)时定义其中几个属性。 属性类型为：

- **资源类型** - Azure 中定义的资源，例如虚拟机。 请参阅 [Azure Monitor 表参考](/azure/azure-monitor/reference/tables/tables-resourcetype)，以了解从 Azure Monitor 日志/Log Analytics 表到资源类型的完整映射。  
- **类别** - 某个信息类型，例如“安全性”或“审核”。  类别与在“表”边栏窗格中定义的类别完全相同。 有关类别的完整列表，请参阅 [Azure Monitor 表参考](/azure/azure-monitor/reference/tables/tables-category)。  
- **解决方案** - 与查询关联的 Azure Monitor 解决方案
- **主题** - 示例查询的主题，例如“活动日志”或“应用日志”。  对示例查询而言，主题属性具有唯一性，可能会因资源类型而异。
- - 查询类型 - 定义查询的类型。 查询类型可以是示例查询、查询包查询或旧版查询
- **标签** - [保存自己的查询](save-query.md)时可以定义和分配的自定义标签。
- **标记** - 可以在 [创建查询包](query-packs.md)时定义的自定义属性。 标记允许组织创建自己的分类来组织查询。

## <a name="favorites"></a>收藏夹
你可以收藏经常使用的查询，以便能够更快速地进行访问。 单击查询旁的星号将其添加到“收藏夹”。 从查询界面中的“收藏夹”选项查看你最喜欢的查询。

## <a name="types-of-queries"></a>查询类型
查询界面用以下查询类型填充：

**示例查询：** 示例查询可以提供对资源的即时了解，并提供一种好方法来开始学习和使用 KQL，从而缩短开始使用 Log Analytics 所花费的时间。 我们收集并特选了 500 多个示例查询（这些查询设计用来提供即时值），这些示例查询的数量还在不断增长。

**查询包：** [查询包](query-packs.md)包含日志查询的集合，其中包括你自己保存的查询。 这包括[默认查询包](query-packs.md#default-query-pack)和你的组织可能已在订阅中创建的任何其他查询包。

**旧的查询：** 以前保存在查询资源管理器体验中的日志查询以及安装在工作区中的查询 Azure 解决方案。 它们将列在“旧的查询”下的“查询”对话框中。
>[!TIP]
> 旧版查询只能在 Log Analytics 工作区中使用。

## <a name="effect-of-query-scope"></a>查询范围的影响
打开 Log Analytics 时可用的查询由当前[查询范围](scope.md)确定。

- 对于工作区 – 所有示例查询和查询包中的查询。 工作区中的旧查询。
- 对于单个资源 – 示例查询和来自资源类型的查询包的查询。 
- 对于资源组 - 示例查询和来自资源组中的资源类型的查询包的查询。 

> [!TIP]
> 范围内的资源越多，门户筛选和显示“查询”对话框所需的时间就越长。


## <a name="next-steps"></a>后续步骤

[KQL 查询入门](get-started-queries.md)

