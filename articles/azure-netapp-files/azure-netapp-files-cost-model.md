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
ms.date: 09/22/2020
ms.author: b-juche
ms.openlocfilehash: 9c4eebae6909c9ef0969bc85bcb9a985db2a7c02
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "91325600"
---
# <a name="cost-model-for-azure-netapp-files"></a>Azure NetApp 文件的成本模型 

了解 Azure NetApp 文件的成本模型有助于管理服务中的开支。 

有关特定于跨区域复制的成本模型，请参阅[跨区域复制的成本模型](cross-region-replication-introduction.md#cost-model-for-cross-region-replication)。

## <a name="calculation-of-capacity-consumption"></a>容量消耗量的计算

将根据预配的存储容量对 Azure NetApp 文件计费。  预配的容量是通过创建容量池分配的。  容量池以一小时为增量基于每一已预配 GiB 的费率按月计费。 单个容量池的最小大小为 4 TiB，以后可按 1 TiB 为增量扩展容量池。 卷是在容量池中创建的。  每个卷分配有一个配额，该配额会从池的预配容量中扣减。 可分配给卷的配额最小为 100 GiB，最大为 100 TiB。  

对于活动卷，从配额中扣减的容量消耗量基于逻辑（有效）容量。

如果卷的实际容量消耗量超过了其存储配额，该卷可继续增长。 只要实际卷大小小于系统限制 (100 TiB)，就仍允许写入。  

容量池中从其预配量中扣减的已用总容量是该池中所有卷的分配配额或实际消耗量（以二者中较大的值为准）的总和： 

   ![已用总容量的计算](../media/azure-netapp-files/azure-netapp-files-total-used-capacity.png)

下图演示了这些概念。  
* 我们有一个预配容量为 4 TiB 的容量池。  该池包含三个卷。  
    * 卷 1 分配有 2 TiB 配额，已消耗了 800 GiB。  
    * 卷 2 分配有 1 TiB 配额，已消耗了 100 GiB。  
    * 卷 3 分配有 500 GiB 配额，但已消耗 800 GiB（超额）。  
* 容量池计量为 4 TiB 容量（预配量）。  
    已消耗 3.8 TiB 容量（卷 1 和 2 分别消耗了 2 TiB 和 1 TiB，卷 3 实际消耗了 800 GiB）。 还剩余 200 GiB 容量。

   ![包含三个卷的容量池](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-three-vols.png)

## <a name="overage-in-capacity-consumption"></a>容量超额消耗  

如果池的已用总容量超过了其预配容量，仍允许数据写入。  在宽限期（一小时）过后，如果池的已用容量仍然超过其预配容量，则池大小将自动以 1 TiB 为增量递增，直到预配容量大于已用总容量。  例如，在上图中，如果卷 3 持续增长，以致实际消耗量达到 1.2 TiB，则在宽限期过后，池大小将自动调整为 5 TiB。  结果是预配的池容量 (5 TiB) 超过已用容量 (4.2 TiB)。  

尽管容量池大小会自动增长以满足卷的需求，但它不会随着卷大小的减小而自动减小。 如果你想要在卷大小减小之后缩减容量池的大小（例如，在清理卷中的数据后），需要手动减小容量池大小。

## <a name="manual-changes-of-the-pool-size"></a>手动更改池大小  

可以手动增大或减小池大小。 但存在以下限制：
* 服务的最小和最大限制  
    请参阅有关[资源限制](azure-netapp-files-resource-limits.md)的文章。
* 最初购买最少 4 TiB 容量后的缩放增量为 1 TiB
* 最小计费增量为一小时
* 不可将预配的池大小减至小于池中已用的总容量。
* 对于附带手动 QoS 的容量池，仅当大小和服务级别提供的吞吐量高于所有卷的实际分配吞吐量时，才能减小池大小。

## <a name="behavior-of-maximum-size-pool-overage"></a>最大池超额的行为   

可以创建或者调整到的容量池最大大小为 500 TiB。  当容量池中已用的总容量超过 500 TiB 时，将发生以下情况：
* 仍允许数据写入（如果卷低于系统最大限制，即 100 TiB）。
* 在一小时宽限期后，池将以 1 TiB 为增量自动调整大小，直到池的预配容量超过已用总容量。
* 不能使用超过 500 TiB 的额外已预配计费池容量来分配卷配额。 也不能使用这种容量来提高性能 QoS 限制。  
    有关性能限制和 QoS 大小调整，请参阅[服务级别](azure-netapp-files-service-levels.md)。

下图演示了这些概念：
* 我们有一个包含高级存储层的容量池，其容量为 500 TiB。 该池包含 9 个卷。
    * 卷 1 至 8 各分配有 60 TiB 配额。  已用的总容量为 480 TiB。  
        每个卷的吞吐量 QoS 限制为 3.75 GiB/秒（60 TiB * 64 MiB/秒）。  
    * 卷 9 分配有 20 TiB 配额。  
        卷 9 的吞吐量 QoS 限制为 1.25 GiB/秒（20 TiB * 64 MiB/秒）。
* 卷 9 存在超额的情况。 它的实际消耗量为 25 TiB。  
    * 在一小时宽限期后，容量池大小将调整为 505 TiB。  
        调整依据是，卷 1 至 8 的已用总容量 = 8 * 60 TiB 配额，卷 9 的实际消耗量为 25 TiB。
    * 计费容量为 505 TiB。
    * 卷 9 的卷配额不可提高（因为分配给池的总配额不能超过 500 TiB）。
    * 不可分配更高的 QoS 吞吐量（因为池的总 QoS 仍然基于 500 TiB）。

   ![包含 9 个卷的容量池](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-nine-vols.png)

## <a name="capacity-consumption-of-snapshots"></a>快照的容量消耗 

Azure NetApp 文件中的快照容量消耗量是根据父卷的配额收费的。  因此，它的计费费率与卷所属的容量池相同。  但是，与活动卷不同，快照消耗量是根据消耗的递增容量计量的。  Azure NetApp 文件快照在本质上是基于差异的。 根据数据的更改率，快照消耗的容量通常比活动卷的逻辑容量少得多。 例如，假设你有一个 500 GiB 卷的快照，该快照只包含 10 GiB 的差异数据。 根据卷配额对该快照收费的容量为 10 GiB，而不是 500 GiB。 

## <a name="next-steps"></a>后续步骤

* [Azure NetApp 文件定价页](https://azure.microsoft.com/pricing/details/storage/netapp/)
* [Azure NetApp 文件的服务级别](azure-netapp-files-service-levels.md)
* [Azure NetApp 文件的资源限制](azure-netapp-files-resource-limits.md)
* [跨区域复制的成本模型](cross-region-replication-introduction.md#cost-model-for-cross-region-replication)
