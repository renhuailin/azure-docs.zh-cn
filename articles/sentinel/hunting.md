---
title: Azure Sentinel 中的搜寻功能 | Microsoft Docs
description: 使用 Azure Sentinel 的内置搜寻式查询来引导你提出正确的问题，以发现数据中的问题。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 6aa9dd27-6506-49c5-8e97-cc1aebecee87
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/27/2021
ms.author: yelevin
ms.openlocfilehash: a8bf6ec24a67327d715b4a5d09b1d0d633b980c5
ms.sourcegitcommit: ce9178647b9668bd7e7a6b8d3aeffa827f854151
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2021
ms.locfileid: "109810337"
---
# <a name="hunt-for-threats-with-azure-sentinel"></a>使用 Azure Sentinel 搜寻威胁

> [!IMPORTANT]
>
> - “搜寻”仪表板的升级目前以预览版提供 。 与此项升级相关的以下各项将标记为“（预览版）”。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)，了解适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

作为安全分析师和调查员，你希望在查找安全威胁方面具有前瞻性，但各种系统和安全设备生成了海量数据，导致难以分析和筛选有意义的事件。 Azure Sentinel 提供强大的搜寻和查询工具，用于在组织的各个数据源中搜寻安全威胁。 为了帮助安全分析师主动找出未由安全应用甚至计划分析规则检测到的新异常，Azure Sentinel 的内置搜寻式查询可引导你提出正确的问题，以便在你的网络上已有的数据中发现问题。 

例如，有一个内置查询可提供有关在基础结构上运行的最不常见进程的数据。 你不希望在每次运行这些进程时都出现警报 - 它们可能完全没有恶意 - 但你可能偶尔想要查看查询以了解是否存在任何异常情况。 

借助 Azure Sentinel 搜寻，可以利用以下功能：

- 内置查询：可从 Azure Sentinel 导航菜单访问的搜寻主页提供现成的查询示例，旨在帮助你入门并熟悉表和查询语言。 这些内置搜寻式查询是由 Microsoft 安全研究人员持续努力开发出来的，他们添加了新查询并微调了现有查询，目的是为你提供一个用于查找新检测结果的入口点，并确定从何处开始搜寻新攻击的起始点。 

- 使用 IntelliSense 的强大查询语言：搜寻式查询是以 [Kusto查询语言 (KQL)](/azure/data-explorer/kusto/query/) 生成的。这种查询语言提供所需的强大功能和灵活性，可让你进一步提升搜寻的覆盖范围。 它是分析规则中的查询使用的语言，也是 Azure Sentinel 中别处的查询使用的语言。

- “搜寻”仪表板（预览版）：通过主页的此项升级，只需单击一下鼠标就能运行所有查询或选定的一部分查询。 通过查看 24 小时内的结果计数、峰值或结果计数的变化，确定要从何处开始搜寻。 还可以按收藏夹、数据源、MITRE ATT&CK 策略或方法、结果或结果差异进行排序和筛选。 查看尚未连接所需数据源的查询，并获取有关如何启用这些查询的建议。

- 创建自己的书签：在搜寻过程中，你可能会遇到看上去异常或可疑的查询结果。 可为这些项“添加书签”- 将其保存并放在一边，以便将来可以回头参考。 可以使用添加了书签的项来创建或扩充事件以进行调查。 有关书签的详细信息，请参阅[在搜寻中使用书签](bookmarks.md)。

- 使用笔记本为调查提供支持：笔记本提供一种自带内核的虚拟沙盒环境。 可以将笔记本与机器学习、可视化和数据分析配合使用来增强搜寻和调查。 可以在笔记本中执行完整调查，封装原始数据、对其运行的代码、结果及其可视化效果，然后保存所有内容，以便可与组织中的其他人共享并让他们重复使用。 

- 查询存储的数据：可在表中访问数据以进行查询。 例如，可以查询进程创建、DNS 事件和许多其他事件类型。

- 社区链接：利用更大社区的力量查找其他查询和数据源。
 
## <a name="get-started-hunting"></a>开始搜寻

在 Azure Sentinel 门户中，单击“搜寻”。

   :::image type="content" source="media/hunting/hunting-start.png" alt-text="Azure Sentinel 开始搜寻" lightbox="media/hunting/hunting-start.png":::

- 打开“搜寻”页时，所有搜寻式查询都显示在单个表中。 此表列出了由 Microsoft 安全分析师团队编写的所有查询，以及你创建或修改的任何其他查询。 每个查询都提供它搜寻的内容以及它在哪种类型的数据上运行。 这些模板按各种策略分组 - 右侧的图标对威胁类型进行分类，例如初始访问、暂留和外泄。

- （预览版）若要查看查询如何应用到环境，请单击“运行所有查询(预览版)”按钮，或者使用每行左侧的复选框选择一部分查询，然后选择“运行选定的查询(预览版)”按钮 。 执行查询可能需要几秒到几分钟时间，具体取决于选择的查询数目、时间范围以及要查询的数据量。

- （预览版）运行完查询后，可以使用“结果”筛选器查看哪些查询返回了结果。 然后，可以排序以查看哪些查询的结果最多或最少。 还可以通过在“结果”筛选器中选择“不适用”来查看哪些查询在环境中处于非活动状态。 将鼠标悬停在“不适用”旁边的信息图标 (i) 上可以查看需要哪些数据源才能使此查询处于活动状态。

- （预览版）可以根据“结果差异”进行排序或筛选以识别数据峰值。 这会将过去 24 小时的结果与前 24-48 小时的结果进行比较，以方便查看较大的数据量差异。

- （预览版）表顶部的 MITRE ATT&CK 策略栏中列出了映射到每个 MITRE ATT&CK 策略的查询数。 策略栏会根据当前应用的筛选器集动态更新。 通过这种方式，可以方便地在按照给定结果计数、较大结果差异、“不适用”结果或任何其他筛选器集进行筛选时查看显示的 MITRE ATT&CK 策略。

- （预览版）查询还可映射到 MITRE ATT&CK 方法。 可以使用“方法”筛选器按照 MITRE ATT&CK 方法进行筛选或排序。 打开查询后，你将能够单击方法来查看 MITRE ATT&CK 方法的说明。

- 可以将任何查询保存到收藏夹。 每次访问“搜寻”页时，都会自动运行已保存到收藏夹的查询。 可以创建自己的搜寻式查询或是克隆并自定义现有搜寻式查询模板。
 
- 在搜寻式查询详细信息页中单击“运行查询”后，无需离开搜寻页即可运行任何查询。 匹配项数将显示在表中的“结果”列内。 查看搜寻式查询及其匹配项的列表。

- 可以在查询详细信息窗格中快速查看基础查询。 可以单击“查看查询结果”链接（在查询窗口下方）或“查看结果”按钮（在窗格底部）来查看结果 。 查询将在“日志”(Log Analytics) 边栏选项卡中打开。在查询下方，可以查看查询的匹配项。

- 若要保留 Log Analytics 中的查询发现的可疑或有用结果，请勾选你要保留的行对应的复选框，然后选择“添加书签”。 这会针对标记的每个行创建一条记录（一个书签），其中包含行结果、创建了结果的查询，以及用于提取用户、主机和 IP 地址的实体映射。 你可以在每个书签中添加自己的标记（参阅下文）和备注。

- 可以通过在“搜寻”主页中单击“书签”选项卡来查看所有添加了书签的结果 。 可将标记添加到书签，以将这些书签分类，从而方便筛选。 例如，如果你正在调查某种攻击活动，可为该活动创建一个标记，将该标记应用到所有相关书签，然后根据该活动筛选所有书签。

- 可以调查单个添加了书签的结果，方法是选择该书签，然后在详细信息窗格中单击“调查”以打开调查体验。 还可以从一个或多个书签创建事件，或者向现有事件添加一个或多个书签，方法是勾选所需书签左侧的复选框，然后从靠近屏幕顶部的“事件操作”下拉菜单中选择“创建新事件”或“添加到现有事件”  。 然后，可以像对待任何其他事件一样会审和调查该事件。

- 发现或创建一个可以提供有关潜在攻击的宝贵见解的搜寻式查询后，可以基于该查询创建自定义检测规则，并将这些见解作为警报传达给安全事件响应者。 在 Log Analytics 中查看该查询的结果（参阅上文），然后单击窗格顶部的“新建警报规则”按钮并选择“创建 Azure Sentinel 警报” 。 此时会打开“分析规则向导”。 根据[教程：创建自定义分析规则以检测威胁](tutorial-detect-threats-custom.md)中所述完成所需的步骤。

## <a name="query-language"></a>查询语言 

Azure Sentinel 中的搜寻基于 Kusto 查询语言。 有关查询语言和受支持运算符的详细信息，请参阅[查询语言参考](../azure-monitor/logs/get-started-queries.md)。

## <a name="public-hunting-query-github-repository"></a>公共搜寻式查询 GitHub 存储库

查看[搜寻式查询存储库](https://github.com/Azure/Azure-Sentinel/tree/master/Hunting%20Queries)。 提供并使用客户共享的示例查询。

 ## <a name="sample-query"></a>示例查询

典型查询以表名开头，后跟一系列由  分隔的\|运算符。

在上面的示例中，从表名 SecurityEvent 开始，并根据需要添加以管道字符分隔的元素。

1. 定义一个时间筛选器，以便仅查看前七天的记录。

1. 在查询中添加筛选器，以便仅显示事件 ID 4688。

1. 在命令行上向查询中添加筛选器，以便仅包含 cscript.exe 的实例。

1. 仅投影有兴趣探索的列，并将结果限制为 1000 个，然后单击“运行查询”。

1. 单击绿色三角形并运行查询。 可以测试查询，并运行它以查找异常行为。

## <a name="useful-operators"></a>有用的运算符

查询语言功能强大并且具有许多可用运算符，下面列出了一些有用的运算符：

where - 筛选表，获取满足谓词的行子集。

summarize - 生成可聚合输入表内容的表。

join - 通过匹配每个表中指定列的值，合并两个表的行以组成新表。

count - 返回输入记录集中的记录数。

top - 返回按指定列排序的前 N 个记录。

limit - 返回指定的行数。

project - 选择要包含、重命名或删除的列，并插入新的计算列。

extend - 创建计算列并将其追加到结果集中。

makeset - 返回表达式在组中所获取非重复值集的动态 (JSON) 数组

find - 在一组表中查找与谓词相匹配的行。

## <a name="save-a-query"></a>保存查询

可以创建或修改查询并将它保存为自己的查询，或是将它与同一租户中的用户共享。

:::image type="content" source="./media/hunting/save-query.png" alt-text="保存查询" lightbox="./media/hunting/save-query.png":::

### <a name="create-a-new-hunting-query"></a>创建新的搜寻式查询

1. 单击“新建查询”。

1. 填写所有空白字段并选择“创建”。

    :::image type="content" source="./media/hunting/new-query.png" alt-text="新查询" lightbox="./media/hunting/new-query.png":::

### <a name="clone-and-modify-an-existing-hunting-query"></a>克隆和修改现有搜寻式查询

1. 在表中选择要修改的搜寻式查询。

1. 在要修改的查询行中选择省略号 (...)，然后选择“克隆查询”。

    :::image type="content" source="./media/hunting/clone-query.png" alt-text="克隆查询" lightbox="./media/hunting/clone-query.png":::

1. 修改查询，然后选择“创建”。

    :::image type="content" source="./media/hunting/custom-query.png" alt-text="自定义查询" lightbox="./media/hunting/custom-query.png":::

## <a name="next-steps"></a>后续步骤

本文介绍了如何使用 Azure Sentinel 运行搜寻调查。 要详细了解 Azure Sentinel，请参阅以下文章：

- [使用笔记本运行自动搜寻活动](notebooks.md)
- [在搜寻时使用书签保存感兴趣的信息](bookmarks.md)
