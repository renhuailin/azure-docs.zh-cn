---
title: 使用日志重播服务将数据库迁移到 SQL 托管实例
description: 了解如何使用日志重播服务将数据库从 SQL Server 迁移到 SQL 托管实例
services: sql-database
ms.service: sql-managed-instance
ms.custom: seo-lt-2019, sqldbrb=1
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: sstein
ms.date: 03/01/2021
ms.openlocfilehash: 0bc00aea67fa2f71599ee62e657e1ca1b0627681
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2021
ms.locfileid: "102199843"
---
# <a name="migrate-databases-from-sql-server-to-sql-managed-instance-by-using-log-replay-service-preview"></a>使用日志重播服务 (预览将数据库从 SQL Server 迁移到 SQL 托管实例) 
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

本文介绍如何使用 LRS) （目前为公共预览版）中的日志重播 (服务将数据库迁移从 SQL Server 2008-2019 手动配置为 Azure SQL 托管实例。 LRS 是一种云服务，它是为 SQL 托管实例而启用的，并且基于 SQL Server 日志传送技术。 

[Azure 数据库迁移服务](/azure/dms/tutorial-sql-server-to-managed-instance) 和 LRS 使用相同的基础迁移技术和相同的 api。 发布 LRS 后，我们将进一步实现本地 SQL Server 与 SQL 托管实例之间的复杂自定义迁移和混合体系结构。

## <a name="when-to-use-log-replay-service"></a>何时使用日志重播服务

当无法使用 Azure 数据库迁移服务进行迁移时，可以直接使用 LRS 与 PowerShell、Azure CLI cmdlet 或 Api，手动构建数据库迁移并将其安排到 SQL 托管实例。 

在以下情况下，可以考虑使用 LRS：
- 你需要对数据库迁移项目进行更多控制。
- 故障转移时，故障转移不会有很大的容差。
- 数据库迁移服务可执行文件不能安装在你的环境中。
- 数据库迁移服务可执行文件不能访问数据库备份。
- 没有对主机操作系统的访问权限，或者没有管理员权限。
- 不能从环境中打开到 Azure 的网络端口。
- 通过选项将备份直接存储到 Azure Blob 存储 `TO URL` 。
- 需要使用差异备份。

> [!NOTE]
> 建议使用数据库迁移服务将数据库从 SQL Server 自动迁移到 SQL 托管实例。 此服务在后端使用相同的 LRS 云服务，并以模式传输日志 `NORECOVERY` 。 如果数据库迁移服务不完全支持你的方案，请考虑使用 LRS 手动协调迁移。

## <a name="how-it-works"></a>工作原理

使用 LRS 生成自定义解决方案，将数据库迁移到云需要几个业务流程步骤，如本节后面的关系图和表中所示。

迁移包括在启用了的 SQL Server 上进行完整的数据库备份 `CHECKSUM` ，并将备份文件复制到 Azure Blob 存储。 LRS 用于将备份文件从 Blob 存储还原到 SQL 托管实例。 Blob 存储是 SQL Server 与 SQL 托管实例之间的中间存储。

LRS 为还原完整备份后添加的任何新的差异或日志备份监视 Blob 存储。 然后，LRS 会自动还原这些新文件。 你可以使用服务来监视 SQL 托管实例上还原的备份文件的进度，并且可以在必要时停止该进程。

对于备份文件，LRS 不需要特定的命名约定。 它会扫描放置在 Blob 存储中的所有文件，并构造仅读取文件头的备份链。 在迁移过程中，数据库处于 "正在还原" 状态。 在 [NORECOVERY](/sql/t-sql/statements/restore-statements-transact-sql#comparison-of-recovery-and-norecovery) 模式下还原数据库，因此在迁移过程完成之前，不能使用这些数据库进行读取或写入。 

如果要迁移多个数据库，则需要：
 
- 将每个数据库的备份放置在 Blob 存储的单独文件夹中。
- 为每个数据库单独启动 LRS。
- 指定不同的路径来分隔 Blob 存储文件夹。 

可以在 *"自动完成"* 或 " *连续* " 模式下启动 LRS。 当你在自动完成模式下启动此功能时，将在还原最后一个指定的备份文件时自动完成迁移。 当你在连续模式下启动 LRS 时，该服务将持续恢复添加的任何新备份文件，并且迁移将仅在手动切换时完成。 

建议你在完成最后的日志结尾备份后手动剪切，并在 SQL 托管实例上显示为已还原。 最后一个转换步骤会使数据库联机，并可用于 SQL 托管实例上的 "读" 和 "写"。

停止 LRS 后，无论是通过自动完成还是通过切换进行手动操作，都无法恢复 SQL 托管实例上联机的数据库的还原过程。 若要在迁移完成后还原其他备份文件，请完成完成后，需要删除数据库。 还需要重新启动 LRS，从头开始还原整个备份链。

:::image type="content" source="./media/log-replay-service-migrate/log-replay-service-conceptual.png" alt-text="说明 SQL 托管实例的日志重播服务业务流程步骤的关系图。" border="false":::
    
| Operation | 详细信息 |
| :----------------------------- | :------------------------- |
| **1. 将数据库备份从 SQL Server 复制到 Blob 存储**。 | 使用 [Azcopy](/azure/storage/common/storage-use-azcopy-v10) 或 [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)将完整备份、差异备份和日志备份从 SQL Server 复制到 Blob 存储容器。 <br /><br />使用任意文件名。 LRS 不需要特定的文件命名约定。<br /><br />迁移多个数据库时，每个数据库都需要一个单独的文件夹。 |
| **2. 在云中启动 LRS**。 | 可以通过选择 cmdlet 来重新启动服务： PowerShell ([azsqlinstancedatabaselogreplay](/powershell/module/az.sql/start-azsqlinstancedatabaselogreplay)) 或 Azure CLI ([az_sql_midb_log_replay_start cmdlet](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_start)) 。 <br /><br /> 为指向 Blob 存储上的备份文件夹的每个数据库单独启动 LRS。 <br /><br /> 启动该服务后，它将从 Blob 存储容器中获取备份，并开始在 SQL 托管实例上还原它们。<br /><br /> 如果在连续模式下启动 LRS，则在还原所有初始上传的备份后，该服务会监视上传到该文件夹的所有新文件。 服务会根据日志序列号 (LSN) 链持续应用日志，直到停止。 |
| **2.1. 监视操作的进度**。 | 可以通过选择 cmdlet 来监视还原操作的进度： PowerShell ([azsqlinstancedatabaselogreplay](/powershell/module/az.sql/get-azsqlinstancedatabaselogreplay)) 或 Azure CLI ([az_sql_midb_log_replay_show cmdlet](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_show)) 。 |
| **2.2. 如有必要，停止操作**。 | 如果需要停止迁移过程，可选择 cmdlet： PowerShell ([azsqlinstancedatabaselogreplay](/powershell/module/az.sql/stop-azsqlinstancedatabaselogreplay)) 或 Azure CLI ([az_sql_midb_log_replay_stop](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_stop)) 。 <br /><br /> 停止操作将删除正在 SQL 托管实例上还原的数据库。 停止某一操作后，将无法恢复数据库的 LRS。 需要从头开始重新启动迁移过程。 |
| **3. 准备就绪** 后，请切换到云中。 | 停止应用程序和工作负荷。 执行最后的日志尾备份，并将其上传到 Azure Blob 存储。<br /><br /> 通过使用所选 cmdlet 启动 LRS 操作来完成转换 `complete` ： PowerShell ([Azsqlinstancedatabaselogreplay](/powershell/module/az.sql/complete-azsqlinstancedatabaselogreplay)) 或 Azure CLI [az_sql_midb_log_replay_complete](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_complete)。 此操作将停止 LRS，并使数据库联机以便在 SQL 托管实例上进行读取和写入。<br /><br /> Repoint 从 SQL Server 到 SQL 托管实例的应用程序连接字符串。 |

## <a name="requirements-for-getting-started"></a>入门的要求

### <a name="sql-server-side"></a>SQL Server 侧
- SQL Server 2008-2019
-  (一个或多个文件的数据库的完整备份) 
-  (一个或多个文件的差异备份) 
- 不会为事务日志文件 (拆分日志备份) 
- `CHECKSUM` 启用备份 (必需的) 

### <a name="azure-side"></a>Azure 端
- PowerShell Az .SQL module 版本2.16.0 或更高版本 (通过[Azure Cloud Shell](/azure/cloud-shell/) [安装](https://www.powershellgallery.com/packages/Az.Sql/)或访问) 
- [安装](/cli/azure/install-azure-cli)Azure CLI 版本2.19.0 或更高版本 () 
- 已预配 Azure Blob 存储容器
- 共享访问签名 (SAS) 安全令牌，其中包含为 Blob 存储容器生成的读取和列出权限

### <a name="migration-of-multiple-databases"></a>迁移多个数据库
必须将不同数据库的备份文件放置在 Blob 存储的单独文件夹中。

指向 Blob 存储中的相应文件夹，分别为每个数据库启动 LRS。 LRS 可以支持每个单个托管实例最多支持100的还原进程。

### <a name="azure-rbac-permissions"></a>Azure RBAC 权限
通过所提供的客户端运行 LRS 需要以下 Azure 角色之一：
- 订阅所有者角色
- [托管实例参与者](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor) 角色
- 具有以下权限的自定义角色： `Microsoft.Sql/managedInstances/databases/*`

## <a name="best-practices"></a>最佳做法

建议遵循以下最佳做法：
- 运行 [数据迁移助手](/sql/dma/dma-overview) ，验证数据库是否已准备好迁移到 SQL 托管实例。 
- 将完整备份和差异备份拆分为多个文件，而不是使用单个文件。
- 启用备份压缩。
- 使用 Cloud Shell 运行脚本，因为它将始终更新为发布的最新 cmdlet。
- 启动 LRS 后，计划在47小时内完成迁移。 这是一个宽限期，阻止安装系统管理的软件修补程序。

> [!IMPORTANT]
> - 在迁移过程完成之前，不能使用通过 LRS 还原的数据库。 
> - LRS 不支持在迁移期间对数据库进行只读访问。
> - 迁移完成后，迁移过程将终止，因为 LRS 不支持恢复过程。

## <a name="steps-to-execute"></a>要执行的步骤

### <a name="make-backups-of-sql-server"></a>备份 SQL Server

可以通过使用以下任一选项来备份 SQL Server：

- 备份到本地磁盘存储，并将文件上传到 Azure Blob 存储（如果你的环境将直接备份限制为 Blob 存储）。
- `TO URL`如果你的环境和安全过程允许，则通过 t-sql 中的选项直接备份到 Blob 存储。 

将想要迁移到完整恢复模式的数据库设置为允许日志备份。

```SQL
-- To permit log backups, before the full database backup, modify the database to use the full recovery
USE master
ALTER DATABASE SampleDB
SET RECOVERY FULL
GO
```

若要在本地存储上手动创建数据库的完整备份、差异备份和日志备份，请使用以下示例 T-sql 脚本。 请确保 `CHECKSUM` 已启用该选项，因为它对于 LRS 是必需的。

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

Azure Blob 存储用作 SQL Server 与 SQL 托管实例之间备份文件的中间存储。 若要在存储帐户中创建新的存储帐户和 blob 容器，请执行以下步骤：

1. [创建存储帐户](../../storage/common/storage-account-create.md?tabs=azure-portal)。
2. 创建存储帐户中[的 blob 容器](../../storage/blobs/storage-quickstart-blobs-portal.md)。

### <a name="copy-backups-from-sql-server-to-blob-storage"></a>将 SQL Server 中的备份复制到 Blob 存储

使用 LRS 将数据库迁移到托管实例时，可以使用以下方法将备份上传到 Blob 存储：
- 使用 SQL Server 本地 [备份到 URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url) 功能
- 使用 [Azcopy](/azure/storage/common/storage-use-azcopy-v10) 或 [Azure 存储资源管理器](https://azure.microsoft.com/en-us/features/storage-explorer) 将备份上传到 blob 容器
- 使用 Azure 门户中的存储资源管理器

### <a name="make-backups-from-sql-server-directly-to-blob-storage"></a>将 SQL Server 中的备份直接备份到 Blob 存储
如果你的公司和网络策略允许这样做，则一种替代方法是使用 "SQL Server 本机 [备份到 URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url) " 选项将 SQL Server 直接备份到 Blob 存储。 如果可以采用此选项，则无需在本地存储上进行备份，并将其上传到 Blob 存储。

第一步，此操作要求你为 Blob 存储生成 SAS 身份验证令牌，然后将令牌导入 SQL Server。 第二步是 `TO URL` 在 t-sql 中用选项进行备份。 确保所有备份都 `CHEKSUM` 启用了选项。

为了便于参考，以下示例代码将备份到 Blob 存储。 此示例不包括有关如何导入 SAS 令牌的说明。 有关详细说明，包括如何生成 SAS 令牌并将其导入到 SQL Server，请在本教程中将 [Azure Blob 存储与 SQL Server 配合使用](/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016#1---create-stored-access-policy-and-shared-access-storage)。 

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

Azure Blob 存储用作 SQL Server 与 SQL 托管实例之间备份文件的中间存储。 需要为 LRS 生成仅具有列表和读取权限的 SAS 身份验证令牌。 令牌可让 LRS 访问 Blob 存储，并使用备份文件将它们还原到 SQL 托管实例上。 

按照以下步骤生成令牌：

1. 从 Azure 门户打开存储资源管理器。
2. 展开 " **Blob 容器**"。
3. 右键单击 "blob 容器"，然后选择 " **获取共享访问签名**"。

   :::image type="content" source="./media/log-replay-service-migrate/lrs-sas-token-01.png" alt-text="显示用于生成 s 身份验证令牌的选项的屏幕截图。":::

4. 选择令牌过期的时间范围。 确保令牌在迁移期间有效。
5. 为令牌选择时区： UTC 或本地时间。
    
   > [!IMPORTANT]
   > 令牌的时区和托管实例可能不匹配。 请确保 SAS 令牌具有适当的时间有效性，并考虑时区。 如果可能，请将时区设置为之前和之后的计划迁移时段。
6. 仅选择 " **读取** " 和 " **列出** 权限"。

   > [!IMPORTANT]
   > 不要选择任何其他权限。 否则，LRS 将无法启动。 此安全要求是设计的。
7. 选择“创建”。

   :::image type="content" source="./media/log-replay-service-migrate/lrs-sas-token-02.png" alt-text="显示令牌过期、时区和权限选项以及 &quot;创建&quot; 按钮的屏幕截图。":::

将生成带有指定时间有效性的 SAS 身份验证。 需要令牌的 URI 版本，如以下屏幕截图所示。

:::image type="content" source="./media/log-replay-service-migrate/lrs-generated-uri-token.png" alt-text="屏幕截图显示了 s 标记的 U R I 版本的示例。":::

### <a name="copy-parameters-from-the-sas-token"></a>从 SAS 令牌复制参数

使用 SAS 令牌启动 LRS 之前，需要了解其结构。 生成的 SAS 令牌的 URI 包含用问号分隔的两部分 (`?`) ，如以下示例中所示：

:::image type="content" source="./media/log-replay-service-migrate/lrs-token-structure.png" alt-text="示例 U R I 用于为日志重播服务生成的 S 标记。" border="false":::

第一个部分（从开始， `https://` 直到问号 (`?`) ）用于 `StorageContainerURI` 作为 LRS 输入到的参数。 它提供有关存储数据库备份文件的文件夹的 LRS 信息。

第二部分是在问号后开始 (`?`) 并一直等到字符串末尾，就是 `StorageContainerSasToken` 参数。 这是实际的签名身份验证令牌，在指定的时间内有效。 此部分不一定需要从开始， `sp=` 如示例中所示。 你的情况可能有所不同。

按如下所示复制参数：

1. 复制标记的第一部分，从 `https://` 所有方法开始，直到问号 (`?`) 。 `StorageContainerUri`在 PowerShell 中将其用作参数，或使用 Azure CLI 启动 LRS。

   :::image type="content" source="./media/log-replay-service-migrate/lrs-token-uri-copy-part-01.png" alt-text="显示复制标记第一部分的屏幕截图。":::

2. 复制标记的第二部分，从问号开始 (`?`) 一直到字符串末尾为止。 `StorageContainerSasToken`在 PowerShell 中将其用作参数，或使用 Azure CLI 启动 LRS。

   :::image type="content" source="./media/log-replay-service-migrate/lrs-token-uri-copy-part-02.png" alt-text="显示复制标记的第二部分的屏幕截图。":::
   
> [!NOTE]
> 复制标记的任一部分时，请勿包含问号。

### <a name="log-in-to-azure-and-select-a-subscription"></a>登录到 Azure 并选择订阅

使用以下 PowerShell cmdlet 登录到 Azure：

```powershell
Login-AzAccount
```

使用以下 PowerShell cmdlet 选择托管实例所在的相应订阅：

```powershell
Select-AzSubscription -SubscriptionId <subscription ID>
```

## <a name="start-the-migration"></a>开始迁移

通过启动 LRS 开始迁移。 可以在 "自动完成" 或 "连续" 模式下启动该服务。 

如果使用自动完成模式，则在还原指定的最后一个备份文件时，迁移将自动完成。 此选项要求 "启动" 命令指定上一个备份文件的文件名。 

使用连续模式时，该服务将持续还原已添加的任何新备份文件。 迁移仅会在手动切换时完成。 

### <a name="start-lrs-in-autocomplete-mode"></a>在自动完成模式下启动 LRS

若要在自动完成模式下启动 LRS，请使用以下 PowerShell 或 Azure CLI 命令。 使用参数指定上一次备份文件名 `-LastBackupName` 。 还原指定的备份文件后，该服务会自动启动转换。

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

以下是使用 PowerShell 在连续模式下启动 LRS 的示例：

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

PowerShell 和 CLI 客户端在连续模式下启动 LRS 是同步的。 这意味着客户端将等待 API 响应报告成功或失败，以启动作业。 

在等待过程中，命令不会将控制返回给命令提示符。 如果正在编写迁移体验的脚本，并且你需要 LRS start 命令来立即回送，以便继续执行脚本的其余部分，可以使用交换机作为后台作业运行 PowerShell `-AsJob` 。 例如：

```PowerShell
$lrsjob = Start-AzSqlInstanceDatabaseLogReplay <required parameters> -AsJob
```

启动后台作业时，作业对象会立即返回，即使该作业需要很长时间才能完成。 当该作业运行时，你可以继续在此会话中工作而不会发生中断。 有关以后台作业的形式运行 PowerShell 的详细信息，请参阅 [PowerShell 启动-作业](/powershell/module/microsoft.powershell.core/start-job#description) 文档。

同样，若要在 Linux 上以后台进程启动 Azure CLI 命令，请 `&` 在 LRS start 命令的末尾使用与号 () ：

```CLI
az sql midb log-replay start <required parameters> &
```

> [!IMPORTANT]
> 启动 LRS 后，任何系统管理的软件修补程序将在47小时内停止。 在此窗口之后，下一个自动软件修补程序将自动停止 LRS。 如果发生这种情况，则无法恢复迁移，需要从头开始重新启动。 

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

如果需要停止迁移，请使用以下 cmdlet。 停止迁移将删除 SQL 托管实例上的还原数据库，因此无法继续迁移。

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

## <a name="complete-the-migration-continuous-mode"></a>完成迁移 (连续模式) 

如果在连续模式下启动 LRS，则在确保所有备份都已还原后，启动转换将完成迁移。 转换后，将迁移数据库并准备好进行读写访问。

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
- 要还原的数据库在迁移过程中不能用于只读访问。
- 启动 LRS 后，系统托管的软件修补程序将被阻止47小时。 此时间段到期后，下一软件更新将停止 LRS。 然后，需要从头开始重新启动 LRS。
- LRS 要求在启用选项的情况备份 SQL Server 上的数据库 `CHECKSUM` 。
- 必须为整个 Azure Blob 存储容器生成 LRS 要使用的 SAS 令牌，并且它必须仅具有 "读取" 和 "列出" 权限。
- 不同数据库的备份文件必须置于 Blob 存储的单独文件夹中。
- 必须为每个数据库单独启动 LRS，该数据库指向 Blob 存储上包含备份文件的不同文件夹。
- LRS 可以支持每个单个托管实例最多支持100的还原进程。

## <a name="troubleshooting"></a>疑难解答

启动 LRS 后，使用 monitoring cmdlet (`get-azsqlinstancedatabaselogreplay` 或 `az_sql_midb_log_replay_show`) 查看操作的状态。 如果 LRS 在一段时间后无法启动并出现错误，请检查最常见的问题：

- SQL 托管实例上的现有数据库与尝试从 SQL Server 中迁移的数据库的名称相同吗？ 通过重命名数据库之一来解决此冲突。
- 数据库备份是否是通过选项创建的 SQL Server `CHECKSUM` ？
- SAS 令牌上的权限是否只读取和列出 LRS？
- 是否在问号后复制了 LRS 的 SAS 令牌 (`?`) ，内容的开始时间如下： `sv=2020-02-10...` ？ 
- SAS 令牌的有效时间是否适用于启动和完成迁移的时间范围？ 由于用于 SQL 托管实例和 SAS 令牌的不同时区，可能会出现不匹配的情况。 尝试重新生成 SAS 令牌，并扩展当前日期前后的时间范围的令牌有效性。
- 数据库名称、资源组名称和托管实例名称拼写是否正确？
- 如果在自动完成模式下启动了 LRS，则为指定的上一备份文件的有效文件名？

## <a name="next-steps"></a>后续步骤
- 了解有关 [将 SQL Server 迁移到 SQL 托管实例](../migration-guides/managed-instance/sql-server-to-managed-instance-guide.md)的详细信息。
- 了解 [SQL Server 和 SQL 托管实例之间的差异](transact-sql-tsql-differences-sql-server.md)的详细信息。
- 详细了解 [迁移到 Azure 的成本和大小工作负载的最佳实践](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs)。
