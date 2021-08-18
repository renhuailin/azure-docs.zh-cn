---
title: 使用事务
description: 在 Azure Synapse Analytics 中使用专用 SQL 池实现事务以开发解决方案的技巧。
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 44e4b35d7e67e566b3ccef61cf5fa22077e844c7
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121861809"
---
# <a name="use-transactions-with-dedicated-sql-pool-in-azure-synapse-analytics"></a>在 Azure Synapse Analytics 中将事务与专用 SQL 池配合使用

在 Azure Synapse Analytics 中使用专用 SQL 池实现事务以开发解决方案的技巧。

## <a name="what-to-expect"></a>期望

如你所料，专用 SQL 池支持将事务作为数据仓库工作负载的一部分。 但是，为了确保大规模保持专用 SQL 池的性能，与 SQL Server 相比，某些功能受到限制。 本文将突出两者的差异，并列出其他信息。

## <a name="transaction-isolation-levels"></a>事务隔离级别

专用 SQL 池实现了 ACID 事务。 事务支持的隔离级别默认为 READ UNCOMMITTED。  你可以通过在连接到主数据库时为用户数据库打开 READ_COMMITTED_SNAPSHOT 数据库选项，将默认的隔离级别更改为 READ COMMITTED SNAPSHOT ISOLATION。  

启用后，此数据库中的所有事务都将在 READ COMMITTED SNAPSHOT ISOLATION 下执行，并且将不接受在会话级别设置 READ UNCOMMITTED。 有关详细信息，请查看 [ALTER DATABASE SET 选项 (Transact-SQL)](/sql/t-sql/statements/alter-database-transact-sql-set-options?preserve-view=true&view=azure-sqldw-latest)。

## <a name="transaction-size"></a>事务大小
单个数据修改事务有大小限制。 限制按每个分发进行应用。 这样，通过将限制乘以分发数，可得总分配额。 

要预计事务中的最大行数，请将分发上限除以每一行的总大小。 对于可变长度列，考虑采用平均的列长度而不使用最大大小。

下表中进行了以下假设：

* 出现平均数据分布
* 平均行长度为 250 个字节

## <a name="gen2"></a>Gen2

| [DWU](resource-consumption-models.md#data-warehouse-units) | 每个分布的上限 (GB) | 分布的数量 | 最大事务大小 (GB) | 每个分布的行数 | 每个事务的最大行数 |
| --- | --- | --- | --- | --- | --- |
| DW100c |1 |60 |60 |4,000,000 |240,000,000 |
| DW200c |1.5 |60 |90 |6,000,000 |360,000,000 |
| DW300c |2.25 |60 |135 |9,000,000 |540,000,000 |
| DW400c |3 |60 |180 |12,000,000 |720,000,000 |
| DW500c |3.75 |60 |225 |15,000,000 |900,000,000 |
| DW1000c |7.5 |60 |450 |30,000,000 |1,800,000,000 |
| DW1500c |11.25 |60 |675 |45,000,000 |2,700,000,000 |
| DW2000c |15 |60 |900 |60,000,000 |3,600,000,000 |
| DW2500c |18.75 |60 |1125 |75,000,000 |4,500,000,000 |
| DW3000c |22.5 |60 |1,350 |90,000,000 |5,400,000,000 |
| DW5000c |37.5 |60 |2,250 |150,000,000 |9,000,000,000 |
| DW6000c |45 |60 |2,700 |180,000,000 |10,800,000,000 |
| DW7500c |56.25 |60 |3,375 |225,000,000 |13,500,000,000 |
| DW10000c |75 |60 |4,500 |300,000,000 |18,000,000,000 |
| DW15000c |112.5 |60 |6,750 |450,000,000 |27,000,000,000 |
| DW30000c |225 |60 |13,500 |900,000,000 |54,000,000,000 |

## <a name="gen1"></a>Gen1

| [DWU](resource-consumption-models.md#data-warehouse-units) | 每个分布的上限 (GB) | 分布的数量 | 最大事务大小 (GB) | 每个分布的行数 | 每个事务的最大行数 |
| --- | --- | --- | --- | --- | --- |
| DW100 |1 |60 |60 |4,000,000 |240,000,000 |
| DW200 |1.5 |60 |90 |6,000,000 |360,000,000 |
| DW300 |2.25 |60 |135 |9,000,000 |540,000,000 |
| DW400 |3 |60 |180 |12,000,000 |720,000,000 |
| DW500 |3.75 |60 |225 |15,000,000 |900,000,000 |
| DW600 |4.5 |60 |270 |18,000,000 |1,080,000,000 |
| DW1000 |7.5 |60 |450 |30,000,000 |1,800,000,000 |
| DW1200 |9 |60 |540 |36,000,000 |2,160,000,000 |
| DW1500 |11.25 |60 |675 |45,000,000 |2,700,000,000 |
| DW2000 |15 |60 |900 |60,000,000 |3,600,000,000 |
| DW3000 |22.5 |60 |1,350 |90,000,000 |5,400,000,000 |
| DW6000 |45 |60 |2,700 |180,000,000 |10,800,000,000 |

事务大小限制按每个事务或操作进行应用。 不会跨所有当前事务进行应用。 因此，允许每个事务向日志写入此数量的数据。

为优化并最大程度地减少写入到日志中的数据量，请参阅[事务最佳做法](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?context=/azure/synapse-analytics/context/context)一文。

> [!WARNING]
> 最大事务大小仅可在哈希或者 ROUND_ROBIN 分布式表（其中数据均匀分布）中实现。 如果事务以偏斜方式向分布写入数据，那么更有可能在达到最大事务大小之前达到该限制。
> <!--REPLICATED_TABLE-->

## <a name="transaction-state"></a>事务状态

专用 SQL 池使用 XACT_STATE() 函数（采用值 -2）来报告失败的事务。 此值表示事务已失败并标记为仅可回滚。

> [!NOTE]
> XACT_STATE 函数使用 -2 表示失败的事务，以代表 SQL Server 中不同的行为。 SQL Server 使用值 -1 来代表无法提交的事务。 SQL Server 可以容忍事务内的某些错误，而无需将其标记为无法提交。 例如，`SELECT 1/0` 导致错误，但不强制事务进入无法提交状态。 SQL Server 还允许读取无法提交的事务。 但是，专用 SQL 池不允许执行此操作。 如果在专用 SQL 池事务内部发生错误，它会自动进入 -2 状态，并且在该语句回退之前，你无法执行任何 Select 语句。 因此，必须查看应用程序代码是否使用 XACT_STATE()，你可能需要修改代码。

例如，在 SQL Server 中，可能会看到如下所示的事务：

```sql
SET NOCOUNT ON;
DECLARE @xact_state smallint = 0;

BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(INT,'ABC');
    END TRY
    BEGIN CATCH
        SET @xact_state = XACT_STATE();

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;

        IF @@TRANCOUNT > 0
        BEGIN
            ROLLBACK TRAN;
            PRINT 'ROLLBACK';
        END

    END CATCH;

IF @@TRANCOUNT >0
BEGIN
    PRINT 'COMMIT';
    COMMIT TRAN;
END

SELECT @xact_state AS TransactionState;
```

前面的代码提供以下错误消息：

Msg 111233, Level 16, State 1, Line 1 111233；当前事务已中止，所有挂起的更改都已回退。 原因：仅回退状态的事务未在 DDL、DML 或 SELECT 语句之前显式回退。

不会获得 ERROR_* 函数的输出值。

在专用 SQL 池中，该代码需要稍做更改：

```sql
SET NOCOUNT ON;
DECLARE @xact_state smallint = 0;

BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(INT,'ABC');
    END TRY
    BEGIN CATCH
        SET @xact_state = XACT_STATE();

        IF @@TRANCOUNT > 0
        BEGIN
            ROLLBACK TRAN;
            PRINT 'ROLLBACK';
        END

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;
    END CATCH;

IF @@TRANCOUNT >0
BEGIN
    PRINT 'COMMIT';
    COMMIT TRAN;
END

SELECT @xact_state AS TransactionState;
```

现在观察到了预期行为。 事务中的错误得到了管理，并且 ERROR_* 函数提供了预期值。

所做的一切改变是事务的 ROLLBACK 必须发生于在 CATCH 块中读取错误信息之前。

## <a name="error_line-function"></a>Error_Line() 函数

另外值得注意的是，专用 SQL 池未实现或不支持 ERROR_LINE() 函数。 如果代码中包含此函数，则需要将它删除才能符合专用 SQL 池的要求。 请在代码中使用查询标签，而不是实现等效的功能。 有关详细信息，请参阅 [LABEL](develop-label.md) 一文。

## <a name="use-of-throw-and-raiserror"></a>THROW 和 RAISERROR 的使用

THROW 是在专用 SQL 池中引发异常的新式做法，但也支持 RAISERROR。 不过，有些值得注意的差异。

* 对于 THROW，用户定义的错误消息数目不能在 100,000 - 150,000 范围内
* RAISERROR 错误消息固定为 50,000
* 不支持 sys.messages

## <a name="limitations"></a>限制

专用 SQL 池有一些与事务相关的其他限制。 这些限制如下：

* 无分布式事务
* 不允许嵌套事务
* 不允许保存点
* 无已命名事务
* 无已标记事务
* 不支持 DDL，例如用户定义的事务内的 CREATE TABLE

## <a name="next-steps"></a>后续步骤

若要了解有关优化事务的详细信息，请参阅[事务最佳做法](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?context=/azure/synapse-analytics/context/context)。 还为[专用 SQL 池](best-practices-dedicated-sql-pool.md)和[无服务器 SQL 池](best-practices-serverless-sql-pool.md)提供了其他最佳做法指南。