---
title: 使用 Azure 文件同步进行灾难恢复的最佳做法
description: 了解使用 Azure 文件同步进行灾难恢复的最佳做法。具体而言，实现高可用性、数据保护和数据冗余。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 08/18/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 87fd5de90896a1a5b97cf9fa07a880ebcea1f35b
ms.sourcegitcommit: 34aa13ead8299439af8b3fe4d1f0c89bde61a6db
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2021
ms.locfileid: "122420589"
---
# <a name="best-practices-for-disaster-recovery-with-azure-file-sync"></a>使用 Azure 文件同步进行灾难恢复的最佳做法

对于 Azure 文件同步，灾难恢复需要考虑三个主要方面：高可用性、数据保护/备份和数据冗余。 本文介绍了每个方面，并帮助你确定要用于自己的灾难恢复解决方案的配置。

在 Azure 文件同步部署中，云终结点始终包含数据的完整副本，但可以将本地服务器看做是数据的可释放缓存。 发生服务器端灾难时，可以通过预配新的服务器、在服务器上安装 Azure 文件同步代理，并将其设置为新的服务器终结点来恢复服务器。

由于其混合性质，某些传统的服务器备份和灾难恢复策略不适用于 Azure 文件同步。对于任何已注册的服务器，Azure 文件同步不支持以下操作：

> [!WARNING]
> 执行上述任何操作可能会导致同步出现问题或者分层文件终端，最终导致数据丢失。 如果执行了其中一项操作，请联系 Azure 支持部门以确保部署正常。

- 将磁盘驱动器从一个服务器传输到另一个服务器
- 从操作系统备份进行还原
- 将服务器的操作系统克隆到另一个服务器
- 恢复到以前的虚拟机检查点
- 如果启用了云分层，则从本地备份还原文件


## <a name="high-availability"></a>高可用性

可以使用两种不同的策略来实现本地服务器的高可用性。 可以配置故障转移群集，也可以配置备用服务器。 这两个配置之间的决定因素是你愿意在系统中的投资规模，以及在发生灾难的情况下最大程度降低系统停机时间是否值得额外付出成本。

对于故障转移群集，不需要采取任何特殊步骤即可使用 Azure 文件同步。对于备用服务器，应进行以下配置：

将以下类型的辅助服务器作为主服务器：具有不同的服务器终结点，这些终结点同步到同一个同步组。但不允许最终用户访问服务器。 这允许所有文件从主服务器同步到备用服务器。 可以考虑启用仅命名空间的分层，这样最初仅下载命名空间。 如果主服务器出现故障，可以使用 DFS-N 快速重新配置最终用户对备用服务器的访问权限。

## <a name="data-protectionbackup"></a>数据保护/备份

保护实际数据是灾难恢复解决方案的关键组成部分。 要通过 Azure 文件共享来执行此操作，有两种主要方式：可以在云中备份数据，也可以在本地备份数据。 我们强烈建议在云中备份数据，因为云终结点将包含数据的完整副本，而服务器终结点可能只包含数据的一个子集。

### <a name="back-up-your-data-in-the-cloud"></a>在云中备份数据

应使用 [Azure 备份](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffile-sync%2ftoc.json)作为云备份解决方案。 Azure 备份负责处理备份计划、保留和还原等任务。 如果需要，可以手动生成[共享快照](../files/storage-snapshots-files.md?toc=/azure/storage/file-sync/toc.json)并配置自己的计划和保留解决方案，但这并不理想。 或者也可以使用第三方解决方案直接备份 Azure 文件共享。

如果发生灾难，可以从共享快照还原，这是文件共享在某个时间点的只读副本。 由于这些快照是只读的，因此不会受到勒索软件的影响。 在大型数据集中，完全共享还原操作需要花费很长时间，因此可以启用对快照的直接用户访问，以便用户可以将所需的数据复制到其本地驱动器，同时还原完成。

快照直接存储在 Azure 文件共享中，无论是手动生成快照还是由 Azure 备份为你生成快照。 因此应该[启用软删除](../files/storage-files-prevent-file-share-deletion.md?toc=/azure/storage/file-sync/toc.json)，以防止快照中的文件共享被意外删除。

有关详细信息，请参阅[关于 Azure 文件共享备份](../../backup/azure-file-share-backup-overview.md)，或与备份提供商联系，确认是否支持备份 Azure 文件共享。

### <a name="back-up-your-data-on-premises"></a>在本地备份数据

如果启用云分层，请不要实施本地备份解决方案。 启用云分层后，只有数据的子集将以本地方式存储在服务器上，其余数据存储在云终结点中。 根据用于本地备份的备份解决方案，分层文件可能被跳过且不进行备份（由于其 FILE_ATTRIBUTE_RECALL_ONDATA_ACCESS 属性），此时将仅作为分层文件进行备份，并且由于实时共享中的更改，在还原后可能无法访问；分层文件也可能会被撤回到磁盘，这将导致大量出口流量费用。

如果决定使用本地备份解决方案，则应在已禁用云分层的同步组中的服务器上执行备份。 执行还原时，使用卷级别或文件级还原选项。 使用文件级别还原选项还原的文件将同步到同步组中的所有终结点，现有文件将被替换为从备份还原的版本。 卷级别还原不会替换云终结点或其他服务器终结点中的更高文件版本。

在 Azure 文件同步代理版本 9 和更高版本中，启用了云分层的卷上支持[卷影复制服务 (VSS) 快照](file-sync-deployment-guide.md#self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service)（包括“以前的版本”选项卡）。 这样自己便可以执行自助式还原，而不是依靠管理员执行还原。 但是，必须通过 PowerShell 启用以前的版本兼容性，这会增加快照存储的成本。 VSS 快照不能防止服务器终结点本身发生灾难，因此，只能与云端备份一起使用。 有关详细信息，请参阅[通过早期版本和 VSS 进行自助式还原](file-sync-deployment-guide.md#self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service)。

## <a name="data-redundancy"></a>数据冗余

若要确保可靠的灾难恢复解决方案，请在基础结构中增加某种形式的数据冗余。 Azure Files 有四种冗余服务，分别是：[本地冗余存储 (LRS)](../common/storage-redundancy.md#locally-redundant-storage)、[区域冗余存储 (ZRS)](../common/storage-redundancy.md#zone-redundant-storage)、[异地冗余存储 (GRS)](../common/storage-redundancy.md#geo-redundant-storage) 和[异地区域冗余存储 (GZRS)](../common/storage-redundancy.md#geo-zone-redundant-storage)。

- [本地冗余存储 (LRS)](../common/storage-redundancy.md#locally-redundant-storage)：使用 LRS，每个文件在 Azure 存储群集中存储三次。 这可以防止由于硬件故障（例如磁盘驱动器损坏）而导致数据丢失。 但是，如果数据中心发生火灾或洪灾等灾难，使用 LRS 的存储帐户的所有副本可能会丢失或不可恢复。
- [区域冗余存储 (ZRS)](../common/storage-redundancy.md#zone-redundant-storage)：使用 ZRS，将存储每个文件的三个副本，但这些副本位于不同的 Azure 可用性区域的三个不同存储群集中，它们在物理上是隔离的。 可用性区域是 Azure 区域中独特的物理位置。 每个区域由一个或多个数据中心组成，这些数据中心配置了独立电源、冷却和网络。 在将副本写入所有三个可用性区域的存储群集前，不允许将其写入存储。 
- [异地冗余存储 (GRS)](../common/storage-redundancy.md#geo-redundant-storage)：使用 GRS 时，有两个区域，即一个主要区域和一个次要区域。 文件在主要区域中的 Azure 存储群集中存储三次。 写入将异步复制到 Microsoft 定义的次要区域。 GRS 提供分布在两个 Azure 区域之间的六个数据副本。
- [异地区域冗余存储 (GZRS)](../common/storage-redundancy.md#geo-zone-redundant-storage)：可以将 GZRS 视为类似于具有异地冗余的 ZRS。 使用 GZRS，文件在主要区域的三个不同存储群集中存储三次。 所有写入都将异步复制到 Microsoft 定义的次要区域。

对于可靠的灾难恢复解决方案，大多数客户应考虑使用 ZRS。 ZRS 增加的额外成本最少，增加了数据冗余优势，并且在发生中断时无缝程度也是最高的。 如果组织策略或法规要求需要对数据进行异地冗余，请考虑使用 GRS 或 GZRS。

### <a name="geo-redundancy"></a>异地冗余

如果在包含云终结点的存储帐户上启用 GRS 或 GZRS，那么也需要在存储同步服务上启用 GRS 或 GZRS。 这可确保在发生灾难时，将有关 Azure 文件同步拓扑的所有信息和云终结点中包含的数据异步复制到配对的辅助区域。

对于配置了 GRS 或 GZRS 的资源，如果主要区域被判定为永久性不可恢复或长时间不可用，Microsoft 将为你的服务启动故障转移。 当存储同步服务使用 GRS 或 GZRS 时，在发生区域灾难时，Azure 文件同步服务将自动故障转移到配对区域。 如果使用的 Azure 文件同步配置了 GRS 或 GZRS，则在发生灾难时，无需执行任何操作。

尽管可以手动请求将存储同步服务故障转移到 GRS 或 GZRS 配对区域，但我们不建议在大规模区域中断之外执行此操作，因为该过程不是无缝的，并且可能会产生额外的成本。 若要启动该过程，请打开支持票证，然后请求将包含 Azure 文件共享的 Azure 存储帐户以及存储同步服务进行故障转移。

> [!WARNING]
> 如果正在手动启动此过程，则必须联系支持人员，请求对存储同步服务进行故障转移。 如果尝试使用辅助区域中的相同服务器终结点创建新的存储同步服务，则可能会导致存储帐户中保留额外数据，因为不会清除 Azure 文件同步的之前安装。

发生故障转移后，服务器终结点将切换为自动与辅助区域中的云终结点进行同步。 但是，服务器终结点必须与云终结点进行协调。 这可能会导致文件冲突，因为次要区域中的数据可能不会被捕获到主区域。

## <a name="next-steps"></a>后续步骤

[了解 Azure 文件共享备份](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffile-sync%2ftoc.json)