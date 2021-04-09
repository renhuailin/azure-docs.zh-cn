---
title: 使用 SQL 池的 T-SQL 视图
description: 有关通过 Azure Synapse Analytics 中的专用 SQL 池和无服务器 SQL 池使用 T-SQL 视图开发解决方案的技巧。
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 9f52c3fd1284ce7e55680d051c5292361067fad9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "101674003"
---
# <a name="t-sql-views-with-dedicated-sql-pool-and-serverless-sql-pool-in-azure-synapse-analytics"></a>T-SQL 视图和 Azure Synapse Analytics 中的专用 SQL 池和无服务器 SQL 池

本文介绍有关通过 Azure Synapse Analytics 中的专用 SQL 池和无服务器 SQL 池使用 T-SQL 视图开发解决方案的技巧。

## <a name="why-use-views"></a>为何使用视图

可以通过多种不同的方式使用视图来提升解决方案的质量。  本文重点介绍几个示例，说明如何使用视图来丰富解决方案，也包括一些需要注意的限制。

### <a name="sql-pool---create-view"></a>SQL 池 - 创建视图

> [!NOTE]
> 本文未讨论 CREATE VIEW 的语法。 有关详细信息，请参阅 [CREATE VIEW](/sql/t-sql/statements/create-view-transact-sql?view=azure-sqldw-latest&preserve-view=true) 文档。

## <a name="architectural-abstraction"></a>体系结构摘要

一种常见的应用模式是，在加载数据时使用 [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true) (CTAS)，后接对象重命名模式来重建表。

以下示例向日期维度添加新的日期记录。 请注意，这里先创建了一个新表 DimDate_New，然后将它重命名以替换表的原始版本。

```sql
CREATE TABLE dbo.DimDate_New
WITH (DISTRIBUTION = ROUND_ROBIN
, CLUSTERED INDEX (DateKey ASC)
)
AS
SELECT *
FROM   dbo.DimDate  AS prod
UNION ALL
SELECT *
FROM   dbo.DimDate_stg AS stg
;

RENAME OBJECT DimDate TO DimDate_Old;
RENAME OBJECT DimDate_New TO DimDate;
```

请记住，此方法可能导致表在用户的视图中出现并消失，并提示“表不存在”错误消息。 使用视图可为用户提供一致的呈现层，同时对基础对象重命名。

用户可以借助视图来访问数据，这样就不需要看到基础表。 除了具有一致的用户体验，此层还确保分析设计人员可以改进数据模型。 能够改进基础表意味着设计人员可以使用 CTAS 来使数据加载过程中的性能最大化。

## <a name="performance-optimization"></a>性能优化

视图还可用于强化表之间的性能优化联接。 例如，视图可以合并冗余分布键作为联接条件的一部分，以便最大程度地减少数据移动。

使用 T-SQL 视图的另一个好处是强制执行特定查询或联接提示。 因此，视图功能可确保始终以最佳方式执行联接。 用户不需要记住联接的正确构造。

## <a name="limitations"></a>限制

Synapse SQL 中的视图仅存储为元数据。 因此，无法使用以下选项：

* 不提供任何架构绑定选项
* 无法通过视图更新基表
* 无法基于临时表创建视图
* 不支持 EXPAND/NOEXPAND 提示
* Synapse SQL 中没有索引视图

## <a name="next-steps"></a>后续步骤

有关更多开发技巧，请参阅 [Synapse SQL 开发概述](develop-overview.md)。



