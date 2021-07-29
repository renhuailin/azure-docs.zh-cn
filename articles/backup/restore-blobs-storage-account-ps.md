---
title: 通过 Azure Powershell 还原 Azure blob
description: 了解如何使用 Azure Powershell 将 Azure blob 还原到任何时间点。
ms.topic: conceptual
ms.date: 05/05/2021
ms.openlocfilehash: 653c3ce6fbfea4d890c3dc3e52fb1032a72b7eab
ms.sourcegitcommit: 2cb7772f60599e065fff13fdecd795cce6500630
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2021
ms.locfileid: "108803055"
---
# <a name="restore-azure-blobs-to-point-in-time-using-azure-powershell"></a>使用 Azure Powershell 将 Azure blob 还原到时间点

本文介绍如何使用 Azure 备份将 [blob](blob-backup-overview.md) 还原到任何时间点。

> [!IMPORTANT]
> Az 5.9.0 版本起提供对 Azure blob 的支持。

> [!IMPORTANT]
> 在继续使用 Azure 备份来还原 Azure blob 之前，请参阅[要点](blob-restore.md#before-you-start)。

本文将指导如何进行以下操作：

- 将 Azure blob 还原到时间点

- 跟踪还原操作状态

示例中，我们将参考资源组“testBkpVaultRG”下的现有备份保管库“TestBkpVault” 。

```azurepowershell-interactive
$TestBkpVault = Get-AzDataProtectionBackupVault -VaultName TestBkpVault -ResourceGroupName "testBkpVaultRG"
```

## <a name="restoring-azure-blobs-within-a-storage-account"></a>还原存储帐户中的 Azure blob

### <a name="fetching-the-valid-time-range-for-restore"></a>获取还原的有效时间范围

由于 blob 的操作备份是连续的，因此没有要从中还原的不同点。 相反，我们需要提取可以将 blob 还原到任何时间点的有效时间范围。 在此示例中，我们检查过去 30 天内有效的还原时间范围。

```azurepowershell-interactive
$startDate = (Get-Date).AddDays(-30)
$endDate = Get-Date
```

首先使用 [Get-AzDataProtectionBackupInstance](/powershell/module/az.dataprotection/get-azdataprotectionbackupinstance?view=azps-5.9.0&preserve-view=true) 命令提取所有实例并标识相关实例。

```azurepowershell-interactive
$AllInstances = Get-AzDataProtectionBackupInstance -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name
```

还可使用 Az. Resourcegraph 和 [Search-AzDataProtectionBackupInstanceInAzGraph](/powershell/module/az.dataprotection/search-azdataprotectionbackupinstanceinazgraph?view=azps-5.9.0&preserve-view=true) 命令搜索多个保管库和订阅中的实例。

```azurepowershell-interactive
$AllInstances = Search-AzDataProtectionBackupInstanceInAzGraph -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -DatasourceType AzureBlob -ProtectionStatus ProtectionConfigured
```

标识实例之后，使用 [AzDataProtectionRestorableTimeRange](/powershell/module/az.dataprotection/find-azdataprotectionrestorabletimerange) 命令提取相关恢复范围。

```azurepowershell-interactive
Find-AzDataProtectionRestorableTimeRange -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupInstanceName $AllInstances[2].BackupInstanceName -StartTime $startDate -endTime $endDate

EndTime    : 2021-04-24T08:57:36.4149422Z
ObjectType : RestorableTimeRange
StartTime  : 2021-03-25T14:27:31.0000000Z

$DesiredPIT = (Get-Date -Date "2021-04-23T02:47:02.9500000Z")
```

### <a name="preparing-the-restore-request"></a>正在准备还原请求

修复还原时间点之后，有多个还原选项。 使用 [Initialize-AzDataProtectionRestoreRequest](/powershell/module/az.dataprotection/initialize-azdataprotectionrestorerequest?view=azps-5.9.0&preserve-view=true) 命令以准备还原请求的所有相关详细信息。

#### <a name="restoring-all-the-blobs-to-a-point-in-time"></a>将所有 blob 还原到时间点

如果使用此选项，则会通过将存储帐户中的所有块 blob 回滚到选定的时间点来还原这些 blob。 包含大量数据或见证高流失率的存储帐户可能需要更长的时间才能还原。

```azurepowershell-interactive
$restorerequest = Initialize-AzDataProtectionRestoreRequest -DatasourceType AzureBlob -SourceDataStore OperationalStore -RestoreLocation $TestBkpVault.Location  -RestoreType OriginalLocation -PointInTime (Get-Date -Date "2021-04-23T02:47:02.9500000Z") -BackupInstance $AllInstances[2]
```

#### <a name="restoring-selected-containers"></a>还原选定的容器

使用此选项可浏览并选择最多 10 个要还原的容器。

```azurepowershell-interactive
$restorerequest = Initialize-AzDataProtectionRestoreRequest -DatasourceType AzureBlob -SourceDataStore OperationalStore -RestoreLocation $TestBkpVault.Location  -RestoreType OriginalLocation -PointInTime (Get-Date -Date "2021-04-23T02:47:02.9500000Z") -BackupInstance $AllInstances[2] -ItemLevelRecovery -ContainersList "abc","xyz"
```

#### <a name="restoring-containers-using-a-prefix-match"></a>使用前缀匹配还原容器

此选项允许你使用前缀匹配来还原 blob 的子集。 最多可以在一个容器内或跨多个容器指定 10 个字典范围的 blob，以在给定时间点将这些 blob 恢复到它们以前的状态。 需谨记以下几点：

- 可以使用正斜杠 (/) 来分隔容器名称和 blob 前缀
- 指定的范围的开始包括在内，但是不包括指定的范围。

[详细了解](blob-restore.md#use-prefix-match-for-restoring-blobs)使用前缀还原 blob 范围。

```azurepowershell-interactive
$restorerequest = Initialize-AzDataProtectionRestoreRequest -DatasourceType AzureBlob -SourceDataStore OperationalStore -RestoreLocation $TestBkpVault.Location  -RestoreType OriginalLocation -PointInTime (Get-Date -Date "2021-04-23T02:47:02.9500000Z") -BackupInstance $AllInstances[2] -ItemLevelRecovery -FromPrefixPattern "containerabc/aaa","containerabc/ccc" -ToPrefixPattern "containerabc/bbb","containerabc/ddd"
```

### <a name="trigger-the-restore"></a>触发还原

使用 [Start-AzDataProtectionBackupInstanceRestore](/powershell/module/az.dataprotection/start-azdataprotectionbackupinstancerestore?view=azps-5.9.0&preserve-view=true) 命令以通过上述准备的请求来触发还原。

```azurepowershell-interactive
Start-AzDataProtectionBackupInstanceRestore -BackupInstanceName $AllInstances[2].BackupInstanceName -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Parameter $restorerequest
```

## <a name="tracking-job"></a>跟踪作业

使用 [Get-AzDataProtectionJob](/powershell/module/az.dataprotection/get-azdataprotectionjob?view=azps-5.9.0&preserve-view=true) 命令跟踪所有作业。 你可以列出所有作业并提取特定作业详细信息。

你还可以使用 Az ResourceGraph 跟踪所有备份保管库中的所有作业。 使用[Search-AzDataProtectionJobInAzGraph](/powershell/module/az.dataprotection/search-azdataprotectionjobinazgraph?view=azps-5.9.0&preserve-view=true)命令获取可跨任何备份保管库的相关作业。

```azurepowershell-interactive
$job = Search-AzDataProtectionJobInAzGraph -Subscription $sub -ResourceGroupName "testBkpVaultRG" -Vault $TestBkpVault.Name -DatasourceType AzureBlob -Operation Restore
```

## <a name="next-steps"></a>后续步骤

[Azure blob 备份概述](blob-backup-overview.md)
