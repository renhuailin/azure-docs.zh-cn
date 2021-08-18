---
title: 迁移到 Azure 文件共享
description: 了解向 Azure 文件共享的迁移并查找迁移指南。
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 3/18/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: a18421163e39fbcf5c1081c79cf06d982613fafb
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113769124"
---
# <a name="migrate-to-azure-file-shares"></a>迁移到 Azure 文件共享

本文介绍迁移到 Azure 文件共享的基本内容。

本文包含迁移基础知识和迁移指南表。 这些指南有助于将文件移到 Azure 文件共享。 指南根据数据所在的位置以及你要移动到的部署模型（仅限云的模型或混合模型）进行组织。

## <a name="migration-basics"></a>迁移基础知识

Azure 有多种可用的云存储类型。 文件迁移到 Azure 的一个重要方面是确定哪个 Azure 存储选项适合你的数据。

[Azure 文件共享](storage-files-introduction.md)适用于常规用途的文件数据。 这种数据包括你针对其使用本地 SMB 或 NFS 共享的任何内容。 通过 [Azure 文件同步](../file-sync/file-sync-planning.md)，可以在本地运行 Windows Server 的服务器上缓存多个 Azure 文件共享的内容。

对于当前在本地服务器上运行的应用，将文件存储在 Azure 文件共享中可能是一个不错的选择。 你可以将应用移到 Azure，并使用 Azure 文件共享作为共享存储。 还可以考虑将 [Azure 磁盘](../../virtual-machines/managed-disks-overview.md) 用于此场景。

某些云应用不依赖于 SMB 或计算机本地数据访问/共享访问。 对于这些应用，[Azure Blob](../blobs/storage-blobs-overview.md) 之类的对象存储通常是最佳选择。

任何迁移的关键在于，将文件从其当前存储位置移到 Azure 时捕获所有适用的文件保真度。 Azure 存储选项支持的保真度和场景所需的保真度同样有助于选择正确的 Azure 存储。 通常，常规用途的文件数据依赖于文件元数据。 应用数据可能不会。

下面是文件的两个基本组件：

- 数据流：文件的数据流存储文件内容。
- 文件元数据：文件元数据具有以下子组件：
   * 文件特性，如只读
   * 文件权限，可称为 NTFS 权限或文件和文件夹 ACL 
   * 时间戳，最值得注意的是创建时间戳和上次修改时间戳
   * 备用数据流，用于存储更多非标准属性的空间

可以将迁移中的文件保真度定义为能够执行以下操作的能力：

- 将所有适用的文件信息存储在源上。
- 通过迁移工具传输文件。
- 将文件存储在迁移的目标存储中。 </br> 最终，此页上的迁移指南目标是一个或多个 Azure 文件共享。 请考虑这个 [Azure 文件共享不支持的功能/文件保真度列表](/rest/api/storageservices/features-not-supported-by-the-azure-file-service)。

若要确保迁移顺利进行，请[根据需要确定最佳复制工具](#migration-toolbox)，并将存储目标与源相匹配。

考虑到上述信息，可以发现 Azure 中常规用途文件的目标存储是 [Azure 文件共享](storage-files-introduction.md)。

与 Azure Blob 中的对象存储不同，Azure 文件共享可本机存储文件元数据。 Azure 文件共享还会保留文件和文件夹层次结构、特性和权限。 由于位于本地，因此 NTFS 权限可以存储在文件和文件夹。

Active Directory 用户（作为其本地域控制器）可以以本机方式访问 Azure 文件共享。 Azure Active Directory 域服务 (Azure AD DS) 用户也可以。 二者都可使用其当前标识，基于共享权限以及文件和文件夹 ACL 来获取访问权限。 此行为类似于连接到本地文件共享的用户。

备用数据流是文件保真度的主要方面，当前无法存储在 Azure 文件共享中的文件上。 它将在使用 Azure 文件同步时，保留在本地。

详细了解 Azure 文件共享的[本地 Active Directory 身份验证](storage-files-identity-auth-active-directory-enable.md)和 [Azure AD DS 身份验证](storage-files-identity-auth-active-directory-domain-service-enable.md)。

## <a name="migration-guides"></a>迁移指南

下表列出了详细的迁移指南。

如何使用该表：

1. 找到当前存储文件的源系统的行。

1. 选择以下目标之一：

   - 使用 Azure 文件同步在本地缓存 Azure 文件共享内容的混合部署
   - 云中的 Azure 文件共享

   选择与你的选择相匹配的目标列。

1. 在源和目标的交集内，表单元格列出可用的迁移场景。 选择一个场景即可直接链接到详细的迁移指南。

没有链接的场景目前还没有发布的迁移指南。 偶尔检查此表中是否有更新。 新指南出台后将会发布。

| 源 | 目标： </br>混合部署 | 目标： </br>仅限云的部署 |
|:---|:--|:--|
| | 工具组合：| 工具组合： |
| Windows Server 2012 R2 和更高版本 | <ul><li>[Azure 文件同步](../file-sync/file-sync-deployment-guide.md)</li><li>[Azure 文件同步和 Azure DataBox](storage-files-migration-server-hybrid-databox.md)</li></ul> | <ul><li>[通过 RoboCopy 迁移到已装载的 Azure 文件共享](storage-files-migration-robocopy.md)</li><li>通过 Azure 文件同步</li></ul> |
| Windows Server 2012 和更低版本 | <ul><li>通过 DataBox 和 Azure 文件同步迁移到最近的服务器 OS</li><li>使用 Azure 文件同步通过存储迁移服务迁移到最新的服务器，然后上传</li></ul> | <ul><li>使用 Azure 文件同步通过存储迁移服务迁移到最新的服务器</li><li>[通过 RoboCopy 迁移到已装载的 Azure 文件共享](storage-files-migration-robocopy.md)</li></ul> |
| 网络连接存储 (NAS) | <ul><li>[通过 Azure 文件同步 + 上传](storage-files-migration-nas-hybrid.md)</li><li>[通过 DataBox + Azure 文件同步](storage-files-migration-nas-hybrid-databox.md)</li></ul> | <ul><li>[通过 DataBox](storage-files-migration-nas-cloud-databox.md)</li><li>[通过 RoboCopy 迁移到已装载的 Azure 文件共享](storage-files-migration-robocopy.md)</li></ul> |
| Linux/Samba | <ul><li>[Azure 文件同步和 RoboCopy](storage-files-migration-linux-hybrid.md)</li></ul> | <ul><li>[通过 RoboCopy 迁移到已装载的 Azure 文件共享](storage-files-migration-robocopy.md)</li></ul> |
| Microsoft Azure StorSimple 8100 或 8600 系列设备 | <ul><li>[通过专用数据迁移云服务](storage-files-migration-storsimple-8000.md)</li></ul> | <ul><li>[通过专用数据迁移云服务](storage-files-migration-storsimple-8000.md)</li></ul> |
| StorSimple 1200 虚拟设备 | <ul><li>[通过 Azure 文件同步](storage-files-migration-storsimple-1200.md)</li></ul> | |

## <a name="migration-toolbox"></a>迁移工具箱

### <a name="file-copy-tools"></a>文件复制工具

Microsoft 和其他公司提供了几种文件复制工具。 若要为迁移场景选择合适的工具，必须考虑以下基本问题：

* 该工具是否支持文件复制的源位置和目标位置？

* 该工具是否支持源存储位置和目标存储位置之间的网络路径或可用协议（例如 REST、SMB 或 NFS）？

* 该工具是否保留源位置和目标位置支持的必要文件保真度？

    在某些情况下，目标存储不支持与源相同的保真度。 如果目标存储足以满足你的需求，则该工具只必须匹配目标的文件保真功能。

* 该工具是否具有可使其适应你的迁移策略的功能？

    例如，考虑该工具是否可以最大程度地减少停机时间。
    
    当某个工具支持将源镜像到目标的选项时，通常可以在同一源和目标上多次运行该工具，而源仍可访问。

    首次运行该工具时，它会复制大量数据。 此初始运行可能会持续一段时间。 它持续的时间通常比使业务流程的数据源脱机所需的时间长。

    通过将源镜像到目标（与使用 robocopy/MIR 一样），可以在该相同源和目标上再次运行该工具。 该运行的速度要快得多，因为它只需要传输在上次运行后发生的源更改。 通过这种方式重新运行复制工具可显著减少停机时间。

下表对 Microsoft 工具及其当前对 Azure 文件共享的适用性进行了分类：

| 建议 | 工具 | 对 Azure 文件共享的支持 | 对文件保真度的保留 |
| :-: | :-- | :---- | :---- |
|![是，推荐](media/storage-files-migration-overview/circle-green-checkmark.png)| RoboCopy | 支持。 可以将 Azure 文件共享装载为网络驱动器。 | 完全保真。* |
|![是，推荐](media/storage-files-migration-overview/circle-green-checkmark.png)| Azure 文件同步 | 以本机方式集成到 Azure 文件共享中。 | 完全保真。* |
|![是，推荐](media/storage-files-migration-overview/circle-green-checkmark.png)| 存储迁移服务 | 间接支持。 可以在 SMS 目标服务器上将 Azure 文件共享装载为网络驱动器。 | 完全保真。* |
|![是，推荐](media/storage-files-migration-overview/circle-green-checkmark.png)| Data Box | 支持。 | DataBox 完全支持元数据。 |
|![不完全推荐](media/storage-files-migration-overview/triangle-yellow-exclamation.png)| AzCopy </br>最新版本 | 支持，但不完全建议这样做。 | 不支持大规模差异复制，某些文件保真度可能会丢失。 </br>[了解如何将 AzCopy 与 Azure 文件共享配合使用](../common/storage-use-azcopy-files.md) |
|![不完全推荐](media/storage-files-migration-overview/triangle-yellow-exclamation.png)| Azure 存储资源管理器 </br>最新版本 | 支持，但不建议这样做。 | 丢失大部分文件保真度，如 ACL。 支持时间戳。 |
|![不推荐](media/storage-files-migration-overview/circle-red-x.png)| Azure 数据工厂 | 支持。 | 不复制元数据。 |
|||||

*完全保真：达到或超过 Azure 文件共享功能\** 。

### <a name="migration-helper-tools"></a>迁移帮助程序工具

本部分介绍有助于计划和运行迁移的工具。

#### <a name="robocopy-from-microsoft-corporation"></a>Microsoft Corporation 的 RoboCopy

RoboCopy 是最适用于文件迁移的工具之一。 它作为 Windows 的一部分提供。 对于此工具的众多选项，主要的 [RoboCopy 文档](/windows-server/administration/windows-commands/robocopy)是一个有用的资源。

#### <a name="treesize-from-jam-software-gmbh"></a>JAM Software GmbH 的 TreeSize

Azure 文件同步主要随项（文件和文件夹）的数量而非总存储量而缩放。 通过 TreeSize 工具，可确定 Windows Server 卷上的项数。

可以使用该工具在 [Azure 文件同步部署](../file-sync/file-sync-deployment-guide.md)之前创建透视。 部署后启用云分层时，也可以使用它。 在这种场景下，可以看到项数以及哪些目录使用服务器缓存最多。

该工具的测试版本为 4.4.1。 它与云分层文件兼容。 该工具在正常运行期间不会导致分层文件的重调。

## <a name="next-steps"></a>后续步骤

1. 为所需的 Azure 文件共享的部署（仅限云的部署或混合部署）创建一个计划。
1. 查看可用迁移指南列表，查找与 Azure 文件共享的源和部署匹配的详细指南。

关于本文中提到的 Azure 文件存储技术的详细信息：

* [Azure 文件共享概述](storage-files-introduction.md)
* [规划 Azure 文件同步部署](../file-sync/file-sync-planning.md)
* [Azure 文件同步：云分层](../file-sync/file-sync-cloud-tiering-overview.md)