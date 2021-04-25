---
title: 使用 Azure PowerShell 还原 Azure 托管磁盘
description: 了解如何使用 Azure PowerShell 还原 Azure 托管磁盘。
ms.topic: conceptual
ms.date: 03/26/2021
ms.openlocfilehash: c6625b43c313d45d4b295dd406e29a2b1d85b387
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520031"
---
# <a name="restore-azure-managed-disks-using-azure-powershell"></a>使用 Azure PowerShell 还原 Azure 托管磁盘

本文将讲解如何从 Azure 备份创建的还原点还原 [Azure 托管磁盘](../virtual-machines/managed-disks-overview.md)。

目前，不支持通过替换备份所在的现有源磁盘选择 Original-Location Recovery (OLR) 还原选项。 可从恢复点还原，以在备份所在的源磁盘所属资源组或任何其他资源组创建一个新磁盘。 这种方法名为 Alternate-Location Recovery (ALR)，有助于保留源磁盘和还原的（新）磁盘。

本文将指导如何进行以下操作：

- 还原并创建新磁盘

- 跟踪还原操作状态

示例中，我们将参考资源组“testBkpVaultRG”下的现有备份保管库“TestBkpVault”

```azurepowershell-interactive
$TestBkpVault = Get-AzDataProtectionBackupVault -VaultName TestBkpVault -ResourceGroupName "testBkpVaultRG"
```

## <a name="restore-to-create-a-new-disk"></a>还原并创建新磁盘

### <a name="setting-up-permissions"></a>设置权限

备份保管库使用托管标识来访问其他 Azure 资源。 若要从备份中还原，备份保管库的托管标识需要对待还原磁盘所在的资源组拥有一组权限。

备份系统管库使用系统分配的托管标识（限制为每个资源一个），并绑定到此资源的生命周期。 可使用 Azure 基于角色的访问控制 (Azure RBAC) 来授予托管标识的访问权限。 托管标识是一种只能用于 Azure 资源的特殊类型的服务主体。 详细了解[托管标识](../active-directory/managed-identities-azure-resources/overview.md)。

为保管库系统分配的托管标识分配对磁盘还原/创建所在目标资源组的相关权限，如[此处](restore-managed-disks.md#restore-to-create-a-new-disk)所述。

### <a name="fetching-the-relevant-recovery-point"></a>正在提取相关恢复点

使用 [Get-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/get-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true) 命令获取所有实例并标识相关实例。

```azurepowershell-interactive
$AllInstances = Get-AzDataProtectionBackupInstance -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name
```

还可使用 **Az. Resourcegraph** 和 [Search-AzDataProtectionBackupInstanceInAzGraph](/powershell/module/az.dataprotection/search-azdataprotectionbackupinstanceinazgraph?view=azps-5.7.0&preserve-view=true) 命令搜索多个保管库和订阅中的实例。

```azurepowershell-interactive
$AllInstances = Search-AzDataProtectionBackupInstanceInAzGraph -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -DatasourceType AzureDisk -ProtectionStatus ProtectionConfigured
```

标识实例后，提取相关恢复点。

```azurepowershell-interactive
$rp = Get-AzDataProtectionRecoveryPoint -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupInstanceName $AllInstances[2].BackupInstanceName
```

### <a name="preparing-the-restore-request"></a>正在准备还原请求

创建目标资源组中要创建的新磁盘的 ARM ID，已分配对目标资源组的权限（[如上](#setting-up-permissions)详述）以及所需磁盘名称。 例如，在具有不同订阅的资源组 targetrg 下，磁盘可以命名为 PSTestDisk2。 

```azurepowershell-interactive
$targetDiskId = /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/targetrg/providers/Microsoft.Compute/disks/PSTestDisk2
```

使用 [Initialize-AzDataProtectionRestoreRequest](/powershell/module/az.dataprotection/initialize-azdataprotectionrestorerequest?view=azps-5.7.0&preserve-view=true) 命令以准备还原请求的所有相关详细信息。

```azurepowershell-interactive
$restorerequest = Initialize-AzDataProtectionRestoreRequest -DatasourceType AzureDisk -SourceDataStore OperationalStore -RestoreLocation $TestBkpVault.Location  -RestoreType AlternateLocation -TargetResourceId $targetDiskId -RecoveryPoint $rp[0].Name
```

### <a name="trigger-the-restore"></a>触发还原

使用 [Start-AzDataProtectionBackupInstanceRestore](/powershell/module/az.dataprotection/start-azdataprotectionbackupinstancerestore?view=azps-5.7.0&preserve-view=true) 命令以通过上述准备的请求来触发还原。

```azurepowershell-interactive
Start-AzDataProtectionBackupInstanceRestore -BackupInstanceName $AllInstances[2].BackupInstanceName -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Parameter $restorerequest
```

## <a name="tracking-job"></a>跟踪作业

使用 [Get-AzDataProtectionJob](/powershell/module/az.dataprotection/get-azdataprotectionjob?view=azps-5.7.0&preserve-view=true) 命令以跟踪所有作业。 可列出所有作业并提取特定作业的详细信息。

还可使用 Az ResourceGraph 以跟踪所有备份保管库中的所有作业。 使用 [Search-AzDataProtectionJobInAzGraph](/powershell/module/az.dataprotection/search-azdataprotectionjobinazgraph?view=azps-5.7.0&preserve-view=true) 命令以获取跨任何备份保管库的相关作业。

```azurepowershell-interactive
$job = Search-AzDataProtectionJobInAzGraph -Subscription $sub -ResourceGroupName "testBkpVaultRG" -Vault $TestBkpVault.Name -DatasourceType AzureDisk -Operation OnDemandBackup
```

## <a name="next-steps"></a>后续步骤

- [Azure 磁盘备份常见问题解答](disk-backup-faq.yml)
