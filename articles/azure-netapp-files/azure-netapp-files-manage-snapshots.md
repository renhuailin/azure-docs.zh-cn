---
title: 使用 Azure NetApp 文件创建按需快照 |Microsoft Docs
description: 介绍如何使用 Azure NetApp 文件创建按需快照。
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
ms.date: 08/12/2021
ms.author: b-juche
ms.openlocfilehash: 2a17ed67830dfcfc3f1c5c3cbd6cc06ceedc8028
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128614175"
---
# <a name="create-an-on-demand-snapshot-for-a-volume"></a>为卷创建按需快照

Azure NetApp 文件支持创建按需[快照](snapshots-introduction.md)和使用快照策略来计划自动创建快照。 还可以[将快照还原到新卷](snapshots-restore-new-volume.md)、[使用客户端还原单个文件](snapshots-restore-file-client.md)，或者[使用快照还原现有的卷](snapshots-revert-volume.md)。 本文介绍如何为卷创建按需快照。 

> [!NOTE] 
> 有关跨区域复制中快照管理的注意事项，请参阅[使用跨区域复制的要求和注意事项](cross-region-replication-requirements-considerations.md)。
 
## <a name="steps"></a>步骤

1.  前往要为其创建快照的卷。 单击“快照”。

    ![显示如何导航到快照边栏选项卡的屏幕截图。](../media/azure-netapp-files/azure-netapp-files-navigate-to-snapshots.png)

2.  单击“+ 添加快照”，为卷创建按需快照。

    ![显示如何添加快照的屏幕截图。](../media/azure-netapp-files/azure-netapp-files-add-snapshot.png)

3.  在“新建快照”窗口中，为要创建的新快照提供名称。   

    ![显示“新建快照”窗口的屏幕截图。](../media/azure-netapp-files/azure-netapp-files-new-snapshot.png)

4. 单击 **“确定”** 。 

## <a name="next-steps"></a>后续步骤

* [详细了解快照](snapshots-introduction.md)
* [使用 Azure NetApp 文件管理快照策略](snapshots-manage-policy.md)
* [Azure NetApp 文件的资源限制](azure-netapp-files-resource-limits.md)
* [Azure NetApp 文件快照 101 视频](https://www.youtube.com/watch?v=uxbTXhtXCkw&feature=youtu.be)
* [什么是 Azure 应用程序一致性快照工具](azacsnap-introduction.md)
