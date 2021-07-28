---
title: Azure NetApp 文件的成本模型 | Microsoft Docs
description: 介绍用于管理服务中开支的 Azure NetApp 文件成本模型。
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
ms.date: 04/30/2021
ms.author: b-juche
ms.openlocfilehash: 2df332450c996c1a7b1b9b5e35b06d4fb226ed93
ms.sourcegitcommit: 89c4843ec85d1baea248e81724781d55bed86417
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2021
ms.locfileid: "108794459"
---
# <a name="cost-model-for-azure-netapp-files"></a>Azure NetApp 文件的成本模型 

了解 Azure NetApp 文件的成本模型有助于管理服务中的开支。 

有关特定于跨区域复制的成本模型，请参阅[跨区域复制的成本模型](cross-region-replication-introduction.md#cost-model-for-cross-region-replication)。

## <a name="calculation-of-capacity-consumption"></a>容量消耗量的计算

将根据预配的存储容量对 Azure NetApp 文件计费。  预配的容量是通过创建容量池分配的。  容量池以一小时为增量基于每一已预配 GiB 的费率按月计费。 单个容量池的最小大小为 4 TiB，以后可按 1 TiB 为增量扩展容量池。 卷是在容量池中创建的。  每个卷分配有一个配额，该配额会从池的预配容量中扣减。 可分配给卷的配额最小为 100 GiB，最大为 100 TiB。  

对于活动卷，从配额中扣减的容量消耗量基于活动文件系统或快照数据上的逻辑（有效）容量。 卷只能包含与集大小相当的数据（配额）。

容量池中从其预配量中扣减的已用总容量是该池中所有卷的实际消耗量的总和： 

   ![显示已用总容量的计算的表达式。](../media/azure-netapp-files/azure-netapp-files-total-used-capacity.png)

## <a name="capacity-consumption-of-snapshots"></a>快照的容量消耗 

Azure NetApp 文件中的快照容量消耗量是根据父卷的配额收费的。  因此，它的计费费率与卷所属的容量池相同。  但是，与活动卷不同，快照消耗量是根据消耗的递增容量计量的。  Azure NetApp 文件快照在本质上是基于差异的。 根据数据的更改率，快照消耗的容量通常比活动卷的逻辑容量少得多。 例如，假设你有一个 500 GiB 卷的快照，该快照只包含 10 GiB 的差异数据。 计入活动文件系统和快照的卷配额的容量消耗将是 510 GiB，而不是 1000 GiB。 一般而言，可以使用建议的 20% 容量来保留一周的快照数据（取决于快照频率和应用程序的每日块级别更改率）。 

下图演示了这些概念。 

* 假设有一个预配容量为 40 TiB 的容量池。 该池包含三个卷：    
    * 卷 1 分配有 20 TiB 配额，已消耗 13 TiB（12 TiB 活动，1 TiB 快照）。
    * 卷 2 分配有 1 TiB 配额，已消耗 450 GiB。
    * 卷 3 分配有 14 TiB 配额，已消耗 8.8 TiB（8 TiB 活动，800 GiB 快照）。   
* 容量池计量为 40 TiB 容量（预配量）。 已消耗 22.25 TiB 容量（卷 1、卷 2 和卷 3 分别消耗了 13 TiB、450 GiB 和 8.8 TiB）。 容量池的剩余容量为 17.75 TiB。   

![显示包含三个卷的容量池的图。](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-three-vols.png)

## <a name="next-steps"></a>后续步骤

* [Azure NetApp 文件定价页](https://azure.microsoft.com/pricing/details/storage/netapp/)
* [Azure NetApp 文件的服务级别](azure-netapp-files-service-levels.md)
* [Azure NetApp 文件的资源限制](azure-netapp-files-resource-limits.md)
* [跨区域复制的成本模型](cross-region-replication-introduction.md#cost-model-for-cross-region-replication)
* [了解卷配额](volume-quota-introduction.md)
* [监视卷容量](monitor-volume-capacity.md)
* [调整容量池或卷的大小](azure-netapp-files-resize-capacity-pools-or-volumes.md)
* [使用标记管理计费](manage-billing-tags.md)
* [容量管理常见问题解答](azure-netapp-files-faqs.md#capacity-management-faqs)
