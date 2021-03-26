---
title: 管理 Azure 资源转移器中在 VM 移动过程中创建的资源
description: 了解如何管理 Azure 资源转移器中在 VM 移动过程中创建的资源
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/10/2020
ms.author: raynew
ms.openlocfilehash: d3c4c4e86e2461ea1d05af284e724a5a2991f040
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "101727033"
---
# <a name="manage-resources-created-for-the-vm-move"></a>管理为 VM 移动创建的资源

本文介绍如何管理由 [Azure 资源转移器](overview.md)显式创建的资源，以便加速 VM 移动过程。 

跨区域移动 VM 后，应手动清除由资源转移器创建的许多资源。

## <a name="delete-resources-created-for-vm-move"></a>删除为 VM 移动创建的资源

手动删除移动集合和为 VM 移动创建的 Site Recovery 资源。

1. 查看资源组 ```ResourceMoverRG-<sourceregion>-<target-region>-<metadataRegionShortName>``` 中的资源。
2. 查看是否已移动/删除移动集合中的 VM 和所有其他源资源。 这可确保没有挂起的资源使用它们。
2. 删除这些资源。

    - 移动集合名称为 ```movecollection-<sourceregion>-<target-region>-<metadata-region>```。
    - 缓存存储账户名称为 ```resmovecache<guid>```
    - 保管库名称为 ```ResourceMove-<sourceregion>-<target-region>-GUID```。

## <a name="next-steps"></a>后续步骤

尝试使用资源转移器[将 VM 移动到](tutorial-move-region-virtual-machines.md)另一个区域。
