---
title: 跨区域移动 Azure Automanage 虚拟机
description: 了解如何跨区域移动 Automanaged 虚拟机
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/05/2021
ms.author: alsin
ms.custom: subject-moving-resources
ms.openlocfilehash: 99371b8618756c196b75858288c5c4785272a7e8
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101650459"
---
# <a name="move-an-azure-automanage-virtual-machine-to-a-different-region"></a>将 Azure Automanage 虚拟机移至其他区域
本文介绍如何在虚拟机上将 Automanage 保持启用状态，以便在将虚拟机移动到其他区域 (VM) 。 出于多种原因，你可能需要将虚拟机移动到另一个区域。 例如，为了利用新的 Azure 区域，为了满足内部策略和监管要求，或者，为了应对容量规划要求。 你移动的这些 Vm 当前可能是 Automanaged 的，你可能希望它们在移动后保持 Automanaged。

## <a name="prerequisites"></a>先决条件
* 确保 [Automanage 支持](./automanage-virtual-machines.md#prerequisites)目标区域。
* 确保 Log Analytics 工作区区域、自动化帐户区域和目标区域都是 [此处](../automation/how-to/region-mappings.md)的区域映射支持的所有区域。

## <a name="prepare-your-automanaged-vms-for-moving"></a>准备要移动的 Automanaged Vm
在 Automanaged Vm 上禁用 Automanage。 为此，可以在 "Automanage" 边栏选项卡中选择 Vm，并在 Automanage 边栏选项卡中单击 " **禁用 automanagement** "。

## <a name="move-your-automanaged-vms-and-re-enable-automanage"></a>移动 Automanaged Vm 并重新启用 Automanage
有关如何移动 Vm 的详细信息，请参阅此 [文](../resource-mover/tutorial-move-region-virtual-machines.md)。

一旦跨区域移动了 Vm，就可以重新启用 Automanage。 [此处](./automanage-virtual-machines.md#enabling-automanage-for-vms-in-azure-portal)提供了详细信息。

## <a name="next-steps"></a>后续步骤
* [了解有关 Azure Automanage 的详细信息](./automanage-virtual-machines.md)
* [查看有关 Azure Automanage 的常见问题](./faq.md)