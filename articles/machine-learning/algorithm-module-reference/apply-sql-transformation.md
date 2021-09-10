---
title: 应用 SQL 转换
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习中的“应用 SQL 转换”模块在输入数据集上运行 SQLite 查询以转换数据。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 08/11/2021
ms.openlocfilehash: 71f320c9144270b157b46a58e2107873439b2227
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122831339"
---
# <a name="apply-sql-transformation"></a>应用 SQL 转换

本文介绍 Azure 机器学习设计器的某个模块。

你可以使用“应用 SQL 转换”模块执行以下操作：
  
-   创建结果表，并将数据集保存在便携式数据库中。  
  
-   对数据类型执行自定义转换，或创建聚合。  
  
-   执行 SQL 查询语句，筛选或更改数据，并以数据表的形式返回查询结果。  

> [!IMPORTANT]
> 此模块中使用的 SQL 引擎是 SQLite  。 有关 SQLite 语法的详细信息，请参阅 [SQL as Understood by SQLite](https://www.sqlite.org/index.html)。
> 此模块会将数据传输到内存 DB 中的 SQLite，因此模块执行需要更多内存，并且可能会遇到 `Out of memory` 错误。 确保计算机具有足够的 RAM。

## <a name="how-to-configure-apply-sql-transformation"></a>如何配置应用 SQL 转换  

该模块可将最多三个数据集用作输入。 引用连接到每个输入端口的数据集时，必须使用名称 `t1`、`t2` 和 `t3`。 表编号指示输入端口的索引。  

下面是演示如何联接两个表的示例代码。 t1 和 t2 是连接到“应用 SQL 转换”左侧和中间输入端口的两个数据集：

```sql
SELECT t1.*
    , t3.Average_Rating
FROM t1 join
    (SELECT placeID
        , AVG(rating) AS Average_Rating
    FROM t2
    GROUP BY placeID
    ) as t3
on t1.placeID = t3.placeID
```
  
剩余参数是使用 SQLite 语法的 SQL 查询。 在“SQL 脚本”文本框中键入多行时，请使用分号终止每条语句。 否则，换行符会转换为空格。  

此模块支持 SQLite 语法的所有标准语句。 如需了解不支持的语句列表，请参阅[技术说明](#technical-notes)部分。

##  <a name="technical-notes"></a>技术说明  

本部分包含实现详情、使用技巧和常见问题解答。

-   端口 1 上始终需要输入。  
  
-   对于包含空格或其他特殊字符的列标识符，请在 `SELECT` 和 `WHERE` 子句中引用列时，始终将列标识符用方括号或双引号括起来。  

-   如果在应用 SQL 转换之前已使用编辑元数据指定列元数据（分类或字段），则应用 SQL 转换的输出将不包含这些属性。   在应用 SQL 转换后，需要使用编辑元数据来编辑列。 
  
### <a name="unsupported-statements"></a>不支持的语句  

尽管 SQLite 支持很多 ANSI SQL 标准，但其并不包括商业关系数据库系统支持的许多功能。 有关详细信息，请参阅 [SQL as Understood by SQLite](http://www.sqlite.org/lang.html)。 另外，请在创建 SQL 语句时注意以下限制：  
  
- SQLite 使用动态类型的值，而不是像在大多数关系数据库系统中的那样将类型分配给列。 它是一种弱类型，并允许隐式转换类型。  
  
- 实现 `LEFT OUTER JOIN`，但不实现 `RIGHT OUTER JOIN` 或 `FULL OUTER JOIN`。  

- 你可将 `RENAME TABLE` 和 `ADD COLUMN` 语句与 `ALTER TABLE` 命令结合使用，但不支持其他子句，例如 `DROP COLUMN`、`ALTER COLUMN` 和 `ADD CONSTRAINT`。  
  
- 可在 SQLite 中创建“视图”，但之后视图为只读。 不可在视图中执行 `DELETE`、`INSERT` 或 `UPDATE` 语句。 但可创建一个触发器，尝试在视图上执行 `DELETE`、`INSERT` 或 `UPDATE`，并在触发器主体中执行其他操作。  
  

除了在官方 SQLite 网站上提供不受支持的功能列表，下面的 Wiki 还提供其他不受支持的功能列表：[SQLite - 不受支持的 SQL](http://www2.sqlite.org/cvstrac/wiki?p=UnsupportedSql)  
    
## <a name="next-steps"></a>后续步骤

请参阅 Azure 机器学习的[可用模块集](module-reference.md)。 
