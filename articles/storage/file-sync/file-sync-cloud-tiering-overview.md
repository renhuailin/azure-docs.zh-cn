---
title: 理解 Azure 文件同步云分层 | Microsoft Docs
description: 理解云分层，这是一项可选的 Azure 文件同步功能。 经常访问的文件在服务器本地缓存；其他文件则分层到 Azure 文件存储。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 1f0ae8b5a4ac2572719340020695a5bcd6b0d8a2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128601342"
---
# <a name="cloud-tiering-overview"></a>云分层概述

云分层是 Azure 文件同步的一项可选功能，可减少所需的本地存储量，同时保留本地文件服务器的性能。

启用后，此功能仅存储本地服务器上经常访问的（热）文件。 不常访问的（冷）文件会拆分为命名空间（文件和文件夹结构）和文件内容。 命名空间存储在本地，文件内容存储在云中的 Azure 文件共享中。

如果用户打开分层文件，Azure 文件同步会从 Azure 的文件共享中无缝召回文件数据。

## <a name="how-cloud-tiering-works"></a>云分层的工作方式

### <a name="cloud-tiering-policies"></a>云分层策略

启用云分层后，可以设置两种策略来通知 Azure 文件同步何时对冷文件进行分层：卷可用空间策略和日期策略 。

#### <a name="volume-free-space-policy"></a>卷可用空间策略

卷可用空间策略告诉 Azure 文件同步，当本地磁盘上被占用一定量的空间时，将冷文件分层到云。

例如，如果本地磁盘容量为 200 GiB，并且你希望至少 40 GiB 的本地磁盘容量始终可用，则应将卷可用空间策略设置为 20%。 卷可用空间适用于卷级别，不适用于各个目录或服务器终结点的级别。

#### <a name="date-policy"></a>日期策略

对于日期策略，如果在 x 天内没有访问（即读取或写入）冷文件，则将这些文件分层到云。 例如，如果你注意到超过 15 天没有访问的文件通常是归档文件，则应该把日期策略设置为 15 天。

有关日期策略和卷可用空间策略如何协同工作的更多示例，请参阅[选择 Azure 文件同步云分层策略](file-sync-choose-cloud-tiering-policies.md)。

### <a name="windows-server-data-deduplication"></a>Windows Server 重复数据删除

从 Windows Server 2016 开始，启用了云分层的卷上均支持重复数据删除。 有关详细信息，请参阅[规划 Azure 文件同步部署](file-sync-planning.md#data-deduplication)。

### <a name="cloud-tiering-heatmap"></a>云分层热度地图

Azure 文件同步监视一段时间内的文件访问（读取和写入操作），并根据访问的频率和最近的访问情况，为每个文件分配一个热度分数。 它使用这些分数在每个服务器终结点上生成命名空间的“热度地图”。 此热度地图是某个已启用云分层的位置上的所有同步文件的列表，按照文件热度分数排序。 最近打开的频繁访问的文件可视为热文件，而几乎没有被碰过的、有一段时间没有进行访问的文件可视为冷文件。

为了确定热度地图中单个文件的相对位置，系统将按以下顺序，使用其时间戳最大值：最大值（上次访问时间、上次修改时间、创建时间）。

通常，会跟踪和提供上次访问时间。 但是，当创建新的服务器终结点时，如果已启用云分层，则没有足够的时间来观察文件访问。 如果没有有效的上次访问时间，则改用上次修改时间来计算热度地图中的相对位置。

日期策略的工作原理也是这样。 如果没有上次访问时间，日期策略将作用于上次修改时间。 如果上次修改时间不可用，则将回退到文件的创建时间。 随着时间的推移，系统会发现更多的文件访问请求并自动开始使用自跟踪的上次访问时间。

> [!NOTE]
> 云分层不依赖 NTFS 功能跟踪上次访问时间。 默认情况下，此 NTFS 功能处于关闭状态，而出于性能方面的考虑，我们不建议手动启用此功能。 云分层单独跟踪上次访问时间。

### <a name="proactive-recalling"></a>主动召回

创建或修改文件时，可以主动将文件召回到指定的服务器。 主动召回使得新文件或修改后的文件可随时在每个指定的服务器中使用。

例如，一家全球分布的公司在美国和印度都有分支机构。 早上（美国时间），信息工作者为一个全新项目创建新文件夹和新文件，并为其工作一整天。 Azure 文件同步会将文件夹和文件同步到 Azure 文件共享（云终结点）。 印度的信息工作者将在其时区继续处理该项目。 当印度团队早上到达时，印度本地启用 Azure 文件同步的服务器需要这些新文件在本地可用，这样他们就能够有效地在本地缓存中工作。 启用此模式可防止由于按需召回而使初始文件访问变慢，并使服务器能够在 Azure 文件共享中更改或创建文件后立即主动召回它们。

如果在本地不需要召回到服务器的文件，则不必要的召回会增加出口流量和成本。 因此，只有在以下情况才启用主动召回：你知道使用来自云的最近更改预先填充服务器的缓存会对使用该服务器上文件的用户或应用程序产生积极影响。

启用主动召回可能还会导致服务器上带宽使用的增加，而由于要召回的文件数量增加，还可能导致本地服务器上其他相对较新的内容进行主动分层。 反过来，如果服务器认为待分层的文件是热文件，则过于频繁地分层可能导致更多的召回。

:::image type="content" source="media/storage-sync-files-deployment-guide/proactive-download.png" alt-text="该图显示了当前有效的服务器终结点的 Azure 文件共享下载行为，以及用于打开相关菜单以对其进行更改的按钮。":::

有关主动召回的详细信息，请参阅[部署 Azure 文件同步](file-sync-deployment-guide.md#proactively-recall-new-and-changed-files-from-an-azure-file-share)。

## <a name="tiered-vs-locally-cached-file-behavior"></a>分层与本地缓存的文件行为

云分层是指命名空间（文件和文件夹层次结构，以及文件属性）和文件内容之间的分隔。

#### <a name="tiered-file"></a>分层文件

对于分层文件，磁盘上的大小为零，因为文件内容本身并未存储在本地。 分层文件后，Azure 文件同步文件系统筛选器 (StorageSync.sys) 将本地文件替换为指针（重分析点）。 重新分析点表示 Azure 文件共享中的文件的 URL。 分层文件在 NTFS 中设置了“脱机”属性和 FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS 属性，这样第三方应用程序便能安全地识别分层文件。

![屏幕截图显示了文件分层时（仅命名空间）的属性。](media/storage-sync-cloud-tiering-overview/cloud-tiering-overview-2.png)

#### <a name="locally-cached-file"></a>本地缓存的文件

另一方面，对于存储在本地文件服务器中的文件，磁盘大小约等于文件的逻辑大小，因为整个文件（文件特性 + 文件内容）都存储在本地。

![屏幕截图显示了未对文件分层（命名空间 + 文件内容）时文件的属性。](media/storage-sync-cloud-tiering-overview/cloud-tiering-overview-1.png)

另外，文件也可能会被部分分层（或部分召回）。 在部分分层文件中，该文件只有一部分存储在磁盘上。 如果支持流式访问文件的应用程序对文件进行部分读取，则可能部分召回卷上的文件。 一些示例包括多媒体播放机和 zip 实用程序。 Azure 文件同步功能非常高效，只从连接的 Azure 文件共享召回请求的信息。

> [!NOTE]
> 大小表示文件的逻辑大小。 磁盘上的大小表示存储在磁盘上的文件流的物理大小。

## <a name="next-steps"></a>后续步骤

- [选择 Azure 文件同步云分层策略](file-sync-choose-cloud-tiering-policies.md)
- [规划 Azure 文件同步部署](file-sync-planning.md)
