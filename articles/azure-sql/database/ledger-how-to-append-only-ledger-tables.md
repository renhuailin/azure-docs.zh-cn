---
title: 创建和使用仅追加账本表
description: 了解如何在 Azure SQL 数据库中创建和使用仅追加账本表。
ms.custom: references_regions
ms.date: 07/23/2021
ms.service: sql-database
ms.subservice: security
ms.reviewer: vanto
ms.topic: how-to
author: JasonMAnderson
ms.author: janders
ms.openlocfilehash: fede72baa7df89e821e496066090658bda2df80e
ms.sourcegitcommit: d9a2b122a6fb7c406e19e2af30a47643122c04da
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2021
ms.locfileid: "114665838"
---
# <a name="create-and-use-append-only-ledger-tables"></a>创建和使用仅追加账本表

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Azure SQL 数据库账本目前为公共预览版，在欧洲西部、巴西南部和美国中西部提供。

本文介绍如何在 Azure SQL 数据库中创建[仅追加账本表](ledger-append-only-ledger-tables.md)。 随后，你可在仅追加账本表中插入值，然后尝试对数据进行更新。 最后，可使用账本视图查看结果。 我们将以设施的卡密钥访问系统为例，这是一种仅追加系统模式。 我们的示例将让你实际了解仅追加账本表及其对应的账本视图之间的关系。

有关详细信息，请参阅[仅追加账本表](ledger-append-only-ledger-tables.md)。

## <a name="prerequisites"></a>先决条件

- 已启用账本的 Azure SQL 数据库。 如果尚未在 SQL 数据库中创建数据库，请参阅[快速入门：在已启用账本的 Azure SQL 数据库中创建数据库](ledger-create-a-single-database-with-ledger-enabled.md)。
- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) 或 [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio)。

## <a name="create-an-append-only-ledger-table"></a>创建仅追加账本表

我们将创建具有以下架构的 `KeyCardEvents` 表。

| 列名称 | 数据类型 | 说明 |
|--|--|--|
| EmployeeID | int | 访问建筑物的员工的唯一 ID |
| AccessOperationDescription | nvarchar (MAX) | 员工的访问操作 |
| Timestamp | datetime2 | 员工访问建筑物的日期和时间 |

> [!IMPORTANT]
> 创建仅追加账本表需要 ENABLE LEDGER 权限。 有关与账本表相关的权限的详细信息，请参阅[权限](/sql/relational-databases/security/permissions-database-engine#asdbpermissions)。 

1. 使用 [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) 或 [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) 创建名为 `[AccessControl].[KeyCardEvents]` 的新架构和表。

   ```sql
   CREATE SCHEMA [AccessControl] 
   CREATE TABLE [AccessControl].[KeyCardEvents]
       (
           [EmployeeID] INT NOT NULL,
           [AccessOperationDescription] NVARCHAR (MAX) NOT NULL,
           [Timestamp] Datetime2 NOT NULL
       )
       WITH (
          LEDGER = ON (
                       APPEND_ONLY = ON
                       )
         );
   ```

1. 使用以下值将新建筑物访问事件添加到 `[AccessControl].[KeyCardEvents]` 表中。

   ```sql
   INSERT INTO [AccessControl].[KeyCardEvents]
   VALUES ('43869', 'Building42', '2020-05-02T19:58:47.1234567')
   ```

1. 查看 KeyCardEvents 表的内容，并指定添加到[仅追加账本表](ledger-append-only-ledger-tables.md)的 [GENERATED ALWAYS](/sql/t-sql/statements/create-table-transact-sql#generate-always-columns) 列。

   ```sql
   SELECT *
        ,[ledger_start_transaction_id]
        ,[ledger_start_sequence_number]
   FROM [AccessControl].[KeyCardEvents]
   ```

   :::image type="content" source="media/ledger/append-only-how-to-keycardevent-table.png" alt-text="屏幕截图，其中显示查询 KeyCardEvents 表的结果。":::

1. 尝试通过将 `EmployeeID` 从 `43869` 更改为 `34184.` 来更新 `KeyCardEvents` 表

   ```sql
   UPDATE [AccessControl].[KeyCardEvents] SET [EmployeeID] = 34184
   ```

   你将收到一条错误消息，其中指出不允许对仅追加账本表进行更新。

   :::image type="content" source="media/ledger/append-only-how-to-1.png" alt-text="屏幕截图，其中显示仅追加错误消息。":::

## <a name="next-steps"></a>后续步骤

- [数据库账本](ledger-database-ledger.md) 
- [摘要管理和数据库验证](ledger-digest-management-and-database-verification.md)
- [仅追加账本表](ledger-append-only-ledger-tables.md) 
- [可更新账本表](ledger-updatable-ledger-tables.md)
- [创建和使用可更新账本表](ledger-how-to-updatable-ledger-tables.md)
- [访问存储在 Azure 机密账本 (ACL) 中的摘要](ledger-how-to-access-acl-digest.md)
- [验证账本表以检测篡改](ledger-verify-database.md)
