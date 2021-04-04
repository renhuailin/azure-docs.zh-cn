---
title: 在 Synapse SQL 中使用临时表
description: 有关在 Synapse SQL 中使用临时表的基本指导。
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 06faa1da71331c299245a93af96166880e7732de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "96451784"
---
# <a name="temporary-tables-in-synapse-sql"></a>Synapse SQL 中的临时表

本文包含有关使用临时表的基本指导，并重点介绍了 Synapse SQL 中的会话级临时表的原则。 

专用 SQL 池和无服务器 SQL 池资源均可利用临时表。 无服务器 SQL 池存在本文末尾介绍的限制。 

## <a name="temporary-tables"></a>临时表

临时表在处理数据时非常有用，尤其是在具有暂时性中间结果的转换期间。 使用 Synapse SQL 时，临时表存在于会话级别。  它们仅对其所创建于的会话可见。 因此，会话注销时将自动删除它们。 

## <a name="temporary-tables-in-dedicated-sql-pool"></a>专用 SQL 池中的临时表

在专用 SQL 池资源中，临时表可以提高性能，因为其结果将写入到本地而不是远程存储。

### <a name="create-a-temporary-table"></a>创建临时表

只需为表名添加 `#` 前缀，即可创建临时表。  例如：

```sql
CREATE TABLE #stats_ddl
(
    [schema_name]        NVARCHAR(128) NOT NULL
,    [table_name]            NVARCHAR(128) NOT NULL
,    [stats_name]            NVARCHAR(128) NOT NULL
,    [stats_is_filtered]     BIT           NOT NULL
,    [seq_nmbr]              BIGINT        NOT NULL
,    [two_part_name]         NVARCHAR(260) NOT NULL
,    [three_part_name]       NVARCHAR(400) NOT NULL
)
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
,    HEAP
)
```

此外可以使用 `CTAS` 通过完全相同的方法来创建临时表：

```sql
CREATE TABLE #stats_ddl
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
,    HEAP
)
AS
(
SELECT
        sm.[name]                                                                AS [schema_name]
,        tb.[name]                                                                AS [table_name]
,        st.[name]                                                                AS [stats_name]
,        st.[has_filter]                                                            AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,                                 QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,        QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM    sys.objects            AS ob
JOIN    sys.stats            AS st    ON    ob.[object_id]        = st.[object_id]
JOIN    sys.stats_columns    AS sc    ON    st.[stats_id]        = sc.[stats_id]
                                    AND st.[object_id]        = sc.[object_id]
JOIN    sys.columns            AS co    ON    sc.[column_id]        = co.[column_id]
                                    AND    sc.[object_id]        = co.[object_id]
JOIN    sys.tables            AS tb    ON    co.[object_id]        = tb.[object_id]
JOIN    sys.schemas            AS sm    ON    tb.[schema_id]        = sm.[schema_id]
WHERE    1=1
AND        st.[user_created]   = 1
GROUP BY
        sm.[name]
,        tb.[name]
,        st.[name]
,        st.[filter_definition]
,        st.[has_filter]
)
;
```

> [!NOTE]
> `CTAS` 是一个强大的命令并具有附加优势，可有效利用事务日志空间。 
> 
> 

### <a name="drop-temporary-tables"></a>删除临时表

创建新会话时，应不存在任何临时表。  不过，如果要调用同一存储过程且使用同一名称来创建临时表，为确保 `CREATE TABLE` 语句成功执行，可以随 `DROP` 使用简单的预存在检查： 

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END
```

为了实现编码一致性，好的做法是对表和临时表都使用此模式。  完成对临时表的操作后，也可使用 `DROP TABLE` 来删除临时表。  

在存储过程开发中，通常将 drop 命令捆绑在过程末尾，以确保清除这些对象。

```sql
DROP TABLE #stats_ddl
```

### <a name="modularize-code"></a>将代码模块化

可以在用户会话中的任何位置使用临时表。 然后，可以利用此功能来帮助你将应用程序代码模块化。  为了进行演示，以下存储过程生成 DDL，按统计信息名称更新数据库中的所有统计信息：

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_update_stats]
(   @update_type    tinyint -- 1 default 2 fullscan 3 sample 4 resample
    ,@sample_pct     tinyint
)
AS

IF @update_type NOT IN (1,2,3,4)
BEGIN;
    THROW 151000,'Invalid value for @update_type parameter. Valid range 1 (default), 2 (fullscan), 3 (sample) or 4 (resample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END

CREATE TABLE #stats_ddl
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
)
AS
(
SELECT
        sm.[name]                                                                AS [schema_name]
,        tb.[name]                                                                AS [table_name]
,        st.[name]                                                                AS [stats_name]
,        st.[has_filter]                                                            AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,                                 QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,        QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM    sys.objects            AS ob
JOIN    sys.stats            AS st    ON    ob.[object_id]        = st.[object_id]
JOIN    sys.stats_columns    AS sc    ON    st.[stats_id]        = sc.[stats_id]
                                    AND st.[object_id]        = sc.[object_id]
JOIN    sys.columns            AS co    ON    sc.[column_id]        = co.[column_id]
                                    AND    sc.[object_id]        = co.[object_id]
JOIN    sys.tables            AS tb    ON    co.[object_id]        = tb.[object_id]
JOIN    sys.schemas            AS sm    ON    tb.[schema_id]        = sm.[schema_id]
WHERE    1=1
AND        st.[user_created]   = 1
GROUP BY
        sm.[name]
,        tb.[name]
,        st.[name]
,        st.[filter_definition]
,        st.[has_filter]
)
SELECT
    CASE @update_type
    WHEN 1
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+');'
    WHEN 2
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH FULLSCAN;'
    WHEN 3
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH SAMPLE '+CAST(@sample_pct AS VARCHAR(20))+' PERCENT;'
    WHEN 4
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH RESAMPLE;'
    END AS [update_stats_ddl]
,   [seq_nmbr]
FROM    t1
;
GO
```

在此阶段，发生的唯一操作是创建存储过程，该存储过程生成 #stats_ddl 表。  如果 #stats_ddl 表已存在，则存储过程会删除该表。 此删除可确保存储过程在会话内多次运行时不会失败。  

由于存储过程的末尾没有 `DROP TABLE`，当存储过程完成后，创建的表将保留，并且可以在存储过程外部进行读取。  

与其他 SQL Server 数据库不同，Synapse SQL 允许你在创建了临时表的过程外部使用该临时表。  通过专用 SQL 池创建的临时表可以在会话中的任何位置使用。 因此，你将拥有模块化程度更高且可管理性更好的代码，如以下示例中所示：

```sql
EXEC [dbo].[prc_sqldw_update_stats] @update_type = 1, @sample_pct = NULL;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''

WHILE @i <= @t
BEGIN
    SET @s=(SELECT update_stats_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

### <a name="temporary-table-limitations"></a>临时表的限制

专用 SQL 池对临时表有一些实现限制：

- 仅支持以会话为作用域的临时表。  不支持全局临时表。
- 不能基于临时表创建视图。
- 只能通过哈希或轮循机制分布来创建临时表。  不支持重复的临时表分布。 

## <a name="temporary-tables-in-serverless-sql-pool"></a>无服务器 SQL 池中的临时表

支持无服务器 SQL 池中的临时表，但它们的用途有限。 它们不能在以文件为查询目标的查询中使用。 

例如，你无法联接其中的数据来自存储中的文件的临时表。 临时表数目的上限为 100，并且它们的总大小的上限为 100MB。

## <a name="next-steps"></a>后续步骤

若要详细了解开发表，请参阅[使用 Synapse SQL 资源设计表](develop-tables-overview.md)一文。

