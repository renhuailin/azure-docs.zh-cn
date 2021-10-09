---
title: 在 Azure NetApp 文件中管理快照策略 | Microsoft Docs
description: 介绍如何使用 Azure NetApp 文件来创建、管理、修改和删除快照策略。
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
ms.openlocfilehash: e4ccbd68ead83f682f39359c053e5a608d34903c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699005"
---
# <a name="manage-snapshot-policies-in-azure-netapp-files"></a>在 Azure NetApp 文件中管理快照策略

[快照](snapshots-introduction.md)支持卷的时间点还原。 可以使用快照策略来计划自动执行[卷快照](snapshots-introduction.md)。 还可以根据需要修改快照策略，或者删除不再需要的快照策略。  

## <a name="create-a-snapshot-policy"></a>创建快照策略 

使用快照策略可按每小时、每天、每周或每月来指定快照创建频率。 还需要为卷指定要保留的最大快照数。  

1.  在“NetApp 帐户”视图中，单击“快照策略”。

    ![显示如何导航到快照策略的屏幕截图。](../media/azure-netapp-files/snapshot-policy-navigation.png)

2.  在“快照策略”窗口中，将“策略状态”设置为“已启用”。 

3.  单击“每小时”、“每日”、“每周”或“每月”选项卡 ，创建每小时、每日、每周或每月的快照策略。 指定“要保留的快照数”。  

    请参阅 [Azure NetApp 文件的资源限制](azure-netapp-files-resource-limits.md)，了解允许为卷设置的最大快照数。 

    以下示例显示每小时快照策略配置。 

    ![显示每小时快照策略的屏幕截图。](../media/azure-netapp-files/snapshot-policy-hourly.png)

    以下示例显示每日快照策略配置。

    ![显示每日快照策略的屏幕截图。](../media/azure-netapp-files/snapshot-policy-daily.png)

    以下示例显示每周快照策略配置。

    ![显示每周快照策略的屏幕截图。](../media/azure-netapp-files/snapshot-policy-weekly.png)

    以下示例显示每月快照策略配置。

    ![显示每月快照策略的屏幕截图。](../media/azure-netapp-files/snapshot-policy-monthly.png) 

4.  单击“保存”  。  

如果需要创建其他快照策略，请重复步骤 3。
创建的策略将显示在快照策略页中。

如果希望卷使用快照策略，需要[将策略应用到卷](snapshots-manage-policy.md#apply-a-snapshot-policy-to-a-volume)。 

## <a name="apply-a-snapshot-policy-to-a-volume"></a>将快照策略应用到卷

如果希望卷使用创建的快照策略，需要将策略应用到卷。 

不能在跨区域复制中将快照策略应用于目标卷。  

1.  请在“卷”页上，右键单击要向其应用快照策略的卷，然后选择“编辑”。

    ![显示“卷”的右键单击菜单的屏幕截图。](../media/azure-netapp-files/volume-right-cick-menu.png) 

2.  在“编辑”窗口中的“快照策略”下，选择要用于卷的策略。  单击“确定”以应用策略。  

    ![显示“快照策略”菜单的屏幕截图。](../media/azure-netapp-files/snapshot-policy-edit.png) 

## <a name="modify-a-snapshot-policy"></a>修改快照策略 

可以修改现有的快照策略，可更改策略状态、快照频率（每小时、每天、每周或每月）或要保留的快照数。  
 
1.  在“NetApp 帐户”视图中，单击“快照策略”。

2.  右键单击要修改的快照策略，然后选择“编辑”。

    ![显示“快照策略”的右键单击菜单的屏幕截图。](../media/azure-netapp-files/snapshot-policy-right-click-menu.png) 

3.  在显示的“快照策略”窗口中进行更改，然后单击“保存”。 

## <a name="delete-a-snapshot-policy"></a>删除快照策略 

可以删除不再想保留的快照策略。   

1.  在“NetApp 帐户”视图中，单击“快照策略”。

2.  右键单击要修改的快照策略，然后选择“删除”。

    ![显示“删除”菜单项的屏幕截图。](../media/azure-netapp-files/snapshot-policy-right-click-menu.png) 

3.  单击“是”以确认要删除快照策略。   

    ![显示快照策略删除确认的屏幕截图。](../media/azure-netapp-files/snapshot-policy-delete-confirm.png) 

## <a name="next-steps"></a>后续步骤

* [快照策略问题故障排除](troubleshoot-snapshot-policies.md)
* [Azure NetApp 文件的资源限制](azure-netapp-files-resource-limits.md)
* [详细了解快照](snapshots-introduction.md)