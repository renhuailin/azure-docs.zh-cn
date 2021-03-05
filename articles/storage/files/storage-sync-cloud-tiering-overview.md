---
title: 了解 Azure 文件同步云分层 |Microsoft Docs
description: 了解云分层，这是一项可选的 Azure 文件同步功能。 经常访问的文件以本地方式缓存在服务器上;其他内容分层为 Azure 文件。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 1/4/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 9ca8df21e0221d364f75eaeb26f0b2961d7e0b08
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2021
ms.locfileid: "102204306"
---
# <a name="cloud-tiering-overview"></a>云分层概述
云分层是 Azure 文件同步的一项可选功能，可减少所需的本地存储量，同时保留本地文件服务器的性能。

启用后，此功能仅存储本地服务器上 (热) 文件中经常访问的文件。 不常访问 (冷) 文件被拆分为命名空间 (文件和文件夹结构) 和文件内容。 命名空间存储在本地，文件内容存储在云中的 Azure 文件共享中。 

当用户打开分层文件时，Azure 文件同步将文件数据从 Azure 中的文件共享中无缝地撤回。

## <a name="how-cloud-tiering-works"></a>云分层的工作方式

### <a name="cloud-tiering-policies"></a>云分层策略
启用云分层时，可以将两个策略设置为在将酷文件分层时通知 Azure 文件同步： **卷可用空间策略** 和 **日期策略**。 

#### <a name="volume-free-space-policy"></a>卷可用空间策略
**卷可用空间策略** 告诉 Azure 文件同步在本地磁盘上占用一定量的空间时，将冷文件分层到云。 

例如，如果本地磁盘容量为 200 GB，并且你希望至少 40 GB 的本地磁盘容量始终可用，则应将卷可用空间策略设置为20%。 卷可用空间适用于卷级别，而不是单个目录或服务器终结点的级别。 

若要了解卷可用空间策略如何影响在添加新服务器终结点时最初下载的文件，请 (参阅) 部分 [影响云分层的同步策略](#sync-policies-that-affect-cloud-tiering) 。

#### <a name="date-policy"></a>日期策略
利用 **日期策略**，如果尚未对其进行访问，则将酷文件分层到云， (即在 x 天内读取或写入) 。 例如，如果您注意到，在未访问的情况下已超过15天的文件通常是存档文件，则应将日期策略设置为15天。 

有关 "日期策略" 和 "卷可用空间" 策略如何协同工作的更多示例，请参阅 [选择 Azure 文件同步云分层策略](storage-sync-choose-cloud-tiering-policies.md)。

### <a name="windows-server-data-deduplication"></a>Windows Server 重复数据删除
从 Windows Server 2016 开始，在启用云分层的卷上支持重复数据删除。 有关更多详细信息，请参阅 [规划 Azure 文件同步部署](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#data-deduplication)。

### <a name="cloud-tiering-heatmap"></a>云分层热度地图
Azure 文件同步监视文件访问 () 一段时间内的读取和写入操作，并根据最频繁和最新访问的情况，为每个文件分配一个热度分数。 它使用这些分数在每个服务器终结点上生成命名空间的 "热度地图"。 此热度地图是一个功能列表，其中包含已启用云分层并按其温度分数排序的位置中的所有同步文件。 最近打开的经常访问的文件被视为是热的，而曾经接触过且未访问过一段时间的文件被视为酷。 

为了确定热度地图中单个文件的相对位置，系统将按以下顺序使用其最大时间戳：最大 (上次访问时间、上次修改时间、创建时间) 。 

通常，会跟踪和提供上次访问时间。 但是，当创建新的服务器终结点时，如果已启用云分层，则没有足够的时间来观察文件访问。 如果没有有效的上次访问时间，则改用上次修改时间来计算热度地图中的相对位置。  

日期策略的工作方式相同。 如果没有上次访问时间，则日期策略将作用于上次修改时间。 如果不可用，它将回退到文件的创建时间。 随着时间的推移，系统会发现更多的文件访问请求并自动开始使用自跟踪的上次访问时间。

> [!Note]
> 云分层不依赖 NTFS 功能跟踪上次访问时间。 默认情况下，此 NTFS 功能处于关闭状态，因此，由于性能方面的原因，我们不建议您手动启用此功能。 云分层单独跟踪上次访问时间。

### <a name="sync-policies-that-affect-cloud-tiering"></a>影响云分层的同步策略

对于 Azure 文件同步代理版本11，你可以设置两个其他 Azure 文件同步策略来影响云分层： **初始下载** 和 **主动撤回**。

#### <a name="initial-download"></a>初始下载

当服务器连接到包含文件的 Azure 文件共享时，你现在可以决定希望服务器最初如何下载文件共享数据。 启用云分层时，有两种选择。 

![启用云分层时的初始下载屏幕截图](media/storage-sync-cloud-tiering-overview/cloud-tiering-overview-3.png)  

第一种方法是首先撤回命名空间，然后按上次修改时间戳撤回文件内容，直到达到本地磁盘容量。 如果你有足够的磁盘空间，并且知道最后修改的文件应在本地缓存，则此选项是理想选择。 由于磁盘空间不足，无法在本地存储的文件将会分层。        

第二种方法是最初仅撤回命名空间，并且仅在被访问时才撤回文件内容。 如果要最大程度地减少本地磁盘上使用的容量并希望用户决定哪些文件应在本地缓存，则此选项最佳。 所有文件都将作为分层文件以此选项启动。

![禁用云分层时的初始下载屏幕截图](media/storage-sync-cloud-tiering-overview/cloud-tiering-overview-4.png)

禁用云分层时，可以使用第三个选项，这是为了避免将分层文件合在一起。 在这种情况下，文件将仅在完全下载后出现在服务器上。 如果你的应用程序需要完全文件存在并且无法容忍其命名空间中的分层文件，这是理想之选。      

#### <a name="proactive-recalling"></a>主动撤回

创建或修改文件时，可以主动地将文件撤回到指定的服务器。 这使新文件或修改后的文件随时可用于每个指定的服务器。 

例如，全球分布式公司在美国和印度有分支机构。 早上 (我们的时间) 信息工作者为全新的项目创建一个新文件夹和新文件，并在其上工作一整天。 Azure 文件同步会将文件夹和文件同步到 (云终结点) 的 Azure 文件共享。 印度的信息工作者将继续在其时区中处理该项目。 在上午，印度的本地 Azure 文件同步启用的服务器需要在本地提供这些新文件，使印度团队能够有效地使用本地缓存。 启用此模式可防止初始文件访问速度较慢，因为需要重新调用，并使服务器能够在 Azure 文件共享中更改或创建文件后主动重新调用文件。

如果在本地不需要回调到服务器的文件，则不必要的撤回可能会增加您的出口流量和成本。 因此，仅当知道使用最新的云更改预填充服务器的缓存时，才启用主动撤回，这会对使用该服务器上文件的用户或应用程序产生积极影响。 

启用主动撤回可能还会导致服务器上的带宽使用率增加，并可能导致本地服务器上的其他相对新内容主动分层，因为正在重新调用文件。 反过来，如果要分层的文件被服务器视为处于热状态，这可能会导致更多的召回。 

:::image type="content" source="media/storage-sync-files-deployment-guide/proactive-download.png" alt-text="显示当前生效的服务器终结点的 Azure 文件共享下载行为的图像，以及用于打开允许更改该服务的菜单的按钮。":::

有关主动调用的详细信息，请参阅 [Deploy Azure 文件同步](storage-sync-files-deployment-guide.md#proactively-recall-new-and-changed-files-from-an-azure-file-share)。

## <a name="tiered-vs-locally-cached-file-behavior"></a>分层与本地缓存的文件行为

云分层是指命名空间 (文件和文件夹层次结构，以及文件属性) 和文件内容之间的隔离。 

#### <a name="tiered-file"></a>分层文件

对于分层文件，磁盘上的大小为零，因为文件内容本身并未存储在本地。 当文件分层时，Azure 文件同步文件系统筛选器 (StorageSync.sys) 将以本地方式将文件替换为 (重新分析点) 的指针。 重新分析点表示 Azure 文件共享中的文件的 URL。 分层文件在 NTFS 中设置了“脱机”属性和 FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS 属性，这样第三方应用程序便能安全地识别分层文件。   

![仅分层命名空间时文件属性的屏幕截图。](media/storage-sync-cloud-tiering-overview/cloud-tiering-overview-2.png)    

#### <a name="locally-cached-file"></a>本地缓存的文件

另一方面，对于存储在本地文件服务器中的文件，磁盘大小约等于文件的逻辑大小，因为整个文件 (文件属性和文件内容) 本地存储。     

![当文件不是分层命名空间 + 文件内容时的屏幕截图。](media/storage-sync-cloud-tiering-overview/cloud-tiering-overview-1.png) 

另外，文件也可能会被部分分层（或部分召回）。 在部分分层文件中，该文件的一部分位于磁盘上。 这种情况可能是由于多媒体播放机或 zip 实用程序（支持文件的流访问）部分读取了文件。 Azure 文件同步为智能，只从连接的 Azure 文件共享撤回请求的信息。

> [!NOTE]
> Size 表示文件的逻辑大小。 磁盘上的大小表示存储在磁盘上的文件流的物理大小。

## <a name="next-steps"></a>后续步骤
* [选择 Azure 文件同步云分层策略](storage-sync-choose-cloud-tiering-policies.md)
* [规划 Azure 文件同步部署](storage-sync-files-planning.md)

