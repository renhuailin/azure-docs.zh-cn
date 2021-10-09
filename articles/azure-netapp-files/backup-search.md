---
title: 搜索 Azure NetApp 文件卷的备份 | Microsoft Docs
description: 介绍如何在卷级别和 NetApp 帐户级别显示和搜索 Azure NetApp 文件卷的备份。
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
ms.openlocfilehash: 71fcf944f0664c1d3e4d07e96348d109042047a8
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2021
ms.locfileid: "129094835"
---
# <a name="search-backups-of-azure-netapp-files-volumes"></a>搜索 Azure NetApp 文件卷的备份

可以在卷级别或 NetApp 帐户级别显示备份并搜索特定备份。

备份快照时会保留用于快照的名称。 快照名称包括前缀 `daily`、`weekly` 或 `monthly`。 它们还包括创建快照时时间戳。 启用备份功能时的第一个快照称为基线快照，其名称包含前缀 `snapmirror`。

如果某个卷被删除，它的备份仍然保留。 备份将列在关联的 NetApp 帐户中的“备份”部分下面。 此列表将包括该地区订阅内（跨 NetApp 帐户）的所有备份。 此列表可用于将备份还原到同一订阅下另一个 NetApp 帐户中的卷。

## <a name="search-backups-at-volume-level"></a>在卷级别搜索备份    

可以在卷级别显示和搜索备份：

1. 选择“卷”。

2. 导航到“备份”以显示卷的备份。   
    “类型”列显示备份是按照“计划”（基于策略）还是“手动”备份方式生成的。 

3. 在“搜索备份”框中，输入要搜索的备份名称。  

    支持部分搜索；不必指定整个备份名称。 搜索根据搜索字符串筛选备份。

    ![显示卷备份列表的屏幕截图。](../media/azure-netapp-files/backup-search-volume-level.png)

## <a name="search-backups-at-netapp-account-level"></a>在 NetApp 帐户级别搜索备份 

可以在订阅区域的所有 NetApp 帐户下显示和搜索与订阅关联的备份。   

1. 选择“NetApp 帐户”。

2. 导航到“备份”。
    “类型”列显示备份是按照“计划”（基于策略）还是“手动”备份方式生成的。  

3. 在“搜索备份”框中，输入要搜索的备份名称。

    * 备份列表包括属于订阅的所有 NetApp 帐户的所有卷（包括活动卷和已删除卷）的所有备份。

    * 备份列表可用于将备份复原到新卷，也可用于删除备份。 复原备份的工作流与[卷级备份复原相同](backup-restore-new-volume.md)。 删除备份的工作流与[卷级备份删除相同](backup-delete.md)。

    * 对[手动备份](backup-configure-manual.md)命名约定建议的最佳做法可帮助你确定备份属于哪个卷。 这种做法在备份属于已删除的卷（保留了备份）的情况下特别有用。

    ![显示 NetApp 帐户的备份列表的屏幕截图。](../media/azure-netapp-files/backup-search-account-level.png)

## <a name="next-steps"></a>后续步骤  

* [了解 Azure NetApp 文件备份](backup-introduction.md)
* [Azure NetApp 文件备份的要求和注意事项](backup-requirements-considerations.md)
* [Azure NetApp 文件的资源限制](azure-netapp-files-resource-limits.md)
* [配置基于策略的备份](backup-configure-policy-based.md)
* [配置手动备份](backup-configure-manual.md)
* [管理备份策略](backup-manage-policies.md)
* [将备份还原到新卷](backup-restore-new-volume.md)
* [禁用卷的备份功能](backup-disable.md)
* [删除卷的备份](backup-delete.md)
* [卷备份指标](azure-netapp-files-metrics.md#volume-backup-metrics)
* [Azure NetApp 文件备份常见问题解答](azure-netapp-files-faqs.md#azure-netapp-files-backup-faqs)
