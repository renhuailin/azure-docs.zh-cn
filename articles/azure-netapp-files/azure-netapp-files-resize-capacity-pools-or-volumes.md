---
title: 为 Azure NetApp 文件重设容量池或卷的大小 | Microsoft Docs
description: 学习如何更改容量池或卷的大小。 重设容量池大小会更改购买的 Azure NetApp 文件容量。
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
ms.date: 03/10/2021
ms.author: b-juche
ms.openlocfilehash: 869f46207b940521ee0b66b5afa9c6e2718ab04f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "104594472"
---
# <a name="resize-a-capacity-pool-or-a-volume"></a>重设容量池或卷的大小
可以根据需要更改容量池或卷的大小。 

## <a name="resize-the-capacity-pool"></a>重设容量池大小 

可以以 1-TiB 递增或递减的方式更改容量池大小。 但是，容量池大小不能小于 4 TiB。 重设容量池大小会更改购买的 Azure NetApp 文件容量。

1. 在“管理 NetApp 帐户”边栏选项卡中，单击要重设大小的容量池。 
2. 右键单击容量池名称，或单击容量池所在行末尾的“...”图标以显示上下文菜单。 
3. 使用上下文菜单选项来重设容量池大小或删除容量池。

## <a name="resize-a-volume"></a>重设卷大小

可以根据需要更改卷大小。 卷的容量消耗是依据其池的预配容量计数的。

1. 在“管理 NetApp 帐户”边栏选项卡中，单击“卷”。 
2. 右键单击要重设大小的卷的名称，或单击卷所在行末尾的“...”图标以显示上下文菜单。
3. 使用上下文菜单选项来重设卷大小或删除卷。

## <a name="resize-a-cross-region-replication-destination-volume"></a>调整跨区域复制目标卷的大小 

在[跨区域复制](cross-region-replication-introduction.md)关系中，目标卷会根据源卷的大小而自动调整大小。 因此，无需单独调整目标卷的大小。 当卷处于活动的复制关系中时，或者当复制对等互连与[重新同步操作](cross-region-replication-manage-disaster-recovery.md#resync-replication)断开时，这种自动调整大小行为适用。 

下表描述了基于[镜像状态](cross-region-replication-display-health-status.md)的目标卷大小调整行为：

|  镜像状态  | 目标卷调整大小行为 |
|-|-|
| *镜像的* | 当目标卷已初始化并准备好接收镜像更新时，调整源卷的大小时会自动调整目标卷的大小。 |
| *已损坏* | 当调整源卷的大小且镜像状态“已损坏”时，目标卷会自动调整大小，同时[重新同步操作](cross-region-replication-manage-disaster-recovery.md#resync-replication)。  |
| *未初始化* | 当调整源卷的大小且镜像状态仍处于“未初始化”状态时，需要手动调整目标卷的大小。 因此，建议你等待初始化完成（也就是说，镜像状态变为“已镜像”）以调整源卷的大小。 | 

> [!IMPORTANT]
> 确保跨区域复制的源卷和目标卷的容量池中有足够的空间。 调整源卷的大小时，目标卷会自动调整大小。 但是，如果托管目标卷的容量池没有足够的空间，则同时调整源卷和目标卷的大小将会失败。

## <a name="next-steps"></a>后续步骤

- [设置容量池](azure-netapp-files-set-up-capacity-pool.md)
- [管理手动 QoS 容量池](manage-manual-qos-capacity-pool.md)
- [动态更改卷的服务级别](dynamic-change-volume-service-level.md) 