---
title: 验证账本表以检测篡改
description: 本文讨论如何验证 Azure SQL 数据库表是否已被篡改。
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: how-to
author: JasonMAnderson
ms.author: janders
ms.reviewer: vanto
ms.date: 07/23/2021
ms.custom: references_regions
ms.openlocfilehash: 7684cc6ecff0d4e36fec0d7df54edd8602aa145f
ms.sourcegitcommit: d9a2b122a6fb7c406e19e2af30a47643122c04da
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2021
ms.locfileid: "114665487"
---
# <a name="verify-a-ledger-table-to-detect-tampering"></a>验证账本表以检测篡改

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Azure SQL 数据库账本目前为公共预览版，在欧洲西部、巴西南部和美国中西部提供。

本文将验证 Azure SQL 数据库账本表中的数据的完整性。 如果在 [SQL 数据库中创建数据库](ledger-create-a-single-database-with-ledger-enabled.md)时已选中“启用自动摘要存储”，请按照 Azure 门户的说明自动生成在[查询编辑器](connect-query-portal.md)中验证数据库账本时所需的 Transact-SQL (T-SQL) 脚本。 否则，请按照 T-SQL 的说明使用 [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) 或 [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio)。

## <a name="prerequisites"></a>先决条件

- 拥有一个有效的 Azure 订阅。 如果没有帐户，请[创建一个免费帐户](https://azure.microsoft.com/free/)。
- [在 SQL 数据库中创建启用了账本的数据库](ledger-create-a-single-database-with-ledger-enabled.md)。
- [创建和使用可更新的账本表](ledger-how-to-updatable-ledger-tables.md)或[创建和使用仅追加账本表](ledger-how-to-append-only-ledger-tables.md)。

## <a name="run-ledger-verification-for-sql-database"></a>运行 SQL 数据库的账本验证

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. 打开 [Azure 门户](https://portal.azure.com/)选择“所有资源”并找到要验证的数据库。 选择 SQL 数据库中的数据库。

     :::image type="content" source="media/ledger/ledger-portal-all-resources.png" alt-text="显示 Azure 门户的屏幕截图，其中选择了“所有资源”选项卡。":::

1. 在“安全性”中，选择“账本”选项 。

   :::image type="content" source="media/ledger/ledger-portal-manage-ledger.png" alt-text="显示 Azure 门户的屏幕截图，其中选择了“安全账本”选项卡。":::

1. 在“账本”窗格中，选择“</> 验证数据库”，然后在窗口中选择预填充文本中的“复制”图标。

     :::image type="content" source="media/ledger/ledger-portal-verify.png" alt-text="Azure 门户验证数据库按钮":::

   > > [!IMPORTANT]
   > 如果尚未为数据库摘要配置自动摘要存储，而是手动管理摘要，请不要复制此脚本。 继续执行步骤 6。

1. 在左侧菜单中，打开“查询编辑器”。

     :::image type="content" source="media/ledger/ledger-portal-open-query-editor.png" alt-text="显示 Azure 门户“查询编辑器”菜单选项的屏幕截图。":::

1. 在“查询编辑器”中，粘贴在步骤 3 中复制的 T-SQL 脚本，然后选择“运行”。 继续执行步骤 8。

   :::image type="content" source="media/ledger/ledger-portal-run-query-editor.png" alt-text="显示 Azure 门户运行查询编辑器以验证数据库的屏幕截图。":::

1. 如果使用手动摘要存储，请在查询编辑器中输入以下 SQL 以检索最新的数据库摘要。 复制为下一步返回的结果中的摘要。

   ```sql
   EXECUTE sp_generate_database_ledger_digest
   ```
   
1. 在查询编辑器中，粘贴以下 T-SQL，将 `<database_digest>` 替换为在步骤 6 中复制的摘要，然后选择“运行”。

   ```sql
   EXECUTE sp_verify_database_ledger N'<database_digest>'
   ```

1. 验证在“结果”窗口中返回以下消息。

   - 如果没有对数据库进行篡改，消息将如下所示：

       ```output
       Ledger verification successful
       ```

   - 如果对数据库进行了篡改，“消息”窗口将显示以下错误。
  
       ```output
       Failed to execute query. Error: The hash of block xxxx in the database ledger does not match the hash provided in the digest for this block.
       ```

# <a name="t-sql-using-automatic-digest-storage"></a>[使用自动摘要存储的 T-SQL](#tab/t-sql-automatic)

1. 使用 [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) 或 [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) 连接到数据库。

1. 使用以下 T-SQL 语句创建新查询：

   ```sql
   DECLARE @digest_locations NVARCHAR(MAX) = (SELECT * FROM sys.database_ledger_digest_locations FOR JSON AUTO, INCLUDE_NULL_VALUES);SELECT @digest_locations as digest_locations;
   BEGIN TRY
       EXEC sys.sp_verify_database_ledger_from_digest_storage @digest_locations;
       SELECT 'Ledger verification succeeded.' AS Result;
   END TRY
   BEGIN CATCH
       THROW;
   END CATCH
   ```

1. 执行查询。 你将看到 digest_locations 返回存储存储数据库摘要的当前位置和任何以前的位置。 “结果”返回账本验证成功或失败消息。

   :::image type="content" source="media/ledger/verification_script_exectution.png" alt-text="使用 Azure Data Studio 运行账本验证的屏幕截图。":::

1. 打开 digest_locations 结果集以查看摘要的位置。 以下示例显示了此数据库的两个摘要存储位置： 

   - path 指示摘要的位置。
   - last_digest_block_id 指示 path 位置中存储的最后一个摘要的块 ID。
   - is_current 指示 path 中的位置是当前位置 (true) 还是之前的位置 (false)。

       ```json
       [
        {
            "path": "https:\/\/digest1.blob.core.windows.net\/sqldbledgerdigests\/janderstestportal2server\/jandersnewdb\/2021-05-20T04:39:47.6570000",
            "last_digest_block_id": 10016,
            "is_current": true
        },
        {
            "path": "https:\/\/jandersneweracl.confidential-ledger.azure.com\/sqldbledgerdigests\/janderstestportal2server\/jandersnewdb\/2021-05-20T04:39:47.6570000",
            "last_digest_block_id": 1704,
            "is_current": false
        }
       ]
       ```

   > [!IMPORTANT]
   > 运行账本验证时，请检查 digest_locations 的位置，以确保从预期位置检索验证中使用的摘要。 你希望确保特权用户在没有配置和锁定的不可变策略的情况下未将摘要存储的位置更改为不受保护的存储位置，例如 Azure 存储。

1. 验证在“结果”窗口中返回以下消息。

   - 如果没有对数据库进行篡改，消息将如下所示：

       ```output
       Ledger verification successful
       ```

   - 如果对数据库进行了篡改，“消息”窗口将显示以下错误：
  
       ```output
       Failed to execute query. Error: The hash of block xxxx in the database ledger doesn't match the hash provided in the digest for this block.
       ```

# <a name="t-sql-using-manual-digest-storage"></a>[使用手动摘要存储的 T-SQL](#tab/t-sql-manual)

1. 使用 [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) 或 [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) 连接到数据库。
1. 使用以下 T-SQL 语句创建新查询：

   ```sql
   /****** This will retrieve the latest digest file  ******/
   EXECUTE sp_generate_database_ledger_digest
   ```

1. 执行查询。 结果包含最新的数据库摘要，并表示当前时间点的数据库哈希。 复制要在下一步中使用的结果的内容。

   :::image type="content" source="media/ledger/ledger-retrieve-digest.png" alt-text="此屏幕截图显示使用 Azure Data Studio 检索摘要结果。":::

1. 使用以下 T-SQL 语句创建新查询。 将 `<YOUR DATABASE DIGEST>` 替换为在上一步骤中复制的摘要。

   ```
   /****** Verifies the integrity of the ledger using the referenced digest  ******/
   EXECUTE sp_verify_database_ledger N'
   <YOUR DATABASE DIGEST>
   '
   ```

1. 执行查询。 “消息”窗口包含以下成功消息。

   :::image type="content" source="media/ledger/ledger-verify-message.png" alt-text="此屏幕截图显示使用 Azure Data Studio 运行 T-SQL 查询进行账本验证后的消息。":::

   > [!TIP]
   > 使用最新的摘要运行账本验证，将只验证生成摘要到运行验证期间的数据库。 若要验证数据库中的历史数据是否未被篡改，请使用多个数据库摘要文件运行验证。 从要验证数据库的时间点开始。 传递多个摘要的验证示例类似于以下查询。

   ```
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
   ```

---

## <a name="next-steps"></a>后续步骤

- [Azure SQL 数据库账本概述](ledger-overview.md)
- [SQL 数据库账本](ledger-database-ledger.md)
- [摘要管理和数据库验证](ledger-digest-management-and-database-verification.md)
- [仅追加账本表](ledger-append-only-ledger-tables.md)
- [可更新账本表](ledger-updatable-ledger-tables.md)
- [访问存储在 Azure 机密账本中的摘要](ledger-how-to-access-acl-digest.md)
