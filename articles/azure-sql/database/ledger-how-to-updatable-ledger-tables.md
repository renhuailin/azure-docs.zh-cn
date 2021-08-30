---
title: 创建和使用可更新的账本表
description: 了解如何在 Azure SQL 数据库中创建和使用可更新的账本表。
ms.custom: references_regions
ms.date: 07/23/2021
ms.service: sql-database
ms.subservice: security
ms.reviewer: vanto
ms.topic: how-to
author: JasonMAnderson
ms.author: janders
ms.openlocfilehash: e4027bcaba4e89e89bec2ffa45d50c94e9a07b12
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121739700"
---
# <a name="create-and-use-updatable-ledger-tables"></a>创建和使用可更新的账本表

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Azure SQL 数据库账本目前为公共预览版，在欧洲西部、巴西南部和美国中西部提供。

本文介绍如何在 Azure SQL 数据库中创建[可更新的账本表](ledger-updatable-ledger-tables.md)。 随后，你可在可更新的账本表中插入值，然后对数据进行更新。 最后，可使用账本视图查看结果。 我们将使用示例银行应用程序跟踪银行客户帐户中的余额。 我们的示例将让你实际了解可更新账本表及其相应的历史记录表和账本视图之间的关系。

## <a name="prerequisites"></a>先决条件

- 已启用账本的 Azure SQL 数据库。 如果尚未在 SQL 数据库中创建数据库，请参阅[快速入门：在已启用账本的 Azure SQL 数据库中创建数据库](ledger-create-a-single-database-with-ledger-enabled.md)。
- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) 或 [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio)。

## <a name="create-an-updatable-ledger-table"></a>创建可更新的账本表

我们将使用以下架构创建帐户余额表。

| 列名称 | 数据类型      | 说明                         |
| ----------- | -------------- | ----------------------------------- |
| CustomerID  | int            | 客户 ID - 聚合的主键 |
| LastName    | varchar (50)   | 客户姓氏                  |
| FirstName   | varchar (50)   | 客户名字                 |
| Balance     | decimal (10,2) | 帐户余额                     |

> [!IMPORTANT]
> 创建可更新的账本表需要“启用账本”权限。 有关与账本表相关的权限的详细信息，请参阅[权限](/sql/relational-databases/security/permissions-database-engine#asdbpermissions)。 

1. 使用 [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) 或 [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) 创建名为 `[Account].[Balance]` 的新架构和表。

   ```sql
   CREATE SCHEMA [Account]
   GO
   
   CREATE TABLE [Account].[Balance]
   (
       [CustomerID] INT NOT NULL PRIMARY KEY CLUSTERED,
       [LastName] VARCHAR (50) NOT NULL,
       [FirstName] VARCHAR (50) NOT NULL,
       [Balance] DECIMAL (10,2) NOT NULL
   )
   WITH 
   (
    SYSTEM_VERSIONING = ON,
    LEDGER = ON
   );
   GO
   ```

    > [!NOTE]
    > 如果在 SQL 数据库中创建数据库时已启用账本数据库，则指定 `LEDGER = ON` 参数是可选操作。
    >
    > 在上述示例中，系统将生成表中 [GENERATED ALWAYS](/sql/t-sql/statements/create-table-transact-sql#generate-always-columns) 列的名称、[账本视图](ledger-updatable-ledger-tables.md#ledger-view)的名称以及[账本视图列](ledger-updatable-ledger-tables.md#ledger-view-schema)的名称。
    >
    > 创建表时，可以将 `<ledger_view_option>` 参数和 [CREATE TABLE (Transact-SQL)](/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current&preserve-view=true) 语句配合使用来自定义账本视图的列名称。 可以自定义 `GENERATED ALWAYS` 列和[历史记录表](ledger-updatable-ledger-tables.md#history-table)的名称。 有关详细信息，请参阅 [CREATE TABLE (Transact-SQL)](/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current&preserve-view=true##x-creating-a-updatable-ledger-table) 中的[账本视图选项](/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current&preserve-view=true#ledger-view-options)和相应的示例。

1. 创建[可更新的账本表](ledger-updatable-ledger-tables.md)时，还会创建相应的历史记录表和账本视图。 运行以下 T-SQL 命令可查看新表和新视图。

   ```sql
   SELECT 
   ts.[name] + '.' + t.[name] AS [ledger_table_name]
   , hs.[name] + '.' + h.[name] AS [history_table_name]
   , vs.[name] + '.' + v.[name] AS [ledger_view_name]
   FROM sys.tables AS t
   JOIN sys.tables AS h ON (h.[object_id] = t.[history_table_id])
   JOIN sys.views v ON (v.[object_id] = t.[ledger_view_id])
   JOIN sys.schemas ts ON (ts.[schema_id] = t.[schema_id])
   JOIN sys.schemas hs ON (hs.[schema_id] = h.[schema_id])
   JOIN sys.schemas vs ON (vs.[schema_id] = v.[schema_id])
   ```

   :::image type="content" source="media/ledger/ledger-updatable-how-to-new-tables.png" alt-text="显示查询新账本表的屏幕截图。":::

1. 插入姓名 `Nick Jones`，作为开户余额为 50 美元的新客户。

   ```sql
   INSERT INTO [Account].[Balance]
   VALUES (1, 'Jones', 'Nick', 50)
   ```

1. 插入姓名 `John Smith`、`Joe Smith` 和 `Mary Michaels`，作为开户余额分别为 500、30 和 200 美元的新客户。

   ```sql
   INSERT INTO [Account].[Balance]
   VALUES (2, 'Smith', 'John', 500),
   (3, 'Smith', 'Joe', 30),
   (4, 'Michaels', 'Mary', 200)
   ```

1. 查看 `[Account].[Balance]` 可更新账本表，指定添加到该表的 [GENERATED ALWAYS](/sql/t-sql/statements/create-table-transact-sql#generate-always-columns) 列。

   ```sql
   SELECT * 
         ,[ledger_start_transaction_id]
         ,[ledger_end_transaction_id]
         ,[ledger_start_sequence_number]
         ,[ledger_end_sequence_number]
   FROM [Account].[Balance] 
   ```

   在结果窗口中，你将首先看到 T-SQL 命令插入的值，以及用于数据世系的系统元数据。

   - `ledger_start_transaction_id` 列记下与插入数据的事务相关联的唯一事务 ID。 因为 `John`、`Joe` 和 `Mary` 是使用同一事务插入的，因此它们共享相同的事务 ID。
   - `ledger_start_sequence_number` 列记下事务插入值的顺序。

      :::image type="content" source="media/ledger/sql-updatable-how-to-1.png" alt-text="显示账本表示例 1 的屏幕截图。":::

1. 将 `Nick` 的余额从 `50` 更新为 `100`。

   ```sql
   UPDATE [Account].[Balance] SET [Balance] = 100
   WHERE [CustomerID] = 1
   ```

1. 复制历史记录表的唯一名称。 下一步需要用到此信息。

   ```sql
   SELECT 
   ts.[name] + '.' + t.[name] AS [ledger_table_name]
   , hs.[name] + '.' + h.[name] AS [history_table_name]
   , vs.[name] + '.' + v.[name] AS [ledger_view_name]
   FROM sys.tables AS t
   JOIN sys.tables AS h ON (h.[object_id] = t.[history_table_id])
   JOIN sys.views v ON (v.[object_id] = t.[ledger_view_id])
   JOIN sys.schemas ts ON (ts.[schema_id] = t.[schema_id])
   JOIN sys.schemas hs ON (hs.[schema_id] = h.[schema_id])
   JOIN sys.schemas vs ON (vs.[schema_id] = v.[schema_id])
   ```

   :::image type="content" source="media/ledger/sql-updatable-how-to-2.png" alt-text="显示账本表示例 2 的屏幕截图。":::

1. 查看 `[Account].[Balance]` 可更新账本表及其相应的历史记录表和账本视图。

   > [!IMPORTANT]
   > 将 `<history_table_name>` 替换为在上一步复制的名称。

   ```sql
   SELECT * 
         ,[ledger_start_transaction_id]
         ,[ledger_end_transaction_id]
         ,[ledger_start_sequence_number]
         ,[ledger_end_sequence_number]
   FROM [Account].[Balance] 
   GO
   
   SELECT * FROM [<Your unique history table name>]
   GO 
   
   SELECT * FROM Account.Balance_Ledger
   ORDER BY ledger_transaction_id
   GO
   ```

   > [!TIP]
   > 建议通过[账本视图](ledger-updatable-ledger-tables.md#ledger-view)而不是[历史记录表](ledger-updatable-ledger-tables.md#history-table)查询更改历史记录。

1. `Nick` 的帐户余额已在可更新账本表中成功更新为 `100`。
1. 历史记录表现在显示 `Nick` 的上期余额为 `50`。
1. 账本视图显示，更新账本表时，对包含 `50` 的原始行执行 `DELETE` 操作。 在对包含 `100` 的新行执行相应的 `INSERT` 操作后，余额显示了 `Nick` 的新余额。

   :::image type="content" source="media/ledger/sql-updatable-how-to-3.png" alt-text="显示账本表示例 3 的屏幕截图。":::


## <a name="next-steps"></a>后续步骤

- [数据库账本](ledger-database-ledger.md)
- [摘要管理和数据库验证](ledger-digest-management-and-database-verification.md) 
- [可更新账本表](ledger-updatable-ledger-tables.md)
- [仅追加账本表](ledger-append-only-ledger-tables.md) 
- [创建和使用仅追加账本表](ledger-how-to-append-only-ledger-tables.md) 
- [访问存储在 Azure 机密账本 (ACL) 中的摘要](ledger-how-to-access-acl-digest.md)
- [验证账本表以检测篡改](ledger-verify-database.md)
