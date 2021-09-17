---
title: 使用 Azure 门户对 Azure 虚拟机进行维护控制
description: 了解如何使用维护控制和 Azure 门户来控制对 Azure VM 应用维护的时间。
author: cynthn
ms.service: virtual-machines
ms.subservice: maintenance-control
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/22/2020
ms.author: cynthn
ms.openlocfilehash: 0db6f8d43a2492b59b407e43eee016d130915c77
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122687739"
---
# <a name="control-updates-with-maintenance-control-and-the-azure-portal"></a>使用维护控制和 Azure 门户来控制更新

适用于：:heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 灵活规模集 :heavy_check_mark: 统一规模集

维护控制允许你决定何时向独立 VM 和 Azure 专用主机应用更新。 本主题介绍了用于维护控制的 Azure 门户选项。 有关使用维护控制的好处、其限制和其他管理选项的详细信息，请参阅[使用维护控制管理平台更新](maintenance-control.md)。

## <a name="create-a-maintenance-configuration"></a>创建维护配置

1. 登录到 Azure 门户。

1. 搜索“维护配置”。
    
    :::image type="content" source="media/virtual-machines-maintenance-control-portal/maintenance-configurations-search-bar.png" alt-text="屏幕截图显示了如何打开“维护配置”":::

1. 单击“添加”。

    :::image type="content" source="media/virtual-machines-maintenance-control-portal/maintenance-configurations-add-2.png" alt-text="屏幕截图显示了如何添加维护配置":::

1. 在“基本信息”选项卡中，选择订阅和资源组，为配置提供名称，选择一个区域，然后选择“主机”作为范围。 单击“下一步” 。
    
    :::image type="content" source="media/virtual-machines-maintenance-control-portal/maintenance-configurations-basics-tab.png" alt-text="屏幕截图显示了维护配置基本信息":::

1. 在“计划”选项卡中，声明 Azure 将对资源应用更新的计划时段。 设置开始日期、维护时段和重复周期。 在创建计划时段后，不再需要手动应用更新。 单击“下一步”  。 

    > [!IMPORTANT]
    > 维护时段持续时间必须为 2 小时或更长时间。 必须将维护重复周期设置为每 35 天至少一次。 

    :::image type="content" source="media/virtual-machines-maintenance-control-portal/maintenance-configurations-schedule-tab.png" alt-text="显示维护配置计划的屏幕截图":::

1. 在“分配”选项卡中，立即分配资源，或跳过此步骤，稍后在维护配置部署后再分配资源。 单击“下一步”  。

1. 添加标记和值。 单击“下一步” 。
    
    :::image type="content" source="media/virtual-machines-maintenance-control-portal/maintenance-configurations-tags-tab.png" alt-text="屏幕截图显示了如何向维护配置添加标记":::

1. 查看摘要。 单击“创建”。

1. 部署完成后，单击“转到资源”。


## <a name="assign-the-configuration"></a>分配此配置

在维护配置的详细信息页面上，单击“分配”，然后单击“分配资源”。 

![屏幕截图显示了如何分配资源](media/virtual-machines-maintenance-control-portal/maintenance-configurations-add-assignment.png)

选择要为其分配维护配置的资源，然后单击“确定”。 “类型”列显示资源是独立 VM 还是 Azure 专用主机。 VM 需要处于运行状态才能分配配置。 如果尝试将配置分配给已停止的 VM，则会出现错误。 

<!---Shantanu to add details about the error case--->

![屏幕截图显示了如何选择资源](media/virtual-machines-maintenance-control-portal/maintenance-configurations-select-resource.png)

## <a name="check-configuration"></a>检查配置

你可以验证是否已正确应用了配置，或查看当前使用“维护配置”指定的任何维护配置。 “类型”列显示配置分配给独立 VM 还是 Azure 专用主机。 

![屏幕截图显示了如何检查维护配置](media/virtual-machines-maintenance-control-portal/maintenance-configurations-host-type.png)

你还可以在特定虚拟机的“属性”页上检查其配置。 单击“维护”可查看分配给该虚拟机的配置。

![屏幕截图显示了如何检查主机的维护](media/virtual-machines-maintenance-control-portal/maintenance-configurations-check-config.png)

## <a name="check-for-pending-updates"></a>检查是否有挂起的更新

还可以通过两种方式检查维护配置是否有挂起的更新。 在“维护配置”的配置详细信息中，单击“分配”并检查“维护状态”。

![屏幕截图显示了如何检查挂起的更新](media/virtual-machines-maintenance-control-portal/maintenance-configurations-pending.png)

你还可以使用“虚拟机”或专用主机的属性来检查特定主机。 

![屏幕截图显示了突出显示的维护状态。](media/virtual-machines-maintenance-control-portal/maintenance-configurations-pending-vm.png)

## <a name="apply-updates"></a>应用更新

可以按需应用挂起的更新。 在 VM 或 Azure 专用主机详细信息上，单击“维护”，然后单击“立即应用维护” 。 应用更新调用可能需要长达 2 小时才能完成。

![屏幕截图显示了如何应用挂起的更新](media/virtual-machines-maintenance-control-portal/maintenance-configurations-apply-updates-now.png)

## <a name="check-the-status-of-applying-updates"></a>检查应用更新的状态 

可以通过“维护配置”或“虚拟机”检查某个配置的更新进度。 在 VM 详细信息中，单击“维护”。 在下面的示例中，“维护状态”显示某个更新处于“挂起”状态。

![屏幕截图显示了如何检查挂起的更新的状态](media/virtual-machines-maintenance-control-portal/maintenance-configurations-status.png)

## <a name="delete-a-maintenance-configuration"></a>删除维护配置

若要删除某个配置，请打开配置详细信息，然后单击“删除”。

![屏幕截图显示了如何删配置。](media/virtual-machines-maintenance-control-portal/maintenance-configurations-delete.png)


## <a name="next-steps"></a>后续步骤

若要了解详细信息，请参阅[维护和更新](maintenance-and-updates.md)。
