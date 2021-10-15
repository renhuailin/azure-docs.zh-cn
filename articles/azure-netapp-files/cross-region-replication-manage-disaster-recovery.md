---
title: 使用 Azure NetApp 文件跨区域复制来管理灾难恢复 | Microsoft Docs
description: 介绍如何使用 Azure NetApp 文件跨区域复制来管理灾难恢复。
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
ms.date: 09/29/2021
ms.author: b-juche
ms.openlocfilehash: 4f1f766fbec4c9e09d1ebd5e982254cdcbd85403
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129273318"
---
# <a name="manage-disaster-recovery-using-cross-region-replication"></a>使用跨区域复制管理灾难恢复 

源卷和目标卷之间正在进行的复制（请参阅[创建卷复制](cross-region-replication-create-peering.md)）会为灾难恢复事件做好准备。 

发生此类事件时，可以[故障转移到目标卷](#fail-over-to-destination-volume)，使客户端能够读取和写入目标卷。 

灾难恢复后，可以执行[重新同步](#resync-replication)操作，将故障转移回源卷。 然后，[重新建立源到目标的复制](#reestablish-source-to-destination-replication)并重新装载源卷以供客户端访问。 

详细信息如下所述。 

## <a name="fail-over-to-destination-volume"></a>故障转移到目标卷

需要激活目标卷时（例如要故障转移到目标区域时）时，需要中断复制对等互连，然后装载目标卷。  

1. 若要中断复制对等互连，请选择目标卷。 单击“存储服务”下的“复制”。  

2.  继续操作前，请检查以下字段：  
    * 确保镜像状态显示为“已进行镜像处理”。   
        如果镜像状态显示为“未初始化”，请不要尝试中断复制对等互连。
    * 确保关系状态显示为“空闲”。   
        如果关系状态显示“正在传输”，请不要尝试中断复制对等互连。   

    请参阅[显示复制关系的运行状况](cross-region-replication-display-health-status.md)。 

3.  单击“中断对等互连”。  

4.  在出现提示时键入“是”，然后单击“中断”按钮 。 

    ![中断复制对等互连](../media/azure-netapp-files/cross-region-replication-break-replication-peering.png)

5.  请按照[为 Windows 或 Linux 虚拟机装载或卸载卷](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)中的步骤装载目标卷。   
    此步骤使客户端能够访问目标卷。

## <a name="resync-volumes-after-disaster-recovery"></a><a name="resync-replication"></a>灾难恢复后重新同步卷

灾难恢复后，可以通过执行重新同步操作来重新激活源卷。  重新同步操作将反向执行复制过程，并将数据从目标卷同步到源卷。  

> [!IMPORTANT] 
> 重新同步操作基于最后可用的常见快照，通过使用来自目标卷的最新更新增量更新源卷来同步源卷和目标卷。 在大多数情况下，此操作无需同步整个卷，因为在最新的常见快照之后，只需将对目标卷所做的更改复制到源卷。  
> 
> 重新同步操作会使用更新的目标卷数据覆盖源卷中任何比最常见的快照更新的数据。 UI 会向发出警告，提示这可能丢失数据。 在操作开始前，系统将提示你确认重新同步操作。  
> 
> 如果因源卷没有在灾难中幸存下来而不存在常见快照，则目标中的所有数据都将重新同步到新创建的源卷。


1. 若要重新同步复制，请选择“源”卷。 单击“存储服务”下的“复制”。 然后单击“重新同步”。  

2. 在出现提示时键入“是”，然后单击“重新同步” 。 
 
    ![重新同步复制内容](../media/azure-netapp-files/cross-region-replication-resync-replication.png)

3. 通过执行[显示复制关系的运行状况](cross-region-replication-display-health-status.md)中的步骤来监视源卷运行状况。   
    当源卷运行状况状态显示以下值时，则表示重新同步操作已完成，目标卷上所做的更改现已在源卷上捕获：   

    * 镜像状态：已进行镜像处理  
    * 传输状态：空闲  

## <a name="reestablish-source-to-destination-replication"></a>重新建立源到目标的复制

从目标到源的重新同步操作完成后，需要再次中断复制对等互连以重新建立源到目标的复制。 此外，还应重新装载源卷，以便客户端可对其进行访问。  

1. 中断复制对等互连：  
    a. 选择目标卷。 单击“存储服务”下的“复制”。  
    b. 继续操作前，请检查以下字段：   
    * 确保镜像状态显示为“已进行镜像处理”。   
    如果镜像状态显示为“未初始化”，请不要尝试中断复制对等互连。  
    * 确保关系状态显示为“空闲”。   
    如果关系状态显示“正在传输”，请不要尝试中断复制对等互连。    

        请参阅[显示复制关系的运行状况](cross-region-replication-display-health-status.md)。 

    c. 单击“中断对等互连”。   
    d. 在出现提示时键入“是”，然后单击“中断”按钮 。  

2. 将源卷与目标卷重新同步：  
    a. 选择目标卷。 单击“存储服务”下的“复制”。 然后单击“重新同步”。   
    b. 在出现提示时键入“是”，然后单击“重新同步”按钮 。

3. 请按照[为 Windows 或 Linux 虚拟机装载或卸载卷](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)中的步骤重新装载源卷。  
    此步骤使客户端能够访问源卷。

## <a name="next-steps"></a>后续步骤  

* [跨区域复制](cross-region-replication-introduction.md)
* [使用跨区域复制的要求和注意事项](cross-region-replication-requirements-considerations.md)
* [显示复制关系的运行状况](cross-region-replication-display-health-status.md)
* [重设跨区域复制目标卷的大小](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-a-cross-region-replication-destination-volume)
* [卷复制指标](azure-netapp-files-metrics.md#replication)
* [删除卷复制或卷](cross-region-replication-delete.md)
* [跨区域复制故障排除](troubleshoot-cross-region-replication.md)

