---
title: 使用日志重播服务将数据库迁移到 SQL 托管实例
description: 了解如何使用日志重播服务将数据库从 SQL Server 迁移到 SQL 托管实例
services: sql-database
ms.service: sql-managed-instance
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.reviewer: sstein
ms.date: 03/01/2021
ms.openlocfilehash: bc0dc72c7547c8f74aec53b7153fc5384c6b634b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101690781"
---
# <a name="migrate-databases-from-sql-server-to-sql-managed-instance-using-log-replay-service-preview"></a>使用日志重播服务 (预览将数据库从 SQL Server 迁移到 SQL 托管实例) 
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

本文介绍如何使用 LRS) 当前在公共预览版中使用日志重播 (服务，手动将数据库迁移从 SQL Server 2008-2019 到 SQL 托管实例。 这是一种基于 SQL Server 日志传送技术为托管实例启用的云服务。 如果存在复杂的自定义迁移和混合体系结构，则在需要更多控制时，如果停机时间存在极小的承受力，或者无法使用 Azure 数据迁移服务 (DM) ，则应使用 LRS。

DMS 和 LRS 都使用相同的基础迁移技术和相同的 Api。 在发布 LRS 的过程中，我们将进一步启用本地之间的复杂自定义迁移和混合体系结构。 SQL Server 和 SQL 托管实例。

## <a name="when-to-use-log-replay-service"></a>何时使用日志重播服务

在 [AZURE DMS](/azure/dms/tutorial-sql-server-to-managed-instance.md) 无法用于迁移的情况下，可通过 POWERSHELL、CLI CMDLET 或 API 直接使用 LRS 云服务，以手动生成并协调到 SQL 托管实例的数据库迁移。 

在以下某些情况下，你可能需要考虑使用 LRS 云服务：
- 你的数据库迁移项目需要更多控制
- 迁移转换的故障转移存在少许容错
- 无法在你的环境中安装 DMS 可执行文件
- DMS 可执行文件没有对数据库备份的文件访问权限
- 对主机操作系统的访问不可用，或没有管理员权限
- 无法打开从环境到 Azure 的网络端口
- 使用 TO URL 选项将备份直接存储在 Azure Blob 存储中
- 需要使用差异备份

> [!NOTE]
> 使用 Azure DMS 将数据库从 SQL Server 迁移到 SQL 托管实例的推荐自动方式。 在 NORECOVERY 模式下，此服务在后端使用相同的 LRS 云服务和日志传送。 当 Azure DMS 不完全支持你的方案时，应考虑使用 LRS 手动安排迁移。

## <a name="how-does-it-work"></a>工作原理

使用 LRS 生成自定义解决方案，将数据库迁移到云时，需要在下表中所示的几个业务流程步骤。

迁移包括在启用了校验和的 SQL Server 上进行完整的数据库备份，以及将备份文件复制到 Azure Blob 存储。 LRS 用于将备份文件从 Azure Blob 存储还原到 SQL 托管实例。 Azure Blob 存储用作 SQL Server 与 SQL 托管实例之间的中间存储。

LRS 将监视 Azure Blob 存储中的任何新差异，或还原完整备份后添加的日志备份，并将自动还原所有添加的新文件。 可以使用服务监视 SQL 托管实例上还原的备份文件的进度，如果需要，还可以中止该过程。

LRS 不需要特定的备份文件命名约定，因为它会扫描放置在 Azure Blob 存储上的所有文件，并且它仅从读取文件头构造备份链。 在迁移过程中，数据库处于 "正在还原" 状态，因为这些数据库在 [NORECOVERY](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql#comparison-of-recovery-and-norecovery) 模式下还原，并且在完成迁移过程之前不能用于读取或写入。 

迁移多个数据库时，需要将每个数据库的备份放置在 Azure Blob 存储上的单独文件夹中。 需要为每个数据库单独启动 LRS，需要指定不同的不同 Azure Blob 存储文件夹的路径。 

可以在 "自动完成" 或连续模式下启动 LRS。 在自动完成模式下启动时，如果已还原指定的上次备份文件名，则迁移将自动完成。 在连续模式下启动时，该服务将持续恢复添加的任何新备份文件，并且迁移将在手动切换时完成。 建议仅在执行最后的日志尾备份并在 SQL 托管实例上将其显示为已还原后，才执行手动转换。 最后一个转换步骤会使数据库联机，并可用于 SQL 托管实例上的 "读" 和 "写"。

LRS 停止后（自动完成时自动执行或切换时），将无法恢复 SQL 托管实例上联机的数据库的还原过程。 若要在通过自动完成完成迁移后还原其他备份文件，或者在切换时手动还原，则需要删除数据库，并重新启动 LRS，以从头开始整个备份链。

![针对 SQL 托管实例说明的日志重播服务协调步骤](./media/log-replay-service-migrate/log-replay-service-conceptual.png)
    
| Operation | 详细信息 |
| :----------------------------- | :------------------------- |
| **1. 将数据库备份从 SQL Server 复制到 Azure Blob 存储**。 | -使用 [Azcopy](/azure/storage/common/storage-use-azcopy-v10)或 [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)将完整备份、差异备份和日志备份从 SQL Server 复制到 Azure Blob 存储容器。 <br />-使用任何文件名，因为 LRS 不需要特定的文件命名约定。<br />-在迁移多个数据库时，每个数据库都需要一个单独的文件夹。 |
| **2. 在云中启动 LRS 服务**。 | -可通过选择 cmdlet 来启动服务： <br /> PowerShell [启动-azsqlinstancedatabaselogreplay](/powershell/module/az.sql/start-azsqlinstancedatabaselogreplay) <br /> CLI [az_sql_midb_log_replay_start cmdlet](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_start)。 <br /> -对指向 Azure Blob 存储上的不同备份文件夹的每个不同数据库单独启动 LRS。 <br />-启动后，服务将从 Azure Blob 存储容器获取备份，并开始在 SQL 托管实例上还原它们。<br /> -如果 LRS 在连续模式下启动，则在还原所有初始上传的备份后，该服务会监视上传到该文件夹的任何新文件，并且将基于 LSN 链持续应用日志，直到服务停止。 |
| **2.1. 监视操作进度**。 | -可以选择或 cmdlet 来监视还原操作的进度： <br /> PowerShell [azsqlinstancedatabaselogreplay](/powershell/module/az.sql/get-azsqlinstancedatabaselogreplay) <br /> CLI [az_sql_midb_log_replay_show cmdlet](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_show)。 |
| **2.2. 如果需要，Stop\abort 操作**。 | -如果迁移过程需要中止，则可以选择使用 cmdlet 来停止操作： <br /> PowerShell [azsqlinstancedatabaselogreplay]/powershell/module/az.sql/stop-azsqlinstancedatabaselogreplay)  <br /> CLI [az_sql_midb_log_replay_stop](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_stop) cmdlet。 <br /><br />-这将导致删除正在 SQL 托管实例上还原的数据库。 <br />-停止后，无法恢复数据库的 LRS。 必须从头开始迁移过程。 |
| **3. 准备就绪时转换到云**。 | -停止应用程序和工作负荷。 执行最后的日志尾备份，并上传到 Azure Blob 存储。<br /> -通过选择 cmdlet 启动 LRS 完成操作来完成转换： <br />PowerShell [azsqlinstancedatabaselogreplay](/powershell/module/az.sql/complete-azsqlinstancedatabaselogreplay) <br /> CLI [az_sql_midb_log_replay_complete](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_complete) cmdlet。 <br /><br />-这将导致停止 LRS 服务，并使数据库联机，以便在 SQL 托管实例上进行读取和写入。<br /> -将应用程序连接字符串从 SQL Server Repoint 到 SQL 托管实例。 |

## <a name="requirements-for-getting-started"></a>入门的要求

### <a name="sql-server-side"></a>SQL Server 侧
- SQL Server 2008-2019
-  (一个或多个文件的数据库的完整备份) 
-  (一个或多个文件的差异备份) 
- 事务日志文件的日志备份 (未拆分) 
- 必须为 (必需的备份 **启用校验和**) 

### <a name="azure-side"></a>Azure 端
- PowerShell Az .SQL module 版本2.16.0 或更高版本 ([安装](https://www.powershellgallery.com/packages/Az.Sql/)或使用 Azure [Cloud Shell](/azure/cloud-shell/)) 
- CLI 版本2.19.0 或更高版本 ([安装](/cli/azure/install-azure-cli)) 
- 已预配 Azure Blob 存储容器
- SAS 安全令牌，其中包含为 blob 存储容器生成的 " **读取** " 和 "仅 **列出** " 权限

### <a name="migrating-multiple-databases"></a>迁移多个数据库
- 不同数据库的备份文件必须位于 Azure Blob 存储的单独文件夹中。
- 对于指向 Azure Blob 存储中的相应文件夹的每个数据库，都必须单独启动 LRS。
- LRS 可以支持每个 SQL 托管实例最多支持100个同时还原进程。

### <a name="azure-rbac-permissions-required"></a>所需的 Azure RBAC 权限
通过所提供的客户端执行 LRS 需要以下 Azure 角色之一：
- “订阅所有者”角色或
- [托管实例参与者](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor) 角色，或
- 具有以下权限的自定义角色：
  - `Microsoft.Sql/managedInstances/databases/*`

## <a name="best-practices"></a>最佳做法

作为最佳做法，强烈建议执行以下操作：
- 运行 [数据迁移助手](/sql/dma/dma-overview) 以验证数据库是否已准备好迁移到 SQL 托管实例。 
- 将完整备份和差异备份拆分为多个文件，而不是一个文件。
- 启用备份压缩。
- 使用 Cloud Shell 执行脚本，因为它始终会更新到发布的最新 cmdlet 中。
- 计划在47小时内完成迁移，因为已启动 LRS 服务。 这是一个宽限期，在 LRS 启动后，它会阻止系统管理的软件修补程序。

> [!IMPORTANT]
> - 在迁移过程完成之前，不能使用 LRS 还原的数据库。
> - LRS 不支持在迁移期间对数据库进行只读访问。
> - 迁移完成后，迁移过程将终止，因为 LRS 不支持还原恢复。

## <a name="steps-to-execute"></a>要执行的步骤

### <a name="make-backups-on-the-sql-server"></a>在 SQL Server 上进行备份

可以通过以下两个选项之一创建 SQL Server 上的备份：

- 备份到本地磁盘存储，并将文件上传到 Azure Blob 存储，以防环境限制直接备份到 Azure Blob 存储。
- 如果你的环境和安全过程允许你这样做，则在 T-sql 中通过 "到 URL" 选项直接备份到 Azure Blob 存储。 

将想要迁移到完整恢复模式的数据库设置为允许日志备份。

```SQL
-- To permit log backups, before the full database backup, modify the database to use the full recovery
USE master
ALTER DATABASE SampleDB
SET RECOVERY FULL
GO
```

若要在本地存储上手动对数据库进行完整、差异和日志备份，请使用下面提供的示例 T-sql 脚本。 请确保已启用 CHECKSUM 选项，因为这是 LRS 的必需要求。

```SQL
-- Example on how to make full database backup to the local disk
BACKUP DATABASE [SampleDB]
TO DISK='C:\BACKUP\SampleDB_full.bak'
WITH INIT, COMPRESSION, CHECKSUM
GO

-- Example on how to make differential database backup to the locak disk
BACKUP DATABASE [SampleDB]
TO DISK='C:\BACKUP\SampleDB_diff.bak'
WITH DIFFERENTIAL, COMPRESSION, CHECKSUM
GO

-- Example on how to make the transactional log backup to the local disk
BACKUP LOG [SampleDB]
TO DISK='C:\BACKUP\SampleDB_log.trn'
WITH COMPRESSION, CHECKSUM
GO
```

### <a name="create-azure-blob-storage"></a>创建 Azure Blob 存储

Azure Blob 存储用作 SQL Server 与 SQL 托管实例之间备份文件的中间存储。 若要在存储帐户中创建新的存储帐户和 blob 容器，请执行以下步骤：

1. [创建存储帐户](../../storage/common/storage-account-create.md?tabs=azure-portal)
2. 创建存储帐户中[的 blob 容器](../../storage/blobs/storage-quickstart-blobs-portal.md)

### <a name="copy-backups-from-sql-server-to-azure-blob-storage"></a>将备份从 SQL Server 复制到 Azure Blob 存储

使用 LRS 将数据库迁移到托管实例时，可以使用以下方法将备份上传到 blob 存储：
- 使用 [Azcopy](/azure/storage/common/storage-use-azcopy-v10)或 [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer) 将备份上传到 blob 容器。
- 使用 Azure 门户中的存储资源管理器。

### <a name="make-backups-from-sql-server-directly-to-azure-blob-storage"></a>将 SQL Server 中的备份直接备份到 Azure Blob 存储

如果你的公司和网络策略允许，另一种方法是使用 SQL Server 本机 [备份到 URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url) "选项将备份从 SQL Server 直接备份到 Azure Blob 存储。 如果可以采用此选项，则不需要在本地存储上进行备份并将其上传到 Azure Blob 存储。

第一步，此操作需要为 Azure Blob 存储生成 SAS 身份验证令牌，并且令牌需要导入到 SQL Server 中。 第二步是在 T-sql 中通过 "TO URL" 选项进行备份。 确保所有备份都已启用 CHEKSUM 选项。

下面提供了用于将备份到 Azure Blob 存储的示例代码。 此示例不包括有关如何导入 SAS 令牌的说明。 以下教程提供了详细说明，包括如何生成 SAS 令牌并将其导入 SQL Server：将 [Azure Blob 存储服务与 SQL Server 配合使用](/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016#1---create-stored-access-policy-and-shared-access-storage)。 

```SQL
-- Example on how to make full database backup to URL
BACKUP DATABASE [SampleDB]
TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/SampleDB_full.bak'
WITH INIT, COMPRESSION, CHECKSUM
GO

-- Example on how to make differential database backup to URL
BACKUP DATABASE [SampleDB]
TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/SampleDB_diff.bak'  
WITH DIFFERENTIAL, COMPRESSION, CHECKSUM
GO

-- Example on how to make the transactional log backup to URL
BACKUP LOG [SampleDB]
TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/SampleDB_log.trn'  
WITH COMPRESSION, CHECKSUM
```

### <a name="generate-azure-blob-storage-sas-authentication-for-lrs"></a>生成适用于 LRS 的 Azure Blob 存储 SAS 身份验证

Azure Blob 存储用作 SQL Server 与 SQL 托管实例之间备份文件的中间存储。 需要生成包含 List 和 Read only 权限的 SAS 身份验证令牌，才能使用 LRS 服务。 这将使 LRS 服务能够访问 Azure Blob 存储，并使用备份文件将它们还原到 SQL 托管实例上。 请按照以下步骤为 LRS 使用生成 SAS 身份验证：

1. 从 Azure 门户访问存储资源管理器
2. 展开“Blob 容器”
3. 右键单击 blob 容器，然后选择 "获取共享访问签名  ![ 日志重播服务生成 SAS 身份验证令牌"](./media/log-replay-service-migrate/lrs-sas-token-01.png)
4. 选择令牌过期时间范围。 确保令牌在迁移期间有效。
5. 选择标记-UTC 或本地时间的时区
    - 令牌的时区和 SQL 托管实例可能不匹配。 请确保 SAS 令牌具有适当的时间有效性，同时考虑时区。 如果可能，请将时区设置为之前和之后的计划迁移时段。
6. 选择 "读取" 和 "仅列出权限"
    - 必须选择其他权限，否则 LRS 将无法启动。 此安全要求是设计的。
7. 单击 "创建" 按钮  ![ 日志重播服务生成 SAS 身份验证令牌](./media/log-replay-service-migrate/lrs-sas-token-02.png)

将使用前面指定的时间有效性生成 SAS 身份验证。 需要生成的令牌的 URI 版本-如以下屏幕截图中所示。

![日志重播服务生成的 SAS 身份验证 URI 示例](./media/log-replay-service-migrate/lrs-generated-uri-token.png)

### <a name="copy-parameters-from-sas-token-generated"></a>从生成的 SAS 令牌复制参数

为了能够正确地使用 SAS 令牌来启动 LRS，我们需要了解其结构。 生成的 SAS 令牌的 URI 包括两个部分：
- StorageContainerUri 和 
- StorageContainerSasToken，用问号分隔 (？ ) ，如下图所示。

    ![日志重播服务生成的 SAS 身份验证 URI 示例](./media/log-replay-service-migrate/lrs-token-structure.png)

- 第一部分以 "https://" 开始，直到问号 (？ ) 用于作为 LRS 输入的 StorageContainerURI 参数。 这会提供有关存储数据库备份文件的文件夹的 LRS 信息。
- 第二部分是在问号后开始 (？ ) ，在示例 "sp =" 中，一直到该字符串的末尾都是 StorageContainerSasToken 参数。 这是实际的签名身份验证令牌，在指定的时间内有效。 此部分不一定需要以 "sp =" 开头，如图所示，你的情况可能有所不同。

复制参数，如下所示：

1. 从 https://开始复制令牌的第一部分，直到问号 (？ ) 并在 PowerShell 或 CLI 中将其用作 StorageContainerUri 参数，以启动 LRS，如以下屏幕截图所示。

    ![日志重播服务复制 StorageContainerUri 参数](./media/log-replay-service-migrate/lrs-token-uri-copy-part-01.png)

2. 从问号开始，将标记的第二部分从问号开始 (？ ) ，直到字符串的末尾，并在 PowerShell 或 CLI 中将其用作 LRS 参数，如以下屏幕截图所示。

    ![日志重播服务复制 StorageContainerSasToken 参数](./media/log-replay-service-migrate/lrs-token-uri-copy-part-02.png)

> [!IMPORTANT]
> - 需要读取和列出 Azure Blob 存储的 SAS 令牌的权限。 如果为 SAS 身份验证令牌授予了其他任何权限，则启动 LRS 服务将会失败。 这些安全要求是由设计决定的。
> - 令牌必须具有相应的时间有效性。 请确保标记与托管实例之间的时间区域被视为。
> - 确保将 PowerShell 或 CLI 的 StorageContainerUri 参数从生成的令牌的 URI 复制到 https://中，直到问题标记 (？ ) 。 不要包含问号。
> 确保将 CLI 的 PowerShell 的 StorageContainerSasToken 参数从生成的令牌的 URI 中复制（从问号开始 (？ ) ，直到字符串的末尾。 不要包含问号。

### <a name="log-in-to-azure-and-select-subscription"></a>登录到 Azure 并选择 "订阅"

使用以下 PowerShell cmdlet 登录到 Azure：

```powershell
Login-AzAccount
```

使用以下 PowerShell cmdlet 选择 SQL 托管实例所在的相应订阅：

```powershell
Select-AzSubscription -SubscriptionId <subscription ID>
```

## <a name="start-the-migration"></a>开始迁移

通过启动 LRS 服务启动迁移。 服务可以在 "自动完成" 或连续模式下启动。 在自动完成模式下启动时，如果已还原指定的最后一个备份文件，则迁移将自动完成。 此选项要求 "启动" 命令指定上一个备份文件的文件名。 在连续模式下启动 LRS 时，服务将持续恢复添加的任何新备份文件，并且迁移将在手动切换时完成。 

### <a name="start-lrs-in-autocomplete-mode"></a>在自动完成模式下启动 LRS

若要在自动完成模式下启动 LRS 服务，请使用以下 PowerShell 或 CLI 命令。 指定包含-LastBackupName 参数的上一个备份文件名。 还原指定的上次备份文件名后，服务将自动启动转换。

在自动完成模式下启动 LRS-PowerShell 示例：

```PowerShell
Start-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName" `
    -Collation "SQL_Latin1_General_CP1_CI_AS" `
    -StorageContainerUri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D" `
    -AutoCompleteRestore `
    -LastBackupName "last_backup.bak"
```

在自动完成模式下启动 LRS-CLI 示例：

```CLI
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb -a --last-bn "backup.bak"
    --storage-uri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

### <a name="start-lrs-in-continuous-mode"></a>在连续模式下启动 LRS

在连续模式下启动 LRS-PowerShell 示例：

```PowerShell
Start-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName" `
    -Collation "SQL_Latin1_General_CP1_CI_AS" -StorageContainerUri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

在连续模式下启动 LRS-CLI 示例：

```CLI
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb
    --storage-uri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

### <a name="scripting-lrs-start-in-continuous-mode"></a>脚本 LRS 以连续模式启动

PowerShell 和 CLI 客户端在连续模式下启动 LRS 是同步的。 这意味着客户端将等待 API 响应报告成功或失败，以启动作业。 在此过程中，命令不会将控件返回给命令提示符。 如果正在编写迁移体验的脚本，并要求 LRS start 命令立即返回控制权，以便继续执行脚本的其余部分，可以使用-AsJob 交换机作为后台作业执行 PowerShell。 例如：

```PowerShell
$lrsjob = Start-AzSqlInstanceDatabaseLogReplay <required parameters> -AsJob
```

启动后台作业时，作业对象会立即返回，即使该作业需要很长时间才能完成。 当该作业运行时，你可以继续在此会话中工作而不会发生中断。 有关以后台作业的形式运行 PowerShell 的详细信息，请参阅 [PowerShell 启动-作业](/powershell/module/microsoft.powershell.core/start-job#description) 文档。

同样，若要在 Linux 上以后台进程的形式启动 CLI 命令，请使用 LRS 开始命令末尾) sign (&。

```CLI
az sql midb log-replay start <required parameters> &
```

> [!IMPORTANT]
> 启动 LRS 后，系统管理的任何软件修补程序将在接下来的47小时内停止。 在传递此窗口时，下一个自动软件修补程序将自动停止正在进行的 LRS。 在这种情况下，无法恢复迁移，需要从头开始重新启动。 

## <a name="monitor-the-migration-progress"></a>监视迁移进度

若要监视迁移操作的进度，请使用以下 PowerShell 命令：

```PowerShell
Get-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName"
```

若要监视迁移操作的进度，请使用以下 CLI 命令：

```CLI
az sql midb log-replay show -g mygroup --mi myinstance -n mymanageddb
```

## <a name="stop-the-migration"></a>停止迁移

如果需要停止迁移，请使用以下 cmdlet。 停止迁移将删除 SQL 托管实例上的还原数据库，因为该数据库无法继续迁移。

若要 stop\abort 迁移过程，请使用以下 PowerShell 命令：

```PowerShell
Stop-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName"
```

若要 stop\abort 迁移过程，请使用以下 CLI 命令：

```CLI
az sql midb log-replay stop -g mygroup --mi myinstance -n mymanageddb
```

## <a name="complete-the-migration-continuous-mode"></a>完成迁移 (连续模式) 

如果在连续模式下启动 LRS，则确保所有备份都已还原后，启动转换将完成迁移。 在转换完成时，将迁移数据库并准备好进行读写访问。

若要在 LRS 连续模式下完成迁移过程，请使用以下 PowerShell 命令：

```PowerShell
Complete-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
-InstanceName "ManagedInstance01" `
-Name "ManagedDatabaseName" `
-LastBackupName "last_backup.bak"
```

若要在 LRS 连续模式下完成迁移过程，请使用以下 CLI 命令：

```CLI
az sql midb log-replay complete -g mygroup --mi myinstance -n mymanageddb --last-backup-name "backup.bak"
```

## <a name="functional-limitations"></a>功能限制

日志重播服务 (LRS) 的功能限制如下：
- 在迁移过程中，要还原的数据库不能用于只读访问。
- 系统托管软件修补程序将在47小时内被阻止，因为启动 LRS。 此时间段到期后，下一软件更新将停止 LRS。 在这种情况下，LRS 需要从头开始重新启动。
- LRS 要求启用 CHECKSUM 选项来备份 SQL Server 上的数据库。
- 需要为整个 Azure Blob 存储容器生成用于 LRS 的 SAS 令牌，并且必须仅具有读取和列出权限。
- 不同数据库的备份文件必须位于 Azure Blob 存储的单独文件夹中。
- 对于每个数据库，每个数据库都必须单独启动 LRS，每个数据库都指向 Azure Blob 存储上的备份文件。
- LRS 可以支持每个 SQL 托管实例最多支持100个同时还原进程。

## <a name="troubleshooting"></a>疑难解答

启动 LRS 后，使用监视 cmdlet (azsqlinstancedatabaselogreplay 或 az_sql_midb_log_replay_show) 查看操作状态。 如果在一段时间后 LRS 无法启动并出现错误，请检查一些最常见的问题：
- 尝试从 SQL Server 迁移的 SQL MI 上是否已存在具有相同名称的数据库？ 通过重命名数据库之一来解决此冲突。
- 数据库备份是否是使用 **CHECKSUM** 选项创建的 SQL Server？
- 针对 SAS 令牌的权限是否只 **读取** 和 **列出** LRS 服务？
- 是否从问号 "？" 后开始复制的 LRS 的 SAS 令牌，其内容与此 "sv = 2020-02-10 ..." 类似。 
- SAS 令牌的 **有效** 时间是否适用于启动和完成迁移的时间范围？ 由于用于 SQL 托管实例和 SAS 令牌的不同 **时区** ，可能会出现不匹配的情况。 尝试重新生成 SAS 令牌，并在当前日期之前和之后延长时间范围的令牌有效性。
- 数据库名称、资源组名称和托管实例名称拼写是否正确？
- 如果 LRS 是在自动完成模式下启动的，则是上次指定的备份文件的有效文件名？

## <a name="next-steps"></a>后续步骤
- 了解有关 [将 SQL Server 迁移到 SQL 托管实例](../migration-guides/managed-instance/sql-server-to-managed-instance-guide.md)的详细信息。
- 详细了解 [SQL Server 和 AZURE SQL 托管实例之间的差异](transact-sql-tsql-differences-sql-server.md)。
- 详细了解 [迁移到 Azure 的成本和大小工作负载的最佳实践](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs)。
