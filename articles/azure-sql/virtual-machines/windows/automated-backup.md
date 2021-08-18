---
title: 适用于 SQL Server 2016/2017 Azure VM 的自动备份 v2 | Microsoft Docs
description: 本文介绍适用于在 Azure 上运行的 SQL Server 2016/2017 VM 的自动备份功能。 本文仅适用于使用 Resource Manager 的 VM。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.assetid: ebd23868-821c-475b-b867-06d4a2e310c7
ms.service: virtual-machines-sql
ms.subservice: backup
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/03/2018
ms.author: mathoma
ms.reviewer: jroth
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a93214a8577dc298551e4e819282a58f10a72f38
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121746604"
---
# <a name="automated-backup-v2-for-azure-virtual-machines-resource-manager"></a>用于 Azure 虚拟机（资源管理器）的自动备份 v2
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [SQL Server 2014](automated-backup-sql-2014.md)
> * [SQL Server 2016 +](automated-backup.md)

自动备份 v2 会在运行 SQL Server 2016 或更高版本 Standard、Enterprise 或 Developer 版本的 Azure VM 上，自动为所有现有数据库和新数据库配置 [Microsoft Azure 的托管备份](/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure)。 这样，便可以配置使用持久 Azure Blob 存储的定期数据库备份。 自动备份 v2 依赖于 [SQL Server 基础架构即服务 (IaaS) 代理扩展](sql-server-iaas-agent-extension-automate-management.md)。

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>先决条件
若要使用自动备份 v2，请查看以下先决条件：

**操作系统**：

- Windows Server 2012 R2 或更高版本

**SQL Server 版本**：

- SQL Server 2016 或更高版本：Developer、Standard 或 Enterprise

> [!NOTE]
> 有关 SQL Server 2014，请参阅[适用于 SQL Server 2014 的自动备份](automated-backup-sql-2014.md)。

**数据库配置**：

- 目标用户数据库必须使用完整恢复模式。 系统数据库不需要使用完整恢复模型。 但是，如果需要为模型或 MSDB 创建日志备份，则必须使用完整恢复模型。 有关对备份使用完整恢复模型产生的影响的详细信息，请参阅[使用完整恢复模型的备份](/previous-versions/sql/sql-server-2008-r2/ms190217(v=sql.105))。 
- 已在[完全管理模式](sql-agent-extension-manually-register-single-vm.md#upgrade-to-full)下，将 SQL Server VM 注册到 SQL IaaS 代理扩展。 
-  自动备份依赖于完整 [SQL Server IaaS 代理扩展](sql-server-iaas-agent-extension-automate-management.md)。 因此，只有默认实例或单个命名实例的目标数据库支持自动备份。 如果没有默认实例，并且存在多个命名实例，则 SQL IaaS 扩展将失败，自动备份将无法工作。 

## <a name="settings"></a>设置
下表描述了可为自动备份 v2 配置的选项。 实际配置步骤根据你使用的是 Azure 门户还是 Azure Windows PowerShell 命令而有所不同。

### <a name="basic-settings"></a>基本设置

| 设置 | 范围（默认值） | 说明 |
| --- | --- | --- |
| **自动备份** | 启用/禁用（已禁用） | 为运行 SQL Server 2016/2017 Developer、Standard 或 Enterprise 的 Azure VM 启用或禁用自动备份。 |
| **保持期** | 1-30 天（30 天） | 保留备份的天数。 |
| **存储帐户** | Azure 存储帐户 | 用于在 Blob 存储中存储自动备份文件的 Azure 存储帐户。 在此位置创建容器，用于存储所有备份文件。 备份文件命名约定包括日期、时间和数据库 GUID。 |
| **加密** |启用/禁用（已禁用） | 启用或禁用加密。 启用加密时，用于还原备份的证书位于指定的存储帐户中。 该证书使用具有相同命名约定的相同 **自动备份** 容器。 如果密码发生更改，则使用该密码生成新证书，但旧证书在备份之前仍会还原。 |
| **密码** |密码文本 | 加密密钥的密码。 仅当启用了加密时才需要此设置。 若要还原加密的备份，必须具有创建该备份时使用的正确密码和相关证书。 |

### <a name="advanced-settings"></a>高级设置

| 设置 | 范围（默认值） | 说明 |
| --- | --- | --- |
| **系统数据库备份** | 启用/禁用（已禁用） | 启用后，此功能还会备份系统数据库：Master、MSDB 和 Model。 对于 MSDB 和 Model 数据库，如果想要创建日志备份，请验证这些数据库是否处于完整恢复模式。 永远不会针对 Master 数据库创建日志备份。 此外，不会创建 TempDB 的备份。 |
| **备份计划** | 手动/自动（自动） | 默认情况下，系统会根据日志的增长情况自动确定备份计划。 用户可以使用手动备份计划来指定备份时段。 此情况下，仅按指定的频率、在特定日期的指定时段内进行备份。 |
| **完整备份频率** | 每日/每周 | 完整备份的频率。 上述两种情况下，均在下一个计划的时间段内开始完整备份。 如果选择“每周”，备份可能会跨越好几天，直到所有数据库都已成功备份。 |
| **完整备份开始时间** | 00:00 – 23:00 (01:00) | 在给定的日期可以开始执行完整备份的时间。 |
| **完整备份时段** | 1 – 23 小时（1 小时） | 在给定的日期可以执行完整备份的时段持续时间。 |
| **日志备份频率** | 5 – 60 分钟（60 分钟） | 日志备份的频率。 |

## <a name="understanding-full-backup-frequency"></a>了解完整备份频率
必须了解每日与每周完整备份之间的差别。 请考虑以下两个示例解决方案。

### <a name="scenario-1-weekly-backups"></a>应用场景 1：每周备份
某个 SQL Server VM 包含多个大型数据库。

在星期一，用户使用以下设置启用了自动备份 v2：

- 备份计划：**手动**
- 完整备份频率：**每周**
- 完整备份开始时间：01:00
- 完整备份时段：1 小时

这意味着，下一个可用备份时段为星期二凌晨 1 点，持续时间为 1 小时。 到时，自动备份将开始逐个备份数据库。 在此方案中，由于数据库非常大，完整备份将完成前几个数据库。 但是，一小时后，并非所有数据库都能得到备份。

如果出现此情况，自动备份会在第二天（即星期三凌晨 1 点）备份剩余的数据库，此操作持续 1 小时。 如果到时仍无法备份所有数据库，系统会在后一天的同一时间再次尝试备份。 在所有数据库均已成功备份之前，系统会不断执行此计划。

再次达到星期二后，自动备份会重新开始备份所有数据库。

此方案表明，自动备份仅在指定的时段内运行，且每个数据库每周备份一次。 另外，如果在一天内无法完成所有备份，备份可能会跨越好几天。

### <a name="scenario-2-daily-backups"></a>应用场景 2：每日备份
某个 SQL Server VM 包含多个大型数据库。

在星期一，用户使用以下设置启用了自动备份 v2：

- 备份计划：手动
- 完整备份频率：每日
- 完整备份开始时间：22:00
- 完整备份时段：6 小时

这意味着，下一个可用备份时段为星期一晚上 10 点，持续时间为 6 小时。 到时，自动备份将开始逐个备份数据库。

然后，在星期二晚上 10 点，再次开始完整备份所有数据库，此操作持续为 6 小时。

> [!IMPORTANT]
> 计划每日备份时，建议安排一个较宽的时段，确保在此时间范围内可以备份所有数据库。 有大量的数据要备份时，这种设置尤其重要。

## <a name="configure-new-vms"></a>配置新 VM

在资源管理器部署模型中创建新的 SQL Server 2016 或 2017 虚拟机时，使用 Azure 门户配置自动备份 v2。

在“SQL Server 设置”选项卡上，选择“自动备份”下的“启用”  。 下面的 Azure 门户屏幕截图显示了“SQL 自动备份”设置。

![Azure 门户中的 SQL 自动备份配置](./media/automated-backup/automated-backup-blade.png)

> [!NOTE]
> 自动备份 v2 默认已禁用。

## <a name="configure-existing-vms"></a>配置现有 VM

对于现有的 SQL Server 虚拟机，请转到[SQL 虚拟机资源](manage-sql-vm-portal.md#access-the-resource)，然后选择“备份”以配置自动备份。

![现有 VM 的 SQL 自动备份](./media/automated-backup/sql-server-configuration.png)


完成后，单击“备份”设置页底部的“应用”按钮，以保存更改 。

首次启用自动备份时，Azure 会在后台配置 SQL Server IaaS 代理。 在此期间，Azure 门户可能不会显示自动备份已配置。 请等待几分钟，以便安装和配置代理。 之后，Azure 门户将反映出新设置。

## <a name="configure-with-powershell"></a>使用 PowerShell 配置

可以使用 PowerShell 来配置自动备份 v2。 开始之前，必须：

- [下载并安装最新的 Azure PowerShell](https://aka.ms/webpi-azps)。
- 打开 Windows PowerShell 并使用 Connect-AzAccount 命令将其与帐户关联。

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

### <a name="install-the-sql-server-iaas-extension"></a>安装 SQL Server IaaS 扩展
如果通过 Azure 门户预配了 SQL Server 虚拟机，应已安装 SQL Server IaaS 扩展。 可通过调用 Get-AzVM 命令并检查 Extensions 属性，确定是否为 VM 安装了该扩展 。

```powershell
$vmname = "vmname"
$resourcegroupname = "resourcegroupname"

(Get-AzVM -Name $vmname -ResourceGroupName $resourcegroupname).Extensions 
```

如果已安装 SQL Server IaaS 代理扩展，应会看到其列为“SqlIaaSAgent”或“SQLIaaSExtension”。 此外，该扩展的“ProvisioningState”应显示“成功”。 

如果未安装或未能预配该扩展，可使用以下命令进行安装。 除了 VM 名称和资源组以外，还必须指定 VM 所在的区域 ( **$region**)。

```powershell
$region = "EASTUS2"
Set-AzVMSqlServerExtension -VMName $vmname `
    -ResourceGroupName $resourcegroupname -Name "SQLIaasExtension" `
    -Version "2.0" -Location $region 
```

### <a name="verify-current-settings"></a><a id="verifysettings"></a> 验证当前设置
如果在预配期间启用了自动备份，可以使用 PowerShell 检查当前配置。 运行 Get-AzVMSqlServerExtension 命令并检查 AutoBackupSettings 属性： 

```powershell
(Get-AzVMSqlServerExtension -VMName $vmname -ResourceGroupName $resourcegroupname).AutoBackupSettings
```

应会看到类似于下面的输出：

```
Enable                      : True
EnableEncryption            : False
RetentionPeriod             : 30
StorageUrl                  : https://test.blob.core.windows.net/
StorageAccessKey            :  
Password                    : 
BackupSystemDbs             : False
BackupScheduleType          : Manual
FullBackupFrequency         : WEEKLY
FullBackupStartTime         : 2
FullBackupWindowHours       : 2
LogBackupFrequency          : 60
```

如果输出显示“启用”设置为“False”，则必须启用自动备份 。 幸运的是，可通过相同的方式启用和配置自动备份。 有关信息，请参阅下一部分。

> [!NOTE] 
> 如果在进行更改后立即检查设置，看到的可能是旧配置值。 请等待几分钟再检查设置，确保更改已应用。

### <a name="configure-automated-backup-v2"></a>配置自动备份 v2
随时可以使用 PowerShell 来启用自动备份以及修改其配置和行为。 

首先，为备份文件选择或创建存储帐户。 以下脚本选择一个存储帐户，或者创建一个存储帐户（如果不存在）。

```powershell
$storage_accountname = "yourstorageaccount"
$storage_resourcegroupname = $resourcegroupname

$storage = Get-AzStorageAccount -ResourceGroupName $resourcegroupname `
    -Name $storage_accountname -ErrorAction SilentlyContinue
If (-Not $storage)
    { $storage = New-AzStorageAccount -ResourceGroupName $storage_resourcegroupname `
    -Name $storage_accountname -SkuName Standard_GRS -Location $region } 
```

> [!NOTE]
> 自动备份不支持在高级存储中存储备份，但可以从使用高级存储的 VM 磁盘创建备份。

然后，使用 New-AzVMSqlServerAutoBackupConfig 命令启用并配置自动备份 v2 设置，以便在 Azure 存储帐户中存储备份。 在本示例中，备份设置为保留 10 天。 系统数据库备份已启用。 完整备份计划为每周运行，时段从 20:00 开始，持续两小时。 日志备份计划为每隔 30 分钟运行。 第二个命令 Set-AzVMSqlServerExtension 使用这些设置更新指定的 Azure VM。

```powershell
$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname -BackupSystemDbs `
    -BackupScheduleType Manual -FullBackupFrequency Weekly `
    -FullBackupStartHour 20 -FullBackupWindowInHours 2 `
    -LogBackupFrequencyInMinutes 30 

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname 
```

可能需要花费几分钟来安装和配置 SQL Server IaaS 代理。 

要启用加密，请修改上述脚本，使其将 **EnableEncryption** 参数连同 **CertificatePassword** 参数的密码（安全字符串）一起传递。 以下脚本启用上一示例中的自动备份设置，并添加加密。

```powershell
$password = "P@ssw0rd"
$encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  

$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -Enable `
    -EnableEncryption -CertificatePassword $encryptionpassword `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname -BackupSystemDbs `
    -BackupScheduleType Manual -FullBackupFrequency Weekly `
    -FullBackupStartHour 20 -FullBackupWindowInHours 2 `
    -LogBackupFrequencyInMinutes 30 

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

若要确认是否应用了这些设置，请 [检查自动备份配置](#verifysettings)。

### <a name="disable-automated-backup"></a>禁用自动备份
若要禁用自动备份，请运行同一个脚本，但不要为 New-AzVMSqlServerAutoBackupConfig 命令指定 -Enable 参数 。 缺少 **-Enable** 参数会向该命令发出指示以禁用此功能。 与安装一样，可能需要花费几分钟时间来禁用自动备份。

```powershell
$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -ResourceGroupName $storage_resourcegroupname

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

### <a name="example-script"></a>示例脚本
以下脚本提供一组可自定义的变量，用来为 VM 启用和配置自动备份。 根据具体的情况，可能需要根据要求自定义该脚本。 例如，如果想要禁用系统数据库备份或启用加密，则必须更改该脚本。

```powershell
$vmname = "yourvmname"
$resourcegroupname = "vmresourcegroupname"
$region = "Azure region name such as EASTUS2"
$storage_accountname = "storageaccountname"
$storage_resourcegroupname = $resourcegroupname
$retentionperiod = 10
$backupscheduletype = "Manual"
$fullbackupfrequency = "Weekly"
$fullbackupstarthour = "20"
$fullbackupwindow = "2"
$logbackupfrequency = "30"

# ResourceGroupName is the resource group which is hosting the VM where you are deploying the SQL Server IaaS Extension 

Set-AzVMSqlServerExtension -VMName $vmname `
    -ResourceGroupName $resourcegroupname -Name "SQLIaasExtension" `
    -Version "2.0" -Location $region

# Creates/use a storage account to store the backups

$storage = Get-AzStorageAccount -ResourceGroupName $resourcegroupname `
    -Name $storage_accountname -ErrorAction SilentlyContinue
If (-Not $storage)
    { $storage = New-AzStorageAccount -ResourceGroupName $storage_resourcegroupname `
    -Name $storage_accountname -SkuName Standard_GRS -Location $region }

# Configure Automated Backup settings

$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays $retentionperiod -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname -BackupSystemDbs `
    -BackupScheduleType $backupscheduletype -FullBackupFrequency $fullbackupfrequency `
    -FullBackupStartHour $fullbackupstarthour -FullBackupWindowInHours $fullbackupwindow `
    -LogBackupFrequencyInMinutes $logbackupfrequency

# Apply the Automated Backup settings to the VM

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

## <a name="monitoring"></a>监视

可通过两种主要方式监视 SQL Server 2016/2017 上的自动备份。 由于自动备份使用 SQL Server 托管备份功能，同样的监视方法对两者均适用。

首先，可通过调用 [msdb.managed_backup.sp_get_backup_diagnostics](/sql/relational-databases/system-stored-procedures/managed-backup-sp-get-backup-diagnostics-transact-sql) 轮询状态。 或查询 [msdb.managed_backup.fn_get_health_status](/sql/relational-databases/system-functions/managed-backup-fn-get-health-status-transact-sql) 表值函数。

另一种方式是利用内置的数据库邮件功能进行通知。

1. 调用 [msdb.managed_backup.sp_set_parameter](/sql/relational-databases/system-stored-procedures/managed-backup-sp-set-parameter-transact-sql) 存储过程，向 SSMBackup2WANotificationEmailIds 参数分配电子邮件地址。 
1. 启用 [SendGrid](https://docs.sendgrid.com/for-developers/partners/microsoft-azure-2021#create-a-twilio-sendgrid-accountcreate-a-twilio-sendgrid-account)，从 Azure VM 发送电子邮件。
1. 使用 SMTP 服务器和用户名配置数据库邮件。 可在 SQL Server Management Studio 中或使用 Transact-SQL 命令配置数据库邮件。 有关详细信息，请参阅[数据库邮件](/sql/relational-databases/database-mail/database-mail)。
1. [配置 SQL Server 代理以使用数据库邮件](/sql/relational-databases/database-mail/configure-sql-server-agent-mail-to-use-database-mail)。
1. 验证是否通过本地 VM 防火墙和适用于 VM 的网络安全组允许该 SMTP 端口。

## <a name="next-steps"></a>后续步骤
自动备份 v2 在 Azure VM 上配置托管备份。 因此，请务必[查看有关托管备份的文档](/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure)，了解其行为和影响。

可以在以下文章中找到针对 Azure VM 上 SQL Server 的其他备份和还原指导：[Azure 虚拟机上的 SQL Server 的备份和还原](backup-restore.md)。

有关其他可用自动化任务的信息，请参阅 [SQL Server IaaS 代理扩展](sql-server-iaas-agent-extension-automate-management.md)。

有关在 Azure VM 上运行 SQL Server 的详细信息，请参阅 [Azure 虚拟机上的 SQL Server 概述](sql-server-on-azure-vm-iaas-what-is-overview.md)。