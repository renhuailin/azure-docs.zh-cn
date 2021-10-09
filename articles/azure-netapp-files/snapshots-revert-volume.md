---
title: 在 Azure NetApp 文件中使用快照还原来还原卷 | Microsoft Docs
description: 介绍如何使用 Azure NetApp 文件将卷还原到以前的状态。
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
ms.openlocfilehash: 7bf50ee981052424cf0a57f366b3e24b483eb5ad
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699136"
---
# <a name="revert-a-volume-using-snapshot-revert-with-azure-netapp-files"></a>在 Azure NetApp 文件中使用快照还原来还原卷

[快照](snapshots-introduction.md)还原功能使你能够快速将卷还原到在创建特定快照时的状态。 在大多数情况下，还原卷比将单个文件从快照还原到活动文件系统要快得多。 与将快照还原到新卷相比，此方法也更节省空间。 

可以在卷的“快照”菜单中找到“还原卷”选项。 选择用于还原的快照后，Azure NetApp 文件会将该卷还原到所选快照创建时它包含的数据和时间戳。 

> [!IMPORTANT]
> 在选定快照之后拍摄的活动文件系统数据和快照将丢失。 快照还原操作会将目标卷中的所有数据替换为所选快照中的数据。 选择快照时，应注意快照内容和创建日期。 快照还原操作无法撤消。

## <a name="steps"></a>步骤

1. 转到卷的“快照”菜单。  右键单击要用于还原操作的快照。 选择“还原卷”。 

    ![描述右键单击快照菜单的屏幕截图。](../media/azure-netapp-files/snapshot-right-click-menu.png) 

2. 在“将卷还原到快照”窗口中，键入卷的名称，然后单击“还原”。   

    卷现在还原到了所选快照的时间点。

![显示“将卷还原到快照”窗口的屏幕截图。](../media/azure-netapp-files/snapshot-revert-volume.png) 

## <a name="next-steps"></a>后续步骤

* [详细了解快照](snapshots-introduction.md)
* [Azure NetApp 文件的资源限制](azure-netapp-files-resource-limits.md)
* [Azure NetApp 文件快照 101 视频](https://www.youtube.com/watch?v=uxbTXhtXCkw)
* [Azure NetApp 文件快照概述](https://anfcommunity.com/2021/01/31/azure-netapp-files-snapshot-overview/)