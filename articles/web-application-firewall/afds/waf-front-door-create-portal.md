---
title: 教程：为 Azure Front Door 创建 WAF 策略 - Azure 门户
description: 本教程介绍如何使用 Azure 门户创建 Web 应用程序防火墙 (WAF) 策略。
author: vhorne
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: tutorial
ms.date: 03/31/2021
ms.author: victorh
ms.openlocfilehash: e7b4544530dc9c0c894ae7a0f2b1d2830f895928
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/01/2021
ms.locfileid: "106122221"
---
# <a name="tutorial-create-a-web-application-firewall-policy-on-azure-front-door-using-the-azure-portal"></a>教程：使用 Azure 门户在 Azure Front Door 上创建 Web 应用程序防火墙策略

本教程介绍如何创建基本的 Azure Web 应用程序防火墙 (WAF) 策略，并将其应用到 Azure Front Door 上的前端主机。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 创建 WAF 策略
> * 将该策略关联到前端主机
> * 配置 WAF 规则

## <a name="prerequisites"></a>先决条件

创建 [Front Door](../../frontdoor/quickstart-create-front-door.md) 或 [Front Door 标准版/高级版](../../frontdoor/standard-premium/create-front-door-portal.md)配置文件。 

## <a name="create-a-web-application-firewall-policy"></a>创建 Web 应用程序防火墙策略

首先使用门户创建包含托管的默认规则集 (DRS) 的基本 WAF 策略。 

1. 在屏幕的左上方选择“创建资源”> 搜索“WAF”> 选择“Web 应用程序防火墙(WAF)”> 选择“创建”。  

1. 在“创建 WAF 策略”页的“基本”选项卡中输入或选择以下信息，对剩余的设置保留默认值，然后选择“查看 + 创建”：   

    | 设置                 | 值                                              |
    | ---                     | ---                                                |
    | 策略适用于              | 选择“全球 WAF (Front Door)”。 |
    | Front Door SKU          | 在基本、标准和高级 SKU 之间进行选择。 |
    | 订阅            | 选择 Front Door 订阅名称。|
    | 资源组          | 选择 Front Door 资源组名称。|
    | 策略名称             | 输入 WAF 策略的唯一名称。|
    | 策略状态            | 设置为“已启用”。 | 

   :::image type="content" source="../media/waf-front-door-create-portal/basic.png" alt-text="“创建 WAF 策略”页的屏幕截图，其中包含订阅、资源组和策略名称的“查看 + 创建”按钮以及列表框。":::

1. 在“创建 WAF 策略”页的“关联”选项卡中选择“+ 关联 Front Door 配置文件”，输入以下设置，然后选择“添加”：   

    | 设置                 | 值                                              |
    | ---                     | ---                                                |
    | Front Door 配置文件              | 选择 Front Door 配置文件名称。 |
    | 域          | 选择要将 WAF 策略关联到的域，然后选择“添加”。 |

    :::image type="content" source="../media/waf-front-door-create-portal/associate-profile.png" alt-text="“关联 Front Door 配置文件”页的屏幕截图。":::
    
    > [!NOTE]
    > 如果域已关联到某个 WAF 策略，该域将会灰显。必须先从关联的策略中删除该域，然后再将该域重新关联到新的 WAF 策略。

1. 依次选择“查看 + 创建”、“创建”。  

## <a name="configure-web-application-firewall-rules-optional"></a>配置 Web 应用程序防火墙规则（可选）

### <a name="change-mode"></a>更改模式

创建 WAF 策略时，该策略默认处于“检测”模式。  在“检测”模式下，WAF 不会阻止任何请求，而会在 WAF 日志中记录与 WAF 规则匹配的请求。 
若要查看 WAF 的运作方式，可将模式设置从“检测”更改为“阻止”。   在“阻止”模式下，与默认规则集 (DRS) 中定义的规则匹配的请求将被阻止，并记录在 WAF 日志中。 

 :::image type="content" source="../media/waf-front-door-create-portal/policy.png" alt-text="“策略设置”部分的屏幕截图。“模式”切换开关设置为“阻止”。":::

### <a name="custom-rules"></a>自定义规则

可以通过选择“自定义规则”部分下的“添加自定义规则”来创建自定义规则。   这会启动自定义规则配置页。 

:::image type="content" source="../media/waf-front-door-create-portal/custom-rules.png" alt-text="“自定义规则”页的屏幕截图。":::

以下示例配置一个自定义规则，当查询字符串包含 **blockme** 时，此规则会阻止相应的请求。

:::image type="content" source="../media/waf-front-door-create-portal/customquerystring2.png" alt-text="“自定义规则配置”页的屏幕截图，其中显示了一个规则的设置，该规则可检查 QueryString 变量是否包含值 blockme。":::

### <a name="default-rule-set-drs"></a>默认规则集 (DRS)

默认已启用 Azure 托管的默认规则集。 当前默认版本为 DefaultRuleSet_1.0。 在 WAF“托管规则”、“分配”中，下拉列表中提供了最近可用的规则集 Microsoft_DefaultRuleSet_1.1。

若要禁用单个规则，请选中规则编号前面的复选框，然后在页面顶部选择“禁用”。 若要更改规则集中单个规则的操作类型，请选中规则编号前面的复选框，然后在页面顶部选择“更改操作”。

:::image type="content" source="../media/waf-front-door-create-portal/managed-rules.png" alt-text="“托管规则”页的屏幕截图，其中显示了规则集、规则组、规则，以及“启用”、“禁用”和“更改操作”按钮。" lightbox="../media/waf-front-door-create-portal/managed-rules-expanded.png":::

## <a name="clean-up-resources"></a>清理资源

不再需要时，请删除资源组及所有相关资源。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [详细了解 Azure Front Door](../../frontdoor/front-door-overview.md)
> [详细了解 Azure Front Door 标准版/高级版](../../frontdoor/standard-premium/overview.md)
