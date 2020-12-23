---
title: 使用 T-sql 循环
description: 有关在 Azure Synapse Analytics 中使用 T-sql 循环、替换游标和开发相关解决方案的提示。
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 99ee41de7ffd66191ff712a5ffbda65f3233196f
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2020
ms.locfileid: "93324435"
---
# <a name="use-t-sql-loops-with-synapse-sql-in-azure-synapse-analytics"></a>在 Azure Synapse 分析中将 T-sql 循环用于 Synapse SQL

本文提供了有关使用 T-sql 循环、替换游标以及使用 Synapse SQL 开发相关解决方案的重要技巧。

## <a name="purpose-of-while-loops"></a>WHILE 循环的用途

Synapse SQL 支持重复执行语句块的 [WHILE](https://docs.microsoft.com/sql/t-sql/language-elements/while-transact-sql?view=sql-server-ver15&preserve-view=true) 循环。 只要指定的条件都成立，或者在代码专门使用 BREAK 关键字终止循环之前，此 WHILE 循环将一直继续。 

Synapse SQL 中的循环用于替换 SQL 代码中定义的游标。 幸运的是，几乎所有以 SQL 代码编写的游标都是快进的只读变体。 因此，WHILE 循环是用于替换游标的绝佳替代方案。

## <a name="replace-cursors-in-synapse-sql"></a>替换 Synapse SQL 中的游标

在开始使用之前，应考虑以下问题： "此游标是否可重写以使用基于集的操作？" 在许多情况下，答案是肯定的，通常这也是最佳方法。 基于集的操作的执行速度通常比迭代的逐行方法要快。

可以轻松地使用循环构造来替换快进只读游标。 下面的代码是一个简单的示例。 此代码示例更新数据库中每个表的统计信息。 通过迭代循环中的表，每个命令将依次执行。

首先，创建一个临时表，其中包含用于标识各个语句的唯一行号：

```sql
CREATE TABLE #tbl
WITH
( DISTRIBUTION = ROUND_ROBIN
)
AS
SELECT  ROW_NUMBER() OVER(ORDER BY (SELECT NULL)) AS Sequence
,       [name]
,       'UPDATE STATISTICS '+QUOTENAME([name]) AS sql_code
FROM    sys.tables
;
```

其次，初始化执行循环所需的变量：

```sql
DECLARE @nbr_statements INT = (SELECT COUNT(*) FROM #tbl)
,       @i INT = 1
;
```

现在，每次对一个语句执行一次循环：

```sql
WHILE   @i <= @nbr_statements
BEGIN
    DECLARE @sql_code NVARCHAR(4000) = (SELECT sql_code FROM #tbl WHERE Sequence = @i);
    EXEC    sp_executesql @sql_code;
    SET     @i +=1;
END
```

最后，将第一个步骤创建的临时表删除

```sql
DROP TABLE #tbl;
```

## <a name="next-steps"></a>后续步骤

有关更多开发技巧，请参阅 [开发概述](develop-overview.md)。
