---
title: 排除 Azure NetApp 文件的容量池故障 | Microsoft Docs
description: 介绍了管理容量池时可能出现的潜在问题，并提供了问题的解决方案。
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
ms.date: 01/14/2021
ms.author: b-juche
ms.openlocfilehash: 7ca7f042aaa97967d47dd9f35afaa647e0d37361
ms.sourcegitcommit: f3f2ec7793ebeee19bd9ffc3004725fb33eb4b3f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2021
ms.locfileid: "129407259"
---
# <a name="troubleshoot-capacity-pool-issues"></a>排查容量池问题

本文介绍了管理容量池（包括更改池）时可能出现的问题的解决方案。 

## <a name="issues-managing-a-capacity-pool"></a>管理容量池时出现的问题 

|     添加状态    |     解决方法    |
|-|-|
| 创建容量池时出现的问题 |  请确保容量池计数不超过限制。 请参阅 [Azure NetApp 文件的资源限制](azure-netapp-files-resource-limits.md)。  如果计数低于限制并且仍出现问题，请提交支持票证并指定容量池名称。 |
| 删除容量池时出现的问题  |  请确保在尝试删除容量池的订阅中删除所有 Azure NetApp 文件卷和快照。 <br> 如果已删除所有卷和快照并且仍无法删除容量池，则对资源的引用可能仍然存在，但不会在门户中显示。 在这种情况下，请提交支持票证，并注明你已执行上述建议步骤。 |
| 创建卷失败或修改卷失败，出现 `Requested throughput not available` 错误 | 卷的可用吞吐量由容量池的大小和服务级别决定。 如果吞吐量不足，则应增加池大小或调整现有的卷吞吐量。 | 

## <a name="issues-when-changing-the-capacity-pool-of-a-volume"></a>更改卷的容量池时出现的问题 

|     添加状态    |     解决方法    |
|-|-|
| 不允许更改卷的容量池。 | 你可能尚未获得使用此功能的授权。 <br> 将卷移动到另一个容量池的功能目前以预览版提供。 如果你是首次使用此功能，则需要先注册该功能并设置 `-FeatureName ANFTierChange`。 请参阅[动态更改卷的服务级别](dynamic-change-volume-service-level.md)中的注册步骤。 |
| 容量池的大小对于卷的总大小来说太小。 |  此错误是由于目标容量池不具备要移动的卷所需的容量而导致的。  <br> 增加目标池的大小，或选择另一个更大的池。  请参阅[重设容量池或卷的大小](azure-netapp-files-resize-capacity-pools-or-volumes.md)。   |
|  池更改无法完成，因为目标池 `'{target pool name}'` 中已经存在一个名为 `'{source pool name}'` 的卷 | 发生此错误的原因是目标容量池中已经存在具有相同名称的卷。  请选择另一个不存在同名卷的容量池。   | 

## <a name="next-steps"></a>后续步骤  

* [创建容量池](azure-netapp-files-set-up-capacity-pool.md)
* [管理手动 QoS 容量池](manage-manual-qos-capacity-pool.md)
* [动态更改卷的服务级别](dynamic-change-volume-service-level.md)
* [重设容量池或卷的大小](azure-netapp-files-resize-capacity-pools-or-volumes.md)
