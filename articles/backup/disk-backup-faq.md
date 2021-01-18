---
title: 有关 Azure 磁盘备份的常见问题
description: 获取有关 Azure 磁盘备份的常见问题的解答
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: 4c4c9f4b8388fed95a19c49b705981b9b9bce2e0
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/18/2021
ms.locfileid: "98557344"
---
# <a name="frequently-asked-questions-about-azure-disk-backup-in-preview"></a>预览版中的 Azure 磁盘备份 (常见问题解答) 

>[!IMPORTANT]
>Azure 磁盘备份没有服务级别协议，不建议用于生产工作负荷。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 有关区域可用性，请参阅 [支持矩阵](disk-backup-support-matrix.md)。
>
>[填写此窗体](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR1vE8L51DIpDmziRt_893LVUNFlEWFJBN09PTDhEMjVHS05UWFkxUlUzUS4u) 以便注册预览版。

本文解答了有关 Azure 磁盘备份的常见问题。 有关 [Azure 磁盘备份](disk-backup-overview.md) 区域可用性、支持的方案和限制的详细信息，请参阅 [支持矩阵](disk-backup-support-matrix.md)。

## <a name="frequently-asked-questions"></a>常见问题

### <a name="can-i-back-up-the-disk-using-the-azure-disk-backup-solution-if-the-same-disk-is-backed-up-using-azure-virtual-machine-backup"></a>如果使用 Azure 虚拟机备份来备份相同磁盘，是否可以使用 Azure 磁盘备份解决方案备份磁盘？

Azure 备份提供对使用磁盘备份和 [AZURE VM 备份](backup-azure-vms-introduction.md) 解决方案备份托管磁盘的并行支持。 当你需要一天的虚拟机的应用程序一致性备份，还需要更频繁地备份 OS 磁盘或特定的数据磁盘时，这会很有用，而不会影响生产应用程序的性能。

### <a name="how-do-i-find-the-snapshot-resource-group-that-i-used-to-configure-backup-for-a-disk"></a>如何实现查找用于配置磁盘备份的快照资源组吗？

在 " **备份实例** " 屏幕中，可以找到 " **概要** " 部分中的 "快照资源组" 字段。 你可以从备份中心或备份保管库中搜索和选择相应磁盘的备份实例。

![快照资源组字段](./media/disk-backup-faq/snapshot-resource-group.png)

### <a name="what-is-a-snapshot-resource-group"></a>什么是快照资源组？

Azure 磁盘备份为托管磁盘提供操作层备份。 也就是说，在计划和按需备份操作过程中创建的快照存储在订阅内的资源组中。 Azure 备份提供即时还原，因为增量快照存储在你的订阅中。 此资源组称为快照资源组。 有关详细信息，请参阅 [配置备份](backup-managed-disks.md#configure-backup)。

### <a name="why-must-the-snapshot-resource-group-be-in-same-subscription-as-that-of-the-disk-being-backed-up"></a>为什么快照资源组必须与要备份的磁盘具有相同的订阅？

不能为该磁盘的订阅之外的特定磁盘创建增量快照。 因此，请选择与要备份的磁盘相同的订阅中的资源组。 详细了解托管磁盘的 [增量快照](https://docs.microsoft.com/azure/virtual-machines/windows/disks-incremental-snapshots-portal#restrictions) 。

### <a name="why-do-i-need-to-provide-role-assignments-to-be-able-to-configure-backups-perform-scheduled-and-on-demand-backups-and-restore-operations"></a>为什么需要提供角色分配才能配置备份、执行计划备份和按需备份以及还原操作？

Azure 磁盘备份使用最低权限方法来发现、保护和还原订阅中的托管磁盘。 为实现此目的，Azure 备份使用 [备份保管库](backup-vault-overview.md) 的托管标识来访问其他 Azure 资源。 系统分配的托管标识限制为每个资源一个，并绑定到此资源的生命周期。 可以使用 Azure RBAC)  (基于角色的访问控制向托管标识授予权限。 托管标识是特定类型的服务主体，只能与 Azure 资源一起使用。 详细了解[托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)。 默认情况下，备份保管库无权访问要备份的磁盘、创建定期快照、在保持期后删除快照以及从备份还原磁盘。 通过向备份保管库的托管标识显式授予角色分配，你可以控制对订阅上资源的管理权限。

### <a name="why-does-backup-policy-limit-the-retention-duration"></a>为什么备份策略会限制保留期？

Azure 磁盘备份使用增量快照，每个磁盘的快照数限制为200。 为了允许除计划备份外的按需备份，备份策略会将备份总数限制为180。 详细了解托管磁盘的 [增量快照](https://docs.microsoft.com/azure/virtual-machines/windows/disks-incremental-snapshots-portal#restrictions) 。

### <a name="how-does-the-hourly-and-daily-backup-frequency-work-in-the-backup-policy"></a>备份策略中的每小时和每日备份频率如何工作？

Azure 磁盘备份每天提供多次备份。 如果需要更频繁的备份，请选择 " **每小时** 备份频率"。 根据所选的 **时间** 间隔，计划备份。 例如，如果 **每隔4小时选择一** 次备份，则每隔4小时就会创建一次备份，以便在一天中平均分布备份。 如果每天备份足以完成，则选择 **每日** 备份频率。 在每天的备份频率中，可以指定备份的时间。 请注意，这一天的时间指示备份开始时间，而不是备份完成的时间。 完成备份操作所需的时间取决于各种因素，包括连续备份之间的变动率。 但是，Azure 磁盘备份是一种无代理备份，它使用不会影响生产应用程序性能的 [增量快照](https://docs.microsoft.com/azure/virtual-machines/windows/disks-incremental-snapshots-portal) 。

### <a name="why-does-the-backup-vaults-redundancy-setting-not-apply-to-the-backups-stored-in-operational-tier-the-snapshot-resource-group"></a>为什么备份保管库的冗余设置不适用于) 快照资源组 (操作层中存储的备份？

Azure 备份使用托管磁盘的 [增量快照](https://docs.microsoft.com/azure/virtual-machines/windows/disks-incremental-snapshots-portal#restrictions) ，这些快照仅存储自上一次快照到标准 HDD 存储以来的磁盘增量更改，而不考虑父磁盘的存储类型。 为了获得更高的可靠性，默认情况下，增量快照存储在区域冗余存储 (ZRS) 在支持 ZRS 的区域中。 目前，Azure 磁盘备份支持不将备份复制到备份保管库存储的托管磁盘的操作备份。 因此备份保管库的备份存储冗余设置不适用于恢复点。

### <a name="can-i-use-backup-center-to-configure-backups-and-manage-backup-instances-for-azure-disks"></a>是否可以使用备份中心来配置 Azure 磁盘的备份和管理备份实例？

是的，Azure 磁盘备份集成到了 [备份中心](backup-center-overview.md)，在 azure 中提供了一种 **统一的管理体验** ，使企业能够大规模控制、监视、操作和分析备份。 你还可以使用备份保管库来备份、还原和管理保管库中受保护的备份实例。

### <a name="why-do-i-need-to-create-a-backup-vault-and-not-use-a-recovery-services-vault"></a>为什么需要创建备份保管库，而不是使用恢复服务保管库？

备份保管库是 Azure 中的一个存储实体，用于承载 Azure 备份支持的某些较新工作负荷的备份数据。 你可以使用备份保管库来保存各种 Azure 服务（例如 Azure Database for PostgreSQL 服务器、Azure 磁盘和 Azure 备份将支持的较新工作负荷）的备份数据。 备份保管库可以方便地组织备份数据，同时将管理开销降至最低。 有关详细信息，请参阅 [备份保管库](https://docs.microsoft.com/azure/backup/backup-vault-overview) 。

### <a name="can-the-disk-to-be-backed-up-and-the-backup-vault-be-in-different-subscriptions"></a>是否可以备份磁盘，备份保管库是否位于不同的订阅中？

是，要备份的源托管磁盘和备份保管库可以位于不同的订阅中。

### <a name="can-the-disk-to-be-backed-up-and-the-backup-vault-be-in-different-regions"></a>是否可以备份磁盘，备份保管库是否位于不同的区域？

否，当前要备份的源托管磁盘，备份保管库必须位于同一区域。

### <a name="can-i-restore-a-disk-into-a-different-subscription"></a>是否可以将磁盘还原到不同的订阅？

是的，你可以将磁盘还原到与进行备份的源托管磁盘的订阅不同的订阅。

### <a name="can-i-back-up-multiple-disks-together"></a>是否可以同时备份多个磁盘？

否，不支持附加到虚拟机的多个磁盘的时间点快照。 有关详细信息，请参阅 [配置备份](backup-managed-disks.md#configure-backup) 并了解有关限制的详细信息，请参阅 [支持矩阵](disk-backup-support-matrix.md)。

### <a name="what-are-my-options-to-back-up-disks-across-multiple-subscriptions"></a>在多个订阅中备份磁盘的选项有哪些？

目前，使用 Azure 门户配置磁盘备份时，最多只能有20个磁盘来自同一订阅。

### <a name="what-is-a-target-resource-group"></a>什么是目标资源组？

在还原操作过程中，可以选择要将磁盘还原到的订阅和资源组。 Azure 备份将从所选资源组中的恢复点创建新磁盘。 这称为目标资源组。 请注意，备份保管库的托管标识要求目标资源组上的角色分配能够成功执行还原操作。 有关详细信息，请参阅 [还原文档](restore-managed-disks.md)。

### <a name="what-are-the-permissions-used-by-azure-backup-during-backup-and-restore-operation"></a>Azure 备份在备份和还原操作期间使用哪些权限？

下面是在要备份的 **磁盘** 上分配的 **磁盘备份读取器** 角色中使用的操作：

"Microsoft. 计算/磁盘/读取"

"BeginGetAccess/磁盘/操作"

以下是对 **快照资源组** 分配的 **磁盘快照参与者** 角色中使用的操作：

"Microsoft. 计算/快照/删除"

"Microsoft. 计算/快照/写入"

"Microsoft. 计算/快照/读取"

"StorageAccounts/write"

"StorageAccounts//read"

"StorageAccounts/删除"

"Microsoft .Resources/订阅/resourceGroups/read"

"StorageAccounts/listkeys/action"

"BeginGetAccess/快照/操作"

"EndGetAccess/快照/操作"

"BeginGetAccess/磁盘/操作"

以下是针对 **目标资源组** 分配的 **磁盘还原操作员** 角色中使用的操作：

"Microsoft 计算/磁盘/写入"

"Microsoft. 计算/磁盘/读取"

"Microsoft .Resources/订阅/resourceGroups/read"

>[!NOTE]
>基于 Azure 备份服务添加的功能，对这些角色的权限可能会发生变化。

## <a name="next-steps"></a>后续步骤

- [Azure 磁盘备份支持矩阵](disk-backup-support-matrix.md)
