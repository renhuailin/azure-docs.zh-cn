---
title: Azure NetApp 文件的跨区域复制故障排除 | Microsoft Docs
description: 介绍了有助于对 Azure NetApp 文件的跨区域复制问题进行故障排除的错误消息和解决方案。
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
ms.date: 03/10/2021
ms.author: b-juche
ms.openlocfilehash: d3d944646689e9e6189b0343e8bf67c8fb0abcbd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "104590919"
---
# <a name="troubleshoot-cross-region-replication"></a>跨区域复制故障排除

介绍了有助于对 Azure NetApp 文件的跨区域复制问题进行故障排除的错误消息和解决方案。 

## <a name="errors-creating-replication"></a>创建复制时出错  

|     错误消息    |     解决方法    |
|-|-|
|     `Volume {0} cannot   be used as source because it is already in replication`    |     不能使用已经处于数据复制关系中的源卷创建复制。    |
|     `Peered region   '{0}' is not accepted`    |     正在尝试在非对等互连区域之间创建复制。    |
|     `RemoteVolumeResource   '{0}' of wrong type '{1}'`    |     验证远程资源 ID 是卷资源 ID。    |

## <a name="errors-authorizing-volume"></a>对卷进行授权时出错  

|     错误消息    |     解决方法    |
|-|-|
|     `Missing value   for 'AuthorizeSourceReplication'`    |     UI 或 API 请求中的 `RemoteResourceID` 缺少或无效（修复错误消息）。    |
|     `Missing value   for 'RemoteVolumeResourceId'`    |     UI 或 API 请求中的 `RemoteResourceID` 缺少或无效。    |
|     `Data   Protection volume not found for RemoteVolumeResourceId: {remoteResourceId}`    |     验证用户的 `RemoteResourceID` 是否正确或是否存在。    |
|     `Remote volume   '{0}' is not configured for replication`    |     目标卷不是数据保护卷。    |
|     `Remote volume   '{0}' does not have source volume '{1}' as RemoteVolumeResourceId`    |     数据保护卷的远程资源 ID 中没有此源卷（输入了错误的源 ID）。    |
|     `The   destination volume replication creation failed (message: {0})`    |     该错误指示服务器错误。 联系人 支持。    |

## <a name="errors-deleting-replication"></a>删除复制时出错

|     错误消息    |     解决方法    |
|-|-|
|     `Replication   cannot be deleted, mirror state needs to be in status: Broken before deleting`    |     验证复制已中断，或者其未初始化且空闲（初始化失败）。    |
|     `Cannot delete   source replication`    |     不允许从源端删除复制。 请确保从目标端删除复制。    |

## <a name="errors-deleting-volume"></a>删除卷时出错

|     错误消息    |     解决方法    |
|-|-|
| `Volume is a member of an active volume replication relationship`  |  请在删除卷之前删除复制。 请参阅[删除复制](cross-region-replication-delete.md)。 此操作要求在删除卷的复制之前中断对等互连。 |
| `Volume with replication cannot be deleted`  |  请在删除卷之前删除复制。 请参阅[删除复制](cross-region-replication-delete.md)。 此操作要求在删除卷的复制之前中断对等互连。 

## <a name="errors-resyncing-volume"></a>重新同步卷时出错

|     错误消息    |     解决方法    |
|-|-|
|     `Volume Replication is in invalid status: (Mirrored|Uninitialized) for operation: 'ResyncReplication'`     |     验证卷复制处于“中断”状态。    |

## <a name="errors-deleting-snapshot"></a>删除快照时出错 

|     错误消息    |     解决方法    |
|-|-|
|     `Snapshot   cannot be deleted, parent volume is a Data Protection volume with a   replication object`    |     如果要删除此快照，请验证是否已中断卷的复制。    |
|     `Cannot delete   volume replication generated snapshot`    |     不允许删除复制基线快照。    |

## <a name="errors-resizing-volumes"></a>重设卷大小时出错

|     错误消息    |     解决方法    |
|-|-|
|   尝试重设源卷的大小失败，出现错误 `"PoolSizeTooSmall","message":"Pool size too small for total volume size."`  |  确保在容量池中有足够的空间用于跨区域复制的源卷和目标卷。 重设源卷的大小时，目标卷会自动重设大小。 但是，如果托管目标卷的容量池没有足够的空间，则同时重设源卷和目标卷的大小将会失败。 请参阅[重设跨区域复制目标卷的大小](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-a-cross-region-replication-destination-volume)了解详细信息。   |

## <a name="next-steps"></a>后续步骤  

* [跨区域复制](cross-region-replication-introduction.md)
* [使用跨区域复制的要求和注意事项](cross-region-replication-requirements-considerations.md)
* [创建卷复制](cross-region-replication-create-peering.md)
* [显示复制关系的运行状况](cross-region-replication-display-health-status.md)
* [管理灾难恢复](cross-region-replication-manage-disaster-recovery.md)
* [重设跨区域复制目标卷的大小](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-a-cross-region-replication-destination-volume)
* [跨区域复制故障排除](troubleshoot-cross-region-replication.md)
