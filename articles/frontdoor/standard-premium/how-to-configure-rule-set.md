---
title: Azure 前门：配置前门规则集
description: 本文提供了有关如何配置规则集的指导。
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: e2fe475b171a99ec27ed162511db289891066e00
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2021
ms.locfileid: "101098455"
---
# <a name="configure-a-rule-set"></a>配置规则集

> [!Note]
> 本文档适用于 Azure 前门标准/高级 (预览版) 。 正在寻找有关 Azure 前门的信息？ 查看 [此处](../front-door-overview.md)。

本教程演示如何在 Azure 门户中创建规则集和第一组规则。 

在本教程中，了解如何：
> [!div class="checklist"]
> - 使用门户配置规则集。
> - 使用门户从 AFD 配置文件中删除规则集

> [!IMPORTANT]
> Azure 前门标准/高级 (预览版) 目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

* 在完成本教程中的步骤之前，必须先创建 Azure 前门标准/高级版。 有关详细信息，请参阅 [快速入门：创建 Azure 前门标准/高级配置文件](create-front-door-portal.md)。

## <a name="configure-rule-set-in-azure-portal"></a>在 Azure 门户中配置规则集

1. 在前门配置文件中，选择 "**设置**" 下的 "**规则集**"。 选择 " **添加** "，并为其指定一个规则集名称。

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-create-rule-set-1.png" alt-text="规则集登录页的屏幕截图。":::
    
1. 选择 " **添加规则** "，创建第一个规则。 为其指定一个规则名称。 然后，选择 " **添加条件** " 或 " **添加操作** " 来定义规则。 对于一个规则，最多可以添加10个条件和5个操作。 在此示例中，我们使用服务器变量为 URL 中包含 *contoso* 的请求添加响应标头8Geo。

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-create-rule-set.png" alt-text="规则集配置页的屏幕截图。":::
    
    > [!NOTE]
    > * 若要从规则中删除条件或操作，请使用特定条件或操作右侧的 "垃圾箱"。
    > * 若要创建适用于所有传入流量的规则，请不要指定任何条件。
    > * 若要在满足特定规则时停止计算剩余规则，请选中 " **停止计算剩余规则**"。 如果选中此选项，则不会执行规则集中的所有剩余规则，而不管是否满足匹配条件。  

1. 您可以通过使用箭头按钮在优先级较高或更低的情况下移动规则，来确定规则集中规则的优先级。 此列表按升序排序，因此最重要的规则排在第一位。

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-rule-set-change-orders.png" alt-text="规则集优先级的屏幕截图。" lightbox="../media/how-to-configure-rule-set/front-door-rule-set-change-orders-expanded.png":::

1. 创建一个或多个规则后，请选择 " **保存** " 以完成创建规则集。

1. 现在，将规则集关联到路由，使其生效。 你可以通过 "规则集" 页关联规则集，也可以通过 "终结点管理器" 创建关联。
 
    **规则集页**： 
    
    1. 选择要关联的规则集。
    
    1. 选择未 *关联* 的链接。
     

    1. 然后在 " **关联路由** " 边栏选项卡中，选择要与该规则集关联的终结点和路由。 
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set.png" alt-text="创建路由页的屏幕截图。":::    
        
    1. 如果所选路由下有多个规则集，请单击 " *下一步* " 以更改规则集顺序。 将从上到下执行规则集。 您可以通过选择规则集并将其上移或下移来更改顺序。然后选择 " *关联*"。
    
        > [!Note]
        > 在此页上，只能将一个规则集与一个路由关联。 若要将规则集与多个路由关联，请使用终结点管理器。
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-2.png" alt-text="规则集顺序的屏幕截图。":::
    
    1. 现在，规则集与路由关联。 您可以查看 "响应" 标头，并看到 "地理国家/地区已添加"。
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-3.png" alt-text="与路由关联的规则的屏幕截图。":::

   **终结点管理器**： 
    
    1. 单击 "终结点管理器"，选择要与规则集关联的终结点。
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-1.png" alt-text="终结点管理器中选择终结点的屏幕截图。" lightbox="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-1-expanded.png":::

    1. 单击 "*编辑终结点*"  
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-2.png" alt-text="在终结点管理器中选择 &quot;编辑终结点&quot; 的屏幕截图。" lightbox="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-2-expanded.png":::

    1. 单击该路由。 
    
         :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-3.png" alt-text="选择路由的屏幕截图。":::
    
    1. 在 " *更新路由* " 边栏选项卡中的 " *规则*" 中，从下拉列表中选择要与路由关联的规则集。 然后，可以通过移动规则集来更改订单。 
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-4.png" alt-text="更新路由页的屏幕截图。":::
    
    1. 然后选择 " *更新* " 或 " *添加* " 完成关联。

## <a name="delete-a-rule-set-from-your-azure-front-door-profile"></a>从 Azure 前门配置文件中删除规则集

在前面的步骤中，你配置了一个规则集，并将其关联到了你的路由。 如果不再需要将规则集关联到前门，可以通过完成以下步骤来删除规则集：

1. 中转到 "**设置**" 下的 "**规则集" 页**，将规则集与所有关联的路由解除关联。

1. 展开到路由，单击三个点，选择 " *编辑路由*"。

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-disassociate-rule-set-1.png" alt-text="在规则集中展开路由的屏幕截图。":::

1. 在 "路由" 页上，选择 "规则" 部分，选择规则集，然后在 " *删除* " 按钮上选择。 

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-disassociate-rule-set-2.png" alt-text="用于删除规则集的 &quot;更新路由&quot; 页的屏幕截图。" lightbox="../media/how-to-configure-rule-set/front-door-disassociate-rule-set-2-expanded.png":::

1. 选择 " *更新* "，规则集将与路由解除关联。

1. 重复步骤2-5 以断开与此规则集关联的其他路由，直到看到 "路由状态 *" 显示为 "未关联"*。

1. 对于未 *关联* 的规则集，可以通过单击右侧的三个点，然后选择 " *删除*" 来删除规则集。 

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-disassociate-rule-set-3.png" alt-text="如何删除规则集的屏幕截图。":::

1. 规则集现已删除。

## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何执行以下操作：

* 创建规则集
* 将规则集关联到 AFD 路由。
* 从 AFD 配置文件中删除规则集

若要了解如何添加包含规则集的安全标头，请继续学习下一教程。

> [!div class="nextstepaction"]
> [设置了规则的安全标头]()
