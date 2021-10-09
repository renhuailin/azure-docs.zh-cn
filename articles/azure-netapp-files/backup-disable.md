---
title: 禁用 Azure NetApp 文件卷的备份功能 | Microsoft Docs
description: 描述如何禁用不再需要备份保护的卷的备份功能。
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
ms.openlocfilehash: 1254a9884063fce100152f370a434c55ec2ae207
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2021
ms.locfileid: "129094868"
---
# <a name="disable-backup-functionality-for-a-volume"></a>禁用卷的备份功能 

如果不再需要备份保护，则可以禁用卷的备份功能。 

> [!IMPORTANT]
> 禁用卷的备份会删除存储在该卷的 Azure 存储中的所有备份。

如果删除了某个卷，但在删除该卷之前未禁用备份策略，则与该卷相关的所有备份将保留在 Azure 存储中，并且列在关联的 NetApp 帐户下。 

## <a name="steps"></a>步骤

1. 选择“卷”。
2. 选择要禁用其备份功能的特定卷。
3. 选择“配置” 。
4. 在“配置备份”页中，将“启用”设置切换为“关闭”。 输入要确认的卷名称，并单击“确定”。

    ![屏幕截图显示“通过配置备份进行还原”窗口，并禁用备份。](../media/azure-netapp-files/backup-configure-backups-disable.png)

## <a name="next-steps"></a>后续步骤  

* [了解 Azure NetApp 文件备份](backup-introduction.md)
* [Azure NetApp 文件备份的要求和注意事项](backup-requirements-considerations.md)
* [Azure NetApp 文件的资源限制](azure-netapp-files-resource-limits.md)
* [配置基于策略的备份](backup-configure-policy-based.md)
* [配置手动备份](backup-configure-manual.md)
* [管理备份策略](backup-manage-policies.md)
* [搜索备份](backup-search.md)
* [将备份还原到新卷](backup-restore-new-volume.md)
* [删除卷的备份](backup-delete.md)
* [卷备份指标](azure-netapp-files-metrics.md#volume-backup-metrics)
* [Azure NetApp 文件备份常见问题解答](azure-netapp-files-faqs.md#azure-netapp-files-backup-faqs)

