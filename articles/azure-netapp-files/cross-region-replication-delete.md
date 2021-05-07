---
title: 删除 Azure NetApp 文件跨区域复制的卷复制或卷 |Microsoft Docs
description: 描述如何删除源卷和目标卷之间不再需要的复制连接。
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
ms.date: 11/18/2020
ms.author: b-juche
ms.openlocfilehash: 5ce7a591acd8203775808457219b0ec392cd696e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "95249881"
---
# <a name="delete-volume-replications-or-volumes"></a>删除卷复制或卷

本文介绍了如何删除卷复制。 还介绍了如何删除源卷或目标卷。

## <a name="delete-volume-replications"></a>删除卷复制

你可以通过删除卷复制来终止源卷和目标卷之间的复制连接。 必须从目标卷中删除复制。 删除操作仅删除复制的授权；它不会删除源或目标卷。 

1. 删除卷复制之前，请确保复制对等互连已断开。 中断复制对等互连： 

    1. 选择 *目标* 卷。 在 "存储服务" 下单击 " **复制** "。  

    2.  在执行后续操作之前，请检查以下字段:  
        * 确保镜像状态显示为 "已 ***镜像***"。   
            如果镜像状态显示为 *未初始化*，请勿尝试中断复制对等互连。
        * 确保关系状态显示为 " ***空闲***"。   
            如果关系状态显示 " *正在传输*"，请勿尝试中断复制对等互连。   

        参见[显示复制关系的健康状况](cross-region-replication-display-health-status.md)。 

    3.  单击 " **中断对等互连**"。  

    4.  在出现提示时键入 **Yes**，然后单击 "**中断**" 。 

        ![中断复制对等互连](../media/azure-netapp-files/cross-region-replication-break-replication-peering.png)


1. 若要删除卷复制，请从源卷或目标卷中选择 " **复制** "。  

2. 单击 **“删除”** 。    

3. 键入 **Yes** 并单击“**删除**”，确认删除。   

    ![删除复制](../media/azure-netapp-files/cross-region-replication-delete-replication.png)

## <a name="delete-source-or-destination-volumes"></a>删除源卷或目标卷

如果要删除源卷或目标卷，则必须按所述顺序执行以下步骤。 否则，将会 `Volume with replication cannot be deleted` 出错。  

1. 从目标卷 [删除卷复制](#delete-volume-replications)。   

2. 右键单击卷名称，然后选择 " **删除**"，根据需要删除目标卷或源卷。   

    ![显示右键单击卷菜单的屏幕截图。](../media/azure-netapp-files/cross-region-replication-delete-volume.png)

## <a name="next-steps"></a>后续步骤  

* [跨区域复制](cross-region-replication-introduction.md)
* [跨区域复制的要求和注意事项](cross-region-replication-requirements-considerations.md)
* [显示复制关系的运行状况](cross-region-replication-display-health-status.md)
* [跨区域复制故障排除](troubleshoot-cross-region-replication.md)

