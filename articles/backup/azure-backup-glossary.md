---
title: Azure 备份术语表
description: 本文定义了有助于 Azure 备份使用的术语。
ms.topic: conceptual
ms.date: 12/21/2020
ms.openlocfilehash: 8baa47667e86b99ebbbf273610809814e768c077
ms.sourcegitcommit: a89a517622a3886b3a44ed42839d41a301c786e0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/22/2020
ms.locfileid: "97733289"
---
# <a name="azure-backup-glossary"></a>Azure 备份术语表

使用 Azure 备份时，此术语词汇表非常有用。

>[!NOTE]
>
> - 标记为前缀为 " (工作负荷特定术语) " 的术语指那些仅在 Azure 备份支持的工作负荷的特定子集上下文中相关的术语。
> - 对于通常在 Azure 备份文档中使用的术语，但引用其他 Azure 服务，会向相关 Azure 服务的文档提供直接链接。

## <a name="afs-azure-file-shares"></a> (Azure 文件共享的 AFS) 

请参阅 [Azure 文件文档](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)。

## <a name="alternate-location-recovery"></a>备用位置恢复

从恢复点恢复到执行备份的原始位置之外的位置。 使用 Azure VM 备份时，这意味着将 VM 还原到执行备份的原始服务器以外的服务器。 使用 Azure 文件共享备份时，这意味着将数据还原到不同于已备份文件共享的文件共享。

## <a name="application-consistent-backup"></a>应用程序一致性备份

 (特定于工作负荷的术语) 

应用程序一致性备份捕获内存内容和挂起的 i/o 操作。 应用一致的快照使用 [vss](#vss-windows-volume-shadow-copy-service) vss 编写器 (或用于 Linux) 的预发布或 post 脚本，以确保在发生备份之前应用数据的一致性。 [了解详细信息](backup-azure-vms-introduction.md)。

## <a name="azure-resource-manager-arm-templates"></a>Azure 资源管理器 (ARM) 模板

请参阅 [ARM 模板文档](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview)。

## <a name="autoprotection-for-databases"></a>数据库) 的 start-autoprotection (

 (特定于工作负荷的术语) 

Start-autoprotection 是一项功能，可让你自动保护独立 SQL Server 实例或 SQL Server Always On 可用性组中的所有数据库。 它不仅能实现现有数据库的备份，而且还可以保护您将来可能添加的所有数据库。

## <a name="availability-storage-replication-types"></a>存储复制类型 (可用性) 

Azure 备份提供了三种复制类型，可使存储和数据高度可用：

### <a name="lrs"></a>LRS

[本地冗余存储 (LRS) ](https://docs.microsoft.com/azure/storage/common/storage-redundancy#locally-redundant-storage) 会将备份数据复制三次 (会在数据中心的存储缩放单位中创建备份数据的三个副本) 。 备份数据的所有副本都存在于同一区域中。 LRS 是一种低成本的选项，用于保护备份数据免受本地硬件故障的影响。

### <a name="grs"></a>GRS

[异地冗余存储 (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy#geo-redundant-storage) 是默认的和推荐的复制选项。 GRS 会将备份数据复制到离源数据主位置数百英里以外的次要区域。 GRS 的成本高于 LRS，但 GRS 为你的备份数据提供更高的持久性级别，即使出现区域性中断也是如此。

>[!NOTE]
>对于启用了跨区域还原功能的 GRS 保管库，备份存储将从 GRS 升级到 GRS (读访问 Geo-Redundant 存储) 。

### <a name="zrs"></a>ZRS

[区域冗余存储 (ZRS) ](https://docs.microsoft.com/azure/storage/common/storage-redundancy#zone-redundant-storage) 复制 [可用性区域](https://docs.microsoft.com/azure/availability-zones/az-overview#availability-zones)中的备份数据，从而在同一区域中确保备份数据的驻留和复原能力。 因此，可以在 ZRS 中备份需要 [数据驻留](https://azure.microsoft.com/resources/achieving-compliant-data-residency-and-security-with-azure/) 的关键工作负荷。

## <a name="azure-command-line-interface-cli"></a>Azure 命令行界面 (CLI)

请参阅 [Azure CLI 文档](https://docs.microsoft.com/cli/azure/what-is-azure-cli)。

## <a name="azure-policy"></a>Azure Policy

请参阅 [Azure 策略文档](https://docs.microsoft.com/azure/governance/policy/overview)。

## <a name="azure-powershell"></a>Azure PowerShell

请参阅 [Azure PowerShell 文档](https://docs.microsoft.com/powershell/azure/)。

## <a name="azure-resource-manager-arm"></a>Azure 资源管理器 (ARM) 

请参阅 [Azure 资源管理器文档](https://docs.microsoft.com/azure/azure-resource-manager/management/overview)。

## <a name="azure-disk-encryption-ade"></a>Azure 磁盘加密 (ADE)

请参阅 [Azure 磁盘加密文档](https://docs.microsoft.com/azure/security/fundamentals/azure-disk-encryption-vms-vmss)。

## <a name="backend-storage--cloud-storage--backup-storage"></a>后端存储/云存储/备份存储

备份实例使用的实际存储。 包括备份实例的所有保留点的大小 (由备份和保留策略) 定义。

## <a name="bare-metal-backup"></a>裸机备份

备份操作系统文件和关键卷上的所有数据（用户数据除外）。 按照定义，裸机备份包括系统状态备份。 在计算机不会启动的情况下，如果必须彻底恢复，则可通过此备份获得保护。 [了解详细信息](backup-mabs-system-state-and-bmr.md)。

## <a name="backup-extensions--vm-extensions"></a>备份扩展/VM 扩展

特定于 Azure VM 工作负载类型的 () 

Azure 虚拟机 (VM) 扩展是小型应用程序，可在 Azure VM 上提供部署后配置和自动化任务。 Azure 备份通过为在计算机上运行的 Azure VM 代理安装一个扩展来备份 Azure VM。 Azure 备份会自动管理这些扩展，并且用户无需手动更新这些扩展。

## <a name="backup-instance--backup-item"></a>备份实例/备份项

备份到具有特定备份和保留策略的保管库的数据源构成了备份实例或备份项。

## <a name="backup-rule--backup-policy"></a>备份规则/备份策略

备份规则是用户定义的规则，用于指定应在数据源上执行备份的时间和频率。 对于某些工作负荷类型，备份策略还提供了一种方法来指定要应用于数据源的快照方法， (完整、增量、差异) 。 备份策略通常创建为备份规则和保留规则的组合。

## <a name="backup-vault"></a>备份保管库

*DataProtection/BackupVaults* 类型的 Azure 资源管理器资源。 目前，备份保管库用于备份适用于 PostgreSQL 服务器的 Azure 数据库。 [了解有关备份保管库的详细信息](backup-azure-recovery-services-vault-overview.md)。

## <a name="bcdr-business-continuity-and-disaster-recovery"></a>BCDR (业务连续性和灾难恢复) 

BCDR 涉及组织必须采用的一组过程，以确保应用和工作负荷在计划内和计划外服务或 Azure 中断期间启动并运行，同时降低业务中断。 [了解](https://azure.microsoft.com/solutions/backup-and-disaster-recovery/#overview) 有关 Azure 提供的各种服务的详细信息，帮助创建 BCDR 策略。

## <a name="churn"></a>改动

在两个连续备份之间备份的数据的更改百分比。 这可能是由于添加了新数据，或者修改或删除了现有数据。

## <a name="crash-consistent-backup"></a>崩溃一致性备份

 (特定于工作负荷的术语) 

如果在备份时 Azure VM 关闭，则往往会发生崩溃一致性快照。 仅会捕获和备份备份时磁盘上已存在的数据。 [了解详细信息](backup-azure-vms-introduction.md#snapshot-consistency)。

## <a name="cross-region-restore-crr"></a>跨区域还原 (CRR) 

作为 [还原选项](backup-azure-arm-restore-vms.md#restore-options)之一，跨区域还原 (CRR) 使你能够还原次要区域（即 [Azure 配对区域](https://docs.microsoft.com/azure/best-practices-availability-paired-regions#what-are-paired-regions)）中的备份项。

## <a name="data-box"></a>数据框

请参阅 [数据框文档](https://docs.microsoft.com/azure/databox/data-box-overview)。

## <a name="datasource"></a>数据源

 (Azure 资源、代理资源或本地资源) 的资源，该资源是备份的候选项。 例如，Azure VM 或 Azure 文件共享。

## <a name="dpm-data-protection-manager"></a>DPM (Data Protection Manager) 

 (特定于工作负荷的术语) 

请参阅 [DPM 文档](https://docs.microsoft.com/system-center/dpm/dpm-overview)。

## <a name="expressroute"></a>ExpressRoute

请参阅 [ExpressRoute 文档](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)。

## <a name="file-system-consistent-backup"></a>文件系统一致性备份

 (特定于工作负荷的术语) 

文件系统一致性备份通过同时创建所有文件的快照来提供一致性。 [了解详细信息](backup-azure-vms-introduction.md#snapshot-consistency)。

## <a name="frontend-storage--source-size"></a>前端存储/源大小

要为数据源备份的数据的大小。 数据源的前端大小确定其 [受保护的实例](#protected-instance) 计数。

## <a name="full-backup"></a>完整备份

在完整备份中，将为每个备份存储整个数据源的副本。

## <a name="gfs-backup-policy"></a>GFS 备份策略

GFS (祖父-父子) 备份策略是一种可用于定义每周、每月和每年备份计划以及每日备份计划的。 每周备份都是 "专家"，每月备份称为 "fathers"，每年备份称为 "grandfathers"。 每组备份副本都可以配置为在不同的时间段内保留，从而可以更好地自定义备份副本的保留选项。 GFS 策略有助于以更高的存储效率更长时间保留备份。

## <a name="iaas-vms--azure-vms"></a>IaaS Vm/Azure Vm

请参阅 [AZURE VM 文档](https://docs.microsoft.com/azure/virtual-machines/)。

## <a name="incremental-backup"></a>增量备份

增量备份仅存储自上次备份后已更改的块。

## <a name="instant-restore"></a>即时还原

"即时还原" 包括直接从备份快照还原计算机，而不是从保管库中的快照副本还原。 即时还原比从保管库还原快。 可用的即时还原点数取决于为快照配置的保持期。

## <a name="iops"></a>IOPS

每秒输入/输出操作数。

## <a name="item-level-restore"></a>项目级还原

 (特定于工作负荷的术语) 

从恢复点还原计算机内部的单个文件或文件夹。

## <a name="job"></a>作业

由用户或 Azure 备份服务创建的与备份相关的任务。 作业可以是计划的，也可以是按需 (即席) 。 有不同类型的作业-备份、还原、配置保护等。 [了解有关作业的详细信息](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault)。

## <a name="mabs--azure-backup-server"></a>MABS/Azure 备份服务器

 (特定于工作负荷的术语) 

使用 Azure 备份服务器，可以通过一个控制台保护应用程序工作负载，如 Hyper-V VM、Microsoft SQL Server、SharePoint Server、Microsoft Exchange 和 Windows 客户端。 它从 DPM 继承了大量工作负荷备份功能，但有一些不同之处。 [了解详细信息](backup-azure-microsoft-azure-backup.md)

## <a name="managed-disks"></a>托管磁盘

请参阅 [托管磁盘文档](https://docs.microsoft.com/azure/virtual-machines/managed-disks-overview)。

## <a name="mars-agent"></a>MARS 代理

 (特定于工作负荷的术语) 

Azure 备份也称为 **Azure 备份代理** 或 **恢复服务代理**，用于将数据从本地计算机和 Azure vm 备份到 Azure 中的备份恢复服务保管库。 [了解详细信息](backup-support-matrix-mars-agent.md)。

## <a name="nsg-network-security-group"></a>NSG (网络安全组) 

请参阅 [NSG 文档](https://docs.microsoft.com/azure/virtual-network/network-security-groups-overview)。

## <a name="offline-seeding"></a>脱机种子设定

脱机种子设定是指在不使用网络带宽的情况下脱机传输初始 (完整) 备份的过程。 它提供一种机制，用于将备份数据复制到物理存储设备上，然后将其发送到附近的 Azure 数据中心并上载到恢复服务保管库。 [了解详细信息](offline-backup-overview.md)。

## <a name="on-demand-backup--ad-hoc-backup"></a>按需备份/即席备份

用户一次只需触发的备份作业，而不是基于已为资源配置的备份计划 (策略) 。

## <a name="original-location-recovery-olr"></a>原始位置恢复 (OLR) 

从还原点到执行备份的源位置进行的恢复，并将其替换为恢复点中存储的状态。 使用 Azure VM 备份时，这意味着将 VM 还原到执行备份的原始服务器。 使用 Azure 文件共享备份时，这意味着将数据还原到备份的文件共享。

## <a name="passphrase"></a>通行短语

 (特定于工作负荷的术语) 

使用 MARS 代理向/从 Azure 备份或还原本地计算机时，通行短语用于加密和解密数据。

## <a name="point-in-time-restore"></a>时间点还原

在特定时间点将项还原到其状态， (PIT) 。

## <a name="private-endpoint"></a>专用终结点

请参阅 [私有终结点文档](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)。

## <a name="protected-instance"></a>受保护实例

受保护的实例是指用于配置 Azure 备份的计算机、物理或虚拟服务器。  从 **计费的角度来看**，计算机的受保护实例计数是其前端大小的函数。 [了解详细信息](https://azure.microsoft.com/pricing/details/backup/)。

## <a name="rbac-role-based-access-control"></a>RBAC (基于角色的访问控制) 

请参阅 [RBAC 文档](https://docs.microsoft.com/azure/role-based-access-control/overview)。

## <a name="recovery-point-restore-point-retention-point"></a>恢复点/还原点/保留点

正在备份的原始数据的副本。 保留点与时间戳关联，因此可以使用它将项目还原到特定的时间点。

## <a name="recovery-services-vault"></a>恢复服务保管库

*Microsoft.recoveryservices/保管库* 类型的 Azure 资源管理器资源。 目前，恢复服务保管库用于备份以下工作负荷： azure vm、Azure Vm 中的 SQL、Azure Vm SAP HANA 和 Azure 文件共享。 它还用于使用 MARS Azure 备份服务器 (MABS) 和 System Center DPM 备份本地工作负荷。 [了解有关恢复服务保管库的详细信息](backup-azure-recovery-services-vault-overview.md)。

## <a name="resource-group"></a>资源组

请参阅 [Azure 资源管理器文档](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-portal#what-is-a-resource-group)。

## <a name="rest-api"></a>REST API

请参阅 [Azure REST API 文档](https://docs.microsoft.com/rest/api/azure/)。

## <a name="retention-rule"></a>保留规则

用户定义的规则，用于指定备份应保留多长时间。

## <a name="rpo-recovery-point-objective"></a>RPO (恢复点目标) 

RPO 表示数据丢失方案中可接受的最大数据丢失量。 这取决于备份频率。

## <a name="rto-recovery-time-objective"></a>RTO (恢复时间目标) 

RTO 表示数据丢失方案后，可以将数据还原到上一个可用时间点的最大可接受时间。

## <a name="scheduled-backup"></a>计划的备份

由为给定项配置的备份策略自动触发的备份作业。

## <a name="secondary-region--paired-region"></a>辅助区域/配对区域

区域对由同一地理位置中的两个区域组成。 一个是主要区域，另一个是次要区域。 某些 Azure (服务（包括具有 GRS) 设置的 Azure 备份）使用成对区域，以确保业务连续性并防范数据丢失。 [了解详细信息](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)。

## <a name="soft-delete"></a>软删除

软删除是一项可帮助防止意外删除备份数据的功能。 使用软删除时，即使恶意执行组件删除了备份 (或意外删除了备份数据) ，备份数据也会保留一段额外时间，从而允许在不丢失数据的情况下恢复该备份项。 [了解详细信息](backup-azure-security-feature-cloud.md)。

## <a name="snapshot"></a>快照

快照是虚拟硬盘 (VHD) 的完整只读副本。 [了解详细信息](https://docs.microsoft.com/azure/virtual-machines/windows/snapshot-copy-managed-disk)。

## <a name="storage-account"></a>存储帐户

请参阅 [存储帐户文档](https://docs.microsoft.com/azure/storage/common/storage-account-overview)。

## <a name="subscription"></a>订阅

Azure 订阅是用于在 Azure 中预置资源的逻辑容器。 它包含所有资源的详细信息，如虚拟机 (Vm) 、数据库等。

## <a name="system-state-backup"></a>系统状态备份

 (特定于工作负荷的术语) 

备份操作系统文件。 通过这种备份可以在计算机启动时进行恢复，但系统文件和注册表会丢失。 [了解详细信息](backup-mabs-system-state-and-bmr.md)。

## <a name="tenant"></a>租户

租户是组织的表示形式。 它是 Azure AD 专用实例，组织或应用开发人员与 Microsoft 建立关系时（例如注册 Azure、Microsoft Intune 或 Microsoft 365）会收到该实例。

## <a name="unmanaged-disk"></a>非托管磁盘

请参阅 [非托管磁盘文档](https://docs.microsoft.com/azure/storage/common/storage-disaster-recovery-guidance#azure-unmanaged-disks)。

## <a name="vault"></a>保管库

Azure 中包含备份数据的存储实体。 它也是 RBAC 和计费的单位。 目前有两种类型的保管库-恢复服务保管库和备份保管库。

## <a name="vault-credentials"></a>保管库凭据

保管库凭据文件是门户为每个保管库生成的证书。 将服务器注册到保管库时，会使用此服务。 [了解详细信息](backup-azure-dpm-introduction.md)。

## <a name="vnet-virtual-network"></a>VNET (虚拟网络) 

请参阅 [VNET 文档](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)。

## <a name="vss-windows-volume-shadow-copy-service"></a>VSS (Windows 卷影复制服务) 

请参阅 [VSS 文档](https://docs.microsoft.com/windows-server/storage/file-server/volume-shadow-copy-service)。

## <a name="next-steps"></a>后续步骤

- [Azure 备份概述](backup-overview.md)
- [Azure 备份体系结构和组件](backup-architecture.md)
