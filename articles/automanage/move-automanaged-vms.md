---
title: 跨区域移动 Azure Automanage 虚拟机
description: 了解如何跨区域移动自动管理的虚拟机
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/05/2021
ms.author: alsin
ms.custom: subject-moving-resources
ms.openlocfilehash: 4e9fba5ff9dc10e0691423da689024af3e2b72bc
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114468340"
---
# <a name="move-an-azure-automanage-virtual-machine-to-a-different-region"></a>将 Azure Automanage 虚拟机移动到另一区域
本文介绍如何在将虚拟机 (VM) 移动到其他区域时保持 Automanage 在该虚拟机上处于启用状态。 由于多方面的原因，你可能需要将虚拟机移动到另一个区域。 例如，为了利用新的 Azure 区域，为了满足内部策略和监管要求，或者，为了应对容量规划要求。 你移动的那些 VM 目前可能是自动管理的，你可能希望它们在移动后仍保持自动管理。

## <a name="prerequisites"></a>先决条件
* 确保 [Automanage 支持](./automanage-virtual-machines.md#prerequisites)你的目标区域。
* 确保 Log Analytics 工作区区域、自动化帐户区域和目标区域都是[此处](../automation/how-to/region-mappings.md)的区域映射支持的区域。

## <a name="prepare-your-automanaged-vms-for-moving"></a>准备要进行移动的自动管理的 VM
在自动管理的 VM 上禁用 Automanage。 为此，可在“Automanage”边栏选项卡中选择 VM，并在“Automanage”边栏选项卡中单击“禁用自动管理”。

## <a name="move-your-automanaged-vms-and-re-enable-automanage"></a>移动自动管理的 VM 并重新启用 Automanage
有关如何移动 VM 的详细信息，请参阅这篇[文章](../resource-mover/tutorial-move-region-virtual-machines.md)。

在跨区域移动了 VM 之后，可以在这些 VM 上重新启用 Automanage。 [此处](./automanage-virtual-machines.md#enabling-automanage-for-vms-in-azure-portal)提供了详细信息。

## <a name="next-steps"></a>后续步骤
* [详细了解 Azure Automanage](./automanage-virtual-machines.md)
* [查看关于 Azure Automanage 的常见问题解答](./faq.yml)