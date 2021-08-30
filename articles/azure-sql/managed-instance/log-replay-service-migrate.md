---
title: 使用日志重播服务将数据库迁移到 SQL 托管实例
description: 了解如何使用日志重播服务将数据库从 SQL Server 迁移到 SQL 托管实例
services: sql-database
ms.service: sql-managed-instance
ms.subservice: migration
ms.custom: seo-lt-2019, sqldbrb=1, devx-track-azurecli, devx-track-azurepowershell
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: mathoma
ms.date: 03/31/2021
ms.openlocfilehash: e76493aa83383e4ce59da77cfb0ce050475ad303
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121751263"
---
# <a name="migrate-databases-from-sql-server-to-sql-managed-instance-by-using-log-replay-service-preview"></a>使用日志重播服务（预览版）将数据库从 SQL Server 迁移到 SQL 托管实例
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

本文介绍如何使用日志重播服务 (LRS) 手动配置从 SQL Server 2008-2019 到 Azure SQL 托管实例的数据库迁移，该服务目前为公共预览版。 LRS 是为 SQL 托管实例启用的云服务，它基于 SQL Server 日志传送技术。 

[Azure 数据库迁移服务](../../dms/tutorial-sql-server-to-managed-instance.md)和 LRS 使用相同的基础迁移技术和相同的 API。 通过发布 LRS，我们将进一步在本地 SQL Server 与 SQL 托管实例之间实现复杂的自定义迁移和混合体系结构。

## <a name="when-to-use-log-replay-service"></a>何时使用日志重播服务

当你不能使用 Azure 数据库迁移服务进行迁移时，可以直接将 LRS 与 PowerShell、Azure CLI cmdlet 或 API 结合使用，手动构建和编排到 SQL 托管实例的数据库迁移。 

在以下情况下，可以考虑使用 LRS：
- 需要加强对数据库迁移项目的控制。
- 在进行迁移直接转换时偶尔允许停机。
- 无法在环境中安装数据库迁移服务可执行文件。
- 数据库迁移服务可执行文件无权访问数据库备份。
- 无法访问主机 OS，或者没有管理员特权。
- 不能将环境中的网络端口打开到 Azure。
- 环境中的带宽限制或代理阻塞性问题。
- 通过 `TO URL` 选项将备份直接存储到 Azure Blob 存储。
- 需要使用差异备份。

> [!NOTE]
> 建议使用数据库迁移服务自动将数据库从 SQL Server 迁移到 SQL 托管实例。 此服务在后端使用相同的 LRS 云服务，并在 `NORECOVERY` 模式下进行日志传送。 当数据库迁移服务不能完全支持你的方案时，请考虑手动使用 LRS 来编排迁移。

## <a name="how-it-works"></a>工作原理

使用 LRS 生成将数据库迁移到云的自定义解决方案时，需要执行此部分后面的图和表中所示的几个编排步骤。

迁移需要在启用了 `CHECKSUM` 的 SQL Server 上进行完整的数据库备份，并将备份文件复制到 Azure Blob 存储。 LRS 用于将备份文件从 Blob 存储还原到 SQL 托管实例。 Blob 存储是 SQL Server 与 SQL 托管实例之间的中间存储。

LRS 监视 Blob 存储中是否有在还原完整备份后添加的任何新的差异备份或日志备份。 然后，LRS 会自动还原这些新文件。 你可以使用该服务监视在 SQL 托管实例上还原备份文件的进度，并且可以在必要时停止该过程。

对于备份文件，LRS 不需要特定的命名约定。 它会扫描放置在 Blob 存储中的所有文件，并通过仅读取文件头来构造备份链。 在迁移过程中，数据库处于“正在还原”状态。 数据库以 [NORECOVERY](/sql/t-sql/statements/restore-statements-transact-sql#comparison-of-recovery-and-norecovery) 模式还原，因此在迁移过程完成之前，不能将它们用于读取或写入。 

如果要迁移多个数据库，则需要：
 
- 将各数据库的备份放在 Blob 存储上不同的文件夹中。
- 为每个数据库单独启动 LRS。
- 指定不同的路径以分隔 Blob 存储文件夹。 

你可以在自动完成或连续模式下启动 LRS。 如果在自动完成模式下启动，将在还原指定的最后一个备份文件后自动完成迁移。 如果在连续模式下启动 LRS，该服务会连续还原已添加的所有新备份文件，迁移只能通过手动直接转换完成。 

建议你在获取最终的日志结尾备份，并且该备份在 SQL 托管实例上显示为已还原之后，手动进行直接转换。 最终的直接转换步骤会使数据库联机，并且可以在 SQL 托管实例上用于读取和写入。

通过自动完成模式自动停止 LRS 或通过直接转换手动停止 LRS 后，无法为 SQL 托管实例上已联机的数据库恢复还原过程。 若要在通过自动完成或直接转换完成迁移后还原其他备份文件，你需要删除数据库。 同时，还需要重启 LRS，从头开始还原整个备份链。

:::image type="content" source="./media/log-replay-service-migrate/log-replay-service-conceptual.png" alt-text="该图解释了 SQL 托管实例的日志重播服务编排步骤。" border="false":::
    
| 操作 | 详细信息 |
| :----------------------------- | :------------------------- |
| **1.将数据库备份从 SQL Server 复制到 Blob 存储**。 | 使用 [AzCopy](../../storage/common/storage-use-azcopy-v10.md) 或 [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)将完整备份、差异备份和日志备份从 SQL Server 复制到 Blob 存储容器。 <br /><br />使用任意文件名。 LRS 不需要特定的文件命名约定。<br /><br />在迁移多个数据库时，每个数据库都需要一个单独的文件夹。 |
| **2.在云中启动 LRS**。 | 可以使用以下 cmdlet 来重启服务：PowerShell ([start-azsqlinstancedatabaselogreplay](/powershell/module/az.sql/start-azsqlinstancedatabaselogreplay)) 或 Azure CLI ([az_sql_midb_log_replay_start cmdlets](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_start))。 <br /><br /> 为指向 Blob 存储上的备份文件夹的每个数据库单独启动 LRS。 <br /><br /> 启动该服务后，它将从 Blob 存储容器中获取备份，并开始在 SQL 托管实例上进行还原。<br /><br /> 如果在连续模式下启动 LRS，则在还原所有最初上传的备份之后，该服务将监视上传到文件夹的所有新文件。 该服务将根据日志序列号 (LSN) 链连续应用日志，直到停止运行。 |
| **2.1.监视操作进度**。 | 可以使用以下 cmdlet 来监视还原操作的进度：PowerShell ([get-azsqlinstancedatabaselogreplay](/powershell/module/az.sql/get-azsqlinstancedatabaselogreplay)) 或 Azure CLI ([az_sql_midb_log_replay_show cmdlets](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_show))。 |
| **2.2.根据需要停止操作**。 | 如果需要停止迁移过程，可以选择以下 cmdlet：PowerShell ([stop-azsqlinstancedatabaselogreplay](/powershell/module/az.sql/stop-azsqlinstancedatabaselogreplay)) 或 Azure CLI ([az_sql_midb_log_replay_stop](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_stop))。 <br /><br /> 停止操作会删除正在 SQL 托管实例上还原的数据库。 停止操作后，将无法为数据库恢复 LRS。 你需要从头开始重启迁移过程。 |
| **3.准备就绪后，直接转换到云**。 | 停止应用程序和工作负载。 获取最后一个日志结尾备份，并将其上传到 Azure Blob 存储。<br /><br /> 通过使用以下 cmdlet 启动 LRS `complete` 操作来完成直接转换：PowerShell ([complete-azsqlinstancedatabaselogreplay](/powershell/module/az.sql/complete-azsqlinstancedatabaselogreplay)) 或 Azure CLI [az_sql_midb_log_replay_complete](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_complete)。 此操作将停止 LRS，并使数据库联机，可以在 SQL 托管实例上用于读取和写入。<br /><br /> 将应用程序连接字符串从 SQL Server 重新指向 SQL 托管实例。 你需要通过在应用程序中手动或自动（例如，在应用程序可从属性或数据库中读取连接字符串的情况下）更改连接字符串来自行安排此步骤。 |

## <a name="requirements-for-getting-started"></a>入门要求

### <a name="sql-server-side"></a>SQL Server 端
- SQL Server 2008-2019
- 数据库的完整备份（一个或多个文件）
- 差异备份（一个或多个文件）
- 日志备份（不为事务日志文件进行拆分）
- 为备份启用 `CHECKSUM`（必需）

### <a name="azure-side"></a>Azure 端
- PowerShell Az.SQL 模块版本 2.16.0 或更高版本（[已安装](https://www.powershellgallery.com/packages/Az.Sql/)或通过 [Azure Cloud Shell](/azure/cloud-shell/) 访问）
- Azure CLI 版本 2.19.0 或更高版本（[已安装](/cli/azure/install-azure-cli)）
- 预配的 Azure Blob 存储容器
- 具有为 Blob 存储容器生成的读取和列出权限的共享访问签名 (SAS) 安全令牌

### <a name="migration-of-multiple-databases"></a>迁移多个数据库
不同数据库的备份文件必须放在 Blob 存储上的不同文件夹中。

通过指向 Blob 存储上的相应文件夹，为每个数据库单独启动 LRS。 对于每个托管实例，LRS 最多可以支持 100 个同时还原过程。

### <a name="azure-rbac-permissions"></a>Azure RBAC 权限
通过系统提供的客户端运行 LRS 需要以下 Azure 角色之一：
- “订阅所有者”角色
- [托管实例参与者](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor)角色
- 具有以下权限的自定义角色：`Microsoft.Sql/managedInstances/databases/*`

## <a name="best-practices"></a>最佳做法

建议采用以下最佳做法：
- 运行[数据迁移助手](/sql/dma/dma-overview)，验证数据库是否已准备好迁移到 SQL 托管实例。 
- 将完整备份和差异备份拆分为多个文件，而不是使用一个文件。
- 启用备份压缩。
- 使用 Cloud Shell 运行脚本，因为它始终更新到最新发布的 cmdlet。
- 计划在启动 LRS 后的 36 小时内完成迁移。 这是一个宽限期，可防止安装系统管理的软件补丁。

> [!IMPORTANT]
> - 在迁移过程完成之前，不能使用正在通过 LRS 还原的数据库。 
> - LRS 不支持在迁移期间对数据库进行只读访问。
> - 迁移完成后，迁移过程即终止，因为 LRS 不支持恢复还原过程。

## <a name="steps-to-execute"></a>要执行的步骤

### <a name="make-backups-of-sql-server"></a>备份 SQL Server

你可以使用以下任一选项来备份 SQL Server：

- 如果受环境所限，无法直接备份到 Blob 存储，则备份到本地磁盘存储，然后将文件上传到 Azure Blob 存储。
- 如果环境和安全过程允许，则使用 T-SQL 中的 `TO URL` 选项直接备份到 Blob 存储。 

将要迁移的数据库设置为完整恢复模式，以允许进行日志备份。

```SQL
-- To permit log backups, before the full database backup, modify the database to use the full recovery
USE master
ALTER DATABASE SampleDB
SET RECOVERY FULL
GO
```

若要在本地存储上手动创建数据库的完整备份、差异备份和日志备份，请使用以下示例 T-SQL 脚本。 确保启用了 `CHECKSUM` 选项，因为 LRS 必须使用该选项。

```SQL
-- Example of how to make a full database backup to the local disk
BACKUP DATABASE [SampleDB]
TO DISK='C:\BACKUP\SampleDB_full.bak'
WITH INIT, COMPRESSION, CHECKSUM
GO

-- Example of how to make a differential database backup to the local disk
BACKUP DATABASE [SampleDB]
TO DISK='C:\BACKUP\SampleDB_diff.bak'
WITH DIFFERENTIAL, COMPRESSION, CHECKSUM
GO

-- Example of how to make a transactional log backup to the local disk
BACKUP LOG [SampleDB]
TO DISK='C:\BACKUP\SampleDB_log.trn'
WITH COMPRESSION, CHECKSUM
GO
```

### <a name="create-a-storage-account"></a>创建存储帐户

Azure Blob 存储用作 SQL Server 与 SQL 托管实例之间的备份文件的中间存储。 若要在存储帐户中创建新的存储帐户和 blob 容器，请执行以下步骤：

1. [创建存储帐户](../../storage/common/storage-account-create.md?tabs=azure-portal)。
2. 在存储帐户中[创建 blob 容器](../../storage/blobs/storage-quickstart-blobs-portal.md)。

### <a name="copy-backups-from-sql-server-to-blob-storage"></a>将备份从 SQL Server 复制到 Blob 存储

使用 LRS 将数据库迁移到托管实例时，可以使用以下方法将备份上传到 Blob 存储：
- 使用 SQL Server 的原生[备份到 URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url) 功能
- 使用 [AzCopy](../../storage/common/storage-use-azcopy-v10.md) 或 [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer)将备份上传到 blob 容器
- 使用 Azure 门户中的存储资源管理器

### <a name="make-backups-from-sql-server-directly-to-blob-storage"></a>从 SQL Server 直接备份到 Blob 存储
如果公司和网络策略允许，另一种方法是使用 SQL Server 的原生[备份到 URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url) 选项直接从 SQL Server 备份到 Blob 存储。 如果可以使用此选项，则无需在本地存储上进行备份并将其上传到 Blob 存储。

作为第一步，此操作要求你为 Blob 存储生成 SAS 身份验证令牌，然后将令牌导入 SQL Server。 第二步是使用 T-SQL 中的 `TO URL` 选项进行备份。 确保所有备份都是在启用了 `CHEKSUM` 选项的情况下进行的。

为了便于参考，以下示例代码将备份到 Blob 存储。 此示例不提供有关如何导入 SAS 令牌的说明。 有关详细说明，包括如何生成 SAS 令牌并将其导入到 SQL Server，请参阅教程[将 Azure Blob 存储与 SQL Server 配合使用](/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016#1---create-stored-access-policy-and-shared-access-storage)。 

```SQL
-- Example of how to make a full database backup to a URL
BACKUP DATABASE [SampleDB]
TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/SampleDB_full.bak'
WITH INIT, COMPRESSION, CHECKSUM
GO
-- Example of how to make a differential database backup to a URL
BACKUP DATABASE [SampleDB]
TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/SampleDB_diff.bak'  
WITH DIFFERENTIAL, COMPRESSION, CHECKSUM
GO

-- Example of how to make a transactional log backup to a URL
BACKUP LOG [SampleDB]
TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/SampleDB_log.trn'  
WITH COMPRESSION, CHECKSUM
```

### <a name="generate-a-blob-storage-sas-authentication-token-for-lrs"></a>为 LRS 生成 Blob 存储 SAS 身份验证令牌

Azure Blob 存储用作 SQL Server 与 SQL 托管实例之间的备份文件的中间存储。 你需要为 LRS 生成仅具有列出和读取权限的 SAS 身份验证令牌。 借助该令牌，LRS 可以访问 Blob 存储，并使用备份文件在 SQL 托管实例上进行还原。 

按照以下步骤生成令牌：

1. 从 Azure 门户打开存储资源管理器。
2. 展开“Blob 容器”。
3. 右键单击 blob 容器，并选择“获取共享访问签名”。

   :::image type="content" source="./media/log-replay-service-migrate/lrs-sas-token-01.png" alt-text="该屏幕截图显示了用于生成 SAS 身份验证令牌的选项。":::

4. 选择令牌到期时间范围。 确保令牌在迁移期间有效。
5. 选择令牌的时区：UTC 或本地时间。
    
   > [!IMPORTANT]
   > 令牌的时区和托管实例可能不匹配。 请确保 SAS 令牌具有适当的时间有效性，并考虑时区因素。 如果可能，请将时区设置为早于和晚于计划迁移时段的时间。
6. 仅选择“读取”和“列出”权限。

   > [!IMPORTANT]
   > 不要选择任何其他权限。 否则，LRS 将无法启动。 此安全要求是设计使然。
7. 选择“创建”。

   :::image type="content" source="./media/log-replay-service-migrate/lrs-sas-token-02.png" alt-text="该屏幕截图显示了 SAS 令牌到期时间、时区和权限的选择以及“创建”按钮。":::

系统将使用你指定的时间有效性生成 SAS 身份验证。 你需要令牌的 URI 版本，如以下屏幕截图所示。

:::image type="content" source="./media/log-replay-service-migrate/lrs-generated-uri-token.png" alt-text="该屏幕截图显示了 SAS 令牌的 URI 版本示例。":::

### <a name="copy-parameters-from-the-sas-token"></a>从 SAS 令牌复制参数

在使用 SAS 令牌启动 LRS 之前，你需要了解其结构。 系统生成的 SAS 令牌的 URI 由两部分组成，这两部分用问号 (`?`) 隔开，如以下示例所示：

:::image type="content" source="./media/log-replay-service-migrate/lrs-token-structure.png" alt-text="为日志重播服务生成的 SAS 令牌的示例 URI。" border="false":::

第一部分从 `https://` 开始，一直到问号 (`?`) 前结束，该部分用于作为输入馈送到 LRS 的 `StorageContainerURI` 参数。 它向 LRS 提供存储数据库备份文件的文件夹的相关信息。

第二部分是 `StorageContainerSasToken` 参数，该部分从问号 (`?`) 后开始，一直到字符串末尾截止。 这是实际的签名身份验证令牌，在指定的时间内有效。 此部分不一定要像示例中那样从 `sp=` 开始。 你的情况可能有所不同。

按如下方式复制参数：

1. 复制令牌的第一部分，即，从 `https://` 开始，一直到问号 (`?`) 前结束。 将其用作 PowerShell 或 Azure CLI 中的 `StorageContainerUri` 参数以启动 LRS。

   :::image type="content" source="./media/log-replay-service-migrate/lrs-token-uri-copy-part-01.png" alt-text="该屏幕截图显示了如何复制令牌的第一部分。":::

2. 复制令牌的第二部分，即，从问号 (`?`) 开始，一直到字符串末尾截止。 将其用作 PowerShell 或 Azure CLI 中的 `StorageContainerSasToken` 参数以启动 LRS。

   :::image type="content" source="./media/log-replay-service-migrate/lrs-token-uri-copy-part-02.png" alt-text="该屏幕截图显示了如何复制令牌的第二部分。":::
   
> [!NOTE]
> 复制令牌的任何一部分时，都不要包含问号。

### <a name="log-in-to-azure-and-select-a-subscription"></a>登录 Azure 并选择订阅

通过以下 PowerShell cmdlet 登录到 Azure：

```powershell
Login-AzAccount
```

使用以下 PowerShell cmdlet 选择托管实例所在的相应订阅：

```powershell
Select-AzSubscription -SubscriptionId <subscription ID>
```

## <a name="start-the-migration"></a>开始迁移

通过启动 LRS 开始进行迁移。 你可以在自动完成或连续模式下启动该服务。 

如果使用自动完成模式，将在还原指定的最后一个备份文件后自动完成迁移。 此选项要求 start 命令指定最后一个备份文件的文件名。 

如果使用连续模式，该服务将连续还原已添加的所有新备份文件。 迁移只能通过手动直接转换完成。 

### <a name="start-lrs-in-autocomplete-mode"></a>在自动完成模式下启动 LRS

若要在自动完成模式下启动 LRS，请使用以下 PowerShell 或 Azure CLI 命令。 使用 `-LastBackupName` 参数指定最后一个备份文件的名称。 还原指定的最后一个备份文件后，服务会自动启动直接转换。

下面是使用 PowerShell 在自动完成模式下启动 LRS 的示例：

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

下面是使用 Azure CLI 在自动完成模式下启动 LRS 的示例：

```CLI
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb -a --last-bn "backup.bak"
    --storage-uri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

### <a name="start-lrs-in-continuous-mode"></a>在连续模式下启动 LRS

下面是使用 PowerShell 在连续模式下启动 LRS 的示例：

```PowerShell
Start-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName" `
    -Collation "SQL_Latin1_General_CP1_CI_AS" -StorageContainerUri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

下面是使用 Azure CLI 在连续模式下启动 LRS 的示例：

```CLI
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb
    --storage-uri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

在连续模式下启动 LRS 的 PowerShell 和 CLI 客户端是同步的。 这意味着，客户端会等到 API 响应报告成功或失败后再启动作业。 

在等待过程中，命令不会将控制权返回给命令提示符。 如果你正在编写迁移体验的脚本，并且需要 LRS start 命令立即交还控制权，以便继续执行脚本的其余部分，则可以使用 `-AsJob` 开关将 PowerShell 作为后台作业运行。 例如：

```PowerShell
$lrsjob = Start-AzSqlInstanceDatabaseLogReplay <required parameters> -AsJob
```

启动后台作业后，即使后台作业需要较长时间才能完成，系统也会立即返回一个作业对象。 当该作业运行时，你可以继续在此会话中工作而不会发生中断。 有关将 PowerShell 作为后台作业运行的详细信息，请参阅 [PowerShell Start-Job](/powershell/module/microsoft.powershell.core/start-job#description) 文档。

同样，若要在 Linux 上以后台进程的形式启动 Azure CLI 命令，请在 LRS start 命令末尾使用与号 (`&`)：

```CLI
az sql midb log-replay start <required parameters> &
```

> [!IMPORTANT]
> 启动 LRS 后，系统管理的所有软件补丁都将暂停 36 小时。 该时段过后，下一个自动化软件补丁会自动停止 LRS。 如果发生这种情况，则无法恢复迁移，而需要从头开始重启迁移。 

## <a name="monitor-the-migration-progress"></a>监视迁移进度

若要通过 PowerShell 监视迁移的进度，请使用以下命令：

```PowerShell
Get-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName"
```

若要通过 Azure CLI 监视迁移的进度，请使用以下命令：

```CLI
az sql midb log-replay show -g mygroup --mi myinstance -n mymanageddb
```

## <a name="stop-the-migration"></a>停止迁移

如果需要停止迁移，请使用以下 cmdlet。 停止迁移将删除 SQL 托管实例上正在还原的数据库，因此无法恢复迁移。

若要通过 PowerShell 停止迁移过程，请使用以下命令：

```PowerShell
Stop-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName"
```

若要通过 Azure CLI 停止迁移过程，请使用以下命令：

```CLI
az sql midb log-replay stop -g mygroup --mi myinstance -n mymanageddb
```

## <a name="complete-the-migration-continuous-mode"></a>完成迁移（连续模式）

如果在连续模式下启动 LRS，则在确保所有备份都已还原后，启动直接转换就可以完成迁移。 直接转换完成后，就可以迁移数据库并准备进行读写访问。

若要通过 PowerShell 在 LRS 连续模式下完成迁移过程，请使用以下命令：

```PowerShell
Complete-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
-InstanceName "ManagedInstance01" `
-Name "ManagedDatabaseName" `
-LastBackupName "last_backup.bak"
```

若要通过 Azure CLI 在 LRS 连续模式下完成迁移过程，请使用以下命令：

```CLI
az sql midb log-replay complete -g mygroup --mi myinstance -n mymanageddb --last-backup-name "backup.bak"
```

## <a name="functional-limitations"></a>功能限制

LRS 的功能限制如下：
- 在迁移过程中，正在还原的数据库不能用于只读访问。
- 启动 LRS 后，系统管理的软件补丁将暂停 36 小时。 该时段过后，下一个软件更新将停止 LRS。 然后，你需要从头开始重启 LRS。
- LRS 要求在启用 `CHECKSUM` 选项的情况下备份 SQL Server 上的数据库。
- LRS 将使用的 SAS 令牌必须是为整个 Azure Blob 存储容器生成的，并且必须仅具有读取和列出权限。
- 不同数据库的备份文件必须放在 Blob 存储上的不同文件夹中。
- 对于指向 Blob 存储上包含备份文件的不同文件夹的每个数据库，必须为其单独启动 LRS。
- 对于每个托管实例，LRS 最多可以支持 100 个同时还原过程。

## <a name="troubleshooting"></a>疑难解答

启动 LRS 后，使用监视 cmdlet（`get-azsqlinstancedatabaselogreplay` 或 `az_sql_midb_log_replay_show`）查看操作状态。 如果一段时间后 LRS 无法启动，并且出现错误，请检查最常见的问题：

- SQL 托管实例上的现有数据库是否与你尝试从 SQL Server 迁移的数据库具有相同的名称？ 可通过重命名其中一个数据库来解决此冲突。
- SQL Server 上的数据库备份是否通过 `CHECKSUM` 选项创建？
- 用于 LRS 的 SAS 令牌是否仅具有读取和列出权限？
- 是否在问号 (`?`) 后开始复制用于 LRS 的 SAS 令牌，内容的开头如下所示：`sv=2020-02-10...`？ 
- SAS 令牌的有效时间是否适用于启动和完成迁移的时间范围？ 由于用于 SQL 托管实例和 SAS 令牌的时区不同，因此可能会出现不匹配的情况。 尝试重新生成 SAS 令牌，并将令牌有效期延长到迁移时间范围的当前日期之前和之后。
- 数据库名称、资源组名称和托管实例名称的拼写是否正确？
- 如果在自动完成模式下启动 LRS，是否为最后一个备份文件指定了有效的文件名？

## <a name="next-steps"></a>后续步骤
- 详细了解如何[将 SQL Server 迁移到 SQL 托管实例](../migration-guides/managed-instance/sql-server-to-managed-instance-guide.md)。
- 详细了解 [SQL Server 与 SQL 托管实例之间的差异](transact-sql-tsql-differences-sql-server.md)。
- 详细了解[为迁移到 Azure 的工作负载计算成本和调整大小的最佳做法](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs)。
