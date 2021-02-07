---
title: Azure 磁盘备份支持矩阵
description: 提供支持设置的摘要，并限制 Azure 磁盘备份。
ms.topic: conceptual
ms.date: 01/07/2021
ms.custom: references_regions
ms.openlocfilehash: 7d6de863a45bcadd73e847cbf407c13363952151
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2021
ms.locfileid: "99806098"
---
# <a name="azure-disk-backup-support-matrix-in-preview"></a>预览版中的 Azure 磁盘备份支持矩阵 () 

>[!IMPORTANT]
>Azure 磁盘备份没有服务级别协议，不建议用于生产工作负荷。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
>
>[填写此窗体](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR1vE8L51DIpDmziRt_893LVUNFlEWFJBN09PTDhEMjVHS05UWFkxUlUzUS4u) 以便注册预览版。

可以使用 [Azure 备份](./backup-overview.md) 来保护 azure 磁盘。 本文总结了区域可用性、支持的方案和限制。

## <a name="supported-regions"></a>支持的区域

Azure 磁盘备份在以下区域提供预览版：美国西部、美国西部、东2、加拿大中部、英国西部、瑞士北部、瑞士西部、澳大利亚中部、澳大利亚中部2、韩国中部、韩国南部、日本西部、东亚、阿拉伯联合酋长国北部、巴西南部、印度中部。 

当更多区域可用时，将公布这些区域。

## <a name="limitations"></a>限制

- Azure 托管磁盘支持 azure 磁盘备份，其中包括共享磁盘 (共享高级 Ssd) 。 不支持非托管磁盘。 由于缺乏快照功能，此解决方案当前不支持包含共享的超磁盘的超磁盘。

- Azure 磁盘备份支持写入加速器磁盘的备份。 但是，在还原过程中，磁盘将还原为正常磁盘。 将写入加速缓存装载到 VM 后，可以在磁盘上启用它。

- Azure 备份提供 Azure 托管磁盘的操作 (快照) 层备份，每日支持多个备份。 备份不会复制到备份保管库。

- 目前，Original-Location 恢复 (OLR) 选项，通过替换从其执行备份的现有源磁盘来还原。 你可以从恢复点还原，以便在同一资源组中创建一个新磁盘，与从中进行备份的源磁盘或任何其他资源组中的磁盘相同。 这称为 Alternate-Location 恢复 (ALR) 。

- 托管磁盘的 Azure 备份使用增量快照，每个磁盘的快照数限制为200个。 为了允许除计划备份之外的按需备份，备份策略会将备份总数限制为180。 详细了解托管磁盘的 [增量快照](../virtual-machines/disks-incremental-snapshots.md#restrictions) 。

- Azure [订阅和服务限制](../azure-resource-manager/management/azure-subscription-service-limits.md#virtual-machine-disk-limits) 适用于每个订阅每个区域的磁盘快照总数。

- 不支持附加到虚拟机的多个磁盘的时间点快照。

- 备份保管库和要备份的磁盘可以在相同或不同的订阅中。 但备份保管库和备份磁盘必须位于同一区域。

- 要备份的磁盘和存储快照的快照资源组必须位于同一订阅中。

- 支持将磁盘从备份还原到相同或不同的订阅。 但是，还原的磁盘将在快照所在的同一区域中创建。

- 无法保护 ADE 加密磁盘。

- 支持通过平台托管密钥或客户托管密钥加密的磁盘。 但是，如果禁用或删除了 Disk Encryption Set KeyVault 键，则使用客户管理的密钥加密的磁盘的还原点将无法还原 (CMK) 。

- 目前不能修改备份策略，并且无法更改在配置磁盘备份时分配给备份实例的快照资源组。

- 目前，配置磁盘备份的 Azure 门户体验最多只能有20个磁盘来自同一订阅。

- 目前 (预览) 期间，不支持使用 PowerShell 和 Azure CLI 来配置磁盘的备份和还原。

- 配置备份时，选择要备份的磁盘以及要存储快照的快照资源组必须属于同一订阅。 不能为该磁盘的订阅之外的特定磁盘创建增量快照。 详细了解托管磁盘的 [增量快照](../virtual-machines/windows/disks-incremental-snapshots-portal.md#restrictions) 。 有关如何选择快照资源组的详细信息，请参阅  [配置备份](backup-managed-disks.md#configure-backup)。

- 对于成功的备份和还原操作，备份保管库的托管标识需要角色分配。 仅使用文档中提供的角色定义。 不支持使用其他角色，如 "所有者"、"参与者" 等。 如果在分配角色后立即开始配置备份或还原操作，则可能会遇到权限问题。 这是因为角色分配需要几分钟才能生效。

- 托管磁盘允许在部署时或之后更改性能层，而无需更改磁盘大小。 Azure 磁盘备份解决方案支持对要备份的源磁盘进行性能层更改。 在还原过程中，还原磁盘的性能层将与备份时源磁盘的性能层相同。 执行还原操作后，请按照 [此处](../virtual-machines/disks-performance-tiers-portal.md) 的文档更改磁盘的性能层。

- 通过对托管磁盘的[私有链接](../virtual-machines/disks-enable-private-links-for-import-export-portal.md)支持，你可以限制导出和导入托管磁盘，使其仅出现在 Azure 虚拟网络中。 Azure 磁盘备份支持备份启用了专用终结点的磁盘。 这不包括可通过专用终结点访问的备份数据或快照。

- 在预览期间，不能禁用备份，因此不支持 " **停止备份并保留备份数据** " 选项。 可以删除备份实例，这不仅会停止备份，还会删除所有备份数据。

## <a name="next-steps"></a>后续步骤

- [备份 Azure 托管磁盘](backup-managed-disks.md)
