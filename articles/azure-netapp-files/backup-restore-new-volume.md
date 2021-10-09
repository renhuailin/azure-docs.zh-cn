---
title: 将备份还原到新的 Azure NetApp 文件卷 | Microsoft Docs
description: 介绍如何将备份还原到新卷。
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
ms.openlocfilehash: 1a0b2e59f2d11caf1db9985cfba937e2df0f44c7
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2021
ms.locfileid: "129094839"
---
# <a name="restore-a-backup-to-a-new-volume"></a>将备份还原到新卷

还原备份会创建一个协议类型相同的新卷。 本文介绍还原操作。 

## <a name="considerations"></a>注意事项

* 在还原完成之前，无法装入由还原操作创建的新卷。 

* 仅当没有基线备份时，才应该触发还原操作。 否则，还原可能会增加备份数据的 Azure Blob 帐户上的负载。 

## <a name="steps"></a>步骤

1. 选择“卷”。 导航到“备份”。

    > [!NOTE]
    > 如果删除了某个卷，但在删除该卷之前未禁用备份策略，则与该卷相关的所有备份将保留在 Azure 存储中，并且你可以再关联的 NetApp 帐户下找到这些备份。  请参阅[在 NetApp 帐户级别搜索备份](backup-search.md#search-backups-at-netapp-account-level)。


2. 从备份列表中，选择要还原的备份。 单击备份右侧的三个点 (`…`)，然后从“操作”菜单中单击“还原为新卷”。   

    ![显示用于将备份还原到新卷的选项的屏幕截图。](../media/azure-netapp-files/backup-restore-new-volume.png)

3. 在出现的“创建卷”页中，根据情况为页中字段提供信息，然后单击“查看 + 创建”以开始将备份还原到新卷。   

    * “协议”字段是从原始卷预先填充的，无法更改。    
        但是，如果在 NetApp 帐户级别从备份列表中还原卷，则需要指定“协议”字段。 “协议”字段必须与原始卷的协议匹配。 否则，还原操作将失败并出现以下错误：  
        `Protocol Type value mismatch between input and source volume of backupId <backup-id of the selected backup>. Supported protocol type : <Protocol Type of the source volume>`

    * “配额”值必须大于或等于触发还原的备份大小（最小 100 GiB）。

    * 将备份还原到的容量池必须有足够的未使用容量来托管新还原的卷。 否则，还原操作会失败。   

    ![显示“创建卷”页的屏幕截图。](../media/azure-netapp-files/backup-restore-create-volume.png)

## <a name="next-steps"></a>后续步骤  

* [了解 Azure NetApp 文件备份](backup-introduction.md)
* [Azure NetApp 文件备份的要求和注意事项](backup-requirements-considerations.md)
* [Azure NetApp 文件的资源限制](azure-netapp-files-resource-limits.md)
* [配置基于策略的备份](backup-configure-policy-based.md)
* [配置手动备份](backup-configure-manual.md)
* [管理备份策略](backup-manage-policies.md)
* [搜索备份](backup-search.md)
* [禁用卷的备份功能](backup-disable.md)
* [删除卷的备份](backup-delete.md)
* [卷备份指标](azure-netapp-files-metrics.md#volume-backup-metrics)
* [Azure NetApp 文件备份常见问题解答](azure-netapp-files-faqs.md#azure-netapp-files-backup-faqs)



