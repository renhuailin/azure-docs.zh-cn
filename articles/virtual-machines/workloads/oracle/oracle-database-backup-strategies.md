---
title: Azure 虚拟机备份策略中的 Oracle Database
description: 用于在 Azure Linux VM 环境中备份 Oracle Database 的选项。
author: cro27
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 01/28/2021
ms.author: cholse
ms.reviewer: dbakevlar
ms.openlocfilehash: 2a61ed8f32fb85c3500ebf191c8bd53808b75eb9
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122696883"
---
# <a name="oracle-database-in-azure-linux-vm-backup-strategies"></a>Azure Linux VM 备份策略中的 Oracle Database

**适用于：** :heavy_check_mark: Linux VM 

数据库备份可保护数据库，防止因存储组件故障和数据中心故障而导致的数据丢失。 通过利用备份，还可以从人为错误中恢复，以及克隆数据库用于开发或测试目的。 

在 Azure 中，所有存储都是高度冗余的，一个或多个磁盘丢失并不会导致数据库中断，所以备份的最常用作用是防范人为错误，以及出于监管目的而进行的克隆操作或数据保留。 在未使用 DataGuard 等灾难恢复技术的情况下，还可以利用备份来防范区域性服务中断。 在这种情况下，必须使用异地冗余复制将备份存储在不同的 Azure 区域中，以便在主数据库区域之外使用。

## <a name="azure-storage"></a>Azure 存储 

[Azure 存储服务](../../../storage/common/storage-introduction.md)是 Microsoft 提供的适用于现代数据存储方案的云存储解决方案。 Azure 存储提供了许多服务，可用于将外部存储装载到 Azure Linux VM，还适合用作 Oracle Database 的备份介质。 若要启动备份或还原操作，并从/向 Azure 存储服务中复制备份，需使用 Oracle RMAN 之类的备份工具。
 
Azure 存储服务具有以下优点：

-  持久且具有高可用性。 冗余可确保数据在发生短暂的硬件故障时是安全的。 默认情况下，所有存储都有三重镜像。 还可以选择在各个数据中心或地理区域之间复制数据，从而在发生本地灾难或自然灾害时提供额外的保护。 以此方式复制的数据在发生意外中断时将保持高可用性。

-  安全。 该服务会对写入到 Azure 存储帐户的所有数据进行加密。 Azure 存储可以精细地控制谁可以访问你的数据。

-  可缩放。 Azure 存储设计为可大规模缩放以满足当今的应用程序在数据存储和性能方面的需求。

-  已托管。 Azure 为你处理硬件维护、更新和关键问题。

-  易访问。 可以通过 HTTP 或 HTTPS 从世界上的任何位置访问 Azure 存储中的数据。 Microsoft 以各种语言（包括 NET、Java、Node.js、Python、PHP、Ruby、Go 和其他语言）提供了适用于 Azure 存储的客户端库以及成熟的 REST API。 Azure 存储支持通过 Azure PowerShell 或 Azure CLI 运行脚本。 而且，Azure 门户和 Azure 存储资源管理器提供了用于处理数据的简单可视化解决方案。

Azure 存储平台包含以下数据服务，这些服务适合用作 Oracle Database 的备份介质：

-  Azure Blob：适用于文本和二进制数据的可大规模缩放的对象存储。 还包括通过 Data Lake Storage Gen2 支持大数据分析。

-  Azure 文件：适用于云或本地部署的托管文件共享。

-  Azure 磁盘：Azure VM 的块级存储卷。

### <a name="cross-regional-storage-mounting"></a>跨区域存储装载

跨区域访问备份存储的功能是 BCDR 的一个重要方面，可用于从备份中克隆数据库到不同的地理区域。 Azure 云存储提供了五个级别的[冗余](../../../storage/common/storage-redundancy.md)
- 本地冗余存储 [(LRS)](../../../storage/common/storage-redundancy.md#locally-redundant-storage)，在主要区域的单个物理位置内复制数据三次。  
- 区域冗余存储 [(ZRS)](../../../storage/common/storage-redundancy.md#zone-redundant-storage)，数据在主要区域中受 LRS 保护，可以跨主要区域中的三个可用性区域 (AZ) 进行同步复制。 每个 AZ 也都受 LRS 保护。 
- 异地冗余存储 [(GRS)](../../../storage/common/storage-redundancy.md#geo-redundant-storage)，数据在主要区域中受 LRS 保护，并会异步复制到次要区域，在其中同样也受 LRS 保护。
- 异地区域冗余存储 [(GZRS)](../../../storage/common/storage-redundancy.md#geo-redundant-storage)，通过使用 ZRS，跨主要区域中的三个 Azure 可用性区域来同步复制数据。 然后，它将数据异步复制到次要区域中的单个物理位置。 所有位置中的数据也都受 LRS 保护。 
- 读取访问异地冗余存储 [(RA-GRS)](../../../storage/common/storage-redundancy.md#geo-redundant-storage) 和读取访问异地区域冗余存储 [(RA-GZRS)](../../../storage/common/storage-redundancy.md#geo-redundant-storage)，允许随时对复制到次要区域的数据进行只读访问。 

#### <a name="blob-storage-and-file-storage"></a>Blob 存储和文件存储

当 Azure 文件存储搭配 SMB 或 NFS 4.1（预览版）协议一起使用，以作为备份存储进行装载时，请注意 Azure 文件存储并不支持读取访问异地冗余存储 (RA-GRS) 和读取访问异地区域冗余存储 (RA-GZRS)。 

此外，如果备份存储要求超过 5 TiB，则 Azure 文件存储需要启用[大文件共享功能](../../../storage/files/storage-files-planning.md)，而该功能并不支持 GRS 或 GZRS 冗余 - 仅支持 LRS。 

使用 NFS 3.0（预览版）协议装载的 Azure Blob 目前仅支持 LRS 和 ZRS 冗余。  

对于配置了任何冗余选项的 Azure Blob，可以使用 Blobfuse 进行装载。

#### <a name="recovery-services-vault"></a>恢复服务保管库 

恢复服务保管库是一个管理实体，它会存储一段时间内创建的恢复点，并提供用于执行备份相关操作的接口。 其中包括按需备份、执行还原和创建备份策略。
Azure 备份会自动处理保管库的存储。 但需要指定在创建时复制存储的方式。 各项在保管库中受到保护后，将无法更改。 对于区域冗余，请选择异地冗余设置。

如果要还原到次要区域，[Azure 配对区域](../../../best-practices-availability-paired-regions.md)需启用[跨区域还原](../../../backup/backup-create-rs-vault.md)功能。 启用跨区域还原时，备份存储将从 GRS 移动到读取访问异地冗余存储 (RA-GRS)。 

### <a name="azure-blob-storage"></a>Azure Blob 存储

Azure Blob 存储是一项非常经济高效、持久且安全的基于云的存储服务，可用于存储大量的非结构化数据，是用于 Oracle Database 备份的理想媒介。 可以使用 NFS v3.0 协议或 Blobfuse (Linux FUSE) 将 Azure Blob 存储装载到 Azure Linux VM。

#### <a name="azure-blob-blobfuse"></a>Azure Blob Blobfuse

[Blobfuse](../../../storage/blobs/storage-how-to-mount-container-linux.md) 是一个开放源代码项目，旨在提供一个受 Azure Blob 存储支持的虚拟文件系统。 它使用 libfuse 开放源代码库与 Linux FUSE 内核模块通信，并通过 Azure 存储 Blob REST API 实施文件系统操作。 Blobfuse 目前适用于 Ubuntu 和 Centos/RedHat 分发版。 还可以借助 [CSI 驱动程序](https://github.com/kubernetes-sigs/blob-csi-driver)用于 Kubernetes。 

虽然是跨所有 Azure 区域通用，并且兼容各种存储帐户类型（包括：常规用途 v1/v2 和 Azure Data Lake Store Gen2），可 Blobfuse 能提供的性能似乎还比不上 SMB 或 NFS 等替代协议。 为了适合用作数据库备份介质，建议使用 SMB 或 [NFS](../../../storage/blobs/storage-how-to-mount-container-linux.md) 协议来装载 Azure Blob 存储。 

#### <a name="azure-blob-nfs-v30-preview"></a>Azure Blob NFS v3.0（预览版）

对于网络文件系统 (NFS) v3.0 协议的 Azure 支持目前处于预览版阶段。 [NFS](../../../storage/blobs/network-file-system-protocol-support.md) 支持 Windows 和 Linux 客户端将 Blob 存储容器装载到 Azure VM。 

在以下区域，NFS 3.0 公共预览版支持具有标准层性能的 GPV2 存储帐户： 
- 澳大利亚东部
- 韩国中部
- 美国中南部。 

为了确保网络安全，用于 NFS 装载的存储帐户必须包含在 VNet 中。 目前，在启用了 NFS 3.0 协议支持的帐户中，暂不支持 Azure Active Directory (AD) 安全性和访问控制列表 (ACL)。

### <a name="azure-files"></a>Azure 文件

[Azure 文件存储](../../../storage/files/storage-files-introduction.md)是一种基于云的、完全托管的分布式文件系统，可以装载到本地或基于云的 Windows、Linux 或 macOS 客户端上。

Azure 文件存储在云中提供完全托管的跨平台文件共享，这些共享项可通过服务器消息块 (SMB) 协议和网络文件系统 (NFS) 协议（预览版）进行访问。 Azure 文件存储目前不支持多协议访问，因此每个共享项只能是 NFS 共享或 SMB 共享。 建议在创建 Azure 文件共享之前，先确定最能满足需求的协议。

还可以通过 Azure 备份到恢复服务保管库来保护 Azure 文件共享，向 Oracle RMAN 备份提供额外一层保护。

#### <a name="azure-files-nfs-v41-preview"></a>Azure 文件存储 NFS v4.1（预览版）

可以使用网络文件系统 (NFS) v4.1 协议，将 Azure 文件共享装载到 Linux 分发版中。 在预览版中，支持的功能有很多限制。 有关详细信息，请参阅[装入 AZURE NFS 文件共享（预览版）](../../../storage/files/storage-files-how-to-mount-nfs-shares.md)。 

另外在预览版中，Azure 文件存储 NFS v4.1 也限制在以下[区域](../../../storage/files/storage-files-how-to-mount-nfs-shares.md)：
- 美国东部（LRS 和 ZRS）
- 美国东部 2
- 美国西部 2
- 西欧
- 东南亚
- 英国南部
- 澳大利亚东部

#### <a name="azure-files-smb-30"></a>Azure 文件存储 SMB 3.0

可以使用 SMB 内核客户端在 Linux 分发版中装载 Azure 文件共享。 在 Linux 分发版上，可以使用通用 Internet 文件系统 (CIFS) 协议，这是 SMB 的一种方言。 在 Linux VM 上装载 Azure 文件存储 SMB 时，会将其装载为 CIFS 类型的文件系统，并且必须安装 CIFS 包。 

Azure 文件存储 SMB 广泛应用于所有 Azure 区域，显示出的性能特征与 NFS v3.0 和 v4.1 协议不相上下，因此是目前向 Azure Linux VM 提供备份存储介质的推荐方法。  

现有两种版本的 SMB 受支持，即 SMB 2.1 和 SMB 3.0，其中后者支持传输中加密，因而更受推荐。 但是，不同的 Linux 内核版本对 SMB 2.1 和 3.0 的支持程度也不同。 有关详细信息，请参阅[在 Linux 上装载 SMB Azure 文件共享](../../../storage/files/storage-how-to-use-files-linux.md)，确保你的应用程序支持 SMB 3.0。 

因为 Azure 文件存储的设计用途就是作为多用户文件共享服务，所以应对某些特性进行优化调整，使其更适合用作备份存储介质。 建议关闭缓存并设置所创建文件的用户和组 ID。

## <a name="azure-netapp-files"></a>Azure NetApp 文件

[Azure NetApp 文件](../../../azure-netapp-files/azure-netapp-files-solution-architectures.md)服务是 Azure VM 中适用于 Oracle Database 的完整存储解决方案。 以企业级、高性能、按流量计费的文件存储为基础构建而成，它支持任何工作负载类型，默认情况下具有高度可用性。 搭配 Oracle Direct NFS (dNFS) 驱动程序，Azure NetApp 文件为 Oracle Database 提供了一个高度优化的存储层。

在使用写时重定向 (RoW) 机制的基础存储系统上，Azure NetApp 文件可提供高效的基于存储的快照副本。 尽管快照副本的创建和还原速度非常快，但也只是第一道防御手段，可以满足任何给定组织所需的绝大部分还原操作，通常是从人为错误中恢复。 不过，快照副本并不是完整备份。 为了涵盖所有备份和还原要求，必须在远程地域中创建外部快照副本和/或其他备份副本，以防发生区域性服务中断。 若要了解有关在 Azure 上使用针对 Oracle Database 的 NetApp 文件的详细信息，请阅读此[报告](https://www.netapp.com/pdf.html?item=/media/17105-tr4780pdf.pdf)。

## <a name="azure-backup-service"></a>Azure 备份服务

[Azure 备份](../../../backup/backup-overview.md)是一种完全托管式 PaaS，提供了简单、安全且经济高效的解决方案来备份数据，并从 Microsoft Azure 云恢复数据。 Azure 备份可以在 Azure VM 上备份和还原本地客户端、Azure VM、Azure 文件存储共享，以及 SQL Server、Oracle、MySQL、PostreSQL 和 SAP HANA 数据库。 

Azure 备份提供独立且隔离的备份，可以防范原始数据的意外破坏。 备份存储在提供恢复点内置管理的[恢复服务保管库](../../../backup/backup-azure-recovery-services-vault-overview.md)中。 配置和可伸缩性很简单，备份经过优化，可以轻松地根据需要还原。 利用 Azure 云的基础功能和无限缩放功能，实现高可用性，无需维护和监视开销。 Azure 备份不会限制传输的入站或出站数据量，也不会收取数据传输费，无论是传输中还是静态数据都得以妥善保护。 

为了确保持续性，Azure 备份提供了多种类型的复制，使备份数据保持高可用性。

- 本地冗余存储 [(LRS)](../../../storage/common/storage-redundancy.md#locally-redundant-storage) 将数据中心的存储缩放单元中的数据复制三次（创建三个数据副本）。
- 异地冗余存储 [(GRS)](../../../storage/common/storage-redundancy.md#geo-redundant-storage) 是默认、也是推荐的复制选项。 GRS 将数据复制到离源数据主位置数英里之外的次要区域中。

使用 GRS 冗余创建的保管库包含用于配置[跨区域还原](../../../backup/backup-create-rs-vault.md#set-storage-redundancy)功能的选项，该选项可还原次要的 Azure 配对区域中的数据。

Azure 备份服务提供了一个[框架](../../../backup/backup-azure-linux-app-consistent.md)，可在 Windows 和 Linux VM 备份期间，保持 Oracle、MySQL、Mongo DB、SAP HANA 和 PostGreSQL 等应用程序的一致性，称为应用程序一致性快照。 这涉及到在拍摄磁盘快照之前调用一个前脚本（使应用程序处于静止状态），并在完成快照后调用后脚本（取消冻结应用程序的命令），以将应用程序恢复到正常模式。 虽然 GitHub 上提供了示例前脚本和后脚本，但这些脚本的创建和维护仍由你负责。 对于 Oracle，数据库必须处于 archivelog 模式以允许联机备份，相应的数据库开始和结束备份命令在前脚本和后脚本中运行，而这些脚本都是你必须创建和维护的。 

Azure 备份现在提供[增强型前脚本和后脚本框架](https://github.com/Azure/azure-linux-extensions/tree/master/VMBackup/main/workloadPatch/DefaultScripts)，在目前的预览版中，Azure 备份服务将为所选应用程序提供打包的前脚本和后脚本。 Azure 备份的用户只需要对应用程序进行命名，然后 Azure VM 备份将自动调用相关的前脚本和后脚本。 打包的前脚本和后脚本将由 Azure 备份团队进行维护，因此可以确保用户获得这些脚本的支持、所有权和有效性。 目前，增强型框架所支持的应用程序是 Oracle 和 MySQL，预计将来还会支持更多应用程序类型。 快照将是存储的完整副本，而不是增量备份或写时复制快照，因此它是还原数据库的有效介质。

## <a name="next-steps"></a>后续步骤

- [创建 Oracle Database 快速入门](oracle-database-quick-create.md)
- [将 Oracle Database 备份到 Azure 文件存储](oracle-database-backup-azure-storage.md)
- [使用 Azure 备份服务备份 Oracle Database](oracle-database-backup-azure-backup.md)
