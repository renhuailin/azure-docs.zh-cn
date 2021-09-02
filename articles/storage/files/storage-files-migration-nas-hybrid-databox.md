---
title: 通过 Data Box 从本地 NAS 迁移到 Azure 文件同步
description: 了解如何通过 Azure Data Box 使用 Azure 文件同步将文件从本地网络连接存储 (NAS) 位置迁移到混合云部署。
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 03/5/2021
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 7f60c2e03b666c51769473120097034830f599b4
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/15/2021
ms.locfileid: "114462278"
---
# <a name="use-data-box-to-migrate-from-network-attached-storage-nas-to-a-hybrid-cloud-deployment-by-using-azure-file-sync"></a>使用 Data Box 通过 Azure 文件同步从网络连接存储 (NAS) 迁移到混合云部署

本迁移文章是适用于关键字 NAS、Azure 文件同步和 Azure Data Box 的几篇文章之一。 请检查本文是否适用于你的场景：

> [!div class="checklist"]
> * 数据源：网络连接存储 (NAS)
> * 迁移路线：NAS &rArr; Data Box &rArr; Azure 文件共享 &rArr; 与 Windows Server 同步
> * 本地缓存文件：是，最终目标是完成 Azure 文件同步部署

如果你的场景与此不同，请参阅[迁移指南表](storage-files-migration-overview.md#migration-guides)。

Azure 文件同步在直接附加存储 (DAS) 位置进行。 它不支持同步到网络附加存储 (NAS) 位置。
因此你需要迁移文件。 本文将指导你规划和实施这种迁移。

## <a name="applies-to"></a>适用于
| 文件共享类型 | SMB | NFS |
|-|:-:|:-:|
| 标准文件共享 (GPv2)、LRS/ZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |
| 标准文件共享 (GPv2)、GRS/GZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |
| 高级文件共享 (FileStorage)、LRS/ZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |

## <a name="migration-goals"></a>迁移目标

目标是将 NAS 设备上的共享移到 Windows Server。 然后对混合云部署使用 Azure 文件同步。 在执行这种迁移时，需要保证生产数据的完整性以及迁移期间的可用性。 后者要求尽量减少服务中断时间，因此可在常规维护时段进行迁移，或者仅略微超出这些时段操作。

## <a name="migration-overview"></a>迁移概述

迁移过程包括多个阶段。 你将需要：
- 部署 Azure 存储帐户和文件共享。
- 部署一台运行 Windows Server 的本地计算机。 
- 配置 Azure 文件同步。
- 使用 Robocopy 迁移文件。
- 执行切换。

以下各部分详细介绍了迁移过程的各个阶段。

> [!TIP]
> 如果你返回到本文，请使用屏幕右侧的导航栏转到你离开的迁移阶段。

## <a name="phase-1-determine-how-many-azure-file-shares-you-need"></a>第 1 阶段：确定需要多少个 Azure 文件共享

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="phase-2-deploy-azure-storage-resources"></a>第 2 阶段：部署 Azure 存储资源

在此阶段中，请查阅第 1 阶段中的映射表，并使用它来预配正确数量的 Azure 存储帐户和其中的文件共享。

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

## <a name="phase-3-determine-how-many-azure-data-box-appliances-you-need"></a>第 3 阶段：确定需要多少台 Azure Data Box 设备

请仅在完成上一阶段后，才开始执行此步骤。 此时应创建 Azure 存储资源（存储帐户和文件共享）。 订购 Data Box 时，需指定 Data Box 要将数据移到哪些存储帐户。

在此阶段中，需要将上一阶段中迁移计划的结果映射到可用 Data Box 选项的各种限制。 这些考虑事项将帮助你针对将 NAS 共享移到 Azure 文件共享时要选择哪些 Data Box 选项以及需要多少台这种设备而进行规划。

若要确定需要多少台设备及设备类型，请考虑以下重要限制：

* 任一 Azure Data Box 设备可将数据移到最多 10 个存储帐户中。 
* 每个 Data Box 选项都有其自己的可用容量。 请参阅 [Data Box 选项](#data-box-options)。

请查阅迁移计划以找到你决定创建的存储帐户数以及每个帐户中的共享数。 然后，查看 NAS 上每个共享的大小。 通过组合使用这些信息，你可以进行优化并决定哪台设备应当将数据发送到哪些存储帐户。 可以使用两台 Data Box 设备将文件移到同一存储帐户中，但不要将单个文件共享的内容拆分到两台 Data Box 设备中。

### <a name="data-box-options"></a>Data Box 选项

对于标准迁移，请选择以下三个 Data Box 选项中的一个或多个： 

* Data Box Disk。
  Microsoft 将向你寄送一到五块 SSD 盘，其中每个盘的容量为 8 TiB，所有盘的最大总容量为 40 TiB。 由于加密和文件系统开销，可用容量大约少 20%。 有关详细信息，请参阅 [Data Box Disk 文档](../../databox/data-box-disk-overview.md)。
* Data Box。
  这是最常用的选项。 Microsoft 将向你寄送一台加固型 Data Box 设备，其工作方式类似于 NAS。 它的可用容量为 80 TiB。 有关详细信息，请参阅 [Data Box 文档](../../databox/data-box-overview.md)。
* Data Box Heavy。
  此选项提供一台带轮子的加固型 Data Box 设备，其工作方式类似于 NAS。 该设备容量为 1 PiB。 由于加密和文件系统开销，可用容量大约少 20%。 有关详细信息，请参阅 [Data Box Heavy 文档](../../databox/data-box-heavy-overview.md)。

## <a name="phase-4-provision-a-suitable-windows-server-instance-on-premises"></a>第 4 阶段：在本地预配合适的 Windows Server 实例

在等待 Azure Data Box 设备到货期间，你可以开始审查要与 Azure 文件同步配合使用的一个或多个 Windows Server 实例的需求。

* 以虚拟机或物理服务器的形式创建 Windows Server 2019 实例（最低版本为 Windows Server 2012 R2）。 也支持 Windows Server 故障转移群集。
* 预配或添加直接附加存储。 （DAS，与不受支持的 NAS 相对。）

部署的 Windows Server 实例的资源配置（计算和 RAM）主要取决于要同步的文件和文件夹数量。 如果有任何顾虑，我们建议使用性能更高的配置。

[了解如何根据需要同步的项数来调整 Windows Server 实例的大小。](../file-sync/file-sync-planning.md#recommended-system-resources)

> [!NOTE]
> 前面链接的文章提供了一个表，其中列出了服务器内存 (RAM) 的范围。 可以对你的服务器使用该范围的下限数字，但应该预料到，这会导致初始同步花费的时间显著增加。

## <a name="phase-5-copy-files-onto-your-data-box"></a>第 5 阶段：将文件复制到 Data Box

当 Data Box 到货时，需要将 Data Box 设置为与 NAS 设备直通。 请按照订购的 Data Box 类型的设置文档进行操作：

* [设置 Data Box](../../databox/data-box-quickstart-portal.md)。
* [设置 Data Box Disk](../../databox/data-box-disk-quickstart-portal.md)。
* [设置 Data Box Heavy](../../databox/data-box-heavy-quickstart-portal.md)。

根据 Data Box 的类型，可以使用 Data Box 复制工具。 目前，我们不建议使用这些工具迁移到 Azure 文件共享，因为它们不会以全保真度将文件复制到 Data Box。 请改用 Robocopy。

当 Data Box 到货时，其上已预先预配了可供在订购时指定的每个存储帐户使用的 SMB 共享。

* 如果你的文件进入高级 Azure 文件共享，则每个高级“文件存储”存储帐户都将有一个 SMB 共享。
* 如果文件进入标准存储帐户，则每个标准（GPv1 和 GPv2）存储帐户都将有三个 SMB 共享。 只有以 `_AzFiles` 结尾的文件共享才与迁移相关。 请忽略任何块和页 blob 共享。

按照 Azure Data Box 文档中的步骤进行操作：

1. [连接到 Data Box](../../databox/data-box-deploy-copy-data.md)。
1. 将数据复制到 Data Box。
1. [准备 Data Box 以上传到 Azure](../../databox/data-box-deploy-picked-up.md)。

链接的 Data Box 文档指定了一个 Robocopy 命令。 该命令不适合用于保持文件和文件夹的完全保真度。 请改用以下命令：

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]


## <a name="phase-6-deploy-the-azure-file-sync-cloud-resource"></a>第 6 阶段：部署 Azure 文件同步云资源

在继续学习本指南之前，请等待所有文件均已进入正确的 Azure 文件共享。 传输和引入 Data Box 数据的过程需要一段时间。

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

## <a name="phase-7-deploy-the-azure-file-sync-agent"></a>第 7 阶段：部署 Azure 文件同步代理

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

## <a name="phase-8-configure-azure-file-sync-on-the-windows-server-instance"></a>第 8 阶段：在 Windows Server 实例上配置 Azure 文件同步

要完成此过程，已注册的本地 Windows Server 实例必须准备就绪，并已连接到 Internet。

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

启用云分层功能，并在“初始下载”部分选择“仅限命名空间”。

> [!IMPORTANT]
> 云分层是一项 Azure 文件同步功能，它允许本地服务器的存储容量小于云中的存储容量但本地服务器仍然有完整的命名空间可用。 在本地有用的数据也缓存在本地，以实现快速访问性能。 云分层是可选的。 可为每个 Azure 文件同步服务器终结点单独设置此功能。 如果 Windows Server 实例上没有足够的本地磁盘容量用于保存所有云数据，并且你想要避免从云中下载所有数据，则需要使用此功能。

针对需要进行配置以便同步的所有 Azure 文件共享/服务器位置，请重复相关步骤来创建同步组并添加匹配的服务器文件夹作为服务器终结点。 等待命名空间的同步完成。 以下部分将会介绍如何确保同步完成。

> [!NOTE]
> 创建服务器终结点后，将开始进行同步。 但是，同步需要枚举（发现）你通过 Data Box 移到 Azure 文件共享中的文件和文件夹。 因此，可能需要经过很长时间，云中的命名空间才会出现在服务器上，具体取决于命名空间的大小。

## <a name="phase-9-wait-for-the-namespace-to-fully-appear-on-the-server"></a>第 9 阶段：等待命名空间完全出现在服务器上

在继续执行后续的迁移步骤之前，请等待服务器已完全从云共享下载命名空间。 如果过早地开始将文件移到服务器上，将会造成不必要的上传甚至文件同步冲突。

若要确定服务器是否已完成初始下载同步，请在正在执行同步的 Windows Server 实例上打开事件查看器，并使用 Azure 文件同步遥测事件日志。
遥测事件日志位于事件查看器中的“应用程序和服务\Microsoft\FileSync\代理”下。

搜索最近的 9102 事件。 同步会话完成后，将记录事件 ID 9102。 事件文本中有一个表示下载同步方向的字段。 （`HResult` 需为零，文件需要下载。）
 
需要看到两个连续的此类型事件（包含此内容），才能确保服务器已完成命名空间的下载。 如果两个 9102 事件之间存在其他事件，也是没有问题的。

## <a name="phase-10-run-robocopy-from-your-nas"></a>第 10 阶段：从 NAS 运行 Robocopy

在服务器完成从云共享初始同步整个命名空间后，你可以继续执行此步骤。 必须先完成初始同步才能继续执行此步骤。 有关详细信息，请参阅前一部分。

在此步骤中，你将运行 Robocopy 作业，以将云共享（其中包含自从创建共享分支以来在 NAS 上所做的最新更改）同步到 Data Box。
此次 Robocopy 运行可能很快就能完成，也可能需要一些时间，具体取决于 NAS 共享上发生的数据变动量。

> [!WARNING]
> 由于 Windows Server 2019 中的回归 Robocopy 行为，Robocopy 的 `/MIR` 开关与分层目标目录不兼容。 在此迁移阶段，不能使用 Windows Server 2019 或 Windows 10 客户端。 请在中间 Windows Server 2016 实例上使用 Robocopy。

下面是基本迁移方法：
 - 在 NAS 设备上运行 Robocopy 以同步 Windows Server 实例。 
 - 使用 Azure 文件同步从 Windows Server 同步 Azure 文件共享。

运行首次本地复制，以复制到 Windows Server 目标文件夹：

1. 标识 NAS 设备上的第一个位置。
1. 标识已配置 Azure 文件同步的 Windows Server 实例上的匹配文件夹。
1. 开始使用 Robocopy 进行复制。

以下 Robocopy 命令仅将差异内容（更新的文件和文件夹）从 NAS 存储复制到 Windows Server 目标文件夹。 然后，Windows Server 实例会将其同步到 Azure 文件共享。 

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]

如果在 Windows Server 实例上预配的存储量小于你的文件在 NAS 设备上使用的存储量，则已配置云分层。 当本地 Windows Server 卷已满时，将启动[云分层](../file-sync/file-sync-cloud-tiering-overview.md)，并对已成功同步的文件进行分层。 云分层将生成足够的空间，以便继续从 NAS 设备进行复制。 云分层每小时检查一次，以确定哪些内容已同步，并释放磁盘空间来使卷可用空间达到 99%。

Robocopy 需要移动的文件数可能会超过可在 Windows Server 实例本地存储的文件数。 Robocopy 移动内容的速度可能会比 Azure 文件同步上传文件并将其分层到本地卷之外的速度更快。 在这种情况下，Robocopy 将会失败。 我们建议以一种可以防止出现这种情况的顺序来处理共享。 例如，只移动 Windows Server 实例上的可用空间能够容纳的共享。 或者避免同时为所有共享启动 Robocopy 作业。 好消息是，`/MIR` 开关能够确保仅移动增量内容。 移动增量内容后，重启的作业不需要再次移动该文件。

### <a name="do-the-cutover"></a>执行切换

首次运行 Robocopy 命令时，用户和应用程序仍会访问 NAS 上的文件，并可能会更改这些文件。 Robocopy 将处理一个目录，接着处理下一个目录。 然后，NAS 上的用户可能会在第一个目录中添加、更改或删除某个在当前 Robocopy 运行期间不会处理的文件。 这是预期的行为。

首次运行涉及到将大量变动的数据通过 Azure 文件同步移到 Windows Server 实例和云中。首次复制可能需要很长时间，具体取决于：

* 上传带宽。
* 本地网络速度，以及 Robocopy 线程数与之匹配的情况。
* Robocopy 和 Azure 文件同步需要处理的项（文件和文件夹）的数量。

在初始运行完成后，再次运行该命令。

第二次针对共享运行 Robocopy 将会更快地完成。 Robocopy 只需传输自上次运行后发生的更改。 可以为同一共享运行重复的作业。

如果你认为停机时间可接受，则需要删除用户对基于 NAS 的共享的访问权限。 可以采取任何防止用户更改文件和文件夹的结构及内容的方式来完成此操作。 例如，可将 DFS 命名空间指向某个不存在的位置，或者更改共享上的根 ACL。

最后一次运行 Robocopy。 Robocopy 将拾取已遗漏的任何更改。
这最后一步所花费的时间取决于 Robocopy 的扫描速度。 可以通过测量上次运行的时长来估算该时间（相当于停机时间）。

在 Windows Server 文件夹上创建一个共享，并根据需要调整 DFS-N 部署来指向该文件夹。 请务必设置与 NAS SMB 共享上相同的共享级别权限。 如果你有一个已加入域的企业级 NAS，则用户 SID 将自动匹配，因为用户在 Active Directory 中，而 Robocopy 以全保真度复制文件和元数据。 如果在 NAS 上使用了本地用户，则需要： 
- 重新创建这些用户作为 Windows Server 本地用户。 
- 将已由 Robocopy 移到 Windows Server 实例的现有 SID 映射到新 Windows Server 本地用户的 SID。

现已完成将一个共享或一组共享迁移到通用根目录或卷（具体取决于第 1 阶段中的映射）的过程。

你可以尝试并行运行其中的几个副本。 建议每次处理一个 Azure 文件共享的范围。

## <a name="troubleshooting"></a>疑难解答

最常见的问题是在 Windows Server 端运行 Robocopy 命令失败并出现“卷已满”消息。 云分层每小时进行一次，以从本地 Windows Server 磁盘中撤出已同步的内容。 其目标是使卷上的可用空间达到 99%。

让同步继续进行并让云分层释放磁盘空间。 可以在 Windows Server 实例上的文件资源管理器中观察该进度。

当 Windows Server 实例有足够的可用容量时，再次运行该命令即可解决问题。 遇到这种情况时，不会有任何中断。 可以放心地继续操作。 再次运行该命令只会带来不便。

若要排查 Azure 文件同步问题，请参阅下一部分列出的文章。

## <a name="next-steps"></a>后续步骤

对于 Azure 文件共享和 Azure 文件同步，有更多的知识有待探索。以下文章可帮助你了解高级选项和最佳做法。 其中还提供了故障排除方面的帮助。 在适当的情况下，这些文章会包含 [Azure 文件共享文档](storage-files-introduction.md)的链接。

* [迁移概述](storage-files-migration-overview.md)
* [规划 Azure 文件同步部署](../file-sync/file-sync-planning.md)
* [创建文件共享](storage-how-to-create-file-share.md)
* [Azure 文件同步排除故障](../file-sync/file-sync-troubleshoot.md)