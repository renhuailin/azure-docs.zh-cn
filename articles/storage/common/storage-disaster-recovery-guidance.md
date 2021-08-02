---
title: 灾难恢复和存储帐户故障转移
titleSuffix: Azure Storage
description: Azure 存储支持异地冗余存储帐户的故障转移。 通过帐户故障转移，可以在主终结点不可用时为存储帐户启动故障转移过程。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 06/09/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 2fdbdcfd847c33bc6d948d12b14f468233b4cf19
ms.sourcegitcommit: f9e368733d7fca2877d9013ae73a8a63911cb88f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111901486"
---
# <a name="disaster-recovery-and-storage-account-failover"></a>灾难恢复和存储帐户故障转移

Microsoft 致力于确保 Azure 服务一直可用。 不过，可能会发生计划外服务中断。 如果应用程序需要复原能力，Microsoft 建议使用异地冗余存储，以便将数据复制到另一个区域。 此外，客户还应制定用于处理区域服务中断的灾难恢复计划。 灾难恢复计划的一个重要组成部分是，准备在主终结点不可用时将故障转移到辅助终结点。

Azure 存储支持异地冗余存储帐户的故障转移。 通过帐户故障转移，可以在主终结点不可用时为存储帐户启动故障转移过程。 故障转移将辅助终结点更新为，存储帐户的主终结点。 在故障转移完成后，客户端便可以开始对新的主终结点执行写入操作。

帐户故障转移适用于常规用途 v1、常规用途 v2 以及使用 Azure 资源管理器部署的 Blob 存储帐户类型。 已启用分层命名空间的存储帐户不支持帐户故障转移。

本文介绍了帐户故障转移所涉及的概念和过程，以及如何让存储帐户做好恢复准备，且造成的客户影响最小。 若要了解如何在 Azure 门户或 PowerShell 中启动帐户故障转移，请参阅[启动帐户故障转移](storage-initiate-account-failover.md)。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="choose-the-right-redundancy-option"></a>选择正确的冗余选项

Azure 存储将维护存储帐户的多个副本，以确保持续性和高可用性。 为帐户选择哪个冗余选项取决于所需的复原能力水平。 为了防止区域中断，请为你的帐户配置异地冗余存储，无论是否选择从次要区域进行读取访问：  

异地冗余存储 (GRS) 或异地区域冗余存储 (GZRS)：在至少相距数百英里的两个地理区域中异步复制数据。 如果主要区域遭遇服务中断，次要区域便会成为数据的冗余源。 可以通过启动故障转移，将辅助终结点转换为主终结点。

读取访问异地冗余存储 (RA-GRS) 或读取访问异地区域冗余存储 (RA-GZRS)：为异地冗余存储提供附加优势，即对辅助终结点的读取访问权限。 如果主终结点发生中断，配置为对辅助终结点进行读取访问并设计为高度可用的应用程序可以继续从辅助终结点读取数据。 Microsoft 建议使用 RA-GZRS 来实现应用程序的最大可用性和持续性。

有关 Azure 存储中冗余的详细信息，请参阅 [Azure 存储冗余](storage-redundancy.md)。

> [!WARNING]
> 异地冗余存储有数据丢失风险。 数据是异步复制到次要区域，这意味着数据写入主要区域与数据写入次要区域之间存在延迟。 发生服务中断时，尚未复制到辅助终结点的对主终结点的写入操作将丢失。

## <a name="design-for-high-availability"></a>旨在实现高可用性

请务必从一开始就设计高可用性应用程序。 有关设计应用程序和计划灾难恢复方面的指导，请参阅以下 Azure 资源：

- [设计适用于 Azure 的可复原应用程序](/azure/architecture/framework/resiliency/app-design)：概述了在 Azure 中生成高可用性应用程序的关键概念。
- [复原能力清单](/azure/architecture/checklist/resiliency-per-service)：用于验证应用程序是否实现高可用性最佳设计做法的清单。
- [使用异地冗余设计高度可用的应用程序](geo-redundant-design.md)：有关如何生成可利用异地冗余存储的应用程序的设计指南。
- [教程：生成使用 Blob 存储的高可用性应用程序](../blobs/storage-create-geo-redundant-storage.md)：介绍了如何生成在模拟故障和恢复时自动切换终结点的高可用性应用程序的教程。 

此外，还请注意下面这些可保持 Azure 存储数据高可用性的最佳做法：

- **磁盘：** 利用 [Azure 备份](https://azure.microsoft.com/services/backup/)备份 Azure 虚拟机使用的 VM 磁盘。 还建议在发生区域灾难时使用 [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) 保护 VM。
- **块 blob：** 启用 [软删除](../blobs/soft-delete-blob-overview.md)以防发生对象级删除和覆盖，或使用 [AzCopy](./storage-use-azcopy-v10.md)、[Azure PowerShell](/powershell/module/az.storage/) 或 [Azure 数据移动库](storage-use-data-movement-library.md)将块 blob 复制到其他区域中的另一个存储帐户内。
- 文件：使用 [Azure 备份](../../backup/azure-file-share-backup-overview.md)来备份文件共享。 同时启用[软删除](../files/storage-files-prevent-file-share-deletion.md)，以防止意外删除文件共享。 对于 GRS 不可用时的异地冗余，请使用 [AzCopy](./storage-use-azcopy-v10.md) 或 [Azure PowerShell](/powershell/module/az.storage/) 将文件复制到不同区域中的其他存储帐户。
- **表：** 使用 [AzCopy](./storage-use-azcopy-v10.md) 将表数据导出到其他区域中的另一个存储帐户内。

## <a name="track-outages"></a>跟踪服务中断

客户可以订阅 [Azure 服务运行状况仪表板](https://azure.microsoft.com/status/)，以跟踪 Azure 存储和其他 Azure 服务的运行状况和状态。

Microsoft 还建议将应用程序设计为，可以应对可能出现的写入故障。 应用程序应公开写入故障，以提醒你主要区域可能存在服务中断。

## <a name="understand-the-account-failover-process"></a>了解帐户故障转移过程

借助客户管理的帐户故障转移，可以在主要区域因任何原因而不可用时，将整个存储帐户故障转移到次要区域。 如果你强制故障转移到次要区域，客户端可以在故障转移完成后开始向辅助终结点写入数据。 故障转移通常需要大约一小时才能完成。

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

### <a name="how-an-account-failover-works"></a>帐户故障转移的工作原理

在正常情况下，客户端将数据写入主要区域中的 Azure 存储帐户，并将此数据异步复制到次要区域。 下图展示了主要区域可用时的场景：

![客户端将数据写入主要区域中的存储帐户](media/storage-disaster-recovery-guidance/primary-available.png)

如果主终结点因任何原因而不可用，客户端无法再向存储帐户写入数据。 下图展示了主终结点不可用、但尚未执行恢复时的场景：

![主终结点不可用，因此客户端无法写入数据](media/storage-disaster-recovery-guidance/primary-unavailable-before-failover.png)

客户启动帐户故障转移到辅助终结点。 故障转移过程更新 Azure 存储提供的 DNS 条目，这样辅助终结点就会成为存储帐户的新主终结点，如下图所示：

![客户启动帐户故障转移到辅助终结点](media/storage-disaster-recovery-guidance/failover-to-secondary.png)

在 DNS 条目已更新且请求定向到新的主终结点后，异地冗余帐户便会恢复写入访问权限。 在故障转移完成后，用于 blob、表、队列和文件的现有存储服务终结点保持不变。

> [!IMPORTANT]
> 在故障转移完成后，存储帐户被配置为在新的主终结点中本地冗余。 若要继续复制到新的辅助终结点，请将帐户重新配置为使用异地冗余。
>
> 请记住，将本地冗余存储帐户转换为使用异地冗余会导致费用和时间消耗。 有关详细信息，请参阅[帐户故障转移的重要影响](storage-initiate-account-failover.md#important-implications-of-account-failover)。

### <a name="anticipate-data-loss"></a>预测数据丢失

> [!CAUTION]
> 帐户故障转移通常涉及一些数据丢失。 请务必了解启动帐户故障转移的影响。  

因为数据是从主要区域异步写入次要区域，所以在写入主要区域的数据复制到次要区域前始终存在延迟。 如果主要区域不可用，最新写入数据可能尚未复制到次要区域。

如果强制执行故障转移，主要区域中的所有数据就会在次要区域成为新的主要区域且存储帐户配置为本地冗余时丢失。 当故障转移发生时，将保留已复制到次要区域的所有数据。 不过，任何写入主要区域、但尚未复制到次要区域的数据会永久丢失。

“上次同步时间”属性表示，最近一次保证已将主要区域中的数据写入次要区域的时间。 上次同步时间之前写入的所有数据都已复制到次要区域中，而在上次同步时间之后写入的数据则可能尚未写入次要区域并发生丢失。 在发生服务中断时，使用此属性可估计启动帐户故障转移可能会造成的数据丢失量。

最佳做法是，将应用程序设计为，可以使用上次同步时间来评估预期数据丢失。 例如，若要记录所有写入操作，可以比较上次写入操作时间与上次同步时间，以确定哪些写入操作尚未同步到次要区域。

如需详细了解如何检查“上次同步时间”属性，请参阅[检查存储帐户的“上次同步时间”属性](last-sync-time-get.md)。

### <a name="use-caution-when-failing-back-to-the-original-primary"></a>谨慎故障回复到原始主要区域

从主要区域故障转移到次要区域后，存储帐户被配置为在新的主要区域中本地冗余。 然后可以将帐户重新配置为使用异地冗余。 如果帐户在故障转移完成后再次配置为使用异地冗余，新的主要区域就会立即开始将数据复制到新的次要区域（在原始故障转移发生前为主要区域）。 不过，将主要区域中的现有数据完全复制到新的次要区域可能需要一段时间才能完成。

为存储帐户重新配置异地冗余后，可以启动另一个故障转移，从新的主要区域故障回复到新的次要区域。 在这种情况下，故障转移发生前的原始主要区域重新成为主要区域，并配置为本地冗余。 故障转移完成后的主要区域（即原始次要区域）中的所有数据都会丢失。 如果在故障回复前存储帐户中的大部分数据都尚未复制到新的次要区域，可能会丢失大量数据。

为了避免大量数据丢失，请在故障回复前检查“上次同步时间”属性的值。 若要评估预期数据丢失，请比较上次同步时间与数据上次写入新的主要区域时间。 

## <a name="initiate-an-account-failover"></a>启动帐户故障转移

可以从 Azure 门户、PowerShell、Azure CLI 或 Azure 存储资源提供程序 API 启动帐户故障转移。 若要详细了解如何启动故障转移，请参阅[启动帐户故障转移](storage-initiate-account-failover.md)。

## <a name="additional-considerations"></a>其他注意事项

请参阅本部分中介绍的其他注意事项，了解强制执行故障转移时对应用程序和服务可能产生的影响。

### <a name="storage-account-containing-archived-blobs"></a>包含已存档 blob 的存储帐户

包含已存档 blob 的存储帐户支持帐户故障转移。 故障转移完成后，需要先将所有已存档 blob 都解除冻结到联机层，然后才能将帐户配置为使用异地冗余。

### <a name="storage-resource-provider"></a>存储资源提供程序

故障转移完成后，客户端可再次读取并写入新的主要区域中的 Azure 存储数据。 但是，Azure 存储资源提供程序不会进行故障转移，因此资源管理操作仍必须在主要区域中进行。 如果主要区域不可用，将无法对存储帐户执行管理操作。

由于 Azure 存储资源提供程序不会进行故障转移，因此在故障转移完成后，[Location](/dotnet/api/microsoft.azure.management.storage.models.trackedresource.location) 属性将返回原始主位置。

### <a name="azure-virtual-machines"></a>Azure 虚拟机

Azure 虚拟机 (VM) 不会在帐户故障转移过程中进行故障转移。 如果主要区域不可用且你故障转移到次要区域，那么就需要在故障转移完成后重新创建所有 VM。 此外，还可能会丢失与帐户故障转移相关联的数据。 Microsoft 建议使用以下特定于 Azure 中虚拟机的[高可用性](../../virtual-machines/availability.md)和[灾难恢复](../../virtual-machines/backup-recovery.md)指南。

### <a name="azure-unmanaged-disks"></a>Azure 非托管磁盘

根据最佳做法，Microsoft 建议将非托管磁盘转换为托管磁盘。 不过，如果需要对包含附加到 Azure VM 的非托管磁盘的帐户进行故障转移，必须在启动故障转移前关闭 VM。

非托管磁盘在 Azure 存储中存储为页 blob。 如果 VM 在 Azure 中运行，任何附加到 VM 的非托管磁盘都会被租用。 如果 blob 上有租用，便无法继续帐户故障转移。 若要执行故障转移，请按照以下步骤操作：

1. 开始前，先记下所有非托管磁盘的名称、逻辑单元号 (LUN) 和附加到的 VM。 此操作可便于在故障转移完成后更轻松地重新附加磁盘。
2. 关闭 VM。
3. 删除 VM，但保留非托管磁盘的 VHD 文件。 记下 VM 删除时间。
4. 等到“上次同步时间”已更新且晚于 VM 删除时间。 这一步很重要，因为如果在故障转移发生时辅助终结点尚未使用 VHD 文件完全更新，那么 VM 可能无法在新的主要区域中正常运行。
5. 启动帐户故障转移。
6. 等到帐户故障转移完成，且次要区域已成为新的主要区域。
7. 在新的主要区域中创建 VM，并重新附加 VHD。
8. 启动新 VM。

请注意，当 VM 关闭时，临时磁盘中存储的任何数据都会丢失。

## <a name="unsupported-features-and-services"></a>不支持的功能和服务

帐户故障转移不支持以下功能或服务：

- Azure 文件同步不支持存储帐户故障转移。 不得对包含 Azure 文件共享且用作 Azure 文件同步中云终结点的存储帐户执行故障转移。 否则，将会导致同步停止，并且可能还会在有新分层文件的情况下导致意外数据丢失。
- 目前不支持启用了分层命名空间的存储帐户（例如 Data Lake Storage Gen2）。
- 无法对包含高级块 blob 的存储帐户执行故障转移。 支持高级块 blob 的存储帐户暂不支持异地冗余。
- 无法对包含任何已启用 [WORM 不可变性策略](../blobs/storage-blob-immutable-storage.md)的容器执行故障转移。 已解锁/锁定的基于时间的保留或法定保留策略会阻止故障转移，以便保持合规性。

## <a name="copying-data-as-an-alternative-to-failover"></a>除了故障转移外，还可以复制数据

如果将存储帐户配置为具有对次要区域的读取访问权限，则可以将应用程序设计为从辅助终结点进行读取。 如果不希望在主要区域发生服务中断时进行故障转移，可使用 [AzCopy](./storage-use-azcopy-v10.md)、[Azure PowerShell](/powershell/module/az.storage/) 或 [Azure 数据移动库](../common/storage-use-data-movement-library.md)等工具，将数据从次要区域中的存储帐户复制到未受影响区域中的另一个存储帐户。 然后，可以将应用程序指向此存储帐户，以进行读写操作。

> [!CAUTION]
> 不应将帐户故障转移用作数据迁移策略的一部分。

## <a name="microsoft-managed-failover"></a>Microsoft 托管的故障转移

在由于重大灾难而导致区域丢失的极端情况下，Microsoft 可能会启动区域故障转移。 在此情况下，不需要采取任何操作。 在 Microsoft 托管的故障转移完成之前，你对存储帐户不拥有写入访问权限。 如果存储帐户已配置 RA-GRS 或 RA-GZRS，应用程序可以从次要区域读取数据。

## <a name="see-also"></a>另请参阅

- [使用异地冗余设计高度可用的应用程序](geo-redundant-design.md)
- [启动帐户故障转移](storage-initiate-account-failover.md)
- [检查存储帐户的“上次同步时间”属性](last-sync-time-get.md)
- [教程：生成使用 Blob 存储的高可用性应用程序](../blobs/storage-create-geo-redundant-storage.md)
