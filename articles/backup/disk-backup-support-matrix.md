---
title: Azure 磁盘备份支持矩阵
description: 汇总 Azure 磁盘备份的支持设置和限制。
ms.topic: conceptual
ms.date: 01/07/2021
ms.custom: references_regions
ms.openlocfilehash: 922bd532275cdd6b09df83b0e9d36fdec1da0b47
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "101707211"
---
# <a name="azure-disk-backup-support-matrix-in-preview"></a>Azure 磁盘备份支持矩阵（预览版）

>[!IMPORTANT]
>此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负载。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
>
>[填写此表格](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR1vE8L51DIpDmziRt_893LVUNFlEWFJBN09PTDhEMjVHS05UWFkxUlUzUS4u)以便注册预览版。

可使用 [Azure 备份](./backup-overview.md)保护 Azure 磁盘。 本文总结了区域可用性、支持的方案以及限制。

## <a name="supported-regions"></a>支持的区域

在以下区域中提供 Azure 磁盘备份预览版：美国西部、美国中西部、美国东部 2、加拿大中部、英国西部、瑞士北部、瑞士西部、澳大利亚中部、澳大利亚中部 2、韩国中部、韩国南部、日本西部、东亚、阿拉伯联合酋长国北部、巴西南部、印度中部。 

如果在更多区域中提供，后面将公布这些区域。

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

- 目前（预览版期间），不支持使用 PowerShell 和 Azure CLI 配置磁盘的备份和还原。

- 配置备份时，所选要进行备份的磁盘以及要存储快照的快照资源组必须属于同一订阅。 无法在某一特定磁盘的订阅之外为该磁盘创建增量快照。 详细了解托管磁盘的[增量快照](../virtual-machines/disks-incremental-snapshots.md#restrictions)。 要详细了解如何选择快照资源组，请参阅[配置备份](backup-managed-disks.md#configure-backup)。

- 为了成功进行备份和还原操作，备份保管库的托管标识需要角色分配。 仅使用文档中提供的角色定义。 不支持使用其他角色，如所有者、参与者等。 如果在分配角色后立即开始配置备份或还原操作，可能会遇到权限问题。 这是因为角色分配需要几分钟才能生效。

- 托管磁盘允许在部署当时或之后更改性能层，无需更改磁盘大小。 Azure 磁盘备份解决方案支持对所备份的源磁盘进行性能层更改。 在还原过程中，还原磁盘的性能层将与备份时源磁盘的性能层相同。 执行还原操作后，请按照[此处](../virtual-machines/disks-performance-tiers-portal.md)的文档更改磁盘的性能层。

- 对托管磁盘的[专用链接](../virtual-machines/disks-enable-private-links-for-import-export-portal.md)支持使你能够限制托管磁盘的导出和导入，使其仅发生在 Azure 虚拟网络中。 Azure 磁盘备份支持备份启用了专用终结点的磁盘。 这不包括可通过专用终结点访问的备份数据或快照。

- 在预览版期间，无法禁用备份，因此不支持“停止备份并保留备份数据”选项。 可以删除备份实例，从而停止备份并删除所有备份数据。

## <a name="next-steps"></a>后续步骤

- [备份 Azure 托管磁盘](backup-managed-disks.md)