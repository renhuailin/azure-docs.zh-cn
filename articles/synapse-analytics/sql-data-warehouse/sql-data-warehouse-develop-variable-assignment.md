---
title: 分配变量
description: 本文介绍在 Azure Synapse Analytics 中为专用 SQL 池分配 T-SQL 变量的基本技巧。
services: synapse-analytics
author: MSTehrani
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: emtehran
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 87448ea737c11af13a52632e5bf4f67dc54d9ae3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "96459230"
---
# <a name="assign-variables-for-dedicated-sql-pools-in-azure-synapse-analytics"></a>在 Azure Synapse Analytics 中为专用 SQL 池分配变量

本文介绍在专用 SQL 池中分配 T-SQL 变量的基本技巧。

## <a name="set-variables-with-declare"></a>使用 DECLARE 设置变量

使用 `DECLARE` 语句或 `SET` 语句设置专用 SQL 池中的变量。 使用 DECLARE 初始化变量是在 SQL 池中设置变量值的最灵活方式之一。

```sql
DECLARE @v  int = 0
;
```

还可以使用 DECLARE 一次性设置多个变量。 不能使用 SELECT 或 UPDATE 执行以下操作：

```sql
DECLARE @v  INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Smith')
,       @v1 INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Jones')
;
```

不能在同一 DECLARE 语句中初始化和使用变量。 为了演示要点，**不** 允许出现以下示例中的情况，因为同一 DECLARE 语句中初始化和使用了 @p1。 因此，以下示例给出了一个错误：

```sql
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## <a name="set-values-with-set"></a>使用 SET 来设置值

SET 是设置单个变量的常见方法。

以下语句是使用 SET 设置变量的有效方法：

```sql
SET     @v = (Select max(database_id) from sys.databases);
SET     @v = 1;
SET     @v = @v+1;
SET     @v +=1;
```

一次只能使用 SET 设置一个变量。 但是可使用复合运算符。

## <a name="limitations"></a>限制

不能使用 UPDATE 来分配变量。

## <a name="next-steps"></a>后续步骤

有关更多开发技巧，请参阅 [开发概述](sql-data-warehouse-overview-develop.md)。
