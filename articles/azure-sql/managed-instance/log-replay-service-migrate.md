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
ms.date: 02/17/2021
ms.openlocfilehash: 7892b1fe0fcad77d1fde8b44f4a8745b5c7dd334
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2021
ms.locfileid: "100654077"
---
# <a name="migrate-databases-from-sql-server-to-sql-managed-instance-using-log-replay-service"></a>使用日志重播服务将数据库从 SQL Server 迁移到 SQL 托管实例
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

本文介绍如何使用日志重播服务 (LRS) 手动配置从 SQL Server 2008-2019 到 SQL 托管实例的数据库迁移。 这是一项云服务，它基于 "无恢复" 模式下的 SQL Server 日志传送技术为托管实例启用了云服务。 如果在需要更多控制或存在停机时间的 LRS 时，不能使用数据迁移服务 (DM) ，则应使用。

## <a name="when-to-use-log-replay-service"></a>何时使用日志重播服务

在 [AZURE DMS](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance) 无法用于迁移的情况下，可以使用 POWERSHELL、CLI CMDLET 或 API 直接使用 LRS 云服务，手动生成数据库迁移并将其安排到 SQL 托管实例。 

在以下某些情况下，你可能需要考虑使用 LRS 云服务：
- 你的数据库迁移项目需要更多控制
- 迁移转换的故障转移存在少许容错
- 无法在你的环境中安装 DMS 可执行文件
- DMS 可执行文件没有对数据库备份的文件访问权限
- 对主机操作系统的访问不可用，或没有管理员权限

> [!NOTE]
> 使用 Azure DMS 将数据库从 SQL Server 迁移到 SQL 托管实例的推荐自动方式。 在无恢复模式下，此服务将在后端使用相同的 LRS 云服务和日志传送。 当 Azure DMS 不完全支持你的方案时，应考虑使用 LRS 手动安排迁移。

## <a name="how-does-it-work"></a>工作原理

使用 LRS 生成自定义解决方案，将数据库迁移到云时，需要使用下表中所示的几个业务流程步骤。

迁移需要在 SQL Server 上进行完整的数据库备份，并将备份文件复制到 Azure Blob 存储。 LRS 用于将备份文件从 Azure Blob 存储还原到 SQL 托管实例。 Azure Blob 存储用作 SQL Server 与 SQL 托管实例之间的中间存储。

LRS 将监视 Azure Blob 存储中的任何新差异，或还原完整备份后添加的日志备份，并将自动还原所有添加的新文件。 可以使用服务监视 SQL 托管实例上还原的备份文件的进度，如果需要，还可以中止该过程。 在迁移过程中还原的数据库将处于还原模式，并且在完成此过程之前，无法用于读取或写入。

可以在 "自动完成" 或连续模式下启动 LRS。 在自动完成模式下启动时，如果已还原指定的最后一个备份文件，则迁移将自动完成。 在连续模式下启动时，该服务将持续恢复添加的任何新备份文件，并且迁移将在手动切换时完成。 最终切换步骤会使数据库在 SQL 托管实例上可用于读取和写入。 

  ![针对 SQL 托管实例说明的日志重播服务协调步骤](./media/log-replay-service-migrate/log-replay-service-conceptual.png)

| Operation | 详细信息 |
| :----------------------------- | :------------------------- |
| **1. 将数据库备份从 SQL Server 复制到 Azure Blob 存储**。 | -使用 [Azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10) 或 [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)将完整备份、差异备份和日志备份从 SQL Server 复制到 Azure Blob 存储。 <br />-在迁移多个数据库时，每个数据库都需要一个单独的文件夹。 |
| **2. 在云中启动 LRS 服务**。 | -可通过选择 cmdlet 来启动服务： <br /> PowerShell [启动-azsqlinstancedatabaselogreplay](https://docs.microsoft.com/powershell/module/az.sql/start-azsqlinstancedatabaselogreplay) <br /> CLI [az_sql_midb_log_replay_start cmdlet](https://docs.microsoft.com/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_start)。 <br /><br />-启动后，该服务将从 Azure Blob 存储中获取备份，并开始在 SQL 托管实例上还原它们。 <br /> -还原所有初始上载的备份后，该服务会监视上传到该文件夹的任何新文件，并且将基于 LSN 链持续应用日志，直到该服务停止。 |
| **2.1. 监视操作进度**。 | -可以选择或 cmdlet 来监视还原操作的进度： <br /> PowerShell [azsqlinstancedatabaselogreplay](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabaselogreplay) <br /> CLI [az_sql_midb_log_replay_show cmdlet](https://docs.microsoft.com/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_show)。 |
| **2.2. 如果需要，Stop\abort 操作**。 | -如果迁移过程需要中止，则可以选择使用 cmdlet 来停止操作： <br /> PowerShell [停止-azsqlinstancedatabaselogreplay](https://docs.microsoft.com/powershell/module/az.sql/stop-azsqlinstancedatabaselogreplay) <br /> CLI [az_sql_midb_log_replay_stop](https://docs.microsoft.com/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_stop) cmdlet。 <br /><br />-这将导致删除 SQL 托管实例上的数据库还原。 <br />-停止后，不能继续为数据库 LRS。 需要从头开始迁移过程。 |
| **3. 准备就绪时转换到云**。 | -所有备份都已还原到 SQL 托管实例后，通过选择 API 调用（或 cmdlet）启动 LRS complete 操作来完成转换： <br />PowerShell [azsqlinstancedatabaselogreplay](https://docs.microsoft.com/powershell/module/az.sql/complete-azsqlinstancedatabaselogreplay) <br /> CLI [az_sql_midb_log_replay_complete](https://docs.microsoft.com/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_complete) cmdlet。 <br /><br />-这将导致停止 LRS 服务并恢复托管实例上的数据库。 <br />-将应用程序连接字符串从 SQL Server Repoint 到 SQL 托管实例。 <br />-On 操作完成数据库可用于云中的 R/W 操作。 |

## <a name="requirements-for-getting-started"></a>入门的要求

### <a name="sql-server-side"></a>SQL Server 侧
- SQL Server 2008-2019
-  (一个或多个文件的数据库的完整备份) 
-  (一个或多个文件的差异备份) 
- 事务日志文件的日志备份 (未拆分) 
- **必须按必需启用校验和**

### <a name="azure-side"></a>Azure 端
-   PowerShell Az .SQL module 版本2.16.0 或更高版本 ([安装](https://www.powershellgallery.com/packages/Az.Sql/)或使用 Azure [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/)) 
-   CLI 版本2.19.0 或更高版本 ([安装](https://docs.microsoft.com/cli/azure/install-azure-cli)) 
-   已预配 Azure Blob 存储
-   SAS 安全令牌，其中包含为 blob 存储生成的 " **读取** " 和 "仅 **列出** " 权限

## <a name="best-practices"></a>最佳做法

作为最佳做法，强烈建议执行以下操作：
- 运行 [数据迁移助手](https://docs.microsoft.com/sql/dma/dma-overview) 来验证数据库是否不会将任何问题迁移到 SQL 托管实例。 
- 将完整备份和差异备份拆分为多个文件，而不是一个文件。
- 启用备份压缩。
- 使用 Cloud Shell 执行脚本，因为它始终会更新到发布的最新 cmdlet 中。
- 计划在47小时内完成迁移，因为已启动 LRS 服务。

> [!IMPORTANT]
> - 在迁移过程完成之前，不能使用 LRS 还原的数据库。 这是因为基础技术在无恢复模式下是日志传送。
> - LRS 不支持日志传送的备用模式，因为 SQL 托管实例和最新的市场 SQL Server 版本之间存在差异。

## <a name="steps-to-execute"></a>要执行的步骤

## <a name="copy-backups-from-sql-server-to-azure-blob-storage"></a>将备份从 SQL Server 复制到 Azure Blob 存储

以下两种方法可用于将备份复制到使用 LRS 将数据库迁移到托管实例中的 blob 存储：
- 使用 SQL Server 本地 [备份到 URL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url) 功能。
- 使用 [Azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)或 [Azure 存储资源管理器](https://azure.microsoft.com/en-us/features/storage-explorer)将备份复制到 Blob 容器。 

## <a name="create-azure-blob-and-sas-authentication-token"></a>创建 Azure Blob 和 SAS 身份验证令牌

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
7. 确保选中 "只读" 和 "仅列出" 权限
8. 单击“创建”
9. 在 URI 中复制以 "sv =" 开头的令牌，以便在代码中使用

> [!IMPORTANT]
> 需要读取和列出 Azure Blob 存储的 SAS 令牌的权限。 如果为 SAS 身份验证令牌授予了其他任何权限，则启动 LRS 服务将会失败。 这些安全要求是由设计决定的。

## <a name="log-in-to-azure-and-select-subscription"></a>登录到 Azure 并选择 "订阅"

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

```powershell
Start-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName" `
    -Collation "SQL_Latin1_General_CP1_CI_AS" `
    -StorageContainerUri "https://test.blob.core.windows.net/testing" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D" `
    -AutoComplete -LastBackupName "last_backup.bak"
```

在自动完成模式下启动 LRS-CLI 示例：

```cli
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb -a --last-bn "backup.bak"
    --storage-uri "https://test.blob.core.windows.net/testing"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

### <a name="start-lrs-in-continuous-mode"></a>在连续模式下启动 LRS

在连续模式下启动 LRS-PowerShell 示例：

```powershell
Start-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName" `
    -Collation "SQL_Latin1_General_CP1_CI_AS" -StorageContainerUri "https://test.blob.core.windows.net/testing" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

在连续模式下启动 LRS-CLI 示例：

```cli
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb
    --storage-uri "https://test.blob.core.windows.net/testing"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

> [!IMPORTANT]
> 启动 LRS 后，系统管理的任何软件修补程序将在接下来的47小时内停止。 在传递此窗口时，下一个自动软件修补程序将自动停止正在进行的 LRS。 在这种情况下，无法恢复迁移，需要从头开始重新启动。 

## <a name="monitor-the-migration-progress"></a>监视迁移进度

若要监视迁移操作的进度，请使用以下 PowerShell 命令：

```powershell
Get-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName"
```

若要监视迁移操作的进度，请使用以下 CLI 命令：

```cli
az sql midb log-replay show -g mygroup --mi myinstance -n mymanageddb
```

## <a name="stop-the-migration"></a>停止迁移

如果需要停止迁移，请使用以下 cmdlet。 停止迁移将删除 SQL 托管实例上的还原数据库，因为无法继续迁移。

若要 stop\abort 迁移过程，请使用以下 PowerShell 命令：

```powershell
Stop-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName"
```

若要 stop\abort 迁移过程，请使用以下 CLI 命令：

```cli
az sql midb log-replay stop -g mygroup --mi myinstance -n mymanageddb
```

## <a name="complete-the-migration-continuous-mode"></a>完成迁移 (连续模式) 

如果在连续模式下启动 LRS，则确保所有备份都已还原后，启动转换将完成迁移。 在转换完成时，将迁移数据库并准备好进行读写访问。

若要在 LRS 连续模式下完成迁移过程，请使用以下 PowerShell 命令：

```powershell
Complete-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
-InstanceName "ManagedInstance01" `
-Name "ManagedDatabaseName" -LastBackupName "last_backup.bak"
```

若要在 LRS 连续模式下完成迁移过程，请使用以下 CLI 命令：

```cli
az sql midb log-replay complete -g mygroup --mi myinstance -n mymanageddb --last-backup-name "backup.bak"
```

## <a name="next-steps"></a>后续步骤
- 了解有关 [将 SQL Server 迁移到 SQL 托管实例](../migration-guides/managed-instance/sql-server-to-managed-instance-guide.md)的详细信息。
- 详细了解 [SQL Server 和 AZURE SQL 托管实例之间的差异](transact-sql-tsql-differences-sql-server.md)。
- 详细了解 [迁移到 Azure 的成本和大小工作负载的最佳实践](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs)。
