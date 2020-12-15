---
title: 排查 Azure NetApp 文件的容量池问题 |Microsoft Docs
description: 介绍管理容量池时可能遇到的潜在问题，并提供问题的解决方案。
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
ms.topic: troubleshooting
ms.date: 11/06/2020
ms.author: b-juche
ms.openlocfilehash: a07898692c8af70fab0d2634ed1513f0f3666a4d
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/15/2020
ms.locfileid: "97505454"
---
# <a name="troubleshoot-capacity-pool-issues"></a>排查容量池问题

本文介绍管理容量池（包括池更改操作）时可能遇到的问题的解决方法。 

## <a name="issues-managing-a-capacity-pool"></a>管理容量池时的问题 

|     添加状态    |     解决方法    |
|-|-|
| 创建容量池时出现问题 |  请确保容量池计数不超过限制。 请参阅 [Azure NetApp 文件的资源限制](azure-netapp-files-resource-limits.md)。  如果计数低于限制并且仍遇到问题，请提交支持票证并指定容量池名称。 |
| 删除容量池时出现问题  |  请确保在尝试删除容量池的订阅中删除所有 Azure NetApp 文件的卷和快照。 <br> 如果已删除所有卷和快照并且仍无法删除容量池，则对资源的引用可能仍然存在，而不会在门户中显示。 在这种情况下，请提交支持票证，并指定已执行上述建议步骤。 |
| 卷创建或修改失败，出现 `Requested throughput not available` 错误 | 卷的可用吞吐量取决于容量池的大小和服务级别。 如果没有足够的吞吐量，则应增加池大小或调整现有的卷吞吐量。 | 

## <a name="issues-when-changing-the-capacity-pool-of-a-volume"></a>更改卷的容量池时遇到的问题 

> [!IMPORTANT] 
> [动态更改卷](dynamic-change-volume-service-level.md)公共预览注册的服务级别，直到进一步通知。

|     添加状态    |     解决方法    |
|-|-|
| 不允许更改卷的容量池。 | 你可能尚未获得使用此功能的授权。 <br> 将卷移动到另一个容量池的功能当前处于预览阶段。 如果是首次使用此功能，则需要先注册该功能，然后再设置 `-FeatureName ANFTierChange` 。 请参阅 [动态更改卷的服务级别](dynamic-change-volume-service-level.md)中的注册步骤。 |
| 容量池大小太小，无法限制总的卷大小。 |  此错误是由于目标容量池没有要移动的卷的可用容量而导致的。  <br> 增加目标池的大小，或选择更大的另一个池。  请参阅 [调整容量池或卷的大小](azure-netapp-files-resize-capacity-pools-or-volumes.md)。   |
|  池更改无法完成，因为 `'{source pool name}'` 目标池中已存在一个名为的卷 `'{target pool name}'` | 发生此错误的原因是目标容量池中已存在具有相同名称的卷。  请选择没有相同名称的卷的另一个容量池。   | 

## <a name="next-steps"></a>后续步骤  

* [设置容量池](azure-netapp-files-set-up-capacity-pool.md)
* [管理手动 QoS 容量池](manage-manual-qos-capacity-pool.md)
* [动态更改卷的服务级别](dynamic-change-volume-service-level.md)
* [重设容量池或卷的大小](azure-netapp-files-resize-capacity-pools-or-volumes.md)
