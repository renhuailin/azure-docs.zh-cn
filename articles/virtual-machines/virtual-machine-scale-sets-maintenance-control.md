---
title: Azure 虚拟机规模集上 OS 映像升级的维护控制概述
description: 了解如何使用维护控制来控制何时将自动 OS 映像升级部署到 Azure 虚拟机规模集。
author: ju-shim
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 09/11/2020
ms.author: jushiman
ms.openlocfilehash: 60922ca7b2e910c883d39a89bebcd6de3c4b4ec8
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122695747"
---
# <a name="maintenance-control-for-azure-virtual-machine-scale-sets"></a>Azure 虚拟机规模集的维护控制 

**适用于：:heavy_check_mark: 统一规模集**

使用维护控制来管理虚拟机规模集的[自动 OS 映像升级](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md)。

有了维护控制，你可以通过更简单且更容易预测的体验来决定何时将更新应用于虚拟机规模集中的操作系统磁盘。 

维护配置跨订阅和资源组生效。

整个工作流归结为以下步骤： 
- 创建维护配置。
- 将虚拟机规模集关联到维护配置。
- 启用自动 OS 升级。


## <a name="limitations"></a>限制

- VM 必须位于规模集中。
- 用户必须具有“资源参与者”  访问权限。
- 维护配置中的维护持续时间必须达到 5 小时或更长时间。
- 维护配置中的维护重复周期必须设置为“天”


## <a name="management-options"></a>管理选项

可以使用以下任一选项来创建和管理维护配置：

- [Azure PowerShell](virtual-machine-scale-sets-maintenance-control-powershell.md)
- [Azure CLI](virtual-machine-scale-sets-maintenance-control-cli.md)
- [Azure 门户](virtual-machine-scale-sets-maintenance-control-portal.md)


## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用 PowerShell 进行虚拟机规模集维护控制](virtual-machine-scale-sets-maintenance-control-powershell.md)
