---
title: 映射数据流转换概述
titleSuffix: Azure Data Factory & Azure Synapse
description: 映射数据流中可用的不同转换的概述
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.custom: synapse
ms.topic: conceptual
ms.date: 10/07/2021
ms.openlocfilehash: 56a6a521eb648a1ad49baf2bf206ef84aa084731
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129714113"
---
# <a name="mapping-data-flow-transformation-overview"></a>映射数据流转换概述

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)] 

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

下面是映射数据流中当前受支持的转换的列表。 你可以单击每个转换，了解其配置详细信息。

| 名称 | 类别 | 说明 |
| ---- | -------- | ----------- |
| [聚合](data-flow-aggregate.md) | 架构修饰符 | 定义不同类型的聚合，如 SUM、MIN、MAX 和 COUNT，并按现有或计算列进行分组。 | 
| [更改行](data-flow-alter-row.md) | 行修饰符 | 对行设置插入、删除、更新和更新插入策略。 |
| [有条件拆分](data-flow-conditional-split.md) | 多个输入/输出 | 根据匹配条件将数据行路由到不同的流。 |
| [派生列](data-flow-derived-column.md) | 架构修饰符 | 使用数据流表达式语言生成新列或修改现有字段。 | 
| [Exists](data-flow-exists.md) | 多个输入/输出 | 检查数据是否存在于另一个源或流中。 | 
| [Filter](data-flow-filter.md) | 行修饰符 | 基于条件筛选行。 |
| [平展](data-flow-flatten.md) | 架构修饰符 |  获取层次结构（如 JSON）中的数组值，并将其展开到各个行。 |
| [Join](data-flow-join.md) | 多个输入/输出 |  合并两个源或流中的数据。 |
| [查找](data-flow-lookup.md) | 多个输入/输出 | 引用来自其他源的数据。 |
| [新建分支](data-flow-new-branch.md) | 多个输入/输出 | 对同一数据流应用多组操作和转换。 |
| [Parse](data-flow-new-branch.md) | 格式化程序 | 分析数据流中的文本列（是 JSON 字符串、带分隔符的文本或 XML 格式文本）。 |
| [透视表](data-flow-pivot.md) | 架构修饰符 | 一种聚合，其中一个或多个分组列将其不同的行值转换到各个列中。 |
| [Rank](data-flow-rank.md) | 架构修饰符 | 根据排序条件生成已排序的排名 |
| [Select](data-flow-select.md) | 架构修饰符 | 别名列和流名称，以及删除或重新排序列 |
| [接收器](data-flow-sink.md) | - | 数据的最终目标 |
| [Sort](data-flow-sort.md) | 行修饰符 | 对当前数据流中的传入行进行排序 |
| [Source](data-flow-source.md) | - | 数据流的数据源 |
| [字符串化](data-flow-stringify.md) | - | 将复杂类型转换为纯字符串 |
| [代理键](data-flow-surrogate-key.md) | 架构修饰符 | 添加增量非业务任意密钥值 |
| [联合](data-flow-union.md) | 多个输入/输出 | 垂直合并多个数据流 |
| [逆透视](data-flow-unpivot.md) | 架构修饰符 | 将列透视为行值 |
| [窗口](data-flow-window.md) | 架构修饰符 |  定义数据流中基于窗口的列聚合。 |
| [Parse](data-flow-parse.md) | 架构修饰符 |  将列数据分析为 Json 或分隔文本 |
