---
title: 使用 Azure 门户为 Azure 虚拟机规模集上的 OS 映像升级进行维护控制
description: 了解如何使用维护控制和 Azure 门户来控制何时向 Azure 虚拟机规模集推出自动 OS 映像升级。
author: ju-shim
ms.service: virtual-machine-scale-sets
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 06/01/2021
ms.author: jushiman
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 656d72663045b4604b9a94cbc686150925caca83
ms.sourcegitcommit: e1874bb73cb669ce1e5203ec0a3777024c23a486
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/16/2021
ms.locfileid: "112202033"
---
# <a name="maintenance-control-for-os-image-upgrades-on-azure-virtual-machine-scale-sets-using-azure-portal"></a>使用 Azure 门户为 Azure 虚拟机规模集上的 OS 映像升级进行维护控制

利用维护控制，可以决定何时将自动来宾 OS 映像升级应用到虚拟机规模集。 本主题介绍了用于维护控制的 Azure 门户选项。 若要了解如何使用维护控制，请参阅 [Azure 虚拟机规模集的维护控制](virtual-machine-scale-sets-maintenance-control.md)。


## <a name="create-a-maintenance-configuration"></a>创建维护配置

1. 登录到 Azure 门户。

1. 搜索“维护配置”。
    
    :::image type="content" source="media/virtual-machine-scale-sets-maintenance-control-portal/maintenance-configurations-search-bar.png" alt-text="屏幕截图显示了如何打开“维护配置”":::

1. 选择 **添加**。

    :::image type="content" source="media/virtual-machine-scale-sets-maintenance-control-portal/maintenance-configurations-add.png" alt-text="屏幕截图显示了如何添加维护配置":::

1. 在“基本信息”选项卡中，选择订阅和资源组，为配置提供名称，选择一个区域，然后选择“OS 映像升级”作为范围。 选择“**下一页**”。
    
    :::image type="content" source="media/virtual-machine-scale-sets-maintenance-control-portal/maintenance-configurations-basics-tab.png" alt-text="屏幕截图显示了维护配置基本信息":::

1. 在“计划”选项卡中，声明 Azure 将对资源应用更新的计划时段。 设置开始日期、维护时段和重复周期。 在创建计划时段后，不再需要手动应用更新。 选择“**下一页**”。 

    > [!IMPORTANT]
    > 维护时段持续时间必须为 5 小时或更长时间。 必须将维护重复周期设置为每天至少一次。 

    :::image type="content" source="media/virtual-machine-scale-sets-maintenance-control-portal/maintenance-configurations-schedule-tab.png" alt-text="显示维护配置计划的屏幕截图":::

1. 在“分配”选项卡中，立即分配资源，或跳过此步骤，在维护配置部署后再分配资源。 选择“**下一页**”。

1. 添加标记和值。 选择“**下一页**”。
    
    :::image type="content" source="media/virtual-machine-scale-sets-maintenance-control-portal/maintenance-configurations-tags-tab.png" alt-text="屏幕截图显示了如何向维护配置添加标记":::

1. 查看摘要。 选择“创建”  。

1. 部署完成后，选择“转到资源”。


## <a name="assign-the-configuration"></a>分配此配置

在维护配置的详细信息页面上，选择“分配”，然后选择“分配资源” 。 

![屏幕截图显示了如何分配资源](media/virtual-machine-scale-sets-maintenance-control-portal/maintenance-configurations-add-assignment.png)

选择要将维护配置分配到的虚拟机规模集资源，然后选择“确定”。  


## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解 Azure 中运行的虚拟机的维护和更新](maintenance-and-updates.md)
