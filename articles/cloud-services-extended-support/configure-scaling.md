---
title: 为 Azure 云服务（外延支持）配置缩放
description: 如何为 Azure 云服务（外延支持）启用缩放选项
ms.topic: how-to
ms.service: cloud-services-extended-support
ms.subservice: autoscale
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: ef8bf7192814ee791066bbbfd080d0d5cf9d6943
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114460895"
---
# <a name="configure-scaling-options-with-azure-cloud-services-extended-support"></a>为 Azure 云服务（外延支持）配置缩放选项 

条件可以配置为允许云服务（外延支持）部署横向缩减和扩展。这些条件可以基于 CPU 使用率、磁盘负载和网络负载。 

在配置云服务部署的缩放时，请考虑以下事项：
- 缩放影响核心的使用。 较大的角色实例会消耗更多的核心，你只能在订阅的核心限制内进行缩放。 有关详细信息，请参阅 [Azure 订阅和服务限制、配额与约束](../azure-resource-manager/management/azure-subscription-service-limits.md)。
- 支持基于队列消息传送阈值进行缩放。 有关详细信息，请参阅[开始使用 Azure 队列存储](../storage/queues/storage-dotnet-how-to-use-queues.md)。
- 为确保云服务（外延支持）应用程序的高可用性，请确保使用两个或更多个角色实例进行部署。
- 仅当所有角色都处于“就绪”状态时，才能进行自定义自动缩放。

## <a name="configure-and-manage-scaling"></a>配置和管理缩放

1. 登录 [Azure 门户](https://portal.azure.com)。 
2. 选择要对其配置缩放的云服务（外延支持）部署。 
3. 选择“缩放”边栏选项卡。 

    :::image type="content" source="media/enable-scaling-1.png" alt-text="此图显示了在 Azure 门户中选中“远程桌面”选项":::

4. 页面会显示可在其中配置缩放的所有角色的列表。 选择要配置的角色。 
5. 选择要配置的缩放类型
    - “手动缩放”会设置实例的绝对计数。
        1. 选择“手动缩放”。
        2. 输入要纵向扩展或缩减到的实例数。
        3. 选择“保存”。

        :::image type="content" source="media/enable-scaling-2.png" alt-text="此图显示了在 Azure 门户中设置“手动缩放”":::

        4. 缩放操作会立即开始。 
        
    - “自定义自动缩放”可让你设置规则来控制缩放的大小。 
        1. 选择“自定义自动缩放”
        2. 选择基于指标或实例计数进行缩放。

        :::image type="content" source="media/enable-scaling-3.png" alt-text="此图显示了在 Azure 门户中设置自定义自动缩放":::

        3. 如果基于指标进行缩放，请根据需要添加规则以实现所需的缩放结果。

        :::image type="content" source="media/enable-scaling-4.png" alt-text="此图显示了在 Azure 门户中设置自定义自动缩放规则":::

        4. 选择“保存”。
        5. 触发规则后，缩放操作会立即开始。
        
6. 通过选择“缩放”选项卡，可以查看或调整应用于部署的现有缩放规则。

    :::image type="content" source="media/enable-scaling-5.png" alt-text="此图显示了在 Azure 门户中调整现有缩放规则":::

## <a name="next-steps"></a>后续步骤 
- 查看云服务（外延支持）的[部署先决条件](deploy-prerequisite.md)。
- 请参阅云服务（外延支持）的[常见问题解答](faq.yml)。
- 使用 [Azure 门户](deploy-portal.md)、[PowerShell](deploy-powershell.md)、[模板](deploy-template.md)或 [Visual Studio](deploy-visual-studio.md) 部署云服务（外延支持）。