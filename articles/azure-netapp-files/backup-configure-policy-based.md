---
title: 为 Azure NetApp 文件配置基于策略的备份 | Microsoft Docs
description: 介绍如何为 Azure NetApp 文件卷配置基于策略（计划）的备份。
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
ms.openlocfilehash: acdb84621af21cc29feb3f750a5ea60cb21e3d4a
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2021
ms.locfileid: "129094815"
---
# <a name="configure-policy-based-backups-for-azure-netapp-files"></a>为 Azure NetApp 文件配置基于策略的备份 

Azure NetApp 文件备份在卷级别支持基于策略（计划）的备份和手动（按需）备份。  可以在同一卷中使用这两种类型的备份。 在配置过程中，你需要首先为 Azure NetApp 文件卷启用备份功能，然后才能进行基于策略的备份或手动备份。 

本文演示如何配置基于策略的备份。  有关手动备份配置，请参阅[配置手动备份](backup-configure-manual.md)。  

> [!IMPORTANT]
> Azure NetApp 文件备份功能目前以预览版提供。 需要通过 [Azure NetApp 文件备份公共预览版](https://aka.ms/anfbackuppreviewsignup)页提交候补名单来请求访问该功能。 等待 Azure NetApp 文件团队的官方确认电子邮件，然后即可使用 Azure NetApp 文件备份功能。

## <a name="about-policy-based-backups"></a>关于基于策略的备份  

若要使基于策略的备份功能正常工作，卷需要具有以下两种类型的策略：  

* 控制何时为卷创建快照的快照策略。
* 控制要用于备份到 Azure 存储的快照的备份策略。

快照策略处理卷上的快照创建。 备份功能使用它将快照备份到 Azure 存储。   

备份是长时间运行的操作。 系统会基于主要工作负载（具有较高优先级）计划备份并在后台运行备份。 根据所备份的卷的大小，备份可能会在后台运行数小时。 没有用于选择备份开始时间的选项。 服务基于内部计划和优化逻辑执行备份。 

分配策略会创建作为卷的当前状态的基线快照并将该快照传输到 Azure 存储。 基线快照使用以 `snapmirror` 开头的名称进行创建。 当第一个计划备份完成时（基于策略），此基线快照会自动删除。 如果将备份策略附加到卷，则在传输基线快照之前，备份列表将为空。 备份完成后，基线备份条目将显示在卷的备份列表中。 基线传输完成后，列表会基于策略每天进行更新。 空备份列表指示基线备份正在进行。 如果卷在分配备份策略之前已具有现有的手动备份，则不会创建基线快照。 仅当卷没有以前的备份时，才会创建基线快照。

## <a name="configure-and-apply-a-snapshot-policy"></a>配置和应用快照策略  

需要创建快照策略并将快照策略关联到要备份的卷。 单个快照策略可以附加到多个卷。 快照策略中的更改可能会影响卷的备份功能。 

1. 登录 Azure 门户，然后导航到“Azure NetApp 文件”。    
2. 选择 Azure NetApp 文件订阅。   
3. 选择“快照策略”。   

    ![显示如何导航到“快照策略”选项的屏幕截图。](../media/azure-netapp-files/backup-navigate-snapshot-policy.png)   

4.  单击“添加快照策略”。
5.  在出现的“快照策略”页中，指定要保留的快照数以及为卷创建快照的计划。 单击“保存”。  

    目前，备份功能只能备份每日、每周和每月快照。 （不支持每小时备份）。   

    * 对于每日快照配置，指定一天中要创建快照的时间。 
    * 对于每周快照配置，指定一周中要创建快照的日期和时间。 
    * 对于每月快照配置，指定一月中要创建快照的日期和时间。 
    * 对于每个快照配置，指定要保留的快照数。

    例如，如果要进行每日备份，则必须使用每日快照计划和快照计数配置快照策略，然后将该每日快照策略应用于卷。 如果更改快照策略或删除每日快照配置，则不会创建新的每日快照，从而导致不会进行每日备份。 相同过程和行为适用于每周和每月备份。  

    确保每个快照都具有唯一的快照计划配置。 按照设计，Azure NetApp 文件会阻止删除最新备份。 如果多个快照具有相同时间（例如，相同的每日和每周计划配置），则 Azure NetApp 文件会将它们视为最新快照，并阻止删除这些备份。  

    以下示例显示每日快照策略配置： 

    ![显示每日快照策略配置的屏幕截图。](../media/azure-netapp-files/backup-daily-snapshot-policy.png)

6.  将快照策略应用于要备份的卷：  

    1. 请在“卷”页上，右键单击要向其应用快照策略的卷，然后选择“编辑”。   
        ![显示卷编辑菜单的屏幕截图。](../media/azure-netapp-files/backup-volume-edit-menu.png)   

    2. 在“编辑”窗口中的“快照策略”下，选择要应用的策略。 单击“确定”。   
        ![显示带有“快照策略”下拉菜单的“编辑”窗口的屏幕截图。](../media/azure-netapp-files/backup-volume-edit-snapshot-policy.png)    

## <a name="configure-a-backup-policy"></a>配置备份策略

备份策略使卷能够按定期计划间隔进行保护。  

需要创建备份策略并将备份策略关联到要备份的卷。 单个备份策略可以附加到多个卷。 可以通过禁用策略或在卷级别禁用备份来临时暂停备份。 还可在卷级别完全禁用备份，从而导致清理 Azure 存储中的所有关联数据。 如果备份策略附加到任何卷，则无法将其删除。

若要启用基于策略（计划）的备份，请执行以下操作： 

1. 登录 Azure 门户，然后导航到“Azure NetApp 文件”。 
2. 选择 Azure NetApp 文件订阅。
3. 选择“备份”。 

    ![显示如何导航到“备份”选项的屏幕截图。](../media/azure-netapp-files/backup-navigate.png)

4. 选择“备份策略”。
5. 选择 **添加** 。 
6. 在“备份策略”页中，指定备份策略名称。  为每日、每周和每月备份输入要保留的备份数。 单击“保存”。

    ![显示“备份策略”窗口的屏幕截图。](../media/azure-netapp-files/backup-policy-window-daily.png)

    * 如果在未附加快照策略的情况下配置备份策略并附加到卷，则备份不会正常运行。 只会将基线快照传输到 Azure 存储。 
    * 对于配置的每个备份策略（例如，每日备份），请确保具有对应的快照策略配置（例如，每日快照）。
    * 备份策略依赖于快照策略。 如果尚未创建快照策略，则可以通过在“备份策略”窗口中选中“创建快照策略”复选框来同时配置这两个策略。   

        ![显示选中了“快照策略”的“备份策略”窗口的屏幕截图。](../media/azure-netapp-files/backup-policy-snapshot-policy-option.png)
 
### <a name="example-of-a-valid-configuration"></a>有效配置的示例

以下示例配置演示如何对卷配置数据保护策略，使卷上有 5 个最新的每日快照、4 个最新的每周快照以及 3 个最新的每月快照。 此配置会导致备份 15 个最新的每日快照、6 个最新的每周快照以及 4 个最新的每月快照。

* 快照策略：   
    每日：`Number of Snapshots to Keep = 5`   
    每周：`Number of Snapshots to Keep = 4`   
    每月：`Number of Snapshots to Keep = 3`
* 备份策略：   
    每日：`Daily Backups to Keep = 15`   
    每周：`Weekly Backups to Keep = 6`   
    每月：`Monthly Backups to Keep = 4`

### <a name="example-of-an-invalid-configuration"></a>无效配置的示例

以下示例配置为每日备份配置了备份策略，但快照策略没有对应的配置。 因此，不会创建每日快照供备份策略进行备份。 此配置仅备份每周和每月快照。

* 快照策略：   
    每周：`Number of Snapshots to Keep = 4`   
    每月：`Number of Snapshots to Keep = 3`   
* 备份策略：   
    每日：`Daily Backups to Keep = 15`   
    每周：`Weekly Backups to Keep = 6`   
    每月：`Monthly Backups to Keep = 4`   

## <a name="enable-backup-functionality-for-a-volume-and-assign-a-backup-policy"></a>为卷启用备份功能并分配备份策略

在进行任何备份（基于策略的备份或手动备份）之前，每个 Azure NetApp 文件卷都必须启用备份功能。 

启用备份功能后，需要将备份策略分配给卷，以便基于策略的备份生效。 （对于手动备份，备份策略是可选的。）

若要为卷启用备份功能，请执行以下操作：  

1. 转到“卷”，选择要启用备份的卷。
2. 选择“配置” 。
3. 在“配置备份”页中，将“启用”设置切换为“打开”。
4. 在“快照策略”下拉菜单中，分配要用于卷的快照策略。 
5. 在“备份策略”下拉菜单中，分配要用于卷的备份策略。 单击“确定”。

    保管库信息已预先填充。  

    ![显示“配置备份”窗口的屏幕截图。](../media/azure-netapp-files/backup-configure-window.png)


## <a name="next-steps"></a>后续步骤  

* [了解 Azure NetApp 文件备份](backup-introduction.md)
* [Azure NetApp 文件备份的要求和注意事项](backup-requirements-considerations.md)
* [Azure NetApp 文件的资源限制](azure-netapp-files-resource-limits.md)
* [配置手动备份](backup-configure-manual.md)
* [管理备份策略](backup-manage-policies.md)
* [搜索备份](backup-search.md)
* [将备份还原到新卷](backup-restore-new-volume.md)
* [禁用卷的备份功能](backup-disable.md)
* [删除卷的备份](backup-delete.md)
* [卷备份指标](azure-netapp-files-metrics.md#volume-backup-metrics)
* [Azure NetApp 文件备份常见问题解答](azure-netapp-files-faqs.md#azure-netapp-files-backup-faqs)


