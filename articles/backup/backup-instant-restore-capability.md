---
title: Azure 即时还原功能
description: Azure 即时还原功能以及有关 VM 备份堆栈、资源管理器部署模型的常见问题解答
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 04/23/2019
ms.openlocfilehash: a70e2780cb2ceeb37379557ad9cb4bb931e9b6f1
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123252225"
---
# <a name="get-improved-backup-and-restore-performance-with-azure-backup-instant-restore-capability"></a>使用 Azure 备份即时还原功能获得更高的备份和还原性能

> [!NOTE]
> 根据用户的反馈，我们已将“VM 备份堆栈 V2”重命名为“即时还原”，以减少与 Azure Stack 功能的混淆。
> 所有 Azure 备份用户现在已升级到“即时还原”。

即时还原的新模型提供以下功能增强：

* 可以使用执行备份作业期间创建的用于恢复的快照，而无需等待将数据传输到保管库的操作完成。 它缩短了快照在触发还原之前复制到保管库的等待时间。
* 默认情况下，可在本地保留快照两天，这缩短了备份和还原时间。 此默认快照保留期值可配置为 1 到 5 天的任何值。
* 最大支持 32 TB 的磁盘大小。 Azure 备份不建议重设磁盘大小。
* 支持标准 SSD 磁盘、标准 HDD 磁盘和高级 SSD 磁盘。
* 还原时可以使用非托管 VM 的原始存储帐户（按磁盘）。 即使 VM 的磁盘跨存储帐户进行分布，也具备此能力。 这可以加快各种 VM 配置的还原操作。
* 对于在存储帐户中使用非托管高级磁盘的 VM 的备份（使用即时还原），建议从总的已分配存储空间中分配 50% 的可用空间，这仅在首次备份时是必需的。 首次备份完成后，50% 的可用空间不再是备份的要求。

## <a name="whats-new-in-this-feature"></a>功能亮点

目前，备份作业包括两个阶段：

1. 获取 VM 快照。
2. 将 VM 快照传输到 Azure 恢复服务保管库。

只有在完成阶段 1 和 2 之后，才认为已创建恢复点。 在执行此项升级的过程中，完成快照后会立即创建恢复点，可以在相同的还原流中，使用此快照恢复点类型执行还原。 可以在 Azure 门户中使用“快照”作为恢复点类型来识别此恢复点，快照传输到保管库后，恢复点类型将更改为“快照和保管库”。

![VM 备份堆栈资源管理器部署模型中的备份作业 - 存储和保管库](./media/backup-azure-vms/instant-rp-flow.png)

默认情况下，快照将保留两天。 此功能允许从保管库中的这些快照执行还原操作，并可缩短还原时间。 它减少了从保管库转换数据和复制回数据所需的时间。

## <a name="feature-considerations"></a>功能注意事项

* 快照将连同磁盘一起存储，以提高恢复点的创建速度并加快还原操作。 因此，可以查看 7 天内创建的快照的相应存储成本。
* 增量快照作为页 blob 存储。 使用非托管磁盘的所有用户需要为其本地存储帐户中存储的快照付费。 由于托管 VM 备份使用的还原点集合在基础存储级别使用 Blob 快照，因此对于托管磁盘，你将看到与 Blob 快照定价对应的成本，并且成本是递增的。
* 对于高级存储帐户，为即时恢复点创建的快照计入分配空间的 10-TB 限制。
* 可以根据还原需求配置快照保留期。 可按如下所述，在备份策略窗格中根据要求将快照保留期设置为最短一天。 如果不经常执行还原，这可帮助节省保留快照的成本。
* 这是单向升级。 一旦升级到即时还原，就不能回退。

>[!NOTE]
>使用此即时还原升级，所有客户（**包含新客户和现有客户**）的快照保留期都将设置为两天的默认值。 但是，可以根据需要将持续时间设置为 1 到 5 天之间的任意值。

## <a name="cost-impact"></a>成本影响

增量快照存储在 VM 的存储帐户中，用于即时恢复。 使用增量快照意味着快照占用的空间等于创建该快照后写入的页面所占用的空间。 仍然对快照占用的每 GB 使用空间计费，每 GB 价格与[定价页](https://azure.microsoft.com/pricing/details/managed-disks/)上提到的价格相同。 对于使用非托管磁盘的 VM，可以在每个磁盘的 VHD 文件的菜单中看到快照。 对于托管磁盘，快照将存储在指定资源组的还原点集合资源中，并且快照本身不直接可见。

>[!NOTE]
> 就每周策略来说，快照保留期固定为 5 天。

## <a name="configure-snapshot-retention"></a>配置快照保留期

### <a name="using-azure-portal"></a>使用 Azure 门户

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

在 Azure 门户中可以看到，“即时还原”部分下的“VM 备份策略”窗格中添加了一个字段。  对于与特定备份策略关联的所有 VM，可以在“VM 备份策略”窗格中更改快照保留持续时间。

![即时还原功能](./media/backup-azure-vms/instant-restore-capability.png)

### <a name="using-powershell"></a>使用 PowerShell

>[!NOTE]
> 从 Az PowerShell 版本 1.6.0 开始，可以使用 PowerShell 在策略中更新即时还原快照保留期

```powershell
$bkpPol = Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM"
$bkpPol.SnapshotRetentionInDays=5
Set-AzRecoveryServicesBackupProtectionPolicy -policy $bkpPol
```

每个策略的默认快照保留期设置为 2 天。 可以将此值更改为 1 到 5 天。 就每周策略来说，快照保留期固定为 5 天。

## <a name="frequently-asked-questions"></a>常见问题

### <a name="what-are-the-cost-implications-of-instant-restore"></a>哪些因素影响即时还原的成本？

快照将连同磁盘一起存储，以加速恢复点的创建和还原操作。 因此，你会看到与 VM 备份策略中选择的快照保留期相对应的存储成本。

### <a name="in-premium-storage-accounts-do-the-snapshots-taken-for-instant-recovery-point-occupy-the-10-tb-snapshot-limit"></a>在高级存储帐户中，为即时恢复点创建的快照是否会占用 10-TB 快照限制？

是的，对于高级存储帐户，为即时恢复点创建的快照会占用 10 TB 的已分配快照空间。

### <a name="how-does-the-snapshot-retention-work-during-the-five-day-period"></a>在 5 天期限内，快照保留的工作方式是怎样的？

如果每天创建一个新快照，则有 5 个单独的增量快照。 快照大小取决于数据变动率，在大多数情况下，变动率大约为 2%-7%。

### <a name="is-an-instant-restore-snapshot-an-incremental-snapshot-or-full-snapshot"></a>即时还原快照是增量快照还是完整快照？

即时还原功能创建的快照是增量快照。

### <a name="how-can-i-calculate-the-approximate-cost-increase-due-to-instant-restore-feature"></a>如何计算即时还原功能大约增加的成本？

这取决于 VM 的变动率。 在稳定状态下，可以假设增加的成本 = 快照保留期 * 每个 VM 的每日变动率 * 每 GB 存储成本。

### <a name="if-the-recovery-type-for-a-restore-point-is-snapshot-and-vault-and-i-perform-a-restore-operation-which-recovery-type-will-be-used"></a>如果还原点的恢复类型是“快照和保管库”，而我执行了还原操作，那么，将使用哪种恢复类型？

如果恢复类型是“快照和保管库”，则会从本地快照自动执行还原，与从保管库执行还原相比，其速度要快得多。

### <a name="what-happens-if-i-select-retention-period-of-restore-point-tier-2-less-than-the-snapshot-tier1-retention-period"></a>如果选择的还原点（第 2 层）保留期小于快照（第 1 层）保留期，会发生什么情况？

除非删除快照（第 1 层），否则新模型不允许删除还原点（第 2 层）。 建议将还原点（第 2 层）保留期设置为大于快照保留期。

### <a name="why-does-my-snapshot-still-exist-even-after-the-set-retention-period-in-backup-policy"></a>为何我即使在备份策略中设置了保留期，我的快照也仍然存在？

如果恢复点包含快照并且它是可用的最新恢复点，则它会一直保留到下一次成功备份为止。 这取决于指定的“垃圾回收”(GC) 策略。 它要求至少始终存在一个最新的恢复点，以防所有后续备份由于 VM 中出现问题而失败。 正常情况下，在恢复点过期后，将在最多 24 小时内进行清理。 在极少数情况下，可能会根据垃圾回收器 (GC) 上较重的负载产生一个或两个额外的快照。

### <a name="why-do-i-see-more-snapshots-than-my-retention-policy"></a>我为什么会看到比保留策略更多的快照？

在将保留策略设置为“1”的场景中，可以找到两个快照。 这要求至少始终存在一个最新的恢复点，以防所有后续备份由于 VM 中出现问题而失败。 这可能导致存在两个快照。<br></br>因此，如果策略设置为“n”个快照，那么有时可能会发现“n+1”个快照。 此外，如果垃圾回收延迟，甚至还可以找到“n+1+2”个快照。 在以下情况下，可能偶尔会发生这种情况：
- 清理快照，这些快照的保留期已过。
- 后端中的垃圾回收器 (GC) 负载过重。

### <a name="i-dont-need-instant-restore-functionality-can-it-be-disabled"></a>我不需要即时还原功能。 是否可以禁用它？

为所有人启用了即时还原功能，不能禁用它。 可以将快照保留期缩短到最少一天。

### <a name="is-it-safe-to-restart-the-vm-during-the-transfer-process-which-can-take-many-hours-will-restarting-the-vm-interrupt-or-slow-down-the-transfer"></a>在传输过程中重启 VM 是否安全（传输过程可能需要数小时）？ 重启 VM 是否会中断传输或减慢传输速度？

安全，并且对数据传输速度绝对不会有任何影响。

