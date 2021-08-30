---
title: 摘要管理和数据库验证
description: 本文提供了有关 Azure SQL 数据库中账本数据库的摘要管理和数据库验证的信息。
ms.custom: references_regions
ms.date: 07/23/2021
ms.service: sql-database
ms.subservice: security
ms.reviewer: vanto
ms.topic: conceptual
author: JasonMAnderson
ms.author: janders
ms.openlocfilehash: 8e6fbbdcb4b6db8ed7e9549b8776010cf01894e4
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121744868"
---
# <a name="digest-management-and-database-verification"></a>摘要管理和数据库验证

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Azure SQL 数据库账本目前为公共预览版，在西欧、巴西南部和美国中西部提供。

Azure SQL 数据库账本提供一种称为“前向完整性”的数据完整性，它提供对账本表中数据的数据篡改的证据。 例如，如果某一银行交易发生在已将余额更新为 `x` 值的账本表中，攻击者稍后修改数据，将余额从 `x` 更改为 `y`，数据库验证将检测到此篡改活动。  

数据库验证过程使用以前生成的一个或多个数据库摘要作为输入。 然后根据账本表的当前状态，该过程重新计算存储在数据库账本中的哈希值。 如果计算所得的哈希值与输入摘要不符，则验证将失败。 该失败指示数据已被篡改。 验证过程会报告它所检测到的所有不一致项。

## <a name="database-digests"></a>数据库摘要

数据库账本中最新块的哈希称为 *数据库摘要*。 它表示生成块时数据库中所有账本表的状态。 生成数据库摘要很有效，因为它只涉及计算最近追加的块的哈希。 

数据库摘要可以由系统自动生成，也可以由用户手动生成。 你可在稍后将其用于验证数据库的完整性。 

数据库摘要以 JSON 文档的形式生成，其中包含最新块的哈希以及与块 ID 相关的元数据。 元数据包括生成摘要的时间，以及此块中最后一个事务的提交时间戳。

验证过程和数据库的完整性取决于输入摘要的完整性。 为此，需要将提取自数据库的数据库摘要存储在受信任的存储中，使其无法被 Azure SQL 数据库服务器的高特权用户或攻击者篡改。

### <a name="automatic-generation-and-storage-of-database-digests"></a>数据库摘要的自动生成和存储

Azure SQL 数据库账本与 [Azure Blob 存储的不可变存储功能](../../storage/blobs/immutable-storage-overview.md)或 [Azure 机密账本](../../confidential-ledger/index.yml)集成。 此集成在 Azure 中提供安全存储服务，防止数据库摘要遭受潜在篡改。 此集成为用户提供了一种简单且经济高效的方法，可以自动执行摘要管理，而无需担心其可用性和地理复制。 

可通过 Azure 门户、PowerShell 或 Azure CLI 配置数据库摘要的自动生成和存储。 配置自动生成和存储后，数据库摘要会按预定义的间隔（30 秒）生成，并上传到所选的存储服务。 如果在 30 秒时间间隔内，系统中未发生任何事务，则不会生成和上传数据库摘要。 此机制可确保仅在数据库中更新数据时生成数据库摘要。

:::image type="content" source="media/ledger/automatic-digest-management.png" alt-text="显示用于启用摘要存储的选项的屏幕截图。"::: 

> [!IMPORTANT]
> 预配后，应在容器上配置[不可变策略](../../storage/blobs/immutable-policy-configure-version-scope.md)，确保防止数据库摘要被篡改。

### <a name="manual-generation-and-storage-of-database-digests"></a>数据库摘要的手动生成和存储

还可使用 Azure SQL 数据库账本按需生成数据库摘要，以便可以手动将摘要存储在你认为受信任的存储目标的任何服务或设备中。 例如，你可能选择本地一次写入多次读取 (WORM) 设备作为目标。 可在 [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) 或 [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) 中运行 [sys.sp_generate_database_ledger_digest](/sql/relational-databases/system-stored-procedures/sys-sp-generate-database-ledger-digest-transact-sql) 存储过程手动生成数据库摘要。

> [!IMPORTANT]
> 生成数据库摘要需要“生成账本摘要”权限。 有关与账本表相关的权限的详细信息，请参阅[权限](/sql/relational-databases/security/permissions-database-engine#asdbpermissions)。 

```sql
EXECUTE sp_generate_database_ledger_digest
```

返回的结果集为单行数据。 应以 JSON 文档的形式将其保存到受信任的存储位置，如下所示：

```json
    {
        "database_name":  "ledgerdb",
        "block_id":  0,
        "hash":  "0xDC160697D823C51377F97020796486A59047EBDBF77C3E8F94EEE0FFF7B38A6A",
        "last_transaction_commit_time":  "2020-11-12T18:01:56.6200000",
        "digest_time":  "2020-11-12T18:39:27.7385724"
    }
```

## <a name="database-verification"></a>数据库验证

验证过程会扫描所有账本和历史记录表。 该过程重新计算其行的 SHA-256 哈希，并将它们与传递到验证存储过程的数据库摘要文件进行比较。 

对于大型账本表，数据库验证过程可能会占用大量资源。 因此，应仅在需要验证数据库的完整性时才使用此过程。 

如果需要频繁监视数据库的完整性，可以每小时或每日执行一次验证过程。 或仅当托管数据的组织经历审核并需要提供有关其数据完整性的加密证据时，才执行此过程。 为了降低验证成本，账本提供用于验证单个账本表的选项，或仅账本表子集的选项。 

数据库验证可通过两个存储过程来完成，具体取决于是[使用自动摘要存储](#database-verification-that-uses-automatic-digest-storage)还是[手动管理摘要](#database-verification-that-uses-manual-digest-storage)。

> [!IMPORTANT]
> 数据库验证需要“查看账本内容”权限。 有关与账本表相关的权限的详细信息，请参阅[权限](/sql/relational-databases/security/permissions-database-engine#asdbpermissions)。 

### <a name="database-verification-that-uses-automatic-digest-storage"></a>使用自动摘要存储的数据库验证

使用自动摘要存储来生成和存储数据库摘要时，摘要存储的位置在系统目录视图 [sys.database_ledger_digest_locations](/sql/relational-databases/system-catalog-views/sys-database-ledger-digest-locations-transact-sql) 中作为 JSON 对象。 运行数据库验证包括执行 [sp_verify_database_ledger_from_digest_storage](/sql/relational-databases/system-stored-procedures/sys-sp-verify-database-ledger-from-digest-storage-transact-sql) 系统存储过程。 在 [sys.database_ledger_digest_locations](/sql/relational-databases/system-catalog-views/sys-database-ledger-digest-locations-transact-sql) 系统目录视图中指定 JSON 对象，数据库摘要在该视图中被配置为存储。 

使用自动摘要存储时，可以在账本表的整个生命周期内更改存储位置。  例如，如果首先使用 Azure 不可变存储来存储摘要文件，但以后想要改为使用 Azure 机密账本，则可以这样做。 位置中的此更改存储在 [sys.database_ledger_digest_locations](/sql/relational-databases/system-catalog-views/sys-database-ledger-digest-locations-transact-sql)。 

为了简化使用多个摘要存储位置时运行的验证，以下脚本将提取摘要的位置，并使用这些位置执行验证。

```sql
DECLARE @digest_locations NVARCHAR(MAX) = (SELECT * FROM sys.database_ledger_digest_locations FOR JSON AUTO, INCLUDE_NULL_VALUES);
SELECT @digest_locations as digest_locations;
BEGIN TRY
    EXEC sys.sp_verify_database_ledger_from_digest_storage @digest_locations;
    SELECT 'Ledger verification succeeded.' AS Result;
END TRY
BEGIN CATCH
    THROW;
END CATCH
```

### <a name="database-verification-that-uses-manual-digest-storage"></a>使用手动摘要存储的数据库验证

使用手动摘要存储生成和存储数据库摘要时，可采用以下存储过程来验证账本数据库。 摘要的 JSON 内容被追加到存储过程中。 运行数据库验证时，可以选择验证数据库中的所有表或特定表。 

下面是 [sp_verify_database_ledger](/sql/relational-databases/system-stored-procedures/sys-sp-verify-database-ledger-transact-sql) 存储过程的语法：

```sql
sp_verify_database_ledger <JSON_document_containing_digests>, <table_name> 
```

下面的代码是传递两个摘要进行验证以运行 [sp_verify_database_ledger](/sql/relational-databases/system-stored-procedures/sys-sp-verify-database-ledger-transact-sql) 存储过程的示例： 

```sql
EXECUTE sp_verify_database_ledger N'
[
    {
        "database_name":  "ledgerdb",
        "block_id":  0,
        "hash":  "0xDC160697D823C51377F97020796486A59047EBDBF77C3E8F94EEE0FFF7B38A6A",
        "last_transaction_commit_time":  "2020-11-12T18:01:56.6200000",
        "digest_time":  "2020-11-12T18:39:27.7385724"
    },
    {
        "database_name":  "ledgerdb",
        "block_id":  1,
        "hash":  "0xE5BE97FDFFA4A16ADF7301C8B2BEBC4BAE5895CD76785D699B815ED2653D9EF8",
        "last_transaction_commit_time":  "2020-11-12T18:39:35.6633333",
        "digest_time":  "2020-11-12T18:43:30.4701575"
    }
]
'
```

`sp_verify_database_ledger` 和 `sp_verify_database_ledger_from_digest_storage` 的返回代码是 `0`（成功）或 `1`（失败）。

## <a name="next-steps"></a>后续步骤

- [Azure SQL 数据库账本概述](ledger-overview.md)
- [可更新账本表](ledger-updatable-ledger-tables.md)   
- [仅追加账本表](ledger-append-only-ledger-tables.md)   
- [数据库账本](ledger-database-ledger.md)