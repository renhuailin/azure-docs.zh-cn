---
title: 通过 Azure DataBox 从本地 NAS 迁移到 Azure 文件同步
description: 了解如何通过 Azure DataBox 使用 Azure 文件同步将文件从本地网络连接存储 (NAS) 位置迁移到混合云部署。
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 03/5/2021
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 144b2f23e40f315441c3de2482ae8aeffe77ec75
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "102583448"
---
# <a name="use-databox-to-migrate-from-network-attached-storage-nas-to-a-hybrid-cloud-deployment-with-azure-file-sync"></a>使用 DataBox 通过 Azure 文件同步从网络连接存储 (NAS) 迁移到混合云部署

本迁移文章是涉及关键字 NAS、Azure 文件同步和 Azure DataBox 的几篇文章之一。 请检查本文是否适用于你的场景：

> [!div class="checklist"]
> * 数据源：网络连接存储 (NAS)
> * 迁移路由：NAS &rArr; DataBox &rArr; Azure 文件共享 &rArr; 与 Windows Server 同步
> * 在本地缓存文件：是，最终目标是一个 Azure 文件同步部署。

如果你的场景与此不同，请参阅[迁移指南表](storage-files-migration-overview.md#migration-guides)。

Azure 文件同步适用于直接连接存储 (DAS) 位置，它不支持同步到网络连接存储 (NAS) 位置。
这使得文件迁移很有必要，本文将指导你如何计划和执行这种迁移。

## <a name="migration-goals"></a>迁移目标

目标是将 NAS 应用程序上的共享移动到 Windows Server。 然后，利用 Azure 文件同步来实现混合云部署。 在执行这种迁移时，需要保证生产数据的完整性以及迁移期间的可用性。 要满足后一项要求，需将停机时间尽量缩短，使之不会超过或者只略微超过例行维护时段。

## <a name="migration-overview"></a>迁移概述

迁移过程包括多个阶段。 你将需要部署 Azure 存储帐户和文件共享，部署本地 Windows Server，配置 Azure 文件同步，使用 RoboCopy 进行迁移，最后执行切换。 以下各部分详细介绍了迁移过程的各个阶段。

> [!TIP]
> 如果你返回到本文，请使用右侧的导航栏转到你离开的迁移阶段。

## <a name="phase-1-identify-how-many-azure-file-shares-you-need"></a>第 1 阶段：确定需要多少个 Azure 文件共享

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="phase-2-deploy-azure-storage-resources"></a>第 2 阶段：部署 Azure 存储资源

在此阶段中，请查阅第 1 阶段中的映射表，并使用它来预配正确数量的 Azure 存储帐户和其中的文件共享。

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

## <a name="phase-3-determine-how-many-azure-databox-appliances-you-need"></a>第 3 阶段：确定需要多少台 Azure DataBox 设备

只有在完成上一阶段后，才应开始此步骤。 此时应创建 Azure 存储资源（存储帐户和文件共享）。 在订购 DataBox 时，你需要指定 DataBox 将数据移动到哪些存储帐户。

在此阶段中，你需要将上一阶段中迁移计划的结果映射到可用 DataBox 选项的各种限制。 这些考虑事项将帮助你针对将你的 NAS 共享移动到 Azure 文件共享时应选择哪些 DataBox 选项以及需要多少台这种设备而进行规划。

若要确定你需要哪种类型的设备以及需要多少台，请考虑以下重要限制：

* 任何 Azure DataBox 都可以将数据移动到最多 10 个存储帐户中。 
* 每个 DataBox 选项都有其各自的可用容量。 请参阅 [DataBox 选项](#databox-options)。

有关你决定创建的存储帐户的数量以及每个帐户中的共享数量，请查阅你的迁移计划。 然后，查看 NAS 上每个共享的大小。 通过组合使用这些信息，你可以进行优化并决定哪台设备应当将数据发送到哪些存储帐户。 你可以让两台 DataBox 设备将文件移动到同一存储帐户中，但不要将单个文件共享的内容拆分到 2 台 DataBox 中。

### <a name="databox-options"></a>DataBox 选项

对于标准迁移，应当选择以下三个 DataBox 选项中的一个或多个： 

* DataBox 磁盘：Microsoft 将向你寄送一到五个 SSD 磁盘，其中每个磁盘的容量为 8 TiB，所有磁盘的总容量最多为 40 TiB。 由于加密和文件系统开销，可用容量大约少 20%。 有关详细信息，请参阅 [DataBox 磁盘文档](../../databox/data-box-disk-overview.md)。
* DataBox：这是最常用的选项。 将向你寄送一台加固型 DataBox 设备，该设备的工作方式类似于 NAS。 它的可用容量为 80 TiB。 有关详细信息，请参阅 [DataBox 文档](../../databox/data-box-overview.md)。
* DataBox Heavy：此选项提供一台带轮子的加固型 DataBox 设备，其工作方式类似于 NAS，容量为 1 PiB。 由于加密和文件系统开销，可用容量大约少 20%。 有关详细信息，请参阅 [DataBox Heavy 文档](../../databox/data-box-heavy-overview.md)。

## <a name="phase-4-provision-a-suitable-windows-server-on-premises"></a>第 4 阶段：在本地预配合适的 Windows Server

在等待 Azure DataBox 到达时，你可能已经开始查看要与 Azure 文件同步配合使用的一个或多个 Windows Server 的需求。

* 以虚拟机或物理服务器的形式创建 Windows Server 2019（最低为 2012R2）。 也支持 Windows Server 故障转移群集。
* 预配或添加直连的存储（DAS 与 NAS 相对，后者不受支持）。

你部署的 Windows Server 的资源配置（计算和 RAM）主要取决于你要同步的项（文件和文件夹）的数量。 如果你有任何顾虑，建议使用性能较高的配置。

[了解如何根据需要同步的项（文件和文件夹）的数量来确定 Windows Server 的大小。](storage-sync-files-planning.md#recommended-system-resources)

> [!NOTE]
> 前面链接的文章提供了一个表，其中有服务器内存 (RAM) 的范围。 你可以将服务器的内存调整为较小的数量，但可以预料到，这样调整可能会导致初始同步花费的时间显著增加。

## <a name="phase-5-copy-files-onto-your-databox"></a>第 5 阶段：将文件复制到 DataBox

当 DataBox 到达时，你需要在 NAS 设备的视线内设置 DataBox。 请按照你订购的 DataBox 类型的安装文档进行操作。

* [设置 Data Box](../../databox/data-box-quickstart-portal.md)
* [设置 Data Box Disk](../../databox/data-box-disk-quickstart-portal.md)
* [设置 Data Box Heavy](../../databox/data-box-heavy-quickstart-portal.md)

可能会有可供你使用的 DataBox 复制工具，具体取决于你的 DataBox 类型。 此时，不建议使用它们迁移到 Azure 文件共享，因为它们不能完全保真地将文件复制到 DataBox。 请改用 RoboCopy。

当你的 DataBox 到达时，它已预先预配了可供在订购时指定的每个存储帐户使用的 SMB 共享。

* 如果你的文件进入高级 Azure 文件共享，则每个高级“文件存储”存储帐户都将有一个 SMB 共享。
* 如果文件进入标准存储帐户，则每个标准（GPv1 和 GPv2）存储帐户都将有三个 SMB 共享。 只有以 `_AzFiles` 结尾的文件共享才适用于你的迁移。 请忽略任何块和页 blob 共享。

按照 Azure DataBox 文档中的步骤进行操作：

1. [连接到 Data Box](../../databox/data-box-deploy-copy-data.md)
1. 将数据复制到 Data Box
1. [准备你的 DataBox 以发往 Azure](../../databox/data-box-deploy-picked-up.md)

链接的 DataBox 文档指定了一个 RoboCopy 命令。 但是，该命令不适合于保持文件和文件夹的完全保真度。 请改用以下命令：

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]


## <a name="phase-6-deploy-the-azure-file-sync-cloud-resource"></a>第 6 阶段：部署 Azure 文件同步云资源

继续按本指南进行操作之前，请等待所有文件均已到达正确的 Azure 文件共享。 寄送和引入 DataBox 数据的过程将花费一些时间。

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

## <a name="phase-7-deploy-the-azure-file-sync-agent"></a>第 7 阶段：部署 Azure 文件同步代理

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

## <a name="phase-8-configure-azure-file-sync-on-the-windows-server"></a>第 8 阶段：在 Windows Server 上配置 Azure 文件同步

要执行此操作，已注册的本地 Windows Server 必须已准备就绪，并且已连接到 Internet。

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

启用云分层功能，并在“初始下载”部分中选择“仅限命名空间”。

> [!IMPORTANT]
> 云分层是一项 AFS 功能，它允许本地服务器的存储容量小于云中的存储容量，但仍然提供完整的命名空间。 在本地有用的数据也缓存在本地，以实现快速访问性能。 云分层是每个 Azure 文件同步“服务器终结点”的一项可选功能。 如果 Windows Server 上没有足够的本地磁盘容量来保存所有云数据，并且你想要避免从云中下载所有数据，则需要使用此功能！

针对需要配置同步的所有 Azure 文件共享/服务器位置，重复执行相关步骤来创建同步组并添加匹配的服务器文件夹作为服务器终结点。等待命名空间的同步完成。 以下部分将详细介绍如何确保上述操作成功完成。

> [!NOTE]
> 创建服务器终结点后，将开始进行同步。 但是，同步需要枚举（发现）你通过 DataBox 移动到 Azure 文件共享中的文件和文件夹。 因此，可能需要经过很长时间，云的命名空间才会开始出现在服务器上，具体取决于命名空间的大小。

## <a name="phase-9-wait-for-the-namespace-to-fully-appear-on-the-server"></a>第 9 阶段：等待命名空间完全出现在服务器上

你必须等待服务器完成从云共享完全下载命名空间，然后再执行你的迁移的任何后续步骤。 如果过早地开始将文件移动到服务器上，则可能会产生不必要的上传甚至文件同步冲突。

若要判断服务器是否已完成初始下载同步，请在正在执行同步的 Windows Server 上打开事件查看器，并使用 Azure 文件同步遥测事件日志。
遥测事件日志位于事件查看器中的“应用程序和服务\Microsoft\FileSync\代理”下。

搜索最近的 9102 事件。 同步会话完成后，将记录事件 ID 9102。 事件文本中有一个表示下载同步方向的字段。 （`HResult` 需要为零，已下载的文件数也是如此）。
 
你需要看到具有此类型和内容的两个连续事件，才能判断服务器已完成命名空间的下载。 如果在两个 9102 事件之间触发了不同的事件，也是可以的。

## <a name="phase-10-catch-up-robocopy-from-your-nas"></a>第 10 阶段：从 NAS 中使用 RoboCopy 进行追加

在服务器完成从云共享初始同步整个命名空间后，你可以继续执行此步骤。 必须先完成初始同步步骤，然后再继续执行此步骤。 有关详细信息，请查看前面的部分。

在此步骤中，你将运行 RoboCopy 作业，向你的云共享追加自从在 DataBox 上创建共享内容分支以来 NAS 上的最新更改。
此 RoboCopy 追加可能会快速完成，也可能会花费一段时间，具体取决于你的 NAS 共享上发生的数据变动量。

> [!WARNING]
> 由于 Windows Server 2019 中的回归 RoboCopy 行为，RoboCopy 的 /MIR 开关与分层目标目录不兼容。 在此迁移阶段，不得使用 Windows Server 2019 或 Windows 10 客户端。 请在中间 Windows Server 2016 上使用 RoboCopy。

基本迁移方法是通过 RoboCopy 从 NAS 设备迁移到 Windows Server，并使用 Azure 文件同步来同步到 Azure 文件共享。

运行首次本地复制，以复制到 Windows Server 目标文件夹：

1. 标识 NAS 设备上的第一个位置。
1. 标识已配置 Azure 文件同步的 Windows Server 上的匹配文件夹。
1. 开始使用 RoboCopy 进行复制

以下 RoboCopy 命令仅将差异内容（更新的文件和文件夹）从 NAS 存储复制到 Windows Server 目标文件夹。 Windows Server 然后会将其同步到 Azure 文件共享。 

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]

如果在 Windows Server 上预配的存储空间少于 NAS 设备上文件占用的存储空间，则已配置云分层。 当本地 Windows Server 卷变满时，将启动[云分层](storage-sync-cloud-tiering-overview.md)，并对已成功同步的文件进行分层。 云分层将生成足够的空间，以便继续从 NAS 设备进行复制。 云分层每小时检查一次，以查看已同步的内容，并释放磁盘空间以使卷可用空间达到 99%。
有可能会发生这种情况：RoboCopy 需要移动大量文件，而这些文件所需的空间超过了在 Windows Server 上为其提供的本地存储空间。 通常，RoboCopy 移动内容的速度比 Azure 文件同步上传文件并将其分层到本地卷之外的速度快得多。 因此，RoboCopy 将失败。 建议以一种可防止出现这种情况的顺序来处理共享。 例如，不要同时为所有共享启动 RoboCopy 作业，或者只移动其大小未超过 Windows Server 上当前可用空间量的共享，等等。 好消息是，使用 /MIR 开关时只会移动增量内容，在移动增量内容后，重启的作业不需要再次移动此文件。

### <a name="user-cut-over"></a>用户完全转移

首次运行 RoboCopy 命令时，用户和应用程序仍将在 NAS 上访问文件，并有可能更改这些文件。 有可能出现这种情况：RoboCopy 处理了一个目录并移至下一个目录，然后源位置 (NAS) 中的用户添加、更改或删除了一个此时不会在当前 RoboCopy 运行中处理的文件。 这是预期的行为。

第一次运行是将变动的大量数据通过 Azure 文件同步移动到 Windows Server 和云中。这第一次复制可能需要很长时间，具体取决于：

* 上传带宽
* 本地网络速度，以及与之匹配的最佳 RoboCopy 线程数
* RoboCopy 和 Azure 文件同步同时需要处理的项（文件和文件夹）的数量

在初始运行完成后，再次运行该命令。

第二次为同一共享运行 RoboCopy 将会更快地完成，因为只需要传输自上次运行后发生的更改。 可以为同一共享运行重复的作业。

如果你认为停机时间是可接受的，则需要删除用户对基于 NAS 的共享的访问权限。 为此，可执行会阻止用户更改文件和文件夹结构及内容的任何步骤。 例如，使 DFS 命名空间指向不存在的位置，或者更改共享上的根 ACL。

运行最后一轮 RoboCopy。 它将选取可能已丢失的任何更改。
这最后一步所花费的时间取决于 RoboCopy 扫描的速度。 可通过测量上一轮运行所用的时间来估算时间（相当于故障时间）。

在 Windows Server 文件夹上创建一个共享，并根据需要调整 DFS-N 部署来指向该文件夹。 请务必设置与 NAS SMB 共享上相同的共享级别权限。 如果你有一个已加入域的企业级 NAS，那么用户 SID 将自动匹配，这是因为用户在 Active Directory 中，且 RoboCopy 会完全保真地复制文件和元数据。 如果在 NAS 上使用了本地用户，则需要将这些用户重新创建为 Windows Server 本地用户，并将 RoboCopy 移动到 Windows Server 上的现有 SID 映射到新的 Windows Server 本地用户的 SID。

你已将共享/共享组迁移到公共根或卷中。 （具体取决于你在第 1 阶段中的映射）

你可以尝试并行运行其中的几个副本。 建议一次处理一个 Azure 文件共享的范围。

## <a name="troubleshoot"></a>故障排除

最可能遇到的问题是在 Windows Server 端运行 RoboCopy 命令失败并出现“卷已满”消息。 云分层每小时进行一次，以从本地 Windows Server 磁盘撤出已同步的内容。 其目标是使卷上的可用空间达到 99%。

让同步继续进行并让云分层释放磁盘空间。 可以在 Windows Server 上的文件资源管理器中观察进度。

当 Windows Server 有足够的可用容量时，重新运行该命令可以解决此问题。 遇到这种情况时，不会有任何中断，可以放心地继续操作。 再次运行该命令只会带来不便。

查看下一部分中有关排查 Azure 文件同步问题的链接。

## <a name="next-steps"></a>后续步骤

对于 Azure 文件共享和 Azure 文件同步，有更多的内容有待探索。以下文章可帮助了解高级选项和最佳做法，还包含疑难解答帮助。 这些文章链接到相应的 [Azure 文件共享文档](storage-files-introduction.md)。

* [迁移概述](storage-files-migration-overview.md)
* [AFS 概述](./storage-sync-files-planning.md)
* [AFS 部署指南](./storage-how-to-create-file-share.md)
* [AFS 故障排除](storage-sync-files-troubleshoot.md)
