---
title: Azure NetApp 文件快照的工作方式 |Microsoft Docs
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
ms.date: 02/05/2021
ms.author: b-juche
ms.openlocfilehash: 526ef0af08833954aef4136716930cec0df40eea
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/06/2021
ms.locfileid: "99625241"
---
# <a name="how-azure-netapp-files-snapshots-work"></a>Azure NetApp 文件快照的工作原理

本文介绍 Azure NetApp 文件快照的工作方式。 Azure NetApp 文件快照技术提供稳定性、可伸缩性和更快的可恢复性，不会影响性能。 Azure NetApp 文件快照技术为数据保护解决方案奠定了基础，包括单个文件还原、卷还原和克隆以及跨区域复制。 

有关使用卷快照的步骤，请参阅 [使用 Azure NetApp 文件管理快照](azure-netapp-files-manage-snapshots.md)。 有关跨区域复制中快照管理的注意事项，请参阅 [使用跨区域复制的要求和注意事项](cross-region-replication-requirements-considerations.md)。

## <a name="what-volume-snapshots-are"></a>哪些卷快照  

Azure NetApp 文件快照是一个时间点文件系统 (卷) 映像。 它可用作理想的联机备份。 你可以使用快照 [创建新卷](azure-netapp-files-manage-snapshots.md#restore-a-snapshot-to-a-new-volume)、 [还原文件](azure-netapp-files-manage-snapshots.md#restore-a-file-from-a-snapshot-using-a-client)或 [还原卷](azure-netapp-files-manage-snapshots.md#revert-a-volume-using-snapshot-revert)。 在 Azure NetApp 文件卷上存储的特定应用程序数据中，可能需要额外的步骤来确保应用程序一致性。  

Underlaying 卷虚拟化技术的独特功能，这是 Azure NetApp 文件的一部分，这是可能的。 与数据库一样，该层使用指向磁盘上的实际数据块的指针。 但是，与数据库不同的是，它不会重写现有块;它将更新的数据写入新块并更改指针。 Azure NetApp 文件快照只是操作块指针，创建 "冻结"、只读视图，使应用程序无需特殊编程即可访问较早版本的文件和目录层次结构。 不会复制实际数据块。 因此，在创建快照时，快照是高效的;它们接近即时，无论卷大小如何。 快照在存储空间中也是高效的。 它们本身不占用空间，并且只保留快照与活动卷之间的增量块。 

下图演示了这些概念：  

![显示快照的主要概念的关系图](../media/azure-netapp-files/snapshot-concepts.png)

在关系图中，用图1a 拍摄快照。 在图1b 中，将更改的数据写入 *新块* 并更新指针。 但快照指针仍指向 *先前写入的块*，提供数据的实时视图和历史视图。 图1c 中创建了另一个快照。 现在，你可以访问三代数据 (实时数据、快照2和快照1，按 age) 的顺序，无需占用需要三个完整副本的卷空间。 

快照只 (*inode 表*) 的卷元数据的副本。 只需几秒钟即可创建，无论卷大小、使用的容量或卷上的活动级别。 因此，拍摄 100 TiB 卷的快照时， () 时间时间与拍摄 100-GiB 卷的快照相同。 创建快照后，对数据文件所做的更改会在文件的活动版本中反映为正常。

同时，从快照指向的数据块保持稳定且不可变。 由于 Azure NetApp 文件卷的 "写入时重定向" 性质，快照不会产生任何性能开销，本身不会占用任何空间。 在一段时间内，每个卷最多可以存储255个快照，所有这些快照都可作为数据的只读和联机版本进行访问，使用的容量少于每个快照之间更改的块的数量。 修改后的块存储在活动卷中。 快照中指向的块将保留 (为要保管的卷中的只读) ，仅当已清除活动卷和快照) 中 (的所有指针时才会改变用途。 因此，随着时间的推移，卷利用率将随着时间的推移而增加，新数据块或 (修改了快照) 数据块。

 下图显示了一段时间内卷的快照和已用空间： 

![显示一段时间内卷的快照和已用空间的关系图](../media/azure-netapp-files/snapshots-used-space-over-time.png)

由于卷快照只记录自最新快照以来的块更改，因此它具有以下主要优点：

* 快照 ***存储效率更高***。   
    快照占用的存储空间最少，因为它不复制整个卷的数据块。 序列中所采用的两个快照仅在两者之间的时间间隔内添加或更改的块不同。 此块增量行为限制了关联的存储容量消耗。 许多备用快照实现使用与活动文件系统相同的存储卷，从而提高存储容量需求。 根据应用程序的每日 *块级别* 更改率，Azure NetApp 文件快照会消耗更多或更少的容量，只占用更改的数据。 平均每日快照消耗范围为多个应用程序卷的已用卷容量的1-5%，或高达20-30%，如 SAP HANA 数据库卷。 请确保 [监视卷和快照的使用情况](azure-netapp-files-metrics.md#volumes) ，以获取快照容量消耗相对于创建和维护的快照数的限制。   

* 快照可 ***快速创建、复制、还原或克隆***。   
    无论卷大小和活动级别如何，只需几秒钟即可创建、复制、还原或克隆快照。 可以按 [需](azure-netapp-files-manage-snapshots.md#create-an-on-demand-snapshot-for-a-volume)创建卷快照。 你还可以使用 [快照策略](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies) 来指定 Azure NetApp 文件应何时自动创建快照，以及要为卷保留多少快照。  可以通过使用应用程序层协调快照来实现应用程序一致性，例如，使用用于 SAP HANA 的 [AzAcSnap 工具](azacsnap-introduction.md) 。

* 快照对卷 ***性能*** 没有影响。   
    由于 underlaying 技术的 "写入时重定向" 性质，存储或保留 Azure NetApp 文件快照不会对性能造成任何影响，甚至会导致数据活动过大。 在大多数情况下，删除快照也几乎不会对性能产生任何影响。 

* 快照提供了 ***可伸缩性*** ，因为它们可以经常创建，并且很多可以保留。   
    Azure NetApp 文件卷最多支持255个快照。 如果能够存储大量的低影响、频繁创建的快照，则可能会增加成功恢复所需版本的数据的可能性。

* 快照提供了 ***用户可见性** _ 和 _ *_文件可恢复_ 性 * *。   
Azure NetApp 文件快照技术的高性能、可伸缩性和稳定性意味着它为用户驱动的恢复提供理想的联机备份。 可以使快照成为用户可访问的文件、目录或卷还原用途。 其他解决方案允许将备份复制到脱机存储或 [复制跨区域](cross-region-replication-introduction.md) ，以便进行保留或灾难恢复。

## <a name="ways-to-create-snapshots"></a>创建快照的方式   

您可以使用多种方法来创建和维护快照：

* 手动 (按需) ，方法是使用：   
    * [Azure 门户](azure-netapp-files-manage-snapshots.md#create-an-on-demand-snapshot-for-a-volume)、 [REST API](/rest/api/netapp/snapshots)、 [Azure CLI](/cli/azure/netappfiles/snapshot)或[PowerShell](/powershell/module/az.netappfiles/new-aznetappfilessnapshot)工具
    * 脚本 (参阅 [示例](azure-netapp-files-solution-architectures.md#sap-tech-community-and-blog-posts)) 

* 自动化，使用：
    * 快照策略，通过 [Azure 门户](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies)、 [REST API](/rest/api/netapp/snapshotpolicies)、 [Azure CLI](/cli/azure/netappfiles/snapshot/policy)或 [PowerShell](/powershell/module/az.netappfiles/new-aznetappfilessnapshotpolicy) 工具
    * 应用程序一致性快照工具，如 [AzAcSnap](azacsnap-introduction.md)

## <a name="how-volumes-and-snapshots-are-replicated-cross-region-for-dr"></a>如何跨区域复制卷和快照以进行灾难恢复  

Azure NetApp 文件支持 [跨区域复制](cross-region-replication-introduction.md) ，以实现灾难恢复 (DR) 目的。 Azure NetApp 文件跨区域复制使用 SnapMirror 技术。 仅通过网络以压缩的高效格式发送已更改的块。 在卷之间启动跨区域复制后，整个卷的内容 (也就是说，实际存储的数据块) 只会传输一次。 此操作称为 *基线传输*。 初始传输后，只传输快照)  (中捕获的更改的块。 结果为源卷的异步1:1 副本，包括所有快照。 此行为采用完整且增量式的复制机制。 这种技术可最大程度地减少跨区域复制所需的数据量，从而节省数据传输成本。 它还缩短了复制时间。 你可以实现较小的恢复点目标 (RPO) ，因为使用有限的数据传输可以更频繁地创建和传输更多的快照。 此外，它不再需要基于主机的复制机制，从而避免了虚拟机和软件许可证费用。

下图显示了跨区域复制方案中的快照流量： 

![在跨区域复制方案中显示快照流量的关系图](../media/azure-netapp-files/snapshot-traffic-cross-region-replication.png)

## <a name="ways-to-restore-data-from-snapshots"></a>从快照还原数据的方法  

Azure NetApp 文件快照技术极大地提高了备份的频率和可靠性。 它会产生最小的性能开销，并可以安全地在活动卷上创建。 Azure NetApp 文件快照允许近乎即时、安全和有选择的用户管理的还原。 本部分介绍了可从 Azure NetApp 文件快照访问或还原数据的各种方式。

### <a name="restoring-files-or-directories-from-snapshots"></a>从快照还原文件或目录 

如果未将 [快照路径可见性](azure-netapp-files-manage-snapshots.md#edit-the-hide-snapshot-path-option) 设置为 `hidden` ，用户可以直接访问快照，以恢复数据的意外删除、损坏或修改。 文件和目录的安全性保留在快照中，快照在设计上是只读的。 因此，还原是安全且简单的。 

下图显示了对快照的文件或目录访问权限： 

![显示对快照的文件或目录访问权限的关系图](../media/azure-netapp-files/snapshot-file-directory-access.png)

在关系图中，Snapshot 1 仅消耗活动卷与快照创建时间之间的差异块。 但在通过卷快照路径访问快照时，数据将 *显示* 为快照创建时的完整卷容量。 通过访问快照文件夹，用户可以通过从选择的快照中复制文件和目录来自行还原数据。

同样，可在 DR 区域中对目标跨区域复制卷的快照进行只读访问，以便进行数据恢复。  

下图显示了跨区域复制方案中的快照访问： 

![显示跨区域复制中的快照访问的关系图](../media/azure-netapp-files/snapshot-access-cross-region-replication.png)

有关从快照还原单个文件或目录的客户端，请参阅 [从快照还原文件](azure-netapp-files-manage-snapshots.md#restore-a-file-from-a-snapshot-using-a-client) 。

### <a name="restoring-cloning-a-snapshot-to-a-new-volume"></a>将快照) 快照还原 (克隆到新卷

可以将 Azure NetApp 文件快照还原到独立的独立卷。 此操作接近即时，而不考虑卷大小和使用的容量。 新创建的卷几乎可立即用于访问，而实际的卷和快照数据块正在复制。 此过程可能需要很长时间才能删除父卷和快照，具体取决于卷大小和容量。 但是，在初始创建后，可以访问该卷，而复制过程将在后台进行。 通过此功能，可快速创建用于数据恢复或卷克隆的卷以进行测试和开发。 按照数据复制过程的性质，当还原完成时，存储容量池消耗会加倍，新卷将显示原始快照的完整活动容量。 完成此过程后，卷将是独立的，并与原始卷解除关联，并且可以独立于新卷来管理或删除源卷和快照。

下图显示了通过还原) 快照 (克隆而创建的新卷：   

![显示通过还原快照创建的新卷的关系图](../media/azure-netapp-files/snapshot-restore-clone-new-volume.png)

可以对复制的快照执行相同的操作， (DR) 卷。 即使跨区域复制保持活动或正在进行，也可以将任何快照还原到新卷。 利用此功能，无需中断创建灾难恢复区域中的测试和开发环境，使数据可供使用，而复制的卷则仅用于 DR 目的。 此使用案例使测试和开发与生产隔离，从而消除对生产环境的潜在影响。  

下图显示了在发生跨区域复制时，使用 DR 目标卷快照) 进行卷还原 (克隆：  

![使用 DR 目标卷快照显示卷还原的关系图](../media/azure-netapp-files/snapshot-restore-clone-target-volume.png)

有关卷还原操作的信息，请参阅将 [快照还原到新卷](azure-netapp-files-manage-snapshots.md#restore-a-snapshot-to-a-new-volume) 。

### <a name="restoring-reverting-a-snapshot-in-place"></a>还原 (恢复) 快照

在某些情况下，由于新卷会消耗存储容量，因此可能不需要或不适合从快照创建新卷。 若要从数据损坏中快速恢复 (例如，) 的数据库损坏或勒索软件攻击，则还原卷本身中的快照可能更合适。 可以使用 Azure NetApp 文件快照还原功能来完成此操作。 此功能使你能够快速将卷恢复到创建特定快照时的状态。 在大多数情况下，恢复卷的速度要快于将单个文件从快照还原到活动文件系统，尤其是在大的多 TiB 卷中。 

恢复卷快照近乎瞬间，只需几秒钟即可完成，即使对于最大的卷也是如此。 活动卷元数据 (*inode 表*) 将替换为快照创建时的快照元数据，从而将卷回滚到该特定时间点。 不需要复制数据块即可使还原生效。 因此，它比将快照还原到新卷更节省空间。 

下图显示了恢复到以前的快照的卷：  

![显示恢复为先前快照的卷的关系图](../media/azure-netapp-files/snapshot-volume-revert.png)

> [!IMPORTANT]
> 所写入的活动文件系统数据和创建快照后拍摄的快照将丢失。 快照还原操作会将目标卷中的所有数据替换为所选快照中的数据。 选择快照时，应注意快照内容和创建日期。 不能撤消快照还原操作。  

有关如何使用此功能的信息，请参阅 [使用快照还原卷](azure-netapp-files-manage-snapshots.md#revert-a-volume-using-snapshot-revert) 。

## <a name="how-snapshots-are-deleted"></a>如何删除快照 

快照确实会消耗存储容量。 因此，它们通常不会无限期地保留。 对于数据保护、保留和可恢复性， (在不同时间点创建的快照数量) 通常会根据 RPO、RTO 和保留 SLA 要求在特定的持续时间内保持联机状态。 但是，较旧的快照通常无需保存在存储服务中，因此可能需要删除以释放空间。 任何时候都可以删除任何快照， (不必按管理员的创建顺序) 。 

> [!IMPORTANT]
> 快照删除操作不能撤消。 你应保留卷的脱机副本以实现数据保护和保留。 

删除快照后，将删除所有从该快照到现有数据块的指针。 如果数据块在活动卷或卷) 的其他快照上没有指向它的指针 (，数据块将返回到卷可用空间供将来使用。 因此，删除快照通常比从活动卷删除数据更多的容量，因为在以前创建的快照中经常捕获数据块。 

下图显示了对从卷中删除快照3的存储消耗的影响：  

![显示快照删除对存储消耗的影响的关系图](../media/azure-netapp-files/snapshot-delete-storage-consumption.png)

请务必 [监视卷和快照的消耗](azure-netapp-files-metrics.md#volumes) 情况，并了解应用程序、活动卷和快照消耗的交互方式。 

有关如何管理快照删除的信息，请参阅 [删除快照](azure-netapp-files-manage-snapshots.md#delete-snapshots) 。 请参阅 [管理快照策略](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies) ，了解如何自动执行此过程。

## <a name="next-steps"></a>后续步骤

* [使用 Azure NetApp 文件管理快照](azure-netapp-files-manage-snapshots.md)
* [监视卷和快照指标](azure-netapp-files-metrics.md#volumes)
* [快照策略问题故障排除](troubleshoot-snapshot-policies.md)
* [Azure NetApp 文件的资源限制](azure-netapp-files-resource-limits.md)
* [Azure NetApp 文件快照101视频](https://www.youtube.com/watch?v=uxbTXhtXCkw)
* [Azure NetApp 文件快照概述](https://anfcommunity.com/2021/01/31/azure-netapp-files-snapshot-overview/)



