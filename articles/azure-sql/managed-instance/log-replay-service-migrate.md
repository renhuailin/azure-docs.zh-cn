---
title: 使用日志重播服务将数据库迁移到 SQL 托管实例
description: 了解如何使用日志重播服务将数据库从 SQL Server 迁移到 SQL 托管实例
services: sql-database
ms.service: sql-managed-instance
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: sstein
ms.date: 02/23/2021
ms.openlocfilehash: 73963763716d7e18b757b5ade8998f23cc589fdb
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101661352"
---
# <a name="migrate-databases-from-sql-server-to-sql-managed-instance-using-log-replay-service"></a>使用日志重播服务将数据库从 SQL Server 迁移到 SQL 托管实例
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

本文介绍如何使用日志重播服务 (LRS) 手动配置从 SQL Server 2008-2019 到 SQL 托管实例的数据库迁移。 这是一种基于 SQL Server 日志传送技术为托管实例启用的云服务。 当不能使用 Azure 数据迁移服务 (DM) 时，如果需要更多控制，或者存在不太大的停机时间，则应使用 LRS。

## <a name="when-to-use-log-replay-service"></a>何时使用日志重播服务

在 [AZURE DMS](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance) 无法用于迁移的情况下，可通过 POWERSHELL、CLI CMDLET 或 API 直接使用 LRS 云服务，以手动生成并协调到 SQL 托管实例的数据库迁移。 

在以下某些情况下，你可能需要考虑使用 LRS 云服务：
- 你的数据库迁移项目需要更多控制
- 迁移转换的故障转移存在少许容错
- 无法在你的环境中安装 DMS 可执行文件
- DMS 可执行文件没有对数据库备份的文件访问权限
- 对主机操作系统的访问不可用，或没有管理员权限
- 无法打开从环境到 Azure 的网络端口

> [!NOTE]
> 使用 Azure DMS 将数据库从 SQL Server 迁移到 SQL 托管实例的推荐自动方式。 在 NORECOVERY 模式下，此服务在后端使用相同的 LRS 云服务和日志传送。 当 Azure DMS 不完全支持你的方案时，应考虑使用 LRS 手动安排迁移。

## <a name="how-does-it-work"></a>工作原理

使用 LRS 生成自定义解决方案，将数据库迁移到云时，需要在下表中所示的几个业务流程步骤。

迁移包括在启用了校验和的 SQL Server 上进行完整的数据库备份，以及将备份文件复制到 Azure Blob 存储。 LRS 用于将备份文件从 Azure Blob 存储还原到 SQL 托管实例。 Azure Blob 存储用作 SQL Server 与 SQL 托管实例之间的中间存储。

LRS 将监视 Azure Blob 存储中的任何新差异，或还原完整备份后添加的日志备份，并将自动还原所有添加的新文件。 可以使用服务监视 SQL 托管实例上还原的备份文件的进度，如果需要，还可以中止该过程。

LRS 不需要特定的备份文件命名约定，因为它会扫描放置在 Azure Blob 存储上的所有文件，并且它仅从读取文件头构造备份链。 在迁移过程中，数据库处于 "正在还原" 状态，因为这些数据库在 [NORECOVERY](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql?view=sql-server-ver15#comparison-of-recovery-and-norecovery) 模式下还原，并且在完成迁移过程之前不能用于读取或写入。 

迁移多个数据库时，需要将每个数据库的备份放置在 Azure Blob 存储上的单独文件夹中。 需要为每个数据库单独启动 LRS，需要指定不同的不同 Azure Blob 存储文件夹路径。 

可以在 "自动完成" 或连续模式下启动 LRS。 在自动完成模式下启动时，如果已还原指定的上次备份文件名，则迁移将自动完成。 在连续模式下启动时，该服务将持续恢复添加的任何新备份文件，并且迁移将在手动切换时完成。 建议仅在执行最后的日志尾备份并在 SQL 托管实例上将其显示为已还原后，才执行手动转换。 最后一个转换步骤会使数据库联机，并可用于 SQL 托管实例上的 "读" 和 "写"。

LRS 停止后（自动完成时自动执行或切换时），将无法恢复 SQL 托管实例上联机的数据库的还原过程。 若要在通过自动完成完成迁移后还原其他备份文件，或者在切换时手动还原，则需要删除数据库，并重新启动 LRS，以从头开始整个备份链。

  ![针对 SQL 托管实例说明的日志重播服务协调步骤](./media/log-replay-service-migrate/log-replay-service-conceptual.png)

| Operation | 详细信息 |
| :----------------------------- | :------------------------- |
| **1. 将数据库备份从 SQL Server 复制到 Azure Blob 存储**。 | -使用 [Azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)或 [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)将完整备份、差异备份和日志备份从 SQL Server 复制到 Azure Blob 存储容器。 <br />-使用任何文件名，因为 LRS 不需要特定的文件命名约定。<br />-在迁移多个数据库时，每个数据库都需要一个单独的文件夹。 |
| **2. 在云中启动 LRS 服务**。 | -可通过选择 cmdlet 来启动服务： <br /> PowerShell [启动-azsqlinstancedatabaselogreplay](https://docs.microsoft.com/powershell/module/az.sql/start-azsqlinstancedatabaselogreplay) <br /> CLI [az_sql_midb_log_replay_start cmdlet](https://docs.microsoft.com/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_start)。 <br /> -对指向 Azure Blob 存储上的不同备份文件夹的每个不同数据库单独启动 LRS。 <br />-启动后，服务将从 Azure Blob 存储容器获取备份，并开始在 SQL 托管实例上还原它们。<br /> -如果 LRS 在连续模式下启动，则在还原所有初始上传的备份后，该服务会监视上传到该文件夹的任何新文件，并且将基于 LSN 链持续应用日志，直到服务停止。 |
| **2.1. 监视操作进度**。 | -可以选择或 cmdlet 来监视还原操作的进度： <br /> PowerShell [azsqlinstancedatabaselogreplay](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabaselogreplay) <br /> CLI [az_sql_midb_log_replay_show cmdlet](https://docs.microsoft.com/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_show)。 |
| **2.2. 如果需要，Stop\abort 操作**。 | -如果迁移过程需要中止，则可以选择使用 cmdlet 来停止操作： <br /> PowerShell [停止-azsqlinstancedatabaselogreplay](https://docs.microsoft.com/powershell/module/az.sql/stop-azsqlinstancedatabaselogreplay) <br /> CLI [az_sql_midb_log_replay_stop](https://docs.microsoft.com/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_stop) cmdlet。 <br /><br />-这将导致删除正在 SQL 托管实例上还原的数据库。 <br />-停止后，无法恢复数据库的 LRS。 需要从头开始迁移过程。 |
| **3. 准备就绪时转换到云**。 | -所有备份都已还原到 SQL 托管实例后，通过使用所选 cmdlet 启动 LRS 完成操作来完成转换： <br />PowerShell [azsqlinstancedatabaselogreplay](https://docs.microsoft.com/powershell/module/az.sql/complete-azsqlinstancedatabaselogreplay) <br /> CLI [az_sql_midb_log_replay_complete](https://docs.microsoft.com/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_complete) cmdlet。 <br /><br />-这将导致停止 LRS 服务，并使数据库联机，以便在 SQL 托管实例上进行读取和写入。<br /> -将应用程序连接字符串从 SQL Server Repoint 到 SQL 托管实例。 |

## <a name="requirements-for-getting-started"></a>入门的要求

### <a name="sql-server-side"></a>SQL Server 侧
- SQL Server 2008-2019
-  (一个或多个文件的数据库的完整备份) 
-  (一个或多个文件的差异备份) 
- 事务日志文件的日志备份 (未拆分) 
- 必须为 (必需的备份 **启用校验和**) 

### <a name="azure-side"></a>Azure 端
- PowerShell Az .SQL module 版本2.16.0 或更高版本 ([安装](https://www.powershellgallery.com/packages/Az.Sql/)或使用 Azure [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/)) 
- CLI 版本2.19.0 或更高版本 ([安装](https://docs.microsoft.com/cli/azure/install-azure-cli)) 
- 已预配 Azure Blob 存储容器
- SAS 安全令牌，其中包含为 blob 存储容器生成的 " **读取** " 和 "仅 **列出** " 权限

### <a name="migrating-multiple-databases"></a>迁移多个数据库
- 不同数据库的备份文件必须位于 Azure Blob 存储的单独文件夹中。
- 需要为指向 Azure Blob 存储上相应文件夹的每个数据库单独启动 LRS。
- LRS 可以支持每个 SQL 托管实例最多支持100个同时还原进程。

### <a name="azure-rbac-permissions-required"></a>所需的 Azure RBAC 权限
通过所提供的客户端执行 LRS 需要以下 Azure 角色之一：
- “订阅所有者”角色或
- [托管实例参与者](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor) 角色，或
- 具有以下权限的自定义角色：
  - `Microsoft.Sql/managedInstances/databases/*`

## <a name="best-practices"></a>最佳实践

作为最佳做法，强烈建议执行以下操作：
- 运行 [数据迁移助手](https://docs.microsoft.com/sql/dma/dma-overview) 以验证数据库是否已准备好迁移到 SQL 托管实例。 
- 将完整备份和差异备份拆分为多个文件，而不是一个文件。
- 启用备份压缩。
- 使用 Cloud Shell 执行脚本，因为它始终会更新到发布的最新 cmdlet 中。
- 计划在47小时内完成迁移，因为已启动 LRS 服务。 这是一个宽限期，在 LRS 启动后，它会阻止系统管理的软件修补程序。

> [!IMPORTANT]
> - 在迁移过程完成之前，不能使用 LRS 还原的数据库。 这是因为基础技术在 NORECOVERY 模式下进行还原。
> - LRS 不支持在迁移期间对数据库进行只读访问的备用还原模式，因为 SQL 托管实例和市场内 SQL Server 之间的版本差异。
> - 通过 "自动完成" 或手动切换完成迁移后，迁移过程将完成，因为 LRS 不支持还原恢复。

## <a name="steps-to-execute"></a>要执行的步骤

### <a name="make-backups-on-the-sql-server"></a>在 SQL Server 上进行备份

可以通过以下两个选项之一创建 SQL Server 上的备份：

- 备份到本地磁盘存储，并将文件上传到 Azure Blob 存储，以防环境限制直接备份到 Azure Blob 存储。
- 如果你的环境和安全过程允许你这样做，则在 T-sql 中通过 "到 URL" 选项直接备份到 Azure Blob 存储。 

将想要迁移到完整恢复模式的数据库设置为允许日志备份。

```SQL
-- To permit log backups, before the full database backup, modify the database to use the full recovery model.
USE master
ALTER DATABASE SampleDB
SET RECOVERY FULL
GO
```

若要在本地存储上手动对数据库进行完整、差异和日志备份，请使用下面提供的示例 T-sql 脚本。 请确保已启用 CHECKSUM 选项，因为这是 LRS 的必需要求。

```SQL
-- Example on how to make full database backup to the local disk
BACKUP DATABASE [SampleDB]
TO DISK='C:\BACKUP\SampleDB_full_14_43.bak',
WITH INIT, COMPRESSION, CHECKSUM
GO

-- Example on how to make differential database backup to the locak disk
BACKUP DATABASE [SampleDB]
TO DISK='C:\BACKUP\SampleDB_diff_14_44.bak',
WITH DIFFERENTIAL, COMPRESSION, CHECKSUM
GO

-- Example on how to make the log backup
BACKUP LOG [SampleDB]
TO DISK='C:\BACKUP\SampleDB_log_14_45.bak',
WITH CHECKSUM
GO
```

备份到本地存储的文件需要上载到 Azure Blob 存储。 如果你的公司策略允许这样做，则以下教程介绍了将 [azure blob 存储服务用于 SQL Server](https://docs.microsoft.com/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016#1---create-stored-access-policy-and-shared-access-storage)的替代方法。 如果使用这种替代方法，请确保在启用 CHECKSUM 选项的情况下进行所有备份。

### <a name="copy-backups-from-sql-server-to-azure-blob-storage"></a>将备份从 SQL Server 复制到 Azure Blob 存储

使用 LRS 将数据库迁移到托管实例时，可以使用以下方法将备份上传到 blob 存储：
- 使用 SQL Server 本地 [备份到 URL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url) 功能。
- 使用 [Azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)或 [Azure 存储资源管理器](https://azure.microsoft.com/en-us/features/storage-explorer) 将备份上传到 blob 容器。
- 使用 Azure 门户中的存储资源管理器。

### <a name="create-azure-blob-and-sas-authentication-token"></a>创建 Azure Blob 和 SAS 身份验证令牌

Azure Blob 存储用作 SQL Server 与 SQL 托管实例之间备份文件的中间存储。 按照以下步骤创建 Azure Blob 存储容器：

1. [创建存储帐户](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal)
2. 创建存储帐户中[的 blob 容器](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)

创建 blob 容器后，只会按照以下步骤生成具有读取和列出权限的 SAS 身份验证令牌：

1. 使用 Azure 门户访问存储帐户
2. 导航到存储资源管理器
3. 展开“Blob 容器”
4. 右键单击 blob 容器
5. 选择 "获取共享访问签名"
6. 选择令牌过期时间范围。 确保令牌在迁移期间有效。
    - 请注意，令牌的时区和 SQL 托管实例可能不匹配。 请确保 SAS 令牌具有适当的时间有效性，同时考虑时区。 如果可能，请将时区设置为之前和之后的计划迁移时段。
8. 确保选中 "只读" 和 "仅列出" 权限
9. 单击“创建”
10. 将该标记复制到问号 "？" 后，然后复制。 SAS 令牌通常以 URI 中的 "sv = 2020-10" 开头，以便在代码中使用。

> [!IMPORTANT]
> - 需要读取和列出 Azure Blob 存储的 SAS 令牌的权限。 如果为 SAS 身份验证令牌授予了其他任何权限，则启动 LRS 服务将会失败。 这些安全要求是由设计决定的。
> - 令牌必须具有相应的时间有效性。 请确保标记和托管实例之间的时间区域被视为。
> - 请确保在字符串末尾之前从 "sv = 2020-10 ..." 开始复制令牌。

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
    -StorageContainerUri "https://test.blob.core.windows.net/testing" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D" `
    -AutoCompleteRestore `
    -LastBackupName "last_backup.bak"
```

在自动完成模式下启动 LRS-CLI 示例：

```CLI
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb -a --last-bn "backup.bak"
    --storage-uri "https://test.blob.core.windows.net/testing"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

### <a name="start-lrs-in-continuous-mode"></a>在连续模式下启动 LRS

在连续模式下启动 LRS-PowerShell 示例：

```PowerShell
Start-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName" `
    -Collation "SQL_Latin1_General_CP1_CI_AS" -StorageContainerUri "https://test.blob.core.windows.net/testing" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

在连续模式下启动 LRS-CLI 示例：

```CLI
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb
    --storage-uri "https://test.blob.core.windows.net/testing"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
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

## <a name="troubleshooting"></a>疑难解答

启动 LRS 后，使用监视 cmdlet (azsqlinstancedatabaselogreplay 或 az_sql_midb_log_replay_show) 查看操作状态。 如果在一段时间后 LRS 无法启动并出现错误，请检查一些最常见的问题：
- 数据库备份是否是使用 **CHECKSUM** 选项创建的 SQL Server？
- 针对 SAS 令牌的权限是否只 **读取** 和 **列出** LRS 服务？
- 是否从问号 "？" 后开始复制的 LRS 的 SAS 令牌，其内容与此 "sv = 2020-02-10 ..." 类似。 
- SAS 令牌的 **有效** 时间是否适用于启动和完成迁移的时间范围？ 请注意，由于用于 SQL 托管实例和 SAS 令牌的不同 **时区** ，可能会出现不匹配的情况。 尝试重新生成 SAS 令牌，并在当前日期之前和之后延长时间范围的令牌有效性。
- 数据库名称、资源组名称和托管实例名称拼写是否正确？
- 如果 LRS 是在自动完成模式下启动的，则是上次指定的备份文件的有效文件名？

## <a name="next-steps"></a>后续步骤
- 了解有关 [将 SQL Server 迁移到 SQL 托管实例](../migration-guides/managed-instance/sql-server-to-managed-instance-guide.md)的详细信息。
- 详细了解 [SQL Server 和 AZURE SQL 托管实例之间的差异](transact-sql-tsql-differences-sql-server.md)。
- 详细了解 [迁移到 Azure 的成本和大小工作负载的最佳实践](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs)。
