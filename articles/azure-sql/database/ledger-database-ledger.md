---
title: 数据库账本
description: 本文提供有关 Azure SQL 数据库中账本数据库表和关联视图的信息
ms.custom: ''
ms.date: 05/25/2021
ms.service: sql-database
ms.subservice: security
ms.reviewer: vanto
ms.topic: conceptual
author: JasonMAnderson
ms.author: janders
ms.openlocfilehash: 031674854af41877483ece5c3969a0208a7a8167
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2021
ms.locfileid: "110385337"
---
# <a name="what-is-the-database-ledger"></a>什么是数据库账本

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Azure SQL 数据库账本目前为公共预览版。

数据库账本在逻辑上使用区块链和 [Merkle 树数据结构](/archive/msdn-magazine/2018/march/blockchain-blockchain-fundamentals)。 数据库账本随着时间推移不断捕获数据库的状态，同时账本表会进行更新。 为实现此目的，数据库账本存储每个事务的条目，捕获关于事务的元数据，例如事务的提交时间戳和执行事务的用户的标识，以及在每个账本表中更新的行的 Merkle 树根。 然后将这些条目追加到防篡改的数据结构，以便将来允许对完整性进行验证。

:::image type="content" source="media/ledger/merkle-tree.png" alt-text="sql 账本 merkle 树":::

有关 Azure SQL 数据库账本如何提供数据完整性的详细信息，请参阅[摘要管理和数据库验证](ledger-digest-management-and-database-verification.md)。

## <a name="where-are-database-transaction-and-block-data-stored"></a>数据库事务和块数据存储在哪里？

关于事务和块的数据在物理上存储为两个新系统目录视图中的行：

- [**sys.database_ledger_transactions**](/sql/relational-databases/system-catalog-views/sys-database-ledger-transactions-transact-sql) - 维护一行，该行包含账本中每个事务的信息，包括此事务所属的块的 ID 以及此事务在该块中的序号。 
- [**sys.database_ledger_blocks**](/sql/relational-databases/system-catalog-views/sys-database-ledger-blocks-transact-sql) - 为账本中的每个块保留一行，包括针对块内事务的 Merkle 树的根，以及形成区块链的上一个块的哈希。

若要查看数据库账本，请在 [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) 或 [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) 中执行以下 T-SQL 语句。

> [!IMPORTANT]
> 查看数据库账本需要“查看账本内容”权限。 有关与账本表相关的权限的详细信息，请参阅[权限](/sql/relational-databases/security/permissions-database-engine#asdbpermissions)。 

```sql
SELECT * FROM sys.database_ledger_transactions
GO

SELECT * FROM sys.database_ledger_blocks
GO
```

以下是包含四个事务的账本表示例，这些事务在数据库账本的区块链中组成了一个块。

:::image type="content" source="media/ledger/database-ledger-1.png" alt-text="示例账本表":::

块每 30 秒就会关闭，或在用户通过执行 [sys.sp_generate_database_ledger_digest](/sql/relational-databases/system-stored-procedures/sys-sp-generate-database-ledger-digest-transact-sql) 存储过程手动生成数据库摘要时关闭。 关闭块后，新事务将被插入新的块中。 块生成进程然后从内存中的队列和 [sys.database_ledger_transactions](/sql/relational-databases/system-catalog-views/sys-database-ledger-transactions-transact-sql) 系统目录视图检索属于已关闭块的所有事务，计算这些事务上的 Merkle 树根和上一个块的哈希，并在 [sys.database_ledger_blocks](/sql/relational-databases/system-catalog-views/sys-database-ledger-blocks-transact-sql) 系统目录视图中保留已关闭的块。 由于这是一个常规的表更新，系统会自动保证其持久性。 为了维护单个块链，此操作是单线程的，但也是高效的，因为它只计算事务信息上的哈希，并且异步发生，因此不会影响事务性能。   

## <a name="next-steps"></a>后续步骤

- [摘要管理和数据库验证](ledger-digest-management-and-database-verification.md)
- [Azure SQL 数据库账本概述](ledger-overview.md) 
- [安全性目录视图 (Transact-SQL)](/sql/relational-databases/system-catalog-views/security-catalog-views-transact-sql)
