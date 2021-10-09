---
title: 使用 Azure PowerShell 备份 Azure 托管磁盘
description: 了解如何使用 Azure PowerShell 备份 Azure 托管磁盘。
ms.topic: conceptual
ms.date: 09/17/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: beb6a266a9436b7c26f5786c5f5a57f10fb9319a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128672640"
---
# <a name="back-up-azure-managed-disks-using-azure-powershell"></a>使用 Azure PowerShell 备份 Azure 托管磁盘

本文介绍如何使用 Azure PowerShell 备份[Azure 托管磁盘](../virtual-machines/managed-disks-overview.md)。

本文将指导如何进行以下操作：

- 创建备份保管库

- 创建备份策略

- 配置 Azure 磁盘备份

- 运行按需备份作业

有关 Azure 磁盘备份区域可用性、支持的方案和限制的详细信息，请参阅[支持矩阵](disk-backup-support-matrix.md)。

## <a name="create-a-backup-vault"></a>创建备份保管库

备份保管库是 Azure 中的一个存储实体，用于保存 Azure 备份支持的各种新型工作负荷（例如 Azure Database for PostgreSQL 服务器和 Azure 磁盘）的备份数据。 备份保管库便于组织备份数据，并最大限度降低管理开销。 备份保管库基于 Azure 的 Azure 资源管理器型号，提供增强功能帮助保护备份数据。

在创建备份保管库之前，请选择保管库数据的存储冗余。 然后继续使用该存储冗余和位置创建备份保管库。 在本文中，我们将在资源组 "testBkpVaultRG" 下的 "westus" 区域创建备份保管库 "TestBkpVault"。 使用[New-AzDataProtectionBackupVault](/powershell/module/az.dataprotection/new-azdataprotectionbackupvault?view=azps-5.7.0&preserve-view=true)命令创建备份保管库。深入了解如何 [创建备份保管库](./backup-vault-overview.md#create-a-backup-vault)。

```azurepowershell-interactive
$storageSetting = New-AzDataProtectionBackupVaultStorageSettingObject -Type LocallyRedundant/GeoRedundant -DataStoreType VaultStore
New-AzDataProtectionBackupVault -ResourceGroupName testBkpVaultRG -VaultName TestBkpVault -Location westus -StorageSetting $storageSetting
$TestBkpVault = Get-AzDataProtectionBackupVault -VaultName TestBkpVault
$TestBKPVault | fl
ETag                :
Id                  : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/TestBkpVault
Identity            : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.DppIdentityDetails
IdentityPrincipalId :
IdentityTenantId    :
IdentityType        :
Location            : westus
Name                : TestBkpVault
ProvisioningState   : Succeeded
StorageSetting      : {Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.StorageSetting}
SystemData          : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.SystemData
Tag                 : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.DppTrackedResourceTags
Type                : Microsoft.DataProtection/backupVaults
```

创建保管库后，让我们创建备份策略来保护 Azure 磁盘。

## <a name="create-a-backup-policy"></a>创建备份策略

若要了解 Azure 磁盘备份之备份策略的内部组件，请使用命令[Get-AzDataProtectionPolicyTemplate](/powershell/module/az.dataprotection/get-azdataprotectionpolicytemplate?view=azps-5.7.0&preserve-view=true)检索策略模板。 此命令返回给定数据源类型的默认策略模板。 使用此策略模板创建新策略。

```azurepowershell-interactive
$policyDefn = Get-AzDataProtectionPolicyTemplate -DatasourceType AzureDisk
$policyDefn | fl


DatasourceType : {Microsoft.Compute/disks}
ObjectType     : BackupPolicy
PolicyRule     : {BackupHourly, Default}

$policyDefn.PolicyRule | fl


BackupParameter           : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.AzureBackupParams
BackupParameterObjectType : AzureBackupParams
DataStoreObjectType       : DataStoreInfoBase
DataStoreType             : OperationalStore
Name                      : BackupHourly
ObjectType                : AzureBackupRule
Trigger                   : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.ScheduleBasedTriggerContext
TriggerObjectType         : ScheduleBasedTriggerContext

IsDefault  : True
Lifecycle  : {Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.SourceLifeCycle}
Name       : Default
ObjectType : AzureRetentionRule
```

策略模板由触发器（决定备份的触发因素）和生命周期（决定删除/复制/移动备份的时间）。 在 Azure 磁盘备份中，触发器的默认值为每 4 小时 (PT4H) 的日程每小时触发，并将每个备份保留 7 天。

```azurepowershell-interactive
 $policyDefn.PolicyRule[0].Trigger | fl


ObjectType                    : ScheduleBasedTriggerContext
ScheduleRepeatingTimeInterval : {R/2020-04-05T13:00:00+00:00/PT4H}
TaggingCriterion              : {Default}
```

```azurepowershell-interactive
$policyDefn.PolicyRule[1].Lifecycle | fl


DeleteAfterDuration        : P7D
DeleteAfterObjectType      : AbsoluteDeleteOption
SourceDataStoreObjectType  : DataStoreInfoBase
SourceDataStoreType        : OperationalStore
TargetDataStoreCopySetting :
```

Azure 磁盘备份每天提供多次备份。 如果需要更频繁地进行备份，请选择“每小时”备份频率，让你能够以每 4、6、8 或 12 小时的间隔进行备份。 根据所选的“时间”间隔安排备份。 例如，如果选择“每 4 小时”备份一次，则会按 4 小时的间隔执行备份，以便备份在一天中均匀分布。 如果每天备份一次即可满足需求，则选择“每日”备份频率。 可在每日备份频率中指定每日备份时间。 请注意，每日备份时间表示备份开始时间，而不是备份完成时间。 完成备份操作所需时间取决于各种因素，包括磁盘大小和连续备份的更改率。 但是，Azure 磁盘备份是使用[增量快照](../virtual-machines/disks-incremental-snapshots.md)的无代理备份，不会影响生产应用程序的性能。

   >[!NOTE]
   > 尽管所选保管库可能具有全局冗余设置，但当前 Azure 磁盘备份仅支持快照数据存储。 所有备份都存储在订阅的资源组中，不会复制到备份保管库存储中。

若要了解有关策略创建的详细信息，请参阅 [Azure 磁盘备份策略](backup-managed-disks.md#create-backup-policy)文档。

如果要编辑每小时频率或保持期，请使用[Edit-AzDataProtectionPolicyTriggerClientObject](/powershell/module/az.dataprotection/edit-azdataprotectionpolicytriggerclientobject?view=azps-5.7.0&preserve-view=true) 和/或 [Edit-AzDataProtectionPolicyRetentionRuleClientObject](/powershell/module/az.dataprotection/edit-azdataprotectionpolicyretentionruleclientobject?view=azps-5.7.0&preserve-view=true) 命令。 一旦策略对象具有所有所需值，请使用 [New-AzDataProtectionBackupPolicy](/powershell/module/az.dataprotection/new-azdataprotectionbackuppolicy?view=azps-5.7.0&preserve-view=true)从策略对象创建新策略。

```azurepowershell-interactive
New-AzDataProtectionBackupPolicy -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Name diskBkpPolicy -Policy $policyDefn

Name                   Type
----                   ----
diskBkpPolicy       Microsoft.DataProtection/backupVaults/backupPolicies

$diskBkpPol = Get-AzDataProtectionBackupPolicy -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Name "diskBkpPolicy"
```

## <a name="configure-backup"></a>配置备份

创建保管库和策略后，用户需要考虑 3 个关键点来保护 Azure 磁盘。

### <a name="key-entities-involved"></a>所涉关键实体

#### <a name="disk-to-be-protected"></a>要保护的磁盘

提取要保护的磁盘 ARM ID。 用作磁盘的标识符。 我们以其他订阅下资源组 "diskrg" 名为 "PSTestDisk" 的磁盘为例。

```azurepowershell-interactive
$DiskId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/PSTestDisk"
```

#### <a name="snapshot-resource-group"></a>快照资源组

磁盘快照存储在订阅内的资源组中。 作为指导原则，建议创建专用资源组作为 Azure 备份服务使用的快照数据存储。 专用资源组可以限制资源组的访问权限，确保备份数据管理的安全性和便捷性。 请注意要放置磁盘快照的资源组 ARM ID

```azurepowershell-interactive
$snapshotrg = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/snapshotrg"
```

#### <a name="backup-vault"></a>备份保管库

备份保管库需要磁盘和快照资源组的权限，以便能够触发快照并管理其生命周期。 保管库的系统分配托管标识用于分配此类权限。 使用[Update-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/update-azrecoveryservicesvault?view=azps-5.7.0&preserve-view=true)命令启用恢复服务保管库的系统分配托管标识。

### <a name="assign-permissions"></a>分配权限

用户需要通过 RBAC 向保管库（保管库 MSI 表示）、相关磁盘和/或磁盘 RG 分配少量权限。 可以通过门户或 PowerShell 执行上述操作。

备份保管库使用托管标识来访问其他 Azure 资源。 如要配置托管磁盘的备份，备份保管库的托管标识需要对创建和管理快照的源磁盘和资源组具有一组权限。

系统分配的托管标识限制为每个资源一个，并绑定到此资源的生命周期。 可使用 Azure 基于角色的访问控制 (Azure RBAC) 来授予托管标识的访问权限。 托管标识是一种只能用于 Azure 资源的特殊类型的服务主体。 详细了解[托管标识](/azure/active-directory/managed-identities-azure-resources/overview)。

若要配置托管磁盘的备份，请确保满足以下先决条件：

- 在需要备份的源磁盘上，将“磁盘备份读取者”角色分配到备份保管库的托管标识。

  1. 转到需要备份的磁盘。
  1. 依次选择“访问控制（标识和访问管理）”、“添加角色分配” 。
  1. 在右侧的上下文窗格中，在“角色”下拉列表中选择“磁盘备份读取者”。
  1. 选择备份保管库的托管标识，然后单击“保存”。
  
     >[!Tip]
     >键入备份保管库名称以选择保管库的托管标识。

  :::image type="content" source="./media/backup-managed-disks-ps/assign-disk-backup-reader-role-inline.png" alt-text="显示在需要备份的源磁盘上，将“磁盘备份读取者”角色分配到备份保管库的托管标识的过程的屏幕截图。" lightbox="./media/backup-managed-disks-ps/assign-disk-backup-reader-role-expanded.png":::

- 在 Azure 备份服务创建和管理备份的资源组上，将“磁盘快照参与者”角色分配给备份保管库的托管标识。 磁盘快照存储在订阅内的资源组中。 若要允许 Azure 备份服务创建、存储和管理快照，需要提供对备份保管库的权限。

  1. 转到资源组。 例如，资源组是 _SnapshotRG_，它与要备份的磁盘位于同一订阅中。
  1. 依次选择“访问控制（标识和访问管理）”、“添加角色分配” 。
  1. 在右侧的上下文窗格中，在“角色”下拉列表中选择“磁盘快照参与者”。 
  1. 选择备份保管库的托管标识，然后单击“保存”。
  
     >[!Tip]
     >键入备份保管库名称以选择保管库的托管标识。

  :::image type="content" source="./media/backup-managed-disks-ps/assign-disk-snapshot-contributor-role-inline.png" alt-text="显示将“磁盘快照参与者”角色分配给资源组上备份保管库的托管标识的过程的屏幕截图。" lightbox="./media/backup-managed-disks-ps/assign-disk-snapshot-contributor-role-expanded.png":::

- 验证备份保管库的托管标识是否在用作快照数据存储的源磁盘和资源组上有一组正确的角色分配。

  1. 转到“备份保管库” -> “标识”，然后选择“Azure 角色分配”。
 
     :::image type="content" source="./media/backup-managed-disks-ps/select-azure-role-assignments-inline.png" alt-text="显示选择 Azure 角色分配的屏幕截图。" lightbox="./media/backup-managed-disks-ps/select-azure-role-assignments-expanded.png":::

  1. 验证角色、资源名称和资源类型是否正确。
 
     :::image type="content" source="./media/backup-managed-disks-ps/verify-role-assignment-details-inline.png" alt-text="显示角色、资源名称和资源类型的验证的屏幕截图。" lightbox="./media/backup-managed-disks-ps/verify-role-assignment-details-expanded.png":::

>[!Note]
>虽然在门户中正确地反映了角色分配，但可能需要大约 15 - 30 分钟才能将权限应用到备份管理库的托管标识上。

### <a name="prepare-the-request"></a>准备请求

一旦设置了所有相关的权限，就可以分 2 步执行备份配置。 首先，使用 [Initialize-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/initialize-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true) 命令通过使用相关保管库、策略、磁盘和快照资源组准备相关请求。 然后，使用[New-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/new-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true)命令提交磁盘保护请求。

```azurepowershell-interactive
$instance = Initialize-AzDataProtectionBackupInstance -DatasourceType AzureDisk -DatasourceLocation $TestBkpvault.Location -PolicyId $diskBkpPol[0].Id -DatasourceId $DiskId 
$instance.Property.PolicyInfo.PolicyParameter.DataStoreParametersList[0].ResourceGroupId = $snapshotrg
New-AzDataProtectionBackupInstance -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupInstance $instance

Name                                                       Type                                                  BackupInstanceName
----                                                       ----                                                  ------------------
diskrg-PSTestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166 Microsoft.DataProtection/backupVaults/backupInstances diskrg-PSTestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166
```

## <a name="run-an-on-demand-backup"></a>运行按需备份

使用[Get-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/get-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true)提取用户需要触发备份的相关备份实例

```azurepowershell-interactive
$instance = Get-AzDataProtectionBackupInstance -SubscriptionId "xxxx-xxx-xxx" -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Name "BackupInstanceName"
```

可在触发备份时指定保留规则。 若要查看策略保留规则，请导航在策略对象中。 以下示例显示名为 'default' 的规则，我们将使用该规则进行按需备份

```azurepowershell-interactive
$policyDefn.PolicyRule | fl


BackupParameter           : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.AzureBackupParams
BackupParameterObjectType : AzureBackupParams
DataStoreObjectType       : DataStoreInfoBase
DataStoreType             : OperationalStore
Name                      : BackupHourly
ObjectType                : AzureBackupRule
Trigger                   : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.ScheduleBasedTriggerContext
TriggerObjectType         : ScheduleBasedTriggerContext

IsDefault  : True
Lifecycle  : {Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.SourceLifeCycle}
Name       : Default
ObjectType : AzureRetentionRule
```

使用[Backup-AzDataProtectionBackupInstanceAdhoc](/powershell/module/az.dataprotection/backup-azdataprotectionbackupinstanceadhoc?view=azps-5.7.0&preserve-view=true)命令触发按需备份。

```azurepowershell-interactive
$AllInstances = Get-AzDataProtectionBackupInstance -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name
Backup-AzDataProtectionBackupInstanceAdhoc -BackupInstanceName $AllInstances[0].Name -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupRuleOptionRuleName "Default"
```

## <a name="tracking-jobs"></a>跟踪作业

使用[Get-AzDataProtectionJob](/powershell/module/az.dataprotection/get-azdataprotectionjob?view=azps-5.7.0&preserve-view=true)命令跟踪所有作业。 你可以列出所有作业并提取特定作业详细信息。

你还可以使用 Az ResourceGraph 跟踪所有备份保管库中的所有作业。 使用[Search-AzDataProtectionJobInAzGraph](/powershell/module/az.dataprotection/search-azdataprotectionjobinazgraph?view=azps-5.7.0&preserve-view=true)命令获取可跨任何备份保管库的相关作业。

```azurepowershell-interactive
  $job = Search-AzDataProtectionJobInAzGraph -Subscription $sub -ResourceGroupName "testBkpVaultRG" -Vault $TestBkpVault.Name -DatasourceType AzureDisk -Operation OnDemandBackup
```

## <a name="next-steps"></a>后续步骤

- [使用 Azure PowerShell 还原 Azure 托管磁盘](restore-managed-disks-ps.md)
