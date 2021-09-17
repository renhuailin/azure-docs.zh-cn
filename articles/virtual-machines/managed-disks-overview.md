---
title: Azure 磁盘存储概述
description: 概要介绍 Azure 托管磁盘，这种磁盘在你使用 VM 时为你处理存储帐户。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 06/29/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: contperf-fy21q1
ms.openlocfilehash: 801e9ed20c86c59d9c72043ff192a3500bae9a5f
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122696131"
---
# <a name="introduction-to-azure-managed-disks"></a>Azure 托管磁盘简介

**适用于：** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 灵活规模集 :heavy_check_mark: 统一规模集

Azure 托管磁盘是由 Azure 托管并与 Azure 虚拟机配合使用的块级存储卷。 托管磁盘类似于本地服务器中的物理磁盘，但它是虚拟化的。 使用托管磁盘时，只需指定磁盘大小、磁盘类型并预配磁盘即可。 预配此磁盘后，Azure 会处理剩余磁盘。

可用的磁盘类型包括超级磁盘、高级固态硬盘 (SSD)、标准 SSD 和标准硬盘驱动器 (HDD)。 有关每种磁盘类型的信息，请参阅[选择适用于 IaaS VM 的磁盘类型](disks-types.md)。

## <a name="benefits-of-managed-disks"></a>托管磁盘的好处

接下来让我们看一下使用托管磁盘可以获得的一些好处。

### <a name="highly-durable-and-available"></a>高度持久和可用

托管磁盘具备 99.999% 的可用性。 托管磁盘实现这一点的方式是：提供三个包含数据的副本，确保高持久性。 如果其中一个或两个副本出现问题，剩下的副本能够确保数据的持久性和对故障的高耐受性。 此架构有助于 Azure 为基础结构即服务 (IaaS) 磁盘持续提供企业级的持久性，年化故障率为 0%，达到行业领先水平。

### <a name="simple-and-scalable-vm-deployment"></a>简单且可缩放的 VM 部署

托管磁盘支持在每个区域中的一个订阅中创建最多 50,000 个同一类型的 VM 磁盘，这样就可以在单个订阅中创建数以千计的 VM。 此功能允许使用 Marketplace 映像，在一个虚拟机规模集中创建多达 1000 台 VM，进一步增加[虚拟机规模集](../virtual-machine-scale-sets/overview.md)的可伸缩性。

### <a name="integration-with-availability-sets"></a>集成可用性集

托管磁盘集成可用性集，可确保[可用性集中的 VM](./availability-set-overview.md) 的磁盘彼此之间完全隔离以避免单点故障。 磁盘自动放置于不同的存储缩放单元（模块）。 如果某个模块因硬件或软件故障而失败，则只有其磁盘在该模块上的 VM 实例会失败。 例如，假定某个应用程序在 5 台 VM 上运行并且这些 VM 位于一个可用性集中。 这些 VM 的磁盘不会存储在同一个模块中，因此，如果一个模块失败，该应用程序的其他实例可以继续运行。

### <a name="integration-with-availability-zones"></a>与可用性区域集成

托管磁盘支持[可用性区域](../availability-zones/az-overview.md)，这是一种高可用性产品/服务，可以保护应用程序免受数据中心故障的影响。 可用性区域是 Azure 区域中独特的物理位置。 每个区域由一个或多个数据中心组成，这些数据中心配置了独立电源、冷却和网络。 为确保能够进行复原，所有已启用的地区中都必须至少有三个单独的区域。 Azure 凭借可用性区域提供一流的 99.99% VM 运行时间 SLA。

### <a name="azure-backup-support"></a>Azure 备份支持

若要防范区域灾难，可以使用 [Azure 备份](../backup/backup-overview.md)创建具有基于时间的备份和备份保留策略的备份作业。 这样就可以随意执行 VM 或托管磁盘还原。 目前，Azure 备份支持高达 32 太字节 (TiB) 的磁盘大小。 [详细了解](../backup/backup-support-matrix-iaas.md) Azure VM 备份支持。

#### <a name="azure-disk-backup"></a>Azure 磁盘备份

Azure 备份提供 Azure 磁盘备份（预览版）作为基于云的本机备份解决方案，可保护托管磁盘中的数据。 它是一个简单、安全且经济高效的解决方案，使你能够通过几个步骤为托管磁盘配置保护。 Azure 磁盘备份提供了一个统包式解决方案，通过自动定期创建快照并使用备份策略在配置的时间段内保留快照，为托管磁盘提供快照生命周期管理。 有关 Azure 磁盘备份的详细信息，请参阅 [Azure 磁盘备份概述（预览版）](../backup/disk-backup-overview.md)。

### <a name="granular-access-control"></a>粒度访问控制

可以使用 [Azure 基于角色的访问控制 (Azure RBAC)](../role-based-access-control/overview.md) 将托管磁盘的特定权限分配给一个或多个用户。 托管磁盘公开了各种操作，包括读取、写入（创建/更新）、删除，以及检索磁盘的[共享访问签名 (SAS) URI](../storage/common/storage-sas-overview.md)。 可以仅将某人员执行其工作所需的操作的访问权限授予该人员。 例如，如果不希望某人员将某个托管磁盘复制到存储帐户，则可以选择不授予对该托管磁盘的导出操作的访问权限。 类似地，如果不希望某人员使用 SAS URI 复制某个托管磁盘，则可以选择不授予对该托管磁盘的该权限。

### <a name="upload-your-vhd"></a>上传 vhd

通过直接上传，可以轻松地将 vhd 传输到 Azure 托管磁盘。 以前，必须遵循一个更复杂的过程，包括将数据暂存到存储帐户中。 现在，步骤更少了。 可以更方便地将本地 VM 上传到 Azure、上传到大型托管磁盘，并简化了备份和还原过程。 通过允许你直接将数据上传到托管磁盘而不将它们附加到 VM，还降低了成本。 可以使用直接上传来上传最大为 32 TiB 的 vhd。

若要了解如何将 vhd 传输到 Azure，请参阅 [CLI](linux/disks-upload-vhd-to-managed-disk-cli.md) 或 [PowerShell](windows/disks-upload-vhd-to-managed-disk-powershell.md) 文章。

## <a name="security"></a>安全性

### <a name="private-links"></a>专用链接

对托管磁盘的专用链接支持可用于导入或导出网络内部的托管磁盘。 通过专用链接，可以为未连接的托管磁盘和快照生成有时间限制的共享访问签名 (SAS) URI，将数据导出到其他区域以进行区域扩展、灾难恢复和取证分析。 还可以使用 SAS URI 将 VHD 从本地直接上传到空磁盘。 现在，可以利用[专用链接](../private-link/private-link-overview.md)将托管磁盘的导出和导入限制于 Azure 虚拟网络。 通过专用链接，可以确保数据仅在安全的 Microsoft 骨干网络内传输。

若要了解如何启用专用链接以导入或导出托管磁盘，请参阅 [CLI](linux/disks-export-import-private-links-cli.md) 或[门户](disks-enable-private-links-for-import-export-portal.md)文章。

### <a name="encryption"></a>加密

托管磁盘提供两种不同的加密。 第一种是服务器端加密 (SSE)，由存储服务执行。 第二种是 Azure 磁盘加密 (ADE)，可以在 VM 的 OS 和数据磁盘上启用。

#### <a name="server-side-encryption"></a>服务器端加密

服务器端加密可提供静态加密并保护数据，让你的组织能够信守安全性与合规性方面所做的承诺。 默认情况下，在所有托管磁盘可用的区域中，所有托管磁盘、快照和映像都启用了服务器端加密。 （另一方面，服务器端加密不会加密临时磁盘，除非你在主机上启用加密；请参阅[磁盘角色：临时磁盘](#temporary-disk)）。

可以让 Azure 为你管理密钥（平台托管的密钥），也可以自行管理密钥（客户管理的密钥）。 请访问 [Azure 磁盘存储的服务器端加密](./disk-encryption.md)以了解详细信息。


#### <a name="azure-disk-encryption"></a>Azure 磁盘加密

Azure 磁盘加密允许加密 IaaS 虚拟机使用的 OS 磁盘和数据磁盘。 此加密包括托管磁盘。 对于 Windows，驱动器是使用行业标准 BitLocker 加密技术加密的。 对于 Linux，磁盘是使用 DM-Crypt 技术加密的。 加密过程与 Azure Key Vault 集成，可让你控制和管理磁盘加密密钥。 有关详细信息，请参阅[适用于 Linux VM 的 Azure 磁盘加密](linux/disk-encryption-overview.md)或[适用于 Windows VM 的 Azure 磁盘加密](windows/disk-encryption-overview.md)。

## <a name="disk-roles"></a>磁盘角色

在 Azure 中有三个主要磁盘角色：数据磁盘、OS 磁盘和临时磁盘。 这些角色将映射到附加到虚拟机的磁盘。

![操作中的磁盘角色](media/virtual-machines-managed-disks-overview/disk-types.png)

### <a name="data-disk"></a>数据磁盘

数据磁盘是附加到虚拟机的托管磁盘，用于存储应用程序数据或其他需要保留的数据。 数据磁盘注册为 SCSI 驱动器并且带有所选择的字母标记。 每个数据磁盘的最大容量为 32,767 gibibytes (GiB)。 虚拟机的大小决定了可附加的磁盘数目，以及可用来托管磁盘的存储类型。

### <a name="os-disk"></a>OS 磁盘

每个虚拟机都附加了一个操作系统磁盘。 该 OS 磁盘有一个预先安装的 OS，是在创建 VM 时选择的。 此磁盘包含启动卷。

此磁盘最大容量为 4,095 GiB。

### <a name="temporary-disk"></a>临时磁盘

大多数 VM 都包含一个临时磁盘，该磁盘不是托管磁盘。 临时磁盘为应用程序和进程提供短期存储，仅用于存储页面或交换文件等数据。 在[维护事件](./understand-vm-reboots.md)期间或[重新部署 VM](/troubleshoot/azure/virtual-machines/redeploy-to-new-node-windows?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 时，临时磁盘上的数据可能会丢失。 在以标准方式成功重启 VM 期间，临时磁盘上的数据将保留。 有关无临时磁盘的 VM 的详细信息，请参阅[无本地临时磁盘的 Azure VM 规格](azure-vms-no-temp-disk.yml)。

在 Azure Linux VM 上，临时磁盘通常为“/dev/sdb”；在 Windows VM 上，临时磁盘默认为 D:。 务器端加密不会加密临时磁盘，除非你在主机上启用加密。

## <a name="managed-disk-snapshots"></a>托管磁盘快照

托管磁盘快照是托管磁盘的只读崩溃一致性完整副本，默认情况下它作为标准托管磁盘进行存储。 使用快照，可以在任意时间点备份托管磁盘。 这些快照独立于源磁盘而存在，并可用来创建新的托管磁盘。 

基于已使用大小对快照计费。 例如，如果创建预配容量为 64 GiB 且实际使用数据大小为 10 GiB 的托管磁盘的快照，则仅针对已用数据大小 10 GiB 对该快照计费。 可以通过查看 [Azure 使用情况报告](../cost-management-billing/understand/review-individual-bill.md)来了解快照的已使用大小。 例如，如果快照的已用数据大小为 10 GiB，则每日使用情况报告将显示 10 GiB/(31 天) = 0.3226 作为已使用数量。

若要了解有关如何为托管磁盘创建快照的详细信息，请查看下列资源：

- [在 Windows 中创建托管磁盘的快照](windows/snapshot-copy-managed-disk.md)
- [在 Linux 中创建托管磁盘的快照](linux/snapshot-copy-managed-disk.md)

### <a name="images"></a>映像

托管磁盘还支持创建托管自定义映像。 可以从存储帐户中的自定义 VHD 创建映像或者直接从通用化 (sysprepped) VM 创建映像。 此过程捕获单个映像。 该映像包含与 VM 关联的所有托管磁盘，包括 OS 磁盘和数据磁盘。 该托管自定义映像支持使用自定义映像创建数百台 VM，且不需要复制或管理任何存储帐户。

有关创建映像的信息，请查看以下文章：

- [如何在 Azure 中捕获通用 VM 的托管映像](windows/capture-image-resource.md)
- [如何使用 Azure CLI 生成和捕获 Linux 虚拟机](linux/capture-image.md)

#### <a name="images-versus-snapshots"></a>映像与快照

了解映像与快照之间的区别很重要。 使用托管磁盘，可以创建已解除分配的通用 VM 的映像。 此映像包括附加到该 VM 的所有磁盘。 可以使用此映像创建 VM，它包括所有磁盘。

快照是磁盘在创建快照那一刻的副本。 它仅应用于一个磁盘。 如果 VM 有一个磁盘（OS 磁盘），则可以为其创建快照或映像，并且可以通过该快照或映像创建 VM。

除了所包含的磁盘，快照无法感知任何其他磁盘。 因此，如果在要求对多个磁盘进行协调的方案（例如条带化方案）中使用，则会出现问题。 快照彼此之间将需要相互协调，而目前并不支持此功能。

## <a name="disk-allocation-and-performance"></a>磁盘分配和性能

下图描绘了如何使用三级预配系统为磁盘实时分配带宽和 IOPS：

![显示带宽和 IOPS 分配情况的三级预配系统](media/virtual-machines-managed-disks-overview/real-time-disk-allocation.png)

第一级预配设置每个磁盘的 IOPS 和带宽分配。  在第二级，计算服务器主机实现 SSD 预配，将其仅应用到存储在服务器的 SSD 上的数据。该 SSD 包括具有缓存功能（ReadWrite 和 ReadOnly）的磁盘以及本地磁盘和临时磁盘。 最后，在第三级进行 VM 网络预配，这适用于计算主机发送给 Azure 存储后端的任何 I/O。 使用此方案时，VM 的性能取决于许多因素，例如 VM 如何使用本地 SSD、附加的磁盘数，以及所附加的磁盘的性能和缓存类型。

下面是有关这些限制的一个示例：Standard_DS1v1 VM 无法达到 P30 磁盘可能达到的 5,000 IOPS，不管它是否进行缓存，因为在 SSD 和网络级别存在限制：

![Standard_DS1v1 示例分配](media/virtual-machines-managed-disks-overview/example-vm-allocation.png)

Azure 使用优先的网络通道进行磁盘流量传输，优先于其他低优先级网络流量。 在出现网络争用时，这有助于磁盘保持预期的性能。 类似地，Azure 存储在后台使用自动负载均衡来处理资源争用和其他问题。 Azure 存储在你创建磁盘时分配所需资源，并应用主动和被动资源均衡来处理流量级别。 这进一步确保磁盘保持其预期的 IOPS 和吞吐量目标。 可以根据需要使用 VM 级别和磁盘级别的指标来跟踪性能和设置警报。

请参阅[为实现高性能而设计](premium-storage-performance.md)一文，了解优化 VM + 磁盘配置以实现所需性能的最佳做法

## <a name="next-steps"></a>后续步骤

如果你想要详细介绍托管磁盘的视频，请查看：[使用托管磁盘提高 Azure VM 复原能力](https://channel9.msdn.com/Blogs/Azure/Managed-Disks-for-Azure-Resiliency)。

在有关磁盘类型的文章中，详细了解 Azure 提供的各个磁盘类型、哪个类型符合自己的需求，并了解其性能目标。

> [!div class="nextstepaction"]
> [选择适用于 IaaS VM 的磁盘类型](disks-types.md)