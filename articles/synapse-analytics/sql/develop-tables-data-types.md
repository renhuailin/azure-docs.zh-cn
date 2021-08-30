---
title: Synapse SQL 中的表数据类型
description: 有关在 Synapse SQL 中定义表数据类型的建议。
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: 32a85b4409f36846a14e21d2f3894b7dbae1ec30
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121723347"
---
# <a name="table-data-types-in-synapse-sql"></a>Synapse SQL 中的表数据类型

本文包含关于在 Synapse SQL 专用池中定义表数据类型的建议。 

## <a name="data-types"></a>数据类型

Synapse SQL 专用池支持最常用的数据类型。 有关受支持数据类型的列表，请参阅 CREATE TABLE 语句中的[数据类型](/sql/t-sql/statements/create-table-azure-sql-data-warehouse#DataTypes&preserve-view=true)。 对于 Synapse SQL 无服务器，请参阅[在 Azure Synapse Analytics 中使用无服务器 SQL 池查询存储文件](./query-data-storage.md)和[如何在 Azure Synapse Analytics 中通过无服务器 SQL 池使用 OPENROWSET](./develop-openrowset.md) 这两篇文章

## <a name="minimize-row-length"></a>最大限度地减小行长度

最大限度地减小数据类型大小可以缩短行长度，从而获得更好的查询性能。 使用适合数据的最小数据类型。

- 避免使用较大默认长度定义字符列。 例如，如果最长的值是 25 个字符，则将列定义为 VARCHAR(25)。
- 当仅需要 VARCHAR 时请避免使用 [NVARCHAR][NVARCHAR]
- 尽可能使用 NVARCHAR(4000) 或 VARCHAR(8000)，而非 NVARCHAR(MAX) 或 VARCHAR(MAX)。
- 避免使用带有 0（零）位数的浮点数和小数。  这些应为 TINYINT、SMALLINT、INT 或 BIGINT。

> [!NOTE]
> 如果使用 PolyBase 外部表来加载 Synapse SQL 表，则定义的表行长度不能超过 1 MB。 当数据长度可变的行超过 1 MB 时，可使用 BCP 而不是 PolyBase 加载行。

## <a name="identify-unsupported-data-types"></a>识别不支持的数据类型

如果从另一个 SQL 数据库迁移数据库，可能会遇到 Synapse SQL 不支持的数据类型。 使用此查询发现现有 SQL 架构中不支持的数据类型。

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','xml')
 AND  y.[is_user_defined] = 1;
```

## <a name="workarounds-for-unsupported-data-types"></a><a name="unsupported-data-types"></a>对不受支持的数据类型的解决方法

下面的列表显示了 Synapse SQL 不支持的数据类型，同时提供可替代不支持的数据类型的可用数据类型。

| 不支持的数据类型 | 解决方法 |
| --- | --- |
| [geometry](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?view=azure-sqldw-latest&preserve-view=true&preserve-view=true) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?view=azure-sqldw-latest&preserve-view=true) |
| [geography](/sql/t-sql/spatial-geography/spatial-types-geography) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?view=azure-sqldw-latest&preserve-view=true) |
| [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql?view=azure-sqldw-latest&preserve-view=true)(4000) |
| [image](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?view=azure-sqldw-latest&preserve-view=true) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?view=azure-sqldw-latest&preserve-view=true) |
| [text](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?view=azure-sqldw-latest&preserve-view=true) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql?view=azure-sqldw-latest&preserve-view=true) |
| [ntext](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?view=azure-sqldw-latest&preserve-view=true) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql?view=azure-sqldw-latest&preserve-view=true) |
| [sql_variant](/sql/t-sql/data-types/sql-variant-transact-sql?view=azure-sqldw-latest&preserve-view=true) |将列拆分成多个强类型化列。 |
| [table](/sql/t-sql/data-types/table-transact-sql?view=azure-sqldw-latest&preserve-view=true) |转换为临时表，或考虑使用 [CETAS](../sql/develop-tables-cetas.md) 将数据存储到存储。 |
| [timestamp](/sql/t-sql/data-types/date-and-time-types) |重写代码来使用 [datetime2](/sql/t-sql/data-types/datetime2-transact-sql?view=azure-sqldw-latest&preserve-view=true) 和 [CURRENT_TIMESTAMP](/sql/t-sql/functions/current-timestamp-transact-sql?view=azure-sqldw-latest&preserve-view=true) 函数。 仅支持常量作为默认值，因此，不能将 current_timestamp 定义为默认约束。 如果需要从 timestamp 类型化列迁移行版本值，请为 NOT NULL 或 NULL 行版本值使用 [BINARY](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?view=azure-sqldw-latest&preserve-view=true)(8) 或 [VARBINARY](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?view=azure-sqldw-latest&preserve-view=true)(8)。 |
| [xml](/sql/t-sql/xml/xml-transact-sql?view=azure-sqldw-latest&preserve-view=true) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql?view=azure-sqldw-latest&preserve-view=true) |
| [用户定义的类型](/sql/relational-databases/native-client/features/using-user-defined-types) |尽可能转换回本机数据类型。 |
| 默认值 | 默认值仅支持文本和常量。 |

## <a name="next-steps"></a>后续步骤

有关开发表的详细信息，请参阅[表概述](develop-overview.md)。
