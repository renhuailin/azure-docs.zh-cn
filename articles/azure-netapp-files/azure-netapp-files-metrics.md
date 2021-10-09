---
title: Azure NetApp 文件的指标 | Microsoft Docs
description: Azure NetApp 文件提供有关已分配的存储、实际存储使用情况、卷、IOPS 和延迟的指标。 使用这些指标来了解使用量和性能。
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
ms.date: 09/29/2021
ms.author: b-juche
ms.openlocfilehash: cc034689e2c3cd6846986680225ca7ca21ac41c8
ms.sourcegitcommit: f3f2ec7793ebeee19bd9ffc3004725fb33eb4b3f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2021
ms.locfileid: "129407525"
---
# <a name="metrics-for-azure-netapp-files"></a>Azure NetApp 文件的指标

Azure NetApp 文件提供有关已分配的存储、实际存储使用情况、卷、IOPS 和延迟的指标。 通过分析这些指标，可以更好地了解 NetApp 帐户的使用模式和卷性能。  

可以通过选择“容量池”或“卷”来查找容量池或卷的指标。   然后单击“指标”以查看可用指标： 

[ ![显示如何导航到“指标”下拉菜单的快照。](../media/azure-netapp-files/metrics-navigate-volume.png) ](../media/azure-netapp-files/metrics-navigate-volume.png#lightbox)

## <a name="usage-metrics-for-capacity-pools"></a><a name="capacity_pools"></a>容量池的使用情况指标

- 池分配大小   
    池的预配大小。

- 分配到卷的池的大小  
    给定容量池中的卷配额总和 (GiB)（即，容量池中卷的预配大小的总和）。  
    这是创建卷期间选择的大小。  

- *池消耗大小*  
    容量池中各个卷已使用的逻辑空间 (GiB) 的总和。  

- 池的总快照大小    
    池中所有卷的快照大小总和。

## <a name="usage-metrics-for-volumes"></a><a name="volumes"></a>卷的使用情况指标

- 卷已使用大小的百分比    
    已用卷（包括快照）的百分比。  
- 卷分配大小   
    卷的预配大小
- 卷配额大小    
    为卷预配的配额大小 (GiB)。   
- 卷消耗大小   
    卷的逻辑大小（所用字节数）。  
    此大小包括活动文件系统和快照使用的逻辑空间。  
- 卷快照大小   
   一个卷中全部快照的大小。  

## <a name="performance-metrics-for-volumes"></a>卷的性能指标

> [!NOTE] 
> “平均读取延迟”和“平均写入延迟”的卷延迟在存储服务中测量，不包括网络延迟。

- 平均读取延迟   
    从卷进行读取的平均时间（以毫秒为单位）。
- 平均写入延迟   
    向卷中进行写入的平均时间（以毫秒为单位）。
- 读取 IOPS   
    每秒读取到卷的次数。
- 写 IOPS   
    每秒向卷中写入的次数。

## <a name="volume-replication-metrics"></a><a name="replication"></a>卷复制指标

> [!NOTE] 
> * 网络传输大小（例如，“卷复制总传输”指标）可能不同于跨区域复制的源或目标卷。 此行为是由于使用有效的复制引擎来最大程度地减少网络传输成本而导致的。
> * 目前是为复制目标卷填充卷复制指标，而不是为复制关系的源填充。

- 卷复制状态是否正常   
    复制关系的状况。 正常状态表示为 `1`。 不正常状态表示为 `0`。

- 卷复制是否正在传输    
    卷复制的状态是否为“正在传输”。 

- 卷复制上次传输持续时间   
    上次传输完成所用的时间（秒）。 

- 卷复制上次传输大小    
    作为上次传输的一部分传输的总字节数。 

- 卷复制进度    
    当前传输操作传输的总数据量。 

- 卷复制总传输字节   
    为关系传输的累计字节数。 

## <a name="throughput-metrics-for-capacity-pools"></a>容量池的吞吐量指标   

* 池分配的吞吐量    
    属于该池的所有卷的总吞吐量。
    
* 池的预配吞吐量   
    此池的预配吞吐量。


## <a name="throughput-metrics-for-volumes"></a>卷的吞吐量指标   

* 读取吞吐量   
    读取吞吐量（以字节/秒为单位）。
    
* 总吞吐量   
    所有吞吐量的总和（以字节/秒为单位）。

* 写入吞吐量    
    写入吞吐量（以字节/秒为单位）。

* 其他吞吐量   
    未读取或写入的其他吞吐量（以字节/秒为单位）。

## <a name="volume-backup-metrics"></a>卷备份指标  

* 卷备份是否已启用   
    显示是否对卷启用了备份。 已启用 `1`。 `0` 表示禁用该作业。

* 卷备份操作是否已完成   
    显示最后一个卷备份或还原操作是否成功完成。  `1` 表示成功。 `0` 表示未成功。

* *卷备份是否已暂停*   
    显示卷的备份策略是否已暂停。  `1` 表示未暂停。 `0` 表示已暂停。

* 卷备份字节数   
    为此卷备份的总字节数。

* 卷备份上次传输的字节数   
    上次备份或还原操作传输的总字节数。  

## <a name="next-steps"></a>后续步骤

* [了解 Azure NetApp 文件的存储层次结构](azure-netapp-files-understand-storage-hierarchy.md)
* [创建容量池](azure-netapp-files-set-up-capacity-pool.md)
* [为 Azure NetApp 文件创建卷](azure-netapp-files-create-volumes.md)
