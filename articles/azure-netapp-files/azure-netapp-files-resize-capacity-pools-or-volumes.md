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
ms.date: 04/30/2021
ms.author: b-juche
ms.openlocfilehash: fcba2424c457d97a45096af9a35b5f2afa4086ba
ms.sourcegitcommit: f3f2ec7793ebeee19bd9ffc3004725fb33eb4b3f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2021
ms.locfileid: "129407240"
---
# <a name="resize-a-capacity-pool-or-a-volume"></a>重设容量池或卷的大小
如有必要，你可以根据需要更改容量池或卷的大小，例如，当卷或容量池已满时。 

有关监视卷容量的信息，请参阅[监视卷的容量](monitor-volume-capacity.md)。

## <a name="resize-the-capacity-pool-using-the-azure-portal"></a>使用 Azure 门户重设容量池大小 

可以以 1-TiB 递增或递减的方式更改容量池大小。 但是，容量池大小不能小于 4 TiB。 重设容量池大小会更改购买的 Azure NetApp 文件容量。

1. 从“NetApp 帐户”视图转到“容量池”，然后单击要重设大小的容量池。
2. 右键单击容量池名称，或单击容量池所在行末尾的“…”图标以显示上下文菜单。 单击“重设大小”。 

    ![显示上下文菜单的屏幕截图。](../media/azure-netapp-files/resize-pool-context-menu.png)  

3. 在“重设池大小”窗口中，指定池的大小。  单击 **“确定”** 。

    ![显示“重设池大小”窗口的屏幕截图。](../media/azure-netapp-files/resize-pool-window.png) 

## <a name="resize-a-volume-using-the-azure-portal"></a>使用 Azure 门户重设卷的大小

可以根据需要更改卷大小。 卷的容量消耗是依据其池的预配容量计数的。

1. 从“NetApp 帐户”视图转到“卷”，然后单击要重设大小的卷。
2. 右键单击卷名，或单击卷行末尾的“…”图标以显示上下文菜单。 单击“重设大小”。

    ![显示卷上下文菜单的屏幕截图。](../media/azure-netapp-files/resize-volume-context-menu.png) 
    
3. 在“更新卷配额”窗口中，指定卷的配额。 单击 **“确定”** 。   

    ![显示“更新卷配额”窗口的屏幕截图。](../media/azure-netapp-files/resize-volume-quota-window.png) 

## <a name="resizing-the-capacity-pool-or-a-volume-using-azure-cli"></a>使用 Azure CLI 重设容量池或卷的大小  

可使用 [Azure 命令行 (CLI) 工具](azure-netapp-files-sdk-cli.md)的以下命令来重设容量池或卷的大小：

* [`az netappfiles pool`](/cli/azure/netappfiles/pool?preserve-view=true&view=azure-cli-latest)
* [`az netappfiles volume`](/cli/azure/netappfiles/volume?preserve-view=true&view=azure-cli-latest)

## <a name="resizing-the-capacity-pool-or-a-volume-using-rest-api"></a>使用 REST API 重设容量池或卷的大小

你可以生成自动化来处理容量池和卷大小更改。   

请参阅 [Azure NetApp 文件的 REST API](azure-netapp-files-develop-with-rest-api.md) 和[使用适用于 Azure NetApp 文件的 PowerShell REST API](develop-rest-api-powershell.md)。 

Azure NetApp 文件的 REST API 规范和示例代码可通过[资源管理器 GitHub 目录](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager/Microsoft.NetApp/stable)获得。 

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

- [创建容量池](azure-netapp-files-set-up-capacity-pool.md)
- [管理手动 QoS 容量池](manage-manual-qos-capacity-pool.md)
- [动态更改卷的服务级别](dynamic-change-volume-service-level.md) 
- [了解卷配额](volume-quota-introduction.md)
- [监视卷容量](monitor-volume-capacity.md)
- [容量管理常见问题解答](azure-netapp-files-faqs.md#capacity-management-faqs)
