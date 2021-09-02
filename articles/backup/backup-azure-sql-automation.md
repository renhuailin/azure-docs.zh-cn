---
title: 通过 PowerShell 备份和还原 Azure VM 中的 SQL 数据库
description: 使用 Azure 备份与 PowerShell 备份和还原 Azure VM 中的 SQL 数据库。
ms.topic: conceptual
ms.date: 06/30/2021
ms.assetid: 57854626-91f9-4677-b6a2-5d12b6a866e1
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: be335dc70290c61f35608b8adf55483bf2a61ca8
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114449445"
---
# <a name="back-up-and-restore-sql-databases-in-azure-vms-with-powershell"></a>使用 PowerShell 备份和还原 Azure VM 中的 SQL 数据库

本文介绍如何在 Azure PowerShell 中使用 [Azure 备份](backup-overview.md)恢复服务保管库来备份和恢复 Azure VM 中的 SQL 数据库。

本文介绍如何执行以下操作：

> [!div class="checklist"]
>
> * 设置 PowerShell 并注册 Azure 恢复服务提供程序。
> * 创建恢复服务保管库。
> * 为 Azure VM 中的 SQL 数据库配置备份。
> * 运行备份作业。
> * 还原已备份的 SQL 数据库。
> * 监视备份和还原作业。

## <a name="before-you-start"></a>开始之前

* [详细了解](backup-azure-recovery-services-vault-overview.md)恢复服务保管库。
* 了解用于[备份 Azure VM 中的 SQL 数据库](backup-azure-sql-database.md#before-you-start)的功能。
* 查看恢复服务的 PowerShell 对象层次结构。

### <a name="recovery-services-object-hierarchy"></a>恢复服务对象层次结构

下图汇总了对象层次结构。

![恢复服务对象层次结构](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

查看 Azure 库中的 **Az.RecoveryServices** [cmdlet 参考](/powershell/module/az.recoveryservices)。

### <a name="set-up-and-install"></a>设置和安装

按如下所述设置 PowerShell：

1. [下载最新版本的 Azure PowerShell](/powershell/azure/install-az-ps)。 所需的最低版本为 1.5.0。

2. 找到包含以下命令的 Azure 备份 PowerShell cmdlet：

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. 查看 Azure 备份和恢复服务保管库的别名与 cmdlet。 下面是可能显示的内容示例。 此 cmdlet 列表并不完整。

    ![恢复服务 cmdlet 列表](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. 使用 **Connect-AzAccount** 登录到 Azure 帐户。
5. 在出现的网页上，系统会提示你输入帐户凭据。

    * 或者，可以结合 **-Credential** 在 **Connect-AzAccount** cmdlet 中将帐户凭据包含为参数。
    * 如果你是为某个租户工作的 CSP 合作伙伴，则需使用 tenantID 或租户主域名将客户指定为一名租户。 例如，Connect-AzAccount -Tenant fabrikam.com。

6. 由于一个帐户可以有多个订阅，因此请将要使用的订阅与帐户关联在一起。

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

7. 首次使用 Azure 备份时，请使用 Register-AzResourceProvider cmdlet 将 Azure 恢复服务提供程序注册到订阅。

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

8. 验证提供程序是否已成功注册：

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

9. 在命令输出中，确认 **RegistrationState** 是否更改为 **Registered**。 如果不是，请再次运行 **Register-AzResourceProvider** cmdlet。

## <a name="create-a-recovery-services-vault"></a>创建恢复服务保管库

请按照以下步骤创建恢复服务保管库。

恢复服务保管库是一种资源管理器资源，因此必须将其放在资源组中。 可以使用现有资源组，也可以使用 New-AzResourceGroup cmdlet 创建资源组。 创建资源组时，请指定资源组的名称和位置。

1. 保管库放在资源组中。 如果没有现有的资源组，请使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 新建一个。 此示例在美国西部区域创建一个新的资源组。

    ```powershell
    New-AzResourceGroup -Name "test-rg" -Location "West US"
    ```

2. 使用 [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) cmdlet 创建保管库。 请为保管库指定与资源组相同的位置。

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. 指定保管库存储使用的冗余类型。

    * 可以使用[本地冗余存储](../storage/common/storage-redundancy.md#locally-redundant-storage)、[异地冗余存储](../storage/common/storage-redundancy.md#geo-redundant-storage)或[区域冗余存储](../storage/common/storage-redundancy.md#zone-redundant-storage)。
    * 以下示例针对 **testvault** 设置为 **GeoRedundant** 的 [Set-AzRecoveryServicesBackupProperty](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) 命令设置 **-BackupStorageRedundancy** 选项。

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

### <a name="view-the-vaults-in-a-subscription"></a>在订阅中查看保管库

若要查看订阅中的所有保管库，请使用 [Get-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/get-azrecoveryservicesvault)。

```powershell
Get-AzRecoveryServicesVault
```

输出如下所示。 其中已提供资源组名称和位置。

```output
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

### <a name="set-the-vault-context"></a>设置保管库上下文

将保管库对象存储在变量中，并设置保管库上下文。

* 许多 Azure 备份 cmdlet 要求将恢复服务保管库对象用作输入，因此可以方便地在变量中存储保管库对象。
* 保管库上下文是在保管库中受保护的数据的类型。 可以使用 [Set-AzRecoveryServicesVaultContext](/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext) 设置它。 设置上下文后，它将应用于所有后续 cmdlet。

以下示例为 testvault 设置保管库上下文。

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>提取保管库 ID

我们已计划根据 Azure PowerShell 指导原则弃用保管库上下文设置。 你可以改为存储或提取保管库 ID，并将其传递给相关命令，如下所示：

```powershell
$testVault = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault"
$testVault.ID
```

## <a name="configure-a-backup-policy"></a>配置备份策略

备份策略指定备份计划，以及备份恢复点的保留期限：

* 一个备份策略至少与一个保留策略相关联。 保留策略定义了在将恢复点删除之前将其保留的时限。
* 使用 [Get-AzRecoveryServicesBackupRetentionPolicyObject](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject) 查看默认的备份保留策略。
* 使用 [Get-AzRecoveryServicesBackupSchedulePolicyObject](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject) 查看默认的备份策略计划。
* 使用 [New-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy) cmdlet 创建新的备份策略。 输入计划和保留策略对象。

默认情况下，会在“计划策略对象”中定义开始时间。 请使用以下示例将开始时间更改为所需的开始时间。 所需的开始时间也应采用 UTC 格式。 下面的示例假设在进行每日备份时，所需的开始时间为 UTC 时间凌晨 1:00。

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "MSSQL"
$UtcTime = Get-Date -Date "2019-03-20 01:30:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> 只需以 30 分钟的倍数提供开始时间。 在上面的示例中，开始时间只能是“01:00:00”或“02:30:00”。 开始时间不能为“01:15:00”。

以下示例将计划策略和保留策略存储在变量中。 然后，它使用这些变量作为新策略 (**NewSQLPolicy**) 的参数。 **NewSQLPolicy** 创建每日“完整”备份，将备份保留 180 天，并每隔 2 小时创建日志备份

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "MSSQL"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "MSSQL"
$NewSQLPolicy = New-AzRecoveryServicesBackupProtectionPolicy -Name "NewSQLPolicy" -WorkloadType "MSSQL" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

输出如下所示。

```output
Name                 WorkloadType       BackupManagementType BackupTime                Frequency                                IsDifferentialBackup IsLogBackupEnabled
                                                                                                                                Enabled
----                 ------------       -------------------- ----------                ---------                                -------------------- ------------------
NewSQLPolicy         MSSQL              AzureWorkload        3/15/2019 01:30:00 AM      Daily                                    False                True
```

## <a name="enable-backup"></a>启用备份

### <a name="registering-the-sql-vm"></a>注册 SQL VM

对于 Azure VM 备份和 Azure 文件共享，备份服务可以连接到这些 Azure 资源管理器资源并提取相关的详细信息。 由于 SQL 是 Azure VM 内部的一个应用程序，因此备份服务需要有权访问该应用程序并提取所需的详细信息。 为此，需要将包含 SQL 应用程序的 Azure VM 注册到恢复服务保管库。 将 SQL VM 注册到保管库后，可以仅在该保管库中保护 SQL 数据库。 使用 [Register-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/register-azrecoveryservicesbackupcontainer) PowerShell cmdlet 注册 VM。

```powershell
 $myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $testVault.ID -Force
```

此命令将返回此资源的“备份容器”，状态为“已注册”

> [!NOTE]
> 如果未指定 force 参数，系统会显示文本“是否要对此容器禁用保护”并要求你进行确认。 请忽略此文本，并输入“Y”以确认。 这是一个已知问题，我们正在努力删除该文本，并取消对 force 参数的要求。

### <a name="fetching-sql-dbs"></a>提取 SQL 数据库

完成注册后，备份服务可以列出 VM 中所有可用的 SQL 组件。 若要查看需要备份到此保管库的所有 SQL 组件，请使用 [Get-AzRecoveryServicesBackupProtectableItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectableitem) PowerShell cmdlet

```powershell
Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -VaultId $testVault.ID
```

此输出将显示所有已注册到此保管库的 SQL VM 中所有不受保护的 SQL 组件以及 ItemType 和 ServerName。 可以通过传递“-Container”参数来进一步筛选特定的 SQL VM，或者结合 ItemType 标志使用“Name”和“ServerName”的组合来查看唯一的 SQL 项。

```powershell
$SQLDB = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $testVault.ID -Name "<Item Name>" -ServerName "<Server Name>"
```

### <a name="configuring-backup"></a>配置备份

提取所需的 SQL 数据库并创建用于备份的策略后，接下来可以使用 [Enable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) cmdlet 来为此 SQL 数据库配置备份。

```output
Enable-AzRecoveryServicesBackupProtection -ProtectableItem $SQLDB -Policy $NewSQLPolicy
```

该命令将一直等到配置备份完成并返回以下输出。

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 6:00:21 PM      3/18/2019 6:01:35 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="fetching-new-sql-dbs"></a>提取新的 SQL 数据库

注册计算机后，备份服务将提取可用数据库的详细信息。 如果以后将 SQL 数据库或 SQL 实例添加到已注册的计算机，则需要手动触发备份服务，才能执行新的查询来再次获取所有不受保护的数据库（包括新添加的数据库）。 在 SQL VM 上使用 [Initialize-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/initialize-azrecoveryservicesbackupprotectableitem) PowerShell cmdlet 执行新的查询。 该命令会等到操作完成为止。 稍后使用 [Get-AzRecoveryServicesBackupProtectableItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectableitem) PowerShell cmdlet 获取不受保护的最新 SQL 组件列表。

```powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $testVault.ID
Initialize-AzRecoveryServicesBackupProtectableItem -Container $SQLContainer -WorkloadType MSSQL -VaultId $testVault.ID
Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $testVault.ID
```

获取相关的可保护项后，根据[上一部分](#configuring-backup)中的说明启用备份。
如果用户不想要手动检测新数据库，可以根据[下面](#enable-autoprotection)所述启用自动保护。

## <a name="enable-autoprotection"></a>启用自动保护

你可以配置备份，以使用特定的策略自动保护将来添加的所有数据库。 若要启用自动保护，请使用 [Enable-AzRecoveryServicesBackupAutoProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupautoprotection) PowerShell cmdlet。

由于说明中的操作是备份将来的所有数据库，因此该操作将在 SQLInstance 级别进行。

```powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $testVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Enable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -Policy $NewSQLPolicy -VaultId $testVault.ID
```

指明自动保护意向后，在计算机中提取新添加的数据库的查询将按计划的后台任务每隔 8 小时进行。

## <a name="restore-sql-dbs"></a>还原 SQL 数据库

Azure 备份可以还原 Azure VM 上运行的 SQL Server 数据库，如下所述：

* 使用事务日志备份还原到特定的日期或时间（精确到秒）。 Azure 备份可自动确定相应的完整备份、差异备份和日志链备份，这些是根据所选时间进行还原所必需的。
* 还原特定的完整备份或差异备份，这样就可以还原到特定的恢复点。

在还原 SQL 数据库之前，请查看[此处](restore-sql-database-azure-vm.md#restore-prerequisites)所述的先决条件。

> [!WARNING]
> 由于与 RBAC 相关的安全问题，我们必须通过 Powershell 在 SQL DB 的还原命令中引入一项中断性变更。 请升级到 Az 6.0.0 或更高版本，以便通过 Powershell 提交正确的还原命令。 下面提供了最新的 PS 命令。

首先使用 [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem) PowerShell cmdlet 提取相关的已备份 SQL 数据库。

```powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $testVault.ID
```

### <a name="fetch-the-relevant-restore-time"></a>提取相关的还原时间

如上所述，你可以将已备份的 SQL 数据库还原到完整/差异副本，或还原到某个日志时间点。

#### <a name="fetch-distinct-recovery-points"></a>提取不同的恢复点

使用 [Get-AzRecoveryServicesBackupRecoveryPoint](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint) 提取已备份的 SQL 数据库的不同（完整/差异）恢复点。

```powershell
$startDate = (Get-Date).AddDays(-7).ToUniversalTime()
$endDate = (Get-Date).ToUniversalTime()
Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $testVault.ID -StartDate $startdate -EndDate $endDate
```

输出类似于以下示例

```output
RecoveryPointId    RecoveryPointType  RecoveryPointTime      ItemName                             BackupManagemen
                                                                                                  tType
---------------    -----------------  -----------------      --------                             ---------------
6660368097802      Full               3/18/2019 8:09:35 PM   MSSQLSERVER;model             AzureWorkload
```

使用“RecoveryPointId”筛选器或数组筛选器提取相关的恢复点。

```powershell
$FullRP = Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $testVault.ID -RecoveryPointId "6660368097802"
```

#### <a name="fetch-point-in-time-recovery-point"></a>提取时点恢复点

如果希望将数据库还原到特定的时间点，可使用 [Get-AzRecoveryServicesBackupRecoveryLogChain](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverylogchain) PowerShell cmdlet。 该 cmdlet 返回一个日期列表，这些日期表示该 SQL 备份项的已中断连续日志链的开始时间和结束时间。 所需的时间点应在此范围内。

```powershell
Get-AzRecoveryServicesBackupRecoveryLogChain -Item $bkpItem -VaultId $testVault.ID
```

输出类似于以下示例。

```output
ItemName                       StartTime                      EndTime
--------                       ---------                      -------
SQLDataBase;MSSQLSERVER;azu... 3/18/2019 8:09:35 PM           3/19/2019 12:08:32 PM
```

以上输出表示你可以还原到显示的开始时间与结束时间之间的任意时间点。 这些时间为 UTC 时间。 在 PowerShell 中构造处于上面所示范围内的任意时间点。

> [!NOTE]
> 选择要还原到的日志时间点后，无需指定起点（即要从中还原数据库的完整备份）。 Azure 备份服务将处理整个恢复计划，即选择所需的完整备份、应用所需的日志备份等。

### <a name="determine-recovery-configuration"></a>确定恢复配置

SQL 数据库还原支持以下还原方案。

* 使用其他恢复点中的数据替代已备份的 SQL 数据库 - OriginalWorkloadRestore
* 将 SQL 数据库还原为同一 SQL 实例中的新数据库 - AlternateWorkloadRestore
* 将 SQL 数据库还原为另一 SQL VM 上另一 SQL 实例中的新数据库 - AlternateWorkloadRestore
* 将 SQL 数据库还原为 .bak 文件 -RestoreAsFiles

提取相关的恢复点（不同的时间点或日志时间点）后，请使用 [Get-AzRecoveryServicesBackupWorkloadRecoveryConfig](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupworkloadrecoveryconfig) PowerShell cmdlet 根据所需的恢复计划提取恢复配置对象。

#### <a name="original-workload-restore"></a>原始工作负荷还原

若要使用恢复点中的数据替代已备份的数据库，只需指定适当的标志和相关恢复点，如以下示例中所示。

##### <a name="original-restore-with-distinct-recovery-point"></a>使用不同恢复点的原始工作负荷还原

```powershell
$OverwriteWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -OriginalWorkloadRestore -VaultId $testVault.ID
```

##### <a name="original-restore-with-log-point-in-time"></a>使用日志时间点的原始工作负荷还原

```powershell
$OverwriteWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem  -OriginalWorkloadRestore -VaultId $testVault.ID
```

#### <a name="alternate-workload-restore"></a>备用工作负荷还原

> [!IMPORTANT]
> 备份的 SQL 数据库只能作为新数据库还原到已在此保管库中“注册”的 Azure VM 上的另一个 SQLInstance 中。

如前所述，如果目标 SQLInstance 位于另一个 Azure VM 中，请确保该 VM [已注册到此保管库](#registering-the-sql-vm)，并且相关的 SQLInstance 显示为可保护的项。 本文档假设目标 SQLInstance 名称为另一虚拟机“Contoso2”中的 MSSQLSERVER。

```powershell
$TargetContainer =  Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -Status Registered  -VaultId $testVault.ID -FriendlyName "Contoso2"
$TargetInstance = Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -ItemType SQLInstance -Name "MSSQLSERVER" -ServerName "Contoso2" -VaultId $testVault.ID
```

然后，只需结合如下所示的适当标志以及目标 SQL 实例所在的目标容器，传递相关的恢复点和目标 SQL 实例。

##### <a name="alternate-restore-with-distinct-recovery-point"></a>使用不同恢复点的备用工作负荷还原

```powershell
$AnotherInstanceWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -TargetItem $TargetInstance -AlternateWorkloadRestore -VaultId $testVault.ID -TargetContainer $TargetContainer
```

##### <a name="alternate-restore-with-log-point-in-time"></a>使用日志时间点的备用工作负荷还原

```powershell
$AnotherInstanceWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem -TargetItem $TargetInstance -AlternateWorkloadRestore -VaultId $testVault.ID -TargetContainer $TargetContainer
```

##### <a name="restore-as-files"></a>作为文件还原

若要将备份数据作为 .bak 文件而不是数据库还原，请选择“作为文件还原”选项。 已备份的 SQL 数据库可以还原为任何已注册到此保管库的目标 VM。

```powershell
$TargetContainer= Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName "VM name" -VaultId $vaultID
```

##### <a name="restore-as-files-with-distinct-recovery-point"></a>使用不同恢复点还原为文件

```powershell
$FileRestoreWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -TargetContainer $TargetContainer -RestoreAsFiles -FilePath "<>" -VaultId $testVault.ID
```

##### <a name="restore-as-files-with-log-point-in-time-from-latest-full"></a>根据最新完整备份使用日志时间点还原为文件

```powershell
$FileRestoreWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -TargetContainer $TargetContainer -RestoreAsFiles -FilePath "<>" -VaultId $testVault.ID
```

##### <a name="restore-as-files-with-log-point-in-time-from-a-specified-full"></a>根据指定完整备份使用日志时间点还原为文件

如果希望提供应该用于还原的特定完整备份，请使用以下命令：

```powershell
$FileRestoreWithLogAndSpecificFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -FromFull $FullRP -TargetContainer $TargetContainer -RestoreAsFiles -FilePath "<>" -VaultId $testVault.ID
```

从 [Get-AzRecoveryServicesBackupWorkloadRecoveryConfig](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupworkloadrecoveryconfig) PowerShell cmdlet 获取的最终恢复点配置对象包含用于还原的所有相关信息，如下所示。

```output
TargetServer         : <SQL server name>
TargetInstance       : <Target Instance name>
RestoredDBName       : <Target Instance name>/azurebackup1_restored_3_19_2019_1850
OverwriteWLIfpresent : No
NoRecoveryMode       : Disabled
targetPhysicalPath   : {azurebackup1, azurebackup1_log}
ContainerId          : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/vmappcontainer;compute;computeRG;SQLVMName
SourceResourceId     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/computeRG/VMAppContainer/SQLVMName
RestoreRequestType   : Alternate WL Restore
RecoveryPoint        : Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureWorkloadRecoveryPoint
PointInTime          : 1/1/0001 12:00:00 AM
```

可以编辑已还原的数据库名称、OverwriteWLIfpresent、NoRecoveryMode 和 targetPhysicalPath 字段。 按如下所示获取目标文件路径的其他详细信息。

```powershell
$AnotherInstanceWithFullConfig.targetPhysicalPath
```

```output
MappingType SourceLogicalName SourcePath                  TargetPath
----------- ----------------- ----------                  ----------
Data        azurebackup1      F:\Data\azurebackup1.mdf    F:\Data\azurebackup1_1553001753.mdf
Log         azurebackup1_log  F:\Log\azurebackup1_log.ldf F:\Log\azurebackup1_log_1553001753.ldf
```

按如下所示将相关 PowerShell 属性设置为字符串值。

```powershell
$AnotherInstanceWithFullConfig.OverwriteWLIfpresent = "Yes"
$AnotherInstanceWithFullConfig | fl
```

```output
TargetServer         : <SQL server name>
TargetInstance       : <Target Instance name>
RestoredDBName       : <Target Instance name>/azurebackup1_restored_3_19_2019_1850
OverwriteWLIfpresent : Yes
NoRecoveryMode       : Disabled
targetPhysicalPath   : {azurebackup1, azurebackup1_log}
ContainerId          : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/vmappcontainer;compute;computeRG;SQLVMName
SourceResourceId     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/computeRG/VMAppContainer/SQLVMName
RestoreRequestType   : Alternate WL Restore
RecoveryPoint        : Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureWorkloadRecoveryPoint
PointInTime          : 1/1/0001 12:00:00 AM
```

> [!IMPORTANT]
> 确保最终恢复配置对象包含全部所需且正确的值，因为还原操作基于该配置对象。

#### <a name="alternate-workload-restore-to-a-vault-in-secondary-region"></a>将备用工作负载还原到次要区域中的保管库

> [!IMPORTANT]
> 从 Az 6.0.0 开始，支持通过 Powershell 将 SQL 还原到次要区域

如果已启用跨区域还原，则恢复点也将复制到配对的次要区域。 然后，可以提取这些恢复点，并触发到该配对区域中某台计算机的还原。 与常规还原时一样，目标计算机应注册到次要区域中的目标保管库。 以下步骤顺序将阐明端到端的过程。

* 提取已复制到次要区域的备份项
* 对于此类项，提取已复制到次要区域的恢复点（相异恢复点和/或日志）
* 然后选择已注册到配对次要区域中某个保管库的目标服务器
* 触发到该服务器的还原，然后使用 JobId 跟踪还原操作。

#### <a name="fetch-backup-items-from-secondary-region"></a>从次要区域中提取备份项

使用常用的命令从次要区域中提取所有 SQL 备份项，但在该命令中需要提供一个额外的参数来指示应该从次要区域提取这些项。

```powershell
$secondaryBkpItems = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload  -WorkloadType MSSQL  -VaultId $testVault.ID -UseSecondaryRegion
```

##### <a name="fetch-distinct-recovery-points-from-secondary-region"></a>从次要区域中提取相异恢复点

使用 [Get-AzRecoveryServicesBackupRecoveryPoint](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint) 提取已备份的 SQL DB 的相异（完整/差异）恢复点，并添加一个参数来指示这些恢复点是从次要区域提取的

```powershell
$startDate = (Get-Date).AddDays(-7).ToUniversalTime()
$endDate = (Get-Date).ToUniversalTime()
Get-AzRecoveryServicesBackupRecoveryPoint -Item $secondaryBkpItems[0] -VaultId $testVault.ID -StartDate $startdate -EndDate $endDate -UseSecondaryRegion
```

输出类似于以下示例

```output
RecoveryPointId    RecoveryPointType  RecoveryPointTime      ItemName                             BackupManagemen
                                                                                                  tType
---------------    -----------------  -----------------      --------                             ---------------
6660368097802      Full               3/18/2019 8:09:35 PM   MSSQLSERVER;model             AzureWorkload
```

使用“RecoveryPointId”筛选器或数组筛选器提取相关的恢复点。

```powershell
$FullRPFromSec = Get-AzRecoveryServicesBackupRecoveryPoint -Item $secondaryBkpItems[0] -VaultId $testVault.ID -RecoveryPointId "6660368097802" -UseSecondaryRegion
```

##### <a name="fetch-log-recovery-points-from-secondary-region"></a>从次要区域中提取日志恢复点

结合参数“-UseSecondaryRegion”使用 [Get-AzRecoveryServicesBackupRecoveryLogChain](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverylogchain) PowerShell cmdlet，这会从次要区域中返回该 SQL 备份项的未中断的连续日志链的开始时间和结束时间。 所需的时间点应在此范围内。

```powershell
Get-AzRecoveryServicesBackupRecoveryLogChain -Item $secondaryBkpItems[0] -VaultId $testVault.ID -UseSecondaryRegion
```

输出类似于以下示例。

```output
ItemName                       StartTime                      EndTime
--------                       ---------                      -------
SQLDataBase;MSSQLSERVER;azu... 3/18/2019 8:09:35 PM           3/19/2019 12:08:32 PM
```

以上输出表示你可以还原到显示的开始时间与结束时间之间的任意时间点。 这些时间为 UTC 时间。 在 PowerShell 中构造处于上面所示范围内的任意时间点。

#### <a name="fetch-target-server-from-secondary-region"></a>从次要区域中提取目标服务器

在次要区域中，需有一个保管库以及一个已注册到该保管库的目标服务器。 获得次要区域目标容器和 SQL 实例后，可以重复使用现有的 cmdlet 来生成还原工作负载配置。 本文档假设虚拟机名称为“secondaryVM”，该虚拟机中的实例名称为“MSSQLInstance”

首先，提取次要区域中的相关保管库，然后获取该保管库中已注册的容器。

```powershell
$PairedRegionVault = Get-AzRecoveryServicesVault -ResourceGroupName SecondaryRG -Name PairedVault
$secContainer =  Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -Status Registered  -VaultId $PairedRegionVault.ID -FriendlyName "secondaryVM"
```

选择已注册的容器后，提取该容器中要将数据库还原到的 SQL 实例。 此处我们假设在“secondaryVM”中有 1 个 SQL 实例，并提取该实例。

```powershell
$secSQLInstance = Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -ItemType SQLInstance -VaultId $PairedRegionVault.ID -Container $secContainer
```

#### <a name="prepare-the-recovery-configuration"></a>准备恢复配置

可以重复使用[前面](#determine-recovery-configuration)所述的用于常规 SQL 还原的命令来生成相关的恢复配置。

##### <a name="for-full-restores-from-secondary-region"></a>从次要区域进行完整还原

```powershell
Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRPFromSec[0] -TargetItem $secSQLInstance -AlternateWorkloadRestore -VaultId $vault.ID -TargetContainer $secContainer
```

##### <a name="for-log-point-in-time-restores-from-secondary-region"></a>从次要区域进行日志时间点还原

```powershell
Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $secondaryBkpItems[0] -TargetItem $secSQLInstance  -AlternateWorkloadRestore -VaultId $vault.ID -TargetContainer $secContainer
```

获取用于主要区域还原或次要区域还原的相关配置后，可以使用同一个还原命令来触发还原，并随后使用 jobID 来跟踪还原操作。

### <a name="restore-with-relevant-configuration"></a>使用相关配置还原

获取并验证相关恢复配置对象后，请使用 [Restore-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) PowerShell cmdlet 启动还原过程。

```powershell
Restore-AzRecoveryServicesBackupItem -WLRecoveryConfig $AnotherInstanceWithLogConfig -VaultId $testVault.ID
```

还原操作将返回要跟踪的作业。

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Restore              InProgress           3/17/2019 10:02:45 AM                                3274xg2b-e4fg-5952-89b4-8cb566gc1748
```

## <a name="manage-sql-backups"></a>管理 SQL 备份

### <a name="on-demand-backup"></a>按需备份

为数据库启用备份后，还可以使用 [Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem) PowerShell cmdlet 来触发数据库的按需备份。 以下示例对启用了压缩的 SQL 数据库触发完整备份，完整备份应保留 60 天。

```powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $testVault.ID
$endDate = (Get-Date).AddDays(60).ToUniversalTime()
Backup-AzRecoveryServicesBackupItem -Item $bkpItem -BackupType Full -EnableCompression -VaultId $testVault.ID -ExpiryDateTimeUTC $endDate
```

按需备份命令返回要跟踪的作业。

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Backup               InProgress           3/18/2019 8:41:27 PM                                2516bb1a-d3ef-4841-97a3-9ba455fb0637
```

如果未返回输出或者你要获取相关的作业 ID，请从 Azure 备份服务[获取作业列表](#track-azure-backup-jobs)，并跟踪该列表及其详细信息。

### <a name="change-policy-for-backup-items"></a>更改备份项的策略

可以将备份项的策略从“Policy1”更改为“Policy2” 。 若要切换备份项的策略，请提取相关策略并备份项，并使用 [Enable-AzRecoveryServices](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) 命令以备份项作为参数。

```powershell
$TargetPol1 = Get-AzRecoveryServicesBackupProtectionPolicy -Name <PolicyName>
$anotherBkpItem = Get-AzRecoveryServicesBackupItem -WorkloadType MSSQL -BackupManagementType AzureWorkload -Name "<BackupItemName>"
Enable-AzRecoveryServicesBackupProtection -Item $anotherBkpItem -Policy $TargetPol1
```

该命令将一直等到配置备份完成并返回以下输出。

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 8:00:21 PM      3/18/2019 8:02:16 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="edit-an-existing-backup-policy"></a>编辑现有备份策略

若要编辑现有策略，请使用 [Set-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy) 命令。

```powershell
Set-AzRecoveryServicesBackupProtectionPolicy -Policy $Pol -SchedulePolicy $SchPol -RetentionPolicy $RetPol
```

经过一段时间后检查备份作业以跟踪任何故障。 如果存在故障，需要解决这些问题。 然后使用 FixForInconsistentItems 参数重新运行编辑策略命令，重新对先前操作失败的所有备份项上的策略尝试编辑。

```powershell
Set-AzRecoveryServicesBackupProtectionPolicy -Policy $Pol -FixForInconsistentItems
```

### <a name="re-register-sql-vms"></a>重新注册 SQL VM

> [!WARNING]
> 在尝试重新注册之前，请务必阅读[此文档](backup-sql-server-azure-troubleshoot.md#re-registration-failures)，以了解失败症状和原因

若要触发 SQL VM 的重新注册，请提取相关的备份容器，并将其传递给 register cmdlet。

```powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $testVault.ID
Register-AzRecoveryServicesBackupContainer -Container $SQLContainer -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $testVault.ID
```

### <a name="stop-protection"></a>停止保护

#### <a name="retain-data"></a>保留数据

如果希望停止保护，可以使用 [Disable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection) PowerShell cmdlet。 此命令将停止计划的备份，但到目前为止备份的数据将永远保留。

```powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $testVault.ID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $testVault.ID
```

#### <a name="delete-backup-data"></a>删除备份数据

若要完全删除保管库中存储的备份数据，只需将“-RemoveRecoveryPoints”标志/开关添加到[“disable”保护命令](#retain-data)。

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $testVault.ID -RemoveRecoveryPoints
```

#### <a name="disable-auto-protection"></a>禁用自动保护

如果在 SQLInstance 上配置了自动保护，可以使用 [Disable-AzRecoveryServicesBackupAutoProtection](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupautoprotection) PowerShell cmdlet 来禁用自动保护。

```powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $testVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Disable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $testVault.ID
```

#### <a name="unregister-sql-vm"></a>取消注册 SQL VM

如果 SQL Server 的所有数据库[不再受保护且不存在任何备份数据](#delete-backup-data)，则可以从此保管库取消注册 SQL VM。 只有这样，你才能在另一保管库中保护数据库。 使用 [Unregister-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer) PowerShell cmdlet 取消注册 SQL VM。

```powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $testVault.ID
 Unregister-AzRecoveryServicesBackupContainer -Container $SQLContainer -VaultId $testVault.ID
```

### <a name="track-azure-backup-jobs"></a>跟踪 Azure 备份作业

请务必注意，Azure 备份只会跟踪 SQL 备份中用户触发的作业。 计划的备份（包括日志备份）不会显示在门户或 PowerShell 中。 但是，如果任一计划的作业失败，将会生成[备份警报](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault)并在门户中显示该警报。 [使用 Azure Monitor](backup-azure-monitoring-use-azuremonitor.md) 跟踪所有计划的作业和其他相关信息。

用户可以使用备份等异步作业的[输出](#on-demand-backup)中返回的 JobID，来跟踪按需/用户触发的操作。 使用 [Get-AzRecoveryServicesBackupJobDetail](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjobdetail) PowerShell cmdlet 跟踪作业及其详细信息。

```powershell
 Get-AzRecoveryServicesBackupJobDetails -JobId 2516bb1a-d3ef-4841-97a3-9ba455fb0637 -VaultId $testVault.ID
```

若要从 Azure 备份服务获取按需作业的列表及其状态，请使用 [Get-AzRecoveryServicesBackupJob](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) PowerShell cmdlet。 以下示例返回所有正在进行的 SQL 作业。

```powershell
Get-AzRecoveryServicesBackupJob -Status InProgress -BackupManagementType AzureWorkload
```

若要取消正在进行的作业，请使用 [Stop-AzRecoveryServicesBackupJob](/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob) PowerShell cmdlet。

## <a name="managing-sql-always-on-availability-groups"></a>管理 SQL Always On 可用性组

对于 SQL Always On 可用性组，请确保[注册可用性组 (AG) 的所有节点](#registering-the-sql-vm)。 注册所有节点后，将在可保护项下以逻辑方式创建一个 SQL 可用性组对象。 SQL AG 下的数据库作为“SQLDatabase”列出。 节点将显示为独立实例，其下的默认 SQL 数据库也将作为 SQL 数据库列出。

例如，假设某个 SQL AG 包含两个节点（sql-server-0 和 sql-server-1）和一个 SQL AG 数据库 。 注册这两个节点后，如果[列出可保护项](#fetching-sql-dbs)，则将列出以下组件

* SQL AG 对象 - 作为 SQLAvailabilityGroup 列出的可保护项类型
* SQL AG 数据库 - 作为 SQLDatabase 列出的可保护项类型
* sql-server-0 - 作为 SQLInstance 列出的可保护项类型
* sql-server-1 - 作为 SQLInstance 列出的可保护项类型
* sql server-0 下的所有默认 SQL 数据库（master、model、msdb）- 作为 SQLDatabase 列出的可保护项类型
* sql server-1 下的所有默认 SQL 数据库（master、model、msdb）- 作为 SQLDatabase 列出的可保护项类型

[列出备份容器](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer)时，sql-server-0、sql-server-1 也作为“AzureVMAppContainer”列出。

只需提取相关数据库以[启用备份](#configuring-backup)，[按需备份](#on-demand-backup)和[还原 PowerShell cmdlet](#restore-sql-dbs) 是相同的。
