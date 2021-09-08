---
title: 使用 Azure 上的 SAP IQ 实施 SAP BW NLS | Microsoft Docs
description: 使用 Azure 上的 SAP IQ 规划、部署和配置 SAP BW NLS 解决方案。
services: virtual-machines,virtual-machines-windows,virtual-machines-linux,virtual-network,storage,azure-netapp-files,azure-shared-disk,managed-disk
documentationcenter: saponazure
author: dennispadia
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/11/2021
ms.author: depadia
ms.openlocfilehash: 7b4f49efff29717e3345a2cc04286e9c3e86c8f7
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123306523"
---
# <a name="sap-bw-nls-implementation-guide-with-sap-iq-on-azure"></a>使用 Azure 上的 SAP IQ 的 SAP BW NLS 实施指南

多年来，运行 SAP Business Warehouse (BW) 系统的客户发现数据库大小呈指数增长，导致计算成本增加。 若要实现成本和性能的合理平衡，客户可以使用近线存储 (NLS) 来迁移历史数据。 

基于 SAP IQ 的 NLS 实施是 SAP 从主数据库（SAP HANA 或 AnyDB）移动历史数据的标准方法。 SAP IQ 集成使经常访问的数据与不经常访问的数据得以分开，从而减少了 SAP BW 系统中的资源需求。 

本指南将提供有关使用 Azure 上的 SAP IQ 规划、部署和配置 SAP BW NLS 的指导原则。 它涵盖与 SAP IQ NLS 部署相关的常见 Azure 服务和功能，不涵盖任何 NLS 合作伙伴解决方案。 

本指南不会将 SAP 关于 NLS 部署的标准文档替换为 SAP IQ。 而是对其官方安装和管理文档的补充。 

## <a name="solution-overview"></a>解决方案概述

在正常运行的 SAP BW 系统中，由于业务和法律方面的要求，数据量会不断增加。 大量的数据会影响系统性能，增加管理工作量，因此需要实施数据老化策略。 

如果要在不删除数据的情况下，保持 SAP BW 系统中的数据量，可以使用数据存档。 数据首先移动到存档或近线存储中，然后从 SAP BW 系统中删除。 你可以直接访问数据，也可以根据需要将其加载回来，具体取决于数据的存档方式。 

SAP BW 用户可以将 SAP IQ 用作近线存储解决方案。 SAP IQ 的适配器作为近线解决方案随 SAP BW 系统一起提供。 实现 NLS 后，常用数据存储在 SAP BW 在线数据库（SAP HANA 或 AnyDB）中。 不经常访问的数据存储在 SAP IQ 中，这样可以降低管理数据的成本，提高 SAP BW 系统的性能。 为了保证在线数据和近线数据的一致性，存档分区处于锁定、只读状态。 

SAP IQ 支持两种类型的体系结构：单工和多路复用。 在单工体系结构中，单个 SAP IQ 服务器的实例在单个虚拟机上运行。 文件可能位于主机或网络存储设备上。 

> [!Important]
> 对于 SAP-NLS 解决方案，只能使用单工体系结构，且它由 SAP 进行评估。

![关系图显示 SAP IQ 解决方案的概述。](media/sap-iq-deployment-guide/sap-iq-solution-overview.png)

在 Azure 中，SAP IQ 服务器必须在单独的虚拟机 (VM) 上实施。 不建议在已经运行其他数据库实例的现有服务器上安装 SAP IQ 软件，因为 SAP IQ 会占用所有 CPU 和内存。 一台 SAP IQ 服务器可用于多个 SAP NLS 实施。 

## <a name="support-matrix"></a>支持矩阵

SAP IQ NLS 解决方案的支持矩阵包括：

- **操作系统**：SAP IQ 仅在操作系统级别认证。 可在 Azure 环境中运行 SAP IQ 认证的操作系统，只要它能兼容在 Azure 基础结构中运行。 有关详细信息，请参阅 [SAP 说明 2133194](https://launchpad.support.sap.com/#/notes/2133194)。

- **SAP BW 兼容性**：SAP IQ 的近线存储仅针对已经在 Unicode 下运行的 SAP BW 系统发布。 [SAP 说明 1796393](https://launchpad.support.sap.com/#/notes/1796393) 包含 SAP BW 相关信息。

- 存储：在 Azure 中，SAP IQ 支持高级托管磁盘（Windows 和 Linux）、Azure 共享磁盘（仅限 Windows）和 Azure NetApp 文件（仅限 Linux）。 

有关基于你的 SAP IQ 版本的更多最新信息，请参阅[产品可用性矩阵](https://userapps.support.sap.com/sap/support/pam)。 

## <a name="sizing"></a>调整大小

SAP IQ 的大小调整仅限于 CPU、内存和存储。 在 [SAP 说明 1951789](https://launchpad.support.sap.com/#/notes/1951789) 中，可发现有关 Azure 上的 SAP IQ 的一般大小调整准则。 按照准则获得的大小调整建议需要映射到 SAP 的认证 Azure 虚拟机类型。 [SAP 说明 1928533](https://launchpad.support.sap.com/#/notes/1928533) 提供了受支持 SAP 产品和 Azure VM 类型的列表。 

[SAP 说明 1951789](https://launchpad.support.sap.com/#/notes/1951789) 中提到的 SAP IQ 大小调整指南和大小调整工作表专为本机使用 SAP IQ 数据库而开发。 由于它们未反映用于规划 SAP IQ 数据库的资源，因此最终你可能会得到 SAP NLS 的未使用资源。

## <a name="azure-resources"></a>Azure 资源

### <a name="regions"></a>区域

如果你已在 Azure 上运行 SAP 系统，则可能已经确定了区域。 SAP IQ 部署必须与要为其实施 NLS 解决方案的 SAP BW 系统位于同一区域。 

若要确定 SAP IQ 的体系结构，需要确保 Azure NetApp 文件（仅限适用于 Linux 的 NFS）等 SAP IQ 所需的服务在该区域可用。 若要查看所在区域的服务可用性，请参阅[各区域的产品可用性](https://azure.microsoft.com/global-infrastructure/services/)网页。

### <a name="availability-sets"></a>可用性集 

若要在 Azure 基础结构中实现 SAP 系统冗余，需要在可用性集或可用性区域中部署应用程序。 尽管可使用 SAP IQ 多路复用体系结构实现 SAP IQ 高可用性，但多路复用体系结构并不符合 NLS 解决方案的要求。 

若要实现 SAP IQ 单工体系结构的高可用性，需要配置具有自定义解决方案的双节点群集。 双节点 SAP IQ 群集可以部署在可用性集或可用性区域中，但附加到节点的 Azure 存储类型将决定其部署方法。 目前，Azure 共享高级磁盘和 Azure NetApp 文件不支持分区部署。 这使得只能选择在可用性集中进行 SAP IQ 部署。 

### <a name="virtual-machines"></a>虚拟机

根据 SAP IQ 大小调整，你需要将需求映射到 Azure 虚拟机。 对于 SAP 产品，Azure 支持此方法。 [SAP 说明 1928533](https://launchpad.support.sap.com/#/notes/1928533) 是一个很好的起点，其中列出了适用于 Windows 和 Linux 上的 SAP 产品的受支持 Azure VM 类型。 

除了选择仅受支持的 VM 类型之外，还需要检查这些 VM 类型在特定区域是否可用。 可以在[各区域的产品可用性](https://azure.microsoft.com/global-infrastructure/services/)网页上查看 VM 类型的可用性。 若要选择定价模型，请参阅[适用于 SAP 工作负载的 Azure 虚拟机](planning-guide.md#azure-virtual-machines-for-sap-workload)。 

> [!TIP]
> 对于生产系统，建议使用 E 系列虚拟机，原因在于其核心内存比。

### <a name="storage"></a>存储

Azure 存储为客户提供了多种存储类型。 有关这些类型的详细信息，请参阅 [Azure 中提供哪些磁盘类型？](../../disks-types.md)一文。 

Azure 中的某些存储类型对 SAP 场景的使用有限，但其他类型非常适合特定的 SAP 工作负载场景或针对这些场景进行了优化。 有关详细信息，请参阅 [适用于 SAP 工作负载的 Azure 存储类型](planning-guide-storage.md)指南。 它强调了适合 SAP 的存储选项。 

对于 Azure 上的 SAP IQ，可使用以下 Azure 存储类型。 根据操作系统（Windows 或 Linux）和部署方法（独立或高可用性）进行选择。

- Azure 托管磁盘

  [托管磁盘](../../managed-disks-overview.md)是 Azure 管理的块级存储卷。 可使用托管磁盘进行 SAP IQ 单工部署。 有多种类型的托管磁盘可供选择，但建议对 SAP IQ 使用[高级 SSD](../../disks-types.md#premium-ssd)。 

- Azure 共享磁盘

  [共享磁盘](../../disks-shared.md)是 Azure 托管磁盘的一项新功能，可将托管磁盘同时附加到多个 VM。 共享托管磁盘不以本机方式提供可以通过 SMB 或 NFS 访问的完全托管文件系统。 需要使用群集管理器（如 [Windows Server 故障转移群集](https://github.com/MicrosoftDocs/windowsserverdocs/blob/master/WindowsServerDocs/failover-clustering/failover-clustering-overview.md) (WSFC)），它处理群集节点通信和写入锁定。 
  
  若要在 Windows 上为 SAP IQ 单工体系结构部署高可用性解决方案，可在 WSFC 管理的两个节点之间使用 Azure 共享磁盘。 [在 Windows Server 上使用 Azure 共享磁盘部署 SAP IQ NLS HA 解决方案](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/deploy-sap-iq-nls-ha-solution-using-azure-shared-disk-on-windows/ba-p/2433089)一文介绍了使用 Azure 共享磁盘的 SAP IQ 部署体系结构。

- Azure NetApp 文件

  Linux 上的 SAP IQ 部署可以将 [Azure NetApp 文件](../../../azure-netapp-files/azure-netapp-files-introduction.md)用作文件系统（NFS 协议），以安装独立解决方案或高可用性解决方案。 此存储产品/服务并非在所有区域都可用。 有关最新信息，请参阅[各区域的产品可用性](https://azure.microsoft.com/global-infrastructure/services/)网页。 [在 SUSE Linux Enterprise Server 上使用 Azure NetApp 文件部署 SAP IQ-NLS HA 解决方案](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/deploy-sap-iq-nls-ha-solution-using-azure-netapp-files-on-suse/ba-p/1651172)一文介绍了使用 Azure NetApp 文件的 SAP IQ 部署体系结构。

下表根据操作系统列出了每种存储类型的建议： 

| 存储类型        | Windows | Linux |
| ------------------- | ------- | ----- |
| Azure 托管磁盘 | 是     | 是   |
| Azure 共享磁盘  | 是     | 否    |
| Azure NetApp 文件  | 否      | 是   |

### <a name="networking"></a>网络

Azure 提供了一个网络基础结构，允许映射可为使用 SAP IQ 作为近线存储的 SAP BW 系统实现的所有场景。 这些场景包括连接到本地系统、连接到不同虚拟网络中的系统等。 有关详细信息，请参阅[适用于 SAP 工作负载的 Microsoft Azure 网络](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/planning-guide.md#microsoft-azure-networking)。

## <a name="deploy-sap-iq-on-windows"></a>在 Windows 上部署 SAP IQ

### <a name="server-preparation-and-installation"></a>服务器准备和安装

若要利用 Windows 上的 SAP IQ 为 NLS 实现准备服务器，可以在 [SAP 说明 2780668 - SAP First Guidance - 使用 SAP IQ 的 BW NLS 实现](https://launchpad.support.sap.com/#/notes/0002780668)中获取最新信息。 它包含关于 SAP BW 系统先决条件、SAP IQ 文件系统布局、安装、配置后任务以及 SAP BW NLS 与 SAP IQ 集成的综合信息。

### <a name="high-availability-deployment"></a>高可用性部署

SAP IQ 同时支持单工和多路复用体系结构。 对于 NLS 解决方案，只能使用和评估单工服务器体系结构。 单工是在单个虚拟机上运行的单个 SAP IQ 服务器实例。 

从技术上讲，可使用多路复用服务器体系结构实现 SAP IQ 高可用性，但多路复用体系结构并不符合 NLS 解决方案的要求。 对于单工服务器体系结构，SAP 不提供任何用于在高可用性配置中运行 SAP IQ 的功能或过程。 

若要在 Windows 上为单工服务器体系结构设置 SAP IQ 高可用性，你需要设置一个自定义解决方案，它需要额外的配置，例如 Windows Server 故障转移群集和共享磁盘。 [在 Windows Server 上使用 Azure 共享磁盘部署 SAP IQ NLS HA 解决方案](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/deploy-sap-iq-nls-ha-solution-using-azure-shared-disk-on-windows/ba-p/2433089)中详细介绍了一种适用于 Windows 上的 SAP IQ 的自定义解决方案。

### <a name="backup-and-restore"></a>备份和还原

在 Azure 中，可按照 [SAP IQ 管理：备份、还原和数据恢复](https://help.sap.com/viewer/a893f37e84f210158511c41edb6a6367/16.1.4.7/5b8309b37f4e46b089465e380c24df59.html)中的说明来计划 SAP IQ 数据库备份。 SAP IQ 提供以下类型的数据库备份。 可以在[备份场景](https://help.sap.com/viewer/a893f37e84f210158511c41edb6a6367/16.1.4.7/a880dc1f84f21015af84f1a6b629dd7a.html)中找到有关每种备份类型的详细信息。

- 完整备份：创建数据库的完整副本。 
- 增量备份：复制自上次任何类型的备份以来的所有事务。 
- 自完整备份后增量备份：备份自上次完整备份以来对数据库进行的所有更改。
- 虚拟备份：从 SAP IQ 存储中复制数据库的所有内容（表数据和元数据除外）。

根据 SAP IQ 数据库的大小，你可以在任何备份场景中计划数据库备份。 但如果将 SAP IQ 与 SAP 提供的 NLS 接口一起使用，则你可能希望将 SAP IQ 数据库的备份过程自动化。 自动化可确保 SAP IQ 数据库能够始终恢复到一致的状态，而不会丢失在主数据库与 SAP IQ 数据库之间移动的数据。 有关为 SAP IQ 近线存储设置自动化的详细信息，请参阅 [SAP 说明 2741824 - 如何为 SAP IQ 冷存储/近线存储设置备份自动化](https://launchpad.support.sap.com/#/notes/2741824)。 

对于大型 SAP IQ 数据库，可使用虚拟备份。 有关详细信息，请参阅[虚拟备份](https://help.sap.com/viewer/a893f37e84f210158511c41edb6a6367/16.1.4.7/a880672184f21015a08dceedc7d19776.html)、[在 SAP Sybase IQ 中引入虚拟备份](https://wiki.scn.sap.com/wiki/display/SYBIQ/Introduction+Virtual+BackUp+(+general++back+up+method+)+in+SAP+Sybase+IQ)。 另请参阅 [SAP 说明 2461985 - 如何备份大型 SAP IQ 数据库](https://launchpad.support.sap.com/#/notes/0002461985)。

如果在 Windows 上使用网络驱动器（SMB 协议）备份和还原 SAP IQ 服务器，请确保使用 UNC 路径进行备份。 使用 UNC 路径进行备份和还原时，需要三个反斜杠 (`\\\`)：

```sql
BACKUP DATABASE FULL TO '\\\sapiq.internal.contoso.net\sapiq-backup\backup\data\<filename>'
```

## <a name="deploy-sap-iq-on-linux"></a>在 Linux 上部署 SAP IQ

### <a name="server-preparation-and-installation"></a>服务器准备和安装

若要利用 Linux 上的 SAP IQ 为 NLS 实现准备服务器，可以在 [SAP 说明 2780668 - SAP First Guidance - 使用 SAP IQ 的 BW NLS 实现](https://launchpad.support.sap.com/#/notes/0002780668)中获取最新信息。 它包含关于 SAP BW 系统先决条件、SAP IQ 文件系统布局、安装、配置后任务以及 SAP BW NLS 与 SAP IQ 集成的综合信息。

### <a name="high-availability-deployment"></a>高可用性部署

SAP IQ 同时支持单工和多路复用体系结构。 对于 NLS 解决方案，只能使用和评估单工服务器体系结构。 单工是在单个虚拟机上运行的单个 SAP IQ 服务器实例。 

从技术上讲，可使用多路复用服务器体系结构实现 SAP IQ 高可用性，但多路复用体系结构并不符合 NLS 解决方案的要求。 对于单工服务器体系结构，SAP 不提供任何用于在高可用性配置中运行 SAP IQ 的功能或过程。

若要在 Linux 上为单工服务器体系结构设置 SAP IQ 高可用性，你需要设置一个自定义解决方案，它需要额外的配置，例如 Pacemaker。 [在 SUSE Linux Enterprise Server 上使用 Azure NetApp 文件部署 SAP IQ-NLS HA 解决方案](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/deploy-sap-iq-nls-ha-solution-using-azure-netapp-files-on-suse/ba-p/1651172)中详细介绍了一种适用于 Linux 上的 SAP IQ 的自定义解决方案。

### <a name="backup-and-restore"></a>备份和还原

在 Azure 中，可按照 [SAP IQ 管理：备份、还原和数据恢复](https://help.sap.com/viewer/a893f37e84f210158511c41edb6a6367/16.1.4.7/5b8309b37f4e46b089465e380c24df59.html)中的说明来计划 SAP IQ 数据库备份。 SAP IQ 提供以下类型的数据库备份。 可以在[备份场景](https://help.sap.com/viewer/a893f37e84f210158511c41edb6a6367/16.1.4.7/a880dc1f84f21015af84f1a6b629dd7a.html)中找到有关每种备份类型的详细信息。

- 完整备份：创建数据库的完整副本。 
- 增量备份：复制自上次任何类型的备份以来的所有事务。 
- 自完整备份后增量备份：备份自上次完整备份以来对数据库进行的所有更改。
- 虚拟备份：从 SAP IQ 存储中复制数据库的所有内容（表数据和元数据除外）。

根据 SAP IQ 数据库的大小，你可以在任何备份场景中计划数据库备份。 但如果将 SAP IQ 与 SAP 提供的 NLS 接口一起使用，则你可能希望将 SAP IQ 数据库的备份过程自动化。 自动化可确保 SAP IQ 数据库能够始终恢复到一致的状态，而不会丢失在主数据库与 SAP IQ 数据库之间移动的数据。 有关为 SAP IQ 近线存储设置自动化的详细信息，请参阅 [SAP 说明 2741824 - 如何为 SAP IQ 冷存储/近线存储设置备份自动化](https://launchpad.support.sap.com/#/notes/2741824)。 

对于大型 SAP IQ 数据库，可使用虚拟备份。 有关详细信息，请参阅[虚拟备份](https://help.sap.com/viewer/a893f37e84f210158511c41edb6a6367/16.1.4.7/a880672184f21015a08dceedc7d19776.html)、[在 SAP Sybase IQ 中引入虚拟备份](https://wiki.scn.sap.com/wiki/display/SYBIQ/Introduction+Virtual+BackUp+(+general++back+up+method+)+in+SAP+Sybase+IQ)。 另请参阅 [SAP 说明 2461985 - 如何备份大型 SAP IQ 数据库](https://launchpad.support.sap.com/#/notes/0002461985)。

## <a name="disaster-recovery"></a>灾难恢复

本部分介绍为 SAP IQ NLS 解决方案提供灾难恢复 (DR) 保护的策略。 它对[为 SAP 设置灾难恢复](../../../site-recovery/site-recovery-sap.md)一文进行了补充，该文档代表了总体 SAP DR 方法的主要资源。 该文档中所述的过程是在抽象级别呈现的。 你需要验证确切的步骤，并全面地测试你的 DR 策略。 

对于 SAP IQ，请参阅 [SAP 说明 2566083](https://launchpad.support.sap.com/#/notes/0002566083)，其中介绍了安全实施 DR 环境的方法。 在 Azure 中，你还可以将 [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md) 用于 SAP IQ DR 策略。 SAP IQ DR 的策略取决于它在 Azure 中的部署方式，并且它还应该与 SAP BW 系统配合工作。 

### <a name="standalone-deployment-of-sap-iq"></a>SAP IQ 的独立部署

如果你已将 SAP IQ 安装为独立系统（该系统没有任何应用程序级冗余或高可用性），但业务需要 DR 设置，则连接到虚拟机，则附加到虚拟机的所有磁盘（Azure 托管磁盘）都将是本地磁盘。 
  
可以使用 [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md) 来复制次要区域中的独立 SAP IQ 虚拟机。 它会将服务器及附加的所有托管磁盘复制到次要区域，以便在发生灾难或停机时，你可以轻松地将故障转移到复制的环境中，并继续工作。 若要开始将所有 SAP IQ VM 复制到 Azure DR 区域，请遵照[将虚拟机复制到 Azure](../../../site-recovery/azure-to-azure-tutorial-enable-replication.md) 中的指导。 

### <a name="highly-available-deployment-of-sap-iq"></a>SAP IQ 的高可用性部署

如果你已将 SAP IQ 安装为高可用性系统，其中 SAP IQ 二进制文件和数据库文件位于 Azure 共享磁盘（仅限 Windows）或 Azure NetApp 文件等网络驱动器（仅限 Linux）上，则你需要确定：

- 是否需要在 DR 站点上使用相同的高可用性 SAP IQ 系统。
- 独立 SAP IQ 实例能否满足你的业务要求。 
  
如果需要在 DR 站点上使用独立的 SAP IQ 实例，可使用 [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md) 将主要 SAP IQ 虚拟机复制到次要区域中。 它会将服务器及附加的所有本地托管磁盘复制到次要区域，但不会复制 Azure 共享磁盘或诸如 Azure NetApp 文件之类的网络驱动器。 
  
若要从 Azure 共享磁盘或网络驱动器复制数据，可使用任何基于文件的复制工具在 Azure 区域之间复制数据。 有关如何在另一个区域中复制 Azure NetApp 文件的详细信息，请参阅[关于 Azure NetApp 文件的常见问题解答](../../../azure-netapp-files/azure-netapp-files-faqs.md#how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region)。

## <a name="next-steps"></a>后续步骤

- [为多层 SAP 应用部署设置灾难恢复](../../../site-recovery/site-recovery-sap.md)
- [适用于 SAP 的 Azure 虚拟机规划和实施](planning-guide.md)
- [适用于 SAP 的 Azure 虚拟机部署](deployment-guide.md)
