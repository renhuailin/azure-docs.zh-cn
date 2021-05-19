---
title: Azure NetApp 文件的性能注意事项 | Microsoft Docs
description: 了解 Azure NetApp 文件的性能，包括配额与吞吐量限制之间的关系，以及如何动态增大/减小卷配额。
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
ms.date: 02/19/2021
ms.author: b-juche
ms.openlocfilehash: f963c87148c08a4855befc5afb79d9c5ea0f4481
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "101713382"
---
# <a name="performance-considerations-for-azure-netapp-files"></a>Azure NetApp 文件的性能考虑因素

具有自动 QoS 的卷的[吞吐量限制](azure-netapp-files-service-levels.md)取决于分配给卷的配额和所选服务级别的组合。 对于具有手动 QoS 的卷，可以单独定义吞吐量限制。 当你制定有关 Azure NetApp 文件的性能计划时，需要了解几个注意事项。 

## <a name="quota-and-throughput"></a>配额和吞吐量  

吞吐量限制只是影响能够达到的实际性能的决定因素之一。  

典型的存储性能注意事项（包括读取和写入混合、传输大小、随机或顺序模式，以及许多其他因素）都会影响能够实现的总体性能。  

测试中观察到的最大实际吞吐量为 4,500 MiB/秒。  在“高级”存储层，70.31 TiB 的自动 QoS 卷配额将预配足以实现此性能级别的吞吐量限制。  

在使用自动 QoS 卷的情况下，如果考虑分配超过 70.31 TiB 的卷配额量，则可以为卷分配更多的配额来存储更多的数据。 但是，增加的配额不会导致实际吞吐量进一步增加。  

同一个实际吞吐量上限也适用于具有手动 QoS 的卷。 可分配给卷的最大吞吐量为 4,500 MiB/秒。

## <a name="automatic-qos-volume-quota-and-throughput"></a>自动 QoS 卷配额和吞吐量

本部分介绍了自动 QoS 类型的卷的配额管理和吞吐量。

### <a name="overprovisioning-the-volume-quota"></a>超量预配卷配额

如果工作负荷的性能受吞吐量限制的约束，则可以超量预配自动 QoS 卷配额，以便设置更高的吞吐量级别，实现更高的性能。  

例如，如果高级存储层中的自动 QoS 卷只有 500 GiB 的数据，但需要 128 MiB/秒的吞吐量，则可将配额设置为 2 TiB，以便相应地设置吞吐量级别（每 TB 64 MiB/秒 * 2 TiB = 128 MiB/秒）。  

如果为了获得更高的吞吐量而一直超量预配卷，请考虑改用手动 QoS 卷或改用更高的服务级别。  在上面的示例中，你可以通过改用超高性能存储层，用一半的自动 QoS 卷配额来实现相同的吞吐量限制（每 TiB 128 MiB/秒 * 1 TiB = 128 MiB/秒）。

### <a name="dynamically-increasing-or-decreasing-volume-quota"></a>动态增大或减小卷配额

如果你的性能需求是暂时性的，或者你在固定的时间段内有增加性能的需求，则可动态增大或减小卷配额以即时调整吞吐量限制。  请注意以下事项： 

* 无需暂停 IO 便可增大或减小卷配额，对卷的访问不会中断，也不会受到影响。  

    可以在 I/O 事务处于活动状态期间针对卷调整配额。  请注意，卷配额不能低于卷中存储的逻辑数据量。

* 当卷配额被更改时，吞吐量限制几乎会立即进行相应的更改。 

    更改不会中断或影响卷访问或 I/O。  

* 调整卷配额可能需要更改容量池大小。  

    可以动态调整容量池大小，而不会影响卷可用性或 I/O。

## <a name="manual-qos-volume-quota-and-throughput"></a>手动 QoS 卷配额和吞吐量 

如果使用手动 QoS 卷，则不必超量预配卷配额来实现更高的吞吐量，因为吞吐量可以单独分配给每个卷。 但是，你仍然需要确保容量池预先预配了足够的吞吐量来满足性能需求。 容量池的吞吐量根据其大小和服务级别进行预配。 有关更多详细信息，请参阅 [Azure NetApp 文件的服务级别](azure-netapp-files-service-levels.md)。


## <a name="next-steps"></a>后续步骤

- [Azure NetApp 文件性能计算器](https://cloud.netapp.com/azure-netapp-files/tco?hs_preview=tIKQbfoF-41214739590)
- [Azure NetApp 文件的服务级别](azure-netapp-files-service-levels.md)
- [Linux 性能基准](performance-benchmarks-linux.md)
