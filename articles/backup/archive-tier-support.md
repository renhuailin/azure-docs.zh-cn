---
title: 存档层支持
description: 了解对 Azure 备份的存档层支持
ms.topic: conceptual
ms.date: 09/10/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 0468e463caa6d589b22596d2fe845014e96e10b8
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128632455"
---
# <a name="archive-tier-support"></a>存档层支持

客户依靠 Azure 备份来存储备份数据，包括可满足由组织的合规性规则定义的保留需求的长期保留 (LTR) 备份数据。 在大多数情况下，较旧的备份数据很少被访问，只是为了满足合规性需求而存储。

除了快照和标准层以外，Azure 备份还支持备份存档层中的长期保留点。

## <a name="scope"></a>范围

支持的工作负荷：

- Azure 虚拟机
  - 仅每月和每年恢复点。 不支持每日和每周恢复点。
  - 保管库标准层中的保留时间不少于 3 个月
  - 剩余保留时间不少于 6 个月
  - 无每日和每周活动依赖项
- Azure 虚拟机中的 SQL Server
  - 仅完整恢复点。 不支持日志和差异。
  - 保管库标准层中的保留时间不少于 45 天
  - 剩余保留时间不少于 6 个月
  - 无依赖项

支持的客户端：

- 使用 PowerShell 来提供功能

>[!Note]
>Azure VM 中对 SQL Server 的存档层支持现在在多个区域正式发布。 有关受支持的区域的详细列表，请参阅[支持矩阵](#support-matrix)。    <br><br>    对于 Azure VM 中提供 SQL Server 的其他区域，存档层支持以有限公共预览版提供。 对 Azure 虚拟机的存档层支持也以有限公共预览版提供。 若要注册有限公共预览版，请使用此[链接](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR463S33c54tEiJLEM6Enqb9UNU5CVTlLVFlGUkNXWVlMNlRPM1lJWUxLRy4u)。

## <a name="get-started-with-powershell"></a>PowerShell 入门

1. 从 GitHub 下载[最新](https://github.com/PowerShell/PowerShell/releases)版本的 PowerShell。

1. 在 PowerShell 中运行以下命令：
  
    ```azurepowershell
    install-module -name Az.RecoveryServices -Repository PSGallery -RequiredVersion 4.4.0 -AllowPrerelease -force
    ```

1. 使用 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet 连接到 Azure。
1. 登录到订阅：

   `Set-AzContext -Subscription "SubscriptionName"`

1. 获取保管库：

    `$vault =  Get-AzRecoveryServicesVault -ResourceGroupName "rgName" -Name "vaultName"`

1. 获取备份项列表：

    - 对于 Azure 虚拟机：

        `$BackupItemList = Get-AzRecoveryServicesBackupItem -vaultId $vault.ID -BackupManagementType "AzureVM" -WorkloadType "AzureVM"`

    - 对于 Azure 虚拟机中的 SQL Server：

        `$BackupItemList = Get-AzRecoveryServicesBackupItem -vaultId $vault.ID -BackupManagementType "AzureWorkload" -WorkloadType "MSSQL"`

1. 获取备份项。

    - 对于 Azure 虚拟机：

        `$bckItm = $BackupItemList | Where-Object {$_.Name -match '<vmName>'}`

    - 对于 Azure 虚拟机中的 SQL Server：

        `$bckItm = $BackupItemList | Where-Object {$_.FriendlyName -eq '<dbName>' -and $_.ContainerName -match '<vmName>'}`

1. 添加要查看其恢复点的日期范围。 例如，如果要查看过去 124 天到过去 95 天之间的恢复点，请使用以下命令：

   ```azurepowershell
    $startDate = (Get-Date).AddDays(-124)
    $endDate = (Get-Date).AddDays(-95) 

    ```
    >[!NOTE]
    >若要查看不同时间范围的恢复点，请相应地修改开始日期和结束日期。
## <a name="use-powershell"></a>使用 PowerShell

### <a name="check-the-archivable-status-of-all-the-recovery-points"></a>检查所有恢复点的可存档状态

现在，可以使用以下 cmdlet 检查备份项的所有恢复点的可存档状态：

```azurepowershell
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -VaultId $vault.ID -Item $bckItm -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime() 

$rp | select RecoveryPointId, @{ Label="IsArchivable";Expression={$_.RecoveryPointMoveReadinessInfo["ArchivedRP"].IsReadyForMove}}, @{ Label="ArchivableInfo";Expression={$_.RecoveryPointMoveReadinessInfo["ArchivedRP"].AdditionalInfo}}
```

### <a name="check-archivable-recovery-points"></a>查看可存档的恢复点

```azurepowershell
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -VaultId $vault.ID -Item $bckItm -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime() -IsReadyForMove $true -TargetTier VaultArchive
```

这会列出与已准备好移动到存档的特定备份项相关联的所有恢复点（从开始日期到结束日期）。 你还可以修改开始日期和结束日期。

### <a name="check-why-a-recovery-point-cannot-be-moved-to-archive"></a>查看无法将恢复点移动到存档的原因

```azurepowershell
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -VaultId $vault.ID -Item $bckItm -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime() -IsReadyForMove $false -TargetTier VaultArchive
$rp[0].RecoveryPointMoveReadinessInfo["ArchivedRP"]
```

其中 `$rp[0]` 是要查看其不可存档原因的恢复点。

示例输出：

```output
IsReadyForMove  AdditionalInfo
--------------  --------------
False           Recovery-Point Type is not eligible for archive move as it is already moved to archive tier
```

### <a name="check-recommended-set-of-archivable-points-only-for-azure-vms"></a>查看一组建议的可存档点（仅适用于 Azure VM）

与虚拟机关联的恢复点本质上是递增的。 将特定恢复点移动到存档后，它将转换为完整备份，然后移动到存档。 因此，与移动到存档关联的成本节省取决于数据源的变动。

因此，Azure 备份会附带一组建议的恢复点，这些恢复点在一起移动时可能会节省成本。

>[!NOTE]
>成本节省取决于多种原因，任意两个实例的原因可能都不相同。

```azurepowershell
$RecommendedRecoveryPointList = Get-AzRecoveryServicesBackupRecommendedArchivableRPGroup -Item $bckItm -VaultId $vault.ID
```

### <a name="move-to-archive"></a>移动到存档

```azurepowershell
Move-AzRecoveryServicesBackupRecoveryPoint -VaultId $vault.ID -RecoveryPoint $rp[0] -SourceTier VaultStandard -DestinationTier VaultArchive
```

其中，`$rp[0]` 是列表中的第一个恢复点。 如果要移动其他恢复点，请使用 `$rp[1]`、`$rp[2]`依此类推。

此命令将可存档的恢复点移动到存档。 它将返回一个作业，该作业可用于通过门户和 PowerShell 跟踪移动操作。

### <a name="view-archived-recovery-points"></a>查看存档的恢复点

此命令返回所有存档的恢复点。

```azurepowershell
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -VaultId $vault.ID -Item $bckItm -Tier VaultArchive -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
```

### <a name="restore-with-powershell"></a>使用 PowerShell 进行还原

对于存档中的恢复点，Azure 备份提供了集成的还原方法。

集成还原是一个两步过程。 第一步是解除冻结存储在存档中的恢复点，并将其临时存储在保管库标准层中，持续时间（也称为解除冻结持续时间）介于 10 到 30 天之间。 默认值为 15 天。 解除冻结有两个不同的优先级：标准和高优先级。 了解有关[解除冻结优先级](../storage/blobs/archive-rehydrate-overview.md#rehydration-priority)的详细信息。

>[!NOTE]
>
>- 解除冻结持续时间一旦选择就无法更改，解除冻结恢复点在解除冻结持续时间内保留在标准层中。
>- 解除冻结的其他步骤会产生费用。

有关 Azure 虚拟机的各种还原方法的详细信息，请参阅[使用 PowerShell 还原 Azure VM](backup-azure-vms-automation.md#restore-an-azure-vm)。

```azurepowershell
Restore-AzRecoveryServicesBackupItem -VaultLocation $vault.Location -RehydratePriority "Standard" -RehydrateDuration 15 -RecoveryPoint $rp -StorageAccountName "SampleSA" -StorageAccountResourceGroupName "SArgName" -TargetResourceGroupName $vault.ResourceGroupName -VaultId $vault.ID
```

若要还原 SQL Server，请执行[这些步骤](backup-azure-sql-automation.md#restore-sql-dbs)。 `Restore-AzRecoveryServicesBackupItem` 命令需要两个附加参数，即 RehydrationDuration 和 RehydrationPriority 。

### <a name="view-jobs-from-powershell"></a>通过 PowerShell 查看作业

若要查看移动和还原作业，请使用以下 PowerShell cmdlet：

```azurepowershell
Get-AzRecoveryServicesBackupJob -VaultId $vault.ID
```

### <a name="move-recovery-points-to-archive-tier-at-scale"></a>将恢复点大规模移到存档层

现在可以使用示例脚本执行大规模操作。 [详细了解](https://github.com/hiaga/Az.RecoveryServices/blob/master/README.md)如何运行示例脚本。 可以从[此处](https://github.com/hiaga/Az.RecoveryServices)下载脚本。

可以使用 Azure 备份提供的示例脚本执行以下操作：

- 将 Azure VM 中 SQL Server 的特定数据库/所有数据库的所有符合条件的恢复点移到存档层。
- 将已为特定 Azure 虚拟机建议的所有恢复点移到存档层。
 
还可以根据需求编写脚本，或修改上述示例脚本以提取所需的备份项。

## <a name="use-the-portal"></a>使用门户

### <a name="check-archived-recovery-point"></a>查看存档的恢复点

你现在可以查看已移动到存档的所有恢复点。

![所有恢复点。](./media/archive-tier-support/restore-points.png)

### <a name="restore-in-the-portal"></a>在门户中还原

对于已移动到存档的恢复点，还原要求你添加解除冻结持续时间和解除冻结优先级的参数。

![在门户中还原。](./media/archive-tier-support/restore-in-portal.png)

### <a name="view-jobs-in-the-portal"></a>在门户中查看作业

![在门户中查看作业。](./media/archive-tier-support/view-jobs-portal.png)

### <a name="modify-protection"></a>修改保护

可以通过两种方式修改数据源的保护：

- 修改现有策略
- 使用新策略保护数据源

在这两种情况下，新策略将应用于所有旧恢复点，有些位于标准层中，而有些位于存档层中。 因此，如果策略发生更改，则可能会删除旧恢复点。

当恢复点移动到存档时，会有 180 天的早期删除期。 费用按比例计算。 如果删除了存档未满 180 天的恢复点，则该恢复点将产生费用（相当于 180 减去在标准层中保留的天数）。

还没有达到存档时长下限（即 6 个月）的恢复点将在删除时产生早期删除费用。

## <a name="stop-protection-and-delete-data"></a>停止保护并删除数据

停止保护并删除数据会删除所有恢复点。 对于保留在存档层中未满 180 天的恢复点，删除恢复点将导致产生早期删除费用。

## <a name="support-matrix"></a>支持矩阵

| 工作负荷 | 预览 | 正式发布 |
| --- | --- | --- |
| Azure VM 中的 SQL Server | 美国中南部、美国中北部、西欧 | 澳大利亚东部、印度中部、北欧、东南亚、东亚、澳大利亚东南部、加拿大中部、巴西南部、加拿大东部、法国中部、法国南部、日本东部、日本西部、韩国中部、韩国南部、印度南部、英国西部、英国南部、美国中部、美国东部 2、美国西部、美国西部 2、美国中西部、美国东部 |
| Azure 虚拟机 | 美国东部、美国东部 2、美国中部、美国中南部、美国西部、美国西部 2、美国中西部、美国中北部、巴西南部、加拿大东部、加拿大中部、西欧、英国南部、英国西部、东亚、日本东部、印度南部、东南亚、澳大利亚东部、印度中部、北欧、澳大利亚东南部、法国中部、法国南部、日本西部、韩国中部、韩国南部 | 无 |

## <a name="error-codes-and-troubleshooting-steps"></a>错误代码和故障排除步骤

无法将恢复点移动到存档时出现多个错误代码。

### <a name="recoverypointtypenoteligibleforarchive"></a>RecoveryPointTypeNotEligibleForArchive

错误消息 - 恢复点类型不符合存档移动的条件

说明 – 当所选恢复点类型不符合移动到存档的条件时，将显示此错误代码。

建议的操作 – 在[此处](#scope)检查恢复点的资格

### <a name="recoverypointhaveactivedependencies"></a>RecoveryPointHaveActiveDependencies

错误消息 - 具有用于还原的活动依赖项的恢复点不符合存档移动的条件

说明 – 所选恢复点具有活动依赖项，因此无法移动到存档。

建议的操作 – 在[此处](#scope)检查恢复点的资格

### <a name="minlifespaninstandardrequiredforarchive"></a>MinLifeSpanInStandardRequiredForArchive

错误消息 - 恢复点不符合存档移动的条件，因为保管库标准层中消耗的生命周期值小于所需的最低值

说明 – 对于 Azure 虚拟机，恢复点必须在标准层中至少保留三个月，对于 Azure 虚拟机中的 SQL Server，保留 45 天

建议的操作 – 在[此处](#scope)检查恢复点的资格

### <a name="minremaininglifespaninarchiverequired"></a>MinRemainingLifeSpanInArchiveRequired

错误消息 - 恢复点剩余生命周期值小于所需的最低值。

说明 – 恢复点为符合存档移动的条件所需的最小生命周期值为 6 个月。

建议的操作 – 在[此处](#scope)检查恢复点的资格

### <a name="usererrorrecoverypointalreadyinarchivetier"></a>UserErrorRecoveryPointAlreadyInArchiveTier

错误消息 - 恢复点不符合存档移动的条件，因为它已经被移动到存档层中

说明 – 所选恢复点已在存档中。 因此，它不符合移动到存档的条件。

### <a name="usererrordatasourcetypeisnotsupportedforrecommendationapi"></a>UserErrorDatasourceTypeIsNotSupportedForRecommendationApi

错误消息 - 数据源类型不符合建议 API 的条件。

说明 – 建议的 API 仅适用于 Azure 虚拟机。 它不适用于所选的数据源类型。

### <a name="usererrorrecoverypointalreadyrehydrated"></a>UserErrorRecoveryPointAlreadyRehydrated

错误消息 - 恢复点已解除冻结。 在此 RP 上不允许解除冻结。

说明 – 所选恢复点已解除冻结。

### <a name="usererrorrecoverypointisnoteligibleforarchivemove"></a>UserErrorRecoveryPointIsNotEligibleForArchiveMove

错误消息 - 恢复点不符合存档移动的条件。

说明 – 所选恢复点不符合存档移动的条件。

### <a name="usererrorrecoverypointnotrehydrated"></a>UserErrorRecoveryPointNotRehydrated

错误消息  - 存档恢复点没有解除冻结。 请在存档 RP 完成解除冻结后重试还原。

说明 – 恢复点没有解除冻结。 请在解除冻结恢复点后尝试还原。

### <a name="usererrorrecoverypointrehydrationnotallowed"></a>UserErrorRecoveryPointRehydrationNotAllowed

错误消息  - 只支持对存档恢复点进行解除冻结 - 只支持对存档恢复点进行解除冻结

说明 – 所选恢复点不允许解除冻结。

### <a name="usererrorrecoverypointrehydrationalreadyinprogress"></a>UserErrorRecoveryPointRehydrationAlreadyInProgress

错误消息 – 存档恢复点的解除冻结已在进行中。

说明 – 所选恢复点的解除冻结已在进行中。

### <a name="rpmovenotsupportedduetoinsufficientretention"></a>RPMoveNotSupportedDueToInsufficientRetention

错误消息 - 无法将恢复点移到存档层中，因为策略中指定的保留期不够长

建议的操作 - 请使用适当的保留设置来更新受保护项上的策略，然后重试。

### <a name="rpmovereadinesstobedetermined"></a>RPMoveReadinessToBeDetermined

错误消息 - 我们仍在确定能否移动此恢复点。

说明 – 恢复点的移动准备情况尚未确定。

建议的操作 - 等待一段时间后再次检查。

## <a name="frequently-asked-questions"></a>常见问题

### <a name="what-will-happen-to-archive-recovery-points-if-i-stop-protection-and-retain-data"></a>如果停止保护并保留数据，存档恢复点会发生什么情况？

恢复点将永久保留在存档中。 有关详细信息，请参阅[停止保护对恢复点的影响](manage-recovery-points.md#impact-of-stop-protection-on-recovery-points)。

### <a name="is-cross-region-restore-supported-from-archive-tier"></a>存档层是否支持跨区域还原？

将 GRS 保管库中的数据从标准层移动到存档层时，数据会移动到 GRS 存档中。 即使启用了跨区域还原，也是如此。 备份数据移动到存档层后，无法将数据还原到配对区域。 但在区域故障期间，辅助区域中的备份数据将可用于还原。 

从主要区域存档层中的恢复点还原时，恢复点将复制到标准层，并根据解除冻结的持续时间保留到主要区域和辅助区域。 可以从这些解除冻结的恢复点执行跨区域还原。

## <a name="next-steps"></a>后续步骤

- [Azure 备份定价](azure-backup-pricing.md)