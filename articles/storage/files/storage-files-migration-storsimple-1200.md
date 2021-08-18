---
title: 迁移 StorSimple 1200 到 Azure 文件同步
description: 了解如何将 StorSimple 1200 系列虚拟设备迁移到 Azure 文件同步。
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 03/09/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 96cc983cba992452882a8f3307974e83683c39a9
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/15/2021
ms.locfileid: "114462173"
---
# <a name="storsimple-1200-migration-to-azure-file-sync"></a>迁移 StorSimple 1200 到 Azure 文件同步

StorSimple 1200 系列是在本地数据中心运行的虚拟设备。 可以将此设备中的数据迁移到 Azure 文件同步环境。 Azure 文件同步是 StorSimple 设备可迁移到的、默认的、策略性的长期 Azure 服务。

StorSimple 1200 系列将于 2022 年 12 月[停用](https://support.microsoft.com/en-us/lifecycle/search?alpha=StorSimple%201200%20Series)。  请务必尽快开始进行迁移规划。 本文提供成功迁移到 Azure 文件同步所需的背景知识和迁移步骤。 

## <a name="applies-to"></a>适用于
| 文件共享类型 | SMB | NFS |
|-|:-:|:-:|
| 标准文件共享 (GPv2)、LRS/ZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |
| 标准文件共享 (GPv2)、GRS/GZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |
| 高级文件共享 (FileStorage)、LRS/ZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |

## <a name="azure-file-sync&quot;></a>Azure 文件同步

> [!IMPORTANT]
> Microsoft 致力于帮助客户完成迁移。 若要获取自定义迁移计划，或者在迁移过程中需要帮助，请向 AzureFilesMigration@microsoft.com 发送电子邮件。

Azure 文件同步是一种 Microsoft 云服务，基于两个主要组件：

* 文件同步和云分层。
* 作为 Azure 中的原生存储的文件共享，可通过多个协议（例如 SMB 和文件 REST）访问。 Azure 文件共享相当于 Windows Server 上的文件共享，后者可作为网络驱动器进行本机装载。 它支持重要的文件保真度特性，如属性、权限和时间戳。 与 StorSimple 不同的是，无需使用应用程序/服务来解释存储在云中的文件和文件夹。 对于在云中存储常规用途文件服务器数据以及部分应用程序数据来说，它是理想的方法，具有灵活性。

本文重点介绍迁移步骤。 如果要在迁移之前详细了解 Azure 文件同步，我们建议阅读以下文章：

* [Azure 文件同步 - 概述](../file-sync/file-sync-planning.md &quot;概述")
* [Azure 文件同步 - 部署指南](../file-sync/file-sync-deployment-guide.md)

## <a name="migration-goals"></a>迁移目标

目标是保证生产数据的完整性并保证可用性。 要满足后一项要求，需将停机时间尽量缩短，使之不会超过或者只略微超过例行维护时段。

## <a name="storsimple-1200-migration-path-to-azure-file-sync"></a>StorSimple 1200 迁移到 Azure 文件同步的路径

运行 Azure 文件同步代理需要本地 Windows Server。 Windows Server 可以使用的最低版本为 2012 R2 服务器，但最好为 Windows Server 2019。

有多种备用迁移路径，如果要记录所有这些路径，并说明它们与本文中建议作为最佳做法的路线相比应承担的风险或不足的原因，文章将会过长，因此不会全部说明。

:::image type="content" source="media/storage-files-migration-storsimple-shared/storsimple-1200-migration-overview.png" alt-text="本文后面的步骤迁移路线概述。":::

上图描述了对应于本文各部分的步骤。

### <a name="step-1-provision-your-on-premises-windows-server-and-storage"></a>步骤 1：预配本地 Windows Server 和存储

1. 以虚拟机或物理服务器的形式创建 Windows Server 2019（最低为 2012R2）。 也支持 Windows Server 故障转移群集。
2. 预配或添加直接连接存储（DAS 与 NAS 相对，后者不受支持）。 Windows Server 存储的大小必须等于或大于虚拟 StorSimple 1200 设备的可用容量的大小。

### <a name="step-2-configure-your-windows-server-storage"></a>步骤 2：配置 Windows Server 存储

在此步骤中，将 StorSimple 存储结构（卷和共享）映射到 Windows Server 存储结构。
如果你计划对存储结构进行更改（即卷的数量、数据文件夹与卷的关联或当前 SMB/NFS 共享之上或之下的子文件夹结构），现在就是考虑这些更改的时间。
在配置 Azure 文件同步后更改文件和文件夹结构非常麻烦，应避免这样做。
本文假设你执行的是 1:1 映射，因此在按照本文中的步骤操作时，必须考虑映射更改。

* 在 Windows Server 系统卷上，任何生产数据不得终止。 系统卷不支持云分层。 但是，迁移以及持续操作（如 StorSimple 替换操作）需要此功能。
* 在 Windows Server 上预配与 StorSimple 1200 虚拟设备相同数量的卷。
* 配置所需的任何 Windows Server 角色、功能和设置。 建议你选择加入 Windows Server 更新以使你的操作系统保持安全和最新。 同样，我们建议选择 Microsoft 更新以使 Microsoft 应用程序保持最新，包括 Azure 文件同步代理。
* 阅读以下步骤之前，请不要配置任何文件夹或共享。

### <a name="step-3-deploy-the-first-azure-file-sync-cloud-resource"></a>步骤 3：部署第一个 Azure 文件同步云资源

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

### <a name="step-4-match-your-local-volume-and-folder-structure-to-azure-file-sync-and-azure-file-share-resources"></a>步骤 4：将本地卷和文件夹结构与 Azure 文件同步和 Azure 文件共享资源匹配

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="step-5-provision-azure-file-shares"></a>步骤 5：预配 Azure 文件共享

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

#### <a name="storage-account-settings"></a>存储帐户设置

可以对存储帐户进行许多配置。 应将以下清单用于配置存储帐户。 例如，你可以在迁移完成后更改网络配置。 

> [!div class="checklist"]
> * 大型文件共享：已启用 - 大型文件共享可提高性能，并使共享中的存储容量最高可达 100TiB。
> * 防火墙和虚拟网络：已禁用 - 不要配置任何 IP 限制或限制存储帐户对特定 VNET 的访问。 在迁移过程中使用存储帐户的公共终结点。 必须允许来自 Azure VM 的所有 IP 地址。 迁移后，最好在存储帐户上配置任何防火墙规则。
> * 专用终结点：支持 - 可以启用专用终结点，但使用公共终结点进行迁移，并且必须保持公共终结点可用。

### <a name="step-6-configure-windows-server-target-folders"></a>步骤 6：配置 Windows Server 目标文件夹

在前面的步骤中，你已考虑了确定同步拓扑组件的所有特性。 现在，准备好服务器来接收要上传的文件。

创建“所有”文件夹，将每个文件夹同步到其自己的 Azure 文件共享。
务必遵循前面所述的文件夹结构。 例如，如果决定将多个本地 SMB 共享一起同步到单个 Azure 文件共享中，则需要将其放在卷上的通用根文件夹下。 立即在卷上创建此目标根文件夹。

你预配的 Azure 文件共享的数量应等于你在此步骤中创建的文件夹数 + 要在根级别同步的卷数量。

### <a name="step-7-deploy-the-azure-file-sync-agent"></a>步骤 7：部署 Azure 文件同步代理

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

### <a name="step-8-configure-sync"></a>步骤 8：配置同步

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!WARNING]
> **确保启用云分层！** 如果本地服务器没有足够的空间来存储 StorSimple 云存储中的数据的总大小，则这是必需的。 将分层策略（它暂时用于迁移）设置为 99% 的卷可用空间。

针对需要配置同步的所有 Azure 文件共享/服务器位置，重复执行相关步骤来创建同步组并添加匹配的服务器文件夹作为服务器终结点。

### <a name="step-9-copy-your-files"></a>步骤 9：复制文件

基本迁移方法是通过 RoboCopy 从 StorSimple 虚拟设备迁移到 Windows Server，并使用 Azure 文件同步来同步到 Azure 文件共享。

运行首次本地复制，以复制到 Windows Server 目标文件夹：

* 确定虚拟 StorSimple 设备上的第一个位置。
* 标识已配置 Azure 文件同步的 Windows Server 上的匹配文件夹。
* 开始使用 RoboCopy 进行复制

以下 RoboCopy 命令将文件从 StorSimple Azure 存储撤回到本地 StorSimple，然后将其移到 Windows Server 目标文件夹。 Windows Server 会将其同步到 Azure 文件共享。 当本地 Windows Server 卷变满时，将启动云分层，并对已成功同步的文件进行分层。 云分层将生成足够的空间，以便继续从 StorSimple 虚拟设备进行复制。 云分层每小时检查一次，以查看已同步的内容，并释放磁盘空间来使卷可用空间达到 99%。

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]

首次运行 RoboCopy 命令时，用户和应用程序仍访问 StorSimple 文件和文件夹，并可能更改这些文件。 有可能出现这种情况：RoboCopy 处理了一个目录并移至下一个目录，然后源位置 (StorSimple) 中的用户添加、更改或删除了一个此时不会在当前 RoboCopy 运行中处理的文件。 没有问题。

第一次运行是将大量数据移动回本地，然后移动到 Windows Server，并通过 Azure 文件同步备份到云中。此操作可能需要很长时间，具体取决于：

* 下载带宽
* StorSimple 云服务的撤回速度
* 上传带宽
* 需要任一服务处理的项（文件和文件夹）的数量

在初始运行完成后，再次运行该命令。

第二次将会更快完成，因为只需要传输自上次运行后发生的更改。 这些更改可能是 StorSimple 的本地更改，因为它们是最新的。 这会进一步缩短时间，因为降低了从云撤回的需求。 在这第二次运行期间，仍可能会累积新的更改。

重复此过程，直到你对完成所需的时间是可接受的停机时间这一点感到满意。

如果你认为停机时间可接受，并且你已准备好使 StorSimple 位置脱机，则立即执行此操作：例如，删除 SMB 共享，以便没有用户可以访问该文件夹，或采取任何其他合适的步骤来防止 StorSimple 上此文件夹中的内容发生更改。

运行最后一轮 RoboCopy。 它将选取可能已丢失的任何更改。
这最后一步所花费的时间取决于 RoboCopy 扫描的速度。 可通过测量上一轮运行所用的时间来估算时间（相当于故障时间）。

在 Windows Server 文件夹上创建一个共享，并根据需要调整 DFS-N 部署来指向该文件夹。 请务必设置与 StorSimple SMB 共享上相同的共享级别权限。

你已将共享/共享组迁移到公共根或卷中。 （具体取决于你所映射并决定需要存储到同一 Azure 文件共享的内容。）

你可以尝试并行运行其中的几个副本。 建议一次处理一个 Azure 文件共享的范围。

> [!WARNING]
> 将所有数据从 StorSimple 移动到 Windows Server 并完成迁移后：返回到 Azure 门户中的所有同步组，并将云分层卷的可用空间百分比值调整为更适合缓存利用率的值，例如 20%。 

云分层卷中的可用空间策略作用于可能有多个服务器终结点从中同步的卷级别。 如果你忘记了调整哪怕一个服务器终结点上的可用空间，同步也将继续应用最严格的规则，并尝试将可用磁盘空间保留为 99%，从而使本地缓存无法按预期运行。 除非你的目标是为仅包含极少访问的存档数据的卷提供命名空间。

## <a name="troubleshoot"></a>疑难解答

最可能遇到的问题是在 Windows Server 端运行 RoboCopy 命令失败并出现“卷已满”消息。 如果是这样，下载速度可能比上传速度快。 云分层每小时进行一次，以从本地 Windows Server 磁盘撤出已同步的内容。

让同步继续进行并让云分层释放磁盘空间。 可以在 Windows Server 上的文件资源管理器中观察进度。

当 Windows Server 有足够的可用容量时，重新运行该命令可以解决此问题。 遇到这种情况时，不会有任何中断，可以放心地继续操作。 再次运行该命令只会带来不便。

还可能会遇到其他 Azure 文件同步问题。
尽管不太可能，但如果出现这种情况，请查看 Azure 文件同步故障排除指南的链接。

[!INCLUDE [storage-files-migration-robocopy-optimize](../../../includes/storage-files-migration-robocopy-optimize.md)]

## <a name="relevant-links"></a>相关链接

迁移内容：

* [StorSimple 8000 系列迁移指南](storage-files-migration-storsimple-8000.md)

Azure 文件同步内容：

* [Azure 文件同步概述](../file-sync/file-sync-planning.md)
* [部署 Azure 文件同步](../file-sync/file-sync-deployment-guide.md)
* [Azure 文件同步故障排除指南](../file-sync/file-sync-troubleshoot.md)