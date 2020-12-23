---
title: 管理恢复点
description: 了解 Azure 备份服务如何管理虚拟机的恢复点
ms.topic: conceptual
ms.date: 11/08/2020
ms.openlocfilehash: 256df693aba0f799c24bcba6defe846e5c37ccaa
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2020
ms.locfileid: "94428572"
---
# <a name="manage-recovery-points"></a>管理恢复点

本文介绍虚拟机的保留期如何工作。 发生备份时，将创建恢复点，从中可以执行还原操作。

对于虚拟机，初始备份是完整备份，后续备份是增量备份。

## <a name="recovery-points-and-retention"></a>恢复点和保留期

### <a name="scheduled-initial-and-incremental-backup"></a>计划的初始备份和增量备份

让我们以一个包含四个块的数据磁盘作为虚拟机 *V1* 的简化示例：块1、块2、块3和块4。 每个块的大小为 16 KB。

![具有四个块的虚拟机](./media/manage-recovery-points/four-blocks.png)

**步骤 1-初始备份：** 初始备份是完整备份。 它充当用于应用后续增量备份的基准。 假设源 VM 上有写入块1和块2的数据。 相同的数据将作为 D1 和 D2 复制到恢复服务保管库存储中。

![复制初始备份](./media/manage-recovery-points/initial-backup.png)

**步骤 2-增量备份1：** 请考虑将新数据添加到了 VM 的块3。 将在下一次增量备份中复制相同的数据，并且仅更改的块存储为 D3。  在每个步骤中，即使块发生更改，也会在恢复点中上传整个 16 KB 块。

![第一次增量备份](./media/manage-recovery-points/first-incremental-backup.png)

**步骤 3-增量备份2：**  现在，请考虑源 VM 上块3和块2上的数据更改。 这些更改将在下一次增量备份中复制为 D3 ' 和 D2 '。

![第二次增量备份](./media/manage-recovery-points/second-incremental-backup.png)

### <a name="on-demand-backup"></a>按需备份

你可以选择在设置保护后，随时运行 VM 的按需备份。

- 如果在首次计划的初始备份之前触发，则按需备份将是完整备份。
- 如果初始备份已完成，并且触发了按需备份，则它是增量备份。
- 为按需备份创建的恢复点的保留时间是在触发备份时指定的保留值。

### <a name="storage-cost"></a>存储成本

为初始备份创建的 **恢复点** 包含包含数据的所有块。 后续增量恢复点仅包含已更改数据的块。 存储成本与所有恢复点上跨越的所有块的总和相对应。

让我们使用上面的示例来了解每个步骤后的存储成本：

|步骤  |备份类型  |已更改块  |存储类型 |
|------|---------|---------|---------|
|1     |     初始备份    | 块1、块2        |    对应于恢复点 1 (D1 + D2)      |
|2     |  增量备份1       |  块3       |   对应于恢复点 1 (D1 + D2) + 恢复点 2 (D3)       |
|3     |    增量备份2     |    块2、块3     |   对应于恢复点 1 (D1 + D2) + 恢复点 2 (D3) + 恢复点 3 (D2 "+ D3" )       |

### <a name="recovery-point-expiration"></a>恢复点过期时间

每个恢复点都具有在备份策略中指定的保留期。 清除会定期发生，并清除已过期的所有恢复点。

恢复点过期后，将被删除或合并。

### <a name="case-1-initial-recovery-point-expires"></a>案例1：初始恢复点过期

初始恢复点过期时，它将与下一个增量恢复点合并。 将删除增量恢复点中覆盖的所有数据块，并合并其余的数据块。 增量备份将成为初始完整备份。 让我们看一个示例：

- 首次备份期间创建的 *恢复点 1* 具有 VM 的完整备份。
- 当 *恢复点 1* 过期时， *恢复点 2* 是下一次完整备份。
- 块 D1 将与 *恢复点 2* 合并，并已删除 D2，因为在 *恢复点 2* 中覆盖了块2中的数据。 此更改被捕获为块 D2 "。
- 块 D1 在连续恢复点中保留原样，直到下一次备份之前对它进行了任何更改。

![第一种情况](./media/manage-recovery-points/first-case.png)

### <a name="case-2-in-between-incremental-recovery-point-expires"></a>案例2：增量恢复点之间的间隔过期

- 如果恢复点 *2* 在 *恢复点 1* 之前过期，则 *恢复点 2* 中的数据将与下一个可用恢复点合并： *恢复点 3* 。 因此块 D3 与 *恢复点 3* 合并。
- *恢复点 1* 仍是带有块 D1 和 D2 的完整备份。

![第二种情况](./media/manage-recovery-points/second-case.png)

### <a name="case-3-on-demand-recovery-point-expires"></a>案例3：按需恢复点过期

在此示例中，计划 (每日备份) 策略按 *n* 天保留期运行。  如果按需备份在下一次计划的备份之前的第四天触发，并且其保留期指定为10天，则仍将是增量备份。 恢复点 (按 *需 RP1* ) 将在恢复点 *3* 之后以及 *恢复点 4* 之前创建。  第14天结束时，按需恢复点 (按 *需 RP1* ) 过期，并将与下一个可用恢复点合并。 服务器上仍存在的数据块会合并，而更改 (覆盖或删除的数据块将从过期的恢复点中删除) 。

![第三种情况](./media/manage-recovery-points/third-case.png)

### <a name="impact-of-policy-change-on-recovery-points"></a>策略更改对恢复点的影响

修改策略时，该策略将应用于新的和现有的恢复点。 有关详细信息，请参阅对 [恢复点的策略更改的影响](backup-architecture.md#impact-of-policy-change-on-recovery-points)。

### <a name="impact-of-stop-protection-on-recovery-points"></a>停止保护对恢复点的影响

可通过两种方法来停止保护 VM：

- **停止保护并删除备份数据。** 此选项将停止所有将来的备份作业来保护 VM，并删除所有恢复点。 如果启用 [软删除](backup-azure-security-feature-cloud.md) ，则已删除的数据将保留14天。 对于软删除状态的项不会产生费用。 数据可在14天内撤消删除。 如果未启用软删除，数据将立即被清理，无法还原 VM，也无法使用 " **恢复备份** " 选项。
- **停止保护并保留备份数据。** 此选项会停止将来所有备份作业对 VM 的保护。 但是，Azure 备份服务会永久保留已备份的恢复点。 你需要付费才能将恢复点保留在保管库中（有关详细信息，请参阅 [Azure 备份定价](https://azure.microsoft.com/pricing/details/backup/)）。 如果需要，你将能够还原 VM。 如果决定恢复 VM 保护，则可以使用 " **恢复备份** " 选项。 恢复备份后，保留规则将应用到过期点。 你还可以使用 "  **删除备份数据** " 选项删除已备份的数据。

## <a name="impact-of-deleting-a-vm-without-stop-protection"></a>删除 VM 的影响而不停止保护

删除不带停止保护的 VM 会影响恢复点，这是不必要的方案。 删除虚拟机之前应停止理想的备份。 因为该资源不存在，所以计划的备份会失败，并出现 [VMNotFoundV2 错误](backup-azure-vms-troubleshoot.md#320001-resourcenotfound---could-not-perform-the-operation-as-vm-no-longer-exists--400094-bcmv2vmnotfound---the-virtual-machine-doesnt-exist--an-azure-virtual-machine-wasnt-found)。 将根据保留策略定期清理恢复点，但虚拟机的最后副本将保持不变，并将相应地进行计费。 根据你的方案，你可以使用以下两个选项：

- **选项1：** 使用任何恢复点还原 VM。 如果要恢复已删除的 VM，请使用相同的名称和相同的资源组还原。 如果将已还原的 VM 保护到相同的保管库，则会自动附加现有恢复点。
- **选项2：** 请参阅恢复服务保管库，停止保护并删除数据。

### <a name="impact-of-expired-recovery-points-for-items-in-soft-deleted-state"></a>处于软删除状态的项的过期恢复点影响

如果对恢复服务保管库启用 [软删除](backup-azure-security-feature-cloud.md) ，则过期恢复点将保持软删除状态，并且不会清除。 当恢复点处于软删除状态时，不会产生任何费用。

### <a name="impact-of-churn-on-backup-performance"></a>改动对备份性能的影响

假设 VM 的总存储是 8 TB，而改动量为5%。 然后，相应的增量备份存储将是 8 TB 的5%，即 0.4 TB。 较高的变动对应于后续增量备份的更高后端存储。 改动会影响备份性能。 改动越多，备份过程越慢，并且更大的后端存储消耗。

若要了解改动如何影响备份性能，请查看以下方案：

|虚拟机  |VM1  |VM2  |VM3  |
|---------|---------|---------|---------|
|数据磁盘数    | 4 (A1、A2、A3、A4)         | 4 (B1，B2，B3，B4)         |  4 (C1、C2、C3、C4)        |
|每个磁盘的大小   |      4 TB   | 4 TB        |  4 TB       |
|备份数据改动    |   A1-4 TB      | B1-1 TB;B2-1 TB <br> B3-1 TB;B4-1 TB  |   C1-2 TB;C4-2 TB      |

备份性能将按顺序 VM2>VM3>VM1。 这是因为改动数据分布在不同的磁盘上。 由于备份磁盘会并行发生，VM2 将显示最佳性能。

## <a name="next-steps"></a>后续步骤

- [Azure 备份体系结构和组件](backup-architecture.md)
