---
title: 使用 RoboCopy 迁移到 Azure 文件共享
description: 了解如何使用 RoboCopy 将位于多个位置的文件迁移到 Azure 文件共享。
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 04/12/2021
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 76fb327eaf2f95bd96513f6382a00e29a54fe893
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/15/2021
ms.locfileid: "114462239"
---
# <a name="use-robocopy-to-migrate-to-azure-file-shares"></a>使用 RoboCopy 迁移到 Azure 文件共享

本迁移文章介绍如何使用 RoboCopy 将文件移动或迁移到 Azure 文件共享中。 RoboCopy 是一个颇受信赖的知名文件复制实用工具，其中提供的功能集非常适合用于迁移。 此工具使用 SMB 协议，因此它可以广泛应用于任何支持 SMB 的源和目标组合。

> [!div class="checklist"]
> * 数据源：任何支持 SMB 协议的源，例如网络附加存储 (NAS)、Windows 或 Linux 服务器、其他 Azure 文件共享，等等
> * 迁移路线：从源存储 &rArr; 装有 RoboCopy 的 Windows 计算机 &rArr; Azure 文件共享

对于不同的源和部署组合，可以使用许多不同的迁移路线。 请查看[迁移指南表](storage-files-migration-overview.md#migration-guides)以找到最符合你的需求的迁移路线。

## <a name="applies-to"></a>适用于
| 文件共享类型 | SMB | NFS |
|-|:-:|:-:|
| 标准文件共享 (GPv2)、LRS/ZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |
| 标准文件共享 (GPv2)、GRS/GZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |
| 高级文件共享 (FileStorage)、LRS/ZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |

## <a name="azcopy-vs-robocopy"></a>AzCopy 与 RoboCopy 的比较
AzCopy 和 RoboCopy 是本质上不同的两种文件复制工具。 RoboCopy 使用任意版本的 SMB 协议。 AzCopy 是“云原生”的工具，只要目标位于 Azure 存储中，就可以使用它来移动数据。 AzCopy 依赖于 REST 协议。

作为受信赖的基于 Windows 的复制工具，RoboCopy 在以全保真度复制文件方面具备绝对的优势。 RoboCopy 提供丰富的功能集，并且能够以全保真度复制文件和文件夹，因此支持许多迁移方案。 请查看[迁移概述文章中的文件保真度部分](storage-files-migration-overview.md#migration-basics)，详细了解以可能的最高保真度复制文件的重要性。

相比之下，AzCopy 只是最近才扩展为支持具有一定保真度的文件复制，并添加了第一组籍以称作迁移工具的功能。 但是，两者之间仍有差距，如果将 AzCopy 标志与 RoboCopy 标志进行比较的话，很容易误解两者的功能。

例如：RoboCopy /MIR 将源镜像到目标 - 这意味着会考虑已添加、已更改和已删除的文件。 运行 AzCopy -sync 时，一个重要的差别是源中已删除的文件不会在目标中删除。 因此，AzCopy 中提供的是不完整的差异复制功能集。 AzCopy 将不断发展。 目前，对于以 Azure 文件共享为目标的迁移方案，不建议使用 AzCopy 工具。 

## <a name="migration-goals"></a>迁移目标

目标是将数据从现有文件共享位置移到 Azure。 在 Azure 中，你会将数据存储在无需安装 Windows Server 即可使用的原生 Azure 文件共享中。 在执行这种迁移时，需要保证生产数据的完整性以及迁移期间的可用性。 要满足后一项要求，需将停机时间尽量缩短，使之不会超过或者只略微超过例行维护时段。

## <a name="migration-overview"></a>迁移概述

迁移过程包括多个阶段。 需要部署 Azure 存储帐户和文件共享。 此外，还需要配置网络，并考虑使用 DFS 命名空间部署 (DFS-N) 或更新现有的部署。 一旦要实际开始复制数据，你将需要考虑使用重复性的差异 RoboCopy 运行来最大程度地减少停机时间，最后将用户切换到新建的 Azure 文件共享。 以下各部分详细介绍了迁移过程的各个阶段。

> [!TIP]
> 如果你返回到本文，请使用右侧的导航栏转到你离开的迁移阶段。

## <a name="phase-1-identify-how-many-azure-file-shares-you-need"></a>第 1 阶段：确定需要多少个 Azure 文件共享

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="phase-2-deploy-azure-storage-resources"></a>第 2 阶段：部署 Azure 存储资源

在此阶段中，请查阅第 1 阶段中的映射表，并使用它来预配正确数量的 Azure 存储帐户和其中的文件共享。

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

## <a name="phase-3-preparing-to-use-azure-file-shares"></a>第 3 阶段：准备使用 Azure 文件共享

使用此阶段中的信息，可以决定如何使 Azure 内部和外部的服务器与用户能够利用你的 Azure 文件共享。 最关键的决策是：

- 网络：使网络能够路由 SMB 流量。
- 身份验证：配置 Azure 存储帐户以进行 Kerberos 身份验证。 为存储帐户配置 AdConnect 和域加入可使你的应用和用户能够使用其 AD 标识进行身份验证
- 授权：针对每个 Azure 文件共享的共享级 ACL 将允许 AD 用户和组访问给定的共享；在 Azure 文件共享中，本机 NTFS ACL 将接管控制权。 然后，基于文件和文件夹 ACL 的授权将发挥作用，就像对本地 SMB 共享一样。
- 业务连续性：将 Azure 文件共享集成到现有环境通常需要保留现有的共享地址。 如果你尚未使用 [DFS 命名空间](files-manage-namespaces.md)，请考虑在环境中建立它。 可将用户和脚本使用的共享地址保持不变。 DFS-N 通过将客户端重定向到 Azure 文件共享，为 SMB 提供了一个命名空间路由服务。

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

### <a name="mounting-an-azure-file-share"></a>装载 Azure 文件共享

在可以使用 RoboCopy 之前，需要使 Azure 文件共享可供通过 SMB 访问。 最简单的方法是将共享作为本地网络驱动器装载到计划用于 RoboCopy 的 Windows Server。 

> [!IMPORTANT]
> 在可以将 Azure 文件共享成功装载到本地 Windows Server 之前，需已完成“第 3 阶段：准备使用 Azure 文件共享”。

准备就绪后，查看[将 Azure 文件共享与 Windows 配合使用的操作指南文章](storage-how-to-use-files-windows.md)。 然后装载要为其启动 RoboCopy 的 Azure 文件共享。

## <a name="phase-4-robocopy"></a>第 4 阶段：RoboCopy

以下 RoboCopy 命令仅将差异内容（更新的文件和文件夹）从源存储复制到 Azure 文件共享。

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]

> [!TIP]
> 如果 RoboCopy 影响了你的生产环境、报告大量的错误，或者进展速度不符合预期，请[查看“故障排除”部分](#troubleshoot-and-optimize)。

## <a name="phase-5-user-cut-over"></a>阶段 5：用户直接转换

首次运行 RoboCopy 命令时，用户和应用程序仍在访问迁移源上的文件，并可能更改这些文件。 有可能出现这种情况：RoboCopy 处理了一个目录并移至下一个目录，然后源位置中的用户添加、更改或删除了一个此时不会在当前 RoboCopy 运行中处理的文件。 这是预期的行为。

首次运行涉及到将大量已改动的数据移到 Azure 文件共享。 首次复制可能需要一段时间。 请查看[“故障排除”部分](#troubleshoot-and-optimize)，以更详细地了解哪些因素可能会影响 RoboCopy 的速度。

在初始运行完成后，再次运行该命令。

第二次为同一共享运行 RoboCopy 将会更快地完成，因为只需要传输自上次运行后发生的更改。 可以为同一共享运行重复的作业。

如果你认为该服务中断时间是可接受的，则需要删除用户对源共享的访问权限。 为此，可执行会阻止用户更改文件和文件夹结构及内容的任何步骤。 例如，使 DFS 命名空间指向不存在的位置，或者更改每个共享上的 ACL。

运行最后一轮 RoboCopy。 它将选取可能已丢失的任何更改。
这最后一步所花费的时间取决于 RoboCopy 的扫描速度。 可通过测量上一轮运行所用的时间来估算时间（相当于故障时间）。

在上一部分，你已将用户配置为[使用其标识访问共享](#phase-3-preparing-to-use-azure-file-shares)，并且应已建立一个策略，让用户[使用指向新 Azure 文件共享 (DFS-N) 的已建立路径](files-manage-namespaces.md)。

可以尝试在不同的源与目标共享之间并行运行上述几种复制操作。 如果要这样做，请记得控制好网络吞吐量以及核心数与线程数之比，以免系统开销过大。

## <a name="troubleshoot-and-optimize"></a>故障排除和优化

[!INCLUDE [storage-files-migration-robocopy-optimize](../../../includes/storage-files-migration-robocopy-optimize.md)]

## <a name="next-steps"></a>后续步骤

对于 Azure 文件共享，有更多的内容有待探索。 以下文章可帮助了解高级选项和最佳做法，还包含疑难解答帮助。 这些文章链接到相应的 [Azure 文件共享文档](storage-files-introduction.md)。

* [迁移概述](storage-files-migration-overview.md)
* [备份：Azure 文件共享快照](storage-snapshots-files.md)
* [如何结合使用 DFS 命名空间和 Azure 文件存储](files-manage-namespaces.md)
