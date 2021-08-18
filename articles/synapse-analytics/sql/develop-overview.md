---
title: 用于开发 Synapse SQL 功能的资源
description: Synapse SQL 的开发概念、设计决策、建议和编程方法。
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 17df008ccc5fcada0ad58313622fc997ccfa3f77
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121860523"
---
# <a name="design-decisions-and-coding-techniques-for-synapse-sql-features-in-azure-synapse-analytics"></a>Azure Synapse Analytics 中 Synapse SQL 功能的设计决策和编程方法
本文提供了 Synapse SQL 专用 SQL 池和无服务器 SQL 池函数的资源列表。 建议的文章分为两部分：关键设计决策，以及开发和编程方法。

本文旨在帮助你针对 Azure Synapse Analytics 中的 Synapse SQL 组件制定最佳技术方案。

## <a name="key-design-decisions"></a>关键设计决策
以下文章重点介绍了 Synapse SQL 开发的概念和设计决策：

| 项目 | 专用 SQL 池 | 无服务器 SQL 池 |
| ------- | -------- | ------------- |
| [连接](connect-overview.md)                    | 是 | 是 |
| [资源类和并发](../sql-data-warehouse/resource-classes-for-workload-management.md?context=/azure/synapse-analytics/context/context) | 是    | 否 |
| [中的](develop-transactions.md)              | 是 | 否 |
| [用户定义的架构](develop-user-defined-schemas.md) | 是 | 是 |
| [表分布](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?context=/azure/synapse-analytics/context/context)                 | 是 | 否 |
| [表索引](../sql-data-warehouse/sql-data-warehouse-tables-index.md?context=/azure/synapse-analytics/context/context)                           | 是 | 否 |
| [表分区](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?context=/azure/synapse-analytics/context/context)                     | 是 | 否 |
| [统计信息](develop-tables-statistics.md)            | 是 | 是 |
| [CTAS](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?context=/azure/synapse-analytics/context/context)                                             | 是 | 否 |
| [外部表](develop-tables-external-tables.md) | 是 | 是 |
| [CETAS](develop-tables-cetas.md)                     | 是 | 是 |


## <a name="recommendations"></a>建议

以下重要文章重点介绍了有关开发的具体编程方法、技巧和建议：

| 项目 | 专用 SQL 池 | 无服务器 SQL 池 |
| ------- | -------- | ------------- |
| [存储过程](develop-stored-procedures.md)  | 是                | 是                      |
| [标签](develop-label.md)                           | 是                | 否                      |
| [视图](develop-views.md)                             | 是                | 是                     |
| [临时表](develop-tables-temporary.md)       | 是                | 是                     |
| [动态 SQL](develop-dynamic-sql.md)                 | 是                | 是                     |
| [循环](develop-loops.md)                         | 是                | 是                     |
| [Group by 选项](develop-group-by-options.md)       | 是                | 否                      |
| [变量赋值](develop-variable-assignment.md) | 是                | 是                     |

## <a name="next-steps"></a>后续步骤
有关更多参考信息，请参阅 [SQL 池 T-SQL 语句](../sql-data-warehouse/sql-data-warehouse-reference-tsql-statements.md?context=/azure/synapse-analytics/context/context)。

