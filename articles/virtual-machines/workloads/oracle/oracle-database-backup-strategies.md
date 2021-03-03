---
title: Azure 虚拟机备份策略中的 Oracle Database
description: 用于在 Azure Linux VM 环境中备份 Oracle 数据库的选项。
author: cro27
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 01/28/2021
ms.author: cholse
ms.reviewer: dbakevlar
ms.openlocfilehash: 8a1eb1c21663e0294cd384daa0ba644adf78007a
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101673212"
---
# <a name="oracle-database-in-azure-linux-vm-backup-strategies"></a>Azure Linux VM 备份策略中的 Oracle Database

数据库备份可保护数据库，防止因存储组件故障和数据中心故障而导致的数据丢失。 它们也可以是一种从人为错误恢复的方法，也可以是一种出于开发或测试目的克隆数据库的方法。 

在 Azure 中，由于所有存储都是高度冗余的，如果丢失一个或多个磁盘，则不会导致数据库服务中断，最常使用备份来防范人为错误，出于出于法规目的而克隆操作或数据保存。 它们也是防范区域性服务中断的一种方法，即 DataGuard 中未使用灾难恢复技术。 在这种情况下，必须使用异地冗余复制将备份存储在不同的 Azure 区域，以便在主数据库区域之外使用。

## <a name="azure-storage"></a>Azure 存储 

[Azure 存储服务](../../../storage/common/storage-introduction.md)是适用于新式数据存储方案的 Microsoft 云存储解决方案。 Azure 存储提供了许多服务，可用于将外部存储装载到 Azure Linux VM，适用于 Oracle 数据库的备份介质。 若要启动备份或还原操作并将备份复制到 Azure 存储服务，请使用 Oracle RMAN 之类的备份工具。
 
Azure 存储服务提供以下优势：

-  持久且具有高可用性。 冗余可确保数据在发生短暂的硬件故障时是安全的。 默认情况下，所有存储都是三重镜像。 还可以选择在各个数据中心或地理区域之间复制数据，从而在发生本地灾难或自然灾害时提供额外的保护。 以此方式复制的数据在发生意外中断时将保持高可用性。

-  安全。 该服务会对写入到 Azure 存储帐户的所有数据进行加密。 Azure 存储可以精细地控制谁可以访问你的数据。

-  可缩放。 Azure 存储设计为可大规模缩放以满足当今的应用程序在数据存储和性能方面的需求。

-  已托管。 Azure 为你处理硬件维护、更新和关键问题。

-  易访问。 可以通过 HTTP 或 HTTPS 从世界上的任何位置访问 Azure 存储中的数据。 Microsoft 以各种语言（包括 NET、Java、Node.js、Python、PHP、Ruby、Go 和其他语言）提供了适用于 Azure 存储的客户端库以及成熟的 REST API。 Azure 存储支持通过 Azure PowerShell 或 Azure CLI 运行脚本。 而且，Azure 门户和 Azure 存储资源管理器提供了用于处理数据的简单可视化解决方案。

Azure 存储平台包含以下数据服务，这些服务适合用作 Oracle 数据库的备份介质：

-  Azure Blob：适用于文本和二进制数据的可大规模缩放的对象存储。 还包括通过 Data Lake Storage Gen2 支持大数据分析。

-  Azure 文件：适用于云或本地部署的托管文件共享。

-  Azure 磁盘：Azure VM 的块级存储卷。

### <a name="cross-regional-storage-mounting"></a>跨区域存储装载

跨区域访问备份存储的功能是 BCDR 的一个重要方面，可用于将数据库从备份克隆到不同的地理区域。 Azure 云存储提供五个级别的 [冗余](../../../storage/common/storage-redundancy.md)
- 本地冗余存储 [ (LRS) ](../../../storage/common/storage-redundancy.md#locally-redundant-storage) 在这种情况下，你的数据会在主要区域中的一个物理位置内复制三次。  
- 区域冗余存储 [ (ZRS) ](../../../storage/common/storage-redundancy.md#zone-redundant-storage) ，其中的数据受主要区域中的 LRS 保护，并跨三个可用性区域 (AZ) 在主要区域中进行同步复制。 每个 AZ 也 LRS 受保护。 
- Geo-Redundant 存储 [ (GRS) ](../../../storage/common/storage-redundancy.md#geo-redundant-storage) ，其中你的数据受主要区域中的 LRS 保护，并将其异步复制到另一区域，其中该区域也受 LRS 保护。
- 地域冗余存储 [ (GZRS) ](../../../storage/common/storage-redundancy.md#geo-redundant-storage) 使用 ZRS 在主要区域的三个 Azure 可用性区域之间同步复制数据。 然后，它将数据异步复制到次要区域中的单个物理位置。 所有位置中的数据也受 LRS 保护。 
- Geo-Redundant 存储 (的读取访问权限 [) ](../../../storage/common/storage-redundancy.md#geo-redundant-storage) 和读取访问权限异地冗余存储 [ (RA-GZRS) ](../../../storage/common/storage-redundancy.md#geo-redundant-storage) 允许对复制到次要区域的数据进行只读访问。 

#### <a name="blob-storage-and-file-storage"></a>Blob 存储和文件存储

将 Azure 文件与 SMB 或 NFS 4.1 一起使用时 (预览) 用于作为备份存储进行装载的协议，请注意，Azure 文件不支持 (GRS) 和读取访问异地冗余存储 (GZRS) 的读取访问地域冗余存储。 

此外，如果备份存储要求超过 5 TiB Azure 文件需要启用了 [大文件共享功能](../../../storage/files/storage-files-planning.md) ，而该功能不支持 GRS 或 GZRS 仅支持冗余的 LRS。 

使用 NFS 3.0 (预览) 协议装载的 Azure Blob 目前仅支持 LRS 和 ZRS 冗余。  

可以使用 Blobfuse 装载配置有任何冗余选项的 Azure Blob。

#### <a name="recovery-services-vault"></a>恢复服务保管库 

恢复服务保管库是一个管理实体，它会存储一段时间内创建的恢复点，并提供用于执行备份相关操作的接口。 其中包括按需备份、执行还原和创建备份策略。
Azure 备份会自动处理保管库的存储。 你需要指定在创建时复制存储的方式。 在保管库中保护项后，不能对其进行更改。 对于 "区域冗余"，请选择 "异地冗余" 设置。

如果要还原到辅助副本，则 [Azure 配对区域](../../../best-practices-availability-paired-regions.md) 启用 [跨区域还原](../../../backup/backup-create-rs-vault.md) 功能。 启用跨区域还原时，备份存储将从 GRS 移到读取访问异地冗余存储 (RA-GRS) 。 

### <a name="azure-blob-storage"></a>Azure Blob 存储

Azure Blob 存储是一项非常经济高效、持久且安全的基于云的存储服务，用于存储大量非结构化数据，是用于 Oracle Database 备份的理想媒体。 可以使用 NFS v2.0 协议或 Blobfuse (Linux 熔断器) 将 azure Blob 存储装载到 Azure Linux VM。

#### <a name="azure-blob-blobfuse"></a>Azure Blob Blobfuse

[Blobfuse](../../../storage/blobs/storage-how-to-mount-container-linux.md) 是一个开源项目，旨在提供 Azure Blob 存储支持的虚拟文件系统。 它使用 libfuse 开源库与 Linux 熔断器内核模块通信，并使用 Azure 存储 Blob REST Api 来实现文件系统操作。 Blobfuse 当前适用于 Ubuntu 和 Centos/RedHat 分发版。 它还可用于使用 [CSI 驱动程序](https://github.com/kubernetes-sigs/blob-csi-driver)的 Kubernetes。 

在所有 Azure 区域中使用时，并适用于所有存储帐户类型，包括常规用途 v1/v2 和 Azure Data Lake Store Gen2，Blobfuse 提供的性能似乎低于备用协议，如 SMB 或 NFS。 作为数据库备份介质的适用性，我们建议使用 SMB 或 [NFS](../../../storage/blobs/storage-how-to-mount-container-linux.md) 协议来装载 Azure Blob 存储。 

#### <a name="azure-blob-nfs-v30-preview"></a>Azure Blob NFS 3.0 (预览) 

对网络文件系统 (NFS) 3.0 协议的 Azure 支持现在为预览版。 [NFS](../../../storage/blobs/network-file-system-protocol-support.md) 支持使 Windows 和 Linux 客户端能够将 Blob 存储容器装载到 Azure VM。 

NFS 3.0 公共预览版在以下区域中支持具有标准层性能的 GPV2 存储帐户： 
- 澳大利亚东部
- 韩国中部
- 美国中南部。 

为了确保网络安全，用于 NFS 装载的存储帐户必须包含在 VNet 中。 Azure Active Directory (AD) 安全性和访问控制列表 () Acl，它们目前尚不支持在其上启用了 NFS 3.0 协议支持的帐户。

### <a name="azure-files"></a>Azure 文件

[Azure 文件](../../../storage/files/storage-files-introduction.md) 是一种基于云的、完全托管的分布式文件系统，可以装载到本地或基于云的 Windows、Linux 或 macOS 客户端上。

Azure 文件在云中提供完全托管的跨平台文件共享，可通过服务器消息块（ (SMB) 协议和网络文件系统 (NFS) 协议 (预览版) 访问。 Azure 文件目前不支持多协议访问，因此共享只能是 NFS 共享或 SMB 共享。 在创建 Azure 文件共享之前，建议确定最适合你的需求的协议。

还可以通过 Azure 备份到恢复服务保管库保护 azure 文件共享，并向 Oracle RMAN 备份提供额外的保护层。

#### <a name="azure-files-nfs-v41-preview"></a>Azure 文件 NFS (预览版) 

可以使用网络文件系统 (NFS) 4.1 协议，在 Linux 分发版中装载 Azure 文件共享。 在预览版中，支持的功能有很多限制， [这里](../../../storage/files/storage-files-how-to-mount-nfs-shares.md)记录了这些限制。 

在预览版中，NFS v2.0 也限制为以下 [区域](../../../storage/files/storage-files-how-to-mount-nfs-shares.md)：
- 美国东部 (LRS 和 ZRS) 
- 美国东部 2
- 美国西部 2
- 西欧
- 东南亚
- 英国南部
- 澳大利亚东部

#### <a name="azure-files-smb-30"></a>Azure 文件 SMB 3。0

可以使用 SMB 内核客户端在 Linux 分发版中装载 Azure 文件共享。 Linux 分发版上提供的通用 Internet 文件系统 (CIFS) 协议是 SMB 的一种方言。 在 Linux Vm 上装载 Azure 文件 SMB 时，会装载为 CIFS 类型 filesystem，并且必须安装 CIFS 包。 

Azure 文件 SMB 在所有 Azure 区域中都已正式发布，并显示与 NFS v2.0 和版本4.1 协议相同的性能特征，目前推荐使用该方法将备份存储媒体提供给 Azure Linux Vm。  

提供了两个受支持的 SMB 版本： SMB 2.1 和 SMB 3.0，因为后者支持传输中的加密。 但是，不同的 Linux 内核版本对 SMB 2.1 和3.0 的支持有所不同，你应在 [此处](../../../storage/files/storage-how-to-use-files-linux.md) 查看表以确保你的应用程序支持 SMB 3.0。 

由于 Azure 文件设计为多用户文件共享服务，因此，你应该调整某些特征，使其更适合用作备份存储媒体。 建议关闭缓存并设置所创建文件的用户和组 Id。

## <a name="azure-netapp-files"></a>Azure NetApp 文件

[Azure NetApp 文件](../../../azure-netapp-files/azure-netapp-files-solution-architectures.md)服务是 azure Vm 中 Oracle 数据库的完整存储解决方案。 基于企业级、高性能、按流量计费的文件存储，它支持任何工作负荷类型，并且默认情况下高度可用。 Azure NetApp 文件和 Oracle 直接 NFS (dNFS) 驱动程序一起提供了高度优化的 Oracle Database 存储层。

Azure NetApp 文件在基础存储系统上提供高效的基于存储的快照副本，该副本使用在写入 (行) 机制时进行重定向。 尽管快照副本的执行和还原速度非常快，但它们只是一种最先进的防护，可以考虑任何给定组织所需的绝大部分还原操作，这通常是从人为错误中恢复的。 但快照副本不是完整备份。 为了涵盖所有备份和还原要求，必须在远程地域中创建外部快照副本和/或其他备份副本，以防发生区域服务中断。 若要了解有关在 Azure 上使用 Oracle 数据库的 NetApp 文件的详细信息，请阅读此 [报告](https://www.netapp.com/pdf.html?item=/media/17105-tr4780pdf.pdf)。

## <a name="azure-backup-service"></a>Azure 备份服务

[Azure 备份](../../../backup/backup-overview.md) 是一种完全托管的 PaaS，提供简单、安全且经济高效的解决方案来备份数据，并从 Microsoft Azure 云恢复数据。 Azure 备份可备份和还原本地客户端、Azure VM、Azure 文件共享，以及 Azure Vm 上 SQL Server、Oracle、MySQL、PostreSQL 和 SAP HANA 数据库。 

Azure 备份提供独立且隔离的备份，可以防范原始数据的意外破坏。 备份存储在 [恢复服务保管库](../../../backup/backup-azure-recovery-services-vault-overview.md) 中，并内置了恢复点管理。 配置和可伸缩性很简单，备份经过优化，可以轻松地根据需要还原。 它使用 Azure 云的基础功能和无限规模来提供高可用性，无维护或监视开销。 Azure 备份不会限制传输的入站或出站数据量，也不会对传输的数据进行收费，并且数据在传输和静止时受到保护。 

为了确保持续性 Azure 备份提供多种类型的复制，使备份数据保持高可用性。

- 本地冗余存储 [ (LRS) ](../../../storage/common/storage-redundancy.md#locally-redundant-storage) 将数据复制三次 (会在数据中心的存储缩放单位中创建数据) 的三个副本。
- 异地冗余存储 [ (GRS) ](../../../storage/common/storage-redundancy.md#geo-redundant-storage) 是默认的和推荐的复制选项。 GRS 将数据复制到离源数据主位置数英里之外的次要区域中。

使用 GRS 冗余创建的保管库包含用于配置 [跨区域还原](../../../backup/backup-create-rs-vault.md#set-storage-redundancy) 功能的选项，该选项允许你还原辅助 Azure 配对区域中的数据。

Azure 备份服务提供了一个 [框架](../../../backup/backup-azure-linux-app-consistent.md) ，用于在 Windows 和 Linux vm 备份期间实现应用程序一致性，这些应用程序包括 Oracle、MySQL、Mongo DB、SAP HANA 和 PostGreSQL，称为应用程序一致性快照。 这涉及到在拍摄磁盘快照之前调用一个预脚本 (来静止应用程序) ，并调用后脚本 (命令在完成快照后将应用程序解除冻结) ，以将应用程序恢复到正常模式。 虽然在 GitHub 上提供了示例前脚本和后脚本，但你有责任来创建和维护这些脚本。 对于 Oracle，数据库必须处于 archivelog 模式以允许联机备份，并且相应的数据库开始和结束备份命令在必须创建和维护的前脚本和后脚本中运行。 

Azure 备份现在提供 [增强型脚本编写和后脚本框架](https://github.com/Azure/azure-linux-extensions/tree/master/VMBackup/main/workloadPatch/DefaultScripts)，目前为预览版，azure 备份服务将为所选应用程序提供打包的预脚本和后脚本。 Azure 备份用户只需对应用程序进行命名，Azure VM 备份就会自动调用相关的前脚本和后脚本。 打包后脚本和操作后脚本将由 Azure 备份团队维护，因此用户可以确保这些脚本的支持、所有权和有效性。 目前，增强型框架支持的应用程序是 Oracle 和 MySQL，将来需要的应用程序类型更多。 快照将是存储的完整副本，而不是增量备份或副本写入快照，因此它是从还原数据库的有效介质。

## <a name="next-steps"></a>后续步骤

- [创建 Oracle Database 快速入门](oracle-database-quick-create.md)
- [将 Oracle Database 备份到 Azure 文件](oracle-database-backup-azure-storage.md)
- [使用 Azure 备份服务备份 Oracle Database](oracle-database-backup-azure-backup.md)


