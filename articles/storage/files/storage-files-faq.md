---
title: Azure 文件常见问题解答 (FAQ) | Microsoft Docs
description: 获取 Azure 文件存储常见问题的解答。 你可以在云或本地的 Windows、Linux 和 macOS 部署上同时装载 Azure 文件共享。
author: roygara
ms.service: storage
ms.date: 02/23/2020
ms.author: rogarana
ms.subservice: files
ms.topic: conceptual
ms.openlocfilehash: ff5d891b4374a61c8806666a48505a2ee4682a67
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2021
ms.locfileid: "110094467"
---
# <a name="frequently-asked-questions-faq-about-azure-files"></a>有关 Azure 文件的常见问题解答 (FAQ)
[Azure 文件存储](storage-files-introduction.md)会在云中提供完全托管的文件共享，这些共享项可通过行业标准的[服务器消息块 (SMB) 协议](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview)或[网络文件系统 (NFS) 协议](https://en.wikipedia.org/wiki/Network_File_System)（预览）进行访问。 你可以在云或 Windows、Linux 和 macOS 的本地部署同时装载 Azure 文件共享。 另外，你也可以使用 Azure 文件同步在 Windows Server 计算机上缓存 Azure 文件共享，以在靠近使用数据的位置实现快速访问。

本文回答了关于 Azure 文件特性和功能（包括 Azure 文件同步与 Azure 文件的使用）的常见问题。 如果本文未能涵盖你的问题，欢迎通过以下渠道联系我们（以升序排列）：

1. 本文评论部分。
2. [有关 Azure 存储的 Microsoft Q&A 问题页面](/answers/topics/azure-file-storage.html)。
3. [Azure 文件 UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files)。 
4. Microsoft 支持部门。 若要创建新的支持请求，请在 Azure 门户中的“帮助”选项卡上，选择“帮助和支持”按钮，然后选择“新建支持请求”。

## <a name="general"></a>常规
* <a id="why-files-useful"></a>
  **Azure 文件如何发挥作用？**  
   你可以使用 Azure 文件在云中创建文件共享，而无需应对物理服务器、设备或装置的开销。 诸如应用操作系统更新和更换损坏的磁盘等琐碎枯燥的工作，我们可为你逐一代劳。 若要详细了解 Azure 文件适用的应用场景，请参阅[为何 Azure 文件很有用](storage-files-introduction.md#why-azure-files-is-useful)。

* <a id="file-access-options"></a>
  **访问 Azure 文件中的文件有哪些不同方式？**  
    可以使用 SMB 3.x 协议将 SMB 文件共享装载在本地计算机上，也可以使用[存储资源管理器](https://storageexplorer.com/)之类的工具访问文件共享中的文件。 可通过复制/粘贴 Azure 门户提供的脚本，将 NFS 文件共享装载到本地计算机上。 在应用程序中，可以使用存储客户端库、REST API、PowerShell 或 Azure CLI 来访问 Azure 文件共享中的文件。

* <a id="what-is-afs"></a>
  **什么是 Azure 文件同步？**  
    你可以使用 Azure 文件同步，即可将组织的文件共享集中在 Azure 文件中，又不失本地文件服务器的灵活性、性能和兼容性。 Azure 文件同步可以将 Windows Server 计算机转换为 Azure 文件共享的快速缓存。 你可以使用 Windows Server 上的任何可用的协议本地访问你的数据，包括 SMB、网络文件系统 (NFS) 和文件传输协议服务 (FTPS)， 并且可以根据需要在世界各地具有多个缓存。

* <a id="files-versus-blobs"></a>
  **相对于 Azure Blob 存储，我为什么要对数据使用 Azure 文件共享？**  
    Azure 文件和 Azure Blob 存储均提供在云中存储大量数据的方法，但是用途略有不同。 
    
    Azure Blob 存储适用于需要存储非结构化数据且具有大规模缩放性的云本机应用程序。 为了更大程度地提升性能和可缩放性，相对于真实的文件系统而言，Azure Blob 存储是更简单的存储抽象。 此外，只可通过基于 REST 的客户端库访问 Azure Blob 存储（或直接通过基于 REST 的协议访问）。

    Azure 文件是一个专门的文件系统， 具有你在使用本地操作系统多年来所熟知和喜爱的所有文件抽象。 例如 Azure Blob 存储，Azure 文件提供了一个 REST 接口和基于 REST 的客户端库。 与 Azure Blob 存储不同的是，Azure 文件存储提供了对 Azure 文件共享的 SMB 或 NFS 访问。 文件共享可以直接装载到本地或云 VM 中的 Windows、Linux 或 macOS，无需编写任何代码，也无效为文件系统附加任何特殊驱动程序。 此外，你也可以使用 Azure 文件同步在本地文件服务器上缓存 Azure SMB 文件共享，以在靠近使用数据的位置实现快速访问。 
   
    有关 Azure 文件和 Azure Blob 存储之间差异的深入描述，请参阅[核心 Azure 存储服务简介](../common/storage-introduction.md)。 若要了解有关 Azure Blob 存储的详细信息，请参阅 [Blob 存储简介](../blobs/storage-blobs-introduction.md)。

* <a id="files-versus-disks"></a>**相对于 Azure 磁盘，我为什么要使用 Azure 文件共享？**  
    Azure 磁盘中的磁盘只是一个磁盘。 若要充分利用 Azure 磁盘，必须将其与在 Azure 中运行的虚拟机相关联。 Azure 磁盘可用于在本地服务器上使用磁盘的所有内容。 你可将其用作操作系统磁盘、操作系统的交换空间，或者应用程序的专用存储空间。 Azure 磁盘其中一个有趣的用途是，可在云中创建一个文件服务器，以在可能使用 Azure 文件共享的相同位置使用。 当需要 Azure 文件存储当前不支持的部署选项时，在 Azure 虚拟机中部署文件服务器则是一种非常行之有效的获取 Azure 中文件存储的方法。 

    但是，相比使用 Azure 文件共享，通过将 Azure 磁盘作为后端存储来运行文件服务器的方式，由于多方面的原因，其经济成本通常会更高。 首先，除了为磁盘存储付费之外，还必须为运行一个或多个 Azure VM 的成本付费。 其次，你还必须管理用于运行文件服务器的 VM。 例如，负责操作系统升级。 最后，如果你最终需要在本地缓存数据，则还要自行安装和管理复制技术（例如，分布式文件系统复制 (DFSR)）来实现此目的。

    要同时充分利用 Azure 文件和托管在 Azure 虚拟机中的文件服务器（除了将 Azure 磁盘作为后端存储），其中一种方法是：在云 VM 上托管的文件服务器上安装 Azure 文件同步。 如果 Azure 文件共享与文件服务器位于同一个区域，则可启用云分层并将卷可用空间百分比设置为最大值 (99%)。 这可最大程度地减少重复数据。 而且，你还可以在文件服务器上使用任何你需要的应用程序，例如，需要 NFS 协议支持的应用程序。

    有关在 Azure 中设置高性能和高可用性文件服务器的选项的信息，请参阅[在 Microsoft Azure 中部署 IaaS VM 来宾群集](https://blogs.msdn.microsoft.com/clustering/2017/02/14/deploying-an-iaas-vm-guest-clusters-in-microsoft-azure/)。 有关 Azure 文件和 Azure 磁盘之间差异的深入描述，请参阅[核心 Azure 存储服务简介](../common/storage-introduction.md)。 若要详细了解 Azure 磁盘，请参阅 [Azure 托管磁盘概述](../../virtual-machines/managed-disks-overview.md)。

* <a id="get-started"></a>
  **如何开始使用 Azure 文件？**  
   开始使用 Azure 文件非常简单。 首先，[创建 SMB 文件共享](storage-how-to-create-file-share.md)或[创建 NFS 共享](storage-files-how-to-create-nfs-shares.md)，然后将其装载到首选操作系统中： 

  * [在 Windows 中装载 SMB 共享](storage-how-to-use-files-windows.md)
  * [在 Linux 中装载 SMB 共享](storage-how-to-use-files-linux.md)
  * [在 macOS 中装载 SMB 共享](storage-how-to-use-files-mac.md)
  * [装载 NFS 文件共享](storage-files-how-to-mount-nfs-shares.md)

    有关部署 Azure 文件共享以替换组织中生产文件共享的详细指南，请参阅[规划 Azure 文件部署](storage-files-planning.md)。

* <a id="redundancy-options"></a>
  **Azure 文件支持哪些存储冗余选项？**  
    目前，Azure 文件支持本地冗余存储 (LRS)、区域冗余存储 (ZRS)、异地冗余存储 (GRS) 和地理区域冗余存储 (GZRS)。 Azure 文件存储高级层目前仅支持 LRS 和 ZRS。

* <a id="tier-options"></a>
  **Azure 文件支持哪些存储层？**  
    Azure 文件支持两种存储层：高级存储层和标准存储层。 标准文件共享是在常规用途（GPv1 或 GPv2）存储帐户中创建的，高级文件共享是在 FileStorage 存储帐户中创建的。 详细了解如何创建[标准文件共享](storage-how-to-create-file-share.md)和[高级文件共享](./storage-how-to-create-file-share.md)。 
    
    > [!NOTE]
    > 你无法使用 Blob 存储帐户或高级常规用途（GPv1 或 GPv2）存储帐户创建 Azure 文件共享。 标准 Azure 文件共享必须只能在标准常规用途帐户中创建，高级 Azure 文件共享必须只能在 FileStorage 存储帐户中创建。 *高级* 常规用途（GPv1 和 GPv2）存储帐户仅适用于高级页 Blob。 

* <a id="file-locking"></a>
  **Azure 文件存储是否支持文件锁定？**  
    是的，Azure 文件完全支持 SMB/Windows 样式文件锁定，[查看详细信息](/rest/api/storageservices/managing-file-locks)。

* <a id="give-us-feedback"></a>
  **我非常希望可以将某项特定功能添加到 Azure 文件。你们会添加它吗？**  
    Azure 文件团队非常乐于听取你针对我们服务提供的任何反馈。 请在 [Azure 文件 UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files) 上进行功能请求投票！ 希望我们提供的众多新功能能够满足你的需求。

## <a name="azure-file-sync"></a>Azure 文件同步

* <a id="afs-region-availability"></a>
  **Azure 文件同步支持哪些区域？**  
    可以在 Azure 文件同步规划指南的[区域可用性](../file-sync/file-sync-planning.md#azure-file-sync-region-availability)部分中找到可用区域列表。 我们将继续添加对其他区域的支持，包括非公共区域。

* <a id="cross-domain-sync"></a>
  **是否可以在同一个同步组中同时包含已加入域的服务器和未加入域的服务器？**  
    是的。 同步组可以包含具有不同的 Active Directory 成员身份的服务器终结点，即使它们未加入域。 虽然从严格意义上讲这种配置可用，但不建议将此作为常规配置，因为在某个服务器上为文件和文件夹定义的访问控制列表 (ACL) 可能无法由同步组中的其他服务器实施。 为获得最佳结果，建议在同一个 Active Directory 林中的服务器之间、在不同 Active Directory 林中但已建立信任关系的服务器之间，或未加入域的服务器之间进行同步。 我们建议你避免混合使用这些配置。

* <a id="afs-change-detection"></a>
  **我通过 SMB 或门户在 Azure 文件共享中直接创建了一个文件。多久后该文件才会同步到同步组中的服务器上？**  
    [!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]


* <a id="afs-sync-time"></a>
  **Azure 文件同步上传 1TiB 数据需要多长时间？**
  
    性能将取决于你的环境设置和配置，以及这是初始同步还是正在进行的同步。有关详细信息，请参阅 [Azure 文件同步性能指标](storage-files-scale-targets.md#azure-file-sync-performance-metrics)

* <a id="afs-conflict-resolution"></a>**如果在两个服务器上几乎同时对同一文件进行了更改后，会发生什么情况？**  
    Azure 文件同步会使用简单的冲突解决策略：同时在两个终结点上保留对文件进行的更改。 最新写入的更改保留原始文件名称。 旧文件（由 LastWriteTime 确定）的终结点名称和冲突号追加到文件名末尾。 对于服务器终结点，终结点名称是服务器名称。 对于云终结点，终结点名称为 Cloud。 名称遵循以下分类： 
   
    \<FileNameWithoutExtension\>-\<endpointName\>\[-#\].\<ext\>  

    例如，如果 CentralServer 是较早写入的位置，则 CompanyReport.docx 的第一个冲突将变为 CompanyReport-CentralServer.docx。 第二个冲突将被命名为 CompanyReport-CentralServer-1.docx。 Azure 文件同步支持每文件 100 个冲突文件。 达到最大冲突文件数后，该文件将无法同步，直到冲突文件数小于 100。

* <a id="afs-storage-redundancy"></a>
  **Azure 文件同步是否支持异地冗余存储？**  
    是的，Azure 文件支持本地冗余存储 (LRS) 和异地冗余存储 (GRS)。 如果通过为 GRS 配置的帐户发起配对区域之间的存储帐户故障转移，Microsoft 建议仅将新区域视为数据的备份。 Azure 文件同步不会自动开始与新的主区域进行同步。 

* <a id="sizeondisk-versus-size"></a>
  **使用 Azure 文件共享后，为什么文件的占用空间属性与大小属性不一致？**  
  请参阅[了解 Azure 文件同步云分层](../file-sync/file-sync-cloud-tiering-overview.md#tiered-vs-locally-cached-file-behavior)。

* <a id="is-my-file-tiered"></a>
  **如何分辨文件是否已被分层？**  
  请参阅[了解云分层](../file-sync/file-sync-how-to-manage-tiered-files.md#how-to-check-if-your-files-are-being-tiered)。

* <a id="afs-recall-file"></a>**我想要使用的一个文件已被分层。如何将文件召回到磁盘以在本地使用？**  
  请参阅[了解云分层](../file-sync/file-sync-how-to-manage-tiered-files.md#how-to-recall-a-tiered-file-to-disk)。

* <a id="afs-force-tiering"></a>
  **如何强制将文件或目录分层？**  
  请参阅[了解云分层](../file-sync/file-sync-how-to-manage-tiered-files.md#how-to-force-a-file-or-directory-to-be-tiered)。

* <a id="afs-effective-vfs"></a>
  **当卷上有多个服务器终结点时，如何解释卷可用空间？**  
  请参阅[了解云分层](../file-sync/file-sync-cloud-tiering-policy.md#multiple-server-endpoints-on-a-local-volume)。
  
* <a id="afs-tiered-files-tiering-disabled"></a>
  **我禁用了云分层，为什么服务器终结点位置中存在分层文件？**  
    有两个原因会导致分层文件存在于服务器终结点位置：

    - 向现有同步组添加新的服务器终结点时，如果为初始下载模式选择“首先召回命名空间”选项或“仅召回命名空间”选项，则文件在本地下载下来之前将显示为分层文件。 为避免出现这种情况，请为初始下载模式选择“避免分层文件”选项。 若要手动召回文件，请使用 [Invoke-StorageSyncFileRecall](../file-sync/file-sync-how-to-manage-tiered-files.md#how-to-recall-a-tiered-file-to-disk) cmdlet。

    - 如果已在服务器终结点上启用云分层，然后将其禁用，则文件在被访问之前将保持分层。

* <a id="afs-tiered-files-not-showing-thumbnails"></a>
  **为什么分层文件在 Windows 资源管理器中未显示缩略图或预览？**  
    对于分层文件，不会在服务器终结点显示缩略图和预览。 此行为正常，因为 Windows 中的缩略图缓存功能有意跳过读取具有脱机属性的文件。 启用云分层后，阅览分层文件将导致它们被下载（召回）。

    此行为不特定于 Azure 文件同步，Windows 资源管理器会为设置了脱机属性的任何文件显示“灰色 X”。 通过 SMB 访问文件时，将看到 X 图标。 有关此行为的详细说明，请参阅 [https://blogs.msdn.microsoft.com/oldnewthing/20170503-00/?p=96105](https://blogs.msdn.microsoft.com/oldnewthing/20170503-00/?p=96105)

    有关如何管理分层文件的问题，请参阅[如何管理分层文件](../file-sync/file-sync-how-to-manage-tiered-files.md)。

* <a id="afs-files-excluded"></a>
  **会被 Azure 文件同步自动排出的文件或文件夹有哪些？**  
  请参阅[跳过的文件](../file-sync/file-sync-planning.md#files-skipped)。

* <a id="afs-os-support"></a>
  **我是否可以将 Azure 文件同步与 Windows Server 2008 R2、Linux 或我的网络连接存储 (NAS) 设备一起使用？**  
    Azure 文件同步当前仅支持 Windows Server 2019、Windows Server 2016 和 Windows Server 2012 R2。 目前，我们暂无可分享的其他计划，但我们愿意根据客户需求支持其他平台。 请在 [Azure 文件 UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files) 上告知你想要我们支持的平台。

* <a id="afs-tiered-files-out-of-endpoint"></a>
  **为什么分层文件存在于服务器终结点命名空间之外？**  
    在 Azure 文件同步代理版本 3 之前，Azure 文件同步阻止将分层文件移到服务器终结点之外但位于服务器终结点所在卷上的其他位置。 复制操作、非分层文件的移动操作以及将分层文件移到其他卷的操作不受影响。 这种行为的原因在于以下隐含假设：文件资源管理器和其他 Windows API 在同一卷上的移动操作是（近乎）即时重命名操作。 这意味着，移动会使文件资源管理器或其他移动方法（如命令行或 PowerShell）看起来没有响应，而 Azure 文件同步会从云中召回数据。 从 [Azure 文件同步代理版本 3.0.12.0](../file-sync/file-sync-release-notes.md#supported-versions) 开始，Azure 文件同步将允许将分层文件移到服务器终结点之外。 我们通过允许分层文件作为服务器终结点之外的分层文件存在，然后在后台召回该文件以避免前面提到的负面影响。 这意味着在同一卷上的移动是即时的，在移动完成后，我们要完成将文件召回到磁盘的所有工作。 

* <a id="afs-do-not-delete-server-endpoint"></a>
  **我在服务器上遇到 Azure 文件同步问题（同步、云分层等）。是否应删除并重新创建服务器终结点？**  
    [!INCLUDE [storage-sync-files-remove-server-endpoint](../../../includes/storage-sync-files-remove-server-endpoint.md)]
    
* <a id="afs-resource-move"></a>
  **是否可将存储同步服务和/或存储帐户移动到不同的资源组、订阅或 Azure AD 租户？**  
   是的，存储同步服务和/或存储帐户可以移动到不同的资源组、订阅或 Azure AD 租户。 存储同步服务或存储帐户移动后，你需要向 Microsoft.StorageSync 应用程序授予对存储帐户的访问权限（请参阅[确保 Azure 文件同步有权访问存储帐户](../file-sync/file-sync-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)）。

    > [!Note]  
    > 创建云终结点时，存储同步服务和存储帐户必须位于相同的 Azure AD 租户中。 创建云终结点后，可以将存储同步服务和存储帐户移到不同的 Azure AD 租户。
    
* <a id="afs-ntfs-acls"></a>
  **Azure 文件同步是否会保留目录/文件级别 NTFS ACL 以及存储在 Azure 文件中的数据？**

    从 2020 年 2 月 24 日开始，由 Azure 文件同步分层的新 ACL 和现有 ACL 将持久保留为 NTFS 格式，直接对 Azure 文件共享所做的 ACL 修改将同步到同步组中的所有服务器。 对 Azure 文件存储的 ACL 所做的任何更改都将通过 Azure 文件同步进行同步。将数据复制到 Azure 文件存储时，请确保使用支持必要“保真度”的复制工具，通过 SMB 或 REST 将属性、时间戳和 ACL 复制到 Azure 文件共享中。 使用 Azure 复制工具（如 AzCopy）时，请务必使用最新版本。 检查[文件复制工具表](storage-files-migration-overview.md#file-copy-tools)获取 Azure 复制工具的概述，以确保可以复制文件的所有重要元数据。

    如果在文件同步管理的文件共享上启用了 Azure 备份，则可以继续在备份还原工作流中还原文件 ACL。 该操作适用于整个共享或单个文件/目录。

    如果使用快照作为文件共享（由文件同步管理）的自我托管备份解决方案的一部分，且如果快照是在 2020 年 2 月 24 日之前拍摄的，则 ACL 可能无法正确还原到 NTFS ACL。 如果发生这种情况，请考虑联系 Azure 支持部门。

* <a id="afs-lastwritetime"></a>
  Azure 文件同步是否同步目录的 LastWriteTime？  
    否。Azure 文件同步不同步目录的 LastWriteTime。 这是设计的结果。
    
## <a name="security-authentication-and-access-control"></a>安全性、身份验证和访问控制
* <a id="ad-support"></a>
**Azure 文件是否支持基于标识的身份验证和访问控制？**  
    
    是的，Azure 文件支持基于标识的身份验证和访问控制。 可以选择以下两种方法之一以使用基于标识的访问控制：本地 Active Directory 域服务或 Azure Active Directory 域服务 (Azure AD DS)。 本地 Active Directory 域服务 (AD DS) 支持使用已加入 AD DS 域的计算机（在本地或在 Azure 中）进行身份验证，以通过 SMB 访问 Azure 文件共享。 通过 SMB 为 Azure 文件进行 Azure AD DS 身份验证，让加入 Azure AD DS 域的 Windows VM 能够使用 Azure AD 凭据访问共享、目录和文件。 有关更多详细信息，请参阅 [Azure 文件基于标识的身份验证支持进行 SMB 访问概述](storage-files-active-directory-overview.md)。 

    Azure 文件还提供了另外两种方法来管理访问控制：

    - 你可以使用共享访问签名 (SAS) 生成在指定时间间隔内有效的具有特定权限的令牌。 例如，可以生成在 10 分钟后到期、对特定文件具有只读访问权限的令牌。 只要拥有此有效令牌，就可以在 10 分钟内拥有对给定文件的只读访问权限。 仅通过 REST API 或客户端库支持共享的访问签名密钥。 你必须使用存储帐户密钥通过 SMB 装载 Azure 文件共享。

    - Azure 文件同步会保留所有自定义 ACL 或 DACL（无论基于 Active Directory 或本地目录），并复制到其同步到的所有服务器终结点。 
    
    若要全面了解 Azure 存储服务支持的所有协议，可以参考[授权访问 Azure 存储](../common/storage-auth.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。 
    
* <a id="encryption-at-rest"></a>
**如何确保已静态加密 Azure 件共享？**  

    是的。 有关详细信息，请参阅 [Azure 存储服务加密](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。

* <a id="access-via-browser"></a>
**如何使用 Web 浏览器提供对特定文件的访问权限？**  

    你可以使用共享访问签名生成在指定时间间隔内有效的、具有特定权限的令牌。 例如，可以生成一个在设定时段内对特定文件具有只读访问权限的令牌。 只要拥有此 URL，就可以在令牌有效期间，直接通过任意 Web 浏览器访问文件。 你可以从类似存储资源管理器的 UI 轻松生成共享的访问签名密钥。

* <a id="file-level-permissions"></a>
**能否对共享中的文件夹指定只读或只写权限？**  

    如果使用 SMB 装载文件共享，则不具有文件夹级的控制权限。 但是，如果使用 REST API 或客户端库创建共享访问签名，则可以在共享内的文件夹上指定只读或只写权限。

* <a id="ip-restrictions"></a>
**是否对 Azure 文件共享实现 IP 限制？**  

    是的。 可以在存储帐户级别对 Azure 文件共享的权限进行限制。 有关详细信息，请参阅[配置 Azure 存储防火墙和虚拟网络](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。

* <a id="data-compliance-policies"></a>
**Azure 文件支持哪些数据符合性策略？**  

   Azure 文件所依据的存储体系结构与 Azure 存储中的其他存储服务使用的相同。 Azure 文件实施的数据符合性策略也与其他 Azure 存储服务使用的相同。 有关 Azure 存储数据符合性的详细信息，可参阅 [Azure 存储符合性产品/服务](../common/storage-compliance-offerings.md)和转到 [Microsoft 信任中心](https://microsoft.com/trustcenter/default.aspx)。

* <a id="file-auditing"></a>
**如何审核 Azure 文件存储中的文件访问和更改？**

  有两个选项提供 Azure 文件存储的审核功能：
  - 如果用户直接访问 Azure 文件共享，则可以使用[Azure 存储日志（预览）](../blobs/monitor-blob-storage.md?tabs=azure-powershell#analyzing-logs)来跟踪文件更改和用户访问。 这些日志可用于故障排除，系统会尽最大努力记录请求。
  - 如果用户通过安装了 Azure 文件同步代理的 Windows Server 访问 Azure 文件共享，请使用[审核策略](/windows/security/threat-protection/auditing/apply-a-basic-audit-policy-on-a-file-or-folder)或第三方产品来跟踪 Windows Server 上的文件更改和用户访问。 
   
### <a name="ad-ds--azure-ad-ds-authentication"></a>AD DS 和 Azure AD DS 身份验证
* <a id="ad-support-devices"></a>
**Azure 文件的 Azure Active Directory 域服务 (Azure AD DS) 身份验证是否支持使用 Azure AD 凭据从加入或者注册到 Azure AD 的设备进行 SMB 访问？**

    不支持。不支持该方案。

* <a id="ad-vm-subscription"></a>
**是否可以从不同订阅下的 VM 使用 Azure AD 凭据访问 Azure 文件共享？**

    如果部署了文件共享的订阅与 VM 已加入域的 Azure AD DS 部署相同的 Azure AD 租户相关联，则可以使用相同的 Azure AD 凭据访问 Azure 文件共享。 限制不是针对订阅，而是针对关联的 Azure AD 租户的。
    
* <a id="ad-support-subscription"></a>
**能否使用不同于 Azure 文件共享的主租户的 Azure AD 租户为 Azure 文件共享启用 Azure AD DS 或本地 AD DS 身份验证？**

    不可以。Azure 文件仅支持 Azure AD DS 或本地 AD DS 与一个 Azure AD 租户集成，该 Azure AD 租户和文件共享驻留在同一订阅中。 只有一个订阅可以与 Azure AD 租户相关联。 此限制适用于 Azure AD DS 和本地 AD DS 身份验证方法。 使用本地 AD DS 进行身份验证时，[AD DS 凭据必须同步到与存储帐户关联的 Azure AD](../../active-directory/hybrid/how-to-connect-install-roadmap.md)。

* <a id="ad-linux-vms"></a>
**用于 Azure 文件共享 的 Azure AD DS 或本地 AD DS 身份验证是否支持 Linux VM？**

    不支持。不支持从 Linux VM 进行身份验证。

* <a id="ad-aad-smb-afs"></a>
**由 Azure 文件同步管理的文件共享是否支持 Azure AD DS 或本地 AD DS 身份验证？**

    是的，可以在由 Azure 文件同步管理的文件共享上启用 Azure AD DS 或本地 AD DS 身份验证。对本地文件服务器上的目录/文件 NTFS ACL 的更改将分层到 Azure 文件，反之亦然。

* <a id="ad-aad-smb-files"></a>
**如何检查是否已在存储帐户上启用 AD DS 身份验证并检索域信息？**

    有关说明，请参阅[此处](./storage-files-identity-ad-ds-enable.md#confirm-the-feature-is-enabled)。

* <a id=""></a>
**Azure 文件的 Azure AD 身份验证是否支持 Linux VM？**

    不支持。不支持从 Linux VM 进行身份验证。

* <a id="ad-multiple-forest"></a>
**用于 Azure 文件共享的本地 AD DS 身份验证是否支持使用多个林与 AD DS 环境集成？**    

    Azure 文件的本地 AD DS 身份验证仅与存储帐户所注册的域服务的对应林集成。 若要支持从其他林进行身份验证，环境必须正确配置林信任。 Azure 文件在 AD DS 中注册的方式与常规文件服务器几乎相同，它在其中创建标识（计算机或服务登录帐户）以用于身份验证。 唯一的区别是，存储帐户的注册 SPN 以“file.core.windows.net”结尾，这与域后缀不匹配。 请咨询域管理员，查看是否需要对后缀路由策略进行任何更新，以启用由于域后缀不同而带来的多林身份验证。 我们提供了下面的示例来配置后缀路由策略。
    
    示例：如果林 A 域中的用户要使用针对林 B 中域注册的存储帐户来访问文件共享，则此操作不会自动进行，因为存储帐户的服务主体的后缀不与林 A 中任何域的后缀相匹配。可以通过为自定义后缀“file.core.windows.net”手动配置从林 A 到林 B 的后缀路由规则来解决此问题。
    首先，必须在林 B 上添加新的自定义后缀。请确保具有相应的管理权限来更改配置，然后执行以下步骤：   
    1. 登录到已加入林 B 的计算机域
    2.  打开“Active Directory 域和信任”控制台
    3.  右键单击“Active Directory 域和信任”
    4.  单击“属性”
    5.  单击“添加”
    6.  添加“file.core.windows.net”作为 UPN 后缀
    7.  单击“应用”，然后单击“确定”关闭向导
    
    接下来，在林 A 上添加后缀路由规则，以便将其重定向到林 B。
    1.  登录到已加入林 A 的计算机域
    2.  打开“Active Directory 域和信任”控制台
    3.  右键单击想要其访问文件共享的域，然后单击“信任”选项卡，并选择来自传出信任的林 B 域。 如果尚未在两个林之间配置信任，则需要先设置信任
    4.  依次单击“属性...”和“名称后缀路由”
    5.  检查“*.file.core.windows.net”后缀是否显示。 如果没有，请单击“刷新”
    6.  选择“*.file.core.windows.net”，然后单击“启用”和“应用”

* <a id=""></a>
**哪些区域适用于 Azure 文件存储 AD DS 身份验证？**

    有关详细信息，请参阅 [AD DS 区域可用性](storage-files-identity-auth-active-directory-enable.md#regional-availability)。
    
* <a id="ad-aad-smb-afs"></a>
**是否可以在由 Azure 文件同步管理的文件共享上利用 Azure 文件存储的 Active Directory (AD) 身份验证？**

    是的，可以在由 Azure 文件同步管理的文件共享上启用 AD 身份验证。对本地文件服务器上的目录/文件 NTFS ACL 的更改将分层到 Azure 文件，反之亦然。

* <a id="ad-aad-smb-files"></a>
**在 AD 中创建表示我的存储帐户的计算机帐户或服务登录帐户有何区别？**

    创建[计算机帐户](/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory)（默认值）或[服务登录帐户](/windows/win32/ad/about-service-logon-accounts)对 Azure 文件的身份验证方式没有任何区别。 可自行选择如何在 AD 环境中将存储帐户表示为标识。 Join-AzStorageAccountForAuth cmdlet 中的默认 DomainAccountType 设置为计算机帐户。 但是，在 AD 环境中对计算机或服务登录帐户配置的密码过期期限可能不同，因此，在[更新 AD 中存储帐户标识的密码](./storage-files-identity-ad-ds-update-password.md)时，需要考虑到这一点。
 
* <a id="ad-support-rest-apis"></a>
**是否有支持获取/设置/复制目录/文件 Windows ACL 的 REST API？**

    是的，支持在使用 [2019-07-07](/rest/api/storageservices/versioning-for-the-azure-storage-services#version-2019-07-07)（或更高版本）REST API 时获取、设置或复制目录或文件 NTFS ACL 的 REST API。 我们还支持在基于 REST 的工具中保留 Windows ACL：[AzCopy v10.4+](https://github.com/Azure/azure-storage-azcopy/releases)。

* <a id="ad-support-rest-apis"></a>
**如何在与 Azure AD 或 AD 凭据建立新连接之前，删除具有存储帐户密钥的缓存凭据并删除现有 SMB 连接？**

    可以按照以下两步式过程删除与存储帐户密钥关联的已保存凭据，并删除 SMB 连接： 
    1. 在 Windows Cmd.exe 中运行以下 cmdlet 以删除凭据。 如果找不到任一凭据，则表示尚未保留凭据，可以跳过此步骤。
    
       cmdkey /delete:Domain:target=storage-account-name.file.core.windows.net
    
    2. 删除与文件共享建立的现有连接。 可以将装载路径指定为装入的驱动器号或 storage-account-name.file.core.windows.net 路径。
    
       接下来使用 <drive-letter/share-path> /delete

## <a name="network-file-system"></a>网络文件系统

* <a id="when-to-use-nfs"></a>
**应何时使用 Azure 文件存储 NFS？**

    请参阅 [NFS 共享（预览）](storage-files-compare-protocols.md#nfs-shares-preview)。

* <a id="backup-nfs-data"></a>
**如何备份 NFS 共享中存储的数据？**

    可以使用熟悉的工具（如 rsync）或其中一个第三方备份合作伙伴的产品来协调 NFS 共享上的数据备份。 多个备份合作伙伴（包括 [Commvault](https://documentation.commvault.com/commvault/v11/article?p=92634.htm)、[Veeam](https://www.veeam.com/blog/?p=123438) 和 [Veritas](https://players.brightcove.net/4396107486001/default_default/index.html?videoId=6189967101001)）包含在我们初始预览版中，并已将其解决方案扩展为适用于 Azure 文件存储的 SMB 3.x 和 NFS 4.1。

* <a id="migrate-nfs-data"></a>
**是否可以将现有数据迁移到 NFS 共享？**

    在某个区域内，可以使用诸如 scp、rsync 或 SSHFS 等标准工具来移动数据。 由于可以同时从多个计算实例访问 Azure 文件存储 NFS，因此可以通过并行上传来提高复制速度。 如果要引入来自区域外部的数据，请使用 VPN 或 Expressroute 从本地数据中心装载到文件系统。

## <a name="on-premises-access"></a>本地访问

* <a id="port-445-blocked"></a>
**我的 ISP 或 IT 阻止了端口 445，这导致 Azure 文件装载失败。我该怎样做？**

    可在此处了解[解决端口 445 受阻问题的各种方法](./storage-troubleshoot-windows-file-connection-problems.md#cause-1-port-445-is-blocked)。 Azure 文件存储只允许来自区域或数据中心外部的使用 SMB 3.x（带加密支持）的连接。 SMB 3.x 协议引入了许多安全功能，包括在 Internet 上使用非常安全的通道加密。 但是，由于在较低 SMB 版本中发现漏洞的历史原因，端口 445 可能已被阻止。 理想情况下，应仅针对 SMB 1.0 流量阻止该端口，并且应在所有客户端上关闭 SMB 1.0。

* <a id="expressroute-not-required"></a>
**必须使用 Azure ExpressRoute 才能在本地连接到 Azure 文件或使用 Azure 文件同步吗？**  

    不是。 ExpressRoute 不是访问 Azure 文件共享的必要条件。 如果要直接在本地装载 Azure 文件共享，则只需打开端口 445（TCP 出站）即可进行 Internet 访问（这是 SMB 用于进行通信的端口）。 如果正在使用 Azure 文件同步，则只需端口 443（TCP 出站）即可进行 HTTPS 访问（无需 SMB）。 但是，你可以将 ExpressRoute 与这些访问选项中任意一项一起使用。

* <a id="mount-locally"></a>
**如何才能在本地计算机上装载 Azure 文件共享？**  

    可以使用 SMB 协议装载文件共享，前提是端口 445（TCP 出站）处于打开状态且客户端支持 SMB 3.x 协议（例如，你使用的是 Windows 10 或 Windows Server 2016）。 如果端口 445 被组织的策略或 ISP 阻止，则可使用 Azure 文件同步访问 Azure 文件共享。

## <a name="backup"></a>备份
* <a id="backup-share"></a>
**如何备份我的 Azure 文件共享？**  
    可以使用定期[共享快照](storage-snapshots-files.md)来防止意外删除。 此外，也可以使用 AzCopy、RoboCopy 或能够备份已装载文件共享的第三方备份工具。 Azure 备份提供 Azure 文件的备份。 深入了解[通过 Azure 备份服务备份 Azure 文件共享](../../backup/backup-afs.md)。

## <a name="share-snapshots"></a>共享快照

### <a name="share-snapshots-general"></a>共享快照：常规
* <a id="what-are-snaphots"></a>
**什么是文件共享快照？**  
    可以使用 Azure 文件共享快照创建只读版本的文件共享。 另外，也可以使用 Azure 文件将早期版本的内容复制回 Azure 或本地中的同一个共享或备用位置中，以做进一步修改。 若要了解有关共享快照的详细信息，请参阅[共享快照概述](storage-snapshots-files.md)。

* <a id="where-are-snapshots-stored"></a>
**共享快照存储在何处？**  
    共享快照与文件共享存储在同一个存储帐户中。

* <a id="snapshot-consistency"></a>
**共享快照是否与应用程序一致？**  
    否，共享快照与应用程序不一致。 执行共享快照前，用户必须将应用程序中的写入刷新到共享中。

* <a id="snapshot-limits"></a>
**对我可使用的共享快照数有限制吗？**  
    是的。 Azure 文件可以最多保留 200 张共享快照。 共享快照不计入共享配额，因此，对所有共享快照使用的总空间没有单独的共享限制。 存储帐户限制仍然适用。 在达到 200 个共享快照之后，必须删除旧的共享快照才可创建新的共享快照。

* <a id="snapshot-cost"></a>
**共享快照的费用是多少？**  
    快照按标准事务和标准存储收费。 快照在本质上是递增的。 基本快照即是共享本身。 所有的后续快照均是递增的，并且只会存储与之前快照的不同之处。 这意味着，如果工作负荷改动极小，则帐单上显示的增量更改也很小。 有关标准 Azure 文件的定价信息，请参阅[定价页](https://azure.microsoft.com/pricing/details/storage/files/)。 目前，查看共享快照已用大小的方法是比较计费的容量与使用的容量。 我们致力于开发改进报告的工具。

* <a id="ntfs-acls-snaphsots"></a>
**目录和文件上的 NTFS ACL 是否保留在共享快照中？**  
    目录和文件上的 NTFS ACL 会保留在共享快照中。

### <a name="create-share-snapshots"></a>创建共享快照
* <a id="file-snaphsots"></a>
**是否可以创建单个文件的共享快照？**  
    可在文件共享级别上创建共享快照。 你可以从文件共享快照还原单个文件，但是不能创建文件级别的共享快照。 但是，如果你已执行共享级别的共享快照，并且想要列出已更改特定文件的共享快照，则可以在已装载 Windows 的共享上的“以前的版本”下执行此操作。 
    
    如需文件快照功能，请通过 [Azure 文件 UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files) 告知我们。

* <a id="encrypted-snapshots"></a>
**是否可以创建加密文件共享的共享快照？**  
    可以对已启用静态加密的 Azure 文件共享执行共享快照。 可以从共享快照中将文件还原到加密文件共享中。 如果共享已加密，则共享快照也会加密。

* <a id="geo-redundant-snaphsots"></a>
**共享快照具有异地冗余吗？**  
    共享快照与捕获它们的 Azure 文件共享具有相同的冗余。 如果已为帐户选择异地冗余存储，则共享快照也会在配对区域中进行冗余存储。

### <a name="manage-share-snapshots"></a>管理共享快照
* <a id="browse-snapshots-linux"></a>
**是否可以在 Linux 中浏览共享快照？**  
    可以使用 Azure CLI 在 Linux 中创建、列出、浏览和还原共享快照。

* <a id="copy-snapshots-to-other-storage-account"></a>
**是否可以将共享快照复制到不同的存储帐户？**  
    可以将文件从共享快照复制到其他位置，但不能复制到共享快照本身。

### <a name="restore-data-from-share-snapshots"></a>从共享快照还原数据
* <a id="promote-share-snapshot"></a>
**是否可以将共享快照提升到基本共享中？**  
    只能将数据从共享快照复制到任何其他目标位置， 不能将共享快照提升到基本共享中。

* <a id="restore-snapshotted-file-to-other-share"></a>
**是否可以将数据从共享快照还原到不同的存储帐户？**  
    是的。 可以将共享快照文件复制到原始位置或备用位置，其中包括位于同一区域或不同区域的相同/不同的存储帐户。 你还可以将文件复制到本地位置或任何其他云。    
  
### <a name="clean-up-share-snapshots"></a>清除共享快照
* <a id="delete-share-keep-snapshots"></a>
**是否可以删除共享，而不删除共享快照？**  
    如果共享上有活动的共享快照，则无法删除此共享。 你可以使用一个 API 将共享快照连同共享一起删除。 此外，也可以在 Azure 门户中删除共享快照和共享。

* <a id="delete-share-with-snapshots"></a>
**如果删除存储帐户，共享快照会怎样？**  
    删除存储帐户后，共享快照也将被删除。

## <a name="billing-and-pricing"></a>计费和定价
* <a id="vm-file-share-network-traffic"></a>
**Azure VM 与 Azure 文件共享之间的网络流量是否作为外部带宽计入订阅费用？**  
    如果文件共享和 VM 位于同一 Azure 区域，则它们之间的流量是不会额外收费的。 如果文件共享和 VM 位于不同的区域，则它们之间的流量会作为外部带宽收费。

* <a id="share-snapshot-price"></a>
**共享快照的费用是多少？**  
     在预览版期间，共享快照容量可免费使用， 但会收取标准存储出口和事务费用。 公开发布后，共享快照的容量和事务均将收费。
     
     共享快照在本质上是递增的。 基本共享快照即是共享本身。 所有的后续共享快照均是递增的，并且只会存储与之前共享快照的不同之处。 你只需为更改的内容付费。 如果你的共享包含 100 GiB 数据，但自执行上次共享快照以来只更改了 5 GiB 数据，则共享快照只额外使用了 5 GiB 数据，而你要为 105 GiB 付费。 有关事务和标准出口费用的更多信息，请参阅[定价页](https://azure.microsoft.com/pricing/details/storage/files/)。

## <a name="scale-and-performance"></a>缩放和性能
* <a id="files-scale-limits"></a>
**Azure 文件存在哪些缩放限制？**  
    有关 Azure 文件的可伸缩性和性能目标的信息，请参阅 [Azure 文件可伸缩性和性能目标](storage-files-scale-targets.md)。

* <a id="need-larger-share"></a>
**Azure 文件共享的可用大小有哪些？**  
    Azure 文件共享大小（高级和标准）最多可扩展到 100 TiB。 请参阅计划指南中的[加入到更大的文件共享（标准层）](storage-files-planning.md#enable-standard-file-shares-to-span-up-to-100-tib)，了解加入标准层的更大文件共享的说明。

* <a id="lfs-performance-impact"></a>
**扩展文件共享配额是否会影响我的工作负荷或 Azure 文件同步？**
    
    不是。 扩展配额不会影响工作负荷或 Azure 文件同步。

* <a id="open-handles-quota"></a>
**多少个客户端可以同时访问同一文件？**    
    单个文件有 2000 个打开句柄配额。 当你拥有 2000 个打开句柄时，会显示一条错误消息，指示已达到此配额。

* <a id="zip-slow-performance"></a>
**解压 Azure 文件中的文件时性能较慢。我该怎样做？**  
    若要将大量文件传输到 Azure 文件，建议使用 AzCopy（Windows 版；Linux 和 Unix 预览版）或 Azure Powershell。 这些工具已针对网络传输进行了优化。

* <a id="slow-perf-windows-81-2012r2"></a>
**为什么在 Windows Server 2012 R2 或 Windows 8.1 上装载了 Azure 文件共享后性能较慢？**  
    在 Windows Server 2012 R2 和 Windows 8.1 上装载 Azure 文件共享后，有一个已知的问题。 在 2014 年 4 月的 Windows 8.1 和 Windows Server 2012 R2 累积更新中已修补了此问题。 请确保 Windows Server 2012 R2 和 Windows 8.1 的所有实例均应用了此修补程序，以获得最佳性能。 （你应始终通过 Windows 更新获取 Windows 修补程序。）有关更多信息，请查看相关的 Microsoft 识库文章[从 Windows 8.1 或 Server 2012 R2 访问 Azure 文件时性能降低](https://support.microsoft.com/kb/3114025)。

## <a name="features-and-interoperability-with-other-services"></a>功能以及与其他服务的互操作性
* <a id="cluster-witness"></a>
**是否可以将 Azure 文件共享作为 Windows 服务器故障转移群集的文件共享见证？**  
    Azure 文件共享目前不支持此配置。 有关如何为 Azure Blob 存储设置此服务的详细信息，请参阅[部署故障转移群集的云见证](/windows-server/failover-clustering/deploy-cloud-witness)。

* <a id="containers"></a>
**是否可以在 Azure 容器实例上装载 Azure 文件共享？**  
    是，当信息超出容器实例生存期时，通过 Azure 文件共享来保存信息不失为一种绝佳选择。 有关更多信息，请参阅[使用 Azure 容器实例装载 Azure 文件共享](../../container-instances/container-instances-volume-azure-files.md)。

* <a id="rest-rename"></a>
**REST API 中是否有重命名操作？**  
    目前没有。

* <a id="nested-shares"></a>
**是否可以设置嵌套共享？也就是说，能否在共享下使用共享？**  
    不是。 文件共享是可以装载的虚拟驱动程序，因此不支持嵌套共享。

* <a id="ibm-mq"></a>
**如何将 Azure 文件与 IBM MQ 配合使用？**  
    IBM 已发布相关文档，帮助 IBM MQ 客户通过 IBM 服务配置 Azure 文件。 有关更多信息，请参阅[如何通过 Microsoft Azure 文件服务设置 IBM MQ 多实例队列管理器](https://github.com/ibm-messaging/mq-azure/wiki/How-to-setup-IBM-MQ-Multi-instance-queue-manager-with-Microsoft-Azure-File-Service)。

## <a name="see-also"></a>另请参阅
* [在 Windows 中排查 Azure 文件问题](storage-troubleshoot-windows-file-connection-problems.md)
* [在 Linux 中排查 Azure 文件问题](storage-troubleshoot-linux-file-connection-problems.md)
* [对 Azure 文件同步进行故障排除](../file-sync/file-sync-troubleshoot.md)
