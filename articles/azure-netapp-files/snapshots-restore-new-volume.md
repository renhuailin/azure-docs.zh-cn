---
title: 使用 Azure NetApp 文件将快照还原到新卷 | Microsoft Docs
description: 介绍如何使用 Azure NetApp 文件从快照创建新卷。
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
ms.date: 09/16/2021
ms.author: b-juche
ms.openlocfilehash: 4c3f65d61cac7f6bafd6d483383c3f85bb3e2516
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699002"
---
# <a name="restore-a-snapshot-to-a-new-volume-using-azure-netapp-files"></a>使用 Azure NetApp 文件将快照还原到新卷

[快照](snapshots-introduction.md)支持卷的时间点还原。 目前，只能将快照还原到新卷。 

## <a name="steps"></a>步骤

1. 从“卷”边栏选项卡选择“快照”以显示快照列表。 
2. 右键单击要还原的快照，然后从菜单选项中选择“还原到新卷”。  

    ![显示“还原新卷”菜单的屏幕截图。](../media/azure-netapp-files/azure-netapp-files-snapshot-restore-to-new-volume.png)

3. 在“创建卷”窗口中提供新卷的信息：  
    * **名称**   
        指定要创建的卷的名称。  
        
        名称在资源组中必须唯一。 它的长度必须至少为三个字符。  它可以使用任何字母数字字符。

    * **配额**  
        指定需要分配给卷的逻辑存储量。  

    ![显示“创建卷”窗口的屏幕截图。](../media/azure-netapp-files/snapshot-restore-new-volume.png) 

4. 单击“查看 + 创建”。  单击“创建”。   
    新卷使用的协议与快照使用的协议相同。   
    快照还原到的新卷将显示在“卷”边栏选项卡中。

## <a name="next-steps"></a>后续步骤

* [详细了解快照](snapshots-introduction.md)
* [监视卷和快照指标](azure-netapp-files-metrics.md#volumes)
* [快照策略问题故障排除](troubleshoot-snapshot-policies.md)
* [Azure NetApp 文件的资源限制](azure-netapp-files-resource-limits.md)
* [Azure NetApp 文件快照 101 视频](https://www.youtube.com/watch?v=uxbTXhtXCkw)
* [Azure NetApp 文件快照概述](https://anfcommunity.com/2021/01/31/azure-netapp-files-snapshot-overview/)