---
title: 编辑 Azure NetApp 文件的“隐藏快照路径”选项 | Microsoft Docs
description: 介绍如何使用 Azure NetApp 文件 来控制快照卷的可见性。
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
ms.openlocfilehash: 5b165bd2e4ca3ee6ac2d16bac7c4d5ff1117fdc9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699008"
---
# <a name="edit-the-hide-snapshot-path-option-of-azure-netapp-files"></a>编辑 Azure NetApp 文件的“隐藏快照路径”选项
“隐藏快照路径”选项控制卷的快照路径是否可见。 在创建 [NFS](azure-netapp-files-create-volumes.md#create-an-nfs-volume) 或 [SMB](azure-netapp-files-create-volumes-smb.md#add-an-smb-volume) 卷的过程中，可以选择指定是否隐藏快照路径。 然后，可以根据需要编辑“隐藏快照路径”选项。  

> [!NOTE]
> 对于跨区域复制中的[目标卷](cross-region-replication-create-peering.md#create-the-data-replication-volume-the-destination-volume)，默认情况下启用“隐藏快照路径”选项，并且不能修改此设置。 

## <a name="steps"></a>步骤

1. 若要查看卷的“隐藏快照路径”选项设置，请选择该卷。 “隐藏快照路径”字段显示是否启用了该选项。   
    ![描述“隐藏快照路径”字段的屏幕截图。](../media/azure-netapp-files/hide-snapshot-path-field.png) 
2. 若要编辑“隐藏快照路径”选项，请在卷页上单击“编辑”，并根据需要修改“隐藏快照路径”选项。   
    ![描述“编辑卷快照”选项的屏幕截图。](../media/azure-netapp-files/volume-edit-snapshot-options.png) 

## <a name="next-steps"></a>后续步骤

* [详细了解快照](snapshots-introduction.md)