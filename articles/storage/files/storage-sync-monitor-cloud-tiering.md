---
title: 监视 Azure 文件同步云分层 |Microsoft Docs
description: 用于监视云分层策略的指标的详细信息。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 1/4/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 93e9d9a552ab1bff16ca15d6bb11faeb5d47a503
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2021
ms.locfileid: "102204303"
---
# <a name="monitor-cloud-tiering"></a>监视云分层
可以通过两种方式监视云分层策略：服务器终结点属性边栏选项卡和 Azure Monitor。

## <a name="monitoring-via-server-endpoint"></a>通过服务器终结点进行监视

登录到 [Azure 门户](https://portal.azure.com/)，然后导航到要监视的服务器终结点的 " **服务器终结点属性** " 边栏选项卡，然后向下滚动到 "云分层" 部分。 

![服务器终结点属性中的 "云分层" 部分的屏幕截图。](media/storage-sync-monitoring-cloud-tiering/cloud-tiering-monitoring-5.png)

**节省空间** 是通过启用云分层节省的空间量。 如果你的本地卷大小足以容纳你的所有数据，将节省0% 的空间。 如果有0% 或节省空间，则可能表示云分层对当前本地缓存大小没有好处。 

**缓存命中率** 是在本地缓存中打开的文件的百分比。 缓存命中率由直接从缓存打开的文件或打开的文件总数计算而来。 缓存命中率为100%，表示你在最近一小时内打开的所有文件都已在本地缓存中。 如果你的目标是减少出口，这会指示当前策略正常工作。

> [!NOTE]
> 使用随机访问模式的工作负荷通常具有更低的缓存命中率。 

**(云) 的总大小** 是已同步到云的文件大小。 

**缓存大小 (server)** 是服务器上文件的总大小（已下载和分层）。 有时，缓存大小可能大于云中文件的总大小。 服务器上卷的群集大小等变量可能会导致此问题。 如果缓存大小大于所需大小，请考虑增加卷可用空间策略。 

Azure 文件同步使用 **有效的卷策略** 来确定要在服务器终结点所在的卷上保留的可用空间量。 当同一卷上有多个服务器终结点时，服务器终结点之间的最高卷可用空间策略将成为该卷上所有服务器终结点的有效卷策略。 例如，如果同一卷上有两个服务器终结点，一个具有30% 的卷可用空间，另一个具有50% 的可用空间，则这两个服务器终结点的有效卷可用空间策略将为50%。

**当前卷可用空间** 是本地服务器上当前可用的卷可用空间。 如果在开始使用卷可用空间策略之前，有大量可用空间，但有更多可用空间，请考虑禁用日期策略。 另一个问题可能是，在当前的分层文件中，最近访问的文件大于在策略启动之前剩余的卷可用空间。 在这种情况下，请考虑增加本地卷大小。 

## <a name="monitoring-via-azure-monitor"></a>通过 Azure Monitor 进行监视

请参阅 **存储同步服务** ，并在侧导航栏中选择 " **指标** "。 

你可以使用三种不同的指标来监视专用于云分层的出口：

- 云分层撤回大小
    - 这是数据的总大小（以字节为单位），可用于确定要召回的数据量。
- 云分层通过应用程序撤回大小
    - 这是一个应用程序以字节为单位回调的数据的总大小，可用于标识正在回调数据的内容。
- 云分层召回吞吐量
    - 这会度量数据的撤回速度（以字节为单位），如果你对性能有担忧，则应该使用此方法。 

若要更具体地了解要显示图形的内容，请考虑使用 " **添加筛选器** " 和 " **应用拆分**"。
 
有关 Azure 文件同步的不同类型指标以及如何使用它们的详细信息，请参阅 [监视 Azure 文件同步](storage-sync-files-monitoring.md)。

有关如何使用指标的详细信息，请参阅[Azure 指标资源管理器入门。](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started)

若要更改云分层策略，请参阅 [选择云分层](storage-sync-choose-cloud-tiering-policies.md)策略。

## <a name="next-steps"></a>后续步骤
* [规划 Azure 文件同步部署](storage-sync-files-planning.md)