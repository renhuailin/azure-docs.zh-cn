---
title: 管理 Azure NetApp 文件的手动 QoS 容量池 | Microsoft Docs
description: 介绍如何管理使用手动 QoS 类型的容量池，包括如何设置手动 QoS 容量池以及如何更改容量池以使用手动 QoS。
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
ms.date: 06/14/2021
ms.author: b-juche
ms.openlocfilehash: dd92472ff810a46743ffa08b1ea2fdd8f9e01da7
ms.sourcegitcommit: f3f2ec7793ebeee19bd9ffc3004725fb33eb4b3f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2021
ms.locfileid: "129407582"
---
# <a name="manage-a-manual-qos-capacity-pool"></a>管理手动 QoS 容量池

本文介绍如何管理使用手动 QoS 类型的容量池。  

请参阅 [Azure NetApp 文件的存储层次结构](azure-netapp-files-understand-storage-hierarchy.md)和 [Azure NetApp 文件的性能注意事项](azure-netapp-files-performance-considerations.md)，以了解有关 QoS 类型的注意事项。  

## <a name="set-up-a-new-manual-qos-capacity-pool"></a>设置新的手动 QoS 容量池 

若要使用手动 QoS 类型创建新的容量池，请执行以下操作：

1. 按照[创建容量池](azure-netapp-files-set-up-capacity-pool.md)中的步骤操作。  

2. 在“新建容量池”窗口中，选择“手动 QoS”类型。  

## <a name="change-a-capacity-pool-to-use-manual-qos"></a><a name="change-to-qos"></a>更改容量池以使用手动 QoS

可以将当前使用自动 QoS 类型的容量池更改为使用手动 QoS 类型。  

> [!IMPORTANT]
> 将容量类型设置为手动 QoS 是永久性更改。 无法将手动 QoS 类型容量池转换为自动 QoS 容量池。  
> 在转换过程中，吞吐量级别可能会达到上限，以符合手动 QoS 类型的卷的吞吐量限制。 请参阅 [Azure NetApp 文件的资源限制](azure-netapp-files-resource-limits.md#resource-limits)。

1. 在 NetApp 帐户的管理边栏选项卡中，单击“容量池”以显示现有的容量池。   
 
2.  单击要更改为使用手动 QoS 的容量池。

3.  单击“更改 QoS 类型”。 然后将“新 QoS 类型”设置为“手动” 。 单击 **“确定”** 。 

![更改 QoS 类型](../media/azure-netapp-files/change-qos-type.png)


## <a name="monitor-the-throughput-of-a-manual-qos-capacity-pool"></a>监视手动 QoS 容量池的吞吐量  

指标可帮助你监视卷的读取和写入吞吐量。  请参阅 [Azure NetApp 文件的指标](azure-netapp-files-metrics.md)。  

## <a name="modify-the-allotted-throughput-of-a-manual-qos-volume"></a>修改手动 QoS 卷的已分配吞吐量 

如果卷包含在手动 QoS 容量池中，可以根据需要修改分配的卷吞吐量。

1. 在“卷”页中，选择要修改其吞吐量的卷。   

2. 单击“更改吞吐量”。 指定所需的“吞吐量 (MiB/S)”。 单击 **“确定”** 。 

    ![更改 QoS 吞吐量](../media/azure-netapp-files/change-qos-throughput.png)

## <a name="next-steps"></a>后续步骤  

* [创建容量池](azure-netapp-files-set-up-capacity-pool.md)
* [Azure NetApp 文件的指标](azure-netapp-files-metrics.md)
* [Azure NetApp 文件的性能考虑因素](azure-netapp-files-performance-considerations.md)
* [排查容量池问题](troubleshoot-capacity-pools.md)
* [Azure NetApp 文件的存储层次结构](azure-netapp-files-understand-storage-hierarchy.md)
* [Azure NetApp 文件的服务级别](azure-netapp-files-service-levels.md)
* [Azure NetApp 文件的成本模型](azure-netapp-files-cost-model.md)
* [Azure NetApp 文件的资源限制](azure-netapp-files-resource-limits.md)
* [创建 NFS 卷](azure-netapp-files-create-volumes.md)
* [创建 SMB 卷](azure-netapp-files-create-volumes-smb.md)
* [创建双协议卷](create-volumes-dual-protocol.md)
