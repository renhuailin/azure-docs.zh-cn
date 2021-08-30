---
title: Azure SQL 数据库账本限制
description: Azure SQL 数据库中账本功能的限制
ms.custom: references_regions
ms.date: 07/23/2021
ms.service: sql-database
ms.subservice: security
ms.reviewer: vanto
ms.topic: conceptual
author: JasonMAnderson
ms.author: janders
ms.openlocfilehash: 94bb5c606527cec34afc978323eb456ff4a8753e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121727071"
---
# <a name="limitations-for-azure-sql-database-ledger"></a>Azure SQL 数据库账本限制

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Azure SQL 数据库账本目前为公共预览版，在欧洲西部、巴西南部和美国中西部提供。

本文概述了将账本表与 Azure SQL 数据库一起使用时的限制。

## <a name="limitations"></a>限制

| 函数 | 限制 |
| :--- | :--- |
| 禁用[账本数据库](ledger-database-ledger.md)   | 启用账本数据库后，无法将其禁用。 |
| 最大列数 | 创建[可更新的账本表](ledger-updatable-ledger-tables.md)时，会向账本表添加四个 [GENERATED ALWAYS](/sql/t-sql/statements/create-table-transact-sql#generate-always-columns) 列。 [仅追加账本表](ledger-append-only-ledger-tables.md)会向账本表添加两列。 这些新列将计入 SQL 数据库中支持的最大列数 (1,024)。 |
| 受限的数据类型 | 不支持 XML、SqlVariant、用户定义类型和 FILESTREAM 数据类型。 |
| 内存中表 | 不支持内存中的表。 |
| 稀疏列集 | 不支持稀疏列集。 |
| 账本截断 | 不支持删除[仅追加账本表](ledger-append-only-ledger-tables.md)和[可更新账本表](ledger-updatable-ledger-tables.md)的历史记录表中的旧数据。 |
| 将现有表转换为账本表 | 数据库中未启用账本的现有表无法转换为账本表。 |
|对[自动化摘要管理](ledger-digest-management-and-database-verification.md)的本地冗余存储 (LRS) 支持 | 在账本表中使用 [Azure 存储不可变 Blob](../../storage/blobs/immutable-storage-overview.md) 进行的自动摘要管理无法让用户能够使用 [LRS](../../storage/common/storage-redundancy.md#locally-redundant-storage) 帐户。|

## <a name="remarks"></a>注解

- 创建账本数据库时，默认情况下在数据库中创建的所有新表（未指定 `APPEND_ONLY = ON` 子句）将是[可更新账本表](ledger-updatable-ledger-tables.md)。 要创建[仅追加账本表](ledger-append-only-ledger-tables.md)，请使用 [CREATE TABLE (Transact-SQL)](/sql/t-sql/statements/create-table-transact-sql) 语句。
- 账本表不能是可筛选表。
- 账本表不能具有全文检索功能。
- 不能重命名账本表。
- 不能将账本表移到其他架构。
- 只能在未指定值的情况下将可为空的列添加到账本表。
- 不能删除账本表中的列。
- 仅允许为账本表使用确定性的计算列。
- 不能以修改此列格式的方式来更改现有列。
  - 允许更改：
    - 为 Null 性。
    - nvarchar/ntext 列的排序规则，以及代码页不更改 char/text 列的时间。
    - 变量长度列的长度。
    - 稀疏性。
- 不允许对账本表使用 SWITCH IN/OUT。
- 具有 `LEDGER = ON` 的数据库不支持长期备份 (LTR)。
- 不能对账本表禁用 `LEDGER` 和 `SYSTEM_VERSIONING` 版本控制。
- `UPDATETEXT` 和 `WRITETEXT` API 不能用于账本表。
- 一个事务最多可更新 200 个账本表。
- 对于可更新的账本表，我们继承了临时表的所有限制。
- 不允许对账本表执行更改跟踪。
- 账本表具有聚集列存储索引时，不能具有行存储非聚集索引。

## <a name="next-steps"></a>后续步骤

- [可更新账本表](ledger-updatable-ledger-tables.md)
- [仅追加账本表](ledger-append-only-ledger-tables.md)
- [数据库账本](ledger-database-ledger.md)
- [摘要管理和数据库验证](ledger-digest-management-and-database-verification.md)