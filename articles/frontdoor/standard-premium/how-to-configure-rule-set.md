---
title: Azure Front Door：配置 Front Door 规则集
description: 本文提供有关如何配置规则集的指导。
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 6863c492059ccee152ecf3d03a09e61793576bcb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "101715592"
---
# <a name="configure-a-rule-set-with-azure-front-door-standardpremium-preview"></a>在 Azure Front Door 标准版/高级版（预览版）中配置规则集

> [!Note]
> 本文档适用于 Azure Front Door 标准/高级（预览版）。 正在寻找有关 Azure Front Door 的信息？ 请查看[此处](../front-door-overview.md)。

本文介绍如何在 Azure 门户中创建规则集和第一组规则。 然后，你将了解如何通过“规则集”页或 Endpoint Manager 将规则集与路由关联。

> [!IMPORTANT]
> Azure Front Door 标准版/高级版（预览版）目前以公共预览版提供。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

* 必须先创建 Azure Front Door 标准版/高级版，然后才能配置规则集。 有关详细信息，请参阅[快速入门：创建 Azure Front Door 标准版/高级版配置文件](create-front-door-portal.md)。

## <a name="configure-rule-set-in-azure-portal"></a>在 Azure 门户中配置规则集

1. 在 Front Door 配置文件中的“设置”下，选择“规则集” 。 选择“添加”，并指定规则集名称。

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-create-rule-set-1.png" alt-text="规则集登陆页的屏幕截图。":::
    
1. 选择“添加规则”，创建你的一个规则。 为其指定规则名称。 然后，选择“添加条件”或“添加操作”来定义规则 。 一个规则最多可添加 10 个条件和 5 个操作。 本示例使用服务器变量为 URL 中包含 contoso 的请求添加响应头 8Geo-country*。

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-create-rule-set.png" alt-text="规则集配置页的屏幕截图。":::
    
    > [!NOTE]
    > * 若要从规则中删除条件或操作，请使用特定条件或操作右侧的垃圾桶。
    > * 若要创建适用于所有传入流量的规则，请不要指定任何条件。
    > * 若要在满足某个特定规则后停止对其余规则进行评估，请选中“停止评估剩余规则”。 如果选中此选项，不论是否满足匹配条件，都不会执行规则集内的所有剩余规则。  

1. 你可以使用箭头按钮更改规则集中各个规则的优先级，以降低或升高规则的优先级。 列表按升序排序，即最重要的规则列于最前面。

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-rule-set-change-orders.png" alt-text="规则集优先级的屏幕截图。" lightbox="../media/how-to-configure-rule-set/front-door-rule-set-change-orders-expanded.png":::

1. 创建一个或多个规则后，请选择“保存”以完成规则集的创建。

1. 现在，将规则集与路由关联，使其生效。 你可通过“规则集”页关联规则集，也可转到“Endpoint Manager”来创建关联。
 
    **“规则集”页**： 
    
    1. 选择要关联的规则集。
    
    1. 选择“未关联”链接。
     

    1. 然后在“关联路由”页中，选择要与该规则集关联的终结点和路由。 
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set.png" alt-text="创建路由页的屏幕截图。":::    
        
    1. 如果所选路由下有多个规则集，请选择“下一步”，更改规则集顺序。 系统将自上而下执行规则集。 选中规则集并将其上移或下移，即可更改顺序。 然后选择“关联”。
    
        > [!Note]
        > 在此页中，只能将一个规则集与一个路由关联。 若要将规则集与多个路由关联，请使用 Endpoint Manager。
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-2.png" alt-text="规则集顺序的屏幕截图。":::
    
    1. 现在，规则集已与路由关联。 你可以查看响应头，并看到已添加 Geo-country。
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-3.png" alt-text="与路由关联的规则的屏幕截图。":::

   **Endpoint Manager**： 
    
    1. 转到“Endpoint Manager”，选择要与规则集关联的终结点。
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-1.png" alt-text="在 Endpoint Manager 中选择终结点的屏幕截图。" lightbox="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-1-expanded.png":::

    1. 选择“编辑终结点”。  
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-2.png" alt-text="在 Endpoint Manager 中选择“编辑终结点”的屏幕截图。" lightbox="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-2-expanded.png":::

    1. 选择“路由”。 
    
         :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-3.png" alt-text="选择“路由”的屏幕截图。":::
    
    1. 在“更新路由”页的“规则”中，从下拉列表中选择要与路由关联的规则集 。 然后，可通过上移和下移规则集来更改顺序。 
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-4.png" alt-text="更新路由页的屏幕截图。":::
    
    1. 然后选择“更新”或“添加”来完成关联 。

## <a name="delete-a-rule-set-from-your-azure-front-door-profile"></a>从 Azure Front Door 配置文件中删除规则集

在前述步骤中，你已配置规则集并将其关联到路由。 如果不再需要将规则集与 Front Door 关联，可完成以下步骤来删除规则集：

1. 转到“规则集”页的“设置”下，将规则集与所有关联的路由解除关联 。

1. 展开“路由”，选择三个点。 然后选择”编辑路由”。

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-disassociate-rule-set-1.png" alt-text="规则集中展开的路由的屏幕截图。":::

1. 转到“路由”页的“规则”部分，选择规则集，然后选择“删除”按钮。 

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-disassociate-rule-set-2.png" alt-text="要删除规则集的“更新路由”页的屏幕截图。" lightbox="../media/how-to-configure-rule-set/front-door-disassociate-rule-set-2-expanded.png":::

1. 选择“更新”，规则集随即将解除与路由的关联。

1. 重复步骤 2-5，将此规则集关联的其他路由解除关联，直到看到“路由状态”显示“未关联”。

1. 对于“未关联”的规则集，你可单击右侧的三个点并选择“删除”来删除该规则集 。 

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-disassociate-rule-set-3.png" alt-text="如何删除规则集的屏幕截图。":::

1. 规则集现已删除。

## <a name="next-steps"></a>后续步骤

了解如何使用规则集添加[安全性标头](how-to-add-security-headers.md)。
