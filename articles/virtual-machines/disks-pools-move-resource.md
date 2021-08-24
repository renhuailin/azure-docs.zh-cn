---
title: 将 Azure 磁盘池（预览版）移到其他订阅
description: 了解如何将 Azure 磁盘池移到其他订阅。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 07/19/2021
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 629ca5617bfc2166e10172863fc6d7560698919d
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114436929"
---
# <a name="move-a-disk-pool-preview-to-a-different-subscription"></a>将磁盘池（预览版）移到其他订阅

移动 Azure 磁盘池（预览版）涉及移动磁盘池本身、磁盘池中包含的磁盘、磁盘池的受管理资源组，以及受管理资源组中包含的所有资源。 目前，Azure 不支持一次将多个资源组移到另一个订阅。 

- 导出现有磁盘池的模板。
- 删除旧磁盘池。
- 移动创建磁盘池所需的 Azure 资源。
- 重新部署磁盘池。

## <a name="export-your-existing-disk-pool-template"></a>导出现有磁盘池模板

若要简化重新部署过程，请从现有磁盘池导出该模板。 可以使用该模板以相同的配置在所选订阅中重新部署磁盘池。 请参阅[此文](../azure-resource-manager/templates/export-template-portal.md#export-template-from-a-resource)，了解如何从资源导出模板。

## <a name="delete-the-old-disk-pool"></a>删除旧磁盘池

导出模板后，就可以删除旧磁盘池。 删除磁盘池会删除磁盘池资源及其受管理资源组。 有关如何删除磁盘池的指南，请参阅[此文](disks-pools-deprovision.md)。

## <a name="move-your-disks-and-virtual-network"></a>移动磁盘和虚拟网络

删除磁盘池后，就可以将虚拟网络、磁盘以及可能的客户端移到要更切换到的订阅。 请参阅[此文](../azure-resource-manager/management/move-resource-group-and-subscription.md)，了解如何将 Azure 资源移到另一个订阅。

## <a name="redeploy-your-disk-pool"></a>重新部署磁盘池

将其他资源移到订阅中以后，请更新旧磁盘池的模板，使得对磁盘、虚拟网络、子网和客户端的所有引用现在都指向其新资源 URI。 完成该操作后，请将模板重新部署到新订阅。 若要了解如何编辑和部署模板，请参阅[此文](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md#edit-and-deploy-the-template)。

## <a name="next-steps"></a>后续步骤

若要了解如何管理磁盘池，请参阅[管理磁盘池](disks-pools-manage.md)。