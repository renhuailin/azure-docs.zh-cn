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
ms.date: 05/06/2021
ms.author: b-juche
ms.openlocfilehash: e16e95bbb65bde6c4c0b38b9c68c0f7287b8b9b3
ms.sourcegitcommit: 89c4843ec85d1baea248e81724781d55bed86417
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2021
ms.locfileid: "108795575"
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
 
- 卷复制延隔时间   
    镜像上的数据落后于源的时间（秒）。 

- 卷复制上次传输持续时间   
    上次传输完成所用的时间（秒）。 

- 卷复制上次传输大小    
    作为上次传输的一部分传输的总字节数。 

- 卷复制进度    
    当前传输操作传输的总数据量。 

- 卷复制总传输字节   
    为关系传输的累计字节数。 

## <a name="throughput-metrics-for-capacity-pools"></a>容量池的吞吐量指标   

* 分配给卷的池的吞吐量    
    分配给给定容量池中的卷的总吞吐量。 也就是说，容量池中卷的已分配吞吐量的总数。   

* 池消耗的吞吐量   
    给定容量池中卷消耗的总吞吐量。   

* 分配给卷的池的吞吐量百分比   
    分配给卷的容量池预配吞吐量百分比。   

* 池消耗的吞吐量百分比    
    卷消耗的容量池预配吞吐量百分比。

## <a name="throughput-metrics-for-volumes"></a>卷的吞吐量指标   

*  卷分配的吞吐量    
    为卷分配的父容量池吞吐量 (MiB/s)。 这是卷能够消耗的最大吞吐量。

* 卷消耗的吞吐量    
    卷正在使用的实际吞吐量 (MiB/s)。

* 卷消耗的吞吐量百分比   
    卷正在使用的已分配吞吐量百分比。 即“卷消耗的吞吐量”占“卷分配的吞吐量”的百分比。


## <a name="next-steps"></a>后续步骤

* [了解 Azure NetApp 文件的存储层次结构](azure-netapp-files-understand-storage-hierarchy.md)
* [设置容量池](azure-netapp-files-set-up-capacity-pool.md)
* [为 Azure NetApp 文件创建卷](azure-netapp-files-create-volumes.md)
