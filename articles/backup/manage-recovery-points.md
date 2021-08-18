---
title: 管理恢复点
description: 了解 Azure 备份服务如何管理虚拟机的恢复点
ms.topic: conceptual
ms.date: 06/17/2021
ms.openlocfilehash: 2dd33b430d5aa117f35f74ff92bb3655a9002803
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "112300662"
---
# <a name="manage-recovery-points"></a>管理恢复点

本文介绍虚拟机保留功能的工作原理。 备份时会创建恢复点，可从恢复点执行还原操作。

对于虚拟机，初始备份是完整备份，后续备份是增量备份。

## <a name="recovery-points-and-retention"></a>恢复点和保留

### <a name="scheduled-initial-and-incremental-backup"></a>计划的初始备份和增量备份

接下来简单举例介绍虚拟机 V1，其数据磁盘由 4 个块组成：块 1、块 2、块 3 和块 4。 每个块的大小均为 16 KB。

![具有 4 个块的虚拟机](./media/manage-recovery-points/four-blocks.png)

**步骤 1 - 初始备份：** 初始备份是一个完整备份。 它充当应用后续增量备份的基线。 假设有数据写入源虚拟机 (VM) 上的块 1 和块 2。 该数据将作为 D1 和 D2 复制到恢复服务保管库存储中。

![初始备份已复制](./media/manage-recovery-points/initial-backup.png)

**步骤 2 - 增量备份 1：** 假设有新数据添加到了 VM 的块 3。 该数据将在下次增量备份中被复制，只有更改的块存储为 D3。  在每个步骤中，即使 1 KB 的块发生更改，都会将整个 16 KB 的块上传至恢复点。

![第一次增量备份](./media/manage-recovery-points/first-incremental-backup.png)

**步骤 3 - 增量备份 2：** 现在假设源 VM 上的块 3 和块 2 有数据更改。 这些更改将在下次增量备份中复制为 D3' 和 D2'。

![第二次增量备份](./media/manage-recovery-points/second-incremental-backup.png)

### <a name="on-demand-backup"></a>按需备份

为 VM 设置保护后，可随时选择为该 VM 运行按需备份。

- 如果按需备份在计划的第一次初始备份之前触发，则它是完整备份。
- 如果按需备份在初始备份完成后触发，则它是增量备份。
- 为按需备份创建的恢复点的保留时间是你在触发备份时指定的保留期值。

### <a name="storage-cost"></a>存储成本

为初始备份创建的恢复点包含具有数据的所有块。 后续增量恢复点仅包含数据发生更改的块。 存储成本与所有恢复点中的所有块的总和相对应。

接下来使用上述示例来了解每个步骤后的存储成本：

|步骤  |备份类型  |已更改的块  |存储类型 |
|------|---------|---------|---------|
|1     |     初始备份    | 块 1、块 2        |    对应于恢复点 1(D1+D2)     |
|2     |  增量备份 1       |  块 3       |   对应于恢复点 1(D1+D2) + 恢复点 2(D3)      |
|3     |    增量备份 2     |    块 2、块 3     |   对应于恢复点 1(D1+D2) + 恢复点 2(D3) + 恢复点 3(D2’+D3’)      |

### <a name="recovery-point-expiration"></a>恢复点有效期

如备份策略中指定的，每个恢复点都有保留期。 系统会定期清理，所有过期的恢复点都会清理。

恢复点过期后，将会被删除或合并。

### <a name="case-1-initial-recovery-point-expires"></a>情况 1：初始恢复点过期

初始恢复点过期时，它将与下一个增量恢复点合并。 增量恢复点中覆盖的所有数据块都将被删除，其余的数据块将会合并。 然后，增量备份成为初始完整备份。 我们来看一个示例：

- 初始备份期间创建的恢复点 1 包含 VM 的完整备份。
- 如果恢复点 1 过期，那么恢复点 2 为下一个完整备份 。
- 块 D1 与恢复点 2 合并，D2 将被删除，原因是块 2 中的数据在恢复点 2 中被覆盖 。 此更改被捕获为块 D2'。
- 块 D1 在连续恢复点中按保留原样，直到在下一次备份之前对它进行任何更改为止。

![第一种情况](./media/manage-recovery-points/first-case.png)

### <a name="case-2-in-between-incremental-recovery-point-expires"></a>情况 2：中间的增量恢复点过期

- 如果恢复点 2 在恢复点 1 之前过期，则恢复点 2 中的数据与下一个可用恢复点（恢复点 3）合并   。 因此，块 D3 与恢复点 3 合并。
- 恢复点 1 仍是包含块 D1 和 D2 的完整备份。

![第二种情况](./media/manage-recovery-points/second-case.png)

### <a name="case-3-on-demand-recovery-point-expires"></a>事例 3：按需恢复点过期

在此示例中，计划（每日备份）策略按计划运行，保留期为 n 天。  如果按需备份在计划的下一次备份之前的第 4 天触发，并且其保留期指定为 10 天，则它仍将是增量备份。 恢复点（按需 RP1）将在恢复点 3 之后且恢复点 4 之前创建  。  第 14 天结束时，按需恢复点（按需 RP1）过期，并将与下一个可用恢复点合并。 服务器上仍存在的数据块会合并，而已更改（覆盖或删除）的数据块将从过期的恢复点中删除。

![第三种情况](./media/manage-recovery-points/third-case.png)

### <a name="impact-of-policy-change-on-recovery-points"></a>策略更改对恢复点的影响

修改策略时，该策略将同时应用于新恢复点和现有恢复点。 有关详细信息，请参阅[策略更改对恢复点的影响](backup-architecture.md#impact-of-policy-change-on-recovery-points)。

### <a name="impact-of-stop-protection-on-recovery-points"></a>停止保护对恢复点的影响

可通过两种方法来停止保护 VM：

- **停止保护并删除备份数据。** 此选项将使所有将来的备份作业停止保护你的 VM 并删除所有恢复点。 如果启用了[软删除](backup-azure-security-feature-cloud.md)，则已删除的数据将保留 14 天。 处于软删除状态的项不会产生费用。 数据可在 14 天内取消删除。 如果未启用软删除，数据将立即被清理，无法还原 VM，也无法使用“恢复备份”选项。
- **停止保护并保留备份数据。** 此选项会停止将来所有备份作业对 VM 的保护。 但是，Azure 备份服务将永久保留已备份的恢复点。 你需要付费才能将恢复点保留在保管库中（有关详细信息，请参阅 [Azure 备份定价](https://azure.microsoft.com/pricing/details/backup/)）。 如果需要，你将能够还原 VM。 如果你决定恢复 VM 保护，可使用“恢复备份”选项。 恢复备份后，保留规则将应用于过期点。 还可使用“删除备份数据”选项来删除已备份的数据。

## <a name="impact-of-deleting-a-vm-without-stop-protection"></a>在不停止保护的情况下删除 VM 的影响

在不停止保护的情况下删除 VM 会影响恢复点，此方案不可取。 理想情况是应在删除虚拟机之前停止备份。 因为该资源不存在，所以计划的备份会失败并显示 [VMNotFoundV2 错误](backup-azure-vms-troubleshoot.md#320001-resourcenotfound---could-not-perform-the-operation-as-vm-no-longer-exists--400094-bcmv2vmnotfound---the-virtual-machine-doesnt-exist--an-azure-virtual-machine-wasnt-found)。 恢复点将根据保留策略定期清理，但虚拟机的最后一个副本将永久保留，并将相应地进行计费。 根据你的方案，有以下两个选项：

- 选项 1：使用任何恢复点还原 VM。 如果要恢复已删除的 VM，请使用相同的名称并在相同的资源组中进行还原。 如果将已还原的 VM 保护到相同的保管库，则现有恢复点会自动附加。
- 选项 2：转到恢复服务保管库，停止对删除数据的保护。

### <a name="impact-of-expired-recovery-points-for-items-in-soft-deleted-state"></a>处于软删除状态的项的恢复点过期的影响

如果对恢复服务保管库启用了[软删除](backup-azure-security-feature-cloud.md)，则过期的恢复点将保持软删除状态并且不会被清理。 当恢复点处于软删除状态时，不会产生任何费用。

### <a name="impact-of-churn-on-backup-performance"></a>变动率对备份性能的影响

假设 VM 的总存储是 8 TB，变动率为 5%。 则相应的增量备份存储将为 8 TB 的 5%，即 0.4 TB。 变动率越高，后续增量备份的后端存储也就越高。 变动率会影响备份性能。 变动率越高，备份过程越慢，后端存储的消耗也就越大。

若要了解变动率如何影响备份性能，请查看以下场景：

|虚拟机  |VM1  |VM2  |VM3  |
|---------|---------|---------|---------|
|数据磁盘数    | 4（A1、A2、A3、A4）        | 4（B1、B2、B3、B4）        |  4（C1、C2、C3、C4）       |
|每个磁盘的大小   |      4 TB   | 4 TB        |  4 TB       |
|备份数据变动量    |   A1- 4 TB      | B1-1 TB；B2-1 TB <br> B3-1 TB；B4-1 TB  |   C1-2 TB；C4-2 TB      |

备份性能的顺序将为 VM2>VM3>VM1。 这是因为变动的数据分布在不同磁盘上。 由于磁盘的备份会并行进行，VM2 将表现出最佳性能。

## <a name="frequently-asked-question"></a>常见问题解答

### <a name="how-can-i-find-the-retention-period-of-an-on-demand-backup"></a>如何找到按需备份的保留期？

按需备份的备份作业中的“恢复点到期时间(UTC)”字段显示恢复点的保留期。 若要了解详细信息，请参阅[运行按需备份](backup-azure-manage-vms.md#run-an-on-demand-backup)。 

## <a name="next-steps"></a>后续步骤

- [Azure 备份体系结构和组件](backup-architecture.md)
