---
title: Azure SQL 数据库仅追加账本表
description: 本文提供有关 Azure SQL 数据库中仅追加账本表架构和视图的信息。
ms.custom: references_regions
ms.date: 07/23/2021
ms.service: sql-database
ms.subservice: security
ms.reviewer: vanto
ms.topic: conceptual
author: JasonMAnderson
ms.author: janders
ms.openlocfilehash: a23190552239af387fa2af362592347c0c46b900
ms.sourcegitcommit: d9a2b122a6fb7c406e19e2af30a47643122c04da
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2021
ms.locfileid: "114666719"
---
# <a name="azure-sql-database-append-only-ledger-tables"></a>Azure SQL 数据库仅追加账本表

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Azure SQL 数据库账本目前为公共预览版，在西欧、巴西南部和美国中西部提供。

仅追加账本表只允许对表执行 `INSERT` 操作，确保数据库管理员等特权用户无法通过传统的[数据操作语言](/sql/t-sql/queries/queries)操作更改数据。 仅追加账本表非常适合不会更新或删除记录的系统（例如安全信息事件和管理系统），或需要将数据从区块链复制到数据库的区块链系统。 由于不能对仅追加表执行 `UPDATE` 或 `DELETE` 操作，因此不需要像对[可更新账本表](ledger-updatable-ledger-tables.md)那样提供相应的历史记录表。

:::image type="content" source="media/ledger/ledger-table-architecture-append-only.png" alt-text="此示意图显示账本表体系结构。":::

可以通过在 [CREATE TABLE (Transact-SQL)](/sql/t-sql/statements/create-table-transact-sql) 语句中指定 `LEDGER = ON` 参数并指定 `APPEND_ONLY = ON` 选项来创建仅追加账本表。

> [!IMPORTANT]
> 创建账本表形式的表后，无法将该表还原为没有账本功能的表。 因此，攻击者无法临时删除账本功能，对表进行更改，然后重新启用账本功能。

### <a name="append-only-ledger-table-schema"></a>仅追加账本表的架构

仅追加表需要具有以下 [GENERATED ALWAYS](/sql/t-sql/statements/create-table-transact-sql#generate-always-columns) 列，这些列包含元数据，说明哪些事务对表进行了更改，以及事务更新行的操作顺序。 创建仅追加账本表时，将在账本表中创建 `GENERATED ALWAYS` 列。 此数据可用于取证目的，了解在一段时间内插入数据的方式。

如果未在 [CREATE TABLE](/sql/t-sql/statements/create-table-transact-sql) 语句中指定 `GENERATED ALWAYS` 列的定义，系统将使用以下默认名称自动添加这些定义。

| 默认列名称 | 数据类型 | 说明 |
|--|--|--|
| ledger_start_transaction_id | bigint | 创建行版本的事务 ID |
| ledger_start_sequence_number | bigint | 创建行版本的事务中的操作序列号 |

## <a name="ledger-view"></a>账本视图

对于每个仅追加账本表，系统会自动生成一个称为“账本视图”的视图。 账本视图报告表中发生的所有行插入。 账本视图主要对[可更新账本表](ledger-updatable-ledger-tables.md)（而不是仅追加账本表）有用，因为仅追加账本表没有任何 `UPDATE` 或 `DELETE` 功能。 仅追加账本表的账本视图可用于实现可更新账本表和仅追加账本表之间的一致性。

### <a name="ledger-view-schema"></a>账本视图架构

> [!NOTE]
> 创建表时，可结合使用 `<ledger_view_option>` 参数和 [CREATE TABLE (Transact-SQL)](/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current&preserve-view=true) 语句来自定义账本视图的列名。 有关详细信息，请参阅 [CREATE TABLE (Transact-SQL)](/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current&preserve-view=true) 中的[账本视图选项](/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current&preserve-view=true#ledger-view-options)和相应的示例。

| 默认列名称 | 数据类型 | 说明 |
| --- | --- | --- |
| ledger_transaction_id | bigint | 创建或删除行版本的事务 ID。 |
| ledger_sequence_number | bigint | 表中事务内的行级别操作的序列号。 |
| ledger_operation_type_id | tinyint | 包含 `0` (INSERT) 或 `1` (DELETE) 。 在账本表中插入行会在此列包含 `0` 的账本视图中生成一个新行。 从账本表中删除行会在此列包含 `1` 的账本视图中生成一个新行。 更新账本表中的行在账本视图中生成两个新行。 在此列中，一行包含 `1`(DELETE)，另一行包含 `1`(INSERT)。 不应对仅追加账本表执行 DELETE。 |
| ledger_operation_type_desc | nvarchar(128) | 包含 `INSERT` 或 `DELETE`。 有关详细信息，请参阅上一行。 |

## <a name="next-steps"></a>后续步骤

- [创建和使用仅追加账本表](ledger-how-to-append-only-ledger-tables.md)
- [创建和使用可更新账本表](ledger-how-to-updatable-ledger-tables.md)
