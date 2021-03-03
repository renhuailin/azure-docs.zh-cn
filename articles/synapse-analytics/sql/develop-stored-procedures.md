---
title: 使用存储过程
description: 使用 Azure Synapse Analytics 中的 Synapse SQL 实现存储过程以进行解决方案开发的技巧。
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 11/03/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 083077b2b29bc415e83d6f5a76941fa83fa853e3
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101674153"
---
# <a name="stored-procedures-using-synapse-sql-in-azure-synapse-analytics"></a>使用 Azure Synapse Analytics 中的 Synapse SQL 存储过程

使用 Synapse SQL 预配和无服务器池可以将复杂的数据处理逻辑放入 SQL 存储过程。 使用存储过程可以很好地封装 SQL 代码，并将其存储在数据仓库中的数据附近。 存储过程通过将代码封装到可管理的单元中来帮助开发人员模块化其解决方案，从而提高代码的可重用性。 每个存储过程还可接受参数，使其更具弹性。
在本文中，你将了解一些用于在 Synapse SQL 池中实现存储过程以开发解决方案的提示。

## <a name="what-to-expect"></a>期望

Synapse SQL 支持许多 SQL Server 中使用的 T-sql 功能。 更重要的是，可使用特定的横向扩展功能将解决方案的性能最大化。 在本文中，你将了解你可以在存储过程中设置的功能。

> [!NOTE]
> 在过程主体中，你只能使用 Synapse SQL surface 区域中支持的功能。 查看 [本文](overview-features.md) ，确定可用于存储过程的对象和语句。 这些文章中的示例使用了在无服务器和专用外围应用中都可用的通用功能。 请参阅本文末尾的 [预配和无服务器 SYNAPSE SQL 池中](#limitations) 的其他限制。

为了保持 SQL 池的缩放性和性能，还有一些特性和功能存在行为差异，其他功能并不受支持。

## <a name="stored-procedures-in-synapse-sql"></a>Synapse SQL 中的存储过程

在下面的示例中，可以看到删除外部对象的过程（如果它们存在于数据库中）：

```sql
CREATE PROCEDURE drop_external_table_if_exists @name SYSNAME
AS BEGIN
    IF (0 <> (SELECT COUNT(*) FROM sys.external_tables WHERE name = @name))
    BEGIN
        DECLARE @drop_stmt NVARCHAR(200) = N'DROP EXTERNAL TABLE ' + @name; 
        EXEC sp_executesql @tsql = @drop_stmt;
    END
END
GO
CREATE PROCEDURE drop_external_file_format_if_exists @name SYSNAME
AS BEGIN
    IF (0 <> (SELECT COUNT(*) FROM sys.external_file_formats WHERE name = @name))
    BEGIN
        DECLARE @drop_stmt NVARCHAR(200) = N'DROP EXTERNAL FILE FORMAT ' + @name; 
        EXEC sp_executesql @tsql = @drop_stmt;
    END
END
GO
CREATE PROCEDURE drop_external_data_source_if_exists @name SYSNAME
AS BEGIN
    IF (0 <> (SELECT COUNT(*) FROM sys.external_data_sources WHERE name = @name))
    BEGIN
        DECLARE @drop_stmt NVARCHAR(200) = N'DROP EXTERNAL DATA SOURCE ' + @name; 
        EXEC sp_executesql @tsql = @drop_stmt;
    END
END
```

可以使用 `EXEC` 可在其中指定过程名称和参数的语句执行这些过程：

```sql
EXEC drop_external_table_if_exists 'mytest';
EXEC drop_external_file_format_if_exists 'mytest';
EXEC drop_external_data_source_if_exists 'mytest';
```

Synapse SQL 提供简化且简化的存储过程实现。 相比于 SQL Server，最大差异是存储过程不是预先编译的代码。 在数据仓库中，与针对大型数据卷运行查询所用的时间相比，编译时间非常少。 保证存储过程代码针对大量查询正确优化更为重要。 目标是要节省时数、分钟数和秒数，而不是毫秒数。 因此，将存储过程视为 SQL 逻辑的容器更有帮助。

当 Synapse SQL 执行你的存储过程时，SQL 语句在运行时进行分析、转换和优化。 在此过程中，每个语句都转换为分布式查询。 针对数据执行的 SQL 代码与提交的查询不同。

## <a name="encapsulate-validation-rules"></a>封装验证规则

使用存储过程，您可以在存储在 SQL 数据库中的单个模块中查找验证逻辑。 在下面的示例中，可以了解如何验证参数的值并更改它们的默认值。

```sql
CREATE PROCEDURE count_objects_by_date_created 
                            @start_date DATETIME2,
                            @end_date DATETIME2
AS BEGIN 

    IF( @start_date >= GETUTCDATE() )
    BEGIN
        THROW 51000, 'Invalid argument @start_date. Value should be in past.', 1;  
    END

    IF( @end_date IS NULL )
    BEGIN
        SET @end_date = GETUTCDATE();
    END

    IF( @start_date >= @end_date )
    BEGIN
        THROW 51000, 'Invalid argument @end_date. Value should be greater than @start_date.', 2;  
    END

    SELECT
         year = YEAR(create_date),
         month = MONTH(create_date),
         objects_created = COUNT(*)
    FROM
        sys.objects
    WHERE
        create_date BETWEEN @start_date AND @end_date
    GROUP BY
        YEAR(create_date), MONTH(create_date);
END
```

调用此过程时，sql 过程中的逻辑将验证输入参数。

```sql

EXEC count_objects_by_date_created '2020-08-01', '2020-09-01'

EXEC count_objects_by_date_created '2020-08-01', NULL

EXEC count_objects_by_date_created '2020-09-01', '2020-08-01'
-- Error
-- Invalid argument @end_date. Value should be greater than @start_date.

EXEC count_objects_by_date_created '2120-09-01', NULL
-- Error
-- Invalid argument @start_date. Value should be in past.
```

## <a name="nesting-stored-procedures"></a>嵌套存储过程

如果存储过程调用其他存储过程或执行动态 SQL，则将内部存储过程或代码调用视为嵌套。
下面的代码演示了嵌套过程的示例：

```sql
CREATE PROCEDURE clean_up @name SYSNAME
AS BEGIN
    EXEC drop_external_table_if_exists @name;
    EXEC drop_external_file_format_if_exists @name;
    EXEC drop_external_data_source_if_exists @name;
END
```

此过程接受一个表示某些名称的参数，然后调用其他过程删除具有此名称的对象。
Synapse SQL 池最多支持8个嵌套级别。 此功能与 SQL Server 略有不同。 SQL Server 中的嵌套级别为 32。

最上层存储过程调用等同于嵌套级别 1。

```sql
EXEC clean_up 'mytest'
```

如果存储过程还调用另一个 EXEC，则嵌套级别将增加到 2。

```sql
CREATE PROCEDURE clean_up @name SYSNAME
AS
    EXEC drop_external_table_if_exists @name  -- This call is nest level 2
GO
EXEC clean_up 'mytest'  -- This call is nest level 1
```

如果第二个过程随后执行某种动态 SQL，则嵌套级别将增加到 3。

```sql
CREATE PROCEDURE drop_external_table_if_exists @name SYSNAME
AS BEGIN
    /* See full code in the previous example */
    EXEC sp_executesql @tsql = @drop_stmt;  -- This call is nest level 3
END
GO
CREATE PROCEDURE clean_up @name SYSNAME
AS
    EXEC drop_external_table_if_exists @name  -- This call is nest level 2
GO
EXEC clean_up 'mytest'  -- This call is nest level 1
```

> [!NOTE]
> Synapse SQL 当前不支持[@ @NESTLEVEL ](/sql/t-sql/functions/nestlevel-transact-sql?view=azure-sqldw-latest&preserve-view=true)。 需要跟踪嵌套级别。 不太可能超过 8 个嵌套级别的限制，但如果超过了，则需要重新修改代码，以使它符合限制内的嵌套级别。

## <a name="insertexecute"></a>INSERT..EXECUTE

预配的 Synapse SQL 池不允许使用 INSERT 语句来使用存储过程的结果集。 可以使用其他方法。 有关示例，请参阅有关预配的 Synapse SQL 池的 [临时表](develop-tables-temporary.md) 的文章。

## <a name="limitations"></a>限制

Synapse SQL 中未实现 Transact-sql 存储过程的某些方面，例如：

| 功能/选项 | 已预配 | 无服务器 |
| --- | --- |
| 临时存储过程 | 否 | 是 |
| 带编号的存储过程 | 否 | 否 |
| 扩展的存储过程 | 否 | 否 |
| CLR 存储过程 | 否 | 否 |
| 加密选项 | 否 | 是 |
| 复制选项 | 否 | 否 |
| 表值参数 | 否 | 否 |
| 只读参数 | 否 | 否 |
| 默认参数 | 否 | 是 |
| 执行上下文 | 否 | 否 |
| Return 语句 | 否 | 是 |
| 向中插入。 EXEC | 否 | 是 |

## <a name="next-steps"></a>后续步骤

有关更多开发技巧，请参阅 [开发概述](develop-overview.md)。
