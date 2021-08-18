---
title: 将本地 NAS 迁移到 Azure 文件共享
description: 了解如何使用 Azure DataBox 将文件从本地网络附加存储 (NAS) 位置迁移到 Azure 文件共享。
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 75387c4309283fc1e3df543bd1dced5f4fa792bf
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/15/2021
ms.locfileid: "114462318"
---
# <a name="use-databox-to-migrate-from-network-attached-storage-nas-to-azure-file-shares"></a>使用 DataBox 从网络附加存储 (NAS) 迁移到 Azure 文件共享

本迁移文章是涉及到“NAS”和“Azure DataBox”关键字的几篇文章中的一篇。 请检查本文是否适用于你的场景：

> [!div class="checklist"]
> * 数据源：网络连接存储 (NAS)
> * 迁移路线：NAS &rArr; DataBox &rArr; Azure 文件共享
> * 在本地缓存文件：否，最终目标是直接在云中使用 Azure 文件共享。 没有使用 Azure 文件同步的计划。

如果你的场景与此不同，请参阅[迁移指南表](storage-files-migration-overview.md#migration-guides)。

本文指导你完成一个端到端的过程，其中包括从 NAS 设备迁移到正常运行的 Azure 文件共享所需进行的规划、部署和网络配置。 本指南使用 Azure DataBox 进行批量数据传输（脱机数据传输）。

## <a name="applies-to"></a>适用于
| 文件共享类型 | SMB | NFS |
|-|:-:|:-:|
| 标准文件共享 (GPv2)、LRS/ZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |
| 标准文件共享 (GPv2)、GRS/GZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |
| 高级文件共享 (FileStorage)、LRS/ZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |

## <a name="migration-goals"></a>迁移目标

目标是将 NAS 设备上的共享迁移到 Azure，并使其成为原生 Azure 文件共享。 无需 Windows Server 即可使用原生 Azure 文件共享。 在执行这种迁移时，需要保证生产数据的完整性以及迁移期间的可用性。 要满足后一项要求，需将停机时间尽量缩短，使之不会超过或者只略微超过例行维护时段。

## <a name="migration-overview"></a>迁移概述

迁移过程包括多个阶段。 你需要部署 Azure 存储帐户和文件共享并配置网络。 然后，你将使用 Azure DataBox 和 RoboCopy 迁移文件，以与更改保持同步。 最后，将用户和应用切换到新创建的 Azure 文件共享。 以下各部分详细介绍了迁移过程的各个阶段。

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

## <a name="phase-4-provision-a-temporary-windows-server"></a>第 4 阶段：预配临时 Windows Server

在等待 Azure DataBox 到货的过程中，可以先部署一个或多个 Windows Server，用于运行 RoboCopy 作业。 

- 这些服务器的第一项用途是将文件复制到 DataBox。
- 这些服务器的第二项用途是，当 DataBox 仍在运输途中时，与 NAS 设备上发生的更改保持同步。 此方法可以最大程度地减少源端的停机时间。

RoboCopy 作业的工作速度主要取决于以下因素：

* 源和目标存储上的 IOPS
* 源和目标存储之间的可用网络带宽 </br> 在“故障排除”部分可以找到更多详细信息：[IOPS 和带宽注意事项](#iops-and-bandwidth-considerations)
* 快速处理命名空间中的文件和文件夹的能力 </br> 在“故障排除”部分可以找到更多详细信息：[处理速度](#processing-speed)
* RoboCopy 运行之间的更改次数 </br> 在“故障排除”部分可以找到更多详细信息：[避免不必要的工作](#avoid-unnecessary-work)

在确定要提供给临时 Windows Server 的 RAM 和线程计数时，请务必注意引用的详细信息。

## <a name="phase-5-preparing-to-use-azure-file-shares"></a>第 5 阶段：准备使用 Azure 文件共享

为了节省时间，应在等待 DataBox 到货的过程中处理此阶段。 使用此阶段中的信息，可以决定如何使 Azure 内部和外部的服务器与用户能够利用你的 Azure 文件共享。 最关键的决策是：

- 网络：使网络能够路由 SMB 流量。
- 身份验证：配置 Azure 存储帐户以进行 Kerberos 身份验证。 为存储帐户配置 AdConnect 和域加入可使你的应用和用户能够使用其 AD 标识进行身份验证
- 授权：针对每个 Azure 文件共享的共享级 ACL 将允许 AD 用户和组访问给定的共享；在 Azure 文件共享中，本机 NTFS ACL 将接管控制权。 然后，基于文件和文件夹 ACL 的授权将发挥作用，就像对本地 SMB 共享一样。
- 业务连续性：将 Azure 文件共享集成到现有环境通常需要保留现有的共享地址。 如果你尚未使用 DFS-Namespaces，请考虑在环境中建立该服务。 可将用户和脚本使用的共享地址保持不变。 你将使用 DFS-N 作为 SMB 的命名空间路由服务，方法是在迁移后将 DFS 命名空间目标重定向到 Azure 文件共享。

:::row:::
    :::column:::
        <iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/jd49W33DxkQ" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    :::column-end:::
    :::column:::
        此视频是有关如何通过五个简单步骤将 Azure 文件共享直接安全地公开给信息工作者和应用的指南和演示。</br>
        该视频提到了某些主题的专用文档：

* [标识概述](storage-files-active-directory-overview.md)
* [如何将存储帐户加入域](storage-files-identity-auth-active-directory-enable.md)
* [Azure 文件共享的网络概述](storage-files-networking-overview.md)
* [如何配置公共和专用终结点](storage-files-networking-endpoints.md)
* [如何配置 S2S VPN](storage-files-configure-s2s-vpn.md)
* [如何配置 Windows P2S VPN](storage-files-configure-p2s-vpn-windows.md)
* [如何配置 Linux P2S VPN](storage-files-configure-p2s-vpn-linux.md)
* [如何配置 DNS 转发](storage-files-networking-dns.md)
* [配置 DFS-N](files-manage-namespaces.md)
   :::column-end:::
:::row-end:::

## <a name="phase-6-copy-files-onto-your-databox"></a>第 6 阶段：将文件复制到 DataBox

当 DataBox 到货时，需要将 DataBox 设置为与 NAS 设备直通。 请按照你订购的 DataBox 类型的安装文档进行操作。

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

```console
Robocopy /MT:32 /NP /NFL /NDL /B /MIR /IT /COPY:DATSO /DCOPY:DAT /UNILOG:<FilePathAndName> <SourcePath> <Dest.Path> 
```
* 若要详细了解各个 RoboCopy 标志的详细信息，请查看即将发布的 [RoboCopy 部分](#robocopy)中的表格。
* 若要详细了解如何适当调整线程计数 `/MT:n`、优化 RoboCopy 的速度，并使 RoboCopy 成为数据中心内良好兼容的工具，请查看 [RoboCopy 故障排除部分](#troubleshoot)。


## <a name="phase-7-catch-up-robocopy-from-your-nas"></a>第 7 阶段：在 NAS 中运行 RoboCopy 进行同步

在 DataBox 报告所有文件和文件夹都已放入计划的 Azure 文件共享后，你可以继续处理此阶段。
仅当 NAS 上的数据自 DataBox 复制启动以来可能已更改时，才需要运行 RoboCopy 进行同步。 在某些情况下当你出于存档目的使用某个共享时，你也许可以在 NAS 上停止对该共享进行更改，直到迁移完成。 也许还可以在迁移期间将 NAS 共享设置为只读，以此满足业务要求。

如果在迁移期间需要使共享可读/写，并且你只能承受很短的停机时间，那么，在将用户访问直接故障转移到 Azure 文件共享之前完成此 RoboCopy 同步步骤就很重要。

在此步骤中，你将运行 RoboCopy 作业，向你的云共享追加自从在 DataBox 上创建共享内容分支以来 NAS 上的最新更改。
此 RoboCopy 追加可能会快速完成，也可能会花费一段时间，具体取决于你的 NAS 共享上发生的数据变动量。

运行首次本地复制，以复制到 Windows Server 目标文件夹：

1. 标识 NAS 设备上的第一个位置。
1. 标识匹配的 Azure 文件共享。
1. 在临时 Windows Server 上将 Azure 文件共享装载为本地网络驱动器
1. 如前所述开始使用 RoboCopy 进行复制

### <a name="mounting-an-azure-file-share"></a>装载 Azure 文件共享

在可以使用 RoboCopy 之前，需要使 Azure 文件共享可供通过 SMB 访问。 最简单的方法是将共享作为本地网络驱动器装载到计划用于 RoboCopy 的 Windows Server。 

> [!IMPORTANT]
> 在可以将 Azure 文件共享成功装载到本地 Windows Server 之前，需已完成“阶段：准备使用 Azure 文件共享”！

准备就绪后，查看[在 Windows 中使用 Azure 文件共享操作指南文章](storage-how-to-use-files-windows.md)，并装载要对其启动 NAS RoboCopy 同步的 Azure 文件共享。

### <a name="robocopy"></a>RoboCopy

以下 RoboCopy 命令仅将差异内容（更新的文件和文件夹）从 NAS 存储复制到 Azure 文件共享。 

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]

> [!TIP]
> 如果 RoboCopy 影响了你的生产环境、报告大量的错误，或者进展速度不符合预期，请[查看“故障排除”部分](#troubleshoot)。

### <a name="user-cut-over"></a>用户完全转移

首次运行 RoboCopy 命令时，用户和应用程序仍将在 NAS 上访问文件，并有可能更改这些文件。 有可能出现这种情况：RoboCopy 处理了一个目录并移至下一个目录，然后源位置 (NAS) 中的用户添加、更改或删除了一个此时不会在当前 RoboCopy 运行中处理的文件。 这是预期的行为。

首次运行涉及到将大量已改动的数据移到 Azure 文件共享。 首次复制可能需要一段时间。 请查看[“故障排除”部分](#troubleshoot)，以更详细地了解哪些因素可能会影响 RoboCopy 的速度。

在初始运行完成后，再次运行该命令。

第二次为同一共享运行 RoboCopy 将会更快地完成，因为只需要传输自上次运行后发生的更改。 可以为同一共享运行重复的作业。

如果你认为停机时间是可接受的，则需要删除用户对基于 NAS 的共享的访问权限。 为此，可执行会阻止用户更改文件和文件夹结构及内容的任何步骤。 例如，使 DFS 命名空间指向不存在的位置，或者更改共享上的根 ACL。

运行最后一轮 RoboCopy。 它将选取可能已丢失的任何更改。
这最后一步所花费的时间取决于 RoboCopy 扫描的速度。 可通过测量上一轮运行所用的时间来估算时间（相当于故障时间）。

在 Windows Server 文件夹上创建一个共享，并根据需要调整 DFS-N 部署来指向该文件夹。 请务必设置与 NAS SMB 共享上相同的共享级别权限。 如果你有一个已加入域的企业级 NAS，那么用户 SID 将自动匹配，这是因为用户在 Active Directory 中，且 RoboCopy 会完全保真地复制文件和元数据。 如果在 NAS 上使用了本地用户，则需要将这些用户重新创建为 Windows Server 本地用户，并将 RoboCopy 移动到 Windows Server 上的现有 SID 映射到新的 Windows Server 本地用户的 SID。

你已将共享/共享组迁移到公共根或卷中。 （具体取决于你在第 1 阶段中的映射）

你可以尝试并行运行其中的几个副本。 建议一次处理一个 Azure 文件共享的范围。

## <a name="troubleshoot"></a>故障排除

[!INCLUDE [storage-files-migration-robocopy-optimize](../../../includes/storage-files-migration-robocopy-optimize.md)]

## <a name="next-steps"></a>后续步骤

对于 Azure 文件共享，有更多的内容有待探索。 以下文章可帮助了解高级选项和最佳做法，还包含疑难解答帮助。 这些文章链接到相应的 [Azure 文件共享文档](storage-files-introduction.md)。

* [迁移概述](storage-files-migration-overview.md)
* [监视、诊断和排查 Microsoft Azure 存储问题](../common/storage-monitoring-diagnosing-troubleshooting.md)
* [关于直接访问的网络注意事项](storage-files-networking-overview.md)
* [备份：Azure 文件共享快照](storage-snapshots-files.md)
