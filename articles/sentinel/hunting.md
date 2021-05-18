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
ms.date: 09/10/2019
ms.author: yelevin
ms.openlocfilehash: b88185a09e69b1c7bd6c6c58b8e22190b4a2e394
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "100576767"
---
# <a name="hunt-for-threats-with-azure-sentinel"></a>使用 Azure Sentinel 搜寻威胁

如果你是希望主动查找安全威胁的调查者，则 Azure Sentinel 强大的搜寻搜索和查询工具可在组织数据源间搜寻安全威胁。 但是系统和安全设备会生成可能难以分析和筛选为有意义事件的海量数据。 为了帮助安全分析师主动查找安全应用未检测到的新异常，Azure Sentinel 的内置搜寻式查询可引导你提出正确的问题，以便在网络上已有的数据中发现问题。 

例如，一个内置查询可提供有关在基础结构上运行的最罕见进程的数据 - 你不希望在每次运行它们时都出现警报，它们可能完全没有恶意，但你可能有时要查看查询以了解是否存在任何异常情况。 



借助 Azure Sentinel 搜寻，可以利用以下功能：

- 内置查询：为了使你开始使用，起始页提供预加载的查询示例，旨在帮助你入门并熟悉表和查询语言。 这些内置搜寻式查询是由 Microsoft 安全研究人员持续努力开发出来的，他们添加了新查询并微调了现有查询，目的是为你提供一个用于查找新检测结果的入口点，并确定从何处开始搜寻新攻击的开始。 

- 使用 IntelliSense 的强大查询语言：以查询语言为基础而构建，可以在你需要使搜寻更进一步时为你提供灵活性。

- 创建自己的书签：在搜寻过程中，你可能会遇到看似异常或可疑的匹配或结果、仪表板或活动。 若要标记这些项以便以后可以返回到它们，请使用书签功能。 书签使你可以保存项以供将来用于创建要进行调查的事件。 有关书签的详细信息，请参阅[在搜寻中使用书签](hunting.md)。
- 使用笔记本自动进行调查：笔记本类似于分步剧本，可以构建它们以演练调查和搜寻的步骤。  笔记本会将所有搜寻步骤都封装可重复使用的剧本中，该剧本可以与组织中的其他人共享。 
- 查询存储的数据：可在表中访问数据以进行查询。 例如，可以查询进程创建、DNS 事件和许多其他事件类型。

- 社区链接：利用更大社区的强大功能查找其他查询和数据源。
 
## <a name="get-started-hunting"></a>开始搜寻

1. 在 Azure Sentinel 门户中，单击“搜寻”。
  ![Azure Sentinel 开始搜寻](media/tutorial-hunting/hunting-start.png)

2. 打开“搜寻”页时，所有搜寻式查询都显示在单个表中。 此表列出了由 Microsoft 安全分析师团队编写的所有查询，以及你创建或修改的任何其他查询。 每个查询都提供它搜寻的内容以及它在哪种类型的数据上运行。 这些模板按各种策略分组 - 右侧的图标对威胁类型进行分类，例如初始访问、暂留和外泄。 可以使用任何字段筛选这些搜寻式查询模板。 可以将任何查询保存到收藏夹。 通过将查询保存到收藏夹，每次访问“搜寻”页时都会自动运行查询。 可以创建自己的搜寻式查询或是克隆并自定义现有搜寻式查询模板。 
 
2. 在搜寻式查询详细信息页中单击“运行查询”，无需离开搜寻页即可运行任何查询。  匹配项的数量会显示在表中。 查看搜寻式查询及其匹配项的列表。 检查终止链中与匹配项关联的阶段。

3. 在查询详细信息窗格中快速查看基础查询，或单击“查看查询结果”以在 Log Analytics 中打开查询。 在底部，查看查询的匹配项。

4.    单击行并选择“添加书签”以添加要调查的行 - 可以对看似可疑的任何内容执行此操作。 

5. 然后，返回到主“搜寻”页并单击“书签”选项卡以查看所有可疑活动 。 

6. 选择一个书签，然后单击“调查”以打开调查体验。 可以筛选书签。 例如，如果你在调查一个市场活动，则可以为该市场活动创建标记，然后基于该市场活动筛选所有书签。

1. 发现哪个搜寻式查询可以提供有关潜在攻击的宝贵见解后，还可以基于该查询创建自定义检测规则，并将这些见解作为警报传达给安全事件响应者。

 

## <a name="query-language"></a>查询语言 

Azure Sentinel 中的搜寻基于 Kusto 查询语言。 有关查询语言和受支持运算符的详细信息，请参阅[查询语言参考](../azure-monitor/logs/get-started-queries.md)。

## <a name="public-hunting-query-github-repository"></a>公共搜寻式查询 GitHub 存储库

查看[搜寻式查询存储库](https://github.com/Azure/Orion)。 提供并使用客户共享的示例查询。

 

## <a name="sample-query"></a>示例查询

典型查询以表名开头，后跟一系列由  分隔的\|运算符。

在上面的示例中，从表名 SecurityEvent 开始，并根据需要添加以管道字符分隔的元素。

1. 定义一个时间筛选器，以便仅查看前七天的记录。

2. 在查询中添加筛选器，以便仅显示事件 ID 4688。

3. 在命令行上向查询中添加筛选器，以便仅包含 cscript.exe 的实例。

4. 仅投影有兴趣探索的列，并将结果限制为 1000 个，然后单击“运行查询”。
5. 单击绿色三角形并运行查询。 可以测试查询，并运行它以查找异常行为。

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

   ![保存查询](./media/tutorial-hunting/save-query.png)

创建新的搜寻式查询：

1. 单击“新建查询”，然后选择“保存” 。
2. 填写所有空白字段并选择“保存”。

   ![新建查询](./media/tutorial-hunting/new-query.png)

克隆并修改现有搜寻式查询：

1. 在表中选择要修改的搜寻式查询。
2. 在要修改的查询行中选择省略号 (...)，然后选择“克隆查询”。

   ![克隆查询](./media/tutorial-hunting/clone-query.png)
 

3. 修改查询，然后选择“创建”。

   ![自定义查询](./media/tutorial-hunting/custom-query.png)

## <a name="next-steps"></a>后续步骤
本文介绍了如何使用 Azure Sentinel 运行搜寻调查。 要详细了解 Azure Sentinel，请参阅以下文章：


- [使用笔记本运行自动搜寻活动](notebooks.md)
- [在搜寻时使用书签保存感兴趣的信息](bookmarks.md)