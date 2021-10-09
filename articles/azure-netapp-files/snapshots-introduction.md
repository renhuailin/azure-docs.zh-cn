---
title: Azure NetApp 文件快照的工作方式 | Microsoft Docs
description: 介绍 Azure NetApp 文件快照的工作方式，包括创建快照的方法、还原快照的方法，以及如何在跨区域复制设置中使用快照。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/27/2021
ms.author: b-juche
ms.openlocfilehash: 15216e29c20503babfd0b03b6a4223281ec038f8
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2021
ms.locfileid: "129092905"
---
# <a name="how-azure-netapp-files-snapshots-work"></a>Azure NetApp 文件快照的工作原理

本文介绍了 Azure NetApp 文件快照的工作方式。 Azure NetApp 文件快照技术提供稳定性、可伸缩性和更快的可恢复性，不会影响性能。 它为数据保护解决方案奠定了基础，包括单个文件还原、卷还原和克隆、跨区域复制和长期保留。 

有关使用卷快照的步骤，请参阅“[使用 Azure NetApp 文件管理快照](azure-netapp-files-manage-snapshots.md)”。 有关跨区域复制中快照管理的注意事项，请参阅“[使用跨区域复制的要求和注意事项](cross-region-replication-requirements-considerations.md)”。

## <a name="what-volume-snapshots-are"></a>卷快照有哪些  

Azure NetApp 文件快照是一个时间点文件系统（卷）映像。 它很适合用作联机备份。 可以使用快照[创建新卷](snapshots-restore-new-volume.md)（克隆）、[还原文件](snapshots-restore-file-client.md)或[还原卷](snapshots-revert-volume.md)。 在 Azure NetApp 文件卷上所存储的特定应用程序数据中，可能需要额外的步骤来确保应用程序一致性。  

Underlaying 卷虚拟化技术是 Azure NetApp 文件的一部分，其独特功能使得低开销快照成为可能。 与数据库一样，该层使用指向磁盘上的实际数据块的指针。 但是，与数据库不同的是，它不会重写现有块，而是将更新的数据写入新块并更改指针，因此可以保留新数据和旧数据。 Azure NetApp 文件快照仅可操作块指针，创建卷的“冻结”只读视图，使得应用程序无需特殊编程即可访问较早版本的文件和目录层次结构。 不会复制实际数据块。 因此，快照在需要创建时很高效；无论卷大小如何，它们接近即时。 快照在存储空间中也很有效率；只会保留快照与活动卷之间的增量块。

下图演示了这些概念：  

![显示快照主要概念的关系图](../media/azure-netapp-files/snapshot-concepts.png)

在这些关系图中，图 1a 中创建了一个快照。 在图 1b 中，更改的数据写入到新块 B1 并且指针被更新。 但快照指针仍指向先前写入的块 B，提供数据的实时视图和历史视图。 图 1c 中创建了另一个快照。 现在，你可以访问三代数据（实时数据、快照 2 和快照 1，按存在期限顺序），无需占用三个完整副本所需的卷空间。 

快照只取卷元数据的一个副本（inode 表）。 无论卷大小、使用的容量或卷上的活动级别，只需几秒钟即可创建。 因此，创建 100-TiB 卷的快照所花费的时间（接近零）与创建 100-GiB 卷的快照相同。 正常情况下，创建快照后，对数据文件所做的更改会反映在文件的活动版本中。

同时，从快照指向的数据块保持稳定且不可变。 由于 Azure NetApp 文件卷的“写入时重定向”性质，快照不会产生任何性能开销，且本身不会占用任何空间。 在一段时间内，每个卷最多可以存储 255 个快照，所有这些快照都可作为数据的只读和联机版本进行访问，使用的容量与每个快照之间已更改块的数量一样少。 已修改块存储在活动卷中。 快照中指向的块保留（形式为只读）在要保管的卷中，仅当已清除（活动卷和快照中）的所有指针时才会改变用途。 因此，随着时间的推移，快照中保留的新数据块或（已修改）数据块会使卷利用率增加。

 下图显示了一段时间内卷的快照和已用空间： 

![显示一段时间内卷的快照和已用空间的关系图](../media/azure-netapp-files/snapshots-used-space-over-time.png)

由于卷快照只记录自最新快照以来的块更改，因此其具有以下主要优点：

* 快照 ***可高效存储***。   
    快照占用的存储空间极小，因为快照不会复制整个卷的任何数据块。 按次序拍摄的两个快照之间的唯一不同点在于两者之间的时间间隔内所添加或更改的块。 这种块增量行为最大程度地减少了关联的存储容量消耗。 许多备用快照实现使用与活动文件系统相同的存储卷，从而提高存储容量需求。 根据应用程序的每日“块级别”更改率，Azure NetApp 文件快照会消耗更多或更少的容量，但只是在已更改数据方面。 对于多个应用程序卷，平均每日快照消耗范围为已用卷容量的 1-5%，或高达 20-30%，如 SAP HANA 数据库卷。 请务必[监视卷和快照的使用情况](azure-netapp-files-metrics.md#volumes)，以了解相对于已创建和已维护快照数量的快照容量消耗。   

* 快照 ***可快速创建、复制、还原或克隆***。   
    无论卷大小和卷上的活动级别如何，只需几秒钟即可创建、复制、还原或克隆快照。 可以[按需创建卷快照](azure-netapp-files-manage-snapshots.md)。 用户还可以使用[快照策略](snapshots-manage-policy.md)来指定 Azure NetApp 文件自动创建快照的时间，以及为卷保留的快照数量。  可以通过使用应用程序层协调快照来实现应用程序一致性，例如，使用适用于 SAP HANA 的 [AzAcSnap 工具](azacsnap-introduction.md)。

* 快照对卷 ***性能*** 没有影响。   
    由于 underlaying 技术的“写入时重定向”性质，存储或保留 Azure NetApp 文件快照不会对性能造成任何影响，甚至在数据活动过大的情况下也是如此。 在大多数情况下，删除快照也几乎不会对性能产生任何影响。 

* 快照具有可伸缩性，因为它们可以频繁创建，并且很多都可以保留。   
    Azure NetApp 文件卷最多支持为每个卷创建 255 个快照。 如果能够存储大量的低影响、频繁创建的快照，这会增加成功恢复所需版本数据的可能性。

* 可以在 Azure 存储中保管快照。   
    为了满足合规和长期数据保留要求，请使用 [Azure NetApp 文件备份](backup-introduction.md)功能将快照保管在受保护卷外部的经济高效且已启用 ZRS 的 Azure 存储中。  

* 快照提供 *用户可见性_ 和 _ *文件可恢复性**。   

Azure NetApp 文件快照技术的高性能、可伸缩性和稳定性意味着它可为用户驱动的恢复提供理想的联机备份。 出于文件、目录或卷还原目的，可将快照设置为可供用户访问。 可使用其他解决方案将备份复制到脱机存储或[跨区域复制](cross-region-replication-introduction.md)，以便进行保留或灾难恢复。

## <a name="ways-to-create-snapshots"></a>创建快照的方法   

您可以使用多种方法来创建和维护快照：

* 手动（按需），方式是使用：   
    * [Azure 门户](azure-netapp-files-manage-snapshots.md#create-an-on-demand-snapshot-for-a-volume)、[REST API](/rest/api/netapp/snapshots)、[Azure CLI](/cli/azure/netappfiles/snapshot) 或 [PowerShell](/powershell/module/az.netappfiles/new-aznetappfilessnapshot) 工具
    * 脚本（参阅“[示例](azure-netapp-files-solution-architectures.md#sap-tech-community-and-blog-posts)”）

* 自动，方式是使用：
    * 快照策略，通过 [Azure 门户](snapshots-manage-policy.md)、[REST API](/rest/api/netapp/snapshotpolicies)、[Azure CLI](/cli/azure/netappfiles/snapshot/policy) 或 [PowerShell](/powershell/module/az.netappfiles/new-aznetappfilessnapshotpolicy) 工具
    * 应用程序一致性快照工具，如 [AzAcSnap](azacsnap-introduction.md)

## <a name="how-volumes-and-snapshots-are-replicated-cross-region-for-dr"></a>如何跨区域复制卷和快照以进行 DR  

Azure NetApp 文件支持[跨区域复制](cross-region-replication-introduction.md)，以实现灾难恢复 (DR) 目的。 Azure NetApp 文件跨区域复制使用 SnapMirror 技术。 只会以经压缩的高效格式通过网络发送更改后的块。 在卷之间启动跨区域复制后，整个卷的内容（即实际存储的数据块）只会传输一次。 此操作称为基线传输。 初始传输后，只会传输更改后的块（在快照中捕获）。 结果为源卷的异步 1:1 副本，包括所有快照。 此行为采用完整且增量的永久复制机制。 这项技术最大程度地减少了跨区域复制所需的数据量，从而节省了数据传输成本。 它还缩短了复制时间。 可以实现较小的恢复点目标 (RPO)，因为通过最大限度地降低数据传输量，可以更频繁地创建和传输更多的快照。 此外，它不再需要基于主机的复制机制，从而避免了虚拟机和软件许可证费用。

下图显示了跨区域复制方案中的快照流量： 

![显示跨区域复制方案中快照流量的关系图](../media/azure-netapp-files/snapshot-traffic-cross-region-replication.png)

## <a name="how-snapshots-can-be-vaulted-for-long-term-retention-and-cost-savings"></a>如何保管快照以实现长期保留和成本节省

如上所述，快照用于快速高效地创建 Azure NetApp 文件卷的快速、节省空间的备份，并提供非常高效的方式用于还原数据文件或完整卷。 这些联机快照充当第一道防线，涵盖了大部分数据恢复操作。   

若要将快照保留更长时间，或者要保留的快照超过了联机快照的最大数目，可以将 Azure NetApp 文件卷中的快照保管到已启用 ZRS 的 Azure 存储中。  [Azure NetApp 文件备份](backup-introduction.md)功能可帮助实现此目的。  该功能可将快照保留更长时间（长达一年甚至更长）。 备份存储在 Azure 存储上，与 Azure NetApp 文件容量池成本相比，这种存储方式更具成本优势，并可以利用不同的存储平台来消除依赖关系和遵守保留要求。

若要对 Azure NetApp 文件卷启用快照保管，请在 Azure NetApp 文件订阅中[配置备份策略](backup-configure-policy-based.md)（在“数据保护”部分下），并指定要保留的每日、每周和每月备份数。 只需执行此操作，就能使用经济高效的长期存储来扩展数据保护。  

下图显示了如何将快照数据从 Azure NetApp 文件卷传输到托管在 Azure 存储上的 Azure NetApp 文件备份存储。

![显示将快照数据从 Azure NetApp 文件卷传输到 Azure NetApp 文件备份存储的示意图](../media/azure-netapp-files/snapshot-data-transfer-backup-storage.png)

如这个简化的示例中所示，Azure NetApp 文件备份功能旨在保留更长的备份历史记录。 请注意右侧的备份存储库包含的快照比左侧的受保护卷和快照更多且更旧。 

大多数用例要求将 Azure NetApp 文件卷上的联机快照保留相对较短的时间（通常是几个月），以便在由于发生应用程序或用户错误而丢失数据时，能够以最常见的方法进行恢复。 Azure NetApp 文件备份功能用于通过将快照发送到经济高效的 Azure 存储，将数据保护期延长至一年或更长。 如图中的蓝色部分所示，第一个传输是基线，它会复制源 Azure NetApp 文件卷和快照中所有已使用的数据块。 连续备份将使用快照机制，通过仅限块的增量更新来更新备份存储库。

## <a name="ways-to-restore-data-from-snapshots"></a>从快照还原数据的方法  

Azure NetApp 文件快照技术极大地提高了备份的频率和可靠性。 它产生的性能开销最小，并可以安全地在活动卷上创建。 Azure NetApp 文件快照允许近乎即时、安全和用户托管（可选）的还原。 本部分介绍了可从 Azure NetApp 文件快照访问或还原数据的各种方式。

### <a name="restoring-files-or-directories-from-online-snapshots"></a>从联机快照还原文件或目录

如果未将[快照路径可见性](snapshots-edit-hide-path.md)设置为 `hidden`，则可以直接访问快照，从意外删除、损坏或修改中恢复数据。 文件和目录的安全性保留在快照中，快照在设计上为只读形式。 因此，还原既安全又简单。 如果快照路径可见性设置为 `hidden`，则可以创建支持票证，让备份管理员或系统管理员从快照还原文件。

下图显示了对快照的文件或目录访问权限： 

![显示对快照的文件或目录访问权限的关系图](../media/azure-netapp-files/snapshot-file-directory-access.png)

在该关系图中，快照 1 仅消耗活动卷与快照创建时间之间的差异块。 但在通过卷快照路径访问快照时，数据将犹如快照创建时的完整卷容量一般显示。 通过访问快照文件夹，可以从选择的快照中复制文件和目录，以还原数据。

同样，可在 DR 区域中对目标跨区域复制卷的快照进行只读访问，以便进行数据恢复。  

下图显示了跨区域复制方案中的快照访问权限： 

![显示跨区域复制中的快照访问权限的关系图](../media/azure-netapp-files/snapshot-access-cross-region-replication.png)

有关从快照还原单个文件或目录，请参阅“[使用客户端从快照还原文件](snapshots-restore-file-client.md)”。

### <a name="restoring-cloning-an-online-snapshot-to-a-new-volume"></a>将联机快照还原（克隆）到新卷

可以将 Azure NetApp 文件快照还原到单独的独立卷（克隆）。 无论卷大小和使用的容量如何，此操作接近即时。 新创建的卷几乎立即可供访问，而实际的卷和快照数据块正在复制。 根据卷大小和容量，此过程可能需要很长时间，期间无法删除父级卷和快照。 但是，该卷在初始创建后即可以访问，而复制过程将在后台进行。 通过此功能，可快速创建用于数据恢复或卷克隆的卷，以进行测试和开发。 按照数据复制过程的性质，当还原完成时，存储容量池消耗会加倍，新卷将显示原始快照的完整活动容量。 此过程完成后，卷将是独立的，并已与原始卷解除关联，并且源卷和快照可以独立于新卷来管理或删除。

下图显示了通过还原（克隆）快照创建的新卷：   

![显示通过还原快照创建的新卷的关系图](../media/azure-netapp-files/snapshot-restore-clone-new-volume.png)

可以对复制的灾难恢复 (DR) 卷快照执行相同的操作。 即使跨区域复制保持活动或正在进行，也可以将任何快照还原到新卷。 利用此功能，无需中断创建 DR 区域内的测试和开发环境，便可使数据可供使用，而复制的卷则仅用于 DR 目的。 此用例使测试和开发能够与生产隔离，从而消除了对生产环境的潜在影响。  

下图显示了在发生跨区域复制时使用 DR 目标卷快照进行的卷还原（克隆）：  

![显示使用 DR 目标卷快照进行卷还原的关系图](../media/azure-netapp-files/snapshot-restore-clone-target-volume.png)

有关卷还原操作，请参阅“[将快照还原到新卷](snapshots-restore-new-volume.md)”。

### <a name="restoring-reverting-an-online-snapshot-in-place"></a>就地还原联机快照

在某些情况下，由于新卷会消耗存储容量，因此可能不需要或不适合从快照创建新卷。 若要从数据损坏中快速恢复（例如，数据库损坏或勒索软件攻击），则还原卷本身中的快照可能会更合适。 可以使用 Azure NetApp 文件[快照还原](snapshots-revert-volume.md)功能来完成此操作。 此功能使用户能够快速将卷还原到创建特定快照时的状态。 在大多数情况下，还原卷比将单个文件从快照还原到活动文件系统要快得多，特别是对于较大的多 TiB 卷。 

卷快照恢复近乎即时，只需几秒钟即可完成，即使对于最大的卷也是如此。 活动卷元数据（Inode 表）将替换为快照创建时的快照元数据，从而将卷回滚到该特定时间点。 不需要复制数据块即可使还原生效。 因此，与将快照还原到新卷相比，此方法更节省空间且速度更快。

下图显示了恢复为先前快照的卷：  

![显示恢复为先前快照的卷的关系图](../media/azure-netapp-files/snapshot-volume-revert.png)

> [!IMPORTANT]
> 在创建所选快照后写入的活动文件系统数据和创建的快照将丢失。 快照还原操作会将目标卷中的所有数据替换为所选快照中的数据。 选择快照时，应注意快照内容和创建日期。 快照还原操作无法撤消。  

有关如何使用此功能，请参阅“[使用快照还原卷](snapshots-revert-volume.md)”。

### <a name="restoring-volume-backups-from-vaulted-snapshots"></a>从保管的快照还原卷备份

可以在卷级别或 NetApp 帐户级别[搜索备份](backup-search.md)。 用于快照的名称在备份快照时将会保留，并包含前缀“daily”、“weekly”或“monthly”。 这些名称还包含快照创建时间和日期的时间戳。 启用备份功能时创建的第一个快照称为基线快照。 基线快照包含受保护卷和快照上的所有数据。 连续的已保管快照是块增量更新，而快照始终是创建保管快照时卷的完整表示形式，并且可以直接还原，而无需将基线与增量更新堆积在一起。 

下图演示了将选定的已保管快照还原到新卷的操作：  

![显示将选定的已保管快照还原到新卷的示意图](../media/azure-netapp-files/snapshot-restore-vaulted-new-volume.png)

### <a name="restoring-individual-files-or-directories-from-vaulted-snapshots"></a>从保管的快照还原单个文件或目录  

若要还原单个文件或目录，需将完整的保管快照还原到新卷，然后可以挂载该卷以浏览要还原的文件或目录。 还原方式是将所需的文件或目录从新还原的卷复制到目标位置。 还原完成后，可以删除已还原的卷。  

如果删除了某个卷，该卷的保管快照（备份）仍将保留，这与联机快照不同，后者是卷的一部分，删除该卷时会一并删除这些联机快照。 即使父卷因发生应用程序或用户错误而被删除或丢失，你也可以从保管的备份中恢复完整的卷，然后还原单个目录。 为此，可以从备份列表中选择适当的保管快照，并将其恢复到新卷。 有关详细信息，请参阅[将备份还原到新卷](backup-restore-new-volume.md)。


## <a name="how-snapshots-are-deleted"></a>如何删除快照  

本部分介绍如何删除联机快照和保管的快照。

### <a name="deleting-online-snapshots"></a>删除联机快照 

快照会消耗存储容量。 因此，它们通常不会无限期地保留。 对于数据保护、保留和可恢复性，许多快照（在不同时间点创建）通常会在特定的持续时间内保持联机状态，具体取决于 RPO、RTO 和保留 SLA 要求。 但是，较旧的快照通常无需保存在存储服务中，因此可能需要删除以释放空间。 管理员可以在任何时候删除（不必按创建顺序）任何快照。 

> [!IMPORTANT]
> 快照删除操作无法撤消。 应保留卷的脱机副本（保管的快照）以实现数据保护和保留目的。 

删除快照后，从该快照指向现有数据块的所有指针都将删除。 仅当数据块没有其他指针指向它（由活动卷或卷中的其他快照指向它）时，该数据块才会返回到卷可用空间供将来使用。 因此，比起从活动卷删除数据，删除快照通常可释放更多的卷容量，因为在以前创建的快照中经常捕获数据块。 

下图显示了从卷中删除快照 3 对存储消耗的影响：  

![显示快照删除对存储消耗的影响的关系图](../media/azure-netapp-files/snapshot-delete-storage-consumption.png)

请务必[监视卷和快照的消耗情况](azure-netapp-files-metrics.md#volumes)，并了解应用程序、活动卷和快照消耗的交互方式。 

有关如何管理快照删除，请参阅“[删除快照](snapshots-delete.md)”。 有关如何自动执行此过程，请参阅“[管理快照策略](snapshots-manage-policy.md)”。

### <a name="deleting-vaulted-snapshots"></a>删除保管的快照

禁用卷的备份会删除存储在该卷的 Azure 存储中的所有保管快照（备份）。

如果删除了某个卷，但在删除该卷之前未禁用备份策略，则与该卷相关的所有备份将保留在 Azure 存储中，并且列在关联的 NetApp 帐户下。

有关详细信息，请参阅[禁用 Azure NetApp 文件卷的备份功能](backup-disable.md)。 

保管的快照历史记录由应用的快照策略自动管理，当保管的快照（备份）计划程序添加了新快照时，将删除最旧的快照。 你也可以手动删除保管的快照。

## <a name="next-steps"></a>后续步骤

* [使用 Azure NetApp 文件管理快照](azure-netapp-files-manage-snapshots.md)
* [监视卷和快照指标](azure-netapp-files-metrics.md#volumes)
* [快照策略问题故障排除](troubleshoot-snapshot-policies.md)
* [Azure NetApp 文件的资源限制](azure-netapp-files-resource-limits.md)
* [Azure NetApp 文件快照 101 视频](https://www.youtube.com/watch?v=uxbTXhtXCkw)
* [Azure NetApp 文件快照概述](https://anfcommunity.com/2021/01/31/azure-netapp-files-snapshot-overview/)
* [了解 Azure NetApp 文件备份](backup-introduction.md)
* [配置基于策略的备份](backup-configure-policy-based.md)
* [配置手动备份](backup-configure-manual.md)
* [管理备份策略](backup-manage-policies.md)
* [搜索备份](backup-search.md)
* [将备份还原到新卷](backup-restore-new-volume.md)
* [禁用卷的备份功能](backup-disable.md)
* [删除卷的备份](backup-delete.md)

