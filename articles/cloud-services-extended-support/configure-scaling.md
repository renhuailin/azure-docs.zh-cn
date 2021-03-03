---
title: '配置 Azure 云服务的缩放 (扩展支持) '
description: '如何为 Azure 云服务启用缩放选项 (扩展支持) '
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: cfa5be01a0d36764086c6c9adf97e6cb166d2bb6
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101728155"
---
# <a name="configure-scaling-options-with-azure-cloud-services-extended-support"></a> (扩展支持) 配置扩展选项与 Azure 云服务 

可以配置条件，使云服务 (扩展支持) 部署的扩展和缩减。这些条件可以基于 CPU 使用情况、磁盘负载和网络负载。 

配置云服务部署的缩放时，请考虑以下信息：
- 缩放会影响核心使用。 较大的角色实例会消耗更多的内核，只能在订阅的核心限制范围内进行缩放。 有关详细信息，请参阅 [Azure 订阅和服务限制、配额与约束](../azure-resource-manager/management/azure-subscription-service-limits.md)。
- 支持基于队列消息传送阈值缩放。 有关详细信息，请参阅[开始使用 Azure 队列存储](../storage/queues/storage-dotnet-how-to-use-queues.md)。
- 若要确保云服务的高可用性 () 应用程序的扩展支持，请确保部署两个或更多角色实例。
- 仅当所有角色都处于 " **就绪** " 状态时，才会发生自定义自动缩放。

## <a name="configure-and-manage-scaling"></a>配置和管理缩放

1. 登录 [Azure 门户](https://portal.azure.com)。 
2. 选择要在其上配置缩放的云服务 (扩展支持) 部署。 
3. 选择 " **缩放** " 边栏选项卡。 

    :::image type="content" source="media/enable-scaling-1.png" alt-text="图像显示在 Azure 门户中选择 &quot;远程桌面&quot; 选项":::

4. 页面将显示可在其中配置缩放的所有角色的列表。 选择要配置的角色。 
5. 选择要配置的规模类型
    - **手动缩放** 将设置实例的绝对计数。
        1. 选择 " **手动缩放**"。
        2. 输入要向上或向下扩展到的实例数。
        3. 选择“保存”。

        :::image type="content" source="media/enable-scaling-2.png" alt-text="图像显示在 Azure 门户中设置手动缩放":::

        4. 缩放操作将立即开始。 
        
    - **自定义自动缩放** 功能可让你设置规则来控制缩放的比例。 
        1. 选择 **自定义自动缩放**
        2. 选择基于指标或实例计数进行缩放。

        :::image type="content" source="media/enable-scaling-3.png" alt-text="图像显示在 Azure 门户中设置自定义自动缩放":::

        3. 如果基于度量值进行缩放，请根据需要添加规则以实现所需的缩放结果。

        :::image type="content" source="media/enable-scaling-4.png" alt-text="图像显示在 Azure 门户中设置自定义自动缩放规则":::

        4. 选择“保存”。
        5. 一旦触发规则，缩放操作就会开始。
        
6. 通过选择 " **缩放** " 选项卡，可以查看或调整应用于部署的现有缩放规则。

    :::image type="content" source="media/enable-scaling-5.png" alt-text="图像显示调整现有缩放规则的 Azure 门户":::

## <a name="next-steps"></a>后续步骤 
- 查看云服务的 [部署先决条件](deploy-prerequisite.md) (扩展支持) 。
- 请参阅云服务（外延支持）的[常见问题解答](faq.md)。
- 使用 [Azure 门户](deploy-portal.md)、[PowerShell](deploy-powershell.md)、[模板](deploy-template.md)或 [Visual Studio](deploy-visual-studio.md) 部署云服务（外延支持）。