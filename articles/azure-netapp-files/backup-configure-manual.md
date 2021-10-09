---
title: 为 Azure NetApp 文件配置手动备份 | Microsoft Docs
description: 介绍如何为 Azure NetApp 文件卷配置手动备份。
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
ms.openlocfilehash: 827268fee96227f056029c8b2a75629353861d7e
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2021
ms.locfileid: "129094829"
---
# <a name="configure-manual-backups-for-azure-netapp-files"></a>为 Azure NetApp 文件配置手动备份 

Azure NetApp 文件备份在卷级别支持基于策略（计划）的备份和手动（按需）备份。  可以在同一卷中使用这两种类型的备份。 在配置过程中，你需要首先为 Azure NetApp 文件卷启用备份功能，然后才能进行基于策略的备份或手动备份。 

本文介绍如何配置手动备份。 有关基于策略的备份配置，请参阅[配置基于策略的备份](backup-configure-policy-based.md)。  

> [!IMPORTANT]
> Azure NetApp 文件备份功能目前以预览版提供。 需要通过 [Azure NetApp 文件备份公共预览版](https://aka.ms/anfbackuppreviewsignup)页提交候补名单来请求访问该功能。 等待 Azure NetApp 文件团队的官方确认电子邮件，然后即可使用 Azure NetApp 文件备份功能。

## <a name="about-manual-backups"></a>关于手动备份  

在进行任何备份（基于策略的备份或手动备份）之前，每个 Azure NetApp 文件卷都必须启用备份功能。   

启用备份功能后，可以选择手动备份卷。 手动备份会创建活动文件系统的时间点快照，并将该快照备份到 Azure 存储帐户。

以下列表汇总了手动备份的行为：  

* 即使该卷已启用备份并配置了备份策略，仍然可以在该卷上创建手动备份。  但是，该卷只能有一个未完成的手动备份请求。 如果你分配了备份策略并且基线传输仍在进行中，则在基线传输完成之前，手动备份的创建将被阻止。

* 创建手动备份会在卷上生成快照。 然后，快照将传输到 Azure 存储。 不会自动删除此快照；需要你手动删除该快照。  但是，不允许删除为最新手动备份生成的快照。  因此，在你创建后续手动备份后，可以清理（删除）为先前手动备份生成的快照（如果不需要保留的话）。 

## <a name="enable-backup-functionality"></a>启用备份功能

在创建手动备份之前请先启用卷的备份功能（如果尚未启用此功能）： 

1. 转到“卷”，选择要启用备份的特定卷。
2. 选择“配置” 。
3. 在“配置备份”页中，将“已启用”设置切换为“打开”。    
    “保管库”字段已预先填充。 
4. 单击“确定”。   

![显示“配置备份”窗口的“已启用”设置的屏幕截图。](../media/azure-netapp-files/backup-configure-enabled.png)

## <a name="create-a-manual-backup-for-a-volume"></a>为卷创建手动备份

1. 转到“卷”，选择要创建手动备份的卷。
2. 选择“添加备份”。
3. 指定备份名称。   

    * 手动备份名称的长度必须为 3 到 256 个字符。   

    * 作为最佳实践，可在实际备份名称之前添加以下格式的前缀。 这样，在卷被删除（保留备份）的情况下有助于识别手动备份。   

        `NetAppAccountName-CapacityPoolName-VolumeName`   

        例如，假设 NetApp 帐户为 `account1`，容量池为 `pool1`，卷名称为 `vol1`。 手动备份的命名方式如下：    

        `account1-pool1-vol1-backup1`   

        如果使用较短的备份名称形式，确保仍包含用于标识 NetApp 帐户、容量池和卷名称的信息以便在备份列表中显示。

4. 单击“创建”。 

    创建手动备份时，还会使用你为备份指定的相同名称在卷上创建快照。 此快照表示活动文件系统的当前状态。 此快照将传输到 Azure 存储。 备份完成后，手动备份条目将显示在卷的备份列表中。

![显示“新建备份”窗口的屏幕截图。](../media/azure-netapp-files/backup-new.png)


## <a name="next-steps"></a>后续步骤  

* [了解 Azure NetApp 文件备份](backup-introduction.md)
* [Azure NetApp 文件备份的要求和注意事项](backup-requirements-considerations.md)
* [Azure NetApp 文件的资源限制](azure-netapp-files-resource-limits.md)
* [配置基于策略的备份](backup-configure-policy-based.md)
* [管理备份策略](backup-manage-policies.md)
* [搜索备份](backup-search.md)
* [将备份还原到新卷](backup-restore-new-volume.md)
* [禁用卷的备份功能](backup-disable.md)
* [删除卷的备份](backup-delete.md)
* [卷备份指标](azure-netapp-files-metrics.md#volume-backup-metrics)
* [Azure NetApp 文件备份常见问题解答](azure-netapp-files-faqs.md#azure-netapp-files-backup-faqs)


