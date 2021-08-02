---
title: 使用 RoboCopy 迁移到 Azure 文件共享
description: 了解如何使用 RoboCopy 将文件从多个位置迁移到 Azure 文件共享。
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 04/12/2021
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 53c0ad42e51e8ffc562827e9a67e01b132dafd89
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2021
ms.locfileid: "108776478"
---
# <a name="use-robocopy-to-migrate-to-azure-file-shares"></a>使用 RoboCopy 迁移到 Azure 文件共享

这篇迁移文章介绍了如何使用 RoboCopy 将文件移动或迁移到 Azure 文件共享。 RoboCopy 是一个众所周知的受信任文件复制实用工具，它提供的功能集非常适合用于迁移。 它使用 SMB 协议，这使得它广泛适用于任何源和目标组合，并支持 SMB。

> [!div class="checklist"]
> * 数据源：支持 SMB 协议的任何源，例如网络连接存储 (NAS)、Windows 或 Linux 服务器、另一个 Azure 文件共享等
> * 迁移路线：源存储 &rArr; 使用 RoboCopy 的 Windows 计算机&rArr; Azure 文件共享

不同的源和部署组合有许多不同的迁移路线。 请参阅[迁移指南表](storage-files-migration-overview.md#migration-guides)，找到最适合你的需求的迁移路线。

## <a name="azcopy-vs-robocopy"></a>AzCopy 与 RoboCopy
AzCopy 和 RoboCopy 是两种本质上不同的文件复制工具。 RoboCopy 使用任意版本的 SMB 协议。 AzCopy 是一种“生于云端”的工具，只要目标是位于 Azure 存储中，就可以使用它来移动数据。 AzCopy 依赖于 REST 协议。

RoboCopy 作为一种基于 Windows 的受信任复制工具，在涉及到完全保真地复制文件时，它具有主场优势。 RoboCopy 支持许多迁移场景，因为它具有丰富的功能，并能够完全保真地复制文件和文件夹。 请参阅[迁移概述一文中的文件保真部分](storage-files-migration-overview.md#migration-basics)，详细了解以最大可能的保真度复制文件的重要性。

另一方面，AzCopy 最近才扩展为支持具有一定保真度的文件复制，并增加了作为迁移工具所需的首批功能。 但是，仍然存在差距，在将 AzCopy 标志与 RoboCopy 标志进行比较时，很容易对功能产生误解。

例如：RoboCopy /MIR 会将源映射到目标 - 这意味着会考虑添加、更改和删除的文件。 与 AzCopy -sync 的一个重要区别在于，在源上删除的文件在目标上不会被删除。 这会使差异复制功能集不完整。 AzCopy 将继续改进。 目前，对于以 Azure 文件共享为目标的迁移场景，不建议使用 AzCopy 工具。 

## <a name="migration-goals"></a>迁移目标

目标是将数据从现有文件共享位置移动到 Azure。 在 Azure 中，你可以将数据存储在本机 Azure 文件共享中，无需 Windows Server 即可使用。 在执行这种迁移时，需要保证生产数据的完整性以及迁移期间的可用性。 要满足后一项要求，需将停机时间尽量缩短，使之不会超过或者只略微超过例行维护时段。

## <a name="migration-overview"></a>迁移概述

迁移过程包括多个阶段。 你将需要部署 Azure 存储帐户和文件共享。 此外，你将配置网络，考虑使用 DFS 命名空间部署 (DFS-N) 或更新现有部署。 一旦到了进行实际数据复制的时候，你将需要考虑重复、差异的 RoboCopy 运行，尽量减少停机时间，最后将你的用户切换到新创建的 Azure 文件共享。 以下各部分详细介绍了迁移过程的各个阶段。

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
- 业务连续性：将 Azure 文件共享集成到现有环境通常需要保留现有的共享地址。 如果你尚未使用 [DFS 命名空间](files-manage-namespaces.md)，请考虑在环境中创建它。 可将用户和脚本使用的共享地址保持不变。 DFS-N 通过将客户端重定向到 Azure 文件共享，为 SMB 提供了一个命名空间路由服务。

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

准备就绪后，请参阅[将 Azure 文件共享与 Windows 配合使用的操作说明](storage-how-to-use-files-windows.md)一文。 然后装载你要启动 RoboCopy 的 Azure 文件共享。

## <a name="phase-4-robocopy"></a>第 4 阶段：Robocopy

以下 RoboCopy 命令仅将差异内容（更新的文件和文件夹）从源存储复制到 Azure 文件共享。

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]

> [!TIP]
> 如果 RoboCopy 影响了你的生产环境、报告大量的错误，或者进展速度不符合预期，请[查看“故障排除”部分](#troubleshoot-and-optimize)。

## <a name="phase-5-user-cut-over"></a>阶段 5：用户直接转换

首次运行 RoboCopy 命令时，用户和应用程序仍访问迁移源上的文件，并可能更改这些文件。 有可能出现这种情况：RoboCopy 处理了一个目录并移至下一个目录，然后源位置中的用户添加、更改或删除了一个此时不会在当前 RoboCopy 运行中处理的文件。 这是预期的行为。

首次运行涉及到将大量已改动的数据移到 Azure 文件共享。 首次复制可能需要一段时间。 请查看[“故障排除”部分](#troubleshoot-and-optimize)，以更详细地了解哪些因素可能会影响 RoboCopy 的速度。

在初始运行完成后，再次运行该命令。

第二次为同一共享运行 RoboCopy 将会更快地完成，因为只需要传输自上次运行后发生的更改。 可以为同一共享运行重复的作业。

如果你认为故障时间是可接受的，则需要移除用户对源共享的访问权限。 为此，可执行会阻止用户更改文件和文件夹结构及内容的任何步骤。 例如，使 DFS 命名空间指向不存在的位置，或者更改每个共享上的 ACL。

运行最后一轮 RoboCopy。 它将选取可能已丢失的任何更改。
这最后一步所花费的时间取决于 RoboCopy 的扫描速度。 可通过测量上一轮运行所用的时间来估算时间（相当于故障时间）。

在上一节中，你已配置用户，让他们[使用其标识访问共享](#phase-3-preparing-to-use-azure-file-shares)，并且应该已经制定了策略，让用户[使用已建立的路径访问新 Azure 文件共享 (DFS-N)](files-manage-namespaces.md)。

可以尝试在不同的源和目标共享之间并行运行几个这样的副本。 执行此操作时，请注意控制网络吞吐量和核心与线程计数的比率，不要使系统超负荷工作。

## <a name="troubleshoot-and-optimize"></a>故障排除和优化

[!INCLUDE [storage-files-migration-robocopy-optimize](../../../includes/storage-files-migration-robocopy-optimize.md)]

## <a name="next-steps"></a>后续步骤

对于 Azure 文件共享，有更多的内容有待探索。 以下文章可帮助了解高级选项和最佳做法，还包含疑难解答帮助。 这些文章链接到相应的 [Azure 文件共享文档](storage-files-introduction.md)。

* [迁移概述](storage-files-migration-overview.md)
* [备份：Azure 文件共享快照](storage-snapshots-files.md)
* [如何结合使用 DFS 命名空间和 Azure 文件存储](files-manage-namespaces.md)
