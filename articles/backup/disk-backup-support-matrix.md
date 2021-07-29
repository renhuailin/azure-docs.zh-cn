---
title: Azure 磁盘备份支持矩阵
description: 汇总 Azure 磁盘备份的支持设置和限制。
ms.topic: conceptual
ms.date: 05/27/2021
ms.custom: references_regions
ms.openlocfilehash: 8b6ea48a326ec42aaf33af61b0ec1a6a07bae8fe
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2021
ms.locfileid: "111750922"
---
# <a name="azure-disk-backup-support-matrix"></a>Azure 磁盘备份支持矩阵

可使用 [Azure 备份](./backup-overview.md)保护 Azure 磁盘。 本文总结了区域可用性、支持的方案以及限制。

## <a name="supported-regions"></a>支持的区域

Azure 磁盘备份在除法国南部、南非西部之外的所有公有云区域提供，但目前不在主权云区域提供。 在这些区域中提供时，我们会公布这些区域。

## <a name="limitations"></a>限制

- Azure 托管磁盘（包括共享磁盘 [共享高级 SSD]）支持 Azure 磁盘备份。 而非托管磁盘不支持。 由于缺少快照功能，此解决方案目前不支持超级磁盘（包括共享超级磁盘）。

- Azure 磁盘备份支持备份写入加速器磁盘。 但在还原过程中，磁盘将还原为普通磁盘。 将写入加速缓存装载到 VM 后，可以在磁盘上启用该缓存。

- Azure 备份为 Azure 托管磁盘提供操作（快照）层备份，并且支持每日多次备份。 备份不会复制到备份保管库。

- 目前，不支持通过替换备份所在的现有源磁盘选择用于还原的“原始位置恢复(OLR)”选项。 可从恢复点还原，以在备份所在的源磁盘所属资源组或任何其他资源组创建一个新磁盘。 这称为“备用位置恢复(ALR)”。

- 托管磁盘的 Azure 备份使用增量快照，每个磁盘的快照限制为 200 个。 为了可以按计划进行备份，以及按需进行备份，备份策略将总备份限制为 180 个。 详细了解托管磁盘的[增量快照](../virtual-machines/disks-incremental-snapshots.md#restrictions)。

- Azure [订阅和服务限制](../azure-resource-manager/management/azure-subscription-service-limits.md#virtual-machine-disk-limits)适用于每个订阅的每个区域的磁盘快照总数。

- 不支持对附加到虚拟机的多个磁盘提取时间点快照。

- 备份保管库和要备份的磁盘可以位于同一订阅或不同订阅。 但是，备份保管库和要备份的磁盘必须位于同一区域。

- 要备份的磁盘和本地存储快照的快照资源组必须位于同一订阅中。

- 支持将磁盘从备份还原到相同或不同的订阅。 但是，还原的磁盘将在快照所在的同一区域创建。

- 无法保护 ADE 加密磁盘。

- 支持使用平台管理的密钥或客户管理的密钥加密的磁盘。 但是，如果禁用或删除了磁盘加密集 KeyVault 密钥，则使用客户管理的密钥 (CMK) 加密的磁盘的还原点将无法还原。

- 目前，备份策略无法修改，在配置磁盘备份时分配给备份实例的快照资源组也无法更改。

- 目前，配置磁盘备份的 Azure 门户体验最多只能备份来自同一订阅的 20 个磁盘。

- Azure 磁盘备份支持 PowerShell。 目前不支持 Azure CLI。

- 配置备份时，所选要进行备份的磁盘以及要存储快照的快照资源组必须属于同一订阅。 无法在某一特定磁盘的订阅之外为该磁盘创建增量快照。 详细了解托管磁盘的[增量快照](../virtual-machines/disks-incremental-snapshots.md#restrictions)。 要详细了解如何选择快照资源组，请参阅[配置备份](backup-managed-disks.md#configure-backup)。

- 为了成功进行备份和还原操作，备份保管库的托管标识需要角色分配。 仅使用文档中提供的角色定义。 不支持使用其他角色，如所有者、参与者等。 如果在分配角色后立即开始配置备份或还原操作，可能会遇到权限问题。 这是因为角色分配需要几分钟才能生效。

- 托管磁盘允许在部署当时或之后更改性能层，无需更改磁盘大小。 Azure 磁盘备份解决方案支持对所备份的源磁盘进行性能层更改。 在还原过程中，还原磁盘的性能层将与备份时源磁盘的性能层相同。 执行还原操作后，请按照[此处](../virtual-machines/disks-performance-tiers-portal.md)的文档更改磁盘的性能层。

- 对托管磁盘的[专用链接](../virtual-machines/disks-enable-private-links-for-import-export-portal.md)支持使你能够限制托管磁盘的导出和导入，使其仅发生在 Azure 虚拟网络中。 Azure 磁盘备份支持备份启用了专用终结点的磁盘。 这不包括可通过专用终结点访问的备份数据或快照。

- 可以删除备份实例，从而停止备份并删除所有备份数据。 目前无法禁用备份，因为“停止备份并保留备份数据”选项不受支持。

- Azure 磁盘备份限制如下：
    
    | 设置 | 最大限制 |
    | --- | --- |
    | 每个备份保管库的备份策略数 | 5000 |
    | 每个备份保管库的备份实例数 | 5000 |
    | 每个备份实例每天允许的按需备份数 | 10 |
    | 每个备份实例每天允许的还原数 | 10 |

## <a name="next-steps"></a>后续步骤

- [备份 Azure 托管磁盘](backup-managed-disks.md)