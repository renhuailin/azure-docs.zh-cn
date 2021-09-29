---
title: Azure 文件同步简介 | Microsoft Docs
description: 本文概述 Azure 文件同步服务，使用该服务可以通过行业标准 SMB 协议在云中创建和使用网络文件共享。
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 04/19/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: e20b911b16ee7ebfe1af4984c9fe701a54b855cd
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128664591"
---
# <a name="what-is-azure-file-sync"></a>什么是 Azure 文件同步？

借助 Azure 文件同步，可以在 Azure 文件存储中集中管理组织的文件共享，同时又能保留 Windows 文件服务器的灵活性、性能和兼容性。 虽然某些用户可能选择在本地保留其数据的完整副本，但 Azure 文件同步还可以将 Windows Server 转换为 Azure 文件共享的快速缓存。 可以使用 Windows Server 上可用的任意协议本地访问数据，包括 SMB、NFS 和 FTPS。 并且可以根据需要在世界各地具有多个缓存。

## <a name="videos"></a>视频

| Azure 文件同步简介 | Azure 文件存储与同步 (Ignite 2019)  |
|-|-|
| [![Azure 文件同步简介视频截屏 - 单击播放！](../files/media/storage-files-introduction/azure-file-sync-video-snapshot.png)](https://www.youtube.com/watch?v=Zm2w8-TRn-o) | [![Azure 文件与同步演示截屏 - 单击播放！](../files/media/storage-files-introduction/ignite-2018-video.png)](https://www.youtube.com/embed/6E2p28XwovU) |

## <a name="benefits-of-azure-file-sync"></a>Azure 文件同步的优势

### <a name="cloud-tiering"></a>云分层

启用云分层后，最常访问的文件将在本地服务器上缓存，最少访问的文件会分层到云中。 你可以控制用于缓存的本地磁盘空间大小。 分层文件可按需快速召回，这可提供无缝体验，还可削减成本，因为只需在本地存储一小部分数据。 有关云分层的详细信息，请参阅[云分层概述](file-sync-cloud-tiering-overview.md)。

### <a name="multi-site-access-and-sync"></a>多站点访问和同步

Azure 文件同步非常适合用于分布式访问方案。 对于每个办公室，你都可以在 Azure 文件同步部署中预配本地 Windows Server。 对一个办公室中的服务器所做的更改会自动同步到所有其他办公室中的服务器。

### <a name="business-continuity-and-disaster-recovery"></a>业务连续性和灾难恢复

Azure 文件同步由 Azure 文件存储提供支持，可为高可用性存储提供多种冗余选项。 由于 Azure 包含数据的可复原副本，因此本地服务器将成为可释放的缓存设备，并可通过将新服务器添加到 Azure 文件同步部署来从失败的服务器进行恢复。 你可以预配另一个 Windows Server，在它上面安装 Azure 文件同步代理，然后将其添加到 Azure 文件同步部署，而无需从本地备份中进行还原。 Azure 文件同步在下载数据之前下载文件命名空间，以便服务器可以尽快启动并运行。 为了更快地进行恢复，可以在部署中包含一个温备用服务器，或者可以将 Azure 文件同步与 Windows 群集一起使用。

## <a name="cloud-side-backup"></a>云端备份

通过使用 Azure 备份在云中执行集中式备份来减少本地备份开支。 Azure 文件存储 SMB 共享具有本机快照功能，可以使用 Azure 备份自动执行此过程，以便计划备份并管理其保留。 Azure 备份还与你的本地服务器集成，因此，当还原到云时，这些更改会自动下载到你的 Windows Server 中。

## <a name="next-steps"></a>后续步骤

- [规划 Azure 文件同步部署](file-sync-planning.md)
- [云分层概述](file-sync-cloud-tiering-overview.md)
- [监视 Azure 文件同步](file-sync-monitoring.md)
