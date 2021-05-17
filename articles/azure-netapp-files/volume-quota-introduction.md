---
title: 了解 Azure NetApp 文件的卷配额 | Microsoft Docs
description: 概述卷配额， 还提供有关监视和管理卷和池容量的参考。
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
ms.openlocfilehash: d648630e02cbf8c1e7c771f77a3d2342fd9731f7
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2021
ms.locfileid: "108293944"
---
# <a name="understand-volume-quota"></a>了解卷配额

本文概述了 Azure NetApp 文件的卷配额， 还提供了详细的参考信息，有助于监视和管理卷或容量池的容量。  

## <a name="behaviors-of-volume-quota"></a>卷配额行为 

* Azure NetApp 文件卷的存储容量限制为卷集大小（配额）。 

* 当卷消耗量达到最大时，卷和基础容量池都不会自动增长。 相反，该卷将收到显示“空间不足”的条件。 不过，可以根据需要[调整容量池或卷的大小](azure-netapp-files-resize-capacity-pools-or-volumes.md)。 应主动[监视卷](monitor-volume-capacity.md)和基础容量池的容量。

* 根据容量池类型，Azure NetApp 文件卷的大小（配额）会影响其带宽性能和预配容量。  有关详细信息，请参阅[自动 QoS 池类型](azure-netapp-files-understand-storage-hierarchy.md#qos_types)。 

* 卷[快照](snapshots-introduction.md)消耗的容量会计入卷的预配空间。 

* 卷配额不适用于[复制目标卷](cross-region-replication-introduction.md)。

* 有关容量消耗量的计算和超额，请参阅 [Azure NetApp 文件的成本模型](azure-netapp-files-cost-model.md)。

## <a name="next-steps"></a>后续步骤

* [Azure NetApp 文件的成本模型](azure-netapp-files-cost-model.md)
* [监视卷容量](monitor-volume-capacity.md)
* [调整容量池或卷的大小](azure-netapp-files-resize-capacity-pools-or-volumes.md)
* [容量管理常见问题解答](azure-netapp-files-faqs.md#capacity-management-faqs)