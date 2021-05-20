---
title: 显示 Azure NetApp 文件复制关系的运行状况 | Microsoft Docs
description: 描述如何在 Azure NetApp 文件的源卷或目标卷上查看复制状态。
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
ms.topic: how-to
ms.date: 03/11/2021
ms.author: b-juche
ms.openlocfilehash: 2819ee3bc76c0b9ff0f35d442e52149096ddc9f7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "104590970"
---
# <a name="display-health-status-of-replication-relationship"></a>显示复制关系的运行状况 

可以查看源卷或目标卷上的复制状态。  

## <a name="display-replication-status"></a>显示复制状态

1. 在源卷或目标卷中，单击任一卷的“存储服务”下的“复制”。

    将显示以下复制状态和运行状况信息：  
    * 终结点类型 – 标识卷是复制的源还是目标。
    * 运行状况 – 显示复制关系的运行状况。
    * 镜像状态 – 显示以下值之一：
        * 未初始化：  
            这是创建对等关系时的初始状态和默认状态。 在初始化成功完成之前，状态将保持未初始化状态。
        * 镜像：   
            目标卷已初始化并准备好接收镜像更新。
        * 中断：   
            这是在对等关系中断之后的状态。 目标卷为 `‘RW’`，并且存在快照。
    * 关系状态 – 显示以下值之一： 
        * *空闲*：  
            没有正在进行的传输操作，也没有禁用未来传输。
        * 正在传输：  
            有一个正在进行的传输操作，且没有禁用未来传输。
    * 复制计划 - 显示初始化（基准复制）完成时将执行增量镜像更新的频率。

    * 总体进度 - 显示当前传输操作传输的总数据量（以字节为单位）。 此数据量是实际传输的位数，可能不同于源和目标卷报告的逻辑空间。  

    ![复制运行状况](../media/azure-netapp-files/cross-region-replication-health-status.png)

> [!NOTE] 
> 如果以前的复制作业未完成，复制关系的运行状况将显示为“不正常”。 此状态是在较低传输时段内传输大型卷的结果（例如，大型卷的传输时间为 10 分钟）。 在这种情况下，关系状态显示“正在传输”，运行状况显示“不正常”。

## <a name="next-steps"></a>后续步骤  

* [跨区域复制](cross-region-replication-introduction.md)
* [管理灾难恢复](cross-region-replication-manage-disaster-recovery.md)
* [重设跨区域复制目标卷的大小](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-a-cross-region-replication-destination-volume)
* [卷复制指标](azure-netapp-files-metrics.md#replication)
* [删除卷复制或卷](cross-region-replication-delete.md)
* [跨区域复制故障排除](troubleshoot-cross-region-replication.md)

