---
title: 管理 Azure NetApp 文件的备份策略 | Microsoft Docs
description: 介绍如何修改或暂停 Azure NetApp 文件卷的备份策略。
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
ms.date: 09/27/2021
ms.author: b-juche
ms.openlocfilehash: c3f440899e53125b6a7e6ef7986d74e4f1d0977d
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2021
ms.locfileid: "129094843"
---
# <a name="manage-backup-policies-for-azure-netapp-files"></a>管理 Azure NetApp 文件的备份策略 

需要为 Azure NetApp 文件[基于策略的备份](backup-configure-policy-based.md)提供一项快照策略配置才能自动备份卷。 使用备份策略设置 Azure NetApp 文件备份后，可以根据需要修改或暂停备份策略。  

备份策略中的更改不会影响手动备份。

## <a name="modify-a-backup-policy"></a>修改备份策略   

可以根据需要修改现有的 Azure NetApp 文件备份策略，确保 Azure NetApp 文件卷的备份覆盖范围适当。  例如，如果需要更改受服务保护的保留备份数，可以修改卷的 Azure NetApp 文件备份策略，以修改要保留的还原副本数。 

若要修改备份策略设置，请执行以下操作：   

1. 导航到“备份”。  

2. 选择“备份策略”，单击备份策略右侧的省略号图标 (`…`)，然后单击“编辑” 。

    ![显示备份策略的区分上下文菜单的屏幕截图。](../media/azure-netapp-files/backup-policies-edit.png)

3. 在“修改备份策略”窗口中，更新要为每日、每周和每月备份保留的还原副本数。 输入备份策略名称以确认操作。 单击“保存”。  

    ![显示“修改备份策略”窗口的屏幕截图。](../media/azure-netapp-files/backup-modify-policy.png)

    > [!NOTE] 
    > 备份在启用并按计划的频率生效后，你无法将备份保留计数更改为 `0`。 备份策略要求至少保留 `1` 个备份。 有关详细信息，请参阅 [Azure NetApp 文件的资源限制](azure-netapp-files-resource-limits.md)。  

## <a name="suspend-a-backup-policy"></a>暂停备份策略  

可以暂停备份策略，使它不会对关联的卷执行任何新的备份操作。 如果需要维护现有备份，但由于版本控制的原因而无法停用这些备份，则可以使用此操作来暂停备份。   

### <a name="suspend-a-backup-policy-for-all-volumes-associated-with-the-policy"></a>为与某个备份策略关联的所有卷暂停该策略

1. 导航到“备份”。

2. 选择“备份策略”，单击备份策略右侧的省略号图标 (`…`)，然后单击“编辑” 。 

3. 将“策略状态”切换为“已禁用”，输入策略名称以确认，然后单击“保存”  。 

    ![显示“修改备份策略”窗口的屏幕截图，其中的“策略状态”为“已禁用”。](../media/azure-netapp-files/backup-modify-policy-disabled.png)

### <a name="suspend-a-backup-policy-for-a-specific-volume"></a>暂停特定卷的备份策略 

1. 转到“卷”。 
2. 选择要暂停其备份的特定卷。
3. 选择“配置” 。
4. 在“配置备份”页上，将“策略状态”切换为“暂停”，输入卷名称以确认，然后单击“确定”  。   

    ![显示“配置备份”窗口的屏幕截图，其中的“策略状态”为“暂停”。](../media/azure-netapp-files/backup-modify-policy-suspend.png)

## <a name="next-steps"></a>后续步骤  

* [了解 Azure NetApp 文件备份](backup-introduction.md)
* [Azure NetApp 文件备份的要求和注意事项](backup-requirements-considerations.md)
* [Azure NetApp 文件的资源限制](azure-netapp-files-resource-limits.md)
* [配置基于策略的备份](backup-configure-policy-based.md)
* [配置手动备份](backup-configure-manual.md)
* [搜索备份](backup-search.md)
* [将备份还原到新卷](backup-restore-new-volume.md)
* [禁用卷的备份功能](backup-disable.md)
* [删除卷的备份](backup-delete.md)
* [卷备份指标](azure-netapp-files-metrics.md#volume-backup-metrics)
* [Azure NetApp 文件备份常见问题解答](azure-netapp-files-faqs.md#azure-netapp-files-backup-faqs)



