---
title: 使用 Azure NetApp 文件卷跨区域复制时的要求和注意事项 | Microsoft Docs
description: 描述了使用 Azure NetApp 文件的卷跨区域复制功能的要求和注意事项。
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
ms.date: 06/04/2021
ms.author: b-juche
ms.openlocfilehash: b94374d8eaa0efac50989a388686f96d9bae0622
ms.sourcegitcommit: bd65925eb409d0c516c48494c5b97960949aee05
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/06/2021
ms.locfileid: "111537129"
---
# <a name="requirements-and-considerations-for-using-cross-region-replication"></a>使用跨区域复制的要求和注意事项 

注意有关[使用 Azure NetApp 文件的卷跨区域复制](cross-region-replication-create-peering.md)功能的要求和注意事项：  

## <a name="requirements-and-considerations"></a>要求和注意事项 

* 跨区域复制功能目前处于预览阶段。 需要提交候补名单请求，以通过 [Azure NetApp 文件跨区域复制候补名单提交页](https://aka.ms/anfcrrpreviewsignup)访问该功能。 在使用跨区域复制功能之前，请先等待来自 Azure NetApp 文件团队的官方确认电子邮件。
* Azure NetApp 文件复制仅适用于某些固定区域对。 请参阅[支持的区域对](cross-region-replication-introduction.md#supported-region-pairs)。 
* 支持将 SMB 卷与 NFS 卷一起使用。 复制 SMB 卷需要在源和目标 NetApp 帐户中连接 Active Directory。 目标 AD 连接必须有权访问 DNS 服务器或 ADDS 域控制器（可从目标区域中的已委派子网访问）。 有关详细信息，请参阅 [Active Directory 连接要求](create-active-directory-connections.md#requirements-for-active-directory-connections)。 
* 目标帐户必须位于与源卷区域不同的区域中。 你还可以在不同的区域中选择现有的 NetApp 帐户。  
* 复制目标卷为只读状态，直到[故障转移到目标区域](cross-region-replication-manage-disaster-recovery.md#fail-over-to-destination-volume)以启用目标卷进行读写。 
* Azure NetApp 文件复制当前不支持多个订阅；必须在单个订阅下执行所有复制。
* 最多可为每个区域的单个订阅中的复制设置五个卷。 你可以打开支持票证，请求增加五个复制目标卷（一个区域中的每个订阅）的默认配额。 
* 接口在源卷上反映新添加的快照时，可能出现最多五分钟的延迟。  
* 不支持级联和扇入/扇出拓扑。
* 目前不支持为从快照创建的源卷配置卷复制。
* 设置跨区域复制后，复制过程将创建 snapmirror 快照以提供源卷和目标卷之间的引用。 在为每次增量传输创建新的 snapmirror 快照时，将自动循环使用 snapmirror 快照。 在删除复制关系和卷之前，不能删除 snapmirror 快照。 
* 在复制关系处于活动或断开状态时，以及在删除复制关系后，可以删除复制关系的源卷上的手动快照。 在复制关系断开之前，不能删除目标卷的手动快照。
* 不能将跨区域复制的源卷或目标卷还原到快照。 复制关系中卷的快照还原功能将会显示为灰色。 

## <a name="next-steps"></a>后续步骤
* [创建卷复制](cross-region-replication-create-peering.md)
* [显示复制关系的运行状况](cross-region-replication-display-health-status.md)
* [管理灾难恢复](cross-region-replication-manage-disaster-recovery.md)
* [卷复制指标](azure-netapp-files-metrics.md#replication)
* [删除卷复制或卷](cross-region-replication-delete.md)
* [跨区域复制故障排除](troubleshoot-cross-region-replication.md)


