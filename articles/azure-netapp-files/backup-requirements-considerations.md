---
title: Azure NetApp 文件备份的要求和注意事项 | Microsoft Docs
description: 介绍在使用 Azure NetApp 文件备份之前需要了解的要求和注意事项。
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
ms.topic: conceptual
ms.date: 10/07/2021
ms.author: b-juche
ms.openlocfilehash: f396904b3832403337a6fa4553e2ae4a4b8c86b4
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2021
ms.locfileid: "129667946"
---
# <a name="requirements-and-considerations-for-azure-netapp-files-backup"></a>Azure NetApp 文件备份的要求和注意事项 

本文介绍在使用 Azure NetApp 文件备份之前需要了解的要求和注意事项。

## <a name="requirements-and-considerations"></a>要求和注意事项

在使用 Azure NetApp 文件备份之前，需要了解几个要求和注意事项： 

* 与 Azure NetApp 文件订阅关联的区域中可以使用 Azure NetApp 文件备份。 某一区域中的 Azure NetApp 文件备份只能保护位于同一区域中的 Azure NetApp 文件卷。 例如，由美国西部 2 中的服务为位于美国西部 2 的卷创建的备份将被发送到同样位于美国西部 2 的 Azure 存储。 Azure NetApp 文件不支持以其他区域为目标的备份或备份复制。  

* 备份实际完成后，备份的显示可能会有最长 5 分钟的延迟。

* 目前，Azure NetApp 文件备份功能支持将关联快照策略创建的每日、每周和每月本地快照备份到 Azure 存储。 当前不支持每小时备份。

* Azure NetApp 文件备份使用[区域冗余存储](../storage/common/storage-redundancy.md#redundancy-in-the-primary-region) (ZRS) 帐户，该帐户在该区域的三个 Azure 可用区之间同步复制数据，但下面列出的仅支持[本地冗余存储](../storage/common/storage-redundancy.md#redundancy-in-the-primary-region) (LRS) 存储的区域除外：   

    * 美国西部   

    LRS 可以从服务器机架和驱动器故障中恢复。 但是，如果数据中心内发生火灾或洪水等灾难，使用 LRS 的存储帐户的所有副本可能会丢失或无法恢复。 

* 使用基于策略的（已计划）Azure NetApp 文件备份需要配置并启用快照策略。 请参阅[使用 Azure NetApp 文件管理快照](azure-netapp-files-manage-snapshots.md)。   
    需要备份的卷要求有一个已配置的快照策略来创建快照。 配置的备份数量存储在 Azure 存储中。 

* 如果出现问题（例如，卷上没有足够的空间）并导致快照策略停止创建新快照，则备份功能将没有任何新快照要备份。 

* 在跨区域复制设置中，只能在源卷上配置 Azure NetApp 文件备份。 不支持在跨区域复制目标卷上进行此配置。

## <a name="next-steps"></a>后续步骤

* [了解 Azure NetApp 文件备份](backup-introduction.md)
* [Azure NetApp 文件的资源限制](azure-netapp-files-resource-limits.md)
* [配置基于策略的备份](backup-configure-policy-based.md)
* [配置手动备份](backup-configure-manual.md)
* [管理备份策略](backup-manage-policies.md)
* [搜索备份](backup-search.md)
* [将备份还原到新卷](backup-restore-new-volume.md)
* [禁用卷的备份功能](backup-disable.md)
* [删除卷的备份](backup-delete.md)
* [卷备份指标](azure-netapp-files-metrics.md#volume-backup-metrics)
* [Azure NetApp 文件备份常见问题解答](azure-netapp-files-faqs.md#azure-netapp-files-backup-faqs)
* [Azure NetApp 文件快照的工作原理](snapshots-introduction.md)
