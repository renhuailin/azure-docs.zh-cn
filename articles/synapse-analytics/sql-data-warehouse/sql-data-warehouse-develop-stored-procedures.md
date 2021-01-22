---
title: 使用存储过程
description: 通过在 Azure Synapse Analytics 中实现专用 SQL 池的存储过程来开发解决方案的技巧。
services: synapse-analytics
author: MSTehrani
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/02/2019
ms.author: emtehran
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: e28eeac131c737d673cac947a3fda30239180a62
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2021
ms.locfileid: "98673580"
---
# <a name="using-stored-procedures-for-dedicated-sql-pools-in-azure-synapse-analytics"></a>对 Azure Synapse Analytics 中的专用 SQL 池使用存储过程

本文提供了通过实现存储过程来开发专用 SQL 池解决方案的技巧。

## <a name="what-to-expect"></a>期望

专用 SQL 池支持 SQL Server 中使用的许多 T-SQL 功能。 更重要的是，可使用特定的横向扩展功能将解决方案的性能最大化。

另外，还可以通过其他存在行为差异的特性和功能来维护专用 SQL 池的规模和性能。

## <a name="introducing-stored-procedures"></a>存储过程简介

存储过程很适合用于封装 SQL 代码，该代码存储在专用 SQL 池数据附近。 通过将代码封装为可管理的单元，存储过程还可帮助开发人员将其解决方案模块化，从而提高代码的可重用性。 每个存储过程还可接受参数，使其更具弹性。

专用 SQL 池提供简化且流畅的存储过程实现。 相比于 SQL Server，最大差异是存储过程不是预先编译的代码。

通常，对于数据仓库，与针对大型数据卷运行查询所用的时间相比，编译时间非常少。 更重要的是确保存储过程代码针对大型查询正确进行优化。

> [!TIP]
> 目标是要节省时数、分钟数和秒数，而不是毫秒数。 因此，可以将存储过程视为 SQL 逻辑的容器。

当专用 SQL 池执行存储过程时，SQL 语句在运行时进行解析、转换和优化。 在此过程中，每个语句都转换为分布式查询。 针对数据执行的 SQL 代码与提交的查询不同。

## <a name="nesting-stored-procedures"></a>嵌套存储过程

如果存储过程调用其他存储过程或执行动态 SQL，则将内部存储过程或代码调用视为嵌套。

专用 SQL 池最多支持 8 个嵌套级别。 与之相比，SQL Server 中的嵌套级别为 32。

最上层存储过程调用等同于嵌套级别 1。

```sql
EXEC prc_nesting
```

如果存储过程还调用另一个 EXEC，则嵌套级别将增加到 2。

```sql
CREATE PROCEDURE prc_nesting
AS
EXEC prc_nesting_2  -- This call is nest level 2
GO
EXEC prc_nesting
```

如果第二个过程随后执行某种动态 SQL，则嵌套级别将增加到 3。

```sql
CREATE PROCEDURE prc_nesting_2
AS
EXEC sp_executesql 'SELECT 'another nest level'  -- This call is nest level 2
GO
EXEC prc_nesting
```

专用 SQL 池当前不支持 [@@NESTLEVEL](/sql/t-sql/functions/nestlevel-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)。 因此，你需要跟踪嵌套级别。 你不太可能超出 8 个嵌套级别的限制。 但是，如果确实超出了此限制，则需要修改你的代码，使嵌套级别在此限制范围内。

## <a name="insertexecute"></a>INSERT..EXECUTE

专用 SQL 池不允许通过 INSERT 语句使用存储过程的结果集。 但是，可以使用替代方法。 有关示例，请参阅[临时表](sql-data-warehouse-tables-temporary.md)上的文章。

## <a name="limitations"></a>限制

专用 SQL 池中未实现 Transact-SQL 存储过程的某些方面，分别是：

* 临时存储过程
* 编号的存储过程
* 扩展的存储过程
* CLR 存储过程
* 加密选项
* 复制选项
* 表值参数
* 只读参数
* 默认参数
* 执行上下文
* return 语句

## <a name="next-steps"></a>后续步骤

有关更多开发技巧，请参阅 [开发概述](sql-data-warehouse-overview-develop.md)。
