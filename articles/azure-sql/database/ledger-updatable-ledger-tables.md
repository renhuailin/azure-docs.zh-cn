---
title: Azure SQL 数据库可更新账本表
description: 本文提供有关 Azure SQL 数据库中可更新账本表、账本架构和账本视图的信息。
ms.custom: references_regions
ms.date: 07/23/2021
ms.service: sql-database
ms.subservice: security
ms.reviewer: vanto
ms.topic: conceptual
author: JasonMAnderson
ms.author: janders
ms.openlocfilehash: 4a84cb359a0840f593fd732339a4ef9f9f3588db
ms.sourcegitcommit: d9a2b122a6fb7c406e19e2af30a47643122c04da
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2021
ms.locfileid: "114665502"
---
# <a name="azure-sql-database-updatable-ledger-tables"></a>Azure SQL 数据库可更新账本表

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Azure SQL 数据库账本目前为公共预览版，在西欧、巴西南部和美国中西部提供。

可更新账本表是系统版本控制表，用户可对其进行更新和删除，同时还提供防篡改功能。 更新或删除时，行的所有早期版本都将保留在辅助表中，称为历史记录表。 历史记录表反映了可更新账本表的架构。 更新行时，行的最新版本将保留在账本表中，而其早期版本由系统插入到历史记录表中，以透明方式向应用程序显示。 

:::image type="content" source="media/ledger/ledger-table-architecture.png" alt-text="显示账本表体系结构的关系图。":::

## <a name="updatable-ledger-tables-vs-temporal-tables"></a>可更新账本表与时态表

可更新账本表和[时态表](/sql/relational-databases/tables/temporal-tables)都是系统版本控制表，其数据库引擎在辅助历史记录表中捕获历史行版本。 任一技术都提供唯一优势。 可更新账本表使当前数据和历史数据都不会被篡改。 时态表支持查询存储在任何时间点的数据，而不是只查询在当前时间点上正确的数据。

可创建既是可更新账本表又是时态表的表来同时使用这两种技术。 可通过两种方式创建可更新账本表：

- 在 Azure 门户中创建新数据库时，在账本配置期间选择“在此数据库的所有将来表上启用账本”，或在 [CREATE DATABASE (Transact-SQL)](/sql/t-sql/statements/create-database-transact-sql) 语句中指定 `LEDGER = ON` 参数。 此操作会创建一个账本数据库，并确保将来在数据库中创建的所有表默认都是可更新账本表。
- 在数据库级别未启用账本的数据库上创建新表时，在 [CREATE TABLE (Transact-SQL)](/sql/t-sql/statements/create-table-transact-sql) 语句中指定 `LEDGER = ON` 参数。

有关在 T-SQL 语句中指定 `LEDGER` 参数时可用选项的详细信息，请参阅 [CREATE TABLE (Transact-SQL)](/sql/t-sql/statements/create-table-transact-sql)。

> [!IMPORTANT]
> 创建账本表后，不能将其还原为不是账本表的表。 因此，攻击者无法在账本表上临时删除账本功能，进行修改，然后重新启用账本功能。 

### <a name="updatable-ledger-table-schema"></a>可更新账本表架构

可更新账本表需要具有以下 [GENERATED ALWAYS](/sql/t-sql/statements/create-table-transact-sql#generate-always-columns) 列，这些列包含元数据，说明哪些事务对表进行了更改，以及交易更新行的操作顺序。 此数据可用于取证目的，了解在一段时间内插入数据的方式。

> [!NOTE]
> 如果未在 [CREATE TABLE (Transact-SQL)](/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current&preserve-view=true) 语句中指定账本表和账本历史记录表所需的 `GENERATED ALWAYS` 列，系统会自动添加这些列，并使用以下默认名称。 有关详细信息，请参阅[创建可更新账本表](/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current&preserve-view=true#x-creating-a-updatable-ledger-table)中的示例。

| 默认列名称 | 数据类型 | 说明 |
| --- | --- | --- |
| ledger_start_transaction_id | bigint | 创建行版本的事务 ID |
| ledger_end_transaction_id | bigint | 删除行版本的事务 ID |
| ledger_start_sequence_number | bigint | 创建行版本的事务中的操作序列号 |
| ledger_end_sequence_number | bigint | 删除行版本的事务中的操作序列号 |

## <a name="history-table"></a>历史记录表

创建可更新账本表时，将自动创建历史记录表。 历史记录表将捕获由于可更新账本表中的更新和删除操作而更改的行的历史值。 历史记录表的架构反映了与其关联的可更新账本表的架构。

创建可更新账本表时，可指定要包含历史记录表的架构的名称和历史记录表的名称，也可以让系统生成历史记录表的名称，并将其添加到与账本表相同的架构中。 具有系统生成的名称的历史记录表称为匿名历史记录表。 匿名历史记录表的命名约定为 `<schema>`.`<updatableledgertablename>`.MSSQL_LedgerHistoryFor_`<GUID>`。

## <a name="ledger-view"></a>账本视图

对于每个可更新账本表，系统会自动生成一个名为“账本视图”的视图。 账本视图是可更新账本表及其关联的历史记录表的联接。 账本视图通过联接历史记录表中的历史数据，报告可更新账本表上发生的所有行修改。 该视图使用户、其合作伙伴或审核员可以分析所有历史操作并检测潜在的篡改。 每个行操作都伴随操作事务的 ID，以及操作是 `DELETE` 还是 `INSERT`。 用户可以检索有关执行事务的时间、执行此事务的用户标识的详细信息，并将其与此事务执行的其他操作关联起来。

例如，如果想要跟踪银行业务场景中的事务历史记录，账本视图将提供一段时间内的事务历史记录。 使用账本视图，无需单独查看可更新账本表和历史记录表，也无需为此构建自己的视图。

有关使用账本视图的示例，请参阅[创建和使用可更新账本表](ledger-how-to-updatable-ledger-tables.md)。

账本视图的架构将反映可更新账本和历史记录表中定义的列，但 [GENERATED ALWAYS](/sql/t-sql/statements/create-table-transact-sql#generate-always-columns) 列不同于可更新账本和历史记录表的列。

### <a name="ledger-view-schema"></a>账本视图架构

> [!NOTE]
> 创建表时，可结合使用 `<ledger_view_option>` 参数和 [CREATE TABLE (Transact-SQL)](/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current&preserve-view=true) 语句来自定义账本视图的列名。 有关详细信息，请参阅 [CREATE TABLE (Transact-SQL)](/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current&preserve-view=true) 中的[账本视图选项](/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current&preserve-view=true#ledger-view-options)和相应的示例。

| 默认列名称 | 数据类型 | 说明 |
| --- | --- | --- |
| ledger_transaction_id | bigint | 创建或删除行版本的事务 ID。 |
| ledger_sequence_number | bigint | 表中事务内的行级别操作的序列号。 |
| ledger_operation_type_id | tinyint | 包含 `0` (INSERT) 或 `1` (DELETE) 。 在账本表中插入行会在此列包含 `0` 的账本视图中生成一个新行。 从账本表中删除行会在此列包含 `1` 的账本视图中生成一个新行。 更新账本表中的行在账本视图中生成两个新行。 在此列中，一行包含 `1`(DELETE)，另一行包含 `1`(INSERT)。 |
| ledger_operation_type_desc | nvarchar(128) | 包含 `INSERT` 或 `DELETE`。 有关详细信息，请参阅上一行。 |

## <a name="next-steps"></a>后续步骤
 
- [创建和使用可更新账本表](ledger-how-to-updatable-ledger-tables.md)
- [创建和使用仅追加账本表](ledger-how-to-append-only-ledger-tables.md) 
