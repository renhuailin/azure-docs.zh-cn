---
title: Azure NetApp 文件的手动 QoS 容量池 |Microsoft Docs
description: 介绍手动 QoS 容量池，并提供其他信息的参考。
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
ms.date: 06/14/2021
ms.author: b-juche
ms.openlocfilehash: 3ff9ed36800c60d50e70b328ab37606ddea09508
ms.sourcegitcommit: f3f2ec7793ebeee19bd9ffc3004725fb33eb4b3f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2021
ms.locfileid: "129407563"
---
# <a name="manual-qos-capacity-pool"></a>手动 QoS 容量池

本文介绍手动服务质量 (QoS) 容量池功能。

## <a name="how-manual-qos-differs-from-auto-qos"></a>手动 QoS 与自动 QoS 的差异

[QoS 类型](azure-netapp-files-understand-storage-hierarchy.md#qos_types)是容量池的属性。 Azure NetApp 文件提供容量池的两种 QoS 类型 - 自动（默认）和手动。  

在手动 QoS 容量池中，可以单独为卷分配容量和吞吐量。 有关最小和最大吞吐量级别，请参阅 [Azure NetApp 文件的资源限制](azure-netapp-files-resource-limits.md#resource-limits)。 使用手动 QoS 容量池创建的所有卷的总吞吐量受池总吞吐量的限制。 它由池大小和服务级别吞吐量共同决定。 

在自动 QoS 容量池中，吞吐量会自动分配到池中的卷，并与分配给这些卷的大小配额成正比。  

请参阅 [Azure NetApp 文件的存储层次结构](azure-netapp-files-understand-storage-hierarchy.md)和 [Azure NetApp 文件的性能注意事项](azure-netapp-files-performance-considerations.md)，以了解有关 QoS 类型的注意事项。

## <a name="example-of-using-manual-qos"></a>使用手动 QoS 的示例

例如，在将手动 QoS 容量池与 SAP HANA 系统、Oracle 数据库或其他需要多个卷的工作负载配合使用时，该容量池可用于创建这些应用程序卷。  每个卷都可以提供单独的大小和吞吐量，以满足应用程序的要求。  有关优点的详细信息，请参阅[手动 QoS 容量池中卷的吞吐量限制示例](azure-netapp-files-service-levels.md#throughput-limit-examples-of-volumes-in-a-manual-qos-capacity-pool)。  

## <a name="how-to-specify-the-manual-qos-type"></a>如何指定手动 QoS 类型

在[创建容量池](azure-netapp-files-set-up-capacity-pool.md)时，可以为容量池指定使用手动 QoS 类型。  还可以[将现有容量池更改为](manage-manual-qos-capacity-pool.md#change-to-qos)使用手动 QoS 类型。 

将容量类型设置为手动 QoS 是永久性的更改。 无法将手动 QoS 类型容量池转换为自动 QoS 容量池。 

## <a name="next-steps"></a>后续步骤

* [管理手动 QoS 容量池](manage-manual-qos-capacity-pool.md)
* [创建容量池](azure-netapp-files-set-up-capacity-pool.md)
* [存储层次结构](azure-netapp-files-understand-storage-hierarchy.md) 
* [Azure NetApp 文件的服务级别](azure-netapp-files-service-levels.md)
* [Azure NetApp 文件的性能考虑因素](azure-netapp-files-performance-considerations.md)
* [Azure NetApp 文件的成本模型](azure-netapp-files-cost-model.md)
* [Azure NetApp 文件的资源限制](azure-netapp-files-resource-limits.md)
* [创建 NFS 卷](azure-netapp-files-create-volumes.md)
* [创建 SMB 卷](azure-netapp-files-create-volumes-smb.md)
* [创建双协议卷](create-volumes-dual-protocol.md)
* [Azure NetApp 文件的指标](azure-netapp-files-metrics.md)
* [排查容量池问题](troubleshoot-capacity-pools.md)
