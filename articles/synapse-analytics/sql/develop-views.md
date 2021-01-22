---
title: 使用 SQL 池的 t-sql 视图
description: 有关在 Azure Synapse 分析中使用 T-sql 视图和开发专用 SQL 池和无服务器 SQL 池的解决方案的技巧。
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: de04be2495c6e81e9c5f8d32f9d876b49482c5fe
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2021
ms.locfileid: "98678366"
---
# <a name="t-sql-views-with-dedicated-sql-pool-and-serverless-sql-pool-in-azure-synapse-analytics"></a>Azure Synapse Analytics 中具有专用 SQL 池和无服务器 SQL 池的 t-sql 视图

在本文中，你将了解有关在 Azure Synapse 分析中使用 T-sql 视图和开发专用 SQL 池和无服务器 SQL 池的解决方案的技巧。

## <a name="why-use-views"></a>为什么使用视图

可以通过多种不同的方式使用视图来提升解决方案的质量。  本文重点介绍几个示例，说明如何使用视图来丰富解决方案，并包括需要考虑的限制。

### <a name="sql-pool---create-view"></a>SQL 池-创建视图

> [!NOTE]
> 本文未讨论 CREATE VIEW 的语法。 有关详细信息，请参阅 [CREATE VIEW](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) 文档。

## <a name="architectural-abstraction"></a>体系结构摘要

常见的应用程序模式是使用 [CREATE TABLE 作为 SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) (CTAS) 重新创建表，在加载数据时，该模式后面跟有对象重命名模式。

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

请记住，此方法可能导致表在用户的视图中出现和消失，并提示 "表不存在" 错误消息。 使用视图可为用户提供一致的呈现层，同时对基础对象重命名。

用户可以借助视图来访问数据，这样就不需要看到基础表。 除了具有一致的用户体验外，此层还确保分析设计人员可以改进数据模型。 改进基础表的能力意味着，设计人员可以使用 CTAS 在数据加载过程中最大程度地提高性能。

## <a name="performance-optimization"></a>性能优化

视图还可用于强制表之间的性能优化联接。 例如，视图可以合并冗余分布键作为联接条件的一部分，以便最大程度地减少数据移动。

使用 T-sql 视图的另一个好处是强制执行特定查询或联接提示。 因此，views 功能可确保始终以最佳方式执行联接。 你将不需要用户记住正确的联接构造。

## <a name="limitations"></a>限制

Synapse SQL 中的视图仅存储为元数据。 因此，无法使用以下选项：

* 没有架构绑定选项
* 无法通过视图更新基表
* 无法基于临时表创建视图
* 不支持 EXPAND/NOEXPAND 提示
* Synapse SQL 中没有索引视图

## <a name="next-steps"></a>后续步骤

有关更多开发技巧，请参阅 [Synapse SQL 开发概述](develop-overview.md)。



