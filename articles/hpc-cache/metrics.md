---
title: Azure HPC 缓存指标和监视
description: 如何查看有关 Azure HPC 缓存的统计信息
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/08/2021
ms.author: v-erkel
ms.openlocfilehash: 0963a412ac3939ad9421c2f7c1de7d72c24995ac
ms.sourcegitcommit: b5508e1b38758472cecdd876a2118aedf8089fec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2021
ms.locfileid: "113589660"
---
# <a name="cache-metrics-and-monitoring"></a>缓存指标和监视

Azure 门户提供多种内置的可视化效果，可用于监视 Azure HPC 缓存的性能。

本文介绍在何处查找可视化效果以及它们显示的内容。

有四种 HPC 缓存门户页显示缓存性能数据：

* **概述**
* **度量值**
* “缓存报表”
* **客户端状态**

从侧栏的“监视”菜单中打开“指标”、“缓存报表”和“客户端状态”页。 缓存的“概述”页是侧栏菜单上的顶部项。

![HPC 缓存的 Azure 门户界面左侧“监视”菜单的屏幕截图。](media/monitoring-menu.png)

## <a name="overview-page"></a>概述页

缓存的“概述”页面显示一些基本的缓存统计信息的图表——缓存吞吐量、每秒操作数和延迟。 图表位于页面底部的“概要”部分下。

![该屏幕截图显示了三个折线图，其中显示的是上述示例缓存的统计信息](media/hpc-cache-overview-stats.png)

这些图表是 Azure 内置监视和分析工具的一部分。 从 [Azure 监视文档](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-in-the-azure-portal)中了解有关这些功能的详细信息。

## <a name="metrics-page"></a>“指标”页

HPC 缓存“指标”页是另一个标准的 Azure 功能。 按照页面上的提示创建自己的图表，并详细了解功能。

## <a name="cache-report"></a>缓存报表

“缓存报表”是一组自定义图表，它们显示缓存的已用空间、可用空间和缓存回收率。

默认情况下，此页显示缓存的所有存储目标的聚合数据，但你可以使用“存储目标”控件来显示某个特定存储目标的缓存消耗数据。 还可以自定义要显示的时间范围。

![“缓存报告”页的屏幕截图。](media/cache-report.png)

* “缓存已用空间”是缓存存储中正在使用的空间，用于存储客户端计算机请求的文件。 如果选择单个存储目标，它会只显示该后端存储系统中的文件所用的空间。

* “缓存可用空间”是缓存存储中剩余的可用容量。

* “缓存回收率”表示从缓存中删除旧文件以为新请求的文件留出空间的速率。 对于单个存储目标，此计算仅包括存储在该目标上的文件。

## <a name="client-status"></a>客户端状态

“客户端状态”页列出了连接到 Azure HPC 缓存的客户端计算机。

![客户端状态页的屏幕截图。](media/client-status.png)

你可以使用筛选器自定义表，以显示特定的客户端地址或地址范围、特定的存储目标或单个缓存装载地址。

表中显示的连接类型与存储目标的客户端连接有关：

* `azure_https_backend` 是与标准 Azure Blob 存储系统的安全客户端连接。
* `mount_backend` 是与硬件 NFS 系统或启用 NFS 的 blob 容器的 NFS 连接。
* `nfs_backend` 类似于 `mount_backend` 连接，但表示由 NFS 内部远程过程调用使用的单独连接，而不是由客户端装载操作启动的连接。

调试客户端与 HPC 缓存之间的连接问题时，这些值非常有用。

## <a name="next-steps"></a>后续步骤

* 了解有关 [Azure 指标和统计信息工具的](../azure-monitor/index.yml)的详细信息
* [管理缓存](hpc-cache-manage.md)
