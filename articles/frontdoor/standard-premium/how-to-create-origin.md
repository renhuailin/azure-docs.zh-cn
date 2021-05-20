---
title: 设置 Azure Front Door 标准版/高级版（预览版）源
description: 本文演示如何使用 Endpoint Manager 配置源。
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: ebc71ea2d354caf0c8f31b1231ecc1487237dd29
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "101741882"
---
# <a name="set-up-an-azure-front-door-standardpremium-preview-origin"></a>设置 Azure Front Door 标准版/高级版（预览版）源

> [!Note]
> 本文档适用于 Azure Front Door 标准版/高级版（预览版）。 正在寻找有关 Azure Front Door 的信息？ 请查看[此处](../front-door-overview.md)。

本文将演示如何在现有源组中创建 Azure Front Door 标准版/高级版源。 

> [!IMPORTANT]
> Azure Front Door 标准版/高级版（预览版）目前以公共预览版提供。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

创建 Azure Front Door 标准版/高级版源之前，必须至少先创建一个源组。

## <a name="create-a-new-azure-front-door-standardpremium-origin"></a>创建新的 Azure Front Door 标准版/高级版源

1. 登录 [Azure 门户](https://portal.azure.com)，并导航到你的 Azure Front Door 标准版/高级版配置文件。

1. 选择“源组”。 然后选择“+ 添加”以创建新的源组。
   
    :::image type="content" source="../media/how-to-create-origin/select-add-origin.png" alt-text="源组登录页的屏幕截图。":::

1. 在“添加源组”页上，输入新源组的唯一名称 。

1. 然后选择“+ 添加源”，将新源添加到此源组。 

    :::image type="content" source="../media/how-to-create-origin/add-origin-view.png" alt-text="“添加源”页的屏幕截图。":::
  
    | 设置 | 值 |
    | --- | --- |
    | 名称 | 输入新 Azure Front Door 源的唯一名称。 |   
    | 原点类型 | 要添加的资源的类型。 Azure Front Door 标准/高级版支持从应用服务、云服务或存储自动发现应用源。 如果需要 Azure 或非 Azure 后端中的其他资源，请选择“自定义主机”。 |
    | 主机名  | 如果未选择“自定义主机”作为源主机类型，请在下拉列表中选择源主机名来选择后端。 |
    | 源主机头 | 输入要针对每个请求发送到后端的主机头值。 有关详细信息，请查看[源主机头](concept-origin.md#hostheader)。 |
    | HTTP 端口 | 输入源支持 HTTP 协议的端口值。 |
    | HTTPS 端口 | 输入源支持 HTTPS 协议的端口值。 |
    | 优先级 | 如果想要使用主服务源来处理所有流量，可为不同的源分配优先级。 此外，如果主源或备份源不可用，请提供备份。 有关详细信息，请查看[优先级](concept-origin.md#priority)。 |
    | 重量 | 若要跨一组源均匀地或根据权重系数分布流量，可为不同的源分配权重。 有关详细信息，请查看[权重](concept-origin.md#weighted)。 |
    | 状态 | 选择此选项以启用源。 |
    | 规则 | 选择将应用于此路由的规则集。 有关如何配置规则的详细信息，请参阅[为 Azure Front Door 配置规则集](how-to-configure-rule-set.md) | 

    > [!IMPORTANT]
    > 在配置期间，API 不会验证是否可从 Front Door 环境访问源。 请确保 Front Door 可访问你的源。

1. 选择“添加”以创建新源。 创建的源应显示在组的源列表中。
  
    :::image type="content" source="../media/how-to-create-origin/origin-list-view.png" alt-text="列表视图中的源的屏幕截图。":::

1. 选择“添加”以将源组添加到当前终结点。 源组应出现在“源组”面板中。

## <a name="clean-up-resources"></a>清理资源
若要删除不再需要的源组，请单击“...”，然后从下拉菜单中选择“删除” 。

:::image type="content" source="../media/how-to-create-origin/delete-origin-group.png" alt-text="如何删除源组的屏幕截图。":::

若要删除不再需要的源，请单击“...”，然后从下拉菜单中选择“删除” 。 

:::image type="content" source="../media/how-to-create-origin/delete-origin-view.png" alt-text="如何删除源的屏幕截图。":::

## <a name="next-steps"></a>后续步骤

若要了解自定义域，请参阅[将自定义域添加](how-to-add-custom-domain.md)到 Azure Front Door 标准/高级版终结点。
