---
title: Azure NetApp 文件卷的跨区域复制 | Microsoft Docs
description: 介绍 Azure NetApp 文件跨区域复制的功能、支持的区域对、服务级别目标、数据持续性和成本模型。
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
ms.date: 08/16/2021
ms.author: b-juche
ms.custom: references_regions
ms.openlocfilehash: 625df6ed5147b77a9829b6e7b3396f4855068d62
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122253854"
---
# <a name="cross-region-replication-of-azure-netapp-files-volumes"></a>Azure NetApp 文件卷的跨区域复制

Azure NetApp 文件复制功能通过跨区域卷复制提供数据保护。 可以以异步方式将数据从一个区域中的 Azure NetApp 文件卷（源）复制到另一个区域中的 Azure NetApp 文件卷（目标）。  此功能可用于在发生区域范围的服务中断或灾难时故障转移关键应用程序。

> [!IMPORTANT]
> 跨区域复制功能目前为预览版。 需要提交候补名单请求，以通过 [Azure NetApp 文件跨区域复制候补名单提交页](https://aka.ms/anfcrrpreviewsignup)访问该功能。 在使用跨区域复制功能之前，请先等待来自 Azure NetApp 文件团队的官方确认电子邮件。

## <a name="supported-cross-region-replication-pairs"></a><a name="supported-region-pairs"></a>支持的跨区域复制对

在各种 [Azure 区域对](../best-practices-availability-paired-regions.md#azure-regional-pairs)和非对之间支持 Azure NetApp 文件卷复制。 Azure NetApp 文件卷复制目前在以下区域之间可用：  

### <a name="azure-regional-pairs"></a>Azure 区域对

* 美国东部和美国西部
* 美国东部 2 和美国中部
* 澳大利亚东部和澳大利亚东南部
* 加拿大中部和加拿大东部
* 印度南部和印度中部 
* 德国中西部和德国北部
* 日本东部和日本西部
* 欧洲北部和欧洲西部
* 英国南部和英国西部
* 阿拉伯联合酋长国北部和阿拉伯联合酋长国中部
* 挪威东部和挪威西部

### <a name="azure-regional-non-standard-pairs"></a>Azure 区域非标准对

*   美国西部 2 和美国东部
*   美国中南部和美国中部
*   美国中南部和美国东部
*   美国中南部和美国东部 2
*   美国东部和美国东部 2
*   美国东部 2 和美国西部 2
*   澳大利亚东部和东南亚 
*   德国中西部和英国南部
*   德国中西部和欧洲西部
*   德国中西部和法国中部

## <a name="service-level-objectives"></a>服务级别目标

恢复点目标 (RPO) 指示可以将数据恢复到的时间点。 RPO 目标通常不到复制计划的两倍，但可能会有所不同。 在某些情况下，由于各种因素（如数据集总大小、更改率、数据覆盖百分比和可用于传输的复制带宽），复制计划可能会超出目标 RPO。   

* 对于 10 分钟的复制计划，RPO 通常不到 20 分钟。  
* 对于每小时复制计划，RPO 通常不到 2 小时。  
* 对于每日复制计划，RPO 通常不到 2 天。  

恢复时间目标 (RTO) 或可容忍的最长业务应用程序停机时间，由引入应用程序并在第二个站点中提供对数据的访问权限的因素确定。 用于中断对等关系以激活目标卷并在第二个站点中提供读取和写入数据访问权限的 RTO 的存储部分应在一分钟内完成。

## <a name="cost-model-for-cross-region-replication"></a>跨区域复制的成本模型  

通过 Azure NetApp 文件跨区域复制，只需为复制的数据量付费。 不会收取任何设置费用，也没有最低的使用费用。 复制价格基于在初始复制配置期间选择的目标卷的复制频率和区域。 有关详细信息，请参阅 [Azure NetApp 文件定价](https://azure.microsoft.com/pricing/details/netapp/)页。  

常规 Azure NetApp 文件存储容量费用适用于复制目标卷（也称为数据保护卷）。 

### <a name="pricing-examples"></a>定价示例

按月收费的跨区域复制量基于当月通过跨区域复制功能复制的数据量。 复制的数据量以 GiB 度量。 它表示从源卷到目标卷的所有常规复制期间以及从目标卷到源卷的所有重新同步复制期间跨两个区域复制的数据总量。

#### <a name="example-1-month-1-baseline-replication-and-incremental-replications"></a>示例1：第 1 个月的基线复制和增量复制

假设以下情况：

* 源卷来自 Azure NetApp 文件高级服务级别。 在当月第一天的开始时间，它的卷配额大小为 1000 GiB，卷已使用大小为 500 GiB。 该卷位于美国中南部区域。
* 目标卷来自 Azure NetApp 文件标准服务级别。 它位于美国东部 2 区域。
* 在上述两个卷之间配置了每小时的跨区域复制。 因此，每个 GiB 的复制价格为 0.12 美元。
* 为简单起见，假设源卷以每小时 0.5-GiB 的数据更改常量更改数据，但卷已使用总大小不会增长（保持为 500 GiB）。 

初始设置完成后，会立即进行基线复制。  

* 基线复制期间复制的数据量：`500 GiB`
* 基线复制费用：`500 GiB * $0.12 = $60`

基线复制完成后，只会复制已更改的块。 因此，在后续增量复制中，每小时只会复制 0.5 GiB 的数据。

* 一个月（30 天）增量复制复制的数据总量：`0.5 GiB * 24 hours * 30 days = 360 GiB`
* 增量复制费用：`360 GiB * $0.12 = $43.2`

在第 1 个月结束时，跨区域复制总费用如下所示：  

*  第 1 个月的跨区域复制总费用：`$60 + $43.2 = $103.2`

常规 Azure NetApp 文件存储容量费用适用于目标卷。 但是，目标卷可以使用不同于源卷层（比源卷层更便宜）的存储层。

#### <a name="example-2-month-2-incremental-replications-and-resync-replications"></a>示例 2：第 2 个月的增量复制和重新同步复制  

假设你有一个源卷、一个目标卷和两者之间的复制关系，请按示例 1 中所述进行设置。 在第二个月（30 天为一月）的 29 天中，按预期发生了每小时复制。

* 29 天增量复制复制的数据总量：`0.5 GiB * 24 hours * 29 days = 348 GiB`

假设在该月的最后一天，在源区域中发生了计划外故障转移，并故障转移到目标卷。 2 小时后，源区域恢复，并执行从目标卷到源卷的重新同步复制。 在 2 小时内，在目标卷上发生了 0.8 GiB 的数据更改，这些数据需要重新同步到源中。

* 过去一天的 22 小时内跨常规复制复制的数据总量：`0.5 GiB * 22 hours = 11 GiB`
* 在一个重新同步复制期间复制的数据量：`0.8 GiB`

因此，在第 2 个月结束时，跨区域复制总费用如下所示：  

* 第 2 个月的跨区域复制总费用：`(348 GiB + 11 GiB + 0.8 GiB) * $0.12 = $43.18`

第 2 个月的常规 Azure NetApp 文件存储容量费用适用于目标卷。

## <a name="next-steps"></a>后续步骤
* [使用跨区域复制的要求和注意事项](cross-region-replication-requirements-considerations.md)
* [创建卷复制](cross-region-replication-create-peering.md)
* [显示复制关系的运行状况](cross-region-replication-display-health-status.md)
* [管理灾难恢复](cross-region-replication-manage-disaster-recovery.md)
* [重设跨区域复制目标卷的大小](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-a-cross-region-replication-destination-volume)
* [卷复制指标](azure-netapp-files-metrics.md#replication)
* [删除卷复制或卷](cross-region-replication-delete.md)
* [跨区域复制故障排除](troubleshoot-cross-region-replication.md)
