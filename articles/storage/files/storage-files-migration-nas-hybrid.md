---
title: 将本地 NAS 迁移到 Azure 文件同步
description: 了解如何使用 Azure 文件同步和 Azure 文件共享将文件从本地网络连接存储 (NAS) 位置迁移到混合云部署。
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 03/19/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: abefaa06cb8d2c0c815d86c6e1386f82e39c52fe
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/15/2021
ms.locfileid: "114462260"
---
# <a name="migrate-from-network-attached-storage-nas-to-a-hybrid-cloud-deployment-with-azure-file-sync"></a>使用 Azure 文件同步从网络连接存储 (NAS) 迁移到混合云部署

这篇讲解迁移的文章是涉及到“NAS”和“Azure 文件同步”这两个关键词的几篇文章中的一篇。请检查本文是否适用于你的场景：

> [!div class="checklist"]
> * 数据源：网络连接存储 (NAS)
> * 迁移路由：NAS &rArr; Windows Server &rArr; 上传并与 Azure 文件共享同步
> * 本地缓存文件：是，最终目标是 Azure 文件同步部署。

如果你的场景与此不同，请参阅[迁移指南表](storage-files-migration-overview.md#migration-guides)。

Azure 文件同步适用于直接连接存储 (DAS) 位置，它不支持同步到网络连接存储 (NAS) 位置。
这使得文件迁移很有必要，本文将指导你如何计划和执行这种迁移。

## <a name="applies-to"></a>适用于
| 文件共享类型 | SMB | NFS |
|-|:-:|:-:|
| 标准文件共享 (GPv2)、LRS/ZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |
| 标准文件共享 (GPv2)、GRS/GZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |
| 高级文件共享 (FileStorage)、LRS/ZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |

## <a name="migration-goals"></a>迁移目标

目标是将 NAS 应用程序上的共享移动到 Windows Server。 然后，对混合云部署使用 Azure 文件同步。 通常在执行迁移时，需要保证迁移过程中生产数据的完整性和可用性。 后者要求尽量减少故障时间，因此可在正常维护时段进行迁移，或者仅略微超出这些时段操作。

## <a name="migration-overview"></a>迁移概述

如 Azure 文件存储的[“迁移概述”文章](storage-files-migration-overview.md)中所述，使用正确的复制工具和方法非常重要。 NAS 设备直接在本地网络上公开 SMB 共享。 Windows Server 中内置的 RoboCopy 是在此迁移场景中移动文件的最佳方式。

- 第 1 阶段：[确定需要多少个 Azure 文件共享](#phase-1-identify-how-many-azure-file-shares-you-need)
- 第 2 阶段：[在本地预配合适的 Windows Server](#phase-2-provision-a-suitable-windows-server-on-premises)
- 第 3 阶段：[部署 Azure 文件同步云资源](#phase-3-deploy-the-azure-file-sync-cloud-resource)
- 第 4 阶段：[部署 Azure 存储资源](#phase-4-deploy-azure-storage-resources)
- 第 5 阶段：[部署 Azure 文件同步代理](#phase-5-deploy-the-azure-file-sync-agent)
- 第 6 阶段：[在 Windows Server 上配置 Azure 文件同步](#phase-6-configure-azure-file-sync-on-the-windows-server)
- 第 7 阶段：[RoboCopy](#phase-7-robocopy)
- 第 8 阶段：[用户缩减](#phase-8-user-cut-over)

## <a name="phase-1-identify-how-many-azure-file-shares-you-need"></a>第 1 阶段：确定需要多少个 Azure 文件共享

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="phase-2-provision-a-suitable-windows-server-on-premises"></a>第 2 阶段：在本地预配合适的 Windows Server

* 以虚拟机或物理服务器的形式创建 Windows Server 2019（最低为 2012R2）。 也支持 Windows Server 故障转移群集。
* 预配或添加直接连接存储（DAS 与 NAS 相对，后者不受支持）。

    预配的存储量可小于当前在 NAS 设备上使用的存储量。 此配置选择要求你同时使用 Azure 文件同步[云分层](../file-sync/file-sync-cloud-tiering-overview.md)功能。
    但是，在后一阶段将文件从较大的 NAS 空间复制到较小的 Windows Server 卷时，需要分批操作：

    1. 移动一组可装入该磁盘的文件
    2. 让文件同步和云分层参与进来
    3. 在卷上创建更多可用空间后，继续复制下一批文件。 或者，查看后面 [RoboCopy 部分](#phase-7-robocopy)中的 RoboCopy 命令了解如何使用新的 `/LFSM` 开关。 使用 `/LFSM` 可以极大地简化 RoboCopy 作业，但它不与你可能依赖的其他一些 RoboCopy 开关兼容。
    
    可通过在 Windows Server 上预配文件在 NAS 设备上所占用的等效空间，避免这种批处理方法。 请考虑在 NAS / Windows 上删除重复数据。 如果不想要永远都向 Windows Server 提交这么高的存储量，可在迁移之后、调整云分层策略之前减少卷的大小。 这会创建 Azure 文件共享的较小本地缓存。

你部署的 Windows Server 的资源配置（计算和 RAM）主要取决于你要同步的项（文件和文件夹）的数量。 如果有任何顾虑，建议使用性能较高的配置。

[了解如何根据需要同步的项（文件和文件夹）的数量来调整 Windows Server 的大小。](../file-sync/file-sync-planning.md#recommended-system-resources)

> [!NOTE]
> 前面链接的文章提供了一个表，其中有服务器内存 (RAM) 的范围。 可将服务器的内存调整为较小的数量，但可以预料到，这样调整可能会导致初始同步花费的时间显著增加。

## <a name="phase-3-deploy-the-azure-file-sync-cloud-resource"></a>第 3 阶段：部署 Azure 文件同步云资源

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

## <a name="phase-4-deploy-azure-storage-resources"></a>第 4 阶段：部署 Azure 存储资源

在此阶段中，请查阅第 1 阶段中的映射表，并使用它来预配正确数量的 Azure 存储帐户和其中的文件共享。

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

## <a name="phase-5-deploy-the-azure-file-sync-agent"></a>第 5 阶段：部署 Azure 文件同步代理

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

## <a name="phase-6-configure-azure-file-sync-on-the-windows-server"></a>第 6 阶段：在 Windows Server 上配置 Azure 文件同步

要执行此操作，已注册的本地 Windows Server 必须已准备就绪，并且已连接到 Internet。

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!IMPORTANT]
> 云分层是一项 AFS 功能，它允许本地服务器的存储容量小于云中的存储容量，但仍然提供完整的命名空间。 在本地有用的数据也缓存在本地，以实现快速访问性能。 云分层是每个 Azure 文件同步服务器终结点的一项可选功能。

> [!WARNING]
> 如果在 Windows server 卷上预配的存储量小于 NAS 设备上数据占用的存储量，则必须使用云分层。 如果不启用云分层，服务器将会不会释放空间来存储所有文件。 将分层策略（它暂时用于迁移）设置为 99% 的卷可用空间。 请确保在迁移完成后返回到云分层设置，并将其设置为从长期来看更有用的级别。

针对需要配置同步的所有 Azure 文件共享/服务器位置，重复执行相关步骤来创建同步组并添加匹配的服务器文件夹作为服务器终结点。

创建所有服务器终结点后，将开始进行同步。 可创建一个测试文件，并查看它是否从服务器位置同步到连接的 Azure 文件共享（如同步组中的云终结点所述）。

否则，服务器文件夹和 Azure 文件共享这两个位置都为空，并在任一位置等待数据。 下一步骤开始将文件复制到 Windows Server，以便 Azure 文件同步将它们移到云中。 在已启用云分层的情况下，如果本地卷上的容量耗尽，服务器将开始对文件进行分层。

## <a name="phase-7-robocopy"></a>第 7 阶段：RoboCopy

基本迁移方法是通过 RoboCopy 从 NAS 设备迁移到 Windows Server，并使用 Azure 文件同步来同步到 Azure 文件共享。

运行首次本地复制，以复制到 Windows Server 目标文件夹：

* 标识 NAS 设备上的第一个位置。
* 标识已配置 Azure 文件同步的 Windows Server 上的匹配文件夹。
* 开始使用 RoboCopy 进行复制

以下 RoboCopy 命令会将文件从 NAS 存储复制到 Windows Server 目标文件夹。 Windows Server 会将其同步到 Azure 文件共享。 

如果在 Windows Server 上预配的存储量小于 NAS 设备上文件占用的存储量，则已配置云分层。 当本地 Windows Server 卷变满时，将启动[云分层](../file-sync/file-sync-cloud-tiering-overview.md)，并对已成功同步的文件进行分层。 云分层将生成足够的空间，以便继续从 NAS 设备进行复制。 云分层每小时检查一次，以查看已同步的内容，并释放磁盘空间来使卷可用空间达到 99%。
RoboCopy 移动文件的速度可能比同步到云和本地分层的速度快，从而耗尽本地磁盘空间。 因此，RoboCopy 将失败。 建议以一种可防止出现这种情况的顺序来处理共享。 例如，不要同时为所有共享启动 RoboCopy 作业，或者只移动其大小未超过 Windows Server 上当前可用空间量的共享，等等。

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]

## <a name="phase-8-user-cut-over"></a>第 8 阶段：用户缩减

首次运行 RoboCopy 命令时，用户和应用程序仍访问 NAS 上的文件，并可能更改这些文件。 有可能出现这种情况：RoboCopy 处理了一个目录并移至下一个目录，然后源位置 (NAS) 中的用户添加、更改或删除了一个此时不会在当前 RoboCopy 运行中处理的文件。 这是预期的行为。

第一次运行是将大量数据通过 Azure 文件同步移动到 Windows Server 和云中。首次复制可能需要很长时间，具体取决于：

* 下载带宽
* 上传带宽
* 本地网络速度，以及与之匹配的最佳 RoboCopy 线程数
* RoboCopy 和 Azure 文件同步同时需要处理的项（文件和文件夹）的数量

在初始运行完成后，再次运行该命令。

第二次将会更快完成，因为只需要传输自上次运行后发生的更改。 在这第二次运行期间，仍可能会累积新的更改。

重复此过程，直到你对特定位置完成 RoboCopy 所花费的时间在可接受的故障时间范围内感到满意为止。

如果你认为故障时间是可接受的，则需要移除用户对基于 NAS 的共享的访问权限。 为此，可执行会阻止用户更改文件和文件夹结构及内容的任何步骤。 例如，使 DFS 命名空间指向不存在的位置，或者更改共享上的根 ACL。

运行最后一轮 RoboCopy。 它将选取可能已丢失的任何更改。
这最后一步所花费的时间取决于 RoboCopy 扫描的速度。 可通过测量上一轮运行所用的时间来估算时间（相当于故障时间）。

在 Windows Server 文件夹上创建一个共享，并根据需要调整 DFS-N 部署来指向该文件夹。 请务必设置与 NAS SMB 共享上相同的共享级别权限。 如果你有一个已加入域的企业级 NAS，那么用户 SID 将自动匹配，这是因为用户在 Active Directory 中，且 RoboCopy 会完全保真地复制文件和元数据。 如果在 NAS 上使用了本地用户，则需要将这些用户重新创建为 Windows Server 本地用户，并将 RoboCopy 移动到 Windows Server 上的现有 SID 映射到新的 Windows Server 本地用户的 SID。

你已将共享/共享组迁移到公共根或卷中。 （具体取决于你在第 1 阶段中的映射）

你可以尝试并行运行其中的几个副本。 建议一次处理一个 Azure 文件共享的范围。

> [!WARNING]
> 将所有数据从 NAS 移动到 Windows Server 并完成迁移后：返回到 Azure 门户中的所有同步组，并将云分层卷的可用空间百分比值调整为更适合缓存利用率的值，例如 20%。 

云分层卷中的可用空间策略作用于可能有多个服务器终结点从中同步的卷级别。 如果你忘记了调整哪怕一个服务器终结点上的可用空间，同步也将继续应用最严格的规则，并尝试将可用磁盘空间保留为 99%，从而使本地缓存无法按预期运行。 除非你的目标是为仅包含极少访问的存档数据的卷提供命名空间，并且要将剩余的存储空间预留给其他方案。

## <a name="troubleshoot"></a>疑难解答

最可能遇到的问题是在 Windows Server 端运行 RoboCopy 命令失败并出现“卷已满”消息。 云分层每小时进行一次，以从本地 Windows Server 磁盘撤出已同步的内容。 其目标是使卷上的可用空间达到 99%。

让同步继续进行并让云分层释放磁盘空间。 可以在 Windows Server 上的文件资源管理器中观察进度。

当 Windows Server 有足够的可用容量时，重新运行该命令可以解决此问题。 遇到这种情况时，不会有任何中断，可以放心地继续操作。 再次运行该命令只会带来不便。

查看下一部分中有关排查 Azure 文件同步问题的链接。

## <a name="next-steps"></a>后续步骤

对于 Azure 文件共享和 Azure 文件同步，有更多的内容有待探索。以下文章可帮助了解高级选项和最佳做法，还包含疑难解答帮助。 这些文章链接到相应的 [Azure 文件共享文档](storage-files-introduction.md)。

* [Azure 文件同步概述](../file-sync/file-sync-planning.md)
* [部署 Azure 文件同步](../file-sync/file-sync-deployment-guide.md)
* [Azure 文件同步故障排除](../file-sync/file-sync-troubleshoot.md)