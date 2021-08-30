---
title: 为 Azure NetApp 文件创建卷复制 | Microsoft Docs
description: 介绍如何为 Azure NetApp 文件创建卷复制对等互连，以设置跨区域复制。
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
ms.date: 08/11/2021
ms.author: b-juche
ms.openlocfilehash: 94528abaf90a94c59dd97c9a4ae0d15162914bc7
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121751378"
---
# <a name="create-volume-replication-for-azure-netapp-files"></a>为 Azure NetApp 文件创建卷复制

> [!IMPORTANT]
> 跨区域复制功能目前为预览版。 需要提交候补名单请求，以通过 [Azure NetApp 文件跨区域复制候补名单提交页](https://aka.ms/anfcrrpreviewsignup)访问该功能。 在使用跨区域复制功能之前，请先等待来自 Azure NetApp 文件团队的官方确认电子邮件。

本文介绍如何通过创建复制对等互连来设置跨区域复制。 

设置复制对等互连使你能够以异步方式将数据从 Azure NetApp 文件卷（源）复制到另一 Azure NetApp 文件卷（目标）。 源卷和目标卷必须部署在不同的区域中。 目标容量池的服务级别可以与源容量池的服务级别匹配，也可以选择不同的服务级别。   

Azure NetApp 文件复制当前不支持多个订阅；必须在单个订阅下执行所有复制。

在开始之前，请确保已查看[使用跨区域复制的要求和注意事项](cross-region-replication-requirements-considerations.md)。  

## <a name="locate-the-source-volume-resource-id"></a>找到源卷资源 ID  

需要获取要复制的源卷的资源 ID。 

1. 请前往源卷，然后选择“设置”下的“属性”以显示源卷资源 ID。   
    ![找到源卷资源 ID](../media/azure-netapp-files/cross-region-replication-source-volume-resource-id.png)
 
2. 将资源 ID 复制到剪贴板。  稍后需要用到此值。

## <a name="create-the-data-replication-volume-the-destination-volume"></a>创建数据复制卷（目标卷）

需要创建要将源卷中的数据复制到其中的目标卷。  你需要在目标区域中具有一个 NetApp 帐户和容量池，然后才能创建目标卷。 

1. 目标帐户必须位于与源卷区域不同的区域中。 如有必要，请按照[创建 NetApp 帐户](azure-netapp-files-create-netapp-account.md)中的步骤在 Azure 区域中创建一个用于复制的 NetApp 帐户。   
还可以在不同的区域中选择现有的 NetApp 帐户。  

2. 如有必要，请按照[设置容量池](azure-netapp-files-set-up-capacity-pool.md)中的步骤在新创建的 NetApp 帐户中创建容量池。   

    还可以选择现有容量池来托管复制目标卷。  

    目标容量池的服务级别可以与源容量池的服务级别匹配，也可以选择不同的服务级别。

3. 按照将[子网委托给 Azure NetApp 文件](azure-netapp-files-delegate-subnet.md)中的步骤操作，在区域中委托要用于复制的子网。

4. 通过选择目标 NetApp 帐户中存储服务下的“卷”来创建数据复制卷。 然后单击“+ 添加数据复制”按钮。  

    ![添加数据复制](../media/azure-netapp-files/cross-region-replication-add-data-replication.png)
 
5. 在显示的“创建卷”页中，在“基本信息”选项卡下填写以下字段：
    * 卷名
    * 容量池
    * 卷配额
        > [!NOTE] 
        > 目标卷的卷配额（大小）应反映源卷的大小。 如果指定的大小小于源卷，则目标卷将自动调整为源卷大小。 
    * 虚拟网络 
    * 子网

    有关字段的详细信息，请参阅[创建 NFS 卷](azure-netapp-files-create-volumes.md#create-an-nfs-volume)。 

6. 在“协议”选项卡下，选择与源卷相同的协议。  
对于 NFS 协议，请确保导出策略规则满足将访问导出的远程网络中的任何主机的要求。  

7. 在“标记”选项卡下，根据需要创建键/值对。  

8. 在“复制”选项卡下，粘贴在[查找源卷资源 ID](#locate-the-source-volume-resource-id) 中获取的源卷资源 ID，然后选择所需的复制计划。 复制计划的选项包括：每 10 分钟、每小时、每日、每周和每月。  

    ![创建卷复制](../media/azure-netapp-files/cross-region-replication-create-volume-replication.png)

9. 单击“审阅并创建”，然后单击“创建”以创建数据复制卷。   

    ![审阅并创建复制](../media/azure-netapp-files/cross-region-replication-review-create-replication.png)

## <a name="authorize-replication-from-the-source-volume"></a>授权从源卷进行复制  

若要授权复制，需要获取复制目标卷的资源 ID 并将其粘贴到复制源卷的“授权”字段。 

1. 在 Azure 门户中，导航到 Azure NetApp 文件。

2. 转到复制目标卷所在的目标 NetApp 帐户和目标容量池。

3. 选择复制目标卷，转到“设置”下的“属性”，然后找到目标卷的“资源 ID”。 将目标卷资源 ID 复制到剪贴板。

    ![属性资源 ID](../media/azure-netapp-files/cross-region-replication-properties-resource-id.png) 
 
4. 在 Azure NetApp 文件中，转到复制源帐户和源容量池。 

5. 找到复制源卷并选择它。 转到存储服务下的“复制”，然后单击“授权”。

    ![授权复制](../media/azure-netapp-files/cross-region-replication-authorize.png) 

6. 在“授权”字段中，粘贴在步骤 3 中获取的目标复制卷资源 ID，然后单击“确定”。

    > [!NOTE]
    > 由于各种因素（例如目标存储在给定时间的状态），源卷的已用空间和目标卷的已用空间之间可能存在差异。 <!-- ANF-14038 --> 

## <a name="next-steps"></a>后续步骤  

* [跨区域复制](cross-region-replication-introduction.md)
* [使用跨区域复制的要求和注意事项](cross-region-replication-requirements-considerations.md)
* [显示复制关系的运行状况](cross-region-replication-display-health-status.md)
* [卷复制指标](azure-netapp-files-metrics.md#replication)
* [管理灾难恢复](cross-region-replication-manage-disaster-recovery.md)
* [删除卷复制或卷](cross-region-replication-delete.md)
* [跨区域复制故障排除](troubleshoot-cross-region-replication.md)

