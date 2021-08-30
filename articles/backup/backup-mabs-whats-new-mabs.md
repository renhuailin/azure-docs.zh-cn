---
title: Microsoft Azure 备份服务器中的新增功能
description: Microsoft Azure 备份服务器提供用于保护 VM、文件和文件夹、工作负载等的增强备份功能。
ms.topic: conceptual
ms.date: 07/27/2021
ms.openlocfilehash: 8f7c0e8709cd899b9b6a285dcd8c556763869c65
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121722660"
---
# <a name="whats-new-in-microsoft-azure-backup-server-mabs"></a>Microsoft Azure 备份服务器 (MABS) 中的新增功能

## <a name="whats-new-in-mabs-v3-ur2"></a>MABS v3 UR2 中的新增功能

Microsoft Azure 备份服务器 (MABS) 版本 3 UR2 支持以下新功能/功能更新。

有关 UR2 问题修复和安装说明的信息，请参阅[知识库文章](https://support.microsoft.com/topic/update-rollup-2-for-microsoft-azure-backup-server-v3-350de164-0ae4-459a-8acf-7777dbb7fd73)。

### <a name="support-for-azure-stack-hci"></a>支持 Azure Stack HCI

借助 MABS v3 UR2，可以在 Azure Stack HCI 上备份虚拟机。 [了解详细信息](./back-up-azure-stack-hyperconverged-infrastructure-virtual-machines.md)。

### <a name="support-for-vmware-70"></a>支持 VMware 7.0

借助 MABS v3 UR2，可以备份 VMware 7.0 VM。 [了解详细信息](./backup-azure-backup-server-vmware.md)。

### <a name="support-for-sql-server-failover-cluster-instance-fci-using-cluster-shared-volume-csv"></a>使用群集共享卷 (CSV) 的 SQL Server 故障转移群集实例 (FCI) 支持

MABS v3 UR2 支持使用群集共享卷 (CSV) 的 SQL Server 故障转移群集实例 (FCI)。 借助 CSV，可以简化 SQL Server 实例的管理。 这有助于你从任何节点管理基础存储，因为具有拥有磁盘的节点的抽象。 [了解详细信息](./backup-azure-sql-mabs.md)。

### <a name="optimized-volume-migration"></a>优化的卷迁移

MABS v3 UR2 支持优化的卷迁移。 借助优化的卷迁移，你可以更快地将数据源移至新卷。 此增强迁移过程仅将活动备份副本（活动副本）迁移到新卷。 所有新恢复点均在新卷上创建，而现有恢复点则在现有卷上维护，并根据保留策略进行清除。 [了解详细信息](/system-center/dpm/volume-to-volume-migration?view=sc-dpm-2019&preserve-view=true)。

### <a name="offline-backup-using-azure-data-box"></a>使用 Azure Data Box 进行脱机备份

MABS v3 UR2 支持使用 Azure Data Box 进行脱机备份。 借助 Microsoft Azure Data Box 集成，可以克服将数 TB 的备份数据从本地存储移至 Azure 存储的挑战。 Azure Data Box 可节省购买自己的与 Azure 兼容的磁盘和连接器或将临时存储预配为暂存位置所需完成的工作量。 Microsoft 还处理端到端的传输物流，你可以通过 Azure 门户跟踪。 [了解详细信息](./offline-backup-azure-data-box-dpm-mabs.md)。

## <a name="whats-new-in-mabs-v3-ur1"></a>MABS V3 UR1 中的新增功能

Microsoft Azure 备份服务器 (MABS) 版本 3 UR1 是最新更新，其中包含重要的 bug 修复以及其他功能和增强功能。 若要查看 MABS V3 UR1 中已修复的 bug 列表和安装说明，请参阅知识库文章 [4534062](https://support.microsoft.com/help/4534062)。

>[!NOTE]
>MABS v3 UR1 不再支持 32 位保护代理。 请参阅[弃用 32 位保护代理](#32-bit-protection-agent-deprecation)。

### <a name="faster-backups-with-tiered-storage-using-ssds"></a>通过使用 SSD 的分层存储加快了备份速度

MABS V2 引入了[新式备份存储](backup-mabs-add-storage.md) (MBS)，从而提高了存储利用率和性能。 MBS 使用 ReFS 作为基础文件系统，并被设计为使用诸如分层存储之类的混合存储。

若要通过 MBS 实现缩放和性能，建议将 MABS V3 UR1 的一小部分（占总存储的 4%）闪存 (SSD) 用作分层卷，并与 DPM HDD 存储结合使用。 具有分层存储的 MABS V3 UR1 的备份速度可提高 50-70%。 有关配置分层存储的步骤，请参阅 DPM 文章[使用分层存储设置 MBS](/system-center/dpm/add-storage#set-up-mbs-with-tiered-storage)。

### <a name="support-for-refs-volumes"></a>对 ReFS 卷的支持

借助 MABS V3 UR1，可以备份 ReFS 卷和部署在 ReFS 卷上的工作负载。 可以备份 ReFS 卷上部署的以下工作负载：

* 操作系统（64 位）：Windows Server 2019、2016、2012 R2、2012。
* SQL Server：SQL Server 2019、SQL Server 2017、2016。
* Exchange：Exchange 2019、2016。
* SharePoint：SharePoint 2019、2016（包含最新 SP）。

>[!NOTE]
> MABS V3 支持备份 ReFS 卷上存储的 Hyper-V VM

>[重要说明] 我们已经发现了一些已删除重复数据的 ReFS 卷的备份问题。 我们正在努力解决这些问题，并将在有可用的修补程序后立即更新此部分。 在此之前，我们将从 MABSv3 UR1 中删除对删除重复数据的 ReFS 卷备份的支持。

### <a name="azure-vmware-solution-protection-support"></a>Azure VMware 解决方案保护支持

借助 MABS v3 UR1，现在可以保护部署在 [Azure VMware 解决方案](../azure-vmware/index.yml)中的虚拟机。

### <a name="vmware-parallel-backups"></a>VMware 并行备份

借助 MABS V3 UR1，单个保护组中的所有 VMware VM 备份将并行进行，从而使 VM 备份速度提高 25%。
早期版本的 MABS 仅跨保护组执行并行备份。 借助 MABS V3 UR1，VMware 增量复制作业将并行运行。 默认情况下，并行运行的作业数设置为 8。 了解有关 [VMware 并行备份](backup-azure-backup-server-vmware.md#vmware-parallel-backups)的详细信息。

### <a name="disk-exclusion-for-vmware-vm-backup"></a>VMware VM 备份的磁盘排除

借助 MABS V3 UR1，可以从 VMware VM 备份中排除特定磁盘。 了解有关[从 VMware VM 备份中排除磁盘](backup-azure-backup-server-vmware.md#exclude-disk-from-vmware-vm-backup)的详细信息。  

### <a name="support-for-additional-layer-of-authentication-to-delete-online-backup"></a>支持额外的身份验证层，以删除联机备份

MABS V3 UR1 为关键操作添加了额外的身份验证层。 当你执行“停止保护并删除数据”操作时，系统将提示你输入安全 PIN。

### <a name="offline-backup-improvements"></a>脱机备份改进

MABS v3 UR1 通过 Azure 导入/导出服务改善了脱机备份体验。 有关详细信息，请参阅[此处](./backup-azure-backup-server-import-export.md)的更新步骤。

>[!NOTE]
>通过 MABS v3 UR2，MABS 可以使用 Azure Data Box 执行脱机备份。 [了解详细信息](./offline-backup-azure-data-box-dpm-mabs.md)。

### <a name="new-cmdlet-parameter"></a>新的 cmdlet 参数

MABS V3 UR1 包含新参数 [-CheckReplicaFragmentation]。 新参数用于计算副本的碎片百分比，并包含在 Copy-DPMDatasourceReplica cmdlet 中。

### <a name="32-bit-protection-agent-deprecation"></a>弃用 32 位保护代理

MABS v3 UR1 不再支持 32 位保护代理。 将 MABS v3 服务器升级到 UR1 后，你将无法保护 32 位工作负载。 任何现有的 32 位保护代理都将处于禁用状态；计划的备份将失败并显示“代理被禁用”错误。 如果要保留这些代理的备份数据，可以通过“保留数据”选项停止保护。 否则，可以删除保护代理。

>[!NOTE]
>查看[更新的保护矩阵](./backup-mabs-protection-matrix.md)，以了解 MABS UR1 支持保护的工作负载。

## <a name="whats-new-in-mabs-v3-rtm"></a>MABS V3 RTM 中的新增功能

Microsoft Azure 备份服务器版本 3 (MABS V3) 包含重要的 bug 修复、Windows Server 2019 支持、SQL 2017 支持以及其他功能和增强功能。 若要查看 MABS V3 中已修复的 bug 列表和安装说明，请参阅知识库文章 [4457852](https://support.microsoft.com/help/4457852/microsoft-azure-backup-server-v3)。

MABS V3 包含以下功能：

### <a name="volume-to-volume-migration"></a>卷到卷的迁移

发布 MABS V2 中的新式备份存储 (MBS) 时，我们宣布推出了工作负荷感知的存储，在其中可以根据存储属性，将某些工作负荷配置为备份到特定的存储。 但是，在配置后，你可能发现，需要将某些数据源的备份移到其他存储，以优化资源利用率。 在 MABS V3 中可以迁移备份，并通过[三个步骤](https://techcommunity.microsoft.com/t5/system-center-blog/sc-2016-dpm-ur4-migrate-backup-storage-in-3-simple-steps/ba-p/351842)将其配置为存储到其他卷。

### <a name="prevent-unexpected-data-loss"></a>防止意外的数据丢失

在企业中，MABS 由管理员团队管理。 尽管用于备份的存储有指导原则可供参考，但在 MABS 中提供错误的卷作为备份存储可能会导致关键数据丢失。 在 MABS V3 中，可以使用[这些 PowerShell cmdlet](./backup-mabs-add-storage.md) 将这些卷配置为不可用于存储，来防止这种情况。

### <a name="custom-size-allocation"></a>自定义大小分配

新式备份存储 (MBS) 根据需要消耗少量的存储。 为此，在配置用于保护后，MABS 会计算备份数据的大小。 但是，如果要同时备份许多的文件和文件夹（例如，在文件服务器中备份时），大小计算可能会花费很长时间。 在 MABS V3 中，可将 MABS 配置为接受默认卷大小而不是计算每个文件的大小，从而可以节省时间。

### <a name="optimized-cc-for-rct-vms"></a>针对 RCT VM 优化的一致性检查

MABS 使用 RCT（Hyper-V 中的本机更改跟踪），可以在发生 VM 崩溃等情况时减少非常耗时的一致性检查的需求。 与基于 VSS 快照的备份提供的更改跟踪相比，RCT 提供更好的复原能力。 在执行任何一致性检查期间，MABS V3 只会传输更改的数据，因而可以进一步优化网络和存储消耗。

### <a name="support-to-tls-12"></a>TLS 1.2 支持

TLS 1.2 是 Microsoft 建议的通信方式，并采用一流的加密。 MABS 现在支持 MABS 与受保护服务器之间的 TLS 1.2 通信，以进行基于证书的身份验证和云备份。

### <a name="vmware-vm-protection-support"></a>VMware VM 保护支持

现在，支持对生产部署使用 VMware VM 备份。 MABS V3 为 VMware VM 保护提供以下功能：

* 支持 vCenter 和 ESXi 6.5、5.5 和 6.0。
* 在云中自动保护 VMware VM。 如果将新的 VMware VM 添加到受保护的文件夹，则会在磁盘和云中自动保护这些 VM。
* VMware 备用位置恢复的恢复效率改进。

### <a name="sql-2017-support"></a>SQL 2017 支持

MABS V3 可与用作 MABS 数据库的 SQL 2017 一同安装。 可将 SQL Server 从 SQL 2016 升级到 SQL 2017，或全新安装 SQL Server。 还可以使用 MABS V3 在群集和非群集环境中备份 SQL 2017 工作负荷。

### <a name="windows-server-2019-support"></a>Windows Server 2019 支持

可在 Windows Server 2019 中安装 MABS V3。 若要将 MABS V3 与 WS2019 配合使用，可以在安装/升级到 MABS V3 之前将 OS 升级到 WS2019，或者在 WS2016 中安装/升级 V3 之后升级 OS。

MABS V3 是完整发行版，可直接安装在 Windows Server 2016、Windows Server 2019 中，或者从 MABS V2 升级。 在升级到或安装备份服务器 V3 之前，请阅读安装先决条件。
在[此处](./backup-azure-microsoft-azure-backup.md#software-package)可以找到有关 MABS 安装/升级步骤的详细信息。

> [!NOTE]
>
> MABS 的代码基与 System Center Data Protection Manager 相同。 MABS v3 相当于 Data Protection Manager 1807。 MABS v3 UR1 相当于 Data Protection Manager 2019 UR1。

## <a name="next-steps"></a>后续步骤

了解如何准备服务器或开始保护工作负荷：

* [准备备份服务器工作负荷](backup-azure-microsoft-azure-backup.md)
* [使用备份服务器备份 VMware 服务器](backup-azure-backup-server-vmware.md)
* [使用备份服务器备份 SQL Server](backup-azure-sql-mabs.md)
* [将 Modern Backup Storage 与备份服务器配合使用](backup-mabs-add-storage.md)