---
title: 创建 Azure NetApp 文件容量池 | Microsoft Docs
description: 介绍如何创建容量池，以便可以在其中创建卷。
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
ms.openlocfilehash: 3488b6807982f97cd5feef9b07a6dc7cbe180dcd
ms.sourcegitcommit: f3f2ec7793ebeee19bd9ffc3004725fb33eb4b3f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2021
ms.locfileid: "129407544"
---
# <a name="create-a-capacity-pool-for-azure-netapp-files"></a>创建 Azure NetApp 文件容量池

创建容量池可以在其中创建卷。  

## <a name="before-you-begin"></a>开始之前 

必须已经创建了一个 NetApp 帐户。   

[创建 NetApp 帐户](azure-netapp-files-create-netapp-account.md)

## <a name="steps"></a>步骤 

1. 转到 NetApp 帐户的管理边栏选项卡，从导航窗格中单击“容量池”。  
    
    ![导航到容量池](../media/azure-netapp-files/azure-netapp-files-navigate-to-capacity-pool.png)

2. 单击“+ 添加池”以创建一个新容量池。   
    此时将显示“新建容量池”窗口。

3. 提供新容量池的以下信息：  
   * **名称**  
     指定容量池的名称。  
     容量池名称对于每个 NetApp 帐户必须是唯一的。

   * **服务级别**   
     此字段显示容量池的目标性能。  
     指定容量池的服务级别：[超级](azure-netapp-files-service-levels.md#Ultra)、[高级](azure-netapp-files-service-levels.md#Premium)或[标准](azure-netapp-files-service-levels.md#Standard)。

    * **大小**     
     指定你购买的容量池的大小。        
     最小容量池大小是 4 TiB。 可以创建大小为 4 TiB 的倍数的池。   

   * QoS   
     指定容量池应使用“手动”还是“自动”QoS 类型。  

     请参阅[存储层次结构](azure-netapp-files-understand-storage-hierarchy.md)和[性能注意事项](azure-netapp-files-performance-considerations.md)以了解 QoS 类型。  

     > [!IMPORTANT] 
     > 将“QoS 类型”设置为“手动”的操作是永久性的。 不能将手动 QoS 容量池转换为使用自动 QoS。 但是，可以将自动 QoS 容量池转换为使用手动 QoS。 请参阅[更改容量池以使用手动 QoS](manage-manual-qos-capacity-pool.md#change-to-qos)。   

    ![新建容量池](../media/azure-netapp-files/azure-netapp-files-new-capacity-pool.png)

4. 单击“创建”。

## <a name="next-steps"></a>后续步骤 

- [存储层次结构](azure-netapp-files-understand-storage-hierarchy.md) 
- [Azure NetApp 文件的服务级别](azure-netapp-files-service-levels.md)
- [Azure NetApp 文件定价页](https://azure.microsoft.com/pricing/details/storage/netapp/)
- [管理手动 QoS 容量池](manage-manual-qos-capacity-pool.md)
- [将子网委派给 Azure NetApp 文件](azure-netapp-files-delegate-subnet.md)
