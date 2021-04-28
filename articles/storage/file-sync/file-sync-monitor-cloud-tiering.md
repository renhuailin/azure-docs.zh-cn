---
title: 监视 Azure 文件同步云分层 | Microsoft Docs
description: 用于监视云分层策略的指标的详细信息。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: cbdde16ff214077d7fe4814c8841d805fe05c37c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/20/2021
ms.locfileid: "107795773"
---
# <a name="monitor-cloud-tiering"></a>监视云分层
可以通过两种方式监视云分层策略：“服务器终结点属性”边栏选项卡和 Azure Monitor。

## <a name="monitoring-via-server-endpoint"></a>通过服务器终结点进行监视

登录到 [Azure 门户](https://portal.azure.com/)，导航到要监视的服务器终结点的“服务器终结点属性”边栏选项卡，然后向下滚动到“云分层”部分。 

![“服务器终结点属性”中的”云分层”部分的屏幕截图。](media/storage-sync-monitoring-cloud-tiering/cloud-tiering-monitoring-5.png)

“节省空间”是通过启用云分层所节省的空间量。 如果本地卷大小足以容纳所有数据，节省空间将为 0%。 如果节省空间为 0% 或更低，这可能表示云分层对当前本地缓存大小没有好处。 

“缓存命中率”是在本地缓存中打开的文件的百分比。 缓存命中率由直接从缓存打开的文件除以打开的文件总数计算得出。 如果缓存命中率为 100%，这表示最近一小时内打开的所有文件都已在本地缓存中。 如果目标是减少流出量，这表明当前策略起作用。

> [!NOTE]
> 采用随机存取模式的工作负载通常缓存命中率较低。 

“总大小(云)”是同步到云的文件的大小。 

“缓存大小(服务器)”是服务器上已下载和已分层的文件的总大小。 有时，缓存大小可能大于云中文件的总大小。 服务器上卷的群集大小之类的变量可能会导致此问题。 如果缓存大小大于所需大小，请考虑增加卷可用空间策略。 

“有效卷策略”用于供 Azure 文件同步确定要在服务器终结点所在卷上剩余的可用空间量。 当同一卷上有多个服务器终结点时，服务器终结点中的最高卷可用空间策略将成为该卷上所有服务器终结点的有效卷策略。 例如，如果同一卷上有两个服务器终结点，一个的卷可用空间为 30%，另一个为 50%，则这两个服务器终结点的有效卷可用空间策略将为 50%。

“当前卷可用空间”是本地服务器上当前可用的卷可用空间。 在卷可用空间策略启动之前，如果流出量很大而可用卷可用空间更大，请考虑禁用日期策略。 另一个问题可能是，此策略启动之前，在当前已分层的文件中，最近访问的文件大于剩余卷可用空间。 在这种情况下，请考虑增加本地卷大小。 

## <a name="monitoring-via-azure-monitor"></a>通过 Azure Monitor 进行监视

转到“存储同步服务”，并在侧导航中选择“指标” 。 

可以使用三种不同的指标专门监视来自云分层的流出量：

- 云分层回调大小
    - 这是回调的数据的总大小（以字节为单位），可用于标识回调的数据量。
- 按应用程序计算的云分层回调大小
    - 这是按应用程序计算的回调数据的总大小（以字节为单位），可用于标识数据的回调者。
- 云分层回调吞吐量
    - 此指标度量回调数据的速度（以字节为单位），当你对性能有疑虑时，应该使用此指标。 

若要让图表显示的内容更加具体，请考虑使用“添加筛选器”和“应用拆分” 。
 
有关 Azure 文件同步的不同类型指标以及如何使用这些指标的详细信息，请参阅[监视 Azure 文件同步](file-sync-monitoring.md)。

有关如何使用指标的详细信息，请参阅 [Azure 指标资源管理器入门](../../azure-monitor/essentials/metrics-getting-started.md)。

若要更改云分层策略，请参阅[选择云分层策略](file-sync-choose-cloud-tiering-policies.md)。

## <a name="next-steps"></a>后续步骤

* [规划 Azure 文件同步部署](file-sync-planning.md)