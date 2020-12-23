---
title: 常见问题解答 - 备份 Azure VM 上的 SAP HANA 数据库
description: 本文解答有关使用 Azure 备份服务备份 SAP HANA 数据库的常见问题。
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: bf662600bafcd18b00c8f8d3b673fc3f9c110aca
ms.sourcegitcommit: 1d366d72357db47feaea20c54004dc4467391364
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/23/2020
ms.locfileid: "95400201"
---
# <a name="frequently-asked-questions--back-up-sap-hana-databases-on-azure-vms"></a>常见问题解答 - 备份 Azure VM 上的 SAP HANA 数据库

本文解答有关使用 Azure 备份服务备份 SAP HANA 数据库的常见问题。

## <a name="backup"></a>Backup

### <a name="how-many-full-backups-are-supported-per-day"></a>每天支持多少次完整备份？

每天仅支持一次完整备份。 不能在同一天触发差异备份和完整备份。

### <a name="do-successful-backup-jobs-create-alerts"></a>成功的备份作业是否会创建警报？

否。 成功的备份作业不会生成警报。 仅针对失败的备份作业发送警报。 [此文](./backup-azure-monitoring-built-in-monitor.md)介绍了门户警报的详细行为。 但是，如果希望在作业成功的情况下也收到警报，可以使用 [Azure Monitor](./backup-azure-monitoring-use-azuremonitor.md)。

### <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>“备份作业”菜单中是否会显示计划的备份作业？

“备份作业”菜单只显示按需备份作业。 对于计划的作业，请使用 [Azure Monitor](./backup-azure-monitoring-use-azuremonitor.md)。

### <a name="are-future-databases-automatically-added-for-backup"></a>未来的数据库会自动添加备份吗？

否，目前不支持该功能。

### <a name="if-i-delete-a-database-from-an-instance-what-will-happen-to-the-backups"></a>如果从实例中删除数据库，备份会发生什么情况？

如果从 SAP HANA 实例中删除某个数据库，仍会尝试数据库备份。 这意味着，已删除的数据库会在“备份项”下显示为不正常状态，但仍受保护。
停止保护此数据库的正确方法是针对此数据库执行“停止备份并删除数据”操作。

### <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-the-behavior-be"></a>如果在保护数据库后更改其名称，会出现怎样的行为？

已重命名的数据库被视为新数据库。 因此，该服务将处理此情况，就好像找不到数据库，备份会失败。 已重命名的数据库会显示为新数据库，必须对其进行保护性配置。

### <a name="what-are-the-prerequisites-to-back-up-sap-hana-databases-on-an-azure-vm"></a>备份 Azure VM 上的 SAP HANA 数据库的先决条件是什么？

请参阅[先决条件](tutorial-backup-sap-hana-db.md#prerequisites)和[预注册脚本的功能](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)部分。

### <a name="what-permissions-should-be-set-so-azure-can-back-up-sap-hana-databases"></a>应设置哪些权限以便 Azure 可以备份 SAP HANA 数据库？

运行预注册脚本即可设置所需的权限，这样 Azure 就可以备份 SAP HANA 数据库。 可在 [此处](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)找到有关预注册脚本用途的详细信息。

### <a name="will-backups-work-after-migrating-sap-hana-from-sdc-to-mdc"></a>将 SAP HANA 从 SDC 迁移到 MDC 后，备份是否正常进行？

请参考故障排除指南中的[说明](./backup-azure-sap-hana-database-troubleshoot.md#sdc-to-mdc-upgrade-with-a-change-in-sid)。

### <a name="what-should-be-done-while-upgrading-within-the-same-version"></a>在同一版本中升级时应执行的操作

请参阅故障排除指南中的 [此部分](backup-azure-sap-hana-database-troubleshoot.md#sdc-version-upgrade-or-mdc-version-upgrade-on-the-same-vm) 。

### <a name="can-azure-hana-backup-be-set-up-against-a-virtual-ip-load-balancer-and-not-a-virtual-machine"></a>是否可以针对虚拟 IP（负载均衡器）而不是虚拟机设置 Azure HANA 备份？

目前，我们无法设置单独针对虚拟 IP 的解决方案。 执行此解决方案需要使用虚拟机。

### <a name="how-can-i-move-an-on-demand-backup-to-the-local-file-system-instead-of-the-azure-vault"></a>如何将按需备份移动到本地文件系统而不是 Azure 保管库？

1. 等待当前正在运行的备份在目标数据库上完成（可从工作室检查是否已完成）。
1. 按照以下步骤禁用日志备份并将目录备份设置为所需 DB 的文件系统：
1. 双击“SYSTEMDB” -> “配置” -> “选择数据库” -> “筛选器(日志)”   。
    1. 将 enable_auto_log_backup 设置为 " **否**"。
    1. 将 catalog_backup_using_backint 设置为 **false**。
1. 在所需的数据库上执行按需备份（完整/差异/增量），并等待备份和目录备份完成。
1. 如果还想要将日志备份移动到文件系统，请将 enable_auto_log_backup 设置为 **"是"**。
1. 恢复到以前的设置，以允许备份流向 Azure 保管库：
    1. 将 enable_auto_log_backup 设置为 **"是"**。
    1. 将 catalog_backup_using_backint 设置为 **true**。

>[!NOTE]
>如果将备份移动到本地文件系统并再次切换回 Azure 保管库，则可能会导致保管库中日志备份的日志链中断。 这会触发完整备份，成功完成后，将开始备份日志。

### <a name="how-can-i-use-sap-hana-backup-with-my-hana-replication-set-up"></a>如何在设置 HANA 复制的情况下使用 SAP HANA 备份？

目前，Azure 备份不能理解 HSR 设置。 这意味着，HSR 的主节点和辅助节点将被视为两个不相关的独立 VM。 你首先需要在主节点上配置备份。 发生故障转移时，必须在辅助节点（现在变为主节点）上配置备份。 不会自动将备份故障转移到另一个节点。

若要在任意给定时间点备份 active (主) 节点中的数据，可以将 **保护切换** 到辅助节点，该节点现在会成为故障转移后的主节点。

若要执行此切换保护操作，请完成以下步骤：

- 在主节点上[停止保护](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database)（使用保留数据）
- 在辅助节点上运行[预注册脚本](https://aka.ms/scriptforpermsonhana)
- 在辅助节点上[发现数据库](tutorial-backup-sap-hana-db.md#discover-the-databases)并在其上[配置备份](tutorial-backup-sap-hana-db.md#configure-backup)

必须在每次故障转移后手动执行这些步骤。 除了 Azure 门户之外，还可以通过命令行/HTTP REST 执行这些步骤。 若要自动执行这些步骤，可以使用 Azure runbook。

以下详细示例介绍切换保护必须如何进行执行：

在本例中，HSR 设置中有两个节点 - 节点 1（主节点）和节点 2（辅助节点）。  对节点 1 配置备份。 如上所述，请不要尝试对节点 2 配置备份。

发生首次故障转移时，节点 2 将变为主节点。 那么：

1. 使用“保留数据”选项停止对节点 1（以前的主节点）的保护。
1. 在节点 2（现在是主节点）上运行预注册脚本。
1. 在节点 2 上发现数据库，分配备份策略并配置备份。

然后，在节点 2 上触发第一次完整备份，并在该备份完成后启动日志备份。

发生下一次故障转移时，节点 1 再次成为主节点，节点 2 变为辅助节点。 现在，重复此过程：

1. 使用“保留数据”选项停止对节点 2 的保护。
1. 在节点 1（已再次成为主节点）上运行预注册脚本
1. 然后使用所需的策略在节点 1 上[恢复备份](sap-hana-db-manage.md#resume-protection-for-an-sap-hana-database)（因为备份之前已在节点 1 上停止）。

然后，在节点 1 上再次触发完整备份，并在该备份完成后启动日志备份。

## <a name="restore"></a>还原

### <a name="why-cant-i-see-the-hana-system-i-want-my-database-to-be-restored-to"></a>为什么我看不到要将数据库还原到的 HANA 系统？

检查是否满足还原到目标 SAP HANA 实例所需的所有先决条件。 有关详细信息，请参阅[先决条件 - 还原 Azure VM 中的 SAP HANA 数据库](./sap-hana-db-restore.md#prerequisites)。

### <a name="why-is-the-overwrite-db-restore-failing-for-my-database"></a>为什么我的数据库的“覆盖 DB”还原会失败？

请确保在还原时选中“强制覆盖”选项。

### <a name="why-do-i-see-the-source-and-target-systems-for-restore-are-incompatible-error"></a>为什么会出现“还原的源系统和目标系统不兼容”错误？

请参阅 SAP HANA 说明 [1642148](https://launchpad.support.sap.com/#/notes/1642148)，了解目前支持的还原类型。

### <a name="can-i-use-a-backup-of-a-database-running-on-sles-to-restore-to-an-rhel-hana-system-or-vice-versa"></a>是否可以使用在 SLES 上运行的数据库的备份还原到 RHEL HANA 系统，或者反向操作？

是的，可以使用在 SLES 上运行的 HANA 数据库上触发的流备份将其还原到 RHEL HANA 系统，反之亦然。 也就是说，可以使用流备份进行跨操作系统还原。 但是，必须确保要还原到的 HANA 系统以及用于还原的 HANA 系统都是兼容的，以便按照 SAP 的要求进行还原。 请参阅 SAP HANA 说明 [1642148](https://launchpad.support.sap.com/#/notes/1642148)，了解兼容的还原类型。

## <a name="policy"></a>策略

### <a name="different-options-available-during-creation-of-a-new-policy-for-sap-hana-backup"></a>为 SAP HANA 备份创建新策略时可用的不同选项

在创建策略之前，您应该清楚 RPO 和 RTO 的要求及其相关成本的影响。

RPO (恢复点目标) 指示用户/客户可接受的数据丢失量。 这取决于日志备份频率。 更频繁的日志备份表明 RPO 较低，Azure 备份服务支持的最小值为15分钟。 日志备份频率为15分钟或更长时间。

RTO (恢复时间目标) 指示在数据丢失方案后，应将数据还原到上一个可用时间点的速度。 这取决于 HANA 使用的恢复策略，这通常取决于还原所需的文件数。 这也会影响成本，下表应有助于理解所有方案及其含义。

|备份策略  |RTO  |Cost  |
|---------|---------|---------|
|每日完整 + 日志     |   最快，因为我们只需要一个完整副本 + 所需日志来执行时间点还原      |    Costliest 选项，因为每日都要创建一个完整的副本，因此在保留时间   |
|每周完整 + 每日差异 + 日志     |   比上述选项慢，但比下一选项更快，因为我们需要一个完整副本 + 一个差异副本 + 日志来执行时间点还原      |    成本较低的选项，因为每日差异通常小于 full，每周一次只创建一次完整副本      |
|每周完整 + 每日增量 + 日志     |  由于我们需要一个完整副本 + "n" 个增量 + 日志来执行时间点恢复，因此速度最慢       |     成本最低的选项，因为每日增量将小于差异，并且仅每周创建一个完整副本    |

> [!NOTE]
> 以上选项是最常见的选项，但并不是唯一的选项。 例如，您可以每周执行一次完整备份 + 差异两次，每周 + 日志。

因此，可以根据 RPO 和 RTO 目标以及成本注意事项来选择策略变体。

### <a name="impact-of-modifying-a-policy"></a>修改策略的影响

确定从策略 1 (P1) 切换到策略 2 (P2) 或编辑策略 1 (P1) 所造成的影响时，应牢记几个原则。

- 还会以追溯方式应用所有更改。 最新的备份策略也适用于先前创建的恢复点。 例如，假设每日完整保留期为30天，并且根据当前活动的策略拍摄了10个恢复点。 如果每日完整保留期更改为10天，则上一个点的到期时间也会重新计算为开始时间 + 10 天，如果过期时间已过，则将其删除。
- 更改范围还包括备份日期、备份类型和保留期。 例如：如果策略从每日完整备份更改为每周完全在星期日，则不在星期日的所有早期版本都将标记为删除。
- 在子节点处于活动/未过期状态之前，不会将其删除。 每个备份类型都有一个过期时间，这取决于当前活动策略。 但会将完整备份类型视为父对象，即 "差异"、"增量" 和 "日志"。 "差异" 和 "日志" 对其他任何人都不是父级。 "增量" 可以是后面的 "增量" 的父级。 即使 "父" 被标记为删除，如果子 "差异" 或 "日志" 未过期，实际上不会将其删除。 例如，如果策略在星期日上从每日完整到每周均已满，则不在星期日的所有以前的完全备份将标记为删除。 但在之前每天拍摄的日志过期之前，不会实际删除它们。 换句话说，根据最新的日志持续时间保留它们。 日志过期后，日志和这些完整备份都将被删除。

使用这些原则，可以阅读下表，了解策略更改的含义。

|旧策略/新策略  |每日完整备份 + 日志  | 每周完整备份 + 每日差异 + 日志  |每周完整备份 + 每日增量备份 + 日志  |
|---------|---------|---------|---------|
|每日完整备份 + 日志     |   -      |    不在一周中同一天的上一个完整备份将标记为删除，但会一直保留到日志保持期     |    不在一周中同一天的上一个完整备份将标记为删除，但会一直保留到日志保持期     |
|每周完整备份 + 每日差异 + 日志     |   根据最新策略重新计算上一个每周的完整保留。 将立即删除以前的差异      |    -     |    将立即删除以前的差异     |
|每周完整备份 + 每日增量备份 + 日志     |     根据最新策略重新计算上一个每周的完整保留。 将立即删除以前的增量备份    |     将立即删除以前的增量备份    |    -     |

## <a name="next-steps"></a>后续步骤

了解如何[备份 SAP HANA 数据库](./backup-azure-sap-hana-database.md)（在 Azure VM 上运行）。
