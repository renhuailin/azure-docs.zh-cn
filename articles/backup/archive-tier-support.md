---
title: " (预览版的存档层支持) "
description: 了解 Azure 备份的存档层支持
ms.topic: conceptual
ms.date: 02/18/2021
ms.openlocfilehash: 30a7915332d1d7ecab87b0db1ddc6dacc0fa69c9
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102050596"
---
# <a name="archive-tier-support-preview"></a> (预览版的存档层支持) 

客户依赖 Azure 备份来存储备份数据，包括其 Long-Term 保留 (从左向右) 备份数据，并在组织的符合性规则定义保留需求。 在大多数情况下，较旧的备份数据很少被访问，只是为了满足遵从性需求而存储。

除了快照和标准层以外，Azure 备份还支持在存档层中备份长期保留点。

## <a name="scope-for-preview"></a>预览范围

支持的工作负荷：

- Azure 虚拟机
  - 仅每月和每年恢复点。 不支持每日和每周恢复点。
  - Age >= Vault-Standard 层中的3个月
  - 保留期左 >= 6 个月
  - 无活动的每日和每周依赖项
- Azure 虚拟机中的 SQL Server
  - 仅完整恢复点。 不支持日志和差异。
  - Age >= 45 天（Vault-Standard 层）
  - 保留期左 >= 6 个月
  - 无依赖项

支持的客户端：

- 此功能是使用 PowerShell 提供的

>[!NOTE]
>Azure Vm 中的 Azure Vm 和 SQL Server 的存档层支持在有限个人注册的公共预览版中提供。 若要注册存档支持，请使用此 [链接](https://aka.ms/ArchivePreviewInterestForm)。

## <a name="get-started-with-powershell"></a>PowerShell 入门

1. 下载 [最新的 PowerShell 模块](https://github.com/Azure/azure-powershell/tree/Az.RecoveryServices-preview) (预览) 。
1. 使用 [AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) cmdlet 连接到 Azure。
1. 登录到你的订阅：

   `Set-AzContext -Subscription "SubscriptionName"`

1. 获取保管库：

    `$vault =  Get-AzRecoveryServicesVault -ResourceGroupName "rgName" -Name "vaultName"`

1. 获取备份项列表：

    `$BackupItemList = Get-AzRecoveryServicesBackupItem -vaultId $vault.ID -BackupManagementType "AzureVM/AzureWorkload" -WorkloadType "AzureVM/MSSQL"`

1. 获取备份项。

    - 对于 Azure 虚拟机：

        `$bckItm = $BackupItemList | Where-Object {$_.Name -match '<vmName>'}`

    - 对于 Azure 虚拟机中的 SQL Server：

        `$bckItm = $BackupItemList | Where-Object {$_.Name -match '<dbName>' -and $_.ContainerName -match '<vmName>'}`

## <a name="use-powershell"></a>使用 PowerShell

### <a name="check-archivable-recovery-points"></a>检查存档恢复点

```azurepowershell
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -VaultId $vault.ID -Item $bckItm  -IsReadyForMove $true -TargetTier VaultArchive
```

这会列出与已准备好移动到存档的特定备份项相关联的所有恢复点。

### <a name="check-why-a-recovery-point-cannot-be-moved-to-archive"></a>检查无法将恢复点移到存档的原因

```azurepowershell
$rp[0].RecoveryPointMoveReadinessInfo["ArchivedRP"]
```

其中 `$rp[0]` 是要检查其不存档的恢复点的位置。

示例输出：

```output
IsReadyForMove  AdditionalInfo
--------------  --------------
False           Recovery-Point Type is not eligible for archive move as it is already moved to archive tier
```

### <a name="check-recommended-set-of-archivable-points-only-for-azure-vms"></a> (仅为 Azure Vm 检查建议的一组存档点) 

与虚拟机关联的恢复点本质上是递增的。 将某个特定恢复点移到存档后，它将转换为完整备份，然后移动到 "存档"。 因此，与存档相关的成本节约取决于数据源的变动。

因此，Azure 备份会附带一组建议的恢复点，这些恢复点在一起移动时可能会节省成本。

>[!NOTE]
>节省的成本取决于多种原因，对于任意两个实例可能不是相同的。

```azurepowershell
$RecommendedRecoveryPointList = Get-AzRecoveryServicesBackupRecommendedArchivableRPGroup -Item $bckItm -VaultId $vault.ID
```

### <a name="move-to-archive"></a>移到存档

```azurepowershell
Move-AzRecoveryServicesBackupRecoveryPoint -VaultId $vault.ID -RecoveryPoint $rp[2] -SourceTier VaultStandard -DestinationTier VaultArchive
```

此命令将存档恢复点移动到 archive。 它将返回一个作业，该作业可用于通过门户和 PowerShell 跟踪移动操作。

### <a name="view-archived-recovery-points"></a>查看存档的恢复点

此命令返回所有存档的恢复点。

```azurepowershell
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -VaultId $vault.ID -Item $bckItm -Tier VaultArchive
```

### <a name="restore-with-powershell"></a>通过 PowerShell 还原

对于存档中的恢复点，Azure 备份提供了集成的还原方法。

集成还原过程分为两步。 第一步涉及解除冻结存储在存档中的恢复点，并将其暂时存储在保管库标准层中，持续时间 (也称为解除冻结持续时间) 介于10到30天之间。 默认值为15天。 解除冻结（标准和高优先级）有两个不同的优先级。 了解有关 [解除冻结优先级](https://docs.microsoft.com/azure/storage/blobs/storage-blob-rehydration#rehydrate-an-archived-blob-to-an-online-tier)的详细信息。

>[!NOTE]
>
>- 不能更改选择的解除冻结持续时间，解除冻结恢复点将保留在解除冻结持续时间的标准层中。
>- 额外的解除冻结步骤会产生成本。

有关 Azure 虚拟机的各种还原方法的详细信息，请参阅 [使用 PowerShell 还原 AZURE VM](backup-azure-vms-automation.md#restore-an-azure-vm)。

```azurepowershell
Restore-AzRecoveryServicesBackupItem -VaultLocation $vault.Location -RehydratePriority "Standard" -RehydrateDuration 15 -RecoveryPoint $rp -StorageAccountName "SampleSA" -StorageAccountResourceGroupName "SArgName" -TargetResourceGroupName $vault.ResourceGroupName -VaultId $vault.ID
```

若要还原 SQL Server，请执行 [以下步骤](backup-azure-sql-automation.md#restore-sql-dbs)。 所需的其他参数为 **RehydrationPriority** 和 **RehydrationDuration**。

### <a name="view-jobs-from-powershell"></a>从 PowerShell 查看作业

若要查看移动和还原作业，请使用以下 PowerShell cmdlet：

```azurepowershell
Get-AzRecoveryServicesBackupJob -VaultId $vault.ID
```

## <a name="use-the-portal"></a>使用门户

### <a name="check-archived-recovery-point"></a>检查存档的恢复点

你现在可以查看已移动到 "存档" 的所有恢复点。

![所有恢复点。](./media/archive-tier-support/restore-points.png)

### <a name="restore-in-the-portal"></a>在门户中还原

对于已移动到存档的恢复点，restore 要求你添加解除冻结 duration 和解除冻结优先级的参数。

![在门户中还原。](./media/archive-tier-support/restore-in-portal.png)

### <a name="view-jobs-in-the-portal"></a>在门户中查看作业

![在门户中查看作业。](./media/archive-tier-support/view-jobs-portal.png)

### <a name="modify-protection"></a>修改保护

可以通过两种方式修改数据源的保护：

- 修改现有策略
- 使用新策略保护数据源

在这两种情况下，新策略将应用于所有旧恢复点，这些恢复点在标准层中，在存档层中。 如果策略发生更改，则可能会删除较旧的恢复点。

当恢复点移至存档时，它们将受到180天的提前删除期的攻击。 按比例计费。 如果删除了不包含在180天存档中的恢复点，则该恢复点将产生相当于180减去在标准层中花费的天数的成本。

至少包含六个月未存档的恢复点将在删除时产生较早的删除成本。

## <a name="stop-protection-and-delete-data"></a>停止保护并删除数据

停止保护并删除数据删除所有恢复点。 对于存档层中未超过180天持续时间的存档中的恢复点，删除恢复点将导致早期删除成本。

## <a name="error-codes-and-troubleshooting-steps"></a>错误代码和故障排除步骤

不能将恢复点移至存档时出现多个错误代码。

### <a name="recoverypointtypenoteligibleforarchive"></a>RecoveryPointTypeNotEligibleForArchive

**错误消息** -Recovery-Point 类型不适用于存档移动

**说明** –当所选恢复点类型不符合移动到存档的条件时，将显示此错误代码。

**建议操作**–在 [此处](#scope-for-preview)检查恢复点的资格

### <a name="recoverypointhaveactivedependencies"></a>RecoveryPointHaveActiveDependencies

**错误消息** -具有用于还原的活动依赖项的 Recovery-Point 不符合存档移动的条件

**说明-** 所选恢复点具有活动依赖项，因此无法将其移动到存档中。

**建议操作**–在 [此处](#scope-for-preview)检查恢复点的资格

### <a name="minlifespaninstandardrequiredforarchive"></a>MinLifeSpanInStandardRequiredForArchive

**错误消息** -Recovery-Point 不符合在保管库中花费的时间，因为在保管库中的寿命低于所需的最小值

**说明** -对于 azure 虚拟机，恢复点必须保持在标准层中至少三个月的时间，并且在 azure 虚拟机中的 SQL Server 为45天

**建议操作**–在 [此处](#scope-for-preview)检查恢复点的资格

### <a name="minremaininglifespaninarchiverequired"></a>MinRemainingLifeSpanInArchiveRequired

**错误消息** -Recovery-Point 剩余寿命小于所需的最小值。

**说明** -存档移动资格恢复点所需的最小使用期限为6个月。

**建议操作**–在 [此处](#scope-for-preview)检查恢复点的资格

### <a name="usererrorrecoverypointalreadyinarchivetier"></a>UserErrorRecoveryPointAlreadyInArchiveTier

**错误消息** -Recovery-Point 不适用于存档移动，因为它已移至存档层

**说明** –所选恢复点已在存档中。 因此，它不能移动到存档。

### <a name="usererrordatasourcetypeisnotsupportedforrecommendationapi"></a>UserErrorDatasourceTypeIsNotSupportedForRecommendationApi

**错误消息** -数据源类型不符合建议 API。

**说明** -建议的 API 仅适用于 Azure 虚拟机。 它不适用于所选的数据源类型。

### <a name="usererrorrecoverypointalreadyrehydrated"></a>UserErrorRecoveryPointAlreadyRehydrated

**错误消息** -恢复点已解除冻结。 此 RP 上不允许解除冻结。

**说明** –所选恢复点已解除冻结。

### <a name="usererrorrecoverypointisnoteligibleforarchivemove"></a>UserErrorRecoveryPointIsNotEligibleForArchiveMove

**错误消息** -恢复点不适合进行存档移动。

**说明** –所选恢复点不适合进行存档移动。

### <a name="usererrorrecoverypointnotrehydrated"></a>UserErrorRecoveryPointNotRehydrated

**错误****消息**-存档恢复点未解除冻结。 解除冻结在存档 RP 上完成后重试还原。

**说明** –恢复点不是解除冻结的。 请在解除冻结恢复点后尝试还原。

### <a name="usererrorrecoverypointrehydrationnotallowed"></a>UserErrorRecoveryPointRehydrationNotAllowed

**错误****消息**-只有存档恢复点支持解除冻结-仅存档恢复点支持解除冻结

**Description** –所选恢复点不允许解除冻结。

### <a name="usererrorrecoverypointrehydrationalreadyinprogress"></a>UserErrorRecoveryPointRehydrationAlreadyInProgress

**错误消息** –存档恢复点的解除冻结已 In-Progress。

**说明** –所选恢复点的解除冻结已在进行中。

### <a name="rpmovenotsupportedduetoinsufficientretention"></a>RPMoveNotSupportedDueToInsufficientRetention

**错误消息** -由于策略中指定的保留期不足，无法将恢复点移到存档层

**建议的操作** -使用适当的保留设置对受保护的项更新策略，然后重试。

### <a name="rpmovereadinesstobedetermined"></a>RPMoveReadinessToBeDetermined

**错误消息** -我们仍在确定是否可以移动此恢复点。

**说明** –恢复点的移动准备情况尚未确定。

**建议的操作** -等待一段时间后再次检查。

## <a name="frequently-asked-questions"></a>常见问题

### <a name="what-will-happen-to-archive-recovery-points-if-i-stop-protection-and-retain-data"></a>如果停止保护并保留数据，存档恢复点会发生什么情况？

恢复点将永久保留在存档中。 有关详细信息，请参阅对 [恢复点停止保护的影响](manage-recovery-points.md#impact-of-stop-protection-on-recovery-points)。

## <a name="next-steps"></a>后续步骤

- [Azure 备份定价](azure-backup-pricing.md)
