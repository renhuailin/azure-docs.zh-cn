---
title: 转换现有 Azure NetApp 文件 SMB 卷以使用 SMB 持续可用性 | Microsoft Docs
description: 介绍如何通过转换现有 Azure NetApp 文件 SMB 卷来启用 SMB CA。
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
ms.date: 05/10/2021
ms.author: b-juche
ms.openlocfilehash: b55b7e5632ad852b199121179772fa078129cdb5
ms.sourcegitcommit: 1b19b8d303b3abe4d4d08bfde0fee441159771e1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/11/2021
ms.locfileid: "109755073"
---
# <a name="convert-existing-smb-volumes-to-use-continuous-availability"></a>转换现有 SMB 卷以使用持续可用性 

可以在[新建 SMB 卷](azure-netapp-files-create-volumes-smb.md#add-an-smb-volume)时启用 SMB 持续可用性 (CA) 功能。 还可以转换现有 SMB 卷以启用 SMB CA 功能。  本文介绍如何通过转换现有卷来启用 SMB CA。  

> [!IMPORTANT]
> 此过程包括从初始卷转换到为 CA 共享启用的新卷。 因此，应为此过程计划维护时段。 

## <a name="steps"></a>步骤

1. 确保已[注册 SMB 持续可用性共享](https://aka.ms/anfsmbcasharespreviewsignup)功能。  
2. 停止使用 SMB 卷的应用程序。 
3. [创建现有卷的按需快照](azure-netapp-files-manage-snapshots.md#create-an-on-demand-snapshot-for-a-volume)。 
4. 从现有卷选择“快照”以显示快照列表。
5. 右键单击要还原的快照，然后从菜单选项中选择“还原为新卷”。
    
    ![显示“还原为新卷”选项的快照。](../media/azure-netapp-files/azure-netapp-files-snapshot-restore-to-new-volume.png)

6. 在显示的“创建卷”窗口中，提供新卷的信息：  

    * **卷名称**    
    指定要创建的卷的名称。   
    名称在资源组中必须唯一。 它的长度必须至少为三个字符。 它可以使用任何字母数字字符。

    * **配额**   
    指定需要分配给卷的逻辑存储量。

    ![显示“创建卷”窗口的快照。](../media/azure-netapp-files/snapshot-restore-new-volume.png) 

7. 在“创建卷”窗口的“协议”部分下，确保选中“启用持续可用性”选项。

    ![显示“启用持续可用性”选项的快照。](../media/azure-netapp-files/enable-continuous-availability-option.png) 

8. 单击“查看 + 创建”。 单击“创建”。   
    新卷使用的协议与快照使用的协议相同。
   快照要还原为的新卷将显示在“卷”视图中。

9. 创建新卷后，单击所选卷边栏选项卡中的“装载说明”。 然后，按照说明装载新卷。    

10. 重新配置应用程序以使用新的卷装入点。   

11. 重启第 2 步中停止的应用程序。 

12. 应用程序开始利用新卷后，且恢复到新卷的过程完成时，即可选择删除初始卷。  

## <a name="next-steps"></a>后续步骤  

* [创建用于 Azure NetApp 文件的 SMB 卷](azure-netapp-files-create-volumes-smb.md)
* [为 Windows 或 Linux 虚拟机装载或卸载卷](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)