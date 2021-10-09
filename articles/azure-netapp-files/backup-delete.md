---
title: 删除 Azure NetApp 文件卷的备份 | Microsoft Docs
description: 介绍如何删除不再需要为卷保留的单个备份。
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
ms.openlocfilehash: c63fc37000fbd6cf376dd5a80b392e87466e9ce3
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2021
ms.locfileid: "129094827"
---
# <a name="delete-backups-of-a-volume"></a>删除卷的备份 

可以删除不再需要为卷保留的单个备份。 删除备份会删除 Azure 存储帐户中的关联对象，从而节省空间。  

无法删除最新备份。  

## <a name="steps"></a>步骤

1. 选择“卷”。
2. 导航到“备份”。
3. 从备份列表中，选择要删除的备份。 单击备份右侧的三个点 (`…`)，然后从“操作”菜单中单击“删除”。

    ![显示备份的“删除”菜单的屏幕截图。](../media/azure-netapp-files/backup-action-menu-delete.png)

## <a name="next-steps"></a>后续步骤  

* [了解 Azure NetApp 文件备份](backup-introduction.md)
* [Azure NetApp 文件备份的要求和注意事项](backup-requirements-considerations.md)
* [Azure NetApp 文件的资源限制](azure-netapp-files-resource-limits.md)
* [配置基于策略的备份](backup-configure-policy-based.md)
* [配置手动备份](backup-configure-manual.md)
* [管理备份策略](backup-manage-policies.md)
* [搜索备份](backup-search.md)
* [将备份还原到新卷](backup-restore-new-volume.md)
* [禁用卷的备份功能](backup-disable.md)
* [卷备份指标](azure-netapp-files-metrics.md#volume-backup-metrics)
* [Azure NetApp 文件备份常见问题解答](azure-netapp-files-faqs.md#azure-netapp-files-backup-faqs)
