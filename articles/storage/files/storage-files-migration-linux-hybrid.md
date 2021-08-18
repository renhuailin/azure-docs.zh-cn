---
title: 从 Linux 迁移到 Azure 文件同步
description: 了解如何使用 Azure 文件同步和 Azure 文件共享将文件从 Linux 服务器位置迁移到混合云部署。
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 03/19/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 18438400054a8f7aa4d718efdff4ef2e116b3bf3
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/15/2021
ms.locfileid: "114462416"
---
# <a name="migrate-from-linux-to-a-hybrid-cloud-deployment-with-azure-file-sync"></a>使用 Azure 文件同步从 Linux 迁移到混合云部署

本文是涉及关键字“NFS”和“Azure 文件同步”的多篇迁移文章中的一篇。请检查本文是否适用于你的场景：

> [!div class="checklist"]
> * 数据源：网络附加存储 (NAS)
> * 迁移路线：包含 SAMBA 的 Linux 服务器 &rArr; Windows Server 2012R2 或更高版本 &rArr; 与 Azure 文件共享同步
> * 本地缓存文件：是，最终目标是 Azure 文件同步部署。

如果你的场景与此不同，请参阅[迁移指南表](storage-files-migration-overview.md#migration-guides)。

Azure 文件同步在配备直接附加存储 (DAS) 的 Windows Server 实例上工作。 它不支持与 Linux 客户端、远程服务器消息块 (SMB) 共享或网络文件系统 (NFS) 共享相互同步。

因此，将文件服务转换为混合部署需要迁移到 Windows Server。 本文将指导你规划和执行此类迁移。

## <a name="applies-to"></a>适用于
| 文件共享类型 | SMB | NFS |
|-|:-:|:-:|
| 标准文件共享 (GPv2)、LRS/ZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |
| 标准文件共享 (GPv2)、GRS/GZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |
| 高级文件共享 (FileStorage)、LRS/ZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |

## <a name="migration-goals"></a>迁移目标

目标是将 Linux Samba 服务器上的共享移到 Windows Server 实例。 然后对混合云部署使用 Azure 文件同步。 在执行这种迁移时，需要保证生产数据的完整性以及迁移期间的可用性。 要满足后一项要求，需将停机时间尽量缩短，使之不会超过或者只略微超过例行维护时段。

## <a name="migration-overview"></a>迁移概述

如 Azure 文件存储[迁移概述文章](storage-files-migration-overview.md)中所述，使用正确的复制工具和方法非常重要。 Linux Samba 服务器直接在本地网络上公开 SMB 共享。 内置于 Windows Server 中的 Robocopy 是在此迁移场景中移动文件的最佳方式。

如果不在 Linux 服务器上运行 Samba，而是要将文件夹迁移到 Windows Server 上的混合部署，则可以使用 Linux 复制工具，而不使用 Robocopy。 请注意复制工具的保真度功能。 查看迁移概述文章中的[迁移基础知识部分](storage-files-migration-overview.md#migration-basics)，了解要在复制工具中查找的内容。

## <a name="phase-1-identify-how-many-azure-file-shares-you-need"></a>第 1 阶段：确定需要多少个 Azure 文件共享

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="phase-2-provision-a-suitable-windows-server-instance-on-premises"></a>第 2 阶段：在本地预配合适的 Windows Server 实例

* 创建一个 Windows Server 2019 实例作为虚拟机或物理服务器。 最低要求为 Windows Server 2012 R2。 也支持 Windows Server 故障转移群集。
* 预配或添加直接附加存储 (DAS)。 不支持网络附加存储 (NAS)。

  如果使用 Azure 文件同步[云分层](../file-sync/file-sync-cloud-tiering-overview.md)功能，则预配的存储量可以小于当前在 Linux Samba 服务器上使用的存储量。 

预配的存储量可以小于当前在 Linux Samba 服务器上使用的存储量。 此配置选择还要求使用 Azure 文件同步[云分层](../file-sync/file-sync-cloud-tiering-overview.md)功能。 但是，在后一阶段将文件从较大的 Linux Samba 服务器空间复制到较小的 Windows Server 卷时，需要分批操作：

  1. 移动一组可装入该磁盘的文件。
  2. 让文件同步和云分层参与进来。
  3. 在卷上创建更多可用空间后，继续复制下一批文件。 或者，查看后面 [RoboCopy 部分](#phase-7-robocopy)中的 RoboCopy 命令了解如何使用新的 `/LFSM` 开关。 使用 `/LFSM` 可以极大地简化 RoboCopy 作业，但它不与你可能依赖的其他一些 RoboCopy 开关兼容。
    
  可以通过在 Windows Server 实例上预配文件在 Linux Samba 服务器上所占用的等量空间来避免这种批处理方法。 考虑在 Windows 上启用重复数据删除。 如果你不想要向 Windows Server 实例提交这么高的存储量，可以在迁移之后、调整云分层策略之前减少卷的大小。 这会创建 Azure 文件共享的较小本地缓存。

部署的 Windows Server 实例的资源配置（计算和 RAM）主要取决于要同步的项（文件和文件夹）数量。 如果有任何顾虑，建议使用性能较高的配置。

[了解如何根据需要同步的项（文件和文件夹）数量来调整 Windows Server 实例的大小。](../file-sync/file-sync-planning.md#recommended-system-resources)

> [!NOTE]
> 前面链接的文章提供了一个表，其中介绍了服务器内存 (RAM) 的范围。 可将服务器的内存调整为较小的数量，但预计初始同步可能会花费更长时间。

## <a name="phase-3-deploy-the-azure-file-sync-cloud-resource"></a>第 3 阶段：部署 Azure 文件同步云资源

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

## <a name="phase-4-deploy-azure-storage-resources"></a>第 4 阶段：部署 Azure 存储资源

在此阶段中，请查阅第 1 阶段中的映射表，并使用它来预配正确数量的 Azure 存储帐户和其中的文件共享。

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

## <a name="phase-5-deploy-the-azure-file-sync-agent"></a>第 5 阶段：部署 Azure 文件同步代理

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

## <a name="phase-6-configure-azure-file-sync-on-the-windows-server-deployment"></a>第 6 阶段：在 Windows Server 部署上配置 Azure 文件同步

要完成此过程，已注册的本地 Windows Server 实例必须准备就绪，并已连接到 Internet。

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!IMPORTANT]
> 云分层是一项 Azure 文件同步功能，它允许本地服务器的存储容量小于云中的存储容量，但仍然提供完整的命名空间。 在本地有用的数据也缓存在本地，以实现快速访问性能。 云分层是每个 Azure 文件同步服务器终结点的可选功能。

> [!WARNING]
> 如果在 Windows server 卷上预配的存储量小于 Linux Samba 服务器上数据占用的存储量，则必须使用云分层。 如果不启用云分层，服务器将不会释放空间来存储所有文件。 暂时将用于迁移的分层策略设置为 99% 的卷可用空间。 确保在迁移完成后返回到云分层设置，并将策略设置为从长期来看更有用的级别。

针对需要配置同步的所有 Azure 文件共享和服务器位置，重复执行相关步骤来创建同步组并添加匹配的服务器文件夹作为服务器终结点。

创建完所有的服务器终结点后，将开始进行同步。 可以创建一个测试文件，并查看它是否从服务器位置同步到连接的 Azure 文件共享（如同步组中的云终结点所述）。

否则，服务器文件夹和 Azure 文件共享这两个位置都为空，并等待数据。 下一步骤开始将文件复制到 Windows Server 实例，以便 Azure 文件同步将它们移到云中。 在已启用云分层的情况下，如果本地卷上的容量耗尽，服务器将开始对文件进行分层。

## <a name="phase-7-robocopy"></a>第 7 阶段：Robocopy

基本迁移方法是使用 Robocopy 来复制文件，并使用 Azure 文件同步来执行同步。

运行首次本地复制，以复制到 Windows Server 目标文件夹：

1. 标识 Linux Samba 服务器上的第一个位置。
1. 标识已配置 Azure 文件同步的 Windows Server 实例上的匹配文件夹。
1. 开始使用 Robocopy 进行复制。

以下 Robocopy 命令将文件从 Linux Samba 服务器的存储复制到 Windows Server 目标文件夹。 Windows Server 会将其同步到 Azure 文件共享。 

如果在 Windows Server 实例上预配的存储量小于 Linux Samba 服务器上文件占用的存储量，则已配置云分层。 当本地 Windows Server 卷已满时，将启动[云分层](../file-sync/file-sync-cloud-tiering-overview.md)，并对已成功同步的文件进行分层。 云分层将生成足够的空间，以便继续从 Linux Samba 服务器进行复制。 云分层每小时检查一次已同步哪些内容，并释放磁盘空间，以满足提供 99% 的卷可用空间这一策略。

Robocopy 移动文件的速度可能比同步到云和本地分层的速度要快，导致耗尽本地磁盘空间。 Robocopy 随后将会失败。 我们建议以一种可以防止出现问题的顺序来处理共享。 例如，考虑不同时对所有共享启动 Robocopy 作业。 或者考虑移动 Windows Server 实例上当前可用空间量能够容纳得下的共享。 如果 Robocopy 作业确实失败，你始终可以重新运行命令，前提是使用以下镜像/清除选项：

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]

## <a name="phase-8-user-cut-over"></a>第 8 阶段：用户完全转移

首次运行 Robocopy 命令时，用户和应用程序仍在访问 Linux Samba 服务器上的文件，并可能更改这些文件。 有可能出现下述情况：Robocopy 处理了一个目录并移至下一个目录，然后源位置 (Linux) 中的用户添加、更改或删除了一个此时不会在当前 Robocopy 运行中处理的文件。 这是预期的行为。

首次运行涉及到将大量数据通过 Azure 文件同步移到 Windows Server 实例和云中。首次复制可能需要很长时间，具体取决于：

* 下载带宽。
* 上传带宽。
* 本地网络速度，以及与之匹配的最佳 Robocopy 线程数
* Robocopy 和 Azure 文件同步需要处理的项（文件和文件夹）数量

在初始运行完成后，再次运行该命令。

第二次运行该命令的速度更快，因为只需传输自上次运行后发生的更改。 在这第二次运行期间，仍可能累积新的更改。

重复此过程，直到你对在特定位置完成 Robocopy 操作所花费的时间在可接受的停机时间范围内感到满意为止。

考虑可接受的停机时间并已准备好将 Linux 位置脱机时，可以更改共享根目录上的 ACL，使用户不再可以访问该位置。 或者，可以执行任何其他适当步骤，防止在 Linux 服务器上更改此文件夹。

运行最后一轮 Robocopy。 它将选取可能已丢失的任何更改。 这最后一步所花费的时间取决于 Robocopy 的扫描速度。 可以通过测量上一轮运行所用的时间来估算时间（相当于停机时间）。

在 Windows Server 文件夹中创建一个共享，并根据情况将 DFS-N 部署调整为指向该文件夹。 请务必设置与 Linux Samba 服务器 SMB 共享上相同的共享级别权限。 如果在 Linux Samba 服务器上使用了本地用户，则需要以 Windows Server 本地用户的形式重新创建这些用户。 此外，对于已由 Robocopy 移到 Windows Server 实例的现有 SID，需将其映射到新的 Windows Server 本地用户的 SID。 如果使用了 Active Directory 帐户和 ACL，Robocopy 会按原样将其移动，无需执行其他操作。

现已完成将一个共享或一组共享迁移到通用根目录或卷（具体取决于第 1 阶段中的映射）的过程。

可以尝试并行运行其中的几个副本。 建议一次处理一个 Azure 文件共享的范围。

> [!WARNING]
> 将 Linux Samba 服务器中的所有数据移到 Windows Server 实例并完成迁移后，在 Azure 门户中返回到所有同步组。 将云分层卷的可用空间百分比调整为更适合缓存利用率的值，例如 20%。 

云分层卷中可用空间的策略作用于可能有多个服务器终结点从中同步的卷级别。 如果你忘记了调整哪怕一个服务器终结点上的可用空间，同步也将继续应用最严格的规则，并尝试使可用磁盘空间保持为 99%。 然后，本地缓存可能无法按预期方式执行。 如果你的目标是为仅包含极少访问的存档数据的卷提供命名空间，并且要将剩余的存储空间预留给其他方案，这种性能可能是可接受的。

## <a name="troubleshoot"></a>疑难解答

最常见的问题是在 Windows Server 端运行 Robocopy 命令失败并出现“卷已满”消息。 云分层每小时进行一次，以从本地 Windows Server 磁盘中撤出已同步的内容。 其目标是实现卷上保持 99% 的可用空间。

让同步继续进行并让云分层释放磁盘空间。 可以在 Windows Server 上的文件资源管理器中观察进度。

当 Windows Server 实例有足够的可用容量时，重新运行该命令可以解决问题。 遇到这种情况时，不会有任何中断，可以放心地继续操作。 再次运行该命令只会带来不便。

查看下一部分中有关排查 Azure 文件同步问题的链接。

## <a name="next-steps"></a>后续步骤

对于 Azure 文件共享和 Azure 文件同步，有更多的内容有待探索。以下文章包含高级选项、最佳做法和故障排除帮助。 这些文章链接到相应的 [Azure 文件共享文档](storage-files-introduction.md)。

* [Azure 文件同步概述](../file-sync/file-sync-planning.md)
* [部署 Azure 文件同步](../file-sync/file-sync-deployment-guide.md)
* [Azure 文件同步故障排除](../file-sync/file-sync-troubleshoot.md)