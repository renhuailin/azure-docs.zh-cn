---
title: Graph 搜索查询语法
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习设计器中的搜索查询语法在管道图中搜索节点。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/24/2021
ms.openlocfilehash: 74cf0b897529e8bb198b6f82a57e187662a4a285
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259212"
---
# <a name="graph-search-query-syntax"></a>Graph 搜索查询语法

本文介绍 Azure 机器学习中的图形搜索功能。 

使用图形搜索可以在调试或生成管道时快速导航节点。 可以在工具栏的输入框中，或在左侧面板中的“搜索”选项卡下键入关键字或查询来触发搜索。 所有匹配的结果都将在画布中以黄色突出显示，如果你在左侧面板中选择一个结果，画布中的节点将以红色突出显示。

![显示示例图形搜索体验的屏幕截图](media/search/graph-search-0322.png)

图形搜索支持针对节点名称和注释进行全文关键字搜索。 此外，你也可以针对节点属性（如 runStatus、duration、computeTarget）进行筛选。 关键字搜索基于 Lucene 查询。 完整的搜索查询如下所示：  

**[[lucene 查询] | [筛选器查询]]** 

你可以使用 Lucene 查询或筛选器查询。 若要同时使用这两种查询，请使用 | 分隔符。 筛选器查询的语法比 Lucene 查询的语法更严格。 因此，如果可以将客户输入分析为这两种查询，将会应用筛选器查询。

例如，`data OR model | compute in {cpucluster}`，这是为了搜索名称或注释包含 `data` 或 `model` 且 compute 为 cpucluster 的节点。
 

## <a name="lucene-query"></a>Lucene 查询

图形搜索使用 Lucene 简单查询作为针对节点“名称”和“注释”的全文搜索语法。 支持以下 Lucene 运算符：

 
- 和/或
- 使用 ?  和 \* 运算符进行通配符匹配。

### <a name="examples"></a>示例

- 简单搜索：`JSON Data`

- AND/OR：`JSON AND Validation`

- 多个 AND/OR：`(JSON AND Validation) OR (TSV AND Training)`

 
- 通配符匹配： 
    - `machi?e learning`
    - `mach*ing`
 
>[!NOTE]
> Lucene 查询的开头不能使用“*”字符。

##  <a name="filter-query"></a>筛选器查询

 
筛选器查询使用以下模式：
 
`**[key1] [operator1] [value1]; [key2] [operator1] [value2];**`

 
可以使用以下节点属性作为关键字：

- runStatus
- 计算
- duration
- reuse
- 发布
- 标记

并使用以下运算符：

- 大于或等于：`>=`
- 小于或等于：`<=`
- 大于：`>`
- 小于：`<`
- 等于：`==`
- 包含：`=`
- 不等于：`!=`
- 位于：`in`

 
 

### <a name="example"></a>示例

- duration > 100;
- status in { Failed,NotStarted}
- compute in {gpu-cluster}; runStatus in {Completed}

## <a name="technical-notes"></a>技术说明

- 多个筛选器之间的关系是“AND”
- 如果选择了 `>=,  >,  <, or  <=`，值将会自动转换为数值类型。 否则，字符串类型会用于比较。
- 对于所有字符串类型值，比较时不区分大小写。
- 运算符“In”需要集合作为值，集合语法为 `{name1, name2, name3}`
- 关键字之间的空格将会被忽略